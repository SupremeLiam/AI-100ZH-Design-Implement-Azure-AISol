# 中间件机器人示例

一个介绍如何拦截和记录消息的示例机器人。

[![部署到 Azure][部署按钮]][部署 CSharp/MiddlewareLogging]

[部署按钮]：https://azuredeploy.net/deploybutton.png
[部署 CSharp/MiddlewareLogging]：https://azuredeploy.net

### 前提条件

运行此示例的最低要求：
* Visual Studio 2015 的最新更新。可在[此处](http://www.visualstudio.com)免费下载社区版。
* Bot Framework Emulator。要安装 Bot Framework Emulator，请在[此处](https://emulator.botframework.com/)下载。请查看[本文](https://github.com/microsoft/botframework-emulator/wiki/Getting-Started)，详细了解 Bot Framework Emulator。

### 代码突出显示

使用对话历史记录时最常见的操作之一是拦截和记录机器人与用户的消息活动。[`Microsoft.Bot.Builder.History`](https://github.com/Microsoft/BotBuilder/tree/master/CSharp/Library/Microsoft.Bot.Builder.History) 命名空间提供了用于执行此操作的接口和类。具体而言，[`IActivityLogger`](https://github.com/Microsoft/BotBuilder/blob/master/CSharp/Library/Microsoft.Bot.Builder/Dialogs/IActivityLogger.cs) 接口包含类需要实现才能记录消息活动的功能的定义。

查看仅在调试中运行时才将消息活动写入跟踪侦听器的 `IActivityLogger` 接口的 [`DebugActivityLogger`](DebugActivityLogger.cs) 实现。

````C#
public class DebugActivityLogger : IActivityLogger
{
    public async Task LogAsync(IActivity activity)
    {
        Debug.WriteLine($"From:{activity.From.Id} - To:{activity.Recipient.Id} - Message:{activity.AsMessageActivity()?.Text}");
    }
}
````

默认情况下，BotBuilder 库使用对话容器注册 [`NullActivityLogger`](https://github.com/Microsoft/BotBuilder/blob/master/CSharp/Library/Microsoft.Bot.Builder/Dialogs/IActivityLogger.cs#L81)，这是一种不需要执行任何操作的活动记录器。查看 [`Global.asax.cs`](Global.asax.cs#L11-L13) 中示例的 [`DebugActivityLogger`](DebugActivityLogger.cs) 的 Autofac 容器中的注册。

````C#
var builder = new ContainerBuilder();
builder.RegisterType<DebugActivityLogger>().AsImplementedInterfaces().InstancePerDependency();
builder.Update(Conversation.Container);
````

### 结果

打开和运行示例解决方案时，你将在 [Visual Studio 输出窗口的调试文本窗格](https://blogs.msdn.microsoft.com/visualstudioalm/2015/02/09/the-output-window-while-debugging-with-visual-studio/) 中看到以下结果。

机器人收到的消息：
````
From:default-user - To:ii845fc9l02209hh6 - Message:Hello bot!
````

发送给用户的消息：
````
From:ii845fc9l02209hh6 - To:default-user - Message:You sent Hello bot! which was 10 characters
````

### 更多信息

要获取有关如何开始使用 Bot Builder for .NET 和对话历史记录的更多信息，请参阅以下资源：

* [Bot Builder for .NET](https://docs.microsoft.com/zh-cn/bot-framework/dotnet/)
* [拦截消息](https://docs.microsoft.com/zh-cn/bot-framework/dotnet/bot-builder-dotnet-middleware)
* [Microsoft.Bot.Builder.History 命名空间](https://docs.botframework.com/zh-cn/csharp/builder/sdkreference/dc/dc6/namespace_microsoft_1_1_bot_1_1_builder_1_1_history.html)
* [TableLogger（使用 Azure 表存储的活动记录器）](https://github.com/Microsoft/BotBuilder/blob/master/CSharp/Library/Microsoft.Bot.Builder.Azure/TableLogger.cs#L60)