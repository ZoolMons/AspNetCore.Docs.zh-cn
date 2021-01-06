---
title: 第 8 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 并发
author: rick-anderson
description: Razor 页面和实体框架教程系列第 8 部分。
ms.author: riande
ms.custom: mvc
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
uid: data/ef-rp/concurrency
ms.openlocfilehash: 573a509041bfb34faf50a227c451824db03f92ee
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053990"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a><span data-ttu-id="bf214-103">第 8 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 并发</span><span class="sxs-lookup"><span data-stu-id="bf214-103">Part 8, Razor Pages with EF Core in ASP.NET Core - Concurrency</span></span>

<span data-ttu-id="bf214-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra) 和 [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="bf214-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bf214-105">本教程介绍如何处理多个用户并发更新同一实体（同时）时出现的冲突。</span><span class="sxs-lookup"><span data-stu-id="bf214-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="bf214-106">并发冲突</span><span class="sxs-lookup"><span data-stu-id="bf214-106">Concurrency conflicts</span></span>

<span data-ttu-id="bf214-107">在以下情况下，会发生并发冲突：</span><span class="sxs-lookup"><span data-stu-id="bf214-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="bf214-108">用户导航到实体的编辑页面。</span><span class="sxs-lookup"><span data-stu-id="bf214-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="bf214-109">第一个用户的更改还未写入数据库之前，另一用户更新同一实体。</span><span class="sxs-lookup"><span data-stu-id="bf214-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="bf214-110">如果未启用并发检测，则最后更新数据库的人员将覆盖其他用户的更改。</span><span class="sxs-lookup"><span data-stu-id="bf214-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="bf214-111">如果这种风险是可以接受的，则并发编程的成本可能会超过收益。</span><span class="sxs-lookup"><span data-stu-id="bf214-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="bf214-112">悲观并发（锁定）</span><span class="sxs-lookup"><span data-stu-id="bf214-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="bf214-113">预防并发冲突的一种方法是使用数据库锁定。</span><span class="sxs-lookup"><span data-stu-id="bf214-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="bf214-114">这称为悲观并发。</span><span class="sxs-lookup"><span data-stu-id="bf214-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="bf214-115">应用在读取要更新的数据库行之前，将请求锁定。</span><span class="sxs-lookup"><span data-stu-id="bf214-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="bf214-116">锁定某一行的更新访问权限之后，其他用户在第一个锁定释放之前无法锁定该行。</span><span class="sxs-lookup"><span data-stu-id="bf214-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="bf214-117">管理锁定有缺点。</span><span class="sxs-lookup"><span data-stu-id="bf214-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="bf214-118">它的编程可能很复杂，并且随着用户增加可能会导致性能问题。</span><span class="sxs-lookup"><span data-stu-id="bf214-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="bf214-119">Entity Framework Core 未提供对它的内置支持，并且本教程不展示其实现方式。</span><span class="sxs-lookup"><span data-stu-id="bf214-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="bf214-120">开放式并发</span><span class="sxs-lookup"><span data-stu-id="bf214-120">Optimistic concurrency</span></span>

<span data-ttu-id="bf214-121">乐观并发允许发生并发冲突，并在并发冲突发生时作出正确反应。</span><span class="sxs-lookup"><span data-stu-id="bf214-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="bf214-122">例如，Jane 访问院系编辑页面，将英语系的预算从 350,000.00 美元更改为 0.00 美元。</span><span class="sxs-lookup"><span data-stu-id="bf214-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![将预算更改为零](concurrency/_static/change-budget30.png)

<span data-ttu-id="bf214-124">在 Jane 单击“保存”之前，John 访问了相同页面，并将开始日期字段从 2007/1/9 更改为 2013/1/9。</span><span class="sxs-lookup"><span data-stu-id="bf214-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![将开始日期更改为 2013](concurrency/_static/change-date30.png)

<span data-ttu-id="bf214-126">Jane 单击“保存”后看到更改生效，因为浏览器会显示预算金额为零的“索引”页面。</span><span class="sxs-lookup"><span data-stu-id="bf214-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="bf214-127">John 单击“编辑”页面上的“保存”，但页面的预算仍显示为 350,000.00 美元。</span><span class="sxs-lookup"><span data-stu-id="bf214-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="bf214-128">接下来的情况取决于并发冲突的处理方式：</span><span class="sxs-lookup"><span data-stu-id="bf214-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="bf214-129">可以跟踪用户已修改的属性，并仅更新数据库中相应的列。</span><span class="sxs-lookup"><span data-stu-id="bf214-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="bf214-130">在这种情况下，数据不会丢失。</span><span class="sxs-lookup"><span data-stu-id="bf214-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="bf214-131">两个用户更新了不同的属性。</span><span class="sxs-lookup"><span data-stu-id="bf214-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="bf214-132">下次有人浏览英语系时，将看到 Jane 和 John 两个人的更改。</span><span class="sxs-lookup"><span data-stu-id="bf214-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="bf214-133">这种更新方法可以减少导致数据丢失的冲突数。</span><span class="sxs-lookup"><span data-stu-id="bf214-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="bf214-134">这种方法具有一些缺点：</span><span class="sxs-lookup"><span data-stu-id="bf214-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="bf214-135">无法避免数据丢失，如果对同一属性进行竞争性更改的话。</span><span class="sxs-lookup"><span data-stu-id="bf214-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="bf214-136">通常不适用于 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="bf214-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="bf214-137">它需要维持重要状态，以便跟踪所有提取值和新值。</span><span class="sxs-lookup"><span data-stu-id="bf214-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="bf214-138">维持大量状态可能影响应用性能。</span><span class="sxs-lookup"><span data-stu-id="bf214-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="bf214-139">可能会增加应用复杂性（与实体上的并发检测相比）。</span><span class="sxs-lookup"><span data-stu-id="bf214-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="bf214-140">可让 John 的更改覆盖 Jane 的更改。</span><span class="sxs-lookup"><span data-stu-id="bf214-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="bf214-141">下次有人浏览英语系时，将看到 2013/9/1 和提取的值 350,000.00 美元。</span><span class="sxs-lookup"><span data-stu-id="bf214-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="bf214-142">这种方法称为“客户端优先”或“最后一个优先”方案 。</span><span class="sxs-lookup"><span data-stu-id="bf214-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="bf214-143">（客户端的所有值优先于数据存储的值。）如果不对并发处理进行任何编码，则自动执行“客户端优先”。</span><span class="sxs-lookup"><span data-stu-id="bf214-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="bf214-144">可以阻止在数据库中更新 John 的更改。</span><span class="sxs-lookup"><span data-stu-id="bf214-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="bf214-145">应用通常会：</span><span class="sxs-lookup"><span data-stu-id="bf214-145">Typically, the app would:</span></span>

  * <span data-ttu-id="bf214-146">显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="bf214-146">Display an error message.</span></span>
  * <span data-ttu-id="bf214-147">显示数据的当前状态。</span><span class="sxs-lookup"><span data-stu-id="bf214-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="bf214-148">允许用户重新应用更改。</span><span class="sxs-lookup"><span data-stu-id="bf214-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="bf214-149">这称为“存储优先”方案。</span><span class="sxs-lookup"><span data-stu-id="bf214-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="bf214-150">（数据存储值优先于客户端提交的值。）本教程实施“存储优先”方案。</span><span class="sxs-lookup"><span data-stu-id="bf214-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="bf214-151">此方法可确保用户在未收到警报时不会覆盖任何更改。</span><span class="sxs-lookup"><span data-stu-id="bf214-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="bf214-152">EF Core 中的冲突检测</span><span class="sxs-lookup"><span data-stu-id="bf214-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="bf214-153">EF Core 在检测到冲突时会引发 `DbConcurrencyException` 异常。</span><span class="sxs-lookup"><span data-stu-id="bf214-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="bf214-154">数据模型必须配置为启用冲突检测。</span><span class="sxs-lookup"><span data-stu-id="bf214-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="bf214-155">启用冲突检测的选项包括以下项：</span><span class="sxs-lookup"><span data-stu-id="bf214-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="bf214-156">配置 EF Core，在 Update 或 Delete 命令的 Where 子句中包含配置为[并发令牌](/ef/core/modeling/concurrency)的列的原始值。</span><span class="sxs-lookup"><span data-stu-id="bf214-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="bf214-157">调用 `SaveChanges` 时，Where 子句查找使用 <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> 特性注释的所有属性的原始值。</span><span class="sxs-lookup"><span data-stu-id="bf214-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> attribute.</span></span> <span data-ttu-id="bf214-158">如果在第一次读取行之后有任意并发令牌属性发生了更改，更新语句将无法查找到要更新的行。</span><span class="sxs-lookup"><span data-stu-id="bf214-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="bf214-159">EF Core 将其解释为并发冲突。</span><span class="sxs-lookup"><span data-stu-id="bf214-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="bf214-160">对于包含许多列的数据库表，此方法可能导致非常多的 Where 子句，并且可能需要大量的状态。</span><span class="sxs-lookup"><span data-stu-id="bf214-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="bf214-161">因此通常不建议使用此方法，并且它也不是本教程中使用的方法。</span><span class="sxs-lookup"><span data-stu-id="bf214-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="bf214-162">数据库表中包含一个可用于确定某行更改时间的跟踪列。</span><span class="sxs-lookup"><span data-stu-id="bf214-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="bf214-163">在 SQL Server 数据库中，跟踪列的数据类型是 `rowversion`。</span><span class="sxs-lookup"><span data-stu-id="bf214-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="bf214-164">`rowversion` 值是一个序列号，该编号随着每次行的更新递增。</span><span class="sxs-lookup"><span data-stu-id="bf214-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="bf214-165">在 Update 或 Delete 命令中，Where 子句包含跟踪列的原始值（原始行版本号）。</span><span class="sxs-lookup"><span data-stu-id="bf214-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="bf214-166">如果其他用户已更改要更新的行，则 `rowversion` 列中的值与原始值不同。</span><span class="sxs-lookup"><span data-stu-id="bf214-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="bf214-167">在这种情况下，Update 或 Delete 语句会由于 Where 子句而无法找到要更新的行。</span><span class="sxs-lookup"><span data-stu-id="bf214-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="bf214-168">如果 Update 或 Delete 命令未影响任何行，EF Core 会引发并发异常。</span><span class="sxs-lookup"><span data-stu-id="bf214-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="bf214-169">添加跟踪属性</span><span class="sxs-lookup"><span data-stu-id="bf214-169">Add a tracking property</span></span>

