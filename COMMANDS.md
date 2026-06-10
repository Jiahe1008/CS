# 命令实现说明

主循环读取用户输入，用空白字符切分命令和参数，然后判断是否为内建命令。若是内建命令，就直接在当前进程中执行；否则使用 `fork()` 创建子进程，并用 `execvp()` 执行外部程序。

其中 `cd`、`exit`、`history` 必须做成内建命令，因为它们需要修改当前 shell 进程状态。其它命令也在本项目中用 C 函数实现，便于实验展示。

## 公共定义和辅助函数

这些定义被多个命令共用。

```c
#define _POSIX_C_SOURCE 200809L

#include <dirent.h>
#include <errno.h>
#include <fcntl.h>
#include <limits.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/stat.h>
#include <sys/wait.h>
#include <unistd.h>

#define MAX_ARGS 128
#define MAX_HISTORY 100
#define MAX_LINE 1024

#ifndef PATH_MAX
#define PATH_MAX 4096
#endif

static char *history[MAX_HISTORY];
static int history_count = 0;
```

```c
static int split_line(char *line, char *argv[], int max_args)
{
    int argc = 0;
    char *token = strtok(line, " \t\r\n");

    while (token != NULL && argc < max_args - 1) {
        argv[argc++] = token;
        token = strtok(NULL, " \t\r\n");
    }

    argv[argc] = NULL;
    return argc;
}

static void strip_newline(char *line)
{
    size_t len = strlen(line);

    if (len > 0 && line[len - 1] == '\n') {
        line[len - 1] = '\0';
    }
}
```

## 1. cd

`cd` 使用 `chdir()` 改变当前 myshell 进程的工作目录。支持 `cd`、`cd ~`、`cd 路径` 和 `cd -`。切换成功后更新 `PWD` 和 `OLDPWD` 环境变量。

```c
static int change_directory(char *argv[])
{
    char old_pwd[PATH_MAX];
    char new_pwd[PATH_MAX];
    const char *target = argv[1];

    if (argv[2] != NULL) {
        fprintf(stderr, "cd: too many arguments\n");
        return 1;
    }

    if (getcwd(old_pwd, sizeof(old_pwd)) == NULL) {
        perror("cd: getcwd");
        return 1;
    }

    if (target == NULL || strcmp(target, "~") == 0) {
        target = getenv("HOME");
        if (target == NULL) {
            fprintf(stderr, "cd: HOME not set\n");
            return 1;
        }
    } else if (strcmp(target, "-") == 0) {
        target = getenv("OLDPWD");
        if (target == NULL) {
            fprintf(stderr, "cd: OLDPWD not set\n");
            return 1;
        }
        printf("%s\n", target);
    }

    if (chdir(target) != 0) {
        fprintf(stderr, "cd: %s: %s\n", target, strerror(errno));
        return 1;
    }

    if (getcwd(new_pwd, sizeof(new_pwd)) == NULL) {
        perror("cd: getcwd");
        return 1;
    }

    setenv("OLDPWD", old_pwd, 1);
    setenv("PWD", new_pwd, 1);
    return 0;
}
```

## 2. ls

`ls` 使用 `opendir()` 打开目录，使用 `readdir()` 逐项读取目录内容，并跳过隐藏文件。若参数是普通文件，则直接输出文件名。

```c
static int list_one_path(const char *path)
{
    DIR *dir = opendir(path);

    if (dir == NULL) {
        struct stat st;

        if (stat(path, &st) == 0) {
            printf("%s\n", path);
            return 0;
        }

        fprintf(stderr, "ls: cannot access '%s': %s\n", path, strerror(errno));
        return 1;
    }

    struct dirent *entry;
    while ((entry = readdir(dir)) != NULL) {
        if (entry->d_name[0] == '.') {
            continue;
        }
        printf("%s\n", entry->d_name);
    }

    if (closedir(dir) != 0) {
        perror("ls");
        return 1;
    }

    return 0;
}

static int builtin_ls(int argc, char *argv[])
{
    int status = 0;

    if (argc == 1) {
        return list_one_path(".");
    }

    for (int i = 1; i < argc; i++) {
        if (argc > 2) {
            printf("%s:\n", argv[i]);
        }

        if (list_one_path(argv[i]) != 0) {
            status = 1;
        }

        if (argc > 2 && i + 1 < argc) {
            putchar('\n');
        }
    }

    return status;
}
```

## 3. pwd

`pwd` 使用 `getcwd()` 获取当前工作目录并输出。

