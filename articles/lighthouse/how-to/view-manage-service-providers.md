---
title: Hizmet sağlayıcılarını görüntüleme ve yönetme
description: Müşteriler, hizmet sağlayıcıları, hizmet sağlayıcısı teklifleri ve Temsilcili kaynaklar hakkındaki bilgileri görüntülemek için Azure portal hizmet sağlayıcıları sayfasını kullanabilir.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f6ee5fb154d75ff715acf99c5184cd1652ccdb80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555573"
---
# <a name="view-and-manage-service-providers"></a>Hizmet sağlayıcılarını görüntüleme ve yönetme

Azure portal **hizmet sağlayıcıları** sayfası, müşterilere [](https://portal.azure.com) [Azure açık thouse](../overview.md)kullanan hizmet sağlayıcılarına yönelik denetim ve görünürlük sağlar. Müşteriler hizmet sağlayıcılarının ayrıntılarını görüntüleyebilir, belirli kaynakları devredebilir, yeni tekliflere bakabilir, hizmet sağlayıcı erişimini kaldırabilir ve daha fazlasını yapabilir.

Azure portal **hizmet sağlayıcıları** sayfasını görüntülemek Için, **tüm hizmetler**' i seçin ve ardından **hizmet sağlayıcılar** ' ı arayın ve seçin. Bu sayfayı, Azure portal üst kısmındaki arama kutusuna "hizmet sağlayıcıları" veya "Azure Aydınlathouse" girerek da bulabilirsiniz.

> [!NOTE]
> **Hizmet sağlayıcıları** sayfasını görüntülemek için, müşterinin kiracısındaki bir kullanıcının [okuyucu yerleşik rolüne](../../role-based-access-control/built-in-roles.md#reader) (veya okuyucu erişimi de içeren başka bir yerleşik Role) sahip olması gerekir.
>
> Teklifleri eklemek veya güncelleştirmek, kaynak atamak ve teklifleri kaldırmak için, kullanıcının `Microsoft.Authorization/roleAssignments/write` [sahip](../../role-based-access-control/built-in-roles.md#owner)gibi izne sahip bir rolü olması gerekir.

**Hizmet sağlayıcılar** sayfasının yalnızca, Azure açık thouse aracılığıyla Müşterinin aboneliklerine veya kaynak gruplarına erişimi olan hizmet sağlayıcılarıyla ilgili bilgileri gösterdiğini unutmayın. Bir müşteri, Azure burthouse kullanmayan ek hizmet sağlayıcılarıyla çalışıyorsa, bu hizmet sağlayıcılarıyla ilgili bilgileri burada görmezsiniz.

## <a name="view-service-provider-details"></a>Hizmet sağlayıcı ayrıntılarını görüntüle

Müşterinin kiracısında çalışmak üzere Azure ınthouse kullanarak geçerli hizmet sağlayıcılarının ayrıntılarını görüntülemek için, **hizmet sağlayıcıları** sayfasının sol tarafındaki **hizmet sağlayıcısı teklifleri** ' ni seçin.

Her teklif için, hizmet sağlayıcısının adını ve onunla ilişkili teklifi görürsünüz. Hizmet sağlayıcısının verildiği rol atamaları dahil olmak üzere bir açıklamayı ve diğer ayrıntıları görüntülemek için bir teklif seçebilirsiniz.

**Temsilciler** sütununda, bu teklif için hizmet sağlayıcısına kaç abonelik ve/veya kaynak grubu atanmış olduğunu görebilirsiniz. Hizmet sağlayıcısı, bu abonelikleri ve/veya kaynak gruplarını teklifte belirtilen erişim düzeylerine göre erişebilecek ve yönetebilecektir.

## <a name="add-or-remove-service-provider-offers"></a>Hizmet sağlayıcı tekliflerini ekleme veya kaldırma

**Hizmet sağlayıcı teklifleri** sayfasında yeni bir hizmet sağlayıcı teklifi eklemek Için **teklif Ekle**' yi seçin. Bu müşteri için bir hizmet sağlayıcının yayımladığı teklifleri görüntülemek için **özel teklifler** ' i seçin. Daha sonra **özel teklifler** ekranından bu teklifi seçebilir ve sonra **Ayarla + abone ol**' u seçebilirsiniz.

Bu teklifin satırındaki çöp kutusu simgesini seçerek istediğiniz zaman bir servis sağlayıcısı teklifini kaldırabilirsiniz. Silme işlemini onayladıktan sonra, söz konusu hizmet sağlayıcının artık bu teklif için daha önce atanmış olan kaynaklara erişimi olmayacaktır.

## <a name="delegate-resources"></a>Kaynaklara temsilci atama

Bir hizmet sağlayıcının bir müşterinin kaynaklarına erişebilmesi ve bunları yönetebilmesi için önce, bir veya daha fazla belirli abonelik ve/veya kaynak grubu için temsilci seçilmelidir. Bir müşteri bir teklifi kabul ettiğinde ancak henüz hiç kaynak temsilcisi yoksa, **hizmet sağlayıcı teklifleri** bölümünün en üstünde bir not görür. Bu, müşterinin, servis sağlayıcı müşterinin kaynaklarına erişmeden önce eylemde bulunan bir işlem yapması gerektiğini bilmesini sağlar.

Abonelikler veya kaynak grupları atamak için:

1. Hizmet sağlayıcı, teklif ve ad içeren satırın kutusunu işaretleyin. Ardından ekranın üst kısmındaki **kaynakları devretmek** ' ı seçin.
1. **Kaynak temsilcisi** sayfasının **Teklif Ayrıntıları** bölümünde, hizmet sağlayıcısı ve teklifiyle ilgili ayrıntıları gözden geçirin. Teklifin rol atamalarını gözden geçirmek için, **Seçili teklifin ayrıntılarını görmek üzere buraya tıklayın ' ı** seçin.
1. **Temsilci** bölümünde, **temsilci abonelikleri** veya **temsilci kaynak grupları**' nı seçin.
1. Bu teklif için temsilci seçmek istediğiniz abonelikleri ve/veya kaynak gruplarını seçin ve ardından **Ekle**' yi seçin.
1. Bu hizmet sağlayıcısına seçtiğiniz kaynaklara erişim izni vermek istediğinizi onaylamak için sayfanın altındaki onay kutusunu seçin ve ardından **temsilci**' i seçin.

## <a name="update-service-provider-offers"></a>Hizmet sağlayıcı tekliflerini güncelleştirme

Bir müşteri teklif ekledikten sonra, hizmet sağlayıcı aynı teklifin güncelleştirilmiş bir sürümünü Azure Marketi 'ne yayımlayabilir. Örneğin, yeni bir rol tanımı eklemek isteyebilir. Teklifin yeni bir sürümü yayımlandıysa, **servis sağlayıcı** teklif sayfasında bu teklif için bir "güncelleştirme" simgesi gösterilir. Teklifin geçerli sürümü ile yeni bir arasındaki farkları görmek için bu simgeyi seçin.

 ![Teklifi Güncelleştir simgesi](../media/update-offer.jpg)

Değişiklikleri gözden geçirdikten sonra müşteri yeni sürüme güncelleştirmeyi tercih edebilir. Bu durumda, yeni sürümde belirtilen yetkilendirmeler ve diğer ayarlar, bu teklif için temsilci seçilmiş olan tüm abonelikler ve/veya kaynak grupları için de geçerlidir.

## <a name="view-delegations"></a>Temsilcileri görüntüle

Temsilci atama, bir müşterinin Temsilcili kaynaklar için hizmet sağlayıcısına izin veren rol atamalarını temsil eder. Bu bilgileri görüntülemek için, **hizmet sağlayıcıları** sayfasının sol tarafındaki **Temsilciler** ' ı seçin.

Sayfanın üst kısmındaki filtreler, yetkilendirme bilgilerinizi sıralamanıza ve gruplandırmasına olanak tanır. Belirli müşterilere, tekliflere veya anahtar sözcüklere göre de filtre uygulayabilirsiniz.

> [!NOTE]
> Müşteriler, Azure portal veya API 'Ler aracılığıyla [temsilci seçilen kapsam için rol atamalarını görüntülerken](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) bu rol atamalarını veya hizmet sağlayıcı kiracısından gelen tüm kullanıcıları görmez.

## <a name="audit-delegations-in-your-environment"></a>Ortamınızdaki denetim temsilcileri

Müşteriler, Azure ışıklı kullanım için temsilci seçilmiş abonelikler ve/veya kaynak grupları hakkında görünürlük elde etmek isteyebilir. Bu, özellikle çok sayıda aboneliğe sahip olan veya yönetim görevlerini gerçekleştiren çok sayıda kullanıcıya sahip olan müşteriler için yararlıdır.

[Bir yönetim kiracısına kapsamları temsilciliğini denetlemek](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json)Için bir [Azure ilkesi yerleşik ilke tanımı](../../governance/policy/samples/built-in-policies.md#lighthouse) sağlıyoruz. Bu ilkeyi, denetlemek istediğiniz tüm abonelikleri içeren bir yönetim grubuna atayabilirsiniz. Bu ilkeyle uyumluluğu denetlediğinizde, tüm temsilci olan abonelikler ve/veya kaynak grupları (ilkenin atandığı yönetim grubu içinde) uyumlu olmayan bir durumda gösterilir. Daha sonra sonuçları gözden geçirebilir ve beklenmeyen temsilci yok olduğunu doğrulayabilirsiniz.

Başka bir [yerleşik ilke tanımı](../../governance/policy/samples/built-in-policies.md#lighthouse) , [temsilcileri yönetim kiracılarına göre kısıtlamanızı](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json)sağlar. Bu ilke benzer şekilde, temsilcilerini sınırlamak istediğiniz abonelikleri içeren bir yönetim grubuna da uygulanabilir. İlke dağıtıldıktan sonra, belirttiğiniz bir kiracıya bir aboneliği devretmek için herhangi bir girişim reddedilir.

İlke atama ve uyumluluk durumu sonuçlarını görüntüleme hakkında daha fazla bilgi için bkz. [hızlı başlangıç: ilke ataması oluşturma](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ışıklı kullanım](../overview.md)hakkında daha fazla bilgi edinin.
- [Hizmet sağlayıcı etkinliğinin nasıl denetleneceğini](view-service-provider-activity.md)öğrenin.
- Hizmet **sağlayıcılarının, Azure Portal müşteriler sayfasında** [müşterileri nasıl görüntüleyip yönetebileceğini](view-manage-customers.md) öğrenin.
- [Birden çok kiracıyı yöneten kuruluşların](../concepts/enterprise.md) , yönetim deneyimlerini birleştirmek için Azure athouse 'ı nasıl kullanabileceği hakkında bilgi edinin.

