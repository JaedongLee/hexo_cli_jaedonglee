---
title: Java-Note-NIO
date: 2018-04-14 08:04:23
tags: NIO
category: Java
---
- 核心部分
    - Channels
    - Buffers
    - Selectors
    - 其它部分不过是一些工具类而已
- Channel
    - 所有的IO在NIO中都从一个Channel开始, Channel有点像流. 数据可以从Channel读到Buffer中, 也可以从Buffer写到Channel中
    - 主要的Channel实现<!-- more -->
        - FileChannel: 从文件中读取数据
        - DatagramChannel: 通过UDP读写网络中的数据
        - SocketChannel: 通过TCP读写网络中的数据
        - ServerSocketChannel: 监听新进来的TCP连接, 对每个新进来的连接都会创建一个SocketChannel
- Buffer: 用于和Channel进行交互. 数据是从通道读入缓冲区, 从缓冲区写入通道.
    - 本质: 缓冲区本质上是一块可以写入数据, 然后可以从中读取数据的内存. 这块内存被包装成NIO Buffer对象, 并提供一组方法, 用来方便的访问该块内存
    - 主要的Buffer实现
        - ByteBuffer
        - CharBuffer
        - DoubleBuffer
        - FloatBuffer
        - IntBuffer
        - LongBuffer
        - ShortBuffer
    - 当向buffer写入数据时, buffer会记录写下了多少数据. 一旦要读取数据, 需要通过flip()方法将Buffer从写模式切换到读模式. 在读模式下, 可以赌气之前写入到buffer的所有数据
    - 一旦读完了所有数据, 就需要清空缓冲区, 让它可以再次被写入. 有两种方式能请清空缓冲区: 调用clear()/compact()方法. clear()方法会清空整个缓冲区, compact()方法只会清除已经读过的数据, 任何未读的数据都被移到缓冲区的起始处, 新写入的数据将放到缓冲区未读数据的后面
    - Buffer的三个属性
        - capacity: 作为一个内存块, Buffer有一个固定的大小值, `你只能往里写capacity个byte, long, char等类型`. 一旦Buffer满了, 需要将其清空(通过读数据或清除数据)才能继续往里写数据
        - position: 
            - 写数据: position表示当前的位置. 最大可为capacity-1
            - 读数据: 当Buffer从写模式切换到读模式, position会被重置为0. 当从Buffer的position处读取数据时, position向前移动到下一个可读的位置
        - limit:
            - 写模式: limit == capacity
            - 读模式: limit == 写数据时的position值
    - Buffer的分配: 想获得一个Buffer对象首先要进行分配. 每一个Buffer类都有一个allocate方法. 
        - 例子
            1. 分配48个字节capacity的ByteBuffer的例子: `ByteBuffer buf = ByteBuffer.allocate(48)`
            2. 分配1024个字符的CharBuffer的例子: `CharBuffer buf = CharBuffer.allocate(1024)`
    - 向Buffer中写数据
        - 方式
            1. 从Channel写到Buffer
            2. 通过Buffer的put()方法写到Buffer里(可以自由操纵每一个单位)
    - flip()方法: flip方法将Buffer从写模式切换到读模式. 调用flip()方法会将position设为0, 并将limit设置成之前position的值
    - 从Buffer中读取数据
        - 方式
            1. 从Buffer读取数据到Channel
                - 例子: `int bytesWritten = inChannel.write(buf)`
            2. 使用get()方法从Buffer中读取数据
                - 例子: `byte aByte = buf.get()`
    - rewind()方法: Buffer.rewind()将position设为0, 使用户可以重读Buffer中的所有数据. limit保持不变, 仍然表示能从Buffer中读取多少个元素
    - clear()与compact()方法: 一旦读完Buffer中的数据, 需要让Buffer准备好再次被写入. 可以通过clear()或compact()方法来完成
        - clear(): position将被设为0, limit被设置为capacity的值. 换句话说, Buffer被清空了. Buffer中的数据并未清除, 只是这些标记告诉我们可以从哪里开始往Buffer里写数据. 如果Buffer中有未读的数据, 调用clear()方法, 数据将被遗忘, 意味着不再有任何标记会告诉你哪些数据被读过, 哪些还没有. 如果Buffer中仍有未读的数据, 且后续还需要这些数据, 但是此时想要先写些数据, 那么使用compact()方法
        - compact(): 将所有未读的数据拷贝到Buffer起始处. 然后将position设到最后一个未读元素正后面. limit属性依然像clear()方法一样, 设置成capacity. 现在Buffer准备好写数据了, 但是不会覆盖未读的数据.
    - mark()与reset()方法: 通过调用Buffer.mark()方法, 可以标记Buffer中的一个特定position. 之后可以通过调用Buffer.reset()方法恢复到这个position. 
            - 例子: 
            ```
            buffer.mark();
            // call buffer.get() a couple of times, e.g. during parsing
            buffer.reset();
            ```
    - equals()与compareTo()方法
        - equals(): 当满足下列条件时, 表示两个Buffer相等:
            1. 有相同的类型(byte/char/int等)
            2. Buffer中剩余的byte/char等的个数相等
            3. Buffer中所有剩余的byte/char相同
            - 注: 剩余元素是从position到limit之间的元素
            - 如上所言, equals只是比较Buffer的一部分, 不是每一个在它里面的元素都比较. 实际上, 它只比较Buffer中的剩余元素
        - compareTo()方法: 比较两个Buffer的剩余元素(byte/char等), 如果满足下列条件, 则认为一个Buffer"小于"另一个Buffer
            1. 第一个不相等的元素小于另一个Buffer中对应的元素
            2. 所有元素都相等, 但第一个Buffer比另一个先耗尽(第一个Buffer的元素个数比另一个少)
