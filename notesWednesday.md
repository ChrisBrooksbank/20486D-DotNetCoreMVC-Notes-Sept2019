#Wednesday Notes

ButterFlys ! ( module 6 ), yesterdays lab

#tag-helpers recap

```html
<span asp-validation-for="CommonName"></span>

<select asp-for="ButterflyFamily" asp-items="Html.GetEnumSelectList<Family>()">
  <option selected="selected" value="">Select</option>
</select>

 <input asp-for="CommonName" />

<form method="post" enctype="multipart/form-data" asp-action="Create">

<span asp-validation-for="ButterflyFamily"></span>

<textarea asp-for="Characteristics"></textarea>
```

#database persistance

SqlConnection(connectionString)
SqlCommand
SqlDataReader - forward only, read only

SqlDataAdapter - allows for caching

SqlProvider

install via nuget

But rewrite on db schema change

Entity framework - ORM object relational mapping

3 layers : logical schema, mapping schema, conceptual schema

Code first ( us ) , model first, database first, **CORE only supports code first**

( there is a code first from existing database wizard )

We will have a context object which inherits from DbContext
DbSet<T>

```c#
public class HrContext : DbContext
{
    public DbSet<Person> Candidates{ get; set; }
}

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddDbContext<HrContext>(options => options.UseSqlite("Data Source=example.db"));
        services.AddMvc();
    }
    public void Configure(IApplicationBuilder app)
    {
        app.UseMvc();
    }
}
    
```

(Use nuget to install Microsoft.EntityFrameworkCore.Sqlite )

OnModelCreating() / database migration

navigation properties in EF :

```c#
public class Country
{
    public int CountryId { get; set; }
    public string Name { get; set; }
    public int Size { get; set; }
    public List<City> Cities { get; set; }    
    public List<Person> People { get; set; }
}
```

**Explicit|Eager (use Include)|Lazy loading**, for navigation properties

##Explicit Loading


##Lazy loading

Marking navigational properties as virtual, means can be overwritten, and it is - by framework, creating class on fly using reflection,  allowing for Lazy Loading. Enable lazy loading in startup configuration.

```c#
public class Country
{
    public int CountryId { get; set; }
    public string Name { get; set; }
    public int Size { get; set; }
    public virtual ICollection<City> Cities { get; set; }    
    public virtual ICollection<Person> People { get; set; }
}
 
public class City
{
    public int CityId { get; set; }
    public string Name { get; set; }
    public int Size { get; set; }
    public int CountryId { get; set; }
    public virtual Country Country { get; set; }    
    public virtual ICollection<Person> People { get; set; }
}
 
public class Person
{
    public int PersonId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public int CountryId { get; set; }
    public virtual Country Country { get; set; }
    public int CityId { get; set; }
    public virtual City City { get; set; }
}
```

##Eager loading :
Gets all data in one hit, so not generally used.

```c#
var countries = _context.Countries
        .Include(country => country.Cities)
            .ThenInclude(city => city.People)
        .ToList();
```

##appsettings.json
Put connection string in here.


```json
{
  "ConnectionStrings": {
    "DefaultConnection":  
        "Server=(localdb)\\MSSQLLocalDB;Database=HumanResources;Trusted_Connection=True"
  }
}
```

```c#
public void ConfigureServices(IServiceCollection services)
{
    string connectionString = _configuration.GetConnectionString("DefaultConnection");
    services.AddDbContext<HrContext>(options => options.UseSqlServer(connectionString));
    services.AddMvc();
}
```

KeyVault on azure will encrypt connection strings, auth using azure AD

##Repository interface
sits over DbContext

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<IRepository, MyRepository>();
    string connectionString = _configuration.GetConnectionString("DefaultConnection");
    services.AddDbContext<HrContext>(options => options.UseSqlServer(connectionString));
    services.AddMvc();
}
```

return value from hierachy in settings

```c#
public class SomeController : Controller
{
    private IConfiguration _configuration;
 
