---
title: 使用 ASP.NET Core 为本机移动应用创建后端服务
author: ardalis
description: 了解如何使用 ASP.NET Core MVC 创建后端服务，以支持本机移动应用。
ms.author: riande
ms.date: 12/05/2019
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: 4e86241771e884ba9079bcdf9a09eebc6acd62c8
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530211"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="46f64-103">使用 ASP.NET Core 为本机移动应用创建后端服务</span><span class="sxs-lookup"><span data-stu-id="46f64-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="46f64-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="46f64-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="46f64-105">移动应用可与 ASP.NET Core 后端服务通信。</span><span class="sxs-lookup"><span data-stu-id="46f64-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="46f64-106">有关从 iOS 模拟器和 Android 仿真程序连接本地 Web 服务的说明，请参阅[从 iOS 模拟器和 Android 仿真程序连接到本地 Web 服务](/xamarin/cross-platform/deploy-test/connect-to-local-web-services)。</span><span class="sxs-lookup"><span data-stu-id="46f64-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="46f64-107">查看或下载后端服务代码示例</span><span class="sxs-lookup"><span data-stu-id="46f64-107">View or download sample backend services code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="46f64-108">本机移动应用示例</span><span class="sxs-lookup"><span data-stu-id="46f64-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="46f64-109">本教程演示如何创建使用 ASP.NET Core MVC 支持本机移动应用的后端服务。</span><span class="sxs-lookup"><span data-stu-id="46f64-109">This tutorial demonstrates how to create backend services using ASP.NET Core MVC to support native mobile apps.</span></span> <span data-ttu-id="46f64-110">它使用 [Xamarin Forms ToDoRest 应用](/xamarin/xamarin-forms/data-cloud/consuming/rest) 作为其本机客户端，其中包括 Android、 iOS、 Windows Universal 和 Window Phone 设备的单独本机客户端。</span><span class="sxs-lookup"><span data-stu-id="46f64-110">It uses the [Xamarin Forms ToDoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, Windows Universal, and Window Phone devices.</span></span> <span data-ttu-id="46f64-111">你可以遵循链接中的教程来创建本机应用程序（并安装需要的免费 Xamarin 工具），以及下载 Xamarin 示例解决方案。</span><span class="sxs-lookup"><span data-stu-id="46f64-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="46f64-112">Xamarin 示例包含一个 ASP.NET Web API 2 服务项目，使用本文中的 ASP.NET Core 应用替换（客户端无需进行任何更改）。</span><span class="sxs-lookup"><span data-stu-id="46f64-112">The Xamarin sample includes an ASP.NET Web API 2 services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![在 Android 智能手机上运行的 ToDoRest 应用程序](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="46f64-114">功能</span><span class="sxs-lookup"><span data-stu-id="46f64-114">Features</span></span>

<span data-ttu-id="46f64-115">ToDoRest 应用支持列出、 添加、删除和更新待办事项。</span><span class="sxs-lookup"><span data-stu-id="46f64-115">The ToDoRest app supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="46f64-116">每个项都有一个 ID、 Name（名称）、Notes（说明）以及一个指示该项是否已完成的属性 Done。</span><span class="sxs-lookup"><span data-stu-id="46f64-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="46f64-117">待办事项的主视图如上所示，列出每个项的名称，并使用复选标记指示它是否已完成。</span><span class="sxs-lookup"><span data-stu-id="46f64-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="46f64-118">点击 `+` 图标打开“添加项”对话框：</span><span class="sxs-lookup"><span data-stu-id="46f64-118">Tapping the `+` icon opens an add item dialog:</span></span>

![“添加项”对话框](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="46f64-120">点击主列表屏幕上的项将打开一个编辑对话框，在其中可以修改项的名称、 说明以及是否完成，或删除项目：</span><span class="sxs-lookup"><span data-stu-id="46f64-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![“编辑项”对话框](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="46f64-122">此示例默认配置为使用托管在 developer.xamarin.com上的后端服务，允许只读操作。</span><span class="sxs-lookup"><span data-stu-id="46f64-122">This sample is configured by default to use backend services hosted at developer.xamarin.com, which allow read-only operations.</span></span> <span data-ttu-id="46f64-123">若要使用在你计算机上运行的下一节创建的 ASP.NET Core 应用对其进行测试，你需要更新应用程序的 `RestUrl` 常量。</span><span class="sxs-lookup"><span data-stu-id="46f64-123">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, you'll need to update the app's `RestUrl` constant.</span></span> <span data-ttu-id="46f64-124">导航到 `ToDoREST` 项目，然后打开 *Constants.cs* 文件。</span><span class="sxs-lookup"><span data-stu-id="46f64-124">Navigate to the `ToDoREST` project and open the *Constants.cs* file.</span></span> <span data-ttu-id="46f64-125">使用包含计算机 IP 的 URL 地址替换 `RestUrl`（不是 localhost 或 127.0.0.1，因为此地址用于从设备模拟器中，而不是从你的计算机中访问）。</span><span class="sxs-lookup"><span data-stu-id="46f64-125">Replace the `RestUrl` with a URL that includes your machine's IP address (not localhost or 127.0.0.1, since this address is used from the device emulator, not from your machine).</span></span> <span data-ttu-id="46f64-126">请包括端口号 (5000)。</span><span class="sxs-lookup"><span data-stu-id="46f64-126">Include the port number as well (5000).</span></span> <span data-ttu-id="46f64-127">为了测试你的服务能否在设备上正常运行，请确保没有活动的防火墙阻止访问此端口。</span><span class="sxs-lookup"><span data-stu-id="46f64-127">In order to test that your services work with a device, ensure you don't have an active firewall blocking access to this port.</span></span>

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="46f64-128">创建 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="46f64-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="46f64-129">在 Visual Studio 中创建一个新的 ASP.NET Core Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="46f64-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="46f64-130">选择 Web API 模板和 No Authentication（无身份验证）。</span><span class="sxs-lookup"><span data-stu-id="46f64-130">Choose the Web API template and No Authentication.</span></span> <span data-ttu-id="46f64-131">将项目命名为 *ToDoApi*。</span><span class="sxs-lookup"><span data-stu-id="46f64-131">Name the project *ToDoApi*.</span></span>

![“新建 ASP.NET Web 应用程序”对话框，其中已选中 Web API 项目模板](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="46f64-133">对于向端口 5000 进行的请求，应用程序均需作出响应。</span><span class="sxs-lookup"><span data-stu-id="46f64-133">The application should respond to all requests made to port 5000.</span></span> <span data-ttu-id="46f64-134">更新 Program.cs，使其包含 `.UseUrls("http://*:5000")`，以便实现以下操作：</span><span class="sxs-lookup"><span data-stu-id="46f64-134">Update *Program.cs* to include `.UseUrls("http://*:5000")` to achieve this:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> <span data-ttu-id="46f64-135">请确保直接运行应用程序，而不是在 IIS Express 后运行，因为在默认情况下，后者会忽略非本地请求。</span><span class="sxs-lookup"><span data-stu-id="46f64-135">Make sure you run the application directly, rather than behind IIS Express, which ignores non-local requests by default.</span></span> <span data-ttu-id="46f64-136">从命令提示符处运行 [dotnet run](/dotnet/core/tools/dotnet-run)，或从 Visual Studio 工具栏中的“调试目标”下拉列表中选择应用程序名称配置文件。</span><span class="sxs-lookup"><span data-stu-id="46f64-136">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the application name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="46f64-137">添加一个模型类来表示待办事项。</span><span class="sxs-lookup"><span data-stu-id="46f64-137">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="46f64-138">使用 `[Required]` 属性标记必需字段：</span><span class="sxs-lookup"><span data-stu-id="46f64-138">Mark required fields with the `[Required]` attribute:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

<span data-ttu-id="46f64-139">API 方法需要通过某种方式处理数据。</span><span class="sxs-lookup"><span data-stu-id="46f64-139">The API methods require some way to work with data.</span></span> <span data-ttu-id="46f64-140">使用原始 Xamarin 示例所用的 `IToDoRepository` 接口：</span><span class="sxs-lookup"><span data-stu-id="46f64-140">Use the same `IToDoRepository` interface the original Xamarin sample uses:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

<span data-ttu-id="46f64-141">在此示例中，该实现仅使用一个专用项集合：</span><span class="sxs-lookup"><span data-stu-id="46f64-141">For this sample, the implementation just uses a private collection of items:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

<span data-ttu-id="46f64-142">在 *Startup.cs* 中配置该实现:</span><span class="sxs-lookup"><span data-stu-id="46f64-142">Configure the implementation in *Startup.cs*:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

<span data-ttu-id="46f64-143">现可创建 ToDoItemsController。</span><span class="sxs-lookup"><span data-stu-id="46f64-143">At this point, you're ready to create the *ToDoItemsController*.</span></span>

> [!TIP]
> <span data-ttu-id="46f64-144">有关创建 Web API 的详细信息，请参阅[使用 ASP.NET Core MVC 和 Visual Studio 生成首个 Web API](../tutorials/first-web-api.md)。</span><span class="sxs-lookup"><span data-stu-id="46f64-144">Learn more about creating web APIs in [Build your first Web API with ASP.NET Core MVC and Visual Studio](../tutorials/first-web-api.md).</span></span>

## <a name="creating-the-controller"></a><span data-ttu-id="46f64-145">创建控制器</span><span class="sxs-lookup"><span data-stu-id="46f64-145">Creating the Controller</span></span>

<span data-ttu-id="46f64-146">在项目中添加新控制器 *ToDoItemsController*。</span><span class="sxs-lookup"><span data-stu-id="46f64-146">Add a new controller to the project, *ToDoItemsController*.</span></span> <span data-ttu-id="46f64-147">它应继承 Microsoft.AspNetCore.Mvc.Controller。</span><span class="sxs-lookup"><span data-stu-id="46f64-147">It should inherit from Microsoft.AspNetCore.Mvc.Controller.</span></span> <span data-ttu-id="46f64-148">添加 `Route` 属性以指示控制器将处理路径以 `api/todoitems` 开始的请求。</span><span class="sxs-lookup"><span data-stu-id="46f64-148">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="46f64-149">路由中的 `[controller]` 标记会被控制器的名称代替（省略 `Controller` 后缀），这对全局路由特别有用。</span><span class="sxs-lookup"><span data-stu-id="46f64-149">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="46f64-150">详细了解 [路由](../fundamentals/routing.md)。</span><span class="sxs-lookup"><span data-stu-id="46f64-150">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="46f64-151">控制器需要 `IToDoRepository` 才能正常运行；通过控制器的构造函数请求该类型的实例。</span><span class="sxs-lookup"><span data-stu-id="46f64-151">The controller requires an `IToDoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="46f64-152">在运行时，此实例将使用框架对 [依赖关系注入](../fundamentals/dependency-injection.md) 的支持来提供。</span><span class="sxs-lookup"><span data-stu-id="46f64-152">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

<span data-ttu-id="46f64-153">此 API 支持四个不同的 HTTP 谓词来执行对数据源的 CRUD（创建、读取、更新、删除）操作。</span><span class="sxs-lookup"><span data-stu-id="46f64-153">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="46f64-154">最简单的是读取操作，它对应于 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="46f64-154">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="46f64-155">读取项目</span><span class="sxs-lookup"><span data-stu-id="46f64-155">Reading Items</span></span>

<span data-ttu-id="46f64-156">要请求项列表，可对 `List` 方法使用 GET 请求。</span><span class="sxs-lookup"><span data-stu-id="46f64-156">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="46f64-157">`[HttpGet]` 方法的 `List` 属性指示此操作应仅处理 GET 请求。</span><span class="sxs-lookup"><span data-stu-id="46f64-157">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="46f64-158">此操作的路由是在控制器上指定的路由。</span><span class="sxs-lookup"><span data-stu-id="46f64-158">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="46f64-159">你不一定必须将操作名称用作路由的一部分。</span><span class="sxs-lookup"><span data-stu-id="46f64-159">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="46f64-160">你只需确保每个操作都有唯一的和明确的路由。</span><span class="sxs-lookup"><span data-stu-id="46f64-160">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="46f64-161">路由属性可以分别应用在控制器和方法级别，以此生成特定的路由。</span><span class="sxs-lookup"><span data-stu-id="46f64-161">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

<span data-ttu-id="46f64-162">`List` 方法返回 200 OK 响应代码和所有 ToDo 项，并序列化为 JSON 。</span><span class="sxs-lookup"><span data-stu-id="46f64-162">The `List` method returns a 200 OK response code and all of the ToDo items, serialized as JSON.</span></span>

<span data-ttu-id="46f64-163">你可以使用多种工具测试新的 API 方法，如 [Postman](https://www.getpostman.com/docs/)，如此处所示：</span><span class="sxs-lookup"><span data-stu-id="46f64-163">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Postman 控制台，其中显示一个 todoitem 的 GET 请求，以及显示所返回的三个项目的 JSON 的响应正文](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="46f64-165">创建项目</span><span class="sxs-lookup"><span data-stu-id="46f64-165">Creating Items</span></span>

<span data-ttu-id="46f64-166">按照约定，创建新数据项映射到 HTTP POST 谓词。</span><span class="sxs-lookup"><span data-stu-id="46f64-166">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="46f64-167">`Create` 方法具有应用于它的 `[HttpPost]` 属性，并接受 `ToDoItem` 实例。</span><span class="sxs-lookup"><span data-stu-id="46f64-167">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `ToDoItem` instance.</span></span> <span data-ttu-id="46f64-168">由于 `item` 参数在 POST 的正文中传递，因此该参数会指定 `[FromBody]` 属性。</span><span class="sxs-lookup"><span data-stu-id="46f64-168">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="46f64-169">在该方法中，会检查项的有效性和之前是否存在于数据存储，并且如果没有任何问题，则使用存储库添加。</span><span class="sxs-lookup"><span data-stu-id="46f64-169">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="46f64-170">检查 `ModelState.IsValid` 将执行 [模型验证](../mvc/models/validation.md)，应该在每个接受用户输入的 API 方法中执行此步骤。</span><span class="sxs-lookup"><span data-stu-id="46f64-170">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

<span data-ttu-id="46f64-171">示例中使用一个枚举，后者包含传递到移动客户端的错误代码：</span><span class="sxs-lookup"><span data-stu-id="46f64-171">The sample uses an enum containing error codes that are passed to the mobile client:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

<span data-ttu-id="46f64-172">使用 Postman 测试添加新项，选择 POST 谓词并在请求正文中以 JSON 格式提供新对象。</span><span class="sxs-lookup"><span data-stu-id="46f64-172">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="46f64-173">你还应添加一个请求标头指定 `Content-Type` 为 `application/json`。</span><span class="sxs-lookup"><span data-stu-id="46f64-173">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![显示 POST 和响应的 Postman 控制台](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="46f64-175">该方法返回在响应中新建的项。</span><span class="sxs-lookup"><span data-stu-id="46f64-175">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="46f64-176">更新项目</span><span class="sxs-lookup"><span data-stu-id="46f64-176">Updating Items</span></span>

<span data-ttu-id="46f64-177">通过使用 HTTP PUT 请求来修改记录。</span><span class="sxs-lookup"><span data-stu-id="46f64-177">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="46f64-178">除了此更改之外，`Edit` 方法几乎与 `Create` 完全相同。</span><span class="sxs-lookup"><span data-stu-id="46f64-178">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="46f64-179">请注意，如果未找到记录，则 `Edit` 操作将返回 `NotFound`(404) 响应。</span><span class="sxs-lookup"><span data-stu-id="46f64-179">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

<span data-ttu-id="46f64-180">若要使用 Postman 进行测试，将谓词更改为 PUT。</span><span class="sxs-lookup"><span data-stu-id="46f64-180">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="46f64-181">在请求正文中指定要更新的对象数据。</span><span class="sxs-lookup"><span data-stu-id="46f64-181">Specify the updated object data in the Body of the request.</span></span>

![显示 PUT 和响应的 Postman 控制台](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="46f64-183">为了与预先存在的 API 保持一致，此方法在成功时返回 `NoContent` (204) 响应。</span><span class="sxs-lookup"><span data-stu-id="46f64-183">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="46f64-184">删除项</span><span class="sxs-lookup"><span data-stu-id="46f64-184">Deleting Items</span></span>

<span data-ttu-id="46f64-185">删除记录可以通过向服务发出 DELETE 请求并传递要删除项的 ID 来完成。</span><span class="sxs-lookup"><span data-stu-id="46f64-185">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="46f64-186">与更新一样，请求的项不存在时会收到 `NotFound` 响应。</span><span class="sxs-lookup"><span data-stu-id="46f64-186">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="46f64-187">请求成功会得到 `NoContent` (204) 响应。</span><span class="sxs-lookup"><span data-stu-id="46f64-187">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

<span data-ttu-id="46f64-188">请注意，在测试删除功能时，请求正文中不需要任何内容。</span><span class="sxs-lookup"><span data-stu-id="46f64-188">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![显示 DELETE 和响应的 Postman 控制台](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a><span data-ttu-id="46f64-190">常见的 Web API 约定</span><span class="sxs-lookup"><span data-stu-id="46f64-190">Common Web API Conventions</span></span>

<span data-ttu-id="46f64-191">开发应用程序的后端服务时，你将想要使用一组一致的约定或策略来处理横切关注点。</span><span class="sxs-lookup"><span data-stu-id="46f64-191">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="46f64-192">例如，在上面所示服务中，针对不存在的特定记录的请求会收到 `NotFound` 响应，而不是`BadRequest` 响应。</span><span class="sxs-lookup"><span data-stu-id="46f64-192">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="46f64-193">同样，对于此服务，传递模型绑定类型的命令始终检查 `ModelState.IsValid` 并为无效的模型类型返回 `BadRequest`。</span><span class="sxs-lookup"><span data-stu-id="46f64-193">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="46f64-194">一旦为 Api 指定通用策略，一般可以将其封装在 [Filter（筛选器）](../mvc/controllers/filters.md)。</span><span class="sxs-lookup"><span data-stu-id="46f64-194">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="46f64-195">详细了解 [如何封装 ASP.NET Core MVC 应用程序中的通用 API 策略](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters)。</span><span class="sxs-lookup"><span data-stu-id="46f64-195">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="46f64-196">其他资源</span><span class="sxs-lookup"><span data-stu-id="46f64-196">Additional resources</span></span>

- [<span data-ttu-id="46f64-197">Xamarin： Web 服务身份验证</span><span class="sxs-lookup"><span data-stu-id="46f64-197">Xamarin.Forms: Web Service Authentication</span></span>](/xamarin/xamarin-forms/data-cloud/authentication/)
- [<span data-ttu-id="46f64-198">Xamarin：使用 RESTful Web 服务</span><span class="sxs-lookup"><span data-stu-id="46f64-198">Xamarin.Forms: Consume a RESTful Web Service</span></span>](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [<span data-ttu-id="46f64-199">Microsoft Learn：在 Xamarin 应用中使用 REST web 服务</span><span class="sxs-lookup"><span data-stu-id="46f64-199">Microsoft Learn: Consume REST web services in Xamarin Apps</span></span>](/learn/modules/consume-rest-services/)
- [<span data-ttu-id="46f64-200">Microsoft Learn：使用 ASP.NET Core 创建 web API</span><span class="sxs-lookup"><span data-stu-id="46f64-200">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
