---
title: http 性能测试工具 wrk
date: 2019-04-15 19:05:05
categories:
  - 开发杂记
---

##### wrk 有什么用？

wrk 是一个的 http 压力测试工具。
我们可以通过 wrk 来测试我们的服务器大概能承受多少的并发量。

<!--more-->

##### 安装 wrk

Ubuntu

```bash
sudo apt-get install build-essential libssl-dev git -y
git clone https://github.com/wg/wrk.git wrk
cd wrk
make
# 添加可执行命令
sudo cp wrk /usr/local/bin
```

Centos

```bash
sudo yum groupinstall 'Development Tools'
sudo yum install -y openssl-devel git
git clone https://github.com/wg/wrk.git wrk
cd wrk
make
# 添加可执行命令
sudo cp wrk /usr/local/bin
```

OS X

[文档](https://github.com/wg/wrk/wiki/Installing-wrk-on-Linux)

Windows 10

[文档](https://github.com/wg/wrk/wiki/Installing-wrk-on-Windows-10)

##### 简单的使用 wrk

```bash
wrk -t10 -c100 -d5s --latency  "http://www.baidu.com"
```

以上命令 wrk 会开启 `10` 个线程来处理 `100` 个客户端对`http://www.baidu.com` 进行 `5` 秒的压力测试

```bash
Running 5s test @ http://www.baidu.com
  10 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   182.38ms  259.62ms   1.71s    89.69%
    Req/Sec   100.21    122.58   839.00     96.38%
  Latency Distribution
     50%   11.54ms
     75%  268.11ms
     90%  468.33ms
     99%    1.26s
  4552 requests in 5.01s, 66.82MB read
  Socket errors: connect 0, read 0, write 0, timeout 2
Requests/sec:    908.83
Transfer/sec:     13.34MB
```

分析以上结果

```bash
Running 5s test @ http://www.baidu.com # 对 http://www.baidu.com 性能测试 5 秒
  10 threads and 100 connections # 10 个线程运行 100 个连接
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   182.38ms  259.62ms   1.71s    89.69%
    Req/Sec   100.21    122.58   839.00     96.38%
    # 每秒完成 100.21 个请求中平均响应时间 182.38ms
    # 每秒完成 122.58 个请求中标准差响应时间 259.62ms (如果相差太大, 说明服务器不太稳定)
    # 每秒完成 839.00 个请求中最大响应时间 1.71.s
  Latency Distribution
     50%   11.54ms # 50% 的请求在 11.54ms 以内处理完成
     75%  268.11ms # 75% 的请求在 268.11ms 以内处理完成
     90%  468.33ms # 90% 的请求在 468.33ms 以内处理完成
     99%    1.26s  # 99% 的请求在 1.26s 以内处理完成
  4552 requests in 5.01s, 66.82MB read # 5.01s 处理完成 4552 个请求, 一共读取了 66.82MB 数据
  Socket errors: connect 0, read 0, write 0, timeout 2 # 2 个请求超时
Requests/sec:    908.83 # 每秒完成 908.83 个请求
Transfer/sec:     13.34MB # 每秒读取 13.34MB 数据

```
