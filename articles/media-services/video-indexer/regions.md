---
title: Video Indexer kullanılabildiği bölgeler-Azure
titleSuffix: Azure Media Services
description: Bu makalede Video Indexer kullanılabildiği Azure bölgeleri ele sunulmuştur.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b67c89e02091d57b4f2dc901005c2dae36deafc2
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838195"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Video Indexer var olan Azure bölgeleri

Video Indexer API 'Leri, çağrının yönlendirileceği Azure bölgesine ayarlamanız gereken bir **konum** parametresi içerir. Bu [, video Indexer kullanılabildiği bir Azure bölgesi](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)olmalıdır.

## <a name="locations"></a>Konumlar

**Konum** parametresine, değeri olarak Azure bölge kodu adı verilmelidir. Önizleme modunda Video Indexer kullanıyorsanız, *"deneme"* değerini değer olarak koymanız gerekir. Aksi takdirde, hesabınızın bulunduğu Azure bölgesinin kod adını almak ve çağrın ' a yönlendirilmek için, [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)'de aşağıdaki satırı çalıştırabilirsiniz:

```bash
az account list-locations
```

Yukarıda gösterilen satırı çalıştırdığınızda tüm Azure bölgelerinin bir listesini alırsınız. Aradığınız *DisplayName* 'e sahip Azure bölgesine gidin ve **konum** parametresi için *ad* değerini kullanın.

Örneğin, Azure bölgesi Batı ABD 2 için (aşağıda görüntülenir), **konum** parametresi için "westus2" kullanacaksınız.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Sonraki adımlar

- [API 'Leri kullanarak dil modelini özelleştirme](customize-language-model-with-api.md)
- [API 'Leri kullanarak markalar modelini özelleştirme](customize-brands-model-with-api.md)
- [API 'Leri kullanarak kişi modelini özelleştirme](customize-person-model-with-api.md)
