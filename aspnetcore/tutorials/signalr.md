---
title: ASP.NET Core SignalR 入门
author: bradygaster
description: 在本教程中，可以创建使用 ASP.NET Core SignalR 的聊天应用。
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
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
uid: tutorials/signalr
ms.openlocfilehash: 1dad2264250bf43ec6c1df679b8754a82a5addfd
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "95417664"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a><span data-ttu-id="fb147-103">教程：ASP.NET Core SignalR 入门</span><span class="sxs-lookup"><span data-stu-id="fb147-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fb147-104">本教程介绍使用 SignalR 生成实时应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="fb147-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="fb147-105">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="fb147-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fb147-106">创建 Web 项目。</span><span class="sxs-lookup"><span data-stu-id="fb147-106">Create a web project.</span></span>
> * <span data-ttu-id="fb147-107">添加 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="fb147-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="fb147-108">创建 SignalR 中心。</span><span class="sxs-lookup"><span data-stu-id="fb147-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="fb147-109">配置项目以使用 SignalR。</span><span class="sxs-lookup"><span data-stu-id="fb147-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="fb147-110">添加可将消息从任何客户端发送到所有连接客户端的代码。</span><span class="sxs-lookup"><span data-stu-id="fb147-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="fb147-111">最终将创建一个正常运行的聊天应用：</span><span class="sxs-lookup"><span data-stu-id="fb147-111">At the end, you'll have a working chat app:</span></span>

