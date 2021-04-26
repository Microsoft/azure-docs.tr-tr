---
title: Kaynak kilitlemeyi anlama
description: Şemayı atarken kaynakları korumak için Azure şemaları 'ndaki kilitleme seçenekleri hakkında bilgi edinin.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: b2004ad294ae0eec1b4f2fc6f49308efd32d652e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920199"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Azure şemaları 'nda kaynak kilitlemeyi anlama

Ölçekteki tutarlı ortamların oluşturulması, bu tutarlılığı sürdürmek için bir mekanizma varsa gerçek anlamda değerlidir. Bu makalede, Azure şemaları 'nda kaynak kilitleme 'nin nasıl çalıştığı açıklanmaktadır. Kaynak kilitleme ve _reddetme atamalarından_ oluşan bir örnek görmek için bkz. [yeni kaynakları koruma](../tutorials/protect-new-resources.md) öğreticisi.

> [!NOTE]
> Azure şemaları tarafından dağıtılan kaynak kilitleri yalnızca şema atama tarafından dağıtılan kaynaklara uygulanır. Zaten var olan kaynak gruplarında olanlar gibi mevcut kaynaklar bunlara kilit eklenmez.

## <a name="locking-modes-and-states"></a>Kilitleme modları ve durumlar

Kilitleme modu, şema ataması için geçerlidir ve üç seçeneğe sahiptir: **kilitleme**, **salt okuma** veya **silme**. Kilitleme modu, bir şema ataması sırasında yapıt dağıtımı sırasında yapılandırılır. Şema ataması güncelleştirilerek farklı bir kilitleme modu ayarlanabilir.
Ancak, kilitleme modları Azure şemaları dışında değiştirilemez.

Şema atamasında yapıtlar tarafından oluşturulan kaynakların dört durumu vardır: **kilitli değil**, **salt okunurdur**, **düzenleme/silme yapılamaz** **veya silinemez**. Her yapıt türü **kilitli değil** durumunda olabilir. Aşağıdaki tablo bir kaynağın durumunu belirlemede kullanılabilir:

|Mod|Yapıt kaynak türü|Durum|Açıklama|
|-|-|-|-|
|Kilitleme|*|Kilitlenmedi|Kaynaklar Azure şemaları tarafından korunmuyor. Bu durum, bir **salt okunurdur** veya bir şema atamasının dışında kaynak grubu yapıtı **silme** ' ya eklenen kaynaklar için de kullanılır.|
|Salt Okunur|Kaynak grubu|Düzenleme/silme yapılamıyor|Kaynak grubu salt okunurdur ve kaynak grubundaki Etiketler değiştirilemez. **Kilitli** kaynaklar bu kaynak grubundan eklenebilir, taşınabilir, değiştirilebilir veya silinebilir.|
|Salt Okunur|Kaynak olmayan Grup|Salt Okunur|Kaynak hiçbir şekilde değiştirilemez. Değişiklik yok ve silinemez.|
|Silme|*|Silinemiyor|Kaynaklar değiştirilebilir, ancak silinemez. **Kilitli** kaynaklar bu kaynak grubundan eklenebilir, taşınabilir, değiştirilebilir veya silinebilir.|

## <a name="overriding-locking-states"></a>Kilitleme durumlarını geçersiz kılma

Genellikle abonelikte, ' sahip ' rolü gibi uygun [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../../role-based-access-control/overview.md) ile herhangi bir kaynağı değiştirme veya silme izni verilmesi olasıdır. Bu erişim, Azure şemaları, dağıtılan bir atamanın parçası olarak kilitlemeyi uygularken durum değildir. Atama salt **okuma** veya **silme** seçeneği ile ayarlandıysa, abonelik sahibi korumalı kaynak üzerinde engellenmiş eylemi gerçekleştirebilir.

Bu güvenlik ölçüsü, tanımlanan şema 'in ve yanlışlıkla ya da programlı silme veya değiştirme işleminden oluşturmakta tasarlanan ortamın tutarlılığını korur.

### <a name="assign-at-management-group"></a>Yönetim grubuna ata

Abonelik sahiplerinin bir şema atamasını kaldırmasını engelleyen tek seçenek, şema 'i bir yönetim grubuna atacaktır. Bu senaryoda, BLUEPRINT atamasını kaldırmak için gerekli izinlere sahip yalnızca yönetim grubunun **sahipleri** vardır.

Şemayı bir abonelik yerine bir yönetim grubuna atamak için, REST API çağrısı şuna benzer şekilde değişir:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

Tarafından tanımlanan yönetim grubu, `{assignmentMG}` Yönetim grubu hiyerarşisinde olmalıdır ya da şema tanımının kaydedildiği yönetim grubu aynı olmalıdır.

Şema atamasının istek gövdesi şöyle görünür:

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

Bu istek gövdesinde ve bir aboneliğe atanmakta olan anahtar farkı `properties.scope` özelliktir. Bu gerekli özellik, şema atamasının uygulandığı aboneliğe ayarlanmalıdır. Abonelik, şema atamasının depolandığı yönetim grubu hiyerarşisinin doğrudan bir alt öğesi olmalıdır.

