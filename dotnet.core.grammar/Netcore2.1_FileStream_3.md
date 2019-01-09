### [Net Core 流操作---特殊文件操作 ](#top) :grey_exclamation: <b id="top"></b>
`介绍一些文件的特殊操作 压缩,观察...`:white_check_mark:

------

- [x] [`1.压缩文件`](#zip)
     - [`CompressionLevel`](#level)
     - [`CompressionMode`](#mode)
     - [`DeflateStream`](#deflate)
- [x] [`2.ZIP文件压缩`](#zip)


------

#####  :octocat: [1.压缩文件-------------------------------------------------------------------------------------------------------------------------------------](#top) <b id="zip"></b> 
`System.IO.Compression`: `命名空间包含提供基本的流压缩和解压缩服务的类。` <br/>

`.NET 使用不同的算法压缩和解锁流的类型,可以使用` `DeflateStream` `GZipStream`
* `Deflate`:` [dɪˈfleɪt] 放气;紧缩;`
* `Archive`:`档案文件`

##### [1.1 CompressionLevel 枚举](#top) <b id="level"></b> 
`指定用来指示压缩操作是强调速度还是强调压缩大小的值。`

|`枚举值`|`Value`|`说明`|
|:----|:----|:-----|
|`Fastest` |1   |`即使结果文件未可选择性地压缩，压缩操作也应尽快完成。`|
|`NoCompression`|2|`该文件不应执行压缩。`|
|`Optimal`|0|`即使操作要用更长的时间来完成，也应该可选性地压缩压缩操作。`|

##### [1.2 CompressionMode 枚举](#top) <b id="mode"></b> 
`指定对基础流进行压缩还是解压缩。`

|`枚举值`|`Value`|`说明`|
|:----|:----|:-----|
|`Compress`|1|`压缩基础流`|
|`Decompress`|0 |`解压缩基础流`|

##### <b id="deflate"></b>  [官方API：DeflateStream](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.compression.deflatestream?view=netframework-4.7.2)
* `提供使用 Deflate 算法压缩和解压缩流的方法和属性。这是用于无损文件压缩和解压缩的行业标准算法 若要使用的 zip 存档，请使用ZipArchive和
ZipArchiveEntry类。`
* `DeflateStream类和 GZipStream 使用相同的 gzip 数据格式压缩算法,使用广泛,无专利限制, GZipStream 后台使用 DeflateStream 
但是增加了` `循环冗余校验。`

##### 构造函数 :speech_balloon:
* `DeflateStream(Stream, CompressionLevel)`:`用指定的流和压缩级别初始化 DeflateStream 类的新实例.`
* `DeflateStream(Stream, CompressionLevel, Boolean)`:`使用指定的流和压缩级别，初始化 DeflateStream 类的新实例，有选择性的打开流。`
* `DeflateStream(Stream, CompressionMode)`:`用指定的流和压缩模式初始化 DeflateStream 类的新实例。`
* `DeflateStream(Stream, CompressionMode, Boolean)`:`使用指定的流和压缩模式，初始化 DeflateStream 类的新实例，有选择性的打开流。`

##### 属性 :speech_balloon:
* `BaseStream`
   * `获取对基础流的引用。`
* `CanRead`
   * `获取一个值，该值指示流是否支持在解压缩文件的过程中读取文件。`
* `CanSeek`
   * `获取一个值，该值指示流是否支持查找。`
* `CanTimeout`
   * `获取一个值，该值确定当前流是否可以超时。`
* `CanWrite`
   * `获取一个值，该值指示流是否支持写入。`
* `Length`
   * `此属性不受支持，并且总是引发 NotSupportedException。`
* `Position`
   * `此属性不受支持，并且总是引发 NotSupportedException。`
* `ReadTimeout`
   * `获取或设置一个值（以毫秒为单位），该值确定流在超时前尝试读取多长时间。`
* `WriteTimeout`
   * `获取或设置一个值（以毫秒为单位），该值确定流在超时前尝试写入多长时间。`

##### 方法 :speech_balloon:
`DeflateStream 为我们提供了一个流的很多的读写方法,但是我们使用压缩流的目的是为了压缩的,...读写方法一并不关心,想要使用读写操作可以使用 FileStrea....
之类的文本二进制流对象`

* `Flush()`
   * `此方法的当前实现没有任何功能。`
* `FlushAsync()`
   * `异步清除此流的所有缓冲区并导致所有缓冲数据都写入基础设备中。`
* `FlushAsync(CancellationToken)`
   * `异步清理这个流的所有缓冲区，并使所有缓冲数据写入基础设备，并且监控取消请求。`
* `CopyTo(Stream)`
   * `从当前流中读取字节并将其写入到另一流中。`
* `CopyTo(Stream, Int32)`:`使用指定的缓冲区大小，从当前流中读取字节并将其写入到另一流中。`

```c#
// 将 User.txt 利用 Deflate压缩算法压缩成  user.cmp 文件
public void UseDeflateStream() {
    using (FileStream inputStream = File.OpenRead(@"Resources/TextFile/User.txt")) {
        FileStream outputStream = File.Create(@"Resources/TextFile/user.cmp");

        using (var compressStream = new DeflateStream(outputStream, CompressionLevel.Fastest)) {
            inputStream.CopyTo(compressStream);
            compressStream.Flush();
        }                
    }
}
//将 user.cmp压缩文件 解压为 info.txt文件
public void UseDeDeflateStream() {
    FileStream inputStream = File.OpenRead(@"Resources/TextFile/user.cmp");
    FileStream stream_FILE = File.Create(@"Resources/TextFile/info.txt");
    var decompress = new DeflateStream(inputStream, CompressionMode.Decompress);
    decompress.CopyTo(stream_FILE);
    stream_FILE.Flush();
}
```

#####  :octocat: [2.ZIP文件压缩](#top) <b id="zip"></b> 
`概括`:`今天ZIP文件格式是许多不同文件类型的标准,NuGet 包就是存储为ZIP文件要使用ZIP压缩方法,需要使用 ZipArchive,ZipArchive 对象包含多个
ZipArchiveEntry对象[因为有可能对文件夹压缩！],ZipArchive类不是一个流,但是它使用流进行读写`

##### ZipArchiveMode枚举 :speech_balloon:

|`枚举值`|`Value`|`说明`|
|:----|:----|:-----|
|`Create`|1|`只允许创建新的存档项`|
|`Read`|0|`只允许读取存档项`|
|`Update`|2|`允许对存档项执行读取和写入操作`|


##### ZipArchiveEntry :speech_balloon:
`表示 zip 档案中的压缩文件。`
* `Zip 存档包含每个压缩文件的一项。 ZipArchiveEntry类使你可以检查某项的属性和打开或删除的项。 当您打开一个条目时，可以通过写入到该压
缩文件的流来修改压缩的文件。`

##### 属性
* `Archive`:`获取该项所属的 zip 存档。`
* `CompressedLength`:`获取在 zip 存档中的项的压缩大小。`
* `ExternalAttributes`:`操作系统和应用程序特定的文件属性。`
* `FullName`:`获取 zip 存档中的项的相对路径。`
* `LastWriteTime`:`获取或设置最近一次更改 zip 存档中的项的时间。`
* `Length`:`获取 zip 存档中的项的未压缩大小。`
* `Name`:`获取在 zip 存档中的项的文件名。`

##### 方法
*


```c#
public void UseZip(string directory,string zipFile) {
    FileStream zipStream = File.OpenWrite(zipFile);
    using (var archive = new ZipArchive(zipStream, ZipArchiveMode.Create)) {
        IEnumerable<String> files = Directory.EnumerateFiles(directory, "*", SearchOption.TopDirectoryOnly);

        foreach(var file in files)
        {
            ZipArchiveEntry entry = archive.CreateEntry(Path.GetFileName(file));
            using (FileStream inputStream = File.OpenRead(file))
            {
                using (Stream outpueStream = entry.Open())
                {
                    inputStream.CopyTo(outpueStream);
                };
            };
        };
    };
}
```


--------------------
`作者:` `KickGod` 
`完成时间`:`2019年1月9日14:28:08`
`备注信息`: `任意使用` 
