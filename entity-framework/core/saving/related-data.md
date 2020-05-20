---
title: 保存相关数据 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 07b6680f-ffcf-412c-9857-f997486b386c
uid: core/saving/related-data
ms.openlocfilehash: 86d32b6172ee21c12a15e9ed4bb0142afc99c8bd
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413611"
---
# <a name="saving-related-data"></a><span data-ttu-id="0502b-102">保存相关数据</span><span class="sxs-lookup"><span data-stu-id="0502b-102">Saving Related Data</span></span>

<span data-ttu-id="0502b-103">除了独立实体以外，还可以使用模型中定义的关系。</span><span class="sxs-lookup"><span data-stu-id="0502b-103">In addition to isolated entities, you can also make use of the relationships defined in your model.</span></span>

> [!TIP]  
> <span data-ttu-id="0502b-104">可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/RelatedData/)。</span><span class="sxs-lookup"><span data-stu-id="0502b-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/RelatedData/) on GitHub.</span></span>

## <a name="adding-a-graph-of-new-entities"></a><span data-ttu-id="0502b-105">添加新实体的关系图</span><span class="sxs-lookup"><span data-stu-id="0502b-105">Adding a graph of new entities</span></span>

<span data-ttu-id="0502b-106">如果创建多个新的相关实体，则将其中一个添加到上下文时也会添加其他实体。</span><span class="sxs-lookup"><span data-stu-id="0502b-106">If you create several new related entities, adding one of them to the context will cause the others to be added too.</span></span>

<span data-ttu-id="0502b-107">在下面的示例中，博客和三篇相关文章将全部被插入数据库中。</span><span class="sxs-lookup"><span data-stu-id="0502b-107">In the following example, the blog and three related posts are all inserted into the database.</span></span> <span data-ttu-id="0502b-108">由于可通过 `Blog.Posts` 导航属性访问这些文章，因此可发现并添加它们。</span><span class="sxs-lookup"><span data-stu-id="0502b-108">The posts are found and added, because they are reachable via the `Blog.Posts` navigation property.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

> [!TIP]  
> <span data-ttu-id="0502b-109">使用 EntityEntry.State 属性仅设置单个实体的状态。</span><span class="sxs-lookup"><span data-stu-id="0502b-109">Use the EntityEntry.State property to set the state of just a single entity.</span></span> <span data-ttu-id="0502b-110">例如，`context.Entry(blog).State = EntityState.Modified` 。</span><span class="sxs-lookup"><span data-stu-id="0502b-110">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="adding-a-related-entity"></a><span data-ttu-id="0502b-111">添加相关实体</span><span class="sxs-lookup"><span data-stu-id="0502b-111">Adding a related entity</span></span>

<span data-ttu-id="0502b-112">如果从已由上下文跟踪的实体的导航属性中引用新实体，则将发现该实体并将其插入到数据库中。</span><span class="sxs-lookup"><span data-stu-id="0502b-112">If you reference a new entity from the navigation property of an entity that is already tracked by the context, the entity will be discovered and inserted into the database.</span></span>

<span data-ttu-id="0502b-113">在下面的示例中，插入 `post` 实体，因为该实体会添加到已从数据库中提取的 `blog` 实体的 `Posts` 属性。</span><span class="sxs-lookup"><span data-stu-id="0502b-113">In the following example, the `post` entity is inserted because it is added to the `Posts` property of the `blog` entity which was fetched from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a><span data-ttu-id="0502b-114">更改关系</span><span class="sxs-lookup"><span data-stu-id="0502b-114">Changing relationships</span></span>

<span data-ttu-id="0502b-115">如果更改实体的导航属性，则将对数据库中的外键列进行相应的更改。</span><span class="sxs-lookup"><span data-stu-id="0502b-115">If you change the navigation property of an entity, the corresponding changes will be made to the foreign key column in the database.</span></span>

<span data-ttu-id="0502b-116">在下面的示例中，`post` 实体更新为属于新的 `blog` 实体，因为其 `Blog` 导航属性设置为指向 `blog`。</span><span class="sxs-lookup"><span data-stu-id="0502b-116">In the following example, the `post` entity is updated to belong to the new `blog` entity because its `Blog` navigation property is set to point to `blog`.</span></span> <span data-ttu-id="0502b-117">请注意，`blog` 也会插入到数据库中，因为它是已由上下文跟踪的实体 (`post`) 的导航属性引用的新实体。</span><span class="sxs-lookup"><span data-stu-id="0502b-117">Note that `blog` will also be inserted into the database because it is a new entity that is referenced by the navigation property of an entity that is already tracked by the context (`post`).</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a><span data-ttu-id="0502b-118">删除关系</span><span class="sxs-lookup"><span data-stu-id="0502b-118">Removing relationships</span></span>

<span data-ttu-id="0502b-119">可以通过将引用导航设置为 `null` 或从集合导航中删除相关实体来删除关系。</span><span class="sxs-lookup"><span data-stu-id="0502b-119">You can remove a relationship by setting a reference navigation to `null`, or removing the related entity from a collection navigation.</span></span>

<span data-ttu-id="0502b-120">根据关系中配置的级联删除行为，删除关系可能会对依赖实体产生副作用。</span><span class="sxs-lookup"><span data-stu-id="0502b-120">Removing a relationship can have side effects on the dependent entity, according to the cascade delete behavior configured in the relationship.</span></span>

<span data-ttu-id="0502b-121">默认情况下，对于必选关系，将配置级联删除行为，并将从数据库中删除子实体/依赖实体。</span><span class="sxs-lookup"><span data-stu-id="0502b-121">By default, for required relationships, a cascade delete behavior is configured and the child/dependent entity will be deleted from the database.</span></span> <span data-ttu-id="0502b-122">对于可选关系，默认情况下不会配置级联删除，但会将外键属性设置为 null。</span><span class="sxs-lookup"><span data-stu-id="0502b-122">For optional relationships, cascade delete is not configured by default, but the foreign key property will be set to null.</span></span>

<span data-ttu-id="0502b-123">要了解有关如何配置关系必要性的信息，请参阅[必选关系和可选关系](../modeling/relationships.md#required-and-optional-relationships)。</span><span class="sxs-lookup"><span data-stu-id="0502b-123">See [Required and Optional Relationships](../modeling/relationships.md#required-and-optional-relationships) to learn about how the requiredness of relationships can be configured.</span></span>

<span data-ttu-id="0502b-124">有关级联删除行为的工作原理、如何显式配置这些行为以及如何按照约定选择这些行为的详细信息，请参阅[级联删除](cascade-delete.md)。</span><span class="sxs-lookup"><span data-stu-id="0502b-124">See [Cascade Delete](cascade-delete.md) for more details on how cascade delete behaviors work, how they can be configured explicitly and  how they are selected by convention.</span></span>

<span data-ttu-id="0502b-125">在下面的示例中，对 `Blog` 和 `Post` 之间的关系配置了级联删除，因此将从数据库中删除 `post` 实体。</span><span class="sxs-lookup"><span data-stu-id="0502b-125">In the following example, a cascade delete is configured on the relationship between `Blog` and `Post`, so the `post` entity is deleted from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#RemovingRelationships)]
