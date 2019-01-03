### [Dotnet Core 正则表达式](#top) <b id="top"></b> :star:

-----
`一切框架都源于基础,那些底层的东西,设计模式,语法特性,架构,思想` [`官网:正则表达式API 4.7.2`](https://docs.microsoft.com/zh-cn/dotnet/api/system.text.regularexpressions?view=netframework-4.7.2)

- [x] [`1.简单的用一用`](#into) 
- [x] [`2.Regex`](#regex) 
- [x] [`3.Match`](#match) 
- [x] [`4.Group`](#group) 

-----
##### [`1.简单的用一用`](#top) :triangular_flag_on_post:  <b id="into"></b>
`使用C# 查询出所有符合日期格式的数据`
```c#
Regex r = new Regex(@"^\d{4}/\d{1,2}/\d{1,2}$");

String[] vals = { "2016/12/23", "2015/5/09","2018/12/23","5646545" };


foreach (var str in vals) {
    if (r.IsMatch(str)) {
        Console.WriteLine($"匹配值:{str} ");    
    }
}
/*
 匹配值:2016/12/23
 匹配值:2015/5/09
 匹配值:2018/12/23
 */
```
[`System.Text.RegularExpressions 命名空间里面的类`](https://docs.microsoft.com/zh-cn/dotnet/api/system.text.regularexpressions?view=netframework-4.7.2)

|`名称`|`说明`|
|:------|:------|
|`Capture `|`表示来自单个成功子表达式捕获的结果。`|
|`CaptureCollection` |`表示单个捕获组执行的捕获集。`|
|`Group`|`表示来自单个捕获组的结果。`|
|`GroupCollection`|`返回一次匹配中捕获的组的集。`|
|`Match `|`表示单个正则表达式匹配的结果。`|
|`MatchCollection`|	`表示通过以迭代方式将正则表达式模式应用于输入字符串所找到的成功匹配的集合。`|
|`Regex`| `	表示不可变的正则表达式。`|
|`RegexCompilationInfo`|`提供有关使用正则表达式将正则表达式编译为独立程序集的信息。`|
|`RegexMatchTimeoutException`|`正则表达式模式匹配方法的执行时间超出其超时时间间隔时引发的异常。`|
|`RegexRunner`|`RegexRunner 类是编译正则表达式的基类。`|
|`RegexRunnerFactory`|`为编译过的正则表达式创建 RegexRunner 类。`|
|`枚举`:`RegexOptions`|`提供用于设置正则表达式选项的枚举值。`|

##### RegexOptions 正则表达式参数
* `Multiline`:`多行模式匹配 更改 ^ 和 $ 的含义，使它们分别在任意一行的行首和行尾匹配，而不仅仅在整个字符串的开头和结尾匹配。`
* `IgnoreCase`:`不区分大小写匹配”`
* `IgnorePatternWhitespace`:`消除模式中的非转义空白并启用由 # 标记的注释`
* `Singleline`:`指定单行模式。`


##### [`2.Regex 类`](#top)  <b id="regex"></b> :triangular_flag_on_post:
`核心:` [`官方API:Regex`](https://docs.microsoft.com/zh-cn/dotnet/api/system.text.regularexpressions.regex?view=netframework-4.7.2) `是.netcore 正则表达式
的核心类`
##### 基本功能

* `匹配项的验证。 在调用IsMatch方法，以确定是否存在匹配项。`
* `检索单个匹配项。 在调用Match方法来检索Match对象，表示一个字符串或部分字符串的第一个匹配项。 可以通过调用来检索后续匹配项Match.NextMatch方法。`
* `检索的所有匹配项。 在调用Matches方法来检索System.Text.RegularExpressions.MatchCollection对象，表示找到的字符串的字符串或部分的所有匹配项。`
* `替换匹配的文本。 在调用Replace方法替换匹配的文本。 也可以由正则表达式定义的替换文本。 此外，某些Replace方法包括MatchEvaluator参数，它允许您以编程方式定义的替换文本。`
* `创建从输入字符串的部分构成的字符串数组。 在调用Split方法拆分输入的字符串与正则表达式定义的位置。`
* `Escape方法可转义可能被解释为正则表达式或输入的字符串中的正则表达式运算符的任何字符。`

##### 字段/属性
* `pattern`：`由 CompileToAssembly 方法生成的 Regex 对象使用。`
* `CacheSize`:`获取或设置已编译的正则表达式的当前静态缓存中的最大项数`
* `Options`:`获取传递给 Regex 构造函数的选项。`
* `RightToLeft`:`获取一个值，该值指示正则表达式是否从右向左进行搜索。`
* `CapNames`:`获取或设置将命名捕获组映射到其索引值的字典。`
* `Caps`:`获取或设置将编号捕获组映射到其索引值的字典。`
##### 构造函数
`它有四个常用的构造函数,且并没有公共的无参数构造函数`
* `记住一点：等待匹配的字符串永远是方法和构造函数的第一个参数`
```c#
public Regex();
public Regex(string pattern);
public Regex(string pattern, RegexOptions options);
public Regex(string pattern, RegexOptions options, TimeSpan matchTimeout);
```
* `TimeSpan`:`使用修改模式的选项和指定在超时前多久模式匹配方法应进行匹配尝试的值为指定正则表达式初始化 Regex 类的新实例。`

##### 常用方法
`Regex 具有很多常用的实例方法和静态方法,实例方法和静态方法的区别就是,内部实现一样,实例方法不需要提供正则表达式,而静态方法需要提供正则表达式,并且实例方法有的,静态方法都有！` [`官方API:方法API`](https://docs.microsoft.com/zh-cn/dotnet/api/system.text.regularexpressions.regex?view=netframework-4.7.2#%E6%96%B9%E6%B3%95)
* `&`:`表示匹配的原来的字符串`

* `Boolean IsMatch(String,str,string pattern,RegexOptions options,TimeSpan matchTimeout)`:`判断是否匹配正则表达式`
    ```c#
      Boolean isMatch = Regex.IsMatch("1427035242@qq.com",
                                @"^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$");
      Console.WriteLine(isMatch); //true
    ```
* `Match Match(String str,string Pattern)`:`可以继续向下匹配哟`
    ```c#
      String wait = "jiang xing xing shi zui chou de ku ku";
      String pattern = @"\b(\w+)\W(\1)\b"; //匹配重复的单词
      Match match = Regex.Match(wait, pattern);
      
      while (match.Success) {
          Console.WriteLine("Duplication {0} Found", match.Groups[0].Value);
          match = match.NextMatch(); //继续向下匹配    
       }
    ```
* `public static string Replace(string input, string pattern, string replacement, RegexOptions options, TimeSpan matchTimeout);`:`替换`
    ```c# 
      String pattern_ = @"\b\d+\.\d+\b";
      String str_wait_to_place = "Total cost: 104.56";
      String replacement = "$$$&";
      
      String  val_place = Regex.Replace(str_wait_to_place, pattern_, replacement);
      Console.WriteLine(val_place); //Total cost: $104.56
    ```
* `public static string[] Split(string input, string pattern, RegexOptions options, TimeSpan matchTimeout);`:`分割字符串`
    ```c#
      String names = "1. meike 2. kikcer 3. tim 4. Tome 5. Jake 6. Wing 7. Qike";
      String pattern_splite = @"\d{1,2}\. ";
      foreach (var item in Regex.Split(names, pattern_splite)) {
           Console.WriteLine(item);    
      }
      /* 空白
        meike
        kikcer
        tim
        Tome
        Jake
        Wing
        Qike
        空白 */
    ```
* `GetGroupNames()`:`返回正则表达式的捕获组名数组。`
* `GetGroupNumbers()`:`返回与数组中的组名相对应的捕获组号的数组`
* `Unescape(String)`:`转换输入字符串中的任何转义字符。`
##### [`3.Match 类`](#top)  <b id="match"></b> :triangular_flag_on_post:
[`Match 类API链接`](https://docs.microsoft.com/zh-cn/dotnet/api/system.text.regularexpressions.match?view=netframework-4.7.2)




##### MatchCollection 是Match 的一个集合
```c#
public static void Main(String[] args) {
    Regex regex = new Regex(@"\dadc");
    MatchCollection collection = regex.Matches("asd1adcasdadadcvadcad49876adc");

    foreach (Match val in collection) {
        Console.WriteLine("value:{0} found in {1} postion",val.Value,val.Index);
    }
    Console.ReadKey();
}
```
##### 3.1 属性
* `Success `:`获取一个值，该值指示匹配是否成功。(Inherited from Group) `
* `Value`:`获取输入的字符串中的捕获的子字符串`
* `Length`:`表示捕获的字符串的长度`
* `Index`:`原始字符串中发现捕获的子字符串的第一个字符的位置`
* `Groups`:`获取由正则表达式匹配的组的集合`
* `Captures`:`按从里到外、从左到右的顺序获取由捕获组匹配的所有捕获的集合（如果正则表达式用 RightToLeft 选项修改了，则顺序为按从里到外、从右到左）。 该集合可以有零个或更多的项`
```c#
using System;
using System.Text.RegularExpressions;

public class Class1
{
   public static void Main()
   {
      string pattern = @"\b(\w+?)\s\1\b";
      string input = "This this is a nice day. What about this? This tastes good. I saw a a dog.";
      foreach (Match match in Regex.Matches(input, pattern, RegexOptions.IgnoreCase))
         Console.WriteLine("{0} (duplicates '{1}') at position {2}", 
                           match.Value, match.Groups[1].Value, match.Index);
   }
}
```
##### 3.2 方法
* `NextMatch()`:`从上一个匹配结束的位置（即在上一个匹配字符之后的字符）开始返回一个包含下一个匹配结果的新 Match 对象。`
* `Result(String)`:`返回对指定替换模式的扩展`
* `Synchronized(Match)`:`返回一个与提供的实例等效的 Match 实例，该实例适合在多个线程间共享`

```c#

String wait = "jiang xing xing shi zui chou de ku ku";
String pattern = @"\b(\w+)\W(\1)\b"; //匹配重复的单词
Match match = Regex.Match(wait, pattern);

while (match.Success) {
  Console.WriteLine("Duplication {0} Found", match.Groups[0].Value);
  match = match.NextMatch(); //继续向下匹配    
}
```
##### [`4.Group 类`](#top)  <b id="group"></b> :triangular_flag_on_post:
`表示来自单个捕获组的结果。`

##### 属性
* `Index`:`原始字符串中发现捕获的子字符串的第一个字符的位置`
* `Value`:`获取捕获的子字符串的长度`
* `Name`:`返回由当前实例表示的捕获组的名称`
* `Success`:`获取一个值，该值指示匹配是否成功`

##### 方法
* `Synchronized(Group)`:`返回一个与提供的对象等效的 Group 对象，在多个线程间共享该对象是安全的`
```c#
public static void Main(String[] args) {
    String input = "Born: July 28, 1989";
    String Pattern = @"\b(\w+)\s(\d{1,2}),\s(\d{4})";

    Match match = Regex.Match(input, Pattern);

    if (match.Success) {
        for (int ctr = 0; ctr < match.Groups.Count; ctr++) {
            Console.WriteLine("Group{0}:{1}", ctr, match.Groups[ctr].Value);
        }
    }
    Console.ReadKey();
}
```
##### [官方API Capture](https://docs.microsoft.com/zh-cn/dotnet/api/system.text.regularexpressions.capture?view=netframework-4.7.2)

`属性`
```
Index 	原始字符串中发现捕获的子字符串的第一个字符的位置。
Length 	获取捕获的子字符串的长度。
Value 	获取输入的字符串中的捕获的子字符串。
```

```c#
public static void Main()
{
  string input = "Yes. This dog is very friendly.";
  string pattern = @"((\w+)[\s.])+";
  foreach (Match match in Regex.Matches(input, pattern))
  {
     Console.WriteLine("Match: {0}", match.Value);
     for (int groupCtr = 0; groupCtr < match.Groups.Count; groupCtr++)
     {
        Group group = match.Groups[groupCtr];
        Console.WriteLine("   Group {0}: {1}", groupCtr, group.Value);
        for (int captureCtr = 0; captureCtr < group.Captures.Count; captureCtr++)
           Console.WriteLine("      Capture {0}: {1}", captureCtr, 
                             group.Captures[captureCtr].Value);
     }                      
  }
}
```

















































