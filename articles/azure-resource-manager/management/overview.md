---
title: Azure Resource Manager genel bakış
description: Azure’daki kaynakların dağıtımı, yönetimi ve erişim denetimi için Azure Resource Manager’ın nasıl kullanılacağı açıklanmaktadır.
ms.topic: overview
ms.date: 03/25/2021
ms.custom: contperf-fy21q1,contperf-fy21q3-portal
ms.openlocfilehash: 6cd9aa82ad2f8a821ae82a361b3f11b72ca25f7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608553"
---
# <a name="what-is-azure-resource-manager"></a>Azure Resource Manager nedir?

Azure Resource Manager, Azure için dağıtım ve yönetim hizmetidir. Azure hesabınızda kaynak oluşturma, güncelleştirme ve silme işlemlerini gerçekleştirmenizi sağlayan bir yönetim katmanı sunar. Dağıtım sonrasında kaynaklarınızın güvenliğini sağlamak ve onları düzenlemek için erişim denetimleri, kilitler ve etiketler gibi yönetim özelliklerini kullanabilirsiniz.

Azure Resource Manager şablonları (ARM şablonları) hakkında bilgi edinmek için bkz. [şablon dağıtımına genel bakış](../templates/overview.md).

## <a name="consistent-management-layer"></a>Tutarlı yönetim katmanı

Kullanıcı Azure araçlarından, API'lerden veya SDK'lardan bir istek gönderdiğinde bu istek Resource Manager'a ulaşır. Hizmet isteğin kimliğini doğrular ve onu yetkilendirir. Resource Manager, istenen eylemin gerçekleştirilmesi için isteği ilgili Azure hizmetine gönderir. Tüm istekler aynı API tarafından işlendiğinden farklı araçlar da kullansanız tutarlı sonuçlar ve özelliklerle karşılaşırsınız.

Aşağıdaki görüntüde Azure Resource Manager'ın Azure isteklerini işleme konusundaki rolü gösterilmiştir.

![Resource Manager istek modeli](./media/overview/consistent-management-layer.png)

Portalda kullanılabilen tüm özellikler aynı zamanda PowerShell, Azure CLI, REST API'leri ve istemci SDK'ları aracılığıyla da kullanılabilir. İlk olarak API'lerle başlatılan işlevler 180 gün içinde portalda kullanıma sunulacaktır.

## <a name="terminology"></a>Terminoloji

Azure Resource Manager’ı kullanmaya yeni başladıysanız bilmiyor olabileceğiniz bazı terimler vardır.

