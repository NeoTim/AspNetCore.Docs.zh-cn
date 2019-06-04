---
title: 配置 Blazor 链接器
author: guardrex
description: 了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2019
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 00676d4311f8955c3c1ef38d31219d62ea9f4a25
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887772"
---
# <a name="configure-the-linker-for-blazor"></a><span data-ttu-id="e86e5-103">配置 Blazor 链接器</span><span class="sxs-lookup"><span data-stu-id="e86e5-103">Configure the Linker for Blazor</span></span>

<span data-ttu-id="e86e5-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e86e5-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e86e5-105">Blazor 在版本生成期间执行[中间语言 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) 链接以从应用的输出程序集中删除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="e86e5-105">Blazor performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a Release build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="e86e5-106">使用以下任何一种方法控制程序集链接：</span><span class="sxs-lookup"><span data-stu-id="e86e5-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="e86e5-107">使用 [MSBuild 属性](#disable-linking-with-a-msbuild-property)全局禁用链接。</span><span class="sxs-lookup"><span data-stu-id="e86e5-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="e86e5-108">使用[配置文件](#control-linking-with-a-configuration-file)按程序集控制链接。</span><span class="sxs-lookup"><span data-stu-id="e86e5-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="e86e5-109">使用 MSBuild 属性禁用链接</span><span class="sxs-lookup"><span data-stu-id="e86e5-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="e86e5-110">在构建应用程序（包括发布）时，默认在发布模式下启用链接。</span><span class="sxs-lookup"><span data-stu-id="e86e5-110">Linking is enabled by default in Release mode when an app is built, which includes publishing.</span></span> <span data-ttu-id="e86e5-111">若要禁用所有程序集链接，请在项目文件中将 `<BlazorLinkOnBuild>` MSBuild 属性设置为 `false`：</span><span class="sxs-lookup"><span data-stu-id="e86e5-111">To disable linking for all assemblies, set the `<BlazorLinkOnBuild>` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="e86e5-112">使用配置文件控制链接</span><span class="sxs-lookup"><span data-stu-id="e86e5-112">Control linking with a configuration file</span></span>

<span data-ttu-id="e86e5-113">通过提供 XML 配置文件并在项目文件中将该文件指定为 MSBuild 项，按程序集控制链接：</span><span class="sxs-lookup"><span data-stu-id="e86e5-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="e86e5-114">Linker.xml：</span><span class="sxs-lookup"><span data-stu-id="e86e5-114">*Linker.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/aspnet/Blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="e86e5-115">有关详细信息，请参阅 [IL Linker：xml 描述符语法](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)。</span><span class="sxs-lookup"><span data-stu-id="e86e5-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>