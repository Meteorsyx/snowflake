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



### 共享磁盘（可扩展）架构

共享磁盘架构是一种早期的扩展方法，旨在将数据存储在中央存储位置，并从多个数据库集群节点访问。所有集群节点访问的数据是一致的，所有数据写入共享磁盘。但需要复杂的磁盘锁定机制来确保数据一致性，同时，数据并发性，允许多个用户在数据库中影响多个事务，也是一个主要问题。



![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1633e82996fd4890b63334ab57e55bfc~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)



### 共享无关（可扩展）架构

共享无关架构是为了应对共享磁盘架构所造成的瓶颈而设计的，其中存储和计算是一起扩展的。

数据在节点之间的分布方式将决定额外开销的程度。在存储和计算之间取得合适的平衡尤为困难。即使可以调整集群的大小，也需要时间进行调整。因此，组织往往会过度配置共享无关资源，导致未使用和不必要的资源。共享无关架构的示例包括IBM DB2、Vertica和Pivotal Greenplum。



![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d2fd7f0e8403497da3f9dc924b4c4505~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)





### NoSQL架构

大多数NoSQL解决方案依赖于共享无关架构，因此它们具有许多相同的限制。NoSQL解决方案的好处是可以存储非关系型数据，而无需先对数据进行转换。

有四种类型的NoSQL数据库：文档存储（document stores）、键值（key-value）存储、列族数据存储或宽列数据存储，以及图形数据库。

nosql存储在大量记录的计算中(如聚合，窗口函数和任意序列)性能表现较差。在表中增删改查单个条目时，nosql表现较好，但不建议进行分析，分析方面表现不佳。



### Snowflake架构

传统架构中可扩展性问题难以解决。Snowflake团队决定采取一种独特的方法。他们不是试图逐步改进或转变现有的软件架构，而是为云环境构建了一个全新的现代数据平台，允许多个用户同时共享实时数据。



Snowflake混合模型架构由三个层级组成，如图2-3所示：云服务层、计算层和数据存储层。每个层级以及三个Snowflake缓存将在以下章节中详细讨论。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f08bba5d6f0249a2a9358c5adafa9849~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

Snowflake的处理引擎是原生的SQL，并且正如我们将在后面的章节中看到的那样，Snowflake还能够处理半结构化和非结构化数据。



#### 云服务层

在Snowflake实例中，所有与数据的交互都始于云服务层，也称为全局服务层（如图2-4所示）。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3373fcb99a4d4062b1eed8f01b308fa4~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

它能协调身份验证、访问控制和加密等活动。它还处理基础设施和元数据的管理功能，以及执行查询解析和优化等功能。

每当用户请求登录时，该请求都会由云服务层处理。当用户提交一个Snowflake查询时，该SQL查询将首先发送到云服务层的优化器，然后再发送到计算层进行处理。云服务层使得可以通过SQL客户端界面执行对数据的数据定义语言（DDL）和数据操作语言（DML）操作。

即使特定用例的云服务成本较高，从经济和/或战略上来看，有时承担这些成本也是有意义的。例如，利用查询的结果缓存，特别是对于大型或复杂的查询，将意味着该查询的计算成本为零。对于DDL命令，特别是在克隆等用例中，选择合适的频率和粒度可以更好地平衡云服务消耗成本和虚拟仓库成本，从而实现整体成本的降低。



#### 查询处理（虚拟仓库）计算层

Snowflake的计算集群，通常简称为虚拟仓库，是由CPU、内存和临时存储组成的动态计算资源集群。

大多数sql查询和所有DML操作都需要一个正在运行的虚拟仓库。一些sql查询可以在不需要虚拟仓库的情况下执行，这和查询结构缓存有关。



Snowflake的独特架构实现了存储和计算的分离，这意味着任何虚拟仓库都可以访问与其他仓库相同的数据，而不会产生争用或影响其他仓库的性能。这是因为每个Snowflake虚拟仓库都是独立运行的，不与其他虚拟仓库共享计算资源（如图2-5所示）。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1791d9afc47549fba0dce14408ea251e~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)



