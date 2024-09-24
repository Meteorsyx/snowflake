# 考试内容

![image-20240920145436916](C:\Users\研修用\AppData\Roaming\Typora\typora-user-images\image-20240920145436916.png)



![image-20240920154150328](C:\Users\研修用\AppData\Roaming\Typora\typora-user-images\image-20240920154150328.png)

# Virtual warehouses

a cluster of compute resources in Snowflake. A virtual warehouse is available in two types:
Snowflake 中的计算资源集群。虚拟仓库有两种类型：

- Standard 标准
- Snowpark-optimized 雪地公园优化



A warehouse provides the required resources, such as CPU, memory, and temporary storage, to perform the following operations in a Snowflake session:
仓库提供在 Snowflake 会话中执行以下操作所需的资源，例如 CPU、内存和临时存储：

- Executing SQL [SELECT](https://docs.snowflake.com/en/sql-reference/sql/select) statements that require compute resources (e.g. retrieving rows from tables and views).
  执行需要计算资源的SQL [SELECT](https://docs.snowflake.com/en/sql-reference/sql/select)语句（例如，从表和视图中检索行）。
- Performing DML operations, such as:
  执行DML操作，例如：
  - Updating rows in tables ([DELETE](https://docs.snowflake.com/en/sql-reference/sql/delete) , [INSERT](https://docs.snowflake.com/en/sql-reference/sql/insert) , [UPDATE](https://docs.snowflake.com/en/sql-reference/sql/update)).
    更新表中的行（ [DELETE](https://docs.snowflake.com/en/sql-reference/sql/delete) 、 [INSERT](https://docs.snowflake.com/en/sql-reference/sql/insert) 、 [UPDATE](https://docs.snowflake.com/en/sql-reference/sql/update) ）。
  - Loading data into tables ([COPY INTO ](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table)).
    将数据加载到表中（ [COPY INTO ](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table) ）。
  - Unloading data from tables ([COPY INTO ](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location)).
    正在从表中卸载数据 ( [COPY INTO ](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location) )。





### Impact on data loading 对数据加载的影响

Increasing the size of a warehouse does **not** always improve data loading performance. Data loading performance is influenced more by the number of files being loaded (and the size of each file) than the size of the warehouse.
增加仓库的大小**并不**总能提高数据加载性能。数据加载性能更多地受到正在加载的文件数量（以及每个文件的大小）的影响，而不是仓库大小的影响。



### Impact on query processing 对查询处理的影响

The size of a warehouse can impact the amount of time required to execute queries submitted to the warehouse, particularly for larger, more complex queries. In general, query performance scales with warehouse size because larger warehouses have more compute resources available to process queries.
仓库的大小可能会影响执行提交到仓库的查询所需的时间，特别是对于更大、更复杂的查询。一般来说，查询性能随着仓库大小而变化，因为较大的仓库有更多的计算资源可用于处理查询。





