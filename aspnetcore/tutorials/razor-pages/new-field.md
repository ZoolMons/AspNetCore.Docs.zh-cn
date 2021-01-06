---
title: 第 7 部分，添加新字段
author: rick-anderson
description: Razor 页面教程系列第 7 部分。
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 2961fcaa8bbb93527eb80f5aa7b18ef04761fb50
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486156"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="c447e-103">第 7 部分，将新字段添加到 ASP.NET Core 中的 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="c447e-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="c447e-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c447e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c447e-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="c447e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c447e-106">在此部分中，[Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 迁移用于：</span><span class="sxs-lookup"><span data-stu-id="c447e-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="c447e-107">将新字段添加到模型。</span><span class="sxs-lookup"><span data-stu-id="c447e-107">Add a new field to the model.</span></span>
* <span data-ttu-id="c447e-108">将新字段架构更改迁移到数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="c447e-109">使用 EF Code First 自动创建数据库时，Code First 将：</span><span class="sxs-lookup"><span data-stu-id="c447e-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="c447e-110">向数据库添加 [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) 表格，以跟踪数据库的架构是否与从生成它的模型类同步。</span><span class="sxs-lookup"><span data-stu-id="c447e-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="c447e-111">如果该模型类未与数据库同步，EF 将引发异常。</span><span class="sxs-lookup"><span data-stu-id="c447e-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="c447e-112">自动验证架构与模型是否同步可以更容易地发现不一致的数据库代码问题。</span><span class="sxs-lookup"><span data-stu-id="c447e-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="c447e-113">向电影模型添加分级属性</span><span class="sxs-lookup"><span data-stu-id="c447e-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="c447e-114">打开 Models/Movie.cs 文件，并添加 `Rating` 属性：</span><span class="sxs-lookup"><span data-stu-id="c447e-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="c447e-115">构建应用程序。</span><span class="sxs-lookup"><span data-stu-id="c447e-115">Build the app.</span></span>

