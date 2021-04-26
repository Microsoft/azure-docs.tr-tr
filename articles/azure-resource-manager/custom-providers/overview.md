---
title: Özel sağlayıcılara genel bakış
description: Azure özel kaynak sağlayıcıları ve Azure API düzlemi 'ni iş akışlarınıza uyacak şekilde genişletmeyi öğrenin.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80398469"
---
# <a name="azure-custom-resource-providers-overview"></a>Azure özel kaynak sağlayıcılarına genel bakış

Azure özel kaynak sağlayıcıları, Azure 'a yönelik bir genişletilebilirlik platformudur. Varsayılan Azure deneyimini zenginleştirmek için kullanılabilecek özel API 'Ler tanımlamanıza olanak sağlar. Bu belgelerde şunları açıklar:

- Azure özel kaynak sağlayıcısı oluşturma ve dağıtma.
- Mevcut iş akışlarını genişletmek için Azure özel kaynak sağlayıcılarını kullanma.
- Başlamak için kılavuzlar ve kod örnekleri nerede bulunur.

![Özel sağlayıcıya genel bakış](./media/overview/overview.png)

> [!IMPORTANT]
> Özel sağlayıcılar Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Özel kaynak sağlayıcıları neler yapabilir?

Azure özel kaynak sağlayıcıları ile elde edebilmeniz için bazı örnekler şunlardır:

- Azure Resource Manager REST API iç ve dış hizmetleri içerecek şekilde genişletin.
- Mevcut Azure iş akışlarının üstünde özel senaryolar etkinleştirin.
- Azure Resource Manager şablonları denetimini ve efektini özelleştirin.

## <a name="what-is-a-custom-resource-provider"></a>Özel kaynak sağlayıcısı nedir?

Azure özel kaynak sağlayıcıları, Azure ile uç nokta arasında bir sözleşme oluşturularak yapılır. Bu sözleşme yeni kaynak ve eylemlerin bir listesini yeni bir kaynak olan **Microsoft. customproviders/resourceproviders** üzerinden tanımlar. Özel kaynak sağlayıcısı daha sonra bu yeni API 'Leri Azure 'da kullanıma sunacaktır. Azure özel kaynak sağlayıcıları üç bölümden oluşur: özel kaynak sağlayıcısı, **uç noktalar** ve özel kaynaklar.

## <a name="how-to-build-custom-resource-providers"></a>Özel kaynak sağlayıcıları oluşturma

Özel kaynak sağlayıcıları, Azure ile uç noktalar arasındaki sözleşmelerin bir listesidir. Bu sözleşme, Azure 'un bir uç nokta ile nasıl etkileşime gireceğini açıklar. Kaynak sağlayıcı bir ara sunucu gibi davranır ve belirtilen **uç noktadan** ve isteklerin isteklerini ve yanıtlarını iletecektir. Kaynak sağlayıcısı iki tür sözleşme belirtebilir: [**Resourcetypes**](./custom-providers-resources-endpoint-how-to.md) ve [**Actions**](./custom-providers-action-endpoint-how-to.md). Bunlar, uç nokta tanımları aracılığıyla etkinleştirilir. Bir uç nokta tanımı üç alandan oluşur: **ad**, **yönlendirme türü** ve **uç nokta**.

Örnek uç nokta:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Özellik | Gerekli | Açıklama
---|---|---
name | *Yes* | Uç nokta tanımının adı. Azure, bu adı '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/' altındaki API aracılığıyla kullanıma sunacaktır.<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName} '
routingType | *eşleşen* | **Uç nokta** ile sözleşme türünü belirler. Belirtilmemişse, varsayılan olarak "proxy" olur.
endpoint | *Yes* | İsteklerin yönlendirileceği uç nokta. Bu, yanıtın yanı sıra isteğin herhangi bir yan etkisini de işler.

### <a name="building-custom-resources"></a>Özel kaynaklar oluşturma

**Resourcetypes** , Azure 'a eklenen yeni özel kaynakları açıklıyor. Bunlar temel Restuz CRUD yöntemlerini sunar. [Özel kaynaklar oluşturma hakkında daha fazla](./custom-providers-resources-endpoint-how-to.md) bilgi

**Resourcetypes** Ile örnek özel kaynak sağlayıcısı:

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

Yukarıdaki örnek için Azure 'a eklenen API 'Ler:

HttpMethod | Örnek URI | Description
---|---|---
PUT | /Subscriptions/{SubscriptionID}/ResourceGroups/{resourcegroupname}/<br>sağlayıcılar/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? api-Version = 2018-09 -01-Preview | Yeni bir kaynak oluşturmak için Azure REST API çağrısı.
DELETE | /Subscriptions/{SubscriptionID}/ResourceGroups/{resourcegroupname}/<br>sağlayıcılar/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? api-Version = 2018-09 -01-Preview | Mevcut bir kaynağı silmek için Azure REST API çağrısı.
GET | /Subscriptions/{SubscriptionID}/ResourceGroups/{resourcegroupname}/<br>sağlayıcılar/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? api-Version = 2018-09 -01-Preview | Mevcut bir kaynağı almak için Azure REST API çağrısı.
GET | /Subscriptions/{SubscriptionID}/ResourceGroups/{resourcegroupname}/<br>sağlayıcılar/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources? api-Version = 2018-09 -01-Preview | Mevcut kaynakların listesini almak için Azure REST API çağrısı.

### <a name="building-custom-actions"></a>Özel eylemler oluşturma

**Eylemler** , Azure 'a eklenen yeni eylemleri anlatmaktadır. Bunlar, kaynak sağlayıcının üstünde veya bir **ResourceType** altında iç içe bulunabilir. [Özel eylemler oluşturma hakkında daha fazla](./custom-providers-action-endpoint-how-to.md) bilgi

**Eylemleri** olan örnek özel kaynak sağlayıcısı:

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

Yukarıdaki örnek için Azure 'a eklenen API 'Ler:

HttpMethod | Örnek URI | Description
---|---|---
POST | /Subscriptions/{SubscriptionID}/ResourceGroups/{resourcegroupname}/<br>sağlayıcılar/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction? api-Version = 2018-09 -01-Preview | Eylemi etkinleştirmek için Azure REST API çağrısı.

## <a name="looking-for-help"></a>Yardım aranıyor

Azure özel kaynak sağlayıcısı geliştirme hakkında sorularınız varsa [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)yapmayı deneyin. Benzer bir soru zaten istendi ve yanıtlamış olabilir, bu nedenle göndermeden önce kontrol edin. ```azure-custom-providers```Hızlı bir yanıt almak için etiketi ekleyin!

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, özel sağlayıcılar hakkında bilgi edindiniz. Özel bir sağlayıcı oluşturmak için sonraki makaleye gidin.

- [Hızlı başlangıç: Azure özel kaynak sağlayıcısı oluşturma ve özel kaynaklar dağıtma](./create-custom-provider.md)
- [Öğretici: Azure 'da özel eylemler ve kaynaklar oluşturma](./tutorial-get-started-with-custom-providers.md)
- [Nasıl yapılır: Azure REST API özel eylemler ekleme](./custom-providers-action-endpoint-how-to.md)
- [Nasıl yapılır: Azure REST API özel kaynak ekleme](./custom-providers-resources-endpoint-how-to.md)
