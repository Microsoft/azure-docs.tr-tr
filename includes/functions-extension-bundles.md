---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639156"
---
Bağlama uzantılarını yüklemenin en kolay yolu, [uzantı](../articles/azure-functions/functions-bindings-register.md#extension-bundles)paketlerinin etkinleştirilme yöntemidir. Paketleri etkinleştirdiğinizde, önceden tanımlanmış bir uzantı paketleri kümesi otomatik olarak yüklenir.

Uzantı paketleri 'ni etkinleştirmek için, Host. json dosyasını açın ve içeriğini aşağıdaki kodla eşleşecek şekilde güncelleştirin:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```