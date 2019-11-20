---
title: Azure bölümü Kullanıcı arabirimi öğesi | Microsoft Docs
description: Azure portal için Microsoft. Common. Section Kullanıcı arabirimi öğesini açıklar. Yönetilen uygulamaları dağıtmak için portaldaki öğeleri gruplandırmak için kullanın.
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
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: fd2c1105078b918043791fd0f18395409bb32f7c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151718"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft. Common. Section Kullanıcı arabirimi öğesi

Bir veya daha fazla öğeyi bir başlık altında gruplandıran bir denetim.

## <a name="ui-sample"></a>UI örneği

![Microsoft. Common. Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Şema

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Açıklamalar

- `elements` en az bir öğe içermeli ve `Microsoft.Common.Section`hariç tüm öğe türlerine sahip olabilir.
- Bu öğe `toolTip` özelliğini desteklemiyor.

## <a name="sample-output"></a>Örnek çıktı
`elements`öğelerin çıkış değerlerine erişmek için, [temel bilgiler ()](create-uidefinition-functions.md#basics) veya [Steps ()](create-uidefinition-functions.md#steps) işlevlerini ve nokta gösterimini kullanın:

```json
steps('configuration').section1.text1
```

`Microsoft.Common.Section` türündeki öğeler kendisine ait hiçbir çıkış değeri içermez.

## <a name="next-steps"></a>Sonraki adımlar

* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
