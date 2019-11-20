---
title: Azure şemaları işlevleri
description: Azure şemaları tanımlarında ve atamalarında şema yapıtları ile kullanılabilecek işlevleri açıklar.
ms.date: 04/15/2019
ms.topic: reference
ms.openlocfilehash: 92539da02ddbe22f943454aff54dae4ccb5af3ce
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128765"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Azure şemaları ile kullanım için işlevler

Azure şemaları, bir şema tanımını daha dinamik hale getiren işlevler sağlar. Bu işlevler, şema tanımları ve şema yapıtları ile kullanım içindir. Kaynak Yöneticisi Şablon yapıtı, bir şema parametresi aracılığıyla dinamik bir değer almaya ek olarak Kaynak Yöneticisi işlevlerinin tam kullanımını destekler.

Aşağıdaki işlevler desteklenir:

- [artifacts](#artifacts)
- [concat](#concat)
- [parametreler](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [aboneliği](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

Bu şema yapıtları çıkışları ile doldurulmuş özelliklerin bir nesnesini döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| artifactName |Yes |string |Şema yapıtı adı. |

### <a name="return-value"></a>Dönüş değeri

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

#### <a name="resource-manager-template-artifact"></a>Şablon yapıtı Kaynak Yöneticisi

Döndürülen nesnenin **Çıkış** özellikleri kaynak yöneticisi şablonu içinde tanımlanır ve dağıtım tarafından döndürülür.

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

Aşağıdaki örnek çıktı özelliğini içeren _Mytemplateyapıt_ kimlikli bir kaynak yöneticisi şablonu yapıtı:

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

| İfadeler | Tür | Değer |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Dizi | \["First", "Second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Dize | adı |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Dize | "dize değeri" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Nesne | {"MyProperty": "My value", "anotherProperty": true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Dize | "My value" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>Concat

`concat(string1, string2, string3, ...)`

Birden çok dize değerini birleştirir ve birleştirilmiş dizeyi döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| string1 |Yes |string |Birleştirme için ilk değer. |
| ek bağımsız değişkenler |Hayır |string |Birleştirme için ek değerler sıralı sırada |

### <a name="return-value"></a>Dönüş değeri

Art arda eklenmiş değerlerin dizesi.

### <a name="remarks"></a>Açıklamalar

Azure Blueprint işlevi, Azure Resource Manager şablonu işlevinden farklıdır ve yalnızca dizelerle çalışır.

### <a name="example"></a>Örnek

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parametreler

`parameters(parameterName)`

Bir şema parametre değeri döndürür. Belirtilen parametre adı, şema tanımında veya şema yapıtlarının içinde tanımlanmalıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| parameterName |Yes |string |Döndürülecek parametrenin adı. |

### <a name="return-value"></a>Dönüş değeri

Belirtilen şema veya şema yapıt parametresinin değeri.

### <a name="remarks"></a>Açıklamalar

Azure Blueprint işlevi, Azure Resource Manager şablonu işlevinden farklıdır ve yalnızca şema parametreleriyle çalışır.

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
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Ardından, şema yapıtında `parameters()` için bağımsız değişken olarak _prenler_ kullanın:

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

### <a name="return-value"></a>Dönüş değeri

Döndürülen nesne aşağıdaki biçimdedir:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Açıklamalar

Azure Blueprint işlevi Azure Resource Manager şablonu işlevinden farklıdır. `resourceGroup()` işlevi, abonelik düzeyindeki yapıt veya şema tanımında kullanılamaz. Yalnızca bir kaynak grubu yapısının parçası olan şema yapıtları için kullanılabilir.

`resourceGroup()` işlevinin yaygın kullanımı, kaynak grubu yapıtı ile aynı konumda kaynak oluşturmaktır.

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

Ardından, bir kaynak grubu yer tutucu nesnesini hedefleyen bir şema yapıtı bağlamında `resourceGroup()` işlevini kullanın. Bu örnekte, şablon yapıtı _Networkingplaceholder_ kaynak grubuna dağıtılır ve bu parametre _ResourceLocation_ ' ı dinamik olarak, bir şablon için _networkingplaceholder_ kaynak grubu konumuyla doldurulmuş olarak sunar. _Networkingplaceholder_ kaynak grubunun konumu, şema tanımında statik olarak tanımlanmış veya atama sırasında dinamik olarak tanımlanmış olabilir. Her iki durumda da şablon yapıtı, bu bilgileri parametre olarak sağlar ve kaynakları doğru konuma dağıtmak için kullanır.

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

Belirtilen kaynak grubu yapıtını temsil eden bir nesne döndürür. Yapıtın bağlamını gerektiren `resourceGroup()`aksine bu işlev, kaynak grubunun bağlamında olmayan belirli bir kaynak grubu yer tutucusunun özelliklerini almak için kullanılır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| placeholderName |Yes |string |Döndürülecek kaynak grubu yapıtı için yer tutucu adı. |

### <a name="return-value"></a>Dönüş değeri

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

Ardından, kaynak grubu yer tutucu nesnesine bir başvuru almak için herhangi bir şema yapıtı bağlamından `resourceGroups()` işlevini kullanın. Bu örnekte, şablon yapıtı _Networkingplaceholder_ kaynak grubunun dışında dağıtılır ve bir _artifactlocation_ parametresi ile _networkingplaceholder_ kaynak grubu konumu ile dinamik olarak doldurulmuş bir parametre sağlar. _Networkingplaceholder_ kaynak grubunun konumu, şema tanımında statik olarak tanımlanmış veya atama sırasında dinamik olarak tanımlanmış olabilir. Her iki durumda da şablon yapıtı, bu bilgileri parametre olarak sağlar ve kaynakları doğru konuma dağıtmak için kullanır.

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

## <a name="subscription"></a>subscription

`subscription()`

Geçerli şema atamasının abonelik hakkındaki ayrıntıları döndürür.

### <a name="return-value"></a>Dönüş değeri

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

Şablon yapıtı için _resourceName_ parametresi olarak geçirilen bir adlandırma kuralı oluşturmak için aboneliğin görünen adını ve `concat()` işlevini kullanın.

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