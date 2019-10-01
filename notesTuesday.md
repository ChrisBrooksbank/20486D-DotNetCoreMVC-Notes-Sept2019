#Tuesday Notes

Controllers, Views, Models

##Controllers
Controllers materialise models and pass to view
HttpRequest, routing to controller and action
Convention over configuration, avoid creating own ControllerFactory !
RedirectToAction()
return NotFound(), 404's
ContentResult. For returning simple text. return Content("some text");
IFormCollection : model binding during post, better to bind to own custom model
can expand routes to define parameters
adhoc passing data : ViewBag or ViewData. 
ViewBag.AdHocData = "this is adhoc";
Startup.Cs : App.UseMvcWithDefaultRoute(); 
=> RouteData.Values["Controller"], action, id

##Routing
important !
default route => host:port/<controller>/<action>/<id>
Reason for routes : Improved search engine rankings, urls easier for visitors to understand and edit

```c#
app.UseMvc(routes =>
{
    routes.MapRoute(
        name: "default",
        template: "{controller}/{action}/{param}",
        defaults: new { controller = "Some", action = "ShowParam", param = "val" });
});
```
swapping route order can cause issues
in above if param was called id in url it WOULDNT match
use ? postfix to make param optional
or {num:int} e.g.

attribute based routing : 
```c#
public class MyController : Controller
{
    [Route("My/{param1}/{param2:int}")]
    public IActionResult SomeMethod(string param1, int param2)
    {
        return Content("param1: " + param1 + ", param2: " + param2);
    }
}
```

##Action Filters
e.g. logging code, runs code before ( or possibly after ) core method
some supplied for : Authorization, Logging, Caching
Can apply to action, controller or entire application
Action Filters, Exeption Filters, Result Filters, Resource Filters ( e.g. caching ), Model Binding, Authorization Filters

Inherit from e.g. ActionFilterAttribute. override OnActionExecuting, OnActionExecuted

Can access request and response objects in here, you get a context object

```c#
public class SimpleActionFilterAttribute : ActionFilterAttribute
{
    public override void OnActionExecuting(ActionExecutingContext filterContext) 
    { 
        Debug.WriteLine("This Event Fired: OnActionExecuting");        
    }
 
    public override void OnActionExecuted(ActionExecutedContext filterContext) 
    { 
        Debug.WriteLine("This Event Fired: OnActionExecuted");        
    }
 
    public override void OnResultExecuting(ResultExecutingContext filterContext)
    {
        Debug.WriteLine("This Event Fired: OnResultExecuting");        
    }
 
    public override void OnResultExecuted(ResultExecutedContext filterContext)
    {
        Debug.WriteLine("This Event Fired: OnResultExecuted");        
    }
}
```

Given a LogActionFilterAttribute
 [ServiceFilter(typeof(LogActionFilterAttribute))]

##Views
@model WebApplication.Models.Car : gives intellisense

##tip : 
Alt-F12 Peek Definition

Can inject IHostingEnvironment into controllers