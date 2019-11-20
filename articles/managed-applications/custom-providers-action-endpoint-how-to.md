---
title: Azure REST API'si için özel eylemler ekleme
description: Azure REST API'si için özel eylemler eklemeyi öğrenin. Bu makalede gereksinimleri ve özel eylemler uygulamak istediğiniz uç noktaları için en iyi yol gösterir.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6fbd20c201e1b141b7276e3283599b00cdefd118
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795292"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Azure REST API'si için özel eylemler ekleme

Bu makalede, özel eylemleri uygulayan Azure özel kaynak sağlayıcısı uç noktaları oluşturmak için en iyi yöntemler ve gereksinimleri üzerinden geçer. Azure özel kaynak sağlayıcılarıyla alışkın değilseniz bkz [genel bakış'ta özel kaynak sağlayıcıları](./custom-providers-overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Bir eylem uç noktası tanımlama

Bir **uç nokta** temel alınan sözleşmenin ve Azure arasında uygulayan bir hizmete işaret eden bir URL. Uç nokta özel kaynak sağlayıcısı tanımlanır ve genel olarak erişilebilen herhangi bir URL olabilir. Aşağıdaki örnekte sahip bir **eylem** adlı `myCustomAction` tarafından uygulanan `endpointURL`.

Örnek **ResourceProvider**:

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
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-an-action-endpoint"></a>Bir eylem uç nokta oluşturma

Bir **uç nokta** uygulayan bir **eylem** azure'da yeni API için istek ve yanıt işlemesi gerekir. Özel kaynak sağlayıcısı ile bir **eylem** olan oluşturuldu, bunu yeni bir API kümesi Azure'da oluşturur. Bu durumda, eylem için yeni Azure işlem API'si oluşturacak `POST` çağırır:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API gelen isteği:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Bu istek için ardından iletilir **uç nokta** biçiminde:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Benzer şekilde, yanıttan **uç nokta** ardından müşteriye geri iletilir. Uç noktasından yanıt döndürmesi gerekir:

- Geçerli bir JSON nesnesi belgesi. Tüm diziler ve dizeleri bir üst nesnenin altında iç içe.
- `Content-Type` Başlığı "application/json; ayarlanmalıdır Charset = utf-8 ".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Azure özel kaynak sağlayıcı yanıtı:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Özel bir eylem çağırma

Bir özel kaynak sağlayıcısının dışına özel bir eylem çağırma başlıca iki yolu vardır:

- Azure CLI
- Azure Resource Manager şablonları

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Parametre | Gerekli | Açıklama
---|---|---
Eylem | *Evet* | Tanımlı eylem adını **ResourceProvider**.
kimlikleri | *Evet* | Kaynak Kimliğini **ResourceProvider**.
istek gövdesi | *Yok* | Gönderilecek istek gövdesi **uç nokta**.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager Şablonu

> [!NOTE]
> Eylemler Azure Resource Manager şablonlarında destek sınırlıdır. Bir şablon içinde çağrılacak eylemi için sırayla içermelidir [ `list` ](../azure-resource-manager/resource-group-template-functions-resource.md#list) adı ön eki.

Örnek **ResourceProvider** listesi eylemi:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Azure Resource Manager şablonu örneği:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Parametre | Gerekli | Açıklama
---|---|---
Resourceıdentifier | *Evet* | Kaynak Kimliğini **ResourceProvider**.
apiVersion | *Evet* | Kaynak çalışma zamanı API sürümü. Bu, her zaman "2018-09-01-preview" olmalıdır.
functionValues | *Yok* | Gönderilecek istek gövdesi **uç nokta**.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure özel kaynak sağlayıcıları hakkında genel bakış](./custom-providers-overview.md)
- [Hızlı Başlangıç: Azure özel kaynak sağlayıcısı oluşturursanız ve özel kaynakları dağıtma](./create-custom-provider.md)
- [Öğretici: Azure'da özel eylemler ve kaynakları oluşturma](./tutorial-custom-providers-101.md)
- [Nasıl Yapılır: Azure REST API'si için özel kaynak ekleme](./custom-providers-resources-endpoint-how-to.md)
