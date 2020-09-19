---
title: Creating New Blog 3
date: 2020-08-12 00:22:23
tags:
---

### 1. Creating basecontroller

```cs
namespace JustinBlog.Controllers {
  [Route ("api/[controller]")]
  [ApiController]
  [Produces ("application/json")]
  public class BaseController : ControllerBase {}
}
```

### 2. Add error handling

```cs
// Errors/RestException.cs
  public class RestException : Exception {
    public HttpStatusCode Code { get; set; }
    public object Errors { get; set; }
    public RestException (HttpStatusCode code, object errors = null) {
        this.Errors = errors;
        this.Code = code;
    }
  }
```

```cs
public class ErrorHandlingMiddleware {
  private readonly RequestDelegate _next;
  private readonly ILogger<ErrorHandlingMiddleware> _logger;
  public ErrorHandlingMiddleware (RequestDelegate next, ILogger<ErrorHandlingMiddleware> logger) {
      this._logger = logger;
      this._next = next;
  }

  public async Task Invoke (HttpContext context) {
      try {
          await _next (context);
      } catch (Exception e) {
          await HandleExceptionAsync (context, e, _logger);
      }
  }

  private async Task HandleExceptionAsync (HttpContext context, Exception e, ILogger<ErrorHandlingMiddleware> logger) {
      object errors = null;
      switch (e) {
          case RestException re:
              logger.LogError (e, "Rest Error");
              errors = re.Errors;
              context.Response.StatusCode = (int) re.Code;
              break;
          case Exception exception:
              logger.LogError (e, "Server Error");
              errors = string.IsNullOrWhiteSpace (e.Message) ? "Error" : e.Message;
              context.Response.StatusCode = (int) HttpStatusCode.InternalServerError;
              break;
      }
      context.Response.ContentType = "application/json";
      if (errors != null) {
          var result = JsonSerializer.Serialize (new {
              errors
          });
          await context.Response.WriteAsync (result);
      }
  }
}
```

### 3. Add swagger

```shell
dotnet add JustinBlog.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

```cs
// ConfigureServices
services.AddSwaggerGen();

// Configure
app.UseSwagger();
app.UseSwaggerUI(c =>{
  c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
});
```

enter `http://localhost:5000/swagger` to open swagger ui

#### Enabling XML comments

```xml
// add config in .csproj file
<PropertyGroup>
  <GenerateDocumentationFile>true</GenerateDocumentationFile>
  <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

```cs
// update service config
services.AddSwaggerGen (c => {
  var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
  var xmlPath = Path.Combine (AppContext.BaseDirectory, xmlFile);
  c.IncludeXmlComments (xmlPath);
});
```

use summary label to create summary of api, use `[Produces ("application/json")]` to generate content type.

```cs
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
[Produces ("application/json")]
[HttpGet]
public async Task<ActionResult<ICollection<Value>>> GetValues () {
    var values = await _context.Values.ToListAsync ();
    return Ok (values);
}
```
