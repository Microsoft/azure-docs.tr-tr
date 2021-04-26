---
author: baanders
description: Azure dijital TWINS örneğine model yüklemek için dosya ekleme
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: add49cabaece1187cb9bcda3a94c92feb08b2439
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304312"
---
Model şuna benzer:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

**Bu modeli TWINS örneğinizi karşıya yüklemek** Için AŞAĞıDAKI Azure CLI komutunu çalıştırın, bu, yukarıdaki modeli satır içi JSON olarak yükler. Komutunu tarayıcınızda [Azure Cloud Shell](../articles/cloud-shell/overview.md) ( **Bash** ORTAMıNı kullanın) veya CLI [yerel olarak yüklüyse](/cli/azure/install-azure-cli)makinenizde çalıştırabilirsiniz.

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```