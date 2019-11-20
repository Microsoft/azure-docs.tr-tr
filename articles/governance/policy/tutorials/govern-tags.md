---
title: Etiket idaresini yönetme
description: Yeni ve mevcut kaynaklarda bir etiket idare modeli oluşturmak ve zorlamak için Azure Ilkesinin değiştirme efektini kullanın.
ms.date: 11/04/2019
ms.topic: tutorial
ms.openlocfilehash: edb74bce5758ae040a6170a8e73be75fc228b001
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74069670"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Öğretici: Azure Ilkesiyle etiket yönetimini yönetme

[Etiketler](../../../azure-resource-manager/resource-group-using-tags.md) , Azure kaynaklarınızı bir taksonomi halinde düzenlemenin önemli bir parçasıdır. [Etiket yönetimi için en iyi yöntemleri](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)takip eden Etiketler, Azure ilkesi ile iş ilkelerinizi uygulamak veya [maliyet yönetimi ile maliyetleri izlemek](../../../cost-management/cost-mgt-best-practices.md#organize-and-tag-your-resources)için temel olabilir.
Etiketlerin nasıl veya neden kullanıldığı önemli değildir. bu etiketleri Azure kaynaklarınıza hızlıca ekleyebilir, değiştirebilir ve kaldırabilirsiniz.

Azure Ilkesinin [değişiklik](../concepts/effects.md#modify) etkisi, hangi kaynak İdaresi aşamasına bakılmaksızın etiketlerin tasarlanmasına yardımcı olmak için tasarlanmıştır. **Değiştirme** şu durumlarda yardımcı olur:

- Buluta yeni başladıysanız ve hiçbir etiket yönetimi yok
- Etiket yönetimi olmayan binlerce kaynak zaten var
- Zaten değiştirmeniz gereken bir taksonominin var

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="identify-requirements"></a>Gereksinimleri tanımla

İdare denetimlerinin iyi uygulamaları gibi, gereksinimler iş gereksiniminizden gelmelidir ve teknik denetimler oluşturmadan önce iyi anlaşılmalıdır. Bu senaryo öğreticisinde, şu öğeler iş gereksinimlerimiz:

- Tüm kaynaklarda gerekli iki etiket: _Costcenter_ ve _env_
- Tüm kapsayıcılarda ve tek kaynaklarda _Costcenter_ bulunmalıdır
  - Kaynaklar içindeki kapsayıcılardan devralınır, ancak bağımsız olarak geçersiz kılınabilen
- _Env_ tüm kapsayıcılarda ve tek tek kaynaklarda bulunmalıdır
  - Kaynaklar, kapsayıcıyı kapsayıcı adlandırma şemasına göre belirlenir ve geçersiz kılınmayabilir
  - Bir kapsayıcıdaki tüm kaynaklar aynı ortamın bir parçası

## <a name="configure-the-costcenter-tag"></a>CostCenter etiketini yapılandırma

Azure Ilkesi tarafından yönetilen bir Azure ortamına özgü şartlar altında, _Costcenter_ etiket gereksinimleri aşağıdakileri çağırır:

- _Costcenter_ etiketini eksik kaynak gruplarını reddetme
- Eksik olduğunda üst kaynak grubundan _Costcenter_ etiketini eklemek için kaynakları değiştirin

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>CostCenter etiketini eksik kaynak gruplarını reddetme

Kaynak grubunun _Costcenter_ 'ı kaynak grubunun adı tarafından belirlenemediğinden, kaynak grubunu oluşturmak için istekte tanımlanmış etikete sahip olmalıdır. [Reddetme](../concepts/effects.md#deny) etkisi olan aşağıdaki ilke kuralı, _costcenter_ etiketine sahip olmayan kaynak gruplarının oluşturulmasını veya güncelleştirilmesini engeller:

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> Bu ilke kuralı bir kaynak grubunu hedeflediğinden, ilke tanımındaki _mod_ ' Indexed ' yerine ' All ' olmalıdır.

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Eksik olduğunda CostCenter etiketini devralacak kaynakları değiştirme

İkinci _Costcenter_ gereksinimi, eksik olduğunda üst kaynak grubundaki etiketi devralması için tüm kaynaklara yöneliktir. Etiket zaten kaynak üzerinde tanımlanmışsa, üst kaynak grubundan farklı olsa bile, tek başına bırakılması gerekir. Aşağıdaki ilke kuralı [değiştirme](../concepts/effects.md#modify)kullanır:

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

Bu ilke kuralı, mevcut [kaynakları düzeltirken etiket](../how-to/remediate-resources.md) değerini değiştirmek Istediğimiz Için **addorreplace** yerine **Add** işlemini kullanır. Ayrıca, üst kaynak grubundan etiket değerini almak için `[resourcegroup()]` şablonu işlevini kullanır.

> [!NOTE]
> Bu ilke kuralı, etiketleri destekleyen kaynakları hedeflediğinden, ilke tanımındaki _mod_ ' dizinli ' olmalıdır. Bu yapılandırma ayrıca bu ilkenin kaynak gruplarını atmasını de sağlar.

## <a name="configure-the-env-tag"></a>Env etiketini yapılandırma

Azure Ilkesi tarafından yönetilen bir Azure ortamına özgü şartlar altında, _env_ etiketi gereksinimleri aşağıdakileri çağırır:

- Kaynak grubunun adlandırma şemasına bağlı olarak kaynak grubundaki _env_ etiketini değiştirme
- Kaynak grubundaki tüm kaynaklardaki _env_ etiketini üst kaynak grubuyla aynı olacak şekilde değiştirin

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Kaynak gruplarını ad temelinde env etiketini değiştirme

Azure ortamınızda bulunan her ortam için bir [değiştirme](../concepts/effects.md#modify) ilkesi gerekir. Her biri için değiştirme ilkesi, bu ilke tanımına benzer bir şekilde görünür:

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> Bu ilke kuralı bir kaynak grubunu hedeflediğinden, ilke tanımındaki _mod_ ' Indexed ' yerine ' All ' olmalıdır.

Bu ilke yalnızca `prd-`üretim kaynakları için kullanılan örnek adlandırma düzenine sahip kaynak gruplarıyla eşleşir. Daha karmaşık adlandırma düzenleri, bu örnekte **olduğu gibi** birkaç **eşleşme** koşullarıyla elde edilebilir.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Env etiketini devralacak kaynakları değiştirme

İş gereksinimi, tüm kaynakların üst kaynak grubunun yaptığı _env_ etiketine sahip olacak şekilde çağrı yapar. Bu etiket geçersiz kılınamıyor, bu yüzden [değiştirme](../concepts/effects.md#modify) efektiyle **addorreplace** işlemini kullanacağız. Örnek değiştirme ilkesi aşağıdaki kurala benzer şekilde görünür:

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> Bu ilke kuralı, etiketleri destekleyen kaynakları hedeflediğinden, ilke tanımındaki _mod_ ' dizinli ' olmalıdır. Bu yapılandırma ayrıca bu ilkenin kaynak gruplarını atmasını de sağlar.

Bu ilke kuralı, _env_ etiketi için üst kaynak grupları değerine sahip olmayan veya _env_ etiketi eksik olan herhangi bir kaynağı arar. Etiket, kaynak üzerinde zaten var olsa da farklı bir değerle, eşleşen kaynakların _env_ etiketi, üst kaynak grupları değeri olarak ayarlanmıştır.

## <a name="assign-the-initiative-and-remediate-resources"></a>Girişimi ata ve kaynakları düzelt

Yukarıdaki etiket ilkeleri oluşturulduktan sonra, etiket yönetimi için tek bir girişimde birleştirin ve bunları bir yönetim grubuna veya aboneliğine atayın. Girişim ve dahil olan ilkeler, var olan kaynakların uyumluluğunu değerlendirir ve ilke kuralındaki **IF** özelliği ile eşleşen yeni veya güncelleştirilmiş kaynaklar için istekleri değiştirir. Ancak, ilke, mevcut uyumlu olmayan kaynakları tanımlanan etiket değişiklikleriyle otomatik olarak güncelleştirmez.

[Deployifnotexists](../concepts/effects.md#deployifnotexists) ilkeleri gibi, **değiştirme** ilkesi, var olan uyumlu olmayan kaynakları değiştirmek için düzeltme görevlerini kullanır. Uyumlu olmayan **değişiklik** kaynaklarınızı belirlemek için [kaynakları](../how-to/remediate-resources.md) düzeltme yönergelerini izleyin ve etiketleri tanımlanan taksonominiz için düzeltin.

## <a name="review"></a>Gözden geçirme

Bu öğreticide, aşağıdaki görevler hakkında bilgi edindiniz:

> [!div class="checklist"]
> - İş gereksinimlerinizi tanımladı
> - Her gereksinimi bir ilke tanımına eşlendi
> - Etiket ilkeleri bir girişim olarak gruplandırılır

## <a name="next-steps"></a>Sonraki adımlar

İlke tanımlarının yapıları hakkında daha fazla bilgi edinmek için şu makaleyi gözden geçirin:

> [!div class="nextstepaction"]
> [Azure İlkesi tanım yapısı](../concepts/definition-structure.md)
