---
title: Kaynak kilitlemeyi anlama
description: Şeması atarken kaynakları korumak için kilitleme seçenekleri hakkında bilgi edinin.
ms.date: 04/24/2019
ms.topic: conceptual
ms.openlocfilehash: 754b9d7f73c6111abf7505e222a1ca5a8712ae45
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960477"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Azure şemaları 'nda kaynak kilitlemeyi anlama

Ölçekteki tutarlı ortamların oluşturulması, bu tutarlılığı sürdürmek için bir mekanizma varsa gerçek anlamda değerlidir. Bu makalede, Azure şemaları 'nda kaynak kilitleme 'nin nasıl çalıştığı açıklanmaktadır. Kaynak kilitleme ve _reddetme atamalarından_oluşan bir örnek görmek için bkz. [yeni kaynakları koruma](../tutorials/protect-new-resources.md) öğreticisi.

## <a name="locking-modes-and-states"></a>Kilitleme modları ve durumlar

Kilitleme modu, şema ataması için geçerlidir ve üç seçeneğe sahiptir: **kilitleme**, **salt okuma**veya **silme**. Kilitleme modu, bir şema ataması sırasında yapıt dağıtımı sırasında yapılandırılır. Şema ataması güncelleştirilerek farklı bir kilitleme modu ayarlanabilir.
Ancak, kilitleme modları, şema dışında değiştirilemez.

Şema atamasında yapıtlar tarafından oluşturulan kaynakların dört durumu vardır: **kilitli değil**, **salt okunurdur**, **düzenleme/silme yapılamaz** **veya silinemez**. Her yapıt türü **kilitli değil** durumunda olabilir. Aşağıdaki tablo bir kaynağın durumunu belirlemede kullanılabilir:

|Mod|Yapıt kaynak türü|Durum|Açıklama|
|-|-|-|-|
|Kilitleme|*|Kilitlenmedi|Kaynaklar, planlar tarafından korunmuyor. Bu durum, bir **salt okunurdur** veya bir şema atamasının dışında kaynak grubu yapıtı **silme** ' ya eklenen kaynaklar için de kullanılır.|
|Salt Okunur|Kaynak grubu|Düzenleme/silme yapılamıyor|Kaynak grubu salt okunurdur ve kaynak grubundaki Etiketler değiştirilemez. **Kilitli** kaynaklar bu kaynak grubundan eklenebilir, taşınabilir, değiştirilebilir veya silinebilir.|
|Salt Okunur|Kaynak olmayan Grup|Salt Okunur|Kaynak hiçbir şekilde değiştirilemez--değişiklik yok ve silinemez.|
|Silme|*|Silinemiyor|Kaynaklar değiştirilebilir, ancak silinemez. **Kilitli** kaynaklar bu kaynak grubundan eklenebilir, taşınabilir, değiştirilebilir veya silinebilir.|

## <a name="overriding-locking-states"></a>Kilitleme durumlarını geçersiz kılma

Genellikle abonelikte, ' sahip ' rolü gibi uygun [rol tabanlı erişim denetimi](../../../role-based-access-control/overview.md) (RBAC) ile herhangi bir kaynağı değiştirme veya silme izni verilmesi olasıdır. Bu erişim, bir dağıtılan atama kapsamında, şemalar kilitlemeyi uygularken durum değildir. Atama salt **okuma** veya **silme** seçeneği ile ayarlandıysa, abonelik sahibi korumalı kaynak üzerinde engellenmiş eylemi gerçekleştirebilir.

Bu güvenlik ölçüsü, tanımlanan şema 'in ve yanlışlıkla ya da programlı silme veya değiştirme işleminden oluşturmakta tasarlanan ortamın tutarlılığını korur.

## <a name="removing-locking-states"></a>Kilitleme durumları kaldırılıyor

Atama tarafından korunan bir kaynağı değiştirmek veya silmek için gerekli hale gelirse, bunu iki şekilde yapabilirsiniz.

- Şema atamasını kilitleme **moduna yükseltme**
- Şema atamasını silme

Atama kaldırıldığında, planlar tarafından oluşturulan kilitler kaldırılır. Ancak, kaynak arka planda bırakılır ve normal yollarla silinmelidir.

## <a name="how-blueprint-locks-work"></a>Şema kilitleri nasıl çalışır?

Atama **yalnızca okuma** veya **silme** seçeneği belirlenmişse, bir şema atama sırasında yapıt [kaynaklarına reddetme izni reddetme eylemi](../../../role-based-access-control/deny-assignments.md) uygulanır. Reddetme eylemi, BLUEPRINT atamasının yönetilen kimliği tarafından eklenir ve yalnızca aynı yönetilen kimliğe göre yapıt kaynaklarından kaldırılabilir. Bu güvenlik ölçüsü, kilitleme mekanizmasını zorlar ve şema kilidi 'nin şemalar dışında kaldırılmasını önler.

![Kaynak grubunda Blueprint reddetme ataması](../media/resource-locking/blueprint-deny-assignment.png)

Her modun [reddetme atama özellikleri](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) aşağıdaki gibidir:

|Mod |İzinler. eylemler |Permissions. NotActions |Sorumlular [i]. Türüyle |Excludesorumlularını [i]. Numarasını | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Salt Okunur |**\*** |**\*/Read** |SystemDefined (herkes) |**Excludedsorumlularını** içinde şema atama ve Kullanıcı tanımlı |Kaynak grubu- _true_; Kaynak- _yanlış_ |
|Silme |**\*/Delete** | |SystemDefined (herkes) |**Excludedsorumlularını** içinde şema atama ve Kullanıcı tanımlı |Kaynak grubu- _true_; Kaynak- _yanlış_ |

> [!IMPORTANT]
> Azure Resource Manager, rol atama ayrıntılarını 30 dakikaya kadar önbelleğe alır. Sonuç olarak, şemayı reddetme, şema kaynaklarını reddetme eylemini reddetme işlemleri hemen etkili olmayabilir. Bu süre boyunca, BLUEPRINT kilitleri tarafından korunması amaçlanan bir kaynağı silmek mümkün olabilir.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Bir sorumluyu reddetme atamasından dışlama

Bazı tasarım veya güvenlik senaryolarında, şema atamasının oluşturduğu [reddetme atamasından](../../../role-based-access-control/deny-assignments.md) bir sorumluyu dışlamak gerekebilir. Bu, [atamayı oluştururken](/rest/api/blueprints/assignments/createorupdate) **kilitler** özelliğindeki **excludedsorumlularını** dizisine en fazla beş değer eklenerek REST API yapılır.
Bu, **Excludedsorumlularını**içeren bir istek gövdesi örneğidir:

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

## <a name="next-steps"></a>Sonraki adımlar

- [Yeni kaynakları koru](../tutorials/protect-new-resources.md) öğreticisini izleyin.
- [Şema yaşam döngüsü](lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](sequencing-order.md) özelleştirmeyi öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin.
- [Genel sorun giderme](../troubleshoot/general.md) adımlarıyla şema atama sorunlarını giderin.