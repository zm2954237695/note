BIO：JDK1.4之前的传统IO模型，本身是同步阻塞模式。线程发起IO请求后，一直阻塞IO，直到缓冲区数据就绪后，再进入下一步操作。

NIO：同步非阻塞的IO模型。线程发起IO请求后，立即返回。同步指的是必须等待IO缓冲区的数据就绪，非阻塞是指，用户线程不原地等待IO缓存区，可以先做一些其他操作，但是要定时轮询检查IO缓冲区数据是否就绪。

AIO：真正意义上的异步非阻塞IO模型。异步非阻塞IO应该让内核系统完成，用户线程只需要告诉内核，当缓冲区就绪后，通知我或者执行我交给你的回调函数。