---
title: ASP.NET Core SignalR JavaScript 客户端
author: bradygaster
description: ASP.NET Core SignalR JavaScript 客户端概述。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 966e8e171752edb230f6da82203fd901b0fdeaab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768937"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.NET Core SignalR JavaScript 客户端

作者：[Rachel Appel](https://twitter.com/rachelappel)

ASP.NET Core SignalR JavaScript 客户端库使开发人员能够调用服务器端集线器代码。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="install-the-signalr-client-package"></a>安装SignalR客户端包

JavaScript SignalR客户端库以[npm](https://www.npmjs.com/)包的形式提供。 以下部分概述了安装客户端库的不同方式。

### <a name="install-with-npm"></a>通过 npm 安装

如果使用的是 Visual Studio，请在根文件夹中的 "**包管理器控制台**" 中运行以下命令。 对于 Visual Studio Code，请从**集成终端**运行以下命令。

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm 将包内容安装到*node_modules\\ *文件夹中。 在*\\wwwroot lib*文件夹下创建名为*signalr*的新文件夹。 将*signalr*文件复制到*wwwroot\lib\signalr*文件夹。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm 将包内容安装到*node_modules\\ *文件夹中。 在*\\wwwroot lib*文件夹下创建名为*signalr*的新文件夹。 将*signalr*文件复制到*wwwroot\lib\signalr*文件夹。

::: moniker-end

在`<script>`元素SignalR中引用 JavaScript 客户端。 例如：

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>使用内容交付网络（CDN）

若要在不使用 npm 先决条件的情况下使用客户端库，请引用 CDN 托管的客户端库副本。 例如：

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

以下 Cdn 提供了客户端库：

::: moniker range=">= aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>通过 LibMan 安装

[LibMan](xref:client-side/libman/index)可用于从 CDN 托管的客户端库安装特定的客户端库文件。 例如，仅将缩小 JavaScript 文件添加到项目。 有关该方法的详细信息，请参阅[添加SignalR客户端库](xref:tutorials/signalr#add-the-signalr-client-library)。

## <a name="connect-to-a-hub"></a>连接到集线器

下面的代码创建并启动连接。 中心名称不区分大小写。

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>跨域连接

通常，浏览器从与请求的页相同的域中加载连接。 但是，在某些情况下，需要与另一个域建立连接。

为了防止恶意站点读取其他站点中的敏感数据，默认情况下会禁用[跨域连接](xref:security/cors)。 若要允许跨源请求，请在`Startup`类中启用它。

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>从客户端调用集线器方法

JavaScript 客户端通过[HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)的[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke)方法在集线器上调用公共方法。 此`invoke`方法接受两个参数：

* 集线器方法的名称。 在下面的示例中，中心的方法名称是`SendMessage`。
* 在 hub 方法中定义的所有参数。 在下面的示例中，自变量名称`message`为。 示例代码使用了在所有主要浏览器（Internet Explorer 除外）的当前版本中受支持的箭头函数语法。

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> 如果在SignalR *无服务器模式下*使用 Azure 服务，则无法从客户端调用集线器方法。 有关详细信息，请参阅[ SignalR服务文档](/azure/azure-signalr/signalr-concept-serverless-development-config)。

`invoke`方法返回 JavaScript[承诺](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)。 当`Promise`服务器上的方法返回时，将解析为返回值（如果有）。 如果服务器上的方法引发错误， `Promise`将拒绝，并出现错误消息。 使用`then`和`catch`方法`Promise`来处理这些情况（或`await`语法）。

`send`方法返回 JavaScript `Promise`。 当`Promise`消息已发送到服务器时，将解决。 如果发送消息时出错， `Promise`将拒绝，并出现错误消息。 使用`then`和`catch`方法`Promise`来处理这些情况（或`await`语法）。

> [!NOTE]
> 使用`send`不会等到服务器收到消息。 因此，不可能从服务器返回数据或错误。

## <a name="call-client-methods-from-hub"></a>从中心调用客户端方法

若要从中心接收消息，请使用的[on](/javascript/api/%40aspnet/signalr/hubconnection#on)方法定义方法`HubConnection`。

* JavaScript 客户端方法的名称。 在下面的示例中，方法名称是`ReceiveMessage`。
* 集线器传递给方法的参数。 在下面的示例中，参数值为`message`。

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

当服务器端代码`connection.on`使用[SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)方法调用时，上面的代码将运行。

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR通过匹配和`SendAsync` `connection.on`中定义的方法名称和参数，确定要调用的客户端方法。

> [!NOTE]
> 作为最佳做法，请在`HubConnection`后面`on`调用[start](/javascript/api/%40aspnet/signalr/hubconnection#start)方法。 这样做可确保在收到消息之前注册处理程序。

## <a name="error-handling-and-logging"></a>错误处理和日志记录

将`catch`方法链接到`start`方法的末尾，以处理客户端错误。 使用`console.error`将错误输出到浏览器控制台。

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

设置客户端日志跟踪，方法是在建立连接时将记录器和事件类型传递给日志。 记录的消息具有指定的日志级别和更高的日志级别。 可用的日志级别如下所示：

* `signalR.LogLevel.Error`&ndash;错误消息。 仅`Error`记录消息。
* `signalR.LogLevel.Warning`&ndash;有关潜在错误的警告消息。 日志`Warning`和`Error`消息。
* `signalR.LogLevel.Information`&ndash;无错误的状态消息。 日志`Information`、 `Warning`和`Error`消息。
* `signalR.LogLevel.Trace`&ndash;跟踪消息。 记录所有内容，包括中心和客户端之间传输的数据。

使用[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的[configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)方法配置日志级别。 消息将记录到浏览器控制台。

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>重新连接客户端

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>自动重新连接

的 JavaScript 客户端SignalR可以配置为使用`withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)上的方法自动重新连接。 默认情况下，它不会自动重新连接。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

在没有任何参数`withAutomaticReconnect()`的情况下，会将客户端配置为分别等待0、2、10和30秒，然后再尝试重新连接尝试。

在开始任何重新连接尝试之前`HubConnection` ，将转换为`HubConnectionState.Reconnecting`状态，并激发`onreconnecting`其回调，而不是`Disconnected`转换为状态， `onclose`并触发其`HubConnection`回调，如不配置自动重新连接。 这为用户提供警告连接已丢失并禁用 UI 元素的机会。

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

如果客户端在其前四次尝试内成功重新`HubConnection`连接，则将转换`Connected`回状态并激发`onreconnected`其回调。 这为用户提供了通知用户连接已重新建立的机会。

由于连接在服务器上看起来是全新的，因此`connectionId`将向`onreconnected`回调提供一个新的。

> [!WARNING]
> 如果`onreconnected` `HubConnection`配置为`connectionId` [跳过协商](xref:signalr/configuration#configure-client-options)，则不会定义回调的参数。

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`不会将`HubConnection`配置为重试初始启动失败，因此，需要手动处理启动失败：

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

如果客户端在其前四次尝试中未成功重新`HubConnection`连接，则将`Disconnected`转换为状态并激发其[onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose)回调。 这为用户提供了通知用户连接永久丢失的机会，并建议刷新页面：

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

若要在断开连接或更改重新连接时间安排之前配置自定义的重新连接`withAutomaticReconnect`尝试次数，请接受一个数字数组，表示在开始每次重新连接尝试之前等待的延迟（以毫秒为单位）。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

前面的示例将配置`HubConnection`为在连接丢失后立即开始尝试重新连接。 这也适用于默认配置。

如果第一次重新连接尝试失败，则第二次重新连接尝试还会立即启动，而不是等待2秒，就像在默认配置中一样。

如果第二次重新连接尝试失败，则第三次重新连接尝试将在10秒内启动，这与默认配置相同。

然后，在第三次重新连接尝试失败后，自定义行为将再次从默认行为与其分离，而不是在另一个30秒内尝试再次尝试重新连接，就像在默认配置中一样。

如果需要更好地控制计时和自动重新连接尝试的次数， `withAutomaticReconnect`则接受一个实现`IRetryPolicy`接口的对象，该对象具有一个名为`nextRetryDelayInMilliseconds`的方法。

`nextRetryDelayInMilliseconds`采用类型`RetryContext`为的单个自变量。 `RetryContext`具有三个属性： `previousRetryCount` `elapsedMilliseconds`和`retryReason` `number` `number` `Error`分别为、和。 第一次重新连接尝试之前`previousRetryCount` ， `elapsedMilliseconds`和都是零， `retryReason`将是导致连接丢失的错误。 每次失败的重试`previousRetryCount`次数递增一次后， `elapsedMilliseconds`将进行更新，以反映到目前为止的重新连接所用的时间（以`retryReason`毫秒为单位），并且将是导致上次重新连接尝试失败的错误。

`nextRetryDelayInMilliseconds`必须返回一个数字，该数字表示在下一次重新连接尝试之前要等待`null`的毫秒`HubConnection`数，或者，如果应停止重新连接，则为。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

或者，你可以编写将手动重新连接客户端的代码，如[手动重新连接](#manually-reconnect)中所示。

::: moniker-end

### <a name="manually-reconnect"></a>手动重新连接

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 在3.0 之前， SignalR不会自动重新连接 JavaScript 客户端。 必须编写代码来手动重新连接客户端。

::: moniker-end

下面的代码演示典型的手动重新连接方法：

1. 创建函数（在本例中为`start`函数）以启动连接。
1. 在连接`start`的`onclose`事件处理程序中调用函数。

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

实际的实现将使用指数回退或在放弃之前重试指定的次数。

## <a name="additional-resources"></a>其他资源

* [JavaScript API 参考](/javascript/api/?view=signalr-js-latest)
* [JavaScript 教程](xref:tutorials/signalr)
* [WebPack 和 TypeScript 教程](xref:tutorials/signalr-typescript-webpack)
* [中心](xref:signalr/hubs)
* [.NET 客户端](xref:signalr/dotnet-client)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
* [跨域请求（CORS）](xref:security/cors)
* [Azure SignalR Service 无服务器文档](/azure/azure-signalr/signalr-concept-serverless-development-config)
