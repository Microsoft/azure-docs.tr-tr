---
title: Azure Maliyet Yönetimi ile bulut yatırımınızı iyileştirme
description: Bu makale bulut yatırımlarınızdan en iyi şekilde yararlanmanıza, maliyetlerinizi azaltmanıza ve paranızın nereye harcandığını değerlendirmenize yardımcı olur.
author: bandersmsft
ms.author: banders
ms.date: 04/02/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: e333b4aadce33e5303820951051ce83c7e65eaf9
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222393"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Azure Maliyet Yönetimi ile bulut yatırımınızı iyileştirme

Azure Maliyet Yönetimi, bulut yatırımınızdan en iyi şekilde faydalanmak için plan yapmanızı, analiz gerçekleştirmenizi ve harcamalarınızı azaltmanızı sağlayan araçlar sunar. Bu belge, maliyet yönetimi konusunda yönteme dayalı bir yaklaşım sunar ve kuruluşunuzun maliyet konusunda karşılaştığı zorlukların üstesinden gelmek için kullanabileceğiniz araçlar hakkında bilgi verir. Azure, bulut çözümü oluşturmayı ve dağıtmayı kolaylaştırır. Ancak kuruluşunuzun maliyetlerini en aza indirmek için bu çözümlerin iyileştirilmesi önemlidir. Bu belgedeki ilkeleri izleyerek ve sunulan araçları kullanarak kuruluşunuzun başarıya hazırlıklı olmasını sağlayabilirsiniz.

## <a name="methodology"></a>Yöntem

Maliyet yönetimi, kurumsal düzeydeki bir sorundur ve bulut kaynakları için para harcamadan önce başlanan sürekli bir uygulama haline gelmelidir. Maliyet yönetimini başarıyla uygulamak ve maliyetleri iyileştirmek için şirketinizin şunları yapması gerekir:

- Başarı için uygun araçlarla hazırlık yapma
- Maliyetlerden sorumlu olma
- Harcamayı iyileştirmek için uygun işlemleri gerçekleştirme

Kuruluşunuzun maliyetleri başarılı bir şekilde yönetebilmesi için aşağıda belirtilen üç önemli grubun ortaklaşa bir çalışma yürütmesi gerekir.

- **Finans**: Bulut harcamaları tahminlerine göre kuruluş genelindeki bütçe isteklerini onaylamaktan sorumlu olan kişilerdir. Bu kişiler, ilgili faturaların ödemesini yapar ve sorumlu kişileri takip etmek için maliyetleri farklı ekiplere atar.
- **Yöneticiler**: Kuruluşta iş konusunda karar alma yetkisine sahip olan ve en iyi harcama sonuçların elde etmek için bulut harcamaları konusunda bilgi sahibi olması gereken kişilerdir.
- **Uygulama ekipleri**: Bulut kaynaklarını her gün yöneten ve kuruluşun ihtiyaçlarına uygun hizmetler geliştiren mühendislerdir. Bu ekipler, belirlenen bütçe dahilinde en yüksek değeri sunmak için esnekliğe ihtiyaç duyar.

### <a name="key-principles"></a>Temel ilkeler

Kuruluşunuzun bulut maliyeti yönetiminde başarılı olmasını sağlamak için aşağıdaki ilkeleri kullanın.

