##### NIO和零拷贝

1） mmap适合小数据量读写，SendFile适合大文件传输

2） mmap需要4次上下文切换，3次数据拷贝；sendFile需要3次上下文切换，最少2次数据拷贝

3）sendFile可以利用DMA方式，减少CPU拷贝，mmap则不能。（必须从内核拷贝到Socket缓冲区）



**主从Reactor多线程**

1. Reactor主线程MainReactor对象通过select监听连接事件，收到事件后，通过Acceptor处理连接事件
2. 当Acceptor处理连接事件后，MainReactor将连接分配给SubReactor
3. subReactor将连接加入到连接队列进行监听，并创建handler进行各种事件处理
4. 当有新事件发生时，subReactor就会调用对应的handler处理
5. handler通过read读取数据，分发给后面的worker线程池处理
6. worker线程池分配独立的woker线程进行业务处理，并返回结果。
7. handler收到响应结果后，再通过send将结果返回给client
8. Reactor主线程可以对应多个Reactor子线程，即MainReactor关联多个SubReactor