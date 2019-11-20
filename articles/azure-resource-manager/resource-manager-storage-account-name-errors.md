---
title: Depolama hesabı adı hataları
description: Bir depolama hesabı adı belirtirken karşılaşabileceğiniz hataları açıklar.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 6af75470ebab61a1eaf7afd0bf946564c5300611
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149415"
---
# <a name="resolve-errors-for-storage-account-names"></a>Depolama hesabı adları hatalarını çözme

Bu makalede, bir depolama hesabı dağıtılırken karşılaşabileceğiniz adlandırma hataları açıklanır.

## <a name="symptom"></a>Belirti

Depolama hesabı adınız yasaklanmış karakterler içeriyorsa, şöyle bir hata alırsınız:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Depolama hesapları için Azure genelinde benzersiz olan kaynak için bir ad sağlamanız gerekir. Benzersiz bir ad sağlamazsanız şöyle bir hata alırsınız:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Aboneliğinizde var olan bir depolama hesabı ile aynı ada sahip bir depolama hesabı dağıtırsanız, ancak farklı bir konum sağlarsanız, depolama hesabının farklı bir konumda zaten var olduğunu belirten bir hata alırsınız. Var olan depolama hesabını silin veya var olan depolama hesabıyla aynı konumu belirtin.

## <a name="cause"></a>Nedeni

Depolama hesabı adları 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakam ve küçük harf kullanılmalıdır. Ad benzersiz olmalıdır.

## <a name="solution"></a>Çözüm

Depolama hesabı adının benzersiz olduğundan emin olun. [Uniquestring](resource-group-template-functions-string.md#uniquestring) işlevinin sonucuyla birlikte adlandırma kurallarınızı birleştirerek benzersiz bir ad oluşturabilirsiniz.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Depolama hesabı adınızın 24 karakterden uzun olmadığından emin olun. [Uniquestring](resource-group-template-functions-string.md#uniquestring) işlevi 13 karakter döndürür. **Uniquestring** sonucuna bir önek veya sonek eklerseniz, 11 karakter veya daha az bir değer sağlayın.

```json
"parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    }
}
```

Depolama hesabınızın adının büyük/küçük harf veya özel karakter içermediğinden emin olun.