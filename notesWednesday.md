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

Explicit|Eager (use Include)|Lazy loading, for navigation properties

Marking navigational properties as virtual, means can be overwritten, and it is - by framework, creating class on fly using reflection,  allowing for Lazy Loading. Enable lazy loading in startup configuration.
















