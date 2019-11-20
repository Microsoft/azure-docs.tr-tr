---
title: Özel kaynak önbellek başvurusu
description: Azure özel kaynak sağlayıcıları için özel kaynak önbellek başvurusu. Bu makalede, önbellek özel kaynaklar uygulama uç noktaları için gereksinimleri üzerinden geçer.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 2970e69e825dacb6f548b3e66a830f221ece0b1c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795236"
---
# <a name="custom-resource-cache-reference"></a>Özel kaynak önbellek başvurusu

Bu makalede, önbellek özel kaynaklar uygulama uç noktaları için gereksinimleri üzerinden geçer. Azure özel kaynak sağlayıcılarıyla alışkın değilseniz bkz [genel bakış'ta özel kaynak sağlayıcıları](./custom-providers-overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Bir önbellek kaynak uç noktası tanımlama

Proxy kaynak belirterek oluşturulabilir **routingType** "Proxy önbellek".

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

## <a name="building-proxy-resource-endpoint"></a>Yapı proxy kaynak uç noktası

Bir **uç nokta** uygulayan bir "Proxy, önbellek" kaynak **uç nokta** azure'da yeni API için istek ve yanıt işlemesi gerekir. Bu durumda, **resourceType** için yeni Azure kaynak API oluşturacak `PUT`, `GET`, ve `DELETE` CRUD tek bir kaynak üzerinde gerçekleştirilecek yanı `GET` var olan tüm kaynakları almak için:

> [!NOTE]
> Azure API istek yöntemleri oluşturacak `PUT`, `GET`, ve `DELETE`, ancak önbellek **uç nokta** işlemek için yalnızca gereken `PUT` ve `DELETE`.
> Önerilir **uç nokta** ayrıca uygulayan `GET`.

### <a name="create-a-custom-resource"></a>Özel kaynak oluştur

Azure API gelen isteği:

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

Bu istek için ardından iletilir **uç nokta** biçiminde:

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

Benzer şekilde, yanıttan **uç nokta** ardından müşteriye geri iletilir. Uç noktasından yanıt döndürmesi gerekir:

- Geçerli bir JSON nesnesi belgesi. Tüm diziler ve dizeleri bir üst nesnenin altında iç içe.
- `Content-Type` Başlığı "application/json; ayarlanmalıdır Charset = utf-8 ".
- Özel kaynak sağlayıcısı üzerine yazar `name`, `type`, ve `id` istek için alanları.
- Özel kaynak sağlayıcısı altındaki alanlar yalnızca döndürür `properties` önbellek uç noktası için nesne.

**Uç nokta** yanıt:

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

`name`, `id`, Ve `type` alanları otomatik olarak oluşturulacak özel kaynak için özel kaynak sağlayıcısı tarafından.

Azure özel kaynak sağlayıcı yanıtı:

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

### <a name="remove-a-custom-resource"></a>Özel bir kaynak Kaldır

Azure API gelen isteği:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Bu istek için ardından iletilir **uç nokta** biçiminde:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Benzer şekilde, yanıttan **uç nokta** ardından müşteriye geri iletilir. Uç noktasından yanıt döndürmesi gerekir:

- Geçerli bir JSON nesnesi belgesi. Tüm diziler ve dizeleri bir üst nesnenin altında iç içe.
- `Content-Type` Başlığı "application/json; ayarlanmalıdır Charset = utf-8 ".
- 200 düzeyinde yanıt döndürüldüğünde Azure özel kaynak sağlayıcısı öğeyi yalnızca önbelleğinden kaldırır. Kaynak mevcut değilse, **uç nokta** 204 döndürmelidir.

**Uç nokta** yanıt:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure özel kaynak sağlayıcı yanıtı:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Özel bir kaynak alma

Azure API gelen isteği:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

İstek olacak **değil** iletilmesi için **uç nokta**.

Azure özel kaynak sağlayıcı yanıtı:

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

Azure API gelen isteği:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Bu istek olacak **değil** iletilmesi için **uç nokta**.

Azure özel kaynak sağlayıcı yanıtı:

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

- [Azure özel kaynak sağlayıcıları hakkında genel bakış](./custom-providers-overview.md)
- [Hızlı Başlangıç: Azure özel kaynak sağlayıcısı oluşturursanız ve özel kaynakları dağıtma](./create-custom-provider.md)
- [Öğretici: Azure'da özel eylemler ve kaynakları oluşturma](./tutorial-custom-providers-101.md)
- [Nasıl Yapılır: Azure REST API'si için özel eylemler ekleme](./custom-providers-action-endpoint-how-to.md)
- [Başvuru: Özel kaynak Proxy başvurusu](./custom-providers-proxy-resource-endpoint-reference.md)
