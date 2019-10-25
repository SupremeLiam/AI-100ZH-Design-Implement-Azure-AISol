---
lab:
    title: '实验 2：实现计算机视觉'
    module: '模块 1:介绍 Azure 认知服务'
---

# 实验 2 - 实现计算机视觉

## 实验 2.0：目标

在本实验中，你将：

- 了解各种认知服务 API
- 了解如何配置应用以调用认知服务
- 构建一个在 .NET 应用程序中调用各种认知服务 API（尤其是计算机视觉）的应用程序

虽然重点是认知服务，但你还将使用 Visual Studio 2017 Community Edition。

> 注意：如果你还没有帐户，请按照指示创建 Azure 帐户和认知服务，并在 [Lab1-Technical_Requirements.md](../Lab1-Technical_Requirements/02-Technical_Requirements.md) 中获取计算机视觉密钥。

## 简介

我们将构建一个端到端的应用程序，它允许你引入自己的图片，使用认知服务获取描述文字和一些关于图像的标记。在后面的实验中，我们将使用 LUIS 构建一个 Bot Framework 机器人，以便轻松且有针对性地查询此类图像。

## 实验 2.1：体系结构

我们将构建一个简单的 C# 应用程序，它允许你从本地驱动器中引入图片，然后调用[计算机视觉 API](https://www.microsoft.com/cognitive-services/zh-cn/computer-vision-api) 以分析图像并获取标记和描述。

