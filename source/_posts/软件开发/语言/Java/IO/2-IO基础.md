---
title: Java IO(二) IO 系统基础
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: Java IO
keywords: NIO
excerpt: IO 是一门语言中的难点，对于 Java 而言，有着丰富的 IO 体系，所以本系列将通过相关资料梳理出 Java IO 的脉络
date: 2022-01-24 22:15:08
thumbnailImage:
---

<!-- toc -->

<span style="color:blue;font-weight:bold">输出流会自动创建文件，输入流读取之前文件必须存在</span>

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/IOClass.svg %}

## 概述

自从 Java 1.0 版本以来，Java 的 I/O 库发生了明显改变，在原来面向字节的类中添加了面向字符和基于 Unicode 的类。在 JDK 1.4 中，添加了 NIO 类，添加进来主要是为了改进性能以及功能。因此，在充分理解 Java I/O 系统之前需要学习相当数量的类。

## File

File 类的名字有一定的误导性，实际上此类既能代表一个特定文件的名称，又能代表一个目录下的一组文件的名称。如果它指的是一个文件集，就可以对此集合调用`list()`方法

```java
//根据一个路径得到 File 对象
File(String pathname);
//根据一个目录和一个子文件得到 File 对象
File(String parent, String child);
//根据一个父 File 对象和一个子文件/目录得到 File 对象
File(File parent, String child);
```

{% alert warning no-icon%}
:notes: `toString()`被重写为文件的绝对路径
{%endalert%}

File 的众多成员方法就是常见的文件操作，如：创建文件、创建目录，移动、重命名等

### 创建功能

```java
//创建文件
public boolean createNewFile() throws IOException;
//创建目录,如果存在这样的目录就不创建,并且返回false,如果父目录不存在,子文件就不会创建
public boolean mkdir();
//创建目录,如果父目录不存在,就会创建多级目录 <==> mkdir -p
public boolean mkdirs();
```

:notes:创建文件主要要点：

- 文件名和目录不可以同名

- 明确需要创建的是一个<span style="color:red;font-weight:bold">文件</span>还是一个<span style="color:red;font-weight:bold">目录</span>

- 相对路径，相对的是当前**正在运行的 java 项目文件**

### 删除功能

```java
public boolean delete();	//删除文件 | 目录(当前目录下必须为空时,才能删除) <==> rm
```

### 重命名功能

```java
public boolean renameTo(File dest);// <==> mv 命令
```

```java
File file = new File("/Users/xxx/Desktop/test");
File file1 = new File("/Users/xxx/Desktop/test1");
file.renameTo(file1);//如果两个文件的父路径不一致,功能将变为重命名+剪切
```

### 判断功能

```java
public boolean isDirectory();	//目录?
public boolean isFile();		//文件?
public boolean isHidden();		//隐藏?
public boolean exists();		//存在?
public boolean canExecute();	//可执行?
public boolean canRead();		//可读?
public boolean canWrite();		//可写?
```

### 基本获取功能

```java
public String getAbsolutePath();//绝对路径
public String getPath();//相对路径
public String getName();//文件名字
public String getParent();//如果父目录存在,返回父目录的路径,否则返回null
public long length();//文件大小,单位:字节数(B)
public long lastModified();//获取最后一次的修改时间,毫秒数
```

```java
File file = new File("/Users/xxx/Desktop/timg.jpg");
System.out.println(file.getAbsolutePath());
System.out.println("" + (file.length()) + 'B');
System.out.println(file.getParent());
```

### 高级获取功能

```java
public String[] list();//获取指定目录下的所有文件或者文件夹的名称数组
public String[] list(FilenameFilter filter);//FilenameFilter接口过滤然
public File[] listFiles();//返回文件对象数组，只深入一层
```

```java
//需求:获取file目录下所有以.jpg结尾的文件
public static void main(String[] args){
  File file = new File("/Users/xxx/Desktop");
  File[] files = file.listFiles();
  for (File tempFile : files) {
    if (tempFile.getName().endsWith(".jpg"))
        System.out.println(tempFile.getAbsolutePath());
  }
}
```

使用`FilenameFilter`接口,充当过滤器，过滤文件

```java
public static void main(String[] args){
	File file = new File("/Users/hmc/Desktop");
	File[] files =
    	file.listFiles(
  	      new FilenameFilter() {
	          @Override
          	public boolean accept(File dir, String name) {
        	    //返回true则将当前File(dir,name)加入到files数组中,否则不进行操作
      	      return new File(dir, name).isFile() && name.endsWith(".jpg");
    	      }
  	      });
	for (File file1 : files) {
  	System.out.println(file1.getAbsolutePath());
	}
}
```

## 输入和输出

编程语言的 I/O 类库中常使用「 流 」 这个抽象概念，它代表任何又能力产出数据的数据源对象或者有能力接受数据的数据端对象。流屏蔽了实际的 I/O 设备中处理数据的细节。Java 类库中的 I/O 类分成输入和输出两部分。通过继承，任何自`InputStream`或`Reader`派生而来的类都含有 `read()`基本方法，用于读取单个字节或者字节数组。同样，任何自`OuputStream`或`Writer`派生而来的类都含有名为 `write()`的基本方法，用于写单格字节或者字节数组。

:sparkles: 但是，通常并不会直接用到这些方法，它们之所以存在是因为别的类可以使用它们，以便提供更有用的接口。因此，日常开发中很少使用单一的类来创建流对象，而是通过堆叠多个流对象来创建所期望的功能「 这是装饰者设计模式 」。实际上，Java 中的流库让人迷惑的主要原因就在于：创建单一的结果流，却需要创建多个对象。

### `InputStream` 类型

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java/java-inputstream.png)

`InputStream`的作用是用来表示哪些从不同数据源产生输入的类，这些数据源包括：

- 字节数组
- String 对象
- 文件
- 管道
- 一个由其他种类多流组成的序列
- 其他数据源，如 `Internet` 连接等

每一种数据源都有相应的 `InputStream`子类。另外，FilterInputStream 也属于一种 InputStream ，为装饰器类提供基类「 装饰器可以把属性或有用的接口与输入流连接在一起

