软删除是一种在业务应用程序中广泛应用的模式。它允许你将某些记录标记为已删除，则不是真正从数据库中删除。用户将不能选择标记为软删除的记录，同时旧的记录仍可以引用到软删除的记录。

## 什么情况下应该使用软删除?


如果您随机询问一些开发人员，他们很可能会说软删除用于恢复已删除的记录。 但是，如果这是唯一促使您使用软删除因素，那么请三思。
在决定项目中是否使用软删除时，除了考虑 “简单数据还原” 这一诉求，有以下几点也是需要考虑的：

1. 历史跟踪和审计（比如有法律规定）
2. 保持参考完整性，避免级联删除
3. 您需要 “优雅” 删除。 例如。长期运行的业务流程可能需要用到 “被删除” 数据来完成业务。


如果您发现自己的业务需求与上面的列表匹配，则意味着您可能需要在应用程序中实现 “软删除” 功能。

实现软删除很容易！您只需将 “Is_Deleted” 或 “Delete_Date” 列添加到数据库表（或文档的属性）中，并将应用程序代码中的所有 delete 语句替换为 update 语句。同时，您需要修改所有查询语句以加入对删除标记字段的判断。 嗯，或者创建一个视图更好？使用物化视图怎么样？ 在这种情况下，我们可能需要实现 “instead of ” 触发器或存储过程 API 来处理视图。在删除实体时，您可能希望删除其引用的某些属性。在这种情况下，为了能够恢复删除，还需要存储删除上下文。还有...   。对，开发人员的生活就是因此变地复杂。


## 软删除的挑战

实际上，实现 “合适” 的软删除并非易事。有时，开发人员选择不实施软删除，只是因为这需要做很多工作。您可能会想到其他选项，例如使用专门的“回收站”表，该表是原始表的一个镜像，存储 从“原始” 表中删除的记录。
但是，如果决定实施软删除，则需要考虑一些因素。

**数据库支持** 将记录标记为已删除的一个问题是它们的唯一键。 举例来说-我们需要将用户及其电子邮件存储在一起。电子邮件必须是唯一的，因此，我们需要为存储用户的表创建唯一的索引。而且，如果删除了用户，理论上我们应该可以使用相同的电子邮件插入新用户。所以，您的数据库应该能够处理此类情况。方法之一 - 在唯一键中包含一个软删除标记字段。如果您的标记字段不是简单的布尔值，而是删除时间戳，则可以解决问题。

另外一个方法 - 指定唯一索引。比如， 在 PostgreSQL 中，有 [分部唯一索引](https://www.postgresql.org/docs/12/indexes-partial.html) ，在 Mysql 中有
[过滤索引](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/create-filtered-indexes)。这些机制允许在构建索引时以断言的方式排除一部分记录。所以对于软删除机制的实现，我们可以排除掉标记为删除的记录。

如果您的数据库支持视图触发器，则可以使用视图和 `instead of` 类型的触发器来创建数据模型。但是这种方法会影响性能，并且无法通过对基础表应用唯一约束来解决问题。

**应用程序支持** 确保所有对涉及到软删除实体的查询都经过了细致地检查，否则可能导致意外的数据泄漏和严重的性能问题。在理想情况下，开发人员不应意识到软删除的存在。因此，如果可能，请尝试引入您的数据访问 API，并防止开发人员使用其他工具来访问数据库（例如直接JDBC连接）。您甚至可能要覆盖标准的 API，例如 JPA 的 Entity Manager，以正确地支持软删除。


**重新审视对标准 API 的使用** First of all, forget about using some of the standard DB and JPA features. If you have your database table and constraint model carefully engineered and tuned, as well as you JPA layer, introducing soft delete requires you to consider the following:

首先，忘掉使用某些标准的 DB 和JPA 功能。如果您已经精心设计和调整了数据库表和约束模型以及 JPA 层，那么引入软删除要求您考虑以下事项：


1. 不能使用标准级联策略。在进行软删除的情况下，您不能删除记录，而是要对其进行更新。 如果仍然需要级联删除，触发器可以帮助您实现此行为。
   
2. 对于触发器 - 你也需要进行检查，确保之前在 pre- 和 post- delete 触发器中的代码可以正确地迁移到 pre- post- update 触发器。这些执行软件删除的代码不能影响正常的 update 处理。 


3. JPA 的生命周期回调 `@PreRemove` 和 `@PostRemove` 将失效。 需要寻找其它方法来实现之前在这两个回调中实现的逻辑。 这里与触发器需要做的修改相似，你需分辨出普通的更新和软删除执行的更新。

 
**你仍然需要满足监管需求** 比如 GDPR ，规则要求如果用户请求删除数据，则数据必须被完全删除。为了遵守规则 ，除软删除，你必须实现一个 “清除” 功能来执行物理删除。在部分情况下，物理删除会影响数据库中相关数据。所以，你需要保持谨慎， 不要删错关键业务数据。


## CUBA 平台中的软删除实现
 

CUBA 平台支持开箱即用的软删除功能，并且软删除功能是默认启用的。 在大多数情况下，开发人员不必考虑是否使用软删除。 CUBA 中的数据操作 API 与众所周知的 JPA API  99％ 相同 。 作为开发人员，您可以使用 `EntityManager` 的 `remove()`，甚至可以使用任意的 JPQL 处理应用程序中的数据。

在底层，CUBA 平台会拦截对 EntityManager 和 JPQL 查询的所有调用，并根据应用程序设置进行转换。框架做了大量复杂的工作来保证正确的应用程序行为：`被删除` 的记录将不受更新影响， `DELETE` 语句将转换为 `UPDATE`，而 `SELECT` 将不再获取 “已删除” 数据 。

The implementation is built on three main pillars:
1. `SoftDelete` interface. CUBA uses JPA (EclipseLink) as the default framework to access data. If your JPA entity implements the `SoftDelete` interface, CUBA will not erase data from the database but just mark it as deleted.
2. `EntityManager` class. This is the main API to deal with data in CUBA. It intercepts all requests to the database and invokes either `delete` or `update` statements based on the entity type and its `soft delete` field value (enabled or disabled). So you can disable soft delete for entities in runtime if required. This approach allows us to invoke JPA entity callbacks related to entity removal, although deep inside the CUBA platform an update statement is generated.
3. `JoinExpressionProvider` API interface in the framework core. The `SoftDeleteJoinExpressionProvider` implementation generates a predicate to take into account an entity status (deleted or not) and its position in the query. E.g. if we select an existing record that references a “soft deleted” record, then the proper condition will be added to the query to select the “deleted” record.

Those APIs make the soft delete process transparent for developers, and removes the burden of answering the “delete or soft delete” question each time when you invoke a JPA API or send a JPQL query. 

This works most of the time, but there is no perfection in this world. For the sake of flexibility, CUBA still allows you to execute native SQL and get access to an underlying DataSource object. So, even with the framework that resolves many soft delete implementation problems, you need to double-check your low-level queries. 

## Conclusion

Using soft-deletion even for one entity will lead you to a bunch of new development conventions. You need to keep in mind that the delete process is not just delete, but sometimes the update, and not all data in a database table should be available for the application. It means that your code should be engineered in a way to take all that into account in all application tiers - from database schema (document structure, etc.) to user interface (even that). 

With a proper framework you will be able to resolve all these problems, but you should still keep in mind that you have “deleted” data that still exists, and build coding standards and conventions that take this into account.
