## 1_Azure 搜索：
预计用时：20-30 分钟

## Azure 搜索 

[Azure 搜索](https://docs.microsoft.com/zh-cn/azure/search/search-what-is-azure-search) 是一种“搜索即服务”的解决方案，开发人员通过该方案可将出色的搜索体验集成到应用程序中，而无需管理基础设施或成为搜索专家。

开发人员在 Azure 中寻找 PaaS 服务，以便在其应用中以更快的速度获得更佳结果。虽然搜索功能是许多类型应用程序的关键所在，但 Web 搜索引擎为搜索设置了很高的门槛。用户期望的是：即时结果，在键入时自动完成，突出显示搜索结果中的热门内容，良好的排名，并且即使他们拼写错误或包含额外字词，也要能够理解他们所寻找的内容。

搜索是一大困难而又很少属于核心专业知识的领域。从基础结构的角度来看，它需要具有高可用性、持久性、规模和操作。从功能角度来看，它需要具有排名、语言支持和地理空间功能。

![搜索要求示例](./resources/assets/AzureSearch-Example.png) 

上面的示例说明了用户在搜索体验中期望的一些组件。[Azure 搜索](https://docs.microsoft.com/zh-cn/azure/search/search-what-is-azure-search)可以实现这些用户体验功能，并提供[监视和报告](https://docs.microsoft.com/zh-cn/azure/search/search-traffic-analytics)、[简单评分](https://docs.microsoft.com/zh-cn/rest/api/searchservice/add-scoring-profiles-to-a-search-index)以及用于[原型](https://docs.microsoft.com/zh-cn/azure/search/search-import-data-portal)和[检验](https://docs.microsoft.com/zh-cn/azure/search/search-explorer)的工具。

常见工作流：
1. 预配服务
	- 你可以通过[门户](https://docs.microsoft.com/zh-cn/azure/search/search-create-service-portal)或 [PowerShell](https://docs.microsoft.com/zh-cn/azure/search/search-manage-powershell) 创建或预配 Azure 搜索服务。
2. 创建索引
	- [索引](https://docs.microsoft.com/zh-cn/azure/search/search-what-is-an-index) 是数据的容器，比如“表”。它具有架构、[CORS 选项](https://docs.microsoft.com/zh-cn/aspnet/core/security/cors)、 搜索选项。你可以在[门户](https://docs.microsoft.com/zh-cn/azure/search/search-create-index-portal)中或在[应用初始化](https://docs.microsoft.com/zh-cn/azure/search/search-create-index-dotnet)期间创建索引。 
3. 索引数据
	- 有两种方法可以[使用数据填充索引](https://docs.microsoft.com/zh-cn/azure/search/search-what-is-data-import)。第一个选项是使用 Azure 搜索 [REST API](https://docs.microsoft.com/zh-cn/azure/search/search-import-data-rest-api) 或 [.NET SDK](https://docs.microsoft.com/zh-cn/azure/search/search-import-data-dotnet) 手动将数据填充到索引中。第二个选项是将一个[支持的数据源](https://docs.microsoft.com/zh-cn/azure/search/search-import-data-portal)指向索引，并让 Azure 搜索自动按计划引入数据。
4. 搜索索引
	- 向 Azure 搜索提交搜索请求时，可以使用简单搜索选项，并可以对结果进行[筛选](https://docs.microsoft.com/zh-cn/azure/search/search-filters)、[排序](https://docs.microsoft.com/zh-cn/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[设计](https://docs.microsoft.com/zh-cn/azure/search/search-faceted-navigation)和[分页](https://docs.microsoft.com/zh-cn/azure/search/search-pagination-page-layout)。 你能够解决拼写错误、语音和正则表达式的问题，并且可以选择使用搜索和[建议](https://docs.microsoft.com/zh-cn/rest/api/searchservice/suggesters)。 通过这些查询参数，你能够对[全文搜索体验](https://docs.microsoft.com/zh-cn/azure/search/search-query-overview) 实现更深层次的控制


### 实验：创建 Azure 搜索服务

在 Azure 门户中，单击 **“创建资源”->“Web + 移动”->“Azure 搜索”**。

单击后，必须根据需要填写几个字段。对于本实验，“F0”免费层就足够了。每个订阅只能拥有一个免费 Azure 搜索实例，因此，如果你或订阅的其他成员已经使用了免费层，则将需要使用“基本”定价层。对本研讨会中的所有实验使用一个资源组。如果你已完成 `lab01.1-pcl_and_cognitive_services`，则可以使用该资源组。

![创建新的 Azure 搜索服务](./resources/assets/AzureSearch-CreateSearchService.png)

创建完成后，打开新搜索服务的面板。

### 实验：创建 Azure 搜索索引

索引是 Azure 搜索服务使用的文档和其他构造的永久存储。索引就像一个数据库，可以保存数据并接受搜索查询。你可以定义要映射的索引架构，以反映要搜索的文档的结构，类似于数据库中的字段。这些字段具有一些属性，这些属性告诉我们这是否为全文可搜索，或者是否可进行筛选。  可以通过编程方式[推送内容](https://docs.microsoft.com/zh-cn/rest/api/searchservice/addupdate-or-delete-documents) 或使用 [Azure 搜索索引器](https://docs.microsoft.com/zh-cn/azure/search/search-indexer-overview) （可抓取常见数据存储）将内容填充到 Azure 搜索中。

对于本实验，我们将使用[用于 Cosmos DB 的 Azure 搜索索引器](https://docs.microsoft.com/zh-cn/azure/search/search-howto-index-documentdb) 来抓取 Cosmos DB 容器中的数据。 

![导入向导](./resources/assets/AzureSearch-ImportData.png) 

在刚刚创建的 Azure 搜索边栏选项卡中，单击 **“导入数据”>“数据源”>“文档 DB”**。

![DocDB 导入向导](./resources/assets/AzureSearch-DataSource.png) 

单击后，为 Cosmos DB 数据源选择名称。如果你完成了上一个实验 (`lab01.1-pcl_and_cognitive_services`)，请选择数据所在的 Cosmos DB 帐户以及相应的容器和集合。如果你没有完成上一个实验，请选择“或输入连接字符串”并粘贴到 `AccountEndpoint=https://timedcosmosdb.documents.azure.com:443/;AccountKey=0aRt6JVgbf9KafBxRVuDMNfAj9YoSBbmpICdJ41N5CwHcjuMcVk7jWDBcu4BxbTitLR1zteauQsnF1Tgqs1A3g==;`。对于这两种情况，数据库应该是“图像”，而集合应该是“元数据”。

单击 **“确定”**。

此时，Azure 搜索将连接到你的 Cosmos DB 容器并分析一些文档，以确定 Azure 搜索索引的默认架构。完成此操作后，你可以根据应用程序的需要设置字段的属性。

>**注意**：你可能会看到一个警告（“_ts”字段不是有效的字段名称）。我们的实验中可以忽略此警告，不过你可以在[此处](https://docs.microsoft.com/azure/search/search-indexer-field-mappings) 阅读更多相关信息。

将索引名称更新为：**图像**

将密钥更新为：**id**（唯一标识每个文档）

将所有字段设置为 **“可检索”**（以便客户端使在搜索时检索这些字段）

将 **“标记”、“NumFaces”和“人脸”** 字段设置为 **“可筛选”**（以便使客户端据这些值筛选结果）

将 **“NumFaces”** 字段设置为 **“可排序”**（以便使客户端根据图像中的人脸数对结果进行排序）

将 **“标签”、“NumFaces”和“人脸”** 字段设置为 **“可分组”**（以便使客户端根据计数对结果进行分组，例如，在搜索结果中，有 5 张带有“沙滩”标签的图片）

将 **“标题”、“标签”和“人脸”** 字段设置为 **“可搜索”**（以便使客户端对这些字段中的文本进行全文搜索）

![配置 Azure 搜索索引](./resources/assets/AzureSearch-ConfigureIndex.png) 

此时，我们将配置 Azure 搜索分析器。  在较高的层次上，你可以将分析器视为采用用户输入的术语并在索引中查找最佳匹配术语的工具。  Azure 搜索包括在必应和 Office 等技术中使用的分析器，这些技术对 56 种语言有深入的了解。  

单击 **“分析器”** 选项卡并设置 **“标题”、“标签”和“人脸”** 字段，以使用 **“英语 - Microsoft”**[分析器](https://docs.microsoft.com/zh-cn/azure/search/search-analyzers)。

![语言分析器](./resources/assets/AzureSearch-Analyzer.png) 

对于索引配置的最后一步，我们将创建一个[**建议器**](https://docs.microsoft.com/zh-cn/rest/api/searchservice/suggesters)来设置要用于提前键入的字段，使用户可键入单词的某些部分，Azure 搜索将在这些字段中查找最佳匹配。要了解关于建议器的详细信息，以及如何扩展搜索以支持模糊匹配，使自己能够根据紧密匹配获得结果，请查看[此示例](https://docs.microsoft.com/zh-cn/azure/search/search-query-lucene-examples#fuzzy-search-example)。


单击 **“建议器”** 选项卡，输入一个建议器名称：**sg** 并选择 **“标签”和“人脸”** 作为字段来查找术语建议

![搜索建议](./resources/assets/AzureSearch-Suggester.png) 

单击 **“确定”** 可完成索引器的配置。  可以按计划设置索引器检查更改的频率，但对于本实验，我们仅运行一次。  

单击 **“高级选项”** 并选择 **“Base 64 编码键”**，确保 ID 字段只使用 Azure 搜索密钥字段中支持的字符。

单击 **“确定”三次** 可启动索引器作业，该作业将开始从 Cosmos DB 数据库导入数据。

![配置索引器](./resources/assets/AzureSearch-ConfigureIndexer.png) 

***查询搜索索引***

你应该会看到一条消息，指示已开始编制索引。  如果要检查索引的状态，可以在主 Azure 搜索边栏选项卡中选择“索引”选项。

此时我们可以尝试搜索索引。  

单击 **“搜索资源管理器”** 并在生成的边栏选项卡中选择你的索引（如果尚未选择）。

单击 **“搜索”** 搜索所有文件。尝试搜索“水”或其他东西，然后使用 **“Ctrl + 单击”** 选择并查看 URL。结果和预期的一样么？

![搜索资源管理器](./resources/assets/AzureSearch-SearchExplorer.png)

在生成的 json 中，你将看到 `@search.score` 后面有一个数字。评分是指对搜索结果中所返回每一项的搜索得分进行计算。该分数是当前搜索操作上下文中项目相关性的指标。分数越高，项目相关性越大。在搜索结果中，根据每个项目计算出的搜索得分，从高到低对项目进行排序。

Azure 搜索使用默认评分来计算初始分数，但可以通过[计分概要文件](https://docs.microsoft.com/zh-cn/rest/api/searchservice/add-scoring-profiles-to-a-search-index) 自定义该计算。如果你想体验一下使用[术语提升](https://docs.microsoft.com/zh-cn/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost) 来评分，本研讨会结束时还有一个额外的实验。

**提早完成？请尝试这个额外加分练习的实验：**

[Postman](https://www.getpostman.com/) 是一款不错的工具，可让你轻松执行 Azure 搜索 REST API 调用，并且它还是一款不错的调试工具。  可从 Azure 搜索资源管理器中获取任何查询，同时获取执行 Azure 搜索 API 密钥，以在 Postman 中执行。

下载 [Postman](https://www.getpostman.com/) 工具并安装。 

安装后，从 Azure 搜索资源管理器中获取查询并将其粘贴到 Postman 中，选择 GET 作为请求类型。  

单击“标头”并输入以下参数：

+ 内容类型：application/json
+ api-key：[在“密钥”部分下的 Azure 搜索门户中输入 API 密钥]

选择“发送”，你应该会看到数据的格式设置为 JSON 格式。

尝试使用[以下示例](https://docs.microsoft.com/zh-cn/rest/api/searchservice/search-documents#a-namebkmkexamplesa-examples)执行其他搜索。


### 继续 [2_LUIS](./2_LUIS.md)

返回 [README](./0_README.md)