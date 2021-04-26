---
title: Efektlerin nasıl çalıştığını anlama
description: Azure Ilke tanımlarının uyumluluğun nasıl yönetildiğini ve raporlanmadığını belirten çeşitli etkileri vardır.
ms.date: 04/19/2021
ms.topic: conceptual
ms.openlocfilehash: e0d6eb5fb37ecf1b13edd945de52398b1e12f192
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861562"
---
# <a name="understand-azure-policy-effects"></a>Azure Ilke efektlerini anlama

Azure İlkesi'ndeki her ilke tanımı tek bir etkiye sahiptir. Bu efekt, ilke kuralı eşleşecek şekilde değerlendirildiğinde ne olacağını belirler. Yeni bir kaynak, güncelleştirilmiş bir kaynak veya mevcut bir kaynak için olmaları durumunda etkiler farklı davranır.

Bu efektler Şu anda bir ilke tanımında destekleniyor:

- [Ekle](#append)
- [Denetle](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Reddetme](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Devre dışı](#disabled)
- [Değiştir](#modify)

Aşağıdaki etkiler _kullanım dışıdır_:

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> **Enforceopaconstraint** veya **Enforceregopolicy** etkileri yerine, kaynak sağlayıcısı moduyla _Denetim_ ve _reddetme_ seçeneğini kullanın `Microsoft.Kubernetes.Data` . Yerleşik ilke tanımları güncelleştirildi. Bu yerleşik ilke tanımlarının mevcut ilke atamaları değiştirildiğinde, _efekt_ parametresi güncelleştirilmiş _allowedValues_ listesindeki bir değere değiştirilmelidir.

## <a name="order-of-evaluation"></a>Değerlendirme sırası

Kaynak oluşturma veya güncelleştirme istekleri önce Azure Ilkesi tarafından değerlendirilir. Azure Ilkesi, kaynak için uygulanan tüm atamaların bir listesini oluşturur ve ardından kaynağı her bir tanıma karşı değerlendirir. Azure Ilkesi, bir [Kaynak Yöneticisi modu](./definition-structure.md#resource-manager-modes)için, Isteği uygun kaynak sağlayıcısına teslim etmeden önce bazı etkileri işler. Bu sıra, bir kaynak Azure Ilkesinin tasarlanan idare denetimlerini karşılamıyorsa, kaynak sağlayıcısı tarafından gereksiz işlemeyi önler. Kaynak sağlayıcısı [moduyla](./definition-structure.md#resource-provider-modes), kaynak sağlayıcısı değerlendirmeyi ve sonucu yönetir ve sonuçları Azure ilkesine geri bildirir.

- İlke kuralının değerlendirilmemesi gerekip gerekmediğini belirlemekte önce **devre dışı bırakıldı** .
- **Ekleme** ve **değiştirme** daha sonra değerlendirilir. Bu, isteği değiştiremediği için bir değişiklik, bir denetim veya reddetme efektinin tetiklemesini engelleyebilir. Bu efektler yalnızca Kaynak Yöneticisi modunda kullanılabilir.
- **Reddet** daha sonra değerlendirilir. Denetimi yapılandırmadan önce değerlendirerek, istenmeyen bir kaynağın çift günlüğü engellenir.
- **Denetim** son olarak değerlendirilir.

Kaynak sağlayıcı Kaynak Yöneticisi modundaki bir istek için bir başarı kodu döndürdüğünde, **Auditınotexists** ve **deployifnotexists** , ek uyumluluk günlüğü veya eylemi gerekip gerekmediğini belirlemeyi değerlendirir.

Ayrıca, `PATCH` yalnızca ilgili alanları değiştiren istekler, `tags` ilke değerlendirmesini ilgili alanları denetleyen koşulları içeren ilkelerle kısıtlar `tags` .

## <a name="append"></a>Ekle

Ekleme veya güncelleştirme sırasında istenen kaynağa ek alanlar eklemek için ekleme kullanılır. Ortak bir örnek, bir depolama kaynağı için izin verilen IP 'Leri belirtmektir.

> [!IMPORTANT]
> Append etiketi olmayan özelliklerle kullanılmak üzere tasarlanmıştır. Append, oluşturma veya güncelleştirme isteği sırasında bir kaynağa etiket ekleyese de, bunun yerine Etiketler için [değiştirme](#modify) efektini kullanmanız önerilir.

### <a name="append-evaluation"></a>Değerlendirme Ekle

Append, bir kaynağın oluşturulması veya güncelleştirilmesi sırasında istek bir kaynak sağlayıcısı tarafından işlenmeden önce değerlendirilir. Append, ilke kuralının **IF** koşulu karşılandığında alanları kaynağa ekler. Ekleme efekti, özgün istekteki bir değeri farklı bir değerle geçersiz kılabilir, bu durumda bir reddetme efekti görevi görür ve isteği reddeder. Varolan bir diziye yeni bir değer eklemek için **\[\*\]** diğer adın sürümünü kullanın.

Ekleme efektini kullanan bir ilke tanımı, değerlendirme döngüsünün bir parçası olarak çalıştırıldığında, zaten mevcut olan kaynaklarda değişiklik yapmaz. Bunun yerine, **IF** koşulunu uyumlu değil olarak karşılayan herhangi bir kaynağı işaretler.

### <a name="append-properties"></a>Ekleme özellikleri

Append efektinin yalnızca bir **ayrıntı** dizisi vardır ve bu gereklidir. **Ayrıntılar** bir dizi olduğundan, tek bir **alan/değer** çifti ya da katları alabilir. Kabul edilebilir alanların listesi için [tanım yapısına](definition-structure.md#fields) bakın.

### <a name="append-examples"></a>Ekleme örnekleri

Örnek 1:  **\[\*\]** 
 bir depolama hesabında IP kuralları ayarlamak için bir dizi **değeri** olan [diğer ad](definition-structure.md#aliases) olmayan tek alan/değer çifti. **\[\*\]** Diğer ad olmayan bir dizi ise, efekt **değeri** tüm dizi olarak ekler. Dizi zaten varsa, çakışmadan bir reddetme olayı oluşur.

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

Örnek 2:  **\[\*\]** BIR depolama hesabında IP kuralları ayarlamak için bir Array **değeri** olan bir [diğer ad](definition-structure.md#aliases) kullanan tek alan/değer çifti. **\[\*\]** Diğer ad kullanıldığında, efekt **değeri** önceden var olabilecek bir diziye ekler. Dizi henüz yoksa, oluşturulur.

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

## <a name="audit"></a>Denetim

Denetim, uyumlu olmayan bir kaynağı değerlendirirken etkinlik günlüğünde bir uyarı olayı oluşturmak için kullanılır, ancak isteği durdurmaz.

### <a name="audit-evaluation"></a>Denetim değerlendirmesi

Denetim, bir kaynağın oluşturulması veya güncelleştirilmesi sırasında Azure Ilkesi tarafından denetlenen son etkiye sahiptir. Kaynak Yöneticisi modu için Azure Ilkesi, kaynağı kaynak sağlayıcısına gönderir. Denetim, bir kaynak isteği ve bir değerlendirme çevrimi için aynı şekilde çalışmaktadır. Yeni ve güncelleştirilmiş kaynaklar için Azure Ilkesi, `Microsoft.Authorization/policies/audit/action` etkinlik günlüğüne bir işlem ekler ve kaynağı uyumlu değil olarak işaretler.

### <a name="audit-properties"></a>Denetim Özellikleri

Kaynak Yöneticisi modu için, denetim efektinin, ilke tanımının **bundan sonra** koşulundaki ek özellikleri yoktur.

Kaynak sağlayıcısı modu için `Microsoft.Kubernetes.Data` , denetim efektinin aşağıdaki ek alt özellikleri **ayrıntılı** olarak vardır.

- **constraintTemplate** (gerekli)
  - Yeni kısıtlamaları tanımlayan CustomResourceDefinition (CRD) kısıtlama şablonu. Şablon rego mantığını, kısıtlama şemasını ve Azure Ilkesinden **değerler** aracılığıyla geçirilen kısıtlama parametrelerini tanımlar.
- **kısıtlama** (zorunlu)
  - Kısıtlama şablonunun CRD uygulama. **Değerler** aracılığıyla geçirilen parametreleri kullanır `{{ .Values.<valuename> }}` . Aşağıdaki örnek 2 ' de, bu değerler `{{ .Values.excludedNamespaces }}` ve ' dir `{{ .Values.allowedContainerImagesRegex }}` .
- **değerler** (isteğe bağlı)
  - Kısıtlamaya geçirilecek parametreleri ve değerleri tanımlar. Her değer, CRD kısıtlama şablonunda bulunmalıdır.

### <a name="audit-example"></a>Denetim örneği

Örnek 1: Kaynak Yöneticisi modları için denetim efektini kullanma.

```json
"then": {
    "effect": "audit"
}
```

Örnek 2: bir kaynak sağlayıcısı modu için denetim efektini kullanma `Microsoft.Kubernetes.Data` . **Ayrıntıların** içindeki ek bilgiler, izin verilen kapsayıcı görüntülerini sınırlamak Için Kubernetes 'Te kullanılacak kısıtlama ŞABLONUNU ve CRD 'yi tanımlar.

```json
"then": {
    "effect": "audit",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

Auditınotexists, **IF** koşuluyla eşleşen kaynakla _ilgili_ kaynakların denetlenmesini sağlar, **ancak koşulun** **ayrıntılarında** belirtilen özellikleri içermez.

### <a name="auditifnotexists-evaluation"></a>Auditınotexists değerlendirmesi

Bir kaynak sağlayıcısı bir oluşturma veya güncelleştirme kaynak isteği tamamladıktan ve başarı durum kodu döndürdüğünden Auditınotexists çalışır. İlgili kaynaklar yoksa veya **ExistenceCondition** tarafından tanımlanan kaynaklar true olarak değerlendirilmiyorsa denetim gerçekleşir. Yeni ve güncelleştirilmiş kaynaklar için Azure Ilkesi, `Microsoft.Authorization/policies/audit/action` etkinlik günlüğüne bir işlem ekler ve kaynağı uyumlu değil olarak işaretler. Tetiklendiğinde **, durum durumunu karşılayan** kaynak, uyumlu değil olarak işaretlenen kaynaktır.

### <a name="auditifnotexists-properties"></a>Auditınotexists özellikleri

Auditınotexists etkilerinin **Details** özelliği, eşleştirilecek ilgili kaynakları tanımlayan tüm alt özellikleri içerir.

- **Tür** (gerekli)
  - Eşleştirilecek ilgili kaynağın türünü belirtir.
  - **Ayrıntılar. Type** , **IF** koşulu kaynağı altında bir kaynak türü ise, ilke değerlendirilen kaynağın kapsamındaki bu **türden** kaynakları sorgular. Aksi takdirde, ilke, değerlendirilen kaynakla aynı kaynak grubu içinde sorgular.
- **Ad** (isteğe bağlı)
  - Eşleştirilecek kaynağın tam adını belirtir ve ilkenin belirtilen türdeki tüm kaynaklar yerine belirli bir kaynağı almasına neden olur.
  - **IF. Field. Type** ve **then. details. Type** ile ilgili koşul değerleri ne zaman eşleşiyorsa, **ad** _gerekli_ olur ve `[field('name')]` ya da `[field('fullName')]` bir alt kaynak için olmalıdır.
    Ancak, bunun yerine bir [Denetim](#audit) etkisi göz önünde bulundurulmalıdır.
- **Resourcegroupname** (isteğe bağlı)
  - İlgili kaynağın eşleştirmesinin farklı bir kaynak grubundan gelmesini sağlar.
  - **Tür** , **IF** koşulu kaynağı altında olacak bir kaynak ise uygulanmaz.
  - Varsayılan değer **IF** koşulu kaynağının kaynak grubudur.
- **Mevcut Tencescope** (isteğe bağlı)
  - İzin verilen değerler, _abonelik_ ve _ResourceGroup_.
  - İle eşleşecek ilgili kaynağı nereden getirecek yerin kapsamını ayarlar.
  - **Tür** , **IF** koşulu kaynağı altında olacak bir kaynak ise uygulanmaz.
  - _ResourceGroup_ Için, **resourcegroupname** içinde belirtilen **IF** koşulu kaynağının kaynak grubu veya kaynak grubu ile sınırlı olur.
  - _Abonelik_ için, ilgili kaynak için tüm aboneliği sorgular.
  - Varsayılan _kaynak grubu_.
- **ExistenceCondition** (isteğe bağlı)
  - Belirtilmemişse, **türdeki** ilgili kaynaklar etkiyi karşılar ve denetimi tetiklemez.
  - , **IF** koşulunun ilke kuralıyla aynı dili kullanır, ancak ilgili her kaynağa ayrı ayrı değerlendirilir.
  - Eşleşen ilgili herhangi bir kaynak true olarak değerlendirilirse, efekt karşılanır ve denetimi tetiklemez.
  - **IF** koşulunda değerlerle denklik denetlemek için [Field ()] kullanabilirsiniz.
  - Örneğin, üst kaynağın ( **IF** koşulunda) eşleşen ilgili kaynakla aynı kaynak konumunda olduğunu doğrulamak için kullanılabilir.

### <a name="auditifnotexists-example"></a>Auditınotexists örneği

Örnek: kötü amaçlı yazılımdan koruma uzantısının mevcut olup olmadığını belirlemesi için sanal makineleri değerlendirir ve ne zaman yoksa denetler.

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

## <a name="deny"></a>Reddet

Reddetme, bir ilke tanımı aracılığıyla tanımlı standartlarla eşleşmeyen bir kaynak isteğini engellemek için kullanılır ve istekte başarısız olur.

### <a name="deny-evaluation"></a>Değerlendirmeyi Reddet

Eşleşen bir kaynağı Kaynak Yöneticisi modunda oluştururken veya güncelleştirirken reddetme, isteği kaynak sağlayıcısına gönderilmeden önce engeller. İstek bir olarak döndürülür `403 (Forbidden)` . Portalda, ilke ataması tarafından engellenmiş olan dağıtımda durum olarak yasak görüntülenir. Kaynak sağlayıcısı, kaynak sağlayıcısı modunda kaynak değerlendirmesini yönetir.

Mevcut kaynakların değerlendirmesi sırasında, bir reddetme ilke tanımıyla eşleşen kaynaklar uyumlu değil olarak işaretlenir.

### <a name="deny-properties"></a>Reddetme özellikleri

Kaynak Yöneticisi modu için, reddetme efekti, ilke tanımının **daha sonra** koşulundaki ek özelliklere sahip olmaz.

Kaynak sağlayıcısı modu için `Microsoft.Kubernetes.Data` , reddetme efekti aşağıdaki ek özellikler alt özelliklerine sahiptir. 

- **constraintTemplate** (gerekli)
  - Yeni kısıtlamaları tanımlayan CustomResourceDefinition (CRD) kısıtlama şablonu. Şablon rego mantığını, kısıtlama şemasını ve Azure Ilkesinden **değerler** aracılığıyla geçirilen kısıtlama parametrelerini tanımlar.
- **kısıtlama** (zorunlu)
  - Kısıtlama şablonunun CRD uygulama. **Değerler** aracılığıyla geçirilen parametreleri kullanır `{{ .Values.<valuename> }}` . Aşağıdaki örnek 2 ' de, bu değerler `{{ .Values.excludedNamespaces }}` ve ' dir `{{ .Values.allowedContainerImagesRegex }}` .
- **değerler** (isteğe bağlı)
  - Kısıtlamaya geçirilecek parametreleri ve değerleri tanımlar. Her değer, CRD kısıtlama şablonunda bulunmalıdır.

### <a name="deny-example"></a>Reddetme örneği

Örnek 1: Kaynak Yöneticisi modları için reddetme efektini kullanma.

```json
"then": {
    "effect": "deny"
}
```

Örnek 2: bir kaynak sağlayıcısı modu için reddetme efektini kullanma `Microsoft.Kubernetes.Data` . **Ayrıntıların** içindeki ek bilgiler, izin verilen kapsayıcı görüntülerini sınırlamak Için Kubernetes 'Te kullanılacak kısıtlama ŞABLONUNU ve CRD 'yi tanımlar.

```json
"then": {
    "effect": "deny",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Bir DeployIfNotExists öğesine benzer şekilde, bir DeployIfNotExists ilke tanımı, koşul karşılandığında bir şablon dağıtımı yürütür.

> [!NOTE]
> [Iç içe şablonlar](../../../azure-resource-manager/templates/linked-templates.md#nested-template) **Deployifnotexists** ile desteklenir, ancak [bağlantılı şablonlar](../../../azure-resource-manager/templates/linked-templates.md#linked-template) Şu anda desteklenmiyor.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists değerlendirmesi

DeployIfNotExists, bir kaynak sağlayıcısı bir oluşturma veya güncelleştirme aboneliği ya da kaynak isteği tamamladıktan sonra bir başarı durum kodu döndürdüğünden 15 dakika sonra çalışır. İlişkili kaynaklar yoksa veya **ExistenceCondition** tarafından tanımlanan kaynaklar true olarak değerlendirilmiyorsa, şablon dağıtımı oluşur. Dağıtımın süresi, şablona dahil edilen kaynakların karmaşıklığına bağlıdır.

Bir değerlendirme çevrimi sırasında, kaynaklarla eşleşen bir DeployIfNotExists etkisi olan ilke tanımları uyumlu değil olarak işaretlenir, ancak bu kaynakta hiçbir işlem yapılmaz. Mevcut uyumlu olmayan kaynaklar bir [Düzeltme göreviyle](../how-to/remediate-resources.md)düzeltilebilir.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists özellikleri

DeployIfNotExists efektinin **Details** özelliği, eşleştirilecek ilgili kaynakları ve yürütülecek şablon dağıtımını tanımlayan tüm alt özellikleri içerir.

- **Tür** (gerekli)
  - Eşleştirilecek ilgili kaynağın türünü belirtir.
  - , **IF** koşulu kaynağı altında bir kaynağı getirmeye çalışırken başlar ve sonra, **IF** koşulu kaynağıyla aynı kaynak grubu içinde sorgular.
- **Ad** (isteğe bağlı)
  - Eşleştirilecek kaynağın tam adını belirtir ve ilkenin belirtilen türdeki tüm kaynaklar yerine belirli bir kaynağı almasına neden olur.
  - **IF. Field. Type** ve **then. details. Type** ile ilgili koşul değerleri ne zaman eşleşiyorsa, **ad** _gerekli_ olur ve `[field('name')]` ya da `[field('fullName')]` bir alt kaynak için olmalıdır.
- **Resourcegroupname** (isteğe bağlı)
  - İlgili kaynağın eşleştirmesinin farklı bir kaynak grubundan gelmesini sağlar.
  - **Tür** , **IF** koşulu kaynağı altında olacak bir kaynak ise uygulanmaz.
  - Varsayılan değer **IF** koşulu kaynağının kaynak grubudur.
  - Bir şablon dağıtımı yürütülürse, bu değerin kaynak grubunda dağıtılır.
- **Mevcut Tencescope** (isteğe bağlı)
  - İzin verilen değerler, _abonelik_ ve _ResourceGroup_.
  - İle eşleşecek ilgili kaynağı nereden getirecek yerin kapsamını ayarlar.
  - **Tür** , **IF** koşulu kaynağı altında olacak bir kaynak ise uygulanmaz.
  - _ResourceGroup_ Için, **resourcegroupname** içinde belirtilen **IF** koşulu kaynağının kaynak grubu veya kaynak grubu ile sınırlı olur.
  - _Abonelik_ için, ilgili kaynak için tüm aboneliği sorgular.
  - Varsayılan _kaynak grubu_.
- **ExistenceCondition** (isteğe bağlı)
  - Belirtilmemişse, **türü** ilgili herhangi bir kaynak etkiyi karşılar ve dağıtımı tetiklemez.
  - , **IF** koşulunun ilke kuralıyla aynı dili kullanır, ancak ilgili her kaynağa ayrı ayrı değerlendirilir.
  - Eşleşen ilgili herhangi bir kaynak true olarak değerlendirilirse, efekt karşılanır ve dağıtımı tetiklemez.
  - **IF** koşulunda değerlerle denklik denetlemek için [Field ()] kullanabilirsiniz.
  - Örneğin, üst kaynağın ( **IF** koşulunda) eşleşen ilgili kaynakla aynı kaynak konumunda olduğunu doğrulamak için kullanılabilir.
- **Roledefinitionıds** (gerekli)
  - Bu özellik, abonelik tarafından erişilebilen rol tabanlı erişim denetimi rol KIMLIĞIYLE eşleşen bir dize dizisi içermelidir. Daha fazla bilgi için bkz. [Düzeltme-ilke tanımını yapılandırma](../how-to/remediate-resources.md#configure-policy-definition).
- **Deploymentscope** (isteğe bağlı)
  - İzin verilen değerler, _abonelik_ ve _ResourceGroup_.
  - Tetiklenecek dağıtımın türünü ayarlar. _Abonelik_ , [abonelik düzeyinde bir dağıtımı](../../../azure-resource-manager/templates/deploy-to-subscription.md)gösterir, _ResourceGroup_ bir kaynak grubuna yönelik bir dağıtımı gösterir.
  - Abonelik düzeyinde dağıtımlar kullanılırken _dağıtımda_ bir _konum_ özelliği belirtilmelidir.
  - Varsayılan _kaynak grubu_.
- **Dağıtım** (gerekli)
  - Bu özellik, put API 'sine geçirilecek olan tam şablon dağıtımını içermelidir `Microsoft.Resources/deployments` . Daha fazla bilgi için bkz. [dağıtımlar REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > **Dağıtım** özelliğinin içindeki tüm işlevler, ilke değil, şablonun bileşenleri olarak değerlendirilir. Özel durum, ilkeden şablona değer geçiren **Parameters** özelliğidir. Bu bölümdeki **değer** bir şablon parametresi adı altında bu değer geçişini gerçekleştirmek için kullanılır (Deployıfnotexists örneğinde bkz. _fulldbname_ ).

### <a name="deployifnotexists-example"></a>DeployIfNotExists örneği

Örnek: transparentDataEncryption etkinleştirilip etkinleştirilmediğini belirlemede SQL Server veritabanlarını değerlendirir. Aksi takdirde, etkinleştirilecek bir dağıtım yürütülür.

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

## <a name="disabled"></a>Devre dışı

Bu efekt, durumları test etmek veya ilke tanımının etkiyi ne zaman parametreleştirildiği için faydalıdır. Bu esneklik, bu ilkenin atamalarının tümünü devre dışı bırakmak yerine tek bir atamayı devre dışı bırakmayı mümkün kılar.

Devre dışı bırakılmış etkine bir alternatif, ilke atamasında ayarlanan **Enforcementmode**' dır.
**Enforcementmode** _devre dışı bırakıldığında_, kaynaklar yine de değerlendirilir. Etkinlik günlükleri ve ilke efekti gibi günlüğe kaydetme gerçekleşmez. Daha fazla bilgi için bkz. [ilke atama-zorlama modu](./assignment-structure.md#enforcement-mode).

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Bu efekt, ilke tanımı _moduyla_ birlikte kullanılır `Microsoft.Kubernetes.Data` . Azure 'da Kubernetes kümelerine [Ilke aracısını](https://www.openpolicyagent.org/) (Opa) açmak Için [Opa kısıtlama çerçevesi](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) ile tanımlanan Gatekeeper v3 giriş denetimi kurallarını geçirmek için kullanılır.

> [!IMPORTANT]
> **Enforceopaconstraint** etkin ve Ilgili **Kubernetes hizmet** kategorisi ile sınırlı önizleme ilkesi tanımları _kullanım dışıdır_. Bunun yerine, efekt _denetimi_ ve kaynak sağlayıcısı ile _reddetme_ modunu kullanın `Microsoft.Kubernetes.Data` .

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint değerlendirmesi

Açık Ilke Aracısı giriş denetleyicisi, kümede bulunan yeni istekleri gerçek zamanlı olarak değerlendirir.
15 dakikada bir, küme için tam tarama tamamlanır ve sonuçlar Azure Ilkesine bildirilir.

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint özellikleri

EnforceOPAConstraint kısıtlama efektinin **Details** özelliği, Gatekeeper v3 giriş denetimi kuralını tanımlayan alt özellikler içerir.

- **constraintTemplate** (gerekli)
  - Yeni kısıtlamaları tanımlayan CustomResourceDefinition (CRD) kısıtlama şablonu. Şablon rego mantığını, kısıtlama şemasını ve Azure Ilkesinden **değerler** aracılığıyla geçirilen kısıtlama parametrelerini tanımlar.
- **kısıtlama** (zorunlu)
  - Kısıtlama şablonunun CRD uygulama. **Değerler** aracılığıyla geçirilen parametreleri kullanır `{{ .Values.<valuename> }}` . Aşağıdaki örnekte, bu değerler ve ' dir `{{ .Values.cpuLimit }}` `{{ .Values.memoryLimit }}` .
- **değerler** (isteğe bağlı)
  - Kısıtlamaya geçirilecek parametreleri ve değerleri tanımlar. Her değer, CRD kısıtlama şablonunda bulunmalıdır.

### <a name="enforceopaconstraint-example"></a>EnforceOPAConstraint örneği

Örnek: Kubernetes içindeki kapsayıcı CPU ve bellek kaynak sınırlarını ayarlamak için ağ geçidi denetleyicisi v3 giriş denetim kuralı.

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

Bu efekt, ilke tanımı _moduyla_ birlikte kullanılır `Microsoft.ContainerService.Data` . [Azure Kubernetes hizmetinde](../../../aks/intro-kubernetes.md) [ilke aracısını](https://www.openpolicyagent.org/) (Opa) açmak için [rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) ile tanımlanan Gatekeeper v2 giriş denetimi kurallarını geçirmek için kullanılır.

> [!IMPORTANT]
> **Enforceregopolicy** efektli sınırlı önizleme ilkesi tanımları ve Ilgili **Kubernetes hizmet** kategorisi _kullanım dışıdır_. Bunun yerine, efekt _denetimi_ ve kaynak sağlayıcısı ile _reddetme_ modunu kullanın `Microsoft.Kubernetes.Data` .

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy değerlendirmesi

Açık Ilke Aracısı giriş denetleyicisi, kümede bulunan yeni istekleri gerçek zamanlı olarak değerlendirir.
15 dakikada bir, küme için tam tarama tamamlanır ve sonuçlar Azure Ilkesine bildirilir.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy özellikleri

EnforceRegoPolicy efektinin **Details** özelliği, Gatekeeper v2 giriş denetimi kuralını tanımlayan alt özellikler içerir.

- **PolicyId** (gerekli)
  - Rego giriş denetimi kuralına parametre olarak geçirilen benzersiz bir ad.
- **ilke** (gerekli)
  - Rego giriş denetimi kuralının URI 'sini belirtir.
- **policyParameters** (isteğe bağlı)
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

## <a name="modify"></a>Değiştir

Değişiklik, oluşturma veya güncelleştirme sırasında bir abonelik veya kaynağa özellikler veya Etiketler eklemek, güncelleştirmek veya kaldırmak için kullanılır. Ortak bir örnek, costCenter gibi kaynaklardaki etiketleri güncelleştirmedir. Mevcut uyumlu olmayan kaynaklar bir [Düzeltme göreviyle](../how-to/remediate-resources.md)düzeltilebilir. Tek bir değiştirme kuralında herhangi bir sayıda işlem olabilir.

Aşağıdaki işlemler değiştirme tarafından desteklenir:

- Kaynak etiketlerini ekleyin, değiştirin veya kaldırın. Etiketler için, `mode` hedef kaynak bir kaynak grubu değilse, bir değiştirme Ilkesi _dizinli_ olarak ayarlanmalıdır.
- `identity.type`Sanal makineler ve sanal makine ölçek kümelerinin yönetilen kimlik türü () değerini ekleyin veya değiştirin.
- Belirli diğer adların (Önizleme) değerlerini ekleyin veya değiştirin.
  - `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }` komutunu kullanma
    Azure PowerShell **4.6.0** veya üzeri sürümlerde, değiştirme ile kullanılabilecek diğer adların bir listesini alın.

> [!IMPORTANT]
> Etiketleri yönetiyorsanız, değişiklik olarak ekle yerine Değiştir kullanılması önerilir, ek işlem türleri ve mevcut kaynakları düzeltme olanağı sağlar. Ancak, yönetilen bir kimlik oluşturmadıysanız veya değiştirme henüz kaynak özelliği için diğer adı desteklemiyorsa, ekleme önerilir.

### <a name="modify-evaluation"></a>Değerlendirmeyi Değiştir

Değişiklik, bir kaynağın oluşturulması veya güncelleştirilmesi sırasında istek bir kaynak sağlayıcısı tarafından işlenmeden önce değerlendirilir. İlke kuralının **IF** koşulu karşılandığında değiştirme işlemleri, istek içeriğine uygulanır. Her değiştirme işlemi, ne zaman uygulanacağını belirleyen bir koşul belirtebilir. _False_ olarak değerlendirilen koşullara sahip işlemler atlanır.

Bir diğer ad belirtildiğinde, değiştirme işleminin istek içeriğini kaynak sağlayıcının reddetmesine neden olacak şekilde değiştirmemesini sağlamak için aşağıdaki ek denetimler gerçekleştirilir:

- Diğer adın eşlendiği özelliği isteğin API sürümünde ' değiştirilebilir ' olarak işaretlenir.
- Değiştirme işlemindeki belirteç türü, isteğin API sürümündeki özelliğin beklenen belirteç türüyle eşleşiyor.

Bu denetimlerden biri başarısız olursa, ilke değerlendirmesi belirtilen **conflictEffect** geri döner.

> [!IMPORTANT]
> Diğer adları içeren tanımların değiştirilmesi, eşlenen özelliğin ' değiştirilebilir ' olmadığı API sürümlerini kullanarak başarısız istekleri önlemek için _Denetim_ **Çakışma efektini** kullanır. Aynı diğer ad API sürümleri arasında farklı davrandığı takdirde, koşullu değiştirme işlemleri her bir API sürümü için kullanılan değiştirme işlemini tespit etmek için kullanılabilir.

Değişiklik efektini kullanan bir ilke tanımı, değerlendirme döngüsünün bir parçası olarak çalıştırıldığında, zaten mevcut olan kaynaklarda değişiklik yapmaz. Bunun yerine, **IF** koşulunu uyumlu değil olarak karşılayan herhangi bir kaynağı işaretler.

### <a name="modify-properties"></a>Özellikleri Değiştir

Değişiklik efektinin **Ayrıntılar** özelliği, düzeltme için gereken izinleri ve etiket değerlerini eklemek, güncelleştirmek veya kaldırmak için kullanılan **işlemleri** tanımlayan tüm alt özellikleri içerir.

- **Roledefinitionıds** (gerekli)
  - Bu özellik, abonelik tarafından erişilebilen rol tabanlı erişim denetimi rol KIMLIĞIYLE eşleşen bir dize dizisi içermelidir. Daha fazla bilgi için bkz. [Düzeltme-ilke tanımını yapılandırma](../how-to/remediate-resources.md#configure-policy-definition).
  - Tanımlanan rol, [katkıda bulunan](../../../role-based-access-control/built-in-roles.md#contributor) rolüne verilen tüm işlemleri içermelidir.
- **conflictEffect** (isteğe bağlı)
  - Birden fazla ilke tanımı aynı özelliği değiştirirse veya değiştirme işlemi belirtilen diğer ad üzerinde çalışmazsa, "wins" ilke tanımını belirler.
    - Yeni veya güncelleştirilmiş kaynaklar için, _reddetme_ ile ilke tanımı öncelik kazanır. _Denetim_ tüm **işlemleri** atlayarak ilke tanımları. Birden fazla ilke tanımı _reddederse_, istek çakışma olarak reddedilir. Tüm ilke tanımlarında _Denetim_ varsa, çakışan ilke tanımlarının **işlemlerinden** hiçbiri işlenir.
    - Mevcut kaynaklar için, birden fazla ilke tanımı _reddederse_, uyumluluk durumu _Çakışma_ olur. Bir veya daha az ilke tanımı _reddederse_, her atama _uyumlu olmayan_ bir uyumluluk durumu döndürür.
  - Kullanılabilir değerler: _Denetim_, _reddetme_, _devre dışı_.
  - Varsayılan değer _reddedildi_.
- **işlemler** (gerekli)
  - Eşleşen kaynaklarda tamamlanacak tüm etiket işlemlerinin dizisi.
  - Özellikler:
    - **işlem** (gerekli)
      - Eşleşen bir kaynakta hangi eylemin yapılacağını tanımlar. Seçenekler şunlardır: _Addorreplace_, _Add_, _Remove_. _Ekleme_ [efektine](#append) benzer şekilde davranır.
    - **alan** (gerekli)
      - Eklenecek, değiştirilecek veya kaldırılacak etiket. Etiket adları diğer [alanlar](./definition-structure.md#fields)için aynı adlandırma kuralına uymalıdır.
    - **değer** (isteğe bağlı)
      - Etiketi ayarlanacak değer.
      - **Işlem** _addorreplace_ veya _Add_ ise bu özellik gereklidir.
    - **koşul** (isteğe bağlı)
      - _Doğru_ veya _yanlış_ olarak değerlendirilen [ilke işlevleriyle](./definition-structure.md#policy-functions) bir Azure ilke dili ifadesi içeren bir dize.
      - Aşağıdaki Ilke işlevlerini desteklemez: `field()` , `resourceGroup()` , `subscription()` .

### <a name="modify-operations"></a>İşlemleri değiştirme

**Operations** özelliği dizisi, birkaç etiketi tek bir ilke tanımından farklı şekillerde değiştirmeyi mümkün kılar. Her işlem, **işlem**, **alan** ve **değer** özelliklerinden oluşur. İşlem düzeltme görevinin etiketlere ne yaptığını belirler, alan hangi etiketin değiştirilmekte olduğunu belirler ve değer bu etiketin yeni ayarını tanımlar. Aşağıdaki örnekte aşağıdaki etiket değişiklikleri yapılır:

- `environment`Etiketi, farklı bir değerle zaten mevcut olsa bile "test" olarak ayarlar.
- Etiketi kaldırır `TempResource` .
- Etiketi, `Dept` ilke atamasında yapılandırılan _deptname_ ilke parametresi olarak ayarlar.

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

|İşlem |Description |
|-|-|
|addOrReplace |Özellik veya etiket farklı bir değerle zaten var olsa bile, tanımlı özelliği veya etiketi ve değeri kaynağa ekler. |
|Ekle |Kaynağa tanımlı özelliği veya etiketi ve değeri ekler. |
|Kaldır |Kaynaktaki tanımlı özelliği veya etiketi kaldırır. |

### <a name="modify-examples"></a>Örnekleri Değiştir

Örnek 1: etiketi ekleyin `environment` ve var olan `environment` etiketleri "test" ile değiştirin:

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

Örnek 2: etiketi kaldırın `env` ve `environment` etiketi ekleyin ya da varolan `environment` etiketleri parametreli bir değerle değiştirin:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "conflictEffect": "deny",
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

Örnek 3: bir depolama hesabının blob ortak erişimine izin vermediğinden, değiştirme işleminin yalnızca API sürümü daha büyük veya ' 2019-04-01 ' değerine eşit olan istekler değerlendirilirken uygulanmasını sağlayın:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/microsoft.authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab"
        ],
        "conflictEffect": "audit",
        "operations": [
            {
                "condition": "[greaterOrEquals(requestContext().apiVersion, '2019-04-01')]",
                "operation": "addOrReplace",
                "field": "Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
                "value": false
            }
        ]
    }
}
```

## <a name="layering-policy-definitions"></a>İlke tanımlarını katmanlama

Bir kaynak, birkaç atamadan etkilenebilir. Bu atamalar aynı kapsamda veya farklı kapsamlardadır olabilir. Bu atamaların her biri de tanımlı farklı bir etkiye sahip olabilir. Her ilke için koşul ve efekt bağımsız olarak değerlendirilir. Örnek:

- İlke 1
  - Kaynak konumunu ' westus ' olarak kısıtlar
  - A aboneliğine atandı
  - Reddetme etkisi
- İlke 2
  - Kaynak konumunu ' eastus ' olarak kısıtlar
  - A aboneliği içindeki kaynak grubu B 'ye atandı
  - Denetim etkisi
  
Bu kurulum aşağıdaki sonuca neden olur:

- ' Eastus ' içindeki kaynak grubu B 'de bulunan herhangi bir kaynak, ilke 2 ile uyumlu ve ilke 1 ile uyumlu değil
- Kaynak grubu B 'de ' eastus ' içinde olmayan herhangi bir kaynak, ilke 2 ile uyumlu değil ve ' westus ' içinde değilse ilke 1 ile uyumlu değil
- ' Westus ' içinde olmayan abonelikte bulunan yeni kaynaklar ilke 1 tarafından reddedildi
- A aboneliği ve ' westus ' içindeki kaynak grubu B 'deki tüm yeni kaynaklar, ilke 2 ' de oluşturulur ve uyumlu değildir

İlke 1 ve ilke 2 ' nin reddetme etkisi varsa, durum şu şekilde değişir:

- Kaynak grubu B 'de ' eastus ' içinde olmayan herhangi bir kaynak, ilke 2 ile uyumlu değil
- Kaynak grubu B 'de ' westus ' içinde olmayan herhangi bir kaynak, ilke 1 ile uyumlu değil
- ' Westus ' içinde olmayan abonelikte bulunan yeni kaynaklar ilke 1 tarafından reddedildi
- A aboneliğinin B kaynak grubundaki tüm yeni kaynaklar reddedildi

Her atama tek tek değerlendirilir. Bu nedenle, bir kaynağın kapsamdaki farklılıklar arasından bir boşluk ile bir kaynak için bir fırsat yoktur. Katman ilkesi tanımlarının net sonucu, **en çok kısıtlayıcı** olarak kabul edilir. Örnek olarak, hem ilke 1 hem de 2 bir reddetme efektine sahipse, çakışan ve çakışan ilke tanımları tarafından bir kaynak engellenir. Hala hedef kapsamda oluşturulacak kaynak gerekiyorsa, doğru ilke atamalarının doğru kapsamları etkilediğini doğrulamak için her atamadaki dışlamaları gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [Azure İlkesi tanımı yapısını](definition-structure.md) gözden geçirin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](../how-to/programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](../how-to/get-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.
