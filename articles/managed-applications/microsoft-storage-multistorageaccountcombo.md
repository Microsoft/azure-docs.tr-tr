---
title: Azure MultiStorageAccountCombo Kullanıcı arabirimi öğesi | Microsoft Docs
description: Azure portal için Microsoft. Storage. MultiStorageAccountCombo UI öğesini açıklar.
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
ms.openlocfilehash: e3fb6f474bfe56f54e6dc621a3893e184ebc71d9
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151445"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft. Storage. MultiStorageAccountCombo UI öğesi

Ortak bir ön ekiyle başlayan adlara sahip birkaç depolama hesabı oluşturmaya yönelik bir denetim grubu.

## <a name="ui-sample"></a>UI örneği

![Microsoft. Storage. MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>Şema

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Örnek çıktı

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>Açıklamalar

- `defaultValue.prefix` değeri, depolama hesabı adları dizisini oluşturmak için bir veya daha fazla tamsayı ile birleştirilir. Örneğin, `defaultValue.prefix` **sa** ve `count` **2**ise, depolama hesabı adları **SA1** ve **SA2** oluşturulur. Oluşturulan depolama hesabı adları otomatik olarak benzersizlik için onaylanır.
- Depolama hesabı adları `count`göre lexıgrafik olarak oluşturulur. Örneğin, `count` 10 ise, depolama hesabı adları iki basamaklı tamsayılarla biter (01, 02, 03).
- `defaultValue.prefix` için varsayılan değer **null**ve `defaultValue.type` için **Premium_LRS**.
- `constraints.allowedTypes` ' de belirtilmeyen herhangi bir tür gizlidir ve `constraints.excludedTypes` belirtilmemiş herhangi bir tür gösterilir. `constraints.allowedTypes` ve `constraints.excludedTypes` her ikisi de isteğe bağlıdır, ancak aynı anda kullanılamaz.
- Depolama hesabı adları oluşturmaya ek olarak, `count` öğesi için uygun çarpanı ayarlamak üzere kullanılır. **2**gibi bir statik değeri veya `[steps('step1').storageAccountCount]`gibi başka bir öğeden dinamik bir değeri destekler. Varsayılan değer **1**' dir.

## <a name="next-steps"></a>Sonraki adımlar

* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
