---
title: Azure Ilkesinde kapsamı anlayın
description: Azure Resource Manager kapsamında kapsam kavramını ve Azure ilkesi 'nin hangi kaynaklarda değerlendirileceğini denetlemek için Azure Ilkesi 'nin nasıl uygulanacağını açıklar.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: c198d2d2961b6d9e10a3b78481183cba7f7197ca
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167745"
---
# <a name="understand-scope-in-azure-policy"></a>Azure Ilkesinde kapsamı anlayın

Hangi kaynakların değerlendirilemekte olduğunu ve hangi kaynakların Azure Ilkesi tarafından değerlendirildiğini belirleyen birçok ayar vardır. Bu denetimlerin birincil kavramı _kapsamdadır_. Azure Ilkesindeki kapsam, kapsamın Azure Resource Manager nasıl çalıştığını temel alır. Üst düzey bir genel bakış için [Azure Resource Manager kapsam](../../../azure-resource-manager/management/overview.md#understand-scope)bölümüne bakın.
Bu makalede, Azure Ilkesinde _kapsamın_ önem derecesi ve ilgili nesneler ve özellikler açıklanmaktadır.

## <a name="definition-location"></a>Tanım konumu

Azure Ilkesi tarafından kullanılan ilk örnek kapsamı bir ilke tanımı oluşturulur. Tanım bir yönetim grubuna veya aboneliğe kaydedilebilir. Konum, girişim veya ilkenin atanabileceği kapsamı belirler. Kaynaklar, atama için hedeflemek üzere tanım konumunun kaynak hiyerarşisinde olmalıdır.

Tanım konumu bir ise:

- **Abonelik** -yalnızca bu abonelikte yer alan kaynaklara ilke tanımı atanabilir.
- **Yönetim grubu** -yalnızca alt yönetim grupları ve alt abonelikler içindeki kaynaklara ilke tanımı atanabilir. İlke tanımını birkaç aboneliğe uygulamayı planlıyorsanız, konum her bir aboneliği içeren bir yönetim grubu olmalıdır.

Konum, varsa ilke tanımını kullanmak istediğiniz tüm kaynaklar tarafından paylaşılan kaynak kapsayıcısı olmalıdır. Bu kaynak kapsayıcısı genellikle kök yönetim grubunun yakınında bir yönetim grubudur.

## <a name="assignment-scopes"></a>Atama kapsamları

Atama, bir kapsam ayarlanmış çeşitli özelliklere sahiptir. Bu özelliklerin kullanımı, Azure Ilkesi için hangi kaynağın değerlendirileceğini ve hangi kaynakların uyumluluğa doğru olduğunu belirler. Bu özellikler aşağıdaki kavramlarla eşlenir:

- Dahil-kaynak hiyerarşisi veya tek bir kaynak, tanım tarafından uyumluluk açısından değerlendirilmelidir. `properties.scope`Atama nesnesindeki özellik, uyumluluk için nelerin ekleneceğini ve değerlendirileceğini belirler. Daha fazla bilgi için bkz. [atama tanımı](./assignment-structure.md).

- Dışlama-bir kaynak hiyerarşisi veya tek bir kaynak, tanım tarafından uyumluluk açısından değerlendirilmemelidir. `properties.notScopes`Atama nesnesindeki _Array_ özelliği, nelerin dışlanacağını belirler. Bu kapsamların içindeki kaynaklar değerlendirilmez veya uyumluluk sayısına dahil değildir. Daha fazla bilgi için bkz. [atama tanımı-dışlanan kapsamlar](./assignment-structure.md#excluded-scopes).

İlke atamasındaki özelliklere ek olarak, [ilke muafiyet](./exemption-structure.md) nesnesi olur. Muafiyetler, bir atamanın bir kısmını değerlendirilmeyecek şekilde belirlemek için bir yöntem sağlayarak kapsam hikayesini geliştirir.

- İstisna (**Önizleme özelliğinde ücretsiz** )-bir kaynak hiyerarşisi veya tek bir kaynak, tanım tarafından uyumluluk açısından değerlendirilmelidir, ancak feragat aldığınız veya başka bir yöntem aracılığıyla azaltılmaya yönelik bir nedenle değerlendirilmeyecektir. Bu durumdaki kaynaklar, izlenebilmeleri için uyumluluk raporlarında **muaf tutulur** . Muafiyet nesnesi, kaynak hiyerarşisinde veya ayrı bir kaynakta bir alt nesne olarak oluşturulur ve bu da muafiyet kapsamını belirler. Kaynak hiyerarşisi veya tek bir kaynak birden fazla atamalara muaf olabilir. Muafiyet, özelliği kullanılarak bir zamanlamaya göre sona ermek üzere yapılandırılabilir `expiresOn` . Daha fazla bilgi için bkz. [Muafiyet tanımı](./exemption-structure.md).

  > [!NOTE]
  > Bir kaynak hiyerarşisi veya ayrı bir kaynak için muafiyet verme etkisi nedeniyle, muafiyetlerin ek güvenlik önlemleri vardır. `Microsoft.Authorization/policyExemptions/write`Kaynak hiyerarşisinde veya tek bir kaynakta işlem gerektirmesinin yanı sıra, bir istisna oluşturucusunun `exempt/Action` hedef atamada fiil olması gerekir.

## <a name="scope-comparison"></a>Kapsam karşılaştırması

Aşağıdaki tablo, kapsam seçeneklerinin bir karşılaştırmasına sahiptir:

| | Edilmesi | Dışlama (notScopes) | Dışarıda |
|---|:---:|:---:|:---:|
|**Kaynaklar değerlendirilir** | &#10004; | - | - |
|**Kaynak Yöneticisi nesnesi** | - | - | &#10004; |
|**İlke atama nesnesinin değiştirilmesini gerektirir** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>Sonraki adımlar

- [İlke tanımı yapısı](./definition-structure.md)hakkında bilgi edinin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](../how-to/programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](../how-to/get-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.