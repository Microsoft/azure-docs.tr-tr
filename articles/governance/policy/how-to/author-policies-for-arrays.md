---
title: Kaynaklardaki dizi özellikleri için yazma ilkeleri
description: Dizi parametreleri oluşturmayı, dizi dili ifadeleri için kurallar oluşturmayı, [*] diğer adını değerlendirmeyi ve mevcut bir diziye öğeleri Azure Ilke tanımı kuralları ile eklemeyi öğrenin.
ms.date: 03/06/2019
ms.topic: conceptual
ms.openlocfilehash: f28cffcf928f9c4da6b2dae2a0811200397c1f0d
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959708"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Azure kaynaklarında dizi özellikleri için yazma ilkeleri

Azure Resource Manager özellikler genellikle dizeler ve Boole değerleri olarak tanımlanır. Bire çok ilişkisi olduğunda, karmaşık özellikler bunun yerine diziler olarak tanımlanır. Azure Ilkesinde diziler birkaç farklı şekilde kullanılır:

- Birden çok seçenek sağlamak için bir [tanım parametresinin](../concepts/definition-structure.md#parameters)türü
- **Ya da** **notın** koşullarını kullanan bir [ilke kuralının](../concepts/definition-structure.md#policy-rule) bir parçası
- **Yok**, **Any**veya **All** gibi belirli senaryoları değerlendirmek için [\[\*\] diğer adını](../concepts/definition-structure.md#understanding-the--alias) değerlendiren bir ilke kuralının parçası
- Var olan bir diziyi değiştirmek veya eklemek için [ekleme efekti](../concepts/effects.md#append)

Bu makalede Azure Ilkesi tarafından kullanılan her kullanım ele alınmaktadır ve birkaç örnek tanım sunulmaktadır.

## <a name="parameter-arrays"></a>Parametre dizileri

### <a name="define-a-parameter-array"></a>Parametre dizisi tanımlama

Bir parametreyi dizi olarak tanımlamak, birden fazla değer gerektiğinde ilke esnekliği sağlar.
Bu ilke tanımı, **Allowedlocations** parametresi için tek bir konuma izin verir ve varsayılan olarak _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

**Tür** _dize_olduğu için, ilke atanırken yalnızca bir değer ayarlanabilir. Bu ilke atanırsa, kapsamdaki kaynaklara yalnızca tek bir Azure bölgesi içinde izin verilir. Çoğu ilke tanımlarının, _eastus2_, _eastus_ve _westus2_gibi onaylanan seçenekler listesi için izin verilmesi gerekir.

Birden çok seçeneğe izin vermek üzere ilke tanımı oluşturmak için _dizi_ **türünü**kullanın. Aynı ilke aşağıdaki gibi yeniden yazılabilir:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Bir ilke tanımı kaydedildikten sonra, bir parametresindeki **tür** özelliği değiştirilemez.

Bu yeni parametre tanımı, ilke ataması sırasında birden fazla değer alır. Dizi özelliği tarafından tanımlanan, atama sırasında kullanılabilir olan değerler **önceden tanımlanmış seçenek** listesiyle daha da sınırlıdır. **AllowedValues** kullanımı isteğe bağlıdır.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Atama sırasında değerleri bir parametre dizisine geçirme

İlke Azure portal aracılığıyla atanırken, _dizi_ **türünde** bir parametre tek bir metin kutusu olarak görüntülenir. İpucu "kullanım" diyor. değerlerini ayırmak için. (örneğin, Londra; New York) ". _Eastus2_, _eastus_ve _westus2_ izin verilen konum değerlerini parametreye geçirmek için aşağıdaki dizeyi kullanın:

`eastus2;eastus;westus2`

Azure CLı, Azure PowerShell veya REST API kullanılırken parametre değerinin biçimi farklıdır. Değerler, parametrenin adını da içeren bir JSON dizesi aracılığıyla geçirilir.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Bu dizeyi her SDK ile kullanmak için aşağıdaki komutları kullanın:

- Azure CLı: komut [az Policy atama Create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) parametresini parametre **params**
- Azure PowerShell: cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) with Parameter **PolicyParameter**
- REST API: _PUT_ [oluşturma](/rest/api/resources/policyassignments/create) işleminde, **Properties. Parameters** özelliğinin değeri olarak istek gövdesinin bir parçası olarak

## <a name="policy-rules-and-arrays"></a>İlke kuralları ve diziler

### <a name="array-conditions"></a>Dizi koşulları

_Dizi_
parametre **türünün** birlikte kullanılabileceği ilke kuralı [Koşulları](../concepts/definition-structure.md#conditions) `in` ve `notIn`sınırlıdır. Aşağıdaki ilke tanımını örnek olarak `equals` koşulu ile gerçekleştirin:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Azure portal aracılığıyla bu ilke tanımını oluşturmaya çalışmak, bu hata iletisi gibi bir hataya yol açar:

- "' {GUID} ' ilkesi doğrulama hataları nedeniyle parametreleştirimedi. İlke parametrelerinin doğru tanımlanıp tanımlanmadığından emin olun. ' [Parameters (' allowedLocations ')] ' dil ifadesinin değerlendirme sonucu, ' Array ' türü, beklenen tür ' String '. '. "

Beklenen koşul **türü** `equals` _dizedir_. **Allowedlocations** **türü** _dizi_olarak tanımlandığından, ilke altyapısı dil ifadesini değerlendirir ve hata oluşturur. `in` ve `notIn` koşulu ile, ilke altyapısı dil ifadesinde **tür** _dizisini_ bekler. Bu hata iletisini çözümlemek için `equals` `in` veya `notIn`olarak değiştirin.

### <a name="evaluating-the--alias"></a>[*] Diğer adı değerlendiriliyor

Adı **[\*]** olan diğer adlar **türün** bir _dizi_olduğunu gösterir. **[\*]** , tüm dizinin değerini değerlendirmek yerine dizinin her bir öğesini değerlendirmek mümkün hale getirir. Öğe değerlendirmesi başına bu üç senaryo vardır: None, any ve ALL.

İlke altyapısı **, yalnızca** **IF** kuralı doğru olarak değerlendirildiğinde **etkisini** tetikler.
Bu olgu, dizi öğelerinin her birini değerlendiren **[\*]** yolunu anlamak için önemlidir.

Aşağıdaki senaryo tablosu için örnek ilke kuralı:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

**Iprules** dizisi aşağıdaki senaryo tablosu için aşağıdaki gibidir:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Aşağıdaki her bir koşul örneği için `<field>` `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`ile değiştirin.

Aşağıdaki sonuçlar, koşulun birleşiminin ve yukarıdaki mevcut değerlerden oluşan örnek ilke kuralının sonucudur:

|Koşul |Sonuç |Açıklama |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Yapma |Tek bir dizi öğesi yanlış (127.0.0.1! = 127.0.0.1) ve diğeri doğru (127.0.0.1! = 192.168.1.1) olarak değerlendirilir, bu nedenle **Not alalals** koşulu _false_ olur ve etki tetiklenmez. |
|`{<field>,"notEquals":"10.0.4.1"}` |İlke etkisi |Her iki dizi öğesi de true olarak değerlendirilir (10.0.4.1! = 127.0.0.1 ve 10.0.4.1! = 192.168.1.1), bu nedenle **Not al** koşulu _true_ ve etki tetiklenir. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |İlke etkisi |Bir Array öğesi true (127.0.0.1 = = 127.0.0.1) ve diğeri false (127.0.0.1 = = 192.168.1.1) olarak değerlendirilir ve bu nedenle **eşittir** koşulu _false_olur. Mantıksal işleç doğru olarak değerlendirilir (false**değil**), bu nedenle etki tetiklenir. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |İlke etkisi |Her iki dizi öğesi de false olarak değerlendirilir (10.0.4.1 = = 127.0.0.1 ve 10.0.4.1 = = 192.168.1.1), bu nedenle **eşittir** koşulu _false_olur. Mantıksal işleç doğru olarak değerlendirilir (false**değil**), bu nedenle etki tetiklenir. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |İlke etkisi |Tek bir dizi öğesi yanlış (127.0.0.1! = 127.0.0.1) ve diğeri doğru (127.0.0.1! = 192.168.1.1) olarak değerlendirilir ve bu nedenle **Not al** koşulu _false_olur. Mantıksal işleç doğru olarak değerlendirilir (false**değil**), bu nedenle etki tetiklenir. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Yapma |Her iki dizi öğesi de true olarak değerlendirilir (10.0.4.1! = 127.0.0.1 ve 10.0.4.1! = 192.168.1.1), bu nedenle **Not al** koşulu _doğrudur_. Mantıksal işleç yanlış olarak değerlendirilir ( _true_değil), bu nedenle etki**tetiklenmez** . |
|`{<field>,"Equals":"127.0.0.1"}` |Yapma |Bir Array öğesi true (127.0.0.1 = = 127.0.0.1) ve diğeri false (127.0.0.1 = = 192.168.1.1) olarak değerlendirilir; bu nedenle **eşittir** koşulu _false_ olur ve etki tetiklenmez. |
|`{<field>,"Equals":"10.0.4.1"}` |Yapma |Her iki dizi öğesi de false olarak değerlendirilir (10.0.4.1 = = 127.0.0.1 ve 10.0.4.1 = = 192.168.1.1), bu nedenle **eşittir** koşulu _false_ olur ve etki tetiklenmez. |

## <a name="the-append-effect-and-arrays"></a>Ekleme efekti ve dizileri

**Ayrıntılar. alanın** bir **[\*]** diğer adı olmasına bağlı olarak, [ekleme efekti](../concepts/effects.md#append) farklı şekilde davranır.

- Bir **[\*]** diğer adı olmadığında, append değeri tüm diziyi **değer** özelliği ile değiştirir
- Bir **[\*]** diğer adı olduğunda, append değeri, var olan diziye **değer** özelliğini ekler veya yeni diziyi oluşturur

Daha fazla bilgi için bkz. [append örnekleri](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](programmatically-create.md)anlayın.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.