一个虚拟仓库在运行时始终会消耗计算资源。然而，Snowflake的虚拟仓库可以随时启动和停止，并且可以在运行时调整大小。Snowflake支持两种不同的仓库扩展方式。通过调整仓库的大小可以将虚拟仓库的规模扩大（scale up），而通过添加集群可以将虚拟仓库的规模扩展（scale out）。可以同时使用这两种扩展方法，也可以只使用其中一种。

**区别总结：**

| 特征     | 添加集群 (Scale Out)   | 扩大仓库规模 (Scale Up)      |
| -------- | ---------------------- | ---------------------------- |
| 扩展方式 | 横向扩展，增加集群数量 | 纵向扩展，增加单个仓库的资源 |
| 主要作用 | 提升并发处理能力       | 提升单个查询的执行速度       |
| 类比     | 多个工厂               | 更强大的工厂                 |
| 适用场景 | 高并发场景             | 复杂、数据量大的查询         |

**选择建议：**

- 如果面临高并发场景，需要同时处理大量用户的查询请求，建议添加集群。
- 如果需要处理复杂、数据量大的查询，需要提升单个查询的执行速度，建议扩大仓库规模。

总而言之，添加集群和扩大仓库规模是两种不同的扩展方式，需要根据实际需求选择合适的方案。

##### 将虚拟仓库的规模扩大以处理大数据量和复杂查询

许多因素会影响虚拟仓库的性能。并发查询的数量、正在查询的表的数量以及数据的大小和组成等因素都应在调整Snowflake虚拟仓库大小时加以考虑。

适当调整大小很重要。如果虚拟仓库太小，导致资源不足，可能会导致查询花费太长时间。如果查询过小且虚拟仓库过大，可能会对成本产生负面影响。

##### 通过使用多集群虚拟仓库进行横向扩展，以最大化并发性

如果并发性问题是由于用户或连接数过多而导致的，扩容将无法充分解决问题。相反，我们需要通过添加集群来进行横向扩展（如图2-9所示），例如从最小集群数为1扩展到最大集群数为3。多集群虚拟仓库可以设置为在用户数量和/或查询数量波动时自动进行扩缩容。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/69539e628f9645ecaedb9b50cfb40973~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

当多集群虚拟仓库配置为采用标准扩缩容策略时，第一个虚拟仓库将在查询排队时立即启动，或者当Snowflake系统检测到比当前运行的集群能够执行的查询多一个时启动。每个后续虚拟仓库将在前一个虚拟仓库启动后的20秒启动。 如果多集群虚拟仓库配置为采用经济扩缩容策略，只有当Snowflake系统估计查询负载可以至少使虚拟仓库保持繁忙六分钟时，虚拟仓库才会启动。

##### 创建和使用虚拟仓库

使用SQL命令在Web界面或工作表中执行虚拟仓库操作。

以下SQL脚本将创建一个具有四个集群的中型虚拟仓库，并在五分钟后自动暂停。

```sql
USE ROLE SYSADMIN; 
CREATE WAREHOUSE CH2_WH WITH WAREHOUSE_SIZE = MEDIUM    
AUTO_SUSPEND = 300 
AUTO_RESUME = true 
INITIALLY_SUSPENDED = true;
```

之前我们讨论了如何通过扩容或缩容来改变虚拟仓库的大小，以及这是一个手动的过程。为了进行扩容或缩容，我们将使用ALTER命令：

```sql
USE ROLE SYSADMIN;
ALTER WAREHOUSE CH2_WH
SET WAREHOUSE_SIZE = LARGE;
```



##### 工作负载的分离和工作负载管理

在传统数据库中，工作负载达到最大容量时，查询处理会变慢。

snowflake在工作负载接近100%时，每个新的查询会被暂停在队列中，直到有足够的资源来执行它们。

有多种方法可以有效处理不同规模的工作负载。其中一种方法是通过将不同的虚拟仓库分配给不同的用户或用户组来分离工作负载（如图2-15所示）。图2-15中显示的虚拟仓库都是相同大小的，但在实际中，不同工作负载的虚拟仓库很可能是不同大小的。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f02364dc22b7484392e58a1740b3dc59~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

#### 集中化（混合列式）数据库存储层

