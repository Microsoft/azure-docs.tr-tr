---
title: Azure TagsByResource Kullanıcı arabirimi öğesi
description: Azure portal için Microsoft. Common. TagsByResource Kullanıcı arabirimi öğesini açıklar. Dağıtım sırasında bir kaynağa etiket uygulamak için kullanın.
author: tfitzmac
ms.service: managed-applications
ms.topic: reference
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 5711759666cd68dc93e5bfb67e51ec1cc0a48bd1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73933148"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft. Common. TagsByResource Kullanıcı arabirimi öğesi

Bir dağıtımdaki kaynaklarla [etiketlerin](../azure-resource-manager/resource-group-using-tags.md) ilişkilendirilmesi için bir denetim.

## <a name="ui-sample"></a>UI örneği

![Microsoft. Common. DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>Şema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Örnek çıktı

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Açıklamalar

- `resources` dizisindeki en az bir öğe belirtilmelidir.
- `resources` her öğe tam bir kaynak türü olmalıdır. Bu öğeler **kaynak** açılan menüsünde görünür ve Kullanıcı tarafından taggable.
- Denetimin çıktısı, bir Azure Resource Manager şablonundaki etiket değerlerinin kolay atanması için biçimlendirilir. Bir şablonda denetimin çıkışını almak için aşağıdaki örnekte gösterildiği gibi şablonunuzda bir parametre ekleyin:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Etiketlenebilir her kaynak için, Etiketler özelliğini bu kaynak türü için parametre değerine atayın:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- TagsByResource parametresine erişirken [IF](../azure-resource-manager/resource-group-template-functions-logical.md#if) işlevini kullanın. Verilen kaynak türüne hiçbir etiket atanmamışsa boş bir nesne atamanızı sağlar.

## <a name="next-steps"></a>Sonraki adımlar

- UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
- UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
