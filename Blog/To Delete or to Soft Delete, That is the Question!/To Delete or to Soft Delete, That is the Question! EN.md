Soft deletion is a widely used pattern applied for business applications. It allows you to mark some records as deleted without actual erasure from the database. Effectively, you prevent a soft-deleted record from being selected, meanwhile all old records can still refer to it.

## When would it make sense to use Soft Delete?

If you ask a few developers randomly, most probably you will hear that soft deletion is used to be able to restore deleted records. However, if this is the only business requirement that pushes you towards integrating soft-deletion - think twice. 
Consider the following requirements in addition to “simple data restore” before making a decision on using soft deletion in your project:
1. History tracking and audit (e.g. for legal reasons)
2. Keeping reference integrity and avoid cascade delete
3. You need “Graceful” delete. E.g. a long-running business process may need data that might be “deleted”, but still required for this particular process to finish

If you see that your business requirements match the list above, it means that you may need to implement the “Soft Delete” feature in your application. 

And implementing soft delete is easy! You just add the “Is_Deleted” or “Delete_Date” column to your tables (or attributes to your Document) and replace all delete statement invocations in your application code to updates. And yes, you need to modify all retrieve statements to take into account this new attribute. Or is it better to create a view? Or a materialized view? In this case, we may need to implement “instead of” triggers or stored procedures API to deal with views. And by deleting an entity you may want to delete some attributes it refers to. In this case, to be able to restore it is also required to store deletion context. And… this is where a developer’s life gets complicated. 

## Challenges of Soft Deletion

In fact, implementing “proper” soft delete is not an easy task. Sometimes developers choose NOT to implement soft delete just because it takes a lot of efforts. You might think about other options like table mirroring with the special “trash can” table that contains deleted records from the “original” table. 

But if it is decided to go forward with the soft delete implementation, you need to take into account some conditions.

**Database support.** One of the issues with records that were marked as deleted is their unique keys. As an example - we need to store users along with their emails. Emails must be unique, therefore, we need to create a unique index for the table that stores users. And if a user is deleted, we should be able to insert a new user with the same email. So, your database should be able to handle such cases. One of the options - include a soft delete marker field into the unique key. If your marker field is not a simple boolean value, but delete timestamp, it may do the trick. 

Another option - special unique indexes. For example, in PostgreSQL it is called partial [unique indexes](https://www.postgresql.org/docs/12/indexes-partial.html), in MSSQL - [filtered indexes](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/create-filtered-indexes). They allow you not to take some rows into account when building indexes by specifying a predicate. So, for the soft delete feature implementation, we can exclude “deleted” records from the index.

If your database supports triggers for views, there is a good chance that you can create your data model using views and `instead of` type of triggers. But this approach causes performance impact and does not resolve the issue with unique constraints applied to the underlying table(s).

**Application Support.** Make sure ALL queries mentioning soft-deleted entities are double-checked, otherwise it can lead to unexpected data leaks and critical performance issues. In the ideal world, a developer should not be aware about soft delete existence. So, if possible, try to introduce your data access API and prevent developers from using other facilities to access a database (like direct JDBC connection). You might even want to override standard APIs like JPA’s Entity Manager to support soft delete properly. 

**Revisit standard APIs usage.** First of all, forget about using some of the standard DB and JPA features. If you have your database table and constraint model carefully engineered and tuned, as well as you JPA layer, introducing soft delete requires you to consider the following:

1. Standard cascade policies will not work. Remember that in case of soft delete, you don’t delete records, you update them. If you still need cascade delete, triggers may help you to implement this behavior.  
2. As for the triggers - you’ll need to review them, too, to ensure that the code from pre- and post- delete triggers is executed in pre- post- update triggers during update-as-soft-delete and not executed during “common” update process.
3. JPA lifecycle callbacks `@PreRemove` and `@PostRemove` will not work. Again, look for other options to implement the functionality that is coded in those callbacks. The rule here is similar to the one applied to triggers - in update-related callbacks you need to distinguish between update and soft delete processes. 

**You still need to meet regulatory requirements.** As an example - GDPR. The regulation says that the data must be completely removed if it is required by a user. To comply with this you still need to implement a “purge” process in addition to soft delete. And this purge procedure affects related data in your database in most cases. So, you need to be careful and not to delete business-critical data. 

## Soft Delete implementation in CUBA Platform

CUBA Platform supports soft delete out-of-the-box and provides soft delete feature as part of the default application behaviour. In most cases, a developer should not worry whether soft delete is used or not. Data manipulation API in CUBA is 99% the same as the well-known JPA API. You, as a developer, can use `EntityManager`’s `remove()` or even use arbitrary JPQL to deal with data in the application. 

Under the hood the CUBA Platform intercepts all invocations to the EntityManager as well as JPQL queries, and transforms them according to the application settings. The framework will take the heavy lifting work and provide correct application behavior: “deleted” records won’t be affected by an update, `DELETE` statement will be transformed to `UPDATE` and `SELECT` won’t fetch “deleted” data.

The implementation is built on three main pillars:
1. `SoftDelete` interface. CUBA uses JPA (EclipseLink) as the default framework to access data. If your JPA entity implements the `SoftDelete` interface, CUBA will not erase data from the database but just mark it as deleted.
2. `EntityManager` class. This is the main API to deal with data in CUBA. It intercepts all requests to the database and invokes either `delete` or `update` statements based on the entity type and its `soft delete` field value (enabled or disabled). So you can disable soft delete for entities in runtime if required. This approach allows us to invoke JPA entity callbacks related to entity removal, although deep inside the CUBA platform an update statement is generated.
3. `JoinExpressionProvider` API interface in the framework core. The `SoftDeleteJoinExpressionProvider` implementation generates a predicate to take into account an entity status (deleted or not) and its position in the query. E.g. if we select an existing record that references a “soft deleted” record, then the proper condition will be added to the query to select the “deleted” record.

Those APIs make the soft delete process transparent for developers, and removes the burden of answering the “delete or soft delete” question each time when you invoke a JPA API or send a JPQL query. 

This works most of the time, but there is no perfection in this world. For the sake of flexibility, CUBA still allows you to execute native SQL and get access to an underlying DataSource object. So, even with the framework that resolves many soft delete implementation problems, you need to double-check your low-level queries. 

## Conclusion

Using soft-deletion even for one entity will lead you to a bunch of new development conventions. You need to keep in mind that the delete process is not just delete, but sometimes the update, and not all data in a database table should be available for the application. It means that your code should be engineered in a way to take all that into account in all application tiers - from database schema (document structure, etc.) to user interface (even that). 

With a proper framework you will be able to resolve all these problems, but you should still keep in mind that you have “deleted” data that still exists, and build coding standards and conventions that take this into account.
