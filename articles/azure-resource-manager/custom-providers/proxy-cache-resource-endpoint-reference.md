---
title: Özel kaynak önbellek başvurusu
description: Azure özel kaynak sağlayıcıları için özel kaynak önbelleği başvurusu. Bu makale, önbellek özel kaynaklarını uygulayan uç noktalar için gereksinimlere geçer.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650389"
---
# <a name="custom-resource-cache-reference"></a>Özel kaynak önbelleği başvurusu

Bu makale, önbellek özel kaynaklarını uygulayan uç noktalar için gereksinimlere geçer. Azure özel kaynak sağlayıcıları hakkında bilginiz yoksa bkz. [özel kaynak sağlayıcılarına genel bakış](overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Önbellek kaynağı uç noktası tanımlama

**Yönlendirme türü** "proxy, önbellek" olarak belirtilerek bir proxy kaynağı oluşturulabilir.

Örnek özel kaynak sağlayıcısı:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
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

## <a name="building-proxy-resource-endpoint"></a>Proxy kaynak uç noktası derleniyor

Bir "proxy, önbellek" kaynak **uç noktası** uygulayan bir **uç nokta** , Azure 'daki yeni API için isteği ve yanıtı işlemelidir. Bu durumda, **ResourceType** ,, ve için yeni bir Azure Kaynak API 'si `PUT` oluşturur `GET` ve `DELETE` tek bir kaynakta CRUD 'yi gerçekleştirir ve `GET` tüm mevcut kaynakları almak için:

> [!NOTE]
> Azure API 'SI, ve istek yöntemlerini oluşturur `PUT` , `GET` `DELETE` ancak önbellek **uç noktasının** yalnızca ve işlemesi gerekir `PUT` `DELETE` .
> **Uç noktanın** de kullanılması önerilir `GET` .

### <a name="create-a-custom-resource"></a>Özel bir kaynak oluşturun

Azure API gelen Istek:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Bu istek daha sonra şu biçimdeki **uç noktaya** iletilir:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Benzer şekilde, **uç noktadan** gelen yanıt daha sonra müşteriye iletilir. Uç noktadan gelen yanıt şunu döndürmelidir:

- Geçerli bir JSON nesne belgesi. Tüm diziler ve dizeler üst nesne altında iç içe olmalıdır.
- `Content-Type`Üst bilgi, "Application/JSON; olarak ayarlanmalıdır charset = UTF-8 ".
- Özel kaynak sağlayıcısı, `name` `type` istek için, ve alanlarını geçersiz kılacaktır `id` .
- Özel kaynak sağlayıcısı yalnızca `properties` bir önbellek uç noktası için nesne altındaki alanları döndürür.

**Uç nokta** Yanıtıyla

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Özel `name` kaynak `id` `type` sağlayıcısı tarafından özel kaynak için,, ve alanları otomatik olarak oluşturulacaktır.

Azure özel kaynak sağlayıcısı yanıtı:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Özel bir kaynağı kaldırma

Azure API gelen Istek:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Bu istek daha sonra şu biçimdeki **uç noktaya** iletilir:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Benzer şekilde, **uç noktadan** gelen yanıt daha sonra müşteriye iletilir. Uç noktadan gelen yanıt şunu döndürmelidir:

- Geçerli bir JSON nesne belgesi. Tüm diziler ve dizeler üst nesne altında iç içe olmalıdır.
- `Content-Type`Üst bilgi, "Application/JSON; olarak ayarlanmalıdır charset = UTF-8 ".
- Azure özel kaynak sağlayıcısı, 200 düzeyinde bir yanıt döndürülürse öğeyi yalnızca kendi önbelleğinden kaldırır. Kaynak mevcut olmasa bile, **uç nokta** 204 döndürmelidir.

**Uç nokta** Yanıtıyla

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure özel kaynak sağlayıcısı yanıtı:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Özel bir kaynak alma

Azure API gelen Istek:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

İstek **uç noktaya** **iletilmeyecektir.**

Azure özel kaynak sağlayıcısı yanıtı:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Tüm özel kaynakları listeleme

Azure API gelen Istek:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Bu istek **uç noktaya** **iletilmeyecektir.**

Azure özel kaynak sağlayıcısı yanıtı:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure özel kaynak sağlayıcılarına genel bakış](overview.md)
- [Hızlı başlangıç: Azure özel kaynak sağlayıcısı oluşturma ve özel kaynaklar dağıtma](./create-custom-provider.md)
- [Öğretici: Azure 'da özel eylemler ve kaynaklar oluşturma](./tutorial-get-started-with-custom-providers.md)
- [Nasıl yapılır: Azure REST API özel eylemler ekleme](./custom-providers-action-endpoint-how-to.md)
- [Başvuru: özel kaynak proxy başvurusu](proxy-resource-endpoint-reference.md)