Snowflake的集中化数据库存储层保存着所有的数据，包括结构化数据和半结构化数据。

每个Snowflake数据库由一个或多个模式（Schema）组成，模式是数据库对象（如表格和视图）的逻辑分组。

Snowflake的数据存储层有时被称为远程磁盘层。底层文件系统是在亚马逊、微软或谷歌云上实现的（如图2-17所示）。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f64f5f21190e462fa877fd37be7cca47~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)



##### 零拷贝克隆简介

零拷贝克隆提供给用户一种快照拷贝Snowflake数据库、模式或表以及其相关数据的方式。在进行零拷贝数据克隆时，不会额外收取存储费用，因为它只是一个元数据操作。零拷贝克隆除了用于创建备份外，还有许多其他用途。最常见的用途是用于支持开发和测试环境。我们将在第8章中看到相关示例。



##### 时间旅行简介

时间旅行(Time Travel)允许您恢复数据库、表或模式的以前版本。

通过时间旅行，您还可以通过将时间旅行功能与克隆功能结合使用，从过去的不同时间点备份数据，或者可以查询不再存在的数据库对象。您可以回溯到过去多久取决于几个不同的因素。时间旅行将在第7章中详细讨论。



##### 存储层的计费

snowflake的数据存储费用是基于压缩数据的每日平均大小而计算的。包括：永久表中持久数据的存储费用，批量数据加载和卸载文件的费用。故障安全数据和使用时间旅行进行数据恢复的费用。同样，被删除数据引用的表的克隆也会被考虑在内。



#### Snowflake缓存

当您提交一个查询时，Snowflake会检查该查询是否之前已经运行过，并且结果是否仍然存在缓存中。如果结果仍然可用，Snowflake将使用缓存的结果集而不是执行您刚刚提交的查询。除了从缓存中检索先前的查询结果外，Snowflake还支持其他缓存技术。Snowflake有三种缓存类型：查询结果缓存、虚拟数据仓库缓存和元数据缓存。



### 知识检查

![image-20240925170638350](C:\Users\研修用\AppData\Roaming\Typora\typora-user-images\image-20240925170638350.png)



![image-20240925170657047](C:\Users\研修用\AppData\Roaming\Typora\typora-user-images\image-20240925170657047.png)

1. The three Snowflake architecture layers are the cloud services layer, the query processing (virtual warehouse) compute layer, and the centralized (hybridcolumnar) database storage layer

   Snowflake 架构的三个层分别是云服务层、查询处理（虚拟仓库）计算层和集中式（混合列式）数据库存储层



2. The cloud services layer and the centralized (hybrid columnar) database storage layer are multitenant. The query processing (virtual warehouse) compute layer is not multitenant. Note that Snowflake is a multitenant service and the cloud object store is a multitenant service. As such, data is not truly isolated at the public cloud level. It is encryption that creates the isolation. Snowflake’s hybrid　tenancy policy, at the public cloud level, uses a multitenant table to consolidate storage but allocates dedicated compute resources to each tenant.

   云服务层和集中式（混合列式）数据库存储层是多租户的。查询处理（虚拟仓库）计算层不是多租户的。请注意，Snowflake 是一种多租户服务，云对象存储也是一种多租户服务。因此，数据在公共云级别并没有真正隔离。加密创造了隔离。在公共云级别，Snowflake 的混合租户策略使用多租户表来整合存储，但为每个租户分配专用的计算资源。

3. The virtual warehouse cache is located in the compute layer. The result cache is located in the cloud services layer. The metadata storage cache layer is located in the cloud services layer.

   虚拟仓库缓存位于计算层，结果缓存位于云服务层，元数据存储缓存层位于云服务层。