|                   类                    |                             功能                              |                             构造器参数                             | 如何使用                                 |
| :-------------------------------------: | :-----------------------------------------------------------: | :----------------------------------------------------------------: | ---------------------------------------- |
|         `ByteArrayInputStream`          |            允许将内存的缓冲区当作 InputStream 使用            |                       缓冲区，字节将从中取出                       | 作为一种数据源：将其与 FilterInputStream |
| `StringBufferInputStream`<br />(已弃用) |                 将 String 转换成 InputStream                  |                 字符串，底层实际使用 StringBuffer                  | 作为一种数据源：将其与 FilterInputStream |
|            `FileInputStream`            |                     用于从文件中读取信息                      |           字符串，表示文件名、文件或 FileDescriptor 对象           | 作为一种数据源：将其与 FilterInputStream |
|           `PipedInputStream`            |     产生用于写入 PipedOutputStream 的数据，实现管道话概念     |                         PipedOutputStream                          | 作为一种数据源：将其与 FilterInputStream |
|          `SequenceInputStream`          |        将两个或多个 InputStream 转换成单一 InputStream        | 两个 InputStream 对象或一个容纳 InputStream 对象的容器 Enumeration | 作为一种数据源：将其与 FilterInputStream |
|           `FilterInputStream`           | 抽象类，作为装饰器的接口，为其他的 InputStream 类提供有用功能 |                                                                    |                                          |

### `OutputStream`类型

该类别的类决定了输出所要去往的目标：字节数组`byte[]`（并不是`String`，当然可以用字节数组自己创建字符串）、文件或管道。另外，FilterOutputStream 为装饰器类提供了一个基类，装饰器类把属性或者有用的接口与输出流连接了起来

|          类           |                                    功能                                    |                    构造器参数                    |                                 如何使用                                 |
| :-------------------: | :------------------------------------------------------------------------: | :----------------------------------------------: | :----------------------------------------------------------------------: |
| ByteArrayOutputStream |        在内存中创建缓冲区，所有送往该流的数据最终都要放置在此缓冲区        |            缓冲区初始化尺寸（可选的）            | 用于指定数据的目的地：将其与 FilterOutputStream 对象相连以提供有用的接口 |
|   FileOutputStream    |                             用于将信息写至文件                             | 字符串，表示文件名、文件或 FilterDescriptor 对象 | 用于指定数据的目的地：将其与 FilterOutputStream 对象相连以提供有用的接口 |
|   PipedOutputStream   | 任何写入其中的信息都会自动作为相关 PipedInputStream 的输出。实现管道化概念 |                 PipedInputStream                 | 用于指定数据的目的地：将其与 FilterOutputStream 对象相连以提供有用的接口 |
|  FilterOutputStream   |   抽象类，作为装饰器的接口，其中装饰器为其他 OutputStream 提供有用的功能   |                                                  |                                                                          |

## 添加属性和有用的接口

FilterInputStream 和 FilterOutputStream 是用来提供装饰器类接口以控制特定输入流和输出流的两个类，分别由 InputStream 和 OutputStream 派生而来，这两个类是装饰器的能够工作的必要条件

### 通过 FilterInputStream 从 InputStream 读取数据

FilterInputStream 能够完成两件完全不同的事情。其中，DataInputStream 允许读取不同的基本类型数据以及 String 对象（所有的方法都以`read`开头，例如：`readBytte()`,`readFloat()`等等），搭配相应的 DataOutputStream，就可以通过数据流将基本类型的数据从一个地方迁移到另一个地方。具体是哪些地方，由 InputStream 可以读取到来源决定

其他 FilterInputStream 类则在内部修改 InputStream 的行为方式，例如：是否缓存，是否保留它所读取过的行（允许我们查询行数或设置行数），以及是否把单一字符推回输入流等等。

不管正在连接的是什么 I/O 设备，几乎每次都要对输入进行缓存，所以 I/O 类库将<font style="color:red">无缓存输入作为特殊情况</font>

|          类           |                                 功能                                  |           构造器参数           |                               如何使用                               |
| :-------------------: | :-------------------------------------------------------------------: | :----------------------------: | :------------------------------------------------------------------: |
|    DataInputStream    | 与 DataOutputStream 搭配使用，可以按照可移植方式从流读取基本数据类型  |          InputStream           |                  包含用于读取基本数据类型的全部接口                  |
|   BufferInputStream   | 使用此类可以防止每次读取时都进行实际写操作<br />代表 「 使用缓冲区 」 | InputStream 可以指定缓冲区大小 | 本质上不提供接口，只不过是向进程中添加缓冲区所必需的，与接口对象搭配 |
| LineNumberInputStream |   跟踪输入流中的行好，可调用 getLineNumber() 和 setLineNumber(int)    |          InputStream           |              仅增加了行号，因此可能要与接口对象搭配使用              |
|  PushbackInputStream  |     具有能弹出一个字节的缓冲区，因此可以将读到的最后一个字符回退      |          InputStream           |             通常作为编译器的扫描器，开发过程中几乎用不到             |

### 通过 FilterOutputStream 从 OutputStream 写入数据

与 DataInputStream 对应的是 DataOutputStream，它可以将各种基本数据类型以及 String 对象格式化输出到流中。如此操作，最终任何机器上的任何 DataInputStream 都能够读取它们。其中写操作都是以`write`开头，例如`writeByte()`,`writeFloat()`等等

PrintStream 最初的目的是为了可视化格式打印所有的基本数据类型以及 String 对象。这与 DataOutputStream 是不同的，后者的目的是将数据置入流中，使 DataInputStream 能够可移植地重构它们。PrintStream 内有两个重要的方法：`print()`和`println()`。对它们进行了重载，以便可打印出各种数据类型。print() 和 println() 之间的差异是，后者在操作完毕后会添加一个换行符。

:cry: PrintStream 可能会有些问题，因为它捕获了所有的 IOExceptions，因此必须使用 `checkError()`自行测试错误状态，如果出现错误此方法将返回`true`，另外，PrintStream 也未完全国际化，不能以平台无关的方式处理换行动作 「 值的庆幸的是，以上出现的问题，最终都在 PrintWriter 中得以解决 」

BufferOutputStream 是一个修改过的 OutputStream，它对数据流使用缓冲技术，因此当每次向流写入时，不必每次都进行实际的物理写动作。所以在进行输出时，可能更经常使用它。FilterOutputStream 的类型以及功能如下表

