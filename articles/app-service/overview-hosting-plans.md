---
title: App Service plana genel bakış-Azure | Microsoft Docs
description: Azure App Service iş için App Service planlarından ve yönetim deneyiminize nasıl yararlanabileceğinizi öğrenin.
keywords: App Service, Azure App Service, ölçek, ölçeklenebilir, ölçeklenebilirlik, App Service planı, App Service maliyeti
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 78440185b4a26bccc8ffb0258416a19aa929af6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470242"
---
# <a name="azure-app-service-plan-overview"></a>Azure App Service plana genel bakış

App Service'teki uygulamalar bir _App Service planında_ çalışır. App Service planı, bir web uygulamasının birlikte çalıştırılacağı işlem kaynakları kümesini tanımlar. Bu işlem kaynakları geleneksel web barındırma içindeki [_sunucu grubuna_](https://wikipedia.org/wiki/Server_farm) benzerdir. Bir veya daha fazla uygulama aynı bilgi işlem kaynaklarında (veya aynı App Service planında) çalışacak şekilde yapılandırılabilir.

Belirli bir bölgede bir App Service planı oluşturduğunuzda (örneğin, Batı Avrupa), bu plan için o bölgedeki bir işlem kaynakları kümesi oluşturulur. Bu App Service planına yerleştirdiğiniz uygulamalar, App Service planınız tarafından tanımlandığı şekilde bu işlem kaynakları üzerinde çalışır. Her bir App Service planı şunları tanımlar:

- Bölge (Batı ABD, Doğu ABD, vb.)
- Sanal makine örneği sayısı
- VM örneklerinin boyutu (küçük, orta, büyük)
- Fiyatlandırma Katmanı (ücretsiz, paylaşılan, temel, standart, Premium, PremiumV2, yalıtılmış)

Bir App Service planının _fiyatlandırma katmanı_ , aldığınız App Service özelliklerini ve plan için ne kadar ücret ödersiniz belirler. Fiyatlandırma katmanlarının birkaç kategorisi vardır:

