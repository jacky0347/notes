---
### 2.java IO+NIO 源码加应用

![java IO 类图](http://orm-chimera-prod.s3.amazonaws.com/1234000001805/figs/lj4e_1201.png)

#### **2.1 File 类** 
* File 和 FileFilter（accept 方法）类为策略模式示例
  过滤逻辑为for循环匹配。

* File包含文件和目录的所有操作均在File类中

* File 和 FileHandler 为代理模式部分操作如比较实际
  由FileHandler完成

* RandomAccessFile 主要功能是完成随机读取功能，可以读取指定位置的内容。(用的比较少)

  ``` java
  /**
   * Created by jacky on 2018/1/17.
   */
  public class FileTest {
      public static void main(String[] args) throws IOException {
          File file = new File(path);
          System.out.println("pathSeparator:"+File.pathSeparator);
          System.out.println("pathSeparatorChar:"+File.pathSeparatorChar);
          System.out.println("separator:"+File.separator);
          System.out.println("canExecute():"+file.canExecute());
          System.out.println("canRead():"+file.canRead());
          System.out.println("canWrite():"+file.canWrite());
          System.out.println("getAbsoluteFile()"+file.getAbsoluteFile());
          System.out.println("getAbsolutePath()"+file.getAbsolutePath());
          System.out.println("getCanonicalFile()"+file.getCanonicalFile());
          System.out.println("getCanonicalPath()"+file.getCanonicalPath());
          System.out.println("getFreeSpace()"+file.getFreeSpace());
          System.out.println("getName()"+file.getName());
          System.out.println("getParent()"+file.getParent());
          System.out.println("toURI()"+file.toURI());
          System.out.println("toURL()"+file.toURL());
          System.out.println("中国".getBytes());
          String[] files = file.list();
          for(String filename:files) {
              System.out.println(filename);
          }

      }
  }
  ```

  ​

**2.1 字节流**

**inputStream 类**



| Modifier and Type | Method and Description                                       |
| ----------------- | ------------------------------------------------------------ |
| `int`             | `**available**()`Returns an estimate of the number of bytes that can be read (or skipped over) from this input stream without blocking by the next invocation of a method for this input stream. |
| `void`            | `**close**()`Closes this input stream and releases any system resources associated with the stream. |
| `void`            | `**mark**(int readlimit)`Marks the current position in this input stream. |
| `boolean`         | `**markSupported**()`Tests if this input stream supports the `mark` and `reset` methods. |
| `abstract int`    | `**read**()`Reads the next byte of data from the input stream. |
| `int`             | `**read**(byte[] b)`Reads some number of bytes from the input stream and stores them into the buffer array `b`. |
| `int`             | `**read**(byte[] b,    int off,    int len)`Reads up to `len` bytes of data from the input stream into an array of bytes. |
| `void`            | `**reset**()`Repositions this stream to the position at the time the `mark` method was last called on this input stream. |
| `long`            | `**skip**(long n)`Skips over and discards `n` bytes of data from this input stream. |

**实现类**

| 类名                    | 描述                                                         |
| ----------------------- | ------------------------------------------------------------ |
| ByteArrayInputStream    | 读取byte数组                                                 |
| FileInputSteam          | 读取文件                                                     |
| ObjectInputStream       | 读取对象可以读取序列化的对象和基本类型数据，通常包装其他InputStream |
| PipedInputStream        | 从PipedOutputStream 读取                                     |
| SequenceInputStream     | 包装类：顺序读取多个InputSteam的数据                         |
| StringBufferInputStream | 从String中读取                                               |

```java
/**
* ByteArrayInputStream read 逻辑
* int 16个字节、byte 8个字节 ,最高位为符号位
* byte 转 int 要补高8位需要，需要低8位和源byte 一直 必须要 & 0xff
* 需要补码、转码的知识：正数存的原值，负数存的补码
*byte类型的数字要&0xff再赋值给int类型，其本质原因就是想保持二进制补码的一致性。
*
* @return  the next byte of data, or <code>-1</code> if the end of the
*          stream has been reached.
*/
public synchronized int read() {
    return (pos < count) ? (buf[pos++] & 0xff ) : -1;
}
```

**包装类 **基类 FilterInputStream

| 类名                                                         | 描述                                           |
| ------------------------------------------------------------ | ---------------------------------------------- |
| BufferedInputSteam                                           | 提供缓存，缓存数组大小可在创建时设置(较为常用) |
| DataInputStream                                              | 读取基本数据类型的接口                         |
| LineNumberInputStream(del)                                   | 弃用了                                         |
| [PushbackInputStream](http://tool.oschina.net/uploads/apidocs/jdk_7u4/java/io/PushbackInputStream.html) |                                                |
|                                                              |                                                |