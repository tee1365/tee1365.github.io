---
title: Creating New Blog 1
date: 2020-08-09 14:50:19
tags:
---

### 1. Create classlib project

### 2. Create reference, Add entityframeworkcore, entityframeworkcore.sqlite

### 3. Add model in domain

### 4. add datacontext in persistence folder,extend DbContext

```cs
public DataContext (DbContextOptions options) : base (options) { }
public DbSet<Value> Values { get; set; }
```

### 5. add datacontext to service

```cs
//ConfigureServices
services.AddDbContext<DataContext> (opt => {
   opt.UseSqlite (Configuration.GetConnectionString ("DefaultConnection"));
});
```

### 6. add sqlite connection at appsetting.json

```json
// appsetting.json
"ConnectionStrings": {
   "DefaultConnection": "Data source=JustinBlog.db"
}
```

### 7. add dotnet-ef via dotnet cli, migrate database

```bash
dotnet ef migrations add "Initial" -p Persistence -s JustionBlog
```

### 8. change program.cs

      ```cs
      // program.cs
      public static void Main (string[] args) {
         var host = CreateHostBuilder (args).Build ();
         using (var scope = host.Services.CreateScope ()) {
            var services = scope.ServiceProvider;
            try {
               var context = services.GetRequiredService<DataContext> ();
               context.Database.Migrate ();
            } catch (Exception e) {
               var logger = services.GetRequiredService<ILogger<Program>> ();
               logger.LogError (e, "An error occured during migration");
            }
         }
         host.Run ();
      }
      ```

using语句，定义一个范围，在范围结束时处理对象。
场景：
当在某个代码段中使用了类的实例，而希望无论因为什么原因，只要离开了这个代码段就自动调用这个类实例的Dispose。

      ```cs
      using (Class1 cls1 = new Class1(), cls2 = new Class1())
      {
      // the code using cls1, cls2
      }
      // call the Dispose on cls1 and cls2
      ```

### 9. Adding test data at datacontext, override OnModelCreating method, then run dotnet-ef command again

```cs
protected override void OnModelCreating (ModelBuilder builder) {
   builder.Entity<Value> ().HasData (
      new Value { Id = 1, Name = "111" },
      new Value { Id = 2, Name = "222" }
   );
}
```

### 10. Adding test controller
