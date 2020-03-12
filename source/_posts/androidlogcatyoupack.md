---
title: Logcat获取指定报名日志
date: 2020-03-12 19:44:59
tags: [logcat]
categories: Android
---

### 前言

对于Android开发来说, Logcat肯定是再熟悉不过. 获取日志输出来定位程序问题, logcat是必不可少的, 最近在弄一个log收集的工具, 需要获取指定程序的日志, 再次记录一下.

<!--more-->

### 1.Logcat源码地址

[https://github.com/android/platform_system_core/tree/master/logcat](https://github.com/android/platform_system_core/tree/master/logcat "https://github.`com`/android/platform_system_core/tree/master/logcat")

### 2.源码中两个方法提示logcat用法

logcat.cpp中

```cpp
static void show_help(android_logcat_context_internal* context) {
    if (!context->error) return;

    const char* cmd = strrchr(context->argv[0], '/');
    cmd = cmd ? cmd + 1 : context->argv[0];

    fprintf(context->error, "Usage: %s [options] [filterspecs]\n", cmd);

    fprintf(context->error, "options include:\n"
                    "  -s              Set default filter to silent. Equivalent to filterspec '*:S'\n"
                    "  -f <file>, --file=<file>               Log to file. Default is stdout\n"
                    "  -r <kbytes>, --rotate-kbytes=<kbytes>\n"
                    "                  Rotate log every kbytes. Requires -f option\n"
                    "  -n <count>, --rotate-count=<count>\n"
                    "                  Sets max number of rotated logs to <count>, default 4\n"
                    "  --id=<id>       If the signature id for logging to file changes, then clear\n"
                    "                  the fileset and continue\n"
                    "  -v <format>, --format=<format>\n"
                    "                  Sets log print format verb and adverbs, where <format> is:\n"
                    "                    brief help long process raw tag thread threadtime time\n"
                    "                  and individually flagged modifying adverbs can be added:\n"
                    "                    color descriptive epoch monotonic printable uid\n"
                    "                    usec UTC year zone\n"
                    "                  Multiple -v parameters or comma separated list of format and\n"
                    "                  format modifiers are allowed.\n"
                    // private and undocumented nsec, no signal, too much noise
                    // useful for -T or -t <timestamp> accurate testing though.
                    "  -D, --dividers  Print dividers between each log buffer\n"
                    "  -c, --clear     Clear (flush) the entire log and exit\n"
                    "                  if Log to File specified, clear fileset instead\n"
                    "  -d              Dump the log and then exit (don't block)\n"
                    "  -e <expr>, --regex=<expr>\n"
                    "                  Only print lines where the log message matches <expr>\n"
                    "                  where <expr> is a regular expression\n"
                    // Leave --head undocumented as alias for -m
                    "  -m <count>, --max-count=<count>\n"
                    "                  Quit after printing <count> lines. This is meant to be\n"
                    "                  paired with --regex, but will work on its own.\n"
                    "  --print         Paired with --regex and --max-count to let content bypass\n"
                    "                  regex filter but still stop at number of matches.\n"
                    // Leave --tail undocumented as alias for -t
                    "  -t <count>      Print only the most recent <count> lines (implies -d)\n"
                    "  -t '<time>'     Print most recent lines since specified time (implies -d)\n"
                    "  -T <count>      Print only the most recent <count> lines (does not imply -d)\n"
                    "  -T '<time>'     Print most recent lines since specified time (not imply -d)\n"
                    "                  count is pure numerical, time is 'MM-DD hh:mm:ss.mmm...'\n"
                    "                  'YYYY-MM-DD hh:mm:ss.mmm...' or 'sssss.mmm...' format\n"
                    "  -g, --buffer-size                      Get the size of the ring buffer.\n"
                    "  -G <size>, --buffer-size=<size>\n"
                    "                  Set size of log ring buffer, may suffix with K or M.\n"
                    "  -L, --last      Dump logs from prior to last reboot\n"
                    "  -b <buffer>, --buffer=<buffer>         Request alternate ring buffer, 'main',\n"
                    "                  'system', 'radio', 'events', 'crash', 'default' or 'all'.\n"
                    "                  Additionally, 'kernel' for userdebug and eng builds, and\n"
                    "                  'security' for Device Owner installations.\n"
                    "                  Multiple -b parameters or comma separated list of buffers are\n"
                    "                  allowed. Buffers interleaved.\n"
                    "                  Default -b main,system,crash,kernel.\n"
                    "  -B, --binary    Output the log in binary.\n"
                    "  -S, --statistics                       Output statistics.\n"
                    "  -p, --prune     Print prune white and ~black list. Service is specified as\n"
                    "                  UID, UID/PID or /PID. Weighed for quicker pruning if prefix\n"
                    "                  with ~, otherwise weighed for longevity if unadorned. All\n"
                    "                  other pruning activity is oldest first. Special case ~!\n"
                    "                  represents an automatic quicker pruning for the noisiest\n"
                    "                  UID as determined by the current statistics.\n"
                    "  -P '<list> ...', --prune='<list> ...'\n"
                    "                  Set prune white and ~black list, using same format as\n"
                    "                  listed above. Must be quoted.\n"
                    "  --pid=<pid>     Only prints logs from the given pid.\n"
                    // Check ANDROID_LOG_WRAP_DEFAULT_TIMEOUT value for match to 2 hours
                    "  --wrap          Sleep for 2 hours or when buffer about to wrap whichever\n"
                    "                  comes first. Improves efficiency of polling by providing\n"
                    "                  an about-to-wrap wakeup.\n");

    fprintf(context->error, "\nfilterspecs are a series of \n"
                   "  <tag>[:priority]\n\n"
                   "where <tag> is a log component tag (or * for all) and priority is:\n"
                   "  V    Verbose (default for <tag>)\n"
                   "  D    Debug (default for '*')\n"
                   "  I    Info\n"
                   "  W    Warn\n"
                   "  E    Error\n"
                   "  F    Fatal\n"
                   "  S    Silent (suppress all output)\n"
                   "\n'*' by itself means '*:D' and <tag> by itself means <tag>:V.\n"
                   "If no '*' filterspec or -s on command line, all filter defaults to '*:V'.\n"
                   "eg: '*:S <tag>' prints only <tag>, '<tag>:S' suppresses all <tag> log messages.\n"
                   "\nIf not specified on the command line, filterspec is set from ANDROID_LOG_TAGS.\n"
                   "\nIf not specified with -v on command line, format is set from ANDROID_PRINTF_LOG\n"
                   "or defaults to \"threadtime\"\n\n");
}

static void show_format_help(android_logcat_context_internal* context) {
    if (!context->error) return;
    fprintf(context->error,
        "-v <format>, --format=<format> options:\n"
        "  Sets log print format verb and adverbs, where <format> is:\n"
        "    brief long process raw tag thread threadtime time\n"
        "  and individually flagged modifying adverbs can be added:\n"
        "    color descriptive epoch monotonic printable uid usec UTC year zone\n"
        "\nSingle format verbs:\n"
        "  brief      — Display priority/tag and PID of the process issuing the message.\n"
        "  long       — Display all metadata fields, separate messages with blank lines.\n"
        "  process    — Display PID only.\n"
        "  raw        — Display the raw log message, with no other metadata fields.\n"
        "  tag        — Display the priority/tag only.\n"
        "  thread     — Display priority, PID and TID of process issuing the message.\n"
        "  threadtime — Display the date, invocation time, priority, tag, and the PID\n"
        "               and TID of the thread issuing the message. (the default format).\n"
        "  time       — Display the date, invocation time, priority/tag, and PID of the\n"
        "             process issuing the message.\n"
        "\nAdverb modifiers can be used in combination:\n"
        "  color       — Display in highlighted color to match priority. i.e. \x1B[38;5;231mVERBOSE\n"
        "                \x1B[38;5;75mDEBUG \x1B[38;5;40mINFO \x1B[38;5;166mWARNING \x1B[38;5;196mERROR FATAL\x1B[0m\n"
        "  descriptive — events logs only, descriptions from event-log-tags database.\n"
        "  epoch       — Display time as seconds since Jan 1 1970.\n"
        "  monotonic   — Display time as cpu seconds since last boot.\n"
        "  printable   — Ensure that any binary logging content is escaped.\n"
        "  uid         — If permitted, display the UID or Android ID of logged process.\n"
        "  usec        — Display time down the microsecond precision.\n"
        "  UTC         — Display time as UTC.\n"
        "  year        — Add the year to the displayed time.\n"
        "  zone        — Add the local timezone to the displayed time.\n"
        "  \"<zone>\"    — Print using this public named timezone (experimental).\n\n"
    );
}
```

### 选项解析 :

- “-s”选项 : 设置输出日志的标签, 只显示该标签的日志;

- “-f”选项 : 将日志输出到文件, 默认输出到标准输出流中, -f 参数执行不成功;

- “-r”选项 : 按照每千字节输出日志, 需要 -f 参数, 不过这个命令没有执行成功;

- “-n”选项 : 设置日志输出的最大数目, 需要 -r 参数, 这个执行 感觉 跟 adb logcat 效果一样;

- “-v”选项 : 设置日志的输出格式, 注意只能设置一项;

- “-c”选项 : 清空所有的日志缓存信息;

- “-d”选项 : 将缓存的日志输出到屏幕上, 并且不会阻塞;

- “-t”选项 : 输出最近的几行日志, 输出完退出, 不阻塞;

- “-g”选项 : 查看日志缓冲区信息;

- “-b”选项 : 加载一个日志缓冲区, 默认是 main, 下面详解;

- “-B”选项 : 以二进制形式输出日志;

### 3.常用功能

- logcat -s <过滤标签> — 根据指定标签过滤

- logcat -f —- 输出到文件

- logcat | grep <过滤内容> —- 根据内容过滤log

- logcat -v time — log带有日期, 之前的版本貌似只能添加一个格式, 最新的版本貌似可以添加多个格式 显示你需要的内容, pid 等;

### 4. 应用

如何获取指定包名的程序的log, 思路就是通过runtime.exec 执行logcat语句, 读流过滤

* runtime.exec 

```
logcat –v time
```

 带有时间信息以及pid的log , 用来后面过滤进程

* 通过包名获取进程pid, runtime.exec

```
ps | grep xxx.xxx.xxx
```

* 然后在读流的过程中去过滤pid, 获取到的 就是你想要得到的应用进程的log