4. You might want to investigate the following if you are experiencing higher than

   expected costs for Snowflake cloud services:

   • Simple queries accessing session information or using session variables

   • Large, complex queries with many joins

   • Single-row inserts, as compared to bulk or batch loading

   • Frequent use of INFORMATION_SCHEMA commands

   • Frequent use of metadata-only commands such as the SHOW command

   如果您遇到 Snowflake 云服务成本高于预期的情况，您可能需要调查以下问题：
   • 访问会话信息或使用会话变量的简单查询

   - **Simple queries accessing session information or using session variables:** While seemingly innocuous, repeatedly executing these queries can consume unnecessary compute resources, especially if they're part of a larger application or process that runs frequently. The overhead of establishing a session and retrieving this information adds up over time.
     **访问会话信息或使用会话变量的简单查询：**虽然看似无害，但重复执行这些查询可能会消耗不必要的计算资源，尤其是当它们是频繁运行的大型应用程序或进程的一部分时。建立会话和检索此信息的开销会随着时间的推移而增加。

   • 具有许多连接的大型复杂查询

   - **Large, complex queries with many joins:** These queries are computationally expensive. The more joins involved, the more data Snowflake needs to process, leading to longer query execution times and higher compute costs. Inefficient query optimization can exacerbate this issue.
     **具有许多联接的大型复杂查询：**这些查询的计算成本很高。涉及的联接越多，Snowflake 需要处理的数据就越多，从而导致查询执行时间更长，计算成本更高。低效的查询优化可能会加剧此问题。

   • 与批量或批量加载相比，单行插入

   - **Single-row inserts compared to batch or bulk loading:** Inserting data row by row is significantly less efficient than using batch or bulk loading methods. Each single-row insert requires a separate transaction, incurring more overhead than a single, larger operation.
     **单行刀片与批量或批量加载的比较：**逐行插入数据的效率明显低于使用批处理或批量加载方法。每个单行插入都需要一个单独的事务，这比单个较大的操作产生更多的开销。

   • 频繁使用 INFORMATION_SCHEMA 命令

   - **Frequent use of INFORMATION_SCHEMA commands:** While `INFORMATION_SCHEMA` provides valuable metadata, excessive querying can consume compute resources. If this information is needed repeatedly, caching the results or optimizing the querying strategy can significantly reduce costs.
     **频繁使用 INFORMATION_SCHEMA 命令：**虽然 `INFORMATION_SCHEMA` 提供了有价值的元数据，但过多的查询会消耗计算资源。如果重复需要此信息，则缓存结果或优化查询策略可以显著降低成本。

   • 频繁使用仅元数据命令（例如 SHOW 命令）

   - **Frequent use of metadata-only commands (e.g., SHOW commands):** Similar to `INFORMATION_SCHEMA` queries, frequent use of `SHOW` commands, while useful for administrative tasks, can add up to significant compute costs if not managed carefully. These commands, while not processing large datasets, still require resources to execute.
     **频繁使用仅元数据命令（例如 SHOW 命令）：**与 `INFORMATION_SCHEMA` 查询类似，频繁使用 `SHOW` 命令虽然对管理任务很有用，但如果不仔细管理，可能会增加大量的计算成本。这些命令虽然不处理大型数据集，但仍需要资源来执行。

5. Scaling up is a manual resizing of a virtual warehouse to a larger or smaller size and is most often undertaken to improve query performance and handle large workloads. Scaling out is an automatic process of increasing and decreasing the number of compute clusters and is more often used to maximize concurrency. Scaling out is achieved using multicluster virtual warehouses, which can automatically scale if the number of users and/or queries tends to fluctuate.

   纵向扩展是手动将虚拟仓库调整为更大或更小的规模，通常用于提高查询性能和处理大量工作负载。横向扩展是自动增加和减少计算集群数量的过程，通常用于最大化并发性。扩展是使用多集群虚拟仓库实现的，如果用户和/或查询数量出现波动，它可以自动扩展。

6. Compute can be scaled up, down, in, or out. In all cases, there is no effect on storage used.

   计算可以扩展、缩小或扩大。在所有情况下，对使用的存储都没有影响。

7. The scalability problem is the main challenge that has been difficult for architectures to solve. Platform architectures need to be scalable to support sharing of the same data at the same time with data-driven teams, large and small, near or far from the data.

   可扩展性问题是架构一直难以解决的主要挑战。平台架构需要可扩展，以支持与数据驱动团队（无论规模大小、距离数据近还是远）同时共享相同的数据。

