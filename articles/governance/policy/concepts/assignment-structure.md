---
title: İlke atama yapısının ayrıntıları
description: Değerlendirme için kaynaklarla ilke tanımlarını ve parametreleri ilişkilendirmek üzere Azure Ilkesi tarafından kullanılan ilke atama tanımını açıklar.
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 9de210b17264330e79ab5978a449e7a494054be2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535874"
---
# <a name="azure-policy-assignment-structure"></a>Azure İlkesi atama yapısı

İlke atamaları, hangi ilkelerin veya girişimlerin hangi kaynaklara atandığını tanımlamak için Azure Ilkesi tarafından kullanılır. İlke ataması, atama zamanında bu kaynak grubu için parametrelerin değerlerini belirleyebilir ve aynı kaynak özelliklerini, uyumluluk için farklı ihtiyaçlarla ele alan ilke tanımlarının yeniden kullanılmasını mümkün kılar.

Bir ilke ataması oluşturmak için JSON kullanırsınız. İlke ataması için öğeleri içerir:

- görünen ad
- açıklama
- meta veriler
- zorlama modu
- Dışlanan kapsamlar
- ilke tanımı
- uyumsuzluk iletileri
- parameters

Örneğin, aşağıdaki JSON, _Donotenzorlama_ modunda dinamik parametrelerle bir ilke atamasını göstermektedir:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Tüm Azure Ilke örnekleri [Azure ilke örneklerimizle](../samples/index.md).

## <a name="display-name-and-description"></a>Görünen ad ve açıklama

**DisplayName** ve **Description** kullanarak ilke atamasını tanımlayabilir ve belirli bir kaynak kümesiyle kullanımı için bağlam sağlayabilirsiniz. **DisplayName** , en fazla _128_ karakter uzunluğunda ve en fazla _512_ karakter uzunluğunda bir **Açıklama** içeriyor.

## <a name="metadata"></a>Meta veri

İsteğe bağlı `metadata` özelliği, ilke atamasıyla ilgili bilgileri depolar. Müşteriler, ' de kuruluşları için yararlı olan özellikleri ve değerleri tanımlayabilir `metadata` . Ancak, Azure Ilkesi tarafından kullanılan bazı _ortak_ özellikler vardır. Her `metadata` özelliğin 1024 karakterlik bir sınırı vardır.

### <a name="common-metadata-properties"></a>Ortak meta veri özellikleri

