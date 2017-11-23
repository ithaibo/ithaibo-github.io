# Java NIO
Java NIO(New IO)

java1.4开始

## NIO vs IO
标准IO基于字节流和字符流进行操作，而NIO基于Channel和Buffer进行操作。

## NIO的基本特性
 - Channels and Buffers
 - Non-blocking IO
 - Selectors

## Java NIO Core API
 - Channel
 - Buffer
 - Selector
 - Pipe
 - FileLock

## NIO数据读写方式
 - 从Channel读到Buffer
 - 从Buffer写到Channel
 
## Channels and Buffers
NIO中的所有IO操作都是从Channel开始的。Channel和流有点类似。
数据从Channel读到Buffer；
从Buffer写到Channel

a list of the primary Channel implementations in Java NIO:
 - FileChannel
 - DatagramChannel
 - SocketChannel
 - ServerSocketChannel

a list of the core Buffer implementations in Java NIO:
 - ByteBuffer
 - CharBuffer
 - DoubleBuffer
 - FloatBuffer
 - IntBuffer
 - LongBuffer
 - ShortBuffer
 - MappedByteBuffer

## Selectors
A Selector allows a single thread to handle multiple Channel's.

This is handy if your application has many connections (Channels) open, 
but only has low traffic on each connection.