    public SomeController(IConfiguration configuration)
    {
        _configuration = configuration;
    }
 
    public IActionResult Index()
    {
        string value = _configuration["address:city"];
        return Content(value);
    }
}
```

##Migrations

##TryUpdateModelAsync  ???

```c#
var cupcakeToUpdate = _repository.GetCupcakeById(id);
bool isUpdated = await TryUpdateModelAsync<Cupcake>(
                      cupcakeToUpdate,
                      "",
                      c => c.BakeryId, 
                      c => c.CupcakeType, 
                      c => c.Description, 
                      c => c.GlutenFree,
                      c => c.Price);
if (isUpdated == true)
{
  _repository.SaveChanges();
  return RedirectToAction(nameof(Index));
}
PopulateBakeriesDropDownList(cupcakeToUpdate.BakeryId);
return View(cupcakeToUpdate);
```

#Module 8: Using Layouts, CSS and JavaScript in ASP.NET Core MVC

##Layouts
* Style template, content layout
* in Views\Shared
* @RenderBody says where to place content of a view in the layout
* ViewBag can pass info to the layout e.g. title. @ViewBag.Title
* View executed first, by razor engine, then the layout
* _ViewStart.cshtml, in top level \Views folder, can define the layout
* Can also specify layout in the controller action, view() override
* @RenderSection("section1")


```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>
    @RenderSection("section1")
    <div>This is layout content between section1 and the body</div>
    @RenderBody()
    <div>This is layout content between the body and section2</div>
    @RenderSection("section2")
</body>
</html>
```

```html
@{
    Layout = "_Layout";
}
 
@section section1
{
    <div>This is section1 content</div>
}
 
<div>This is the body</div>
 
@section section2
{
    <div>This is section2 content</div>
}
```

#package-lock.json
https://docs.npmjs.com/files/package-locks
your dependancies have dependencies, normally run "npm shrinkwrap"

override useStaticFiles to use Node_Modules, otherwise not picked up

```c#
app.UseNodeModules(env.ContentRootPath) // extension method created in lab
```

#Client Side Validation
MS supports JQuery.Validate
asp-validation-for // tag-helper

#Client Side Development ( Module 9 )

##BootStrap

##Sass

```sass
/* Define a mixin */
@mixin normalized-text() {
  font-weight: 300;
  line-height: 1.2;
}
 
/* Use a Mixin in multiple places */
.description {
  @include normalized-text();
  color: red;
}
.comment {
  @include normalized-text();
  color: grey;
}
```

##Task Runners
Tasks can be chained. 
e.g. tasks : build, clean ...

###Grunt
Gruntfile.js

```js
module.exports = function(grunt) {
    grunt.initConfig({
        sass: {
            dist: {
                files: [{
                    expand: true,
                    cwd: 'Styles',
                    src: ['**/*.scss'],
                    dest: 'wwwroot/styles',
                    ext: '.css'
                }]
            }
        }
    });    
};
```

###Gulp

running "gulp tasks", shows you all tasks and which tasks each one of those runs, i.e. hierachy


```js
var gulp = require('gulp');
var sass = require('gulp-sass');
 
var paths = {
    webroot: "./wwwroot/"
};
 
paths.sass = "./Styles/*.scss";
paths.compiledCss = paths.webroot + "styles/";
 
gulp.task("sass", function() {
    return gulp.src(paths.sass)
        .pipe(sass().on('error', sass.logError)) 
        .pipe(gulp.dest(paths.compiledCss));
});    
```
* Visual Studio has "task runner explorer"
* can add "gulp sass" as a pre-build event in visual studio


##Bundling.
Reduce number of requests required by browser to render the web page.

##Minification
Reduces whitespace and shortens variable names
gulp-cssmin
gulp-uglify

gulp.watch() // monitor folders for changes.

##Responsive design

ViewPort

@media queries

##Flexbox Layout

Lab !