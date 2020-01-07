---
lab:
    title: '实验 1：满足技术要求'
    module: '模块 1:介绍 Azure 认知服务'
---

# 实验 1：满足技术要求

## 实验 1.1：设置技术、环境和密钥

这个实验适用于使用 Azure 的人工智能 (AI) 工程师或 AI 开发人员。为确保你有时间完成练习，在开始这个课程的实验之前，需要满足一些要求。

理想情况下，你之前应接触过 Visual Studio。我们将把它用于在实验中构建的所有内容，因此你应熟悉[如何使用它](https://docs.microsoft.com/zh-cn/visualstudio/ide/visual-studio-ide) 创建应用程序。此外，这不是我们教授代码或开发的课程。我们假设你对 C# 有一定的了解（中级 - 你可以在[此处](https://mva.microsoft.com/zh-cn/training-courses/c-fundamentals-for-absolute-beginners-16169?l=Lvld4EQIC_2706218949) 和[此处](https://docs.microsoft.com/zh-cn/dotnet/csharp/quick-starts/)进行学习），但你不知道如何使用认知服务实现解决方案。

### 帐户设置

> **注意** 你可以使用不同的环境来完成这个实验。  讲师将指导你完成必要的步骤，以启动和运行环境。   这可能像使用你在教室中登录的计算机一样简单，也可能像设置虚拟化环境一样复杂。  实验是使用 Azure 上的 Azure Data Science Virtual Machine (DSVM) 创建和测试的，因此需要使用 Azure 帐户。

#### 设置 Azure 帐户：

你可以在以下位置激活 Azure 免费试用版：[https://azure.microsoft.com/zh-cn/free/](https://azure.microsoft.com/zh-cn/free/)。

如果你已获得用于完成这个实验的 Azure Pass，则可访问 [http://www.microsoftazurepass.com/](http://www.microsoftazurepass.com/) 激活它。  请按照 [https://www.microsoftazurepass.com/howto](https://www.microsoftazurepass.com/howto) 上的说明进行操作，其中记录了激活过程。 Microsoft 帐户可能具有 Azure 的 **一个免费试用版** 以及与之关联的 Azure Pass，因此如果你已在 Microsoft 帐户上激活 Azure Pass，则需要使用免费试用版或使用其他 Microsoft 帐户。

### 环境设置

这些实验旨在与使用 [Visual Studio 2019 Community Community Edition](https://www.visualstudio.com/downloads/) 的 .NET Framework 结合使用。  最初的研讨会旨在通过 Azure Data Science Virtual Machine (DSVM) 进行使用和测试。  实际上，只有高级 Azure 订阅才能在 Azure 上创建 DSVM 资源，但可以使用运行 Visual Studio 2019 Community Edition 的本地计算机以及整个实验步骤中列出的所需软件下载来完成实验。

### 需要的 URL 和密钥

在完成这实验的过程中，我们将收集各种认知服务密钥和存储密钥。你应将所有内容保存在文本文件中，以便在将来的实验中轻松访问它们。

>_密钥_
>
>- 认知服务 API URL：
>- 认知服务 API 密钥：
>- LUIS API 终结点：
>- LUIS API 密钥：
>- LUIS API 应用 ID：
>- 机器人应用名称：
>- 机器人应用 ID：
>- 机器人应用密码：
>- Azure 存储连接字符串：
>- Cosmos DB URL：
>- Cosmos DB 密钥：
>- DirectLine 密钥：

> **注**：在本实验中，不会填充其中部分内容。

### Azure 设置

以下步骤将为随后的实验配置 Azure 环境。

####    认知服务

第一个实验专注于[计算机视觉](https://www.microsoft.com/cognitive-services/zh-cn/computer-vision-api)认知服务。Microsoft Azure 允许创建一个涵盖所有服务的认知服务帐户，但你也可以选择为单个服务创建认知服务帐户。  以下步骤将创建一个涵盖所有认知服务的帐户。

1.  打开 [Azure 门户](https://portal.azure.com)

1.  单击 **“+ 创建资源”** 然后在搜索框中输入**“认知服务”** 

1.  从可用选项中选择**“认知服务”**，然后单击**“创建”**

> **注**：可以创建特定的认知服务资源，也可以创建包含所有终结点的单个资源。

1.  键入自己选择的名称

1.  选择订阅和资源组

1.  选择 **S0** 定价层

1.  勾选确认复选框

1.  单击**“创建”**

1.  导航到新资源，单击**“快速入门”**

1.  将 **URL** 和 **终结点**复制到记事本

####    Azure 存储帐户

1.  在门户中，单击**“+ 创建资源”**，然后在搜索框中输入**“存储”** 

1.  在可用选项中选择**“存储帐户”**，然后单击**“创建”**

1.  选择订阅和资源组

1.  键入存储帐户的唯一名称。

1.  对于“位置”，选择与资源组一致的位置

1.  性能应该为**“标准”**

1.  帐户类型应为**“StorageV2 (常规用途 v2)”**

1.  对于复制，选择**“本地冗余存储(LRS)”**

1.  单击**“查看 + 创建”**

1.  单击**“创建”**

1.  导航到新资源，单击**“访问密钥”**

1.  **将连接字符串**复制到记事本

1.  选择**“概述”**，然后单击**“容器”**

1.  选择**“+ 容器”**

1.  在名称处输入**“images”**

1.  选择**确定**

####    Cosmos DB

1.  打开 [Azure 门户](https://portal.azure.com)

1.  单击**“+ 创建资源”**，然后在搜索框中输入**“Cosmos”** 

1.  从可用选项中选择**“Azure Cosmos DB”** 

1.  单击**“创建”**

1.  选择订阅和资源组

1.  输入唯一帐户名称，选择与资源组匹配的位置

1.  单击**“查看 + 创建”**

1.  单击**“创建”**

1.  导航到新资源，在**“设置”**下选择**“密钥”**

1.  将 **URL** 和 **主密钥**复制到记事本

### Bot Builder SDK

我们将使用适用于 C# 的 Bot Builder 模板，以在这个课程中创建机器人。

#### 下载 Bot Builder SDK

1.  在浏览器中打开[这里的 C# Bot Builder SDK v4 模板](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4) 

1.  单击**“下载”**

1.  导航到下载文件夹位置，然后双击安装

1.  请务必选择 Visual Studio 的所有版本，然后单击**“安装”**。  如果出现提示，请单击**“结束任务”**。  

1.  单击**“关闭”**。现在，机器人模板应该已添加到 Visual Studio 模板。 

### 机器人仿真器

我们将使用最新的 .NET SDK (v4) 开发机器人。  为了进行本地测试，我们需要下载 Bot Framework Emulator。

### 下载 Bot Framework Emulator

可以下载 v4 Bot Framework Emulator，以在本地测试机器人。其余实验的说明将假设你已下载 v4 Emulator。 

1.  转到[此页面](https://github.com/Microsoft/BotFramework-Emulator/releases) 并下载带有“4.5.1”或更高版本标记的最新版模拟器（如果使用的是 Windows，请选择“*-windows-setup.exe”文件）。

> **注：** 模拟器的安装位置为 
`"C:\Users\_your-username\AppData\Local\Programs\@bfemulatormain\Bot Framework Emulator.exe"`，但也可以在开始菜单中搜索 **“bot framework”** 以访问它。

## 学分

这个系列的实验改编自[认知服务教程](https://github.com/noodlefrenzy/CognitiveServicesTutorial)和[学习 AI 集训营](https://github.com/Azure/LearnAI-Bootcamp)

##  资源

为了加深你对此处描述的体系结构的理解，并让团队的更多成员参与 AI 解决方案的开发，我们建议你查看以下资源：

- [认知服务](https://www.microsoft.com/cognitive-services) - AI 工程师
- [Cosmos DB](https://docs.microsoft.com/zh-cn/azure/cosmos-db/) - 数据工程师
- [Azure 搜索](https://azure.microsoft.com/zh-cn/services/search/) - 搜索工程师
- [Bot 开发人员门户](http://dev.botframework.com) - AI 工程师

## 后续步骤

-   [实验 02-01：实现计算机视觉](../Lab2-Implement_Computer_Vision/01-Introduction.md)
