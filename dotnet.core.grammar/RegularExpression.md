### [Dotnet Core 正则表达式](#top) <b id="top"></b> :star:

-----
`一切框架都源于基础,那些底层的东西,设计模式,语法特性,架构,思想` [`官网:正则表达式API 4.7.2`](https://docs.microsoft.com/zh-cn/dotnet/api/system.text.regularexpressions?view=netframework-4.7.2)

- [x] [`1.简单的用一用`](#into) 
- [x] [`2.Regex`](#regex) 
-----
##### :triangular_flag_on_post: [`1.简单的用一用`](#top)  <b id="into"></b>
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
##### :triangular_flag_on_post: [`2.Regex 类`](#top)  <b id="regex"></b>
`核心:` [`Regex`](https://docs.microsoft.com/zh-cn/dotnet/api/system.text.regularexpressions.regex?view=netframework-4.7.2) `是.netcore 正则表达式
的核心类`
