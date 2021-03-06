---
title: 相关数据的显式加载 - EF Core
description: 使用 Entity Framework Core 显式加载相关数据
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: 4cfc11237b498f5357476ee4ad96fdc279cd3fee
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078863"
---
# <a name="explicit-loading-of-related-data"></a>相关数据的显式加载

## <a name="explicit-loading"></a>显式加载

可以通过 `DbContext.Entry(...)` API 显式加载导航属性。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

还可以通过执行返回关联实体的单独查询来显式加载导航属性。 如果已启用更改跟踪，则在查询具体化实体时，EF Core 将自动设置新加载的实体的导航属性以引用任何已加载的实体，并设置已加载实体的导航属性以引用新加载的实体。

## <a name="querying-related-entities"></a>查询相关实体

还可以获得表示导航属性内容的 LINQ 查询。

这使你可对查询应用其他运算符。 例如，无需将关联实体加载到内存中，即可对关联实体执行聚合运算。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

还可以筛选要加载到内存中的关联实体。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]
