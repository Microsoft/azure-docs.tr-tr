---
title: App Service planları
description: App Service planların Azure App Service nasıl çalıştığını, müşteriye nasıl faturalandırıldığını ve gereksinimlerinize göre nasıl ölçekleyeceğinizi öğrenin.
keywords: App Service, Azure App Service, ölçek, ölçeklenebilir, ölçeklenebilirlik, App Service planı, App Service maliyeti
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 6e5de3cdec7a9c503f4b7bf7056bd62f1ddf682d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594023"
---
# <a name="azure-app-service-plan-overview"></a>Azure App Service planına genel bakış

App Service (Web Apps, API Apps veya Mobile Apps), bir uygulama her zaman bir _App Service planında_ çalışır. Ayrıca, [Azure işlevleri](../azure-functions/dedicated-plan.md) bir _App Service planında_ çalıştırma seçeneğine de sahiptir. App Service planı, bir web uygulamasının birlikte çalıştırılacağı işlem kaynakları kümesini tanımlar. Bu işlem kaynakları geleneksel web barındırma içindeki [_sunucu grubuna_](https://wikipedia.org/wiki/Server_farm) benzerdir. Bir veya daha fazla uygulama aynı bilgi işlem kaynaklarında (veya aynı App Service planında) çalışacak şekilde yapılandırılabilir.

Belirli bir bölgede bir App Service planı oluşturduğunuzda (örneğin, Batı Avrupa), bu plan için o bölgedeki bir işlem kaynakları kümesi oluşturulur. Bu App Service planına yerleştirdiğiniz uygulamalar, App Service planınız tarafından tanımlandığı şekilde bu işlem kaynakları üzerinde çalışır. Her App Service planı şunları tanımlar:

- Bölge (Batı ABD, Doğu ABD vb.)
- VM örneklerinin sayısı
- VM örneklerinin boyutu (Küçük, Orta, Büyük)
- Fiyatlandırma Katmanı (ücretsiz, paylaşılan, temel, standart, Premium, PremiumV2, PremiumV3, yalıtılmış)

Bir App Service planının _fiyatlandırma katmanı_ , aldığınız App Service özelliklerini ve plan için ne kadar ücret ödersiniz belirler. Birkaç fiyatlandırma katmanı kategorisi vardır:

- **Paylaşılan işlem**: **ücretsiz** ve **paylaşılan** iki temel katman, diğer müşterilerin uygulamaları da dahil olmak üzere diğer App Service uygulamalarla aynı Azure VM üzerinde bir uygulamayı çalıştırır. Bu katmanlar, paylaşılan kaynaklar üzerinde çalışan her uygulamaya belirli CPU kotaları atar ve kaynaklar için ölçeklendirme yapılamaz.
- **Adanmış işlem**: **temel**, **standart**, **Premium**, **PremiumV2** ve **PremiumV3** katmanları, uygulamaları adanmış Azure VM 'lerinde çalıştırır. Yalnızca aynı App Service planındaki uygulamalar aynı işlem kaynaklarını kullanır. Katman yükseldikçe ölçeği genişletme için kullanabileceğiniz VM örneği sayısı da artar.
- **Yalıtılmış**: Bu katman adanmış Azure sanal ağlarında adanmış Azure VM 'leri çalıştırır. Uygulamalarınıza işlem yalıtımının en üstünde ağ yalıtımı sağlar. En fazla ölçek genişletme özellikleri sunan katmandır.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Her katman, App Service özelliklerinin belirli bir alt kümesini de sağlar. Bu özellikler özel etki alanları ve TLS/SSL sertifikaları, otomatik ölçeklendirme, dağıtım yuvaları, yedeklemeler, Traffic Manager tümleştirme ve daha fazlasını içerir. Katman arttıkça, daha fazla özellik mevcuttur. Her fiyatlandırma katmanında hangi özelliklerin desteklendiğini öğrenmek için [App Service plan ayrıntıları](https://azure.microsoft.com/pricing/details/app-service/plans/)' na bakın.

<a name="new-pricing-tier-premiumv3"></a>

> [!NOTE]
> Yeni **PremiumV3** fiyatlandırma katmanı, **Standart** katmana kıyasla daha hızlı işlemciler (sanal CPU başına en az 195 [acu](../virtual-machines/acu.md) ), SSD depolama ve dört bellekten çekirdek oranına sahip makineleri garanti eder. **PremiumV3** Ayrıca **Standart** katmanda bulunan tüm gelişmiş özellikleri sağlarken daha fazla örnek sayısı aracılığıyla daha yüksek ölçeklendirmeyi destekler. Mevcut **PremiumV2** katmanında bulunan tüm özellikler **PremiumV3**'e dahildir.
>
> Diğer ayrılmış katmanlara benzer şekilde, bu katmanda üç VM boyutu vardır:
>
> - Küçük (2 CPU çekirdeği, 8 GiB bellek) 
> - Orta (4 CPU çekirdeği, 16 GiB bellek) 
> - Büyük (8 CPU çekirdeği, 32 GiB bellek)  
>
> **PremiumV3** fiyatlandırma bilgileri için bkz. [App Service fiyatlandırması](https://azure.microsoft.com/pricing/details/app-service/).
>
> Yeni **PremiumV3** fiyatlandırma katmanını kullanmaya başlamak için bkz. [App Service Için PremiumV3 katmanını yapılandırma](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Uygulamam nasıl çalışır ve ölçeklendirebilir?

**Ücretsiz** ve **paylaşılan** katmanlarda bir uygulama, paylaşılan BIR sanal makine örneğindeki CPU dakikalarını alır ve ölçeklendiremez. Diğer katmanlarda, bir uygulama aşağıdaki gibi çalışır ve ölçeklenir.

App Service ' de bir uygulama oluşturduğunuzda, bu bir App Service planına konur. Uygulama çalıştığında, App Service planında yapılandırılan tüm sanal makine örneklerinde çalışır. Birden çok uygulama aynı App Service planındaysa, hepsi aynı sanal makine örneklerini paylaşır. Bir uygulama için birden çok dağıtım yuvadıysanız, tüm dağıtım yuvaları aynı sanal makine örneklerinde da çalışır. Tanılama günlüklerini etkinleştirir, yedeklemeler gerçekleştirebilir veya Web Işleri çalıştırırsanız, bu sanal makine örneklerinde CPU döngüleri ve bellek de kullanır.

Bu şekilde App Service planı, App Service uygulamaların ölçek birimidir. Plan beş VM örneği çalıştıracak şekilde yapılandırıldıysa, plandaki tüm uygulamalar beş örnek üzerinde çalıştırılır. Plan otomatik ölçeklendirme için yapılandırıldıysa, plandaki tüm uygulamalar otomatik ölçeklendirme ayarlarına göre birlikte ölçeklendirilir.

Bir uygulamanın ölçeklendirilmesi hakkında daha fazla bilgi için bkz. [Ölçek örneği sayısı el ile veya otomatik olarak](../azure-monitor/autoscale/autoscale-get-started.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>App Service planımın maliyeti ne kadardır?

Bu bölümde App Service uygulamalar nasıl faturalandırılır açıklanmaktadır. Bölgeye özgü ayrıntılı fiyatlandırma bilgileri için bkz. [App Service fiyatlandırması](https://azure.microsoft.com/pricing/details/app-service/).

**Ücretsiz** katman haricinde, bir App Service planı kullandığı işlem kaynakları üzerinde ücret taşır.

- **Paylaşılan** katmanda, her uygulama CPU dakikalık bir kota alır, bu nedenle _her uygulama_ CPU kotasına göre ücretlendirilir.
- Adanmış işlem katmanlarında (**temel**, **Standart**, **Premium**, **PremiumV2**, **PremiumV3**), App Service planı, uygulamaların ölçeklendiği sanal makine örneklerinin sayısını tanımlar, böylece App Service planındaki _her sanal makine örneği_ ücretlendirilir. Bu sanal makine örnekleri, üzerinde kaç uygulama çalıştığını dikkate almaksızın aynı şekilde ücretlendirilir. Beklenmeyen ücretlerden kaçınmak için bkz. [App Service planını Temizleme](app-service-plan-manage.md#delete).
- **Yalıtılmış** katmanda App Service ortamı, uygulamalarınızı çalıştıran yalıtılmış çalışanların sayısını tanımlar ve _her çalışan_ ücretlendirilir. Ayrıca, App Service Ortamı çalıştıran bir düz damga ücreti de vardır.

Kullanabileceğiniz App Service Özellikleri (özel etki alanları, TLS/SSL sertifikaları, dağıtım yuvaları, yedeklemeler vb.) kullanmak için ücretlendirilmezsiniz. Özel durumlar şunlardır:

- App Service etki alanları-bir Azure 'da satın alırken ve her yıl yeniledığınızda ödeyin.
- App Service sertifikaları-bir Azure 'da satın alırken ve her yıl yeniledığınızda ödeyin.
- IP tabanlı TLS bağlantıları-her IP tabanlı TLS bağlantısı için saatlik bir ücretlendirme vardır ancak bazı **Standart** katman veya YUKARıDAKI bir IP tabanlı TLS bağlantısı elde etmenizi sağlar. SNı tabanlı TLS bağlantıları ücretsizdir.

> [!NOTE]
> App Service başka bir Azure hizmeti ile tümleştirirseniz, bu diğer hizmetlerden gelen ücretleri dikkate almanız gerekebilir. Örneğin, uygulamanızı coğrafi olarak ölçeklendirmek için Azure Traffic Manager kullanıyorsanız, Azure Traffic Manager kullanımınıza göre de ücretlendirilecek. Azure 'da hizmetler arası maliyetlerinizi tahmin etmek için bkz. [Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/). 

Bulut harcamalarınızı iyileştirmek ve kaydetmek istiyor musunuz?

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Uygulamamın daha fazla özelliğe veya özelliğe ihtiyacı varsa ne olacak?

App Service planınızı dilediğiniz zaman büyütebilir veya küçültebilirsiniz. Planın fiyatlandırma katmanını değiştirmek kadar basittir. Başlangıçta düşük fiyatlandırma katmanı seçip daha fazla App Service özelliğine ihtiyaç duyduğunuzda ölçeklendirebilirsiniz.

Örneğin, Web uygulamanızı **ücretsiz** bir App Service planınızdan test etmeye başlayabilir ve hiçbir şey ödeyebilirsiniz. [Özel DNS adınızı](app-service-web-tutorial-custom-domain.md) Web uygulamasına eklemek istediğinizde, planınızı **paylaşılan** katmana ölçeklendirmeniz yeterlidir. Daha sonra, [BIR TLS bağlama oluşturmak](configure-ssl-bindings.md)istediğinizde, planınızı **temel** katmana ölçeklendirin. [Hazırlama ortamları](deploy-staging-slots.md)kullanmak Istediğinizde, **Standart** katmana kadar ölçeklendirin. Daha fazla çekirdeğe, belleğe veya depolamaya ihtiyacınız olduğunda, aynı katmanda daha büyük bir VM boyutuna kadar ölçeklendirin.

Aynı işlem tersine de geçerlidir. Daha yüksek bir katmanın özelliklerine veya özelliklerine artık ihtiyacınız olmadığını düşünüyorsanız, daha düşük bir katmana ölçeklendirebilirsiniz ve bu sayede paradan tasarruf edebilirsiniz.

App Service planını ölçeklendirme hakkında daha fazla bilgi için bkz. [Azure 'da bir uygulamayı ölçeklendirme](manage-scale-up.md).

Uygulamanız diğer uygulamalarla aynı App Service planalıyorsa, işlem kaynaklarını yalıtarak uygulamanın performansını geliştirmek isteyebilirsiniz. Uygulamayı ayrı bir App Service planına taşıyarak bunu yapabilirsiniz. Daha fazla bilgi için bkz. [bir uygulamayı başka bir App Service planına taşıma](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Bir uygulamayı yeni bir plana mi yoksa mevcut bir plana mi koymalıyım?

App Service planınız için ayrılan bilgi işlem kaynakları için ödeme yapınızdan (bkz. [App Service planımın maliyeti nedir?](#cost)), birden çok uygulamayı tek bir App Service plana yerleştirerek para tasarrufu sağlayabilirsiniz. Planda yükü işlemek için yeterli kaynak olduğu sürece mevcut bir plana uygulama eklemeye devam edebilirsiniz. Ancak, aynı App Service planı içindeki uygulamaların aynı işlem kaynaklarını paylaştığından emin olmak için aklınızda bulundurun. Yeni uygulamanın gerekli kaynaklara sahip olup olmadığını belirlemek için var olan App Service planının kapasitesini ve yeni uygulamanın beklenen yükünü anlamanız gerekir. Bir App Service planının aşırı yüklenmesi yeni ve var olan uygulamalarınızın kesinti yaşamasına neden olabilir.

Aşağıdaki durumlarda, uygulamanızı yeni bir App Service planında yalıtabilirsiniz:

- Uygulama yoğun kaynak yoğunluklu bir işlemdir.
- Uygulamayı, mevcut plandaki diğer uygulamalardan bağımsız olarak ölçeklendirmek istiyorsunuz.
- Uygulamanın farklı bir coğrafi bölgede kaynağı olması gerekir.

Bu şekilde, uygulamanız için yeni bir kaynak kümesi ayırabilir ve uygulamalarınızın daha fazla denetimini elde edebilirsiniz.

## <a name="manage-an-app-service-plan"></a>App Service planını yönetme

> [!div class="nextstepaction"]
> [App Service planını yönetme](app-service-plan-manage.md)