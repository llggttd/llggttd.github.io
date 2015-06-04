title: "unix下创建守护进程"
date: 2015-06-03 10:37:42
updated: 2015-06-03 10:37:42
categories:
tags:
---

守护进程(daemon), 表示后台运行的程序，一般随系统的启动自动地启动且在用户logoff后仍然能够继续运行。该daemon进程一般在启动后需要与父进程断开关系，并使进程没有控制终端（tty）。因为daemon程序在后台执行，不需要于终端交互，通常就关闭STDIN、STDOUT和STDER。daemon无法输出信息，可以使用syslog或自己的日志系统进行日志处理。

## 

``` c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/types.h>
#include <sys/stat.h>

int main(int argc, char const *argv[]) {
    
    const char *log_file = "main.log";
    int count = 0;

    switch(fork()) {
        case 0:
            printf("I hava the world, I am %d\n", getpid());
            break;

        case -1:
            printf("fork failed\n");
            return 1;

        default:
            printf("Now, the world is the kid's, Goodbye!\n");
            return 0;
    }

    //创建新的会话
    setsid();

    //设置新的权限掩码
    umask(0);

    //更改当前目录
    chdir("/tmp");

    //打开新的文件接收程序的的输出
    int fd_log = open(log_file, O_CREAT | O_RDWR, 0777);
    if (fd_log == -1){
        printf("fail to open %s\n", log_file);
        return 2;
    }

    //关闭默认打开的标准输入、输出、错误文件
    close(STDIN_FILENO);
    close(STDOUT_FILENO);
    close(STDERR_FILENO);

    //绑定日志文件到子进程的标准输出
    dup2(fd_log, STDIN_FILENO);
    dup2(fd_log, STDOUT_FILENO);
    dup2(fd_log, STDERR_FILENO);

    while(1) {
        printf("This come form %d, #%d\n", getpid(), count);
        sleep(5);
        count++;
    }
    
    return 0;
}
```