|          类          |                                                功能                                                |                          构造器参数                          |                                如何使用                                |
| :------------------: | :------------------------------------------------------------------------------------------------: | :----------------------------------------------------------: | :--------------------------------------------------------------------: |
|   DataOutputStream   |                                    与 DataInputStream 搭配使用                                     |                         OutputStream                         |                   包含用于写入基本类型数据的全部接口                   |
|     PrintStream      |           用于产生格式化输出，其中 DataOutputStream 处理数据的存储，PrintStream 处理显示           | OutputStream 可以用 boolean 值指示是否在每次换行时清空缓冲区 |     应该是对 OutpuStream 对象的 final 封装，kennel 会经常使用到他      |
| BufferedOutputStream | 使用它以避免每次发送数据时都要进行实际的写操作，代表 「 使用缓冲区 」，可以调用 flush() 清空缓冲区 |               OutputStream 可以指定缓冲区大小                | 本质上并不提供接口，只不过是向进程中添加缓冲区所必需的，与接口对象搭配 |

## Reader 和 Writer

Reader 和 Writer 是 Java 1.1 中新增加的两个类，提供兼容 Unicode 与面向字符的 I/O 功能，这两个类并不会替代已有的 InputStream 以及 OutputStream 。有时必须把来自于字节层次结构中的类和字符层次结构中的类结合起来使用。为了实现这个目的，要用到适配器类，这是一种适配器模式。InputStreamReader 可以将 InputStream 转换为 Reader，而 OutputStreamWriter 能够将 OutputStream 转换为 Writer。

设计 Reader 和 Writer 继承层次结构主要是为了国际化，老的 I/O 流继承层次结构仅支持 8 位字节流，并且不能很好地处理 16 位 Unicode 字符，由于 Unicode 用于字符国际化（ Java 本身的 char 也是 16 位的 Unicode），所以添加 Reader 和 Writer 继承层次结构是为了在所有的 I/O 操作中都支持 Unicode

### 数据的来源与去处

几乎所有原始的 Java I/O 流类都有相应的 Reader 和 Writer 类来提供天然的 Unicode 操作，这并不代笔着无论什么情况都可以使用字符流而不使用字节流。其实在某些特殊的情况下，就只支持面向字节流而没有面向字符流的支持，因此，最明智的做法是尽量尝试使用 Reader 和 Writer

下面展示了在两个继承层次结构中，信息的来源和去处之间的对应关系

| 来源与去处：Java 1.0 类 |           对应的 Java 1.1 类           |
| :---------------------: | :------------------------------------: |
|       InputStream       | Reader<br />适配器：InputStreamReader  |
|      OutputStream       | Writer<br />适配器：OutputStreamWriter |
|     FileInputStream     |               FileReader               |
|    FileOutputStream     |               FileWriter               |
|  ByteArrayInputStream   |            CharArrayReader             |
|  ByteArrayOutputStream  |            CharArrayWriter             |
|    PipedInputStream     |              PipedReader               |
|    PipedOutputStream    |              PipedWriter               |

这两个不同的继承层次结构中的结构即使不能说完全相同，也是非常相似了

### 更改流的行为

对于 InputStream 和 OutputStream 来说，可以使用 FIlterInputStream 和 FilterOutputStream 的装饰器子类来修改流已满足特殊需求，Reader 和 Writer 的类继承层次结构继续沿用相同思想，但是具体细节却并不相同，下表相对于前一表格来说，左右之间的对应关系的近似程序更加粗略一些。造成这种差别的原因是因为类的不同组织形式不同。例如： BufferedOutputStream 是 FilterOutputStream 的子类，但是 BufferedWriter 并不是 FIlterWriter 的子类，但是这些类的接口却是十分相似的

| Java 1.0 类          |                              Java 1.1 类                               |
| -------------------- | :--------------------------------------------------------------------: |
| FilterInputStream    |                              FilterReader                              |
| FilterOutputStream   |                    FilterWriter（抽象类，没有子类）                    |
| BufferedInputStream  |                             BufferedReader                             |
| BufferedOutputStream |                             BufferedWriter                             |
| DataInputStream      | 最好使用 BufferedReader 而不是使用 DataInputStream 的 readLine（）方法 |
| PritStream           |                              PrintWriter                               |
| StreamTokenizer      |                StreamTokenizer（接受 Reader 的构造器）                 |
| PushbackInpuStream   |                             PushbackReader                             |

## 自我独立的类：RandomAccessFile

RandomAccessFile 适用于由大小已知的文件操作，可以使用`seek()`将记录从一处转移到另一处，然后读取或者修改记录。此类与 InputStream 和 OutputStream 没有任何关联，大部分的方法都是本地的，这么做是因为此类拥有和别的 I/O 类型本质不同的行为（可以在一个文件内向前和向后移动）。在任何情况下，它都是自我独立的，直接从 Object 派生而来。

从本质上来说，RandomAccessFile 的工作方式类似于把 DataInputStream 和 DataOutputStream 组合起来使用，还添加了一些方法

```java
public RandomAccessFile(File file,String mode)throws FileNotFoundException;
public RandomAccessFile(String name,String mode)throws FileNotFoundException;
```

:notes:mode：和 C 一样，打开文件的方式,(`r`,`rw`,`rws`,`rwd`)

```java
// 同OutputStream流相同
public void write(int b)throws IOException;
public void write(byte[] b)throws IOException;
public void write(byte[] b)throws IOException;

// 同 DataOutputStream
public final void writeInt(int v)throws IOException;
public final void writeLong(long v)throws IOException;

public long getFilePointer()throws IOException;//获取当前指针位置
public void seek(long pos)throws IOException;//跳转文件指针到指定字节位置

public int read()throws IOException;
public final char readChar()throws IOException;
//-----------------------------------------同DataInputStream流相同
```

## I/O 流的典型使用方式

尽管可以通过不同的方式组合 I/O 流类，但日常开发中也就只用到其中的几种组合，下面的例子可以作为典型的 I/O 用法的基本参考。在这些例子中，异常处理都被简化为将异常传递给控制台，但在真正的项目中，不应该这么做，并且还需要考虑更加复杂的错误处理方式

### 使用缓冲方式读取文件

如果想要打开一个文件，并读取文件汇总的内容，可以使用 String 或 FIle 对象作为文件名的 FileInputReader，为了读取速度，最好再加上缓冲，也就是将所产生的引用传给 BufferedReader 构造器就可以。由于 BufferedReader 也提供了 readLine() 方法，所以这是我们的最终对象和进行读取的接口，当 readLine() 返回 null 时，此时就将文件所有内容读取完毕

```java
	public static void readFile(String filePath) throws IOException {
		BufferedReader reader = new BufferedReader(new FileReader(filePath));
		String received = null;
		while ((received = reader.readLine()) != null) {
			System.out.println(received);
		}
		reader.close();
	}
```