![SignalR 示例应用](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="fb147-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="fb147-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fb147-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb147-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fb147-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fb147-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fb147-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fb147-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="fb147-117">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="fb147-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fb147-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb147-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="fb147-119">从菜单中选择“文件”>“新建项目”。</span><span class="sxs-lookup"><span data-stu-id="fb147-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="fb147-120">在“创建新项目”对话框中，选择“ASP.NET Core Web 应用程序”，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="fb147-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="fb147-121">在“配置新项目”对话框中，将项目命名为“SignalRChat”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="fb147-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="fb147-122">在“创建新的 ASP.NET Core Web 应用程序”对话框中，选择 .NET Core 和 ASP.NET Core 3.1  。</span><span class="sxs-lookup"><span data-stu-id="fb147-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.1**.</span></span> 

* <span data-ttu-id="fb147-123">选择“Web 应用程序”以创建使用 Razor Pages 的项目，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="fb147-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Visual Studio 中的“新建项目”对话框](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fb147-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fb147-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="fb147-126">在将要在其中创建新项目文件夹的文件夹中打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="fb147-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="fb147-127">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fb147-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   cd SignalRChat
   code -r .
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fb147-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fb147-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fb147-129">从菜单中选择“文件”>“新建解决方案”。</span><span class="sxs-lookup"><span data-stu-id="fb147-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="fb147-130">选择“.NET Core”>“应用”>“Web 应用程序”（不要选择“Web 应用程序(Model-View-Controller)”），然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="fb147-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="fb147-131">确保“目标框架”设置为 .NET Core 3.1，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="fb147-131">Make sure the **Target Framework** is set to **.NET Core 3.1**, and then select **Next**.</span></span>

* <span data-ttu-id="fb147-132">将项目命名为“SignalRChat”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="fb147-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="fb147-133">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="fb147-133">Add the SignalR client library</span></span>

<span data-ttu-id="fb147-134">ASP.NET Core 3.1 共享框架中包含 SignalR 服务器库。</span><span class="sxs-lookup"><span data-stu-id="fb147-134">The SignalR server library is included in the ASP.NET Core 3.1 shared framework.</span></span> <span data-ttu-id="fb147-135">JavaScript 客户端库不会自动包含在项目中。</span><span class="sxs-lookup"><span data-stu-id="fb147-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="fb147-136">对于此教程，使用库管理器 (LibMan) 从 unpkg 获取客户端库。</span><span class="sxs-lookup"><span data-stu-id="fb147-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="fb147-137">unpkg 是一个内容分发网络 (CDN)，可分发在 npm（即 Node.js 包管理器）中找到的任何内容。</span><span class="sxs-lookup"><span data-stu-id="fb147-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fb147-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb147-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="fb147-139">在“解决方案资源管理器”中，右键单击项目，然后选择“添加”>“客户端库”  。</span><span class="sxs-lookup"><span data-stu-id="fb147-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="fb147-140">在“添加客户端库”对话框中，对于“提供程序”，选择“unpkg”。</span><span class="sxs-lookup"><span data-stu-id="fb147-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="fb147-141">对于“库”，输入 `@microsoft/signalr@latest`。</span><span class="sxs-lookup"><span data-stu-id="fb147-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="fb147-142">选择“选择特定文件”，展开“dist/browser”文件夹，然后选择“signalr.js”和“signalr.min.js”。</span><span class="sxs-lookup"><span data-stu-id="fb147-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="fb147-143">将“目标位置”设置为 wwwroot/js/signalr/，然后选择“安装”。</span><span class="sxs-lookup"><span data-stu-id="fb147-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![“添加客户端库”对话框 - 选择库](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="fb147-145">LibMan 创建 wwwroot/js/signalr 文件夹并将所选文件复制到该文件夹。</span><span class="sxs-lookup"><span data-stu-id="fb147-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fb147-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fb147-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="fb147-147">在集成终端中，运行以下命令以安装 LibMan。</span><span class="sxs-lookup"><span data-stu-id="fb147-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="fb147-148">使用 LibMan 运行以下命令，以获取 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="fb147-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="fb147-149">可能需要等待几秒钟的时间才能看到输出。</span><span class="sxs-lookup"><span data-stu-id="fb147-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="fb147-150">参数指定以下选项：</span><span class="sxs-lookup"><span data-stu-id="fb147-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="fb147-151">使用 unpkg 提供程序。</span><span class="sxs-lookup"><span data-stu-id="fb147-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="fb147-152">将文件复制到 wwwroot/js/signalr 目标。</span><span class="sxs-lookup"><span data-stu-id="fb147-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="fb147-153">仅复制指定的文件。</span><span class="sxs-lookup"><span data-stu-id="fb147-153">Copy only the specified files.</span></span>

  <span data-ttu-id="fb147-154">输出如下所示：</span><span class="sxs-lookup"><span data-stu-id="fb147-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fb147-155">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fb147-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fb147-156">在“终端”中，运行以下命令以安装 LibMan。</span><span class="sxs-lookup"><span data-stu-id="fb147-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="fb147-157">导航到项目文件夹（包含 SignalRChat.csproj 文件的文件夹）。</span><span class="sxs-lookup"><span data-stu-id="fb147-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="fb147-158">使用 LibMan 运行以下命令，以获取 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="fb147-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="fb147-159">参数指定以下选项：</span><span class="sxs-lookup"><span data-stu-id="fb147-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="fb147-160">使用 unpkg 提供程序。</span><span class="sxs-lookup"><span data-stu-id="fb147-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="fb147-161">将文件复制到 wwwroot/js/signalr 目标。</span><span class="sxs-lookup"><span data-stu-id="fb147-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="fb147-162">仅复制指定的文件。</span><span class="sxs-lookup"><span data-stu-id="fb147-162">Copy only the specified files.</span></span>

  <span data-ttu-id="fb147-163">输出如下所示：</span><span class="sxs-lookup"><span data-stu-id="fb147-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="fb147-164">创建 SignalR 中心</span><span class="sxs-lookup"><span data-stu-id="fb147-164">Create a SignalR hub</span></span>

<span data-ttu-id="fb147-165">*中心* 是一个类，用作处理客户端 - 服务器通信的高级管道。</span><span class="sxs-lookup"><span data-stu-id="fb147-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="fb147-166">在 SignalRChat 项目文件夹中，创建 Hubs 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fb147-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="fb147-167">在 Hubs 文件夹中，使用以下代码创建 ChatHub.cs 文件 ：</span><span class="sxs-lookup"><span data-stu-id="fb147-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="fb147-168">`ChatHub` 类继承自 SignalR `Hub` 类。</span><span class="sxs-lookup"><span data-stu-id="fb147-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="fb147-169">`Hub` 类管理连接、组和消息。</span><span class="sxs-lookup"><span data-stu-id="fb147-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="fb147-170">可通过已连接客户端调用 `SendMessage`，以向所有客户端发送消息。</span><span class="sxs-lookup"><span data-stu-id="fb147-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="fb147-171">本教程后面部分将显示调用该方法的 JavaScript 客户端代码。</span><span class="sxs-lookup"><span data-stu-id="fb147-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="fb147-172">SignalR 代码是异步模式，可提供最大的可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="fb147-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-no-locsignalr"></a><span data-ttu-id="fb147-173">配置 SignalR</span><span class="sxs-lookup"><span data-stu-id="fb147-173">Configure SignalR</span></span>

<span data-ttu-id="fb147-174">必须将 SignalR 服务器配置为将 SignalR 请求传递给 SignalR。</span><span class="sxs-lookup"><span data-stu-id="fb147-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="fb147-175">将以下突出显示的代码添加到 Startup.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="fb147-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="fb147-176">这些更改将 SignalR 添加到 ASP.NET Core 依赖关系注入和路由系统。</span><span class="sxs-lookup"><span data-stu-id="fb147-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="fb147-177">添加 SignalR 客户端代码</span><span class="sxs-lookup"><span data-stu-id="fb147-177">Add SignalR client code</span></span>

* <span data-ttu-id="fb147-178">使用以下代码替换 Pages\Index.cshtml 中的内容：</span><span class="sxs-lookup"><span data-stu-id="fb147-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="fb147-179">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="fb147-179">The preceding code:</span></span>

  * <span data-ttu-id="fb147-180">创建名称以及消息文本的文本框和“提交”按钮。</span><span class="sxs-lookup"><span data-stu-id="fb147-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="fb147-181">使用 `id="messagesList"` 创建一个列表，用于显示从 SignalR 中心接收的消息。</span><span class="sxs-lookup"><span data-stu-id="fb147-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="fb147-182">包含对 SignalR 的脚本引用以及在下一步中创建的“chat.js”应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="fb147-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="fb147-183">在 wwwroot/js 文件夹中，使用以下代码创建 chat.js 文件 ：</span><span class="sxs-lookup"><span data-stu-id="fb147-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="fb147-184">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="fb147-184">The preceding code:</span></span>

  * <span data-ttu-id="fb147-185">创建并启动连接。</span><span class="sxs-lookup"><span data-stu-id="fb147-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="fb147-186">向“提交”按钮添加一个用于向中心发送消息的处理程序。</span><span class="sxs-lookup"><span data-stu-id="fb147-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="fb147-187">向连接对象添加一个用于从中心接收消息并将其添加到列表的处理程序。</span><span class="sxs-lookup"><span data-stu-id="fb147-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="fb147-188">运行应用</span><span class="sxs-lookup"><span data-stu-id="fb147-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fb147-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb147-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fb147-190">按 Ctrl+F5 可运行应用而不进行调试。</span><span class="sxs-lookup"><span data-stu-id="fb147-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fb147-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fb147-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fb147-192">在集成终端中，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fb147-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fb147-193">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fb147-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fb147-194">从菜单中选择“运行”>“开始执行(不调试)”。</span><span class="sxs-lookup"><span data-stu-id="fb147-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="fb147-195">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="fb147-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="fb147-196">选择任一浏览器，输入名称和消息，然后选择“发送消息”按钮。</span><span class="sxs-lookup"><span data-stu-id="fb147-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="fb147-197">两个页面上立即显示名称和消息。</span><span class="sxs-lookup"><span data-stu-id="fb147-197">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR 示例应用](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="fb147-199">如果应用不起作用，请打开浏览器开发人员工具 (F12) 并转到控制台。</span><span class="sxs-lookup"><span data-stu-id="fb147-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="fb147-200">可能会看到与 HTML 和 JavaScript 代码相关的错误。</span><span class="sxs-lookup"><span data-stu-id="fb147-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="fb147-201">例如，假设将 signalr.js 放在不同于系统指示的文件夹中。</span><span class="sxs-lookup"><span data-stu-id="fb147-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="fb147-202">在这种情况下，对该文件的引用将不起作用，并且你将在控制台中看到 404 错误。</span><span class="sxs-lookup"><span data-stu-id="fb147-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="fb147-203">![未找到 signalr.js 错误](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="fb147-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="fb147-204">如果 Chrome 中出现 ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY 错误，请运行这些命令以更新开发证书：</span><span class="sxs-lookup"><span data-stu-id="fb147-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fb147-205">本教程介绍使用 SignalR 生成实时应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="fb147-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="fb147-206">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="fb147-206">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="fb147-207">创建 Web 项目。</span><span class="sxs-lookup"><span data-stu-id="fb147-207">Create a web project.</span></span>   
> * <span data-ttu-id="fb147-208">添加 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="fb147-208">Add the SignalR client library.</span></span>   
> * <span data-ttu-id="fb147-209">创建 SignalR 中心。</span><span class="sxs-lookup"><span data-stu-id="fb147-209">Create a SignalR hub.</span></span> 
> * <span data-ttu-id="fb147-210">配置项目以使用 SignalR。</span><span class="sxs-lookup"><span data-stu-id="fb147-210">Configure the project to use SignalR.</span></span> 
> * <span data-ttu-id="fb147-211">添加可将消息从任何客户端发送到所有连接客户端的代码。</span><span class="sxs-lookup"><span data-stu-id="fb147-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="fb147-212">最后，你将拥有一个工作聊天应用：![SignalR 示例应用](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="fb147-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="fb147-213">先决条件</span><span class="sxs-lookup"><span data-stu-id="fb147-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="fb147-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb147-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="fb147-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fb147-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fb147-216">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fb147-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="fb147-217">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="fb147-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="fb147-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb147-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="fb147-219">从菜单中选择“文件”>“新建项目”。</span><span class="sxs-lookup"><span data-stu-id="fb147-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="fb147-220">在“新建项目”对话框中，选择“已安装”>“Visual C#”>“Web”>“ASP.NET Core Web 应用” 。</span><span class="sxs-lookup"><span data-stu-id="fb147-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="fb147-221">将项目命名为“SignalRChat”。</span><span class="sxs-lookup"><span data-stu-id="fb147-221">Name the project *SignalRChat*.</span></span>   

  ![Visual Studio 中的“新建项目”对话框](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="fb147-223">选择“Web 应用”，以创建使用 Razor Pages 的项目。</span><span class="sxs-lookup"><span data-stu-id="fb147-223">Select **Web Application** to create a project that uses Razor Pages.</span></span>   

* <span data-ttu-id="fb147-224">选择“.NET Core”目标框架，选择“ASP.NET Core 2.2”，然后单击“确定”  。</span><span class="sxs-lookup"><span data-stu-id="fb147-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Visual Studio 中的“新建项目”对话框](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="fb147-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fb147-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="fb147-227">在将要在其中创建新项目文件夹的文件夹中打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="fb147-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="fb147-228">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fb147-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fb147-229">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fb147-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="fb147-230">从菜单中选择“文件”>“新建解决方案”。</span><span class="sxs-lookup"><span data-stu-id="fb147-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="fb147-231">选择“.NET Core”>“应用”>“ASP.NET Core Web 应用”（请勿选择 ASP.NET Core Web 应用 (MVC)） 。</span><span class="sxs-lookup"><span data-stu-id="fb147-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="fb147-232">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="fb147-232">Select **Next**.</span></span>  

* <span data-ttu-id="fb147-233">将项目命名为“SignalRChat”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="fb147-233">Name the project *SignalRChat*, and then select **Create**.</span></span> 

--- 

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="fb147-234">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="fb147-234">Add the SignalR client library</span></span> 

<span data-ttu-id="fb147-235">`Microsoft.AspNetCore.App` 元包中包括 SignalR 服务器库。</span><span class="sxs-lookup"><span data-stu-id="fb147-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="fb147-236">JavaScript 客户端库不会自动包含在项目中。</span><span class="sxs-lookup"><span data-stu-id="fb147-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="fb147-237">对于此教程，使用库管理器 (LibMan) 从 unpkg 获取客户端库。</span><span class="sxs-lookup"><span data-stu-id="fb147-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="fb147-238">unpkg 是一个内容分发网络 (CDN)，可分发在 npm（即 Node.js 包管理器）中找到的任何内容。</span><span class="sxs-lookup"><span data-stu-id="fb147-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>   

# <a name="visual-studio"></a>[<span data-ttu-id="fb147-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb147-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="fb147-240">在“解决方案资源管理器”中，右键单击项目，然后选择“添加”>“客户端库”  。</span><span class="sxs-lookup"><span data-stu-id="fb147-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="fb147-241">在“添加客户端库”对话框中，对于“提供程序”，选择“unpkg”。</span><span class="sxs-lookup"><span data-stu-id="fb147-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="fb147-242">对于“库”，输入 `@microsoft/signalr@3`，然后选择不是预览版的最新版本。</span><span class="sxs-lookup"><span data-stu-id="fb147-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![“添加客户端库”对话框 - 选择库](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="fb147-244">选择“选择特定文件”，展开“dist/browser”文件夹，然后选择“signalr.js”和“signalr.min.js”。</span><span class="sxs-lookup"><span data-stu-id="fb147-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="fb147-245">将“目标位置”设置为 wwwroot/lib/signalr/，然后选择“安装”。</span><span class="sxs-lookup"><span data-stu-id="fb147-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![“添加客户端库”对话框 - 选择文件和目标](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="fb147-247">LibMan 创建 wwwroot/lib/signalr 文件夹并将所选文件复制到该文件夹。</span><span class="sxs-lookup"><span data-stu-id="fb147-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="fb147-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fb147-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="fb147-249">在集成终端中，运行以下命令以安装 LibMan。</span><span class="sxs-lookup"><span data-stu-id="fb147-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="fb147-250">使用 LibMan 运行以下命令，以获取 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="fb147-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="fb147-251">可能需要等待几秒钟的时间才能看到输出。</span><span class="sxs-lookup"><span data-stu-id="fb147-251">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="fb147-252">参数指定以下选项：</span><span class="sxs-lookup"><span data-stu-id="fb147-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="fb147-253">使用 unpkg 提供程序。</span><span class="sxs-lookup"><span data-stu-id="fb147-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="fb147-254">将文件复制到 wwwroot/lib/signalr 目标。</span><span class="sxs-lookup"><span data-stu-id="fb147-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="fb147-255">仅复制指定的文件。</span><span class="sxs-lookup"><span data-stu-id="fb147-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="fb147-256">输出如下所示：</span><span class="sxs-lookup"><span data-stu-id="fb147-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fb147-257">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fb147-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="fb147-258">在“终端”中，运行以下命令以安装 LibMan。</span><span class="sxs-lookup"><span data-stu-id="fb147-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="fb147-259">导航到项目文件夹（包含 SignalRChat.csproj 文件的文件夹）。</span><span class="sxs-lookup"><span data-stu-id="fb147-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>   

* <span data-ttu-id="fb147-260">使用 LibMan 运行以下命令，以获取 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="fb147-260">Run the following command to get the SignalR client library by using LibMan.</span></span>    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="fb147-261">参数指定以下选项：</span><span class="sxs-lookup"><span data-stu-id="fb147-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="fb147-262">使用 unpkg 提供程序。</span><span class="sxs-lookup"><span data-stu-id="fb147-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="fb147-263">将文件复制到 wwwroot/lib/signalr 目标。</span><span class="sxs-lookup"><span data-stu-id="fb147-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="fb147-264">仅复制指定的文件。</span><span class="sxs-lookup"><span data-stu-id="fb147-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="fb147-265">输出如下所示：</span><span class="sxs-lookup"><span data-stu-id="fb147-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="fb147-266">创建 SignalR 中心</span><span class="sxs-lookup"><span data-stu-id="fb147-266">Create a SignalR hub</span></span>   

<span data-ttu-id="fb147-267">*中心* 是一个类，用作处理客户端 - 服务器通信的高级管道。</span><span class="sxs-lookup"><span data-stu-id="fb147-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="fb147-268">在 SignalRChat 项目文件夹中，创建 Hubs 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fb147-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>  

* <span data-ttu-id="fb147-269">在 Hubs 文件夹中，使用以下代码创建 ChatHub.cs 文件 ：</span><span class="sxs-lookup"><span data-stu-id="fb147-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="fb147-270">`ChatHub` 类继承自 SignalR `Hub` 类。</span><span class="sxs-lookup"><span data-stu-id="fb147-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="fb147-271">`Hub` 类管理连接、组和消息。</span><span class="sxs-lookup"><span data-stu-id="fb147-271">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="fb147-272">可通过已连接客户端调用 `SendMessage`，以向所有客户端发送消息。</span><span class="sxs-lookup"><span data-stu-id="fb147-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="fb147-273">本教程后面部分将显示调用该方法的 JavaScript 客户端代码。</span><span class="sxs-lookup"><span data-stu-id="fb147-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="fb147-274">SignalR 代码是异步模式，可提供最大的可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="fb147-274">SignalR code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-no-locsignalr"></a><span data-ttu-id="fb147-275">配置 SignalR</span><span class="sxs-lookup"><span data-stu-id="fb147-275">Configure SignalR</span></span>  

<span data-ttu-id="fb147-276">必须将 SignalR 服务器配置为将 SignalR 请求传递给 SignalR。</span><span class="sxs-lookup"><span data-stu-id="fb147-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>    

* <span data-ttu-id="fb147-277">将以下突出显示的代码添加到 Startup.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="fb147-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="fb147-278">这些更改将 SignalR 添加到 ASP.NET Core 依赖关系注入系统和中间件管道。</span><span class="sxs-lookup"><span data-stu-id="fb147-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="fb147-279">添加 SignalR 客户端代码</span><span class="sxs-lookup"><span data-stu-id="fb147-279">Add SignalR client code</span></span>    

* <span data-ttu-id="fb147-280">使用以下代码替换 Pages\Index.cshtml 中的内容：</span><span class="sxs-lookup"><span data-stu-id="fb147-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="fb147-281">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="fb147-281">The preceding code:</span></span>   

  * <span data-ttu-id="fb147-282">创建名称以及消息文本的文本框和“提交”按钮。</span><span class="sxs-lookup"><span data-stu-id="fb147-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="fb147-283">使用 `id="messagesList"` 创建一个列表，用于显示从 SignalR 中心接收的消息。</span><span class="sxs-lookup"><span data-stu-id="fb147-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>   
  * <span data-ttu-id="fb147-284">包含对 SignalR 的脚本引用以及在下一步中创建的“chat.js”应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="fb147-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="fb147-285">在 wwwroot/js 文件夹中，使用以下代码创建 chat.js 文件 ：</span><span class="sxs-lookup"><span data-stu-id="fb147-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="fb147-286">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="fb147-286">The preceding code:</span></span>   

  * <span data-ttu-id="fb147-287">创建并启动连接。</span><span class="sxs-lookup"><span data-stu-id="fb147-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="fb147-288">向“提交”按钮添加一个用于向中心发送消息的处理程序。</span><span class="sxs-lookup"><span data-stu-id="fb147-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="fb147-289">向连接对象添加一个用于从中心接收消息并将其添加到列表的处理程序。</span><span class="sxs-lookup"><span data-stu-id="fb147-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="fb147-290">运行应用</span><span class="sxs-lookup"><span data-stu-id="fb147-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="fb147-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb147-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="fb147-292">按 Ctrl+F5 可运行应用而不进行调试。</span><span class="sxs-lookup"><span data-stu-id="fb147-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="fb147-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fb147-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="fb147-294">在集成终端中，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fb147-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fb147-295">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fb147-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fb147-296">从菜单中选择“运行”>“开始执行(不调试)”。</span><span class="sxs-lookup"><span data-stu-id="fb147-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="fb147-297">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="fb147-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="fb147-298">选择任一浏览器，输入名称和消息，然后选择“发送消息”按钮。</span><span class="sxs-lookup"><span data-stu-id="fb147-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="fb147-299">两个页面上立即显示名称和消息。</span><span class="sxs-lookup"><span data-stu-id="fb147-299">The name and message are displayed on both pages instantly.</span></span>   

  ![SignalR 示例应用](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> <span data-ttu-id="fb147-301">如果应用不起作用，请打开浏览器开发人员工具 (F12) 并转到控制台。</span><span class="sxs-lookup"><span data-stu-id="fb147-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="fb147-302">可能会看到与 HTML 和 JavaScript 代码相关的错误。</span><span class="sxs-lookup"><span data-stu-id="fb147-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="fb147-303">例如，假设将 signalr.js 放在不同于系统指示的文件夹中。</span><span class="sxs-lookup"><span data-stu-id="fb147-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="fb147-304">在这种情况下，对该文件的引用将不起作用，并且你将在控制台中看到 404 错误。</span><span class="sxs-lookup"><span data-stu-id="fb147-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="fb147-305">![未找到 signalr.js 错误](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="fb147-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="fb147-306">其他资源</span><span class="sxs-lookup"><span data-stu-id="fb147-306">Additional resources</span></span> 
* [<span data-ttu-id="fb147-307">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="fb147-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
