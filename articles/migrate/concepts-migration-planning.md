---
title: Azure geçişi ile geçiş planı oluşturma
description: Azure geçişi ile geçiş planı oluşturma hakkında rehberlik sağlar.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 1c2be47060004d464003c00cbbddb3b58a136e3c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871155"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Azure Geçişi ile geçiş planı oluşturma

Azure geçişi ile Azure 'a geçiş planınızı derlemek için bu makaleyi [izleyin.](migrate-services-overview.md) 

## <a name="define-cloud-migration-goals"></a>Bulut geçiş hedeflerini tanımlama

Başlamadan önce, buluta taşımak için [mosyon](/azure/cloud-adoption-framework/strategy/motivations) 'nizi anlamak ve değerlendirmek, başarılı bir iş sonucuna katkıda bulunabilir. [Bulut benimseme çerçevesinde](/azure/cloud-adoption-framework)açıklandığı gibi, birkaç tetikleyici ve sonuç vardır.   

**İş etkinliği** | **Geçiş sonucu**
--- | ---
Veri merkezi çıkışı | Maliyet 
Merger, alım veya divestiture | Satıcı/teknik karmaşıklığa karşı azaltma
Sermaye masraflarını azaltma | İç işlemleri iyileştirme
Görev açısından kritik teknolojiler için destek sonu | İş çevikliği artırma
Yasal uyumluluk değişikliklerine yanıt verme | Yeni teknik yetenekler için hazırlık
Yeni veri egemenlik gereksinimleri | Pazar taleplerini karşılamak için ölçekleme
Kesintilerde azaltma ve BT kararlılığı geliştirmeleri | Coğrafi talepleri karşılamak için ölçekleme

Mosyon 'inizi tanımlamak, stratejik geçiş hedeflerinizi sabitlemek için size yardımcı olur. Bir sonraki adım, iş yükleriniz için uyarlanmış bir geçiş yolu tanımlamak ve planlamak. [Azure geçişi: bulma ve değerlendirme](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) Aracı, şirket içi iş yüklerini değerlendirmenize yardımcı olur ve geçiş yapmanıza yardımcı olacak yönergeler ve araçlar sağlar.

## <a name="understand-your-digital-estate"></a>Dijital Emlak bilgilerinizi anlayın

Şirket içi altyapınızı, uygulamalarınızı ve bağımlılıklarını tanımlayarak başlayın. Bu, Azure 'a geçiş için iş yüklerini tanımlamanızı ve iyileştirilmiş maliyet projeksiyonlarını toplamanıza yardımcı olur. Bulma ve değerlendirme aracı, kullandığınız iş yüklerini, iş yükleri ve iş yükü iyileştirmesi arasındaki bağımlılıkları belirlemenize yardımcı olur.

### <a name="workloads-in-use"></a>Kullanımdaki iş yükleri

Azure geçişi, şirket içi VMware VM 'Leri, Hyper-V VM 'Leri, diğer sanallaştırılmış sunucular ve fiziksel sunucuları aracısız bulma işlemini gerçekleştirmek için basit bir Azure geçiş gereci kullanır. Sürekli bulma, sunucu yapılandırma bilgilerini ve performans meta verilerini ve uygulama verilerini toplar. Gereçlerin şirket içi sunuculardan nasıl topladığı aşağıda verilmiştir: 

- Sunucu, disk ve NIC meta verileri.

- Yüklü uygulamalar, roller ve Özellikler.

- CPU ve bellek kullanımı, disk ıOPS ve aktarım hızı dahil olmak üzere performans verileri.

Veriler toplandıktan sonra, uygulamalar bulmak ve sunucularınızda çalışan SQL Server örnekleri için uygulama envanteri listesini dışarı aktarabilirsiniz. SQL Server hazır olduğunu anlamak için Azure geçişi: veritabanı değerlendirmesi aracını kullanabilirsiniz.

 ![Portalda uygulama envanteri](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Uygulama envanteri dışarı aktarma](./media/concepts-migration-planning/application-inventory-export.png)

Bulma ve değerlendirme aracı ile keşfedilen verilerin yanı sıra, sunucunuzun ve veritabanınızın bir görünümünü oluşturmak ve sunucularınızın iş birimleri, uygulama sahipleri, coğrafi öğeler vb. arasında nasıl dağıtıldığını anlamak için yapılandırma yönetimi veritabanı (CMDB) verilerinizi kullanabilirsiniz. Bu, geçiş için hangi iş yüklerinin önceliklendirileceğine karar verir. 

