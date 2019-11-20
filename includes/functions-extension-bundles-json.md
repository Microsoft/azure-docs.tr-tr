---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129082"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

`extensionBundle`' de aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama |
| -------- | ----------- |
| id | Microsoft Azure Işlevleri Uzantısı paketleri için ad alanı. |
| version | Yüklenecek paket sürümü. Işlevler çalışma zamanı her zaman, sürüm aralığı veya aralığı tarafından tanımlanan izin verilen en fazla sürümü seçer. Yukarıdaki sürüm değeri, 1.0.0 'e kadar tüm paket sürümlerinin 2.0.0 dahil değil, izin vermez. Daha fazla bilgi için bkz. [Sürüm aralıklarını belirtmek için Aralık gösterimi](/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Paket sürümleri artıkında paket değişim artışı. Paketteki paketler ana bir sürümle artında ana sürüm değişiklikleri oluşur. Paketteki ana sürüm değişiklikleri genellikle Işlevler çalışma zamanının ana sürümündeki bir değişiklikle birlikte bulunur.  

Varsayılan paket tarafından yüklenen geçerli uzantılar kümesi, bu [Extensions. json dosyasında](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)numaralandırılır.
