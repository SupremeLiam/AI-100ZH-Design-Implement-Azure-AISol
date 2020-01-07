---
lab:
    title: '实验 5：将 QnA Maker 与机器人集成'
    module: '模块 3:使用 QnA Maker 增强机器人'
---

# 实验 5：创建自定义 QnA Maker 机器人

##  简介

在本实验中，我们将探索 QnA Maker，了解如何使用它创建连接到预先训练的知识库的机器人。  使用 QnAMaker 可以上传文档或指向网页，并让它预先填充知识库，以便为常规用途（例如常见问题解答）的简单机器人提供信息。

## 实验 5.1：QnA Maker 设置

1.  打开 [Azure 门户](https://portal.azure.com)

1.  单击 **“添加新资源”**

1.  键入 **“QnA Maker”**，选择 **“QnA Maker”**

1.  单击 **“创建”**

1.  键入名称

1.  选择资源定价层的 **S0** 层。 我们不使用免费层，因为稍后我们将上传大于 1MB 的文件。

1.  选择你的资源组

1.  对于搜索定价层，请选择 **F** 层

1.  输入应用名称，该名称必须唯一

1.  单击 **创建**。 这将在你的资源组中创建以下资源：

-   应用服务计划
-   应用服务
-   Application Insights
-   搜索服务
-   QnAMaker 类型的认知服务实例

## 实验 5.2：创建知识库

1.  打开 [QnA Maker 站点](https://qnamaker.ai)

1.  在右上角，单击 **“登录”**。使用新 QnA Maker 资源的 Azure 凭据登录

1.  在顶部导航区，单击 **“创建知识库”**

1.  跳过 **步骤 1**，因为你已创建资源

1.  选择绑定到 QnA Maker 资源的 Azure AD 和订阅，然后选择新建的 QnA Maker 资源

1.  对于名称，键入 **“Microsoft FAQ”***

1.  对于文件，单击 **“添加文件”**，浏览至 **code/surface-pro-4-user-guide-EN.pdf** 文件

1.  对于文件，单击 **“添加文件”**，浏览至 **code/Manage Azure Blob Storage** 文件

> **注意：**[此处](https://docs.microsoft.com/zh-cn/azure/cognitive-services/qnamaker/concepts/data-sources-supported) 提供有关支持的文件类型和数据源的详细信息

1.  对于 **“闲聊”**，选择 **“风趣”**

1.  单击 **“创建 KB”**

## 实验 5.3：发布和测试知识库

1.  查看知识库 QnA 对，根据我们提供的两个文档，你应该能看到约 200 个不同的对

1.  在顶部菜单中，单击 **“发布”**。  

1.  在“发布”页面上，单击 **“发布”**。 服务发布后，单击“成功”页面上的 **“创建机器人”** 按钮

1.  如果出现提示，请使用与实验室资源组绑定的帐户登录。

1.  在“机器人服务创建”页面上，修复所有命名错误，然后单击 **“创建”**。

> **注意：** Azure 的最新更改要求从某些资源名称中删除破折号 ("-")

1.  创建机器人资源后，导航至新的 **“Web 应用机器人”**，然后单击 **“在网上聊天中测试”**

1.  向机器人询问与 Surface Pro 4 或管理 Azure Blob 存储相关的任何问题：

+   如何增加内存？
+   电池充电需要多长时间？
+   如何进行硬重置？
+   什么是 blob？

1.  向机器人询问一些它不了解的问题，例如：

+   如何才能在打保龄球时一击全中？

## 实验 5.4：下载机器人源代码

1.  在 **“机器人管理”** 下，选择 **“生成”** 选项卡

1.  单击 **“下载机器人源代码”**，出现提示时单击 **“是”**。  

1.  Azure 将生成源代码，完成后，单击 **“下载机器人源代码”**，出现提示时，选择 **“是”**

1.  将压缩文件解压缩到本地计算机

1.  打开解决方案文件，Visual Studio 将打开

1.  打开 **Startup.cs** 文件，你会发现此处未添加任何特殊内容

1.  打开 **Bots/{BotName}.cs** 文件，请注意，这是 QnA Maker 的初始化位置：

```csharp
var qnaMaker = new QnAMaker(new QnAMakerEndpoint
{
    KnowledgeBaseId = _configuration["QnAKnowledgebaseId"],
    EndpointKey = _configuration["QnAAuthKey"],
    Host = GetHostname()
},
null,
httpClient);
```

然后调用：

```csharp
var response = await qnaMaker.GetAnswersAsync(turnContext);
if (response != null && response.Length > 0)
{
    await turnContext.SendActivityAsync(MessageFactory.Text(response[0].Answer), cancellationToken);
}
else
{
    await turnContext.SendActivityAsync(MessageFactory.Text("找不到任何 QnA Maker 答案。"), cancellationToken);
}
```

如你所见，只需几行代码，即可将生成的 QnA Maker 添加到你自己的机器人中，这非常简单。

## 延伸阅读

如何将 QnAMaker 代码集成到图片机器人中？

##  资源

-   [什么是 QnA Maker 服务？](https://docs.microsoft.com/zh-cn/azure/cognitive-services/qnamaker/overview/overview)

##  后续步骤

-   [实验 06-01：实现 LUIS](../Lab6-Implement_LUIS/01-Introduction.md)