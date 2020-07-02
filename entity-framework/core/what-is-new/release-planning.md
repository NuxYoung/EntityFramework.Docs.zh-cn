---
title: EF Core 版本规划
author: ajcvickers
ms.date: 01/28/2020
uid: core/what-is-new/release_planning
ms.openlocfilehash: df933ac2462fcc18c53f49d862836fd2d6a4dd99
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370183"
---
# <a name="release-planning-process"></a><span data-ttu-id="56530-102">版本规划过程</span><span class="sxs-lookup"><span data-stu-id="56530-102">Release planning process</span></span>

<span data-ttu-id="56530-103">我们常常会被问到如何选择将添加到特定版本的特定功能。</span><span class="sxs-lookup"><span data-stu-id="56530-103">We often get questions about how we choose specific features to go into a particular release.</span></span>
<span data-ttu-id="56530-104">该文档概述了我们采用的过程。</span><span class="sxs-lookup"><span data-stu-id="56530-104">This document outlines the process we use.</span></span>
<span data-ttu-id="56530-105">随着我们找到更好的规划方法，该过程在不断演变，但总体思路仍保持不变。</span><span class="sxs-lookup"><span data-stu-id="56530-105">The process is continually evolving as we find better ways to plan, but the general ideas remain the same.</span></span>

## <a name="different-kinds-of-releases"></a><span data-ttu-id="56530-106">不同类型的版本</span><span class="sxs-lookup"><span data-stu-id="56530-106">Different kinds of releases</span></span>

<span data-ttu-id="56530-107">不同类型的版本包含不同类型的更改。</span><span class="sxs-lookup"><span data-stu-id="56530-107">Different kinds of release contain different kinds of changes.</span></span>
<span data-ttu-id="56530-108">这反过来意味着对不同类型的版本而言，版本规划是不同的。</span><span class="sxs-lookup"><span data-stu-id="56530-108">This in turn means that the release planning is different for different kinds of release.</span></span>

### <a name="patch-releases"></a><span data-ttu-id="56530-109">修补程序版本</span><span class="sxs-lookup"><span data-stu-id="56530-109">Patch releases</span></span>

<span data-ttu-id="56530-110">修补程序版本只更改了版本的“修补程序”部分。</span><span class="sxs-lookup"><span data-stu-id="56530-110">Patch releases change only the "patch" part of the version.</span></span>
<span data-ttu-id="56530-111">例如，EF Core 3.1.1 是修补在 EF Core 3.1.0 中发现的问题的版本   。</span><span class="sxs-lookup"><span data-stu-id="56530-111">For example, EF Core 3.1.**1** is a release that patches issues found in EF Core 3.1.**0**.</span></span>

<span data-ttu-id="56530-112">修补程序版本旨在修复关键客户 bug。</span><span class="sxs-lookup"><span data-stu-id="56530-112">Patch releases are intended to fix critical customer bugs.</span></span>
<span data-ttu-id="56530-113">这意味着修补程序版本中没有新功能。</span><span class="sxs-lookup"><span data-stu-id="56530-113">This means patch releases do not include new features.</span></span>
<span data-ttu-id="56530-114">在特殊情况下，修补程序版本中不允许包含 API 更改。</span><span class="sxs-lookup"><span data-stu-id="56530-114">API changes are not allowed in patch releases except in special circumstances.</span></span>

<span data-ttu-id="56530-115">在修补程序版本中进行更改的门槛很高。</span><span class="sxs-lookup"><span data-stu-id="56530-115">The bar to make a change in a patch release is very high.</span></span>
<span data-ttu-id="56530-116">这是因为有必要保证修补程序版本不会引入新的 bug。</span><span class="sxs-lookup"><span data-stu-id="56530-116">This is because it is critical that patch releases do not introduce new bugs.</span></span>
<span data-ttu-id="56530-117">因此，决策过程着重于高价值和低风险。</span><span class="sxs-lookup"><span data-stu-id="56530-117">Therefore, the decision process emphasizes high value and low risk.</span></span>

