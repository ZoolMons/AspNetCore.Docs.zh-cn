---
title: dotnet aspnet-codegenerator 命令
author: rick-anderson
description: dotnet aspnet-codegenerator 命令可用于搭建 ASP.NET Core 项目的基架。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "94920698"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet aspnet-codegenerator

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator` - 运行 ASP.NET Core 基架引擎。 使用 `dotnet aspnet-codegenerator` 只需要从命令行搭建基架，不必使用 Visual Studio 搭建基架。

## <a name="install-and-update-aspnet-codegenerator"></a>安装和更新 aspnet-codegenerator

安装 [.NET SDK](https://dotnet.microsoft.com/download)。

`dotnet-aspnet-codegenerator` 是必须安装的一个[全局工具](/dotnet/core/tools/global-tools)。 以下命令安装 `dotnet-aspnet-codegenerator` 工具的最新稳定版本：

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

以下命令将 `dotnet-aspnet-codegenerator` 更新到已安装的.NET Core SDK 提供的最新稳定版本：

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a>卸载 aspnet-codegenerator

可能需要卸载 `aspnet-codegenerator` 才能解决问题。 例如，如果安装了 `aspnet-codegenerator` 的预览版本，请在安装发布版本之前卸载它。

以下命令卸载 `dotnet-aspnet-codegenerator` 工具并安装最新稳定版本：

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>摘要

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>描述

`dotnet aspnet-codegenerator` 全局命令运行 ASP.NET Core 代码生成器和基架引擎。

## <a name="arguments"></a>自变量

`generator`

要运行的代码生成器。 以下是可用的生成器：

| Generator  | 操作                                                            |
| ---------- | -------------------------------------------------------------------- |
| area       | [搭建区域的基架](xref:mvc/controllers/areas)                      |
| controller | [搭建控制器的基架](xref:tutorials/first-mvc-app/adding-model)  |
| 标识   | [构建 Identity](xref:security/authentication/scaffold-identity) |
| razorpage  | [构建 Razor Pages](xref:tutorials/razor-pages/model)            |
| 查看       | [搭建视图的基架](xref:mvc/views/overview)                          |

## <a name="options"></a>选项

`-n|--nuget-package-dir`

指定 NuGet 包目录。

`-c|--configuration {Debug|Release}`

定义生成配置。 默认值为 `Debug`。

`-tfm|--target-framework`

要使用的目标[框架](/dotnet/standard/frameworks)。 例如 `net46`。

`-b|--build-base-path`

生成基本路径。

`-h|--help`

打印出有关命令的简短帮助。

`--no-build`

运行前不生成项目。 还将隐式设置 `--no-restore` 标记。

`-p|--project <PATH>`

指定要运行的项目文件的路径（文件夹名称或完整路径）。 如果未指定，则默认为当前目录。

## <a name="generator-options"></a>生成器选项

以下各节详细说明了受支持的生成器的可用选项：

* 区域
* 控制器
* Identity  
* Razorpage
* 视图

<a name="area"></a>

### <a name="area-options"></a>区域选项

此工具适用于具有控制器和视图的 ASP.NET Core Web 项目。 它不适用于 Razor Pages 应用。

用法：`dotnet aspnet-codegenerator area AreaNameToGenerate`

前面的命令生成以下文件夹：

* *Areas*
  * AreaNameToGenerate
    * *Controllers*
    * *Data*
    * Models
    * *Views*

<a name="ctl"></a>

### <a name="controller-options"></a>控制器选项

下表列出了 `aspnet-codegenerator` `controller` 和 `razorpage` 的选项：

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

下表列出了对于 `aspnet-codegenerator controller` 是唯一的选项：

| 选项                         | 描述                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| --controllerName 或 -name      | 控制器的名称。                                                                                   |
| --useAsyncActions 或 -async    | 生成异步控制器操作。                                                                        |
| --noViews 或 -nv               | 不生成任何视图。                                                                                    |
| --restWithNoViews 或 -api      | 生成具有 REST 样式 API 的控制器。 假设 `noViews` 并且忽略任何与视图相关的选项。 |
| --readWriteActions 或 -actions | 不使用模型生成具有读/写操作的控制器。                                              |

使用 `-h` 开关获取 `aspnet-codegenerator controller` 命令方面的帮助：

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

请参阅[搭建 movie 模型的基架](xref:tutorials/first-mvc-app/adding-model)，查看 `dotnet aspnet-codegenerator controller` 示例。

### <a name="no-locrazorpage"></a>Razorpage

<a name="rp"></a>

可以通过指定新页面的名称和要使用的模板来单独搭建 Razor Pages 的基架。 支持如下模板：

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

例如，以下命令使用 Edit 模板生成MyEdit.cshtml  和 MyEdit.cshtml.cs：

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

通常不指定模板和生成的文件名，而是创建以下模板：

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

下表列出了 `aspnet-codegenerator` `razorpage` 和 `controller` 的选项：

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

下表列出了对于 `aspnet-codegenerator razorpage` 是唯一的选项：

| 选项                        | 描述                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| --namespaceName 或 -namespace | 用于生成的 PageModel 的命名空间的名称                          |
| --partialView 或 -partial     | 生成分部视图。 如果指定此选项，将忽略布局选项 -l 和 -udl。 |
| --noPageModel 或 -npm         | 切换为不针对 Empty 模板生成 PageModel 类                           |

使用 `-h` 开关获取 `aspnet-codegenerator razorpage` 命令方面的帮助：

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

请参阅[搭建 movie 模型的基架](xref:tutorials/razor-pages/model)，查看 `dotnet aspnet-codegenerator razorpage` 示例。

### Identity

请参阅[基架Identity](xref:security/authentication/scaffold-identity)
