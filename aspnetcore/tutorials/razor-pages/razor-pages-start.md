---
title: 教程：在 ASP.NET Core 中开始使用 Razor Pages
author: rick-anderson
description: 本教程是系列教程中的第一个教程，介绍生成 ASP.NET Core Razor 页面 Web 应用的基础知识。
ms.author: riande
ms.date: 09/15/2020
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 61ff4edfaedb22ea88d6bb0be2390ff734879942
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588433"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="cb1ec-103">教程：在 ASP.NET Core 中开始使用 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="cb1ec-103">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="cb1ec-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cb1ec-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="cb1ec-105">本教程是系列教程中的第一个教程，介绍生成 ASP.NET Core Razor 页面 Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="cb1ec-106">有关面向熟悉控制器和视图的开发人员的更高级介绍，请参阅 [Razor Pages 简介](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="cb1ec-107">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="cb1ec-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cb1ec-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cb1ec-110">创建 Razor 页面 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="cb1ec-111">运行应用。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-111">Run the app.</span></span>
> * <span data-ttu-id="cb1ec-112">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-112">Examine the project files.</span></span>

<span data-ttu-id="cb1ec-113">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行增强。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll enhance in later tutorials.</span></span>

![主页或 Index 页](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="cb1ec-115">先决条件</span><span class="sxs-lookup"><span data-stu-id="cb1ec-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb1ec-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb1ec-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cb1ec-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb1ec-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cb1ec-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cb1ec-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="cb1ec-119">创建 Razor 页面 Web 应用</span><span class="sxs-lookup"><span data-stu-id="cb1ec-119">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb1ec-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb1ec-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cb1ec-121">启动 Visual Studio 并选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-121">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="cb1ec-122">有关详细信息，请参阅[在 Visual Studio 中新建项目](/visualstudio/ide/create-new-project)。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-122">For more information, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![从“启动”窗口创建新项目](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="cb1ec-124">在“创建新项目”对话框中，选择“ASP.NET Core Web 应用程序”，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-124">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

    ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="cb1ec-126">在“配置新项目”对话框中，为“项目名称”输入 `RazorPagesMovie`。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-126">In the **Configure your new project** dialog, enter `RazorPagesMovie` for **Project name**.</span></span> <span data-ttu-id="cb1ec-127">请务必将项目命名为“RazorPagesMovie”（包括匹配大小写），这样在复制和粘贴代码时命名空间就会匹配。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-127">It's important to name the project *RazorPagesMovie*, including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="cb1ec-128">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-128">Select **Create**.</span></span>

    ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

1. <span data-ttu-id="cb1ec-130">在“创建新的 ASP.NET Core Web 应用程序”对话框中，选择：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-130">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="cb1ec-131">下拉列表中的“.NET Core”和“ASP.NET Core 5.0”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="cb1ec-132">**Web 应用程序**。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-132">**Web Application**.</span></span>
    1. <span data-ttu-id="cb1ec-133">**Create**。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-133">**Create**.</span></span>

     ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="cb1ec-135">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-135">The following starter project is created:</span></span>

    ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="cb1ec-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb1ec-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="cb1ec-138">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="cb1ec-139">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="cb1ec-140">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * <span data-ttu-id="cb1ec-141">`dotnet new` 命令在“RazorPagesMovie”文件夹中新建 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-141">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
   * <span data-ttu-id="cb1ec-142">`code` 命令在 Visual Studio Code 的当前实例中打开“RazorPagesMovie”文件夹。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-142">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cb1ec-143">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cb1ec-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="cb1ec-144">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-144">Select **File** > **New Solution**.</span></span>

    ![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="cb1ec-146">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="cb1ec-147">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="cb1ec-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![macOS Web 应用模板选择](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="cb1ec-149">在“配置新的 Web 应用程序”对话框中：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="cb1ec-150">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="cb1ec-151">如果看到用于选择“目标框架”的选项，请选择最新的 .NET 5.x 版本。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-151">If presented an option to select a **Target Framework**, select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="cb1ec-152">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-152">Select **Next**.</span></span>

1. <span data-ttu-id="cb1ec-153">将项目命名为“PagesMovie”，然后选择“创建” *Razor* 。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-153">Name the project *RazorPagesMovie* and select **Create**.</span></span>

    ![macOS 命名项目](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="cb1ec-155">运行应用</span><span class="sxs-lookup"><span data-stu-id="cb1ec-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="cb1ec-156">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="cb1ec-156">Examine the project files</span></span>

<span data-ttu-id="cb1ec-157">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="cb1ec-158">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="cb1ec-158">Pages folder</span></span>

<span data-ttu-id="cb1ec-159">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-159">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="cb1ec-160">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-160">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="cb1ec-161">一个 .cshtml 文件，其中包含使用 Razor 语法的 C# 代码的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-161">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="cb1ec-162">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="cb1ec-163">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="cb1ec-164">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="cb1ec-165">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="cb1ec-166">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="cb1ec-167">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="cb1ec-167">wwwroot folder</span></span>

<span data-ttu-id="cb1ec-168">包含静态资产，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="cb1ec-169">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### appsettings.json

<span data-ttu-id="cb1ec-170">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="cb1ec-171">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="cb1ec-172">Program.cs</span><span class="sxs-lookup"><span data-stu-id="cb1ec-172">Program.cs</span></span>

<span data-ttu-id="cb1ec-173">包含应用的入口点。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-173">Contains the entry point for the app.</span></span> <span data-ttu-id="cb1ec-174">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="cb1ec-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="cb1ec-175">Startup.cs</span></span>

<span data-ttu-id="cb1ec-176">包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="cb1ec-177">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cb1ec-178">后续步骤</span><span class="sxs-lookup"><span data-stu-id="cb1ec-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="cb1ec-179">下一篇：添加模型</span><span class="sxs-lookup"><span data-stu-id="cb1ec-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="cb1ec-180">本教程是系列教程中的第一个教程，介绍生成 ASP.NET Core Razor 页面 Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="cb1ec-181">有关面向熟悉控制器和视图的开发人员的更高级介绍，请参阅 [Razor Pages 简介](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="cb1ec-182">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="cb1ec-183">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cb1ec-184">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cb1ec-185">创建 Razor 页面 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-185">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="cb1ec-186">运行应用。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-186">Run the app.</span></span>
> * <span data-ttu-id="cb1ec-187">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-187">Examine the project files.</span></span>

<span data-ttu-id="cb1ec-188">在本教程结束时，你将有一个工作的 Razor 页面 Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-188">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或 Index 页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="cb1ec-190">先决条件</span><span class="sxs-lookup"><span data-stu-id="cb1ec-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb1ec-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb1ec-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cb1ec-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb1ec-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cb1ec-193">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cb1ec-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="cb1ec-194">创建 Razor 页面 Web 应用</span><span class="sxs-lookup"><span data-stu-id="cb1ec-194">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb1ec-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb1ec-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cb1ec-196">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="cb1ec-197">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-197">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="cb1ec-198">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="cb1ec-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="cb1ec-199">将项目命名为“RazorPagesMovie”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-199">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="cb1ec-200">请务必将项目命名为“RazorPagesMovie”，这样在复制和粘贴代码时命名空间就会匹配。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-200">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="cb1ec-201">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="cb1ec-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="cb1ec-202">在下拉列表中选择“ASP.NET Core 3.1”，然后依次选择“Web 应用程序”和“创建”  。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="cb1ec-204">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-204">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="cb1ec-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb1ec-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cb1ec-207">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="cb1ec-208">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="cb1ec-209">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="cb1ec-210">`dotnet new` 命令在“RazorPagesMovie”文件夹中新建 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-210">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="cb1ec-211">`code` 命令在 Visual Studio Code 的当前实例中打开“RazorPagesMovie”文件夹。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-211">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="cb1ec-212">在状态栏的 OmniSharp 火焰图标变绿后，对话框就会询问“'RazorPagesMovie' 缺少生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="cb1ec-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="cb1ec-213">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-213">Select **Yes**.</span></span>

  <span data-ttu-id="cb1ec-214">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。  </span><span class="sxs-lookup"><span data-stu-id="cb1ec-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cb1ec-215">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cb1ec-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cb1ec-216">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-216">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="cb1ec-218">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="cb1ec-219">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="cb1ec-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Web 应用模板选择](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="cb1ec-221">在“配置新的 Web 应用程序”对话框中：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="cb1ec-222">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="cb1ec-223">如果看到用于选择“目标框架”的选项，请选择最新的 3.x 版本。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-223">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="cb1ec-224">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-224">Select **Next**.</span></span>

* <span data-ttu-id="cb1ec-225">将项目命名为“RazorPagesMovie”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-225">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS 命名项目](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="cb1ec-227">运行应用</span><span class="sxs-lookup"><span data-stu-id="cb1ec-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="cb1ec-228">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="cb1ec-228">Examine the project files</span></span>

<span data-ttu-id="cb1ec-229">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="cb1ec-230">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="cb1ec-230">Pages folder</span></span>

<span data-ttu-id="cb1ec-231">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-231">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="cb1ec-232">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-232">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="cb1ec-233">一个 .cshtml 文件，其中包含使用 Razor 语法的 C# 代码的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-233">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="cb1ec-234">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="cb1ec-235">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="cb1ec-236">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="cb1ec-237">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="cb1ec-238">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="cb1ec-239">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="cb1ec-239">wwwroot folder</span></span>

<span data-ttu-id="cb1ec-240">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="cb1ec-241">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="cb1ec-242">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="cb1ec-242">appSettings.json</span></span>

<span data-ttu-id="cb1ec-243">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="cb1ec-244">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="cb1ec-245">Program.cs</span><span class="sxs-lookup"><span data-stu-id="cb1ec-245">Program.cs</span></span>

<span data-ttu-id="cb1ec-246">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-246">Contains the entry point for the program.</span></span> <span data-ttu-id="cb1ec-247">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="cb1ec-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="cb1ec-248">Startup.cs</span></span>

<span data-ttu-id="cb1ec-249">包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="cb1ec-250">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cb1ec-251">后续步骤</span><span class="sxs-lookup"><span data-stu-id="cb1ec-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="cb1ec-252">下一篇：添加模型</span><span class="sxs-lookup"><span data-stu-id="cb1ec-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cb1ec-253">这是系列中的第一个教程。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="cb1ec-254">[本系列](xref:tutorials/razor-pages/index)介绍构建 ASP.NET Core Razor 页面 Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="cb1ec-255">有关面向熟悉控制器和视图的开发人员的更高级介绍，请参阅 [Razor Pages 简介](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="cb1ec-256">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="cb1ec-257">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cb1ec-258">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cb1ec-259">创建 Razor 页面 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-259">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="cb1ec-260">运行应用。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-260">Run the app.</span></span>
> * <span data-ttu-id="cb1ec-261">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-261">Examine the project files.</span></span>

<span data-ttu-id="cb1ec-262">在本教程结束时，你将有一个工作的 Razor 页面 Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-262">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或 Index 页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="cb1ec-264">先决条件</span><span class="sxs-lookup"><span data-stu-id="cb1ec-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb1ec-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb1ec-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cb1ec-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb1ec-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cb1ec-267">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cb1ec-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="cb1ec-268">创建 Razor 页面 Web 应用</span><span class="sxs-lookup"><span data-stu-id="cb1ec-268">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb1ec-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb1ec-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cb1ec-270">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="cb1ec-271">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-271">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="cb1ec-273">将项目命名为“RazorPagesMovie”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-273">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="cb1ec-274">请务必将项目命名为“RazorPagesMovie”，这样在复制和粘贴代码时命名空间就会匹配。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-274">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

* <span data-ttu-id="cb1ec-276">在下拉列表中选择“ASP.NET Core 2.2”，然后依次选择“Web 应用程序”和“创建”  。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="cb1ec-278">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-278">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="cb1ec-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb1ec-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cb1ec-281">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="cb1ec-282">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="cb1ec-283">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="cb1ec-284">`dotnet new` 命令在“RazorPagesMovie”文件夹中新建 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-284">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="cb1ec-285">`code` 命令在 Visual Studio Code 的当前实例中打开“RazorPagesMovie”文件夹。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-285">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="cb1ec-286">在状态栏的 OmniSharp 火焰图标变绿后，对话框就会询问“'RazorPagesMovie' 缺少生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="cb1ec-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="cb1ec-287">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-287">Select **Yes**.</span></span>

  <span data-ttu-id="cb1ec-288">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。  </span><span class="sxs-lookup"><span data-stu-id="cb1ec-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cb1ec-289">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cb1ec-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cb1ec-290">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-290">Select **File** > **New Solution**.</span></span>

![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="cb1ec-292">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="cb1ec-293">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="cb1ec-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="cb1ec-294">在“配置新的 Web 应用程序”对话框中：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="cb1ec-295">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="cb1ec-296">如果看到用于选择“目标框架”的选项，请选择最新的 2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-296">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="cb1ec-297">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-297">Select **Next**.</span></span>

* <span data-ttu-id="cb1ec-298">将项目命名为“RazorPagesMovie”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-298">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="cb1ec-300">运行应用</span><span class="sxs-lookup"><span data-stu-id="cb1ec-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb1ec-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb1ec-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cb1ec-302">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="cb1ec-303">使用“Ctrl+F5”<kbd></kbd>启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="cb1ec-304">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="cb1ec-305">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="cb1ec-306">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="cb1ec-307">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="cb1ec-308">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="cb1ec-309">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="cb1ec-310">在应用的主页上，选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="cb1ec-311">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或 Index 页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="cb1ec-313">下图展示了提供同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![主页或 Index 页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="cb1ec-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb1ec-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="cb1ec-316">按 Ctrl+F5<kbd></kbd> 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="cb1ec-317">使用“Ctrl+F5”<kbd></kbd>启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="cb1ec-318">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="cb1ec-319">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并转到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="cb1ec-320">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="cb1ec-321">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="cb1ec-322">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="cb1ec-323">在应用的主页上，选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="cb1ec-324">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或 Index 页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="cb1ec-326">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-326">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或 Index 页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cb1ec-328">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cb1ec-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="cb1ec-329">按 Cmd-Opt-F5，以在不使用调试器的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="cb1ec-330">使用 Cmd+Opt+F5<kbd></kbd> 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="cb1ec-331">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="cb1ec-332">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并转到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="cb1ec-333">在应用的主页上，选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="cb1ec-334">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或 Index 页](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="cb1ec-336">下图展示了提供同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![主页或 Index 页](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="cb1ec-338">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="cb1ec-338">Examine the project files</span></span>

<span data-ttu-id="cb1ec-339">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="cb1ec-340">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="cb1ec-340">Pages folder</span></span>

<span data-ttu-id="cb1ec-341">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-341">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="cb1ec-342">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="cb1ec-342">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="cb1ec-343">一个 .cshtml 文件，其中包含使用 Razor 语法的 C# 代码的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-343">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="cb1ec-344">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="cb1ec-345">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="cb1ec-346">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="cb1ec-347">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="cb1ec-348">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="cb1ec-349">Razor 页面派生自 `PageModel`。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-349">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="cb1ec-350">按照约定，`PageModel` 派生的类称为 `<PageName>Model`。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="cb1ec-351">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="cb1ec-351">wwwroot folder</span></span>

<span data-ttu-id="cb1ec-352">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="cb1ec-353">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="cb1ec-354">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="cb1ec-354">appSettings.json</span></span>

<span data-ttu-id="cb1ec-355">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="cb1ec-356">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="cb1ec-357">Program.cs</span><span class="sxs-lookup"><span data-stu-id="cb1ec-357">Program.cs</span></span>

<span data-ttu-id="cb1ec-358">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-358">Contains the entry point for the program.</span></span> <span data-ttu-id="cb1ec-359">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="cb1ec-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="cb1ec-360">Startup.cs</span></span>

<span data-ttu-id="cb1ec-361">包含配置应用行为的代码，例如是否需要同意 cookie。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-361">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="cb1ec-362">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="cb1ec-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cb1ec-363">其他资源</span><span class="sxs-lookup"><span data-stu-id="cb1ec-363">Additional resources</span></span>

* [<span data-ttu-id="cb1ec-364">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="cb1ec-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="cb1ec-365">后续步骤</span><span class="sxs-lookup"><span data-stu-id="cb1ec-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="cb1ec-366">下一篇：添加模型</span><span class="sxs-lookup"><span data-stu-id="cb1ec-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
