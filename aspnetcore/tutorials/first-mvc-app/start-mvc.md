---
title: ASP.NET Core MVC 入门
author: rick-anderson
description: 了解如何开始使用 ASP.NET Core MVC。
ms.author: riande
ms.date: 01/20/2021
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: 8e5f216a354b1ed7559b433d3a4867627bf60df3
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589772"
---
# <a name="get-started-with-aspnet-core-mvc"></a>ASP.NET Core MVC 入门

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

这是本系列教程的第一个教程，介绍具有控制器和视图的 ASP.NET Core MVC Web 开发。

在本系列结束时，你将拥有一个管理和显示电影数据的应用。 您将学习如何：

> [!div class="checklist"]
> * 创建 Web 应用。
> * 添加和构架模型。
> * 使用数据库。
> * 添加搜索和验证。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample)（[如何下载](xref:index#how-to-download-a-sample)）。

## <a name="prerequisites"></a>先决条件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a>创建 Web 应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 启动 Visual Studio 并选择“创建新项目”。
* 在“新建项目”对话框中，选择“ASP.NET Core Web 应用程序”>“下一步”。
* 在“配置新项目”对话框中，为“项目名称”输入 `MvcMovie`。 务必要将项目命名为“MvcMovie”。 复制代码时，大小写需要匹配每个 `namespace` 匹配项。
* 选择“创建”。
* 在“创建新的 ASP.NET Core Web 应用程序”对话框中，选择：
  * 下拉列表中的“.NET Core”和“ASP.NET Core 5.0”。
  * ASP.NET Core Web 应用程序（模型-视图-控制器）。
  * **Create**。

![创建新的 ASP.NET Core Web 应用呈现 ](start-mvc/_static/mvcVS19v16.9.png)

有关创建项目的替代方法，请参阅[在 Visual Studio 中创建新项目](/visualstudio/ide/create-new-project)。

Visual Studio 为创建的 MVC 项目使用默认项目模板。 创建的项目：

* 是一个有效的应用。
* 是一个基本的入门项目。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

本教程假定用户熟悉 VS Code。 有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs)和 [Visual Studio Code 帮助](#visual-studio-code-help)。

* 打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。
* 更改为将包含项目的目录 (`cd`)。
* 运行以下命令：

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * 如果出现对话框，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。**是否添加它们?”** ，选择“是”

  * `dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。
  * `code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 选择“文件”>“新建解决方案” 。

  ![macOS 新建解决方案](start-mvc/_static/new_project_vsmac.png)

* 在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。    在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   

  ![macOS Web 应用模板选择](start-mvc/_static/web_app_template_vsmac.png)

* 在“配置新的 Web 应用”对话框中：

  * 确认已将“身份验证”设置为“无身份验证”。
  * 如果看到用于选择“目标框架”的选项，请选择最新的 5.x 版本。
  * 选择“下一步”。

* 将项目命名为“MvcMovie”，然后选择“创建”。 

  ![macOS 命名项目](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>运行应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 选择 Ctrl+F5 以在不使用调试程序的情况下运行应用。

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio：

  * 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)。
  * 运行应用。

  地址栏显示 `localhost:port#`，而不是显示 `example.com`。 本地计算机的标准主机名为 `localhost`。 当 Visual Studio 创建 Web 项目时，对 Web 服务器使用的是随机端口。

在不进行调试的情况下，通过选择 Ctrl+F5 启动应用，可以：

* 更改代码。
* 保存文件。
* 快速刷新浏览器并查看代码更改。

可以从“调试”菜单项中以调试或非调试模式启动应用：

![调试菜单](start-mvc/_static/debug_menu50.png)

可以通过选择“IIS Express”按钮来调试应用

![IIS Express](start-mvc/_static/iis_express50.png)

下图显示该应用：

![主页或索引页](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 选择 Ctrl+F5，以在不使用调试程序的情况下运行。

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code：

  * 启动 [Kestrel](xref:fundamentals/servers/kestrel)
  * 启动浏览器。
  * 导航到 `https://localhost:5001`。

  地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。 本地计算机的标准主机名为 `localhost`。 Localhost 仅为来自本地计算机的 Web 请求提供服务。

在不进行调试的情况下，通过选择 Ctrl+F5 启动应用，可以：

* 更改代码。
* 保存文件。
* 快速刷新浏览器并查看代码更改。

  ![主页或索引页](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 选择“运行”>“开始执行(不调试)”以启动应用。

  Visual Studio for Mac：

  * 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器。
  * 启动浏览器。
  * 导航到 `http://localhost:port`，其中 port 是随机选择的端口号。

  [!INCLUDE[](~/includes/trustCertMac.md)]

  地址栏显示 `localhost:port#`，而不是显示 `example.com`。 本地计算机的标准主机名为 `localhost`。 当 Visual Studio 创建 Web 项目时，对 Web 服务器使用的是随机端口。

可以从“运行”菜单中以调试或非调试模式启动应用。

下图显示该应用：

![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。

> [!div class="step-by-step"]
> [下一篇：添加控制器](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

这是本系列教程的第一个教程，介绍具有控制器和视图的 ASP.NET Core MVC Web 开发。

在本系列结束时，你将拥有一个管理和显示电影数据的应用。 您将学习如何：

> [!div class="checklist"]
> * 创建 Web 应用。
> * 添加和构架模型。
> * 使用数据库。
> * 添加搜索和验证。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample)（[如何下载](xref:index#how-to-download-a-sample)）。

## <a name="prerequisites"></a>先决条件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>创建 Web 应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 Visual Studio 中，选择“创建新项目”。

* 选择“ASP.NET Core Web 应用程序”>“下一步”。

  ![创建新的 ASP.NET Core Web 应用项目](start-mvc/_static/np_2.1.png)

* 将项目命名为“MvcMovie”，然后选择“创建” 。 将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。

  ![配置新项目](start-mvc/_static/config.png)

* 选择“Web 应用(模型-视图-控制器)”。 在下拉框中，选择“.NET Core”和“ASP.NET Core 3.1”，然后选择“创建”。

  ![“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web ](start-mvc/_static/new_project30.png)

Visual Studio 为创建的 MVC 项目使用默认项目模板。 创建的项目：

* 是一个有效的应用。
* 是一个基本的入门项目。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

本教程假定用户熟悉 VS Code。 有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs)和 [Visual Studio Code 帮助](#visual-studio-code-help)。

* 打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。
* 将目录 (`cd`) 更改为包含项目的文件夹。
* 运行以下命令：

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * 一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。**是否添加它们?”** ，选择“是”。

  * `dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。
  * `code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 选择“文件”>“新建解决方案” 。

  ![macOS 新建解决方案](start-mvc/_static/new_project_vsmac.png)

* 在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。    在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   

  ![macOS Web 应用模板选择](start-mvc/_static/web_app_template_vsmac.png)

* 在“配置新的 Web 应用”对话框中：

  * 确认已将“身份验证”设置为“无身份验证”。
  * 如果看到用于选择“目标框架”的选项，请选择最新的 3.x 版本。
  * 选择“下一步”。

* 将项目命名为“MvcMovie”，然后选择“创建”。 

  ![macOS 命名项目](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>运行应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 选择 Ctrl+F5 可运行应用，不会进行调试。

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio：

  * 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)。
  * 运行应用。

  地址栏显示 `localhost:port#`，而不是显示 `example.com`。 本地计算机的标准主机名为 `localhost`。 当 Visual Studio 创建 Web 项目时，对 Web 服务器使用的是随机端口。

在不进行调试的情况下，通过选择 Ctrl+F5 启动应用，可以：

* 更改代码。
* 保存文件。
* 快速刷新浏览器并查看代码更改。

可以从“调试”菜单项中以调试或非调试模式启动应用：

![调试菜单](start-mvc/_static/debug_menu.png)

可以通过选择“IIS Express”按钮来调试应用

![IIS Express](start-mvc/_static/iis_express.png)

下图显示该应用：

![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 选择 Ctrl+F5 可运行应用，不会进行调试。

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code：

  * 启动 [Kestrel](xref:fundamentals/servers/kestrel)
  * 启动浏览器。
  * 导航到 `https://localhost:5001`。

  地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。 本地计算机的标准主机名为 `localhost`。 Localhost 仅为来自本地计算机的 Web 请求提供服务。

在不进行调试的情况下，通过选择 Ctrl+F5 启动应用，可以：

* 更改代码。
* 保存文件。
* 快速刷新浏览器并查看代码更改。

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 选择“运行”>“开始执行(不调试)”以启动应用。

  Visual Studio for Mac：启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。

[!INCLUDE[](~/includes/trustCertMac.md)]

地址栏显示 `localhost:port#`，而不是显示 `example.com`。 本地计算机的标准主机名为 `localhost`。 Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。 运行应用时，将看到不同的端口号。

可以从“运行”菜单中以调试或非调试模式启动应用。

下图显示该应用：

![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。

> [!div class="step-by-step"]
> [下一页](adding-controller.md)

::: moniker-end
