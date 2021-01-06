---
title: 第 2 部分，ASP.NET Core 中的 Razor 页面和 EF Core - CRUD
author: rick-anderson
description: Razor 页面和实体框架教程系列第 2 部分。
ms.author: riande
ms.date: 07/22/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/crud
ms.openlocfilehash: 4a48fb094888d51aa6f881c82e4f20ffbc84c8e2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "96901166"
---
# <a name="part-2-no-locrazor-pages-with-ef-core-in-aspnet-core---crud"></a><span data-ttu-id="4e6bf-103">第 2 部分，ASP.NET Core 中的 Razor 页面和 EF Core - CRUD</span><span class="sxs-lookup"><span data-stu-id="4e6bf-103">Part 2, Razor Pages with EF Core in ASP.NET Core - CRUD</span></span>

<span data-ttu-id="4e6bf-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4e6bf-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4e6bf-105">本教程将介绍和自定义已搭建基架的 CRUD （创建、读取、更新、删除）代码。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-105">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

## <a name="no-repository"></a><span data-ttu-id="4e6bf-106">无存储库</span><span class="sxs-lookup"><span data-stu-id="4e6bf-106">No repository</span></span>

<span data-ttu-id="4e6bf-107">某些开发人员使用服务层或存储库模式在 UI (Razor Pages) 和数据访问层之间创建抽象层。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-107">Some developers use a service layer or repository pattern to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span> <span data-ttu-id="4e6bf-108">本教程不会这样做。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-108">This tutorial doesn't do that.</span></span> <span data-ttu-id="4e6bf-109">为最大程度降低复杂性并让本教程重点介绍 EF Core，将直接在页面模型类中添加 EF Core 代码。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-109">To minimize complexity and keep the tutorial focused on EF Core, EF Core code is added directly to the page model classes.</span></span> 

## <a name="update-the-details-page"></a><span data-ttu-id="4e6bf-110">更新“详细信息”页</span><span class="sxs-lookup"><span data-stu-id="4e6bf-110">Update the Details page</span></span>

<span data-ttu-id="4e6bf-111">“学生”页的基架代码不包括注册数据。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-111">The scaffolded code for the Students pages doesn't include enrollment data.</span></span> <span data-ttu-id="4e6bf-112">本部分将向“详细信息”页添加注册。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-112">In this section, you add enrollments to the Details page.</span></span>

### <a name="read-enrollments"></a><span data-ttu-id="4e6bf-113">读取注册</span><span class="sxs-lookup"><span data-stu-id="4e6bf-113">Read enrollments</span></span>

<span data-ttu-id="4e6bf-114">为了在页面上显示学生的注册数据，你需要读取这些数据。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-114">To display a student's enrollment data on the page, you need to read it.</span></span> <span data-ttu-id="4e6bf-115">Pages/Students/Details.cshtml.cs 中的基架代码仅读取学生数据，但不读取注册数据：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-115">The scaffolded code in *Pages/Students/Details.cshtml.cs* reads only the Student data, without the Enrollment data:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

<span data-ttu-id="4e6bf-116">使用以下代码替换 `OnGetAsync` 方法以读取所选学生的注册数据。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-116">Replace the `OnGetAsync` method with the following code to read enrollment data for the selected student.</span></span> <span data-ttu-id="4e6bf-117">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-117">The changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

<span data-ttu-id="4e6bf-118">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 和 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 方法使上下文加载 `Student.Enrollments` 导航属性，并在每个注册中加载 `Enrollment.Course` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-118">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="4e6bf-119">这些方法将在[与数据读取相关](xref:data/ef-rp/read-related-data)的教程中进行详细介绍。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-119">These methods are examined in detail in the [Read related data](xref:data/ef-rp/read-related-data) tutorial.</span></span>

<span data-ttu-id="4e6bf-120">对于返回的实体未在当前上下文中更新的情况，[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 方法将会提升性能。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-120">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios where the entities returned are not updated in the current context.</span></span> <span data-ttu-id="4e6bf-121">`AsNoTracking` 将在本教程的后续部分中讨论。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-121">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-enrollments"></a><span data-ttu-id="4e6bf-122">显示注册</span><span class="sxs-lookup"><span data-stu-id="4e6bf-122">Display enrollments</span></span>

<span data-ttu-id="4e6bf-123">使用以下代码替换 *Pages/Students/Details.cshtml* 中的代码以显示注册列表。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-123">Replace the code in *Pages/Students/Details.cshtml* with the following code to display a list of enrollments.</span></span> <span data-ttu-id="4e6bf-124">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-124">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="4e6bf-125">上面的代码循环通过 `Enrollments` 导航属性中的实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-125">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="4e6bf-126">它将针对每个注册显示课程标题和成绩。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-126">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="4e6bf-127">课程标题从 Course 实体中检索，该实体存储在 Enrollments 实体的 `Course` 导航属性中。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-127">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="4e6bf-128">运行应用，选择“学生”选项卡，然后单击学生的“详细信息”链接 。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-128">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="4e6bf-129">随即显示出所选学生的课程和成绩列表。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-129">The list of courses and grades for the selected student is displayed.</span></span>

### <a name="ways-to-read-one-entity"></a><span data-ttu-id="4e6bf-130">读取一个实体的方法</span><span class="sxs-lookup"><span data-stu-id="4e6bf-130">Ways to read one entity</span></span>

<span data-ttu-id="4e6bf-131">生成的代码使用 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) 读取一个实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-131">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) to read one entity.</span></span> <span data-ttu-id="4e6bf-132">如果未找到任何内容，则此方法返回 NULL；否则，它将返回满足查询筛选条件的第一行。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-132">This method returns null if nothing is found; otherwise, it returns the first row found that satisfies the query filter criteria.</span></span> <span data-ttu-id="4e6bf-133">`FirstOrDefaultAsync` 通常是比以下备选方案更好的选择：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-133">`FirstOrDefaultAsync` is generally a better choice than the following alternatives:</span></span>

* <span data-ttu-id="4e6bf-134">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - 如果有多个满足查询筛选器的实体，则引发异常。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-134">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - Throws an exception if there's more than one entity that satisfies the query filter.</span></span> <span data-ttu-id="4e6bf-135">若要确定查询是否可以返回多行，`SingleOrDefaultAsync` 会尝试提取多个行。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-135">To determine if more than one row could be returned by the query, `SingleOrDefaultAsync` tries to fetch multiple rows.</span></span> <span data-ttu-id="4e6bf-136">如果查询只能返回一个实体，就像它在搜索唯一键时一样，那么该额外工作是不必要的。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-136">This extra work is unnecessary if the query can only return one entity, as when it searches on a unique key.</span></span>
* <span data-ttu-id="4e6bf-137">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - 查找具有主键 ( PK) 的实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-137">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="4e6bf-138">如果具有 PK 的实体正在由上下文跟踪，会返回该实体且不向数据库发出请求。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-138">If an entity with the PK is being tracked by the context, it's returned without a request to the database.</span></span> <span data-ttu-id="4e6bf-139">此方法经过优化，可查找单个实体，但无法通过 `FindAsync` 调用 `Include`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-139">This method is optimized to look up a single entity, but you can't call `Include` with `FindAsync`.</span></span>  <span data-ttu-id="4e6bf-140">如果需要相关数据，`FirstOrDefaultAsync` 则是更好的选择。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-140">So if related data is needed, `FirstOrDefaultAsync` is the better choice.</span></span>

### <a name="route-data-vs-query-string"></a><span data-ttu-id="4e6bf-141">路由数据与查询字符串</span><span class="sxs-lookup"><span data-stu-id="4e6bf-141">Route data vs. query string</span></span>

<span data-ttu-id="4e6bf-142">“详细信息”页的 URL 是 `https://localhost:<port>/Students/Details?id=1`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-142">The URL for the Details page is `https://localhost:<port>/Students/Details?id=1`.</span></span> <span data-ttu-id="4e6bf-143">实体的主键值在查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-143">The entity's primary key value is in the query string.</span></span> <span data-ttu-id="4e6bf-144">某些开发人员偏向于在路由数据中传递键值：`https://localhost:<port>/Students/Details/1`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-144">Some developers prefer to pass the key value in route data: `https://localhost:<port>/Students/Details/1`.</span></span> <span data-ttu-id="4e6bf-145">有关详细信息，请参阅[更新生成的代码](xref:tutorials/razor-pages/da1#update-the-generated-code)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-145">For more information, see [Update the generated code](xref:tutorials/razor-pages/da1#update-the-generated-code).</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="4e6bf-146">更新“创建”页</span><span class="sxs-lookup"><span data-stu-id="4e6bf-146">Update the Create page</span></span>

