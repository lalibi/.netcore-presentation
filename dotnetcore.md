# .NET Core

---

## History

- Microsoft started development on the .NET Framework in the late 1990s originally under the name of Next Generation Windows Services (NGWS). <!-- .element: class="fragment fade-up" -->
- By late 2001 the first beta versions of .NET 1.0 were released. <!-- .element: class="fragment fade-up" -->
- The first version of .NET Framework was released on 13 February 2002, bringing managed code to Windows NT 4.0, 98, 2000, ME and XP. <!-- .element: class="fragment fade-up" -->

--

<img height="550" data-src="https://upload.wikimedia.org/wikipedia/commons/d/d3/DotNet.svg" alt=".NET Framework History">

<aside class="footnote">By <a href="//commons.wikimedia.org/wiki/User:Soumyasch" title="User:Soumyasch">Soumyasch</a> - <span class="int-own-work" lang="en">Own work</span>, <a href="http://creativecommons.org/licenses/by-sa/3.0/" title="Creative Commons Attribution-Share Alike 3.0">CC BY-SA 3.0</a>, <a href="https://commons.wikimedia.org/w/index.php?curid=2953328">Link</a></aside>

---

### Microsoft announced .NET Core in 2014

- Free <!-- .element: class="fragment fade-up" -->
- Cross Platform <!-- .element: class="fragment fade-up" -->
- Open source <!-- .element: class="fragment fade-up" -->

---

## C# has evolved

Currently at version 7.0

- Async/Await <!-- .element: class="fragment fade-up" -->
- Lambdas <!-- .element: class="fragment fade-up" -->
- Generics <!-- .element: class="fragment fade-up" -->

---

## Coding best practices

- Unit Testing <!-- .element: class="fragment fade-up" -->
- Dependency Injection <!-- .element: class="fragment fade-up" -->

---

## Installing .NET Core SDK

- ![](https://www.google.com/s2/favicons?domain=www.microsoft.com) [.NET](https://www.microsoft.com/net)
    - ![](https://www.google.com/s2/favicons?domain=www.microsoft.com) [.NET and C# - Get Started in 10 Minutes](https://www.microsoft.com/net/learn/get-started/windows)
    - ![](https://www.google.com/s2/favicons?domain=www.microsoft.com) [.NET Downloads for Windows](https://www.microsoft.com/net/download/windows)

--

## Using Chocolatey

```
choco install dotnetcore-sdk -y
```

---

## Checking status

```
dotnet

dotnet --version

where dotnet
<or>
gcm dotnet
```

---

## Getting started

```
dotnet --help

dotnet new --help

dotnet new console --help
```

---

## Starting a new Console app

```
mkdir consoleapp

cd consoleapp

dotnet new console
```

--

### `Program.cs`

```C#
using System;

namespace consoleapp
{
    class Program
    {
        static void Main(string[] args)
        {
            int i = 0;
            var name = args[0];

            Console.WriteLine($"Hello {name}!");
        }
    }
}

```

--

### Running the app

```
dotnet build

dotnet run

dotnet publish -o output
```

---

## Starting a new Web app

```
cd ..

mkdir webapp

cd webapp

dotnet new web
```

--

### `Program.cs`

```C#
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace webapp
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}
```

--

### `Startup.cs`

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

namespace webapp
{
    public class Startup
    {
        public IConfiguration Configuration { get; set; }

        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        // This method gets called by the runtime. Use this method to add services to the container.
        // For more information on how to configure your application, visit https://go.microsoft.com/fwlink/?LinkID=398940
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseMvc();

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync("Hello World!");
            });
        }
    }
}
```

--

### `appsettings.json`

```json
{
    "SiteName": ".NET Core Workshop!"
}
```

--

### `Pages\Index.cshtml`

```razor
@page
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

@{
    var name = "Nikos";
}

<h1>Home page of "@Configuration["SiteName"]"</h1>

<footer>Copyright &copy; @(DateTime.Now.Year)</footer>
```

--

### Running the app

```
dotnet build

dotnet run

dotnet publish -o output
```

OR

<kbd>F5</kbd> / <kbd>Ctrl</kbd>-<kbd>F5</kbd>

---

## Starting a new Razor Pages app

```
cd ..

mkdir razorapp

cd webapp

dotnet new web
```

--

## `Startup.css`

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using razorapp.Domain;

namespace razorapp
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContext<AppDbContext>(options =>
                options.UseInMemoryDatabase("demo")
            );

            services.AddLogging();

            services.AddMvc();
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
        {
            loggerFactory.AddConsole();
            loggerFactory.AddDebug();

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Error");
            }

            app.UseStaticFiles();

            app.UseMvc();
        }
    }
}
```

--

### `appsettings.json`

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  }
}
```

--

### `appsettings.Development.json`

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  }
}
```

--

### `Pages\Customers\Create.cshtml`

```razor
@page
@model razorapp.Pages.Customers.CreateModel

@{
    ViewData["Title"] = "Customers";
}

<h2>Customers</h2>

<p>Enter data:</p>

<form method="POST">
    <label for="">First Name:</label>
    <input asp-for="Customer.FirstName" />
    <span asp-validation-for="Customer.FirstName" class="error"></span>

    <label for="">Last Name:</label>
    <input asp-for="Customer.LastName" />

    <input type="submit" />

    <div asp-validation-summary="All"></div>
</form>

@section Scripts
{
    @{ await Html.RenderPartialAsync("_ValidationScriptsPartial"); }
}
```

--

### `Pages\Customers\Create.cshtml.cs`

