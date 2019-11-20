---
title: Azure StorageAccountSelector Kullanıcı arabirimi öğesi | Microsoft Docs
description: Azure portal için Microsoft. Storage. StorageAccountSelector Kullanıcı arabirimi öğesini açıklar.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: e1f96b42e58bcb09cfc2836c993626a889669fc0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151467"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft. Storage. StorageAccountSelector Kullanıcı arabirimi öğesi

Yeni veya mevcut bir depolama hesabı seçmek için bir denetim.

## <a name="ui-sample"></a>UI örneği

Denetim varsayılan değeri gösterir.

![Microsoft. Storage. StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Denetim, kullanıcının yeni bir depolama hesabı oluşturmasını veya var olan bir depolama hesabını seçmesini sağlar.

![Microsoft. Storage. StorageAccountSelector yeni](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

## <a name="schema"></a>Şema

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Örnek çıktı

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>Açıklamalar

- Belirtilmişse, `defaultValue.name` benzersizlik için otomatik olarak onaylanır. Depolama hesabı adı benzersiz değilse, kullanıcının farklı bir ad belirtmesi veya var olan bir depolama hesabını seçmesi gerekir.
- `defaultValue.type` için varsayılan değer **Premium_LRS**.
- `constraints.allowedTypes` ' de belirtilmeyen herhangi bir tür gizlidir ve `constraints.excludedTypes` belirtilmemiş herhangi bir tür gösterilir. `constraints.allowedTypes` ve `constraints.excludedTypes` her ikisi de isteğe bağlıdır, ancak aynı anda kullanılamaz.
- `options.hideExisting` **true**ise, Kullanıcı var olan bir depolama hesabını seçemezsiniz. Varsayılan değer **false**.

## <a name="next-steps"></a>Sonraki adımlar
* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
