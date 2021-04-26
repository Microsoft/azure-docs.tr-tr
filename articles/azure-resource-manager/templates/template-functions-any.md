---
title: Şablon işlevleri-any
description: Türleri dönüştürmek için Bıcep ' de kullanılabilir olan herhangi bir işlevi açıklar.
ms.topic: conceptual
author: tfitzmac
ms.author: tomfitz
ms.service: azure-resource-manager
ms.subservice: templates
ms.date: 03/02/2021
ms.openlocfilehash: b0cb51c9a79d1100de7f1ef32fe326eddcdd6dcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746914"
---
# <a name="any-function-for-bicep"></a>Bıcep için herhangi bir işlev

Bıcep `any()` , bıcep türü sisteminde tür hatalarını çözümlemek için çağrılan bir işlevi destekler. Bu işlevi, sağladığınız değerin biçimi sistem tarafından beklenen şekilde eşleşmediği zaman kullanın. Örneğin, özellik bir sayı gerektiriyorsa, ancak bunun gibi bir dize olarak sağlamanız gerekir `'0.5'` . `any()`Tür sistemi tarafından bildirilen hatayı bastırmak için işlevini kullanın.

Bu işlev, Azure Resource Manager şablonu çalışma zamanında yok. Yalnızca Bıcep tarafından kullanılır ve oluşturulan şablon için JSON 'da yer verilmez.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="any"></a>herhangi biri

`any(value)`

Herhangi bir veri türüyle uyumlu bir değer döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| değer | Yes | tüm türler | Uyumlu bir türe dönüştürülecek değer. |

### <a name="return-value"></a>Döndürülen değer

Bir formdaki, herhangi bir veri türüyle uyumlu olan değer.

### <a name="examples"></a>Örnekler

Aşağıdaki örnek şablon, `any()` sayısal değerleri dizeler olarak sağlamak için işlevinin nasıl kullanılacağını gösterir.

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

İşlev, Bıcep içindeki atanmış herhangi bir değer üzerinde çalışır. Aşağıdaki örnek, bir `any()` bağımsız değişken olarak Üçlü ifade ile kullanılır.  

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>Sonraki adımlar

İşlevin daha karmaşık kullanımları için `any()` aşağıdaki örneklere bakın:

* [Belirli adlar gerektiren alt kaynaklar](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [Kaynak özelliği, var olsa da kaynağın türünde tanımlı değil](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