```c
static int builtin_pwd(char *argv[])
{
    char cwd[PATH_MAX];

    if (argv[1] != NULL) {
        fprintf(stderr, "pwd: too many arguments\n");
        return 1;
    }

    if (getcwd(cwd, sizeof(cwd)) == NULL) {
        perror("pwd");
        return 1;
    }

    printf("%s\n", cwd);
    return 0;
}
```

## 4. rm

`rm` 使用 `unlink()` 删除普通文件。当前实现不支持删除目录，也不支持 `-r` 等选项。

```c
static int builtin_rm(int argc, char *argv[])
{
    int status = 0;

    if (argc < 2) {
        fprintf(stderr, "rm: missing operand\n");
        return 1;
    }

    for (int i = 1; i < argc; i++) {
        if (unlink(argv[i]) != 0) {
            fprintf(stderr, "rm: cannot remove '%s': %s\n", argv[i], strerror(errno));
            status = 1;
        }
    }

    return status;
}
```

## 5. mkdir

`mkdir` 使用系统调用 `mkdir()` 创建目录，默认权限为 `0755`。

```c
static int builtin_mkdir(int argc, char *argv[])
{
    int status = 0;

    if (argc < 2) {
        fprintf(stderr, "mkdir: missing operand\n");
        return 1;
    }

    for (int i = 1; i < argc; i++) {
        if (mkdir(argv[i], 0755) != 0) {
            fprintf(stderr, "mkdir: cannot create directory '%s': %s\n", argv[i],
                    strerror(errno));
            status = 1;
        }
    }

    return status;
}
```

## 6. mv

`mv` 使用 `rename()` 实现文件或目录改名、移动。如果目标是目录，则自动把源文件名拼到目标目录后面。

```c
static const char *base_name(const char *path)
{
    const char *slash = strrchr(path, '/');

    if (slash == NULL) {
        return path;
    }

    return slash + 1;
}

static int is_directory(const char *path)
{
    struct stat st;

    return stat(path, &st) == 0 && S_ISDIR(st.st_mode);
}

static int make_copy_target(const char *src, const char *dst, char *target,
                            size_t target_size)
{
    int written;

    if (is_directory(dst)) {
        written = snprintf(target, target_size, "%s/%s", dst, base_name(src));
    } else {
        written = snprintf(target, target_size, "%s", dst);
    }

    if (written < 0 || (size_t)written >= target_size) {
        fprintf(stderr, "cp: target path is too long\n");
        return 1;
    }

    return 0;
}

static int builtin_mv(int argc, char *argv[])
{
    char target[PATH_MAX];

    if (argc != 3) {
        fprintf(stderr, "mv: usage: mv SOURCE DEST\n");
        return 1;
    }

    if (make_copy_target(argv[1], argv[2], target, sizeof(target)) != 0) {
        return 1;
    }

    if (rename(argv[1], target) != 0) {
        fprintf(stderr, "mv: cannot move '%s' to '%s': %s\n", argv[1], target,
                strerror(errno));
        return 1;
    }

    return 0;
}
```

## 7. cp

`cp` 先用 `stat()` 判断源文件是否为普通文件，再用 `open()` 打开源文件和目标文件，最后通过 `read()` 和 `write()` 循环复制内容。如果目标是目录，则复制到该目录下并保持原文件名。