<span data-ttu-id="bf214-170">在 Models/Department.cs 中，添加名为 RowVersion 的跟踪属性：</span><span class="sxs-lookup"><span data-stu-id="bf214-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="bf214-171"><xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 特性用于将列标识为并发跟踪列。</span><span class="sxs-lookup"><span data-stu-id="bf214-171">The <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="bf214-172">Fluent API 是指定跟踪属性的另一种方法：</span><span class="sxs-lookup"><span data-stu-id="bf214-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="bf214-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf214-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf214-174">对于 SQL Server 数据库，定义为字节数组的实体属性上的 `[Timestamp]` 特性：</span><span class="sxs-lookup"><span data-stu-id="bf214-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="bf214-175">使列包含在 DELETE 和 UPDATE WHERE 子句中。</span><span class="sxs-lookup"><span data-stu-id="bf214-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="bf214-176">将数据库中的列类型设置为 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="bf214-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="bf214-177">数据库生成有序的行版本号，该版本号随着每次行的更新递增。</span><span class="sxs-lookup"><span data-stu-id="bf214-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="bf214-178">在 `Update` 或 `Delete` 命令中，`Where` 子句包括提取的行版本值。</span><span class="sxs-lookup"><span data-stu-id="bf214-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="bf214-179">如果要更新的行在提取之后已更改：</span><span class="sxs-lookup"><span data-stu-id="bf214-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="bf214-180">当前的行版本值与提取值不相匹配。</span><span class="sxs-lookup"><span data-stu-id="bf214-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="bf214-181">`Update` 或 `Delete` 命令不查找行，因为 `Where` 子句会查找提取行的版本值。</span><span class="sxs-lookup"><span data-stu-id="bf214-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="bf214-182">引发一个 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="bf214-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="bf214-183">以下代码显示更新 Department 名称时由 EF Core 生成的部分 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="bf214-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="bf214-184">前面突出显示的代码显示包含 `RowVersion` 的 `WHERE` 子句。</span><span class="sxs-lookup"><span data-stu-id="bf214-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="bf214-185">如果数据库 `RowVersion` 不等于 `RowVersion` 参数 (`@p2`)，则不更新行。</span><span class="sxs-lookup"><span data-stu-id="bf214-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="bf214-186">以下突出显示的代码显示验证更新哪一行的 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="bf214-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="bf214-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) 返回受上一语句影响的行数。</span><span class="sxs-lookup"><span data-stu-id="bf214-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="bf214-188">如果没有更新行，EF Core 会引发 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="bf214-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf214-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf214-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bf214-190">对于 SQLite 数据库，定义为字节数组的实体属性上的 `[Timestamp]` 特性：</span><span class="sxs-lookup"><span data-stu-id="bf214-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="bf214-191">使列包含在 DELETE 和 UPDATE WHERE 子句中。</span><span class="sxs-lookup"><span data-stu-id="bf214-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="bf214-192">映射到 BLOB 列类型。</span><span class="sxs-lookup"><span data-stu-id="bf214-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="bf214-193">每当行更新时，数据库触发器都会将 RowVersion 列更新为新的随机字节数组。</span><span class="sxs-lookup"><span data-stu-id="bf214-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="bf214-194">在 `Update` 或 `Delete` 命令中，`Where` 子句包括 RowVersion 列的提取值。</span><span class="sxs-lookup"><span data-stu-id="bf214-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="bf214-195">如果要更新的行在提取之后已更改：</span><span class="sxs-lookup"><span data-stu-id="bf214-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="bf214-196">当前的行版本值与提取值不相匹配。</span><span class="sxs-lookup"><span data-stu-id="bf214-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="bf214-197">`Update` 或 `Delete` 命令不查找行，因为 `Where` 子句会查找原始行版本值。</span><span class="sxs-lookup"><span data-stu-id="bf214-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="bf214-198">引发一个 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="bf214-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="bf214-199">更新数据库</span><span class="sxs-lookup"><span data-stu-id="bf214-199">Update the database</span></span>