### 读取内存中的数据

StringReader 是 Reader 的一个装饰类，用法是读取一个 String 字符串，类似的还存在 StringWriter 类

:question: 为什么有这两个类？

可以方便进行 I/O 流的测试，目前所有的流创建起来非常复杂（来自于文件或者网络），使用 StringReader 或 StringWriter 就能够通过非常简单的方式创建一个 Reader 或 Writer，并且这个流读取/写入的内容就是构造器中传入的字符串。并且通过此类读取/写入的字符串最终都是保存在内存中的，并没有直接传输到硬盘上。

```java
	public static void readMemory(String filePath) throws IOException {
		StringReader stringReader = new StringReader(new BufferedReader(new FileReader(filePath)).readLine());
		int recv;
    //每次从 StringReader 中读取一个字符
		while ((recv = stringReader.read())!=-1){
			System.out.println((char) recv);
		}
	}
```

### 格式化的从内存读取数据

读取格式化的数据，使用 DataInputStream ，它是一个面向字节的 I/O 类，并不是面向字符的，可以使用 InputStream 读取任何数据

```java
	public static void formatMemoryInput(String path) {
		try {
			DataInputStream dataInputStream =
					new DataInputStream(new ByteArrayInputStream(new BufferedReader(new FileReader(path)).readLine().
							getBytes(StandardCharsets.UTF_8)));
			while (dataInputStream.available()!=0)) {
				System.out.println((char) dataInputStream.readByte());
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
```

:notes: 可以使用 available() 方法查看还有多少可供读取的字符，读取的媒介类型不同时，此方法的工作方式是不同的。此方法想要实现的功能是：在没有阻塞的情况下所能读取的字节数。对于文件，这意味着整个文件，对于不同类型的流，可能就不是这样的，所以这个方法需要谨慎使用。

也可以通过捕获异常的方式来监测输入的末尾，但是使用异常进行流控制，被认为是对异常特性的错误使用

### 基本的文件输出

FileWriter 对象可以向文件写入数据，首先创建一个与指定文件连接的 FileWriter，实际上，通常会用 BufferedWriter 将其包装起来用以缓冲输出，「 缓冲往往能显著地增加 I/O 操作的性能 」，在本例中，为了提供格式化机制，它被装饰成 PrintWriter

```java
public static void basicFileOut(String path, String outpath) throws IOException {
   BufferedReader bufferedReader = new BufferedReader(new FileReader(path));
   PrintWriter printWriter = new PrintWriter(new BufferedWriter(new FileWriter(outpath)));
   int lineCount = 1;
   String recv;
   while ((recv = bufferedReader.readLine()) != null) {
      printWriter.println(lineCount + ":" + recv);
      lineCount++;
   }
   bufferedReader.close();
   printWriter.close();
}
```

如果不为所有的输出文件调用 close()，就会发现缓冲区内容不会被刷新清空，最终输出文件中的内容就不会完整。Java SE5 在 PrintWriter 中添加了一个辅助构造器，使得你不必在每次希望创建文本文件并向其中写入时，都去执行所有的装饰工作。

```java
public static void basicFileOut(String path, String outpath) throws IOException {
  BufferedReader bufferedReader = new BufferedReader(new FileReader(path));
  // 新的辅助构造器，只需要传入文件输出路径即可
  PrintWriter printWriter = new PrintWriter(outpath);
  int lineCount = 1;
  String received;
  while ((received = bufferedReader.readLine()) != null) {
    printWriter.println(lineCount + ":" + received);
    lineCount++;
  }
  bufferedReader.close();
  printWriter.close();
}
```

### 存储和恢复数据

PrintWriter 可以对数据进行格式化，但是为了输出可供另一个流恢复的数据，需要用 DataOutputStream 写入数据，并用 DataInputStream 恢复数据。这些流可以是任何形式，但在下面的示例中使用的是一个文件，并且对于读和写都进行了缓冲处理。

```java
public static void storingAndRecoveringData(String inputPath,String outputPath) throws IOException {
   DataOutputStream dataOutputStream =
         new DataOutputStream(new BufferedOutputStream(new FileOutputStream(outputPath)));
   dataOutputStream.writeDouble(3.1415926);
   dataOutputStream.writeUTF("That was pi");
   dataOutputStream.writeDouble(1.41413);
   dataOutputStream.writeUTF("square root of 2");
   dataOutputStream.close();
   DataInputStream dataInputStream = new DataInputStream(new BufferedInputStream(new FileInputStream(inputPath)));
   System.out.println(dataInputStream.readDouble());
   System.out.println(dataInputStream.readUTF());
   System.out.println(dataInputStream.readDouble());
   System.out.println(dataInputStream.readUTF());
}
```

如果我们使用 DataOutputStream 写入数据，Java 保证可以使用 DataInputStream 准确地读取数据。无论读和写数据的平台多么的不同，只要两个平台上都有 Java，这种问题就不会再发生。

### 标准的异常处理代码

```java
public static void main(String[] args) {
  String path = "/Users/hmc/Desktop/test.txt";
  FileOutputStream fileOutputStream = null;
  try {
    fileOutputStream = new FileOutputStream(path);
    fileOutputStream.write("hello java".getBytes());
  } catch (IOException e) {
    e.printStackTrace();
  } finally {
    if (fileOutputStream != null) {
      try {
        fileOutputStream.close();
      } catch (IOException e) {
        e.printStackTrace();
      }
    }
  }
}
```

```java
public void fileOutputStreamLearning() {
    	//jdk8 新特性
		try (FileOutputStream fos = new FileOutputStream("D:\\JavaProject\\java-code\\.gitignore1", true)) {
			fos.write(22);// 写入一个字符
			fos.write("这就是我要写入的内容".getBytes());
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

```

## 标准 I/O

标准 I/O 这个术语参考的是 Unix 中 「 程序所使用的单一信息流 」这个概念。程序是所有输入都可以来自于标准输入，它的所有输出也都可以发送到标准输出，以及所有的错误信息都可以发送到标准错误。

:sparkles: 标准 I/O 的意义在于：可以很容易地把程序串联起来，一个程序的标准输出可以称为另一程序的标准输入

### 从标准输入中读取

按照标准 I/O，Java 提供了 System.in、System.out 和 System.err，其中`System.out`已经事先被包装成了 PrintStream 对象，System.err 同样也是 PrintStream，但 System.in 却是一个没有被包装过的未经加工的 InputStream。这意味着尽管我们可以立即使用 System.out 和 System.err，但是在读取 System.in 之前必须对其进行包装。