```c
static const char *base_name(const char *path)
{
    const char *slash = strrchr(path, '/');

    if (slash == NULL) {
        return path;
    }

    return slash + 1;
}

static int is_directory(const char *path)
{
    struct stat st;

    return stat(path, &st) == 0 && S_ISDIR(st.st_mode);
}

static int make_copy_target(const char *src, const char *dst, char *target,
                            size_t target_size)
{
    int written;

    if (is_directory(dst)) {
        written = snprintf(target, target_size, "%s/%s", dst, base_name(src));
    } else {
        written = snprintf(target, target_size, "%s", dst);
    }

    if (written < 0 || (size_t)written >= target_size) {
        fprintf(stderr, "cp: target path is too long\n");
        return 1;
    }

    return 0;
}

static int copy_file(const char *src, const char *dst)
{
    char target[PATH_MAX];
    char buffer[8192];
    struct stat st;

    if (stat(src, &st) != 0) {
        fprintf(stderr, "cp: cannot stat '%s': %s\n", src, strerror(errno));
        return 1;
    }

    if (!S_ISREG(st.st_mode)) {
        fprintf(stderr, "cp: '%s' is not a regular file\n", src);
        return 1;
    }

    if (make_copy_target(src, dst, target, sizeof(target)) != 0) {
        return 1;
    }

    int in_fd = open(src, O_RDONLY);
    if (in_fd < 0) {
        fprintf(stderr, "cp: cannot open '%s': %s\n", src, strerror(errno));
        return 1;
    }

    int out_fd = open(target, O_WRONLY | O_CREAT | O_TRUNC, st.st_mode & 0777);
    if (out_fd < 0) {
        fprintf(stderr, "cp: cannot create '%s': %s\n", target, strerror(errno));
        close(in_fd);
        return 1;
    }

    ssize_t nread;
    while ((nread = read(in_fd, buffer, sizeof(buffer))) > 0) {
        ssize_t total_written = 0;

        while (total_written < nread) {
            ssize_t nwritten = write(out_fd, buffer + total_written,
                                     (size_t)(nread - total_written));
            if (nwritten < 0) {
                fprintf(stderr, "cp: write error: %s\n", strerror(errno));
                close(in_fd);
                close(out_fd);
                return 1;
            }
            total_written += nwritten;
        }
    }

    if (nread < 0) {
        fprintf(stderr, "cp: read error: %s\n", strerror(errno));
        close(in_fd);
        close(out_fd);
        return 1;
    }

    if (close(in_fd) != 0) {
        perror("cp: close input");
        close(out_fd);
        return 1;
    }

    if (close(out_fd) != 0) {
        perror("cp: close output");
        return 1;
    }

    return 0;
}

static int builtin_cp(int argc, char *argv[])
{
    if (argc != 3) {
        fprintf(stderr, "cp: usage: cp SOURCE DEST\n");
        return 1;
    }

    return copy_file(argv[1], argv[2]);
}
```

## 8. exit

`exit` 不需要单独函数，在主循环中判断到命令名为 `exit` 后直接 `break`，跳出 shell 主循环，最后释放历史命令占用的内存并结束程序。

```c
if (strcmp(argv[0], "exit") == 0) {
    break;
}
```

主循环退出后的清理代码如下：

```c
free_history();
return 0;
```

## 9. history

`history` 使用一个字符串数组保存最近 100 条命令。每次用户输入有效命令后，先调用 `add_history()` 保存原始命令；执行 `history` 时按序号输出。

```c
static char *history[MAX_HISTORY];
static int history_count = 0;

static void add_history(const char *line)
{
    if (line[0] == '\0') {
        return;
    }

    if (history_count == MAX_HISTORY) {
        free(history[0]);
        memmove(history, history + 1, sizeof(history[0]) * (MAX_HISTORY - 1));
        history_count--;
    }

    history[history_count] = strdup(line);
    if (history[history_count] == NULL) {
        perror("history");
        return;
    }

    history_count++;
}

static void free_history(void)
{
    for (int i = 0; i < history_count; i++) {
        free(history[i]);
    }
}

static int builtin_history(char *argv[])
{
    if (argv[1] != NULL) {
        fprintf(stderr, "history: too many arguments\n");
        return 1;
    }

    for (int i = 0; i < history_count; i++) {
        printf("%4d  %s\n", i + 1, history[i]);
    }

    return 0;
}
```

## 命令分发代码

下面代码展示 myshell 如何把用户输入分发到各个内建命令。

```c
if (strcmp(argv[0], "exit") == 0) {
    break;
}

if (strcmp(argv[0], "cd") == 0) {
    change_directory(argv);
    continue;
}

if (strcmp(argv[0], "ls") == 0) {
    builtin_ls(argc, argv);
    continue;
}

if (strcmp(argv[0], "pwd") == 0) {
    builtin_pwd(argv);
    continue;
}

if (strcmp(argv[0], "rm") == 0) {
    builtin_rm(argc, argv);
    continue;
}

if (strcmp(argv[0], "mkdir") == 0) {
    builtin_mkdir(argc, argv);
    continue;
}

if (strcmp(argv[0], "mv") == 0) {
    builtin_mv(argc, argv);
    continue;
}

if (strcmp(argv[0], "cp") == 0) {
    builtin_cp(argc, argv);
    continue;
}

if (strcmp(argv[0], "history") == 0) {
    builtin_history(argv);
    continue;
}
```

如果命令不是上述内建命令，就交给外部程序执行。

```c
static int run_external_command(char *argv[])
{
    pid_t pid = fork();

    if (pid < 0) {
        perror("fork");
        return 1;
    }

    if (pid == 0) {
        execvp(argv[0], argv);
        fprintf(stderr, "%s: %s\n", argv[0], strerror(errno));
        _exit(127);
    }

    int status;
    if (waitpid(pid, &status, 0) < 0) {
        perror("waitpid");
        return 1;
    }

    if (WIFEXITED(status)) {
        return WEXITSTATUS(status);
    }

    return 1;
}
```
