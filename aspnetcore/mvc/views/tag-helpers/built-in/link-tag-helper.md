---
title: ASP.NET Core 中的链接标记帮助程序
author: rick-anderson
ms.author: riande
description: 了解 ASP.NET Core 链接标记帮助程序属性以及每个属性在扩展 HTML 链接标记的行为中所起的作用。
ms.custom: mvc
ms.date: 09/24/2019
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: e1e2e58b4ab9087e1f9de5b5c03b587feb88f1b9
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256482"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="08ea2-103">ASP.NET Core 中的链接标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="08ea2-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="08ea2-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="08ea2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="08ea2-105">[链接标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)用于生成指向主要或回退 CSS 文件的链接。</span><span class="sxs-lookup"><span data-stu-id="08ea2-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="08ea2-106">通常主 CSS 文件位于[内容分发网络](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN)。</span><span class="sxs-lookup"><span data-stu-id="08ea2-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="08ea2-107">可以使用链接标记帮助程序指定 CSS 文件的 CDN 以及回退文件（CDN 不可用时）。</span><span class="sxs-lookup"><span data-stu-id="08ea2-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="08ea2-108">链接标记帮助程序借助本地宿主的可靠性提供 CDN 性能优势。</span><span class="sxs-lookup"><span data-stu-id="08ea2-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="08ea2-109">以下 Razor 标记显示使用 ASP.NET Core Web 应用模板创建的布局文件的 `head` 元素：</span><span class="sxs-lookup"><span data-stu-id="08ea2-109">The following Razor markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="08ea2-110">以下是上述代码呈现的 HTML（非开发环境）：</span><span class="sxs-lookup"><span data-stu-id="08ea2-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-csharp[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="08ea2-111">在上述代码中，链接标记帮助程序生成 `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` 元素以及以下 JavaScript（用于验证是否可以从 CDN 获取请求的 bootstrap.min.css 文件）。</span><span class="sxs-lookup"><span data-stu-id="08ea2-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="08ea2-112">在此示例中，可以获取 CSS 文件，因此标记帮助程序使用 CDN CSS 文件生成 `<link />` 元素。</span><span class="sxs-lookup"><span data-stu-id="08ea2-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="08ea2-113">常用的链接标记帮助程序属性</span><span class="sxs-lookup"><span data-stu-id="08ea2-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="08ea2-114">若要了解所有链接标记帮助程序属性和方法，请参阅[链接标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)。</span><span class="sxs-lookup"><span data-stu-id="08ea2-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="08ea2-115">href</span><span class="sxs-lookup"><span data-stu-id="08ea2-115">href</span></span>

<span data-ttu-id="08ea2-116">链接的资源的首选地址。</span><span class="sxs-lookup"><span data-stu-id="08ea2-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="08ea2-117">在任何情况下，均会将此地址传递到生成的 HTML。</span><span class="sxs-lookup"><span data-stu-id="08ea2-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="08ea2-118">asp-fallback-href</span><span class="sxs-lookup"><span data-stu-id="08ea2-118">asp-fallback-href</span></span>

<span data-ttu-id="08ea2-119">主 URL 失效后要回退到的 CSS 样式表的 URL。</span><span class="sxs-lookup"><span data-stu-id="08ea2-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="08ea2-120">asp-fallback-test-class</span><span class="sxs-lookup"><span data-stu-id="08ea2-120">asp-fallback-test-class</span></span>

<span data-ttu-id="08ea2-121">样式表中定义的用于回退测试的类名称。</span><span class="sxs-lookup"><span data-stu-id="08ea2-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="08ea2-122">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>。</span><span class="sxs-lookup"><span data-stu-id="08ea2-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="08ea2-123">asp-fallback-test-property</span><span class="sxs-lookup"><span data-stu-id="08ea2-123">asp-fallback-test-property</span></span>

<span data-ttu-id="08ea2-124">用于回退测试的 CSS 属性名称。</span><span class="sxs-lookup"><span data-stu-id="08ea2-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="08ea2-125">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>。</span><span class="sxs-lookup"><span data-stu-id="08ea2-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="08ea2-126">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="08ea2-126">asp-fallback-test-value</span></span>

<span data-ttu-id="08ea2-127">用于回退测试的 CSS 属性值。</span><span class="sxs-lookup"><span data-stu-id="08ea2-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="08ea2-128">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>。</span><span class="sxs-lookup"><span data-stu-id="08ea2-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="08ea2-129">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="08ea2-129">asp-fallback-test-value</span></span>

<span data-ttu-id="08ea2-130">用于回退测试的 CSS 属性值。</span><span class="sxs-lookup"><span data-stu-id="08ea2-130">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="08ea2-131">有关详细信息，请参阅<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>。</span><span class="sxs-lookup"><span data-stu-id="08ea2-131">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span></span>

## <a name="additional-resources"></a><span data-ttu-id="08ea2-132">其他资源</span><span class="sxs-lookup"><span data-stu-id="08ea2-132">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>