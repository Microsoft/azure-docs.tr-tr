---
title: 'Öğretici: etiket yönetimini yönetme'
description: Bu öğreticide, yeni ve mevcut kaynaklarda bir etiket idare modeli oluşturmak ve zorlamak için Azure Ilkesinin değiştirme efektini kullanırsınız.
ms.date: 03/31/2021
ms.topic: tutorial
ms.openlocfilehash: 1e8850dd6fe380b0ea38a2a1722734d917c63d0c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093291"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Öğretici: Azure Ilkesiyle etiket yönetimini yönetme

[Etiketler](../../../azure-resource-manager/management/tag-resources.md) , Azure kaynaklarınızı bir taksonomi halinde düzenlemenin önemli bir parçasıdır. [Etiket yönetimi için en iyi yöntemleri](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)takip eden Etiketler, Azure ilkesi ile iş ilkelerinizi uygulamak veya [maliyet yönetimi ile maliyetleri izlemek](../../../cost-management-billing/costs/cost-mgt-best-practices.md#tag-shared-resources)için temel olabilir.
Etiketlerin nasıl veya neden kullanıldığı önemli değildir. bu etiketleri Azure kaynaklarınıza hızlıca ekleyebilir, değiştirebilir ve kaldırabilirsiniz. Azure kaynağınızın etiketlemeyi destekleyip desteklemediğini görmek için bkz. [etiket desteği](../../../azure-resource-manager/management/tag-support.md).

Azure Ilkesinin [değişiklik](../concepts/effects.md#modify) etkisi, hangi kaynak İdaresi aşamasına bakılmaksızın etiketlerin tasarlanmasına yardımcı olmak için tasarlanmıştır. **Değiştirme** şu durumlarda yardımcı olur:

- Buluta yeni başladıysanız ve hiçbir etiket yönetimi yok
- Etiket yönetimi olmayan binlerce kaynak zaten var
- Zaten değiştirmeniz gereken bir taksonominin var

Bu öğreticide, aşağıdaki görevleri tamamlayacaksınız:

> [!div class="checklist"]
> - İş gereksinimlerinizi belirlemek
> - Her gereksinimi bir ilke tanımına eşleyin
> - Etiket ilkelerini bir girişim olarak gruplayın

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için bir Azure aboneliğinizin olması gerekir. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="identify-requirements"></a>Gereksinimleri tanımla

İdare denetimlerinin iyi uygulamaları gibi, gereksinimler iş gereksiniminizden gelmelidir ve teknik denetimler oluşturmadan önce iyi anlaşılmalıdır. Bu senaryo öğreticisinde, şu öğeler iş gereksinimlerimiz:

- Tüm kaynaklarda gerekli iki etiket: _Costcenter_ ve _env_
- Tüm kapsayıcılarda ve tek kaynaklarda _Costcenter_ bulunmalıdır
  - Kaynaklar içindeki kapsayıcılardan devralınır, ancak bağımsız olarak geçersiz kılınabilen
- _Env_ tüm kapsayıcılarda ve tek tek kaynaklarda bulunmalıdır
  - Kaynaklar, kapsayıcıyı kapsayıcı adlandırma şemasına göre belirlenir ve geçersiz kılınmayabilir
  - Bir kapsayıcıdaki tüm kaynaklar aynı ortamın bir parçası

## <a name="configure-the-costcenter-tag"></a>CostCenter etiketini yapılandırma

Azure Ilkesi tarafından yönetilen bir Azure ortamına özgü şartlar altında, _Costcenter_ etiket gereksinimleri aşağıdaki sonuçlar için çağrı sağlar:

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

Bu ilke kuralı, mevcut [kaynakları düzeltirken etiket](../how-to/remediate-resources.md) değerini değiştirmek Istediğimiz Için **addorreplace** yerine **Add** işlemini kullanır. Ayrıca, `[resourcegroup()]` üst kaynak grubundan etiket değerini almak için şablon işlevini kullanır.

> [!NOTE]
> Bu ilke kuralı, etiketleri destekleyen kaynakları hedeflediğinden, ilke tanımındaki _mod_ ' dizinli ' olmalıdır. Bu yapılandırma ayrıca bu ilkenin kaynak gruplarını atmasını de sağlar.

## <a name="configure-the-env-tag"></a>Env etiketini yapılandırma

Azure Ilkesi tarafından yönetilen bir Azure ortamına özgü şartlar altında, _env_ etiketi gereksinimleri aşağıdaki sonuçlar için çağrı sağlar:

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
        },
        {
            "field": "tags['Env']",
            "notEquals": "Production"
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

Bu ilke yalnızca üretim kaynakları için kullanılan örnek adlandırma düzenine sahip kaynak gruplarıyla eşleşir `prd-` . Daha karmaşık adlandırma düzenleri, bu örnekte **olduğu gibi** birkaç **eşleşme** koşullarıyla elde edilebilir.

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

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticideki kaynaklarla çalışmayı bitirdiğinizde, yukarıda oluşturulan atamalardan veya tanımlardan birini silmek için aşağıdaki adımları kullanın:

1. Azure Ilkesi sayfasının sol tarafında **yazma** ' nın altında **tanımlar** (veya atamayı silmeye çalışıyorsanız **atamalar** ) ' ı seçin.

1. Kaldırmak istediğiniz yeni girişim veya tanımını (ya da atamayı) arayın.

1. Satıra sağ tıklayın ya da tanımın (veya atamanın) sonundaki üç noktayı seçip **Tanımı sil** (veya **Atamayı sil**) öğesini seçin.

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