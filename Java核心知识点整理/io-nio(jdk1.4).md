

	#流：流是一组有顺序的，有起点和终点的字节集合，是对数据传输的总称或抽象，本质是数据传输，根据数据传输特性将流抽象为各种类，方便更直观的进行数据操作。
	#io
		#分类：根据数据类型分为：字符流和字节流，根据流的方向分为输入流和输出流
		#字节流以字节为单位，字符流以字符为单位，一次可读2个字节，字节流可用于处理所有类型数据，而字符流只能处理字符类型的数据，只要是处理纯文本就优先考虑使用字符流，除此之外都使用字节流
		#字节输入流InputStream：
			#InputStream 是所有的输入字节流的父类，它是一个抽象类。
			#ByteArrayInputStream、StringBufferInputStream、FileInputStream 是三种基本的介质流，它们分别从Byte 数组、StringBuffer、和本地文件中读取数据。PipedInputStream 是从与其它线程共用的管道
			#ObjectInputStream 和所有FilterInputStream 的子类都是装饰流（装饰器模式的主角）。
		#字节输出流OutputStream：
			#OutputStream 是所有的输出字节流的父类，它是一个抽象类。
			#ByteArrayOutputStream、FileOutputStream 是两种基本的介质流，它们分别向Byte 数组、和本地文件中写入数据。PipedOutputStream 是向与其它线程共用的管道中写入数据，
			#ObjectOutputStream 和所有FilterOutputStream 的子类都是装饰流。
		#字符输入流Reader：
			#Reader 是所有的输入字符流的父类，它是一个抽象类。
			#CharReader、StringReader 是两种基本的介质流，它们分别将Char 数组、String中读取数据。PipedReader 是从与其它线程共用的管道中读取数据。
			#BufferedReader 很明显就是一个装饰器，它和其子类负责装饰其它Reader 对象。
			#FilterReader 是所有自定义具体装饰流的父类，其子类PushbackReader 对Reader 对象进行装饰，会增加一个行号。
			#InputStreamReader 是一个连接字节流和字符流的桥梁，它将字节流转变为字符流。FileReader 可以说是一个达到此功能、常用的工具类，在其源代码中明显使用了将FileInputStream 转变为Reader 的方法。我们可以从这个类中得到一定的技巧。Reader 中各个类的用途和使用方法基本和InputStream 中的类使用一致。后面会有Reader 与InputStream 的对应关系。
		#字符输出流Writer：
			#Writer 是所有的输出字符流的父类，它是一个抽象类。
			#CharArrayWriter、StringWriter 是两种基本的介质流，它们分别向Char 数组、String 中写入数据。PipedWriter 是向与其它线程共用的管道中写入数据，
			#BufferedWriter 是一个装饰器为Writer 提供缓冲功能。
			#PrintWriter 和PrintStream 极其类似，功能和使用也非常相似。
			#OutputStreamWriter 是OutputStream 到Writer 转换的桥梁，它的子类FileWriter 其实就是一个实现此功能的具体类（具体可以研究一SourceCode）。功能和使用和OutputStream 极其类似，后面会有它们的对应图。
	#nio
		#io靠字节字符单项传输，I/O 操作的阻塞管理粒度是以服务于请求的线程为单位的,有可能大量的线程会闲置,处于盲等状态，造成I/O资源利用率不高，影响整个系统的性能。
		#而NIO是靠块， 也就相当于一个Buffer， 一块一块的传输， 速度较快。一个NIO流可以同时传输多个块等， 也就是所谓的异步传输
		#非阻塞原理：事件驱动机制：事件到的时候触发，而不是同步的去监视事件。
		#NIO 工具包提出了基于Buffer（缓冲区）、Channel（通道）、Selector（选择器）的新模式
		#buffer内部构造
			#一个 buffer 主要由 position,limit,capacity 三个变量来控制读写的过程。
			#参数：写模式；读模式
				#position：当前写入的单位数据数量；当前读取的单位数据位置。
				#limit：代表最多能写多少单位数据和容量是一样的；代表最多能读多少单位数据，和之前写入的单位数据量一致。
				#capacity：buffer 容量；buffer 容量
			#常见方法：
				#flip(): 写模式转换成读模式
				#rewind() ：将 position 重置为 0 ，一般用于重复读。
				#clear() ：清空 buffer ，准备再次被写入 (position 变成 0 ， limit 变成 capacity) 。
				#compact(): 将未读取的数据拷贝到 buffer 的头部位。
				#mark() 、 reset():mark 可以标记一个位置， reset 可以重置到该位置。
			#常见类：ByteBuffer 、 MappedByteBuffer 、 CharBuffer 、 DoubleBuffer 、 FloatBuffer 、 IntBuffer 、 LongBuffer 、ShortBuffer 。
		#channel常见类：FileChannel 、 DatagramChannel(UDP) 、 SocketChannel(TCP客户端) 、 ServerSocketChannel(TCP服务端)
		#Selector：异步 IO 的核心类，它能检测一个或多个通道 (channel) 上的事件，并将事件分发出去。使用一个 select 线程就能监听多个通道上的事件，并基于事件驱动触发相应的响应。而不需要为每个 channel 去分配一个线程。
		#SelectionKey：包含了事件的状态信息和时间对应的通道的绑定。
		#代码示例：
			String infile = "C:\\copy.sql";  
			String outfile = "C:\\copy.txt";  
			// 获取源文件和目标文件的输入输出流  
			FileInputStream fin = new FileInputStream(infile);  
			FileOutputStream fout = new FileOutputStream(outfile);  
			// 获取输入输出通道  
			FileChannel fcin = fin.getChannel();  
			FileChannel fcout = fout.getChannel();  
			// 创建缓冲区  
			ByteBuffer buffer = ByteBuffer.allocate(1024);  
			while (true) {  
				// clear方法重设缓冲区，使它可以接受读入的数据  
				buffer.clear();  
				// 从输入通道中将数据读到缓冲区  
				int r = fcin.read(buffer);  
				// read方法返回读取的字节数，可能为零，如果该通道已到达流的末尾，则返回-1  
				if (r == -1) {  
					break;  
				}  
				// 写模式转换成读模式， 
				buffer.flip();  
				// 从输出通道中将数据写入缓冲区  
				fcout.write(buffer);