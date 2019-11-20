---
title: Özel kaynak ara sunucu başvurusu
description: Azure özel kaynak sağlayıcıları için özel kaynak proxy başvurusu. Bu makalede proxy özel kaynaklar uygulama uç noktaları gereksinimlerini inceleyin.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: fe470165d11b24ffb1df704ecaa9804663fc9e5c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796026"
---
# <a name="custom-resource-proxy-reference"></a>Özel kaynak Proxy başvurusu

Bu makalede proxy özel kaynaklar uygulama uç noktaları gereksinimlerini inceleyin. Azure özel kaynak sağlayıcılarıyla alışkın değilseniz bkz [genel bakış'ta özel kaynak sağlayıcıları](./custom-providers-overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Proxy kaynak uç noktası tanımlama

Proxy kaynak belirterek oluşturulabilir **routingType** "Proxy".

Örnek özel kaynak sağlayıcısı:

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
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>Yapı proxy kaynak uç noktası

Bir **uç nokta** uygulayan bir "Proxy" kaynak **uç nokta** azure'da yeni API için istek ve yanıt işlemesi gerekir. Bu durumda, **resourceType** için yeni Azure kaynak API oluşturacak `PUT`, `GET`, ve `DELETE` CRUD tek bir kaynak üzerinde gerçekleştirilecek yanı `GET` var olan tüm kaynakları almak için.

> [!NOTE]
> `id`, `name`, Ve `type` alanlar gerekli değildir, ancak özel kaynağın mevcut Azure ekosistemi ile tümleştirmek için gereklidir.

Örnek kaynak:

``` JSON
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

Parametre başvurusu:

Özellik | Örnek | Açıklama
---|---|---
name | '{myCustomResourceName}' | Özel kaynak adı.
türü | }' | Kaynak türü ad alanı.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources / {myCustomResourceName}' | Kaynak kimliği.

### <a name="create-a-custom-resource"></a>Özel kaynak oluştur

Azure API gelen isteği:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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

**Uç nokta** yanıt:

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

Benzer şekilde yanıtı **uç nokta** ardından müşteriye geri iletilir. Uç noktasından yanıt döndürmesi gerekir:

- Geçerli JSON nesnesi belgesi. Tüm diziler ve dizeleri bir üst nesnenin altında iç içe.
- `Content-Type` Başlığı "application/json; ayarlanmalıdır Charset = utf-8 ".

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

Bu istek için ardından iletilir **uç nokta** biçiminde:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Benzer şekilde, yanıttan **uç nokta** ardından müşteriye geri iletilir. Uç noktasından yanıt döndürmesi gerekir:

- Geçerli bir JSON nesnesi belgesi. Tüm diziler ve dizeleri bir üst nesnenin altında iç içe.
- `Content-Type` Başlığı "application/json; ayarlanmalıdır Charset = utf-8 ".

**Uç nokta** yanıt:

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

Bu istek için ardından iletilir **uç nokta** biçiminde:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Benzer şekilde, yanıttan **uç nokta** ardından müşteriye geri iletilir. Uç noktasından yanıt döndürmesi gerekir:

- Geçerli bir JSON nesnesi belgesi. Tüm diziler ve dizeleri bir üst nesnenin altında iç içe.
- `Content-Type` Başlığı "application/json; ayarlanmalıdır Charset = utf-8 ".
- Kaynak listesi altında en üst düzey yerleştirilmelidir `value` özelliği.

**Uç nokta** yanıt:

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
- [Başvuru: Özel kaynak önbellek başvurusu](./custom-providers-proxy-cache-resource-endpoint-reference.md)
