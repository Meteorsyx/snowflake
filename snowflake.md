# 考试内容

![image-20240920145436916](C:\Users\研修用\AppData\Roaming\Typora\typora-user-images\image-20240920145436916.png)



![image-20240920154150328](C:\Users\研修用\AppData\Roaming\Typora\typora-user-images\image-20240920154150328.png)

# Virtual warehouses

## 1. Overview

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



## 2. Multicluster

### Benefits of multi-cluster warehouses 多集群仓库的好处

With a standard, single-cluster warehouse, if your user/query load increases to the point where you need more compute resources:
使用标准的单集群仓库，如果您的用户/查询负载增加到需要更多计算资源的程度：

1. You must either increase the size of the warehouse or start additional warehouses and explicitly redirect the additional users/queries to these warehouses.
   您必须增加仓库的大小或启动额外的仓库并显式地将额外的用户/查询重定向到这些仓库。
2. Then, when the resources are no longer needed, to conserve credits, you must manually downsize the larger warehouse or suspend the additional warehouses.
   然后，当不再需要资源时，为了节省积分，您必须手动缩小较大仓库的规模或暂停额外的仓库。

In contrast, a multi-cluster warehouse enables larger numbers of users to connect to the same size warehouse. In addition:
相比之下，多集群仓库可以让更多的用户连接到相同规模的仓库。此外：

- In Auto-scale mode, a multi-cluster warehouse eliminates the need for resizing the warehouse or starting and stopping additional warehouses to handle fluctuating workloads. Snowflake automatically starts and stops additional clusters as needed.
  在自动缩放模式下，多集群仓库无需调整仓库大小或启动和停止其他仓库来处理波动的工作负载。 Snowflake 根据需要自动启动和停止其他集群。
- In Maximized mode, you can control the capacity of the multi-cluster warehouse by increasing or decreasing the number of clusters as needed.
  在最大化模式下，您可以根据需要通过增加或减少集群数量来控制多集群仓库的容量。



## 3. Considerations　注意事项

The keys to using warehouses effectively and efficiently are:
有效且高效地使用仓库的关键是：

1. Experiment with different types of queries and different warehouse sizes to determine the combinations that best meet your specific query needs and workload.
   尝试不同类型的查询和不同的仓库大小，以确定最能满足您的特定查询需求和工作负载的组合。
2. Don’t focus on warehouse size. Snowflake utilizes per-second billing, so you can run larger warehouses (Large, X-Large, 2X-Large, etc.) and simply suspend them when not in use.
   不要关注仓库大小。 Snowflake 采用按秒计费，因此您可以运行更大的仓库（大型、超大、2 倍大等），并在不使用时简单地暂停它们。



# １.Snowflake AI and ML 雪花人工智能和机器学习





# 创建和管理Snowflake架构

我们将详细介绍Snowflake架构的三个不同层级：云服务层、查询处理（虚拟仓库）计算层和集中式（混合列式）数据库存储层。

