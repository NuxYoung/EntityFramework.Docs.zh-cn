---
title: 实体类型-EF Core
description: 如何使用 Entity Framework Core 配置和映射实体类型
author: roji
ms.date: 12/03/2019
uid: core/modeling/entity-types
ms.openlocfilehash: fead7f9e37efb7f674f429acbfd16c2ca78480d4
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071506"
---
# <a name="entity-types"></a>实体类型

在上下文中包含一种类型的 DbSet，这意味着它包含在 EF Core 的模型中;我们通常将此类类型称为 *实体*。 EF Core 可以从/向数据库中读取和写入实体实例，如果使用的是关系数据库，EF Core 可以通过迁移为实体创建表。

## <a name="including-types-in-the-model"></a>在模型中包含类型

按照约定，在上下文中的 DbSet 属性中公开的类型作为实体包含在模型中。 还包括方法中指定的实体类型 `OnModelCreating` ，就像通过递归方式浏览其他发现的实体类型的导航属性找到的任何类型一样。

在下面的代码示例中，包含了所有类型：

* `Blog` 包含，因为它在上下文的 DbSet 属性中公开。
* `Post` 包含，因为它通过 `Blog.Posts` 导航属性发现。
* `AuditEntry` 因为它是在中指定的 `OnModelCreating` 。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a>从模型中排除类型

如果您不希望在模型中包含某一类型，则可以排除它：

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

## <a name="table-name"></a>表名称

按照约定，每个实体类型将设置为映射到与公开实体的 DbSet 属性同名的数据库表。 如果给定实体不存在 DbSet，则使用类名称。

可以手动配置表名：

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a>表架构

使用关系数据库时，表按约定在数据库的默认架构中创建。 例如，Microsoft SQL Server 将使用 `dbo` 架构 (SQLite 不支持) 的架构。

你可以配置要在特定架构中创建的表，如下所示：

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

您还可以在模型级别定义 Fluent API 的默认架构，而不是为每个表指定架构：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

请注意，设置默认架构也会影响其他数据库对象，如序列。
