## 3_LUIS：
预计用时：15-20 分钟

### 实验 3.1：更新机器人以使用 LUIS

我们需要更新我们的机器人才能使用 LUIS。  可以通过使用 [LuisDialog 类](https://docs.botframework.com/zh-cn/csharp/builder/sdkreference/d8/df9/class_microsoft_1_1_bot_1_1_builder_1_1_dialogs_1_1_luis_dialog.html)来进行更新。  

在 **RootDialog.cs** 文件中，添加对以下命名空间的引用：

```csharp

using Microsoft.Bot.Builder.Luis;
using Microsoft.Bot.Builder.Luis.Models;

```

接下来，使用 LUIS 应用 ID 和 LUIS 密钥为类提供一个 LuisModel 属性。  如果找不到这些值，请返回 http://luis.ai。  单击你的应用程序，然后转到“发布应用”页。可从终结点 URL 获取 LUIS 应用 ID 和 LUIS 密钥（提示：LUIS 应用 ID 中将包含连字符，而 LUIS 密钥则不会。需要使用 LUIS 应用 ID 和 LUIS 密钥将以下代码行直接放在 `[Serializable]` 上方：

```csharp

namespace PictureBot.Dialogs
{
    [LuisModel("YOUR-APP-ID", "YOUR-SUBSCRIPTION-KEY")]
    [Serializable]

...
```

> 有趣的是：可以使用 [Autofac](https://autofac.org/) 在类上动态加载 LuisModel 属性，而不是对其进行硬编码，以便可以将其正确存储在配置文件中。  [AlarmBot 示例](https://github.com/Microsoft/BotBuilder/blob/master/CSharp/Samples/AlarmBot/Models/AlarmModule.cs#L24)中有相关示例。  


让我们从添加“问候语”意图开始（在已经放入 DispatchDialog 中的所有代码下）：

```csharp
        [LuisIntent("Greeting")]
        [ScorableGroup(1)]
        public async Task Greeting(IDialogContext context, LuisResult result)
        {
            // 重复逻辑，用于针对 Scorable 进行的教学。  
            await context.PostAsync("Hello from LUIS!  I am a Photo Organization Bot.  I can search your photos, share your photos on Twitter, and order prints of your photos.  You can ask me things like 'find pictures of food'.");
        }
```

按 F5 以运行应用。在机器人仿真器中，尝试以不同的方式向机器人发送 hello。发送“whats up”与“hello”会发生什么？

LUIS 中的“None”意图意味着言语没有映射到任何意图。  在这种情况下，我们则需要使优先级下降到 ScorableGroup 的下一个级别。  在 RootDialog 类中添加“None”方法，如下所示：
```csharp
        [LuisIntent("")]
        [LuisIntent("None")]
        public async Task None(IDialogContext context, LuisResult result)
        {
            // Luis 返回“None”作为入选的意图，
            // 因此使优先级下降为 ScorableGroups 的下一级。  
            ContinueWithNextGroup();
        }
```

最后，为其余意图添加方法。  将针对得分最高的意图调用相应的方法。与邻座讨论“SearchPics”和“SharePics”代码。代码还将执行什么操作？ 


```csharp
        [LuisIntent("SearchPics")]
        public async Task SearchPics(IDialogContext context, LuisResult result)
        {
            // 检查 LUIS 是否识别出了我们应该查找的搜索词。  
            string facet = null;
            EntityRecommendation rec;
            if (result.TryFindEntity("facet", out rec)) facet = rec.Entity;

            // 如果我们不知道搜索什么（例如，用户说
            // “查找图片”或“搜索”而不是“查找 x 的图片”)，
            // 则会提示搜索词。  
            if (string.IsNullOrEmpty(facet))
            {
                PromptDialog.Text(context, ResumeAfterSearchTopicClarification,
                    "What kind of picture do you want to search for?");
            }
            else
            {
                await context.PostAsync("Searching pictures...");
                context.Call(new SearchDialog(facet), ResumeAfterSearchDialog);
            }
        }

        [LuisIntent("OrderPic")]
        public async Task OrderPic(IDialogContext context, LuisResult result)
        {
            await context.PostAsync("Ordering your pictures...");
        }

        [LuisIntent("SharePic")]
        public async Task SharePic(IDialogContext context, LuisResult result)
        {
            PromptDialog.Confirm(context, AfterShareAsync,
                "Are you sure you want to tweet this picture?");
        }

        private async Task AfterShareAsync(IDialogContext context, IAwaitable<bool> result)
        {
            if (result.GetAwaiter().GetResult() == true)
            {
                // 是，共享照片。
                await context.PostAsync("Posting tweet.");
            }
            else
            {
                // 否，不共享照片。  
                await context.PostAsync("OK, I won't share it.");
            }
        }


```



> 提示：“SharePic”方法包含一些代码，用于显示如何提示“是/否”确认以及设置 ScorableGroup。这段代码实际上没有发布 tweet，因为我们不想花时间让每个人都设置 Twitter 开发人员帐户之类的，但是如果你愿意，可以尝试实现此操作。

你可能已经注意到了，对于我们刚刚添加的意图，我们还没有实现可得分组。修改你的代码，使所有的 `LuisIntent` 都为可得分组优先级 1。在添加了 LUIS 功能后，你现在可以在 `ResumeAfterChoice` 方法中取消对两行 `await` 的注释。  

修改代码后，按 F5 在 Visual Studio 中运行，然后在 Bot Framework Emulator 中启动新对话。  尝试与机器人聊天，并确保获得预期的响应。  如果得到任何意外的结果，请记下它们并[修改 LUIS](https://docs.microsoft.com/zh-cn/azure/cognitive-services/LUIS/Train-Test)。

记住必须要重新训练并重新发布 LUIS 模型。  然后可以返回到仿真器中的机器人，并再次尝试。  

> 有趣的是：建议的言语非常强大。  LUIS 会明智地决定要显示的言语。  它会选择那些最能帮助它改进的言语，由“人机回圈”手动标记出来。  例如，如果 LUIS 模型预测一个给定的言语以 47% 的置信度映射到 Intent1，并预测它以 48% 的置信度映射到 Intent2，那么这是一个非常适合显示给人类以进行手动映射的候选项，因为模型在两个意图之间非常接近。  


> 额外加分练习（稍后完成）：在“Dialogs”文件夹中创建一个 OrderDialog 类。  通过使用 [FormFlow](https://docs.botframework.com/zh-cn/csharp/builder/sdkreference/forms.html) 的机器人创建下令打印的过程。  你的机器人需要收集以下信息：照片尺寸（8x10、5x7、皮夹大小等）、打印数量、亮面或亚光面处理，用户电话号码和用户电子邮件。



最后，如果上述服务都无法理解，请添加默认处理程序。  此 ScorableGroup 需要一个显式的 MethodBind，因为它没有被 LuisIntent 或 RegexPattern 属性（包括 MethodBind）修饰。

```csharp

        // 由于前一组中没有 scorable 入选，对话将发送一条帮助消息。
        [MethodBind]
        [ScorableGroup(2)]
        public async Task Default(IDialogContext context, IActivity activity)
        {
            await context.PostAsync("I'm sorry.I didn't understand you.");
            await context.PostAsync("You can tell me to find photos, tweet them, and order prints.  Here is an example: \"find pictures of food\".");
        }

```

按 F5 在机器人仿真器中运行你的机器人并进行测试。  



### 继续 [4_Publish_and_Register](./4_Publish_and_Register.md)  
返回 [README](./0_README.md)