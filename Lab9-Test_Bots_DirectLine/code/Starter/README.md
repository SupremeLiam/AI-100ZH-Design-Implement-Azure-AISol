# PictureBot

Bot Framework v4 Echo Bot 示例。

此机器人是使用 [Bot Framework](https://dev.botframework.com) 创建的，其中显示了如何创建简单的机器人，以用于接受来自用户的输入并做出相应的回应。

## 先决条件：

- [.NET Core SDK](https://dotnet.microsoft.com/download) 版本 2.1

```bash
  # 确定 dotnet 版本
  dotnet --version
```

## 尝试本示例

- 在终端中，导航到 `PictureBot`

```bash
    # 更改为项目文件夹
    cd # PictureBot
```

- 从终端或 Visual Studio 运行机器人，选择选项 A 或 B。

  A) 从终端

```bash
  # 运行机器人
  dotnet run
```

  B) 或者从 Visual Studio

  - 启动 Visual Studio
  - “文件”->“打开”->“项目/解决方案”
  - 导航到 `PictureBot` 文件夹
  - 选择 `PictureBot.csproj` 文件
  - 按 `F5` 运行项目

## 使用 Bot Framework Emulator 测试机器人

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) 是一种桌面应用程序，机器人开发人员可使用它测试和调试本地主机上的机器人或者通过隧道远程运行的机器人。

- 从 [此处](https://github.com/Microsoft/BotFramework-Emulator/releases)安装 Bot Framework Emulator 4.5.0 或更高版本

### 使用 Bot Framework Emulator 连接到机器人

- 启动 Bot Framework Emulator
- “文件”->“打开机器人”
- 输入机器人 URL `http://localhost:3978/api/messages`

## 将机器人部署到 Azure

要详细了解如何将机器人部署到 Azure，请参阅[将机器人部署到 Azure](https://aka.ms/azuredeployment)，获取部署说明的完整列表。

## 延伸阅读

- [Bot Framework 文档](https://docs.botframework.com)
- [机器人基础知识](https://docs.microsoft.com/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0)
- [活动处理](https://docs.microsoft.com/zh-cn/azure/bot-service/bot-builder-concept-activity-processing?view=azure-bot-service-4.0)
- [Azure 机器人服务说明](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
- [Azure 机器人服务文档](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)
- [.NET Core CLI 工具](https://docs.microsoft.com/zh-cn/dotnet/core/tools/?tabs=netcore2x)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
- [Azure 门户](https://portal.azure.com)
- [使用 LUIS 的语言理解](https://docs.microsoft.com/zh-cn/azure/cognitive-services/luis/)
- [通道和机器人连接器服务](https://docs.microsoft.com/zh-cn/azure/bot-service/bot-concepts?view=azure-bot-service-4.0)
