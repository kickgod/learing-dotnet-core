### [EF Core 视图的问题](#top) :grey_exclamation: <b id="top"></b>
`文章简单介绍概括`:white_check_mark:

------

- [x] [`1.EF Core 是如何处理视图的`](#target1)
- [x] [`2.使用例子`](#target2)
- [x] [`3.DBQuery`](#target3)


------

#####  :octocat: [1.EF Core 是如何处理视图的](#top) <b id="target1"></b> 
`概括`:`视图本质上是什么,其实和表类似但是又不是表它是一张或者多张表的映射,视图的本质是脚本 对于SQL 查询语句的保存,表是实体类型
 EFCore 是这样对视图理解的,EF Core 认为视图是 对于实体的查询结果 所以EFCore 提供了查询类型 DbQuery<TViewEntity> 把视图当做查询类型`

* `注意`:`视图，这意味着它将被视为只读查询源和不能作为目标的更新、 插入或删除操作`
#####  :octocat: [2.使用例子](#top) <b id="target2"></b> 

```c#
//视图
public virtual DbQuery<vInstituteToModuleMap> VInstituteToModuleMaps { get; set; }
//表
public  DbSet<Module> Modules { get; set; }
```
`在方法中指定对应视图`
```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);
    //对应哪个视图
    modelBuilder.Query<vInstituteToModuleMap>().ToView("InstituteToModuleView", "dbo");
}
```


#####  :octocat: [3.DBQuery](#top) <b id="target3"></b> 
`概括`:`DbQuery在查询上和DbSet相差不大，基本上通用。 但是只是它只是用来做查询的` 
[`DBQuery<TView> API信息`](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.entityframeworkcore.dbquery-1?view=efcore-2.1)




--------------------
`作者:` `KickGod` 
`完成时间`:`2019年2月3日17:26:19`
`备注信息`: `任意使用` 
