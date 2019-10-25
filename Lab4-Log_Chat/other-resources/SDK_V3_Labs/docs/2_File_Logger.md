# 文件记录器

## 1.	目标

本实验的目的是使用中间件将聊天对话记录到文件中。过多的输入/输出 (I/O) 操作可能导致机器人响应缓慢。在本实验中，我们将利用全局事件来有效地将聊天对话记录到文件中。此活动是上一个实验概念的扩展。

从 Visual Studio 中的 code\file-core-Middleware 导入项目。

## 2. 一种低效的日志记录方式

使用 File.AppendAllLines 将聊天对话记录到文件中是一种非常简单的方法，如下面的代码片段所示。File.AppendAllLines 打开一个文件，并将指定的字符串附加到该文件，然后关闭该文件。但是，这种日志记录方式可能非常低效，因为我们将为来自用户/机器人的每条消息打开和关闭文件。
tu
````C#
public class DebugActivityLogger : IActivityLogger
{
    public async Task LogAsync(IActivity activity)
    {
        File.AppendAllLines("C:\\Users\\username\\log.txt", new[] { $"From:{activity.From.Id} - To:{activity.Recipient.Id} - Message:{activity.AsMessageActivity().Text}" });
    }
}
````

## 3.	一种有效的记录方式

使用 Global.asax 文件并使用与机器人生命周期相关的事件将日志记录到文件中是一种更为有效的方法。扩展 global.asax，如下面的代码片段所示。请将以下行更改为环境中存在的路径。

````C# 
tw = new StreamWriter("C:\\Users\\username\\log.txt", true);
````

Application_Start 打开 StreamWriter，它实现以特定的编码将字符写入流中的 Stream 包装器。这样做延续了机器人的生命，并且现在我们可以写入它，而不用为每条消息打开和关闭文件。请务必注意，StreamWriter 现在作为参数发送到 DebugActivityLogger。日志文件可以在 Application_End 中关闭，在卸载应用程序之前，每个应用程序的生命周期都会调用一次 Application_End。

````C#
using System.Web.Http;
using Autofac;
using Microsoft.Bot.Builder.Dialogs;
using System.IO;
using System.Diagnostics;
using System;


namespace MiddlewareBot
{
    public class WebApiApplication : System.Web.HttpApplication
    {
        static TextWriter tw = null;
        protected void Application_Start()
        {
            tw = new StreamWriter("C:\\Users\\username\\log.txt", true);
            Conversation.UpdateContainer(builder =>
            {
                builder.RegisterType<DebugActivityLogger>().AsImplementedInterfaces().InstancePerDependency().WithParameter("inputFile", tw);
            });

            GlobalConfiguration.Configure(WebApiConfig.Register);
        }

        protected void Application_End()
        {
            tw.Close();
        }
    }
}
````

通过添加下面的行，接收 DebugActivityLogger 构造函数中的文件参数并更新 LogAsync 以立即写入日志文件。

````C#
tw.WriteLine($"From:{activity.From.Id} - To:{activity.Recipient.Id} - Message:{activity.AsMessageActivity().Text}", true);
tw.Flush();
````

整个 DebugActivityLogger 代码如下所示：

````C#
using System.Diagnostics;
using System.Threading.Tasks;
using Microsoft.Bot.Builder.History;
using Microsoft.Bot.Connector;
using System.IO;

namespace MiddlewareBot
{    
    public class DebugActivityLogger : IActivityLogger
    {
        TextWriter tw;
        public DebugActivityLogger(TextWriter inputFile)
        {
            this.tw = inputFile;
        }


        public async Task LogAsync(IActivity activity)
        {
            tw.WriteLine($"From:{activity.From.Id} - To:{activity.Recipient.Id} - Message:{activity.AsMessageActivity().Text}", true);
            tw.Flush();
        }
    }

}
````

## 4. 日志文件

在仿真器中运行机器人应用程序并使用消息进行测试。调查行中指定的日志文件

````C# 
tw = new StreamWriter("C:\\Users\\username\\log.txt", true);
````

要查看来自用户和机器人的日志消息，消息现在应在日志文件中显示如下：

````From:2c1c7fa3 - To:56800324 - Message:a log message````

````From:56800324 - To:2c1c7fa3 - Message:You sent a log message which was 13 characters````

## 5. 选择性记录日志

在某些情况下，期望对选择性消息执行建模或选择性地记录。有许多这样的例子：a) 非常活跃的社区机器人可以很快地捕获大量的聊天消息，并且许多聊天消息可能不是非常有用。b) 可能还需要有选择地记录以关注与特定趋势产品类别/主题相关的某些用户/机器人或消息。

人们总是可以捕获所有聊天消息并对其进行筛选以挖掘选择性消息。但是，灵活地进行选择性记录可能非常有用，并且 Microsoft Bot Framework 允许这样做。要选择性地记录来自用户的消息，可以调查活动 json 并筛选“名称”。例如，在下面的 json 中，消息由“Bot1”发送。

```json
{
  "type": "message",
  "timestamp": "2017-10-27T11:19:15.2025869Z",
  "localTimestamp": "2017-10-27T07:19:15.2140891-04:00",
  "serviceUrl": "http://localhost:9000/",
  "channelId": "emulator",
  "from": {
    "id": "56800324",
    "name": "Bot1"
  },
  "conversation": {
    "isGroup": false,
    "id": "8a684db8",
    "name": "Conv1"
  },
  "recipient": {
    "id": "2c1c7fa3",
    "name": "User1"
  },
  "membersAdded": [],
  "membersRemoved": [],
  "text": "You sent hi which was 2 characters",
  "attachments": [],
  "entities": [],
  "replyToId": "09df56eecd28457b87bba3e67f173b84"
}
```
在调查 json 之后，通过引入一个简单的条件来检查 ````activity.From.Name```` 属性，可以修改 DebugActivityLogger 中的 LogAsync 方法以筛选用户消息：

````C#
public class DebugActivityLogger : IActivityLogger
{
        public async Task LogAsync(IActivity activity)
        {
           if (!activity.From.Name.Contains("Bot"))
            {
               Debug.WriteLine($"From:{activity.From.Id} - To:{activity.Recipient.Id} - Message:{activity.AsMessageActivity()?.Text}");
            }
        }
}
````


### 继续 [3_SQL_Logger](3_SQL_Logger.md)

返回 [0_README](../0_README.md)