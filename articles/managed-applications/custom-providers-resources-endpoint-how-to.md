---
title: Azure REST API'si için özel kaynak ekleme
description: Azure REST API'si için özel kaynakları eklemeyi öğrenin. Bu makalede gereksinimleri ve özel kaynaklar uygulamak istediğiniz uç noktaları için en iyi yol gösterir.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: a3cd1fe69a0d99f9faf3a451f76a3a420d713711
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795209"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Azure REST API'si için özel kaynak ekleme

Bu makalede, özel kaynaklar uygulayan Azure özel kaynak sağlayıcısı uç noktaları oluşturmak için en iyi yöntemler ve gereksinimleri üzerinden geçer. Azure özel kaynak sağlayıcılarıyla alışkın değilseniz bkz [genel bakış'ta özel kaynak sağlayıcıları](./custom-providers-overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Bir kaynak uç noktası tanımlama

Bir **uç nokta** temel alınan sözleşmenin ve Azure arasında uygulayan bir hizmete işaret eden bir URL. Uç nokta özel kaynak sağlayıcısı tanımlanır ve genel olarak erişilebilen herhangi bir URL olabilir. Aşağıdaki örnekte sahip bir **resourceType** adlı `myCustomResource` tarafından uygulanan `endpointURL`.

Örnek **ResourceProvider**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
        "routingType": "Proxy, Cache",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-a-resource-endpoint"></a>Bir kaynak uç noktası oluşturma

Bir **uç nokta** uygulayan bir **resourceType** azure'da yeni API için istek ve yanıt işlemesi gerekir. Özel kaynak sağlayıcısı ile bir **resourceType** olan oluşturuldu, bunu yeni bir API kümesi Azure'da oluşturur. Bu durumda, **resourceType** için yeni Azure kaynak API oluşturacak `PUT`, `GET`, ve `DELETE` CRUD tek bir kaynak üzerinde gerçekleştirilecek yanı `GET` var olan tüm kaynakları almak için:

Tek kaynak işlemek (`PUT`, `GET`, ve `DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Tüm kaynakları almak (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Özel kaynaklar için iki tür özel kaynak sağlayıcıları teklif **routingTypes**: "`Proxy`"ve"`Proxy, Cache`".

### <a name="proxy-routing-type"></a>Proxy yönlendirme türü

"`Proxy`" **RoutingType** tüm yöntemleri için istek proxy'leri **uç nokta** özel kaynak sağlayıcısı belirtildi. Ne zaman kullanılacağı "`Proxy`":

- Yanıt üzerinde tam denetim gereklidir.
- Sistemlerini mevcut kaynaklar ile tümleştirme.

Daha fazla bilgi için "`Proxy`" kaynaklar bkz [özel kaynak proxy başvurusu](./custom-providers-proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>Proxy önbellek yönlendirme türü

"`Proxy, Cache`" **RoutingType** proxy'leri yalnızca `PUT` ve `DELETE` istek yöntemlere **uç nokta** özel kaynak sağlayıcısı belirtilmiş. Özel kaynak sağlayıcısı otomatik olarak iade `GET` istekleri alarak önbelleğinde ne depoladığı üzerinde. Özel bir kaynak önbellek ile işaretlenmişse, özel kaynak sağlayıcısı ayrıca Ekle / API'leri Azure uyumlu hale getirmek için yanıt alanların üzerine yazılır. Ne zaman kullanılacağı "`Proxy, Cache`":

- Mevcut kaynak olan yeni bir sistem oluşturma.
- Mevcut Azure ekosistemiyle çalışır.

Daha fazla bilgi için "`Proxy, Cache`" kaynaklar bkz [özel kaynak önbellek başvurusu](./custom-providers-proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Özel bir kaynak oluşturma

Bir özel kaynak sağlayıcısının dışına özel bir kaynak oluşturma başlıca iki yolu vardır:

- Azure CLI
- Azure Resource Manager şablonları

### <a name="azure-cli"></a>Azure CLI

Özel bir kaynak oluşturun:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

Parametre | Gerekli | Açıklama
---|---|---
tam-nesnesi | *Evet* | Özellikleri nesnesi konum, etiketler, sku ve/veya planı gibi diğer seçenekleri içerip içermediğini belirtir.
id | *Evet* | Özel kaynak kaynak kimliği. Bu kapatıp bulunmalıdır **ResourceProvider**
properties | *Evet* | Gönderilecek istek gövdesi **uç nokta**.

Özel bir Azure kaynağı silin:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametre | Gerekli | Açıklama
---|---|---
id | *Evet* | Özel kaynak kaynak kimliği. Bu kapatıp bulunmalıdır **ResourceProvider**.

Özel bir Azure kaynağı alın:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametre | Gerekli | Açıklama
---|---|---
id | *Evet* | Özel kaynak kaynak kimliği. Bu kapatıp bulunmalıdır **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Azure Resource Manager Şablonu

> [!NOTE]
> Kaynak gerektiren yanıtta uygun bir yer `id`, `name`, ve `type` gelen **uç nokta**.

Azure Resource Manager şablonları gerektiren `id`, `name`, ve `type` doğru aşağı akış uç noktasından döndürülür. Döndürülen kaynak yanıtı biçiminde olmalıdır:

Örnek **uç nokta** yanıt:

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Azure Resource Manager şablonu örneği:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

Parametre | Gerekli | Açıklama
---|---|---
resourceTypeName | *Evet* | **Adı** , **resourceType** özel sağlayıcıda tanımlanmış.
resourceProviderName | *Evet* | Özel kaynak sağlayıcı örneği adı.
customResourceName | *Evet* | Özel kaynak adı.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure özel kaynak sağlayıcıları hakkında genel bakış](./custom-providers-overview.md)
- [Hızlı Başlangıç: Azure özel kaynak sağlayıcısı oluşturursanız ve özel kaynakları dağıtma](./create-custom-provider.md)
- [Öğretici: Azure'da özel eylemler ve kaynakları oluşturma](./tutorial-custom-providers-101.md)
- [Nasıl Yapılır: Azure REST API'si için özel eylemler ekleme](./custom-providers-action-endpoint-how-to.md)
- [Başvuru: Özel kaynak Proxy başvurusu](./custom-providers-proxy-resource-endpoint-reference.md)
- [Başvuru: Özel kaynak önbellek başvurusu](./custom-providers-proxy-cache-resource-endpoint-reference.md)
