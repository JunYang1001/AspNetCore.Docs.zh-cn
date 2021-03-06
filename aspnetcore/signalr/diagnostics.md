---
title: ASP.NET Core 中的日志记录和诊断SignalR
author: anurse
description: 了解如何从 ASP.NET Core SignalR应用收集诊断信息。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: 5fda458c2418c3570d55d551ce5144730afd7f85
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767221"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>ASP.NET Core SignalR 中的日志记录和诊断

作者： [Andrew Stanton](https://twitter.com/anurse)

本文介绍如何从 ASP.NET Core SignalR 应用收集诊断信息，以帮助解决问题。

## <a name="server-side-logging"></a>服务器端日志记录

> [!WARNING]
> 服务器端日志可能包含应用中的敏感信息。 **请勿**将原始日志从生产应用发布到 GitHub 等公共论坛。

由于 SignalR 是 ASP.NET Core 的一部分，因此它使用 ASP.NET Core 日志记录系统。 在默认配置中，SignalR 记录的信息非常小，但这可以进行配置。 有关配置 ASP.NET Core 日志记录的详细信息，请参阅有关[ASP.NET Core 日志记录](xref:fundamentals/logging/index#configuration)的文档。

SignalR 使用两个记录器类别：

* `Microsoft.AspNetCore.SignalR`&ndash;对于与集线器协议相关的日志，激活集线器，调用方法，以及其他与中心相关的活动。
* `Microsoft.AspNetCore.Http.Connections`&ndash;对于与传输相关的日志，例如 Websocket、长轮询和服务器发送事件以及低级别 SignalR 基础结构。

若要从 SignalR 启用详细日志，请通过将以下项添加到`Debug` *appsettings*文件中`LogLevel` `Logging`的子部分，将上述两个前缀配置为文件中的级别：

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

你还可以在方法的代码中对`CreateWebHostBuilder`此进行配置：

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

如果不使用基于 JSON 的配置，请在配置系统中设置以下配置值：

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

查看配置系统的文档以确定如何指定嵌套配置值。 例如，使用环境变量时，将使用`_`两个字符，而不`:`是（例如`Logging__LogLevel__Microsoft.AspNetCore.SignalR`）。

建议为应用收集`Debug`更详细的诊断时使用级别。 此`Trace`级别生成非常低级别的诊断，很少需要诊断应用中的问题。

## <a name="access-server-side-logs"></a>访问服务器端日志

访问服务器端日志的方式取决于运行的环境。

### <a name="as-a-console-app-outside-iis"></a>作为 IIS 外部的控制台应用

如果在控制台应用中运行，则默认情况下应启用[控制台记录器](xref:fundamentals/logging/index#console-provider)。 SignalR 日志将显示在控制台中。

### <a name="within-iis-express-from-visual-studio"></a>在 Visual Studio IIS Express 中

Visual Studio 会在 "**输出**" 窗口中显示日志输出。 选择**ASP.NET Core Web 服务器**"下拉选项。

### <a name="azure-app-service"></a>Azure 应用服务

在 Azure App Service 门户的 "**诊断日志**" 部分中，启用 "**应用程序日志记录（文件系统）** " `Verbose`选项，并将**级别**配置为。 日志**流**服务和应用服务文件系统的日志中应提供日志。 有关详细信息，请参阅[Azure 日志流式处理](xref:fundamentals/logging/index#azure-log-streaming)。

### <a name="other-environments"></a>其他环境

如果将应用部署到另一个环境（例如 Docker、Kubernetes 或 Windows 服务），请参阅<xref:fundamentals/logging/index> ，以了解有关如何配置适用于环境的日志记录提供程序的详细信息。

## <a name="javascript-client-logging"></a>JavaScript 客户端日志记录

> [!WARNING]
> 客户端日志可能包含应用中的敏感信息。 **请勿**将原始日志从生产应用发布到 GitHub 等公共论坛。

使用 JavaScript 客户端时，可以使用中`configureLogging` `HubConnectionBuilder`的方法配置日志记录选项：

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

若要完全禁用日志记录`signalR.LogLevel.None` ，请`configureLogging`在方法中指定。

下表显示了可用于 JavaScript 客户端的日志级别。 将日志级别设置为这些值之一，可以在表中对该级别和其之上的所有级别进行日志记录。

| 级别 | 说明 |
| ----- | ----------- |
| `None` | 不记录任何消息。 |
| `Critical` | 指示整个应用程序中的失败的消息。 |
| `Error` | 指示当前操作失败的消息。 |
| `Warning` | 指示非严重问题的消息。 |
| `Information` | 信息性消息。 |
| `Debug` | 诊断消息对于调试很有用。 |
| `Trace` | 旨在诊断特定问题的详细诊断消息。 |

配置详细级别后，日志将写入浏览器控制台（或 NodeJS 应用中的标准输出）。

如果要将日志发送到自定义日志记录系统，可以提供实现`ILogger`接口的 JavaScript 对象。 唯一需要实现的方法是`log`，它将使用事件的级别和与事件关联的消息。 例如：

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET 客户端日志记录

> [!WARNING]
> 客户端日志可能包含应用中的敏感信息。 **请勿**将原始日志从生产应用发布到 GitHub 等公共论坛。

若要从 .NET 客户端获取日志，可以对`ConfigureLogging` `HubConnectionBuilder`使用方法。 这与和`ConfigureLogging` `WebHostBuilder` `HostBuilder`上的方法的工作方式相同。 你可以配置 ASP.NET Core 中使用的相同日志记录提供程序。 但是，您必须为单独的日志提供程序手动安装和启用 NuGet 包。

### <a name="console-logging"></a>控制台日志记录

若要启用控制台日志记录，请添加[""。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 然后，使用`AddConsole`方法来配置控制台记录器：

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>调试输出窗口日志记录

还可以配置日志，以便在 Visual Studio 中切换到 "**输出**" 窗口。 安装 " ["，](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug)然后使用`AddDebug`方法：

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>其他日志记录提供程序

SignalR支持其他日志记录提供程序，如 Serilog、Seq、NLog 或与集成的`Microsoft.Extensions.Logging`任何其他日志记录系统。 如果日志记录系统提供， `ILoggerProvider`则可以将其注册到`AddProvider`：

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>控件详细级别

如果要从应用中的其他位置进行日志记录，则将默认级别`Debug`更改为可能太详细。 您可以使用筛选器来配置SignalR日志的日志记录级别。 可以在代码中完成此操作，其方式与在服务器上的操作大致相同：

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>网络跟踪

> [!WARNING]
> 网络跟踪包含应用发送的每个消息的全部内容。 **切勿**将原始网络跟踪从生产应用发布到 GitHub 等公共论坛。

如果遇到问题，网络跟踪有时可以提供很多有用的信息。 如果要在我们的问题跟踪程序上发布问题，此方法特别有用。

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>使用 Fiddler 收集网络跟踪（首选选项）

此方法适用于所有应用。

Fiddler 是一个非常强大的工具，用于收集 HTTP 跟踪。 从[telerik.com/fiddler](https://www.telerik.com/fiddler)安装它，启动它，然后运行你的应用程序并重现此问题。 Fiddler 适用于 Windows，并且有适用于 macOS 和 Linux 的 beta 版本。

如果使用 HTTPS 进行连接，则需要执行一些额外的步骤来确保 Fiddler 可以解密 HTTPS 流量。 有关更多详细信息，请参阅[Fiddler 文档](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)。

收集跟踪后，可以通过从菜单栏中选择 "**文件** > " "**保存** > **所有会话**" 来导出跟踪。

![正在从 Fiddler 导出所有会话](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>使用 tcpdump 收集网络跟踪（仅限 macOS 和 Linux）

此方法适用于所有应用。

可以通过在命令行界面中运行以下命令，使用 tcpdump 收集原始 TCP 跟踪。 如果出现权限错误， `root`你可能需要将命令作为或的前缀： `sudo`

```console
tcpdump -i [interface] -w trace.pcap
```

将`[interface]`替换为要捕获的网络接口。 通常，这类似于`/dev/eth0` （适用于标准以太网接口）或`/dev/lo0` （对于 localhost 流量）。 有关详细信息，请参阅`tcpdump`主机系统上的手册页。

## <a name="collect-a-network-trace-in-the-browser"></a>在浏览器中收集网络跟踪

此方法仅适用于基于浏览器的应用。

大多数浏览器开发人员工具都有一个 "网络" 选项卡，该选项卡允许您捕获浏览器和服务器之间的网络活动。 但是，这些跟踪不包括 WebSocket 和服务器发送的事件消息。 如果正在使用这些传输，则使用 Fiddler 或 TcpDump 等工具（如下所述）是更好的方法。

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge 和 Internet Explorer

（对于边缘和 Internet Explorer，说明是相同的）

1. 按 F12 打开开发工具
2. 单击 "网络" 选项卡
3. 刷新页面（如果需要）并重现问题
4. 单击工具栏中的 "保存" 图标，将跟踪作为 "HAR" 文件导出：

![Microsoft Edge 开发工具网络选项卡上的 "保存" 图标](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. 按 F12 打开开发工具
2. 单击 "网络" 选项卡
3. 刷新页面（如果需要）并重现问题
4. 右键单击请求列表中的任意位置，然后选择 "另存为包含内容的 HAR"：

![Google Chrome 开发工具网络选项卡中的 "另存为 HAR 与内容" 选项](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. 按 F12 打开开发工具
2. 单击 "网络" 选项卡
3. 刷新页面（如果需要）并重现问题
4. 右键单击请求列表中的任意位置，然后选择 "全部保存为 HAR"

![Mozilla Firefox 开发工具网络选项卡中的 "全部保存为 HAR" 选项](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>将诊断文件附加到 GitHub 问题

可以通过对其进行重命名，将诊断文件附加到 GitHub 问题`.txt` ，以便它们具有一个扩展，然后将其拖放到该问题上。

> [!NOTE]
> 请不要将日志文件或网络跟踪的内容粘贴到 GitHub 问题中。 这些日志和跟踪可能会很大，GitHub 通常会将其截断。

![将日志文件拖到 GitHub 问题上](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>其他资源

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
