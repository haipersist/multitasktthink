# 多进程模型

一个master,多个worker。

先说一下pre-fork

pre-fork服务器和fork服务器相似，通过一个单独的进程来处理每条请求。

不同的是，pre-fork服务器会通过预先开启大量的进程，等待并处理接到的请求。

由于采用了这种方式来开启进程，服务器并不需要等待新的进程启动而消耗时间，因而能够以更快的速度处理多用户请求。

另外，pre-fork服务器在遇到极大的高峰负载时仍能保持良好的性能状态。这是因为不管什么时候，只要预先设定的所有进程都已被用来处理请求时，服务器仍可追加额外的进程。

Master进程只用来管理这些子进程，不负责接收客户端请求；而子进程用来处理请求。Master和Worker之间通过信号沟通。 如下图（摘自一位大牛）：

![](<../.gitbook/assets/image (6).png>)



进程之间是通过信号通信，那么信号是至关重要的，gunicorn提供几种不同的信号：

* HUP，重启所有的配置和所有的worker进程
* QUIT，正常关闭，它会等待所有worker进程处理完各自的东西后关闭
* INT/TERM，立即关闭，强行中止所有的处理
* TTIN，增加一个worker进程
* TTOU，减少一个worker进程
* USR1，重新打开由master和worker所有的日志处理
* USR2，重新运行master和worker
* WINCH，正常关闭所有worker进程，保持主控master进程的运行