<span data-ttu-id="bf214-200">添加 `RowVersion` 属性可更改需要迁移的数据库模型。</span><span class="sxs-lookup"><span data-stu-id="bf214-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="bf214-201">生成项目。</span><span class="sxs-lookup"><span data-stu-id="bf214-201">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="bf214-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf214-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bf214-203">在 PMC 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bf214-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf214-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf214-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bf214-205">在终端中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bf214-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="bf214-206">此命令：</span><span class="sxs-lookup"><span data-stu-id="bf214-206">This command:</span></span>

* <span data-ttu-id="bf214-207">创建 Migrations/{time stamp}_RowVersion.cs 迁移文件。</span><span class="sxs-lookup"><span data-stu-id="bf214-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="bf214-208">更新 Migrations/SchoolContextModelSnapshot.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="bf214-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="bf214-209">此次更新将以下突出显示的代码添加到 `BuildModel` 方法：</span><span class="sxs-lookup"><span data-stu-id="bf214-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="bf214-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf214-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bf214-211">在 PMC 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bf214-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf214-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf214-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bf214-213">打开 `Migrations/<timestamp>_RowVersion.cs` 文件，并添加以下突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="bf214-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="bf214-214">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="bf214-214">The preceding code:</span></span>

  * <span data-ttu-id="bf214-215">将现有行更新为随机 blob 值。</span><span class="sxs-lookup"><span data-stu-id="bf214-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="bf214-216">添加数据库触发器，该触发器在行更新时将 RowVersion 列设置为随机 blob 值。</span><span class="sxs-lookup"><span data-stu-id="bf214-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="bf214-217">在终端中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bf214-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="bf214-218">搭建“院系”页面的基架</span><span class="sxs-lookup"><span data-stu-id="bf214-218">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf214-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf214-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bf214-220">遵循[搭建“学生”页的基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明，但以下情况除外：</span><span class="sxs-lookup"><span data-stu-id="bf214-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="bf214-221">创建“Pages/Departments”文件夹。</span><span class="sxs-lookup"><span data-stu-id="bf214-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="bf214-222">将 `Department` 用于模型类。</span><span class="sxs-lookup"><span data-stu-id="bf214-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="bf214-223">使用现有的上下文类，而不是新建上下文类。</span><span class="sxs-lookup"><span data-stu-id="bf214-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf214-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf214-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bf214-225">创建“Pages/Departments”文件夹。</span><span class="sxs-lookup"><span data-stu-id="bf214-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="bf214-226">运行以下命令，搭建“院系”页的基架。</span><span class="sxs-lookup"><span data-stu-id="bf214-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="bf214-227">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="bf214-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="bf214-228">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="bf214-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="bf214-229">生成项目。</span><span class="sxs-lookup"><span data-stu-id="bf214-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="bf214-230">更新“索引”页</span><span class="sxs-lookup"><span data-stu-id="bf214-230">Update the Index page</span></span>

<span data-ttu-id="bf214-231">基架工具为“索引”页创建了 `RowVersion` 列，但生产应用中不会显示该字段。</span><span class="sxs-lookup"><span data-stu-id="bf214-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="bf214-232">本教程中显示 `RowVersion` 的最后一个字节，以帮助展示并发处理的工作原理。</span><span class="sxs-lookup"><span data-stu-id="bf214-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="bf214-233">无法保证最后一个字节本身是唯一的。</span><span class="sxs-lookup"><span data-stu-id="bf214-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="bf214-234">更新 Pages\Departments\Index.cshtml 页：</span><span class="sxs-lookup"><span data-stu-id="bf214-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="bf214-235">用院系替换索引。</span><span class="sxs-lookup"><span data-stu-id="bf214-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="bf214-236">更改包含 `RowVersion` 的代码，以便只显示字节数组的最后一个字节。</span><span class="sxs-lookup"><span data-stu-id="bf214-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="bf214-237">将 FirstMidName 替换为 FullName。</span><span class="sxs-lookup"><span data-stu-id="bf214-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="bf214-238">以下代码显示更新后的页面：</span><span class="sxs-lookup"><span data-stu-id="bf214-238">The following code shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="bf214-239">更新编辑页模型</span><span class="sxs-lookup"><span data-stu-id="bf214-239">Update the Edit page model</span></span>

<span data-ttu-id="bf214-240">使用以下代码更新 Pages\Departments\Edit.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="bf214-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="bf214-241">当在 `OnGet` 方法中提取时，<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> 是使用实体中的 `rowVersion` 值进行更新。</span><span class="sxs-lookup"><span data-stu-id="bf214-241">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="bf214-242">EF Core 使用包含原始 `RowVersion` 值的 WHERE 子句生成 SQL UPDATE 命令。</span><span class="sxs-lookup"><span data-stu-id="bf214-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="bf214-243">如果没有行受到 UPDATE 命令影响（没有行具有原始 `RowVersion` 值），将引发 `DbUpdateConcurrencyException` 异常。</span><span class="sxs-lookup"><span data-stu-id="bf214-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="bf214-244">在上述突出显示的代码中：</span><span class="sxs-lookup"><span data-stu-id="bf214-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="bf214-245">`Department.RowVersion` 中的值是最初在“编辑”页的 Get 请求中所提取的实体中的值。</span><span class="sxs-lookup"><span data-stu-id="bf214-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="bf214-246">通过 Razor 页面中显示将要编辑的实体的隐藏字段将该值提供给 `OnPost` 方法。</span><span class="sxs-lookup"><span data-stu-id="bf214-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="bf214-247">模型绑定器将隐藏字段值复制到 `Department.RowVersion`。</span><span class="sxs-lookup"><span data-stu-id="bf214-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="bf214-248">`OriginalValue` 是 EF Core 将用于 Where 子句的值。</span><span class="sxs-lookup"><span data-stu-id="bf214-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="bf214-249">在执行突出显示的代码行之前，`OriginalValue` 具有在此方法中调用 `FirstOrDefaultAsync` 时数据库中的值，该值可能与“编辑”页面上所显示的值不同。</span><span class="sxs-lookup"><span data-stu-id="bf214-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="bf214-250">突出显示的代码可确保 EF Core 使用原始 `RowVersion` 值，该值来自于 SQL UPDATE 语句的 Where 子句中所显示的 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="bf214-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="bf214-251">发生并发错误时，以下突出显示的代码会获取客户端值（发布到此方法的值）和数据库值。</span><span class="sxs-lookup"><span data-stu-id="bf214-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="bf214-252">以下代码为每列添加自定义错误消息，这些列中的数据库值与发布到 `OnPostAsync` 的值不同：</span><span class="sxs-lookup"><span data-stu-id="bf214-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="bf214-253">以下突出显示的代码将 `RowVersion` 值设置为从数据库检索的新值。</span><span class="sxs-lookup"><span data-stu-id="bf214-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="bf214-254">用户下次单击“保存”时，将仅捕获最后一次显示编辑页后发生的并发错误。</span><span class="sxs-lookup"><span data-stu-id="bf214-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="bf214-255">`ModelState` 具有旧的 `RowVersion` 值，因此需使用 `ModelState.Remove` 语句。</span><span class="sxs-lookup"><span data-stu-id="bf214-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="bf214-256">在 Razor 页面中，当两者都存在时，字段的 `ModelState` 值优于模型属性值。</span><span class="sxs-lookup"><span data-stu-id="bf214-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-edit-page"></a><span data-ttu-id="bf214-257">更新“编辑”页</span><span class="sxs-lookup"><span data-stu-id="bf214-257">Update the Edit page</span></span>

