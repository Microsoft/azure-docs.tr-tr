---
title: Genel Bakış
description: Azure’daki kaynakların dağıtımı, yönetimi ve erişim denetimi için Azure Resource Manager’ın nasıl kullanılacağı açıklanmaktadır.
ms.topic: overview
ms.date: 08/29/2019
ms.openlocfilehash: 29691739bb0e42b293bf4730917087496ad2fd69
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150737"
---
# <a name="azure-resource-manager-overview"></a>Azure Resource Manager genel bakış

Azure Resource Manager, Azure dağıtım ve yönetim hizmetidir. Azure aboneliğinizde kaynak oluşturmanıza, güncelleştirmenize ve silmenizi sağlayan bir yönetim katmanı sağlar. Dağıtımdan sonra kaynaklarınızı güvenli hale getirmek ve düzenlemek için erişim denetimi, kilitler ve Etiketler gibi yönetim özelliklerini kullanırsınız.

Azure Resource Manager şablonları hakkında bilgi edinmek için bkz. [şablon dağıtımı genel bakış](template-deployment-overview.md).

## <a name="consistent-management-layer"></a>Tutarlı yönetim katmanı

Bir Kullanıcı Azure araçlarından, API 'lerden veya SDK 'lardan bir istek gönderdiğinde, Kaynak Yöneticisi isteği alır. İsteğin kimliğini doğrular ve yetkilendirir. Kaynak Yöneticisi isteği, istenen eylemi alan Azure hizmetine gönderir. Tüm istekler aynı API aracılığıyla işlendiğinden, tüm farklı araçlarda tutarlı sonuçlar ve yetenekler görürsünüz.

Aşağıdaki görüntüde, Azure isteklerini işlerken Azure Resource Manager rolün yürütüldüğü gösterilmektedir. 

![Resource Manager istek modeli](./media/resource-group-overview/consistent-management-layer.png)

Portalda kullanılabilen tüm yetenekler, PowerShell, Azure CLı, REST API 'Ler ve istemci SDK 'Ları aracılığıyla da kullanılabilir. İlk olarak API'lerle başlatılan işlevler 180 gün içinde portalda kullanıma sunulacaktır.

## <a name="terminology"></a>Terminoloji

Azure Resource Manager’ı kullanmaya yeni başladıysanız bilmiyor olabileceğiniz bazı terimler vardır.

