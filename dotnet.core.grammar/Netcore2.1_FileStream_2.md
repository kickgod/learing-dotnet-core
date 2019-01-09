### [Net Core 流操作---文件流 ❕](#top) :grey_exclamation: <b id="top"></b>
`文件流又分为二进制文件和文本文件` [`文件和流`](https://docs.microsoft.com/zh-cn/dotnet/standard/io/) :white_check_mark:
:white_check_mark:

------

- [x] [`1.使用文件流`](#target1)
- [x] [`2.FileStream`](#filestream)
- [x] [`3.文本读写 StreamReader/StreamWriter`](#stream)
     * [`StreamReader`](#reader)
     * [`StreamWriter`](#writer)
- [x] [`4.二进制读写 BinaryReader/BinaryWriter`](#binary)
     * [`BinaryReader`](#breader)
     * [`BinaryWriter`](#bwriter)
------

#####  :octocat: [1.使用文件流](#top) <b id="target1"></b> 
`概括`：`要使用FileStream 需要四个信息`
* :one: `要访问的文件`---`文件路径就行 String`
* :two: `打开文件的模式,新建一个文件？ 打开一个现有的文件? 是覆盖原来内容还是追加内容`----`枚举 FileMode` 
* :three: `访问文件的形式,只读还是只写 还是读写`----`枚举 FileAccess`
* :four: `是否共享访问,允许其他流同时访问文件,那么这些流式只读还会只写？`------`枚举 FileShare`


##### 1.FileMode

|`打开方式` | `值`|`描述`|
|---|:----|----|
|`CreateNew` |1 |	`创建新文件。 这需要 Write 权限。 文件已存在，将引发 IOException异常。` |
|`Create`    |2 |`创建新文件。 如果文件已存在，它将被覆盖。 这需要 Write 权限。如果文件不存在，则使用 CreateNew；否则使用 Truncate。 如果该文件已存在但为隐藏文件，则将引发 UnauthorizedAccessException异常。`| 
|`Open`|3 |`指定操作系统应打开现有文件。 打开文件的能力取决于 FileAccess 枚举所指定的值。 如果文件不存在，引发一个 FileNotFoundException 异常。`|
|`OpenOrCreate`|4 |`指定操作系统应打开文件（如果文件存在）；否则，应创建新文件。 如果用 FileAccess.Read 打开文件，则需要 Read权限。 如果文件访问为 FileAccess.Write，则需要 Write权限。 如果用 FileAccess.ReadWrite 打开文件，则同时需要 Read 和 Write权限。`|
|`Truncate`|5 |`指定操作系统应打开现有文件。 该文件被打开时，将被截断为零字节大小。 这需要 Write 权限。 尝试从使用 FileMode.Truncate 打开的文件中进行读取将导致 ArgumentException 异常。`|
|`Append`|6 |`若存在文件，则打开该文件并查找到文件尾，或者创建一个新文件。 这需要 Append 权限。 FileMode.Append 只能与 FileAccess.Write 一起使用。 试图查找文件尾之前的位置时会引发 IOException 异常，并且任何试图读取的操作都会失败并引发 NotSupportedException 异常。`|

* `1. FileMode.Append 只能够在 FileAccess.Write 模式下面使用 `
##### 2.FileShare
|`打开方式` |`描述`|
|:------|:----|
|`Delete`|`允许随后删除文件`|
|`Read`|`允许随后打开文件读取`|
|`ReadWrite`|`允许随后打开文件读取或写入`|
|`Write`|`允许随后打开文件写入`|
|`Inheritable `|`使文件句柄可由子进程继承。 Win32 不直接支持此功能`|
|`None`|`谢绝共享当前文件。 文件关闭前，打开该文件的任何请求`|


##### 3.FileAccess

|`打开方式` |`描述`|
|:------|:----|
|`Read `|`对文件的读访问。 可从文件中读取数据。 与 Write 组合以进行读写访问`|
|`ReadWrite `|`对文件的读写访问权限。 可从文件读取数据和将数据写入文件`|
|`Write `|`文件的写访问。 可将数据写入文件。 与 Read 组合以进行读写访问`|


##### 创建一个 [API: FileStream](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.filestream?view=netframework-4.7.2)
`FileStream 有12个构造函数,也就是有十二种构造方法....`

```c#
public FileStream(string path, FileMode mode, FileAccess access, FileShare share);

public void UseDmo() {
    using (var stream = new FileStream(Path.GetFullPath(@"Resources/TextFile/User.txt"), 
    FileMode.Open, FileAccess.Read, FileShare.None)) {
      Console.WriteLine($"是否可读: {stream.CanRead}");
      Console.WriteLine(stream.CanWrite);

    }
}
```


#####  :octocat: [2.FileStream](#top) <b id="filestream"></b> 
`非常常用的文件操作流` [`官方API说明--!`](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.filestream?view=netframework-4.7.2)
##### 1.FileStream 流属性
* `CanRead`:`获取一个值，该值指示当前流是否支持读取。`
* `CanSeek`:`获取一个值，该值指示当前流是否支持查找。`
* `CanTimeout`:`获取一个值，该值确定当前流是否可以超时。`
* `CanWrite`:`获取一个值，该值指示当前流是否支持写入。`
* `Handle`:`获取当前 FileStream 对象所封装文件的操作系统文件句柄。`
* `IsAsync`:`获取一个值，它指示 FileStream 是异步打开还是同步打开的。`
* `Length`:`获取用字节表示的流长度。`
* `Name`:`获取 FileStream 中已打开的文件的绝对路径。`
* `Position`:`获取或设置此流的当前位置。`
* `ReadTimeout`:`获取或设置一个值（以毫秒为单位），该值确定流在超时前尝试读取多长时间。`
* `SafeFileHandle`:`获取 SafeFileHandle 对象，它代表当前 FileStream 对象所封装的文件的操作系统文件句柄。`
* `WriteTimeout`:`获取或设置一个值（以毫秒为单位），该值确定流在超时前尝试写入多长时间。`

```c#
public void UseDmo() {
    using (var stream = new FileStream(Path.GetFullPath(@"Resources/TextFile/User.txt"), FileMode.Open, 
    FileAccess.ReadWrite, FileShare.None)) {
        Console.WriteLine($"是否可读: {stream.CanRead}");
        Console.WriteLine($"是否可写: {stream.CanWrite}");
        Console.WriteLine($"是否执行随机: {stream.CanSeek}");
        Console.WriteLine($"是否支持超时: {stream.CanTimeout}");
        Console.WriteLine($"Stream长度: {stream.Length}");
        Console.WriteLine($"游标位置: {stream.Position}");
    }
}
```
##### 2.分析文本文件的编码
`对于文本文件,下一步就是读取流中第一个字节----序言.序言提供了文件如何编码的信息(使用的文本格式),这也称为顺序标记`
* `读取一个流的时候,利用ReadByte可以从流中读取一个字节`
* `使用Read方法可以填充一个字节数组.第二个参数是字节数组中的偏移量 第三个参数是可用于填充的字节数 返回读取的字节数`
* `GetEncoding 方法创建了一个包含五个字节的数组`

##### 3.编码格式
* `ASCII`:`使用八位存储一个字节 包含小写大写字母,控制字符 每一个字符 七位`
* `Unicode 编码`:`有小或大端顺序法存储,FF紧随FE之后,表示小端字节序,而FE后跟FF,就表示使用大端字节序`
* `UTF-16`:`16位两个字节表示一个字符,但是数量还是不够表示所有的字符`
* `UTF-32`:`32位 四个字节表示一个字符`
* `UTF-8`:`采取可编程的字符定义,一个字符定义使用为1~6 个字符 字符串序列为 ：0xEF 0xBB 0xBF`


`返回流的编码格式`
```c#
public System.Text.Encoding GetEncoding(Stream stream) {
    if (!stream.CanSeek) {
        throw new ArgumentException("错误:流不能随机读取");
    }
    Encoding encoding = Encoding.ASCII;

    byte[] bom = new byte[5];
    int nRead = stream.Read(bom, 0, 5);//从0 开始读取五个字节

    if (bom[0] == 0xff && bom[1] == 0xfe && bom[2] == 0 && bom[3] == 0)
    {
        stream.Seek(4, SeekOrigin.Begin);
        return Encoding.UTF32;
    }
    else if (bom[0] == 0xff && bom[1] == 0xfe)
    {
        stream.Seek(2, SeekOrigin.Begin);
        return Encoding.Unicode;
    }
    else if (bom[0] == 0xfe && bom[1] == 0xff)
    {
        stream.Seek(2, SeekOrigin.Begin);
        return Encoding.BigEndianUnicode;
    }
    else if (bom[0] == 0xef && bom[1] == 0xbb && bom[2] == 0xbf) {
        stream.Seek(3, SeekOrigin.Begin);
        return Encoding.UTF8;
    }
    stream.Seek(0, SeekOrigin.Begin);
    return encoding;
}

```

##### 4.读取流
`打开文件创建流之后,使用Read方法读取文件,重复此过程直到读取到0为止` `注意`: `1.要记得Dispose方法关闭流,或者 using自动关闭`
* `Read(Byte[], Int32, Int32)`:	`从流中读取字节块并将该数据写入给定缓冲区中。`
* `Read(Span<Byte>)`:`Inherited from Stream`
* `ReadAsync(Byte[], Int32, Int32) `:`从当前流异步读取字节序列，并将流中的位置提升读取的字节数。(Inherited from Stream)`
* `ReadAsync(Byte[], Int32, Int32, CancellationToken)`

从当前流异步读取字节的序列
```c#
public void UseDmo() {
    using (var stream = new FileStream(Path.GetFullPath(@"Resources/TextFile/User.txt"), FileMode.Open,
    FileAccess.ReadWrite, FileShare.None)) {
        Byte[] buffer = new byte[256];

        Encoding encoder = GetEncoding(stream); //什么编码格式

        Console.WriteLine(encoder.BodyName);

        while (true) {
            int readByteCount = stream.Read(buffer, 0, buffer.Length);
            if (readByteCount == 0)
            {
                break;
            } else if (readByteCount < buffer.Length)
            {
                Array.Clear(buffer, readByteCount, buffer.Length - readByteCount);
            }

            String result = encoder.GetString(buffer, 0, readByteCount);

            Console.WriteLine($"Read Count:{readByteCount} ---Text:{result}");
        }
    }
}
```
##### 5.写入流
* `public override void Write (byte[] array, int offset, int count);`
    * `array :包含要写入该流的数据的缓冲区。` 
    * `array 中的从零开始的字节偏移量，从此处开始将字节复制到该流。`
    * `count 最多写入的字节数。`
* `将字节的序列异步写入当前流，将该流中的当前位置提升写入的字节数，并监视取消请求。` [`API 说明`](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.filestream.writeasync?view=netframework-4.7.2)
```c#
[System.Runtime.InteropServices.ComVisible(false)]
public override System.Threading.Tasks.Task WriteAsync 
(byte[] buffer,
int offset, 
int count, 
System.Threading.CancellationToken cancellationToken);
```
* `public override void WriteByte (byte value)`:`一个字节写入文件流中的当前位置。`
`可以借助File类打开一个写入流`
```c#
FileStream stream_ = File.OpenWrite(Path.GetFullPath(@"Resources/TextFile/User.txt"));
```
`正常打开`:`记住正常打开的流要记得关闭,并且流写入的字节要使用编码器 转换字符得到字符的编码`
```c#
public void WriteStream()
{
    //FileStream stream_ = File.OpenWrite(Path.GetFullPath(@"Resources/TextFile/User.txt"));

    using (var stream = new FileStream(Path.GetFullPath(@"Resources/TextFile/User.txt"), 
    FileMode.Append, FileAccess.Write, FileShare.None))
    {
        byte[] data = Encoding.UTF8.GetBytes("我是你们的爸爸啊，儿子！");
        stream.Write(data,0,data.Length);
    }
}
```

##### 6.复制流
* `CopyTo(Stream) `:`从当前流中读取字节并将其写入到另一流中。(Inherited from Stream) `
* `CopyTo(Stream, Int32)`:`使用指定的缓冲区大小，从当前流中读取字节并将其写入到另一流中。(Inherited from Stream) `
* `CopyToAsync(Stream)`:`从当前流中异步读取字节并将其写入到另一个流中。(Inherited from Stream) `
* `CopyToAsync(Stream, Int32, CancellationToken)`:`使用指定的缓冲区大小和取消令牌，从当前流中异步读取字节并将其写入到另一个流中。(Inherited from Stream) `
* `Flush()`:`清除此流的缓冲区，使得所有缓冲数据都写入到文件中。`
* `Flush(Boolean)`:`清除此流的缓冲区，将所有缓冲数据都写入到文件中，并且也清除所有中间文件缓冲区。`
* `FlushAsync()`:`异步清除此流的所有缓冲区并导致所有缓冲数据都写入基础设备中。(Inherited from Stream) `


```c#
public void CopyStream() {
    using (var stream = new FileStream(Path.GetFullPath(@"Resources/TextFile/User.txt"), 
    FileMode.Open, FileAccess.Read, FileShare.None))
    {
        FileStream fileStream = File.OpenWrite(@"Resources/TextFile/CopyUser.txt");
        stream.CopyTo(fileStream);
        fileStream.Flush();
        Console.WriteLine("Copy成功");
    }
}
```

##### 7.随机访问流
`可以快速访问文件中的特定位置` <br/> 
`位置方法`:`通过它可以将游标定义到流中的一个随机位置,读取不同的数据`
* `public override long Seek (long offset, System.IO.SeekOrigin origin);`
    * `offset 相对于 origin 的点，从此处开始查找。`
    * `origin 使用 SeekOrigin 类型的值，将开始位置、结束位置或当前位置指定为 offset 的参考点。`
    
##### 8.使用缓存的流
`使用流在读写的时候,是进过缓存的,并不是每次读写改变都放映到文件中,而是预先保留在缓存区中,因为每一次对于文件的保存改变需要,读取字节,链接文件系统,定位文件,磁盘读写过程。` `对于文件操作系统会自动完成读写操作,但是需要编写一个流类,从其他没有缓存的设备中读取数据。如果这样,就应该从BufferStream 创建一个类,它实现一个缓存区 但是它并不适用于程序频繁切换读数据和谐数据的情形`


#####  :octocat: [3.StreamReader/StreamWriter](#top) <b id="stream"></b> 
`使用FileStream 类读写文本文件,需要使用字节数组,处理前一节描述的编码.其实我们可以直接使用StreamReader/StreamWriter 读写FileStream，无须处理字节数组和编码，比较轻松！ `
* `他们非常适合文本类型的工作,自定处理文本位置问题！`

#####  :octocat: [3.1 StreamReader](#top) <b id="reader"></b> 
[`官方API`](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.streamreader?view=netframework-4.7.2)

* `StreamReader(Stream)`:`为指定的流初始化 StreamReader 类的新实例。`
* `StreamReader(Stream, Encoding)`:`用指定的字符编码为指定的流初始化 StreamReader 类的一个新实例。`
* `StreamReader(String)`:`为指定的文件名初始化 StreamReader 类的新实例。`
* `StreamReader(Stream, Encoding, Boolean, Int32)`:`为指定的流初始化 StreamReader 类的新实例，带有指定的字符编码、字节顺序标记检测选项和缓冲区大小。`
* `File.OpenText(path)`:`也可以构造一个StreamReader`
```c#
public void StreamReader()
{
    using (var stream = new FileStream(Path.GetFullPath(@"Resources/TextFile/User.txt"),
    FileMode.Open, FileAccess.ReadWrite, FileShare.None))
    {
        using (StreamReader reader = new StreamReader(stream)) {
            while (!reader.EndOfStream) {
                String line = reader.ReadLine();
                Console.WriteLine("Start: " + line);
            };
        }; 
    };
}
```

##### 属性
* `CurrentEncoding`:`获取当前 StreamReader 对象正在使用的当前字符编码。`
* `EndOfStream 	`:`获取一个值，该值指示当前的流位置是否在流结尾。`

##### 读取方法
* `Read()`:`读取输入流中的下一个字符并使该字符位置提升一个字符。`
* `Read(Char[], Int32, Int32)`:`从指定的索引位置开始将来自当前流的指定的最多字符读到缓冲区。`
* `ReadAsync(Char[], Int32, Int32)`:`从当前流中异步读取指定的最大字符，并且从指定的索引位置开始将该数据写入缓冲区。`
* `ReadLine()`:`从当前流中读取一行字符并将数据作为字符串返回。`
* `ReadLineAsync()`:`从当前流中异步读取一行字符并将数据作为字符串返回。`
* `ReadToEnd()`:`读取来自流的当前位置到结尾的所有字符。`
* `ReadToEndAsync()`:`异步读取来自流的当前位置到结尾的所有字符并将它们作为一个字符串返回。`
* `DiscardBufferedData()`:`清除内部缓冲区。`
* `Peek()`:	`返回下一个可用字符，但不使用它。`

```c#
using (StreamReader sr = new StreamReader(path)) 
{

    while (sr.Peek() > -1) 
    {
        Console.WriteLine(sr.ReadLine());
    }
}
```

#####  :octocat: [3.2 StreamWriter](#top) <b id="writer"></b> 
`它和StreamWriter 和 StreamReader的区别在  StreamWriter只能用于写文件, 实现一个 TextWriter，使其以一种特定的编码向流中写入字符。`
```c#
[System.Runtime.InteropServices.ComVisible(true)]
[System.Serializable]
public class StreamWriter : System.IO.TextWriter
```
`正常情况下！ 你对于一个文件的写 是追加在一个文件的开头！ `
##### 构造函数: 后面的带中括号可以从右到左省略
* `StreamWriter(Stream)`:`使用 UTF-8 编码及默认的缓冲区大小，为指定的流初始化 StreamWriter 类的新实例。`
* `StreamWriter(Stream [, Encoding] [, Int32] [, Boolean])`:`使用指定的编码和缓冲区大小，为指定的流初始化 StreamWriter 类的新实例，并可以选择保持流处于打开状态。`
* `StreamWriter(String)`:`用默认编码和缓冲区大小，为指定的文件初始化 StreamWriter 类的一个新实例。`
* `StreamWriter(String[, Boolean][, Encoding] [, Int32])`:`使用指定编码和缓冲区大小，为指定路径上的指定文件初始化 StreamWriter 类的新实例。 如果该文件存在，则可以将其覆盖或向其追加。 如果该文件不存在，此构造函数将创建一个新文件。`

##### 字段
* `CoreNewLine `:`存储用于此 TextWriter 的换行符。`
* `Null`:`提供 StreamWriter，它不带任何可写入但无法从中读取的后备存储。`

##### 属性
* `AutoFlush`:`获取或设置一个值，该值指示 StreamWriter 在每次调用 Write(Char) 之后是否都将其缓冲区刷新到基础流。`
* `BaseStream`:`获取同后备存储连接的基础流。`
* `Encoding`:`获取在其中写入输出的 Encoding。`
* `FormatProvider`:`获取控制格式设置的对象。(Inherited from TextWriter)`
* `NewLine `:`获取或设置由当前 TextWriter 使用的行结束符字符串。(Inherited from TextWriter) `

##### 方法
* `Flush()`:`清理当前写入器的所有缓冲区，并使所有缓冲数据写入基础流。`
* `FlushAsync()`:`异步清除此流的所有缓冲区并导致所有缓冲数据都写入基础设备中。`
* `Write(T Type)`:`T为 C#自带的基本类型 String Single Float Double Int32.....`
* `WriteAsync(T Type)`:`将字符异步写入该流。`
* `WriteLine(T Type)`:`T为 C#自带的基本类型 String Single Float Double Int32.....`
* `WriteLine(String, Object)`
* `WriteLine()`:`将行终止符异步写入该流。`
* `WriteLineAsync()`:`将行终止符异步写入该流。`
* `WriteAsync(Char[])`:`以异步形式将字符数组写入到下一个字符串或流。(Inherited from TextWriter) `
* `WriteAsync(Char[], Int32, Int32)`:`将字符的子数组异步写入该流。`
    * `包含要写入的数据的字符数组。`
    * `在开始读取数据时缓冲区中的字符位置。`
    * `要写入的最大字符数。`
    * `返回 Task 表示异步写入操作的任务。`
* `WriteLine(Char[], Int32, Int32)`:`将后跟行结束符的字符子数组写入文本字符串或流。(Inherited from TextWriter) `

##### 行结尾
* `windows`:`CR LF`
* `macintosh`:`CR`
* `Unix`:`LF`
* `Unicode 行分隔符号`:`LS`
* `Unicode 段落分隔符`:`PS`

```c#
using (var stream = new FileStream(Path.GetFullPath(@"Resources/TextFile/User.txt"), FileMode.Append, 
FileAccess.Write, FileShare.None)) {
    using (var writer = new StreamWriter(stream)) {
        //编码格式
        Console.WriteLine(writer.Encoding.BodyName);
        writer.WriteLineAsync("与其让你在我爱中憔悴,宁愿你受伤流泪！").Wait();
        writer.Write("I am a boy! Do you know.");
    }
}

DirectoryInfo[] cDirs = new DirectoryInfo(@"c:\").GetDirectories();

// Write each directory name to a file.
using (StreamWriter sw = new StreamWriter("CDriveDirs.txt"))
{
    foreach (DirectoryInfo dir in cDirs)
    {
        sw.WriteLine(dir.Name);
    }
}
}
```

#####  :octocat: [4. 二进制读写 BinaryReader/BinaryWriter](#top) <b id="binary"></b> 
`读写二进制文件的一种选择是直接使用流类型,使用字节数组执行读写操作,但是另一个更好的选择是使用为二进制文件专门量身定制的读取器和写入器。使用方式和
StreamReader/StreamWriter 一样,区别在于二进制文件是不需要使用编码的,文件使用的是二进制格式而不是文件格式`

##### [4.1 BinaryReader](#top) <b id="breader"></b> 
`读取二进制文件`

##### 构造函数
* `BinaryReader(Stream)`:`基于所指定的流和特定的 UTF-8 编码，初始化 BinaryReader 类的新实例。`
* `BinaryReader(Stream, Encoding)`:`基于所指定的流和特定的字符编码，初始化 BinaryReader 类的新实例。`
* `BinaryReader(Stream, Encoding, Boolean)`:`基于所提供的流和特定的字符编码，初始化 BinaryReader 类的新实例，有选择性的打开流。`

##### 属性
* `BaseStream`:`公开对 BinaryReader 的基础流的访问。`

##### 方法
* `Read(Byte[], Int32, Int32)`:`从字节数组中的指定点开始，从流中读取指定的字节数。`
* `Read(Char[], Int32, Int32)`:`从字符数组中的指定点开始，从流中读取指定的字符数。`
* `Readxxx()`:`18种 读取各种数据类型的方法`
* `FillBuffer(Int32)`:`用从流中读取的指定字节数填充内部缓冲区。`
* `PeekChar()`:`返回下一个可用的字符，并且不提升字节或字符的位置。`
* `MemberwiseClone()`:`创建当前 Object 的浅表副本。(Inherited from Object) `

```c#
const string fileName = "AppSettings.dat";

static void Main()
{
    WriteDefaultValues();
    DisplayValues();
}

public static void WriteDefaultValues()
{
    using (BinaryWriter writer = new BinaryWriter(File.Open(fileName, FileMode.Create)))
    {
        writer.Write(1.250F);
        writer.Write(@"c:\Temp");
        writer.Write(10);
        writer.Write(true);
    }
}

public static void DisplayValues()
{
    float aspectRatio;
    string tempDirectory;
    int autoSaveTime;
    bool showStatusBar;

    if (File.Exists(fileName))
    {
        using (BinaryReader reader = new BinaryReader(File.Open(fileName, FileMode.Open)))
        {
            aspectRatio = reader.ReadSingle();
            tempDirectory = reader.ReadString();
            autoSaveTime = reader.ReadInt32();
            showStatusBar = reader.ReadBoolean();
        }

        Console.WriteLine("Aspect ratio set to: " + aspectRatio);
        Console.WriteLine("Temp directory is: " + tempDirectory);
        Console.WriteLine("Auto save time set to: " + autoSaveTime);
        Console.WriteLine("Show status bar: " + showStatusBar);
    }
}
```

##### [4.2 BinaryWriter](#top) <b id="bwriter"></b> 
`将二进制中的基元类型写入流并支持用特定的编码写入字符串。`

##### 构造函数
* `BinaryWriter()`:`初始化写入流的 BinaryWriter 类的新实例。`
* `BinaryWriter(Stream)`:`基于所指定的流和特定的 UTF-8 编码，初始化 BinaryWriter 类的新实例。`
* `BinaryWriter(Stream, Encoding)`:`基于所指定的流和特定的字符编码，初始化 BinaryWriter 类的新实例。`
* `BinaryWriter(Stream, Encoding, Boolean)`:`基于所提供的流和特定的字符编码，初始化 BinaryWriter 类的新实例，有选择性的打开流。`

##### 字段
* `Null`:`指定无后备存储的 BinaryWriter。`
* `OutStream`:`包含基础流。`

##### 方法
* `Seek(Int32, SeekOrigin) `:`设置当前流中的位置。`
* `Flush()`:`清理当前编写器的所有缓冲区，使所有缓冲数据写入基础设备。`
* `Write(Char[], Int32, Int32)`:`将字符数组部分写入当前流，并根据所使用的 Encoding（可能还根据向流中写入的特定字符），提升流的当前位置。`
* `Write(T type)`:`让T 类型的元素写入`

--------------------
`作者:` `KickGod` 
`完成时间`:`2019年1月9日13:20:36`
`备注信息`: `任意使用` 
