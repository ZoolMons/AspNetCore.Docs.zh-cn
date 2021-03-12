---
title: 第 4 部分：使用数据库
author: rick-anderson
description: Razor 页面教程系列的第 4 部分。
ms.author: riande
ms.date: 01/05/2021
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
ms.openlocfilehash: 3133ff7a192f5e3bdec4f6f1e6e6d55f966d7e64
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589759"
---
# <a name="part-4-of-tutorial-series-on-razor-pages"></a><span data-ttu-id="cae68-103">Razor 页面教程系列第 4 部分</span><span class="sxs-lookup"><span data-stu-id="cae68-103">Part 4 of tutorial series on Razor Pages</span></span>

<span data-ttu-id="cae68-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="cae68-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cae68-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="cae68-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cae68-106">`RazorPagesMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="cae68-106">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="cae68-107">在 Startup.cs 的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="cae68-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cae68-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cae68-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cae68-109">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cae68-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="cae68-110">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString` 键。</span><span class="sxs-lookup"><span data-stu-id="cae68-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="cae68-111">进行本地开发时，配置从 appsettings.json 文件获取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="cae68-111">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cae68-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cae68-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cae68-113">生成的连接字符串类似于以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="cae68-113">The generated connection string is similar to the following JSON:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie50/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cae68-114">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cae68-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="cae68-115">将应用部署到测试或生产服务器时，可以使用环境变量将连接字符串设置为测试或生产数据库服务器。</span><span class="sxs-lookup"><span data-stu-id="cae68-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="cae68-116">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="cae68-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cae68-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cae68-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="cae68-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="cae68-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="cae68-119">LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。</span><span class="sxs-lookup"><span data-stu-id="cae68-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="cae68-120">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="cae68-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="cae68-121">默认情况下，LocalDB 数据库在 `C:\Users\<user>\` 目录下创建 `*.mdf` 文件。</span><span class="sxs-lookup"><span data-stu-id="cae68-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="cae68-122">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="cae68-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![“视图”菜单](sql/_static/5/ssox.png)

