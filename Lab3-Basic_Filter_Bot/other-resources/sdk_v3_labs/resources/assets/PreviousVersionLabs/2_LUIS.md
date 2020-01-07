## 2_Luis：
预计用时：20-30 分钟

## LUIS

首先，让我们[了解 Microsoft 的语言理解智能服务 (LUIS)](https://docs.microsoft.com/zh-cn/azure/cognitive-services/LUIS/Home)。

我们现已了解 LUIS 的含义，接着就要规划 LUIS 应用。我们将创建一个基于搜索返回图像的机器人，然后我们可以共享或订购这些图像。我们需要创建可触发机器人能够执行的不同操作的意图，然后创建实体来对执行该操作所需的一些参数建模。例如，PictureBot 的一个意图可能是“SearchPics”，该意图触发搜索服务来查找照片，这需要一个“facet”实体才能了解要搜索的内容。可在[此处](https://docs.microsoft.com/zh-cn/azure/cognitive-services/LUIS/plan-your-app) 查看更多规划应用的示例。

构思好应用后，我们就可以[构建并对其进行训练](https://docs.microsoft.com/zh-cn/azure/cognitive-services/LUIS/luis-get-started-create-app)。以下是创建 LUIS 应用程序时通常要采取的步骤：
  1. [添加意图](https://docs.microsoft.com/zh-cn/azure/cognitive-services/LUIS/add-intents) 
  2. [添加言语](https://docs.microsoft.com/zh-cn/azure/cognitive-services/LUIS/add-example-utterances)
  3. [添加实体](https://docs.microsoft.com/zh-cn/azure/cognitive-services/LUIS/add-entities)
  4. [使用功能改进性能](https://docs.microsoft.com/zh-cn/azure/cognitive-services/LUIS/add-features)
  5. [训练和测试](https://docs.microsoft.com/zh-cn/azure/cognitive-services/LUIS/train-test)
  6. [使用主动学习](https://docs.microsoft.com/zh-cn/azure/cognitive-services/LUIS/label-suggested-utterances)
  7. [发布](https://docs.microsoft.com/zh-cn/azure/cognitive-services/LUIS/publishapp)


### 实验：在门户中创建 LUIS 服务

在“门户”中，点击 **“创建资源”**，然后在搜索框中输入 **“LUIS”** 并选择 **“语言理解智能服务”**：

这将引导你填写将要创建的 API 终结点的一些详细信息，选择感兴趣的 API 和希望终结点驻留的位置，以及想要的定价方案。免费层足以满足本实验的需求。由于 LUIS 在 Microsoft 内部（以一种安全的方式）存储图像，为帮助改进未来的认知服务视觉产品/服务，将需要选中该框来确认你对此没有问题。

创建新的 API 订阅后，可以从边栏选项卡的相应部分获取密钥并将其添加到密钥列表中。

![认知 API 密钥](./resources/assets/cognitive-keys.PNG)

### 实验：使用 LUIS 为应用程序添加智能功能

在下一个实验中，我们将创建 PictureBot。首先，我们来了解一下如何使用 LUIS 添加一些自然语言功能。使用 LUIS，可以将自然语言（用户在与机器人交谈时可能会用到的单词/短语/句子）映射到意图（用户想要执行的任务或操作）中。  就我们的应用程序而言，可能有几个意图，例如：查找图片、共享图片和订购图片打印件。  我们可以举几个示例言语作为询问这些内容的方法，并且 LUIS 将根据其学到的内容将其他新的言语映射到每个意图。  

导航到 [https://www.luis.ai](https://www.luis.ai) 并使用 Microsoft 帐户登录。  （该帐户应与上一节中用于创建 LUIS 密钥的帐户相同）。  你应该重定向到 [https://www.luis.ai/applications](https://www.luis.ai/applications) 上 LUIS 应用程序的列表。  我们将创建一个新 LUIS 应用来支持机器人。  

> 有趣的是：请注意，[当前页面](https://www.luis.ai/applications) 上的“新应用”按钮旁边也有一个“导入应用”。  创建 LUIS 应用程序后，可以将整个应用导出为 JSON 并将其签入源代码管理。  这是建议的最佳做法，因此你可以在编写代码时对 LUIS 模型进行版本控制。  可以使用“导入应用”按钮重新导入导出的 LUIS 应用。  如果跟不上实验的进度并想走捷径，则可以单击“导入应用”按钮并导入 [LUIS 模型](./resources/code/LUIS/PictureBotLuisModel.json)。  

在 [https://www.luis.ai/applications](https://www.luis.ai/applications) 中单击“新建应用”按钮。  为其命名（我选择“PictureBotLuisModel”）并将区域性设置为“英语”。  你可以选择提供说明。然后单击“创建”。  

![LUIS 新应用](./resources/assets/LuisNewApp.png) 

你将进入新应用的“仪表板”。  应用 ID 会显示；请记录下来，之后将作为 **LUIS 应用 ID**。  然后单击“创建意图”。  

![LUIS 仪表板](./resources/assets/LuisDashboard.jpg) 

我们希望机器人能够执行以下操作：
+ 搜索/查找图片
+ 在社交媒体上分享图片
+ 订购图片打印件
+ 问候用户（不过这也可以通过其他方式完成，我们稍后会了解这一点）

让我们为请求实现其中每一项的用户创建意图。  单击“添加意图”按钮。  

请将第一个意图命名为“问候语”，然后单击“保存”。  然后，提供几个用户在问候机器人时可能回应的言语示例，并在提供每个示例后按 Enter。  输入一些言语后，单击“保存”。  

![LUIS 问候语意图](./resources/assets/LuisGreetingIntent.jpg) 

我们来了解一下如何创建实体。  用户请求搜索图片时，他们可以指定要寻找的内容。  让我们在实体中捕获该内容。  

单击左侧列中的“实体”，然后单击“添加自定义实体”。  请将实体命名为“facet”并将实体类型命名为“简单”。  然后单击“保存”。  

![添加 Facet 实体](./resources/assets/AddFacetEntity.jpg) 

接下来，单击左侧边栏中的“意图”，然后单击黄色的“添加意图”按钮。  请将意图命名为“SearchPics”，然后单击“保存”。  

与针对问候语执行的操作一样，让我们添加一些示例言语（用户在与机器人交谈时可能会用到的单词/短语/句子）。  人们可能会通过多种方式搜索图片。  请随意使用下面的一些言语，并添加自己要求机器人搜索图片会使用的措辞。 

+ 查找户外照片
+ 是否有火车的图片？
+ 查找食物的图片。
+ 搜索一名 6 个月大的男孩的照片
+ 请提供 20 岁女性的图片
+ 请显示海滩的图片
+ 我想查找狗的照片
+ 搜索室内女性的照片
+ 显示看起来很开心的女孩的照片
+ 我想查找伤心女孩的照片
+ 请提供快乐的婴儿的照片

使用一些言语后，我们必须让 LUIS 了解如何挑选 **搜索主题作** 为“facet”实体。“facet”实体选择的所有内容都将被搜索。请将鼠标悬停在词上方并单击该词（或拖动选择一组字词），然后选择“facet”实体。 

![标注实体](./resources/assets/LabellingEntity.jpg) 

以下列出的言语...

![添加 Facet 实体](./resources/assets/SearchPicsIntentBefore.jpg) 

…在标记为 facet 时，可能会变成以下内容。  

![添加 Facet 实体](./resources/assets/SearchPicsIntentAfter.jpg) 

完成后，请不要忘记单击“保存”！  

最后，单击左侧边栏中的“意图”，并添加另外两个意图：
+ 将其中一个意图命名为 **“SharePic”**。  这可以通过诸如“分享这张照片”、“你能为此发推文吗？”或“发布到 Twitter”之类的言语来识别。  
+ 创建另一个名为 **“OrderPic”** 的意图。  这可以与“打印这张照片”、“我想订购打印件”，“我可以获得大小为 8x10 的照片吗？”以及“订购钱包”等言语进行沟通。  
选择言语时，使用问题、命令和“我想...”格式的组合会很有帮助。  

另请注意，有一种意图名为“None”。  不映射到任何意图的随机言语可能会映射到“None”。  

接下来将训练模型。  单击左侧边栏中的“训练和测试”。  然后单击训练按钮。  这会构建用于执行言语的模型 --> 使用提供的训练数据映射意图。训练并不总是立竿见影。有时还会排队等待，并且可能需要几分钟时间。

然后单击左侧边栏中的“发布应用”。  发布应用时有几个选项，包括启用[详细终结点响应或必应拼写检查器](https://docs.microsoft.com/zh-cn/azure/cognitive-services/LUIS/PublishApp)。如果尚未执行此操作，请选择先前设置的终结点密钥，或者单击链接从 Azure 帐户添加密钥。  可以将终结点槽保留为“生产”。  然后单击“发布”。  



![发布 LUIS 应用](./resources/assets/PublishLuisApp.png) 

发布会创建终结点，以调用 LUIS 模型。  随即显示 URL，我们将在后面的实验中说明。

单击左侧边栏中的“训练和测试”。  选中“启用已发布模型”框以使调用通过已发布的终结点完成，而不是直接调用模型。尝试输入一些言语，并查看返回的意图。  
>遗憾的是，“启用已发布的模型”中存在一个 bug，并且只适用于 Chrome。可以下载 Chrome 并尝试此操作，也可以跳过它，不过请记住这未启用。

![测试 LUIS](./resources/assets/TestLuis.png) 

你也可以[在浏览器中测试发布的终结点](https://docs.microsoft.com/zh-cn/azure/cognitive-services/LUIS/PublishApp#test-your-published-endpoint-in-a-browser)。复制 URL，然后将 `{YOUR-KEY-HERE}` 替换为要使用的资源的密钥字符串列中列出的其中一个密钥。若要在浏览器中打开此 URL，请将 URL 参数 `&q` 设置为测试查询。例如，请将 `&q=Find pictures of dogs` 追加到 URL，然后按 Enter。浏览器显示 HTTP 终结点的 JSON 响应。

**提早完成？请尝试以下额外额度的任务：**


创建可由“SearchPics”意图利用的其他实体。例如，我们知道应用可确定年龄，请尝试为年龄创建预生成实体。 

使用实体类型为“列表”的自定义实体进行探索以捕获情感和性别。请参阅下面的情感示例。 

![带列表的自定义情感实体](./resources/assets/CustomEmotionEntityWithList.jpg) 

> **注意：** 添加更多实体或功能时，请不要忘记转到 **“意图”>“言语”**，并使用添加的实体来确认/添加更多的实体。此外，还需要重新训练和发布模型。


### 继续 [3_Bot](./3_Bot.md)

返回 [README](./0_README.md)