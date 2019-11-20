---
title: Efektlerin nasıl çalıştığını anlama
description: Azure Ilke tanımlarının uyumluluğun nasıl yönetildiğini ve raporlanmadığını belirten çeşitli etkileri vardır.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c345f96ef5176c4afb3d46aaca2e9903c7911fb1
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959899"
---
# <a name="understand-azure-policy-effects"></a>Azure İlkesi etkilerini anlama

Azure İlkesi her ilke tanımında, tek bir etkiye sahiptir. Bu etkiyi ilke kuralı eşleşecek şekilde değerlendirildiğinde ne olacağını belirler. Yeni bir kaynak, güncelleştirilmiş bir kaynak veya mevcut bir kaynak için olmaları durumunda etkileri farklı davranır.

Bu efektler Şu anda bir ilke tanımında destekleniyor:

- [Ýna](#append)
- [Denetim](#audit)
- [Auditınotexists](#auditifnotexists)
- [Reddedebilir](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Devre dışı](#disabled)
- [Enforceopaconstraint](#enforceopaconstraint) (Önizleme)
- [Enforceregopolicy](#enforceregopolicy) (Önizleme)
- [Değiştirebilirler](#modify)

## <a name="order-of-evaluation"></a>Değerlendirme sırası

Azure Resource Manager aracılığıyla kaynak oluşturma veya güncelleştirme istekleri, önce Azure Ilkesi tarafından değerlendirilir. Azure Ilkesi, kaynak için uygulanan tüm atamaların bir listesini oluşturur ve ardından kaynağı her bir tanıma karşı değerlendirir. Azure Ilkesi, isteği uygun kaynak sağlayıcısına teslim etmeden önce çeşitli etkileri işler. Bunun yapılması, bir kaynak Azure Ilkesinin tasarlanan idare denetimlerini karşılamıyorsa kaynak sağlayıcısı tarafından gereksiz işlemeyi önler.

- **Devre dışı bırakılmış** önce ilke kuralı değerlendirileceğini belirlemek için denetlenir.
- **Ekleme** ve **değiştirme** daha sonra değerlendirilir. Bu, isteği değiştiremediği için bir değişiklik, bir denetim veya reddetme efektinin tetiklemesini engelleyebilir.
- **Reddetme** ardından değerlendirilir. Değerlendirerek reddetme denetim önce istenmeyen bir kaynağın çift günlük kaydı engellenir.
- **Denetim** giden kaynak sağlayıcıya isteği önce değerlendirilir.

Kaynak sağlayıcı bir başarı kodu döndürür sonra **AuditIfNotExists** ve **Deployıfnotexists** ek uyumluluk günlük kaydı veya eylem gerekli olup olmadığını belirlemek için değerlendirin.

Şu anda **Enforceopaconstraint** veya **Enforceregopolicy** etkileri için herhangi bir değerlendirme sırası yoktur.

## <a name="disabled"></a>Devre dışı

Bu etkiyi durumlarda test etmek veya ne zaman ilke tanımı etkisi parametreli için kullanışlıdır. Bu esneklik, bu ilkenin atamalarının tümünü devre dışı bırakmak yerine tek bir atama devre dışı bırakmak mümkün kılar.

Devre dışı bırakılmış etkine bir alternatif, ilke atamasında ayarlanan **Enforcementmode** ' dır.
**Enforcementmode** _devre dışı bırakıldığında_, kaynaklar yine de değerlendirilir. Etkinlik günlükleri ve ilke efekti gibi günlüğe kaydetme gerçekleşmez. Daha fazla bilgi için bkz. [ilke atama-zorlama modu](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Ekle

Append oluşturma veya güncelleştirme sırasında istenen kaynak için ek alanlar eklemek için kullanılır. Ortak bir örnek, bir depolama kaynağı için izin verilen IP 'Leri belirtmektir.

> [!IMPORTANT]
> Append etiketi olmayan özelliklerle kullanılmak üzere tasarlanmıştır. Append, oluşturma veya güncelleştirme isteği sırasında bir kaynağa etiket ekleyese de, bunun yerine Etiketler için [değiştirme](#modify) efektini kullanmanız önerilir.

### <a name="append-evaluation"></a>Değerlendirme Ekle

Ekleme isteği, oluşturma veya bir kaynağın güncelleştirilmesi sırasında bir kaynak sağlayıcısı tarafından işlenen önce değerlendirilir. Append kaynağa alanları ekler, **varsa** ilke kuralının koşul karşılanıyorsa. Append etkili bir değer özgün istek farklı bir değerle geçersiz kılarsınız, reddetme etkisi davranır ve isteği reddeder. Varolan bir diziye yeni bir değer eklemek için diğer adın **[\*]** sürümünü kullanın.

Append efekt kullanarak bir ilke tanımı bir değerlendirme döngüsü bir parçası olarak çalıştırdığınızda, zaten mevcut olan kaynakları değişiklik yapmaz. Bunun yerine, bunu karşılayan herhangi bir kaynağa işaretler **varsa** uyumsuz olarak koşul.

### <a name="append-properties"></a>Özellikler ekleme

Bir ekleme yalnızca etkisi bir **ayrıntıları** gerekli olan bir dizi. Olarak **ayrıntıları** bir dizi ya da tek bir sürebilir **alan/değer** bölümleri veya katları çifti. Başvurmak [tanım yapısı](definition-structure.md#fields) için kabul edilebilir alanların listesi.

### <a name="append-examples"></a>Append örnekleri

Örnek 1: bir depolama hesabında IP kuralları ayarlamak için bir dizi **değeri** olan **[\*]** olmayan bir [diğer ad](definition-structure.md#aliases) kullanan tek **alan/değer** çifti. **[\*]** olmayan diğer ad bir dizi olduğunda, efekt **değeri** tüm dizi olarak ekler. Dizi zaten varsa, çakışmadan bir reddetme olayı oluşur.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Örnek 2: bir depolama hesabında IP kuralları ayarlamak için bir dizi **değeri** ile bir **[\*]** [diğer adı](definition-structure.md#aliases) kullanan tek **alan/değer** çifti. **[\*]** diğer adını kullanarak, efekt **değeri** önceden mevcut olabilecek bir diziye ekler. Dizi henüz yoksa, oluşturulur.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="modify"></a>Değiştir

Değişiklik, oluşturma veya güncelleştirme sırasında bir kaynağa etiket eklemek, güncelleştirmek veya kaldırmak için kullanılır. Ortak bir örnek, costCenter gibi kaynaklardaki etiketleri güncelleştirmedir. Hedef kaynak bir kaynak grubu değilse, bir değiştirme ilkesinde `mode` her zaman _dizinli_ olarak ayarlanmış olması gerekir. Mevcut uyumlu olmayan kaynaklar bir [Düzeltme göreviyle](../how-to/remediate-resources.md)düzeltilebilir. Tek bir değiştirme kuralında herhangi bir sayıda işlem olabilir.

> [!IMPORTANT]
> Şu anda yalnızca etiketleriyle kullanım için değiştirin. Etiketleri yönetiyorsanız, Değiştir yerine Değiştir kullanılması önerilir, ek işlem türleri ve mevcut kaynakları düzeltme yeteneği sağlar. Bununla birlikte, yönetilen bir kimlik oluşturabilebilmeniz durumunda ekleme önerilir.

### <a name="modify-evaluation"></a>Değerlendirmeyi Değiştir

Değişiklik, bir kaynağın oluşturulması veya güncelleştirilmesi sırasında istek bir kaynak sağlayıcısı tarafından işlenmeden önce değerlendirilir. İlke kuralının **IF** koşulu karşılandığında, bir kaynaktaki ekleme veya güncelleştirme etiketlerini değiştirme.

Değişiklik efektini kullanan bir ilke tanımı, değerlendirme döngüsünün bir parçası olarak çalıştırıldığında, zaten mevcut olan kaynaklarda değişiklik yapmaz. Bunun yerine, bunu karşılayan herhangi bir kaynağa işaretler **varsa** uyumsuz olarak koşul.

### <a name="modify-properties"></a>Özellikleri Değiştir

Değişiklik efektinin **Ayrıntılar** özelliği, düzeltme için gereken izinleri ve etiket değerlerini eklemek, güncelleştirmek veya kaldırmak için kullanılan **işlemleri** tanımlayan tüm alt özellikleri içerir.

- **roleDefinitionIds** [gerekli]
  - Bu özellik, rol tabanlı erişim denetimine rol kimliği erişilebilir tarafından eşleşen bir dize dizisi içermesi gerekir. Daha fazla bilgi için [düzeltme - ilke tanımı yapılandırma](../how-to/remediate-resources.md#configure-policy-definition).
  - Tanımlanan rol, [katkıda bulunan](../../../role-based-access-control/built-in-roles.md#contributor) rolüne verilen tüm işlemleri içermelidir.
- **işlemler** [gerekli]
  - Eşleşen kaynaklarda tamamlanacak tüm etiket işlemlerinin dizisi.
  - Özelliklerinin
    - **işlem** [gerekli]
      - Eşleşen bir kaynakta hangi eylemin yapılacağını tanımlar. Seçenekler şunlardır: _Addorreplace_, _Add_, _Remove_. _Ekleme_ [efektine](#append) benzer şekilde davranır.
    - **alan** [gerekli]
      - Eklenecek, değiştirilecek veya kaldırılacak etiket. Etiket adları diğer [alanlar](./definition-structure.md#fields)için aynı adlandırma kuralına uymalıdır.
    - **değer** (isteğe bağlı)
      - Etiketi ayarlanacak değer.
      - **Işlem** _addorreplace_ veya _Add_ise bu özellik gereklidir.

### <a name="modify-operations"></a>İşlemleri değiştirme

**Operations** özelliği dizisi, birkaç etiketi tek bir ilke tanımından farklı şekillerde değiştirmeyi mümkün kılar. Her işlem, **işlem**, **alan**ve **değer** özelliklerinden oluşur. İşlem düzeltme görevinin etiketlere ne yaptığını belirler, alan hangi etiketin değiştirilmekte olduğunu belirler ve değer bu etiketin yeni ayarını tanımlar. Aşağıdaki örnekte aşağıdaki etiket değişiklikleri yapılır:

- Farklı bir değerle zaten mevcut olsa bile, `environment` etiketini "test" olarak ayarlar.
- `TempResource`etiketini kaldırır.
- `Dept` etiketini ilke atamasında yapılandırılan _Deptname_ ilke parametresi olarak ayarlar.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

**Operation** özelliği aşağıdaki seçeneklere sahiptir:

|İşlem |Açıklama |
|-|-|
|addOrReplace |Etiket, farklı bir değerle zaten mevcut olsa bile, kaynağa tanımlı etiketi ve değeri ekler. |
|Ekle |Kaynağa tanımlı etiketi ve değeri ekler. |
|Kaldır |Tanımlanan etiketi kaynaktan kaldırır. |

### <a name="modify-examples"></a>Örnekleri Değiştir

Örnek 1: `environment` etiketini ekleyin ve var olan `environment` etiketlerini "test" ile değiştirin:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Örnek 2: `env` etiketini kaldırın ve `environment` etiketini ekleyin ya da var olan `environment` etiketlerini parametreli bir değerle değiştirin:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

## <a name="deny"></a>Reddet

Reddetme istek başarısız olur ve bir ilke tanımı ile tanımlanan standartları eşleşmiyor kaynak isteğiyle önlemek için kullanılır.

### <a name="deny-evaluation"></a>Değerlendirme Reddet

Ne zaman oluşturulurken veya güncelleştirilirken bir eşleşen kaynak Reddet isteği kaynak sağlayıcıya gönderilmeden önce engeller. İstek olarak döndürülen bir `403 (Forbidden)`. Portalda, Yasak bir ilke ataması tarafından engellendi dağıtım durumu olarak görüntülenebilir.

Var olan kaynakların değerlendirmesi sırasında bir reddetme ilke tanımı eşleşen kaynak uyumlu değil olarak işaretlenir.

### <a name="deny-properties"></a>Özellikleri Reddet

Ek özellikleri kullanmak için reddetme etkisinin yok **ardından** ilke tanımının koşul.

### <a name="deny-example"></a>Örnek Reddet

Örnek: reddetme etkisinin kullanma.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Denetim

Denetim etkinlik günlüğünde, uyumlu olmayan bir kaynak değerlendirirken uyarı olayı oluşturmak için kullanılır, ancak istek bitmez.

### <a name="audit-evaluation"></a>Denetim değerlendirme

Denetim, bir kaynağın oluşturulması veya güncelleştirilmesi sırasında Azure Ilkesi tarafından denetlenen son etkiye sahiptir. Daha sonra Azure Ilkesi kaynağı kaynak sağlayıcısına gönderir. Denetim için kaynak isteğiyle ve değerlendirme döngüsü aynı şekilde çalışır. Azure Ilkesi, etkinlik günlüğüne bir `Microsoft.Authorization/policies/audit/action` işlemi ekler ve kaynağı uyumlu değil olarak işaretler.

### <a name="audit-properties"></a>Denetim Özellikleri

Ek özellikleri kullanmak için bir denetim etkisi yoktur **ardından** ilke tanımının koşul.

### <a name="audit-example"></a>Denetim örneği

Örnek: denetim etkisiyle kullanma.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists sağlayan eşleşen kaynak denetim **varsa** koşulu, belirtilen bileşenleri yok ancak **ayrıntıları** , **ardından** koşul.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists değerlendirme

Bir kaynak sağlayıcısı oluşturma veya güncelleştirme kaynak isteğiyle işlediği ve bir başarı durum kodu döndürdü sonra AuditIfNotExists çalıştırır. Hiçbir ilgili kaynaklar varsa veya kaynaklar tarafından tanımlanan denetim gerçekleşir **ExistenceCondition** doğru olarak değerlendirilebilmesi yok. Azure Ilkesi, denetim efektine benzer bir şekilde etkinlik günlüğüne bir `Microsoft.Authorization/policies/audit/action` işlemi ekler. Tetiklendiğinde, memnun kaynak **varsa** ile uyumsuz olarak işaretlenmiş kaynak bir durumdur.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists özellikleri

**Ayrıntıları** eşleştirmek için ilgili kaynakları tanımlayan tüm alt AuditIfNotExists etkileri özelliğine sahiptir.

- **Tür** [gerekli]
  - Eşleştirmek için ilgili kaynak türünü belirtir.
  - **Ayrıntılar. Type** , **IF** koşulu kaynağı altında bir kaynak türü ise, ilke değerlendirilen kaynağın kapsamındaki bu **türden** kaynakları sorgular. Aksi takdirde, ilke, değerlendirilen kaynakla aynı kaynak grubu içinde sorgular.
- **Ad** (isteğe bağlı)
  - Eşleştirilecek kaynak tam adını belirtir ve belirli bir kaynak belirtilen türdeki tüm kaynakları yerine getirmek ilke neden olur.
  - **IF. Field. Type** ve **then. details. Type** ile ilgili koşul değerleri ne zaman eşleşiyorsa, **ad** _gerekli_ olur ve `[field('name')]`olmalıdır. Ancak, bunun yerine bir [Denetim](#audit) etkisi göz önünde bulundurulmalıdır.
- **ResourceGroupName** (isteğe bağlı)
  - İlişkili kaynağın farklı bir kaynak grubundan gelen eşleşen sağlar.
  - Varsa geçerli değildir **türü** altında olan bir kaynağın **varsa** koşul kaynağı.
  - Varsayılan değer **varsa** kaynağın kaynak grubu koşul.
- **ExistenceScope** (isteğe bağlı)
  - İzin verilen değerler _abonelik_ ve _ResourceGroup_.
  - Gelen eşleştirmek için ilgili kaynak almak nereye kapsamını belirler.
  - Varsa geçerli değildir **türü** altında olan bir kaynağın **varsa** koşul kaynağı.
  - İçin _ResourceGroup_, için sınırlar **varsa** koşul kaynağın kaynak grubu veya belirtilen kaynak grubu **ResourceGroupName**.
  - İçin _abonelik_, ilgili kaynak için tüm abonelik sorgular.
  - Varsayılan değer _ResourceGroup_.
- **ExistenceCondition** (isteğe bağlı)
  - Belirtilmezse, kaynağın ilgili **türü** etkisi karşılar ve denetim tetiklemediğini.
  - Aynı dil için ilke kuralı olarak kullandığı **varsa** koşul, ancak her bir ilgili kaynak karşı ayrı ayrı değerlendirilir.
  - Eşleşen tüm ilgili kaynakları true olarak değerlendirilirse, efekt sağlandığında ve denetim tetiklemediğini.
  - [Field()] değerlerle denklik denetlenecek kullanabilirsiniz **varsa** koşul.
  - Örneğin, doğrulamak için kullanılabilir üst kaynak (içinde **varsa** koşul) eşleşen ilgili kaynak ile aynı kaynak konumda olduğundan.

### <a name="auditifnotexists-example"></a>AuditIfNotExists örneği

Örnek: kötü amaçlı yazılımdan koruma uzantısını var, ardından eksik olduğunda denetimleri belirlemek için sanal makineleri değerlendirir.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>Deployıfnotexists

Bir DeployIfNotExists öğesine benzer şekilde, bir DeployIfNotExists ilke tanımı, koşul karşılandığında bir şablon dağıtımı yürütür.

> [!NOTE]
> [İç içe şablonlar](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) ile desteklenen **Deployıfnotexists**, ancak [bağlı şablonları](../../../azure-resource-manager/resource-group-linked-templates.md) şu anda desteklenmiyor.

### <a name="deployifnotexists-evaluation"></a>Deployıfnotexists değerlendirme

Bir kaynak sağlayıcısı oluşturma veya güncelleştirme kaynak isteğiyle işlediği ve bir başarı durum kodu döndürdü sonra Deployıfnotexists çalıştırır. Hiçbir ilgili kaynaklar varsa veya kaynaklar tarafından tanımlanan bir şablon dağıtımı gerçekleşir **ExistenceCondition** doğru olarak değerlendirilebilmesi yok.

Bir değerlendirme döngüsü sırasında kaynaklarla eşleşen ilke tanımları Deployıfnotexists etkisi ile uyumlu değil olarak işaretlenir ancak bu kaynak üzerinde hiçbir işlem yapılmaz.

### <a name="deployifnotexists-properties"></a>Deployıfnotexists özellikleri

DeployIfNotExists efektinin **Details** özelliği, eşleştirilecek ilgili kaynakları ve yürütülecek şablon dağıtımını tanımlayan tüm alt özellikleri içerir.

- **Tür** [gerekli]
  - Eşleştirmek için ilgili kaynak türünü belirtir.
  - Başlar altında bir kaynak getirilmeye çalışılırken tarafından **varsa** koşul kaynağı, ardından aynı kaynak grubunda sorgulara **varsa** koşul kaynağı.
- **Ad** (isteğe bağlı)
  - Eşleştirilecek kaynak tam adını belirtir ve belirli bir kaynak belirtilen türdeki tüm kaynakları yerine getirmek ilke neden olur.
  - **IF. Field. Type** ve **then. details. Type** ile ilgili koşul değerleri ne zaman eşleşiyorsa, **ad** _gerekli_ olur ve `[field('name')]`olmalıdır.
- **ResourceGroupName** (isteğe bağlı)
  - İlişkili kaynağın farklı bir kaynak grubundan gelen eşleşen sağlar.
  - Varsa geçerli değildir **türü** altında olan bir kaynağın **varsa** koşul kaynağı.
  - Varsayılan değer **varsa** kaynağın kaynak grubu koşul.
  - Şablon dağıtımı yürütülürse, bu değer kaynak grubunda dağıtılır.
- **ExistenceScope** (isteğe bağlı)
  - İzin verilen değerler _abonelik_ ve _ResourceGroup_.
  - Gelen eşleştirmek için ilgili kaynak almak nereye kapsamını belirler.
  - Varsa geçerli değildir **türü** altında olan bir kaynağın **varsa** koşul kaynağı.
  - İçin _ResourceGroup_, için sınırlar **varsa** koşul kaynağın kaynak grubu veya belirtilen kaynak grubu **ResourceGroupName**.
  - İçin _abonelik_, ilgili kaynak için tüm abonelik sorgular.
  - Varsayılan değer _ResourceGroup_.
- **ExistenceCondition** (isteğe bağlı)
  - Belirtilmezse, kaynağın ilgili **türü** etkisi karşılar ve dağıtımı tetikleyecek değil.
  - Aynı dil için ilke kuralı olarak kullandığı **varsa** koşul, ancak her bir ilgili kaynak karşı ayrı ayrı değerlendirilir.
  - Eşleşen tüm ilgili kaynakları true olarak değerlendirilirse, efekt sağlandığında ve dağıtımı tetikleyecek değil.
  - [Field()] değerlerle denklik denetlenecek kullanabilirsiniz **varsa** koşul.
  - Örneğin, doğrulamak için kullanılabilir üst kaynak (içinde **varsa** koşul) eşleşen ilgili kaynak ile aynı kaynak konumda olduğundan.
- **roleDefinitionIds** [gerekli]
  - Bu özellik, rol tabanlı erişim denetimine rol kimliği erişilebilir tarafından eşleşen bir dize dizisi içermesi gerekir. Daha fazla bilgi için [düzeltme - ilke tanımı yapılandırma](../how-to/remediate-resources.md#configure-policy-definition).
- **Deploymentscope** (isteğe bağlı)
  - İzin verilen değerler _abonelik_ ve _ResourceGroup_.
  - Tetiklenecek dağıtımın türünü ayarlar. _Abonelik_ , [abonelik düzeyinde bir dağıtımı](../../../azure-resource-manager/deploy-to-subscription.md)gösterir, _ResourceGroup_ bir kaynak grubuna yönelik bir dağıtımı gösterir.
  - Abonelik düzeyinde dağıtımlar kullanılırken _dağıtımda_ bir _konum_ özelliği belirtilmelidir.
  - Varsayılan değer _ResourceGroup_.
- **Dağıtım** [gerekli]
  - Bu özellik için geçirilir gibi tam şablon dağıtımı içermelidir `Microsoft.Resources/deployments` API yerleştirin. Daha fazla bilgi için [dağıtımları REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Tüm işlevler içinde **dağıtım** özelliği, ilke şablonu bileşenleri olarak değerlendirilir. Özel durum **parametreleri** şablona ilkeden değerleri geçirir özelliği. **Değer** bu bölümünde bir şablon parametre adı geçirerek bu değer gerçekleştirmek için kullanılır (bkz _fullDbName_ Deployıfnotexists örnekte).

### <a name="deployifnotexists-example"></a>Deployıfnotexists örneği

Örnek: SQL Server veritabanlarını transparentDataEncryption etkin olup olmadığını belirlemek için değerlendirir. Aksi takdirde, etkinleştirilecek bir dağıtım yürütülür.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Bu efekt `Microsoft.Kubernetes.Data`ilke tanımı *moduyla* kullanılır. [Ilke aracısını](https://www.openpolicyagent.org/) (Opa) Azure 'da kendi kendine yönetilen Kubernetes kümelerine açmak Için [Opa kısıtlama çerçevesi](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) ile tanımlanan Gatekeeper v3 giriş denetimi kurallarını geçirmek için kullanılır.

> [!NOTE]
> [AKS altyapısı Için Azure Ilkesi](aks-engine.md) genel önizlemede ve yalnızca yerleşik ilke tanımlarını destekler.

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint değerlendirmesi

Açık Ilke Aracısı giriş denetleyicisi, kümede bulunan yeni istekleri gerçek zamanlı olarak değerlendirir.
Her 5 dakikada bir küme için tam tarama tamamlanır ve sonuçlar Azure Ilkesine bildirilir.

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint özellikleri

EnforceOPAConstraint kısıtlama efektinin **Details** özelliği, Gatekeeper v3 giriş denetimi kuralını tanımlayan alt özellikler içerir.

- **constraintTemplate** [gerekli]
  - Yeni kısıtlamaları tanımlayan CustomResourceDefinition (CRD) kısıtlama şablonu. Şablon rego mantığını, kısıtlama şemasını ve Azure Ilkesinden **değerler** aracılığıyla geçirilen kısıtlama parametrelerini tanımlar.
- **kısıtlama** [gerekli]
  - Kısıtlama şablonunun CRD uygulama. **Değerler** aracılığıyla geçirilen parametreleri `{{ .Values.<valuename> }}`olarak kullanır. Aşağıdaki örnekte bu `{{ .Values.cpuLimit }}` ve `{{ .Values.memoryLimit }}`.
- **değerler** [isteğe bağlı]
  - Kısıtlamaya geçirilecek parametreleri ve değerleri tanımlar. Her değer, CRD kısıtlama şablonunda bulunmalıdır.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy örneği

Örnek: AKS altyapısındaki kapsayıcı CPU ve bellek kaynak sınırlarını ayarlamak için ağ geçidi denetleyicisi v3 giriş denetim kuralı.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Bu efekt `Microsoft.ContainerService.Data`ilke tanımı *moduyla* kullanılır. [Azure Kubernetes hizmetinde](../../../aks/intro-kubernetes.md) [ilke aracısını](https://www.openpolicyagent.org/) (Opa) açmak için [rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) ile tanımlanan Gatekeeper v2 giriş denetimi kurallarını geçirmek için kullanılır.

> [!NOTE]
> [AKS Için Azure Ilkesi](rego-for-aks.md) sınırlı önizlemededir ve yalnızca yerleşik ilke tanımlarını destekler

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy değerlendirmesi

Açık Ilke Aracısı giriş denetleyicisi, kümede bulunan yeni istekleri gerçek zamanlı olarak değerlendirir.
Her 5 dakikada bir küme için tam tarama tamamlanır ve sonuçlar Azure Ilkesine bildirilir.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy özellikleri

EnforceRegoPolicy efektinin **Details** özelliği, Gatekeeper v2 giriş denetimi kuralını tanımlayan alt özellikler içerir.

- **PolicyId** [gerekli]
  - Rego giriş denetimi kuralına parametre olarak geçirilen benzersiz bir ad.
- **ilke** [gerekli]
  - Rego giriş denetimi kuralının URI 'sini belirtir.
- **policyParameters** [isteğe bağlı]
  - Rego ilkesine geçirilecek parametreleri ve değerleri tanımlar.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy örneği

Örnek: Gatekeeper v2 giriş denetimi kuralı, AKS içinde yalnızca belirtilen kapsayıcı görüntülerine izin verir.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="layering-policies"></a>Katmanlama ilkeleri

Bir kaynak tarafından birkaç atamaları etkilenebilir. Bu atamaları, aynı kapsamda veya farklı kapsamlardaki olabilir. Her biri bu atamaları da tanımlanan farklı bir etkiye sahip olma olasılığı yüksektir. Her ilke için geçerli ve koşul bağımsız olarak değerlendirilir. Örneğin:

- İlke 1
  - Kaynak konumu 'westus' için sınırlar
  - Bir abonelik için atanan
  - Reddetme etkisi
- İlke 2
  - 'Myresourcegroup' kaynak konumuna kısıtlar
  - Bir Abonelikteki kaynak grubu B atanan
  - Denetim etkisi
  
Bu kurulum, aşağıdaki sonucu neden olur:

- 'Myresourcegroup' kaynak grubunda B zaten bulunan herhangi bir kaynağa uyumlu İlkesi 2 ve 1 ilkeyle uyumlu olmayan
- 2 ilkeyle uyumlu olmayan ve 1 değilse 'westus' ilkeye uyumlu olmayan herhangi bir kaynak zaten kaynak grubundaki B 'eastus' içinde değil
- Yeni bir kaynak değil 'westus', abonelik a 1 ilke tarafından reddedildi
- Oluşturulan ve 2 ilkesindeki uyumlu olmayan yeni bir kaynak bir aboneliği ve 'westus' kaynak grubunda B

Hem ilke 1 ve 2 İlkesi vardı efekt, durum değişikliklerini reddet:

- Kaynak grubu B 'eastus' de bulunan herhangi bir kaynak İlkesi 2 uyumlu değil
- Kaynak grubu B 'westus' de bulunan herhangi bir kaynak İlkesi 1 uyumlu değil
- Yeni bir kaynak değil 'westus', abonelik a 1 ilke tarafından reddedildi
- Abonelik a B kaynak grubundaki yeni bir kaynak engellendi

Her atama ayrı ayrı değerlendirilir. Bu nedenle, hiç bir kaynak için bir fırsat boşluk aracılığıyla irsaliyesi için kapsam farklarını öğesinden. Sonucunda katmanlama ilkeleri veya ilke çakışma olarak kabul edilir **toplu en kısıtlayıcı**. Örnek olarak, her iki ilke 1 ve 2 reddetme etkisinin aksine olsaydı çakışan ve çakışan ilkeleri tarafından kaynak engellenebilir. Kaynağın yine de gerekliyse, gözden geçirme hedef kapsamda oluşturulan doğru kapsamlar doğru ilkelerini doğrulamak için her atamada Dışlamalar etkiliyor olabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [Azure İlkesi tanımı yapısını](definition-structure.md) gözden geçirin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](../how-to/programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](../how-to/getting-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.
