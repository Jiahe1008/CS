```cpp
#include <vector>
vector<int> v1;                    // 空 vector
vector<int> v2(10);                 // 10 个元素，默认 0
vector<int> v3(10, -1);              // 10 个 -1
vector<int> v4 = {1, 2, 3, 4};       // 列表初始化

v.push_back(10);      // 尾部插入
v.emplace_back(20);   // 更高效（C++11）

v.pop_back();                  // 删除最后一个
v.erase(v.begin() + i);        // 删除第 i 个元素
v.erase(v.begin(), v.begin()+3); // 删除区间
v.clear();                     // 清空

v[i];           // 不检查越界
v.at(i);        // 越界抛异常
v.front();      // 第一个
v.back();       // 最后一个

for (int i = 0; i < v.size(); i++) {
    cout << v[i] << " ";
}

for (auto it = v.begin(); it != v.end(); ++it) {
    cout << *it << " ";
}

for (int x : v) {
    cout << x << " ";
}

v.size();        // 元素个数
v.empty();       // 是否为空
v.capacity();    // 当前分配的内存容量
v.resize(20);    // 改变 size
v.reserve(100);  // 预留空间（不改变 size）

#include <algorithm>

sort(v.begin(), v.end());               // 升序
sort(v.begin(), v.end(), greater<>());  // 降序

struct Node {
    int a, b;
};

vector<Node> v;
sort(v.begin(), v.end(), [](const Node& x, const Node& y) {
    return x.a > y.a;
});

// 查找
auto it = find(v.begin(), v.end(), 10);
if (it != v.end()) {
    cout << "found";
}


vector<vector<int>> mat;
vector<vector<int>> mat(n, vector<int>(m, 0));
```