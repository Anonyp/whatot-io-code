title: perf 简介
date: 2013-06-27 21:06:33
categories:
tags: [program, perf]
---


应该详细阅读:

* [EN] https://perf.wiki.kernel.org/index.php/Tutorial
* [EN] https://perf.wiki.kernel.org/index.php/Main_Page
* [EN] http://stackoverflow.com/questions/1777556/alternatives-to-gprof
* [CN] http://www.ibm.com/developerworks/cn/linux/l-cn-perf1/
* [CN] http://blog.csdn.net/bluebeach/article/details/5912062


**From man pages**

perf - Performance analysis tools for Linux

Performance counters for Linux are a new kernel-based subsystem that provide a framework for all things performance analysis. It covers hardware level (CPU/PMU, Performance Monitoring Unit) features and software features (software counters, tracepoints) as well.

<!--more-->

## Commands:

### perf --help

```
$ perf --help

 usage: perf [--version] [--help] COMMAND [ARGS]

 The most commonly used perf commands are:
   annotate        Read perf.data (created by perf record) and display annotated code
   archive         Create archive with object files with build-ids found in perf.data file
   bench           General framework for benchmark suites
   buildid-cache   Manage build-id cache.
   buildid-list    List the buildids in a perf.data file
   diff            Read two perf.data files and display the differential profile
   evlist          List the event names in a perf.data file
   inject          Filter to augment the events stream with additional information
   kmem            Tool to trace/measure kernel memory(slab) properties
   kvm             Tool to trace/measure kvm guest os
   list            List all symbolic event types
   lock            Analyze lock events
   record          Run a command and record its profile into perf.data
   report          Read perf.data (created by perf record) and display the profile
   sched           Tool to trace/measure scheduler properties (latencies)
   script          Read perf.data (created by perf record) and display trace output
   stat            Run a command and gather performance counter statistics
   test            Runs sanity tests.
   timechart       Tool to visualize total system behavior during a workload
   top             System profiling tool.
   trace           strace inspired tool
   probe           Define new dynamic tracepoints

 See 'perf help COMMAND' for more information on a specific command.
```

### perf help COMMAND

或者 ``perf COMMAND -h``

exmaple:

```
$ perf stat -h

 usage: perf stat [<options>] [<command>]

    -e, --event <event>   event selector. use 'perf list' to list available events
        --filter <filter>
                          event filter
    -i, --no-inherit      child tasks do not inherit counters
    -p, --pid <pid>       stat events on existing process id
    -t, --tid <tid>       stat events on existing thread id
    -a, --all-cpus        system-wide collection from all CPUs
    -g, --group           put the counters into a counter group
    -c, --scale           scale/normalize counters
    -v, --verbose         be more verbose (show counter open errors, etc)
    -r, --repeat <n>      repeat command and print average + stddev (max: 100)
    -n, --null            null run - dont start any counters
    -d, --detailed        detailed run - start a lot of events
    -S, --sync            call sync() before starting a run
    -B, --big-num         print large numbers with thousands' separators
    -C, --cpu <cpu>       list of cpus to monitor in system-wide
    -A, --no-aggr         disable CPU count aggregation
    -x, --field-separator <separator>
                          print counts with custom separator
    -G, --cgroup <name>   monitor event in cgroup name only
    -o, --output <file>   output file name
        --append          append to the output file
        --log-fd <n>      log output to fd, instead of stderr
        --pre <command>   command to run prior to the measured command
        --post <command>  command to run after to the measured command
    -I, --interval-print <n>
                          print counts at regular interval in ms (>= 100)
        --aggr-socket     aggregate counts per processor socket
```

## COMMAND 简单说明

* ``perf list`` 命令可以列出所有能够触发 perf 采样点的事件

* ``perf stat [-e <EVENT> | --event=EVENT] [-a] <command>`` or ``perf stat [-e <EVENT> | --event=EVENT] [-a] — <command> [<options>]`` 以概括精简的方式提供被调试程序运行的整体情况和汇总数据

* ``perf top [-e <EVENT> | --event=EVENT] [<options>]`` 用于实时显示当前系统的性能统计信息

* ``perf record [-e <EVENT> | --event=EVENT] [-l] [-a] <command>``
	* ``perf -g report`` 显示调用关系