```java
BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
String s;
while ((s = in.readLine()) != null && s.length() != 0) {
   System.out.println(s);
}
```

### 将 System.out 转换成 PrintWriter

System.out 是一个 PrintStream ，而 PrintWriter 是一个 OutputStream，PrintWriter 有一个可以接受 OutputStream 作为参数的构造器

```java
// true 表示 auto flush，以便开启自动清空功能，否则，你可能看不到输出
PrintWriter out = new PrintWriter(System.out, true);
out.println("hello print writer");
```

### 标准 I/O 重定向

Java 的 System 类提供了一些简单的静态方法调用，以允许程序对标准输入、输出和错误 I/O 流进行重定向

```java
setIn(InputStream);
setOut(PrintStream);
setErr(PrintStream);
```

:notes: 重定向操作的是字节流，而不是字符流，因此我们使用的是 InputStream 和 OutputStream 而不是 Reader 和 Writer

## 压缩

Java IO 类库中的类支持读写压缩格式的数据流，因此可以使用这些流对其他的 I/O 类进行封装，以提供压缩功能。

:sparkles: 这些类不是从 Reader 和 Writer 类派生而来的，而是属于 InputStream 和 OutputStream 继承层次结构的一部分。

|       压缩类        |                           功能                           |
| :-----------------: | :------------------------------------------------------: |
| CheckedInputStream  |       GetCheckSum() 为任何 InputStream 产生校验和        |
|  CheckOutputStream  |       GetCheckSum() 为任何 OutputStream 产生校验和       |
| DeflateOutputStream |                       压缩类的基类                       |
|   ZipOutputStream   | 一个 DeflateOutputStream，用于将数据压缩成 ZIP 文件格式  |
|  GZIPOutputStream   | 一个 DeflateOutputStream，用于将数据压缩成 GZIP 文件格式 |
| InflaterInputStream |                      解压缩类的基类                      |
|   ZipInputStream    | 一个 InflaterInputStream，用于解压缩 Zip 文件格式的数据  |
|   GZIPInputStream   | 一个 InflaterInputStream，用于解压缩 GZip 文件格式的数据 |

## 文件字节流

文件字节流用来向文件进行相关的读取与写出操作，具体有两个类：FileInputStream 以及 FileOutputStream

### FileInputStream

```java
public FileInputStream(File file)throws FileNotFoundException;
public FileInputStream(FileDescriptor fdObj);
public FileInputStream(String name)throws FileNotFoundException;
```

```java
//每次读取一个字节,并将字节的ASCII码值返回,如果读取到文件末尾,返回-1
public int read() throws IOException;
//返回值是实际读取的长度，如果没有数据读入，返回-1
public int read(byte[] b)throws IOException;
//返回读取的总长度，如果没有数据读入，返回-1
public int read(byte[] b,int off,int len)throws IOException;
```

```java
FileInputStream fileInputStream = new FileInputStream(path);
int bs = 0;
while ((bs = fileInputStream.read()) != -1) {
  System.out.print((char) bs);//使用print不会手动给程序增加换行
}
fileInputStream.close();
```

```java
FileInputStream fileInputStream1 = new FileInputStream(path);
byte[] by = new byte[1024];
int len = 0;
while ((len = fileInputStream1.read(by)) != -1) {
  String str = new String(by, 0, len);
  System.out.println(str);
}
fileInputStream1.close();
```

### FileOutputStream

创建对象就会强制生成新文件，清空源文件内容

```java
public FileOutputStream(File file)throws FileNotFoundException;
//如果append为true,则进行文件的追加写入
public FileOutputStream(File file,boolean append)throws FileNotFoundException;
public FileOutputStream(String name)throws FileNotFoundException;
public FileOutputStream(String name,boolean append)throws FileNotFoundException;
```

```java
public void write(int b)throws IOException;//写入b对应ASCII码表中的字符
public void write(byte[] b)throws IOException;//写一个字节数组
public void write(byte[] b,int off,int len)throws IOException;//写一个数组的一部分
public void close()throws IOException;
```

```java
public static void main(String[] args) throws IOException {
	File file = new File("E:\\lifeInformation\\test.txt");
    //源代码throws FileNotFoundException异常需要自己处理此异常
    FileOutputStream fileOutputStream = new FileOutputStream(file);
		//会抛出IOException异常，IOException是FileNotFoundException异常的父类
    fileOutputStream.write("hello world io".getBytes());
    fileOutputStream.close();
  }
```

:question:为什么一定要关闭文件？

让流对象变成垃圾，这样就可以被垃圾回收器回收并通知系统去释放该文件相关的资源

## 字节缓冲区流

通过`FileInputStream`中**字节数组读写数据**的确比读取一个字节的方式快；然而如果拥有一个**缓存区**将比单纯的字节数组读写数据更快，Java 提供了带缓冲区的字节类，这种类被称为:**缓冲区类**，<span style="color:red;font-weight:bold">专门对 FileInputStream 以及 FileOutputStream 包装</span>主要有如下两类:

缓冲区写数据流类（`BuffededOutputStream`）和缓冲区读数据流类（`BufferedInputStream`）

### BufferedOutputStream

使用缓冲区，更加快速高效的写数据，由于 BufferedOutputStream 流是作用在 OutputStream 流之上的，所以`BufferedOutputStream`构造方法的参数就应该传入`OuputStream对象`

```java
//使用默认的缓冲区大小，通常使用
public BufferedOutputStream(OutputStream out);
//指定缓冲区的大小，一般用不上
public BufferedOutputStream(OutputStream out,int size);
```

```java
// 高效缓冲区类,每次写一个字节
public void write(int b)throws IOException;
// 继承自FilterOutputStream方法,高效缓冲区类,每次写一个缓冲区数组
public void write(byte[] b)throws IOException;
// 将缓冲区字节数组某一区间上的内容写入流中
public void write(byte[] b,int off,int len)throws IOException;
```

```java
    BufferedOutputStream bufferedOutputStream =
        new BufferedOutputStream(
            new FileOutputStream(("E:\\research_plan\\2020..17~\\test.txt")));
    bufferedOutputStream.write("hello java bufferedOutputStream".getBytes());
    bufferedOutputStream.close();
```

### BufferedInputputStream

使用缓冲区对流中的数据进行高效的读取，同缓冲区写数据流相同，基于字节输入流，所以构造参数需要字节输入流

