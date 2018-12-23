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
##### [`2.Regex 类`](#top)  <b id="regex"></b> :triangular_flag_on_post:
`核心:` [`Regex`](https://docs.microsoft.com/zh-cn/dotnet/api/system.text.regularexpressions.regex?view=netframework-4.7.2) `是.netcore 正则表达式
的核心类`

##### 构造函数
`它有四个常用的构造函数,且并没有公共的无参数构造函数`
* `记住一点：等待匹配的字符串永远是方法和构造函数的第一个参数`
```c#
public Regex(string pattern);
public Regex(string pattern, RegexOptions options);
public Regex(string pattern, RegexOptions options, TimeSpan matchTimeout);
```
* `TimeSpan`:`使用修改模式的选项和指定在超时前多久模式匹配方法应进行匹配尝试的值为指定正则表达式初始化 Regex 类的新实例。`

##### 常用方法
`Regex 具有很多常用的实例方法和静态方法,实例方法和静态方法的区别就是,内部实现一样,实例方法不需要提供正则表达式,而静态方法需要提供正则表达式,并且实例方法有的,静态方法都有！`

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
      /*
        空白
        meike
        kikcer
        tim
        Tome
        Jake
        Wing
        Qike
        空白
      */
    ```
##### [`3.Match 类`](#top)  <b id="match"></b> :triangular_flag_on_post:
``

##### [`4.Group 类`](#top)  <b id="group"></b> :triangular_flag_on_post:
``























































