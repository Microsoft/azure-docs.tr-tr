---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010522"
---
Bağlama uzantılarını yüklemenin en kolay yolu, [uzantı](../articles/azure-functions/functions-bindings-register.md#extension-bundles)paketlerinin etkinleştirilme yöntemidir. Paketleri etkinleştirdiğinizde, önceden tanımlanmış bir uzantı paketleri kümesi otomatik olarak yüklenir.

Uzantı paketlerini etkinleştirmek için dosyadaki host.jsaçın ve içeriğini aşağıdaki kodla eşleşecek şekilde güncelleştirin:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```