```java
BufferedInputStream(InputStream in);
BufferedInputStream(InputStream in, int size);
```

```java
public int read()throws IOException;
public int read(byte[] b,int off,int len)throws IOException;
public void read(byte[] b)throws IOException;//继承自FilterInputStream方法
public void mark(int readlimit);//设置mark
public void reset()throws IOException;//重置读取指针到mark处
public boolean markSupported();//判断当前类是否支持mark()以及reset()方法
```

```java
  public static void main(String[] args) throws IOException {
    BufferedInputStream bufferedInputStream =
        new BufferedInputStream(new FileInputStream("E:\\research_plan\\2020..17~\\test.txt"));
    bufferedInputStream.mark(0);
    byte[] by = new byte[1024];
    int len = 0;
    while ((len = bufferedInputStream.read()) != -1) {
      System.out.print((char) len);
    }
    System.out.println("-------------------------");
    bufferedInputStream.reset();//重置指针，从文件头部再一次读取文件内容
    while ((len = bufferedInputStream.read(by)) != -1) {
      System.out.println(new String(by, 0, len));
    }
    bufferedInputStream.close();
  }
```

:notes: Java 文件读取虽有多种方式，但是多种读取方式**共享同个文件读取指针**，意味着：**前一种读取方式读完整个文件时，后一种方式将读取不到任何内容**（此时文件指针已经移动到了文件尾部）。存在重复读取一个文件时，就需要重置文件指针，保证后一种读取方式能够正确读取文件内容。

:thinking:为什么缓冲区数据流的构造参数需要我们手动传入输入输出流，而不是和输入输出流一样，直接传入一个文件路径？

这是一种装饰器模式，如果使用文件路径，缓冲区流就使得 Java IO 系统更加复杂

## 转换流

将字节流转换为字符流,写入数据的**编码**以及读出数据的**解码**过程均是在**转换流类中进行**，其他的过程并不需要涉及这个步骤

### OutputStreamWriter

此类继承自`java.io.Writer`一种输出转换字符流转换类，一个字符 = 两个字节。

- 字符流数据不直接进入硬盘而是**写入缓冲区**，所以每次写完之后需要`flush()`或者`close()`。

- 与字节流不同，转换流每次**写入的是什么最终在文件中就会保存什么**，而不会出现字节流的情况:不论写什么最终都会**转换为 char 类型的字符**.比如写入`97`,最终在文件中保存的就是`a`.

```java
//采用默认编码把字节流的数据转换为字符流
public OutputStreamWriter(OutputStream out);
//根据指定编码把字节流转换为字符流
public OutputStreamWriter(OutputStream out,String charsetName)
    throws UnsupportedEncodingException;
```

```java
//----------------------------------------------------继承的
public void write(String str)throws IOException;
public void write(char[] cbuf)throws IOException;
//----------------------------------------------------重写的
public void write(String str,int off,int len)throws IOException;
public void write(char[] cbuf,int off,int len)throws IOException;
public void write(int c)throws IOException;//写一个字符
public void flush()throws IOException;//刷新缓冲，将数据写入硬盘
public void close()throws IOException;//先刷新缓冲区，然后关闭文件
```

每次使用一个**字节输出流的类**以及**字符集**初始化字符流对象即可实现

```java
    OutputStreamWriter outputStreamWriter =
        new OutputStreamWriter(new FileOutputStream("testx.txt"), "GBK");
    outputStreamWriter.write("中国");
    outputStreamWriter.close();
```

:vs:flush()与 close()的区别？

- **close()**关闭流对象，但是先刷新一次缓冲区，关闭之后，流对象不可以继续使用
- **flush()**仅仅刷新缓冲区，刷新之后，流对象还可以继续使用

### FileWriter

继承自`java.io.OutputStreamWriter`，由于常见的操作都是使用本地默认编码，所以不用指定编码

```java
public FileWriter(String fileName,boolean append)throws IOException;
public FileWriter(String fileName)throws IOException;//直接指明写入文件的路径字符串
public FileWriter(File file,boolean append)throws IOException;
public FileWriter(File file)throws IOException;
```

### InputStreamReader

是字节流变换为**字符流**的桥梁，<span style="color:red;font-weight:bold">并不支持`mark()`以及`reset()`</span>

```java
InputStreamReader(InputStream in);
InputStreamReader(InputStream in, String charsetName);
```

```java
//字符流每次读入一个字符
public int read()throws IOException;
//字符流每次读入一个数组
public int read(char[] cbuf,int offset,int length)throws IOException;
```

每次**读入一个字符**的输入流

```java
OutputStreamWriter outputStreamWriter =
    new OutputStreamWriter(new FileOutputStream("testx.txt"), "GBK");
outputStreamWriter.write("中国");
outputStreamWriter.close();
InputStreamReader inputStreamReader =
    new InputStreamReader(new FileInputStream("testx.txt"), "utf-8");
    int ch = 0;
    while ((ch = inputStreamReader.read()) != -1) {
      System.out.print((char) ch);
    }
inputStreamReader.close();
```

每次**读入一个数组**的输入流

```java
OutputStreamWriter outputStreamWriter =
    new OutputStreamWriter(new FileOutputStream("testx.txt"), "GBK");
outputStreamWriter.write("中国");
outputStreamWriter.close();
InputStreamReader inputStreamReader =
    new InputStreamReader(new FileInputStream("testx.txt"), "utf-8");
char[] cbuf = new char[1024];
int len = cbuf.length;
int offset = 0;
while ((len = inputStreamReader.read(cbuf, offset, len)) != -1) {
  System.out.print(cbuf);
}
```

### FileReader

FileOutputStream 流对应的字符流

```java
public FileReader(File file)throws FileNotFoundException;
public FileReader(String fileName)throws FileNotFoundException;
```

## 字符缓冲区流

字符流为了高效读写，同样提供了对应了字符缓冲流

- BufferedReader
- BufferedWriter

### BufferedWriter

```java
//使用Writer的具体子类FileWriter初始化字符缓冲输出流，缓冲区使用默认大小
public BufferedWriter(Writer out);
//指定缓冲区大小
public BufferedWriter(Writer out,int sz);
```

```java
public void write(String s,int off,int len)throws IOException;//写入特定长度的字符串
public void write(char[] cbuf,int off,int len)throws IOException;
public void write(int c)throws IOException;
public void flush()throws IOException;
public void close()throws IOException;
//在当前行插入一个换行符，能够根据系统不同输入不同的字符
public void newLine()throws IOException;
```

