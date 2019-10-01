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

Always use helper methods to generate urls, so they update as routes do, dynamically

Razor view engine
@ identifys server side code
You can escape an @ with another @. @@
use @: to explicitly define line as content not code
@() wraps code to avoid confusion

You can inject a service into a view using the @inject directive
But prob not good design unless just reformatting ??

###html helpers
```c#
@Html.ActionLink(text, method, new { id = 1 } ); // generates the anchor tab
@Url.Action(); // just generates path

```

###tag helpers
They look like attributes of html, not code, need enabling
```html
<a asp-action="AnotherAction" asp-route-employeename="@currentname">Press me</a>
```

###partial views
convention is that filename starts with an underscore, and is in views\shared folder.

```#chtml
@model Models.SimpleModel
 
@await Html.PartialAsync("_MyPartialView")
Value received in index view from model is @Model.Value
@await Html.PartialAsync("_MyPartialView")
```

##View Components
Has a public non abstract class, normally derived from ViewComponent, implements InvokeAsync

And a view ( in views\shared\components )

Render with <vc:My> or @await Component.InvokeAsync("My",5)

Advice is to avoid these ?



##tip : 
Alt-F12 Peek Definition

Can inject IHostingEnvironment into controllers