<span data-ttu-id="56530-118">在下述情况中，我们修补问题的可能性更大：</span><span class="sxs-lookup"><span data-stu-id="56530-118">We are more likely to patch an issue if:</span></span>
  * <span data-ttu-id="56530-119">该问题会影响多个客户</span><span class="sxs-lookup"><span data-stu-id="56530-119">It is impacting multiple customers</span></span>
  * <span data-ttu-id="56530-120">该问题与上一版本相比属于性能退化</span><span class="sxs-lookup"><span data-stu-id="56530-120">It is a regression from a previous release</span></span>
  * <span data-ttu-id="56530-121">失败会导致数据损坏</span><span class="sxs-lookup"><span data-stu-id="56530-121">The failure causes data corruption</span></span>

<span data-ttu-id="56530-122">在下述情况中，我们修补问题的可能性更小：</span><span class="sxs-lookup"><span data-stu-id="56530-122">We are less likely to patch an issue if:</span></span>
  * <span data-ttu-id="56530-123">有合理的解决方法</span><span class="sxs-lookup"><span data-stu-id="56530-123">There are reasonable workarounds</span></span>
  * <span data-ttu-id="56530-124">此修补程序极可能损坏其他某些功能</span><span class="sxs-lookup"><span data-stu-id="56530-124">The fix has high risk of breaking something else</span></span>
  * <span data-ttu-id="56530-125">bug 处于极端情况</span><span class="sxs-lookup"><span data-stu-id="56530-125">The bug is in a corner-case</span></span>

