---
title: Azure Media Services v3 için bulutlar ve bölgeler
description: Bu makale, uç noktalar için kullanılan URL 'Ler ve bölgelere yönelik kodlar hakkında konuşur.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: 74f30ebb766ea34603c0a30455837c47222967c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954336"
---
# <a name="regional-code-names-and-endpoints"></a>Bölgesel kod adları ve uç noktalar

### <a name="region-code-name"></a>Bölge kodu adı

Bir komutta veya istekte **konum** parametresi kullanıldığında, **konum** değeri olarak bölge kodu adını belirtmeniz gerekir. Hesabınızın bulunduğu bölgenin kod adını almak ve çağrın ' a yönlendirilmek için, Azure CLı 'de aşağıdaki satırı çalıştırabilirsiniz.

```azurecli-interactive
az account list-locations
```

Yukarıda gösterilen satırı çalıştırdığınızda tüm Azure bölgelerinin bir listesini alırsınız. Aradığınız *DisplayName* 'e sahip Azure bölgesine gidin ve **konum** parametresi için *ad* değerini kullanın.

Örneğin, Azure bölgesi Batı ABD 2 için (aşağıda görüntülenir), **konum** parametresi için "westus2" kullanacaksınız.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Uç Noktalar  

Aşağıdaki uç noktalar, farklı ulusal Azure bulutlarından Media Services hesaplarına bağlanırken bilmemiz için önemlidir.

### <a name="global-azure"></a>Küresel Azure

| Hizmet | Uç Nokta |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Kimlik Doğrulaması | `https://login.microsoftonline.com/` |
| Belirteç hedef kitlesi | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Kamu

| Hizmet | Uç Nokta |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Kimlik Doğrulaması | `https://login.microsoftonline.us/` |
| Belirteç hedef kitlesi | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure Almanya

> [!NOTE]
> Azure Almanya uç noktaları yalnızca Almanya 'daki Sogeign bulutları için geçerlidir.

| Hizmet | Uç Nokta |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Kimlik Doğrulaması | `https://login.microsoftonline.de/` |
| Belirteç hedef kitlesi | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

| Hizmet | Uç Nokta |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Kimlik Doğrulaması | `https://login.chinacloudapi.cn/` |
| Belirteç hedef kitlesi |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Ayrıca bkz.

* [Azure bölgeleri](https://azure.microsoft.com/global-infrastructure/regions/)
* [Bölgesel kod adları ve uç noktalar](azure-regions-code-names.md)
* [Azure coğrafyaları](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Sonraki adımlar

[Media Services v3 genel bakış](media-services-overview.md)
