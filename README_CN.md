# isolate

isolate 是一个沙箱工具，用于安全地运行不受信任的可执行文件，例如编程竞赛中提交的程序。isolate 为它们提供了有限访问的环境，防止它们影响主机系统。它利用了 Linux 内核的特性，如命名空间和控制组。

isolate 由 Martin Mareš（<mj@ucw.cz>）和 Bernard Blackham（<bernard@blackham.com.au>）开发，并由前者继续维护。其他一些人为功能和错误修复提供了补丁（请参阅 Git 历史以获取列表）。感谢！

最初，isolate 是[Moe 竞赛环境](http://www.ucw.cz/moe/)的一部分，但它发展成为一个单独的项目，被不同的竞赛系统使用，其中最突出的是[CMS](https://github.com/cms-dev/cms)。它现在位于[GitHub](https://github.com/ioi/isolate)，您可以在那里提交错误报告和功能请求。

如果您对更多细节感兴趣，请阅读 Martin 和 Bernard 在信息学奥林匹克期刊上发表的关于[isolate 设计](https://mj.ucw.cz/papers/isolate.pdf)和[评分系统安全性](https://mj.ucw.cz/papers/secgrad.pdf)的论文。此外，isolate 的[手册页](http://www.ucw.cz/moe/isolate.1.html)可在线获取。

要编译 isolate，您需要：

- libcap 库的头文件（通常在 libcap-dev 软件包中可用）

- libsystemd 库的头文件（编译 isolate-cg-keeper 时需要 libsystemd-dev 软件包）

您可能需要`a2x`（在[AsciiDoc](https://asciidoc-py.github.io/a2x.1.html)中找到）来构建手册。但如果您只想要 isolate 二进制文件，只需运行`make isolate`即可。

推荐的系统设置在手册页的"INSTALLATION"和"REPRODUCIBILITY"章节中有描述。

runoob@LAPTOP-EUEHO147:~/repo/test$ isolate init
请指定一个 isolate 命令（例如 --init，--run）。
用法：isolate [<选项>] <命令>

选项：

- `--as-uid=<uid>`：以给定用户的身份执行操作（需要 root 权限）
- `--as-gid=<gid>`：以给定组的身份执行操作（需要 root 权限）
- `-b, --box-id=<id>`：当并行使用多个沙箱时，每个沙箱必须具有唯一的 ID
- `--cg`：启用控制组的使用
- `--cg-mem=<size>`：将控制组的内存使用限制为`<size>`KB
- `-c, --chdir=<dir>`：在执行程序之前切换到`<dir>`目录
- `--core=<size>`：将核心文件限制为`<size>`KB（默认：0）
- `-d, --dir=<dir>`：使`<dir>`目录在沙箱内可见
- `--dir=<in>=<out>`：将外部的`<out>`目录作为内部的`<in>`目录可见
- `--dir=<in>=`：删除先前定义的目录规则（甚至是默认规则）
- `--dir=...:<opt>`：为规则指定选项：
  - `dev`：允许访问块/字符设备
  - `fs`：挂载文件系统（例如，`--dir=/proc:proc:fs`）
  - `maybe`：如果`<out>`不存在，则跳过规则
  - `noexec`：不允许执行二进制文件
  - `norec`：不递归绑定目录
  - `rw`：允许读写访问
  - `tmp`：创建为临时目录（暗示 rw）
- `-D, --no-default-dirs`：不添加默认目录规则
- `-f, --fsize=<size>`：可创建的文件的最大大小（以 KB 为单位）
- `-E, --env=<var>`：从父进程继承环境变量`<var>`
- `-E, --env=<var>=<val>`：将环境变量`<var>`设置为`<val>`；如果`<var>`为空，则取消设置
- `-x, --extra-time=<time>`：设置额外的超时时间，之前超时的程序尚未被杀死，以便报告其真实的执行时间（秒，允许分数）
- `-e, --full-env`：继承父进程的完整环境
- `--inherit-fds`：继承父进程的所有文件描述符
- `-m, --mem=<size>`：将地址空间限制为`<size>`KB
- `-M, --meta=<file>`：将进程信息输出到`<file>`中（名称：值）
- `-n, --open-files=<max>`：将打开文件的数量限制为`<max>`（默认：64，0 表示无限制）
- `-q, --quota=<blk>,<ino>`：将磁盘配额设置为`<blk>`块和`<ino>`节点
- `--share-net`：与父进程共享网络命名空间
- `-s, --silent`：除了致命错误外，不打印状态消息
- `--special-files`：保留在沙箱内产生的非常规文件（符号链接等）
- `-k, --stack=<size>`：将堆栈大小限制为`<size>`KB（默认：0 表示无限制）
- `-r, --stderr=<file>`：将 stderr 重定向到`<file>`中
- `--stderr-to-stdout`：将 stderr 重定向到 stdout
- `-i, --stdin=<file>`：从

`<file>`中重定向 stdin

- `-o, --stdout=<file>`：将 stdout 重定向到`<file>`中
- `-p, --processes[=<max>]`：启用多个进程（最多`<max>`个）；需要`--cg`
- `-t, --time=<time>`：设置运行时间限制（秒，允许分数）
- `--tty-hack`：在沙箱中允许交互式程序（请参阅 man 手册了解注意事项）
- `-v, --verbose`：详细输出（多次使用以获取更多详细信息）
- `--wait`：如果沙箱当前正忙，则等待而不是拒绝运行
- `-w, --wall-time=<time>`：设置墙钟时间限制（秒，允许分数）

命令：

- `--init`：初始化沙箱（当使用`--cg`时还初始化其控制组）
- `--run -- <cmd> ...`：在沙箱内运行给定命令
- `--cleanup`：清理沙箱
- `--print-cg-root`：打印控制组层次结构的根
- `--version`：显示程序版本和配置
