---
title: 第 4 部分：使用数据库
author: rick-anderson
description: Razor 页面教程系列的第 4 部分。
ms.author: riande
ms.date: 09/26/2020
ms.custom: contperf-fy21q2
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 552a74016c281af248ce735c6ed6a5a55768a16a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486235"
---
# <a name="part-4-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="360c0-103">Razor 页面教程系列第 4 部分</span><span class="sxs-lookup"><span data-stu-id="360c0-103">Part 4 of tutorial series on Razor Pages</span></span>

<span data-ttu-id="360c0-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="360c0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="360c0-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="360c0-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="360c0-106">`RazorPagesMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="360c0-106">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="360c0-107">在 Startup.cs 的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="360c0-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="360c0-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="360c0-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="360c0-109">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="360c0-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="360c0-110">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString` 键。</span><span class="sxs-lookup"><span data-stu-id="360c0-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="360c0-111">进行本地开发时，配置从 appsettings.json 文件获取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="360c0-111">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="360c0-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="360c0-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="360c0-113">生成的连接字符串将类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="360c0-113">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="360c0-114">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="360c0-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="360c0-115">将应用部署到测试或生产服务器时，可以使用环境变量将连接字符串设置为测试或生产数据库服务器。</span><span class="sxs-lookup"><span data-stu-id="360c0-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="360c0-116">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="360c0-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="360c0-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="360c0-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="360c0-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="360c0-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="360c0-119">LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。</span><span class="sxs-lookup"><span data-stu-id="360c0-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="360c0-120">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="360c0-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="360c0-121">默认情况下，LocalDB 数据库在 `C:\Users\<user>\` 目录下创建 `*.mdf` 文件。</span><span class="sxs-lookup"><span data-stu-id="360c0-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="360c0-122">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="360c0-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![“视图”菜单](sql/_static/5/ssox.png)

1. <span data-ttu-id="360c0-124">右键单击 `Movie` 表，然后选择“视图设计器”：</span><span class="sxs-lookup"><span data-stu-id="360c0-124">Right-click on the `Movie` table and select **View Designer**:</span></span>

   ![Movie 表上打开的上下文菜单](sql/_static/5/design.png)

   ![设计器中打开的 Movie 表](sql/_static/dv.png)

   <span data-ttu-id="360c0-127">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="360c0-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="360c0-128">默认情况下，EF 为该主键创建一个名为 `ID` 的属性。</span><span class="sxs-lookup"><span data-stu-id="360c0-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="360c0-129">右键单击 `Movie` 表，然后选择“查看数据”：</span><span class="sxs-lookup"><span data-stu-id="360c0-129">Right-click on the `Movie` table and select **View Data**:</span></span>

   ![显示表数据的打开的 Movie 表](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="360c0-131">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="360c0-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="360c0-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="360c0-132">SQLite</span></span>

<span data-ttu-id="360c0-133">[SQLite](https://www.sqlite.org/) 网站上表示：</span><span class="sxs-lookup"><span data-stu-id="360c0-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="360c0-134">SQLite 是一个自包含、高可靠性、嵌入式、功能完整、公共域的 SQL 数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="360c0-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="360c0-135">SQLite 是世界上使用最多的数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="360c0-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="360c0-136">可以下载许多第三方工具来管理并查看 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="360c0-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="360c0-137">下面的图片来自 [DB Browser for SQLite](https://sqlitebrowser.org/)。</span><span class="sxs-lookup"><span data-stu-id="360c0-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="360c0-138">如果你有最喜欢的 SQLite 工具，请发表评论以分享你喜欢的方面。</span><span class="sxs-lookup"><span data-stu-id="360c0-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![显示电影数据库的 DB Browser for SQLite](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="360c0-140">在本教程中，使用 Entity Framework Core 迁移功能（若可行）。</span><span class="sxs-lookup"><span data-stu-id="360c0-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="360c0-141">迁移会更新数据库架构，使其与数据模型中的更改相匹配。</span><span class="sxs-lookup"><span data-stu-id="360c0-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="360c0-142">但是，迁移仅能执行 EF Core 提供程序所支持的更改类型，且 SQLite 提供程序的功能将受限。</span><span class="sxs-lookup"><span data-stu-id="360c0-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="360c0-143">例如，支持添加列，但不支持删除或更改列。</span><span class="sxs-lookup"><span data-stu-id="360c0-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="360c0-144">如果已创建迁移以删除或更改列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。</span><span class="sxs-lookup"><span data-stu-id="360c0-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="360c0-145">由于上述限制，本教程不对 SQLite 架构更改使用迁移。</span><span class="sxs-lookup"><span data-stu-id="360c0-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="360c0-146">转而在架构更改时，放弃并重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="360c0-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="360c0-147">要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。</span><span class="sxs-lookup"><span data-stu-id="360c0-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="360c0-148">表重新生成涉及：</span><span class="sxs-lookup"><span data-stu-id="360c0-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="360c0-149">创建新表。</span><span class="sxs-lookup"><span data-stu-id="360c0-149">Creating a new table.</span></span>
>* <span data-ttu-id="360c0-150">将旧表中的数据复制到新表中。</span><span class="sxs-lookup"><span data-stu-id="360c0-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="360c0-151">放弃旧表。</span><span class="sxs-lookup"><span data-stu-id="360c0-151">Dropping the old table.</span></span>
>* <span data-ttu-id="360c0-152">为新表重命名。</span><span class="sxs-lookup"><span data-stu-id="360c0-152">Renaming the new table.</span></span>
>
><span data-ttu-id="360c0-153">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="360c0-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="360c0-154">SQLite EF Core 数据库提供程序限制</span><span class="sxs-lookup"><span data-stu-id="360c0-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="360c0-155">自定义迁移代码</span><span class="sxs-lookup"><span data-stu-id="360c0-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="360c0-156">数据种子设定</span><span class="sxs-lookup"><span data-stu-id="360c0-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="360c0-157">SQLite ALTER TABLE 语句</span><span class="sxs-lookup"><span data-stu-id="360c0-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="360c0-158">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="360c0-158">Seed the database</span></span>

<span data-ttu-id="360c0-159">使用以下代码在 Models 文件夹中创建一个名为 `SeedData` 的新类：</span><span class="sxs-lookup"><span data-stu-id="360c0-159">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="360c0-160">如果数据库中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="360c0-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="360c0-161">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="360c0-161">Add the seed initializer</span></span>

<span data-ttu-id="360c0-162">将 Program.cs 的内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="360c0-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

<span data-ttu-id="360c0-163">前面的代码修改了 `Main` 方法，以便执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="360c0-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="360c0-164">从依赖注入容器中获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="360c0-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="360c0-165">调用 `seedData.Initialize` 方法，并向其传递数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="360c0-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="360c0-166">Seed 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="360c0-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="360c0-167">[using 语句](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)将确保释放上下文。</span><span class="sxs-lookup"><span data-stu-id="360c0-167">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="360c0-168">未运行 `Update-Database` 时出现以下异常：</span><span class="sxs-lookup"><span data-stu-id="360c0-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="360c0-169">测试应用</span><span class="sxs-lookup"><span data-stu-id="360c0-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="360c0-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="360c0-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="360c0-171">删除数据库中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="360c0-171">Delete all the records in the database.</span></span> <span data-ttu-id="360c0-172">使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作</span><span class="sxs-lookup"><span data-stu-id="360c0-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="360c0-173">通过调用 `Startup` 类中的方法强制应用初始化，使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="360c0-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="360c0-174">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="360c0-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="360c0-175">使用以下任一方法停止并重启 IIS：</span><span class="sxs-lookup"><span data-stu-id="360c0-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="360c0-176">右键单击通知区域中的 IIS Express 系统任务栏图标，然后选择“退出”或“停止站点”：</span><span class="sxs-lookup"><span data-stu-id="360c0-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site**:</span></span>

      ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![上下文菜单](sql/_static/stopIIS.png)

   1. <span data-ttu-id="360c0-179">如果应用是在非调试模式下运行的，请按 F5<kbd></kbd> 以在调试模式下运行。</span><span class="sxs-lookup"><span data-stu-id="360c0-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="360c0-180">如果应用处于调试模式下，请停止调试程序并按 F5<kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="360c0-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="360c0-181">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="360c0-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="360c0-182">删除数据库中的所有记录，使种子方法运行。</span><span class="sxs-lookup"><span data-stu-id="360c0-182">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="360c0-183">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="360c0-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="360c0-184">应用将显示设定为种子的数据：</span><span class="sxs-lookup"><span data-stu-id="360c0-184">The app shows the seeded data:</span></span>

![在浏览器中打开的显示电影数据的电影应用程序](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="360c0-186">其他资源</span><span class="sxs-lookup"><span data-stu-id="360c0-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="360c0-187">[上一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)
> [下一篇：更新页面](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="360c0-187">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="360c0-188">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="360c0-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="360c0-189">`RazorPagesMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="360c0-189">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="360c0-190">在 Startup.cs 的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="360c0-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="360c0-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="360c0-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="360c0-192">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="360c0-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="360c0-193">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString` 键。</span><span class="sxs-lookup"><span data-stu-id="360c0-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="360c0-194">进行本地开发时，配置从 appsettings.json 文件获取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="360c0-194">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="360c0-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="360c0-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="360c0-196">生成的连接字符串将类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="360c0-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="360c0-197">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="360c0-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="360c0-198">将应用部署到测试或生产服务器时，可以使用环境变量将连接字符串设置为测试或生产数据库服务器。</span><span class="sxs-lookup"><span data-stu-id="360c0-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="360c0-199">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="360c0-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="360c0-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="360c0-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="360c0-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="360c0-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="360c0-202">LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。</span><span class="sxs-lookup"><span data-stu-id="360c0-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="360c0-203">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="360c0-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="360c0-204">默认情况下，LocalDB 数据库在 `C:\Users\<user>\` 目录下创建 `*.mdf` 文件。</span><span class="sxs-lookup"><span data-stu-id="360c0-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="360c0-205">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="360c0-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![“视图”菜单](sql/_static/ssox.png)

* <span data-ttu-id="360c0-207">右键单击 `Movie` 表，然后选择“视图设计器”：</span><span class="sxs-lookup"><span data-stu-id="360c0-207">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Movie 表上打开的上下文菜单](sql/_static/design.png)

  ![设计器中打开的 Movie 表](sql/_static/dv.png)

<span data-ttu-id="360c0-210">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="360c0-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="360c0-211">默认情况下，EF 为该主键创建一个名为 `ID` 的属性。</span><span class="sxs-lookup"><span data-stu-id="360c0-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="360c0-212">右键单击 `Movie` 表，然后选择“查看数据”：</span><span class="sxs-lookup"><span data-stu-id="360c0-212">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![显示表数据的打开的 Movie 表](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="360c0-214">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="360c0-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="360c0-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="360c0-215">SQLite</span></span>

<span data-ttu-id="360c0-216">[SQLite](https://www.sqlite.org/) 网站上表示：</span><span class="sxs-lookup"><span data-stu-id="360c0-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="360c0-217">SQLite 是一个自包含、高可靠性、嵌入式、功能完整、公共域的 SQL 数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="360c0-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="360c0-218">SQLite 是世界上使用最多的数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="360c0-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="360c0-219">可以下载许多第三方工具来管理并查看 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="360c0-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="360c0-220">下面的图片来自 [DB Browser for SQLite](https://sqlitebrowser.org/)。</span><span class="sxs-lookup"><span data-stu-id="360c0-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="360c0-221">如果你有最喜欢的 SQLite 工具，请发表评论以分享你喜欢的方面。</span><span class="sxs-lookup"><span data-stu-id="360c0-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![显示电影数据库的 DB Browser for SQLite](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="360c0-223">在本教程中，使用 Entity Framework Core 迁移功能（若可行）。</span><span class="sxs-lookup"><span data-stu-id="360c0-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="360c0-224">迁移会更新数据库架构，使其与数据模型中的更改相匹配。</span><span class="sxs-lookup"><span data-stu-id="360c0-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="360c0-225">但是，迁移仅能执行 EF Core 提供程序所支持的更改类型，且 SQLite 提供程序的功能将受限。</span><span class="sxs-lookup"><span data-stu-id="360c0-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="360c0-226">例如，支持添加列，但不支持删除或更改列。</span><span class="sxs-lookup"><span data-stu-id="360c0-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="360c0-227">如果已创建迁移以删除或更改列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。</span><span class="sxs-lookup"><span data-stu-id="360c0-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="360c0-228">由于上述限制，本教程不对 SQLite 架构更改使用迁移。</span><span class="sxs-lookup"><span data-stu-id="360c0-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="360c0-229">转而在架构更改时，放弃并重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="360c0-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="360c0-230">要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。</span><span class="sxs-lookup"><span data-stu-id="360c0-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="360c0-231">表重新生成涉及：</span><span class="sxs-lookup"><span data-stu-id="360c0-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="360c0-232">创建新表。</span><span class="sxs-lookup"><span data-stu-id="360c0-232">Creating a new table.</span></span>
>* <span data-ttu-id="360c0-233">将旧表中的数据复制到新表中。</span><span class="sxs-lookup"><span data-stu-id="360c0-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="360c0-234">放弃旧表。</span><span class="sxs-lookup"><span data-stu-id="360c0-234">Dropping the old table.</span></span>
>* <span data-ttu-id="360c0-235">为新表重命名。</span><span class="sxs-lookup"><span data-stu-id="360c0-235">Renaming the new table.</span></span>
>
><span data-ttu-id="360c0-236">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="360c0-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="360c0-237">SQLite EF Core 数据库提供程序限制</span><span class="sxs-lookup"><span data-stu-id="360c0-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="360c0-238">自定义迁移代码</span><span class="sxs-lookup"><span data-stu-id="360c0-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="360c0-239">数据种子设定</span><span class="sxs-lookup"><span data-stu-id="360c0-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="360c0-240">SQLite ALTER TABLE 语句</span><span class="sxs-lookup"><span data-stu-id="360c0-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="360c0-241">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="360c0-241">Seed the database</span></span>

<span data-ttu-id="360c0-242">使用以下代码在 Models 文件夹中创建一个名为 `SeedData` 的新类：</span><span class="sxs-lookup"><span data-stu-id="360c0-242">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="360c0-243">如果数据库中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="360c0-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="360c0-244">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="360c0-244">Add the seed initializer</span></span>

<span data-ttu-id="360c0-245">将 Program.cs 的内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="360c0-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="360c0-246">前面的代码修改了 `Main` 方法，以便执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="360c0-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="360c0-247">从依赖注入容器中获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="360c0-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="360c0-248">调用 `seedData.Initialize` 方法，并向其传递数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="360c0-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="360c0-249">Seed 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="360c0-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="360c0-250">[using 语句](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)将确保释放上下文。</span><span class="sxs-lookup"><span data-stu-id="360c0-250">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="360c0-251">未运行 `Update-Database` 时出现以下异常：</span><span class="sxs-lookup"><span data-stu-id="360c0-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="360c0-252">测试应用</span><span class="sxs-lookup"><span data-stu-id="360c0-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="360c0-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="360c0-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="360c0-254">删除数据库中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="360c0-254">Delete all the records in the database.</span></span> <span data-ttu-id="360c0-255">使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="360c0-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="360c0-256">通过调用 `Startup` 类中的方法强制应用初始化，使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="360c0-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="360c0-257">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="360c0-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="360c0-258">使用以下任一方法停止并重启 IIS：</span><span class="sxs-lookup"><span data-stu-id="360c0-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="360c0-259">右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点” ：</span><span class="sxs-lookup"><span data-stu-id="360c0-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](sql/_static/stopIIS.png)

    * <span data-ttu-id="360c0-262">如果应用是在非调试模式下运行的，请按 F5<kbd></kbd> 以在调试模式下运行。</span><span class="sxs-lookup"><span data-stu-id="360c0-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="360c0-263">如果应用处于调试模式下，请停止调试程序并按 F5<kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="360c0-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="360c0-264">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="360c0-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="360c0-265">删除数据库中的所有记录，使种子方法运行。</span><span class="sxs-lookup"><span data-stu-id="360c0-265">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="360c0-266">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="360c0-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="360c0-267">应用将显示设定为种子的数据：</span><span class="sxs-lookup"><span data-stu-id="360c0-267">The app shows the seeded data:</span></span>

![在 Chrome 中打开的显示电影数据的电影应用程序](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="360c0-269">其他资源</span><span class="sxs-lookup"><span data-stu-id="360c0-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="360c0-270">[上一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)
> [下一篇：更新页面](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="360c0-270">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="360c0-271">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="360c0-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="360c0-272">`RazorPagesMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="360c0-272">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="360c0-273">在 Startup.cs 的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="360c0-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="360c0-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="360c0-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="360c0-275">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="360c0-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="360c0-276">有关 `ConfigureServices` 中使用的方法的详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="360c0-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="360c0-277">面向 `CookiePolicyOptions` 的 [ASP.NET Core 中的欧盟一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="360c0-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="360c0-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="360c0-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="360c0-279">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString` 键。</span><span class="sxs-lookup"><span data-stu-id="360c0-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="360c0-280">进行本地开发时，配置从 appsettings.json 文件获取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="360c0-280">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="360c0-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="360c0-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="360c0-282">生成的连接字符串将类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="360c0-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="360c0-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="360c0-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="360c0-284">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="360c0-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="360c0-285">将应用部署到测试或生产服务器时，可以使用环境变量将连接字符串设置为测试或生产数据库服务器。</span><span class="sxs-lookup"><span data-stu-id="360c0-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="360c0-286">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="360c0-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="360c0-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="360c0-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="360c0-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="360c0-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="360c0-289">LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。</span><span class="sxs-lookup"><span data-stu-id="360c0-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="360c0-290">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="360c0-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="360c0-291">默认情况下，LocalDB 数据库在 `C:/Users/<user/>` 目录下创建 `*.mdf` 文件。</span><span class="sxs-lookup"><span data-stu-id="360c0-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="360c0-292">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="360c0-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![“视图”菜单](sql/_static/ssox.png)

* <span data-ttu-id="360c0-294">右键单击 `Movie` 表，然后选择“视图设计器”：</span><span class="sxs-lookup"><span data-stu-id="360c0-294">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Movie 表上打开的上下文菜单](sql/_static/design.png)

  ![设计器中打开的 Movie 表](sql/_static/dv.png)

<span data-ttu-id="360c0-297">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="360c0-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="360c0-298">默认情况下，EF 为该主键创建一个名为 `ID` 的属性。</span><span class="sxs-lookup"><span data-stu-id="360c0-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="360c0-299">右键单击 `Movie` 表，然后选择“查看数据”：</span><span class="sxs-lookup"><span data-stu-id="360c0-299">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![显示表数据的打开的 Movie 表](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="360c0-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="360c0-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="360c0-302">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="360c0-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="360c0-303">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="360c0-303">Seed the database</span></span>

<span data-ttu-id="360c0-304">使用以下代码在 Models 文件夹中创建一个名为 `SeedData` 的新类：</span><span class="sxs-lookup"><span data-stu-id="360c0-304">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="360c0-305">如果数据库中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="360c0-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="360c0-306">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="360c0-306">Add the seed initializer</span></span>

<span data-ttu-id="360c0-307">将 Program.cs 的内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="360c0-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="360c0-308">前面的代码修改了 `Main` 方法，以便执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="360c0-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="360c0-309">从依赖注入容器中获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="360c0-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="360c0-310">调用 `seedData.Initialize` 方法，并向其传递数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="360c0-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="360c0-311">Seed 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="360c0-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="360c0-312">[using 语句](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)将确保释放上下文。</span><span class="sxs-lookup"><span data-stu-id="360c0-312">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="360c0-313">生产应用不会调用 `Database.Migrate`。</span><span class="sxs-lookup"><span data-stu-id="360c0-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="360c0-314">它会添加到上面的代码中，以防止在未运行 `Update-Database` 时出现以下异常：</span><span class="sxs-lookup"><span data-stu-id="360c0-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="360c0-315">SqlException：无法打开登录请求的数据库“RazorPagesMovieContext-21”。</span><span class="sxs-lookup"><span data-stu-id="360c0-315">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="360c0-316">登录失败。</span><span class="sxs-lookup"><span data-stu-id="360c0-316">The login failed.</span></span>
<span data-ttu-id="360c0-317">用户“用户名”登录失败。</span><span class="sxs-lookup"><span data-stu-id="360c0-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="360c0-318">测试应用</span><span class="sxs-lookup"><span data-stu-id="360c0-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="360c0-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="360c0-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="360c0-320">删除数据库中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="360c0-320">Delete all the records in the database.</span></span> <span data-ttu-id="360c0-321">可以使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作</span><span class="sxs-lookup"><span data-stu-id="360c0-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="360c0-322">通过调用 `Startup` 类中的方法强制应用初始化，使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="360c0-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="360c0-323">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="360c0-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="360c0-324">可以使用以下任一方法来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="360c0-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="360c0-325">右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点” ：</span><span class="sxs-lookup"><span data-stu-id="360c0-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](sql/_static/stopIIS.png)

    * <span data-ttu-id="360c0-328">如果应用是在非调试模式下运行的，请按 F5<kbd></kbd> 以在调试模式下运行。</span><span class="sxs-lookup"><span data-stu-id="360c0-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="360c0-329">如果应用处于调试模式下，请停止调试程序并按 F5<kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="360c0-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="360c0-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="360c0-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="360c0-331">删除数据库中的所有记录，使种子方法运行。</span><span class="sxs-lookup"><span data-stu-id="360c0-331">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="360c0-332">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="360c0-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="360c0-333">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="360c0-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="360c0-334">删除数据库中的所有记录，使种子方法运行。</span><span class="sxs-lookup"><span data-stu-id="360c0-334">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="360c0-335">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="360c0-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="360c0-336">应用将显示设定为种子的数据：</span><span class="sxs-lookup"><span data-stu-id="360c0-336">The app shows the seeded data:</span></span>

![在 Chrome 中打开的显示电影数据的电影应用程序](sql/_static/m55https.png)

<span data-ttu-id="360c0-338">在下一教程中将对数据的展示进行整理。</span><span class="sxs-lookup"><span data-stu-id="360c0-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="360c0-339">其他资源</span><span class="sxs-lookup"><span data-stu-id="360c0-339">Additional resources</span></span>

* [<span data-ttu-id="360c0-340">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="360c0-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="360c0-341">[上一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)
> [下一篇：更新页面](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="360c0-341">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
