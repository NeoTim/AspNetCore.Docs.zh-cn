---
title: 数据保护 Api 的其他 ASP.NET Core
author: rick-anderson
description: 了解 ASP.NET Core Data Protection ISecret 接口。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 947c6eb62550d325492365ece84d45a14845888f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630908"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>数据保护 Api 的其他 ASP.NET Core

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> 实现以下任何接口的类型对于多个调用方应是线程安全的。

## <a name="isecret"></a>ISecret

`ISecret`接口表示机密值，如加密密钥材料。 它包含以下 API 图面：

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer`方法用原始机密值填充所提供的缓冲区。 此 API 将缓冲区作为参数而不是直接返回，这 `byte[]` 使调用方可以固定缓冲区对象，从而限制托管垃圾回收器的机密公开。

`Secret`类型是在 `ISecret` 进程内内存中存储机密值的具体实现。 在 Windows 平台上，通过 [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory)对机密值进行加密。
