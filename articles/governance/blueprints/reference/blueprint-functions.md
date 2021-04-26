---
title: Azure şemaları işlevleri
description: Azure şemaları tanımlarında ve atamalarında şema yapıtları ile kullanılabilecek işlevleri açıklar.
ms.date: 01/27/2021
ms.topic: reference
ms.openlocfilehash: 92cb906e87179073b7a69aa0bd4eab22c77087f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919283"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Azure şemaları ile kullanım için işlevler

Azure şemaları, bir şema tanımını daha dinamik hale getiren işlevler sağlar. Bu işlevler, şema tanımları ve şema yapıtları ile kullanım içindir. Azure Resource Manager şablonu (ARM şablonu) yapıtı, bir şema parametresi aracılığıyla dinamik bir değer almaya ek olarak Kaynak Yöneticisi işlevlerinin tam kullanımını destekler.

Aşağıdaki işlevler desteklenir:

- [Yapıt](#artifacts)
- [Concat](#concat)
- [parametrelere](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [aboneliğiniz](#subscription)

## <a name="artifacts"></a>Yapıt

`artifacts(artifactName)`

Bu şema yapıtları çıkışları ile doldurulmuş özelliklerin bir nesnesini döndürür.

> [!NOTE]
> `artifacts()`Işlev ARM şablonunun içinden kullanılamaz. İşlev yalnızca şema tanımı JSON ' de veya şema tanımı Azure PowerShell ya da [kod olarak planlar](https://github.com/Azure/azure-blueprints/blob/master/README.md)' ın bir parçası olarak REST API şema YÖNETIMI sırasında JSON yapısı içinde kullanılabilir.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| artifactName |Yes |string |Şema yapıtı adı. |

### <a name="return-value"></a>Döndürülen değer

Çıkış özelliklerinin nesnesi. **Çıktılar** özellikleri, başvurulmakta olan şema yapıtı türüne bağlıdır. Tüm türler şu biçimdedir:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>İlke atama yapıtı

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="arm-template-artifact"></a>ARM şablon yapıtı

Döndürülen nesnenin **çıkışlar** özellikleri ARM şablonunda tanımlanır ve dağıtım tarafından döndürülür.

#### <a name="role-assignment-artifact"></a>Rol atama yapıtı

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Örnek

Aşağıdaki örnek çıktı özelliğini içeren _Mytemplateyapıt_ KIMLIKLI bir ARM şablonu yapıtı:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

_Mytemplateyapıt_ örneğinden veri almaya ilişkin bazı örnekler şunlardır:

| Expression | Tür | Değer |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Dizi | \["First", "Second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Dize | adı |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Dize | "dize değeri" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Nesne | {"MyProperty": "My value", "anotherProperty": true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Dize | "My value" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | Doğru |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Birden çok dize değerini birleştirir ve birleştirilmiş dizeyi döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| dize1 |Yes |string |Birleştirme için ilk değer. |
| ek bağımsız değişkenler |No |string |Birleştirme için ek değerler sıralı sırada |

### <a name="return-value"></a>Döndürülen değer

Art arda eklenmiş değerlerin dizesi.

### <a name="remarks"></a>Açıklamalar

Azure Blueprint işlevi, yalnızca dizelerle çalışacak şekilde ARM şablon işlevinden farklıdır.

### <a name="example"></a>Örnek

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Bir şema parametre değeri döndürür. Belirtilen parametre adı, şema tanımında veya şema yapıtlarının içinde tanımlanmalıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| parameterName |Yes |string |Döndürülecek parametrenin adı. |

### <a name="return-value"></a>Döndürülen değer

Belirtilen şema veya şema yapıt parametresinin değeri.

### <a name="remarks"></a>Açıklamalar

Azure Blueprint işlevi, yalnızca şema parametreleriyle birlikte çalışarak ARM şablon işlevinden farklıdır.

### <a name="example"></a>Örnek

Şema tanımında parametre _prenileri_ tanımlayın:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the Azure role assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Ardından, bir şema yapıtı içinde için bağımsız değişken olarak _Prenalid_ 'leri kullanın `parameters()` :

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Geçerli kaynak grubunu temsil eden bir nesne döndürür.

### <a name="return-value"></a>Döndürülen değer

Döndürülen nesne aşağıdaki biçimdedir:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Açıklamalar

Azure Blueprint işlevi ARM şablon işlevinden farklıdır. `resourceGroup()`İşlev, abonelik düzeyindeki yapıt veya şema tanımında kullanılamaz. Yalnızca bir kaynak grubu yapısının parçası olan şema yapıtları için kullanılabilir.

İşlevin yaygın kullanımı, `resourceGroup()` kaynak grubu yapıtı ile aynı konumda kaynak oluşturmaktır.

### <a name="example"></a>Örnek

Kaynak grubunun konumunu kullanmak için, başka bir yapıtın konumu olarak, şema tanımında veya atama sırasında ayarlayın, şema tanımınızda bir kaynak grubu yer tutucu nesnesi bildirin. Bu örnekte _Networkingplaceholder_ , kaynak grubu yer tutucusunun adıdır.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Ardından, `resourceGroup()` bir kaynak grubu yer tutucu nesnesini hedefleyen bir şema yapıtı bağlamında işlevini kullanın. Bu örnekte, şablon yapıtı _Networkingplaceholder_ kaynak grubuna dağıtılır ve bu parametre _ResourceLocation_ ' ı dinamik olarak, bir şablon için _networkingplaceholder_ kaynak grubu konumuyla doldurulmuş olarak sunar. _Networkingplaceholder_ kaynak grubunun konumu, şema tanımında statik olarak tanımlanmış veya atama sırasında dinamik olarak tanımlanmış olabilir. Her iki durumda da şablon yapıtı, bu bilgileri parametre olarak sağlar ve kaynakları doğru konuma dağıtmak için kullanır.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Belirtilen kaynak grubu yapıtını temsil eden bir nesne döndürür. `resourceGroup()`Yapıt bağlamını gerektiren aksine, bu işlev, kaynak grubunun bağlamında olmayan belirli bir kaynak grubu yer tutucusunun özelliklerini almak için kullanılır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| placeholderName |Yes |string |Döndürülecek kaynak grubu yapıtı için yer tutucu adı. |

### <a name="return-value"></a>Döndürülen değer

Döndürülen nesne aşağıdaki biçimdedir:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Örnek

Kaynak grubunun konumunu kullanmak için, başka bir yapıtın konumu olarak, şema tanımında veya atama sırasında ayarlayın, şema tanımınızda bir kaynak grubu yer tutucu nesnesi bildirin. Bu örnekte _Networkingplaceholder_ , kaynak grubu yer tutucusunun adıdır.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Ardından, `resourceGroups()` kaynak grubu yer tutucu nesnesine bir başvuru almak için herhangi bir şema yapıtı bağlamından işlevini kullanın. Bu örnekte, şablon yapıtı _Networkingplaceholder_ kaynak grubunun dışında dağıtılır ve bir _artifactlocation_ parametresi ile _networkingplaceholder_ kaynak grubu konumu ile dinamik olarak doldurulmuş bir parametre sağlar. _Networkingplaceholder_ kaynak grubunun konumu, şema tanımında statik olarak tanımlanmış veya atama sırasında dinamik olarak tanımlanmış olabilir. Her iki durumda da şablon yapıtı, bu bilgileri parametre olarak sağlar ve kaynakları doğru konuma dağıtmak için kullanır.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>aboneliği

`subscription()`

Geçerli şema atamasının abonelik hakkındaki ayrıntıları döndürür.

### <a name="return-value"></a>Döndürülen değer

Döndürülen nesne aşağıdaki biçimdedir:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Örnek

`concat()`Şablon yapıtı Için _resourceName_ parametresi olarak geçirilen bir adlandırma kuralı oluşturmak için aboneliğin görünen adını ve işlevini kullanın.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Şema yaşam döngüsü](../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin.
- [Genel sorun giderme](../troubleshoot/general.md) adımlarıyla şema atama sorunlarını giderin.