在这个实验的后续部分，我们将向你展示如何查询数据，以及构建 [Bot Framework](https://dev.botframework.com/) 机器人以进行查询。最后，我们将通过 [LUIS](https://www.microsoft.com/cognitive-services/zh-cn/language-understanding-intelligent-service-luis) 扩展这个机器人，以自动从查询中获取意图，并使用这些信息智能地指导搜索。

![体系结构图](../images/AI_Immersion_Arch.png)

> 这个实验按这个[认知服务教程](https://github.com/noodlefrenzy/CognitiveServicesTutorial)进行了修改。

## 实验 2.2：资源

[主要](./)课程文件夹中有两个目录：

- **code**：其中包含我们将使用的几个目录：
  - **Starting-ImageProcessing** 和 **Finished-ImageProcessing**：有一个用于启动的文件夹，如果你正在进行实验，则应使用这个文件夹，但如果你遇到困难或时间不足，还有一个已完成的文件夹。每个文件夹都包含一个解决方案 (.sln)，其中具有实验的多个不同项目，让我们总体来看一下：
    - **ProcessingLibrary**：这是一个可移植类库 (PCL)，包含用于访问与视觉相关的各种认知服务的帮助程序类，以及一些用于封装结果的“Insights”类。

- **sample_images**：一些示例图像用于测试认知服务的实现。

### 入门

请按照指示创建 Azure 帐户和认知服务，并在 [Lab1-Technical_Requirements.md](../Lab1-Technical_Requirements/02-Technical_Requirements.md) 中获取计算机视觉密钥。

在进行这个实验的过程中，我们将收集认知服务密钥和存储密钥。你应将所有内容保存在文本文件中，以便在将来的实验中轻松访问它们。

**获取认知服务 API 密钥**

在门户中，我们将首先为要使用的认知服务创建密钥。我们主要使用[计算机视觉](https://www.microsoft.com/cognitive-services/zh-cn/computer-vision-api)认知服务，因此我们首先来创建一个 API 密钥。

在门户中，单击 **“+ 新建”** 按钮（当你将鼠标悬停在它上面时，它会显示 **“创建资源”**），然后在搜索框中输入 **“计算机视觉”** 并选择 **“计算机视觉 API”**：

![创建认知服务密钥](../images/new-cognitive-services.PNG)

这将引导你填写将要创建的 API 终结点的一些详细信息，选择感兴趣的 API 和希望终结点驻留的位置（**!!放在美国西部地区，否则它将无效!!**），以及想要的定价方案。我们将使用 **S1**，这样我们就可以获得这个教程所需的吞吐量。固定到仪表板__，这样你就可以轻松找到它。由于计算机视觉 API 在 Microsoft 内部（以安全的方式）存储图像，为帮助改进未来的认知服务视觉产品/服务，在创建资源之前，将需要选中表明你已经确定的框。

**再次确认你是否将计算机视觉服务放在美国西部**

> 以下实验中的代码已设置为使用美国西部，以便调用计算机视觉 API。在今后，你可以在[此处](https://docs.microsoft.com/zh-cn/azure/cognitive-services/Computer-vision/Vision-API-How-to-Topics/HowToSubscribe)了解如何调用其他区域。


![认知 API 密钥](../images/cognitive-keys.PNG)

>注意：这里有两个密钥用于你将创建的每个认知服务 API。任何一个密钥都有效。可在[此处](https://blogs.msdn.microsoft.com/mast/2013/11/06/why-does-an-azure-storage-account-have-two-access-keys/)阅读关于多个密钥的详细信息。

## 实验 2.3：图像处理

## 认知服务

认知服务可以用于为你的应用、网站和机器人引入算法，通过自然的交流方式来了解、听取、说出、理解和解释你的用户需求。

可用的认知服务有五个主要类别：
- **视觉**：图像处理算法，用于识别、标注和调整图片
- **知识**：映射复杂的信息和数据，以解决智能推荐和语义搜索等任务
- **语言**：允许你的应用使用预构建脚本处理自然语言，评估情绪并学习如何识别用户想要的内容
- **语音**：将音频转换为文本，使用语音进行验证，或将说话人识别添加到应用中
- **搜索**：将必应搜索 API 添加到应用中，并利用该功能将数以亿计的网页、图像、视频和新闻整合到一个 API 调用中

可以浏览[服务目录](https://azure.microsoft.com/zh-cn/services/cognitive-services/directory/)中的所有特定 API。

你可能还记得，我们今天要构建的应用程序将使用[计算机视觉](https://www.microsoft.com/cognitive-services/zh-cn/computer-vision-api)获取标记和描述。

我们来讨论如何在应用程序中调用认知服务。

### **图像处理库** ###

在你的解决方案的 `code/Starting-ImageProcessing` 下，可找到 `Processing Library`。这是一个[可移植类库 (PCL)](https://docs.microsoft.com/zh-cn/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)，有助于快速且轻松地构建跨平台的应用和库。它可用作多种服务的包装器。这个特定的 PCL 包含一些用于访问计算机视觉 API 的帮助程序类（位于 ServiceHelpers 文件夹中）以及一个封装结果的“ImageInsights”类。稍后，我们将创建一个图像处理器类，它将负责包装一个图像，并公开多种充当通往认知服务桥梁的方法和属性。

![处理库 PCL](../images/ProcessingLibrary.png)

创建图像处理器后（在实验 2.4 中），你应该能够选取这个可移植类库并将其放置在涉及认知服务的其他项目中（根据想要使用的认知服务，将需要做出一些修改）。


**ProcessingLibrary：服务帮助程序**

当你开发应用时，可以使用服务帮助程序简化操作。服务帮助程序的其中一个主要作用是能够检测 API 调用何时返回超过调用率的错误，并自动重试该调用（经过一些延迟之后）。它们还可帮助引入方法，处理异常以及处理密钥。

你可在[智能 Kiosk 示例应用程序](https://github.com/Microsoft/Cognitive-Samples-IntelligentKiosk/tree/master/Kiosk/ServiceHelpers)中找到适用于某些其他认知服务的其他服务帮助程序。利用这些资源，可以根据需要轻松添加和删除未来项目中的服务帮助程序。


**ProcessingLibrary：“ImageInsights”类**

看看“ImageInsights”类。你可以看到，我们正在调用图像中的 `Caption` 和 `Tags` 以及唯一的 `ImageId`。“ImageInsights”从计算机视觉 API（或在选择调用多个时，从认知服务）选取我们所需的信息。

现在让我们退回一步。它不像创建“ImageInsights”类以及从服务帮助程序处复制某些方法/错误处理那么简单。我们仍需调用 API 并在某处处理图像。出于这个实验的目的，我们将逐步创建 `ImageProcessor.cs`，但在未来的项目中，可随时将这个类添加到 PCL 并从那里开始操作（根据要调用的认知服务和要处理的图像、文本、语音等内容，将需要对它进行修改）。



## 实验 2.4：创建 `ImageProcessor.cs`

如果还没有，请导航到 `code/Starting-ImageProcessing`，可在其中找到 `Processing Library`。

右键单击解决方案，然后选择“构建解决方案”。如果有与 `ImageProcessor.cs` 相关的错误，你现在可以忽略它们，因为我们即将解决它们。

导航到 `ProcessingLibrary` 中的 `ImageProcessor.cs`。

**步骤 1**：将以下 [`using` 指令](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/using-directive) 添加 **到该类顶部**，位于命名空间上方：

```csharp
using System;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ProjectOxford.Vision;
using ServiceHelpers;
```

[Project Oxford](https://blogs.technet.microsoft.com/machinelearning/tag/project-oxford/) 是在其中开始进行许多认知服务的项目。如你所见，NuGet 包甚至在标记在 Project Oxford 下。在这种情况下，我们将为计算机视觉 API调用 `Microsoft.ProjectOxford.Vision`。此外，我们将引用服务帮助程序（请记住，这些将简化我们的操作）。你需要根据要在应用程序中利用的认知服务来引用不同的包。

**步骤 2**：在 `ImageProcessor.cs` 中，我们将首先创建一种用于处理图像的方法，即 `ProcessImageAsync`。将以下代码粘贴到 `ImageProcessor` 类中（在 `{ }` 之间）：

```csharp
public static async Task<ImageInsights> ProcessImageAsync(Func<Task<Stream>> imageStreamCallback, string imageId)
        {


```

> 提示：由于左大括号，Visual Studio 将引发错误。将所需的内容添加到方法末尾。如果需要帮助，请向领座询问。

在上述代码中，我们使用 `Func<Task<Stream>>`，因为我们要确保可以多次处理图像（为需要它的每项服务都处理一次），因此我们具有一个函数，可让我们回到获取流。由于获取流通常是异步操作，而不是函数返回流本身的操作，因它会返回一个允许我们以异步形式执行这个操作的任务。

**步骤 3**：在 `ImageProcessor.cs` 的 `ProcessImageAsync` 方法中，我们将设置一个将通过处理器填充的[静态数组](https://stackoverflow.com/questions/4594850/definition-of-static-arrays)。如你所见，这些是我们想要为 `ImageInsights.cs` 调用的主要属性。在 `ProcessImageAsync` 的 `{ }` 之间添加以下代码：

```csharp
VisualFeature[] DefaultVisualFeaturesList = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };
```

**步骤 4**：接下来，我们要调用认知服务（尤其是计算机视觉），并将结果放在 `imageAnalysisResult` 中。使用下面的代码调用计算机视觉 API（在 `VisionServiceHelper.cs` 的帮助下），并将结果存储在 `imageAnalysisResult` 中。在 `VisionServiceHelper.cs` 的底部附近，你希望查看用于调用（`RunTaskWithAutoRetryOnQuotaLimitExceededError`、`DescribeAsync`、`AnalyzeImageAsync`、`RecognizeTextAsyncYou`）的可用方法。你将使用 AnalyzeImageAsync 方法，以返回可视特征。

```csharp
var imageAnalysisResult = await VisionServiceHelper.AnalyzeImageAsync(imageStreamCallback, DefaultVisualFeaturesList);
```

**步骤 5**：调用计算机视觉服务之后，我们想要在仅包含以下结果的“ImageInsights”中创建一个条目：ImageId、Caption 和 Tags（可通过重新访问 `ImageInsights.cs` 进行确认）。将以下代码粘贴在 `var imageAnalysisResult` 下，并填写 `ImageId`、`Caption` 和 `Tags` 的代码：


```csharp
            ImageInsights result = new ImageInsights
            {
                ImageId = imageId,
                Caption = imageAnalysisResult.Description.Captions[0].Text,
                Tags = imageAnalysisResult.Tags.Select(t => t.Name).ToArray()
            };
```


所以现在我们有了计算机视觉 API 所需的描述文字和标记，并且每个图像的结果（具有 imageId）都存储在“ImageInsights”中。

**步骤 6**：最后，我们需要通过在方法末尾添加以下行来关闭该方法：

```csharp
return result;
```

生成 `ImageProcessor.cs` 之后，不要忘记保存它！

想要确保已正确设置 `ImageProcessor.cs` 吗？可在[此处](./code/Finished-ImageProcessing/ProcessingLibrary/ImageProcessor.cs)找到完整课程。

## 2.5_TestCLI

## 浏览 Cosmos DB

Azure Cosmos DB 是 Microsoft 的可复原 NoSQL PaaS 解决方案，对于存储结构松散的数据（如我们具有的包含图像元数据结果的数据）非常有用。还有其他选择（Azure 表存储、SQL Server），但 Cosmos DB 使我们可以灵活地自由发展架构（比如为新服务添加数据）、轻松查询，并且可以快速集成到 Azure 搜索（我们将在之后的实验中进行这个操作）。

### 实验 2.6（可选）：了解 CosmosDBHelper
Cosmos DB 不是这个实验的重点，但如果你对将要进行的操作感兴趣，这里有一些我们将使用的代码的重点：

- 导航到 `ImageStorageLibrary` 中的 `CosmosDBHelper.cs` 类。查看代码和注释。使用的许多实现都可在[入门指南](https://docs.microsoft.com/zh-cn/azure/cosmos-db/documentdb-get-started)中找到。
- 转到 `TestCLI` 的 `Util.cs` 并查看 `ImageMetadata` 类（代码和注释）。在这里，我们会将从认知服务中检索的 `ImageInsights` 转换为要存储到 Cosmos DB 的相应元数据。
- 最后，查看 `TestCLI` 和 `ProcessDirectoryAsync` 中的 `Program.cs`。首先，我们要检查是否已上传图像和元数据 - 可以使用 `CosmosDBHelper` 按 ID 查找文档，如果文档不存在，则返回 `null`。接下来，如果我们设置了 `forceUpdate` 或之前未处理图像，则将使用 `ProcessingLibrary` 中的 `ImageProcessor` 调用认知服务，并检索添加到当前 `ImageMetadata` 的 `ImageInsights`。  

完成所有这些操作后，我们首先可以使用 `BlobStorageHelper` 实例将实际图像存储到 Blob 存储中，然后使用 `CosmosDBHelper` 实例将 `ImageMetadata` 存储到 Cosmos DB 中。如果文档已经存在（基于我们上一次检查），则应更新现有文档。如果不存在，我们应该创建一个新文档。

### 实验 2.7：使用 TestCLI 加载图像

我们将实现主要的处理和存储代码作为命令行/控制台应用程序，因为这使你可以专注于处理代码，而无需担心事件循环、窗体或任何其他用户体验相关干扰。之后可随意添加自己的用户体验。

设置认知服务 API 密钥、Azure Blob 存储连接字符串以及 Cosmos DB 终结点 URI 和 TestCLI__ 的 `settings.json` 中的密钥后，即可运行 TestCLI__。

运行 TestCLI__，然后打开命令提示符并导航到这个实验的应用程序的“调试”文件夹。然后输入 `TestCLI.exe`。应得到以下结果：

```
    > TestCLI.exe

    Usage:  [options]

    Options:
    -force            Use to force update even if file has already been added.
    -settings         The settings file (optional, will use embedded resource settings.json if not set)
    -process          The directory to process
    -query            The query to run
    -?  | -h | --help  Show help information
```

默认情况下，它会从 `settings.json` 中加载你的设置（它会将其构建到 `.exe`），但你可以使用 `-settings` 标记提供你自己的设置。要将图像（及其来自认知服务的元数据）加载到你的云存储中，你只需要求 TestCLI__ `-process` 图像目录，如下所示：

>将 <%GitHubDir%> 值替换为在其中克隆存储库的文件夹。

```
    > TestCLI.exe -process <%GitHubDir%>\AI-100-Design-Implement-Azure-AISol\Lab2-Implement_Computer_Vision\sample_images
```

完成处理后，可直接使用 TestCLI__ 查询 Cosmos DB，如下所示：

```
    > TestCLI.exe -query "select * from images"
```

花一些时间查看示例图像（可在 /sample_images 中找到它们），并将图像与应用程序中的结果进行比较。
