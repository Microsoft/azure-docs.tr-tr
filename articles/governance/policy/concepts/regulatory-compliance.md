---
title: Girişim tanımlarında mevzuat uyumluluğu
description: Access Control, yapılandırma yönetimi ve diğerleri gibi yasal etki alanına göre ilkeleri gruplandırmak için bir girişim tanımının nasıl kullanılacağını açıklar.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 08173cd94e0841fa7a18fd55d53aa97281724ede
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106094073"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Azure Ilkesinde mevzuata uyumluluğu

Azure Ilkesinde mevzuata uyumluluğu,, sorumluluğa göre **denetimlerin** ve **Uyumluluk etki alanlarının** bir listesini görüntülemek için yerleşik girişim tanımları sağlar (_Müşteri_, _Microsoft_, _paylaşılan_).
Microsoft-sorumlu denetimlerinde, bu uyumluluğa ulaşmak için üçüncü taraf kanıtlamayı ve uygulama ayrıntılarımızı temel alan denetim sonuçlarımıza ek ayrıntılar sağlıyoruz.
Microsoft-sorumlu denetimleri `type` [statiktir](./definition-structure.md#type).

> [!NOTE]
> Mevzuat uyumluluğu bir önizleme özelliğidir. Güncelleştirilmiş yerleşik bileşenler için, girişimler ilgili uyumluluk standardına eşlenir. Mevcut uyumluluk standart girişimleri, yasal uyumluluğu destekleyecek şekilde güncelleştiriliyor.

## <a name="regulatory-compliance-defined"></a>Mevzuat uyumluluğu tanımlandı

Yasal uyumluluk, bir girişim tanımının [Gruplandırma](./initiative-definition-structure.md#policy-definition-groups) bölümünde oluşturulur. Yerleşik olarak, girişim tanımındaki her gruplandırma bir ad (**Denetim**), bir kategori (**Uyumluluk etki alanı**) tanımlar ve bu **denetimle** ilgili bilgiler içeren [policymetadata](./initiative-definition-structure.md#metadata-objects) nesnesine bir başvuru sağlar. Mevzuat uyumluluk girişimi tanımının `category` özelliği, **mevzuat uyumluluğu** olarak ayarlanmalıdır. Aksi halde standart girişim tanımı olarak, mevzuat uyumluluk girişimleri dinamik atamalar oluşturmak için [parametreleri](./initiative-definition-structure.md#parameters) destekler.

Müşteriler kendi düzenleme uyumluluğu girişimlerini oluşturabilir. Bu tanımlar özgün olabilir veya mevcut yerleşik tanımlardan kopyalanabilir. Başvuru olarak yerleşik bir mevzuat uyumluluk girişimi tanımı kullanıyorsanız, [Azure Policy GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance)deposunda mevzuat uyumluluk tanımlarının kaynağını izlemeniz önerilir.

Azure Güvenlik Merkezi panonuza özel bir mevzuat uyumluluk girişimi bağlamak için bkz. [Azure Güvenlik Merkezi-özel güvenlik Ilkeleri kullanma](../../../security-center/custom-security-policies.md).

## <a name="regulatory-compliance-in-portal"></a>Portalda mevzuata uyumluluğu

[Gruplarla](./initiative-definition-structure.md#policy-definition-groups)bir girişim tanımı oluşturulduğunda, bu girişim Için portaldaki **Uyumluluk** ayrıntıları sayfasında ek bilgiler bulunur. 

Yeni bir sekme, sayfaya **denetimler** eklenir. Filtreleme, **Uyumluluk etki alanı** tarafından kullanılabilir ve Ilke tanımları `title` **policymetadata** nesnesinden alana göre gruplandırılır. Her satır, uyumluluk durumunu, onun parçası olan **Uyumluluk etki alanını** , sorumluluk bilgilerini ve bu **denetimi** ne kadar uyumlu olmayan ve uyumlu ilke tanımlarının olduğunu gösteren bir **denetimi** temsil eder.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="NıST SP 800-53 R4, uyumlu ve uyumlu olmayan denetimleri gösteren bir yerleşik tanım için mevzuat uyumluluğuna genel bakış ekran görüntüsü.":::

Bir **Denetim** seçildiğinde, bu denetimle ilgili ayrıntıların bir sayfası açılır. **Genel bakış** , ve içindeki bilgileri `description` içerir `requirements` . **İlkeler** sekmesinin altında, bu **denetime** katkıda bulunan girişim içindeki tüm bireysel ilke tanımları vardır. **Kaynak uyumluluğu** sekmesi, görüntülenmekte olan **denetimin** üye ilkesi tarafından değerlendirilen her bir kaynağın ayrıntılı bir görünümünü sağlar.

> [!NOTE]
> Bir [statik](./definition-structure.md#type) ilke tanımı için **Microsoft tarafından yönetilen** bir değerlendirme türü kullanılır `type` .

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="NıST SP 800-53 R4 yerleşik tanımının sınır koruma denetimi için mevzuat uyumluluk ayrıntılarının ekran görüntüsü.":::

Aynı **Denetim** sayfasından, **kaynak uyumluluğu** sekmesine geçiş yapmak, bu **denetimin** ilke tanımlarının dahil olduğu tüm kaynakları gösterir. Ad, KIMLIK, uyumluluk durumu, kaynak türü ve konum için filtreler kullanılabilir.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="NıST SP 800-53 R4 yerleşik tanımının sınır koruma denetimine kaynak uyumluluğuna ilişkin ekran görüntüsü.":::

## <a name="regulatory-compliance-in-sdk"></a>SDK 'da mevzuata uyumluluğu

Bir girişim tanımında mevzuat uyumluluğu etkinse, değerlendirme taraması kaydı, olayları ve ilke durumları SDK 'sının her biri ek özellikler döndürür. Bu ek özellikler uyumluluk durumuna göre gruplandırılır ve her durumda kaç grup olduğu hakkında bilgi sağlar.

Aşağıdaki kod, bir çağrıdan eklenen sonuçların bir örneğidir `summarize` :

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Girişim tanımı yapısına](./initiative-definition-structure.md) bakın
- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [İlkenin etkilerini anlama](./effects.md) konusunu gözden geçirin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
