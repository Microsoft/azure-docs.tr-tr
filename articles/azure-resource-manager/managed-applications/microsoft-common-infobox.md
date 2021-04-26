---
title: Infobox Kullanıcı arabirimi öğesi
description: Azure portal için Microsoft. Common. Infobox Kullanıcı arabirimi öğesini açıklar. Yönetilen uygulamayı dağıttığınızda metin veya uyarı eklemek için kullanın.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 7580ac0650706d6aee49bbf0e8235e8c5dab33f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87033368"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft. Common. Infobox Kullanıcı arabirimi öğesi

Bilgi kutusu ekleyen bir denetim. Bu kutu, kullanıcıların sağladıkları değerleri anlamasına yardımcı olan önemli metin veya uyarılar içerir. Daha fazla bilgi için bir URI 'ye de bağlanabilir.

## <a name="ui-sample"></a>UI örneği

![Microsoft. Common. Infobox](./media/managed-application-elements/microsoft-common-infobox.png)


## <a name="schema"></a>Şema

```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="sample-output"></a>Örnek çıktı

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Açıklamalar

* İçin `icon` **none**, **Info**, **Warning** veya **Error** kullanın.
* `uri`Özelliği isteğe bağlıdır.

## <a name="next-steps"></a>Sonraki adımlar

* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
