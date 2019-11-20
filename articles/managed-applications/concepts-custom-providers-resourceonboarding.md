---
title: Azure özel sağlayıcılar kaynak ekleme
description: Diğer Azure kaynak türlerine yönetim veya yapılandırma uygulamak için Azure özel sağlayıcılarını kullanarak kaynak ekleme işlemini gerçekleştirme hakkında bilgi edinin.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 65e0f795a2b0efa327aec02c01cdb3706bf91d29
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818774"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Azure özel sağlayıcılar kaynak eklemeye genel bakış

Azure özel sağlayıcılar kaynak ekleme, Azure Kaynak türleri için bir genişletilebilirlik modelidir. Mevcut Azure kaynakları genelinde işlem veya yönetim işlemlerini ölçeklendirerek uygulamanıza olanak tanır. Daha fazla bilgi için bkz. [Azure özel sağlayıcılarının Azure 'U nasıl genişletebilen](./custom-providers-overview.md). Bu makalede şunları açıklanmaktadır:

- Kaynak ekleme 'nin yapabilecekleri durum.
- Kaynak ekleme temelleri ve kullanımı.
- Başlamak için kılavuzlar ve kod örnekleri nerede bulunur.

> [!IMPORTANT]
> Özel sağlayıcılar Şu anda genel önizlemededir.
> Bu önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve bunu üretim iş yükleri için önermiyoruz. Bazı özellikler desteklenmeyebilir veya kısıtlı özelliklere sahip olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Kaynak ekleme ne yapabilir?

[Azure özel sağlayıcılar özel kaynaklarına](./custom-providers-resources-endpoint-how-to.md)benzer şekilde kaynak ekleme, bir uç noktaya "ekleme" isteklerinin proxy 'sini oluşturacak bir sözleşmeyi tanımlar. Özel kaynakların aksine, kaynak ekleme yeni bir kaynak türü oluşturmaz. Bunun yerine, mevcut kaynak türlerinin uzantısına izin verir. Kaynak ekleme Azure Ilkesiyle birlikte çalışarak, kaynakların yönetimi ve yapılandırması, ölçeklendirilmesine göre yapılabilir. Kaynak ekleme iş akışlarına ilişkin bazı örnekler:

- Sanal makine uzantılarına yükler ve bunları yönetin.
- Azure depolama hesaplarında Varsayılanları karşıya yükleyin ve yapılandırın.
- Ölçek sırasında taban çizgisi tanılama ayarlarını etkinleştirin.

## <a name="resource-onboarding-basics"></a>Kaynak ekleme temelleri

Microsoft. CustomProviders/resourceProviders ve Microsoft. CustomProviders/Association kaynak türlerini kullanarak Azure özel sağlayıcıları aracılığıyla kaynak ekleme 'yi yapılandırırsınız. Özel bir sağlayıcı için kaynak eklemeyi etkinleştirmek üzere, yapılandırma işlemi sırasında, "Extension" adlı bir **Routingtype** ile "Association" adlı bir **ResourceType** oluşturun. Microsoft. CustomProviders/Association ve Microsoft. CustomProviders/resourceProviders 'ın aynı kaynak grubuna ait olması gerekmez.

Örnek bir Azure özel sağlayıcısı aşağıda verilmiştir:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Özellik | Gerekli mi? | Açıklama
---|---|---
ad | Evet | Uç nokta tanımının adı. Kaynak ekleme için, adın "ilişkilendirmeler" olması gerekir.
routingType | Evet | Uç nokta ile sözleşmenin türünü belirler. Kaynak ekleme için geçerli **Routingtypes** "proxy, önbellek, uzantı" ve "Web kancası, önbellek, uzantı" dır.
endpoint | Evet | İsteklerin yönlendirileceği uç nokta. Bu işlem, isteğin tüm yan etkilerini ve yanıtını işler.

İlişki kaynak türü ile özel sağlayıcıyı oluşturduktan sonra, Microsoft. CustomProviders/ilişkilendirmelerini kullanarak hedefleyebilirsiniz. Microsoft. CustomProviders/Association, diğer herhangi bir Azure kaynağını genişletebilen bir uzantı kaynağıdır. Microsoft. CustomProviders/Association 'ın bir örneği oluşturulduğunda, geçerli bir Microsoft. CustomProviders/resourceProviders veya Microsoft. Solutions/Applications kaynak KIMLIĞI olması gereken bir **Targetresourceıd**özelliği alır. Bu durumlarda, istek oluşturduğunuz Microsoft. CustomProviders/resourceProviders örneğinde ilişkilendirmeler kaynak türüne iletilir.

> [!NOTE]
> **Targetresourceıd**olarak bir Microsoft. Solutions/APPLICATIONS kaynak kimliği sağlanmışsa, yönetilen kaynak grubunda "public" adıyla dağıtılan bir Microsoft. Customproviders/resourceproviders olmalıdır.

Örnek Azure özel sağlayıcıları ilişkilendirmesi:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Özellik | Gerekli mi? | Açıklama
---|---|---
Targetresourceıd | Evet | Microsoft. CustomProviders/resourceProviders veya Microsoft. Solutions/Applications kaynak KIMLIĞI.

## <a name="how-to-use-resource-onboarding"></a>Kaynak ekleme 'yi kullanma

Kaynak ekleme, Microsoft. CustomProviders/Association uzantı kaynağıyla diğer kaynakları genişleterek işe yarar. Aşağıdaki örnekte, istek bir sanal makine için yapılır, ancak herhangi bir kaynak genişletilebilir.

İlk olarak, bir ilişki kaynak türü ile özel bir sağlayıcı kaynağı oluşturmanız gerekir. Bu işlem, özel sağlayıcıyı hedefleyen karşılık gelen bir Microsoft. CustomProviders/Association kaynağı oluşturulduğunda kullanılacak geri çağırma URL 'sini bildirir.

Örnek Microsoft. CustomProviders/resourceProviders oluşturma isteği:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

Özel sağlayıcıyı oluşturduktan sonra, diğer kaynakları hedefleyebilir ve özel sağlayıcının yan etkilerini bunlara uygulayabilirsiniz.

Örnek Microsoft. CustomProviders/Association oluşturma isteği:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Daha sonra bu istek, bu formda **Targetresourceıd** tarafından başvurulan, oluşturduğunuz özel sağlayıcıda belirtilen uç noktaya iletilir:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Uç noktanın bir uygulama/JSON `Content-Type` ve geçerli bir JSON yanıt gövdesi ile yanıt vermesi gerekir. JSON 'ın **Özellikler** nesnesi altında döndürülen alanlar ilişkilendirme dönüş yanıtına eklenecektir.

## <a name="getting-help"></a>Yardım alma

Azure özel kaynak sağlayıcıları geliştirme hakkında sorularınız varsa [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)üzerinde soru sormayı deneyin. Benzer bir soru zaten yanıtlanmış olabilir, bu nedenle göndermeden önce kontrol edin. Hızlı bir yanıt almak için etiketi ```azure-custom-providers``` ekleyin!

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, özel sağlayıcılar hakkında bilgi edindiniz. Daha fazla bilgi için şu makalelere göz atın:

- [Öğretici: özel sağlayıcılarla kaynak ekleme](./tutorial-custom-providers-resource-onboarding.md)
- [Öğretici: Azure 'da özel eylemler ve kaynaklar oluşturma](./tutorial-custom-providers-101.md)
- [Hızlı başlangıç: özel kaynak sağlayıcısı oluşturma ve özel kaynaklar dağıtma](./create-custom-provider.md)
- [Nasıl yapılır: Azure REST API özel eylemler ekleme](./custom-providers-action-endpoint-how-to.md)
- [Nasıl yapılır: Azure REST API özel kaynak ekleme](./custom-providers-resources-endpoint-how-to.md)