### <a name="dependencies-between-workloads"></a>İş yükleri arasındaki bağımlılıklar

Sunucu bulma işleminden sonra, çapraz sunucu bağımlılıklarını görselleştirme ve belirlemek için [bağımlılıkları analiz](concepts-dependency-visualization.md)edebilir ve bağımlı sunucuları Azure 'a taşımaya yönelik iyileştirme stratejilerini yapabilirsiniz. Görselleştirme, belirli sunucuların kullanımda olup olmadığını veya geçiş yapmak yerine kullanımdan engellenmeyeceğini anlamaya yardımcı olur.  Bağımlılıkları analiz etmek, hiçbir şeyin geri döndürülmemesini ve geçiş sırasında beklenmedik kesintilerden ayrılmanıza yardımcı olur. Uygulamanızın envanterini ve bağımlılık analizinizi tamamladıktan sonra, yüksek güvenilirlikli sunucu grupları oluşturabilir ve bunları değerlendirmeye başlayabilirsiniz.

 ![Bağımlılık eşlemesi](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>İyileştirme ve boyutlandırma

Azure, zaman içinde bulut kapasitenizi yeniden boyutlandırmak için esneklik sağlar ve geçiş, sunucularınıza ayrılan CPU ve bellek kaynaklarını iyileştirebilmeniz için bir fırsat sağlar. Kimlik sağladığınız sunucularda değerlendirme oluşturmak, iş yükü performans geçmişinizi anlamanıza yardımcı olur. Bu, Azure VM SKU 'Larını ve Azure 'daki disk önerilerini doğru boyutlandırmak için önemlidir.

## <a name="assess-migration-readiness"></a>Geçiş hazırlığını değerlendir


### <a name="readinesssuitability-analysis"></a>Hazırlık/uygunluk Analizi

Değerlendirme raporunu dışa aktarabilir ve Azure hazırlığını anlamak için bu kategorilere filtre uygulayabilirsiniz:

- **Azure Için hazırlanın**: sunucular, hiçbir değişiklik yapılmadan Azure 'a kadar geçirilebilir. 
- **Azure Için koşullu** olarak kullanılabilir: sunucular Azure 'a geçirilebilir, ancak değerlendirmede belirtilen düzeltme yönergelerine uygun olarak küçük değişikliklere ihtiyaç duyar.
- **Azure için hazırlanma**: sunucular, olduğu gibi Azure 'a geçirilemez. Sorunların, geçişten önce düzeltme kılavuzlarına göre düzeltilmesi gerekir. 
- **Hazır olma bilinmiyor**: Azure geçişi, yetersiz meta veri nedeniyle sunucu hazırlığını belirleyemiyor.

Veritabanı değerlendirmelerini kullanarak, Azure SQL veritabanı 'na veya Azure SQL yönetilen örneklerine geçiş için SQL Server verilerinizin hazır olduğunu değerlendirebilirsiniz. Değerlendirme, SQL Server örneklerinizin her biri için geçiş hazırlık durumu yüzdesini gösterir. Bunlara ek olarak, her bir örnek için Azure 'da önerilen hedefi, olası geçiş engelleyicileri, önemli değişiklikler sayısını, Azure SQL VERITABANı veya Azure SQL VM için hazır olduğunu ve uyumluluk düzeyini görebilirsiniz. Geçiş engelleyicilerin etkisini ve bunları düzeltmeye yönelik önerileri anlamak için daha ayrıntılı bilgi alabilirsiniz.

 ![Veritabanı değerlendirmeleri](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Boyutlandırma önerileri

Bir sunucu Azure için hazırlanın olarak işaretlendikten sonra bulma ve değerlendirme, sunucularınız için Azure VM SKU 'SU ve disk türünü tanımlayan boyutlandırma önerilerini sağlar. Performans geçmişi (geçiş yaparken kaynakları iyileştirmek için) veya performans geçmişi olmadan şirket içi sunucu ayarlarına bağlı olarak boyutlandırma önerilerini alabilirsiniz. Veritabanı değerlendirmesinde, veritabanı SKU 'SU, fiyatlandırma katmanı ve işlem düzeyi için öneriler görebilirsiniz.  

### <a name="get-compute-costs"></a>İşlem maliyetlerini al

Azure geçişi değerlendirmelerinde performans tabanlı boyutlandırma seçeneği, VM 'Leri sağ boyutlandırmanıza yardımcı olur ve Azure 'da iş yüklerini iyileştirmek için en iyi uygulama olarak kullanılmalıdır. Doğru boyutlandırmanın yanı sıra, Azure maliyetlerini kaydetmeye yardımcı olacak diğer birkaç seçenek de mevcuttur: 

- **Ayrılmış örnekler**: [ayrılmış örneklerle (RI)](https://azure.microsoft.com/pricing/reserved-vm-instances/), [Kullandıkça Öde fiyatlandırmasıyla](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)karşılaştırıldığında maliyetleri önemli ölçüde azaltabilirsiniz.
- **Azure hibrit avantajı**: [Azure hibrit avantajı](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)Ile, etkin yazılım güvencesi veya Linux aboneliklerine sahip şirket Içi Windows Server lisanslarını Azure 'a getirebilir ve ayrılmış örnekler seçenekleriyle birleştirebilirsiniz.
- **Kurumsal Anlaşma**: Azure [Kurumsal sözleşmeleri (EA)](../cost-management-billing/manage/ea-portal-agreements.md) , Azure abonelikleri ve hizmetleri için tasarruf sunabilir.
- **Teklifler**: birden çok [Azure teklifi](https://azure.microsoft.com/support/legal/offer-details/)vardır. Örneğin, [Kullandıkça Öde geliştirme ve test](https://azure.microsoft.com/pricing/dev-test/)veya [Kurumsal Geliştirme ve test teklif](https://azure.microsoft.com/offers/ms-azr-0148p/), geliştirme ve test VM 'leri için daha düşük ücretler sağlamak üzere
- **VM çalışma süresi**: Azure VM 'lerinin çalıştığı gün başına aylık gün ve saat sayısını gözden geçirebilirsiniz. Sunucuları kullanımda olmadığında kapatmak, maliyetlerinizi azaltabilir (RIS için geçerli değildir).
- **Hedef bölge**: belirli bir bölgeye geçiş yapılıp yapılmayacağını anlamak için farklı bölgelerde değerlendirmeler oluşturabilirsiniz. 

### <a name="visualize-data"></a>Verileri görselleştirme

Portalda bulma ve değerlendirme raporlarını (Azure hazırlık bilgileri ve aylık maliyet dağılımı ile) görüntüleyebilirsiniz. Ayrıca, değerlendirmeyi dışarı aktarabilir ve ek görselleştirmelerle geçiş planınızı zenginleştirebilirsiniz. Farklı özellikler birleşimleriyle birden çok değerlendirme oluşturabilir ve işletmeniz için en iyi şekilde çalışan özellikler kümesini seçebilirsiniz.  

 ![Değerlendirmelere genel bakış](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>Boşlukları/engelleyicileri değerlendir

Geçirmek istediğiniz uygulamaları ve iş yüklerini saptayıp, bunlar için kapalı kalma süresi kısıtlamalarını belirlemek ve uygulamalarınız ile temel alınan altyapı arasında işlem bağımlılıklarını aramak için. Bu analiz, kurtarma süresi hedefini (RTO) karşılayan geçişleri planlamanızı ve sıfır veri kaybını en aza düşürmenize yardımcı olur. Geçirmeden önce, sunucu/SQL veritabanı geçişini engelleyebilen uyumluluk sorunlarını veya desteklenmeyen özellikleri incelemenizi ve azaltmanız önerilir. Azure geçişi bulma ve değerlendirme raporu ve Azure geçişi veritabanı değerlendirmesi, bu konuda yardımcı olabilir. 

### <a name="prioritize-workloads"></a>İş yüklerini önceliklendir

Stoğunuzun bilgilerini topladıktan sonra, önce hangi uygulama ve iş yüklerinin geçirilecek olduğunu belirleyebilirsiniz. Uygulamaları sistematik ve denetlenebilir bir şekilde geçirmek için bir "uygulama ve öğrenme" yaklaşımı geliştirin, böylece tam ölçekli bir geçişe başlamadan önce herhangi bir kusuru dışarı aktarabilirsiniz.

Geçiş sırasının önceliklerini belirlemek için karmaşıklık, zaman geçişi, iş aciliyet, üretim/üretim dışı hususlar, uyumluluk, güvenlik gereksinimleri, uygulama bilgileri vb. gibi stratejik faktörleri kullanabilirsiniz. 

Birkaç öneri:

- **Hızlı WINS önceliklerini belirleme**: tam olarak kullanıma yönelik sunucular ve veritabanları da dahil olmak üzere düşük asılı olan meyve 'yi belirlemek için değerlendirme raporlarını kullanın ve Azure 'a geçiş için en az çaba gerektirir. Tablo, bunu yapmak için birkaç yol özetler.

    **Durum** | **Eylem**
    --- | ---
    **Azure 'a hazırlık VM 'Ler** | Değerlendirme raporunu dışarı aktarın ve *Azure Için hazırlık* durumuna sahip tüm sunucuları filtreleyin. Bu, [Azure geçişi: sunucu geçiş](migrate-services-overview.md#azure-migrate-server-migration-tool) Aracı ' nı kullanarak, çalıştırdığınız ve Azure 'a kaydırma yaptığınız ilk sunucu grubu olabilir.
    **Destek sonu işletim sistemleri** | Değerlendirme raporunu dışarı aktarın ve Windows Server 2008 R2/Windows Server 2008 çalıştıran tüm sunucuları filtreleyin. Bu işletim sistemleri destek sonunda yer almaktadır ve Azure 'a geçirdiğinizde yalnızca Azure, üç yıllık güvenlik güncelleştirmesi sağlar. Azure Hibrit Avantajı birleştirir ve RIS kullanıyorsanız, tasarruflar çok daha yüksek olabilir.
    **SQL Server geçiş** | Azure geçişi: veritabanı geçiş aracını kullanarak Azure SQL veritabanı için kullanıma yönelik veritabanlarını geçirmek üzere veritabanı değerlendirmesi önerilerini kullanın. Azure geçişi: sunucu geçiş aracını kullanarak Azure SQL VM için hazırlanıyor veritabanlarını geçirin.
    **Destek sonu yazılımı** | Uygulama envanterinizi dışarı aktarın ve destek sonuna ulaşmakta olabilecek tüm yazılım/uzantılara filtre uygulayın. Bu uygulamaların geçiş için önceliğini belirleyin.
    **Sağlanan sunucular** | Değerlendirme raporunu dışarı aktarın ve düşük CPU kullanımı (%) olan sunucular için filtre uygulayın ve bellek kullanımı (%).  Doğru boyutlu bir Azure VM 'ye geçiş yapın ve az kullanılan kaynaklar için maliyet tasarrufu yapın.
    **Aşırı sağlanan sunucular** | Yüksek CPU kullanımına sahip sunucular için değerlendirme raporu ve filtre 'yi dışarı aktarma (%) ve bellek kullanımı (%).  Kapasite kısıtlamalarını çözün, daha fazla sunucunun kesilmesini önleyin ve bu sunucuları Azure 'a geçirerek performansı artırın. Azure 'da, talebi karşılamak için otomatik ölçeklendirme özelliklerini kullanın.<br/><br/> Depolama kısıtlamalarını araştırmak için değerlendirme raporlarını çözümleyin. Disk ıOPS ve aktarım hızını ve önerilen disk türünü analiz edin.

- **Küçük bir başlangıç yapın ve ardından büyük** risk ve karmaşıklık sunan uygulamaları ve iş yüklerini taşıyarak, geçiş stratejinizde güvenle geliştirin. Pilot geçişleri için aday olabilecek geliştirme ve test iş yüklerini bulmak ve geçirmek için Azure geçişi değerlendirme önerilerini CMDB deponuzla birlikte çözümleyin. Pilot geçişlerinden geri bildirim ve dersleri, üretim iş yüklerini geçirmeye başladığınızda yararlı olabilir.  
- **Uyumlu**: Azure, sektördeki en büyük uyumluluk portföyünü ve tekliflerin kapsamını içerir. Geçiş önceliklerini belirlemek için uyumluluk gereksinimleri ' ni kullanarak, uygulamalar ve iş yükleri ulusal, bölgesel ve sektöre özgü standartlarınız ve yasaları ile uyumludur. Bu özellikle, iş açısından kritik işlemle ilgilenen, hassas bilgileri tutan veya çok fazla düzenlenen sektörde bulunan kuruluşlar için geçerlidir. Bu tür kuruluşlar, standartlar ve düzenlemeler ABO ve sık değişebilir, ile devam etmek zor olur.  

## <a name="finalize-the-migration-plan"></a>Geçiş planını sonlandırma

Geçiş planınızı sonuçlandırmadan önce, diğer olası engelleyiciler aşağıdaki gibi göz önünde bulundurduğunuzdan ve bunları azaltmanıza emin olun: 

- **Ağ gereksinimleri**: ağ bant genişliği ve gecikme kısıtlamalarını değerlendirin, bu durum, beklenmedik gecikmelerin ve kesintilerin geçiş çoğaltma hızına neden olabilir.
- **Test/geçiş sonrası tyaks**: bir zaman arabelleğinin geçirilmiş uygulamalar için performans ve Kullanıcı kabul testi gerçekleştirmesini veya veritabanı bağlantı dizelerini güncelleştirme, Web sunucularını yapılandırma, kesme/ince ayar uygulama ve temizleme gibi uygulamaları yapılandırma/.
- **İzinler**: önerilen Azure izinlerini ve geçiş için gereken sunucu/veritabanı erişim rollerini ve izinlerini gözden geçirin.
- **Eğitim**: kuruluşunuzu dijital dönüşüm için hazırlayın. Sağlam bir eğitim altyapısı, başarılı bir kurumsal değişiklik için önemlidir. Azure temelleri, çözüm mimarileri ve güvenlik kursları dahil [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF)ücretsiz eğitime göz atın. Takımınızı [Azure sertifikalarını](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)keşfetmeye teşvik edin.  
- **Uygulama desteği**: ihtiyacınız varsa uygulamanız için destek alın. Birçok kuruluş, bulut geçişini desteklemeye yönelik yardım dışarıdan tercih eder. Kişiselleştirilmiş yardım ile hızlı ve sorunsuz bir şekilde Azure 'a geçmek için, bir [Azure Expert yönetilen hizmet sağlayıcısı](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)veya [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF)göz önünde bulundurun.  


Geçirmek istediğiniz uygulamalar, uygulama/veritabanı kullanılabilirliği, kapalı kalma süresi kısıtlamaları ve geçiş kilometre taşları hakkında ayrıntılı bilgi içeren etkili bir bulut geçiş planı oluşturun. Plan, veri kopyasının ne kadar süreceğine göz önünde bulundurur ve geçiş sonrası testler ve kesilen etkinlikler için gerçekçi bir arabellek dahil eder. 

Geçiş sonrası test planı, geçirilen uygulamaların beklendiği gibi çalışmasını ve tüm veritabanı nesnelerinin ve veri ilişkilerinin buluta başarıyla aktarılmasını sağlamak için işlevsel, tümleştirme, güvenlik ve performans testi ve kullanım durumları içermelidir.  

Geçiş yol haritası oluşturun ve uygulamalarınızı ve veritabanlarınızı en az sıfır kapalı kalma süresine geçirmek için bir bakım penceresi bildirin ve geçiş sırasında olası işletimsel ve iş etkisini sınırlayın.  

## <a name="migrate"></a>Geçiş

Tam ölçekli bir geçişe başlamadan önce Azure geçişi 'nde bir test geçişi çalıştırmanızı öneririz. Test geçişi, dahil edilen süreyi tahmin etmenize yardımcı olur ve geçiş planınızı ince ayar. Olası sorunları bulmaya ve bunları tam geçişten önce gidermeye yönelik bir fırsat sağlar.

Geçişe hazırsanız, sorunsuz ve tümleşik bir geçiş deneyimi için Azure geçişi: sunucu geçiş aracı 'nı ve Azure veri geçiş hizmeti 'ni (DMS) uçtan uca izlemeye kullanın.

- Sunucu geçiş aracı ile, şirket içi VM 'Leri ve sunucuları ya da diğer özel veya genel bulutta bulunan VM 'Leri (AWS, GCP dahil) sıfır kapalı kalma süresiyle geçirebilirsiniz.
- Azure DMS, birden çok veritabanı kaynağından Azure veri platformları arasında kesintisiz geçişler sağlamak için tasarlanan tam olarak yönetilen bir hizmet sağlar.  

## <a name="next-steps"></a>Sonraki adımlar

- Azure bulut benimseme çerçevesindeki [bulut geçiş yolculuğunu](/azure/architecture/cloud-adoption/getting-started/migrate)araştırın   .
- Azure geçişi hakkında [hızlı bir genel bakış](migrate-services-overview.md) elde edin ve Başlarken [videosunu](https://youtu.be/wFfq3YPxYHE)izleyin.
- [Azure VM](concepts-assessment-calculation.md)'lerine geçiş Için VM 'leri değerlendirme hakkında daha fazla bilgi edinin.