```C#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;
using razorapp.Domain;

namespace razorapp.Pages.Customers
{
    public class CreateModel : PageModel
    {
        [BindProperty]
        public Customer Customer { get; set; }

        [TempData]
        public string Message { get; set; }

        private readonly AppDbContext _db;

        private ILogger<CreateModel> _log;

        public CreateModel(AppDbContext db, ILogger<CreateModel> log)
        {
            _db = db;
            _log = log;
        }

        public async Task<IActionResult> OnPostAsync()
        {
            if (!ModelState.IsValid)
            {
                return Page();
            }

            _db.Customers.Add(Customer);

            await _db.SaveChangesAsync();

            Message = $"Customer {Customer.Id} created";

            _log.LogInformation(Message);

            return RedirectToPage("./Index");
        }
    }
}
```

--

### `Pages\Customers\Index.cshtml`

```razor
@page
@model IndexModel

@{
    ViewData["Title"] = "Customers";
}

<div class="row">

    <h3>@Model.Message</h3>

    <form method="POST">
        <table class="table">
            <thead>
                <tr>
                    <td>Id</td>
                    <td>First Name</td>
                    <td>Last Name</td>
                    <td>&nbsp;</td>
                </tr>
            </thead>
            <tbody>
                @foreach (var customer in Model.Customers)
                {
                    <tr>
                        <td>@customer.Id</td>
                        <td>@customer.FirstName</td>
                        <td>@customer.LastName</td>
                        <td>
                            <a asp-page="./Edit" asp-route-id="@customer.Id">edit</a>
                            <button asp-page-handler="Delete" asp-route-id="@customer.Id" type="submit">delete</button>
                        </td>
                    </tr>
                }
            </tbody>
        </table>
    </form>
    <a asp-page="./Create">Create New Customer</a>
</div>
```

--

### `Pages\Customers\Index.cshtml.cs`

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.EntityFrameworkCore;

using razorapp.Domain;

namespace razorapp.Pages.Customers
{
    public class IndexModel : PageModel
    {
        [TempData]
        public string Message { get; set; }

        public IList<Customer> Customers { get; private set; }

        private readonly AppDbContext _db;

        public IndexModel(AppDbContext db)
        {
            _db = db;
        }

        public async Task OnGetAsync()
        {
            Customers = await _db.Customers.ToListAsync();
        }

        public async Task<IActionResult> OnPostDeleteAsync(int id)
        {
            var customer = await _db.Customers.FindAsync(id);

            if (customer != null)
            {
                _db.Customers.Remove(customer);
                await _db.SaveChangesAsync();
            }

            return RedirectToPage();
        }
    }
}
```

--

### `Pages\Customers\Edit.cshtml`

```razor
@page "{id:int}"
@model razorapp.Pages.Customers.EditModel

@{
    ViewData["Title"] = "Edit Customers";
}

<h2>Edit Customer - @Model.Customer.Id</h2>

<p>Enter data:</p>

<form method="POST">
    <input asp-for="Customer.Id" type="hidden" />

    <label for="">First Name:</label>
    <input asp-for="Customer.FirstName" />
    <span asp-validation-for="Customer.FirstName" class="error"></span>

    <label for="">Last Name:</label>
    <input asp-for="Customer.LastName" />

    <input type="submit" />

    <div asp-validation-summary="All"></div>
</form>

@section Scripts
{
    @{ await Html.RenderPartialAsync("_ValidationScriptsPartial"); }
}
```

--

### `Pages\Customers\Edit.cshtml.cs`

```C#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.EntityFrameworkCore;

using razorapp.Domain;

namespace razorapp.Pages.Customers
{
    public class EditModel : PageModel
    {
        [BindProperty]
        public Customer Customer { get; set; }

        private readonly AppDbContext _db;

        public EditModel(AppDbContext db) { _db = db; }

        public async Task<IActionResult> OnGetAsync(int id)
        {
            Customer = await _db.Customers.FindAsync(id);

            if (Customer == null)
            {
                return RedirectToPage("./Index");
            }

            return Page();
        }

        public async Task<IActionResult> OnPostAsync()
        {
            if (!ModelState.IsValid)
            {
                return Page();
            }

            _db.Attach(Customer).State = EntityState.Modified;

            try
            {
                await _db.SaveChangesAsync();
            }
            catch(DbUpdateConcurrencyException ex)
            {
                throw new System.Exception($"Customer {Customer.Id} saved by someone else in the meantime.", ex);
            }

            var customer = await _db.Customers.FindAsync(Customer.Id);

            if (customer == null)
            {
                return RedirectToPage("./Index");
            }

            customer.FirstName = Customer.FirstName;
            customer.LastName = Customer.LastName;

            await _db.SaveChangesAsync();

            return RedirectToPage("./Index");
        }
    }
}
```

---

## Resources / Next steps

- ![](https://www.google.com/s2/favicons?domain=mva.microsoft.com) [ASP.NET Core - Beginner - Microsoft Virtual Academy](https://mva.microsoft.com/en-us/training-courses/asp-net-core-beginner-18153?l=VM5gy36dE_6611787171)
- ![](https://www.google.com/s2/favicons?domain=mva.microsoft.com) [ASP.NET Core - Intermediate - Microsoft Virtual Academy](https://mva.microsoft.com/en-us/training-courses/aspnet-core-intermediate-18154?l=tigJ5mbeE_8711787171)
- ![](https://www.google.com/s2/favicons?domain=mva.microsoft.com) [ASP.NET Core - Advanced - Microsoft Virtual Academy](https://mva.microsoft.com/en-us/training-courses/aspnet-core-advanced-18155?l=Rw9sqs7dE_7011787171)