* **kaynak** - Azure ile kullanılabilen yönetilebilir bir öğe. Sanal makineler, depolama hesapları, Web uygulamaları, veritabanları ve sanal ağlar, kaynak örnekleridir.
* **kaynak grubu** - Bir Azure çözümü için ilgili kaynakları bir arada tutan bir kapsayıcıdır. Kaynak grubu, Grup olarak yönetmek istediğiniz kaynakları içerir. Kuruluşunuz için en mantıklı duruma göre kaynak grubunda hangi kaynakların ait olduğuna karar verirsiniz. Bkz. [Kaynak grupları](#resource-groups).
* **kaynak sağlayıcısı** -Azure kaynakları sağlayan bir hizmet. Örneğin, ortak bir kaynak sağlayıcısı, sanal makine kaynağını sağlayan Microsoft. COMPUTE ' dir. Microsoft. Storage, başka bir ortak kaynak sağlayıcıdır. Bkz. [kaynak sağlayıcıları ve türleri](resource-manager-supported-services.md).
* **Kaynak Yöneticisi Template** -bir kaynak grubuna veya aboneliğe dağıtılacak bir veya daha fazla kaynağı tanımlayan bir JAVASCRIPT nesne GÖSTERIMI (JSON) dosyası. Şablon, kaynakları tutarlı ve sürekli olarak dağıtmak için kullanılabilir. [Şablon dağıtımı genel bakış](template-deployment-overview.md)bölümüne bakın.
* **bildirim temelli söz dizimi** - Oluşturmaya yönelik programlama komutları dizisini yazmak zorunda kalmadan "Oluşturmak istediğiniz şeyi" belirtmenize imkan tanıyan söz dizimi. Resource Manager şablonu, bildirim temelli söz diziminin bir örneğidir. Dosya içinde Azure’a dağıtılacak altyapının özelliklerini tanımlarsınız.  [Şablon dağıtımı genel bakış](template-deployment-overview.md)bölümüne bakın.

## <a name="the-benefits-of-using-resource-manager"></a>Resource Manager’ı kullanmanın avantajları

Kaynak Yöneticisi, şunları yapabilirsiniz:

* Altyapınızı betikler yerine bildirim temelli şablonlar aracılığıyla yönetin.

* Bu kaynakları tek tek işlemek yerine, çözümünüz için tüm kaynakları bir grup olarak dağıtın, yönetin ve izleyin.

* Çözümünüzü geliştirme yaşam döngüsü boyunca yeniden dağıtın ve kaynaklarınızın tutarlı bir durumda dağıtıldığından emin olmanız gerekir.

* Doğru sırada dağıtılabilmesi için kaynaklar arasındaki bağımlılıkları tanımlayın.

* Rol tabanlı Access Control (RBAC), yönetim platformuyla yerel olarak tümleşik olduğundan, kaynak grubunuzdaki tüm hizmetlere erişim denetimi uygulayın.

* Aboneliğinizdeki tüm kaynakları mantıksal olarak düzenlemek için kaynaklara Etiketler uygulayın.

* Aynı etiketi paylaşan bir grup kaynak için maliyetleri görüntüleyerek kuruluşunuzun faturalandırmasını açıklığa kavuşturun.

## <a name="understand-scope"></a>Kapsamı anlama

Azure dört kapsam düzeyi sağlar: [Yönetim grupları](../governance/management-groups/overview.md), abonelikler, [kaynak grupları](#resource-groups)ve kaynaklar. Aşağıdaki resimde bu katmanlara ait bir örnek gösterilir.

![Kapsam](./media/resource-group-overview/scope-levels.png)

Yönetim ayarlarını bu kapsam düzeylerinden birinde uygularsınız. Seçtiğiniz düzey, ayarın ne kadar yaygın olarak uygulanacağını belirler. Düşük düzeyler, yüksek düzeylerdeki ayarları devralır. Örneğin, aboneliğe bir [ilke](../governance/policy/overview.md) uyguladığınızda, ilke aboneliğinizdeki tüm kaynak gruplarına ve kaynaklara uygulanır. Kaynak grubuna bir ilke uyguladığınızda, bu ilke kaynak grubu ve tüm kaynakları uygulanır. Ancak, başka bir kaynak grubu Bu ilke atamasına sahip değildir.

Şablonları yönetim gruplarına, aboneliklerine veya kaynak gruplarına dağıtabilirsiniz.

## <a name="resource-groups"></a>Kaynak grupları

Kaynak gruplarınızı tanımlarken göz önüne almanız gereken bazı önemli faktörler bulunur:

* Grubunuzdaki tüm kaynaklar aynı yaşam döngüsünü paylaşmalıdır. Bunları birlikte dağıtır, güncelleştirir ve silersiniz. Veritabanı sunucusu gibi bir kaynağın farklı bir dağıtım döngüsünde bulunması gerekiyorsa, bu kaynak farklı bir kaynak grubuna konulmalıdır.

* Her kaynak yalnızca bir kaynak grubunda yer alabilir.

* Bir kaynak grubuna dilediğiniz zaman kaynak ekleyebilir veya kaldırabilirsiniz.

* Bir kaynağı, bir kaynak grubundan bir diğerine taşıyabilirsiniz. Daha fazla bilgi için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](resource-group-move-resources.md).

* Bir kaynak grubu, farklı bölgelerde bulunan kaynakları içerebilir.

* Bir kaynak grubu, yönetim eylemleri için erişim denetimini incelemek üzere kullanılabilir.

* Bir kaynak diğer kaynak gruplarındaki kaynaklarla etkileşim kurabilir. İki kaynak ilişkili olmasına karşın aynı yaşam döngüsünü paylaşmadığında (örneğin, bir veritabanına bağlanan web uygulamaları) bu etkileşim yaygın olarak görülür.

Bir kaynak grubu oluştururken bu kaynak grubu için bir konum belirtmeniz gerekir. "Bir kaynak grubu için neden konum gerekli olsun? Ayrıca kaynaklar kaynak grubundan farklı konumlarda olabiliyorsa kaynak grubu konumu neden önemli olsun?" diye soruyor olabilirsiniz Kaynak grubu, kaynaklarla ilgili meta verileri depolar. Kaynak grubu için bir konum belirttiğinizde, meta verilerin nerede depolandığını belirtirsiniz. Uyumluluk nedeniyle verilerinizin belirli bir bölgeye depolandığından emin olmanız gerekebilir.

Kaynak grubunun bölgesi geçici olarak kullanılamıyorsa, meta veriler kullanılamadığından kaynak grubundaki kaynakları güncelleştiremezsiniz. Diğer bölgelerdeki kaynaklar beklendiği gibi çalışmaya devam eder, ancak bunları güncelleştiremezsiniz. Güvenilir uygulamalar oluşturma hakkında daha fazla bilgi için bkz. [güvenilir Azure uygulamaları tasarlama](/azure/architecture/checklist/resiliency-per-service).

## <a name="resiliency-of-azure-resource-manager"></a>Azure Resource Manager esnekliği

Azure Resource Manager hizmeti dayanıklılık ve sürekli kullanılabilirlik için tasarlanmıştır. REST API Kaynak Yöneticisi ve denetim düzlemi işlemleri (management.azure.com 'e gönderilen istekler):

* Bölgeler arasında dağıtılır. Bazı hizmetler bölgesel olarak verilebilir.

* Birden çok Kullanılabilirlik Alanları sahip konumlarda Kullanılabilirlik Alanları (Ayrıca bölge) üzerinden dağıtılır.

* Tek bir mantıksal veri merkezine bağımlı değildir.

* Bakım etkinlikleri için hiçbir şekilde alınmadı.

Bu dayanıklılık, Kaynak Yöneticisi aracılığıyla istek alan hizmetler için geçerlidir. Örneğin, bu dayanıklılık avantajlarından yararlanın Key Vault.

## <a name="next-steps"></a>Sonraki adımlar

* Kaynak sağlayıcıları tarafından sunulan tüm işlemler için bkz. [Azure REST API 'leri](/rest/api/azure/).

* Kaynakları taşıma hakkında bilgi edinmek için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](resource-group-move-resources.md).

* Kaynakları etiketleme hakkında bilgi edinmek için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](resource-group-using-tags.md).

* Kaynakları kilitleme hakkında bilgi edinmek için bkz. [beklenmeyen değişiklikleri engellemek için kaynakları kilitleme](resource-group-lock-resources.md).

* Dağıtımlar için şablon oluşturma hakkında bilgi için bkz. [şablon dağıtımı genel bakış](template-deployment-overview.md).
