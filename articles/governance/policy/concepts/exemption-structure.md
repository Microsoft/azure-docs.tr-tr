---
title: İlke muafiyet yapısının ayrıntıları
description: Kaynakları veya tanımları değerlendirmesinden muaf tutmak için Azure Ilkesi tarafından kullanılan ilke muafiyet tanımını açıklar.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: ecf956d7507dfa2168e4f2591e4b661423801365
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091744"
---
# <a name="azure-policy-exemption-structure"></a>Azure Ilke muafiyet yapısı

Azure Ilke muafiyetleri (Önizleme) özelliği, bir kaynak hiyerarşisini veya tek bir kaynağı, girişim veya tanım değerlendirmesinden _muaf tutmak_ için kullanılır. _Muaf tutulan_ kaynaklar genel uyumluluğa doğru sayılır, ancak değerlendirilemiyor veya geçici bir feragat aldığınız sahip olamaz. Daha fazla bilgi için bkz. [Azure ilkesinde kapsamı anlama](./scope.md). Azure Ilkesi muafiyetleri yalnızca [Kaynak Yöneticisi modlarıyla](./definition-structure.md#resource-manager-modes) çalışır ve [kaynak sağlayıcısı modlarıyla](./definition-structure.md#resource-provider-modes)çalışmaz.

> [!IMPORTANT]
> Bu özellik **Önizleme** süresince ücretsizdir. Fiyatlandırma ayrıntıları için bkz. [Azure ilkesi fiyatlandırması](https://azure.microsoft.com/pricing/details/azure-policy/). Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bir ilke muafiyeti oluşturmak için JSON kullanırsınız. İlke muafiyeti için öğeleri içerir:

- görünen ad
- açıklama
- meta veriler
- ilke ataması
- bir girişim içindeki ilke tanımları
- muafiyet kategorisi
- dolmadan

> [!NOTE]
> Bir ilke muafiyeti, kaynak hiyerarşisinde bir alt nesne veya muafiyeti verilen tek kaynak olarak oluşturulur, bu nedenle hedef muafiyet tanımına dahil değildir.

Örneğin, aşağıdaki JSON bir kaynağın **feragat aldığınız** kategorisinde adlı bir girişim atamasında bir ilke muafiyeti gösterir `resourceShouldBeCompliantInit` . Kaynak, girişim  içindeki ilke tanımlarının yalnızca iki birinden, `customOrgPolicy` özel ilke tanımına (başvuruya `requiredTags` ) ve ' izin verilen konumların ' YERLEŞIK ilke tanımına (kimlik: `e56962a6-4747-49cd-b67b-bf8b01975c4c` , başvuru) muaf tutulur `allowedLocations` :

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

İlke muafiyeti tarafından kullanılan eşleştirmeden ilgili girişimin parçacığı `policyDefinitionReferenceIds` :

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>Görünen ad ve açıklama

İlke muafiyetini tanımlamak ve belirli kaynakla kullanımı için bağlam sağlamak üzere **DisplayName** ve **Description** kullanın. **DisplayName** , en fazla _128_ karakter uzunluğunda ve en fazla _512_ karakter uzunluğunda bir **Açıklama** içeriyor.

## <a name="metadata"></a>Meta veri

**Metadata** özelliği, ilgili bilgileri depolamak için gereken herhangi bir alt özelliği oluşturmaya izin verir. Yukarıdaki örnekte, **RequestedBy**, **approma by**, **approvedOn** ve **bilet başvurusu** özellikleri, istisnayı kimin ne zaman ve ne zaman onayladığı ve istek için bir iç izleme bileti sağlamak için müşteri değerleri içerir. Bu **meta veri** özellikleri örneklerdir, ancak gerekli değildir ve **meta veriler** bu alt özelliklerle sınırlı değildir.

## <a name="policy-assignment-id"></a>İlke atama KIMLIĞI

Bu alan, bir ilke atamasının ya da bir girişim atamasının tam yol adı olmalıdır.
`policyAssignmentId` dizi değil bir dizedir. Bu özellik, üst kaynak hiyerarşisinin veya ayrı bir kaynağın hangi atamayı _dışarıda_ olduğunu tanımlar.

## <a name="policy-definition-ids"></a>İlke tanımı kimlikleri

, `policyAssignmentId` Bir girişim ataması için ise, özelliği, `policyDefinitionReferenceIds` Konu kaynağında hangi ilke tanımının bir istisna olduğunu belirtmek için kullanılabilir. Kaynak bir veya daha fazla dahil edilen ilke tanımlarından muaf tutulmayabilir, bu özellik bir _dizidir_. Değerlerin, alanlardaki girişim tanımındaki değerlerle eşleşmesi gerekir `policyDefinitions.policyDefinitionReferenceId` .

## <a name="exemption-category"></a>Muafiyet kategorisi

İki muafiyet kategorisi vardır ve muafiyetleri gruplandırmak için kullanılır:

- **Azaltıldı**: ilke amacı başka bir yöntem aracılığıyla karşılandığından muafiyet verilir.
- **Feragat aldığınız**: muafiyet, kaynağın uyumsuzluk durumu geçici olarak kabul edildiği için verilir. Bu kategoriyi kullanmanın başka bir nedeni, bir girişim içindeki bir veya daha fazla tanımından dışlanması gereken bir kaynak veya kaynak hiyerarşisine yöneliktir, ancak tüm girişimden dışlamamalıdır.

## <a name="expiration"></a>Bitiş tarihi

Bir kaynak hiyerarşisinin veya tek bir kaynağın bir atamaya artık _muaf_ olmaması durumunda `expiresOn` özelliği ayarlayın. Bu isteğe bağlı özellik Universal ISO 8601 DateTime biçiminde olmalıdır `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

> [!NOTE]
> Tarihe ulaşıldığında ilke muafiyetleri silinmez `expiresOn` . Nesne kayıt tutulması için korunur, ancak muafiyet artık onaylanmaz.

## <a name="required-permissions"></a>Gerekli izinler

Ilke muafiyeti nesnelerini yönetmek için gereken Azure RBAC izinleri, `Microsoft.Authorization/policyExemptions` işlem grubunda. Yerleşik roller [kaynak Ilkesi katılımcısı](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) ve [Güvenlik Yöneticisi](../../../role-based-access-control/built-in-roles.md#security-admin) , hem Izinleri hem de `read` `write` [ilke öngörüleri veri yazıcısı (Önizleme)](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) `read` iznine sahiptir.

Muafiyetler, bir istisna verme etkisi nedeniyle ek güvenlik önlemleri vardır. `Microsoft.Authorization/policyExemptions/write`Kaynak hiyerarşisinde veya tek bir kaynakta işlemin gerektirdiğinin ötesinde, bir istisna oluşturucusunun `exempt/Action` hedef atamada fiil olması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [İlke tanımı yapısı](./definition-structure.md)hakkında bilgi edinin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](../how-to/programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](../how-to/get-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.