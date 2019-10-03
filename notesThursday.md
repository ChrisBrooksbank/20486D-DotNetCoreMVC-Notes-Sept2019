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

#Managing Security

Two Factor Authentication, easy to setup on Azure AD

IdentityClass inheriting from IdentityUser and add attributes to it.
Requires IdentityDBContext for all database communications.
Requires call to AddDefaultIdentity in ConfigureServices
Requires call to UseAuthentication in Configure

AddDefaultIdentity

```c#
services.AddDefaultIdentity<WebsiteUser>(options =>
            {
                options.Password.RequiredLength = 10;
                options.Password.RequiredUniqueChars = 3;
                options.Password.RequireDigit = true;
                options.Password.RequireNonAlphanumeric = true;
                options.Password.RequireUppercase = true;
                options.Password.RequireLowercase = false;
            })
                .AddEntityFrameworkStores<AuthenticationContext>();
```

(created new app which authenticated using Azure AD)

Using razor class library, so you wont see views, just need to create AccountController with a couple of endpoints.

Database created with user, roles and claims tables.

[Authorize(roles|claims)] add attribute to controller actions
[Anonymous]

could have CustomClaimsProvider

Best approach is to restict access using claims.

##Cross site scripting
Shouldnt usually be an issue
If render back something user has added, without encoding, can have this problem

Is encoded by default
But if you did @Html.Raw(content) => oops

##Cross-Site Request Forgery
I get a authentication cookie from first site, I goto another site, it sends the auth cookie from 1st site

[ValidateAntiForgeryToken] // this is a filter runs before request executed
AutoValidateAntiForgeryToken

Uses hidden field, if doesnt match boom

see : https://stackoverflow.com/questions/13621934/validateantiforgerytoken-purpose-explanation-and-example

"MVC's anti-forgery support writes a unique value to an HTTP-only cookie and then the same value is written to the form. When the page is submitted, an error is raised if the cookie value doesn't match the form value."

"To use it, decorate the action method or controller with the ValidateAntiForgeryToken attribute and place a call to @Html.AntiForgeryToken() in the forms posting to the method."

#Sql Injection
https://www.slideshare.net/stamparm/euro-python-2011miroslavstamparsqlmapsecuritydevelopmentinpython/16-Exploits_of_a_Mom_XKCDW

#CORS
Cross Origin Resource Sharing

* You download content from a site
* Includes Ajax calls to another domain, problem, CORS stops this, unless CORS policy says it is allowed

So setup a CORS policy , in startup app.UseCors(fred => fred.whatever);

#SSL

##HttpOnly Cookies
https://dotnetcoretutorials.com/2017/01/15/httponly-cookies-asp-net-core/

HttpOnly is a flag that can be used when setting a cookie to block access to the cookie from client side scripts. Javascript for example cannot read a cookie that has HttpOnly set. This helps mitigate a large part of XSS attacks as many of these attempt to read cookies and send them back to the attacker, possibly leaking sensitive information or worst case scenario, allowing the attacker to impersonate the user with login cookies.

(An attacker can use a tool such as XSS Tunnel to bypass HTTPOnly protection.)

```http
Set-Cookie: <name>=<value>[; <Max-Age>=<age>]
[; expires=<date>][; domain=<domain_name>]
[; path=<some_path>][; secure][; HttpOnly]
```

##Secure Cookies
Secure cookies are a type of HTTP cookie that have Secure attribute set, which limits the scope of the cookie to "secure" channels (where "secure" is defined by the user agent, typically web browser).[1] When a cookie has the Secure attribute, the user agent will include the cookie in an HTTP request only if the request is transmitted over a secure channel (typically HTTPS).[1] Although seemingly useful for protecting cookies from active network attackers, the Secure attribute protects only the cookie's confidentiality

##SameSite Cookies
Introduced by Google ?

#Performance and Communication ( Module 12 )

##Caching
Increases scalability
Can cache HTML (fragments or ) or data

cache tag helper attributes

```c#
<cache expires-on="@new DateTime(2025, 12, 31, 23, 59, 59)">
    <div>
        @DateTime.Now.ToString("dd/MM/yyyy hh:mm")
    </div>
</cache>
 
<cache expires-after="TimeSpan.FromSeconds(60)">
    <div>
        @DateTime.Now.ToString("dd/MM/yyyy hh:mm")
    </div>
</cache>
 
<cache expires-sliding="TimeSpan.FromSeconds(5)">
    <div>
        @DateTime.Now.ToString("dd/MM/yyyy hh:mm:ss")
    </div>
</cache>
```

```c#
<cache vary-by-query="MaxPrice, MinPrice">
    <div>
        @for (int i = 0; i < Model.Count; i++)
        {
            <div>
                @string.Format("{0}: {1}", Model[i].Name, Model[i].Price)
            </div>
        }
    </div>
</cache>
```

IMemoryCache, register the service in ConfigureServices

```c#
public class HomeController : Controller
{
    private IProductService _productService;
    private IMemoryCache _memoryCache;
    private const string PRODUCT_KEY = "Products";
 
    public HomeController(IProductService productService, IMemoryCache memoryCache)
    {
        _productService = productService;
        _memoryCache = memoryCache;
    }
 
    public IActionResult Index()
    {
        List<Product> products;
        if (!_memoryCache.TryGetValue(PRODUCT_KEY, out products))
        {
            products = _productService.GetProducts();
            _memoryCache.Set(PRODUCT_KEY, products);
        }
        return View(products);
    }
}
```

Can use Redis cache ( IDistributedCache ), call AddDistributedRedisCache to register.

##State storage
client side : hidden fields, cookies, query strings

serverside: TempData (shorter lifespan than sessionstate - lives for request, including after redirect) , HttpContext.Items, Cache, Dependency Injection, SessionState (20 mins by default, HttpContext.Session)

HTML5 - local and session storage objects.
Local storage persists until removed and is shared.
session storage is automatically removed, per tab.
On browser.

```js
var storage_key = "num_of_visits";
var numberOfVisitsString = localStorage.getItem(storage_key);
var numberOfVisits = 1;
if (numberOfVisitsString) {
    numberOfVisits = parseInt(numberOfVisitsString) + 1;
}
alert("This page has been visited " + numberOfVisits + " times");
if (numberOfVisits >= 5) {
    localStorage.removeItem(storage_key);
} else {
    localStorage.setItem(storage_key, numberOfVisits);
}
```

##Web Sockets - two way communication
SignalR introduced to get around lack of support of websockets some browsers.

SignalR has concept of a hub -server side.
ClientSide - javascript to allow commms with hub.

Serializing messages : JSON or MessagePack