- Selector
    - Selector允许单线程处理多个Channel. 如果应用打开了多个连接(通道), 但每个连接的流量都很低, 使用Selector就会很方便. 例如, 在一个聊天服务器中
    - 为什么使用Selector: 仅用单个线程来处理多个Channels的好处是，只需要更少的线程来处理通道。事实上，可以只用一个线程处理所有的通道。对于操作系统来说，线程之间上下文切换的开销很大，而且每个线程都要占用系统的一些资源（如内存）。因此，使用的线程越少越好。
    - 使用Selector: 
        1. 创建一个Selector:
        ```
        Selector selector = Selector.open();
        ```
        1. 向Selector注册Channel
        ```
        channel.configureBlocking(false);
        SelectionKey key = channel.register(selector, SelectionKey.OP_READ);
        ```
        与Selector一起使用时, Channel必须处于非阻塞模式. 这意味着不能将FileChannel与Selector一起使用, 因为FileChannel不能切换到非阻塞模式. 而套接字通道可以
        - 注意register()方法的第二个参数. 这是一个"interest集合", 意思是在通过Selector监听Channel时对什么事件感兴趣. 可以监听四种不同类型的事件:
            1. Connect
            2. Accept
            3. Read
            4. Write
            - 这四种事件用SelectionKey的四个常量来表示:
                1. SelectionKey.OP_CONNECT
                2. SelectionKey.OP_ACCEPT
                3. SelectionKey.OP_READ
                4. SelectionKey.OP_WRITE
                - 如果对不止一种事件感兴趣, 那么可以用"位或"操作符将常量连接起来, 如下: `int interestSet = SelectionKey.OP_READ | SelectionKey.OP_WRITE`
            - SelectionKey: 
                - 当向Selector注册Channel时, register()方法会返回一个SelectionKey对象. 这个对象包含了一些你感兴趣的属性:
                    - interest集合: 所选择的感兴趣的事件集合. 可以通过SelectionKey读写interest集合, 像这样
                    ```
                    int interestSet = selectionKey.interestOps();
                    boolean isInterstedInAccept = (interestSet & SelectionKey.OP_ACCEPT) == SelectionKey.OP_ACCEPT;
                    boolean isInterestedInConnect = interestSet & SelectionKey.OP_CONNECT;
                    boolean isInterestedInRead = interestSet & SelectionKey.OP_READ;
                    boolean isInterestedInWrite = interestSet & SelectionKey.OP_WRITE;
                    ```
                    可以看出, 用"位与"操作interest集合和给定的SelectionKey常量, 可以确定某个确定的事件是否在interest集合中
                    - ready集合: 已准备就绪的通道的集合(interest集合中的), 可以这样访问ready集合
                    ```
                    int readySet = selectionKey.readyOps();
                    ```
                    检测interest集合可以使用检测interest集合一样的方法
                    ```
                    selectionKey.isAcceptable();
                    selectionKey.isConnectable();
                    selectionKey.isReadable();
                    selectionKey.isWritable();
                    ```
                    - Channel
                    - Selector
                    - 附加的对象(可选)
                - `当把一个channel注册到指定的Selector上时, 实际上就是将(channel, selector)封装成一个SelectionKey对象, 并将此对象保存在Selector对象中`
        2. 调用它的select()方法, 这个方法会一直阻塞到某个注册的通道有事件就绪. 一旦这个方法返回, 线程就可以处理这些事件, 事件的例子有如新连接进来/数据接收
            - 调用select()方法, 会返回所感兴趣的事件(如连接/接受/读或写)已经准备就绪的那些通道.
            - 重载的select()方法
                - int select(): 保持阻塞知道至少有一个通道在注册的事件上就绪, 返回的int表示注册数
                - int select(long timeout): 和select()一样, 除了最长会阻塞timeout毫秒
                - int selectNow(): 不会阻塞, 不管什么通道立即返回, 如果没有就绪, 则返回0
            - selectedKeys()
                - 如果调用select()方法后返回的值大于1, 就可以通过调用selector的selectedKeys()方法, 访问"已选择键集(selected key set)"中的就绪通道. 例如: `Set selectedKeys = selector.selectedKeys()`, 可以遍历这个已选择的键的集合来访问就绪的通道
    - 