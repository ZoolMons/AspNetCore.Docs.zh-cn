---
title: 教程：读取相关数据 - ASP.NET MVC 和 EF Core
description: 本教程将读取并显示相关数据 - 即 Entity Framework 加载到导航属性中的数据。
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
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
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: bf6fe42ae87637d0e8b9ce17d5d0c05d28b24501
ms.sourcegitcommit: 7b6781051d341a1daaf46c6a4368fa8a5701db81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2021
ms.locfileid: "105638752"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a>教程：读取相关数据 - ASP.NET MVC 和 EF Core

前面教程创建了学校数据模型。 本教程将读取并显示相关数据 - 即 Entity Framework 加载到导航属性中的数据。

下图是将会用到的页面。

![“课程索引”页](read-related-data/_static/courses-index.png)

![“讲师索引”页](read-related-data/_static/instructors-index.png)

在本教程中，你将了解：

> [!div class="checklist"]
> * 了解如何加载相关数据
> * 创建“课程”页
> * 创建“讲师”页
> * 了解显式加载

## <a name="prerequisites"></a>先决条件

* [创建复杂数据模型](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a>了解如何加载相关数据

对象关系映射 (ORM) 软件（如 Entity Framework）可通过多种方式将相关数据加载到实体的导航属性中：

* [预先加载：](/ef/core/querying/related-data)读取该实体时，会同时检索相关数据。 此时通常会出现单一联接查询，检索所有必需数据。 可使用 `Include` 和 `ThenInclude` 方法指定 Entity Framework Core 中的预先加载。

  ![预先加载示例](read-related-data/_static/eager-loading.png)

  可在单独查询中检索一些数据，EF 会“修正”导航属性。  也就是说，EF 会自动添加单独检索的实体，将其添加到之前检索的实体的导航属性中所属的位置。 对于检索相关数据的查询，可使用 `Load` 方法，而不采用返回列表或对象的方法，如 `ToList` 或 `Single`。

  ![单独查询示例](read-related-data/_static/separate-queries.png)

* [显式加载：](/ef/core/querying/related-data)首次读取实体时，不检索相关数据。 如有需要，可编写检索相关数据的代码。 就像使用单独查询进行预先加载一样，显式加载时会向数据库发送多个查询。 二者的区别在于，代码通过显式加载指定要加载的导航属性。 在 Entity Framework Core 1.1 中，可使用 `Load` 方法执行显式加载。 例如：

  ![显式加载示例](read-related-data/_static/explicit-loading.png)

* [推迟加载：](/ef/core/querying/related-data)首次读取实体时，不检索相关数据。 然而，首次尝试访问导航属性时，会自动检索导航属性所需的数据。 每次首次尝试从导航属性获取数据时，都向数据库发送查询。 Entity Framework Core 1.0 不支持延迟加载。

### <a name="performance-considerations"></a>性能注意事项

如果知道自己需要每个检索的实体的相关数据，选择预先加载可获得最佳性能，因为相比每个检索的实体的单独查询，发送到数据库的单个查询更加有效。 例如，假设每个系有十个相关课程。 预先加载所有相关数据时，只会进行单一（联接）查询，往返数据库一次。 单独查询每个系的课程时，会往返数据库十一次。 延迟较高时，额外往返数据库对性能尤为不利。

另一方面，在某些情况下，单独查询会更加高效。 在一个查询中预先加载所有相关数据时，可能会生成一个非常复杂的联接，SQL Server 无法有效处理该联接。 或者，如果你正在处理一组实体且只需访问其子集的导航属性，那么采用单独查询可获得更佳性能，因为预先加载所有数据后，会检索不需要的数据。 如果看重性能，那么最好测试两种方式的性能，以便做出最佳选择。

## <a name="create-a-courses-page"></a>创建“课程”页

`Course` 实体包括导航属性，其中包含分配有课程的系的 `Department` 实体。 若要在课程列表中显示接受分配的系的名称，需从位于 `Name` 导航属性中的 `Department` 实体获取 `Course.Department` 属性。

使用与带视图的 MVC 控制器相同的选项，及之前用于 `StudentsController` 的 Entity Framework 基架为 `Course` 实体类型创建名为 `CoursesController` 的控制器，如下图所示：

![添加课程控制器](read-related-data/_static/add-courses-controller.png)

打开 CoursesController.cs 并检查 `Index` 方法。 自动基架使用 `Include` 方法为 `Department` 导航属性指定了预先加载。

将 `Index` 方法替换为以下代码，该代码为返回 Course 实体（是 `courses` 而不是 `schoolContext`）的 `IQueryable` 赋予了更合适的名称：

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

在 Views/Courses/Index.cshtml 中，将模板代码替换为以下代码。 突出显示所作更改：

[!code-cshtml[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

已对基架代码进行了如下更改：

* 将标题从“索引”更改为“课程”。 

* 添加了显示 `CourseID` 属性值的“数字”列。 默认情况下，不针对主键进行架构，因为对最终用户而言，它们通常没有意义。 但在这种情况下主键是有意义的，而你需要将其呈现出来。

* 更改“院系”列，显示院系名称。 该代码显示已加载到 `Department` 导航属性中的 `Department` 实体的 `Name` 属性：

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

运行应用并选择“课程”选项卡，查看包含系名称的列表。

![“课程索引”页](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a>创建“讲师”页

本节将为 `Instructor` 实体创建一个控制器和视图，从而显示“讲师”页：

![“讲师索引”页](read-related-data/_static/instructors-index.png)

该页面通过以下方式读取和显示相关数据：

* 讲师列表显示 `OfficeAssignment` 实体的相关数据。 `Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一的关系。 将预先加载 `OfficeAssignment` 实体。 如前所述，需要主表所有检索行的相关数据时，预先加载通常更有效。 在这种情况下，你希望显示所有显示的讲师的办公室分配情况。

* 用户选择一名讲师时，显示相关 `Course` 实体。 `Instructor` 和 `Course` 实体之间存在多对多关系。 对 `Course` 实体及其相关的 `Department` 实体使用预先加载。 在这种情况下，单独查询可能更有效，因为仅需显示所选讲师的课程。 但此示例显示的是如何在本身就位于导航属性内的实体中预先加载导航属性。

* 用户选择一门课程时，会显示 `Enrollments` 实体集的相关数据。 `Course` 和 `Enrollment` 实体之间存在一对多的关系。 单独查询 `Enrollment` 实体及其相关 `Student` 实体。

### <a name="create-a-view-model-for-the-instructor-index-view"></a>创建“讲师索引”视图的视图模型

“讲师”页显示来自三个不同表格的数据。 因此将创建包含三个属性的视图模型，每个属性都包含一个表的数据。

在 SchoolViewModels 文件夹中，创建 InstructorIndexData.cs，并使用以下代码替换现有代码 ：

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>创建讲师控制器和视图

使用 EF 读/写操作创建讲师控制器，如下图所示：

![添加讲师控制器](read-related-data/_static/add-instructors-controller.png)

打开 InstructorsController.cs 并为 ViewModels 名称空间添加 using 语句：

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

使用以下代码替换 Index 方法，预先加载相关数据并将其放入视图模型。

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

该方法接受可选路由数据 (`id`) 和查询字符串参数 (`courseID`)，二者提供所选讲师和课程的 ID 值。 参数由页面上的“选择”超链接提供。

代码先创建一个视图模型实例，并在其中放入讲师列表。 代码指定预先加载 `Instructor.OfficeAssignment` 和 `Instructor.CourseAssignments` 导航属性。 在 `CourseAssignments` 属性中，加载 `Course` 属性，在其中加载 `Enrollments` 和 `Department` 属性，同时在每个 `Enrollment` 实体中加载 `Student` 属性。

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

由于视图始终需要 `OfficeAssignment` 实体，因此更有效的做法是在同一查询中获取。 在网页中选择讲师后，需要 Course 实体，因此只有在页面频繁显示选中课程时，单个查询才比多个查询更有效。

代码重复 `CourseAssignments` 和 `Course`，因为你需要 `Course` 中的两个属性。 `ThenInclude` 调用的第一个字符串获取 `CourseAssignment.Course`、`Course.Enrollments` 和 `Enrollment.Student`。

可在[此处](/ef/core/querying/related-data/eager#including-multiple-levels)了解有关包含多级相关数据的详细信息。

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

此时，代码中的另一个 `ThenInclude` 将成为 `Student` 的导航属性，你不需要该属性。 但调用 `Include` 是从 `Instructor` 属性重新开始，因此必须再次遍历该链，这次指定 `Course.Department` 而不是 `Course.Enrollments`。

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

选择讲师时，将执行以下代码。 从视图模型中的讲师列表检索所选讲师。 然后向视图模型的 `Courses` 属性加载来自讲师 `CourseAssignments` 导航属性的 `Course` 实体。

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

`Where` 方法返回一个集合，但在这种情况下，向该方法传入条件后，只返回一个 Instructor 实体。 `Single` 方法将集合转换为单个 `Instructor` 实体，让你可以访问该实体的 `CourseAssignments` 属性。 `CourseAssignments` 属性包含多个 `CourseAssignment` 实体，而你只需要相关的 `Course` 实体。

如果知道集合只有一个项，则可在集合上使用 `Single` 方法。 如果集合为空或包含多个项，`Single` 方法将引发异常。 还可使用 `SingleOrDefault`，该方式在集合为空时返回默认值（本例中为 null）。 但在这种情况下，仍会引发异常（尝试在 null 引用上查找 `Courses` 属性时），并且异常消息不会清楚指出异常原因。 调用 `Single` 方法时，还可传入 Where 条件，而不是分别调用 `Where` 方法：

```csharp
.Single(i => i.ID == id.Value)
```

而不是：

```csharp
.Where(i => i.ID == id.Value).Single()
```

接着，如果选择了课程，则从视图模型中的课程列表中检索所选课程。 然后，视图模型的 `Enrollments` 属性加载该课程 `Enrollments` 导航属性中的 Enrollment 实体。

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>修改讲师索引视图

在 Views/Instructors/Index.cshtml 中，将模板代码替换为以下代码。 突出显示所作更改。

::: moniker range=">= aspnetcore-2.2"

[!code-cshtml[](intro/samples/5cu-snap/Views/Instructors/Index.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

已对现有代码进行了如下更改：

* 将模型类更改为了 `InstructorIndexData`。

* 将页标题从“索引”更改为了“讲师” 。

* 添加了仅在 `item.OfficeAssignment` 不为 null 时才显示 `item.OfficeAssignment.Location` 的“办公室”列。 （由于这是一对零或一的关系，因此可能没有相关的 OfficeAssignment 实体。）

  ```cshtml
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* 添加了显示每位讲师所授课程的“课程”列。 有关更多信息，请参阅 Razor 语法文章的[显式行转换](xref:mvc/views/razor#explicit-line-transition)部分。

* 添加了代码来将 Bootstrap CSS 类附有条件地添加到所选讲师的 `tr` 元素中。 此类会设置选定行的背景色。

* 紧贴每行其他链接的前端添加了标有 Select 的新超链接，从而使所选讲师 ID 发送到 `Index` 方法。

  ```cshtml
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

运行应用并选择“讲师”选项卡。没有相关 OfficeAssignment 实体时，该页面显示相关 OfficeAssignment 实体的 Location 属性和空表格单元格。

![未选择“讲师索引”页中的任何项](read-related-data/_static/instructors-index-no-selection.png)

在 Views/Instructors/Index.cshtml 文件中，关闭表格元素（在文件末尾）后，添加以下代码。 选择讲师时，此代码显示与讲师相关的课程列表。

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=63-98)]

此代码读取视图模型的 `Courses` 属性以显示课程列表。 它还提供 Select 超链接，该链接可将所选课程的 ID 发送到 `Index` 操作方法。

刷新页面并选择讲师。 此时会出现一个网格，其中显示有分配给所选讲师的课程，且还显示有每个课程的分配系的名称。

![已选择“讲师索引”页中的讲师](read-related-data/_static/instructors-index-instructor-selected.png)

在刚刚添加的代码块后，添加以下代码。 选择课程后，代码将显示参与课程的学生列表。

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=101-125)]

此代码读取视图模型的 `Enrollments` 属性，从而显示参与课程的学生列表。

再次刷新该页并选择讲师。 然后选择一门课程，查看参与的学生列表及其成绩。

![已选择“讲师索引”页中的讲师和课程](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a>关于显式加载

在 InstructorsController.cs 中检索讲师列表时，指定了预先加载 `CourseAssignments` 导航属性。

假设你希望用户在选中讲师和课程时尽量少查看注册情况。 此时建议只在有请求时加载注册数据。 若要查看如何执行显式加载的示例，请使用以下代码替换 `Index` 方法，这将删除预先加载 `Enrollments` 并显式加载该属性。 代码所作更改为突出显示状态。

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

新代码将从检索 Instructor 实体的代码中删除注册数据的 `ThenInclude` 方法调用。 它还会删除 `AsNoTracking`。  如果选择了讲师和课程，突出显示的代码会检索所选课程的 `Enrollment` 实体，及每个 `Enrollment` 的 `Student` 实体。

运行应用，立即转到“讲师”索引页，尽管已经更改了数据的检索方式，但该页上显示的内容没有任何不同。

## <a name="get-the-code"></a>获取代码

[下载或查看已完成的应用程序。](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 已了解如何加载相关数据
> * 已创建“课程”页
> * 已创建“讲师”页
> * 已了解显式加载

请继续阅读下一篇教程，了解如何更新相关数据。

> [!div class="nextstepaction"]
> [更新相关数据](update-related-data.md)