8. When Auto-scale mode is selected, the choice of scaling policy is either Standard or Economy:
   • With the Standard scaling policy, the first virtual warehouse immediately starts when a query is queued, or the Snowflake system detects that there is more than one query than the currently running clusters can execute.
   • With the Economy scaling policy, a virtual warehouse starts only if the Snowflake system estimates the query load can keep the virtual warehouse busy for at least six minutes. The goal of the Economy scaling policy is to conserve credits by keeping virtual warehouses fully loaded.

   选择自动扩展模式时，扩展策略的选择为标准或经济：
   • 使用标准扩展策略，当查询排队时，第一个虚拟仓库会立即启动，或者 Snowflake 系统检测到当前正在运行的集群无法执行的查询不止一个。
   • 使用经济扩展策略，只有当 Snowflake 系统估计查询负载可以让虚拟仓库忙碌至少六分钟时，虚拟仓库才会启动。经济扩展策略的目标是通过保持虚拟仓库满载来节省信用。

9. You need to configure the following components for multicluster virtual warehouses:
   • Mode
   — Auto-scale; can be set to Standard or Economy
   — Maximized; maximized when the Min Clusters value is greater than 1 and both the Min Clusters and Max Clusters values are equal
   • Min Clusters
   • Max Clusters

   您需要为多集群虚拟仓库配置以下组件：
   • 模式
   — 自动扩展；可设置为标准或经济
   — 最大化；当最小集群值大于 1 且最小集群值和最大集群值相等时最大化
   • 最小集群
   • 最大集群

10. You can change the virtual warehouse via the drop-down menu. You can use the USE WAREHOUSE SQL command in the worksheet.

   您可以通过下拉菜单更改虚拟仓库。您可以在工作表中使用 USE WAREHOUSE SQL 命令。





# Creating and Managing Snowflake Securable Database Objects

## Creating and Managing Snowflake Databases

the database and schema comprise the namespace.whenever we work with database objects we'll need to specify a namespace,unless he schema and database we want to use are the active context in workspace.

数据库和模式组成了命名空间。每当我们使用数据库对象时，我们都需要指定一个命名空间，除非我们要使用的模式和数据库是工作区中的活动上下文。

We can create two main types of databases: permanent (persistent) and transient. At the time we create a database, the default will be a permanent database, if we don’t specify which of the two types we want to create.

我们可以创建两种主要类型的数据库：永久（持久）和瞬态。在创建数据库时，如果我们不指定要创建这两种类型中的哪一种，则默认为永久数据库。



CREATE DATABASE is the command used to create a new database, clone an existing database, create a database from a share provided by another Snowflake account, or create a replica of an existing primary database (i.e., a secondary database).

CREATE DATABASE 是用于创建新数据库、克隆现有数据库、从另一个 Snowflake 帐户提供的共享创建数据库或创建现有主数据库的副本（即辅助数据库）的命令。



创建一个临时数据库

```SQL
USE ROLE SYSADMIN;
USE WAREHOUSE COMPUTE_WH;
CREATE OR REPLACE TRANSIENT DATABASE DEMO3B_DB
COMMENT = 'Transient Database for Chapter 3 Exercises';
```



retention_time是数据保留时间，与time travel 天数相同，并指定删除后保留基础数据的天数，并且可以对数据库执行CLONE and UNDROP命令。临时数据库无法修改保留时间。

![image-20240926140531604](C:\Users\研修用\AppData\Roaming\Typora\typora-user-images\image-20240926140531604.png)



修改数据保留时间

```SQL
USE ROLE SYSADMIN;
ALTER DATABASE DEMO3A_DB
SET DATA_RETENTION_TIME_IN_DAYS = 10;
```



## Creating and Managing Snowflake Schemas

创建schemas

```sql
USE ROLE SYSADMIN;
USE DATABASE DEMO3A_DB;
CREATE OR REPLACE SCHEMA BANKING;
```



```sql
//更改模式的保留时间
USE ROLE SYSADMIN;
ALTER SCHEMA DEMO3A_DB.BANKING
SET DATA_RETENTION_TIME_IN_DAYS=1;
```