> [!NOTE]
> Yönetim grubu kapsamına atanan bir şema, hala abonelik düzeyinde bir şema ataması olarak çalışır. Tek fark, abonelik sahiplerinin atamayı ve ilişkili kilitleri kaldırmasını engellemek için şema atamasının depolandığı yerdir.

## <a name="removing-locking-states"></a>Kilitleme durumları kaldırılıyor

Atama tarafından korunan bir kaynağı değiştirmek veya silmek için gerekli hale gelirse, bunu iki şekilde yapabilirsiniz.

- Şema atamasını kilitleme **moduna yükseltme**
- Şema atamasını silme

Atama kaldırıldığında, Azure şemaları tarafından oluşturulan kilitler kaldırılır. Ancak, kaynak arka planda bırakılır ve normal yollarla silinmelidir.

## <a name="how-blueprint-locks-work"></a>Şema kilitleri nasıl çalışır?

Atama salt **okuma** veya **silme** seçeneği belirlenmişse, bir şema atama sırasında yapıt kaynaklarına bir Azure RBAC [reddetme](../../../role-based-access-control/deny-assignments.md) ataması reddetme eylemi uygulanır. Reddetme eylemi, BLUEPRINT atamasının yönetilen kimliği tarafından eklenir ve yalnızca aynı yönetilen kimliğe göre yapıt kaynaklarından kaldırılabilir. Bu güvenlik ölçüsü, kilitleme mekanizmasını zorlar ve Azure şemaları dışında şema kilidinin kaldırılmasını engeller.

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="Bir kaynak grubu için erişim denetimi (I M) sayfasının ve atamaları reddetme sekmesinin ekran görüntüsü." border="false":::

Her modun [reddetme atama özellikleri](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) aşağıdaki gibidir:

|Mod |İzinler. eylemler |Permissions. NotActions |Sorumlular [i]. Türüyle |Excludesorumlularını [i]. Numarasını | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Salt Okunur |**\** _ |_ *\* **<br />** /Microsoft. Authorization/kilitleri oku/Sil/ **<br />** Microsoft. Network/virtualNetwork/alt ağları/JOIN/Action** |SystemDefined (herkes) |**Excludedsorumlularını** içinde şema atama ve Kullanıcı tanımlı |Kaynak grubu- _true_; Kaynak- _yanlış_ |
|Silme |**\*/Delete** | **Microsoft. Authorization/kilitleri/silme**<br />**Microsoft. Network/virtualNetwork/alt ağlar/JOIN/Action** |SystemDefined (herkes) |**Excludedsorumlularını** içinde şema atama ve Kullanıcı tanımlı |Kaynak grubu- _true_; Kaynak- _yanlış_ |

> [!IMPORTANT]
> Azure Resource Manager, rol atama ayrıntılarını 30 dakikaya kadar önbelleğe alır. Sonuç olarak, şemayı reddetme, şema kaynaklarını reddetme eylemini reddetme işlemleri hemen etkili olmayabilir. Bu süre boyunca, BLUEPRINT kilitleri tarafından korunması amaçlanan bir kaynağı silmek mümkün olabilir.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Bir sorumluyu reddetme atamasından dışlama

Bazı tasarım veya güvenlik senaryolarında, şema atamasının oluşturduğu [reddetme atamasından](../../../role-based-access-control/deny-assignments.md) bir sorumluyu dışlamak gerekebilir. Bu adım, [atamayı oluştururken](/rest/api/blueprints/assignments/createorupdate) **kilitler** özelliğindeki **excludedsorumlularını** dizisine en fazla beş değer eklenerek REST API yapılır. Aşağıdaki atama tanımı, **Excludedsorumlularını** içeren bir istek gövdesi örneğidir:

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>Reddetme atamasından bir eylemi hariç tutma

Bir şema atamasında bir [reddetme atamasında](../../../role-based-access-control/deny-assignments.md) [sorumluyu dışlamama](#exclude-a-principal-from-a-deny-assignment) benzer şekilde, belirli [Azure Kaynak sağlayıcısı işlemlerini](../../../role-based-access-control/resource-provider-operations.md)dışarıda bırakabilirsiniz. **Properties. kilitleri** bloğunda, **excludedsorumlularını** aynı yerde, bir **excludedadıctions** eklenebilir:

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

**Excludedsorumlularını** açık olmalıdır, **excludedadctions** girişleri, `*` kaynak sağlayıcısı işlemlerinin joker karakter eşleştirmesinin kullanımını kullanabilirler.

## <a name="next-steps"></a>Sonraki adımlar

- [Yeni kaynakları koru](../tutorials/protect-new-resources.md) öğreticisini izleyin.
- [Şema yaşam döngüsü](./lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](./parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](./sequencing-order.md) özelleştirmeyi öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin.
- [Genel sorun giderme](../troubleshoot/general.md) adımlarıyla şema atama sorunlarını giderin.
