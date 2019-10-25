---
lab:
    title: '实验 1：满足技术要求'
    module: '模块 1:介绍 Azure 认知服务'
---

# 实验 1：满足技术要求

## 实验 1.1：设置技术、环境和密钥

这个实验适用于使用 Azure 的人工智能 (AI) 工程师或 AI 开发人员。为确保你有时间完成练习，在开始这个课程的实验之前，需要满足一些要求。

理想情况下，你之前应接触过 Visual Studio。我们将把它用于在实验中构建的所有内容，因此你应熟悉[如何使用它](https://docs.microsoft.com/zh-cn/visualstudio/ide/visual-studio-ide)创建应用程序。此外，这不是我们教授代码或开发的课程。我们假设你对 C# 有一定的了解（中级 - 你可以在[此处](https://mva.microsoft.com/zh-cn/training-courses/c-fundamentals-for-absolute-beginners-16169?l=Lvld4EQIC_2706218949)和[此处](https://docs.microsoft.com/zh-cn/dotnet/csharp/quick-starts/)进行学习），但你不知道如何使用认知服务实现解决方案。

### 帐户设置

> 注意：你可以使用不同的环境来完成这个实验。  讲师将指导你完成必要的步骤，以启动和运行环境。   这可能像使用你在教室中登录的计算机一样简单，也可能像设置虚拟化环境一样复杂。  实验是使用 Azure 上的 Azure Data Science Virtual Machine (DSVM) 创建和测试的，因此需要使用 Azure 帐户。

#### 设置 Azure 帐户：

你可以在以下位置激活 Azure 免费试用版：[https://azure.microsoft.com/zh-cn/free/](https://azure.microsoft.com/zh-cn/free/)。

如果你已获得用于完成这个实验的 Azure Pass，则可访问 [http://www.microsoftazurepass.com/](http://www.microsoftazurepass.com/) 激活它。  请按照 [https://www.microsoftazurepass.com/howto](https://www.microsoftazurepass.com/howto) 上的说明进行操作，其中记录了激活过程。  Microsoft 帐户可能具有 Azure **的一个免费试用版** 以及与之关联的 Azure Pass，因此如果你已在 Microsoft 帐户上激活 Azure Pass，则需要使用免费试用版或使用其他 Microsoft 帐户。

### 环境设置

这些实验旨在与使用 [Visual Studio 2017 Community Community Edition](https://www.visualstudio.com/downloads/) 的 .NET Framework 结合使用。  最初的研讨会旨在通过 Azure Data Science Virtual Machine (DSVM) 进行使用和测试。  实际上，只有高级 Azure 订阅才能在 Azure 上创建 DSVM 资源，但可以使用运行 Visual Studio 2017 Community Edition 的本地计算机以及整个实验步骤中列出的所需软件下载来完成实验。

### 密钥设置

在完成这实验的过程中，我们将收集各种认知服务密钥和存储密钥。你应将所有内容保存在文本文件中，以便在将来的实验中轻松访问它们。

>_密钥_
>
>  - 认知服务培训密钥：
>- 认知服务 API 密钥：
>  -  LUIS API 终结点、密钥、应用 ID：
>  -  机器人应用名称：
>  -  机器人应用 ID：
>  -  机器人应用密码：


#### 获取培训密钥：

通过培训 API 密钥，可以采用编程方式创建、管理和培训自定义视觉项目。可在支持培训密钥的 AI 服务上找到它们。  或者，可以使用为你创建的每项服务生成的密钥。   实验将讨论相应服务的密钥位置。

> 注意：不支持 Internet Explorer。我们建议使用 Edge、Firefox 或 Chrome。

#### 获取认知服务 API 密钥：

我们主要使用[计算机视觉](https://www.microsoft.com/cognitive-services/zh-cn/computer-vision-api)认知服务，因此我们要为那个服务创建一个 API 密钥。

在门户中，单击 **“+ 新建”** 按钮（当你将鼠标悬停在它上面时，它会显示 **“创建资源”**），然后在搜索框中输入 **“计算机视觉”** 并选择 **“计算机视觉 API”**：

![创建认知服务密钥](../../Linked_Image_Files/new-cognitive-services.PNG)

这将引导你填写将要创建的 API 终结点的一些详细信息，选择感兴趣的 API 和希望终结点驻留的位置（**!!放在美国西部地区，否则它将无效!!**），以及想要的定价方案。我们将使用 **S1**，这样我们就可以获得这个教程所需的吞吐量。固定到仪表板__，这样你就可以轻松找到它。由于计算机视觉 API 在 Microsoft 内部（以安全的方式）存储图像，为帮助改进未来的认知服务视觉产品/服务，在创建资源之前，将需要选中表明你已经确定的框。

**再次确认你是否将计算机视觉服务放在美国西部**

> 以下实验中的代码已设置为使用美国西部，以便调用计算机视觉 API。在今后，你可以在[此处](https://docs.microsoft.com/zh-cn/azure/cognitive-services/Computer-vision/Vision-API-How-to-Topics/HowToSubscribe)了解如何调用其他区域。


### Bot Builder SDK

我们将使用适用于 C# 的 Bot Builder 模板，以在这个课程中创建机器人。

#### 下载 Bot Builder SDK

[在此处下载适用于 C# 的 Bot Builder SDK v4 模板](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4)并单击“另存为”，将其保存到 Visual C# 的 Visual Studio ItemTemplates 文件夹。这通常位于 `C:\Users\`_your-username_`\Documents\Visual Studio 2017\Templates\ItemTemplates\Visual C#`。导航到文件夹位置并双击安装，然后单击“安装”以将模板添加到你的 Visual Studio 模板。根据你的浏览器，下载模板时，可以双击它，并将其直接安装到 Visual Studio Community 2017；这样就完成了。

### 机器人仿真器

我们将使用最新的 .NET SDK (v4) 开发机器人。  首先，我们需要下载 Bot Framework Emulator，并创建一个 Web App Bot 以及获取源代码。

##### 下载 Bot Framework Emulator

可以下载 v4 预览版 Bot Framework Emulator，以在本地测试机器人。其余实验的说明将假设你已下载 v4 Emulator（而不是 v3 Emulator）。通过转到[此页面](https://github.com/Microsoft/BotFramework-Emulator/releases)并下载具有“4.1.0”标记的最新版本的模拟器来下载模拟器（如果你使用的是 Windows，请选择“.exe”文件）。

模拟器安装到 `c:\Users\`_your-username_`\AppData\Local\botframework\app-`_version_`\botframework-emulator.exe` 或 Downloads 文件夹，具体取决于浏览器。

## 实验 1.2：体系结构概述

我们将构建一个简单的 C# 应用程序，它允许你从本地驱动器中引入图片，然后调用[计算机视觉 API](https://www.microsoft.com/cognitive-services/zh-cn/computer-vision-api) 以分析图像并获取标记和描述。

在这个实验的后续部分，我们将向你展示如何构建一个 [Bot Framework](https://dev.botframework.com/) 机器人，以与客户的文本查询进行交互。然后，我们将展示一个快速解决方案，用于将现有的知识库和常见问题解答集成到带有 [QnA Maker](https://docs.microsoft.com/zh-cn/azure/cognitive-services/qnamaker/overview/overview) 的机器人框架中。最后，我们将通过 [LUIS](https://www.microsoft.com/cognitive-services/zh-cn/language-understanding-intelligent-service-luis) 扩展这个机器人，以自动从查询中获取意图，并使用这些信息智能地响应客户的文本请求。

我们将仅提供用于使用必应搜索的上下文，以使客户能够在与机器人交互期间访问其他数据，但在实验期间不会实现这些方案。参与者会受邀阅读关于[必应 Web 搜索](https://azure.microsoft.com/zh-cn/services/cognitive-services/directory/search/)服务的更多信息。

虽然超出了这个实验的范围，但这个体系结构会集成 Azure 的数据解决方案，通过 [Blob 存储]((https://docs.microsoft.com/zh-cn/azure/storage/storage-dotnet-how-to-use-blobs)和 [Cosmos DB](https://azure.microsoft.com/zh-cn/services/cosmos-db/) 管理这个体系结构中图像和元数据的存储。

![体系结构图](../../Linked_Image_Files/AI_Immersion_Arch.png)

## 实验 1.3：未来项目/学习的资源

为了加深你对此处描述的体系结构的理解，并让团队的更多成员参与 AI 解决方案的开发，我们建议你查看以下资源：

- [认知服务](https://www.microsoft.com/cognitive-services) - AI 工程师
- [Cosmos DB](https://docs.microsoft.com/zh-cn/azure/cosmos-db/) - 数据工程师
- [Azure 搜索](https://azure.microsoft.com/zh-cn/services/search/) - 搜索工程师
- [Bot 开发人员门户](http://dev.botframework.com) - AI 工程师


## 点数

这个系列的实验改编自[认知服务教程](https://github.com/noodlefrenzy/CognitiveServicesTutorial)和[学习 AI 集训营](https://github.com/Azure/LearnAI-Bootcamp)
