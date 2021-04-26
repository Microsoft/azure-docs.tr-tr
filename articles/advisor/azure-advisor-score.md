---
title: Danışman puanı kullanarak Azure iş yüklerini iyileştirin
description: Azure 'dan en iyi şekilde yararlanmak için Azure Advisor puanı 'nı kullanın.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 11b20bc3b4d604d3a7ff4608cd1c21f129c1cb6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630131"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Danışman puanı kullanarak Azure iş yüklerini iyileştirin

## <a name="introduction-to-advisor-score"></a>Danışman puanına giriş

Azure Advisor, iş yükleriniz için en iyi yöntem önerilerini sağlar. Bu öneriler kişiselleştirilmiş ve size yardımcı olmaya yönelik eylem yapılabilir:

* İş yüklerinizin duruşunu iyileştirin ve Azure dağıtımlarınızı iyileştirin.
* En iyi uygulamaları takip ederek en iyi sorunları önceden önleyin.
* Azure iş yüklerinizi [Microsoft Azure Well-Architected çerçevesinin](/azure/architecture/framework/)beş anına karşı değerlendirin.

Advisor 'ın temel bir özelliği olarak, danışman puanı bu hedeflere etkili ve verimli bir şekilde ulaşmanıza yardımcı olabilir.

Azure 'dan en iyi şekilde yararlanmak için iş yükünün en iyi duruma getirme yolculuğunda nerede olduğunu anlamanız çok önemlidir. Hangi hizmetlerin veya kaynakların iyi şekilde tüketildiğini ve hangilerinin olmadığını bilmeniz gerekir. Ayrıca, önerileri temel alarak eylemlerinizin önceliklerini en üst düzeye çıkarmak için nasıl önceliklendireceğimizi öğrenmek isteyeceksiniz.

Bu en iyi duruma getirme yolculuğunda yaptığınız ilerlemeyi izlemek ve raporlamak de önemlidir. Danışman puanı sayesinde, her şeyi yeni bir kumla deneyimle kolayca yapabilirsiniz.

Kişiselleştirilmiş bulut danışmanınızın yanı sıra Azure Advisor, sektördeki en iyi uygulamaları denetlemek için kullanım telemetrinizi ve kaynak yapılandırmanızı sürekli olarak değerlendirir. Danışman daha sonra bulgularını tek bir puanın içinde toplar. Bu puanın ardından, güvenilir, güvenli ve uygun maliyetli çözümler oluşturmak için gerekli adımları göz önünde bulundurularak bir bakışta bilgi verebilirsiniz.

Danışman puanı, beş kategori puanına daha fazla ayrılabilir olan genel bir puandan oluşur. Her bir danışman kategorisi için bir puan, Well-Architected çerçevesinin beş paragraf boyutunu temsil eder.

