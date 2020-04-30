---
title: 编写数据库提供程序-EF Core
author: anmiller
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
uid: core/providers/writing-a-provider
ms.openlocfilehash: 662c7e386bbdf0ff1e4e5051349d6a5c56a3df7b
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538462"
---
# <a name="writing-a-database-provider"></a><span data-ttu-id="0a818-102">编写数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="0a818-102">Writing a Database Provider</span></span>

<span data-ttu-id="0a818-103">有关编写 Entity Framework Core 数据库提供程序的信息，请参阅，希望通过[Arthur Vickers](https://github.com/ajcvickers)[编写 EF Core 提供程序](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)。</span><span class="sxs-lookup"><span data-stu-id="0a818-103">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

> [!NOTE]
> <span data-ttu-id="0a818-104">自 EF Core 1.1 起，这些文章尚未更新，因此该时间以来发生了重大更改。</span><span class="sxs-lookup"><span data-stu-id="0a818-104">These posts have not been updated since EF Core 1.1 and there have been significant changes since that time.</span></span>  
<span data-ttu-id="0a818-105">[问题 681](https://github.com/dotnet/EntityFramework.Docs/issues/681)正在跟踪对此文档的更新。</span><span class="sxs-lookup"><span data-stu-id="0a818-105">[Issue 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) is tracking updates to this documentation.</span></span>

<span data-ttu-id="0a818-106">EF Core 基本代码是开放源代码，并且包含多个可用作参考的数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="0a818-106">The EF Core codebase is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="0a818-107">可以在<https://github.com/aspnet/EntityFrameworkCore>中找到源代码。</span><span class="sxs-lookup"><span data-stu-id="0a818-107">You can find the source code at <https://github.com/aspnet/EntityFrameworkCore>.</span></span> <span data-ttu-id="0a818-108">查看常用的第三方提供程序（例如[Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、 [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)和[SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)）的代码可能也很有帮助。</span><span class="sxs-lookup"><span data-stu-id="0a818-108">It may also be helpful to look at the code for commonly used third-party providers, such as [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span></span> <span data-ttu-id="0a818-109">特别是，设置这些项目是为了从和运行我们在 NuGet 上发布的功能测试。</span><span class="sxs-lookup"><span data-stu-id="0a818-109">In particular, these projects are setup to extend from and run functional tests that we publish on NuGet.</span></span> <span data-ttu-id="0a818-110">强烈建议使用这种设置。</span><span class="sxs-lookup"><span data-stu-id="0a818-110">This kind of setup is strongly recommended.</span></span>

## <a name="keeping-up-to-date-with-provider-changes"></a><span data-ttu-id="0a818-111">提供提供程序更改的最新状态</span><span class="sxs-lookup"><span data-stu-id="0a818-111">Keeping up-to-date with provider changes</span></span>

<span data-ttu-id="0a818-112">从2.1 版本开始，开始使用后，我们创建了一个可能需要对提供程序代码进行相应更改的[更改日志](provider-log.md)。</span><span class="sxs-lookup"><span data-stu-id="0a818-112">Starting with work after the 2.1 release, we have created a [log of changes](provider-log.md) that may need corresponding changes in provider code.</span></span> <span data-ttu-id="0a818-113">这旨在帮助更新现有提供程序以使用 EF Core 的新版本。</span><span class="sxs-lookup"><span data-stu-id="0a818-113">This is intended to help when updating an existing provider to work with a new version of EF Core.</span></span>

<span data-ttu-id="0a818-114">在2.1 之前，我们使用了[`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)和[`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)标签作为 GitHub 问题，并提供了类似用途的拉取请求。</span><span class="sxs-lookup"><span data-stu-id="0a818-114">Prior to 2.1, we used the [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) and [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) labels on our GitHub issues and pull requests for a similar purpose.</span></span> <span data-ttu-id="0a818-115">我们将 continiue 将这些标签用于问题，指出给定版本中的哪些工作项可能还需要在提供程序中完成工作。</span><span class="sxs-lookup"><span data-stu-id="0a818-115">We will continiue to use these lables on issues to give an indication which work items in a given release may also require work to be done in providers.</span></span> <span data-ttu-id="0a818-116">`providers-beware`标签通常表示工作项的实现可能会中断提供程序，而`providers-fyi`标签通常意味着提供程序将不会中断，但可能仍需要更改代码，例如，启用新功能。</span><span class="sxs-lookup"><span data-stu-id="0a818-116">A `providers-beware` label typically means that the implementation of an work item may break providers, while a `providers-fyi` label typically means that providers will not be broken, but code may need to be changed anyway, for example, to enable new functionality.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="0a818-117">建议的第三方提供程序命名</span><span class="sxs-lookup"><span data-stu-id="0a818-117">Suggested naming of third party providers</span></span>

<span data-ttu-id="0a818-118">建议为 NuGet 包使用以下命名。</span><span class="sxs-lookup"><span data-stu-id="0a818-118">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="0a818-119">这与 EF Core 团队传递的包的名称一致。</span><span class="sxs-lookup"><span data-stu-id="0a818-119">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="0a818-120">例如：</span><span class="sxs-lookup"><span data-stu-id="0a818-120">For example:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
