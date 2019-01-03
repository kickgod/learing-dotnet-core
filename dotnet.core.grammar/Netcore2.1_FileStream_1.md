### [Net Core 流操作](#top) :grey_exclamation: <b id="top"></b>
`文件和流 I/O（输入/输出）是指在存储媒介中传入或传出数据` [`文件和流`](https://docs.microsoft.com/zh-cn/dotnet/standard/io/) :white_check_mark:

------

- [x] [`1.简单的介绍一下`](#intro)


------

#####  :octocat: [1.简单的介绍一下](#top) <b id="intro"></b> 
`我们常常使用下面五个类来进行文件操作,但是处理文件有更加强大的选项,使用流操作`
* `File - 提供用于创建、复制、删除、移动和打开文件的静态方法，并可帮助创建 FileStream 对象。`
* `FileInfo - 提供用于创建、复制、删除、移动和打开文件的实例方法，并可帮助创建 FileStream 对象。`
* `Directory - 提供用于创建、移动和枚举目录和子目录的静态方法。`
* `DirectoryInfo - 提供用于创建、移动和枚举目录和子目录的实例方法。`
* `Path - 提供用于以跨平台的方式处理目录字符串的方法和属性。`<br/>

`概括`:`流是一个用于传输数据的对象,数据可向两个方向传输`
* `如果数据从外部传输到程序中,这就是读取流`
* `如果数据从程序传输到外部源中,这就是写入流`<br/>

`外部流可能来自于文件,但是也不完全是一个文件,他还可以来是` <br/>
* `使用一个网络协议读写网络上的数据,其目的是选择数据,或从另一个计算机上发送数据`
* `读写到命名管道`
* `吧数据读写到一个内存区域上` <br/>

`有一些流只能写入,有些流只能读取,一些流随机存取【可以定位游标】根据基础数据源或存储库，流可能只支持这些功能中的一部分。 例如，PipeStream 类不支持查找。 流的 CanRead、CanWrite 和 CanSeek 属性指定流支持的操作。 `
* `System.IO.MemoryStream`：`读写内存`
* `System.Net.Sockets.NetworkStream`:`处理网络数据`
* `Stream 类是一切流的最高抽象 也就是基类`
* `System.IO.Compression.DeflateStream`:`压缩数据的流,可以写入FileStream,MemoryStream，NetWork.`
* `CrptoStream`:`加密数据 用于将数据流链接到加密转换。`
* `PipeStream `:`用于通过匿名和命名管道进行读取和写入。`
* `BufferedStream` : `用于改进读取和写入操作的性能。`

##### 常用的流对象

|`流`|`说明`|
|:-------|:----|
|`FileStream `| `用于对文件进行读取和写入操作。`|
|`IsolatedStorageFileStream`|` 用于对独立存储中的文件进行读取或写入操作。`|
|`MemoryStream`| `用于作为后备存储对内存进行读取和写入操作。`|
|`BufferedStream` | `用于改进读取和写入操作的性能。`|
|`NetworkStream`| `用于通过网络套接字进行读取和写入。`|
|`PipeStream `|` 用于通过匿名和命名管道进行读取和写入。`|
|`CryptoStream`| ` 用于将数据流链接到加密转换。`|

##### 读取器和编写器
* `System.IO 命名空间还提供用于在流中读取和写入已编码字符的类型。 通常，流用于字节输入和输出。 读取器和编写器类型处理编码字符与字节之间的来回转换，以便流可以完成操作。 每个读取器和编写器类都与流关联，可以通过类的 BaseStream 属性进行检索。`
* `下面是一些常用的读取器和编写器类：`
    * `BinaryReader 和 BinaryWriter - 用于将基元数据类型作为二进制值进行读取和写入。`
    * `StreamReader 和 StreamWriter - 用于通过使用编码值在字符和字节之间来回转换来读取和写入字符。`
    * `StringReader 和 StringWriter - 用于从字符串读取字符以及将字符写入字符串中。`
    * `TextReader 和 TextWriter - 用作其他读取器和编写器（读取和写入字符和字符串，而不是二进制数据）的抽象基类。`

##### 压缩
* `压缩是指减小文件大小以便存储的过程。 解压缩是提取压缩文件的内容以使这些内容采用可用格式的过程。 System.IO.Compression 命名空间包含用于对文件和流进行压缩或解压缩的类型。`
* `在对文件和流进行压缩和解压缩时，经常使用以下类：`
    * `ZipArchive - 用于在 zip 存档中创建和检索条目。`
    * `ZipArchiveEntry - 用于表示压缩文件。`
    * `ZipFile - 用于创建、提取和打开压缩包。`
    * `ZipFileExtensions - 用于创建和提供压缩包中的条目。`
    * `DeflateStream - 用于使用 Deflate 算法对流进行压缩和解压缩。`
    * `GZipStream - 用于采用 gzip 数据格式对流进行压缩和解压缩。`



--------------------
`作者:` `模板` 
`完成时间`:`2018年12月31日18:33:38`
`备注信息`: `禁止转载` 
