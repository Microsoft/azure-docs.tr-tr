---
title: include dosyası
description: include dosyası
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a0a9bc29c3e20a025fb2c46a71c2f134c37bee04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84465111"
---
Aşağıdaki PowerShell komutu, depolama hesabının genel uç noktasındaki tüm trafiği reddeder. Bu komutun `-Bypass` parametresi olarak ayarlandığını unutmayın `AzureServices` . Bu, Azure Dosya Eşitleme gibi güvenilen ilk taraf hizmetlerin, genel uç nokta aracılığıyla depolama hesabına erişmesi için izin verir.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```