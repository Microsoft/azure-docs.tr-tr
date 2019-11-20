---
title: Azure yönetilen uygulamalarında özel rol tanımlarına genel bakış | Microsoft Docs
description: Yönetilen uygulamalar için özel rol tanımları oluşturma kavramını açıklar.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7f4371bea467d6d4c99a776e03cdf13070d77ac6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818390"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Azure yönetilen uygulamalarında özel rol tanımı yapıtı

Özel rol tanımı yönetilen uygulamalarda isteğe bağlı bir yapıdır. Yönetilen uygulamanın işlevlerini gerçekleştirmesi için gereken izinleri belirlemek için kullanılır.

Bu makalede özel rol tanımı yapıtı ve özelliklerine genel bir bakış sunulmaktadır.

## <a name="custom-role-definition-artifact"></a>Özel rol tanımı yapıtı

CustomRoleDefinition. JSON özel rol tanımı yapıtı adını yazmanız gerekir. Yönetilen bir uygulama tanımı oluşturan. zip paketindeki Createuıdefinition. JSON ve mainTemplate. JSON ile aynı düzeyde yer koyun. . Zip paketini oluşturma ve yönetilen uygulama tanımını yayımlama hakkında bilgi edinmek için bkz [. yönetilen uygulama tanımını yayımlama.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Özel rol tanımı şeması

CustomRoleDefinition. JSON dosyası, rol dizisi olan bir en üst düzey `roles` özelliğine sahiptir. Bu roller, yönetilen uygulamanın çalışması için gereken izinlerdir. Şu anda yalnızca yerleşik rollere izin veriliyor, ancak birden çok rol belirtebilirsiniz. Role rol tanımının KIMLIĞI veya rol adı tarafından başvurulabilir.

Özel rol tanımı için örnek JSON:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>Roller

Rol bir `$.properties.roleName` veya `id`oluşur:

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> `id` ya da `roleName` alanını kullanabilirsiniz. Yalnızca bir tane gereklidir. Bu alanlar, uygulanması gereken rol tanımını aramak için kullanılır. Her ikisi de sağlanırsa `id` alanı kullanılacaktır.

|Özellik|Gerekli mi?|Açıklama|
|---------|---------|---------|
|id|Evet|Yerleşik rolün KIMLIĞI. Tam KIMLIĞI veya yalnızca GUID 'YI kullanabilirsiniz.|
|RoleName|Evet|Yerleşik rolün adı.|