# 使用 LUIS 和 Azure 搜索开发智能应用程序

在此动手实验中，你将了解如何使用 Microsoft Bot Framework、Azure 搜索和 Microsoft 的语言理解智能服务 (LUIS) 从端到端创建智能机器人。


## 目标
在本研讨会中，你将学习以下内容：
- 了解如何实现 Azure 搜索功能，从而在应用程序内部提供积极的搜索体验
- 配置 Azure 搜索服务以扩展数据来启用全文、语言感知搜索
- 生成、训练和发布 LUIS 模型以帮助机器人有效沟通
- 使用利用 LUIS 和 Azure 搜索的 Microsoft Bot Framework 生成智能机器人


虽然重点在于 LUIS 和 Azure 搜索，但你还将利用以下技术：

- Data Science Virtual Machine (DSVM)
- Windows 10 SDK (UWP)
- CosmosDB
- Azure 存储
- Visual Studio


## 前提条件

本研讨会面向 Azure 的 AI 开发人员。由于这是一个简短的研讨会，因此在你参与之前需要具备一些条件。

第一，你应该拥有使用 Visual Studio 的经验。我们会将它用于我们在研讨会中构建的所有内容，因此你应熟悉[如何使用它](https://docs.microsoft.com/zh-cn/visualstudio/ide/visual-studio-ide)来创建应用程序。此外，这不是一门教授如何编写代码或开发应用程序的课程。我们假设你知道如何使用 C# 编写代码（可以在[此处](https://mva.microsoft.com/zh-cn/training-courses/c-fundamentals-for-absolute-beginners-16169?l=Lvld4EQIC_2706218949)学习如何编写代码），但是不知道如何实现高级搜索和 NLP（自然语言处理）解决方案。

第二，你应该拥有一些使用 Microsoft Bot Framework 开发机器人的经验。我们不会花太多时间讨论如何设计它们或对话的工作原理。如果你不熟悉 Bot Framework，则应该在参与研讨会之前参加[此 Microsoft 虚拟学院课程](https://mva.microsoft.com/zh-cn/training-courses/creating-bots-in-the-microsoft-bot-framework-using-c-17590#!)。

第三，你应该拥有使用门户的经验，并且能够在 Azure 上创建资源（和花钱）。我们不会为本研讨会提供 Azure Pass。

>**注意**：本研讨会的开发和测试是使用 Visual Studio Community 版本 15.4.0 在 Data Science Virtual Machine (DSVM) 上进行的

## 简介

我们将构建一个端到端的场景，使你能够引入自己的图片、使用认知服务查找图像中的对象和人物、了解那些人的感情以及将所有数据存储到 NoSQL 存储 (CosmosDB)。我们将使用该 NoSQL 存储来填充 Azure 搜索索引，然后使用 LUIS 生成一个 Bot Framework 机器人来进行简单、有针对性的查询。

> **注意**：本实验是 `lab01.1-pcl_and_cognitive_services` 的延续；我们将跳过以下操作：引入图像，使用认知服务来确定有关图像的信息，以及将数据存储在 DocumentDB 中。该实验中唯一要供我们使用的工具是 DocumentDB，用于填充我们的搜索索引。如果已完成 `lab01.1-pcl_and_cognitive_services`，则可以选择使用 DocumentDB 连接字符串，而不是提供的字符串。

## 体系结构

在 `lab01.1-pcl_and_cognitive_services` 中，我们生成了一个简单的 C# 应用程序，使你能够从本地驱动器中引入图像，然后调用多种不同的认知服务来收集这些图像上的数据：

- [计算机视觉](https://www.microsoft.com/cognitive-services/zh-cn/computer-vision-api)： 用于获取标签和描述
- [人脸](https://www.microsoft.com/cognitive-services/zh-cn/face-api)： 用于从每个图像中抓取人脸及其细节
- [情感](https://www.microsoft.com/cognitive-services/zh-cn/emotion-api)： 用于提取图像中每张人脸的情绪分数

获得这些数据后，我们会对其进行处理并将所需的所有信息存储在 [DocumentDB](https://azure.microsoft.com/zh-cn/services/documentdb/)（我们的 [NoSQL](https://en.wikipedia.org/wiki/NoSQL) [PaaS](https://azure.microsoft.com/zh-cn/overview/what-is-paas/) 产品/服务）中。

现在数据已存储在 DocumentDB 中，我们将在其基础上构建一个 [Azure 搜索](https://azure.microsoft.com/zh-cn/services/search/)索引（Azure 搜索是我们用于分组、容错搜索的 PaaS 产品/服务，请考虑没有管理开销的弹性搜索）。我们将向你演示如何查询数据，然后构建一个 [Bot Framework](https://dev.botframework.com/) 机器人来查询数据。最后，我们将通过 [LUIS](https://www.microsoft.com/cognitive-services/zh-cn/language-understanding-intelligent-service-luis) 扩展这个机器人，以自动从查询中获取意图，并使用这些信息智能地指导搜索。

![体系结构图](./resources/assets/AI_Immersion_Arch.png)

> 这个实验按这个[认知服务教程](https://github.com/noodlefrenzy/CognitiveServicesTutorial) 进行了修改。

## 导航 GitHub ##

[资源](./resources)文件夹中包含几个目录：

- **assets**：这包含实验手册的所有图像。可以忽略此文件夹。
- **code**：其中包含我们将使用的几个目录：
	- **LUIS**：在这里，你将找到 PictureBot 的 LUIS 模型。你将创建自己的模型，但如果未跟上进度或希望测试另外的 LUIS 模型，则可以使用 .json 文件导入此 LUIS 应用。
	- **Models**：在我们将搜索添加到 PictureBot 时会用到这些类。
	- **Finished-PictureBot**：此处有已完成的 PictureBot.sln，用于研讨会后面的部分，到时我们会将 LUIS 和搜索索引集成到 Bot Framework 中。如果你跟不上进度或遇到困难，可以参考此目录中的内容。

> 你需要 Visual Studio 来运行这些实验，但如果你已经为其中一个研讨会部署了 Windows Data Science Virtual Machine，则可以使用此已部署的工具。

## 收集密钥

在本实验过程中，我们将收集各种密钥。建议将所有密钥保存在一个文本文件中，以便在整个研讨会期间能轻松地获取它们。

>_密钥_
>- LUIS API：
>- Cosmos DB 连接字符串：
>- Azure 搜索名称：
>- Azure 搜索密钥：
>- Bot Framework 应用名称：
>- Bot Framework 应用 ID：
>- Bot Framework 应用密码：


## 导航实验

本研讨会分为五个部分：
- [1_AzureSearch](./1_AzureSearch.md)：此处你将了解 Azure 搜索并创建索引
- [2_LUIS](./2_LUIS.md)：我们将构建一个 LUIS 模型来增强机器人（下一个实验中将构建）的语言理解能力
- [3_Bot](./3_Bot.md)：了解如何使用 Bot Framework 将所有内容组合在一起
- [4_Bot_Enhancements](./4_Bot_Enhancements.md)：我们将通过使用正则表达式增强我们的机器人并使用 Bot Connector 进行发布
- [5_Challenge_and_Closing](./4_Challenge_and_Closing.md)：如果你完成了所有实验，请尝试这一挑战。你还可以找到一份摘要，其中包含已完成的工作以及了解详细信息的位置。



### 继续 [1_AzureSearch](./1_AzureSearch.md)


