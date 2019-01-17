### [控制器方法返回类型 IActionResult](#top) :grey_exclamation: <b id="top"></b>
`每一个控制器方法一般都具有一个 IActionResult返回类型`:white_check_mark:

------

- [x] [`1.IActionResult`](#target1)
- [x] [`2.例子`](#target2)
- [x] [`3.如果不简写`](#target3)

------

#####  :octocat: [1.IActionResult](#top) <b id="target1"></b> 
`返回类型一般是一个接口 IActionResult asp.net core 提供了五种接口的实现对象`

|`具体实现`|`说明`|`简易写法`|
|:-----|:-----|:-----|
|`JsonResult`|`返回JSON结构数据`|`json(obj)`|
|`RedirectResult`|`跳转到新地址`|`redirect(url)`|
|`FileResult`|`返回文件`|`file()`|
|`ViewResult`|`返回视图页面 是一种非常常见的方式`|`view()`|
|`ContentResult`|`文本内容`|`content(str)`|



#####  :octocat: [2.例子](#top) <b id="target2"></b> 
`使用非常方便`
```c#
public class DemoController : Controller
{
  public IActionResult FileUpload()
  {
      return View();
  }

  [HttpGet]
  public IActionResult Student() {
      return  Json(new { name = "JxKicker",age = 20,sex = true });
  }
}
```

#####  :octocat: [3.如果不简写](#top) <b id="target3"></b> 
`那么自己new 一个对象吧`
```c#
[HttpGet]
public IActionResult Student() {
    JsonResult json = new JsonResult(new { name = "JxKicker", age = 20, sex = true })
    return  json;
}
```




--------------------
`作者:` `JxKicker` 
`完成时间`:`2019年1月17日14:06:55`
`备注信息`: `任意使用`  