* **kaynak** -Azure aracılığıyla kullanılabilen yönetilebilir bir öğe. Sanal makineler, depolama hesapları, web uygulamaları, veritabanları ve sanal ağlar kaynaklara örnek olarak verilebilir. Kaynak grupları, abonelikler, yönetim grupları ve Etiketler Ayrıca kaynaklara örnektir.
* **kaynak grubu** -bir Azure çözümü için ilgili kaynakları tutan bir kapsayıcı. Kaynak grubu, grup olarak yönetmek istediğiniz kaynakları içerir. Kuruluşunuz açısından en mantıklı duruma göre hangi kaynakların bir kaynak grubuna ait olduğuna siz karar verirsiniz. Bkz. [kaynak grupları](#resource-groups).
* **kaynak sağlayıcısı** -Azure kaynakları sağlayan bir hizmet. Örneğin, bir ortak kaynak sağlayıcısı `Microsoft.Compute` , sanal makine kaynağını temin eder. `Microsoft.Storage` başka bir ortak kaynak sağlayıcısıdır. Bkz. [kaynak sağlayıcıları ve türleri](resource-providers-and-types.md).
* **Kaynak Yöneticisi Template** -bir kaynak grubuna, aboneliğe, yönetim grubuna veya kiracıya dağıtılacak bir veya daha fazla kaynağı tanımlayan bir JAVASCRIPT nesne GÖSTERIMI (JSON) dosyası. Şablon, kaynakları tutarlı ve sürekli olarak dağıtmak için kullanılabilir. [Şablon dağıtımı genel bakış](../templates/overview.md)bölümüne bakın.
* **bildirim temelli söz dizimi** - Oluşturmaya yönelik programlama komutları dizisini yazmak zorunda kalmadan "Oluşturmak istediğiniz şeyi" belirtmenize imkan tanıyan söz dizimi. Resource Manager şablonu, bildirim temelli söz diziminin bir örneğidir. Dosya içinde Azure’a dağıtılacak altyapının özelliklerini tanımlarsınız.  [Şablon dağıtımı genel bakış](../templates/overview.md)bölümüne bakın.

## <a name="the-benefits-of-using-resource-manager"></a>Resource Manager’ı kullanmanın avantajları

Resource Manager ile şunları yapabilirsiniz:

* Altyapınızı yönetmek için betikler yerine bildirim temelli şablonlar kullanın.

* Çözümünüze ait kaynakları ayrı ayrı işlemek yerine tümünü grup olarak birlikte dağıtın, yönetin ve izleyin.

* Çözümünüzü geliştirme yaşam döngüsü boyunca yeniden dağıtın ve kaynaklarınızın tutarlı bir şekilde dağıtılacağından emin olun.

* Kaynaklar arasındaki bağımlılıkları tanımlayarak doğru sırada dağıtılmalarını sağlayın.

* Azure rol tabanlı erişim denetimi (Azure RBAC), yönetim platformuyla yerel olarak tümleştirildiği için tüm hizmetlere erişim denetimi uygulayın.

* Aboneliğinizdeki tüm kaynakları mantıksal olarak düzenlemek için kaynaklara etiket uygulayın.

* Aynı etiketi paylaşan bir grup kaynak ile ilgili maliyetleri görüntüleyerek kuruluşunuzun faturalandırma süreçlerini kolaylaştırın.

## <a name="understand-scope"></a>Kapsamı anlama

Azure dört kapsam düzeyi sağlar: [Yönetim grupları](../../governance/management-groups/overview.md), abonelikler, [kaynak grupları](#resource-groups)ve kaynaklar. Aşağıdaki resimde bu katmanlara ait bir örnek gösterilir.

![Yönetim düzeyleri](./media/overview/scope-levels.png)

Yönetim ayarlarını bu kapsam düzeylerinden birinde uygularsınız. Seçtiğiniz düzey, ayarın ne kadar yaygın olarak uygulanacağını belirler. Düşük düzeyler, yüksek düzeylerdeki ayarları devralır. Örneğin, aboneliğe bir [ilke](../../governance/policy/overview.md) uyguladığınızda, ilke aboneliğinizdeki tüm kaynak gruplarına ve kaynaklara uygulanır. Kaynak grubunda bir ilke uyguladığınızda, bu ilke kaynak grubuna ve tüm kaynaklarına uygulanır. Ancak, başka bir kaynak grubu Bu ilke atamasına sahip değildir.

Şablonları kiracılara, yönetim gruplarına, aboneliklere veya kaynak gruplarına dağıtabilirsiniz.

## <a name="resource-groups"></a>Kaynak grupları

Kaynak gruplarınızı tanımlarken göz önüne almanız gereken bazı önemli faktörler bulunur:

* Kaynak grubunuzdaki tüm kaynaklar aynı yaşam döngüsünü paylaşmalıdır. Bunları birlikte dağıtır, güncelleştirir ve silersiniz. Sunucu gibi bir kaynağın farklı bir dağıtım döngüsüyle bulunması gerekiyorsa, başka bir kaynak grubunda olması gerekir.

* Her kaynak yalnızca bir kaynak grubunda yer alabilir.

* Bir kaynak grubuna dilediğiniz zaman kaynak ekleyebilir veya kaldırabilirsiniz.

* Bir kaynağı, bir kaynak grubundan bir diğerine taşıyabilirsiniz. Daha fazla bilgi için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](move-resource-group-and-subscription.md).

* Bir kaynak grubundaki kaynaklar kaynak grubundan farklı bölgelerde bulunabilir.

* Bir kaynak grubu oluştururken bu kaynak grubu için bir konum belirtmeniz gerekir. "Bir kaynak grubu için neden konum gerekli olsun? Ayrıca kaynaklar kaynak grubundan farklı konumlarda olabiliyorsa kaynak grubu konumu neden önemli olsun?" diye soruyor olabilirsiniz Kaynak grubu, kaynaklarla ilgili meta verileri depolar. Kaynak grubu için bir konum belirttiğinizde, meta verilerin nerede depolandığını belirtirsiniz. Uyumluluk nedeniyle verilerinizin belirli bir bölgeye depolandığından emin olmanız gerekebilir.

   Kaynak grubunun bölgesi geçici olarak kullanılamıyorsa, meta veriler kullanılamadığından kaynak grubundaki kaynakları güncelleştiremezsiniz. Diğer bölgelerdeki kaynaklar beklendiği gibi çalışmaya devam eder, ancak bunları güncelleştiremezsiniz. Güvenilir uygulamalar oluşturma hakkında daha fazla bilgi için bkz. [güvenilir Azure uygulamaları tasarlama](/azure/architecture/checklist/resiliency-per-service).

* Bir kaynak grubu, yönetim eylemleri için erişim denetimini incelemek üzere kullanılabilir. Bir kaynak grubunu yönetmek için [Azure ilkeleri](../../governance/policy/overview.md), [Azure rolleri](../../role-based-access-control/role-assignments-portal.md)veya [kaynak kilitleri](lock-resources.md)atayabilirsiniz.

* Bir kaynak grubuna [Etiketler uygulayabilirsiniz](tag-resources.md) . Kaynak grubundaki kaynaklar bu etiketleri almıyor.

* Kaynak, diğer kaynak gruplarındaki kaynaklara bağlanabilir. Bu senaryo, iki kaynak ilişkili olduğunda ancak aynı yaşam döngüsünü paylaşmadığında yaygın bir senaryodur. Örneğin, farklı bir kaynak grubundaki bir veritabanına bağlanan bir Web uygulamanız olabilir.

* Bir kaynak grubunu sildiğinizde, kaynak grubundaki tüm kaynaklar da silinir. Azure Resource Manager Bu silmeleri nasıl düzenleyen hakkında daha fazla bilgi için bkz. [Azure Resource Manager kaynak grubu ve kaynak silme](delete-resource-group.md).

* Her kaynak grubunda bir kaynak türünün en fazla 800 örneğini dağıtabilirsiniz. Bazı kaynak türleri [800 örnek sınırından muaf](resources-without-resource-group-limit.md)tutulur. Daha fazla bilgi için bkz. [kaynak grubu sınırları](azure-subscription-service-limits.md#resource-group-limits).

* Bazı kaynaklar bir kaynak grubunun dışında bulunabilir. Bu kaynaklar [aboneliğe](../templates/deploy-to-subscription.md), [yönetim grubuna](../templates/deploy-to-management-group.md)veya [kiracıya](../templates/deploy-to-tenant.md)dağıtılır. Bu kapsamlarda yalnızca belirli kaynak türleri desteklenir.

* Bir kaynak grubu oluşturmak için [Portal](manage-resource-groups-portal.md#create-resource-groups), [POWERSHELL](manage-resource-groups-powershell.md#create-resource-groups), [Azure CLI](manage-resource-groups-cli.md#create-resource-groups)veya [ARM şablonunu](../templates/deploy-to-subscription.md#resource-groups)kullanabilirsiniz.

## <a name="resiliency-of-azure-resource-manager"></a>Azure Resource Manager esnekliği

Azure Resource Manager hizmeti dayanıklılık ve sürekli kullanılabilirlik için tasarlanmıştır. REST API Kaynak Yöneticisi ve denetim düzlemi işlemleri (gönderilen istekler `management.azure.com` ):

* Bölgeler arasında dağıtılır. Bazı hizmetler bölgesel olarak verilebilir.

* Birden çok Kullanılabilirlik Alanları sahip konumlarda Kullanılabilirlik Alanları (ve bölgelerin yanı sıra) arasında dağıtılır.

* Tek bir mantıksal veri merkezine bağımlı değildir.

* Bakım etkinlikleri için hiçbir şekilde alınmadı.

Bu dayanıklılık, Kaynak Yöneticisi aracılığıyla istek alan hizmetler için geçerlidir. Örneğin, bu dayanıklılık avantajlarından yararlanın Key Vault.

## <a name="next-steps"></a>Sonraki adımlar

* Azure hizmetleri genelinde uygulanan sınırlar hakkında bilgi edinmek için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](azure-subscription-service-limits.md).

* Kaynakları taşıma hakkında bilgi edinmek için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](move-resource-group-and-subscription.md).

* Kaynakları etiketleme hakkında bilgi edinmek için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](tag-resources.md).

* Kaynakları kilitleme hakkında bilgi edinmek için bkz. [beklenmeyen değişiklikleri engellemek için kaynakları kilitleme](lock-resources.md).
