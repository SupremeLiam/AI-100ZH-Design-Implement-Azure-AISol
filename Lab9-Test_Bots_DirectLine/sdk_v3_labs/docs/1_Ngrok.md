# 使用 ngrok 进行开发和测试
 
## 1.	目标
 
使用 Microsoft Bot Framework 将机器人配置为可用于特定通道，你需要将机器人服务托管在公共 URL 终结点上。如果机器人服务位于隐藏在 NAT 或防火墙后的本地服务器端口上，通道将无法访问它。
  
在设计/生成/测试代码时，你并不希望总是得不断重新部署。因为这会产生额外的托管成本。于是，你可以使用 ngrok，帮助加快机器人的开发/测试。本实验的目标是使用 ngrok 将机器人公开到公共 Internet，并使用公共终结点在模拟器中测试机器人。
  
## 2.	设置
  
 a.	  如果尚未安装 ngrok，请从 https://ngrok.com/download 下载 ngrok 并为自己的操作系统安装。解压并安装下载的 ngrok 文件。

 b.	  打开（上一个实验中的）任何机器人项目或使用“机器人应用程序”项目模板新建一个 EchoBot。请运行机器人。如果创建 EchoBot，你应在浏览器中看到以下消息：

**EchoBot**

在这里介绍你的机器人和使用条款等。

访问 Bot Framework，注册机器人。注册时，务必将机器人的终结点设置为 https://your_bots_hostname/api/messages

*** 以上信息仅供参考。无需为本实验注册机器人。

## 3.	转发

 a.	 鉴于机器人托管在 localhost:3979 上，我们可以使用 ngrok 将此端口公开到公共 Internet

 b.	 打开终端并转到安装 ngrok 的文件夹
 
 c.	 运行下面的命令，你将看到转发 URL：

 ````ngrok.exe http 3979 -host-header="localhost:3979"````

![转发 URL](images/ForwardingUrl.png)

 e.	 在机器人模拟器中输入转发 URL (http)机器人 URL 将 /api/messages 附加到转发 URL。在模拟器中通过发送消息测试机器人。我们现在使用的是公共终结点（而不是 localhost）来测试机器人。


![机器人 URL](images/BotUrl.png)

## 4.	提早完成？试一试本练习，以获得额外加分：

 在 Microsoft Bot Framework 上注册机器人时，能否使用消息终结点的转发 URL？使用任何通道（例如 Skype）上的转发 URL 在通道上测试机器人。

### 继续学习 [2_对机器人进行单元测试](2_Unit_Testing_Bots.md)

 返回 [README](../0_README.md)
