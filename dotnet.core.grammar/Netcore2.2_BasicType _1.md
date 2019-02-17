#### [Dotnet Core 基础类型](#top)  <b id="top"></b>
`介绍一下基础类型的操作`:white_check_mark:

------

- [x] [`1.枚举类型`](#target1)


------

#####  [1.枚举类型](#top) <b id="target1"></b> 
`枚举，也称为枚举、 是整型类型的标签分配到值的子集。 可以使用它们来代替文本，使代码更具可读性且更易维护。使用` `enum` `来声明`
```c#
//表示课程状态
public enum CourceStatus
{
    Using = 0,
    Normal = 1
}
```
`枚举也是可以遍历的`
```c#
static void Main(string[] args)
{
    Array eArray = System.Enum.GetValues(typeof(CourceStatus));
    foreach(CourceStatus status in eArray){
        Console.WriteLine(status);
    }
}
```




--------------------
`作者:` `KickGod` 
`完成时间`:`2019年2月17日14:18:55`
`备注信息`: `任意使用` 