### BufferedReader

```java
//使用Reader的具体子类FileReader初始化字符缓冲流，缓冲区大小使用默认长度
public BufferedReader(Reader in);
public BufferedReader(Reader in,aint sz);
```

```java
public int read()throws IOException;//每次读入一个字符
public int read(char[] cbuf,int off,int len)throws IOException;//每次读入一个字符数组
//每次读一行,包含该行内容的字符串，不包含任何终止符.如果到达文件结尾返回null
public String readLine()throws IOException;
```

### LineNumberReader

继承自`java.io.BufferedReader`,具有字符缓冲区流两个所不具有的特定方法`打印行号`

```java
LineNumberReader(Reader in);
LineNumberReader(Reader in, int sz);
```

```java
public int getLineNumber();//获取当前读取文件的行号
public void setLineNumber(int lineNumber);//设置行号的其实记录值
```

```java
BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter("test.txt", true));
bufferedWriter.write("这里似乎有点意思");
bufferedWriter.newLine();
bufferedWriter.close();
BufferedReader bufferedReader = new BufferedReader(new FileReader("test.txt"));
char[] cbuf = new char[1024];
int len = cbuf.length;
while ((len = bufferedReader.read(cbuf, 0, len)) != -1) {
	System.out.print(cbuf);
}
bufferedReader.close();
```

## 数据输入输出流

继承自`java.io.FilterInputStream`,可以对基本数据类型操作的 IO 类

### DataInputStream

读取`DateOutputSream`写入文件中的内容

```
DataInputStream(InputStream in);
```

```java
public final short readShort()throws IOException;//读取一个short类型的数据
public final long readLong()throws IOException;//读取一个long类型的数据
public final char readChar()throws IOException;//读取一个char类型的数据
public final xxx readXxx()throws IOException;//读取一个基本数据类型为xxx的数据
```

:notes:由于写入的数据并不是正规的字节或者字符数据，因此使用记事本打开是一堆乱码，只能通过相应的`DataInputStream`进行读取，并且写入的是什么类型读取时也应该调用相应类型的 API

### DatsOutputStream

继承自`java.io.FilterOutputStream`，用于传递基本数据类型以及 String 对象供 DataInputStream 读取

```java
public DataOutputStream(OutputStream out);
```

```java
public final void writeByte(int v)throws IOException;
public final void writeInt(int v)throws IOException;
//向文件中写一个基本数据类型为xxx的数据
public final void writeXxx(int v)throws IOException;
```

```java
DataOutputStream dataOutputStream =	new DataOutputStream(new FileOutputStream("text.txt", true));
dataOutputStream.writeInt(3);
dataOutputStream.writeShort();
dataOutputStream.writeChar('a');
dataOutputStream.close();
DataInputStream dataInputStream = new DataInputStream(new FileInputStream("text.txt"));
System.out.println(dataInputStream.readInt());
System.out.println(dataInputStream.readShort());
System.out.println(dataInputStream.readChar());
dataInputStream.close();
```

## 内存操作流

用于处理临时存储信息，<span style="color:red;font-weight:bold">程序结束，数据就从内存中消失</span>

|       字节数组        |    字符数组     |    字符串    |
| :-------------------: | :-------------: | :----------: |
| ByteArrayInputStream  | CharArrayReader | StringReader |
| ByteArrayOutputStream | CharArrayWriter | StringWriter |

### ByteArrayOutputStream

- 继承自`java.io.OutputStream`,属于字节流的范畴，写入的数据并不会保存到本地磁盘中，而是存在于内存缓冲区中.
- 此流并不需要显示调用 close()去关闭，并且手动调用 close()后仍然可以向其中写入数据
- 由于数据仅仅存在于内存缓冲区中，所以如果需要读取当前数据，需要将缓冲区中的数据保存到一个`byte[]`或者`String`中，而后使用`ByteArrayInputStream`流对这些数据进行读取

```java
public ByteArrayOutputStream();//创建一个内存操作流，内存缓冲区容量大小随着输入数据自适应增长
public ByteArrayOutputStream(int size);//创建一个字节数组输出流，并且字节数组输出流的大小限定为size
```

```java
public byte[] toByteArray();//将当前字节数组输出流中的内容返回，提供给输入流进行读取
public String toString();	//将当前字节数组输出流中的内容转换成String类型返回
//将输出流中的内容按照特定的编码集返回
public String toString(String charsetName)throws UnsupportedEncodingException;
public void writeTo(OutputStream out)throws IOException;//将当前字节输出流中的内容写入到OutputStream流中
public void write(int b);	//写入一个字符
public void write(byte[] b,int off,int len);//一次性写入一个数组
public int size();//获取当前字节数组的长度，用于读取当前输出流内部数据时使用
```

### ByteArrayInputStream

继承自`java.io.InputStream`,属于字节流的范畴

```java
public ByteArrayInputStream(byte[] buf);	//使用从输出流中传递出来的字节数组，查看当前内存缓冲区中的数据
public ByteArrayInputStream(byte[] buf,int offset,int length);
```

```java
//读取方法和字节流的read方法相同
public int read(byte[] b,int off,int len);
public int read();
```

## 打印流

:sparkles:打印流是一种写数据的流，最终系统会进行读取，没有提供给用户对应的读数据接口；可以向此流中写入任意类型的数据，如果启动了自动刷新，能够自动刷新写入的内容；此流和其他的输出流相同是可以直接操作文本文件的

:thinking: 共有哪些流对象是可以直接操作文本文件的尼？

目前共有如下几种流可以直接作用于文件中：FileInputStream、FileOutputStream、FileReader、FileWriter、PrintStream（字节打印流）、PrintWriter（字符打印流）

打印字符流,继承自`java.io.Writer`可以直接将内容写入到一个文件中,但是其也拥有自己的特有方法:能够自动刷新,每写一次就自动刷新文件

```java
public PrintWriter(File file);
PrintWriter(String fileName);
//---------------------------------------使用字节流
PrintWriter(OutputStream out);
PrintWriter(OutputStream out, boolean autoFlush);//启动自动刷新
//---------------------------------------使用字符流
PrintWriter(Writer out);
PrintWriter(Writer out, boolean autoFlush)
```

