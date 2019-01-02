### [.Net Core 反射](#top) :grey_exclamation: <b id="top"></b>
`反射是非常重要的一门学问！ 通过这个你可以得到一个类的信息,包括方法,字段,属性,构造函数,特性,它在框架中经常用到! 反射是一个庞大的体系,只能按照需求,慢慢的去了解整个架构,先简单介绍两个类,很明显查资料更加关键`:white_check_mark:

------

- [x] [`1.System.Type 类`](#target1)
- [x] [`2.System.Type 属性`](#target2)
- [x] [`3.System.Type 方法`](#target3)
- [x] [`4.Assembley 类`](#target4)
- [x] [`4.Assembley 属性`](#target5)

------

#####  :octocat: [1.System.Type 类](#top) <b id="target1"></b> 
[`官方API`](https://docs.microsoft.com/zh-cn/dotnet/api/system.type?view=netframework-4.7.2) `通过类可以访问关于任何数据类型的信息`

* `命名空间`
  * `using System;`
  * `using System.Reflection;`

##### 1.如何构造一个Type类
* `1. typeof() 操作符`
```c#
Type personType = typeof(Person); 
Type intType = typeof(Int32);
```
* `2.通过GetType() 继承自Object的终极父类的方法`
```c#
Int32 val = 32;
Person p1 = new Person("JxKicker", "2016110418", 18);
Type personType = p1.GetType();
Type intType = val.GetType();
```
* `3.Type.GetType(String ref) 调用Type类的静态方法`
```c#
Type personType = Type.GetType("IType.Person");
Type intType = Type.GetType("System.Int32");
```
* `Type 只有一个无参数的构造函数 Type()`

#####  :octocat: [2.System.Type 属性](#top) <b id="target2"></b> 
[`官方链接:Type类的属性`](https://docs.microsoft.com/zh-cn/dotnet/api/system.type?view=netframework-4.7.2#%E5%B1%9E%E6%80%A7) `Type
有好几十个属性需要请在官方API查询`
* `Name`:`数据类型名称`
* `FullName`:`数据类型完全名称 包含命名空间`
* `NameSpace`:`数据类型所属的命名空间`
* `Assembly`:` 	获取在其中声明该类型的 Assembly。 对于泛型类型，则获取在其中定义该泛型类型的 Assembly。`
* `Attributes`:`获取与 Type 关联的属性。`
* `BaseType`:`获取当前 Type 直接从中继承的类型。也就是说,我返回这个玩意儿的爸爸 父类 父接口`
* `IsNested`:`获取一个指示当前 Type 对象是否表示其定义嵌套在另一个类型的定义之内的类型的值。普通话表示: 你是否是一个类里面定义的类 `
* `ReflectedType`:`获取用于获取该成员的类对象。`
* `IsAbstract`:`是否是抽象类`
* `IsClass`:`是否是一个类`
* `IsInterface`:`是否是一个接口`
* `IsPublic`：`是否是公共的`
* `IsSealed`:`是否是密封的`
* `IsValueType`:`是否是值类型`
* `UnderlyingSystemType`:`指示表示该类型的公共语言运行时提供的类型。`
```c#
static void Main(string[] args)
{
    Object[] list = { "Jxkicker", 58, 99.56f, 125.32M, new Person("JxKicker", "2016110418", 18) };

    foreach (var val in list) {
        Console.WriteLine("{0}- Type:{1}", val, val.GetType().Name);
    }
    Console.ReadKey();
}
```

#####  :octocat: [3.System.Type 方法](#top) <b id="target3"></b>
[`官方链接:Type类的方法`](https://docs.microsoft.com/zh-cn/dotnet/api/system.type?view=netframework-4.7.2#%E6%96%B9%E6%B3%95)
`Type 有好几十个方法,需要请在官方API查询`
* `GetConstructors()`:`返回为当前 Type 定义的所有公共构造函数。`
* `GetConstructor(Type[])`:`搜索其参数与指定数组中的类型匹配的公共实例构造函数。`
* `GetMethods()`:`返回为当前 Type 的所有公共方法。`
* `GetMethod(String)`:`获得指定名称的方法`
* `GetEvents()`:`返回由当前 Type 声明或继承的所有公共事件。`
* `GetField(String)`:`搜索具有指定名称的公共字段。`
* `GetFields()`:`返回当前 Type 的所有公共字段。`
* `GetProperty(String)`:`搜索具有指定名称的公共属性。`
* `GetCustomAttributes(Boolean)`:`在派生类中重写时，返回应用于此成员的所有自定义属性的数组。`
* `GetCustomAttributes(Type, Boolean)`:` 	在派生类中重写时，返回应用于此成员并由 Type 标识的自定义属性的数组。`
* `GetMember(String)`:`搜索具有指定名称的公共成员。`
* `GetMembers()`:` 返回为当前 Type 的所有公共成员`

|`返回对象的类型`|`方法`|
|:----|:------|
|`ConstructorInfo`|`GetConstructors,GetConstructor`|
|`EventInfo`|`GetEvent,GetEvents`|
|`FieldInfo`|`GetFiled,GetFileds`|
|`MemberInfo`|`GetMembers,GetMember`|
|`PropertyInfo`|`GetProperty,GetPropertys`|



#####  :octocat: [4.Assembley 类](#top) <b id="target4"></b>
`它允许访问给定程序集的元数据,它也包含可以加载和执行程序集的方法,包含非常多的属性和方法,不是说假的,是真的很多属性和方法` [`官方链接:API 信息`](https://docs.microsoft.com/zh-cn/dotnet/api/system.reflection.assembly?view=netframework-4.7.2)

##### 构造一个 Assembley
* `通过 load 方法` [`官方API`](https://docs.microsoft.com/zh-cn/dotnet/api/system.appdomain.load?view=netframework-4.7.2)
```c#
Load(Byte[]) 	
加载带有基于通用对象文件格式 (COFF) 的图像的 Assembly，该图像包含已发出的 Assembly。

Load(AssemblyName) 	
在给定 AssemblyName 的情况下加载 Assembly。

Load(String) 	
在给定其显示名称的情况下加载 Assembly。

Load(Byte[], Byte[]) 	
加载带有基于通用对象文件格式 (COFF) 的图像的 Assembly，该图像包含已发出的 Assembly。
还加载表示 Assembly 的符号的原始字节。

Load(AssemblyName, Evidence) 	
在给定 AssemblyName 的情况下加载 Assembly。

Load(String, Evidence) 	
在给定其显示名称的情况下加载 Assembly。

Load(Byte[], Byte[], Evidence) 	
加载带有基于通用对象文件格式 (COFF) 的图像的 Assembly，该图像包含已发出的 Assembly。 
还加载表示 Assembly 的符号的原始字节。
```
* `LoadFrom(String)`
```c#
Assembly SampleAssembly;
SampleAssembly = Assembly.LoadFrom("c:\\Sample.Assembly.dll");

MethodInfo Method = SampleAssembly.GetTypes()[0].GetMethod("Method1");

ParameterInfo[] Params = Method.GetParameters();
// Display information about method parameters.
// Param = sParam1
//   Type = System.String
//   Position = 0
//   Optional=False
foreach (ParameterInfo Param in Params)
{
    Console.WriteLine("Param=" + Param.Name.ToString());
    Console.WriteLine("  Type=" + Param.ParameterType.ToString());
    Console.WriteLine("  Position=" + Param.Position.ToString());
    Console.WriteLine("  Optional=" + Param.IsOptional.ToString());
}
```
#####  :octocat: [5.Assembley 属性](#top) <b id="target5"></b>
[`官方API`](https://docs.microsoft.com/zh-cn/dotnet/api/system.reflection.assembly?view=netframework-4.7.2#%E5%B1%9E%E6%80%A7)
* `FullName`:`获取程序集的显示名称。`
* `Modules`:`获取包含此程序集中模块的集合。`
* `ReflectionOnly`:`获取 Boolean 值，该值指示此程序集是否被加载到只反射上下文中`
* `EntryPoint`:`获取此程序集的入口点`

#####  :octocat: [6.Assembley 方法](#top) <b id="target6"></b>
[`官方API`](https://docs.microsoft.com/zh-cn/dotnet/api/system.reflection.assembly?view=netframework-4.7.2#%E6%96%B9%E6%B3%95)
* `CreateInstance(String)`:`使用区分大小写的搜索，从此程序集中查找指定的类型，然后使用系统激活器创建它的实例。`
* `CreateInstance(String, Boolean)`:`使用可选的区分大小写搜索，从此程序集中查找指定的类型，然后使用系统激活器创建它的实例。`
* `GetCustomAttributes(Boolean)`:`获取此程序集的所有自定义属性`
* `GetCustomAttributes(Type, Boolean)`:`获取按类型指定的此程序集的自定义属性`
* `GetType(String)`:`获取程序集实例中具有指定名称的 Type 对象`
* `GetName() `:`	获取此程序集的 AssemblyName。`


--------------------
`作者:` `KickGod` 
`完成时间`:`2019年1月2日21:13:26`
`备注信息`: `随意使用` 
