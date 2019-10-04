#Friday Notes

#Web Apis

#REST
Representational State Transfer

#HTTP request

```
GET http://localhost:4392/travelers/1 HTTP/1.1
Accept: text/html, application/xhtml+xml, */*
Accept-Language: en-US,en;q=0.7,he;q=0.3
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Accept-Encoding: gzip, deflate
Host: localhost:4392
DNT: 1
Connection: Keep-Alive
```

#HTTP Response

```
HTTP/1.1 200 OK
Server: ASP.NET Development Server/11.0.0.0
Date: Tue, 13 Nov 2012 18:05:11 GMT
X-AspNet-Version: 4.0.30319
Cache-Control: no-cache
Pragma: no-cache
Expires: -1
Content-Type: application/json; charset=utf-8
Content-Length: 188
Connection: Close
 
{"TravelerId":1,"TravelerUserIdentity":"aaabbbccc","FirstName":"FirstName1","LastName":"LastName1","MobilePhone":"555-555-5555","HomeAddress":"One microsoft road","Passport":"AB123456789"}
```

[Status Codes](https://www.restapitutorial.com/httpstatuscodes.html)

#Postman

( SOAP, WSDL )

Convention over configuration. If we call endpoint GET then it gets GETS routed to it. Or can use [HTTPGet] to make explicit.

* GET
* POST
* PUT - send everything, not just whats changed
* DELETE
(Patch) - can send only whats changed
(Merge) - can send only whats changed

Uses model binding, can use hints such as [FromBody] [FromUrl]

[AcceptVerbs]
[ActionName]

```c#
[Route("api/[controller]")]
public class HomeController
{
	[HttpGet("{id}/{name}")] // this defines position in url
	public string Get(int id, string name)
	{
	  // whatever
	}
}
```

[open API specification](https://swagger.io/specification/)

Swagger. 
Can then use NSwageStudio to generate C# client from the swagger.
SwashBuckle

```c#
  // returns XML if thats whats specified in accept header of request
  // (can also write custom formatters)
  services.AddMvc().AddXmlSerializerFormatters();
```

There are base class methods to return content, appropiate 
```c#
return Ok(_items[id])
```

#JSON.stringify()

XMLHttpRequest object

```html
<!DOCTYPE html>
<html>
<head>
    <title>Index</title>
    <script src="http://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.js">
    </script>
    <script>
        $(function() {
            $.ajax({
                url: "http://localhost:[port]/api/values/key1",
                type: "GET"
            }).done(function (responseText) {
                $("#myDiv").text(responseText);
            }).fail(function () {
                alert("An error has occurred");
            });
        });
    </script>
</head>
<body>
```

#HttpClient

```c#
public class HomeController : Controller
{
    private IHttpClientFactory _httpClientFactory;
 
    public HomeController(IHttpClientFactory httpClientFactory)
    {
        _httpClientFactory = httpClientFactory;
    }
 
    public IActionResult Index()
    {
        HttpClient httpClient = _httpClientFactory.CreateClient();
        httpClient.BaseAddress = new Uri("http://localhost:[port]");
        HttpResponseMessage response = httpClient.GetAsync("api/values/key1").Result;
        if (response.IsSuccessStatusCode)
        {
            string result = response.Content.ReadAsStringAsync().Result;
            return Content(result);
        }
        else
        {
            return Content("An error has occurred");
        }
    }
}
```

[OData](https://www.odata.org/)

[httpClientFactory](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)

MSPaint






