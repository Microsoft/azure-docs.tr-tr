---
title: ETCS ve sorumlu kullanımı-kişiselleştirici
titleSuffix: Azure Cognitive Services
description: Bu yönergeler, şirketinizde ve hizmetinizde güven oluşturmanıza yardımcı olacak bir şekilde kişiselleştirme uygulamanıza yardımcı olmaya yönelik olarak tasarlanmıştır. Araştırmayı durakladığınızdan emin olun, insanların yaşamına yönelik olarak kişiselleştirmenin etkisini öğrenin ve bilinçli edin. Şüpheli, Arama Kılavuzu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: 11b626c0033814f0886ac76fff0c5d4087a80554
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720232"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Kişiselleştirici sorumlu uygulamasına ilişkin yönergeler

Kişiler ve topluluğu 'ın AI 'nin tüm potansiyelini karşılamalarını sağlamak için, uygulamaların uygulamalara AI ekledikleri güvenlerini ve AI ile derlenen uygulamaların kullanıcılarını elde ettikleri şekilde tasarlanmaları gerekir. Bu yönergeler, şirketinizde ve hizmetinizde güven oluşturmanıza yardımcı olacak bir şekilde kişiselleştirici uygulamanıza yardımcı olmaya yönelik olarak tasarlanmıştır. Araştırmayı durakladığınızdan emin olun, insanların yaşamına yönelik olarak kişiselleştirmenin etkisini öğrenin ve bilinçli edin. Şüpheli, Arama Kılavuzu.

Bu yönergeler yasal öneri olarak düşünülmemiştir ve uygulamanızın bu alandaki ve sektörün yasaların hızlı ilerlekiyle uyumlu olması için ayrı olarak emin olmanız gerekir.