<span data-ttu-id="bf214-258">使用以下代码更新 Pages/Departments/Edit.cshtml：</span><span class="sxs-lookup"><span data-stu-id="bf214-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="bf214-259">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="bf214-259">The preceding code:</span></span>

* <span data-ttu-id="bf214-260">将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="bf214-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="bf214-261">添加隐藏的行版本。</span><span class="sxs-lookup"><span data-stu-id="bf214-261">Adds a hidden row version.</span></span> <span data-ttu-id="bf214-262">必须添加 `RowVersion`，以便回发能够绑定值。</span><span class="sxs-lookup"><span data-stu-id="bf214-262">`RowVersion` must be added so postback binds the value.</span></span>
* <span data-ttu-id="bf214-263">显示 `RowVersion` 的最后一个字节以进行调试。</span><span class="sxs-lookup"><span data-stu-id="bf214-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="bf214-264">将 `ViewData` 替换为强类型 `InstructorNameSL`。</span><span class="sxs-lookup"><span data-stu-id="bf214-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="bf214-265">使用编辑页测试并发冲突</span><span class="sxs-lookup"><span data-stu-id="bf214-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="bf214-266">在英语系打开编辑的两个浏览器实例：</span><span class="sxs-lookup"><span data-stu-id="bf214-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="bf214-267">运行应用，然后选择“院系”。</span><span class="sxs-lookup"><span data-stu-id="bf214-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="bf214-268">右键单击英语系的“编辑”超链接，然后选择“在新选项卡中打开” 。</span><span class="sxs-lookup"><span data-stu-id="bf214-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="bf214-269">在第一个选项卡中，单击英语系的“编辑”超链接。</span><span class="sxs-lookup"><span data-stu-id="bf214-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="bf214-270">两个浏览器选项卡显示相同信息。</span><span class="sxs-lookup"><span data-stu-id="bf214-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="bf214-271">在第一个浏览器选项卡中更改名称，然后单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="bf214-271">Change the name in the first browser tab and click **Save**.</span></span>

