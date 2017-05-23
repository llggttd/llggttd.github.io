---
title: "使用Unix日志"
date: 2015-06-04 10:48:29
updated: 2015-06-04 10:48:29
categories:
- linux
- 日志
tags:
---

*discription*

## {{ title }}

### openlog

void openlog(const char *ident, int option, int facility);

此函数用来打开一个到系统日志记录程序的连接，打开之后就可以用syslog或vsyslog函数向系统日志里添加信息了。而closelog函数就是用来关闭此连接的。

第一个参数ident是一个标记，ident所表示的字符串将固定地加在每行日志的前面以标识这个日志，通常就写成当前程序的名称以作标记。

第二个参数option一般是下列选项值取“与”运算(使用“|”表示，如“LOG_CONS | LOG_PID”)的结果：

- LOG_CONS：如果送到system logger时发生问题，直接写入系统终端;
- LOG_NDELAY：立即开启连接，通常连接是在第一次写入消息时才打开的;
- LOG_PERROR：将消息也同时送到stderr设备;
- LOG_PID：将进程PID含入所有消息中。

第三个参数facility指明记录日志的程序的类型，它主要具有如下几类日志类型：

- LOG_AUTH ：安全/授权消息
- LOG_AUTHPRIV：安全/授权消息
- LOG_CRON：时间守护进程(cron和at)专用
- LOG_DAEMON：其它系统守护进程
- LOG_KERN：核心消息
- LOG_LOCAL0到LOG_LOCAL7：系统保留
- LOG_LPR：printer子系统
- LOG_MAIL：mail子系统
- LOG_NEWS：USENET新闻子系统
- LOG_SYSLOG：syslogd进程内部所产生的消息
- LOG_USER(缺省)：一般使用者缺省使用消息
- LOG_UUCP：UUCP子系统
- LOG_FTP：FTP子系统使用

### syslog

void syslog(int priority, const char * message, ...);

在实际使用中，如果我们的程序要使用系统日志功能，只需要在程序启动时使用openlog函数来连接syslogd程序，后面随时用syslog函数写日志就行了。

第一个参数是消息的紧急级别priority,它是由severity level和facility组成的。Facility已经在上面介绍了，下面介绍一下severity level，也就是消息的重要级别，它主要包括：

- LOG_EMERG：紧急状况
- LOG_ALERT：高优先级问题，比如说数据库崩溃等，必须要立即采取反应行动
- LOG_CRIT：重要状况发生，比如硬件故障
- LOG_ERR：错误发生
- LOG_WARNING：警告发生
- LOG_NOTICE：一般状况，需要引起注意
- LOG_INFO：信息状况
- LOG_DEBUG：调试消息

第二个参数是消息及其格式，之后是格式对应的参数，如同C语言里面printf输出函数一样使用

### closelog

void closelog( void );

关闭打开的系统日志连接