<span data-ttu-id="56530-126">在[长期支持 (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) 版本的整个生存期内，这一门槛逐渐升高。</span><span class="sxs-lookup"><span data-stu-id="56530-126">This bar gradually rises through the lifetime of a [long-term support (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) release.</span></span> <span data-ttu-id="56530-127">这是因为 LTS 版本着重于稳定性。</span><span class="sxs-lookup"><span data-stu-id="56530-127">This is because LTS releases emphasize stability.</span></span>

<span data-ttu-id="56530-128">关于是否修复某问题的最终决定由 Microsoft 的 .NET 主管作出。</span><span class="sxs-lookup"><span data-stu-id="56530-128">The final decision about whether or not to patch an issue is made by the .NET Directors at Microsoft.</span></span>

### <a name="minor-releases"></a><span data-ttu-id="56530-129">次要版本</span><span class="sxs-lookup"><span data-stu-id="56530-129">Minor releases</span></span>

<span data-ttu-id="56530-130">次要版本只更改了版本的“次要”部分。</span><span class="sxs-lookup"><span data-stu-id="56530-130">Minor releases change only the "minor" part of the version.</span></span>
<span data-ttu-id="56530-131">例如，EF Core 3.1.0 是对 EF Core 3.0.0 进行改进的一个版本   。</span><span class="sxs-lookup"><span data-stu-id="56530-131">For example, EF Core 3.**1**.0 is a release that improves on EF Core 3.**0**.0.</span></span>

<span data-ttu-id="56530-132">次要版本：</span><span class="sxs-lookup"><span data-stu-id="56530-132">Minor releases:</span></span>
* <span data-ttu-id="56530-133">旨在提升上一版本的质量和功能</span><span class="sxs-lookup"><span data-stu-id="56530-133">Are intended to improve the quality and features of the previous release</span></span>
* <span data-ttu-id="56530-134">通常包含 bug 修复和新功能</span><span class="sxs-lookup"><span data-stu-id="56530-134">Typically contain bug fixes and new features</span></span>
* <span data-ttu-id="56530-135">不包含有意的中断性变更</span><span class="sxs-lookup"><span data-stu-id="56530-135">Do not include intentional breaking changes</span></span>
* <span data-ttu-id="56530-136">有一些推送给 NuGet 的预发行预览版</span><span class="sxs-lookup"><span data-stu-id="56530-136">Have a few prerelease previews pushed to NuGet</span></span>

### <a name="major-releases"></a><span data-ttu-id="56530-137">主要版本</span><span class="sxs-lookup"><span data-stu-id="56530-137">Major releases</span></span>

<span data-ttu-id="56530-138">主要版本更改的是 EF“主要”版本号。</span><span class="sxs-lookup"><span data-stu-id="56530-138">Major releases change the EF "major" version number.</span></span>
<span data-ttu-id="56530-139">例如，EF Core 3.0.0 是与 EF Core 2.2.x 相比性能大幅提升的一个主要版本  。</span><span class="sxs-lookup"><span data-stu-id="56530-139">For example, EF Core **3**.0.0 is a major release that makes a big step forward over EF Core 2.2.x.</span></span>

<span data-ttu-id="56530-140">主要版本：</span><span class="sxs-lookup"><span data-stu-id="56530-140">Major releases:</span></span>
* <span data-ttu-id="56530-141">旨在提升上一版本的质量和功能</span><span class="sxs-lookup"><span data-stu-id="56530-141">Are intended to improve the quality and features of the previous release</span></span>
* <span data-ttu-id="56530-142">通常包含 bug 修复和新功能</span><span class="sxs-lookup"><span data-stu-id="56530-142">Typically contain bug fixes and new features</span></span>
  * <span data-ttu-id="56530-143">某些新功能可能在根本上更改了 EF Core 工作的方式</span><span class="sxs-lookup"><span data-stu-id="56530-143">Some of the new features may be fundamental changes to the way EF Core works</span></span>
* <span data-ttu-id="56530-144">通常包含有意的中断性变更</span><span class="sxs-lookup"><span data-stu-id="56530-144">Typically include intentional breaking changes</span></span>
  * <span data-ttu-id="56530-145">就我们知道的而言，中断性变更是 EF Core 演变的必要部分</span><span class="sxs-lookup"><span data-stu-id="56530-145">Breaking changes are necessary part of evolving EF Core as we learn</span></span>
  * <span data-ttu-id="56530-146">但是，由于可能对客户造成影响，我们在进行任何中断性变更方面考虑谨慎。</span><span class="sxs-lookup"><span data-stu-id="56530-146">However, we think very carefully about making any breaking change because of the potential customer impact.</span></span> <span data-ttu-id="56530-147">过程，我们在中断性变更方面过于激进。</span><span class="sxs-lookup"><span data-stu-id="56530-147">We may have been too aggressive with breaking changes in the past.</span></span> <span data-ttu-id="56530-148">而今后，我们将努力最大程度减少会使应用程序中断的更改，同时努力减少会使数据库提供程序和扩展中断的更改。</span><span class="sxs-lookup"><span data-stu-id="56530-148">Going forward, we will strive to minimize changes that break applications, and to reduce changes that break database providers and extensions.</span></span>
* <span data-ttu-id="56530-149">有很多推送给 NuGet 的预发行预览版</span><span class="sxs-lookup"><span data-stu-id="56530-149">Have many prerelease previews pushed to NuGet</span></span>

## <a name="planning-for-majorminor-releases"></a><span data-ttu-id="56530-150">主要/次要版本规划</span><span class="sxs-lookup"><span data-stu-id="56530-150">Planning for major/minor releases</span></span>

### <a name="github-issue-tracking"></a><span data-ttu-id="56530-151">GitHub 问题跟踪</span><span class="sxs-lookup"><span data-stu-id="56530-151">GitHub issue tracking</span></span>

<span data-ttu-id="56530-152">对于所有 EF Core 规划来说，GitHub ([https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)) 都是可靠来源。</span><span class="sxs-lookup"><span data-stu-id="56530-152">GitHub ([https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)) is the source-of-truth for all EF Core planning.</span></span>

<span data-ttu-id="56530-153">GitHub 上的问题具有：</span><span class="sxs-lookup"><span data-stu-id="56530-153">Issues on GitHub have:</span></span>

* <span data-ttu-id="56530-154">状态</span><span class="sxs-lookup"><span data-stu-id="56530-154">A state</span></span>
  * <span data-ttu-id="56530-155">[Open](https://github.com/dotnet/efcore/issues) 问题尚未得到解决。</span><span class="sxs-lookup"><span data-stu-id="56530-155">[Open](https://github.com/dotnet/efcore/issues) issues have not been addressed.</span></span>
  * <span data-ttu-id="56530-156">[Closed](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aclosed) 问题已得到解决。</span><span class="sxs-lookup"><span data-stu-id="56530-156">[Closed](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aclosed) issues have been addressed.</span></span>
    * <span data-ttu-id="56530-157">所有已解决的问题都[标记了 closed-fixed](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Aclosed-fixed+is%3Aclosed)。</span><span class="sxs-lookup"><span data-stu-id="56530-157">All issues that have been fixed are [tagged with closed-fixed](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Aclosed-fixed+is%3Aclosed).</span></span> <span data-ttu-id="56530-158">标记有 closed-fixed 的问题已得到修复且已合并，但可能尚未发布。</span><span class="sxs-lookup"><span data-stu-id="56530-158">An issue tagged with closed-fixed is fixed and merged, but may not have been released.</span></span>
    * <span data-ttu-id="56530-159">其他 `closed-` 标签指出了关闭问题的其他原因。</span><span class="sxs-lookup"><span data-stu-id="56530-159">Other `closed-` labels indicate other reasons for closing an issue.</span></span> <span data-ttu-id="56530-160">例如，重复问题标记有 closed-duplicate。</span><span class="sxs-lookup"><span data-stu-id="56530-160">For example, duplicates are tagged with closed-duplicate.</span></span>
* <span data-ttu-id="56530-161">类型</span><span class="sxs-lookup"><span data-stu-id="56530-161">A type</span></span>
  * <span data-ttu-id="56530-162">[Bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-bug) 表示错误。</span><span class="sxs-lookup"><span data-stu-id="56530-162">[Bugs](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-bug) represent bugs.</span></span>
  * <span data-ttu-id="56530-163">[增强功能](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-enhancement)表示新功能或现有功能中的更优功能。</span><span class="sxs-lookup"><span data-stu-id="56530-163">[Enhancements](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-enhancement) represent new features or better functionality in existing features.</span></span>
* <span data-ttu-id="56530-164">里程碑</span><span class="sxs-lookup"><span data-stu-id="56530-164">A milestone</span></span>
  * <span data-ttu-id="56530-165">[没有里程碑的问题](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+no%3Amilestone)由团队进行处理。</span><span class="sxs-lookup"><span data-stu-id="56530-165">[Issues with no milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+no%3Amilestone) are being considered by the team.</span></span> <span data-ttu-id="56530-166">尚未作出有关如何处理问题的决定，或者对决定的某项更改正在探讨中。</span><span class="sxs-lookup"><span data-stu-id="56530-166">The decision on what to do with the issue has not yet been made or a change in the decision is being considered.</span></span>
  * <span data-ttu-id="56530-167">[积压工作 (backlog) 里程碑中的问题](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog)是指 EF 团队将考虑在某个未来版本中处理的项目</span><span class="sxs-lookup"><span data-stu-id="56530-167">[Issues in the Backlog milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog) are items that the EF team will consider working on in a future release</span></span>
    * <span data-ttu-id="56530-168">积压工作 (backlog) 中的问题可能[标记有 consider-for-next-release](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Aconsider-for-next-release)，这是指该工作项在下一版本列表中的排名靠前。</span><span class="sxs-lookup"><span data-stu-id="56530-168">Issues in the backlog may be [tagged with consider-for-next-release](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Aconsider-for-next-release) indicating that this work item is high on the list for the next release.</span></span>
  * <span data-ttu-id="56530-169">经版本控制的里程碑中的待解决问题是指团队计划在该版本中处理的项目。</span><span class="sxs-lookup"><span data-stu-id="56530-169">Open issues in a versioned milestone are items that the team plans to work on in that version.</span></span> <span data-ttu-id="56530-170">例如，[这些是我们计划针对 EF Core 5.0 进行处理的问题](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A5.0.0)。</span><span class="sxs-lookup"><span data-stu-id="56530-170">For example, [these are the issues we plan to work on for EF Core 5.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A5.0.0).</span></span>
  * <span data-ttu-id="56530-171">经版本控制的里程碑中的已关闭问题是指对该版本而言已完成的问题。</span><span class="sxs-lookup"><span data-stu-id="56530-171">Closed issues in a versioned milestone are issues that are completed for that version.</span></span> <span data-ttu-id="56530-172">请注意，该版本可能尚未发布。</span><span class="sxs-lookup"><span data-stu-id="56530-172">Note that the version may not yet have been released.</span></span> <span data-ttu-id="56530-173">例如，[这些是我们计划针对 EF Core 3.0 已完成的问题](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A3.0.0+is%3Aclosed)。</span><span class="sxs-lookup"><span data-stu-id="56530-173">For example, [these are the issues completed for EF Core 3.0](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A3.0.0+is%3Aclosed).</span></span>
* <span data-ttu-id="56530-174">请投票！</span><span class="sxs-lookup"><span data-stu-id="56530-174">Votes!</span></span>
  * <span data-ttu-id="56530-175">投票是指出某个问题对你而言很重要的最佳方式。</span><span class="sxs-lookup"><span data-stu-id="56530-175">Voting is the best way to indicate that an issue is important to you.</span></span>
  * <span data-ttu-id="56530-176">只需向问题添加“大拇指朝上”👍 即可进行投票。</span><span class="sxs-lookup"><span data-stu-id="56530-176">To vote, just add a "thumbs-up" 👍 to the issue.</span></span> <span data-ttu-id="56530-177">例如，[这些是得票数靠前的问题](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)</span><span class="sxs-lookup"><span data-stu-id="56530-177">For example, [these are the top-voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)</span></span>
  * <span data-ttu-id="56530-178">如果你认为添加评论会提高重要性，还请添加描述你为何需要该功能的特定原因。</span><span class="sxs-lookup"><span data-stu-id="56530-178">Please also comment describing specific reasons you need the feature if you feel this adds value.</span></span> <span data-ttu-id="56530-179">评论“+1”（即赞同）或给出类似评论不会提高重要性。</span><span class="sxs-lookup"><span data-stu-id="56530-179">Commenting "+1" or similar does not add value.</span></span>

### <a name="the-planning-process"></a><span data-ttu-id="56530-180">规划过程</span><span class="sxs-lookup"><span data-stu-id="56530-180">The planning process</span></span>

<span data-ttu-id="56530-181">与从积压工作 (backlog) 中提取被请求最多次的请求而言，规划过程的参与度更高。</span><span class="sxs-lookup"><span data-stu-id="56530-181">The planning process is more involved than just taking the top-most requested features from the backlog.</span></span>
<span data-ttu-id="56530-182">这是因为我们会用多种方式从多名利益干系人那里收集反馈。</span><span class="sxs-lookup"><span data-stu-id="56530-182">This is because we gather feedback from multiple stakeholders in multiple ways.</span></span>
<span data-ttu-id="56530-183">然后，我们会根据下列内容调整版本：</span><span class="sxs-lookup"><span data-stu-id="56530-183">We then shape a release based on:</span></span>

* <span data-ttu-id="56530-184">客户输入的内容</span><span class="sxs-lookup"><span data-stu-id="56530-184">Input from customers</span></span>
* <span data-ttu-id="56530-185">其他利益干系人输入的内容</span><span class="sxs-lookup"><span data-stu-id="56530-185">Input from other stakeholders</span></span>
* <span data-ttu-id="56530-186">战略方向</span><span class="sxs-lookup"><span data-stu-id="56530-186">Strategic direction</span></span>
* <span data-ttu-id="56530-187">可用资源</span><span class="sxs-lookup"><span data-stu-id="56530-187">Resources available</span></span>
* <span data-ttu-id="56530-188">计划</span><span class="sxs-lookup"><span data-stu-id="56530-188">Schedule</span></span>

<span data-ttu-id="56530-189">我们提出的一些问题是：</span><span class="sxs-lookup"><span data-stu-id="56530-189">Some of the questions we ask are:</span></span>

1. <span data-ttu-id="56530-190">**我们认为有多少开发人员会使用该功能？该功能会使他们的应用程序/体验有多大的改善？**</span><span class="sxs-lookup"><span data-stu-id="56530-190">**How many developers we think will use the feature and how much better will it make their applications or experience?**</span></span> <span data-ttu-id="56530-191">为了回答这个问题，我们收集众多来源（其中包括对问题的评论和投票）的反馈。</span><span class="sxs-lookup"><span data-stu-id="56530-191">To answer this question, we collect feedback from many sources — Comments and votes on issues is one of those sources.</span></span> <span data-ttu-id="56530-192">另一方面是与重要客户的具体互动。</span><span class="sxs-lookup"><span data-stu-id="56530-192">Specific engagements with important customers is another.</span></span>

2. <span data-ttu-id="56530-193">**在尚未实现此功能的情况下，用户可用的变通方法是什么？**</span><span class="sxs-lookup"><span data-stu-id="56530-193">**What are the workarounds people can use if we don't implement this feature yet?**</span></span> <span data-ttu-id="56530-194">例如，许多开发人员可以映射联接表，以解决缺少本机多对多支持的问题。</span><span class="sxs-lookup"><span data-stu-id="56530-194">For example, many developers can map a join table to work around lack of native many-to-many support.</span></span> <span data-ttu-id="56530-195">显然，并非所有开发人员都希望这样做，但很多开发人员都可以这样做，而这也作为决策的一个考虑因素。</span><span class="sxs-lookup"><span data-stu-id="56530-195">Obviously, not all developers want to do it, but many can, and that counts as a factor in our decision.</span></span>

3. <span data-ttu-id="56530-196">**实现此功能是否有助于 EF Core 的体系结构发展，从而帮助我们实现其他功能？**</span><span class="sxs-lookup"><span data-stu-id="56530-196">**Does implementing this feature evolve the architecture of EF Core such that it moves us closer to implementing other features?**</span></span> <span data-ttu-id="56530-197">我们往往偏爱充当其他功能的构建基块的功能。</span><span class="sxs-lookup"><span data-stu-id="56530-197">We tend to favor features that act as building blocks for other features.</span></span> <span data-ttu-id="56530-198">例如，属性包实体可有助于提供多对多支持，而且实体构造函数也启用了延迟加载支持。</span><span class="sxs-lookup"><span data-stu-id="56530-198">For instance, property bag entities can help us move towards many-to-many support, and entity constructors enabled our lazy loading support.</span></span>

4. <span data-ttu-id="56530-199">**功能是可扩展性点吗？**</span><span class="sxs-lookup"><span data-stu-id="56530-199">**Is the feature an extensibility point?**</span></span> <span data-ttu-id="56530-200">我们往往偏爱扩展点（而不是常规功能），因为它们能让开发人员挂钩自己的行为，并补偿缺少的任何功能。</span><span class="sxs-lookup"><span data-stu-id="56530-200">We tend to favor extensibility points over normal features because they enable developers to hook their own behaviors and compensate for any missing functionality.</span></span>

5. <span data-ttu-id="56530-201">**该功能与其他产品结合使用时的增效作用如何？**</span><span class="sxs-lookup"><span data-stu-id="56530-201">**What is the synergy of the feature when used in combination with other products?**</span></span> <span data-ttu-id="56530-202">我们往往偏爱能够实现或显著改善结合使用 EF Core 与其他产品（如 .NET Core、最新版 Visual Studio、Microsoft Azure 等）的体验的功能。</span><span class="sxs-lookup"><span data-stu-id="56530-202">We favor features that enable or significantly improve the experience of using EF Core with other products, such as .NET Core, the latest version of Visual Studio, Microsoft Azure, and so on.</span></span>

6. <span data-ttu-id="56530-203">**从事功能开发工作的人员的技能如何？如何能最充分地利用这些资源？**</span><span class="sxs-lookup"><span data-stu-id="56530-203">**What are the skills of the people available to work on a feature, and how can we best leverage these resources?**</span></span> <span data-ttu-id="56530-204">EF 团队的每个成员以及我们的社区参与者都拥有各个领域的不同程度经验，我们需要相应地进行计划。</span><span class="sxs-lookup"><span data-stu-id="56530-204">Each member of the EF team and our community contributors has different levels of experience in different areas, so we have to plan accordingly.</span></span> <span data-ttu-id="56530-205">即便我们希望“全员就位”开发特定功能（如 GroupBy 转换或多对多支持），这也是不切实际的。</span><span class="sxs-lookup"><span data-stu-id="56530-205">Even if we wanted to have "all hands on deck" to work on a specific feature like GroupBy translations, or many-to-many, that wouldn't be practical.</span></span>