Daha fazla bilgi edinmek için [Maliyet Yönetimi başarı için planlama](https://www.youtube.com/watch?v=dVuwITdSAZ4) videosunu izleyin. Diğer videoları izlemek için [Maliyet Yönetimi YouTube kanalını](https://www.youtube.com/c/AzureCostManagement) ziyaret edin.

>[!VIDEO https://www.youtube.com/embed/dVuwITdSAZ4]

#### <a name="planning"></a>Planlama

Kapsamlı ve önceden planlama, bulut kullanımını işletmenizin ihtiyaçlarına göre şekillendirmenizi sağlar. Kendinize şu soruları sorun:

- Çözmem gereken iş sorunu nedir?
- Kaynaklarım için hangi kullanım düzenlerine ihtiyaç duyuyorum?

Bu sorulara verdiğiniz yanıtlar, size uygun teklifleri seçmenize yardımcı olacaktır. Bu yanıtlar kullanılacak altyapıyı ve bunun Azure verimliliğini en üst düzeye çıkarmak için nasıl kullanılması gerektiğini belirler.

#### <a name="visibility"></a>Görünürlük

Maliyet Yönetimi, doğru şekilde yapılandırıldığında insanları sorumlu oldukları Azure maliyetleri veya harcadıkları para konusunda bilgilendirmenize yardımcı olur. Azure, paranızı *nereye* harcadığınızı göstermek üzere tasarlanmış hizmetlere sahiptir. Bu araçlardan faydalanabilirsiniz. Bu araçlar az kullanılan kaynakları bulmanıza, gereksiz kaynakları kaldırmanıza ve tasarruf fırsatlarını en üst düzeye çıkarmanıza yardımcı olabilir.

#### <a name="accountability"></a>Sorumluluk

Sorumlu insanların ekiplerinin yaptığı harcamanın farkına varması için maliyetleri kuruluşunuz içinde dağıtın. Kuruluşunuzun Azure harcamalarını tam olarak anlamak için kaynaklarınızı maliyet dağıtımı içgörülerini en üst düzeye çıkaracak şekilde düzenlemeniz gerekir. İyi bir organizasyon, kuruluşunuzda yapılan harcamaları verimli hale getirmek için maliyetleri yönetip azaltmanıza ve kişileri sorumlu tutmanıza yardımcı olur.

#### <a name="optimization"></a>İyileştirme

Harcamalarınızı azaltmak için harekete geçin. Planlama ve maliyet görünürlüğünü artırma sayesinde elde edilen bulguları en iyi şekilde değerlendirin. Satın alma ve lisanslama iyileştirmelerinin yanı sıra bu belgenin ilerleyen bölümlerinde açıklanan altyapı dağıtım değişiklikleri konusunda harekete geçebilirsiniz.

#### <a name="iteration"></a>Yineleme

Kuruluşunuzdaki herkesin maliyet yönetimi yaşam döngüsüne dahil olması gerekir. Maliyetleri iyileştirmek için sürekli olarak etkileşimde kalmaları gerekir. Bu yinelemeli süreç konusunda istekli olun ve kuruluşunuzdaki sorumlu bulut idaresinin temel noktalarından biri haline getirin.

![Görünürlük, sorumluluk ve iyileştirme adımlarını gösteren temel ilkeler diyagramı](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Plan yaparken maliyetleri de göz önünde bulundurun

Bulut kaynaklarını dağıtmadan önce şu noktaları değerlendirin:

- İhtiyaçlarınıza en uygun Azure teklifi
- Kullanmayı planladığınız kaynaklar
- Bunların yaklaşık maliyeti

Azure, değerlendirme sürecinde size yardımcı olacak araçlar sunar. Bu araçlar, iş yüklerinizi etkinleştirmek için gerekli olan yatırım hakkında fikir verebilir. Ardından durumunuza en uygun yapılandırmayı seçebilirsiniz.

### <a name="azure-onboarding-options"></a>Azure ekleme seçenekleri

Maliyet Yönetimi deneyiminizi en üst düzeye çıkarmanın ilk adımı, sizin için en uygun olan Azure teklifini araştırmak ve belirlemektir. Azure'ı gelecekte nasıl kullanmayı planladığınızı düşünün. Ayrıca faturalandırma modelinizin nasıl yapılandırılmasını istediğinizi dikkate alın. Karar verirken aşağıdaki soruları dikkate alın:

- Azure'ı ne kadar süreyle kullanmayı planlıyorum? Yalnızca test mi ediyorum veya uzun vadeli bir altyapı oluşturmayı planlıyor muyum?
- Azure için nasıl ödeme yapmak istiyorum? İndirimli fiyatlarla peşin mi ödemeliyim yoksa ay sonunda mı fatura almalıyım?

Farklı seçenekler hakkında daha fazla bilgi edinmek için bkz. [Azure'ı satın alma](https://azure.microsoft.com/pricing/purchase-options/). En yaygın faturalandırma modellerinin bazıları aşağıda tanımlanmıştır.

#### <a name="free"></a>[Ücretsiz](https://azure.microsoft.com/free/)

- Popüler ücretsiz hizmetleri 12 ay boyunca kullanma
- Hizmetleri 30 gün boyunca araştırmak için faturalandırma para biriminizde kredi 200 ABD Doları
- 25'ten fazla hizmet her zaman ücretsizdir

#### <a name="pay-as-you-go"></a>[Kullandıkça öde](https://azure.microsoft.com/offers/ms-azr-0003p)

- Minimum tutar veya taahhüt yoktur
- Rekabetçi fiyatlar
- Yalnızca kullandığınız kadar ödeyin
- İstediğiniz zaman iptal edin

#### <a name="enterprise-agreement"></a>[Kurumsal Anlaşma](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Önceden yapılan Azure Ön Ödemesi (eski adıyla parasal taahhüt) seçenekleri
- Düşük Azure fiyatlarına erişim

#### <a name="azure-in-csp"></a>[CSP'de Azure](https://azure.microsoft.com/offers/ms-azr-0145p/)

- CSP iş ortakları müşterilerinin ihtiyaçlarıyla ilgili olarak ilk iletişim noktası ve müşteri ilişkilerinin merkezidir
- CSP iş ortakları yeni müşteriler sağlar, abonelik siparişleri verir, abonelikleri yönetir ve müşterileri adına yönetici görevlerini gerçekleştirir
- CSP iş ortakları hizmetleri benzersiz çözümlerde paketler veya fiyatlandırmayı, koşulları ve faturalamayı denetleyerek Azure’ın yeniden satışını yapar

## <a name="estimate-the-cost-of-your-solution"></a>Çözümünüzün maliyetini tahmin etme

Altyapıyı dağıtmadan önce çözümünüzün ne kadar tutacağını değerlendirin. Değerlendirme, kuruluşunuzun iş yükü için bir bütçe oluşturmasına yardımcı olur. Daha sonra zaman içinde bütçe raporunu kullanarak başlangıçtaki tahmininizin geçerli olup olmadığını belirleyebilirsiniz. Bunu daha sonra dağıtılan çözümünüzün gerçek maliyetiyle de karşılaştırabilirsiniz.

### <a name="azure-pricing-calculator"></a>Azure fiyatlandırma hesaplayıcısı

Azure fiyatlandırma hesaplayıcısı, farklı Azure hizmetlerini karıştırıp eşleştirerek maliyet tahmini oluşturmanızı sağlar. Çözümünüzü Azure'da uygulamak için farklı yöntemlerden faydalanabilirsiniz ve bu da toplam harcama tutarınızı etkileyebilir. Bulut dağıtımınızın tüm altyapı ihtiyaçlarını en başından hesaba katmak, aracı en verimli şekilde kullanmanıza yardımcı olabilir. Yapacağınız Azure harcamalarıyla ilgili gerçeğe oldukça yakın bir tahmin elde etmenize yardımcı olabilir.

Daha fazla bilgi için bkz. [Azure fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Geçişi

Azure Geçişi, kuruluşunuzun şirket içindeki veri merkezlerinde çalışan iş yüklerini değerlendiren hizmettir. Bu hizmet, Azure'da oluşturacağınız çözümle ilgili ihtiyaçlarınız konusunda size yol gösterebilir. Azure Geçişi ilk olarak şirket içi ortamdaki makinelerinizi analiz eder ve geçişin mümkün olup olmadığını belirler. Daha sonra performansı en üst düzeye çıkarmak için Azure'daki VM boyutlarıyla ilgili önerilerde bulunur. Son olarak Azure tabanlı çözüm için bir tahmini maliyet çıkarır.

Daha fazla bilgi için bkz. [Azure Geçişi](../../migrate/migrate-services-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Maliyetlerinizi analiz etme ve yönetme

Kuruluşunuzun maliyetlerinin zaman içinde nasıl değiştiğini takip edin. Harcamalarınızı doğru şekilde anlamak ve yönetmek için aşağıdaki teknikleri kullanın.

### <a name="organize-resources-to-maximize-cost-insights-and-accountability"></a>Maliyet içgörülerini ve sorumluluğunu en üst düzeye çıkarmak için kaynakları düzenleme

Azure faturalamanız ve kaynak hiyerarşileriniz için iyi planlanmış bir organizasyonel yapı, bulut altyapınızı oluşturdukça maliyetlerinizi daha iyi bir kavrayıp denetlemenize yardımcı olur. Kullanılabilir organizasyonel araçları daha iyi anlayıp bunlardan nasıl yararlanacağınızı öğrenmek için [Varlık hiyerarşilerini ayarlama](https://www.youtube.com/watch?v=n3TLRaYJ1NY) adlı videoyu izleyin. Diğer videoları izlemek için [Maliyet Yönetimi YouTube kanalını](https://www.youtube.com/c/AzureCostManagement) ziyaret edin.

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

İhtiyaçlarınızı karşılayan bir hiyerarşiyi değerlendirip oluştururken kendinize aşağıdaki soruları sorun.

*Hangi faturalama hiyerarşisini kullanabilirim ve yararlanabileceğim farklı kapsamlar nelerdir?*

Azure teklifinizin türünü belirleyerek kuruluşunuzun faturalama düzenlemesini tanımlayın. Her Azure faturalama düzenlemesine ilişkin kullanılabilir kapsamlar [Kapsamları anlama ve bunlarla çalışma](understand-work-scopes.md) bölümünde belgelenmiştir.

*Birden çok ekibim varsa aboneliklerini ve kaynak gruplarını nasıl düzenlemem gerekir?*

Her ekip için bir abonelik veya kaynak grubu oluşturmak yaygın bir uygulamadır. Bu uygulamalar, maliyetleri ayırt etmenize ve ekipleri bunlardan sorumlu tutmanıza yardımcı olur. Ancak, maliyetler aboneliğe veya kaynak grubuna bağlıdır.

Birden çok aboneliği olan ekipleriniz varsa maliyetleri birlikte analiz etmek için abonelikleri yönetim gruplarına ayırmanız faydalı olabilir. Yönetim grupları, abonelikler ve kaynak gruplarının tümü Azure RBAC hiyerarşisinin birer parçasıdır. Ekiplerinizdeki denetime erişmek için bunları birlikte kullanın.

Kaynaklar özellikle birden çok ekip veya iş yükü tarafından paylaşıldığında birden çok kapsama yayılabilir. Kaynakları etiketlerle tanımlamanız yararlı olabilir. Etiketler, sonraki bölümde ayrıntılı bir şekilde ele alınacaktır.

*Geliştirme ve Üretim ortamlarına sahip miyim?*

Düşük fiyatlandırmadan yararlanmak için geliştirme ortamlarınızda Geliştirme ve Test abonelikleri oluşturmanız faydalı olabilir. İş yükleri birden çok ekibe veya Azure kapsamına yayıldıysa bunları tanımlamak için etiketleri kullanabilirsiniz.

### <a name="tag-shared-resources"></a>Paylaşılan kaynakları etiketleme

Etiketler, birden çok ekip ve Azure kapsamı genelinde yayılan maliyetleri anlamanın etkin bir yoludur. Örneğin, pek çok ekip tarafından kullanılan e-posta sunucusu gibi bir kaynağınız olabilir. E-posta sunucusu gibi paylaşılan bir kaynağı, paylaşılan kaynaklara ayrılmış bir aboneliğe veya mevcut bir aboneliğe ekleyebilirsiniz. Mevcut bir aboneliğe eklerseniz abonelik sahibi, bu kaynağın maliyetinin her ay kendi ekibine yansıtılmasını istemeyebilir. Bu örnek özelinde, kaynağı paylaşılan olarak tanımlamak için bir etiket kullanabilirsiniz.

Benzer şekilde, farklı ekiplerin sahip olduğu birden çok abonelik genelinde kaynaklar kullanan Test veya Üretim gibi web uygulamalarınız veya ortamlarınız da olabilir. İş yüklerinin tam maliyetini daha iyi kavramak amacıyla, bu iş yükleri tarafından kullanılan kaynakları etiketleyin. Etiketler düzgün bir şekilde uygulandığında, eğilimleri daha iyi anlamak için bunları maliyet analizinde filtre olarak uygulayabilirsiniz.

Kaynak etiketlemeye yönelik plan yaptıktan sonra, kaynaklar üzerinde etiketlemeyi zorunlu kılmak için bir Azure ilkesi yapılandırabilirsiniz. Ölçeklenebilir kaynak etiketlemesini zorunlu kılmanıza yardımcı olacak kullanılabilir araçları anlamak için [Azure Maliyet Yönetimi ile etiket ilkelerini gözden geçirme](https://www.youtube.com/watch?v=nHQYcYGKuyw) adlı videoyu izleyin. Diğer videoları izlemek için [Maliyet Yönetimi YouTube kanalını](https://www.youtube.com/c/AzureCostManagement) ziyaret edin.

>[!VIDEO https://www.youtube.com/embed/nHQYcYGKuyw]

### <a name="use-cost-analysis"></a>Maliyet analizini kullanma

Maliyet analizi, maliyetlerinizi standart kaynak özelliklerini kullanarak dilimlemenizi ve bu sayede kuruluş maliyetlerinizi analiz etmenizi sağlar. Analiz sırasında aşağıdaki yaygın soruları dikkate alın. Bu soruların düzenli olarak yanıtlanması, bilgi sahibi olmanıza ve maliyet odaklı kararlar vermenize yardımcı olacaktır.

- **Geçerli aya ait tahmini maliyet**: Bu ay ne kadar masrafım oldu? Bütçem dahilinde kalabilir miyim?
- **Anomalileri araştırma**: Maliyetlerin normal kullanım için uygun bir aralıkta olduğundan emin olmak için düzenli denetimler gerçekleştirin. Eğilimler nelerdir? Aykırı değerler var mı?
- **Fatura mutabakatı**: Son faturam bir önceki aya göre daha mı yüksek? Son bir ayda harcama alışkanlıklarım ne yönde değişti?
- **İç geri ödeme**: Ne kadar ödeme yaptığımı biliyorum ancak bu ücretleri kuruluşumun geneline nasıl dağıtabilirim?

Daha fazla bilgi için bkz. [Maliyet analizi](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Faturalandırma verilerini zamanlamaya göre dışarı aktarma

Faturalandırma verilerinizi pano veya finansal sistem gibi harici bir sisteme aktarmanız gerekiyor mu? Dosyaların Azure Depolama'ya otomatik olarak dışarı aktarılmasını sağlayın ve her ay onları indirmekle uğraşmayın. Faturalandırma verilerinizi eşitlemek için diğer sistemlerle otomatik tümleştirme de ayarlayabilirsiniz.

Faturalandırma verilerini dışa aktarma hakkında daha fazla bilgi için bkz. [Dışarı aktarılan verileri oluşturma ve yönetme](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Bütçe oluşturma

Harcama düzenlerinizi belirledikten ve analiz ettikten sonra kendiniz ve ekipleriniz için limitler belirlemeye başlamanız önemlidir. Azure bütçeleri, birçok farklı eşiğe ve uyarıya sahip maliyet veya kullanım tabanlı bütçeler belirleme esnekliği sunar. Bütçenizin nasıl harcandığını görmek ve gerektiğinde değişiklikler yapmak için oluşturduğunuz bütçeleri düzenli olarak gözden geçirdiğinizden emin olun. Azure bütçeleri ayrıca belirli bir bütçe eşiğine ulaşıldığında etkinleştirilecek otomatik tetikleyici yapılandırmanızı sağlar. Örneğin hizmetinizi VM'leri kapatacak şekilde yapılandırabilirsiniz. Alternatif olarak bütçe tetikleyicisi ile altyapınızı farklı bir fiyatlandırma katmanına taşıyabilirsiniz.

Daha fazla bilgi için bkz. [Azure Bütçeleri](tutorial-acm-create-budgets.md).

Bütçe tabanlı otomasyon hakkında daha fazla bilgi için bkz. [Bütçe Tabanlı Otomasyon](../manage/cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>İyileştirmek için harekete geçin
Harcamalarınızı iyileştirmek için aşağıdaki yöntemleri kullanın.

### <a name="cut-out-waste"></a>Boşta olanları kaldırın

Azure’da altyapınızı dağıttıktan sonra bu altyapının kullanıldığından emin olmanız önemlidir. Hemen tasarruf yapmaya başlamanın en kolay yolu, kaynaklarınızı gözden geçirmek ve kullanılmayan kaynakları kaldırmaktır. Bu şekilde kaynaklarınızın mümkün olduğunca verimli bir şekilde kullanılıp kullanılmadığını belirlemeniz gerekir.

#### <a name="azure-advisor"></a>Azure Advisor

Azure Danışmanı, CPU ve ağ kullanımı açısından düşük kullanıma sahip olan sanal makineleri tanımlama gibi işlemler gerçekleştiren bir hizmettir. Buradan alacağınız verilerle makineleri çalıştırmaya devam etmenin tahmini maliyetini dikkate alarak makineyi kapatabilir veya yeniden boyutlandırabilirsiniz. Danışman ayrıca ayrılmış örnek satın alma işlemleriyle ilgili önerilerde de bulunur. Öneriler, son 30 gün içindeki sanal makine kullanımınızı temel alır. Gerekli işlemler yapıldığında öneriler, harcamalarınızı azaltmanıza yardımcı olabilir.

Daha fazla bilgi için bkz. [Azure Danışmanı](../../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>VM'lerinizi doğru şekilde boyutlandırın

VM boyutları, genel Azure maliyetlerinizi önemli ölçüde etkiler. Azure'da gerekli olan VM sayısı, şu anda şirket içi veri merkezinizde dağıtılmış olan sayı olmayabilir. Çalıştırmayı planladığınız iş yükleri için doğru boyutu seçtiğinizden emin olun.

Daha fazla bilgi için bkz. [Azure IaaS: uygun boyutlar ve maliyetler](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/).

### <a name="use-purchase-discounts"></a>Satın alma indirimlerini kullanın

Azure, kuruluşunuzun tasarruf yapmasını sağlayabilecek birçok farklı indirime sahiptir.

#### <a name="azure-reservations"></a>Azure Rezervasyonlar

Azure Rezervasyonlar, bir yıllık veya üç yıllık sanal makine ya da SQL Veritabanı işlem kapasitesi için önceden ödeme yapmanıza olanak sağlar. Önceden ödeme yapmanız, kullandığınız kaynaklarda indirim almanızı sağlar. Azure rezervasyonları, bir yıllık veya üç yıllık ön ödeme taahhüdüyle kullandıkça ödeme bazındaki fiyatlarla sanal makinenizin veya SQL veritabanınızın işlem maliyetlerini yüzde 72’ye kadar büyük ölçüde azaltabilir. Rezervasyonlar bir faturalandırma indirimi sağlar ve sanal makinelerinizin veya SQL veritabanlarınızın çalışma zamanı durumunu etkilemez.

Daha fazla bilgi için bkz. [Azure Rezervasyonlar nedir?](../reservations/save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Azure Hibrit Avantajı’nı kullanın

Şirket içi dağıtımlarınızda zaten Windows Server veya SQL Server lisansınız varsa, Azure’da tasarruf sağlamak için Azure Hibrit Avantajı programını kullanabilirsiniz. Windows Server avantajı ile her lisans, işletim sistemi maliyetini karşılar (iki adede kadar sanal makinede) ve siz yalnızca temel işlem maliyetleri için ödeme yaparsınız. Sanal çekirdek SQL Veritabanı seçeneklerinde yüzde 55’e kadar tasarruf elde etmek için mevcut SQL Server lisanslarını kullanabilirsiniz. Seçenekler arasında, SQL Server Integration Services ve Azure Sanal Makinelerinde SQL Server yer alır.

Daha fazla bilgi için bkz. [Azure Hibrit Avantajı tasarruf hesaplayıcısı](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Diğer kaynaklar

Azure ayrıca daha düşük ücretler karşılığında Azure'daki fazla kapasiteden faydalanan hizmetler oluşturmanızı sağlayan bir özelliğe de sahiptir. Daha fazla bilgi için bkz. [Batch ile düşük öncelikli VM’ler kullanma](../../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Sonraki adımlar
- Azure Maliyet Yönetimi'ni kullanmaya yeni başladıysanız, Azure harcamalarınızı izleyip kontrol altına almanıza ve kaynak kullanımını iyileştirmenize nasıl yardımcı olabileceğini öğrenmek için [Azure Maliyet Yönetimi nedir?](../cost-management-billing-overview.md) sayfasını inceleyin.