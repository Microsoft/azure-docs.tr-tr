---
title: include dosyası
description: include dosyası
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aad66a91f7de8380ac7e87f0ce8e35ed43cac4a6
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594552"
---
Belirli bir işlev uygulamasında tüm işlevleri için kod ana bilgisayar yapılandırma dosyası içeren bir kök proje klasörü ve bir veya daha fazla alt klasörleri içinde yer alır. Her bir alt klasör için ayrı bir işlev kodunu içerir. Klasör yapısı'nda aşağıdaki gösterimi gösterilmektedir:

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

İçinde sürüm 2.x işlev uygulamasında tüm işlevleri işlevler çalışma zamanının aynı dil yığını paylaşım gerekir.  

[Host.json](../articles/azure-functions/functions-host-json.md) dosya çalışma zamanı özel yapılandırmaları içerir ve işlev uygulaması kök klasöründe bulunur. A *bin* klasörü paketler ve işlev uygulamasını gerektiren diğer kitaplık dosyalarını içerir. İşlev uygulaması projesi için dile özgü gereksinimleri bölümünü inceleyin:

* [C# sınıf kitaplığı (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# betiği (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#komut dosyası](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



