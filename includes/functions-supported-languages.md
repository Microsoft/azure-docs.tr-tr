---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 01e62685fa73a7f547ef5246b28fdfdf659e7afa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623402"
---
|Dil                                 |'in         |2.x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-reference-csharp.md)|GA (.NET Framework 4,7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3,1)<br/>[Önizleme (.NET 5,0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (düğüm 6)|GA (düğüm 10 & 8)| GA (düğüm 14, 12, & 10) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4,7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3,1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|Yok|GA (Java 8)| GA (Java 11 & 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |Yok|GA (PowerShell Core 6)| GA (PowerShell 7 & çekirdek 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|Yok|GA (Python 3,7 & 3,6)| GA (Python 3,8, 3,7, & 3,6) <br/> Önizleme (Python 3,9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |Yok|GA<sup>2</sup>| GA<sup>2</sup> |


<sup>1</sup> .NET sınıf kitaplığı uygulamaları hedefleme çalışma zamanı sürüm 2. x artık .NET Core 2. x uyumluluk modundaki .net Core 3,1 ' de çalıştırılabilir. Daha fazla bilgi için bkz. [işlevler v2. x konuları](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> Transpiling aracılığıyla JavaScript ile desteklenir.

Desteklenen dil sürümleri hakkında daha fazla ayrıntı için dile özgü Geliştirici Kılavuzu makalesine bakın.   
Dil desteğiyle ilgili planlı değişiklikler hakkında daha fazla bilgi için bkz. [Azure yol haritası](https://azure.microsoft.com/roadmap/?tag=functions).