- `assignedBy` (dize): atamayı oluşturan güvenlik sorumlusunun kolay adı.
- `createdBy` (dize): atamayı oluşturan güvenlik sorumlusunun GUID 'SI.
- `createdOn` (dize): atama oluşturma saatinin Universal ISO 8601 tarih saat biçimi.
- `parameterScopes` (nesne): anahtarın [strongtype](./definition-structure.md#strongtype) yapılandırılmış bir parametre adıyla eşleştiği anahtar-değer çiftleri koleksiyonu ve değer, _strongtype_ ile eşleşen kullanılabilir kaynakların listesini sağlamak için portalda kullanılan kaynak kapsamını tanımlar. Kapsam atama kapsamından farklıysa, Portal bu değeri ayarlar. Ayarlanırsa, portalda ilke atamasının bir düzenlemesi, parametrenin kapsamını bu değere otomatik olarak ayarlar. Ancak, kapsam değere kilitli değildir ve başka bir kapsama değiştirilebilir.

  Aşağıdaki örnek, `parameterScopes` , atama portalda düzenlenirken kaynak seçimi için bir kapsam ayarlayan **Backuppolicyıd** adlı bir _strongtype_ parametresine yöneliktir.

  ```json
  "metadata": {
      "parameterScopes": {
          "backupPolicyId": "/subscriptions/{SubscriptionID}/resourcegroups/{ResourceGroupName}"
      }
  }
  ```

- `updatedBy` (dize): varsa, atamayı güncelleştiren güvenlik sorumlusunun kolay adı.
- `updatedOn` (dize): varsa, atama güncelleştirme zamanının Universal ISO 8601 tarih saat biçimi.

## <a name="enforcement-mode"></a>Zorlama modu

**Enforcementmode** özelliği, müşterilere ilke efektini başlatmadan veya [Azure etkinlik günlüğündeki](../../../azure-monitor/essentials/platform-logs-overview.md)girişleri tetiklemeden mevcut kaynaklarda bir ilkenin sonucunu test etme yeteneği sağlar. Bu senaryo genellikle "What If" olarak adlandırılır ve güvenli dağıtım uygulamalarına hizalanır. **Enforcementmode** [devre dışı](./effects.md#disabled) etkilerden farklıdır, çünkü bu efekt kaynak değerlendirmesinin hiç oluşmasını önler.

Bu özellik aşağıdaki değerlere sahiptir:

|Mod |JSON değeri |Tür |El ile düzelt |Etkinlik günlüğü girişi |Açıklama |
|-|-|-|-|-|-|
|Etkin |Varsayılan |string |Yes |Yes |İlke etkisi, kaynak oluşturma veya güncelleştirme sırasında zorlanır. |
|Devre dışı |Donotenzorlamalı |string |Yes |Hayır | İlke etkisi, kaynak oluşturma veya güncelleştirme sırasında zorlanmaz. |

İlke veya girişim tanımında **Enforcementmode** belirtilmemişse, _varsayılan_ değer kullanılır. **Enforcementmode** , _Donotenzorlamalı_ olarak ayarlandığında bile, dağıtım [görevleri](../how-to/remediate-resources.md) [deployifnotexists](./effects.md#deployifnotexists) ilkeleri için başlatılabilir.

## <a name="excluded-scopes"></a>Dışlanan kapsamlar

Atamanın **kapsamı** tüm alt kaynak kapsayıcılarını ve alt kaynakları içerir. Bir alt kaynak kapsayıcısına veya alt kaynağa tanım uygulanmazsa, **Notscopes** ayarları ayarlanarak her biri değerlendirmeden _dışlanabilirler_ . Bu özellik bir veya daha fazla kaynak kapsayıcısının veya kaynağın değerlendirmesinden DIŞLANMASINI sağlamak için bir dizidir. **Notscopes** , ilk atama oluşturulduktan sonra eklenebilir veya güncelleştirilir.

> [!NOTE]
> _Dışlanan_ bir kaynak, _muaf tutulan_ bir kaynaktan farklıdır. Daha fazla bilgi için bkz. [Azure ilkesinde kapsamı anlama](./scope.md).

## <a name="policy-definition-id"></a>İlke tanımı KIMLIĞI

Bu alan, bir ilke tanımının ya da bir girişim tanımının tam yol adı olmalıdır.
`policyDefinitionId` dizi değil bir dizedir. Bunun yerine bir [girişim](./initiative-definition-structure.md) kullanmak için birden çok ilke genellikle birlikte atanırsa, bu önerilir.

## <a name="non-compliance-messages"></a>Uyumsuzluk iletileri

Bir kaynağın ilkeyle veya girişim tanımıyla uyumsuz olduğunu açıklayan özel bir ileti ayarlamak için `nonComplianceMessages` atama tanımında ayarlayın. Bu düğüm, girdilerden oluşan bir dizidir `message` . Bu özel ileti, uyumsuz olmayan ve isteğe bağlı olarak varsayılan hata iletisine ek niteliğindedir.

> [!IMPORTANT]
> Uyumsuzluk için özel iletiler yalnızca [Kaynak Yöneticisi mod](./definition-structure.md#resource-manager-modes) tanımlarına sahip tanımlarda veya girişimlerle desteklenir.

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Atama bir girişim için ise, girişim içindeki her ilke tanımı için farklı iletiler yapılandırılabilirler. İletiler, `policyDefinitionReferenceId` girişim tanımında yapılandırılan değeri kullanır. Ayrıntılar için bkz. [ilke tanımları özellikleri](./initiative-definition-structure.md#policy-definition-properties).

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>Parametreler

İlke atamasının bu segmenti, [ilke tanımı veya girişim tanımında](./definition-structure.md#parameters)tanımlanan parametrelerin değerlerini sağlar. Bu tasarım, farklı kaynaklarla bir ilke veya girişim tanımını yeniden kullanmayı mümkün kılar, ancak farklı iş değerlerini veya sonuçlarını denetleyebilir.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

Bu örnekte, daha önce ilke tanımında tanımlanan parametreler ve ' dir `prefix` `suffix` . Bu ilke ataması `prefix` , **depta** ve `suffix` to **-LC** olarak ayarlanır. Aynı ilke tanımı farklı bir departman için farklı bir parametre kümesiyle yeniden kullanılabilir, ancak esneklik sağlarken ilke tanımlarının çoğaltılmasını ve karmaşıklığını azaltır.

## <a name="next-steps"></a>Sonraki adımlar

- [İlke tanımı yapısı](./definition-structure.md)hakkında bilgi edinin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](../how-to/programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](../how-to/get-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.
