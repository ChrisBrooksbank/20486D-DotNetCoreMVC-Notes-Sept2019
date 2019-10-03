#Thursday Notes - testing and troubleshooting

##Unit tests
Typically want to execute minimum of 75% code base.
Arrange => Act => Assert

Its possible to test Razor pages if you want to, that its returning expected HTML.

Testing controllers or just service classes ?
Recommend testing of routing at least.
Pass it a mock HttpRequest, check which controller and action is called.

Test-Driven development

[TestMethod]

[Moq quickstart](https://github.com/Moq/moq4/wiki/Quickstart)

###IHostingEnvironment
configured in launchsettings.json
IsDevelopment(), IsProduction(), IsStaging(), IsEnvironment("foo")

developer exception page
Prevents the forgetting to turn off detailed exceptions when deployed issue of old.

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else if (env.IsStaging() || env.IsProduction() || env.IsEnvironment("ExternalProduction"))
    {
        app.UseExceptionHandler("/error");
    }
 
    app.UseMvcWithDefaultRoute();
 
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("…");
    });
}
```

Can create own attribute e.g. [HandleException] inheriting ExceptionFilterAttribute

```c#
public class MyExceptionFilter : ExceptionFilterAttribute
{
    public override void OnException(ExceptionContext context)
    {
        if (!context.ModelState.IsValid)
        {
            var result = new ViewResult { ViewName = "InvalidModel" };
            context.Result = result;
            context.ExceptionHandled = true;
        }
    }
}
```

Can add attribute to controller action, or to global collection in startup.cs





