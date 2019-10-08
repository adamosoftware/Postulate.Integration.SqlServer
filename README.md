Nuget package: **SqlIntegration.Library** pre-release version 0.2.3

This is intended to evolve into an Extract, Transform, and Load library for SQL Server -- utilities for merging data across connections for data warehouse and reporting applications. I'm also interested in data migrations that involve foreign keys and multi-tenant isolation. I've done a lot of work in this area over the years, but it's been all over the place. So, I'm trying to consolidate these prior efforts.

At this point, the main thing I have working is the [BulkInsert](https://github.com/adamosoftware/SqlIntegration/blob/master/SqlIntegration.Library/BulkInsert.cs) static class. This is for doing high volume inserts across connections. It's not very "smart" in the sense that it doesn't know how to *upsert* or merge data when some of it may exist already in the target connection. (I do offer a [callback](https://github.com/adamosoftware/SqlIntegration/blob/master/SqlIntegration.Library/Classes/BulkInsertOptions.cs#L12) option that lets you perform arbitrary validation or a check to see if a record exists before inserting it, but this totally defeats the performance gains of batched inserts.) It does leverage the ability of SQL INSERTs to have multiple VALUES clauses, which is how I achieve batching. I did this because I was having a lot of trouble using SqlDataAdapter batching -- I find it too [mysterious and uncooperative](https://github.com/dotnet/corefx/issues/29391).

At some point soon, I'll implement a **ViewMaterializer** which is used to copy rows incrementally as they change from a view to a table for faster reporting. It's typical to create views to encapsulate complex reporting logic, but their occasional complexity may make reports too slow. Some of this can be mitigated with indexed views, but frankly I've had mixed results with those. I wanted a generic solution for converting views to base tables incrementally and automatically. I've done this before, but it was [an old implementation](https://github.com/adamosoftware/ViewMaterializer/blob/master/ViewMaterializer/ViewMaterializer.cs) that needs to be modernized. This is meant to work with SQL Server [change tracking](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/about-change-tracking-sql-server?view=sql-server-2017).

Another thing I'm into is migrating data across systems. A few times in my life I've had to migrate data that generated new identity values that cascaded through many foreign keys. I'll have more to say on this when I can devote some time to it.