```sql
//更改表存在的schemas
use role sysadmin;
create or replace schema demo3b_db.banking;
alter table demo3b_db.public.summary
rename to demo3b_db.banking.summary;
```

 In a managed access schema, the schema owner manages grants on the objects within a schema, such as tables and views, but doesn’t have any of the USAGE, SELECT, or DROP privileges on the objects.

在托管访问模式中，模式所有者管理模式内对象（例如表和视图）的授权，但不拥有这些对象的任何 USAGE、SELECT 或 DROP 权限。



托管访问模式下，只有schema owner 和 manage grants 可以管理授权权限。



## INFORMATION_SCHEMA

INFORMATION_SCHEMA包括有关数据库中对象的元数据信息以及账户级对象（如角色）。

每个INFORMATION_SCHEMA都包含20多个系统定义的试图。这些视图可以分为两类: 账户视图和数据库视图。



## ACCOUNT_USAGE  Schema

ACCOUNT_USAGE schema is very similar to the INFORMATION_SCHEMA, but with three differences:
• The SNOWFLAKE database ACCOUNT_USAGE schema includes records for dropped objects whereas the INFORMATION_SCHEMA does not.
• The ACCOUNT_USAGE schema has a longer retention time for historical usage data. Whereas the INFORMATION_SCHEMA has data available ranging from seven days to six months, the ACCOUNT_USAGE view retains historical data for one year.
• Most views in the INFORMATION_SCHEMA have no latency, but
the latency time for ACCOUNT_USAGE could range from 45 minutes
to three hours. Specifically, for the INFORMATION_SCHEMA, there
may be a one- to two-hour delay in updating storage-related statistics
for ACTIVE_BYTES, TIME_TRAVEL_BYTES, FAILSAFE_BYTES, and
RETAINED_FOR_CLONE_BYTES.

ACCOUNT_USAGE 架构与 INFORMATION_SCHEMA 非常相似，但有三个不同之处：
• SNOWFLAKE 数据库 ACCOUNT_USAGE 架构包含已删除对象的记录，而 INFORMATION_SCHEMA 则不包含。
• ACCOUNT_USAGE 架构对历史使用数据的保留时间更长。INFORMATION_SCHEMA 的数据可用时间为 7 天到 6 个月，而 ACCOUNT_USAGE 视图保留历史数据为一年。
• INFORMATION_SCHEMA 中的大多数视图没有延迟，但 ACCOUNT_USAGE 的延迟时间可能为 45 分钟到 3 小时。具体而言，对于 INFORMATION_SCHEMA，更新 ACTIVE_BYTES、TIME_TRAVEL_BYTES、FAILSAFE_BYTES 和 RETAINED_FOR_CLONE_BYTES 的存储相关统计信息可能会有一到两个小时的延迟。



ACCOUNT_USAGE Schema 的常见用途之一是跟踪您的账户中每个虚拟仓库在一段时间内（月初至今）使用的积分

```sql
USE ROLE ACCOUNTADMIN;
USE DATABASE SNOWFLAKE;
USE SCHEMA ACCOUNT_USAGE;
USE WAREHOUSE COMPUTE_WH;
SELECT start_time::date AS USAGE_DATE, WAREHOUSE_NAME,
    SUM(credits_used) AS TOTAL_CREDITS_CONSUMED
FROM warehouse_metering_history
WHERE start_time >=date_trunc(Month,current_date)
GROUP BY 1,2
ORDER BY 2,1;
```



## Schema Object Hierarchy 模式对象层次结构

## Snowflake简介

临时表不能转换为永久表，反之亦然。如果需要转换，需要建立一个新表，再将数据复制进去。

## Creating and Managing Views



| 特性       | 非具体化视图                           | 具体化视图                               |
| ---------- | -------------------------------------- | ---------------------------------------- |
| 数据存储   | 不存储数据                             | 存储查询结果的快照                       |
| 数据更新   | 实时更新                               | 需要定期刷新                             |
| 性能       | 对于简单查询性能较好，复杂查询性能较差 | 对于复杂查询性能较好，但需要考虑刷新成本 |
| 空间占用   | 占用空间小                             | 占用空间大                               |
| 数据一致性 | 数据一致性高                           | 数据一致性取决于刷新频率                 |



## Introduction to Snowflake Stages: File Format Included