```java
public void print(boolean b);//print重载了所有基本数据类型,但是不支持自动刷新
//重载了常用的基本数据类型,开启自动刷新时,会将内容自动自动保存至文件,每次会在行末尾增加一个换行符
public void println(boolean x);
public void write(char[] buf,int off,int len);//继承自父类的传统写方法
public void close();//如果未启动自动刷新,则依靠flush()或者close()将内容写入文件
public void flush();
public PrintWriter format(String format,Object... args);
public PrintWriter printf(String format,Object... args);
```

:notes:如果启动了自动刷新,则 `println`, `printf`, or`format` methods 将会自动刷新.

```java
println();//等价于
/**********************/
bw.write();
bw.newLine();
bw.flush()
```

```java
PrintWriter printWriter = new PrintWriter(new FileWriter("text.txt"), true);
printWriter.println("中国");
printWriter.close();//养成关闭文件的好习惯
```

## 标准输入输出流

System 类中的两个成员变量:

```java
public static final InputStream in;//标准输入流
public static final PrintStream out;//标准输出流
public static final PrintStream err;
```

### 键盘录入数据

:sparkles: 一个程序要想获取外部提供的输入数据，共有以下三种：

- 通过 main 方法的 args 接受参数
- 使用 JDK5 以后的新特性 Scanner
- 使用字符缓冲流包装标准输入流(System.in)实现

```java
System.out.println("使用Scanner进行数据的读取");
Scanner scanner = new Scanner(System.in);
int x = scanner.nextInt();
System.out.println(x);
System.out.println("使用bufferedreader进行字符串的读取");
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
System.out.println(bufferedReader.readLine());
System.out.println("使用bufferedreader进行int的读取");
String line = bufferedReader.readLine();
int i = Integer.parseInt(line);
System.out.println(i);
```

使用字符流将数据写入到控制台

```java
BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(System.out));
bufferedWriter.write("使用bufferedreader进行字符串的读取\n");
bufferedWriter.flush();
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
System.out.println(bufferedReader.readLine());
```

## 合并流

以前的文件流传输过程:

```java
//a.txt---> b.txt
//c.txt---> b.txt
//需求
//a.txt+c.txt --> b.txt
```

### SequenceInputStream

继承自`java.io.InputStream`,主要将多个文件合并处理

```java
public SequenceInputStream(InputStream s1,InputStream s2);
public SequenceInputStream(Enumeration<? extends InputStream> e);
//Enumeration 是vector中一个方法的返回值类型
```

其中`Enumeration`<E>是一个接口，只有两种方法

```java
boolean hasMoreElements();//等同于iterator中的hasNext()
E nextElement();//等同于iterator中的next()方法
```

```java
   for (Enumeration<E> e = v.elements(); e.hasMoreElements();)
       System.out.println(e.nextElement());
```

均继承自`InputStream`没有自己的特有方法

```java
void close();
int	read();
int	read(byte[] b, int off, int len);
```

## 序列化流

- 序列化流：
  - 将对象按照流一样的方式存入文件中或在网络中传输(对象-->流)
  - `ObjectOutputStream`
- 反序列化流:
  - 将文本文件中的流对象数据或者网络中的流对象数据还原成对象(流数据-->对象)
  - `ObjectInputStream`

### ObjectIntputStream

```java
public ObjectInputStream(InputStream in)throws IOException;
protected ObjectInputStream()throws IOException,SecurityException;
```

Person 类实现了序列化接口，那么它本身也会拥有一个标记值，如果相应的类做出了更改，这个 ID 就会更改，如果更改之后没有进行序列化操作，继续进行反序列的操作将会出现异常。

```java
//从当前输入流自动读取一个对象
public final Object readObject()throws IOException,ClassNotFoundException;
```

### ObjdectOutputStream

```java
protected ObjectOutputStream()throws IOException,SecurityException;
public ObjectOutputStream(OutputStream out)throws IOException;
```

`java.io.NotSerializableException`：未序列化异常，需要实现`Serializable`接口，以启用序列化功能。Serializable 接口没有任何方法，这种没有方法的接口为`标记接口`

```java
//序列化一个对象
public final void writeObject(Object obj)throws IOException;
```

### ID

:question: 在实际开发中，可能还需要使用以前写过的数据，不能重新写入，如何处理？

- 将当前类的 ID 固定，由于每次会读取当前类中固定的 ID 值，所以对类的任何修改，均不会造成之后的访问失败

```java
static final long serialVersionUID = 1L;//推荐使用IDE帮助生成随机的ID
```

### 序列化选择

一个类中可能有多个成员变量，有些可能并不需要进行序列化，此使就需要使用`transient`关键字，声明不需要序列化的成员变量

```java
class Person implements Serializable{
  public String name;
  private transient int age;//序列化时并不会序列化此字段，反序列化时读取到的值就是默认值
      @Override
  public String toString() {
    return "Person{" +
            "name='" + name + '\'' +
            ", age=" + age +
            '}';
  }
}
```

## Properties

属性集合类，是一个可以和 I/O 流结合使用的集合类，`properties`可保存在流中或从流中加载，属性列表中每个键及其对应的值都是一个字符串

```java
public Properties();
public Properties(Properties defaults);
```

```java
public V put(String key,String value);
//------------------------------------------------继承自hashtable
public Object setProperty(String key,String value);//添加元素
public String getProperty(String key);//获取元素
public Set<String> stringPropertyNames();//获取所有的键的集合
//把文件中的数据读取到集合中，文件中的数据必须是键值对形式
public void load(Reader reader)throws IOException;
//把集合中的数据存储到文件，
public void store(OutputStream out,String comments)throws IOException;
```

## 附： 编码表

|    码表    |                              用途                               |
| :--------: | :-------------------------------------------------------------: |
| ISO-8859-1 |                    拉丁码表,8 位表示一个数据                    |
|    GB23    |                        中国的中文编码表                         |
|   GBK23    |                        中国的中文编码表                         |
|    GBK     | 中国的中文编码表升级,融合了更多的中文文字符号,java 默认中文码表 |
|  GB18030   |                         GBK 的取代版本                          |
|  Unicode   |             国际标准码,融合了多种文字,java 默认码表             |
|   UTF-8    |       最多用三个字节来表示一个字符,具有较好的兼容其他码表       |

[Java I/O](https://www.bookstack.cn/read/Interview-Notebook/notes-Java%20IO.md)
[Java Properties 类](https://www.runoob.com/java/java-properties-class.html)
[深入学习 JAVA -IO 流详解](https://segmentfault.com/a/1190000023008389)
[使用 StringWriter 和 StringReader 的好处](https://blog.csdn.net/weixin_42476601/article/details/85698762)