1. <span data-ttu-id="cae68-124">右键单击 `Movie` 表，然后选择“视图设计器”：</span><span class="sxs-lookup"><span data-stu-id="cae68-124">Right-click on the `Movie` table and select **View Designer**:</span></span>

   ![Movie 表上打开的上下文菜单](sql/_static/5/design.png)

   ![设计器中打开的 Movie 表](sql/_static/dv.png)

   <span data-ttu-id="cae68-127">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="cae68-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="cae68-128">默认情况下，EF 为该主键创建一个名为 `ID` 的属性。</span><span class="sxs-lookup"><span data-stu-id="cae68-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="cae68-129">右键单击 `Movie` 表，然后选择“查看数据”：</span><span class="sxs-lookup"><span data-stu-id="cae68-129">Right-click on the `Movie` table and select **View Data**:</span></span>

   ![显示表数据的打开的 Movie 表](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cae68-131">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cae68-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="cae68-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="cae68-132">SQLite</span></span>

<span data-ttu-id="cae68-133">[SQLite](https://www.sqlite.org/) 网站上表示：</span><span class="sxs-lookup"><span data-stu-id="cae68-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="cae68-134">SQLite 是一个自包含、高可靠性、嵌入式、功能完整、公共域的 SQL 数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="cae68-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="cae68-135">SQLite 是世界上使用最多的数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="cae68-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="cae68-136">可以下载许多第三方工具来管理并查看 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="cae68-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="cae68-137">下面的图片来自 [DB Browser for SQLite](https://sqlitebrowser.org/)。</span><span class="sxs-lookup"><span data-stu-id="cae68-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="cae68-138">如果你有最喜欢的 SQLite 工具，请发表评论以分享你喜欢的方面。</span><span class="sxs-lookup"><span data-stu-id="cae68-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![显示电影数据库的 DB Browser for SQLite](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="cae68-140">在本教程中，使用 Entity Framework Core 迁移功能（若可行）。</span><span class="sxs-lookup"><span data-stu-id="cae68-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="cae68-141">迁移会更新数据库架构，使其与数据模型中的更改相匹配。</span><span class="sxs-lookup"><span data-stu-id="cae68-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="cae68-142">但是，迁移仅能执行 EF Core 提供程序所支持的更改类型，且 SQLite 提供程序的功能将受限。</span><span class="sxs-lookup"><span data-stu-id="cae68-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="cae68-143">例如，支持添加列，但不支持删除或更改列。</span><span class="sxs-lookup"><span data-stu-id="cae68-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="cae68-144">如果已创建迁移以删除或更改列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。</span><span class="sxs-lookup"><span data-stu-id="cae68-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="cae68-145">由于上述限制，本教程不对 SQLite 架构更改使用迁移。</span><span class="sxs-lookup"><span data-stu-id="cae68-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="cae68-146">转而在架构更改时，放弃并重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="cae68-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="cae68-147">要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。</span><span class="sxs-lookup"><span data-stu-id="cae68-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="cae68-148">表重新生成涉及：</span><span class="sxs-lookup"><span data-stu-id="cae68-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="cae68-149">创建新表。</span><span class="sxs-lookup"><span data-stu-id="cae68-149">Creating a new table.</span></span>
>* <span data-ttu-id="cae68-150">将旧表中的数据复制到新表中。</span><span class="sxs-lookup"><span data-stu-id="cae68-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="cae68-151">放弃旧表。</span><span class="sxs-lookup"><span data-stu-id="cae68-151">Dropping the old table.</span></span>
>* <span data-ttu-id="cae68-152">为新表重命名。</span><span class="sxs-lookup"><span data-stu-id="cae68-152">Renaming the new table.</span></span>
>
><span data-ttu-id="cae68-153">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="cae68-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="cae68-154">SQLite EF Core 数据库提供程序限制</span><span class="sxs-lookup"><span data-stu-id="cae68-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="cae68-155">自定义迁移代码</span><span class="sxs-lookup"><span data-stu-id="cae68-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="cae68-156">数据种子设定</span><span class="sxs-lookup"><span data-stu-id="cae68-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="cae68-157">SQLite ALTER TABLE 语句</span><span class="sxs-lookup"><span data-stu-id="cae68-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="cae68-158">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="cae68-158">Seed the database</span></span>

<span data-ttu-id="cae68-159">使用以下代码在 Models 文件夹中创建一个名为 `SeedData` 的新类：</span><span class="sxs-lookup"><span data-stu-id="cae68-159">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="cae68-160">如果数据库中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="cae68-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="cae68-161">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="cae68-161">Add the seed initializer</span></span>

<span data-ttu-id="cae68-162">将 Program.cs 的内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="cae68-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

<span data-ttu-id="cae68-163">前面的代码修改了 `Main` 方法，以便执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cae68-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="cae68-164">从依赖注入容器中获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="cae68-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="cae68-165">调用 `seedData.Initialize` 方法，并向其传递数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="cae68-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="cae68-166">Seed 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="cae68-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="cae68-167">[using 语句](/dotnet/csharp/language-reference/keywords/using-statement)将确保释放上下文。</span><span class="sxs-lookup"><span data-stu-id="cae68-167">The [using statement](/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="cae68-168">未运行 `Update-Database` 时出现以下异常：</span><span class="sxs-lookup"><span data-stu-id="cae68-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="cae68-169">测试应用</span><span class="sxs-lookup"><span data-stu-id="cae68-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cae68-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cae68-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cae68-171">删除数据库中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="cae68-171">Delete all the records in the database.</span></span> <span data-ttu-id="cae68-172">使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作</span><span class="sxs-lookup"><span data-stu-id="cae68-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="cae68-173">通过调用 `Startup` 类中的方法强制应用初始化，使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="cae68-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="cae68-174">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="cae68-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="cae68-175">使用以下任一方法停止并重启 IIS：</span><span class="sxs-lookup"><span data-stu-id="cae68-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="cae68-176">右键单击通知区域中的 IIS Express 系统任务栏图标，然后选择“退出”或“停止站点”：</span><span class="sxs-lookup"><span data-stu-id="cae68-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site**:</span></span>

      ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![上下文菜单](sql/_static/stopIIS.png)

   1. <span data-ttu-id="cae68-179">如果应用是在非调试模式下运行的，请按 F5<kbd></kbd> 以在调试模式下运行。</span><span class="sxs-lookup"><span data-stu-id="cae68-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="cae68-180">如果应用处于调试模式下，请停止调试程序并按 F5<kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="cae68-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cae68-181">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cae68-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="cae68-182">删除数据库中的所有记录，使种子方法运行。</span><span class="sxs-lookup"><span data-stu-id="cae68-182">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="cae68-183">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="cae68-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="cae68-184">应用将显示设定为种子的数据：</span><span class="sxs-lookup"><span data-stu-id="cae68-184">The app shows the seeded data:</span></span>

![在浏览器中打开的显示电影数据的电影应用程序](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="cae68-186">其他资源</span><span class="sxs-lookup"><span data-stu-id="cae68-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cae68-187">[上一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)
> [下一篇：更新页面](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="cae68-187">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="cae68-188">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="cae68-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cae68-189">`RazorPagesMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="cae68-189">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="cae68-190">在 Startup.cs 的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="cae68-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cae68-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cae68-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cae68-192">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cae68-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="cae68-193">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString` 键。</span><span class="sxs-lookup"><span data-stu-id="cae68-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="cae68-194">进行本地开发时，配置从 appsettings.json 文件获取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="cae68-194">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cae68-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cae68-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cae68-196">生成的连接字符串将类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="cae68-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cae68-197">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cae68-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="cae68-198">将应用部署到测试或生产服务器时，可以使用环境变量将连接字符串设置为测试或生产数据库服务器。</span><span class="sxs-lookup"><span data-stu-id="cae68-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="cae68-199">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="cae68-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cae68-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cae68-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="cae68-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="cae68-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="cae68-202">LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。</span><span class="sxs-lookup"><span data-stu-id="cae68-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="cae68-203">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="cae68-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="cae68-204">默认情况下，LocalDB 数据库在 `C:\Users\<user>\` 目录下创建 `*.mdf` 文件。</span><span class="sxs-lookup"><span data-stu-id="cae68-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="cae68-205">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="cae68-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![“视图”菜单](sql/_static/ssox.png)

* <span data-ttu-id="cae68-207">右键单击 `Movie` 表，然后选择“视图设计器”：</span><span class="sxs-lookup"><span data-stu-id="cae68-207">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Movie 表上打开的上下文菜单](sql/_static/design.png)

  ![设计器中打开的 Movie 表](sql/_static/dv.png)

<span data-ttu-id="cae68-210">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="cae68-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="cae68-211">默认情况下，EF 为该主键创建一个名为 `ID` 的属性。</span><span class="sxs-lookup"><span data-stu-id="cae68-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="cae68-212">右键单击 `Movie` 表，然后选择“查看数据”：</span><span class="sxs-lookup"><span data-stu-id="cae68-212">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![显示表数据的打开的 Movie 表](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cae68-214">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cae68-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="cae68-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="cae68-215">SQLite</span></span>

<span data-ttu-id="cae68-216">[SQLite](https://www.sqlite.org/) 网站上表示：</span><span class="sxs-lookup"><span data-stu-id="cae68-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="cae68-217">SQLite 是一个自包含、高可靠性、嵌入式、功能完整、公共域的 SQL 数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="cae68-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="cae68-218">SQLite 是世界上使用最多的数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="cae68-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="cae68-219">可以下载许多第三方工具来管理并查看 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="cae68-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="cae68-220">下面的图片来自 [DB Browser for SQLite](https://sqlitebrowser.org/)。</span><span class="sxs-lookup"><span data-stu-id="cae68-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="cae68-221">如果你有最喜欢的 SQLite 工具，请发表评论以分享你喜欢的方面。</span><span class="sxs-lookup"><span data-stu-id="cae68-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![显示电影数据库的 DB Browser for SQLite](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="cae68-223">在本教程中，使用 Entity Framework Core 迁移功能（若可行）。</span><span class="sxs-lookup"><span data-stu-id="cae68-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="cae68-224">迁移会更新数据库架构，使其与数据模型中的更改相匹配。</span><span class="sxs-lookup"><span data-stu-id="cae68-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="cae68-225">但是，迁移仅能执行 EF Core 提供程序所支持的更改类型，且 SQLite 提供程序的功能将受限。</span><span class="sxs-lookup"><span data-stu-id="cae68-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="cae68-226">例如，支持添加列，但不支持删除或更改列。</span><span class="sxs-lookup"><span data-stu-id="cae68-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="cae68-227">如果已创建迁移以删除或更改列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。</span><span class="sxs-lookup"><span data-stu-id="cae68-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="cae68-228">由于上述限制，本教程不对 SQLite 架构更改使用迁移。</span><span class="sxs-lookup"><span data-stu-id="cae68-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="cae68-229">转而在架构更改时，放弃并重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="cae68-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="cae68-230">要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。</span><span class="sxs-lookup"><span data-stu-id="cae68-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="cae68-231">表重新生成涉及：</span><span class="sxs-lookup"><span data-stu-id="cae68-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="cae68-232">创建新表。</span><span class="sxs-lookup"><span data-stu-id="cae68-232">Creating a new table.</span></span>
>* <span data-ttu-id="cae68-233">将旧表中的数据复制到新表中。</span><span class="sxs-lookup"><span data-stu-id="cae68-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="cae68-234">放弃旧表。</span><span class="sxs-lookup"><span data-stu-id="cae68-234">Dropping the old table.</span></span>
>* <span data-ttu-id="cae68-235">为新表重命名。</span><span class="sxs-lookup"><span data-stu-id="cae68-235">Renaming the new table.</span></span>
>
><span data-ttu-id="cae68-236">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="cae68-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="cae68-237">SQLite EF Core 数据库提供程序限制</span><span class="sxs-lookup"><span data-stu-id="cae68-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="cae68-238">自定义迁移代码</span><span class="sxs-lookup"><span data-stu-id="cae68-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="cae68-239">数据种子设定</span><span class="sxs-lookup"><span data-stu-id="cae68-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="cae68-240">SQLite ALTER TABLE 语句</span><span class="sxs-lookup"><span data-stu-id="cae68-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="cae68-241">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="cae68-241">Seed the database</span></span>

<span data-ttu-id="cae68-242">使用以下代码在 Models 文件夹中创建一个名为 `SeedData` 的新类：</span><span class="sxs-lookup"><span data-stu-id="cae68-242">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="cae68-243">如果数据库中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="cae68-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="cae68-244">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="cae68-244">Add the seed initializer</span></span>

<span data-ttu-id="cae68-245">将 Program.cs 的内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="cae68-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="cae68-246">前面的代码修改了 `Main` 方法，以便执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cae68-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="cae68-247">从依赖注入容器中获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="cae68-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="cae68-248">调用 `seedData.Initialize` 方法，并向其传递数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="cae68-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="cae68-249">Seed 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="cae68-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="cae68-250">[using 语句](/dotnet/csharp/language-reference/keywords/using-statement)将确保释放上下文。</span><span class="sxs-lookup"><span data-stu-id="cae68-250">The [using statement](/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="cae68-251">未运行 `Update-Database` 时出现以下异常：</span><span class="sxs-lookup"><span data-stu-id="cae68-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="cae68-252">测试应用</span><span class="sxs-lookup"><span data-stu-id="cae68-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cae68-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cae68-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cae68-254">删除数据库中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="cae68-254">Delete all the records in the database.</span></span> <span data-ttu-id="cae68-255">使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="cae68-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="cae68-256">通过调用 `Startup` 类中的方法强制应用初始化，使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="cae68-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="cae68-257">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="cae68-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="cae68-258">使用以下任一方法停止并重启 IIS：</span><span class="sxs-lookup"><span data-stu-id="cae68-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="cae68-259">右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点” ：</span><span class="sxs-lookup"><span data-stu-id="cae68-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](sql/_static/stopIIS.png)

    * <span data-ttu-id="cae68-262">如果应用是在非调试模式下运行的，请按 F5<kbd></kbd> 以在调试模式下运行。</span><span class="sxs-lookup"><span data-stu-id="cae68-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="cae68-263">如果应用处于调试模式下，请停止调试程序并按 F5<kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="cae68-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cae68-264">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cae68-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="cae68-265">删除数据库中的所有记录，使种子方法运行。</span><span class="sxs-lookup"><span data-stu-id="cae68-265">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="cae68-266">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="cae68-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="cae68-267">应用将显示设定为种子的数据：</span><span class="sxs-lookup"><span data-stu-id="cae68-267">The app shows the seeded data:</span></span>

![在 Chrome 中打开的显示电影数据的电影应用程序](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="cae68-269">其他资源</span><span class="sxs-lookup"><span data-stu-id="cae68-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cae68-270">[上一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)
> [下一篇：更新页面](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="cae68-270">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cae68-271">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="cae68-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cae68-272">`RazorPagesMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="cae68-272">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="cae68-273">在 Startup.cs 的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="cae68-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cae68-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cae68-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-17)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cae68-275">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cae68-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="cae68-276">有关 `ConfigureServices` 中使用的方法的详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="cae68-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="cae68-277">面向 `CookiePolicyOptions` 的 [ASP.NET Core 中的欧盟一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="cae68-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="cae68-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="cae68-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="cae68-279">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString` 键。</span><span class="sxs-lookup"><span data-stu-id="cae68-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="cae68-280">进行本地开发时，配置从 appsettings.json 文件获取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="cae68-280">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cae68-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cae68-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cae68-282">生成的连接字符串将类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="cae68-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json?highlight=8-10)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cae68-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cae68-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cae68-284">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cae68-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="cae68-285">将应用部署到测试或生产服务器时，可以使用环境变量将连接字符串设置为测试或生产数据库服务器。</span><span class="sxs-lookup"><span data-stu-id="cae68-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="cae68-286">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="cae68-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cae68-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cae68-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="cae68-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="cae68-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="cae68-289">LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。</span><span class="sxs-lookup"><span data-stu-id="cae68-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="cae68-290">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="cae68-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="cae68-291">默认情况下，LocalDB 数据库在 `C:/Users/<user/>` 目录下创建 `*.mdf` 文件。</span><span class="sxs-lookup"><span data-stu-id="cae68-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="cae68-292">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="cae68-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![“视图”菜单](sql/_static/ssox.png)

* <span data-ttu-id="cae68-294">右键单击 `Movie` 表，然后选择“视图设计器”：</span><span class="sxs-lookup"><span data-stu-id="cae68-294">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Movie 表上打开的上下文菜单](sql/_static/design.png)

  ![设计器中打开的 Movie 表](sql/_static/dv.png)

<span data-ttu-id="cae68-297">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="cae68-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="cae68-298">默认情况下，EF 为该主键创建一个名为 `ID` 的属性。</span><span class="sxs-lookup"><span data-stu-id="cae68-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="cae68-299">右键单击 `Movie` 表，然后选择“查看数据”：</span><span class="sxs-lookup"><span data-stu-id="cae68-299">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![显示表数据的打开的 Movie 表](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="cae68-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cae68-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cae68-302">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cae68-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="cae68-303">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="cae68-303">Seed the database</span></span>

<span data-ttu-id="cae68-304">使用以下代码在 Models 文件夹中创建一个名为 `SeedData` 的新类：</span><span class="sxs-lookup"><span data-stu-id="cae68-304">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="cae68-305">如果数据库中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="cae68-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="cae68-306">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="cae68-306">Add the seed initializer</span></span>

<span data-ttu-id="cae68-307">将 Program.cs 的内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="cae68-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="cae68-308">前面的代码修改了 `Main` 方法，以便执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cae68-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="cae68-309">从依赖注入容器中获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="cae68-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="cae68-310">调用 `seedData.Initialize` 方法，并向其传递数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="cae68-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="cae68-311">Seed 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="cae68-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="cae68-312">[using 语句](/dotnet/csharp/language-reference/keywords/using-statement)将确保释放上下文。</span><span class="sxs-lookup"><span data-stu-id="cae68-312">The [using statement](/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="cae68-313">生产应用不会调用 `Database.Migrate`。</span><span class="sxs-lookup"><span data-stu-id="cae68-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="cae68-314">它会添加到上面的代码中，以防止在未运行 `Update-Database` 时出现以下异常：</span><span class="sxs-lookup"><span data-stu-id="cae68-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="cae68-315">SqlException：无法打开登录请求的数据库“RazorPagesMovieContext-21”。</span><span class="sxs-lookup"><span data-stu-id="cae68-315">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="cae68-316">登录失败。</span><span class="sxs-lookup"><span data-stu-id="cae68-316">The login failed.</span></span>
<span data-ttu-id="cae68-317">用户“用户名”登录失败。</span><span class="sxs-lookup"><span data-stu-id="cae68-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="cae68-318">测试应用</span><span class="sxs-lookup"><span data-stu-id="cae68-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cae68-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cae68-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cae68-320">删除数据库中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="cae68-320">Delete all the records in the database.</span></span> <span data-ttu-id="cae68-321">可以使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作</span><span class="sxs-lookup"><span data-stu-id="cae68-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="cae68-322">通过调用 `Startup` 类中的方法强制应用初始化，使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="cae68-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="cae68-323">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="cae68-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="cae68-324">可以使用以下任一方法来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="cae68-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="cae68-325">右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点” ：</span><span class="sxs-lookup"><span data-stu-id="cae68-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](sql/_static/stopIIS.png)

    * <span data-ttu-id="cae68-328">如果应用是在非调试模式下运行的，请按 F5<kbd></kbd> 以在调试模式下运行。</span><span class="sxs-lookup"><span data-stu-id="cae68-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="cae68-329">如果应用处于调试模式下，请停止调试程序并按 F5<kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="cae68-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cae68-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cae68-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cae68-331">删除数据库中的所有记录，使种子方法运行。</span><span class="sxs-lookup"><span data-stu-id="cae68-331">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="cae68-332">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="cae68-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cae68-333">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cae68-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cae68-334">删除数据库中的所有记录，使种子方法运行。</span><span class="sxs-lookup"><span data-stu-id="cae68-334">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="cae68-335">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="cae68-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="cae68-336">应用将显示设定为种子的数据：</span><span class="sxs-lookup"><span data-stu-id="cae68-336">The app shows the seeded data:</span></span>

![在 Chrome 中打开的显示电影数据的电影应用程序](sql/_static/m55https.png)

<span data-ttu-id="cae68-338">在下一教程中将对数据的展示进行整理。</span><span class="sxs-lookup"><span data-stu-id="cae68-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cae68-339">其他资源</span><span class="sxs-lookup"><span data-stu-id="cae68-339">Additional resources</span></span>

* [<span data-ttu-id="cae68-340">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="cae68-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="cae68-341">[上一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)
> [下一篇：更新页面](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="cae68-341">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end