Ayrıca, kişiselleştirici kullanarak uygulamanızı tasarlarken, her türlü veri merkezli AI sistemi geliştirirken, etklilik, gizlilik, güvenlik, güvenlik, içerme, saydamlık ve sorumluluk dahil olmak üzere sahip olduğunuz çok sayıda sorumluluğu göz önünde bulundurmanız gerekir. [Önerilen okuma](#recommended-reading) bölümünde bunlar hakkında daha fazla bilgi edinebilirsiniz.

Aşağıdaki içeriği bir başlatıcı denetim listesi olarak kullanabilir ve senaryonuza özelleştirip geliştirebilirsiniz. Bu belgenin iki ana bölümü vardır: ilki, Kişiselleştiriciye yönelik senaryolar, Özellikler ve ödüller seçerken sorumlu kullanım konularını vurgulamak için ayrılmıştır. İkincisi, Microsoft 'un AI sistemleri derlerken göz önünde bulundurulması gereken bir değer kümesi alır ve kişiselleştirici kullanım kullanmanın bunları nasıl etkilediği hakkında eylem yapılabilir öneriler ve riskler sağlar. 


## <a name="your-responsibility"></a>Sorumluluğu

Sorumlu uygulamaya yönelik tüm yönergeler, bu algoritmaları Topluluğu ' de kullanmanın etkileri açısından, geliştiricilerin ve işletmelerin kişiselleştirici ve sorumlu olduğunu temel alır. Kuruluşunuzun dağıtabolacağı bir uygulama geliştiriyorsanız, işlemi için rol ve sorumluluğun yanı sıra kişileri nasıl etkileyeceğini de bilmelisiniz. Üçüncü taraf tarafından dağıtılacak bir uygulama tasarladıysanız, uygulamanın davranışından sorumlu olan ve öğrenmiş olan belge ile paylaşılan bir anlama gelin.

Güven, karşılamış taahhütler kavramı üzerine kurulmuştur. kullanıcılarınızı, topluluğu ve uygulamalarınızın üzerinde çalıştıkları geçerli çatıyı, sahip oldukları açık ve örtük taahhütlerini belirlemek için göz önünde bulundurun.

Microsoft, bu sorumluluklar üzerinde işlem yapmanıza yardımcı olmak için sürekli olarak kendi araçlarına ve belgelerine bir çaba yerleştiriliyor. Ek araçlara inanıyorsanız, ürün özellikleri ve belgelerinin kişiselleştirici kullanımı için bu yönergeleri uygulamanıza yardımcı olacağını [Microsoft 'a geri bildirim sağlayın](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) .


## <a name="factors-for-responsibly-implementing-personalizer"></a>Kişiselleştirici uygulayan bir sorumlu için etken

Kişiselleştirici uygulama, kullanıcılarınız ve işletmeniz için harika bir değer olabilir. Kişiselleştirici sorumlu uygulamak için aşağıdaki yönergeleri göz önünde bulundurarak başlatın:

* Kişiselleştirmeyi uygulamak için kullanım durumlarını seçme.
* [Ödül işlevleri](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-rewards.md)oluşturma.
* Bağlam ve kişiselleştirme için kullanacağınız olası eylemlerle ilgili [özellikleri](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-features.md) seçme.


## <a name="choosing-use-cases-for-personalizer"></a>Kişiselleştirici için kullanım örneklerini seçme

İçeriği ve kullanıcı arabirimlerini kişiselleştirmek için öğrenmiş bir hizmet kullanmak faydalıdır. Bu, kişiselleştirmenin gerçek dünyada negatif yan etkileri oluşturmasının, kullanıcılar içerik kişiselleştirmenin farkında olup olmadığı da dahil olmak üzere, yanlış bir şekilde uygulanabilir. 

Negatif yan etkileri veya "ödül" de daha fazla basitleştirilmesi gereken çok uzun süreli karmaşık faktörlere bağlı olan ve "ödül" de saydamlık ekleme senaryolarına bağlı olan kişiselleştirici kullanımları örnekleri bireyler için sonuçlar. Bu, "SONUÇSAL" seçimler veya bir zarar riski içeren seçimler olarak değerlendirilir. Örnek: 


* **Finans**: kredi, mali ve sigorta ürünlerinin tekliflerini kişiselleştirmek, risk faktörleri kişilerin bilgi sahibi olmadığı, bu bilgileri edinemediğini veya itiraz etmediğini öğrenmez. 
* **Eğitim**: önerilerin okullara ve eğitim kurumlarına yönelik dereceleri kişiselleştirilmesi ve bunların diğer seçeneklere yönelik kullanıcıların farkında olabileceğini tahmin etmek.
* **Democracy ve Cıvic katılımı**: Kullanıcı için içerik kişiselleştirilmesi, etkili bir şekilde opvaons
* **Üçüncü taraf ödül değerlendirmesi**: kullanıcının kendi davranışından bir değerlendirme yapmak yerine, kullanıcının bir ikinci üçüncü taraf değerlendirmesini temel alan öğelerin kişiselleştirilmesi.
* **Araştırmayla Ilgili toleranslı**: kişiselleştiriciye ait araştırma davranışının zararlı olmasına neden olabilecek herhangi bir durum.

Kişiselleştirici için kullanım durumları seçerken:

* Kişiselleştirmenin kullanıcılarınıza nasıl yardımcı olduğunu göz önünde bulundurarak tasarım sürecini başlatın.
* Kişiselleştirme desenleri veya araştırması nedeniyle bazı öğeler kullanıcılar için derecelendirilmemişse gerçek dünyada olumsuz sonuçları göz önünde bulundurun.
* Kullanım durumlarınızın otomatik işleme oluşturduğunu, [GDPR](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) article 22 veya diğer yasalar kapsamında düzenlenen veri konularını önemli ölçüde etkilediği konusunda düşünün.
* Kendi kendini karşılayan Prophecy döngülerini düşünün. Bu durum, bir kişiselleştirmenin bir modeli daha sonra ilgili içeriğe erişimini daha sonra daha sonra dışlanmasını sağlamak için meydana gelebilir. Örneğin, düşük bir gelir alanındaki kişilerin çoğu Premium bir sigorta teklifi elde etmeyin ve komşulardan hiç kimse, yeterince araştırma olmaması durumunda teklifi bir bütün olarak görebilir.
* Daha sonra kişiselleştirici yeniden oluşturulması gereken durumlarda modellerin ve öğrenme ilkelerinin kopyalarını kaydedin. Bu işlemi düzenli aralıklarla veya her model yenileme döneminde yapabilirsiniz.
* Alan için araştırma düzeyini göz önünde bulundurun ve "yankı oluklığı" efektlerini azaltmak için bunu bir araç olarak nasıl kullanacağınızı düşünün.


## <a name="selecting-features-for-personalizer"></a>Kişiselleştirici için özellikleri seçme

İçeriği kişiselleştirmek, içerik ve Kullanıcı hakkında faydalı bilgiler olmasına bağlıdır. Bazı uygulamalar ve sektörler için bazı kullanıcı özelliklerinin doğrudan veya dolaylı olarak Discriminatory ve potansiyel olarak kabul edilebilir olduğunu aklınızda bulundurun.

Bu özelliklerin etkisini göz önünde bulundurun:

* **Kullanıcı demograflarına**yönelik özellikler: cinsel, cinsiyet, yaş, Yarış, dini ile ilgili Özellikler: Bu özelliklere, yasal nedenlerle belirli uygulamalarda izin verilmiyor olabilir ve kişiselleştirmenin yaydığı için bu özelliklere, bu özelliklerin çevresinde kişiselleştirilmesi istenebilir genelleştirmeler ve sapma. Bu sapma yayılmaya bir örnek, Mühendislik için, ELDERLY veya cinsiyet tabanlı hedef kitlelere gösterilmeyecek bir iş postadır.
* **Yerel ayar bilgileri**: dünyanın birçok yerinde, konum bilgileri (örneğin, bir posta kodu, posta kodu veya komşuları adı) gelir, yarış ve dini ile yüksek bir şekilde bağıntılı olabilir.
* **Eşitliği 'ın Kullanıcı tarafından algılanma sayısı**: Uygulamanızın ses kararları aldığı durumlarda bile, uygulamanızda görüntülenen içeriğin, uygulama değişiklikleri olan özellikler ile bağıntılı bir şekilde değişiklikler olduğunu göz önünde bulundurun. discriminatory.
* **Özelliklerde ISTENMEDEN sapma**: yalnızca popülasyonun bir alt kümesini etkileyen özellikler kullanılarak tanıtılamayabilir. Bu, bir resim veya metin analizinde metin halinde varlıkları saptamak için görüntü analizini kullanırken olduğu gibi, Özellikler oluşturulduysa, daha fazla dikkat gerektirir. Bu özellikleri oluşturmak için kullandığınız hizmetlerin özelliklerinden haberdar olun.

İçerik ve eylemlerde, Kişiselleştiriciye göndermek için özellikler seçerken aşağıdaki uygulamaları uygulayın:

* Bazı uygulamalar için belirli özellikleri kullanmanın yasallığını ve ahiteyi göz önünde bulundurun ve masum görünümlü özelliklerin,
* Algoritmaların ve veri analizinin göreceği seçenekleri kişiselleştirmek için kullanıldığı kullanıcılara saydam olun.
* Kendinize sorun: kullanıcılarınızın içeriği kişiselleştirmek için bu bilgileri kullandım. Belirli öğeleri vurgulamak veya gizlemek için kararının nasıl yapıldığını rahatlıkla göstermem gerekir mi?
* Diğer özelliklere göre sınıflandırma veya segmentasyon verileri yerine davranış kullanın. Demografik bilgileri, geçmiş nedenlerle perakendeciler tarafından, bir dijital çağdan önce toplanması ve üzerinde işlem yapması basit olan, ancak ilgili demografik bilgilerinin gerçek etkileşime sahip olduğunuzda nasıl olduğunu sorulandı. bağlamsal veriler ve kullanıcı kimliğine daha yakından ilişki kuran geçmiş verileri.
* Özelliklerin kötü amaçlı kullanıcılar tarafından ' sahte ' olarak nasıl önlenebildiğini, büyük sayılarda yararlanılması durumunda eğitim kişiselleştirmesine, emtibss ve belirli kullanıcı sınıflarının tacklerini yanıltmak için bir yanılmmaya yol açabilecek şekilde nasıl engelleyebileceğiniz göz önünde bulundurun. 
* Uygun ve uygun olduğunda, kullanıcılarınızın belirli kişisel özelliklerine sahip olmaya izin vermek veya bu özellikleri kabul etmek için uygulamanızı tasarlayın. Bunlar, "konum bilgileri", "cihaz bilgileri", "geçmiş satın alma geçmişi" vb. gibi gruplandırılabilir.


## <a name="computing-rewards-for-personalizer"></a>Kişiselleştirici için yeniden ödüller hesaplanıyor

Kişiselleştirici, uygulama iş mantığınızın verdiği ödül puanına göre hangi eylemin yeniden ele alınacağı seçimini geliştirmek için çaba harcar.

İyi oluşturulmuş bir ödül puanı, bir kuruluşun görevi için bağlı olan bir iş hedefine yönelik kısa süreli bir ara sunucu olarak görev görür.

Örneğin, tıklatmalar üzerinde yeniden çarpıtma,, tıklanmış veya bir iş sonucuna bağlı olmadığı durumlarda bile, diğer her şeyin masrafına, kişiselleştirici hizmet arama tıklamasını sağlar.

Benzer bir örnek olarak, bir haber sitesi, "kullanıcının içeriği okumak için yeterli zaman harcadığı" gibi, her şeyin daha anlamlı bir şeye bağlı olmasını isteyebilir. "İlgili makalelere veya başvurulara tıkladık mi?". Kişiselleştirici ile ölçümleri birbirine yakından bağlamak çok kolay. Ancak iyi sonuçlar ile kısa süreli kullanıcı etkileşimini onaylamamaya dikkat edin.

### <a name="unintended-consequences-from-reward-scores"></a>Ödül Puanlarından gelen istenmeyen sonuçlar
Reward puanları, en iyi şekilde oluşturulabilir, ancak kişiselleştirici içeriği nasıl derecelendirildiği konusunda beklenmedik sonuçlarla veya istemeden sonuçlar oluşturabilir. 

Aşağıdaki örnekleri göz önünde bulundurun:

* Video içeriğinin kişiselleştirilmesini video uzunluğu oranında izlemek, büyük olasılıkla daha kısa videolar sıralama eğilimi gösterir.
* Sosyal medya paylaşımlarını, nasıl paylaşıldığını veya içeriğin kendisini analiz etmeden daha iyi hale getirme, çok sayıda "katılım" de kapsayan ve çok az değer ekleyen rahatsız edici, aracılı veya ınflammatory içeriğini derecelendirmeye neden olabilir.
* Kullanıcıların değişiklik yapması beklendikleri Kullanıcı arabirimi öğelerinde eylemi yeniden çarpıtmak, Kullanıcı arabiriminin kullanılabilirliğini ve tahmin edilebilirliğini etkileyebilir; burada düğmeler, uyarı olmadan konum veya amaca göre değişiklik gösterir ve bu da daha zor hale gelir üretken kalmak için Kullanıcı grupları.

Bu en iyi yöntemleri uygulayın:

* Etki ve yan etkileri anlamak için farklı ödül yaklaşımlarını kullanarak sisteminizdeki çevrimdışı denemeleri çalıştırın.
* Ödül işlevlerinizi değerlendirin ve size son derece Naïve bir kişinin yorumu ile nasıl eğmekte olduğunu ve bununla istenmeyen sonuçlara nasıl ulaşabilmesini isteyin.


## <a name="responsible-design-considerations"></a>Sorumlu tasarım konuları

Aşağıda AI 'nin sorumlu uygulamalarına ilişkin tasarımın alanları verilmiştir. [Gelecekte hesaplanan](https://news.microsoft.com/futurecomputed/)bu çerçeveyi daha fazla öğrenin.

![Gelecekte hesaplanan AI değerleri](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Altına
*AI sistemlerini tasarlayan ve dağıtan kişilerin, sistemlerinin nasıl çalıştığı konusunda sorumlu olması gerekir*. 

* Kişiselleştirici, belge oluşturma ve bunları ekibiniz, Yöneticiler ve tedarikçilerle iletişim kurma hakkında iç yönergeler oluşturun.
* Yeniden puanlarının nasıl hesaplandığı hakkında düzenli incelemeler yapın, hangi özelliklerin kişiselleştirmeyi etkilediğini görmek için çevrimdışı değerlendirmeler gerçekleştirin ve gereksiz ve gereksiz özellikleri ortadan kaldırmak için sonuçları kullanın.
* Kişiselleştirici 'ın nasıl kullanıldığı, ne amaçla ve hangi verileri içeren kullanıcılarınız için net bir şekilde iletişim kurun.
* Kişiselleştirici tarafından sonuçları yeniden oluşturmak için kullanılan modeller, öğrenme ilkeleri ve diğer veriler gibi arşiv bilgileri ve varlıklar.

### <a name="transparency"></a>Saydamlık
*AI sistemleri*anlaşılabilir olmalıdır. Kişiselleştirici ile:

* *Kullanıcılara içeriğin nasıl kişiselleştirilme hakkında bilgi verin.* Örneğin, kullanıcılarınızı, Kullanıcı ve eylemlerin kişiselleştirici sonuçlarında bir rol oynadığı `Why These Suggestions?` etiketli bir düğme gösterebilirsiniz.
* Kullanım koşullarınızın, deneyimi kişiselleştirmek için kullanıcılar ve davranışları hakkında bilgi kullanacağınızı söylediğinizden emin olun.

### <a name="fairness"></a>Eşitliği
*AI sistemleri tüm kişileri oldukça kabul etmelidir*.

* Sonuçların uzun süreli, sonuçsal veya gerçek bir zarar olduğu durumlarda kullanım durumları için kişiselleştirici kullanmayın.
* İçeriği kişiselleştirmek için uygun olmayan veya istenmeyen bir iki özelliği yaymaya yardımcı olabilecek özellikleri kullanmayın. Örneğin, benzer finansal koşullara sahip herkes, mali ürünlere yönelik kişiselleştirilmiş önerilerin aynısını görmelidir.
* Düzenleyicilerde, algoritmik araçlarından veya kullanıcılardan kaynaklanan özelliklerde mevcut olabilecek iki özelliği anlayın.

### <a name="reliability-and-safety"></a>Güvenilirlik ve güvenlik
*AI sistemleri güvenilir ve güvenli bir şekilde gerçekleştirmelidir*. Kişiselleştirici için:

* *Seçilmemesi gereken Kişiselleştiriciye eylem sağlamamalıdır*. Örneğin, anonim veya yaşı olmayan bir kullanıcı için öneri oluşturmak üzere uygun olmayan filmlerde, kişiselleştirilmesi gereken eylemler filtrelenmelidir.
* *Kişiselleştirici modelinizi bir iş varlığı olarak yönetin*.  Model ve öğrenimi ilkelerinin ne sıklıkla kaydedilip yedeklendiğinin ve bunu önemli bir iş varlığı olarak kabul etmeyi düşünün. Geçmiş sonuçları çoğaltma, kendi kendine denetim ve ölçüm geliştirmesi için önemlidir.
* *Kullanıcılardan doğrudan geri bildirim almak için kanallar sağlayın*. Yalnızca doğru seyircilerin doğru içeriği görmesini sağlamak için güvenlik denetimlerini kodlamaya ek olarak, kullanıcıların şaşırtıcı veya rahatsız edici içerikleri raporlamalarına yönelik bir geri bildirim mekanizması sağlayın. Özellikle içeriğiniz kullanıcılardan veya 3. taraflardan geliyorsa, içeriği gözden geçirmek ve doğrulamak için Microsoft Content Moderator veya ek araçlar kullanmayı düşünün.
* *Sık sık çevrimdışı değerlendirmeler gerçekleştirin*. Bu, eğilimleri izlemenize ve verimliliğinden emin olmanıza yardımcı olur.
* *Kötü amaçlı işlemeyi tespit etmek ve üzerinde işlem yapmak için bir Işlem oluşturun*. Makine öğrenimi ve AI sistemlerinin ortamlarından yararlanarak, sonucu hedeflerine doğru kaydırmak için ortamından öğrenme imkanına sahip olacak aktörler vardır. Kişiselleştirici kullanımı önemli seçimleri etkilemek için bir pozisyontaklarca, uygun koşullarda insan incelemesi dahil olmak üzere bu saldırı sınıflarını tespit etmek ve azaltmak için uygun bir yol olduğundan emin olun.

### <a name="security-and-privacy"></a>Güvenlik ve gizlilik
*AI sistemleri güvende ve gizliliğe güvenmelidir*. Kişiselleştirici kullanılırken:

* *Kullanıcıları, toplanan veriler ve bu verilerin nasıl kullanıldığı hakkında öne*, yerel ve sektör düzenlemelerinizi izleyerek önceden onayı elde edin.
* *Gizlilik Koruması Kullanıcı denetimleri sağlar.* Kişisel bilgileri depolayan uygulamalar için, şu gibi işlevler için kolay bir bulma düğmesi sağlamayı düşünün: 
   * `Show me all you know about me`    
   * `Forget my last interaction` 
   * `Delete all you know about me`

Bazı durumlarda, bunlar yasal olarak gerekli olabilir. Yeniden eğitim modellerindeki avantajları, silinen verilerin izlemelerini içermediğinden düzenli aralıklarla değerlendirin.

### <a name="inclusiveness"></a>Her ikisi de
*Çok çeşitli insan ihtiyaçlarını ve deneyimlerini ele*edin.
* *Erişilebilirlik etkin arabirimler için kişiselleştirilmiş deneyimler sağlar.* Etkileşimlerde efor, taşıma ve gereksiz tekrarların miktarını azaltmak için iyi kişiselleştirmeden gelen verimlilik, özellikle Engelli kişiler için yararlı olabilir.
* *Uygulama davranışını bağlam olarak ayarlayın*. Bir sohbet bot içindeki amaçlar arasında belirsizliği ortadan kaldırmak için kişiselleştirici kullanabilirsiniz; Örneğin, doğru yorum bağlamsal olabilir ve bir boyut tümüne uygun olmayabilir. 


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Daha fazla veri koruma ve idare için öngörülü hazırlık

Mevzuata bağlamlarda belirli değişiklikleri tahmin etmek zordur, ancak genel olarak, kişisel verilerin daha etkili bir şekilde kullanımını sağlamak ve algoritmik karar verme ile ilgili saydamlık ve seçim sağlamak için en düşük yasal Framework 'ün ötesine geçecekti.


* Kişilerden toplanan veriler üzerinde yeni kısıtlamalar olabileceği ve kararlar almak için nasıl kullanıldığını göstermek için bir sorun olması gereken bir duruma planlamayı düşünün.
* Kullanıcıların, bu güvenlik açığı bulunan popülasyonu, çocukları, ekonomik güvenlik açığı olan kullanıcıları ya da kullanıcıların algoritmik düzenlemesini etkilemesine izin verebilecek daha fazla hazır olma durumu göz önünde bulundurun.
* Veri toplama programlarının ve algoritmaların nasıl çalındığını ve kendini kanıtlamış stratejik hatalardan kaçının.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proje yaşam döngünüz sırasında öngörülü değerlendirmeler

Ekip üyeleri, kullanıcılar ve iş sahipleri için, sorumlu kullanımlarla ilgili sorunları bildirme ve çözümlenmesini önceliklendiren ve yeniden dengeyi önleyen bir işlem oluşturan Yöntemler oluşturmayı düşünün.

Herhangi bir teknolojinin kullanmanın yan etkileri hakkında düşünce olan herhangi bir kişi, perspektiflerinin ve yaşam deneyimleriyle sınırlıdır. Ekipleriniz, kullanıcılarınız veya danışmanlık panolarınıza daha fazla sayıda ses vererek kullanılabilir olan oplarons aralığını genişletin; Bu şekilde, mümkün olduğunca konuşmaları önerilir. Bu etki alanındaki ekip bilgisini daha fazla genişletmek ve karmaşık ve hassas konuları tartışmak için yetenek eklemek amacıyla eğitim ve öğrenme malzemelerini değerlendirin.

Uygulama yaşam döngüsünde, Kullanıcı deneyimi, güvenlik veya DevOps ile ilgili görevler gibi diğer çapraz uygulamalar gibi, sorumlu görevlerle ilgili görevleri kabul etmeyi düşünün. Bu görevler ve gereksinimleri düşündüler olamaz. Sorumlu kullanım, uygulama yaşam döngüsü boyunca ele alınmalıdır ve doğrulanmalıdır.
 
## <a name="questions-and-feedback"></a>Sorular ve geri bildirim

Microsoft, bu sorumluluklar üzerinde işlem yapmanıza yardımcı olmak için sürekli olarak araçlara ve belgelere çaba yerleştiriliyor. Takımınız, ek araçlara, ürün özelliklerine ve belgelere, kişiselleştirici kullanımı için bu yönergeleri uygulamanıza yardımcı olduğunu düşünüyorsanız, [Microsoft 'a geri bildirim sağlamak](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) için sizi davet eder.

## <a name="recommended-reading"></a>Önerilen okuma

* Microsoft 'un, Ocak 2018 defterinde yayımlanan AI 'nin sorumlu geliştirmesi için, [gelecekte hesaplanan](https://news.microsoft.com/futurecomputed/)
* [Geleceğe kim sahip?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) Jaron Lanier.
* [Matematik ve matematiksel bir şekilde Yıkıcıkları](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction)
* DJ Patil, Hietmason, Mike Loukides tarafından [ETCS ve veri bilimi](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) .
* [ACM, Etics kodu](https://www.acm.org/code-of-ethics)
* [Genesel bilgiler ayırt edici olmayan bir Işlem-GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [Accountable algoritmaları için FATML Ilkeleri](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Sonraki adımlar

[Özellikler: eylem ve bağlam](concepts-features.md).
