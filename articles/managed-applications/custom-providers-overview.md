---
title: Azure özel kaynak sağlayıcıları genel bakış
description: Azure özel kaynak sağlayıcıları ve iş akışlarınızı sığdırmak için Azure API düzlemi genişletme hakkında bilgi edinin.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d25ef00adc307bae57da2c04d4472874f8d67bcd
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796075"
---
# <a name="azure-custom-resource-providers-overview"></a>Azure özel kaynak sağlayıcılara genel bakış

Azure özel kaynak sağlayıcıları, Azure için genişletilebilirlik platformudur. Azure deneyimini tanımladığınız varsayılan zenginleştirmek için kullanılan özel API'ler sağlar. Bu belgede açıklanmaktadır:

- Derleme ve Azure özel kaynak sağlayıcısı dağıtma nasıl.
- Mevcut iş akışlarına genişletmek için Azure özel kaynak sağlayıcıları nasıl.
- Başlamak için kılavuzları ve kod örneklerini nerede bulacağını.

![Özel sağlayıcı genel bakış](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Özel sağlayıcıları şu an genel Önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Özel kaynak sağlayıcıları neler yapabilirsiniz

Azure özel kaynak sağlayıcılarıyla elde edebileceğiniz bazı örnekler şunlardır:

- Azure Resource Manager REST API'si iç ve dış hizmetler içerecek şekilde genişletin.
- Mevcut Azure iş akışlarını en üstünde özel senaryolara olanak tanır.
- Azure Resource Manager şablonları denetimi ve etkili özelleştirin.

## <a name="what-is-a-custom-resource-provider"></a>Özel kaynak sağlayıcısı nedir

Azure özel kaynak sağlayıcıları, Azure ve uç nokta arasında bir sözleşme oluşturarak yapılır. Bu sözleşmeyi yeni kaynakları ve eylemleri aracılığıyla yeni bir kaynak listesi tanımlar **Microsoft.CustomProviders/resourceProviders**. Özel kaynak sağlayıcısı, ardından bu yeni API'leri azure'da açığa çıkarır. Azure özel kaynak sağlayıcıları, üç bölümden oluşur: özel kaynak sağlayıcısı **uç noktaları**ve özel kaynakların.

## <a name="how-to-build-custom-resource-providers"></a>Özel kaynak sağlayıcıları oluşturma

Özel kaynak sağlayıcıları, Azure ve uç noktaları arasında sözleşmeleri listesidir. Bu sözleşme, Azure, bir uç nokta ile nasıl etkileşim kuracağını açıklar. Kaynak sağlayıcısı davranır isteklerini ve yanıtlarını belirtilen gelen ve giden iletir ve bir proxy gibi **uç nokta**. Bir kaynak sağlayıcısı sözleşmeleri iki türü belirtebilirsiniz: [ **kaynak türlerinin** ](./custom-providers-resources-endpoint-how-to.md) ve [ **eylemleri**](./custom-providers-action-endpoint-how-to.md). Bu uç nokta tanımları etkinleştirilir. Bir uç nokta tanımı üç alandan oluşur: **adı**, **routingType**, ve **uç nokta**.

Örnek uç noktası:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Özellik | Gerekli | Açıklama
---|---|---
name | *Evet* | Uç nokta tanımı adı. Azure kullanıma sunma bu adı altında kendi API aracılığıyla ' /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders / {resourceProviderName} / {endpointDefinitionName}'
routingType | *Yok* | Sözleşme türü ile belirler **uç nokta**. Belirtilmezse, varsayılan olarak "Proxy'sine" olacaktır.
endpoint | *Evet* | İstekleri yönlendirmek için uç nokta. Bu isteğin tüm yan etkileri yanı sıra yanıt işler.

### <a name="building-custom-resources"></a>Özel kaynaklar oluşturma

**Kaynak türlerinin** Azure'a eklenen yeni özel kaynaklar açıklanır. Bu temel RESTful CRUD yöntemleri açığa. Bkz: [özel kaynakları oluşturma hakkında daha fazla bilgi](./custom-providers-resources-endpoint-how-to.md)

Örnek özel kaynak sağlayıcısı ile **kaynak türlerinin**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

API'leri, yukarıdaki örnek için Azure'a eklendi:

HttpMethod | Örnek URI | Açıklama
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Yeni bir kaynak oluşturmak için Azure REST API çağrısı.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Mevcut bir kaynağı silmek için Azure REST API çağrısı.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Mevcut bir kaynağı almak için Azure REST API çağrısı.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources? api sürümü 2018-09-01-preview = | Var olan kaynakların listesini almak için Azure REST API çağrısı.

### <a name="building-custom-actions"></a>Özel Eylemler oluşturma

**Eylemler** Azure'a eklenen yeni eylemler anlatılmaktadır. Bunlar kaynak sağlayıcısı üzerine ifşa edilememesi ya altında iç içe bir **resourceType**. Bkz: [özel eylemler oluşturma hakkında daha fazla bilgi](./custom-providers-action-endpoint-how-to.md)

Örnek özel kaynak sağlayıcısı ile **Eylemler**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

API'leri, yukarıdaki örnek için Azure'a eklendi:

HttpMethod | Örnek URI | Açıklama
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction? api sürümü 2018-09-01-preview = | Eylemini etkinleştirmek için Azure REST API çağrısı.

## <a name="looking-for-help"></a>Yardım

Azure özel kaynak sağlayıcısı geliştirme için ilgili sorularınız varsa, sorma [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Benzer bir soru soruların sorulmuş olduğunu ve sorularınızın, ilk önce yayınlayarak kontrol edin. Etiket Ekle ```azure-custom-providers``` hızlı yanıt almak için!

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, özel sağlayıcıları hakkında bilgi edindiniz. Özel bir sağlayıcı oluşturmak için sonraki makaleye gidin.

- [Hızlı Başlangıç: Azure özel kaynak sağlayıcısı oluşturursanız ve özel kaynakları dağıtma](./create-custom-provider.md)
- [Öğretici: Azure'da özel eylemler ve kaynakları oluşturma](./tutorial-custom-providers-101.md)
- [Nasıl Yapılır: Azure REST API'si için özel eylemler ekleme](./custom-providers-action-endpoint-how-to.md)
- [Nasıl Yapılır: Azure REST API'si için özel kaynak ekleme](./custom-providers-resources-endpoint-how-to.md)
