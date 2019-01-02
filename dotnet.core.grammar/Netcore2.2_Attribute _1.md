### [特性](#top) :grey_exclamation: <b id="top"></b>
`特性,你也可以称之为注解！,也就是类方法上面中括号里面的东西,书写框架的时候必备产品!每一个.NET 程序员必备技能。你值得拥有。`:white_check_mark:
[`官方资料`](https://docs.microsoft.com/zh-cn/dotnet/standard/attributes/)

------

- [x] [`1.说明一下`](#target1)
     - [`1.1 .NET 内置特性`](#little1)
- [x] [`2.自定义特性`](#target2)
- [x] [`3.指定AttributeUsage 特性`](#target3)
- [x] [`4.特性的参数问题`](#target4)
- [x] [`5.调试必要得到执行信息`](#target5)
------

#####  :octocat: [1.说明一下](#top) <b id="target1"></b> 
`概括`：`可以用特性扩展元数据,一切的特性都继承自` 
[`System.Attribute`](https://docs.microsoft.com/zh-cn/dotnet/api/system.attribute?view=netframework-4.7.2) `基类 
在.NET使用特性，可以有效地将元数据或声明性信息与代码（程序集、类型、方法、属性等）相关联。 将特性与程序实体相关联后，可以在运行
时使用反射这项技术 查询特性。`
* `命名空间依赖`
  * `using System.Diagnostics;`
  * `using System.Runtime.CompilerServices;`
  * `System.Attribute`
* `使用例子`

  ```c#
  [Serializable]
  public class SampleClass
  {
      // Objects of this type can be serialized.
  }
  ```
* `使用方法匹配构造函数 无参构造函数可以直接写类名去掉 Attribute 如果要使用带参数的构造函数可以这样`
```c#
  [Obsolete("此方法已经弃用")]
```
* `如何初始化,构造函数为定义的字段,属性呢？`
```c#
  [Obsolete("此方法已经弃用",error=false)]
```
#####  [1.1 .NET 内置特性](#top) <b id="little1"></b> 
`概括`:`系统定义的注解将会影响编译器的执行`
* [`Obsolete`](https://docs.microsoft.com/zh-cn/dotnet/api/system.obsoleteattribute?view=netframework-4.7.2)：`标记不再使用的程序元素。 此类不能被继承。`
```c#
//告诉编译器,这个方法以及被弃用,并且需要报错,不允许编译 设为 false 那么只是警告,不会报错能够通过编译

[Obsolete("此方法已经废除,我们已经重载的ToString 方法,请使用ToString 方法！" ,true)]
public String Show() {
    return $"Name: {this._name} Id: {this._id} Age: {this._age}";
}

//编译器会在调用这个方法的地方画 蓝色 下划线 并且会提示 
// `You should use new Function that Name is NewChangeState`
[Obsolete("You should use new Function that Name is NewChangeState")]
public int ChangeStats(int i)
{
    return i + 10;
}
//如果你什么都不写,那么只会 下划线提示,方法以及过时了
[Obsolete]
public String Show() {
    return $"Name: {this._name} Id: {this._id} Age: {this._age}";
}
```
* [`Conditional`](https://docs.microsoft.com/zh-cn/dotnet/api/system.diagnostics.conditionalattribute?view=netframework-4.7.2) `如果
没有使用#define IsTest 定义 IsTest 那么调用这个方法的地方 方法不会生效` `只能用于无返回值方法`
```C#
    [Conditional("IsTest")]
    public static void wahr()
    {
        Console.WriteLine("没有调用我的");
    }
    //主函数调用这个方法后 毫无输出   
    consoleTest();
    consoleTest();
    consoleTest();
    consoleTest();

```
* [`DebuggerStepThrough`](https://docs.microsoft.com/zh-cn/dotnet/api/system.diagnostics.debuggerstepthroughattribute?view=netframework-4.7.2) 特性
` 指示调试器逐句通过代码，而不是单步执行代码。 此类不能被继承。`
```C#
    [DebuggerStepThrough]
    public int ChangeStats(int i)
    {
        return i + 10;
    }
```



#####  :octocat: [2.自定义特性[注解]](#top) <b id="target2"></b> 
`概括`:`这才是真正的主题,.NET内置特性可以影响编译器的执行,但是自定义特性却不会,但是自定义特性非常强大的因素在于使用反射,代码可以以读取这些元数据,
使用它们在运行时做出决策,这在 ASP.NET CORE WEB 开发框架尤其使用频繁。自定义特性可以影响代码运行方式` <br/> <br/>
:one: `所有的特性类[注解类]都继承自  Attribute 所以自定义特性类  也是需要` <br/>
:two: `定义类的时候,类名需要Attribute后缀,使用的时候可以去掉` <br/>
:three: `其次你要指定这个特性可以应用到那些类型的程序集上面(类,接口,方法,结构体,属性...)` <br/>
:four: `是否可以多次应用到同一个程序元素上面` <br/>
:five: `特性应用到接口或类上面,是否由派生类和接口继承` <br/>
:seven: `这个特性有哪些必选参数和可选参数` <br/>

```c#
// 使用注解类的时候如果发现没有加上 Attribute 后缀,编译器会自动加上,并加载特性类,没有的时候不会加上
class BanUseAttribute:Attribute
{
}
```

#####  :octocat: [3.指定 AttributeUsage 特性](#top) <b id="target3"></b> 
`概括`:`特性本身需要使用一个特性来标记自身,来说明我这个特性所支持的程序集类型 `
* `必选参数`:`AttributeTargets 应用目标,枚举值`
* `可选参数`:`AllowMultiple 获取或设置一个布尔值，该值指示能否为一个程序元素指定多个指示属性实例。就只指定了这个注解,还可以指定其他的注解不`
* `可选参数`:`Inherited 获取或设置 Boolean 值，该值确定指示的属性是否由派生类和重写成员继承。`
* `可选参数`:`ValidOn 获取一组值，这组值标识指示的属性可应用到的程序元素。`

##### AttributeTargets

```c#

Assembly = 1, // 摘要:特性可以应用于程序集。

Module = 2, // 摘要: 特性可以应用于模块中。

Class = 4, // 摘要:特性可以应用于类。

Struct = 8, // 摘要:特性可以应用于结构;即，类型值。

Enum = 16, // 摘要:特性可以应用于枚举。

Constructor = 32, // 摘要:特性可以应用于构造函数。

Method = 64, // 摘要:特性可以应用于方法。 

Property = 128, // 摘要:特性可以应用于属性。

Field = 256, // 摘要:特性可以应用于字段。

Event = 512, // 摘要:特性可以应用于事件。

Interface = 1024, // 摘要:特性可以应用于接口。

Parameter = 2048, // 摘要:特性可以应用于参数。

Delegate = 4096, // 摘要:特性可以应用于委托。

ReturnValue = 8192, // 摘要:特性可以应用于返回的值。

GenericParameter = 16384, // 摘要:特性可以应用于泛型参数。

All = 32767 // 摘要: 特性可以应用于任何应用程序元素。
```
`如果同时适用于多种程序集 那么你可以 使用 OR 操作符 |`
```c#
//适用于 类个 方法
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method ,AllowMultiple =true,Inherited =false)]
public class BanUseAttribute:Attribute
{
    public BanUseAttribute() {}

    public BanUseAttribute(string message)
    {
        this.message = message;
    }
    public BanUseAttribute(string message, string placeInfo)
    {
        this.message = message;
        this.placeInfo = placeInfo;
    }
    //禁用说明
    public String message { get; set; }
    //替代信息
    public String placeInfo { get; set; }

}
```

#####  :octocat: [4.特性的参数问题](#top) <b id="target4"></b> 
`参数分为必选参数和可选参数,可选参数就是不再构造函数中的其他参数`
`匹配无参数构造函数`
```c#
[BanUse]
class Person
{
   ...
}
```
`匹配带参数的构造函数`
```c#
[BanUse("这个类已经不再被支持","请使用 LabCom.lib.Entity.Account")]
```
`可选参数初始阿虎使用`: `参数名`:`参数值`
```c#
[BanUse("这个类已经不再被支持",placeInfo = "请使用 LabCom.lib.Entity.Account")]
```

#####  :octocat: [3.得到调用者信息](#top) <b id="target5"></b> 
* `CallerFilePath`:`在那个文件调用此方法 参数必须有默认值 值由编译器自动填入`
* `CallerLineNumber`:`调用行号  参数必须有默认值 值由编译器自动填入`
* `CallerMemberName`:`调用此方法的方法名称 参数必须有默认值 值由编译器自动填入`

```C#
public void Print([CallerFilePath] string filename="Person.cs", [CallerLineNumber] int linenumer=0
    ,[CallerMemberName] string methodname="Print")
{
    Console.WriteLine($"文件:{filename}");
    Console.WriteLine($"行号:{linenumer}");
    Console.WriteLine($"调用者名称:{methodname}");
} 
```


--------------------
`作者:` `KickGod` 
`完成时间`:`2019年1月2日15:09:56`
`备注信息`: `随意!你随意` 