Günlük, haftalık ve aylık eğilimleri içeren genel puan ve kategori puanınızı görüntüleyerek zaman içinde yaptığınız ilerlemeyi izleyebilirsiniz. Hedefleri elde etmenize yardımcı olmak için kıyaslamalar da ayarlayabilirsiniz.

 ![Danışman puanı sayfasını gösteren ekran görüntüsü.](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Danışman Puanını yorumlama

Danışman, Genel Danışman puanınızı ve danışman kategorilerinin bir dökümünü yüzde cinsinden görüntüler. Herhangi bir kategoride %100 puanı, Advisor tarafından değerlendirilen tüm kaynaklarınızın, Advisor 'ın önerdiği en iyi uygulamaları izlediği anlamına gelir. Spektrumun diğer ucunda, %0 puanı, hiçbir kaynakınızın hiçbir kaynak tarafından, Advisor 'ın önerilerini izlemediği anlamına gelir. Bu puanı kullanarak, aşağıdaki akışı kolayca elde edebilirsiniz:

* **Danışman puanı** , iş yükünüzün veya aboneliklerinizin bir danışman puanına göre nasıl çalıştığını temel almanıza yardımcı olur. Eğilimin ne olduğunu anlamak için geçmiş eğilimlerini de görebilirsiniz.
* Her öneri için **kategoriye göre puan** , en iyi puanı elde etmek için bekleyen önerilerin ne olduğunu söyler. Bu değerler, hem önerinin ağırlığını hem de tahmin edilen uygulama kolaylığını yansıtır. Bu faktörler, zaman içinde en fazla değeri almanıza yardımcı olur. Önceliklendirmeleri de size yardımcı olur.
* Her öneri için **Kategori puanı etkisi** , her kategori için düzeltme eylemlerinizi önceliklendirmenize yardımcı olur.

Kategori puanınıza her bir önerinin katkısı, Azure portal **danışman puanı** sayfasında net bir şekilde gösterilir. **Potansiyel puan artışı** sütununda listelenen yüzde noktasıyla her bir kategori Puanını artırabilirsiniz. Bu değer, hem kategori içindeki önerinin ağırlığını hem de potansiyel olarak en kolay görevleri karşılamak için öngörülen uygulama kolaylığını yansıtır. En büyük puan etkileriyle önerilerin odaklanarak, en fazla ilerlemeyi zaman içinde yapmanıza yardımcı olur.

![Danışman Puanlama etkisini gösteren ekran görüntüsü.](./media/advisor-score-2.png)

Herhangi bir danışman önerisi tek bir kaynakla ilgili değilse, bu önerileri erteleyebilir veya kapatabilirsiniz. Bir sonraki yenileme ile puan hesaplamasından dışlanır. Danışman Ayrıca bu girişi modeli geliştirmek için ek geri bildirimde bulunmak için de kullanacaktır.

## <a name="how-is-an-advisor-score-calculated"></a>Danışman puanı nasıl hesaplanır?

Danışman kategori puanlarınızı ve genel danışman puanınızı yüzde olarak görüntüler. Herhangi bir kategoride %100 puanı, *Advisor tarafından* değerlendirilen tüm kaynaklarınızın, Advisor 'ın önerdiği en iyi uygulamaları izlediği anlamına gelir. Spektrumun diğer ucunda, %0 puanı, hiçbir kaynağınız, Advisor tarafından değerlendirilmeyen danışman önerilerini takip eder.

**Beş kategorinin her biri 100 olan en yüksek puanı.** Genel Danışman puanınız, uygulanabilir her bir kategori puanının toplamı olarak hesaplanır. Bu, tüm uygulanabilir kategorilerden en yüksek potansiyel puanın toplamına bölünür. Çoğu abonelik için, Advisor her kategoriden puan ekler ve 500 'e böler. Ancak *her kategori puanı yalnızca Advisor tarafından değerlendirilen kaynakları kullanıyorsanız hesaplanır*.

### <a name="advisor-score-calculation-example"></a>Danışman Puanlama Hesaplama örneği

* **Tek abonelik puanı:** Bu örnek, aboneliğiniz için tüm danışman kategori puanlarının basit ortalamasıdır. Danışman kategori puanları- **Cost** = 73, **güvenilirlik** = 85, **Işletimsel üstün** = 77 ve **performans** = 100 ise, danışman puanı (73 + 85 + 77 + 100)/(4x100) = 0,84% veya %84 olur.
* **Birden çok abonelik puanı:** Birden çok abonelik seçildiğinde, oluşturulan genel danışman puanları ağırlıklı Toplam Kategori puanlardır. Burada her danışman kategori puanı, abonelikler tarafından tüketilen kaynaklara göre toplanır. Danışman ağırlıklı toplanan kategori puanlarını aldıktan sonra, bir abonelik için genel puan sağlamak üzere danışman basit bir ortalama hesaplama yapar.

### <a name="scoring-methodology"></a>Puanlama yöntemi

Danışman puanı hesaplaması dört adımda özetlenebilir:

1. Danışman, *etkilenen kaynakların perakende maliyetini* hesaplar. Bu kaynaklar, Advisor 'da en az bir önerisine sahip olan aboneliklerinizde yer alan olanlardır.
1. Danışman, değerlendirilen *kaynakların perakende maliyetini* hesaplar. Bu kaynaklar, herhangi bir önerilere sahip olup olmadığına bakılmaksızın Advisor tarafından izlenen bir alanlardır.
1. Her öneri türü için, Advisor *sağlıklı kaynak oranını* hesaplar. Bu oran, etkilenen kaynakların perakende maliyetlerine göre değerlendirilen kaynakların perakende maliyetine göre hesaplanır.
1. Danışman her kategoride sağlıklı kaynak oranına üç ek ağırlık uygular:

   * Daha büyük etkilerle öneriler, daha düşük etkilerle önerilerden daha ağır.
   * Uzun süreli önerilere sahip kaynaklar puanınızdan daha fazla sayacaktır.
   * Danışman 'de ertelemeyi veya kapatabilmeniz gereken kaynaklar puanınızdan tamamen kaldırılır.

Danışman, her kategori için bir danışman puanı sağlamak üzere bu modeli bir danışman kategori düzeyinde uygular. **Güvenlik** , [güvenli bir puan](../security-center/secure-score-security-controls.md#introduction-to-secure-score) modeli kullanır. Basit bir ortalama, son danışman Puanını üretir.

## <a name="advisor-score-faqs"></a>Danışman puanı SSS

### <a name="how-often-is-my-score-refreshed"></a>Puanım ne sıklıkta yenilenir?

Puanınız günde en az bir kez yenilenir.

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>Neden bazı önerilerin kategori puanı etki sütununda boş "-" değeri var mı?

Danışman, Puanlama modelindeki son değişikliklerle yeni öneriler veya öneriler içermez. Kısa bir değerlendirme süresinden sonra, genellikle birkaç hafta sonra puanta yer alır.

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>Olası tasarrufları azalsa bile, bazı önerilerin maliyet puanı neden büyük ölçüde etkiler?

**Maliyet** puanınız, az kullanılan kaynakların olası tasarruflarını ve Bu önerilerin uygulanması için öngörülen kolaylığınızı yansıtır. Örneğin, olası tasarruflar daha düşük olsa bile, daha uzun bir süre boşta kalan etkilenen kaynaklara fazladan ağırlık uygulanır.

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>Neden bir veya daha fazla kategori veya abonelik için puanım yok?

Danışman yalnızca danışman tarafından değerlendirilen kaynakları olan kategoriler ve abonelikler için bir puan üretir.

### <a name="what-if-a-recommendation-isnt-relevant"></a>Öneri ilgili değilse ne olacak?

Danışmandan bir öneriyi atlarsanız puanınızın hesaplanmasıyla atlanacaktır. Eksik öneriler, danışman önerilerin kalitesini iyileştirmenize de yardımcı olur.

### <a name="why-did-my-score-change"></a>Puanım Neden değişsin?

Etkilenen kaynakları, Advisor 'ın önerdiği en iyi uygulamaları benimseerek düzeltdiğinizde, puan verebilirsiniz. Aboneliğiniz ya da aboneliğinizdeki izinlere sahip olan herkes yeni kaynaklar değiştirdiyseniz veya oluşturulmuşsa puanınızdaki dalgalanmaları de görebilirsiniz. Puanınız, tüm kaynakların toplam maliyetine göre maliyet etkilenen kaynakların oranını temel alır.

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Danışman bir abonelikteki kaynakların perakende maliyetini nasıl hesaplar?

Danışman, [Azure fiyatlandırması](https://azure.microsoft.com/pricing/)'nda yayınlanan Kullandıkça Öde tarifesine sahiptir. Bu ücretler, geçerli indirimleri yansıtmaz. Ücretler daha sonra kaynağın ayrıldığı son günde kullanım miktarı ile çarpılır. Kaynak maliyetinin hesaplanmasıyla indirimleri hariç tutma, Advisor 'ın indirimlerin farklılık gösterebileceği abonelikler, kiracılar ve kayıtlar arasında karşılaştırılabilir olmasını sağlar.

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>Puanın puanlarını almak için Advisor 'daki önerileri görüntülemesi gerekir mi?

Hayır. Puanınız, bu en iyi yöntemleri etkin bir şekilde benimseseniz ve danışman içinde önerileriniz hiçbir şekilde görüntüleyemeseniz bile, Advisor 'ın önerdiği en iyi uygulamaları benimsemenizi gösterir.

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>Puanlama yöntemi üretim ile geliştirme ve test iş yüklerini birbirinden ayırt etmektedir mi?

Hayır, şimdilik değil. Ancak, bu kaynaklar geliştirme ve test için kullanılıyorsa ve öneriler uygulanmadığında, tek tek kaynaklardaki önerileri kapatabilirsiniz.

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>100 kaynakla bir abonelik ile 100.000 kaynakla bir abonelik arasındaki puanları karşılaştırabilir miyim?

Puanlama yöntemi, bir abonelik ve hizmet karışımındaki kaynak sayısını denetlemek için tasarlanmıştır. Daha az kaynağa sahip abonelikler, daha fazla kaynağı olan aboneliklerden daha yüksek veya daha düşük puan içerebilir.

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>Puan etkisi sütununda "yakında çıkacak" I gördiğimde ne anlama geliyor?

Bu ileti, önerinin yeni olduğu anlamına gelir ve danışman puan modeline getirmek için çalışıyoruz. Bu yeni öneri bir puan hesaplamasında dikkate alındıktan sonra, önerinizi için puan etkisi değerini görürsünüz.

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>Puanım, Azure 'da ne kadar harcadığım hakkında bağlıdır?

Hayır. Puanınız ne kadar harcadığınız hakkında bir yansıma değildir. Gereksiz harcama daha düşük bir **Maliyet** puanına neden olur.

## <a name="access-advisor-score"></a>Erişim Danışmanı puanı

Danışman puanı Azure portal genel önizlemededir. Sol bölmede, **Advisor** bölümünün altında, **Advisor Score** bölümüne bakın.

![Danışman puan giriş noktasını gösteren ekran görüntüsü.](./media/advisor-score-3.png)

## <a name="next-steps"></a>Sonraki adımlar

Danışman önerileri hakkında daha fazla bilgi için bkz.

* [Advisor 'a giriş](advisor-overview.md)
* [Danışman'ı kullanmaya başlama](advisor-get-started.md)
* [Danışman maliyet önerileri](advisor-cost-recommendations.md)
* [Advisor performans önerileri](advisor-performance-recommendations.md)
* [Danışman güvenlik önerileri](advisor-security-recommendations.md)
* [Danışman işletimsel üstün öneriler](advisor-operational-excellence-recommendations.md)
