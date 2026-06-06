### hgm8

version1

version2
1. 将pair和fused改成残差
2. 独立了anchor_fused的权重
3. 减少了hop_num数量
```python
  

class MemoryAnchorStage(nn.Module, MemoryHypergraphMixin):

    """Dense-anchor stage guided by persistent object memory.

  

    This is the main HGM8 block:

      1) dense anchors from cat(pre, post)

      2) coordinate/local hypergraph context via local refinement

      3) feature-memory hypergraph context via top-k memory incidence

      4) optional boundary context

      5) Mamba on graph-conditioned dense anchor sequence

      6) reverse aggregation to update persistent memory

    """

  

    def __init__(

        self,

        in_dim: int,

        hidden_dim: int = 128,

        mem_topk: int = 8,

        drop_path: float = 0.0,

        norm_layer=nn.LayerNorm,

        use_mamba: bool = True,

        use_boundary: bool = True,

        **vss_kwargs: Any,

    ):

        super().__init__()

        self.hidden_dim = hidden_dim

        self.mem_topk = mem_topk

        self.use_boundary = use_boundary

        self.shared_proj = ConvBNAct(in_dim, hidden_dim, 1)

        self.pair_proj = ConvBNAct(in_dim * 2, hidden_dim, 1)

        print("init memory ===")

        # Coordinate-space local hypergraph surrogate.

        self.local_refine = nn.Sequential(

            LocalRefineBlock(hidden_dim),

            LocalRefineBlock(hidden_dim),

        )

  

        self.pos_proj = nn.Sequential(

            nn.Linear(2, hidden_dim),

            nn.LayerNorm(hidden_dim),

        )

        self.pos_scale = nn.Parameter(torch.tensor(0.25))

  

        # A, local_ctx, mem_ctx, boundary_ctx

        self.fuse_shared = nn.Sequential(

            nn.Linear(hidden_dim * 4, hidden_dim * 2), nn.GELU()

        )

        self.fuse_head_pair = nn.Linear(hidden_dim * 2, hidden_dim)

        self.fuse_head_pre  = nn.Linear(hidden_dim * 2, hidden_dim)

        self.fuse_head_post = nn.Linear(hidden_dim * 2, hidden_dim)

        self.anchor_norm = nn.LayerNorm(hidden_dim)

  

        # self.anchor_mamba = (

        #     SequenceVSS1D(hidden_dim, drop_path=drop_path, norm_layer=norm_layer, **vss_kwargs)

        #     if use_mamba else nn.Identity()

        # )

        self.anchor_mamba = (

            VSS2D(hidden_dim, drop_path=drop_path, norm_layer=norm_layer, channel_first=False, **vss_kwargs)

            if use_mamba else nn.Identity()

        )

  

        self.temporal_reasoner = MemorySelectiveTemporalReasoner(

            hidden_dim=hidden_dim,

            temporal_k=8,

            num_hops=2,  # 轻量级对齐

            # ... 其他参数 ...

        )

  

        self.anchor_out = nn.Sequential(

            nn.Linear(hidden_dim, hidden_dim),

            nn.GELU(),

            nn.Linear(hidden_dim, hidden_dim),

        )

  

        self.memory_update = nn.Sequential(

            nn.Linear(hidden_dim * 2, hidden_dim * 2),

            nn.GELU(),

            nn.Linear(hidden_dim * 2, hidden_dim),

        )

        self.memory_norm = nn.LayerNorm(hidden_dim)

        self.late_fuse_proj = ConvBNAct(hidden_dim * 2, hidden_dim, 1)

        self.out_refine = nn.Sequential(

            LocalRefineBlock(hidden_dim),

            LocalRefineBlock(hidden_dim),

        )

  

    @staticmethod

    def _coords(h: int, w: int, device: torch.device, dtype: torch.dtype) -> torch.Tensor:

        yy = torch.linspace(-1, 1, h, device=device, dtype=dtype)

        xx = torch.linspace(-1, 1, w, device=device, dtype=dtype)

        gy, gx = torch.meshgrid(yy, xx, indexing="ij")

        return torch.stack([gy.reshape(-1), gx.reshape(-1)], dim=-1)

  

    def forward(

        self,

        pre_feat: torch.Tensor,

        post_feat: torch.Tensor,

        memory_bank: MemoryInput,

        memory_bank_pre: MemoryInput,   # T1 专属记忆库

        memory_bank_post: MemoryInput,  # T2 专属记忆库

        boundary: Optional[torch.Tensor] = None,

        return_aux: bool = False,

    ):

        x = self.pair_proj(torch.cat([pre_feat, post_feat], dim=1))  # [B,D,H,W]

        pre_x = self.shared_proj(pre_feat)   # [B,128,H,W]

        post_x = self.shared_proj(post_feat) # [B,128,H,W]

        b, d, h, w = x.shape

  

        local_map = self.local_refine(x)

  

        local_map_pre = self.local_refine( pre_x)

        local_map_post = self.local_refine(post_x)

        anchors = x.flatten(2).transpose(1, 2)

        anchors_pre = pre_x.flatten(2).transpose(1, 2)

        anchors_post = post_x.flatten(2).transpose(1, 2)

        local_ctx_pre = local_map_pre.flatten(2).transpose(1, 2)

        local_ctx_post = local_map_post.flatten(2).transpose(1, 2)

        coords = self._coords(h, w, x.device, anchors_pre.dtype)

        pos_emb = self.pos_scale * self.pos_proj(coords).unsqueeze(0)

        anchors_pos_pre = anchors_pre + pos_emb

        anchors_pos_post = anchors_post + pos_emb

        anchors_pos = anchors + pos_emb

  

        anchors = x.flatten(2).transpose(1, 2)      

        local_ctx = local_map.flatten(2).transpose(1, 2)

  

        mem_ctx_pre = self.memory_to_anchor(anchors_pos_pre, memory_bank_pre)

        mem_ctx_post = self.memory_to_anchor(anchors_pos_post, memory_bank_post)

        mem_ctx = self.memory_to_anchor(anchors_pos, memory_bank)  # [B,N,D]

  

        if boundary is None:

            boundary_ctx = torch.zeros_like(anchors)

        else:

            if boundary.shape[-2:] != (h, w):

                boundary = F.interpolate(boundary, size=(h, w), mode="bilinear", align_corners=False)

            boundary_ctx = boundary.flatten(2).transpose(1, 2)

  

        anchors_hg = anchors + self.fuse_head_pair(self.fuse_shared(torch.cat([

            anchors_pos,local_ctx,mem_ctx,boundary_ctx,

        ], dim=-1)))

        anchors_hg = self.anchor_norm(anchors_hg)

  

        anchors_hg_pre = anchors_pre + self.fuse_head_pre(self.fuse_shared(torch.cat([

            anchors_pos_pre, local_ctx_pre, mem_ctx_pre, boundary_ctx

        ], dim=-1)))

        anchors_hg_pre = self.anchor_norm(anchors_hg_pre)

  

        anchors_hg_post = anchors_post + self.fuse_head_post(self.fuse_shared(torch.cat([

            anchors_pos_post, local_ctx_post, mem_ctx_post, boundary_ctx

        ], dim=-1)))

        anchors_hg_post = self.anchor_norm(anchors_hg_post)

  
  

        # 更新 T1 记忆

        bank_pre = _as_memory_bank(memory_bank_pre)

        latest_memory_pre = bank_pre[-1]

        mem_obs_pre = self.anchor_to_memory(anchors_hg_pre, latest_memory_pre)

        mem_updated_pre = self.memory_norm(latest_memory_pre + self.memory_update(torch.cat([latest_memory_pre, mem_obs_pre], dim=-1)))

  

        # 更新 T2 记忆

        bank_post = _as_memory_bank(memory_bank_post)

        latest_memory_post = bank_post[-1]

        mem_obs_post = self.anchor_to_memory(anchors_hg_post, latest_memory_post)

        mem_updated_post = self.memory_norm(latest_memory_post + self.memory_update(torch.cat([latest_memory_post, mem_obs_post], dim=-1)))

        # Update only the latest memory, but query all memories through memory_bank.

        # 更新 cat 记忆

        bank = _as_memory_bank(memory_bank)

        latest_memory = bank[-1]

        mem_obs = self.anchor_to_memory(anchors_hg, latest_memory)

        mem_new = self.memory_norm(latest_memory + self.memory_update(torch.cat([latest_memory, mem_obs], dim=-1)))

        mem_pair, mem_pre_final, mem_post_final = self.temporal_reasoner(

            mem_updated_pre, mem_updated_post

        )

        anchors_hg_pre_2d = anchors_hg_pre.transpose(1, 2).contiguous().view(b, d, h, w)

        anchors_hg_post_2d = anchors_hg_post.transpose(1, 2).contiguous().view(b, d, h, w)

        anchors_hg_2d = anchors_hg.transpose(1, 2).contiguous().view(b, d, h, w)

  

        fused_2d = self.late_fuse_proj(torch.cat([anchors_hg_pre_2d, anchors_hg_post_2d], dim=1))

        anchors_m_2d_fused = self.anchor_mamba_fused(fused_2d)

        anchors_m_2d_pair = self.anchor_mamba_pair(anchors_hg_2d)

        anchors_m = anchors_m_2d_fused.flatten(2).transpose(1, 2) + anchor_m_2d_pair.flatten(2).transpose(1, 2)

        delta = self.anchor_out(anchors_m).transpose(1, 2).contiguous().view(b, d, h, w)

        out = x + delta

        out = out + self.out_refine(out)

  

        if return_aux:

            aux = {}

            aux.update(_stat("anchor_x", x))

            aux.update(_stat("anchor_local_ctx", local_ctx))

            aux.update(_stat("anchor_mem_ctx", mem_ctx))

            aux.update(_stat("anchor_boundary_ctx", boundary_ctx))

            aux.update(_stat("anchor_m", anchors_hg))

            aux.update(_stat("memory_new", mem_new))

            aux.update(_stat("anchor_out", out))

            return out, mem_new, mem_pre_final, mem_post_final, aux

        return out, mem_new, mem_pre_final, mem_post_final
```