1. <span data-ttu-id="c447e-116">编辑 Pages/Movies/Index.cshtml，并添加 `Rating` 字段：</span><span class="sxs-lookup"><span data-stu-id="c447e-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="c447e-117">更新以下页面：</span><span class="sxs-lookup"><span data-stu-id="c447e-117">Update the following pages:</span></span>
   1. <span data-ttu-id="c447e-118">将 `Rating` 字段添加到“删除”和“详细信息”页面。</span><span class="sxs-lookup"><span data-stu-id="c447e-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="c447e-119">使用 `Rating` 字段更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="c447e-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="c447e-120">将 `Rating` 字段添加到“编辑”页面。</span><span class="sxs-lookup"><span data-stu-id="c447e-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="c447e-121">在数据库更新为包括新字段之前，应用将不会正常工作。</span><span class="sxs-lookup"><span data-stu-id="c447e-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="c447e-122">在不更新数据库的情况下运行应用会引发 `SqlException`：</span><span class="sxs-lookup"><span data-stu-id="c447e-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="c447e-123">`SqlException` 异常是由于更新的 Movie 模型类与数据库的 Movie 表架构不同导致的。</span><span class="sxs-lookup"><span data-stu-id="c447e-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="c447e-124">数据库表中没有 `Rating` 列。</span><span class="sxs-lookup"><span data-stu-id="c447e-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="c447e-125">可通过几种方法解决此错误：</span><span class="sxs-lookup"><span data-stu-id="c447e-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="c447e-126">让 Entity Framework 自动丢弃并使用新的模型类架构重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="c447e-127">此方法在开发周期早期很方便，通过该方法可以一起快速改进模型和数据库架构。</span><span class="sxs-lookup"><span data-stu-id="c447e-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="c447e-128">此方法的缺点是会导致数据库中的现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="c447e-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="c447e-129">请勿对生产数据库使用此方法！</span><span class="sxs-lookup"><span data-stu-id="c447e-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="c447e-130">在架构更改时丢弃数据库，并使用初始化表达式通过测试数据自动设定数据库种子，这通常是开发应用的有效方式。</span><span class="sxs-lookup"><span data-stu-id="c447e-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="c447e-131">对现有数据库架构进行显式修改，使它与模型类相匹配。</span><span class="sxs-lookup"><span data-stu-id="c447e-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="c447e-132">此方法的优点是可以保留数据。</span><span class="sxs-lookup"><span data-stu-id="c447e-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="c447e-133">可以手动或通过创建数据库更改脚本进行此更改。</span><span class="sxs-lookup"><span data-stu-id="c447e-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="c447e-134">使用 Code First 迁移更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="c447e-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="c447e-135">对于本教程，请使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="c447e-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="c447e-136">更新 `SeedData` 类，使它提供新列的值。</span><span class="sxs-lookup"><span data-stu-id="c447e-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="c447e-137">示例更改如下所示，但对每个 `new Movie` 块做出此更改。</span><span class="sxs-lookup"><span data-stu-id="c447e-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="c447e-138">请参阅[已完成的 SeedData.cs 文件](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)。</span><span class="sxs-lookup"><span data-stu-id="c447e-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="c447e-139">生成解决方案。</span><span class="sxs-lookup"><span data-stu-id="c447e-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c447e-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c447e-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="c447e-141">添加用于评级字段的迁移</span><span class="sxs-lookup"><span data-stu-id="c447e-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="c447e-142">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。 </span><span class="sxs-lookup"><span data-stu-id="c447e-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="c447e-143">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="c447e-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="c447e-144">`Add-Migration` 命令会通知框架执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c447e-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="c447e-145">将 `Movie` 模型与 `Movie` 数据库架构进行比较。</span><span class="sxs-lookup"><span data-stu-id="c447e-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="c447e-146">创建代码以将数据库架构迁移到新模型。</span><span class="sxs-lookup"><span data-stu-id="c447e-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="c447e-147">名称“Rating”是任意的，用于对迁移文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="c447e-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="c447e-148">为迁移文件使用有意义的名称是有帮助的。</span><span class="sxs-lookup"><span data-stu-id="c447e-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="c447e-149">`Update-Database` 命令指示框架将架构更改应用到数据库并保留现有数据。</span><span class="sxs-lookup"><span data-stu-id="c447e-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="c447e-150">如果删除数据库中的所有记录，初始化表达式会设定数据库种子，并将包括 `Rating` 字段。</span><span class="sxs-lookup"><span data-stu-id="c447e-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="c447e-151">可以使用浏览器中的删除链接，也可以从 [Sql Server 对象资源管理器](xref:tutorials/razor-pages/sql#ssox) (SSOX) 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="c447e-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="c447e-152">另一个方案是删除数据库，并使用迁移来重新创建该数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="c447e-153">删除 SSOX 中的数据库：</span><span class="sxs-lookup"><span data-stu-id="c447e-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="c447e-154">在 SSOX 中选择数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="c447e-155">右键单击数据库，并选择“删除”。</span><span class="sxs-lookup"><span data-stu-id="c447e-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="c447e-156">检查“关闭现有连接”。</span><span class="sxs-lookup"><span data-stu-id="c447e-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="c447e-157">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c447e-157">Select **OK**.</span></span>
1. <span data-ttu-id="c447e-158">在 [PMC](xref:tutorials/razor-pages/new-field#pmc) 中更新数据库：</span><span class="sxs-lookup"><span data-stu-id="c447e-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c447e-159">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c447e-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="c447e-160">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="c447e-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="c447e-161">在本教程中，使用 Entity Framework Core 迁移功能（若可行）。</span><span class="sxs-lookup"><span data-stu-id="c447e-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="c447e-162">迁移会更新数据库架构，使其与数据模型中的更改相匹配。</span><span class="sxs-lookup"><span data-stu-id="c447e-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="c447e-163">但是，迁移仅能执行 EF Core 提供程序所支持的更改类型，且 SQLite 提供程序的功能将受限。</span><span class="sxs-lookup"><span data-stu-id="c447e-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="c447e-164">例如，支持添加列，但不支持删除或更改列。</span><span class="sxs-lookup"><span data-stu-id="c447e-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="c447e-165">如果已创建迁移以删除或更改列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。</span><span class="sxs-lookup"><span data-stu-id="c447e-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="c447e-166">由于上述限制，本教程不对 SQLite 架构更改使用迁移。</span><span class="sxs-lookup"><span data-stu-id="c447e-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="c447e-167">转而在架构更改时，放弃并重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="c447e-168">要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。</span><span class="sxs-lookup"><span data-stu-id="c447e-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="c447e-169">表重新生成涉及：</span><span class="sxs-lookup"><span data-stu-id="c447e-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="c447e-170">创建新表。</span><span class="sxs-lookup"><span data-stu-id="c447e-170">Creating a new table.</span></span>
>* <span data-ttu-id="c447e-171">将旧表中的数据复制到新表中。</span><span class="sxs-lookup"><span data-stu-id="c447e-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="c447e-172">放弃旧表。</span><span class="sxs-lookup"><span data-stu-id="c447e-172">Dropping the old table.</span></span>
>* <span data-ttu-id="c447e-173">为新表重命名。</span><span class="sxs-lookup"><span data-stu-id="c447e-173">Renaming the new table.</span></span>
>
><span data-ttu-id="c447e-174">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="c447e-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="c447e-175">SQLite EF Core 数据库提供程序限制</span><span class="sxs-lookup"><span data-stu-id="c447e-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="c447e-176">自定义迁移代码</span><span class="sxs-lookup"><span data-stu-id="c447e-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="c447e-177">数据种子设定</span><span class="sxs-lookup"><span data-stu-id="c447e-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="c447e-178">SQLite ALTER TABLE 语句</span><span class="sxs-lookup"><span data-stu-id="c447e-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="c447e-179">删除迁移文件夹。</span><span class="sxs-lookup"><span data-stu-id="c447e-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="c447e-180">使用以下命令重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="c447e-181">运行应用，并验证是否可以创建/编辑/显示具有 `Rating` 字段的电影。</span><span class="sxs-lookup"><span data-stu-id="c447e-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="c447e-182">如果数据库未设定种子，则在 `SeedData.Initialize` 方法中设置断点。</span><span class="sxs-lookup"><span data-stu-id="c447e-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c447e-183">其他资源</span><span class="sxs-lookup"><span data-stu-id="c447e-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c447e-184">[上一篇：添加搜索](xref:tutorials/razor-pages/search)
> [下一步：添加验证](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="c447e-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="c447e-185">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="c447e-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c447e-186">在此部分中，[Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 迁移用于：</span><span class="sxs-lookup"><span data-stu-id="c447e-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="c447e-187">将新字段添加到模型。</span><span class="sxs-lookup"><span data-stu-id="c447e-187">Add a new field to the model.</span></span>
* <span data-ttu-id="c447e-188">将新字段架构更改迁移到数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="c447e-189">使用 EF Code First 自动创建数据库时，Code First 将：</span><span class="sxs-lookup"><span data-stu-id="c447e-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="c447e-190">向数据库添加 [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) 表格，以跟踪数据库的架构是否与从生成它的模型类同步。</span><span class="sxs-lookup"><span data-stu-id="c447e-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="c447e-191">如果该模型类未与数据库同步，EF 将引发异常。</span><span class="sxs-lookup"><span data-stu-id="c447e-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="c447e-192">自动验证架构与模型是否同步可以更容易地发现不一致的数据库代码问题。</span><span class="sxs-lookup"><span data-stu-id="c447e-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="c447e-193">向电影模型添加分级属性</span><span class="sxs-lookup"><span data-stu-id="c447e-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="c447e-194">打开 Models/Movie.cs 文件，并添加 `Rating` 属性：</span><span class="sxs-lookup"><span data-stu-id="c447e-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="c447e-195">构建应用程序。</span><span class="sxs-lookup"><span data-stu-id="c447e-195">Build the app.</span></span>

1. <span data-ttu-id="c447e-196">编辑 Pages/Movies/Index.cshtml，并添加 `Rating` 字段：</span><span class="sxs-lookup"><span data-stu-id="c447e-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="c447e-197">更新以下页面：</span><span class="sxs-lookup"><span data-stu-id="c447e-197">Update the following pages:</span></span>
   1. <span data-ttu-id="c447e-198">将 `Rating` 字段添加到“删除”和“详细信息”页面。</span><span class="sxs-lookup"><span data-stu-id="c447e-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="c447e-199">使用 `Rating` 字段更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="c447e-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="c447e-200">将 `Rating` 字段添加到“编辑”页面。</span><span class="sxs-lookup"><span data-stu-id="c447e-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="c447e-201">在数据库更新为包括新字段之前，应用将不会正常工作。</span><span class="sxs-lookup"><span data-stu-id="c447e-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="c447e-202">在不更新数据库的情况下运行应用会引发 `SqlException`：</span><span class="sxs-lookup"><span data-stu-id="c447e-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="c447e-203">`SqlException` 异常是由于更新的 Movie 模型类与数据库的 Movie 表架构不同导致的。</span><span class="sxs-lookup"><span data-stu-id="c447e-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="c447e-204">数据库表中没有 `Rating` 列。</span><span class="sxs-lookup"><span data-stu-id="c447e-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="c447e-205">可通过几种方法解决此错误：</span><span class="sxs-lookup"><span data-stu-id="c447e-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="c447e-206">让 Entity Framework 自动丢弃并使用新的模型类架构重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="c447e-207">此方法在开发周期早期很方便，通过该方法可以一起快速改进模型和数据库架构。</span><span class="sxs-lookup"><span data-stu-id="c447e-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="c447e-208">此方法的缺点是会导致数据库中的现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="c447e-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="c447e-209">请勿对生产数据库使用此方法！</span><span class="sxs-lookup"><span data-stu-id="c447e-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="c447e-210">在架构更改时丢弃数据库，并使用初始化表达式通过测试数据自动设定数据库种子，这通常是开发应用的有效方式。</span><span class="sxs-lookup"><span data-stu-id="c447e-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="c447e-211">对现有数据库架构进行显式修改，使它与模型类相匹配。</span><span class="sxs-lookup"><span data-stu-id="c447e-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="c447e-212">此方法的优点是可以保留数据。</span><span class="sxs-lookup"><span data-stu-id="c447e-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="c447e-213">可以手动或通过创建数据库更改脚本进行此更改。</span><span class="sxs-lookup"><span data-stu-id="c447e-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="c447e-214">使用 Code First 迁移更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="c447e-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="c447e-215">对于本教程，请使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="c447e-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="c447e-216">更新 `SeedData` 类，使它提供新列的值。</span><span class="sxs-lookup"><span data-stu-id="c447e-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="c447e-217">示例更改如下所示，但对每个 `new Movie` 块做出此更改。</span><span class="sxs-lookup"><span data-stu-id="c447e-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="c447e-218">请参阅[已完成的 SeedData.cs 文件](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)。</span><span class="sxs-lookup"><span data-stu-id="c447e-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="c447e-219">生成解决方案。</span><span class="sxs-lookup"><span data-stu-id="c447e-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c447e-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c447e-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="c447e-221">添加用于评级字段的迁移</span><span class="sxs-lookup"><span data-stu-id="c447e-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="c447e-222">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。 </span><span class="sxs-lookup"><span data-stu-id="c447e-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="c447e-223">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="c447e-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="c447e-224">`Add-Migration` 命令会通知框架执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c447e-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="c447e-225">将 `Movie` 模型与 `Movie` 数据库架构进行比较。</span><span class="sxs-lookup"><span data-stu-id="c447e-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="c447e-226">创建代码以将数据库架构迁移到新模型。</span><span class="sxs-lookup"><span data-stu-id="c447e-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="c447e-227">名称“Rating”是任意的，用于对迁移文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="c447e-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="c447e-228">为迁移文件使用有意义的名称是有帮助的。</span><span class="sxs-lookup"><span data-stu-id="c447e-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="c447e-229">`Update-Database` 命令指示框架将架构更改应用到数据库并保留现有数据。</span><span class="sxs-lookup"><span data-stu-id="c447e-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="c447e-230">如果删除数据库中的所有记录，初始化表达式会设定数据库种子，并将包括 `Rating` 字段。</span><span class="sxs-lookup"><span data-stu-id="c447e-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="c447e-231">可以使用浏览器中的删除链接，也可以从 [Sql Server 对象资源管理器](xref:tutorials/razor-pages/sql#ssox) (SSOX) 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="c447e-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="c447e-232">另一个方案是删除数据库，并使用迁移来重新创建该数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="c447e-233">删除 SSOX 中的数据库：</span><span class="sxs-lookup"><span data-stu-id="c447e-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="c447e-234">在 SSOX 中选择数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="c447e-235">右键单击数据库，并选择“删除”。</span><span class="sxs-lookup"><span data-stu-id="c447e-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="c447e-236">检查“关闭现有连接”。</span><span class="sxs-lookup"><span data-stu-id="c447e-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="c447e-237">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c447e-237">Select **OK**.</span></span>
* <span data-ttu-id="c447e-238">在 [PMC](xref:tutorials/razor-pages/new-field#pmc) 中更新数据库：</span><span class="sxs-lookup"><span data-stu-id="c447e-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c447e-239">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c447e-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="c447e-240">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="c447e-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="c447e-241">在本教程中，使用 Entity Framework Core 迁移功能（若可行）。</span><span class="sxs-lookup"><span data-stu-id="c447e-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="c447e-242">迁移会更新数据库架构，使其与数据模型中的更改相匹配。</span><span class="sxs-lookup"><span data-stu-id="c447e-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="c447e-243">但是，迁移仅能执行 EF Core 提供程序所支持的更改类型，且 SQLite 提供程序的功能将受限。</span><span class="sxs-lookup"><span data-stu-id="c447e-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="c447e-244">例如，支持添加列，但不支持删除或更改列。</span><span class="sxs-lookup"><span data-stu-id="c447e-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="c447e-245">如果已创建迁移以删除或更改列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。</span><span class="sxs-lookup"><span data-stu-id="c447e-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="c447e-246">由于上述限制，本教程不对 SQLite 架构更改使用迁移。</span><span class="sxs-lookup"><span data-stu-id="c447e-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="c447e-247">转而在架构更改时，放弃并重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="c447e-248">要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。</span><span class="sxs-lookup"><span data-stu-id="c447e-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="c447e-249">表重新生成涉及：</span><span class="sxs-lookup"><span data-stu-id="c447e-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="c447e-250">创建新表。</span><span class="sxs-lookup"><span data-stu-id="c447e-250">Creating a new table.</span></span>
>* <span data-ttu-id="c447e-251">将旧表中的数据复制到新表中。</span><span class="sxs-lookup"><span data-stu-id="c447e-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="c447e-252">放弃旧表。</span><span class="sxs-lookup"><span data-stu-id="c447e-252">Dropping the old table.</span></span>
>* <span data-ttu-id="c447e-253">为新表重命名。</span><span class="sxs-lookup"><span data-stu-id="c447e-253">Renaming the new table.</span></span>
>
><span data-ttu-id="c447e-254">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="c447e-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="c447e-255">SQLite EF Core 数据库提供程序限制</span><span class="sxs-lookup"><span data-stu-id="c447e-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="c447e-256">自定义迁移代码</span><span class="sxs-lookup"><span data-stu-id="c447e-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="c447e-257">数据种子设定</span><span class="sxs-lookup"><span data-stu-id="c447e-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="c447e-258">SQLite ALTER TABLE 语句</span><span class="sxs-lookup"><span data-stu-id="c447e-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="c447e-259">删除迁移文件夹。</span><span class="sxs-lookup"><span data-stu-id="c447e-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="c447e-260">使用以下命令重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="c447e-261">运行应用，并验证是否可以创建/编辑/显示具有 `Rating` 字段的电影。</span><span class="sxs-lookup"><span data-stu-id="c447e-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="c447e-262">如果数据库未设定种子，则在 `SeedData.Initialize` 方法中设置断点。</span><span class="sxs-lookup"><span data-stu-id="c447e-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c447e-263">其他资源</span><span class="sxs-lookup"><span data-stu-id="c447e-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c447e-264">[上一篇：添加搜索](xref:tutorials/razor-pages/search)
> [下一步：添加验证](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="c447e-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c447e-265">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="c447e-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c447e-266">在此部分中，[Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 迁移用于：</span><span class="sxs-lookup"><span data-stu-id="c447e-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="c447e-267">将新字段添加到模型。</span><span class="sxs-lookup"><span data-stu-id="c447e-267">Add a new field to the model.</span></span>
* <span data-ttu-id="c447e-268">将新字段架构更改迁移到数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="c447e-269">使用 EF Code First 自动创建数据库时，Code First 将：</span><span class="sxs-lookup"><span data-stu-id="c447e-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="c447e-270">向数据库添加 [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) 表格，以跟踪数据库的架构是否与从生成它的模型类同步。</span><span class="sxs-lookup"><span data-stu-id="c447e-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="c447e-271">如果该模型类未与数据库同步，EF 将引发异常。</span><span class="sxs-lookup"><span data-stu-id="c447e-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="c447e-272">自动验证架构与模型是否同步可以更容易地发现不一致的数据库代码问题。</span><span class="sxs-lookup"><span data-stu-id="c447e-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="c447e-273">向电影模型添加分级属性</span><span class="sxs-lookup"><span data-stu-id="c447e-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="c447e-274">打开 Models/Movie.cs 文件，并添加 `Rating` 属性：</span><span class="sxs-lookup"><span data-stu-id="c447e-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="c447e-275">构建应用程序。</span><span class="sxs-lookup"><span data-stu-id="c447e-275">Build the app.</span></span>

<span data-ttu-id="c447e-276">编辑 Pages/Movies/Index.cshtml，并添加 `Rating` 字段：</span><span class="sxs-lookup"><span data-stu-id="c447e-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="c447e-277">更新以下页面：</span><span class="sxs-lookup"><span data-stu-id="c447e-277">Update the following pages:</span></span>

* <span data-ttu-id="c447e-278">将 `Rating` 字段添加到“删除”和“详细信息”页面。</span><span class="sxs-lookup"><span data-stu-id="c447e-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="c447e-279">使用 `Rating` 字段更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="c447e-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="c447e-280">将 `Rating` 字段添加到“编辑”页面。</span><span class="sxs-lookup"><span data-stu-id="c447e-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="c447e-281">在数据库更新为包括新字段之前，应用将不会正常工作。</span><span class="sxs-lookup"><span data-stu-id="c447e-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="c447e-282">如果应用立即运行，应用会引发 `SqlException`：</span><span class="sxs-lookup"><span data-stu-id="c447e-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="c447e-283">此错误是由于更新的 Movie 模型类与数据库的 Movie 表架构不同导致的。</span><span class="sxs-lookup"><span data-stu-id="c447e-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="c447e-284">数据库表中没有 `Rating` 列。</span><span class="sxs-lookup"><span data-stu-id="c447e-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="c447e-285">可通过几种方法解决此错误：</span><span class="sxs-lookup"><span data-stu-id="c447e-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="c447e-286">让 Entity Framework 自动丢弃并使用新的模型类架构重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="c447e-287">此方法在开发周期早期很方便，通过该方法可以一起快速改进模型和数据库架构。</span><span class="sxs-lookup"><span data-stu-id="c447e-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="c447e-288">此方法的缺点是会导致数据库中的现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="c447e-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="c447e-289">请勿对生产数据库使用此方法！</span><span class="sxs-lookup"><span data-stu-id="c447e-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="c447e-290">在架构更改时丢弃数据库，并使用初始化表达式通过测试数据自动设定数据库种子，这通常是开发应用的有效方式。</span><span class="sxs-lookup"><span data-stu-id="c447e-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="c447e-291">对现有数据库架构进行显式修改，使它与模型类相匹配。</span><span class="sxs-lookup"><span data-stu-id="c447e-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="c447e-292">此方法的优点是可以保留数据。</span><span class="sxs-lookup"><span data-stu-id="c447e-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="c447e-293">可以手动或通过创建数据库更改脚本进行此更改。</span><span class="sxs-lookup"><span data-stu-id="c447e-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="c447e-294">使用 Code First 迁移更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="c447e-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="c447e-295">对于本教程，请使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="c447e-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="c447e-296">更新 `SeedData` 类，使它提供新列的值。</span><span class="sxs-lookup"><span data-stu-id="c447e-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="c447e-297">示例更改如下所示，但对每个 `new Movie` 块做出此更改。</span><span class="sxs-lookup"><span data-stu-id="c447e-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="c447e-298">请参阅[已完成的 SeedData.cs 文件](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)。</span><span class="sxs-lookup"><span data-stu-id="c447e-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="c447e-299">生成解决方案。</span><span class="sxs-lookup"><span data-stu-id="c447e-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c447e-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c447e-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="c447e-301">添加用于评级字段的迁移</span><span class="sxs-lookup"><span data-stu-id="c447e-301">Add a migration for the rating field</span></span>

<span data-ttu-id="c447e-302">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。 </span><span class="sxs-lookup"><span data-stu-id="c447e-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="c447e-303">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="c447e-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="c447e-304">`Add-Migration` 命令会通知框架执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c447e-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="c447e-305">将 `Movie` 模型与 `Movie` 数据库架构进行比较。</span><span class="sxs-lookup"><span data-stu-id="c447e-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="c447e-306">创建代码以将数据库架构迁移到新模型。</span><span class="sxs-lookup"><span data-stu-id="c447e-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="c447e-307">名称“Rating”是任意的，用于对迁移文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="c447e-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="c447e-308">为迁移文件使用有意义的名称是有帮助的。</span><span class="sxs-lookup"><span data-stu-id="c447e-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="c447e-309">`Update-Database` 命令指示框架将架构更改应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="c447e-310">如果删除数据库中的所有记录，初始化表达式会设定数据库种子，并将包括 `Rating` 字段。</span><span class="sxs-lookup"><span data-stu-id="c447e-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="c447e-311">可以使用浏览器中的删除链接，也可以从 [Sql Server 对象资源管理器](xref:tutorials/razor-pages/sql#ssox) (SSOX) 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="c447e-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="c447e-312">另一个方案是删除数据库，并使用迁移来重新创建该数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="c447e-313">删除 SSOX 中的数据库：</span><span class="sxs-lookup"><span data-stu-id="c447e-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="c447e-314">在 SSOX 中选择数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="c447e-315">右键单击数据库，并选择“删除”。</span><span class="sxs-lookup"><span data-stu-id="c447e-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="c447e-316">检查“关闭现有连接”。</span><span class="sxs-lookup"><span data-stu-id="c447e-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="c447e-317">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c447e-317">Select **OK**.</span></span>
* <span data-ttu-id="c447e-318">在 [PMC](xref:tutorials/razor-pages/new-field#pmc) 中更新数据库：</span><span class="sxs-lookup"><span data-stu-id="c447e-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c447e-319">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c447e-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="c447e-320">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="c447e-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="c447e-321">在本教程中，使用 Entity Framework Core 迁移功能（若可行）。</span><span class="sxs-lookup"><span data-stu-id="c447e-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="c447e-322">迁移会更新数据库架构，使其与数据模型中的更改相匹配。</span><span class="sxs-lookup"><span data-stu-id="c447e-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="c447e-323">但是，迁移仅能执行 EF Core 提供程序所支持的更改类型，且 SQLite 提供程序的功能将受限。</span><span class="sxs-lookup"><span data-stu-id="c447e-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="c447e-324">例如，支持添加列，但不支持删除或更改列。</span><span class="sxs-lookup"><span data-stu-id="c447e-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="c447e-325">如果已创建迁移以删除或更改列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。</span><span class="sxs-lookup"><span data-stu-id="c447e-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="c447e-326">由于上述限制，本教程不对 SQLite 架构更改使用迁移。</span><span class="sxs-lookup"><span data-stu-id="c447e-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="c447e-327">转而在架构更改时，放弃并重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="c447e-328">要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。</span><span class="sxs-lookup"><span data-stu-id="c447e-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="c447e-329">表重新生成涉及：</span><span class="sxs-lookup"><span data-stu-id="c447e-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="c447e-330">创建新表。</span><span class="sxs-lookup"><span data-stu-id="c447e-330">Creating a new table.</span></span>
>* <span data-ttu-id="c447e-331">将旧表中的数据复制到新表中。</span><span class="sxs-lookup"><span data-stu-id="c447e-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="c447e-332">放弃旧表。</span><span class="sxs-lookup"><span data-stu-id="c447e-332">Dropping the old table.</span></span>
>* <span data-ttu-id="c447e-333">为新表重命名。</span><span class="sxs-lookup"><span data-stu-id="c447e-333">Renaming the new table.</span></span>
>
><span data-ttu-id="c447e-334">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="c447e-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="c447e-335">SQLite EF Core 数据库提供程序限制</span><span class="sxs-lookup"><span data-stu-id="c447e-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="c447e-336">自定义迁移代码</span><span class="sxs-lookup"><span data-stu-id="c447e-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="c447e-337">数据种子设定</span><span class="sxs-lookup"><span data-stu-id="c447e-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="c447e-338">SQLite ALTER TABLE 语句</span><span class="sxs-lookup"><span data-stu-id="c447e-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="c447e-339">删除数据库并通过迁移重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="c447e-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="c447e-340">若要删除该数据库，请删除数据库文件 (MvcMovie.db)。</span><span class="sxs-lookup"><span data-stu-id="c447e-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="c447e-341">然后运行 `ef database update` 命令：</span><span class="sxs-lookup"><span data-stu-id="c447e-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="c447e-342">运行应用，并验证是否可以创建/编辑/显示具有 `Rating` 字段的电影。</span><span class="sxs-lookup"><span data-stu-id="c447e-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="c447e-343">如果数据库未设定种子，则在 `SeedData.Initialize` 方法中设置断点。</span><span class="sxs-lookup"><span data-stu-id="c447e-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c447e-344">其他资源</span><span class="sxs-lookup"><span data-stu-id="c447e-344">Additional resources</span></span>

* [<span data-ttu-id="c447e-345">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="c447e-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="c447e-346">[上一篇：添加搜索](xref:tutorials/razor-pages/search)
> [下一步：添加验证](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="c447e-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