<span data-ttu-id="4e6bf-147">“创建”页面的基架 `OnPostAsync` 代码容易受到[过多发布攻击](#overposting)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-147">The scaffolded `OnPostAsync` code for the Create page is vulnerable to [overposting](#overposting).</span></span> <span data-ttu-id="4e6bf-148">使用以下代码替换 Pages/Students/Create.cshtml.cs 中的 `OnPostAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-148">Replace the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="4e6bf-149">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="4e6bf-149">TryUpdateModelAsync</span></span>

<span data-ttu-id="4e6bf-150">前面的代码将创建一个 Student 对象，然后使用发布的表单域更新 Student 对象的属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-150">The preceding code creates a Student object and then uses posted form fields to update the Student object's properties.</span></span> <span data-ttu-id="4e6bf-151">[TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 方法：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-151">The [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) method:</span></span>

* <span data-ttu-id="4e6bf-152">使用 [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) 中 [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) 属性的已发布的表单值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-152">Uses the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span>
* <span data-ttu-id="4e6bf-153">仅更新列出的属性 (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-153">Updates only the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>
* <span data-ttu-id="4e6bf-154">查找带有“student”前缀的表单值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-154">Looks for form fields with a "student" prefix.</span></span> <span data-ttu-id="4e6bf-155">例如 `Student.FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-155">For example, `Student.FirstMidName`.</span></span> <span data-ttu-id="4e6bf-156">该自变量不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-156">It's not case sensitive.</span></span>
* <span data-ttu-id="4e6bf-157">使用[模型绑定](xref:mvc/models/model-binding)系统将字符串中的表单值转换为 `Student` 模型中的类型。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-157">Uses the [model binding](xref:mvc/models/model-binding) system to convert form values from strings to the types in the `Student` model.</span></span> <span data-ttu-id="4e6bf-158">例如，`EnrollmentDate` 转换为 `DateTime`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-158">For example, `EnrollmentDate` is converted to `DateTime`.</span></span>

<span data-ttu-id="4e6bf-159">运行应用，并创建一个学生实体以测试“创建”页。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-159">Run the app, and create a student entity to test the Create page.</span></span>

## <a name="overposting"></a><span data-ttu-id="4e6bf-160">过多发布</span><span class="sxs-lookup"><span data-stu-id="4e6bf-160">Overposting</span></span>

<span data-ttu-id="4e6bf-161">使用 `TryUpdateModel` 更新具有已发布值的字段是一种最佳的安全做法，因为这能阻止过多发布。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-161">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="4e6bf-162">例如，假设 Student 实体包含此网页不应更新或添加的 `Secret` 属性：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-162">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="4e6bf-163">即使应用的创建或更新 Razor 页面上没有 `Secret` 字段，黑客仍可利用过多发布设置 `Secret` 值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-163">Even if the app doesn't have a `Secret` field on the create or update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="4e6bf-164">黑客也可使用 Fiddler 等工具或通过编写某个 JavaScript 来发布 `Secret` 表单值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-164">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="4e6bf-165">原始代码不会限制模型绑定器在创建“学生”实例时使用的字段。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-165">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="4e6bf-166">黑客为 `Secret` 表单域指定的任何值都会在数据库中更新。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-166">Whatever value the hacker specified for the `Secret` form field is updated in the database.</span></span> <span data-ttu-id="4e6bf-167">下图显示 Fiddler 工具正在将 `Secret` 字段（值为“OverPost”）添加到已发布的表单值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-167">The following image shows the Fiddler tool adding the `Secret` field, with the value "OverPost", to the posted form values.</span></span>

![Fiddler 添加 Secret 字段](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="4e6bf-169">值“OverPost”已成功添加到所插入行的 `Secret` 属性中。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-169">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="4e6bf-170">即使应用设计器从未打算用“创建”页设置 `Secret` 属性，也会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-170">That happens even though the app designer never intended the `Secret` property to be set with the Create page.</span></span>

### <a name="view-model"></a><span data-ttu-id="4e6bf-171">视图模型</span><span class="sxs-lookup"><span data-stu-id="4e6bf-171">View model</span></span>

<span data-ttu-id="4e6bf-172">视图模型还提供了一种防止过度发布的方法。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-172">View models provide an alternative way to prevent overposting.</span></span>

<span data-ttu-id="4e6bf-173">应用程序模型通常称为域模型。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-173">The application model is often called the domain model.</span></span> <span data-ttu-id="4e6bf-174">域模型通常包含数据库中对应实体所需的全部属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-174">The domain model typically contains all the properties required by the corresponding entity in the database.</span></span> <span data-ttu-id="4e6bf-175">视图模型仅包含 UI 页（例如“创建”页）所需的属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-175">The view model contains only the properties needed for the UI page, for example, the Create page.</span></span>

<span data-ttu-id="4e6bf-176">除视图模型外，某些应用使用绑定模型或输入模型在 Razor Pages 页面模型类和浏览器之间传递数据。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-176">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> 

<span data-ttu-id="4e6bf-177">请考虑以下 `StudentVM` 视图模型：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-177">Consider the following `StudentVM` view model:</span></span>

[!code-csharp[Main](intro/samples/cu50/ViewModels/StudentVM.cs?name=snippet)]

<span data-ttu-id="4e6bf-178">以下代码使用 `StudentVM` 视图模型创建新的学生：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-178">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml.cs?name=snippet)]

<span data-ttu-id="4e6bf-179">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 方法通过从另一个 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 对象读取值来设置此对象的值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-179">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="4e6bf-180">`SetValues` 使用属性名称匹配。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-180">`SetValues` uses property name matching.</span></span> <span data-ttu-id="4e6bf-181">视图模型类型：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-181">The view model type:</span></span>

* <span data-ttu-id="4e6bf-182">无需与模型类型相关。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-182">Doesn't need to be related to the model type.</span></span>
* <span data-ttu-id="4e6bf-183">需要具有匹配的属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-183">Needs to have properties that match.</span></span>

<span data-ttu-id="4e6bf-184">使用 `StudentVM` 要求“创建”页使用 `StudentVM`，而不是 `Student`：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-184">Using `StudentVM` requires the Create page use `StudentVM` rather than `Student`:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml)]

## <a name="update-the-edit-page"></a><span data-ttu-id="4e6bf-185">更新“编辑”页</span><span class="sxs-lookup"><span data-stu-id="4e6bf-185">Update the Edit page</span></span>

<span data-ttu-id="4e6bf-186">在 Pages/Students/Edit.cshtml.cs 中，使用以下代码替换 `OnGetAsync` 和 `OnPostAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-186">In *Pages/Students/Edit.cshtml.cs*, replace the `OnGetAsync` and `OnPostAsync` methods with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

<span data-ttu-id="4e6bf-187">代码更改与“创建”页类似，但有少数例外：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-187">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="4e6bf-188">已将 `FirstOrDefaultAsync` 替换为 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-188">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="4e6bf-189">不需要包含相关数据时，`FindAsync` 效率更高。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-189">When you don't have to include related data, `FindAsync` is more efficient.</span></span>
* <span data-ttu-id="4e6bf-190">`OnPostAsync` 具有 `id` 参数。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-190">`OnPostAsync` has an `id` parameter.</span></span>
* <span data-ttu-id="4e6bf-191">当前学生是从数据库中提取的，而非通过创建空学生获得。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-191">The current student is fetched from the database, rather than creating an empty student.</span></span>

<span data-ttu-id="4e6bf-192">运行应用，并通过创建和编辑学生进行测试。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-192">Run the app, and test it by creating and editing a student.</span></span>

## <a name="entity-states"></a><span data-ttu-id="4e6bf-193">实体状态</span><span class="sxs-lookup"><span data-stu-id="4e6bf-193">Entity States</span></span>

<span data-ttu-id="4e6bf-194">数据库上下文会随时跟踪内存中的实体是否已与其在数据库中的对应行进行同步。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-194">The database context keeps track of whether entities in memory are in sync with their corresponding rows in the database.</span></span> <span data-ttu-id="4e6bf-195">此跟踪信息可确定调用 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) 后的行为。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-195">This tracking information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="4e6bf-196">例如，将新实体传递到 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 方法时，该实体的状态设置为 [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-196">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="4e6bf-197">调用 `SaveChangesAsync` 时，数据库上下文会发出 SQL `INSERT` 命令。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-197">When `SaveChangesAsync` is called, the database context issues a SQL `INSERT` command.</span></span>

<span data-ttu-id="4e6bf-198">实体可能处于[以下状态](/dotnet/api/microsoft.entityframeworkcore.entitystate)之一：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-198">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="4e6bf-199">`Added`：数据库中尚不存在实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-199">`Added`: The entity doesn't yet exist in the database.</span></span> <span data-ttu-id="4e6bf-200">`SaveChanges` 方法发出 `INSERT` 语句。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-200">The `SaveChanges` method issues an `INSERT` statement.</span></span>

* <span data-ttu-id="4e6bf-201">`Unchanged`：无需保存对该实体所做的任何更改。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-201">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="4e6bf-202">从数据库中读取实体时，该实体具有此状态。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-202">An entity has this status when it's read from the database.</span></span>

* <span data-ttu-id="4e6bf-203">`Modified`：已修改实体的部分或全部属性值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-203">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="4e6bf-204">`SaveChanges` 方法发出 `UPDATE` 语句。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-204">The `SaveChanges` method issues an `UPDATE` statement.</span></span>

* <span data-ttu-id="4e6bf-205">`Deleted`：已标记该实体进行删除。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-205">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="4e6bf-206">`SaveChanges` 方法发出 `DELETE` 语句。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-206">The `SaveChanges` method issues a `DELETE` statement.</span></span>

* <span data-ttu-id="4e6bf-207">`Detached`：数据库上下文未跟踪该实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-207">`Detached`: The entity isn't being tracked by the database context.</span></span>

<span data-ttu-id="4e6bf-208">在桌面应用中，通常会自动设置状态更改。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-208">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="4e6bf-209">读取实体并执行更改后，实体状态自动更改为 `Modified`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-209">An entity is read, changes are made, and the entity state is automatically changed to `Modified`.</span></span> <span data-ttu-id="4e6bf-210">调用 `SaveChanges` 会生成仅更新已更改属性的 SQL `UPDATE` 语句。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-210">Calling `SaveChanges` generates a SQL `UPDATE` statement that updates only the changed properties.</span></span>

<span data-ttu-id="4e6bf-211">在 Web 应用中，读取实体并显示数据的 `DbContext` 将在页面呈现后进行处理。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-211">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="4e6bf-212">调用页面 `OnPostAsync` 方法时，将发出具有 `DbContext` 的新实例的 Web 请求。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-212">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="4e6bf-213">如果在这个新的上下文中重新读取实体，则会模拟桌面处理。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-213">Rereading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="4e6bf-214">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="4e6bf-214">Update the Delete page</span></span>

<span data-ttu-id="4e6bf-215">在此部分中，当对 `SaveChanges` 的调用失败时，将实现自定义错误消息。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-215">In this section, a custom error message is implemented when the call to `SaveChanges` fails.</span></span>

<span data-ttu-id="4e6bf-216">使用以下代码替换 *Pages/Students/Delete.cshtml.cs* 中的代码。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-216">Replace the code in *Pages/Students/Delete.cshtml.cs* with the following code.</span></span> <span data-ttu-id="4e6bf-217">突出显示所作更改：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-217">The changes are highlighted:</span></span>

[!code-csharp[Main](intro/samples/cu50/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=12-14,22,30-33,45-99)]

<span data-ttu-id="4e6bf-218">前面的代码将可选参数 `saveChangesError` 添加到 `OnGetAsync` 方法签名中。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-218">The preceding code adds the optional parameter `saveChangesError` to the `OnGetAsync` method signature.</span></span> <span data-ttu-id="4e6bf-219">`saveChangesError` 指示学生对象删除失败后是否调用该方法。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-219">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="4e6bf-220">删除操作可能由于暂时性网络问题而失败。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-220">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="4e6bf-221">数据库在云中时，更可能出现暂时性网络错误。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-221">Transient network errors are more likely when the database is in the cloud.</span></span> <span data-ttu-id="4e6bf-222">通过 UI 调用“删除”页 `OnGetAsync` 时，`saveChangesError` 参数为 `false`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-222">The `saveChangesError` parameter is `false` when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="4e6bf-223">当 `OnPostAsync` 调用 `OnGetAsync`（由于删除操作失败）时，`saveChangesError` 参数为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-223">When `OnGetAsync` is called by `OnPostAsync` because the delete operation failed, the `saveChangesError` parameter is `true`.</span></span>

<span data-ttu-id="4e6bf-224">`OnPostAsync` 方法检索所选实体，然后调用 [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 方法将实体的状态设置为 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-224">The `OnPostAsync` method retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="4e6bf-225">调用 `SaveChanges` 时生成 SQL `DELETE` 命令。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-225">When `SaveChanges` is called, a SQL `DELETE` command is generated.</span></span> <span data-ttu-id="4e6bf-226">如果 `Remove` 失败：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-226">If `Remove` fails:</span></span>

* <span data-ttu-id="4e6bf-227">捕获数据库异常。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-227">The database exception is caught.</span></span>
* <span data-ttu-id="4e6bf-228">通过 `saveChangesError=true` 调用“删除”页 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-228">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

<span data-ttu-id="4e6bf-229">向 Pages/Students/Delete.cshtml 添加错误消息：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-229">Add an error message to *Pages/Students/Delete.cshtml*:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

<span data-ttu-id="4e6bf-230">运行应用并删除学生以测试“删除”页。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-230">Run the app and delete a student to test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4e6bf-231">后续步骤</span><span class="sxs-lookup"><span data-stu-id="4e6bf-231">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4e6bf-232">[上一个教程](xref:data/ef-rp/intro)
> [下一个教程](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="4e6bf-232">[Previous tutorial](xref:data/ef-rp/intro)
[Next tutorial](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="4e6bf-233">本教程将介绍和自定义已搭建基架的 CRUD （创建、读取、更新、删除）代码。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-233">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

## <a name="no-repository"></a><span data-ttu-id="4e6bf-234">无存储库</span><span class="sxs-lookup"><span data-stu-id="4e6bf-234">No repository</span></span>

<span data-ttu-id="4e6bf-235">某些开发人员使用服务层或存储库模式在 UI (Razor Pages) 和数据访问层之间创建抽象层。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-235">Some developers use a service layer or repository pattern to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span> <span data-ttu-id="4e6bf-236">本教程不会这样做。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-236">This tutorial doesn't do that.</span></span> <span data-ttu-id="4e6bf-237">为最大程度降低复杂性并让本教程重点介绍 EF Core，将直接在页面模型类中添加 EF Core 代码。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-237">To minimize complexity and keep the tutorial focused on EF Core, EF Core code is added directly to the page model classes.</span></span> 

## <a name="update-the-details-page"></a><span data-ttu-id="4e6bf-238">更新“详细信息”页</span><span class="sxs-lookup"><span data-stu-id="4e6bf-238">Update the Details page</span></span>

<span data-ttu-id="4e6bf-239">“学生”页的基架代码不包括注册数据。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-239">The scaffolded code for the Students pages doesn't include enrollment data.</span></span> <span data-ttu-id="4e6bf-240">本部分将向“详细信息”页添加注册。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-240">In this section, enrollments are added to the Details page.</span></span>

### <a name="read-enrollments"></a><span data-ttu-id="4e6bf-241">读取注册</span><span class="sxs-lookup"><span data-stu-id="4e6bf-241">Read enrollments</span></span>

<span data-ttu-id="4e6bf-242">若要在页面上显示学生的注册数据，需要读取注册数据。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-242">To display a student's enrollment data on the page, the enrollement data needs to be read.</span></span> <span data-ttu-id="4e6bf-243">Pages/Students/Details.cshtml.cs 中的基架代码仅读取学生数据，但不读取注册数据：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-243">The scaffolded code in *Pages/Students/Details.cshtml.cs* reads only the Student data, without the Enrollment data:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

<span data-ttu-id="4e6bf-244">使用以下代码替换 `OnGetAsync` 方法以读取所选学生的注册数据。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-244">Replace the `OnGetAsync` method with the following code to read enrollment data for the selected student.</span></span> <span data-ttu-id="4e6bf-245">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-245">The changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

<span data-ttu-id="4e6bf-246">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 和 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 方法使上下文加载 `Student.Enrollments` 导航属性，并在每个注册中加载 `Enrollment.Course` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-246">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="4e6bf-247">这些方法将在[与数据读取相关](xref:data/ef-rp/read-related-data)的教程中进行详细介绍。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-247">These methods are examined in detail in the [Reading related data](xref:data/ef-rp/read-related-data) tutorial.</span></span>

<span data-ttu-id="4e6bf-248">对于返回的实体未在当前上下文中更新的情况，[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 方法将会提升性能。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-248">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios where the entities returned are not updated in the current context.</span></span> <span data-ttu-id="4e6bf-249">`AsNoTracking` 将在本教程的后续部分中讨论。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-249">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-enrollments"></a><span data-ttu-id="4e6bf-250">显示注册</span><span class="sxs-lookup"><span data-stu-id="4e6bf-250">Display enrollments</span></span>

<span data-ttu-id="4e6bf-251">使用以下代码替换 *Pages/Students/Details.cshtml* 中的代码以显示注册列表。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-251">Replace the code in *Pages/Students/Details.cshtml* with the following code to display a list of enrollments.</span></span> <span data-ttu-id="4e6bf-252">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-252">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="4e6bf-253">上面的代码循环通过 `Enrollments` 导航属性中的实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-253">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="4e6bf-254">它将针对每个注册显示课程标题和成绩。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-254">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="4e6bf-255">课程标题从 Course 实体中检索，该实体存储在 Enrollments 实体的 `Course` 导航属性中。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-255">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="4e6bf-256">运行应用，选择“学生”选项卡，然后单击学生的“详细信息”链接 。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-256">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="4e6bf-257">随即显示出所选学生的课程和成绩列表。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-257">The list of courses and grades for the selected student is displayed.</span></span>

### <a name="ways-to-read-one-entity"></a><span data-ttu-id="4e6bf-258">读取一个实体的方法</span><span class="sxs-lookup"><span data-stu-id="4e6bf-258">Ways to read one entity</span></span>

<span data-ttu-id="4e6bf-259">生成的代码使用 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) 读取一个实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-259">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) to read one entity.</span></span> <span data-ttu-id="4e6bf-260">如果未找到任何内容，则此方法返回 NULL；否则，它将返回满足查询筛选条件的第一行。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-260">This method returns null if nothing is found; otherwise, it returns the first row found that satisfies the query filter criteria.</span></span> <span data-ttu-id="4e6bf-261">`FirstOrDefaultAsync` 通常是比以下备选方案更好的选择：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-261">`FirstOrDefaultAsync` is generally a better choice than the following alternatives:</span></span>

* <span data-ttu-id="4e6bf-262">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - 如果有多个满足查询筛选器的实体，则引发异常。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-262">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - Throws an exception if there's more than one entity that satisfies the query filter.</span></span> <span data-ttu-id="4e6bf-263">若要确定查询是否可以返回多行，`SingleOrDefaultAsync` 会尝试提取多个行。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-263">To determine if more than one row could be returned by the query, `SingleOrDefaultAsync` tries to fetch multiple rows.</span></span> <span data-ttu-id="4e6bf-264">如果查询只能返回一个实体，就像它在搜索唯一键时一样，那么该额外工作是不必要的。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-264">This extra work is unnecessary if the query can only return one entity, as when it searches on a unique key.</span></span>
* <span data-ttu-id="4e6bf-265">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - 查找具有主键 ( PK) 的实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-265">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="4e6bf-266">如果具有 PK 的实体正在由上下文跟踪，会返回该实体且不向数据库发出请求。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-266">If an entity with the PK is being tracked by the context, it's returned without a request to the database.</span></span> <span data-ttu-id="4e6bf-267">此方法经过优化，可查找单个实体，但无法通过 `FindAsync` 调用 `Include`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-267">This method is optimized to look up a single entity, but you can't call `Include` with `FindAsync`.</span></span>  <span data-ttu-id="4e6bf-268">如果需要相关数据，`FirstOrDefaultAsync` 则是更好的选择。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-268">So if related data is needed, `FirstOrDefaultAsync` is the better choice.</span></span>

### <a name="route-data-vs-query-string"></a><span data-ttu-id="4e6bf-269">路由数据与查询字符串</span><span class="sxs-lookup"><span data-stu-id="4e6bf-269">Route data vs. query string</span></span>

<span data-ttu-id="4e6bf-270">“详细信息”页的 URL 是 `https://localhost:<port>/Students/Details?id=1`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-270">The URL for the Details page is `https://localhost:<port>/Students/Details?id=1`.</span></span> <span data-ttu-id="4e6bf-271">实体的主键值在查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-271">The entity's primary key value is in the query string.</span></span> <span data-ttu-id="4e6bf-272">某些开发人员偏向于在路由数据中传递键值：`https://localhost:<port>/Students/Details/1`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-272">Some developers prefer to pass the key value in route data: `https://localhost:<port>/Students/Details/1`.</span></span> <span data-ttu-id="4e6bf-273">有关详细信息，请参阅[更新生成的代码](xref:tutorials/razor-pages/da1#update-the-generated-code)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-273">For more information, see [Update the generated code](xref:tutorials/razor-pages/da1#update-the-generated-code).</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="4e6bf-274">更新“创建”页</span><span class="sxs-lookup"><span data-stu-id="4e6bf-274">Update the Create page</span></span>

<span data-ttu-id="4e6bf-275">“创建”页面的基架 `OnPostAsync` 代码容易受到[过多发布攻击](#overposting)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-275">The scaffolded `OnPostAsync` code for the Create page is vulnerable to [overposting](#overposting).</span></span> <span data-ttu-id="4e6bf-276">使用以下代码替换 Pages/Students/Create.cshtml.cs 中的 `OnPostAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-276">Replace the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="4e6bf-277">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="4e6bf-277">TryUpdateModelAsync</span></span>

<span data-ttu-id="4e6bf-278">前面的代码将创建一个 Student 对象，然后使用发布的表单域更新 Student 对象的属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-278">The preceding code creates a Student object and then uses posted form fields to update the Student object's properties.</span></span> <span data-ttu-id="4e6bf-279">[TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 方法：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-279">The [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) method:</span></span>

* <span data-ttu-id="4e6bf-280">使用 [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) 中 [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) 属性的已发布的表单值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-280">Uses the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span>
* <span data-ttu-id="4e6bf-281">仅更新列出的属性 (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-281">Updates only the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>
* <span data-ttu-id="4e6bf-282">查找带有“student”前缀的表单值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-282">Looks for form fields with a "student" prefix.</span></span> <span data-ttu-id="4e6bf-283">例如 `Student.FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-283">For example, `Student.FirstMidName`.</span></span> <span data-ttu-id="4e6bf-284">该自变量不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-284">It's not case sensitive.</span></span>
* <span data-ttu-id="4e6bf-285">使用[模型绑定](xref:mvc/models/model-binding)系统将字符串中的表单值转换为 `Student` 模型中的类型。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-285">Uses the [model binding](xref:mvc/models/model-binding) system to convert form values from strings to the types in the `Student` model.</span></span> <span data-ttu-id="4e6bf-286">例如，`EnrollmentDate` 必须转换为 DateTime。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-286">For example, `EnrollmentDate` has to be converted to DateTime.</span></span>

<span data-ttu-id="4e6bf-287">运行应用，并创建一个学生实体以测试“创建”页。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-287">Run the app, and create a student entity to test the Create page.</span></span>

## <a name="overposting"></a><span data-ttu-id="4e6bf-288">过多发布</span><span class="sxs-lookup"><span data-stu-id="4e6bf-288">Overposting</span></span>

<span data-ttu-id="4e6bf-289">使用 `TryUpdateModel` 更新具有已发布值的字段是一种最佳的安全做法，因为这能阻止过多发布。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-289">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="4e6bf-290">例如，假设 Student 实体包含此网页不应更新或添加的 `Secret` 属性：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-290">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="4e6bf-291">即使应用的创建或更新 Razor 页面上没有 `Secret` 字段，黑客仍可利用过多发布设置 `Secret` 值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-291">Even if the app doesn't have a `Secret` field on the create or update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="4e6bf-292">黑客也可使用 Fiddler 等工具或通过编写某个 JavaScript 来发布 `Secret` 表单值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-292">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="4e6bf-293">原始代码不会限制模型绑定器在创建“学生”实例时使用的字段。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-293">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="4e6bf-294">黑客为 `Secret` 表单域指定的任何值都会在数据库中更新。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-294">Whatever value the hacker specified for the `Secret` form field is updated in the database.</span></span> <span data-ttu-id="4e6bf-295">下图显示 Fiddler 工具正在将 `Secret` 字段（值为“OverPost”）添加到已发布的表单值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-295">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler 添加 Secret 字段](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="4e6bf-297">值“OverPost”已成功添加到所插入行的 `Secret` 属性中。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-297">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="4e6bf-298">即使应用设计器从未打算用“创建”页设置 `Secret` 属性，也会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-298">That happens even though the app designer never intended the `Secret` property to be set with the Create page.</span></span>

### <a name="view-model"></a><span data-ttu-id="4e6bf-299">视图模型</span><span class="sxs-lookup"><span data-stu-id="4e6bf-299">View model</span></span>

<span data-ttu-id="4e6bf-300">视图模型还提供了一种防止过度发布的方法。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-300">View models provide an alternative way to prevent overposting.</span></span>

<span data-ttu-id="4e6bf-301">应用程序模型通常称为域模型。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-301">The application model is often called the domain model.</span></span> <span data-ttu-id="4e6bf-302">域模型通常包含数据库中对应实体所需的全部属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-302">The domain model typically contains all the properties required by the corresponding entity in the database.</span></span> <span data-ttu-id="4e6bf-303">视图模型只包含它所用于的 UI（例如，“创建”页）所需的属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-303">The view model contains only the properties needed for the UI that it is used for (for example, the Create page).</span></span>

<span data-ttu-id="4e6bf-304">除视图模型外，某些应用使用绑定模型或输入模型在 Razor Pages 页面模型类和浏览器之间传递数据。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-304">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> 

<span data-ttu-id="4e6bf-305">请考虑以下 `Student` 视图模型：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-305">Consider the following `Student` view model:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

<span data-ttu-id="4e6bf-306">以下代码使用 `StudentVM` 视图模型创建新的学生：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-306">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="4e6bf-307">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 方法通过从另一个 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 对象读取值来设置此对象的值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-307">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="4e6bf-308">`SetValues` 使用属性名称匹配。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-308">`SetValues` uses property name matching.</span></span> <span data-ttu-id="4e6bf-309">视图模型类型不需要与模型类型相关，它只需要具有匹配的属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-309">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="4e6bf-310">使用 `StudentVM` 时需要更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) 才能使用 `StudentVM` 而非 `Student`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-310">Using `StudentVM` requires [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="4e6bf-311">更新“编辑”页</span><span class="sxs-lookup"><span data-stu-id="4e6bf-311">Update the Edit page</span></span>

<span data-ttu-id="4e6bf-312">在 Pages/Students/Edit.cshtml.cs 中，使用以下代码替换 `OnGetAsync` 和 `OnPostAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-312">In *Pages/Students/Edit.cshtml.cs*, replace the `OnGetAsync` and `OnPostAsync` methods with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

<span data-ttu-id="4e6bf-313">代码更改与“创建”页类似，但有少数例外：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-313">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="4e6bf-314">已将 `FirstOrDefaultAsync` 替换为 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-314">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="4e6bf-315">如果不需要包含的相关数据，`FindAsync` 更高效。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-315">When included related data is not needed, `FindAsync` is more efficient.</span></span>
* <span data-ttu-id="4e6bf-316">`OnPostAsync` 具有 `id` 参数。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-316">`OnPostAsync` has an `id` parameter.</span></span>
* <span data-ttu-id="4e6bf-317">当前学生是从数据库中提取的，而非通过创建空学生获得。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-317">The current student is fetched from the database, rather than creating an empty student.</span></span>

<span data-ttu-id="4e6bf-318">运行应用，并通过创建和编辑学生进行测试。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-318">Run the app, and test it by creating and editing a student.</span></span>

## <a name="entity-states"></a><span data-ttu-id="4e6bf-319">实体状态</span><span class="sxs-lookup"><span data-stu-id="4e6bf-319">Entity States</span></span>

<span data-ttu-id="4e6bf-320">数据库上下文会随时跟踪内存中的实体是否已与其在数据库中的对应行进行同步。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-320">The database context keeps track of whether entities in memory are in sync with their corresponding rows in the database.</span></span> <span data-ttu-id="4e6bf-321">此跟踪信息可确定调用 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) 后的行为。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-321">This tracking information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="4e6bf-322">例如，将新实体传递到 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 方法时，该实体的状态设置为 [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-322">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="4e6bf-323">调用 `SaveChangesAsync` 时，数据库上下文会发出 SQL INSERT 命令。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-323">When `SaveChangesAsync` is called, the database context issues a SQL INSERT command.</span></span>

<span data-ttu-id="4e6bf-324">实体可能处于[以下状态](/dotnet/api/microsoft.entityframeworkcore.entitystate)之一：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-324">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="4e6bf-325">`Added`：数据库中尚不存在实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-325">`Added`: The entity doesn't yet exist in the database.</span></span> <span data-ttu-id="4e6bf-326">`SaveChanges` 方法发出 INSERT 语句。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-326">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="4e6bf-327">`Unchanged`：无需保存对该实体所做的任何更改。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-327">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="4e6bf-328">从数据库中读取实体时，该实体具有此状态。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-328">An entity has this status when it's read from the database.</span></span>

* <span data-ttu-id="4e6bf-329">`Modified`：已修改实体的部分或全部属性值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-329">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="4e6bf-330">`SaveChanges` 方法发出 UPDATE 语句。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-330">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="4e6bf-331">`Deleted`：已标记该实体进行删除。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-331">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="4e6bf-332">`SaveChanges` 方法发出 DELETE 语句。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-332">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="4e6bf-333">`Detached`：数据库上下文未跟踪该实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-333">`Detached`: The entity isn't being tracked by the database context.</span></span>

<span data-ttu-id="4e6bf-334">在桌面应用中，通常会自动设置状态更改。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-334">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="4e6bf-335">读取实体并执行更改后，实体状态自动更改为 `Modified`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-335">An entity is read, changes are made, and the entity state is automatically changed to `Modified`.</span></span> <span data-ttu-id="4e6bf-336">调用 `SaveChanges` 会生成仅更新已更改属性的 SQL UPDATE 语句。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-336">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="4e6bf-337">在 Web 应用中，读取实体并显示数据的 `DbContext` 将在页面呈现后进行处理。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-337">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="4e6bf-338">调用页面 `OnPostAsync` 方法时，将发出具有 `DbContext` 的新实例的 Web 请求。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-338">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="4e6bf-339">如果在这个新的上下文中重新读取实体，则会模拟桌面处理。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-339">Rereading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="4e6bf-340">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="4e6bf-340">Update the Delete page</span></span>

<span data-ttu-id="4e6bf-341">在此部分中，当对 `SaveChanges` 的调用失败时，将实现自定义错误消息。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-341">In this section, you implement a custom error message when the call to `SaveChanges` fails.</span></span>

<span data-ttu-id="4e6bf-342">使用以下代码替换 *Pages/Students/Delete.cshtml.cs* 中的代码。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-342">Replace the code in *Pages/Students/Delete.cshtml.cs* with the following code.</span></span> <span data-ttu-id="4e6bf-343">更改将突出显示（而不是清除 `using` 语句）。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-343">The changes are highlighted (other than cleanup of `using` statements).</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

<span data-ttu-id="4e6bf-344">前面的代码将可选参数 `saveChangesError` 添加到 `OnGetAsync` 方法签名中。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-344">The preceding code adds the optional parameter `saveChangesError` to the `OnGetAsync` method signature.</span></span> <span data-ttu-id="4e6bf-345">`saveChangesError` 指示学生对象删除失败后是否调用该方法。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-345">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="4e6bf-346">删除操作可能由于暂时性网络问题而失败。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-346">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="4e6bf-347">数据库在云中时，更可能出现暂时性网络错误。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-347">Transient network errors are more likely when the database is in the cloud.</span></span> <span data-ttu-id="4e6bf-348">通过 UI 调用“删除”页 `OnGetAsync` 时，`saveChangesError` 参数为 false。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-348">The `saveChangesError` parameter is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="4e6bf-349">当 `OnPostAsync` 调用 `OnGetAsync`（由于删除操作失败）时，`saveChangesError` 参数为 true。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-349">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

<span data-ttu-id="4e6bf-350">`OnPostAsync` 方法检索所选实体，然后调用 [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 方法将实体的状态设置为 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-350">The `OnPostAsync` method retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="4e6bf-351">调用 `SaveChanges` 时生成 SQL DELETE 命令。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-351">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="4e6bf-352">如果 `Remove` 失败：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-352">If `Remove` fails:</span></span>

* <span data-ttu-id="4e6bf-353">捕获数据库异常。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-353">The database exception is caught.</span></span>
* <span data-ttu-id="4e6bf-354">通过 `saveChangesError=true` 调用“删除”页的 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-354">The Delete page's `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

<span data-ttu-id="4e6bf-355">向“删除”Razor 页面添加错误消息 (Pages/Students/Delete.cshtml)：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-355">Add an error message to the Delete Razor Page (*Pages/Students/Delete.cshtml*):</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

<span data-ttu-id="4e6bf-356">运行应用并删除学生以测试“删除”页。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-356">Run the app and delete a student to test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4e6bf-357">后续步骤</span><span class="sxs-lookup"><span data-stu-id="4e6bf-357">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4e6bf-358">[上一个教程](xref:data/ef-rp/intro)
> [下一个教程](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="4e6bf-358">[Previous tutorial](xref:data/ef-rp/intro)
[Next tutorial](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4e6bf-359">本教程将介绍和自定义已搭建基架的 CRUD （创建、读取、更新、删除）代码。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-359">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

<span data-ttu-id="4e6bf-360">为最大程度降低复杂性并让这些教程集中介绍 EF Core，将在页面模型中使用 EF Core 代码。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-360">To minimize complexity and keep these tutorials focused on EF Core, EF Core code is used in the page models.</span></span> <span data-ttu-id="4e6bf-361">某些开发人员使用服务层或存储库模式在 UI (Razor Pages) 和数据访问层之间创建抽象层。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-361">Some developers use a service layer or repository pattern in to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span>

<span data-ttu-id="4e6bf-362">本教程将检查 Students 文件夹中的“创建”、“编辑”、“删除”和“详细信息”Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-362">In this tutorial, the Create, Edit, Delete, and Details Razor Pages in the *Students* folder are examined.</span></span>

<span data-ttu-id="4e6bf-363">基架代码将以下模式用于“创建”、“编辑”和“删除”页面：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-363">The scaffolded code uses the following pattern for Create, Edit, and Delete pages:</span></span>

* <span data-ttu-id="4e6bf-364">使用 HTTP GET 方法 `OnGetAsync` 获取和显示请求数据。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-364">Get and display the requested data with the HTTP GET method `OnGetAsync`.</span></span>
* <span data-ttu-id="4e6bf-365">使用 HTTP POST 方法 `OnPostAsync` 将更改保存到数据。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-365">Save changes to the data with the HTTP POST method `OnPostAsync`.</span></span>

<span data-ttu-id="4e6bf-366">“索引”和“详细信息”页面使用 HTTP GET 方法 `OnGetAsync` 获取和显示请求数据</span><span class="sxs-lookup"><span data-stu-id="4e6bf-366">The Index and Details pages get and display the requested data with the HTTP GET method `OnGetAsync`</span></span>

## <a name="singleordefaultasync-vs-firstordefaultasync"></a><span data-ttu-id="4e6bf-367">SingleOrDefaultAsync 与FirstOrDefaultAsync</span><span class="sxs-lookup"><span data-stu-id="4e6bf-367">SingleOrDefaultAsync vs. FirstOrDefaultAsync</span></span>

<span data-ttu-id="4e6bf-368">生成的代码使用 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_)其推荐度通常高于 [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-368">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), which is generally preferred over [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

 <span data-ttu-id="4e6bf-369">提取一个实体时，使用 `FirstOrDefaultAsync` 比使用 `SingleOrDefaultAsync` 更高效：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-369">`FirstOrDefaultAsync` is more efficient than `SingleOrDefaultAsync` at fetching one entity:</span></span>

* <span data-ttu-id="4e6bf-370">代码需要验证查询仅返回一个实体时除外。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-370">Unless the code needs to verify that there's not more than one entity returned from the query.</span></span>
* <span data-ttu-id="4e6bf-371">`SingleOrDefaultAsync` 会提取更多数据并执行不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-371">`SingleOrDefaultAsync` fetches more data and does unnecessary work.</span></span>
* <span data-ttu-id="4e6bf-372">如果有多个实体符合筛选部分，`SingleOrDefaultAsync` 将引发异常。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-372">`SingleOrDefaultAsync` throws an exception if there's more than one entity that fits the filter part.</span></span>
* <span data-ttu-id="4e6bf-373">如果有多个实体符合筛选部分，`FirstOrDefaultAsync` 不引发异常。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-373">`FirstOrDefaultAsync` doesn't throw if there's more than one entity that fits the filter part.</span></span>

<a name="FindAsync"></a>

### <a name="findasync"></a><span data-ttu-id="4e6bf-374">FindAsync</span><span class="sxs-lookup"><span data-stu-id="4e6bf-374">FindAsync</span></span>

<span data-ttu-id="4e6bf-375">在大部分基架代码中，[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) 可用于替代 `FirstOrDefaultAsync`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-375">In much of the scaffolded code, [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) can be used in place of `FirstOrDefaultAsync`.</span></span>

<span data-ttu-id="4e6bf-376">`FindAsync`:</span><span class="sxs-lookup"><span data-stu-id="4e6bf-376">`FindAsync`:</span></span>

* <span data-ttu-id="4e6bf-377">查找具有主键 (PK) 的实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-377">Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="4e6bf-378">如果具有 PK 的实体正在由上下文跟踪，会返回该实体且不向 DB 发出请求。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-378">If an entity with the PK is being tracked by the context, it's returned without a request to the DB.</span></span>
* <span data-ttu-id="4e6bf-379">既简单又简洁。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-379">Is simple and concise.</span></span>
* <span data-ttu-id="4e6bf-380">经过优化后可查找单个实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-380">Is optimized to look up a single entity.</span></span>
* <span data-ttu-id="4e6bf-381">在某些情况下可以提供性能优势，但很少发生在典型的 Web 应用中。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-381">Can have perf benefits in some situations, but that rarely happens for typical web apps.</span></span>
* <span data-ttu-id="4e6bf-382">以隐式方式使用 [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) 而不是 [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-382">Implicitly uses [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) instead of [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

<span data-ttu-id="4e6bf-383">如果想要 `Include` 其他实体，则 `FindAsync` 将不再适用。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-383">But if you want to `Include` other entities, then `FindAsync` is no longer appropriate.</span></span> <span data-ttu-id="4e6bf-384">这意味着可能需要放弃 `FindAsync` 并随着应用运行移动到查询。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-384">This means that you may need to abandon `FindAsync` and move to a query as your app progresses.</span></span>

## <a name="customize-the-details-page"></a><span data-ttu-id="4e6bf-385">自定义“详细信息”页</span><span class="sxs-lookup"><span data-stu-id="4e6bf-385">Customize the Details page</span></span>

<span data-ttu-id="4e6bf-386">浏览到 `Pages/Students` 页面。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-386">Browse to `Pages/Students` page.</span></span> <span data-ttu-id="4e6bf-387">“编辑”、“详细信息”和“删除”链接是在 Pages/Students/Index.cshtml 文件中由[定位点标记帮助器](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)生成的  。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-387">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Students/Index.cshtml* file.</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

<span data-ttu-id="4e6bf-388">运行应用并选择“详细信息”链接。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-388">Run the app and select a **Details** link.</span></span> <span data-ttu-id="4e6bf-389">URL 的格式为 `http://localhost:5000/Students/Details?id=2`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-389">The URL is of the form `http://localhost:5000/Students/Details?id=2`.</span></span> <span data-ttu-id="4e6bf-390">“学生 ID”通过查询字符串 (`?id=2`) 进行传递。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-390">The Student ID is passed using a query string (`?id=2`).</span></span>

<span data-ttu-id="4e6bf-391">更新“编辑”、“详细信息”和“删除”Razor 页面以使用 `"{id:int}"` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-391">Update the Edit, Details, and Delete Razor Pages to use the `"{id:int}"` route template.</span></span> <span data-ttu-id="4e6bf-392">将上述每个页面的页面指令从 `@page` 更改为 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-392">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span>

<span data-ttu-id="4e6bf-393">如果对具有不包含整数路由值的“{id:int}”路由模板的页面发起请求，则该请求将返回 HTTP 404（找不到）错误。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-393">A request to the page with the "{id:int}" route template that does **not** include a integer route value returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="4e6bf-394">例如，`http://localhost:5000/Students/Details` 返回 404 错误。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-394">For example, `http://localhost:5000/Students/Details` returns a 404 error.</span></span> <span data-ttu-id="4e6bf-395">若要使 ID 可选，请将 `?` 追加到路由约束：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-395">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="4e6bf-396">运行应用，单击“详细信息”链接，并验证确认 URL 正在将 ID 作为路由数据 (`http://localhost:5000/Students/Details/2`) 进行传递。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-396">Run the app, click on a Details link, and verify the URL is passing the ID as route data (`http://localhost:5000/Students/Details/2`).</span></span>

<span data-ttu-id="4e6bf-397">不要将 `@page` 全局更改为 `@page "{id:int}"`，这样做会破坏指向“主页”和“创建”页面的链接。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-397">Don't globally change `@page` to `@page "{id:int}"`, doing so breaks the links to the Home and Create pages.</span></span>

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a><span data-ttu-id="4e6bf-398">添加相关数据</span><span class="sxs-lookup"><span data-stu-id="4e6bf-398">Add related data</span></span>

<span data-ttu-id="4e6bf-399">“学生索引”页的基架代码不包括 `Enrollments` 属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-399">The scaffolded code for the Students Index page doesn't include the `Enrollments` property.</span></span> <span data-ttu-id="4e6bf-400">在本部分，`Enrollments` 集合的内容显示在“详细信息”页中。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-400">In this section, the contents of the `Enrollments` collection is displayed in the Details page.</span></span>

<span data-ttu-id="4e6bf-401">Pages/Students/Details.cshtml.cs 的 `OnGetAsync` 方法使用 `FirstOrDefaultAsync` 方法检索单个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-401">The `OnGetAsync` method of *Pages/Students/Details.cshtml.cs* uses the `FirstOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="4e6bf-402">添加以下突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-402">Add the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="4e6bf-403">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 和 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 方法使上下文加载 `Student.Enrollments` 导航属性，并在每个注册中加载 `Enrollment.Course` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-403">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="4e6bf-404">这些方法将在与数据读取相关的教程中进行详细介绍。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-404">These methods are examined in detail in the reading-related data tutorial.</span></span>

<span data-ttu-id="4e6bf-405">对于返回的实体未在当前上下文中更新的情况，[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 方法将会提升性能。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-405">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios when the entities returned are not updated in the current context.</span></span> <span data-ttu-id="4e6bf-406">`AsNoTracking` 将在本教程的后续部分中讨论。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-406">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-related-enrollments-on-the-details-page"></a><span data-ttu-id="4e6bf-407">在“详细信息”页中显示相关注册</span><span class="sxs-lookup"><span data-stu-id="4e6bf-407">Display related enrollments on the Details page</span></span>

<span data-ttu-id="4e6bf-408">打开 Pages/Students/Details.cshtml。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-408">Open *Pages/Students/Details.cshtml*.</span></span> <span data-ttu-id="4e6bf-409">添加以下突出显示的代码以显示注册列表：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-409">Add the following highlighted code to display a list of enrollments:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="4e6bf-410">如果代码缩进在粘贴代码后出现错误，请按 CTRL-K-D 进行更正。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-410">If code indentation is wrong after the code is pasted, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="4e6bf-411">上面的代码循环通过 `Enrollments` 导航属性中的实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-411">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="4e6bf-412">它将针对每个注册显示课程标题和成绩。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-412">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="4e6bf-413">课程标题从 Course 实体中检索，该实体存储在 Enrollments 实体的 `Course` 导航属性中。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-413">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="4e6bf-414">运行应用，选择“学生”选项卡，然后单击学生的“详细信息”链接 。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-414">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="4e6bf-415">随即显示出所选学生的课程和成绩列表。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-415">The list of courses and grades for the selected student is displayed.</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="4e6bf-416">更新“创建”页</span><span class="sxs-lookup"><span data-stu-id="4e6bf-416">Update the Create page</span></span>

<span data-ttu-id="4e6bf-417">将 Pages/Students/Create.cshtml.cs 中的 `OnPostAsync` 方法更新为以下代码：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-417">Update the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="4e6bf-418">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="4e6bf-418">TryUpdateModelAsync</span></span>

<span data-ttu-id="4e6bf-419">检查 [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 代码：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-419">Examine the [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

<span data-ttu-id="4e6bf-420">在前面的代码中，`TryUpdateModelAsync<Student>` 尝试使用 [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) 的 [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) 属性中已发布的表单值更新 `emptyStudent` 对象。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-420">In the preceding code, `TryUpdateModelAsync<Student>` tries to update the `emptyStudent` object using the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span> <span data-ttu-id="4e6bf-421">`TryUpdateModelAsync` 仅更新列出的属性 (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-421">`TryUpdateModelAsync` only updates the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>

<span data-ttu-id="4e6bf-422">在上述示例中：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-422">In the preceding sample:</span></span>

* <span data-ttu-id="4e6bf-423">第二个自变量 (`"student", // Prefix`) 是用于查找值的前缀。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-423">The second argument (`"student", // Prefix`) is the prefix uses to look up values.</span></span> <span data-ttu-id="4e6bf-424">该自变量不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-424">It's not case sensitive.</span></span>
* <span data-ttu-id="4e6bf-425">已发布的表单值通过[模型绑定](xref:mvc/models/model-binding)转换为 `Student` 模型中的类型。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-425">The posted form values are converted to the types in the `Student` model using [model binding](xref:mvc/models/model-binding).</span></span>

<a id="overpost"></a>

### <a name="overposting"></a><span data-ttu-id="4e6bf-426">过多发布</span><span class="sxs-lookup"><span data-stu-id="4e6bf-426">Overposting</span></span>

<span data-ttu-id="4e6bf-427">使用 `TryUpdateModel` 更新具有已发布值的字段是一种最佳的安全做法，因为这能阻止过多发布。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-427">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="4e6bf-428">例如，假设 Student 实体包含此网页不应更新或添加的 `Secret` 属性：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-428">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="4e6bf-429">即使应用的创建或更新 Razor 页面上没有 `Secret` 字段，黑客仍可利用过多发布设置 `Secret` 值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-429">Even if the app doesn't have a `Secret` field on the create/update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="4e6bf-430">黑客也可使用 Fiddler 等工具或通过编写某个 JavaScript 来发布 `Secret` 表单值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-430">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="4e6bf-431">原始代码不会限制模型绑定器在创建“学生”实例时使用的字段。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-431">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="4e6bf-432">黑客为 `Secret` 表单字段指定的任何值都会在 DB 中更新。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-432">Whatever value the hacker specified for the `Secret` form field is updated in the DB.</span></span> <span data-ttu-id="4e6bf-433">下图显示 Fiddler 工具正在将 `Secret` 字段（值为“OverPost”）添加到已发布的表单值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-433">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler 添加 Secret 字段](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="4e6bf-435">值“OverPost”已成功添加到所插入行的 `Secret` 属性中。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-435">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="4e6bf-436">应用程序设计器绝不会在“创建”页设置 `Secret` 属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-436">The app designer never intended the `Secret` property to be set with the Create page.</span></span>

<a name="vm"></a>

### <a name="view-model"></a><span data-ttu-id="4e6bf-437">视图模型</span><span class="sxs-lookup"><span data-stu-id="4e6bf-437">View model</span></span>

<span data-ttu-id="4e6bf-438">视图模型通常包含应用程序所用的模型中包括的属性的子集。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-438">A view model typically contains a subset of the properties included in the model used by the application.</span></span> <span data-ttu-id="4e6bf-439">应用程序模型通常称为域模型。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-439">The application model is often called the domain model.</span></span> <span data-ttu-id="4e6bf-440">域模型通常包含 DB 中对应实体所需的全部属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-440">The domain model typically contains all the properties required by the corresponding entity in the DB.</span></span> <span data-ttu-id="4e6bf-441">视图模型仅包含 UI 层（例如“创建”页）所需的属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-441">The view model contains only the properties needed for the UI layer (for example, the Create page).</span></span> <span data-ttu-id="4e6bf-442">除视图模型外，某些应用使用绑定模型或输入模型在 Razor Pages 页面模型类和浏览器之间传递数据。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-442">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> <span data-ttu-id="4e6bf-443">请考虑以下 `Student` 视图模型：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-443">Consider the following `Student` view model:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

<span data-ttu-id="4e6bf-444">视图模型还提供了一种防止过度发布的方法。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-444">View models provide an alternative way to prevent overposting.</span></span> <span data-ttu-id="4e6bf-445">视图模型仅包含要查看（显示）或更新的属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-445">The view model contains only the properties to view (display) or update.</span></span>

<span data-ttu-id="4e6bf-446">以下代码使用 `StudentVM` 视图模型创建新的学生：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-446">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="4e6bf-447">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 方法通过从另一个 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 对象读取值来设置此对象的值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-447">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="4e6bf-448">`SetValues` 使用属性名称匹配。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-448">`SetValues` uses property name matching.</span></span> <span data-ttu-id="4e6bf-449">视图模型类型不需要与模型类型相关，它只需要具有匹配的属性。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-449">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="4e6bf-450">使用 `StudentVM` 时需要更新 [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) 才能使用 `StudentVM` 而非 `Student`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-450">Using `StudentVM` requires [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

<span data-ttu-id="4e6bf-451">在 Razor Pages 中，`PageModel` 派生类就是视图模型。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-451">In Razor Pages, the `PageModel` derived class is the view model.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="4e6bf-452">更新“编辑”页</span><span class="sxs-lookup"><span data-stu-id="4e6bf-452">Update the Edit page</span></span>

<span data-ttu-id="4e6bf-453">更新“编辑”页的页面模型。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-453">Update the page model for the Edit page.</span></span> <span data-ttu-id="4e6bf-454">突出显示所作的主要更改：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-454">The major changes are highlighted:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

<span data-ttu-id="4e6bf-455">代码更改与“创建”页类似，但有少数例外：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-455">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="4e6bf-456">`OnPostAsync` 具有可选的 `id` 参数。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-456">`OnPostAsync` has an optional `id` parameter.</span></span>
* <span data-ttu-id="4e6bf-457">当前学生是从 DB 提取的，而非通过创建空学生获得。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-457">The current student is fetched from the DB, rather than creating an empty student.</span></span>
* <span data-ttu-id="4e6bf-458">已将 `FirstOrDefaultAsync` 替换为 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-458">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="4e6bf-459">从主键中选择实体时，使用 `FindAsync` 是一个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-459">`FindAsync` is a good choice when selecting an entity from the primary key.</span></span> <span data-ttu-id="4e6bf-460">请参阅 [FindAsync](#FindAsync) 了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-460">See [FindAsync](#FindAsync) for more information.</span></span>

### <a name="test-the-edit-and-create-pages"></a><span data-ttu-id="4e6bf-461">测试“编辑”和“创建”页</span><span class="sxs-lookup"><span data-stu-id="4e6bf-461">Test the Edit and Create pages</span></span>

<span data-ttu-id="4e6bf-462">创建和编辑几个学生实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-462">Create and edit a few student entities.</span></span>

## <a name="entity-states"></a><span data-ttu-id="4e6bf-463">实体状态</span><span class="sxs-lookup"><span data-stu-id="4e6bf-463">Entity States</span></span>

<span data-ttu-id="4e6bf-464">DB 上下文会随时跟踪内存中的实体是否已与其在 DB 中的对应行进行同步。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-464">The DB context keeps track of whether entities in memory are in sync with their corresponding rows in the DB.</span></span> <span data-ttu-id="4e6bf-465">DB 上下文同步信息可决定调用 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) 后的行为。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-465">The DB context sync information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="4e6bf-466">例如，将新实体传递到 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 方法时，该实体的状态设置为 [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-466">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="4e6bf-467">调用 `SaveChangesAsync` 时，DB 上下文会发出 SQL INSERT 命令。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-467">When `SaveChangesAsync` is called, the DB context issues a SQL INSERT command.</span></span>

<span data-ttu-id="4e6bf-468">实体可能处于[以下状态](/dotnet/api/microsoft.entityframeworkcore.entitystate)之一：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-468">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="4e6bf-469">`Added`：DB 中尚不存在实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-469">`Added`: The entity doesn't yet exist in the DB.</span></span> <span data-ttu-id="4e6bf-470">`SaveChanges` 方法发出 INSERT 语句。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-470">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="4e6bf-471">`Unchanged`：无需保存对该实体所做的任何更改。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-471">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="4e6bf-472">从 DB 中读取实体时，该实体将具有此状态。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-472">An entity has this status when it's read from the DB.</span></span>

* <span data-ttu-id="4e6bf-473">`Modified`：已修改实体的部分或全部属性值。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-473">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="4e6bf-474">`SaveChanges` 方法发出 UPDATE 语句。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-474">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="4e6bf-475">`Deleted`：已标记该实体进行删除。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-475">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="4e6bf-476">`SaveChanges` 方法发出 DELETE 语句。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-476">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="4e6bf-477">`Detached`：DB 上下文未跟踪该实体。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-477">`Detached`: The entity isn't being tracked by the DB context.</span></span>

<span data-ttu-id="4e6bf-478">在桌面应用中，通常会自动设置状态更改。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-478">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="4e6bf-479">读取实体并执行更改后，实体状态自动更改为 `Modified`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-479">An entity is read, changes are made, and the entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="4e6bf-480">调用 `SaveChanges` 会生成仅更新已更改属性的 SQL UPDATE 语句。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-480">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="4e6bf-481">在 Web 应用中，读取实体并显示数据的 `DbContext` 将在页面呈现后进行处理。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-481">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="4e6bf-482">调用页面 `OnPostAsync` 方法时，将发出具有 `DbContext` 的新实例的 Web 请求。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-482">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="4e6bf-483">如果在这个新的上下文中重新读取实体，则会模拟桌面处理。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-483">Re-reading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="4e6bf-484">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="4e6bf-484">Update the Delete page</span></span>

<span data-ttu-id="4e6bf-485">在此部分中，当对 `SaveChanges` 的调用失败时，将添加用于实现自定义错误消息的代码。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-485">In this section, code is added to implement a custom error message when the call to `SaveChanges` fails.</span></span> <span data-ttu-id="4e6bf-486">添加字符串，使其包含可能的错误消息：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-486">Add a string to contain possible error messages:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

<span data-ttu-id="4e6bf-487">将 `OnGetAsync` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-487">Replace the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

<span data-ttu-id="4e6bf-488">上述代码包含可选参数 `saveChangesError`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-488">The preceding code contains the optional parameter `saveChangesError`.</span></span> <span data-ttu-id="4e6bf-489">`saveChangesError` 指示学生对象删除失败后是否调用该方法。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-489">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="4e6bf-490">删除操作可能由于暂时性网络问题而失败。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-490">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="4e6bf-491">云端更可能出现暂时性网络错误。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-491">Transient network errors are more likely in the cloud.</span></span> <span data-ttu-id="4e6bf-492">通过 UI 调用“删除”页 `OnGetAsync` 时，`saveChangesError` 为 false。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-492">`saveChangesError`is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="4e6bf-493">当 `OnPostAsync` 调用 `OnGetAsync`（由于删除操作失败）时，`saveChangesError` 参数为 true。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-493">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

### <a name="the-delete-pages-onpostasync-method"></a><span data-ttu-id="4e6bf-494">“删除”页 OnPostAsync 方法</span><span class="sxs-lookup"><span data-stu-id="4e6bf-494">The Delete pages OnPostAsync method</span></span>

<span data-ttu-id="4e6bf-495">将 `OnPostAsync` 替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-495">Replace the `OnPostAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="4e6bf-496">上述代码检索所选的实体，然后调用 [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 方法，将实体的状态设置为 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-496">The preceding code retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="4e6bf-497">调用 `SaveChanges` 时生成 SQL DELETE 命令。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-497">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="4e6bf-498">如果 `Remove` 失败：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-498">If `Remove` fails:</span></span>

* <span data-ttu-id="4e6bf-499">会捕获 DB 异常。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-499">The DB exception is caught.</span></span>
* <span data-ttu-id="4e6bf-500">通过 `saveChangesError=true` 调用“删除”页 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-500">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

### <a name="update-the-delete-no-locrazor-page"></a><span data-ttu-id="4e6bf-501">更新“删除”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="4e6bf-501">Update the Delete Razor Page</span></span>

<span data-ttu-id="4e6bf-502">将以下突出显示的错误消息添加到“删除”Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-502">Add the following highlighted error message to the Delete Razor Page.</span></span>
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

<span data-ttu-id="4e6bf-503">测试“删除”。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-503">Test Delete.</span></span>

## <a name="common-errors"></a><span data-ttu-id="4e6bf-504">常见错误</span><span class="sxs-lookup"><span data-stu-id="4e6bf-504">Common errors</span></span>

<span data-ttu-id="4e6bf-505">“学生/索引”或其他链接不起作用：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-505">Students/Index or other links don't work:</span></span>

<span data-ttu-id="4e6bf-506">验证确认 Razor 页面包含正确的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-506">Verify the Razor Page contains the correct `@page` directive.</span></span> <span data-ttu-id="4e6bf-507">例如，“学生”/“索引”Razor 页面不得包含路由模板：</span><span class="sxs-lookup"><span data-stu-id="4e6bf-507">For example, The Students/Index Razor Page should **not** contain a route template:</span></span>

```cshtml
@page "{id:int}"
```

<span data-ttu-id="4e6bf-508">每个 Razor 页面均必须包含 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="4e6bf-508">Each Razor Page must include the `@page` directive.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="4e6bf-509">其他资源</span><span class="sxs-lookup"><span data-stu-id="4e6bf-509">Additional resources</span></span>

* [<span data-ttu-id="4e6bf-510">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="4e6bf-510">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> <span data-ttu-id="4e6bf-511">[上一页](xref:data/ef-rp/intro)
> [下一页](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="4e6bf-511">[Previous](xref:data/ef-rp/intro)
[Next](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end