![更改后的“院系编辑”页 1](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="bf214-273">浏览器显示更改值并更新 rowVersion 标记后的索引页。</span><span class="sxs-lookup"><span data-stu-id="bf214-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="bf214-274">请注意更新后的 rowVersion 标记，它在其他选项卡的第二回发中显示。</span><span class="sxs-lookup"><span data-stu-id="bf214-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="bf214-275">在第二个浏览器选项卡中更改不同字段。</span><span class="sxs-lookup"><span data-stu-id="bf214-275">Change a different field in the second browser tab.</span></span>

![更改后的“院系编辑”页 2](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="bf214-277">单击“保存”  。</span><span class="sxs-lookup"><span data-stu-id="bf214-277">Click **Save**.</span></span> <span data-ttu-id="bf214-278">可看见所有不匹配数据库值的字段的错误消息：</span><span class="sxs-lookup"><span data-stu-id="bf214-278">You see error messages for all fields that don't match the database values:</span></span>

![“院系编辑”页错误消息](concurrency/_static/edit-error30.png)

<span data-ttu-id="bf214-280">此浏览器窗口将不会更改名称字段。</span><span class="sxs-lookup"><span data-stu-id="bf214-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="bf214-281">将当前值（语言）复制并粘贴到名称字段。</span><span class="sxs-lookup"><span data-stu-id="bf214-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="bf214-282">退出选项卡。客户端验证将删除错误消息。</span><span class="sxs-lookup"><span data-stu-id="bf214-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="bf214-283">再次单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="bf214-283">Click **Save** again.</span></span> <span data-ttu-id="bf214-284">保存在第二个浏览器选项卡中输入的值。</span><span class="sxs-lookup"><span data-stu-id="bf214-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="bf214-285">在索引页中可以看到保存的值。</span><span class="sxs-lookup"><span data-stu-id="bf214-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page-model"></a><span data-ttu-id="bf214-286">更新“删除”页面模型</span><span class="sxs-lookup"><span data-stu-id="bf214-286">Update the Delete page model</span></span>

<span data-ttu-id="bf214-287">使用以下代码更新 Pages/Departments/Delete.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="bf214-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="bf214-288">删除页检测提取实体并更改时的并发冲突。</span><span class="sxs-lookup"><span data-stu-id="bf214-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="bf214-289">提取实体后，`Department.RowVersion` 为行版本。</span><span class="sxs-lookup"><span data-stu-id="bf214-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="bf214-290">EF Core 创建 SQL DELETE 命令时，它包括具有 `RowVersion` 的 WHERE 子句。</span><span class="sxs-lookup"><span data-stu-id="bf214-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="bf214-291">如果 SQL DELETE 命令导致零行受影响：</span><span class="sxs-lookup"><span data-stu-id="bf214-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="bf214-292">SQL DELETE 命令中的 `RowVersion` 与数据库中的 `RowVersion` 不匹配。</span><span class="sxs-lookup"><span data-stu-id="bf214-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="bf214-293">引发 DbUpdateConcurrencyException 异常。</span><span class="sxs-lookup"><span data-stu-id="bf214-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="bf214-294">使用 `concurrencyError` 调用 `OnGetAsync`。</span><span class="sxs-lookup"><span data-stu-id="bf214-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="bf214-295">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="bf214-295">Update the Delete page</span></span>

<span data-ttu-id="bf214-296">使用以下代码更新 Pages/Departments/Delete.cshtml：</span><span class="sxs-lookup"><span data-stu-id="bf214-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

<span data-ttu-id="bf214-297">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="bf214-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="bf214-298">将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="bf214-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="bf214-299">添加错误消息。</span><span class="sxs-lookup"><span data-stu-id="bf214-299">Adds an error message.</span></span>
* <span data-ttu-id="bf214-300">将“管理员”字段中的 FirstMidName 替换为 FullName。</span><span class="sxs-lookup"><span data-stu-id="bf214-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="bf214-301">更改 `RowVersion` 以显示最后一个字节。</span><span class="sxs-lookup"><span data-stu-id="bf214-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="bf214-302">添加隐藏的行版本。</span><span class="sxs-lookup"><span data-stu-id="bf214-302">Adds a hidden row version.</span></span> <span data-ttu-id="bf214-303">必须添加 `RowVersion`，以便回发能够绑定值。</span><span class="sxs-lookup"><span data-stu-id="bf214-303">`RowVersion` must be added so postback binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="bf214-304">测试并发冲突</span><span class="sxs-lookup"><span data-stu-id="bf214-304">Test concurrency conflicts</span></span>

<span data-ttu-id="bf214-305">创建测试系。</span><span class="sxs-lookup"><span data-stu-id="bf214-305">Create a test department.</span></span>

<span data-ttu-id="bf214-306">在测试系打开删除的两个浏览器实例：</span><span class="sxs-lookup"><span data-stu-id="bf214-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="bf214-307">运行应用，然后选择“院系”。</span><span class="sxs-lookup"><span data-stu-id="bf214-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="bf214-308">右键单击测试系的“删除”超链接，然后选择“在新选项卡中打开” 。</span><span class="sxs-lookup"><span data-stu-id="bf214-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="bf214-309">单击测试系的“编辑”超链接。</span><span class="sxs-lookup"><span data-stu-id="bf214-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="bf214-310">两个浏览器选项卡显示相同信息。</span><span class="sxs-lookup"><span data-stu-id="bf214-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="bf214-311">在第一个浏览器选项卡中更改预算，然后单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="bf214-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="bf214-312">浏览器显示更改值并更新 rowVersion 标记后的索引页。</span><span class="sxs-lookup"><span data-stu-id="bf214-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="bf214-313">请注意更新后的 rowVersion 标记，它在其他选项卡的第二回发中显示。</span><span class="sxs-lookup"><span data-stu-id="bf214-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="bf214-314">从第二个选项卡中删除测试部门。并发错误显示来自数据库的当前值。</span><span class="sxs-lookup"><span data-stu-id="bf214-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="bf214-315">单击“删除”删除实体，除非 `RowVersion` 已更新。</span><span class="sxs-lookup"><span data-stu-id="bf214-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bf214-316">其他资源</span><span class="sxs-lookup"><span data-stu-id="bf214-316">Additional resources</span></span>

* [<span data-ttu-id="bf214-317">EF Core 中的并发令牌</span><span class="sxs-lookup"><span data-stu-id="bf214-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="bf214-318">EF Core 中的并发处理</span><span class="sxs-lookup"><span data-stu-id="bf214-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="bf214-319">调试 ASP.NET Core 2.x 源</span><span class="sxs-lookup"><span data-stu-id="bf214-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="bf214-320">后续步骤</span><span class="sxs-lookup"><span data-stu-id="bf214-320">Next steps</span></span>

<span data-ttu-id="bf214-321">这是本系列的最后一个教程。</span><span class="sxs-lookup"><span data-stu-id="bf214-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="bf214-322">[本系列教程的 MVC 版本](xref:data/ef-mvc/index)中介绍了其他主题。</span><span class="sxs-lookup"><span data-stu-id="bf214-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="bf214-323">上一个教程</span><span class="sxs-lookup"><span data-stu-id="bf214-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bf214-324">本教程介绍如何处理多个用户并发更新同一实体（同时）时出现的冲突。</span><span class="sxs-lookup"><span data-stu-id="bf214-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="bf214-325">如果遇到无法解决的问题，请[下载或查看已完成的应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="bf214-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="bf214-326">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="bf214-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="bf214-327">并发冲突</span><span class="sxs-lookup"><span data-stu-id="bf214-327">Concurrency conflicts</span></span>

<span data-ttu-id="bf214-328">在以下情况下，会发生并发冲突：</span><span class="sxs-lookup"><span data-stu-id="bf214-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="bf214-329">用户导航到实体的编辑页面。</span><span class="sxs-lookup"><span data-stu-id="bf214-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="bf214-330">第一个用户的更改还未写入数据库之前，另一用户更新同一实体。</span><span class="sxs-lookup"><span data-stu-id="bf214-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="bf214-331">如果未启用并发检测，当发生并发更新时：</span><span class="sxs-lookup"><span data-stu-id="bf214-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="bf214-332">最后一个更新优先。</span><span class="sxs-lookup"><span data-stu-id="bf214-332">The last update wins.</span></span> <span data-ttu-id="bf214-333">也就是最后一个更新的值保存至数据库。</span><span class="sxs-lookup"><span data-stu-id="bf214-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="bf214-334">第一个并发更新将会丢失。</span><span class="sxs-lookup"><span data-stu-id="bf214-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="bf214-335">开放式并发</span><span class="sxs-lookup"><span data-stu-id="bf214-335">Optimistic concurrency</span></span>

<span data-ttu-id="bf214-336">乐观并发允许发生并发冲突，并在并发冲突发生时作出正确反应。</span><span class="sxs-lookup"><span data-stu-id="bf214-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="bf214-337">例如，Jane 访问院系编辑页面，将英语系的预算从 350,000.00 美元更改为 0.00 美元。</span><span class="sxs-lookup"><span data-stu-id="bf214-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![将预算更改为零](concurrency/_static/change-budget.png)

<span data-ttu-id="bf214-339">在 Jane 单击“保存”之前，John 访问了相同页面，并将开始日期字段从 2007/1/9 更改为 2013/1/9。</span><span class="sxs-lookup"><span data-stu-id="bf214-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![将开始日期更改为 2013](concurrency/_static/change-date.png)

<span data-ttu-id="bf214-341">Jane 先单击“保存”，并在浏览器显示索引页时看到她的更改。</span><span class="sxs-lookup"><span data-stu-id="bf214-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![预算已更改为零](concurrency/_static/budget-zero.png)

<span data-ttu-id="bf214-343">John 单击“编辑”页面上的“保存”，但页面的预算仍显示为 350,000.00 美元。</span><span class="sxs-lookup"><span data-stu-id="bf214-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="bf214-344">接下来的情况取决于并发冲突的处理方式。</span><span class="sxs-lookup"><span data-stu-id="bf214-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="bf214-345">乐观并发包括以下选项：</span><span class="sxs-lookup"><span data-stu-id="bf214-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="bf214-346">可以跟踪用户已修改的属性，并仅更新数据库中相应的列。</span><span class="sxs-lookup"><span data-stu-id="bf214-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="bf214-347">在这种情况下，数据不会丢失。</span><span class="sxs-lookup"><span data-stu-id="bf214-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="bf214-348">两个用户更新了不同的属性。</span><span class="sxs-lookup"><span data-stu-id="bf214-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="bf214-349">下次有人浏览英语系时，将看到 Jane 和 John 两个人的更改。</span><span class="sxs-lookup"><span data-stu-id="bf214-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="bf214-350">这种更新方法可以减少导致数据丢失的冲突数。</span><span class="sxs-lookup"><span data-stu-id="bf214-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="bf214-351">这种方法：</span><span class="sxs-lookup"><span data-stu-id="bf214-351">This approach:</span></span>
 
  * <span data-ttu-id="bf214-352">无法避免数据丢失，如果对同一属性进行竞争性更改的话。</span><span class="sxs-lookup"><span data-stu-id="bf214-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="bf214-353">通常不适用于 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="bf214-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="bf214-354">它需要维持重要状态，以便跟踪所有提取值和新值。</span><span class="sxs-lookup"><span data-stu-id="bf214-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="bf214-355">维持大量状态可能影响应用性能。</span><span class="sxs-lookup"><span data-stu-id="bf214-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="bf214-356">可能会增加应用复杂性（与实体上的并发检测相比）。</span><span class="sxs-lookup"><span data-stu-id="bf214-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="bf214-357">可让 John 的更改覆盖 Jane 的更改。</span><span class="sxs-lookup"><span data-stu-id="bf214-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="bf214-358">下次有人浏览英语系时，将看到 2013/9/1 和提取的值 350,000.00 美元。</span><span class="sxs-lookup"><span data-stu-id="bf214-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="bf214-359">这种方法称为“客户端优先”或“最后一个优先”方案 。</span><span class="sxs-lookup"><span data-stu-id="bf214-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="bf214-360">（客户端的所有值优先于数据存储的值。）如果不对并发处理进行任何编码，则自动执行“客户端优先”。</span><span class="sxs-lookup"><span data-stu-id="bf214-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="bf214-361">可以阻止在数据库中更新 John 的更改。</span><span class="sxs-lookup"><span data-stu-id="bf214-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="bf214-362">应用通常会：</span><span class="sxs-lookup"><span data-stu-id="bf214-362">Typically, the app would:</span></span>

  * <span data-ttu-id="bf214-363">显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="bf214-363">Display an error message.</span></span>
  * <span data-ttu-id="bf214-364">显示数据的当前状态。</span><span class="sxs-lookup"><span data-stu-id="bf214-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="bf214-365">允许用户重新应用更改。</span><span class="sxs-lookup"><span data-stu-id="bf214-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="bf214-366">这称为“存储优先”方案。</span><span class="sxs-lookup"><span data-stu-id="bf214-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="bf214-367">（数据存储值优先于客户端提交的值。）本教程实施“存储优先”方案。</span><span class="sxs-lookup"><span data-stu-id="bf214-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="bf214-368">此方法可确保用户在未收到警报时不会覆盖任何更改。</span><span class="sxs-lookup"><span data-stu-id="bf214-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="bf214-369">处理并发</span><span class="sxs-lookup"><span data-stu-id="bf214-369">Handling concurrency</span></span> 

<span data-ttu-id="bf214-370">当属性配置为[并发令牌](/ef/core/modeling/concurrency)时：</span><span class="sxs-lookup"><span data-stu-id="bf214-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="bf214-371">EF Core 验证提取属性后是否未更改属性。</span><span class="sxs-lookup"><span data-stu-id="bf214-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="bf214-372">调用 [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) 或 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) 时会执行此检查。</span><span class="sxs-lookup"><span data-stu-id="bf214-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="bf214-373">如果提取属性后更改了属性，将引发 [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception)。</span><span class="sxs-lookup"><span data-stu-id="bf214-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) is thrown.</span></span> 

<span data-ttu-id="bf214-374">数据库和数据模型必须配置为支持引发 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="bf214-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="bf214-375">检测属性的并发冲突</span><span class="sxs-lookup"><span data-stu-id="bf214-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="bf214-376">可使用 [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) 特性在属性级别检测并发冲突。</span><span class="sxs-lookup"><span data-stu-id="bf214-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="bf214-377">该特性可应用于模型上的多个属性。</span><span class="sxs-lookup"><span data-stu-id="bf214-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="bf214-378">有关详细信息，请参阅[数据注释 - ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations)。</span><span class="sxs-lookup"><span data-stu-id="bf214-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="bf214-379">本教程中不使用 `[ConcurrencyCheck]` 特性。</span><span class="sxs-lookup"><span data-stu-id="bf214-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="bf214-380">检测行的并发冲突</span><span class="sxs-lookup"><span data-stu-id="bf214-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="bf214-381">要检测并发冲突，请将 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 跟踪列添加到模型。</span><span class="sxs-lookup"><span data-stu-id="bf214-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="bf214-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="bf214-382">`rowversion` :</span></span>

* <span data-ttu-id="bf214-383">是 SQL Server 特定的。</span><span class="sxs-lookup"><span data-stu-id="bf214-383">Is SQL Server specific.</span></span> <span data-ttu-id="bf214-384">其他数据库可能无法提供类似功能。</span><span class="sxs-lookup"><span data-stu-id="bf214-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="bf214-385">用于确定从数据库提取实体后未更改实体。</span><span class="sxs-lookup"><span data-stu-id="bf214-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="bf214-386">数据库生成 `rowversion` 序号，该数字随着每次行的更新递增。</span><span class="sxs-lookup"><span data-stu-id="bf214-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="bf214-387">在 `Update` 或 `Delete` 命令中，`Where` 子句包括 `rowversion` 的提取值。</span><span class="sxs-lookup"><span data-stu-id="bf214-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="bf214-388">如果要更新的行已更改：</span><span class="sxs-lookup"><span data-stu-id="bf214-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="bf214-389">`rowversion` 不匹配提取值。</span><span class="sxs-lookup"><span data-stu-id="bf214-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="bf214-390">`Update` 或 `Delete` 命令不能找到行，因为 `Where` 子句包含提取的 `rowversion`。</span><span class="sxs-lookup"><span data-stu-id="bf214-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="bf214-391">引发一个 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="bf214-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="bf214-392">在 EF Core 中，如果未通过 `Update` 或 `Delete` 命令更新行，则引发并发异常。</span><span class="sxs-lookup"><span data-stu-id="bf214-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="bf214-393">向 Department 实体添加跟踪属性</span><span class="sxs-lookup"><span data-stu-id="bf214-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="bf214-394">在 Models/Department.cs 中，添加名为 RowVersion 的跟踪属性：</span><span class="sxs-lookup"><span data-stu-id="bf214-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="bf214-395">[Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) 特性指定此列包含在 `Update` 和 `Delete` 命令的 `Where` 子句中。</span><span class="sxs-lookup"><span data-stu-id="bf214-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="bf214-396">该特性称为 `Timestamp`，因为之前版本的 SQL Server 在 SQL `rowversion` 类型将其替换之前使用 SQL `timestamp` 数据类型。</span><span class="sxs-lookup"><span data-stu-id="bf214-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="bf214-397">Fluent API 还可指定跟踪属性：</span><span class="sxs-lookup"><span data-stu-id="bf214-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="bf214-398">以下代码显示更新 Department 名称时由 EF Core 生成的部分 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="bf214-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="bf214-399">前面突出显示的代码显示包含 `RowVersion` 的 `WHERE` 子句。</span><span class="sxs-lookup"><span data-stu-id="bf214-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="bf214-400">如果数据库 `RowVersion` 不等于 `RowVersion` 参数（`@p2`），则不更新行。</span><span class="sxs-lookup"><span data-stu-id="bf214-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="bf214-401">以下突出显示的代码显示验证更新哪一行的 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="bf214-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="bf214-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) 返回受上一语句影响的行数。</span><span class="sxs-lookup"><span data-stu-id="bf214-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="bf214-403">在没有行更新的情况下，EF Core 引发 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="bf214-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="bf214-404">在 Visual Studio 的输出窗口中可看见 EF Core 生成的 T-SQL。</span><span class="sxs-lookup"><span data-stu-id="bf214-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="bf214-405">更新数据库</span><span class="sxs-lookup"><span data-stu-id="bf214-405">Update the DB</span></span>

<span data-ttu-id="bf214-406">添加 `RowVersion` 属性可更改数据库模型，这需要迁移。</span><span class="sxs-lookup"><span data-stu-id="bf214-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="bf214-407">生成项目。</span><span class="sxs-lookup"><span data-stu-id="bf214-407">Build the project.</span></span> <span data-ttu-id="bf214-408">在命令窗口中输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="bf214-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="bf214-409">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="bf214-409">The preceding commands:</span></span>

* <span data-ttu-id="bf214-410">添加 Migrations/{time stamp}_RowVersion.cs 迁移文件。</span><span class="sxs-lookup"><span data-stu-id="bf214-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="bf214-411">更新 Migrations/SchoolContextModelSnapshot.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="bf214-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="bf214-412">此次更新将以下突出显示的代码添加到 `BuildModel` 方法：</span><span class="sxs-lookup"><span data-stu-id="bf214-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="bf214-413">运行迁移以更新数据库。</span><span class="sxs-lookup"><span data-stu-id="bf214-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="bf214-414">构架院系模型</span><span class="sxs-lookup"><span data-stu-id="bf214-414">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf214-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf214-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="bf214-416">按照[为“学生”模型搭建基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明操作，并对模型类使用 `Department`。</span><span class="sxs-lookup"><span data-stu-id="bf214-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf214-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf214-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="bf214-418">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="bf214-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="bf214-419">上述命令为 `Department` 模型创建基架。</span><span class="sxs-lookup"><span data-stu-id="bf214-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="bf214-420">在 Visual Studio 中打开项目。</span><span class="sxs-lookup"><span data-stu-id="bf214-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="bf214-421">生成项目。</span><span class="sxs-lookup"><span data-stu-id="bf214-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="bf214-422">更新院系索引页</span><span class="sxs-lookup"><span data-stu-id="bf214-422">Update the Departments Index page</span></span>

<span data-ttu-id="bf214-423">基架引擎为索引页创建 `RowVersion` 列，但不应显示该字段。</span><span class="sxs-lookup"><span data-stu-id="bf214-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="bf214-424">本教程中显示 `RowVersion` 的最后一个字节，以帮助理解并发。</span><span class="sxs-lookup"><span data-stu-id="bf214-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="bf214-425">不能保证最后一个字节是唯一的。</span><span class="sxs-lookup"><span data-stu-id="bf214-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="bf214-426">实际应用不会显示 `RowVersion` 或 `RowVersion` 的最后一个字节。</span><span class="sxs-lookup"><span data-stu-id="bf214-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="bf214-427">更新索引页：</span><span class="sxs-lookup"><span data-stu-id="bf214-427">Update the Index page:</span></span>

* <span data-ttu-id="bf214-428">用院系替换索引。</span><span class="sxs-lookup"><span data-stu-id="bf214-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="bf214-429">将包含 `RowVersion` 的标记替换为 `RowVersion` 的最后一个字节。</span><span class="sxs-lookup"><span data-stu-id="bf214-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="bf214-430">将 FirstMidName 替换为 FullName。</span><span class="sxs-lookup"><span data-stu-id="bf214-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="bf214-431">以下标记显示更新后的页面：</span><span class="sxs-lookup"><span data-stu-id="bf214-431">The following markup shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="bf214-432">更新编辑页模型</span><span class="sxs-lookup"><span data-stu-id="bf214-432">Update the Edit page model</span></span>

<span data-ttu-id="bf214-433">使用以下代码更新 Pages\Departments\Edit.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="bf214-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="bf214-434">要检测并发问题，请使用来自所提取实体的 `rowVersion` 值更新 [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue)。</span><span class="sxs-lookup"><span data-stu-id="bf214-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="bf214-435">EF Core 使用包含原始 `RowVersion` 值的 WHERE 子句生成 SQL UPDATE 命令。</span><span class="sxs-lookup"><span data-stu-id="bf214-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="bf214-436">如果没有行受到 UPDATE 命令影响（没有行具有原始 `RowVersion` 值），将引发 `DbUpdateConcurrencyException` 异常。</span><span class="sxs-lookup"><span data-stu-id="bf214-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="bf214-437">在前面的代码中，`Department.RowVersion` 为实体提取后的值。</span><span class="sxs-lookup"><span data-stu-id="bf214-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="bf214-438">使用此方法调用 `FirstOrDefaultAsync` 时，`OriginalValue` 为数据库中的值。</span><span class="sxs-lookup"><span data-stu-id="bf214-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="bf214-439">以下代码获取客户端值（向此方法发布的值）和数据库值：</span><span class="sxs-lookup"><span data-stu-id="bf214-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="bf214-440">以下代码为每列添加自定义错误消息，这些列中的数据库值与发布到 `OnPostAsync` 的值不同：</span><span class="sxs-lookup"><span data-stu-id="bf214-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="bf214-441">以下突出显示的代码将 `RowVersion` 值设置为从数据库检索的新值。</span><span class="sxs-lookup"><span data-stu-id="bf214-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="bf214-442">用户下次单击“保存”时，将仅捕获最后一次显示编辑页后发生的并发错误。</span><span class="sxs-lookup"><span data-stu-id="bf214-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="bf214-443">`ModelState` 具有旧的 `RowVersion` 值，因此需使用 `ModelState.Remove` 语句。</span><span class="sxs-lookup"><span data-stu-id="bf214-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="bf214-444">在 Razor 页面中，当两者都存在时，字段的 `ModelState` 值优于模型属性值。</span><span class="sxs-lookup"><span data-stu-id="bf214-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="bf214-445">更新“编辑”页</span><span class="sxs-lookup"><span data-stu-id="bf214-445">Update the Edit page</span></span>

<span data-ttu-id="bf214-446">使用以下标记更新 Pages/Departments/Edit.cshtml：</span><span class="sxs-lookup"><span data-stu-id="bf214-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="bf214-447">前面的标记：</span><span class="sxs-lookup"><span data-stu-id="bf214-447">The preceding markup:</span></span>

* <span data-ttu-id="bf214-448">将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="bf214-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="bf214-449">添加隐藏的行版本。</span><span class="sxs-lookup"><span data-stu-id="bf214-449">Adds a hidden row version.</span></span> <span data-ttu-id="bf214-450">必须添加 `RowVersion`，以便回发绑定值。</span><span class="sxs-lookup"><span data-stu-id="bf214-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="bf214-451">显示 `RowVersion` 的最后一个字节以进行调试。</span><span class="sxs-lookup"><span data-stu-id="bf214-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="bf214-452">将 `ViewData` 替换为强类型 `InstructorNameSL`。</span><span class="sxs-lookup"><span data-stu-id="bf214-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="bf214-453">使用编辑页测试并发冲突</span><span class="sxs-lookup"><span data-stu-id="bf214-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="bf214-454">在英语系打开编辑的两个浏览器实例：</span><span class="sxs-lookup"><span data-stu-id="bf214-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="bf214-455">运行应用，然后选择“院系”。</span><span class="sxs-lookup"><span data-stu-id="bf214-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="bf214-456">右键单击英语系的“编辑”超链接，然后选择“在新选项卡中打开” 。</span><span class="sxs-lookup"><span data-stu-id="bf214-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="bf214-457">在第一个选项卡中，单击英语系的“编辑”超链接。</span><span class="sxs-lookup"><span data-stu-id="bf214-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="bf214-458">两个浏览器选项卡显示相同信息。</span><span class="sxs-lookup"><span data-stu-id="bf214-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="bf214-459">在第一个浏览器选项卡中更改名称，然后单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="bf214-459">Change the name in the first browser tab and click **Save**.</span></span>

![更改后的“院系编辑”页 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="bf214-461">浏览器显示更改值并更新 rowVersion 标记后的索引页。</span><span class="sxs-lookup"><span data-stu-id="bf214-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="bf214-462">请注意更新后的 rowVersion 标记，它在其他选项卡的第二回发中显示。</span><span class="sxs-lookup"><span data-stu-id="bf214-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="bf214-463">在第二个浏览器选项卡中更改不同字段。</span><span class="sxs-lookup"><span data-stu-id="bf214-463">Change a different field in the second browser tab.</span></span>

![更改后的“院系编辑”页 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="bf214-465">单击“保存”  。</span><span class="sxs-lookup"><span data-stu-id="bf214-465">Click **Save**.</span></span> <span data-ttu-id="bf214-466">可看见所有不匹配数据库值的字段的错误消息：</span><span class="sxs-lookup"><span data-stu-id="bf214-466">You see error messages for all fields that don't match the DB values:</span></span>

![“院系编辑”页错误消息](concurrency/_static/edit-error.png)

<span data-ttu-id="bf214-468">此浏览器窗口将不会更改名称字段。</span><span class="sxs-lookup"><span data-stu-id="bf214-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="bf214-469">将当前值（语言）复制并粘贴到名称字段。</span><span class="sxs-lookup"><span data-stu-id="bf214-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="bf214-470">退出选项卡。客户端验证将删除错误消息。</span><span class="sxs-lookup"><span data-stu-id="bf214-470">Tab out. Client-side validation removes the error message.</span></span>

![“院系编辑”页错误消息](concurrency/_static/cv.png)

<span data-ttu-id="bf214-472">再次单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="bf214-472">Click **Save** again.</span></span> <span data-ttu-id="bf214-473">保存在第二个浏览器选项卡中输入的值。</span><span class="sxs-lookup"><span data-stu-id="bf214-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="bf214-474">在索引页中可以看到保存的值。</span><span class="sxs-lookup"><span data-stu-id="bf214-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="bf214-475">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="bf214-475">Update the Delete page</span></span>

<span data-ttu-id="bf214-476">使用以下代码更新“删除”页模型：</span><span class="sxs-lookup"><span data-stu-id="bf214-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="bf214-477">删除页检测提取实体并更改时的并发冲突。</span><span class="sxs-lookup"><span data-stu-id="bf214-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="bf214-478">提取实体后，`Department.RowVersion` 为行版本。</span><span class="sxs-lookup"><span data-stu-id="bf214-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="bf214-479">EF Core 创建 SQL DELETE 命令时，它包括具有 `RowVersion` 的 WHERE 子句。</span><span class="sxs-lookup"><span data-stu-id="bf214-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="bf214-480">如果 SQL DELETE 命令导致零行受影响：</span><span class="sxs-lookup"><span data-stu-id="bf214-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="bf214-481">SQL DELETE 命令中的 `RowVersion` 与数据库中的 `RowVersion` 不匹配。</span><span class="sxs-lookup"><span data-stu-id="bf214-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="bf214-482">引发 DbUpdateConcurrencyException 异常。</span><span class="sxs-lookup"><span data-stu-id="bf214-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="bf214-483">使用 `concurrencyError` 调用 `OnGetAsync`。</span><span class="sxs-lookup"><span data-stu-id="bf214-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="bf214-484">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="bf214-484">Update the Delete page</span></span>

<span data-ttu-id="bf214-485">使用以下代码更新 Pages/Departments/Delete.cshtml：</span><span class="sxs-lookup"><span data-stu-id="bf214-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="bf214-486">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="bf214-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="bf214-487">将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="bf214-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="bf214-488">添加错误消息。</span><span class="sxs-lookup"><span data-stu-id="bf214-488">Adds an error message.</span></span>
* <span data-ttu-id="bf214-489">将“管理员”字段中的 FirstMidName 替换为 FullName。</span><span class="sxs-lookup"><span data-stu-id="bf214-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="bf214-490">更改 `RowVersion` 以显示最后一个字节。</span><span class="sxs-lookup"><span data-stu-id="bf214-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="bf214-491">添加隐藏的行版本。</span><span class="sxs-lookup"><span data-stu-id="bf214-491">Adds a hidden row version.</span></span> <span data-ttu-id="bf214-492">必须添加 `RowVersion`，以便回发绑定值。</span><span class="sxs-lookup"><span data-stu-id="bf214-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="bf214-493">使用删除页测试并发冲突</span><span class="sxs-lookup"><span data-stu-id="bf214-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="bf214-494">创建测试系。</span><span class="sxs-lookup"><span data-stu-id="bf214-494">Create a test department.</span></span>

<span data-ttu-id="bf214-495">在测试系打开删除的两个浏览器实例：</span><span class="sxs-lookup"><span data-stu-id="bf214-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="bf214-496">运行应用，然后选择“院系”。</span><span class="sxs-lookup"><span data-stu-id="bf214-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="bf214-497">右键单击测试系的“删除”超链接，然后选择“在新选项卡中打开” 。</span><span class="sxs-lookup"><span data-stu-id="bf214-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="bf214-498">单击测试系的“编辑”超链接。</span><span class="sxs-lookup"><span data-stu-id="bf214-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="bf214-499">两个浏览器选项卡显示相同信息。</span><span class="sxs-lookup"><span data-stu-id="bf214-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="bf214-500">在第一个浏览器选项卡中更改预算，然后单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="bf214-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="bf214-501">浏览器显示更改值并更新 rowVersion 标记后的索引页。</span><span class="sxs-lookup"><span data-stu-id="bf214-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="bf214-502">请注意更新后的 rowVersion 标记，它在其他选项卡的第二回发中显示。</span><span class="sxs-lookup"><span data-stu-id="bf214-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="bf214-503">从第二个选项卡中删除测试部门。并发错误显示来自数据库的当前值。</span><span class="sxs-lookup"><span data-stu-id="bf214-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="bf214-504">单击“删除”删除实体，除非 `RowVersion` 已更新。</span><span class="sxs-lookup"><span data-stu-id="bf214-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

<span data-ttu-id="bf214-505">请参阅[继承](xref:data/ef-mvc/inheritance)了解如何继承数据模型。</span><span class="sxs-lookup"><span data-stu-id="bf214-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="bf214-506">其他资源</span><span class="sxs-lookup"><span data-stu-id="bf214-506">Additional resources</span></span>

* [<span data-ttu-id="bf214-507">EF Core 中的并发令牌</span><span class="sxs-lookup"><span data-stu-id="bf214-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="bf214-508">EF Core 中的并发处理</span><span class="sxs-lookup"><span data-stu-id="bf214-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="bf214-509">本教程的 YouTube 版本（处理并发冲突）</span><span class="sxs-lookup"><span data-stu-id="bf214-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="bf214-510">本教程的 YouTube 版本（第 2 部分）</span><span class="sxs-lookup"><span data-stu-id="bf214-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="bf214-511">本教程的 YouTube 版本（第 3 部分）</span><span class="sxs-lookup"><span data-stu-id="bf214-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="bf214-512">上一篇</span><span class="sxs-lookup"><span data-stu-id="bf214-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