使用stage存储文件，可以将文件数据库通过分隔符快速将数据导入表中。



![image-20240927151056214](C:\Users\研修用\AppData\Roaming\Typora\typora-user-images\image-20240927151056214.png)



## **Extending SQL with Stored Procedures and UDFs**

**两者区别总结:**

| 特性      | 存储过程                                      | 用户自定义函数 (UDF)                          |
| --------- | --------------------------------------------- | --------------------------------------------- |
| 返回值    | 可以返回多个值（通过输出参数）                | 只能返回单个值                                |
| SQL语句数 | 可以包含多个SQL语句                           | 通常只包含一个SQL语句或简单的逻辑块           |
| 数据修改  | 可以执行数据修改操作 (INSERT, UPDATE, DELETE) | 不能执行数据修改操作 (INSERT, UPDATE, DELETE) |
| 主要用途  | 复杂的数据库操作，业务逻辑实现                | 简洁的计算或逻辑操作，数据转换                |
| 调用方式  | `EXEC` 或 `CALL` 语句                         | 直接在SQL语句中调用，像内置函数一样使用       |

通过安全的UDF，使用者账户与其他用户共享此安全UDF时，可以无需看到任何底层数据，表结构或sql代码。



## Introduction to Pipes,Streams,and Sequences                          管道，流和序列介绍

**1. Pipes (管道):**

Pipes是Snowflake用于高效加载数据的机制。它允许你从外部存储（例如，云存储桶中的文件）自动、持续地将数据加载到Snowflake表中。 你可以配置Pipes来监控指定的存储位置，并在新文件出现时自动将它们加载到Snowflake中。这对于处理大量数据，特别是实时数据流非常有用。 Pipes通常与Snowpipe一起使用，Snowpipe是一个用于管理和监控数据加载到管道中的服务。[6](https://docs.snowflake.com/en/sql-reference/sql/create-pipe) [1](https://docs.snowflake.com/en/user-guide/data-load-snowpipe-intro)

**关键特性:**

- **自动加载:** 无需手动启动加载过程。
- **持续加载:** 持续监控数据源，并自动加载新数据。
- **高吞吐量:** 能够高效处理大量数据。
- **可扩展性:** 可以根据需要调整管道配置以处理不同的数据量。

**2. Streams (流):**

Streams是Snowflake用于捕获表中数据更改的机制。当表中的数据发生插入、更新或删除操作时，Streams会记录这些更改，并将其作为有序的更改记录序列存储起来。 你可以使用Streams来构建实时数据处理管道，例如，构建变更数据捕获（CDC）系统或实时数据分析应用。[4](https://risingwave.com/blog/snowflake-triggers-how-to-use-streams-tasks-examples/) [2](https://docs.snowflake.com/en/user-guide/streams-intro)

**关键特性:**

- **变更数据捕获:** 跟踪表中数据的更改。
- **实时处理:** 提供对实时数据更改的访问。
- **有序记录:** 按时间顺序记录更改。
- **事务一致性:** 确保更改记录的一致性。

**3. Sequences (序列):**

Sequences是Snowflake用于生成唯一数字序列的对象。 它们通常用于为表中的行生成唯一的标识符，例如主键。 Sequences可以自动生成数字，并确保生成的数字是唯一的，这对于避免主键冲突非常重要。[5](https://medium.com/@trustngs/snowflake-snowpro-core-preparation-part-5-file-formats-sequences-streams-tasks-df0c8a42fcb9)

**关键特性:**

- **唯一性:** 确保生成的数字是唯一的。
- **自动递增:** 自动生成下一个数字。
- **可定制:** 可以自定义序列的起始值、步长等参数。
- **并发安全:** 在并发环境下也能保证唯一性。

**总结:**

Pipes用于数据加载，Streams用于捕获数据更改，Sequences用于生成唯一数字。它们是Snowflake提供的不同数据管理工具，可以根据你的需求选择合适的工具来处理数据。 它们之间并没有直接的依赖关系，可以独立使用，也可以组合使用以构建更复杂的数据处理流程。 例如，你可以使用Pipes加载数据，然后使用Streams来监控这些数据的更改。
