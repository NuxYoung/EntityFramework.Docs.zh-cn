---
title: 加载相关数据 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 86b9d08377ea8295b746e5f0217a408edcfe1517
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370468"
---
# <a name="loading-related-data"></a>加载相关数据

Entity Framework Core 允许你在模型中使用导航属性来加载相关实体。 有三种常见的 O/RM 模式可用于加载关联数据。

* **预先加载**表示从数据库中加载关联数据，作为初始查询的一部分。
* **显式加载**表示稍后从数据库中显式加载关联数据。
* **延迟加载**表示在访问导航属性时，从数据库中以透明方式加载关联数据。

> [!TIP]  
> 可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)。

## <a name="eager-loading"></a>预先加载

可以使用 `Include` 方法来指定要包含在查询结果中的关联数据。 在以下示例中，结果中返回的blogs将使用关联的posts填充其 `Posts` 属性。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> Entity Framework Core 会根据之前已加载到上下文实例中的实体自动填充导航属性。 因此，即使不显式包含导航属性的数据，如果先前加载了部分或所有关联实体，则仍可能填充该属性。

可以在单个查询中包含多个关系的关联数据。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a>包含多个层级

使用 `ThenInclude` 方法可以依循关系包含多个层级的关联数据。 以下示例加载了所有博客、其关联文章及每篇文章的作者。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

可通过链式调用 `ThenInclude`，进一步包含更深级别的关联数据。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

可以将来自多个级别和多个根的关联数据合并到同一查询中。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

你可能希望将已包含的某个实体的多个关联实体都包含进来。 例如，当查询 `Blogs` 时，你会包含 `Posts`，然后希望同时包含 `Posts` 的 `Author` 和 `Tags`。 为此，需要从根级别开始指定每个包含路径。 例如，`Blog -> Posts -> Author` 和 `Blog -> Posts -> Tags`。 这并不意味着会获得冗余联接查询，在大多数情况下，EF 会在生成 SQL 时合并相应的联接查询。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

> [!CAUTION]
> 从版本 3.0.0 开始，每个 `Include` 都将导致向关系提供程序生成的 SQL 查询添加额外的 JOIN，而以前的版本则生成其他 SQL 查询。 这可以显著地改变（提升或降低）查询性能。 具体而言，具有大量 `Include` 运算符的 LINQ 查询可能需要将分解为多个单独的 LINQ 查询，以避免笛卡尔爆炸问题。

### <a name="filtered-include"></a>经过筛选的包含

> [!NOTE]
> EF Core 5.0 中已引入了此功能。

在应用包含功能来加载相关数据时，可对已包含的集合导航应用某些可枚举的操作，这样就可对结果进行筛选和排序。

支持的操作包括：`Where`、`OrderBy`、`OrderByDescending`、`ThenBy`、`ThenByDescending`、`Skip` 和 `Take`。

应对传递到 Include 方法的 Lambda 中的集合导航应用这类操作，如下例所示：

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

只能对每个包含的导航执行一组唯一的筛选器操作。 如果为某个给定的集合导航应用了多个包含操作（下例中为 `blog.Posts`），则只能对其中一个导航指定筛选器操作： 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

或者，可对多次包含的每个导航应用相同的操作：

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> 在跟踪查询时，由于[导航修正](tracking.md)，Filtered Include 的结果可能不符合预期。 之前已查询且已存储在更改跟踪器的所有相关实体都将在 Filtered Include 查询的结果中显示，即使它们不符合筛选器的要求也是如此。 在这些情况下使用 Filtered Include 时，请考虑使用 `NoTracking` 查询或重新创建 DbContext。

示例：

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rathat than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a>派生类型上的包含

可以使用 `Include` 和 `ThenInclude` 包括来自仅在派生类型上定义的导航的相关数据。

给定以下模型：

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

所有人员（可以使用许多模式预先加载的学生）的 `School` 导航的内容：

* 使用强制转换

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* 使用 `as` 运算符

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* 使用 `Include` 的重载，该方法采用 `string` 类型的参数

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a>显式加载

可以通过 `DbContext.Entry(...)` API 显式加载导航属性。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

还可以通过执行返回关联实体的单独查询来显式加载导航属性。 如果已启用更改跟踪，则在加载实体时，EF Core 将自动设置新加载的实体的导航属性以引用任何已加载的实体，并设置已加载实体的导航属性以引用新加载的实体。

### <a name="querying-related-entities"></a>查询相关实体

还可以获得表示导航属性内容的 LINQ 查询。

这样可以执行诸如通过相关实体运行聚合运算符而无需将其加载到内存中等操作。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

还可以筛选要加载到内存中的关联实体。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a>延迟加载

使用延迟加载的最简单方式是通过安装 [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) 包，并通过调用 `UseLazyLoadingProxies` 来启用该包。 例如：

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

或在使用 AddDbContext 时：

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

EF Core 接着会为可重写的任何导航属性（即，必须是 `virtual` 且在可被继承的类上）启用延迟加载。 例如，在以下实体中，`Post.Blog` 和 `Blog.Posts` 导航属性将被延迟加载。

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

### <a name="lazy-loading-without-proxies"></a>不使用代理的延迟加载

使用代理进行延迟加载的工作方式是将 `ILazyLoader` 注入到实体中，如[实体类型构造函数](../modeling/constructors.md)中所述。 例如：

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

这不要求实体类型为可继承的类型，也不要求导航属性必须是虚拟的，且允许通过 `new` 创建的实体实例在附加到上下文后可进行延迟加载。 但它需要对 [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) 包中定义的 `ILazyLoader` 服务的引用。 此包包含所允许的最少的一组类型，以便将依赖此包时所产生的影响降至最低。 不过，可以将 `ILazyLoader.Load` 方法以委托的形式注入，这样就可以完全避免依赖于实体类型的任何 EF Core 包。 例如：

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

上述代码使用 `Load` 扩展方法，以便更干净地使用委托：

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]  
> 延迟加载委托的构造函数参数必须名为“lazyLoader”。 未来的一个版本中的配置将计划采用另一个名称。

## <a name="related-data-and-serialization"></a>关联数据和序列化

由于 EF Core 会自动修正导航属性，因此在对象图中可能会产生循环引用。 例如，加载博客及其关联文章会生成引用文章集合的博客对象。 而其中每篇文章又会引用该博客。

某些序列化框架不允许使用循环引用。 例如，Json.NET 在产生循环引用的情况下，会引发以下异常。

> Newtonsoft.Json.JsonSerializationException：为“MyApplication.Models.Blog”类型的“Blog”属性检测到自引用循环。

如果正在使用 ASP.NET Core，则可以将 Json.NET 配置为忽略在对象图中找到的循环引用。 这是在 `Startup.cs` 中通过 `ConfigureServices(...)` 方法实现的。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

另一种方法是使用 `[JsonIgnore]` 特性修饰其中一个导航属性，该特性指示 Json.NET 在序列化时不遍历该导航属性。
