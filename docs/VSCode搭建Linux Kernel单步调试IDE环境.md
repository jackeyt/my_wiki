# VS Code搭建ARM Linux Makefile工程IDE（C/C++）[史上最详细]

[TOC]

## 0.环境介绍
> 主机：ubuntu 18.04(LTS)
> 开发板主控：RK3399(ARM 64bit)
或者其他任意一款只有对应的工具链即可
> OS:Linux version 4.4.143
> 开发板IP：192.168.19.171
> 编译器：aarch64-linux-gnu-gcc (gcc version 7.3.0)
> 调试器：主机：aarch64-linux-gnu-gdb (gcc version 7.3.0)    开发板：gdbserver(7.3.0)
> VS Code版本
    版本 1.23.1
    提交 d0182c3417d225529c6d5ad24b7572815d0de9ac
    日期 2018-05-10T16:04:33.747Z
    Shell 1.7.12
    渲染器 58.0.3029.110
    Node 7.9.0
    架构 x64
> VS Code插件：C/C++ （在扩展里面添加即可）