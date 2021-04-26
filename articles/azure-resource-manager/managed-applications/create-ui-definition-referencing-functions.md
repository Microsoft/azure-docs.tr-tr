---
title: UI tanımı başvuru işlevleri oluştur
description: Diğer nesnelere başvuran Azure portal için Kullanıcı arabirimi tanımları oluştururken kullanılacak işlevleri açıklar.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87099918"
---
# <a name="createuidefinition-referencing-functions"></a>Createuıdefinition başvuru işlevleri

Bir Createuıdefinition dosyasının özelliklerinden veya bağlamındaki çıkışlara başvururken kullanılacak işlevler.

## <a name="basics"></a>temel bilgileri

[Temel bilgiler](create-uidefinition-overview.md#basics) adımında tanımlanan bir öğenin çıkış değerlerini döndürür. Öğenin adını bu işleve parametre olarak geçirin.

Diğer adımlarda öğelerin çıkış değerlerini almak için, [Steps ()](#steps) işlevini kullanın.

Aşağıdaki örnek, temel bilgiler adımında adlı öğesinin çıkışını döndürür `clusterName` :

```json
"[basics('clusterName')]"
```

Döndürülen değerler, alınan öğe türüne göre farklılık gösterir.

## <a name="location"></a>location

Temel bilgiler adımında veya geçerli bağlamda seçilen konumu döndürür.

Aşağıdaki örnek şöyle bir değer döndürür `"westus"` :

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

Temel bilgiler adımında veya geçerli bağlamda seçilen resourceGroup hakkındaki ayrıntıları döndürür.

Aşağıdaki örnek:

```json
"[resourceGroup()]"
```

Aşağıdaki özellikleri döndürür:

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

Nokta gösterimiyle belirli bir değeri alabilirsiniz.

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>adımlar

Belirtilen adımdaki öğeleri döndürür. Adımın adını bu işleve parametre olarak geçirin. Döndürülen öğeler ' de, belirli özellik değerlerini alabilirsiniz.

Temel bilgiler adımında öğelerin çıkış değerlerini almak için, [temel bilgiler ()](#basics) işlevini kullanın.

Aşağıdaki örnek adlı adımı döndürür `vmParameters` . Bu adımda adlı bir öğedir `adminUsername` .

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>aboneliği

Temel bilgiler adımında veya geçerli bağlamda seçilen abonelik için özellikleri döndürür.

Aşağıdaki örnek:

```json
"[subscription()]"
```

Aşağıdaki özellikleri döndürür:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>Sonraki adımlar

* Portal arabirimini geliştirmeye giriş hakkında daha fazla bilgi için bkz. [ Azure yönetilen uygulamanın oluşturma deneyimi içinCreateUiDefinition.js](create-uidefinition-overview.md).