- **Paylaşılan işlem**: **ücretsiz** ve **paylaşılan**iki temel katman, diğer müşterilerin uygulamaları da dahil olmak üzere diğer App Service uygulamalarla aynı Azure VM üzerinde bir uygulamayı çalıştırır. Bu katmanlar, paylaşılan kaynaklarda çalışan her bir uygulamaya CPU kotaları ayırır ve kaynaklar ölçeklenmez.
- **Adanmış işlem**: **temel**, **Standart**, **Premium**ve **PremiumV2** katmanları, uygulamaları adanmış Azure VM 'lerinde çalıştırır. Yalnızca aynı App Service planındaki uygulamalar aynı işlem kaynaklarını paylaşır. Katman arttıkça, daha fazla sanal makine örneği, ölçek genişletme için kullanılabilir.
- **Yalıtılmış**: Bu katman adanmış Azure sanal ağlarında adanmış Azure VM 'leri çalıştırır. Uygulamalarınıza işlem yalıtımının en üstünde ağ yalıtımı sağlar. En büyük genişleme özelliklerini sağlar.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Her katman, App Service özelliklerinin belirli bir alt kümesini de sağlar. Bu özellikler özel etki alanları ve SSL sertifikaları, otomatik ölçeklendirme, dağıtım yuvaları, yedeklemeler, Traffic Manager tümleştirme ve daha fazlasını içerir. Katman arttıkça, daha fazla özellik mevcuttur. Her fiyatlandırma katmanında hangi özelliklerin desteklendiğini öğrenmek için [App Service plan ayrıntıları](https://azure.microsoft.com/pricing/details/app-service/plans/)' na bakın.

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Yeni **PremiumV2** fiyatlandırma katmanı, **Standart** katmana kıyasla daha hızlı işlemcilere, SSD depolamaya ve çift bellekten çekirdek oranına sahip [Dv2 serisi VM 'ler](../virtual-machines/windows/sizes-general.md#dv2-series) sağlar. **PremiumV2** Ayrıca, Standart planda bulunan tüm gelişmiş özellikleri sağlarken daha fazla örnek sayısı aracılığıyla daha fazla ölçeklendirmeyi destekler. Mevcut **Premium** katmanda bulunan tüm özellikler **PremiumV2**' ye dahildir.
>
> Diğer ayrılmış katmanlara benzer şekilde, bu katmanda üç VM boyutu vardır:
>
> - Küçük (bir CPU çekirdeği, 3,5 GiB bellek) 
> - Orta (iki CPU çekirdeği, 7 GiB bellek) 
> - Büyük (dört CPU çekirdeği, 14 GiB bellek)  
>
> **PremiumV2** fiyatlandırma bilgileri için bkz. [App Service fiyatlandırması](https://azure.microsoft.com/pricing/details/app-service/).
>
> Yeni **PremiumV2** fiyatlandırma katmanını kullanmaya başlamak için bkz. [App Service Için PremiumV2 katmanını yapılandırma](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Uygulamam nasıl çalışır ve ölçeklendirebilir?

**Ücretsiz** ve **paylaşılan** katmanlarda bir uygulama, paylaşılan BIR sanal makine örneğindeki CPU dakikalarını alır ve ölçeklendiremez. Diğer katmanlarda, bir uygulama aşağıdaki gibi çalışır ve ölçeklenir.

App Service ' de bir uygulama oluşturduğunuzda, bu bir App Service planına konur. Uygulama çalıştığında, App Service planında yapılandırılan tüm sanal makine örneklerinde çalışır. Birden çok uygulama aynı App Service planındaysa, hepsi aynı sanal makine örneklerini paylaşır. Bir uygulama için birden çok dağıtım yuvadıysanız, tüm dağıtım yuvaları aynı sanal makine örneklerinde da çalışır. Tanılama günlüklerini etkinleştirir, yedeklemeler gerçekleştirebilir veya Web Işleri çalıştırırsanız, bu sanal makine örneklerinde CPU döngüleri ve bellek de kullanır.

Bu şekilde App Service planı, App Service uygulamaların ölçek birimidir. Plan beş VM örneği çalıştıracak şekilde yapılandırıldıysa, plandaki tüm uygulamalar beş örnek üzerinde çalıştırılır. Plan otomatik ölçeklendirme için yapılandırıldıysa, plandaki tüm uygulamalar otomatik ölçeklendirme ayarlarına göre birlikte ölçeklendirilir.

Bir uygulamanın ölçeklendirilmesi hakkında daha fazla bilgi için bkz. [Ölçek örneği sayısı el ile veya otomatik olarak](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>App Service planımın maliyeti ne kadar?

Bu bölümde App Service uygulamalar nasıl faturalandırılır açıklanmaktadır. Bölgeye özgü ayrıntılı fiyatlandırma bilgileri için bkz. [App Service fiyatlandırması](https://azure.microsoft.com/pricing/details/app-service/).

**Ücretsiz** katman haricinde, bir App Service planı kullandığı işlem kaynakları üzerinde saatlik ücret taşır.

- **Paylaşılan** katmanda, her uygulama bir CPU dakikası kotası alır, bu nedenle _her uygulama_ CPU kotası için saatlik olarak ücretlendirilir.
- Adanmış işlem katmanlarında (**temel**, **Standart**, **Premium**, **PremiumV2**) App Service planı, uygulamaların ölçeklendiği sanal makine örneklerinin sayısını tanımlar, böylece App Service planındaki _her sanal makine örneği_ saatlik ücretlendirilir. Bu sanal makine örnekleri, üzerinde kaç uygulama çalıştığını dikkate almaksızın aynı şekilde ücretlendirilir. Beklenmeyen ücretlerden kaçınmak için bkz. [App Service planını Temizleme](app-service-plan-manage.md#delete).
- **Yalıtılmış** katmanda App Service ortamı, uygulamalarınızı çalıştıran yalıtılmış çalışanların sayısını tanımlar ve _her çalışan_ saatlik olarak ücretlendirilir. Ayrıca, App Service Ortamı çalıştırmak için saatlik temel bir ücret de vardır.

Kullanabileceğiniz App Service Özellikleri (özel etki alanları, SSL sertifikaları, dağıtım yuvaları, yedeklemeler vb.) kullanmak için ücretlendirilmezsiniz. Özel durumlar şunlardır:

- App Service etki alanları-bir Azure 'da satın alırken ve her yıl yeniledığınızda ödeyin.
- App Service sertifikaları-bir Azure 'da satın alırken ve her yıl yeniledığınızda ödeyin.
- IP tabanlı SSL bağlantıları-her IP tabanlı SSL bağlantısı için saatlik ücret verilir, ancak bazı **Standart** katman veya YUKARıDAKI bir IP tabanlı SSL bağlantısı size ücretsiz olarak sunulur. SNı tabanlı SSL bağlantıları ücretsizdir.

> [!NOTE]
> App Service başka bir Azure hizmeti ile tümleştirirseniz, bu diğer hizmetlerden gelen ücretleri dikkate almanız gerekebilir. Örneğin, uygulamanızı coğrafi olarak ölçeklendirmek için Azure Traffic Manager kullanıyorsanız, Azure Traffic Manager kullanımınıza göre de ücretlendirilecek. Azure 'da hizmetler arası maliyetlerinizi tahmin etmek için bkz. [Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Uygulamamın daha fazla özelliğe veya özelliğe ihtiyacı varsa ne olacak?

App Service planınız dilediğiniz zaman yukarı ve aşağı ölçeklendirilebilir. Planın fiyatlandırma katmanını değiştirmek kadar basittir. İlk başta daha düşük bir fiyatlandırma katmanı seçebilir ve daha sonra daha fazla App Service özelliğine ihtiyacınız olduğunda ölçeği daha sonra değiştirebilirsiniz.

Örneğin, Web uygulamanızı **ücretsiz** bir App Service planınızdan test etmeye başlayabilir ve hiçbir şey ödeyebilirsiniz. [Özel DNS adınızı](app-service-web-tutorial-custom-domain.md) Web uygulamasına eklemek istediğinizde, planınızı **paylaşılan** katmana ölçeklendirmeniz yeterlidir. Daha sonra, [BIR SSL bağlaması oluşturmak](configure-ssl-bindings.md)istediğinizde, planınızı **temel** katmana ölçeklendirin. [Hazırlama ortamları](deploy-staging-slots.md)kullanmak Istediğinizde, **Standart** katmana kadar ölçeklendirin. Daha fazla çekirdeğe, belleğe veya depolamaya ihtiyacınız olduğunda, aynı katmanda daha büyük bir VM boyutuna kadar ölçeklendirin.

Aynı işlem tersine de geçerlidir. Daha yüksek bir katmanın özelliklerine veya özelliklerine artık ihtiyacınız olmadığını düşünüyorsanız, daha düşük bir katmana ölçeklendirebilirsiniz ve bu sayede paradan tasarruf edebilirsiniz.

App Service planını ölçeklendirme hakkında daha fazla bilgi için bkz. [Azure 'da bir uygulamayı ölçeklendirme](manage-scale-up.md).

Uygulamanız diğer uygulamalarla aynı App Service planalıyorsa, işlem kaynaklarını yalıtarak uygulamanın performansını geliştirmek isteyebilirsiniz. Uygulamayı ayrı bir App Service planına taşıyarak bunu yapabilirsiniz. Daha fazla bilgi için bkz. [bir uygulamayı başka bir App Service planına taşıma](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Bir uygulamayı yeni bir plana mi yoksa mevcut bir plana mi koymalıyım?

App Service planınız için ayrılan bilgi işlem kaynakları için ödeme yapınızdan (bkz. [App Service planımın maliyeti nedir?](#cost)), birden çok uygulamayı tek bir App Service plana yerleştirerek para tasarrufu sağlayabilirsiniz. Planda yükü işlemek için yeterli kaynak olduğu sürece mevcut bir plana uygulama eklemeye devam edebilirsiniz. Ancak, aynı App Service planı içindeki uygulamaların aynı işlem kaynaklarını paylaştığından emin olmak için aklınızda bulundurun. Yeni uygulamanın gerekli kaynaklara sahip olup olmadığını öğrenmek için mevcut App Service planının kapasitesini ve yeni uygulama için beklenen yükü anlamanız gerekir. App Service planının aşırı yüklenmesi, yeni ve mevcut uygulamalarınız için kapalı kalma süresine neden olabilir.

Uygulamanızı şu durumlarda yeni bir App Service planına ayırın:

- Uygulama yoğun kaynak yoğunluklu bir işlemdir.
- Uygulamayı, mevcut plandaki diğer uygulamalardan bağımsız olarak ölçeklendirmek istiyorsunuz.
- Uygulamanın farklı bir coğrafi bölgede kaynağı olması gerekir.

Bu şekilde, uygulamanız için yeni bir kaynak kümesi ayırabilir ve uygulamalarınızın daha fazla denetimini elde edebilirsiniz.

## <a name="manage-an-app-service-plan"></a>App Service planını yönetme

> [!div class="nextstepaction"]
> [App Service planını yönetme](app-service-plan-manage.md)
