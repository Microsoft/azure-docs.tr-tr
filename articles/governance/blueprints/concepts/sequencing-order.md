---
title: Dağıtım sırası sırasını anlayın
description: Şema yapıtlarının bir şema atama sırasında dağıtıldığı ve dağıtım sırasının nasıl özelleştirileceğine ilişkin varsayılan sıra hakkında bilgi edinin.
ms.date: 08/22/2019
ms.topic: conceptual
ms.openlocfilehash: 51026862c989f15acf6d3e21702cfcfc8b2b27b0
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128828"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Azure 'de dağıtım sırasını anlayın

Azure şemaları, bir şema tanımının atamasını işlerken kaynak oluşturma sırasını belirlemede bir **sıralama düzeni** kullanır. Bu makalede aşağıdaki kavramlar açıklanmaktadır:

- Kullanılan varsayılan sıralama düzeni
- Sıralamayı Özelleştirme
- Özelleştirilmiş sıra nasıl işlenir

JSON örneklerinde, kendi değerlerinizle değiştirmeniz gereken değişkenler vardır:

- `{YourMG}` - Yönetim grubunuzun adıyla değiştirin

## <a name="default-sequencing-order"></a>Varsayılan sıralama düzeni

Şema tanımı yapıtları dağıtmaya yönelik bir yönerge içermiyorsa veya yönerge null ise, aşağıdaki sıra kullanılır:

- Yapıt adına göre sıralanan abonelik düzeyi **rol atama** yapıtları
- Yapıt adına göre sıralanan abonelik düzeyi **ilke atama** yapıtları
- Yapıt adına göre sıralanan abonelik düzeyi **Azure Resource Manager şablon** yapıtları
- **Kaynak grubu** yapıtları (alt yapıtlar dahil) yer tutucu adına göre sıralanmış

Her **kaynak grubu** yapıtları içinde, bu kaynak grubu içinde yapıların oluşturulması için aşağıdaki sıra sırası kullanılır:

- Yapıt adına göre sıralanan kaynak grubu alt **rol ataması** yapıtları
- Yapıt adına göre sıralanan kaynak grubu alt **ilke atama** yapıtları
- Yapıt adına göre sıralanan kaynak grubu alt **Azure Resource Manager şablonu** yapıtları

> [!NOTE]
> [Yapıtlar ()](../reference/blueprint-functions.md#artifacts) kullanımı, başvurulan yapıtın üzerinde örtük bir bağımlılık oluşturur.

## <a name="customizing-the-sequencing-order"></a>Sıralama düzenini özelleştirme

Büyük şema tanımları oluştururken kaynakların belirli bir sırada oluşturulması gerekebilir. Bu senaryonun en yaygın kullanım deseninin, bir şema tanımının birçok Azure Resource Manager şablonu içermesi durumunda olur. Planlar sıralama düzeninin tanımlanmasına izin vererek bu düzeni işler.

Sıralama, JSON içinde bir `dependsOn` özelliği tanımlayarak gerçekleştirilir. Kaynak grupları ve yapıt nesneleri için şema tanımı bu özelliği destekler. `dependsOn`, belirli yapıtın oluşturulmadan önce oluşturulması gereken yapıt adlarından oluşan bir dize dizisidir.

> [!NOTE]
> Şema nesneleri oluştururken, her yapıt kaynağı, [PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact)kullanıyorsanız veya [REST API](/rest/api/blueprints/artifacts/createorupdate)kullanılıyorsa URL uç noktası olan adı dosya adıyla alır.
> yapıtlar içindeki _resourceGroup_ başvuruları, şema tanımında tanımlananlarla aynı olmalıdır.

### <a name="example---ordered-resource-group"></a>Örnek-sıralı kaynak grubu

Bu örnek şeması, bir standart kaynak grubuyla birlikte `dependsOn`için bir değer bildirerek özel bir sıralama düzeni tanımlamış bir kaynak grubuna sahiptir. Bu durumda, **atamaadı** adlı yapıt, **sıralı-RG** kaynak grubundan önce işlenir.
**Standart-RG** , varsayılan sıralama düzeni başına işlenir.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Örnek-özel siparişle yapıt

Bu örnek, bir Azure Resource Manager şablonuna bağlı olan bir ilke yapıtıdır. Varsayılan sıralama olarak, Azure Resource Manager şablondan önce bir ilke yapıtı oluşturulur. Bu sıralama, ilke yapıtının Azure Resource Manager şablonunun oluşturulmasını beklemesini sağlar.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Örnek-bir kaynak grubuna bağlı olarak abonelik düzeyi şablon yapıtı

Bu örnek, bir kaynak grubuna bağlı olmak için abonelik düzeyinde dağıtılan bir Kaynak Yöneticisi şablonu içindir. Varsayılan sıralamada, abonelik düzeyi yapıtlar bu kaynak gruplarındaki herhangi bir kaynak grubundan ve alt yapıtlardan önce oluşturulur. Kaynak grubu, aşağıdaki gibi şema tanımında tanımlanmıştır:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

**Bana bekle** kaynak grubuna bağlı olarak abonelik düzeyi şablonu yapıtı şöyle tanımlanır:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>Özelleştirilmiş diziyi işleme

Oluşturma işlemi sırasında, planlar yapıtlarının bağımlılık grafiğini oluşturmak için bir topik sıralama kullanılır. Denetim, kaynak grupları ve yapıtlar arasındaki her bağımlılık düzeyinin desteklendiğinden emin olmanızı sağlar.

Bir yapıt bağımlılığının varsayılan sırayı değiştirmediği bildirilirse, hiçbir değişiklik yapılmaz. Bir örnek, abonelik düzeyi ilkesine bağlı olan bir kaynak grubudur. Diğer bir örnek, kaynak grubu ' standart-RG ' alt rol atamasına bağlı olan bir kaynak grubu ' standart-RG ' alt ilke atamasıdır. Her iki durumda da `dependsOn` varsayılan sıralama sırasını değiştirmezdi ve hiçbir değişiklik yapılmazlar.

## <a name="next-steps"></a>Sonraki adımlar

- [Şema yaşam döngüsü](lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](parameters.md) kullanımını anlayın.
- [Şema kaynak kilitleme](resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin.
- [Genel sorun giderme](../troubleshoot/general.md) adımlarıyla şema atama sorunlarını giderin.