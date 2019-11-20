---
title: Tahmine dayalı bakım çözümleri - Team Data Science Process için Azure yapay ZEKA Kılavuzu
description: Veri bilimi, Tahmine dayalı bakım çözümleri birden çok dikey sektörler çalışmasını sağlayan kapsamlı bir açıklaması.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/11/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: ec87146c721222702073eae067a259aa9848d0f7
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048980"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Tahmine dayalı bakım çözümleri için Azure yapay ZEKA Kılavuzu

## <a name="summary"></a>Özet

Tahmine dayalı Bakım (**PdM**) çeşitli sektörlerde işletmede yüksek varlık kullanımına ulaşmak ve maliyetlerinde tasarruf yardımcı olabilecek Tahmine dayalı analiz, yaygın bir uygulamadır. Bu kılavuz iş ve analitik yönergeleri ve başarılı bir şekilde geliştirip PdM çözümleri kullanarak dağıtmak için en iyi araya [Microsoft Azure yapay ZEKA platformu](https://azure.microsoft.com/overview/ai-platform) teknoloji.

Yeni başlayanlar için bu kılavuz, sektöre özgü iş senaryoları ve bu senaryolar için PdM uygun işlemi sunar. Modelleme ve veri gereksinimleri PdM çözümler oluşturmak için de sağlanır. Ana Kılavuzu'nun veri hazırlama, özellik Mühendisliği, model oluşturma ve modeli kullanıma hazır hale getirme adımları dahil olmak üzere veri bilimi süreci üzerinde - içeriktir. Bu anahtar kavramlar tamamlamak için bu kılavuzu bir dizi PdM uygulama geliştirmeyi hızlandırmak için çözüm şablonu listeler. Kılavuzu, ayrıca pratik AI veri bilimi arkasında hakkında daha fazla bilgi edinmek için yararlı eğitim kaynaklarına işaret eder. 

### <a name="data-science-guide-overview-and-target-audience"></a>Veri bilimi Kılavuzu genel bakış ve hedef kitle
Bu kılavuzun ilk yarısında, bu sorunları gidermeye yönelik PdM uygulama avantajları, tipik iş sorunlarını açıklar ve bazı ortak kullanım durumları listeler. İş karar mekanizmalarına (BDMs) bu içeriğe ilişkin yararlı olacaktır. İkinci yarısında hiç veri bilimi PdM arkasında açıklar ve bu kılavuzda gösterilen ilkeleri kullanılarak oluşturulan PdM çözümlerinin bir listesini sağlar. Öğrenme yolları ve eğitim malzemesi işaretçileri de sağlar. Teknik karar verenler (TDMs) bu içeriğin yararlı bulabilirsiniz.

| İle Başlat... | Eğer... |
|:---------------|:---------------|
| [Tahmine dayalı bakım için İş Gerekçesi](#business-case-for-predictive-maintenance) |kapalı kalma süresi ve işletim giderlerini azaltmak ve donanım kullanımını iyileştirmek için arayan bir iş karar veren (BDM) |
| [Tahmine dayalı bakım için veri bilimi](#data-science-for-predictive-maintenance) |benzersiz veri işleme ve Tahmine dayalı bakım için yapay ZEKA gereksinimleri anlamak için PdM teknolojilerini değerlendirirken bir teknik karar veren (TDM) |
| [Tahmine dayalı bakım çözüm şablonları](#solution-templates-for-predictive-maintenance)|bir yazılım Mimarı veya hızlı bir tanıtım veya bir kavram kanıtı yukarı bekleme isteyen yapay ZEKA geliştiricisi |
| [Tahmine dayalı bakım için eğitim kaynakları](#training-resources-for-predictive-maintenance) | Yukarıdaki, bir bölümünü veya tamamını ve temel kavramları veri bilimi, araçları ve teknikleri öğrenmek istersiniz.

### <a name="prerequisite-knowledge"></a>Önkoşul bilgileri
Önceki veri bilimi bilgisine sahip okuyucunun BDM içeriği beklemiyor. İstatistik ve veri bilimi temel bilgiye TDM içeriği için yararlıdır. Azure veri ve yapay ZEKA Hizmetleri, Python, R, XML ve JSON bilgi önerilir. Yapay ZEKA teknikleri, Python ve R paketleri uygulanır. Çözüm şablonları, Azure Hizmetleri, geliştirme araçları ve SDK'lar kullanarak uygulanır.

## <a name="business-case-for-predictive-maintenance"></a>Tahmine dayalı bakım için İş Gerekçesi

İşletmeler, en yüksek verimlilik ve kullanımı üzerinde sermaye yatırımlarınızı kendi dönüş hayata geçirmek için çalıştırılması için kritik donanımı gerektirir. Bu varlıklar, uçak motorları, turbines, elevators ya da milyonlarca - fotokopi, kahve makineler veya Su coolers gibi günlük Gereçleri aşağı maliyet endüstriyel chillers - değişiklik gösterebilir.
- Varsayılan olarak, çoğu işletmenin dayanan _düzeltici Bakım_, burada parçaları olarak değiştirilir ve bunlar başarısız olduğunda. Düzeltme bakım sağlar bölümleri tamamen kullanılır (Bu nedenle değil İsraf bileşen yaşam), ancak maliyetlerini iş kapalı kalma süresi, işçilik ve zamanlanmamış bakım gereksinimlerini (kapalı, saat veya kullanışsız konumları).
- İleri düzey, işletmelerin uygulama sırasında _önleyici bakım_, nerede bunlar bir bölümü için faydalı ömrü belirlemek ve korumak veya bir hatadan önce değiştirin. Önleyici bakım, zamanlanmamış veya yıkıcı arızaların yaşanmasını engeller. Ancak yüksek maliyetinden zamanlanmış bir kapalı kalma süresi, bileşenin tam yaşam kullanın ve hala işçilik önce eksik kullanımı kalır.
- Amacı _Tahmine dayalı Bakım_ etkinleştirerek düzeltme ve önleyici bakım, arasındaki dengeyi optimize etmek için _zamanında_ bileşenlerinin değiştirme. Yakın bir hata olduğunda bu yaklaşım yalnızca bu bileşenleri yerini alır. Bileşen lifespans (önleyici Bakımı karşılaştırıldığında) genişleterek ve zamanlanmamış Bakım ve işçi maliyetleri (düzeltme bakım) azaltma, işletmelerin maliyet tasarrufu ve rekabet avantajları elde edebilirsiniz.

## <a name="business-problems-in-pdm"></a>PdM iş sorunları
İşletmeler, beklenmeyen hatalar nedeniyle yüksek işletimsel risk yüz tanıma ve öngörü sorunların kök nedenini karmaşık sistemlerde sınırlı. İşle ilgili önemli sorulara bazıları şunlardır:

- Donanım ya da sistemin performansını veya işlev anomalileri algılayın.
- Bir varlık yakın gelecekte başlatılamayabilir olup olmadığını tahmin edin.
- Bir varlığın kalan faydalı ömrü tahmin edin.
- Bir varlığın hatası ana nedenlerini belirleyin.
- Bakım işlemleri, zaman göre bir varlık üzerinde yapılması gerektiğini belirleyin.

PdM tipik hedef deyimlerini şunlardır:

- Görev açısından kritik ekipman işletimsel riskini azaltır.
- Ortaya çıkmadan önce hatalarını tahmin varlıklar üzerinde oranını artırın.
- Bakım maliyeti, just-ın-time bakım işlemleri sağlayarak denetler.
- Müşteri attrition düşürün, marka resmi ve kayıp satış iyileştirin.
- Stok düzeylerini yeniden sıralama noktayı tahmin ederek çalışmasını azaltarak düşük stok maliyetlerini.
- Çeşitli bakım sorunlarına bağlı keşfedin.
- KPI'ları (ana performans göstergelerini) gibi sistem durumu puanları varlık koşullarını sağlayın.
- Kalan kullanım ömrü varlığını tahmin edin.
- Zamanında bakım etkinlikleri önerilir.
- Yalnızca zaman envanterinde değiştirme bölümleri için sipariş tarihlerini tahmin ederek etkinleştirin.

Bu hedef deyimleri için başlangıç noktaları şunlardır:

- _Veri bilimcileri_ analiz ve Tahmine dayalı belirli sorunları çözmek için.
- _Bulut mimarlarının ve geliştiricilerinin_ birlikte uçtan uca çözüm yerleştirmek için.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Tahmine dayalı bakım için sorunları niteleme
Tüm kullanım veya iş sorunlarını etkili bir şekilde PdM tarafından çözülebilir vurgulamak önemlidir. Sorun seçim sırasında düşünülmesi gereken üç önemli belirleme ölçütleri vardır:

- Sorun, doğası gereği Tahmine dayalı olmak zorundadır; diğer bir deyişle, olmalıdır bir hedef veya tahmin etmek için bir sonuç. Sorun da eyleminin algılandığında, bunların hatalarını önlemek için bir yol olmalıdır.
- Sorun kaydını işletimsel geçmişini içeren donanım olmalıdır _iyi ve hatalı sonuçlar_. Hatalı sonuçları azaltmak için gerçekleştirilen eylemleri kümesini de kullanılabilir olmalıdır bu kayıtları bir parçası olarak. Hata raporlarını, performans düşüşü bakım günlükleri onarın ve Değiştir günlükleri de önemlidir. Ayrıca, bunları ve değişiklik kayıtları geliştirmek için gerçekleştirilen onarım de yararlıdır.
- Kaydedilen geçmiş olarak yansıtılmasını _ilgili_ , verileri _yeterli_ kullanım örneğini desteklemek için yeterli kalite. Veri ilgi ve sufficiency hakkında daha fazla bilgi için bkz. [veri gereksinimleri için Tahmine dayalı Bakım](#data-requirements-for-predictive-maintenance).
- Son olarak, iş sorunu açık bir anlayışa sahip etki alanı uzmanları olması gerekir. Bunlar, iç işlemler ve anlamak ve yorumlamak veri analisti yardımcı olacak yöntemler farkında olmalıdır. Bunlar ayrıca Yardım gerekirse sorunları için doğru verileri toplamak için mevcut iş süreçlerini için gerekli değişiklikleri yapabilir olmalıdır.

## <a name="sample-pdm-use-cases"></a>Örnek PdM kullanım örnekleri
Bu bölümde PdM Havacılık yardımcı programları ve taşıma gibi çeşitli sektörlerden kullanım örnekleri koleksiyonunu ele alınmaktadır. Her bölüm, bir iş sorununu ile başlar ve PdM, iş sorununu ve son olarak bir PdM çözüm avantajlarını çevreleyen ilgili verileri avantajları anlatılmaktadır.

| İş sorununu | PdM sağladığı avantajları |
|:-----------------|-------------------|
|**Meydan meteorolojik**      |                   |
|_Uçuş gecikme ve iptallerinin_ mekanik sorunları nedeniyle. Sürede onarılamıyor hataları uçuşlar iptal edilmesine neden ve zamanlama ve işlemleri kesintiye uğratabilir. |PdM çözümleri mekanik hatalar nedeniyle iptal edildi veya Gecikmeli uçak olasılığını tahmin edebilirsiniz.|
|_Uçak motoru bölümleri hatası_: Uçak motoru bölümü değişiklik olan Havayolu sektör içinde en sık kullanılan bakım görevlerini arasında. Bakım çözümleri bileşen stok kullanılabilirliği, teslim ve planlama dikkatli yönetim gerektirir|Bileşen güvenilirlik ıntelligence'a yatırım maliyetlerini önemli ölçüde azalma için müşteri adayları toplamak işaretleyebilmesine.|
|**Finans** |                         |
|_ATM hatası_ bankacılık sektör içinde sık karşılaşılan bir sorundur. Buradaki sorun, bir ATM nakit mevzuatı işlem nakit dağıtıcısı kağıt sıkıştı veya bölümü hata nedeniyle kesintiye uğrarsa olasılık rapor etmektir. İşlem hata tahminlere göre ATM proaktif olarak hatalarının oluşmasını önlemek için hizmet verebilir.| Makine sürecin yarısında bir işlem başarısız olmasına izin vermek yerine, istenen hizmetini reddetmek için makine üzerinde Tahmine dayalı programa alternatiftir.|
|**Enerji** |                          |
|_Rüzgar türbin hatalarıyla_: Rüzgar türbinler, çevre açısından sorumlu ülkelerde/bölgelerde ana enerji kaynağıdır ve yüksek sermaye maliyetlerini içerir. Bir anahtar Rüzgar turbines Oluşturucu motor bileşenidir. kendi hata türbinin etkisiz işler. Ayrıca, düzeltmek son derece pahalı olur.|MTTF (ortalama süresi hatası) gibi KPI'leri tahmin etme, enerji şirketlerinin türbinin hatalarını önlemek ve çok az kesinti olun yardımcı olabilir. Hata olasılığını yakında başarısız olma olasılığı yüksek olan turbines izlemek için teknisyenleri bildirmek ve Bakım zaman tabanlı regimes zamanlayın. Tahmine dayalı modeller, sorunların kök nedenlerini teknisyenleri yardımcı olan hataya katkıda bulunan farklı faktörlerden Öngörüler daha iyi anlamak sağlar.|
|_Devre kesici hataları_: ev ve işletmelerin elektrik dağıtımını power satırları enerji teslimi garanti etmek için her zaman çalışır durumda gerektirir. Devre kesicilerin yardımcı sınırlamak veya zarar gücüne önlemek satırları aşırı yüklemesi sırasında veya olumsuz koşullar hava durumu. İş sorununu Burada, devre kesici hataları tahmin etmektir.| PdM çözümleri onarım maliyetleri azaltıp devre Kesiciler gibi donanım ömrü yardımcı olur. Beklenmeyen hataları ve hizmet kesintilerine azaltarak güç ağ kalitesini artırmak yardımcı olurlar.|
|**Nakliye ve lojistik** |    |
|_Asansör kapı hataları_: büyük Asansör şirketler işlevsel elevators dünyanın dört bir yanındaki milyonlarca için tam bir yığın hizmet sağlar. Asansör güvenlik, güvenilirlik ve çalışma süresi müşterileri için temel sorun var. Bu şirketler bu ve diğer çeşitli özniteliklerini düzeltme ve önleyici bakımla yardımcı olmak için algılayıcılar aracılığıyla izler. İçinde bir Asansör, en yaygın müşteri sorunu Asansör kapılar gerçekleştiriyor. Bu durumda iş sorununu kapı hatalarının olası neden tahmin eden bir Bilgi Bankası Tahmine dayalı uygulama sağlamaktır.| Elevators sermaye yatırımlarınızı için büyük olasılıkla bir 20-30 yıl kullanım ömrü ' dir. Bu nedenle her bir potansiyel satış son derece rekabetçi olabilir. Bu nedenle hizmeti ve Destek beklentileri yüksektir. Tahmine dayalı bakım, bu şirketler, rakiplerini, üründeki bir avantajı sağlar ve hizmet.|
|_Tekerlek hataları_: tüm yarısını derailments eğitmek ve milyarlarca genel parmaklık sektörde benzeri olmayan maliyet Tekerlek hataları hesabı. Tekerlek hataları de bazen erken ayırmak parmaklık neden bozulmasına rails neden olur. Parmaklık sonları derailments gibi yıkıcı olaylara kısalmasına neden olur. Bu tür örnekleri önlemek için railways tekerlekleri performansını izleyebilir ve bunları önleyici bir şekilde değiştirin. İş burada tekerleği hataları tahmin sorunudur.| Tekerlekleri, Tahmine dayalı bakım ile wheels, just-ın-time değiştirme yardımcı olur |
|_Subway train kapı hataları_: bir ana subway operations gecikme train otomobiller kapı hatalarının nedeni. Burada problemini train kapı arızaların tahmin edilmesine sağlamaktır.|Kapı hatası ya da bir kapı hatası kadar gün sayısını erken farkındalık bakım zamanlamaları kapı eğitim iş İyileştir yardımcı olur.|

Sonraki bölümde, yukarıda açıklanan PdM avantajlarından nasıl detayına alır.

## <a name="data-science-for-predictive-maintenance"></a>Tahmine dayalı bakım için veri bilimi

Bu bölümde, PdM için veri bilimi ilkeleri ve uygulama genel yönergeleri sağlar. TDM, çözüm Mimarı yardımcı olmayı hedefler ya da bir geliştirici, önkoşulları ve PdM için uçtan uca yapay ZEKA uygulamaları oluşturmak için işleme anlama. Bu bölümde tanıtımları incelenmesi birlikte okuyabilir ve kavram kanıtı şablonları listelenen [Tahmine dayalı bakım çözüm şablonları](#solution-templates-for-predictive-maintenance). Ardından, Azure'da PdM çözümünüzü uygulamak için bu ilkeleri ve en iyi yöntemler kullanabilirsiniz.

> [!NOTE]
> Bu kılavuz, veri bilimi okuyucu öğretmek için tasarlanmamıştır. Birkaç faydalı kaynaklar bölümünde daha fazla bilgi için sağlanan [Tahmine dayalı bakım için eğitim kaynakları](#training-resources-for-predictive-maintenance). [Çözüm şablonları](#solution-templates-for-predictive-maintenance) Kılavuzu'nda listelenen bazı belirli PdM sorunlar için bu AI teknikleri gösterir.

## <a name="data-requirements-for-predictive-maintenance"></a>Tahmine dayalı bakım için veri gereksinimleri

Tüm öğrenme başarısını ne verilen kalite (a) ve (b) learner yeteneğini bağlıdır. Tahmine dayalı modelleri desenler geçmiş verilerden bilgi almak ve gözlemlenen bu eğilimlere bağlı olarak belirli bir olasılık ile gelecekteki sonuçları tahmin edin. Tahmine dayalı bir modelin doğruluğunu ilgi düzeyini, sufficiency ve eğitim ve test veri kalitesi bağlıdır. 'Bu model kullanılarak puanlanır' yeni veri, eğitim ve test verileri olarak aynı özellikleri ve şema olması gerekir. Yeni verileri özellik özellikleri (tür, yoğunluğu, dağıtım ve benzeri), eğitim ve test veri kümeleri eşleşmesi gerekir. Bu bölümde, bu tür veri gereksinimlerine biridir.

### <a name="relevant-data"></a>İlgili verileri

İlk olarak, veri olması gerekir _ilgili sorun_. Göz önünde bulundurun _Tekerlek hatası_ ele alınan kullanım eğitim verileri tekerleği işlemlerle ilgili özellikleri yukarıdaki - içermelidir. Sorun hatasını tahmin etmek için ise _oldukça yaygınlaştı sistem_, oldukça yaygınlaştı sistem tüm farklı bileşenlerini kapsayacak şekilde eğitim verileri vardır. Daha büyük bir alt sistem hatasını ikinci koşul hedefleyen ise ilk harfi belirli bir bileşeni hedefler. İkinci daha veri dağınık olduğundan daha büyük alt sistemlerin yerine belirli bileşenleri hakkında öngörü sistemleri tasarlamak için genel kullanılması önerilir. Etki alanı uzmanı (bkz [sorunları Tahmine dayalı bakım için uygun](#qualifying-problems-for-predictive-maintenance)) veri analizi için en uygun alt kümelerini seçme içinde yardımcı olmalıdır. İlgili veri kaynaklarına daha ayrıntılı olarak ele alınmıştır [Tahmine dayalı bakım için veri hazırlama](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Yeterli veri
İki soruyla hatası geçmiş verileri ile ilgili sık sorulan: (1) "kaç hatası olaylarının bir model eğitip gerekli midir?" (2) "kaç kayıt" yeterince "kabul edilir?" Kesin yanıt yoktur, ancak yalnızca Thumb kuralları. (1) için daha hatası olaylarının sayısını daha iyi bir model. (2) ve veri ve sorun Çözüldü bağlamı hatası olaylarının sayısına bağlıdır. Ancak, bir makine çok sık başarısız olursa diğer taraftan, ardından işletme, hangi hata örnekleri azaltacak yerini alır. Burada yine etki alanında Uzman rehberlik önemlidir. Ancak sorun büyümesinin üstesinden gelmek için yöntemleri vardır _nadir olayları_. Bu bölümde ele alınmıştır [imbalanced veri işleme](#handling-imbalanced-data).

### <a name="quality-data"></a>Kalite verileri
Veri Kalitesi önemlidir - her bir tahmin unsuru öznitelik değeri olmalı _doğru_ birlikte hedef değişkeninin değeri. Veri Kalitesi istatistik ve veri yönetimi iyi studied alanındadır ve out dolayısıyla, bu kılavuzun kapsamı.

> [!NOTE]
> Çeşitli kaynaklar ve kalite verileri sunmak için Kurumsal ürünleri vardır. Başvurular bir örneği aşağıda verilmiştir:
> - Dasu, T, Johnson T., Keşif veri madenciliği ve verileri, 2003 Wiley, temizleme.
> - [Keşif veri analizi, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J nicel veri büyük veritabanları için temizleme](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der Ara, M, veri temizleme R ile giriş](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Tahmine dayalı bakım için veri hazırlama

### <a name="data-sources"></a>Veri kaynakları

Tahmine dayalı bakım için ilgili veri kaynaklarına içerir ancak bunlarla sınırlı değildir:
- Hata geçmişi
- Bakım/onarma geçmişi
- Makine çalışma koşullarını
- Donanım meta verileri

#### <a name="failure-history"></a>Hata geçmişi
Hata olayları PdM uygulamalarda seyrek kullanılır. Ancak, tahmin modellerini oluştururken, işletimsel desen normal bir bileşenin yanı sıra hakkında kendi hata desenleri öğrenmek algoritma gerekir. Bu nedenle eğitim verileri örnekler hem kategorilerden yeterli sayıda içermelidir. Bakım kayıt ve bölümleri değişiklik geçmişi hatası olaylarını bulmak için uygun kaynaklardır. Bazı etki alanı bilgisini yardımıyla eğitim verilerdeki anomaliler hatalar olarak tanımlanabilir.

#### <a name="maintenancerepair-history"></a>Bakım/onarma geçmişi
Bir varlığın bakım geçmişi, değişen bileşenlerle ilgili ayrıntıları ve gerçekleştirilen onarım etkinliklerini içerir. Bu olaylar, düşme düzenlerini kaydeder. Eğitim verileri bu çok önemli bilgileri olmaması yanıltıcı modeli sonuçlara yol açabilir. Özel hata kodlarını veya sipariş tarihlerini bölümleri için başarısızlık geçmişi de bakım geçmişi içinde bulunabilir. Hata desenleri etkileyen ek veri kaynaklarını araştırılması ve etki alanı uzmanları tarafından sağlanan.

#### <a name="machine-operating-conditions"></a>Makine çalışma koşullarını
Algılayıcı tabanlı (veya diğer) akış verilerini ekipmanın işleminde, bir önemli veri kaynağıdır. Bir anahtar PdM içinde bir makinenin sistem durumu, rutin işlemi sırasında zamanla düşürür varsayılır. Veriler, bu eskime düzeni ve anomalileri düşmesine yol açar ve zaman açısından değişkenlik gösteren Özellikler içermesi beklenir. Zamana bağlı veriler yönüyle algoritması hata ve hata olmayan desenleri zamanla öğrenmek için gereklidir. Bu veri noktalarında bağlı olarak, süresinin kaç tane daha fazla birimi bir makine başarısız önce çalışmaya devam edebilirsiniz tahmin etmek algoritma öğrenir.

#### <a name="static-feature-data"></a>Statik özellik verileri
Statik özellikler ekipman hakkındaki meta verileri alır. Örnekler donanım oluşturma, modeli, üretilen tarihi, tarih hizmetinin, sistem ve diğer teknik belirtimler konumunu başlatın.

İlgili verileri örnekleri [örnek PdM kullanım örnekleri](#sample-pdm-use-cases) aşağıdaki tabloda verilmiştir:

| Kullanım Örneği | İlgili veri örnekleri |
|:---------|---------------------------|
|_Uçuş gecikmesini ve iptalleri_ | Rota bilgilerini uçuş Bacak biçiminde uçuş ve günlükleri sayfasında. Uçuş BAI verileri, ayrılma/varış tarihi, zaman, Havaalanı, layovers vb. yönlendirme ayrıntılarını içerir. Sayfa günlüğü, bir dizi hata ve bir hata ve bakım kodu içerir.|
|_Uçak motoru bölümleri hatası_ | Çeşitli parçalarının koşula bilgilerini uçak, sensörlerden alınan veri toplanmadı. Bakım kayıt bileşeni hataları oluştu ve bunların yerini belirlemenize yardımcı olur.|
|_ATM hatası_ | Sensör okumaları (nakit/onay ürünü) her bir işlem için ve nakit dağıtmak. Notlar, Not kalınlığı, Not varış uzaklığı, denetim öznitelikleri vb. arasında boşluk ölçümü hakkında bilgi. Hata kodları sağlayan bakım kayıtları, onarım bilgileri, nakit dağıtıcısının en son yeniden doldurulduğu zaman.|
|_Rüzgar türbinin hatası_ | Algılayıcılar, sıcaklık, Rüzgar yönü, güç oluşturma, Oluşturucu hızı vb. gibi türbin koşullarını izler. Veriler, çeşitli bölgelerde bulunan Rüzgar gruplarından birden çok rüzgar türbinler tarafından toplanır. Genellikle, her türbinin ölçümleri bir sabit bir zaman aralığında geçiş birden çok sensör okumaları sahip olacaktır.|
|_Devre kesici hataları_ | Bakım günlükleri düzeltme, önleyici ve sistematik Eylemler içerir. Gibi devre kesicileri açma ve kapama eylemler için gönderilen otomatik ve el ile komutları içeren işletimsel verileri. Üretim tarihi, konum, model vb. gibi cihaz meta verileri Voltaj düzeyleri, coğrafi konum, çevresel koşullar gibi devre kesici belirtimleri.|
|_Asansör kapı hataları_| Asansör meta veri türü Asansör, üretilen tarih, bakım sıklığı, yapı türü ve benzeri gibi. Kapı döngüleri, ortalama kapağı kapatma zaman sayısı gibi işletimsel bilgiler. Hata geçmişi ile neden olur.|
|_Tekerlek hataları_ | Tekerlek hızlandırma, örgü örnekleri, gidiş uzaklığı, hız vb. ölçülü algılayıcı verileri. Üretici ve mamul tarihi gibi tekerlekler hakkında statik bilgiler. Sipariş tarihlerini ve miktarlar izleyen parça sipariş veritabanından olayla hatası verileri.|
|_Subway train kapı hataları_ | Kapı açılış ve kapanış kez train kapı geçerli durumu gibi diğer işletimsel verileri. Statik veri varlığı tanımlayıcısı, saati ve koşul değeri sütunları içerir.|

### <a name="data-types"></a>Veri türleri
Yukarıdaki veri kaynaklarına göz önünde bulundurulduğunda, gözlemlenen PdM etki alanında iki ana veri türleri şunlardır:

- _Zamana bağlı veriler_: işlem telemetrisini, makine koşullar, iş sırası türleri, zaman damgalarını kaydı bir zamanda sahip öncelik kodları. Hata, bakım/onarma ve Kullanım Geçmişi her olayla ilişkili zaman damgaları da gerekir.
- _Statik veri_: Makine özellikleri ve işleç özellikleri genel statik makineler işleci öznitelikleri ve teknik belirtimler tanımladıkları olduğundan. Bu özellikler, zaman içinde değişebilir, ayrıca bunlarla ilişkili zaman damgaları sahip olmalıdır.

Tahmin unsuru ve hedef değişkenlerini önceden işlenmiş/dönüştürülmüş olmalıdır [sayısal, kategorik ve diğer veri türleri](https://www.statsdirect.com/help/basics/measurement_scales.htm) bağlı kullanılan algoritma.

### <a name="data-preprocessing"></a>Veri ön işleme
Bir önkoşul olarak _özellik Mühendisliği_, çeşitli akışlarından onu olduğu özellikleri oluşturmanıza kolayca bir şema oluşturmak için verileri hazırlama. Verilerin ilk kayıtların bulunduğu bir tablo görselleştirin. Tablodaki her satır bir eğitim örneğini temsil eder ve sütunlarını temsil eden _tahmin unsuru_ özellikleri (bağımsız öznitelikler veya değişkenleri olarak da bilinir). Son sütunların güvenilecek şekilde verileri düzenleme _hedef_ (bağımlı değişkeni). Her bir eğitim örneği için Ata bir _etiket_ bu sütunun değeri.

Zamana bağlı veriler için sensör verilerinin süresi zaman birimler halinde böler. Her bir kaydı bir varlık için zaman birimi ait olması gereken _ve farklı bilgi sunmalıdır_. Zaman birimi katları saniye, dakika, saat, gün, iş gereksinimlerinize göre tanımlanan ay, ve benzeri. Zaman birimi _veri toplama sıklığı ile aynı olması gerekmez_. Sıklığı yüksekse, herhangi bir birim önemli fark diğer veriler gösterilmeyebilir. Örneğin, ortam sıcaklığı 10 saniyede toplanan varsayalım. Eğitim verileri, aynı aralık kullanarak yalnızca örnek sayısını herhangi bir ek bilgi sağlamadan Şişir. Bu durumda, daha iyi bir stratejisi üzerinde İş Gerekçesi verilere tekrar 10 dakika ya da bir saat göre ortalama kullanmak olabilir.

Statik veriler için
- _Bakım kayıt_: ham bakım verilere sahip bir varlık tanımlayıcısı ve belirli bir anda zaman içinde gerçekleştirilen bakım etkinlikleri hakkında bilgilerle zaman damgası. Bakım etkinliklerine dönüştürme _kategorik_ sütunları, burada her kategori tanımlayıcı benzersiz olarak eşler için bir özel bakım eylemi. Bakım kayıt için şema varlık tanımlayıcısı, saati ve Bakım eylemi içerir.

- _Hata Kayıt_: belirli hata kodlarıyla veya hatası olaylarının tanımlanan belirli iş koşullarına göre hataları veya hatasının nedenleri kaydedilebilir. Donanım birden çok hata kodları sahip olduğu durumlarda, etki alanında Uzman hedef değişkene ilgili olanları belirlemenize yardımcı olmalıdır. Diğer hata kodları veya koşulları oluşturmak için kullandığı _tahmin unsuru_ bu hatalar ile ilişkilendirmek özellikleri. Hata kayıt için şema varsa varlık tanımlayıcısı, saat, başarısız veya hata nedeni - verilebilir.

- _Makine ve işleci meta verilerini_: bir varlık ilgili öznitelikleriyle birlikte kendi işleci ile ilişkilendirmek için bir şema makine ve işleci verileri birleştirin. Makine koşulları için şema varlık tanımlayıcısı, varlık özellikleri, işleci tanımlayıcısı ve işleç özellikleri içerir.

Ön işleme adımları diğer veri içeren _eksik değerleri işleme_ ve _normalleştirme_ öznitelik değerleri. Ayrıntılı bir tartışma bu kılavuzun kapsamı dışındadır - bazı yararlı başvuruları için sonraki bölüme bakın.

Yukarıdaki ile yerinde, özellik Mühendisliği varlık tanımlayıcısı ve zaman damgası göre yukarıdaki tabloların katılmak için önce son dönüşümü veri kaynakları önceden işlenmiş. Makine normal işlem olduğunda ortaya çıkan tabloda hata sütunu için null değerler gerekir. Bu null değerler, normal işlem için bir gösterge olarak imputed. Bu hata sütun oluşturulacağı _Tahmine dayalı bir model için etiketleri_. Daha fazla bilgi için üzerinde bölümüne bakın. [modelleme teknikleri Tahmine dayalı bakım için](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Özellik mühendisliği
Özellik Mühendisliği, veri modelleme önce ilk adımdır. Veri bilimi işlemi kendi rolünde [burada açıklanan](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). A _özellik_ sıcaklık, baskısı, titreşim ve benzeri gibi model - için Tahmine dayalı bir özniteliktir. PdM için özellik Mühendisliği, hacimle süre içinde toplanan geçmiş veriler üzerinde bir makinenin sistem durumu özetleyen içerir. Bu anlamda den eşlerine Uzaktan izleme, anomali algılama ve hata algılama gibi farklı. 

### <a name="time-windows"></a>Zaman pencereleri
Uzaktan izleme sürümünden itibaren gerçekleşen olayları raporlama kapsar _zaman içinde nokta_. Anomali algılama modelleri (puan) gelen akışları veri noktaları itibarıyla bayrağı anomalileri zaman değerlendirin. Hata algılama belirli türlerdeki sürede noktaları ortaya çıktıkları olmasını hataları sınıflandırır. Buna karşılık, PdM üzerinden hatalarını tahmin içerir. bir _gelecekte süre_üzerinden makine davranışını temsil eden özellikler göre _geçmiş süre_. PdM için özellik zaman tek tek noktalarından Tahmine dayalı olarak çok gürültülü verilerdir. Veriler her bir özellik olması gerekir _smoothened_ zaman pencereleri veri noktaları toplama tarafından.

### <a name="lag-features"></a>Lag özellikleri
Ne kadar geleceğe tahmin modeli olan iş gereksinimlerini tanımlayın. Sırayla bu süre, 'ne kadar model aramak yanınızdadır' tanımlamak yardımcı olur bu tahminler gerçekleştirin. Bu 'geri dönem bakarak' adlı _lag_, ve bu bekleme dönemi boyunca mühendislik özellikler adlandırılır _lag özellikleri_. Bu bölümde, veri kaynaklarından zaman damgaları ve statik veri kaynaklarından oluşturma özelliği ile oluşturulmuş lag özellikleri açıklanmaktadır. Lag özellikleridir genellikle _sayısal_ yapısı.

> [!IMPORTANT]
> Pencere boyutu deneme ile belirlenir ve bir etki alanında Uzman yardımı ile sonlandırılır. Seçim ve gecikme özelliklerini, bunların toplamalar ve windows türü tanımı için aynı uyarı tutar.

#### <a name="rolling-aggregates"></a>Sıralı toplamaları
Her bir varlık kaydı için sıralı bir pencere boyutu "W" toplamları hesaplamak için zaman birimlerinin sayısı olarak seçilir. Lag özellikleri, W dönemleri kullanılarak ardından hesaplanır _tarihinden önce_ kaydın. Şekil 1'de mavi satırlar her zaman birimi için bir varlık için kayıtlı algılayıcı değerlerini gösterir. Bunlar, W = 3 boyutunun bir pencere üzerinde çalışırken bir özellik değerlerinin ortalamasını gösterir. Hareketli ortalamanın damgalı t aralıktaki tüm kayıtlar üzerinden hesaplanır<sub>1</sub> (turuncu içinde) t<sub>2</sub> (yeşil içinde). W genellikle dakika veya saat verilerin doğasına bağlı olarak değerdir. Ancak belirli bir varlığın tüm geçmiş kaydının kadar zaman sorunları, büyük bir W (örneğin, 12 aylık) çekme sağlayabilir.

![Şekil 1. Toplama özellikleri çalışırken](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png) Şekil 1. Toplama özellikleri alınıyor

Toplamlar bir zaman penceresi üzerinde çalışırken, örnek sayısı, ortalama, CUMESUM (birikmeli toplamı) ölçüler, en düşük/en yüksek değerleri verilebilir. Ayrıca, farkı, standart sapma ve aykırı değerleri ötesinde N standart sapma sayısı sık sık kullanılır. Örnekler için uygulanabilir Toplamalarına [kullanım](#sample-pdm-use-cases) bu kılavuzda, aşağıda listelenmiştir. 
- _Uçuş gecikme_: geçen gün/hafta boyunca hata kodları sayısı.
- _Uçak motoru bölüm hatası_: geçen gün, standart sapma ve toplam gün, hafta vb. dahil olmak üzere toplama anlamına gelir. Bu ölçüm, iş etki alanı uzmanı ile birlikte belirtilmelidir.
- _ATM hataları_: sıralı anlamına gelir, Orta, aralığı, standart sapma, aykırı değerleri dışında üst ve alt CUMESUM üç standart sapma sayısı.
- _Subway train kapı hataları_: önceki gün, haftalık, vb. iki hafta içinde olay sayısı.
- _Devre kesici hataları_: hata geçtiğimiz hafta içinde yıl, üç yıl vb. sayar.

Başka bir kullanışlı PdM, eğilim değişiklikleri, ani ve verileri anormallikleri algoritmalarını kullanarak düzeyi değişiklikleri yakalamak için bir tekniktir.

#### <a name="tumbling-aggregates"></a>Atlayan toplamaları
Bir varlığın etiketlendiği her kayıt için, _w-<sub>k</sub>_  boyutundaki bir pencere tanımlanmıştır; burada _k_ , _w_boyutundaki pencerelerin sayısıdır. Daha sonra toplamalar, bir kaydın zaman damgasından önceki dönemler için _Windows_ _W-k, w-<sub>(k-1)</sub>,..., w-<sub>2</sub>, w-<sub>1</sub>_  ' n i _n üzerine yazılır_ . _k_ kısa vadeli etkileri yakalamak için küçük bir sayı veya uzun süreli performans düşüşü desenleri yakalamak için büyük bir sayı olabilir. (bkz: Şekil 2).

![Şekil 2. Atlayan toplama özellikleri](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png) Şekil 2. Toplama özellikleri atlayan

Rüzgar turbines kullanım örneği oluşturulabilir, H = 1 k ile özellikleri, öteleme = 3. Bunlar, her üst ve alt aykırı değerleri kullanarak son üç ay için gecikme aktarıldığını belirtir.

### <a name="static-features"></a>Statik özellikler

Tarih gibi donanım üretim, model numarası, konum, teknik belirtimler statik özellikler bazı örnekleri verilmiştir. Bunlar kabul _kategorik_ modelleme değişkenleri. Voltaj, geçerli, güç kapasitesi, dönüştürücü türü ve güç kaynağı, devre kesici kullanım örneği bazı örnek olarak verilebilir. Tekerlek hataları için örnek Lastik tekerlekleri (karışımı vs çelik) türüdür.

Şu ana kadar bahsedilen veri hazırlama çabalarını aşağıda gösterildiği gibi düzenlenmiş veri neden. Eğitim, sınama ve doğrulama veri (Bu örnekte zaman gün birimleri cinsinden gösterilmiştir) Bu mantıksal şemaya sahip olmalıdır.

| Varlık Kimliği | Zaman | \<Özellik sütunları > | Etiketle |
| ---- | ---- | --- | --- |
| A123 |1\. gün | . . . | . |
| A123 |2\. gün | . . . | . |
| ...  |...   | . . . | . |
| B234 |1\. gün | . . . | . |
| B234 |2\. gün | . . . | . |
| ...  |...   | . . . | . |

Özellik Mühendisliği son adımında, **etiketleme** hedef değişkeni. Bu işlem, modelleme tekniği bağlıdır. Buna karşılık, modelleme teknik problemini ve kullanılabilir verilerin doğasına bağlıdır. Etiketleme, sonraki bölümde ele alınmıştır.

> [!IMPORTANT]
> Veri hazırlama ve özellik Mühendisliği başarılı PdM çözümleri gelmesi teknikleri modelleme olarak kadar önemlidir. Etki alanı uzmanı ve pratik doğru özellikleri ve veri modeli için gelen içinde önemli zaman ayırmanız. Özellik Mühendisliği birçok Kitaplar'dan küçük bir örnek aşağıda listelenmiştir:
> - Pyle, d veri hazırlığı için veri (Morgan Kaufmann dizide veri yönetim sistemleri), 1999 araştırma
> - Zheng, A., Casari Machine Learning için a özellik Mühendisliği: ilkeler ve veri uzmanları, O'Reilly, 2018'e yönelik teknikler.
> - Dong, G. Liu, H. (düzenleyiciler), Machine Learning ve veri analizi için özellik Mühendisliği (Bölümman & salonu/CRC veri madenciliği ve bilgi bulma serisi), CRC basma, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Tahmine dayalı bakım için modelleme teknikleri

Bu bölüm, belirli bir etiket oluşturma yöntemleriyle birlikte PdM sorunlar için ana modelleme teknikleri açıklar. Farklı endüstriler arasında bir tek modelleme teknik kullanılabilir dikkat edin. Modelleme teknikleri, veri eldeki bağlamında yerine veri bilimi sorununu eşleştirilir.

> [!IMPORTANT]
> Hata koşulları ve etiketleme stratejisi için etiketleri seçimi  
> etki alanı Uzman danışmanlığı içinde belirlenmesi.

### <a name="binary-classification"></a>İkili sınıflandırma
İkili sınıflandırma, gelecekteki _bir zaman diliminde bir ekipman parçasının başarısız olma olasılığını tahmin_ etmek için kullanılır ve _gelecek ufuk dönemi X_olarak adlandırılır. X, iş sorunu ve taraftaki veriler tarafından etki alanı uzmanlığına göre belirlenir. Örnekler şunlardır:
- _Minimum sağlama süresi_ bakım kaynakları dağıtma, söz konusu dönemde arıza oluştuğu sırada bir sorundan kaçınmak için bakımının bileşenleri değiştirmek için gerekli.
- _en az bir olay sayısı_ bir sorun oluşmadan önce ortaya çıkar.

Bu teknik eğitim örnekleri iki tür tanımlanır. Pozitif bir örnek _belirten bir hata_, etiketi = 1. Normal işlemler gösterir, negatif bir örnek, etiketi = 0. Hedef değişkeni ve bu nedenle, etiket değerleri _kategorik_. Modeli, büyük olasılıkla başarısız veya zaman birimi X önümüzdeki normal olarak çalışmak her yeni örneğin tanımlamanız gerekir.

#### <a name="label-construction-for-binary-classification"></a>İkili sınıflandırma için etiket oluşturma
Burada soru şudur: "varlık sonraki başarısız olduğunu belirten olasılığı nedir zaman birimi X?" Bu soru, bir varlığın "arıza" olarak arıza öncesinde etiketini X kayıtları yanıtlamak için (Etiket = 1), "normal" olarak tüm kayıtları etiketlemelerine (etiket = 0). (bkz: Şekil 3).

![Şekil 3. İkili sınıflandırma etiketleme](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png) Şekil 3. İkili sınıflandırma etiketleme

Bazı kullanım örnekleri için stratejisi etiketleme örnekleri aşağıda listelenmiştir.
- _Uçuş gecikme_: X seçilebilir 1 gün gecikmeler sonraki 24 saat içindeki tahmin etmek için. Ardından hataları önce 24 saat içinde olan tüm uçuşlar 1 etiketlenmiştir.
- _ATM nakit etiket hataları_: sonraki bir saat içinde bir işlem hatası olasılığını belirlemek için bir hedef olabilir. Bu durumda, hatanın son bir saat içinde gerçekleşen tüm işlemleri 1 etiketlenmiştir. Sonraki N para birimi hata olasılığını tahmin dispensed, notları hata son N notlarına dispensed tüm notları 1 etiketlenmiştir.
- _Devre kesici hataları_: sonraki devre kesici komut hatası tahmin etmek için hedef olabilir. Bu durumda, bir sonraki komuttan olmasını X seçilir.
- _Eğitim kapı hataları_: X iki gün olarak seçilebilir.
- _Rüzgar türbinin hataları_: X iki ay seçilebilir.

### <a name="regression-for-predictive-maintenance"></a>Tahmine dayalı bakım için regresyon
Regresyon modellerini alışkın olduğunuz _bir varlığın kalan faydalı ömrü (RUL) hesaplaması_. RUL sonraki hata gerçekleşmeden önce bir varlık çalışır durumda süre miktarı tanımlanır. Her bir eğitim örneğe ait olduğu için zaman birimi kaydıdır _nY_ bir varlık için burada _n_ katsayıdır. Modelin her yeni bir örnek olarak, RUL hesaplamak bir _sürekli numarası_. Bu hatadan önce kalan süreyi gösterir.

#### <a name="label-construction-for-regression"></a>Regresyon için etiket oluşturma
Burada soru şudur: "Ekipmanın kalan faydalı ömrü (RUL) nedir?" Arıza öncesinde her bir kayıt için sonraki hatasından önce kalan zaman birimlerinin sayısı için etiket hesaplayın. Bu yöntemde, etiketleri sürekli değişkenlerdir. (Bkz: Şekil 4)

![Şekil 4. Regresyon için etiketleme](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png) Şekil 4 '. Regresyon için etiketleme

Regresyon için etiketleme başvuru içeren bir hata noktası gerçekleştirilir. Ne kadar varlık hatasından önce da sıçramıştır bilmeden özelliği hesaplamasına mümkün değildir. Bu nedenle buna ikili sınıflandırma için varlıklar veri herhangi bir hata olmadan modelleme için kullanılamaz. Bu sorunu en iyi adlı başka bir istatistik teknik tarafından ele [yaşam analizi](https://en.wikipedia.org/wiki/Survival_analysis). Ancak, zaman açısından değişkenlik gösteren verilerin sık aralıklarla ile ilgili PdM kullanım örnekleri için bu tekniği uygularken olası zorluklar ortaya çıkabilir. Yaşam analizi hakkında daha fazla bilgi için bkz. [bu bir çağrı](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Tahmine dayalı bakım için çok sınıflı sınıflandırma
Çok sınıflı sınıflandırma teknikleri PdM çözümlerinde iki senaryo için kullanılabilir:
- Tahmin _iki gelecekteki sonuçları_: ilk sonuç elde edilir _başarısızlık zaman aralığı_ bir varlık için. Varlık, birden çok olası süreler birine atanır. İkinci sonuç bir dönem için son hata olasılığını bulunduğu _birden çok kök biri neden_. Bu tahmin belirtileri ve planı bakım zamanlamaları için izlemek bakım ekibi sağlar.
- Tahmin _en olası kök nedeni_ belirli bir hata. Bu sonucu doğru bir hatasını düzeltmek için bakım eylemleri kümesini önerir. Temel nedenler ve önerilen onarım kişilerinin sıralı bir listesi, bir hatadan sonra onarım eylemlerini öncelik teknisyenleri yardımcı olabilir.

#### <a name="label-construction-for-multi-class-classification"></a>Çok sınıflı sınıflandırma için etiket oluşturma
Burada Soru: "bir varlık sonraki başarısız olduğunu belirten olasılığı nedir _nZ_ zaman birimlerinin burada _n_ nokta sayısı?" Bu soruyu cevaplamak için demet süre (3Z 2Z, Z) kullanarak bir varlığın arıza öncesinde nZ kayıtları etiketleyin. Etiket diğer tüm kayıtları "normal" (etiket = 0). Bu yöntemde, hedef değişken tutar _kategorik_ değerleri. (Bkz. Şekil 5).

![Şekil 5. Hata zamanı tahmin çok sınıflı sınıflandırma etiketleri](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png) Şekil 5 '. Hata zaman tahmini için çok sınıflı sınıflandırma etiketleme

Burada Soru: "varlığı sonraki başarısız olduğunu belirten olasılığı nedir kök nedeni/sorunu nedeniyle zaman birimlerinin X _P<sub>miyim</sub>_ ?" Burada _miyim_ olası nedenlerini sayısıdır. Bu soru, bir varlığın arıza öncesinde etiketini X kayıtları yanıtlamak için "kök nedenden dolayı başarısız üzere _P<sub>miyim</sub>_ " (etiket = _P<sub>miyim</sub>_ ). "Normal" olarak tüm kayıtları etiket (etiket = 0). Bu yöntemde, etiketleri kategorik (bkz. Şekil 6) ayrıca.

![Şekil 6. Kök neden çok sınıflı sınıflandırma etiketleri tahmin](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png) Şekil 6. Kök nedeni tahmin için çok sınıflı sınıflandırma etiketleme

Bir hata olasılığı nedeniyle her model atar _P<sub>miyim</sub>_  olasılık hiç hatasının yanı sıra. Bu olasılıklar gelecekte ortaya en olası sorunları tahmin izin vermek için büyüklük sıralanabilir.

Burada soru şudur: "Bakım eylemleri bir hatadan sonra önerilir?" Bu soruyu yanıtlamak için etiketleme _seçilecek gelecekteki bir gelecekte gerekmez_, model hatası gelecekte tahmin etmektir değil. Bu yalnızca en olası kök nedeni tahmin etmektir _hata zaten oluştuktan sonra_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Eğitim, doğrulama ve Tahmine dayalı bakım için test yöntemleri
[Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) tam bir modeli eğitme test doğrulama döngüsü kapsamını sağlar. Bu bölümde, PdM için benzersiz yönleri açıklanmaktadır.

### <a name="cross-validation"></a>Çapraz doğrulama
Amacı [çapraz doğrulama](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) "eğitim aşamasında modeli test etmek için" bir veri kümesini tanımlamaktır. Bu veri kümesi olarak adlandırılan _doğrulama kümesi_. Bu teknik yardımcı sınırı sorunları gibi _overfitting_ ve model bağımsız bir veri kümesine nasıl generalize üzerinde bir öngörü sağlar. Diğer bir deyişle, gerçek bir sorunu olabilir bir bilinmeyen bir veri kümesi. Eğitim ve test rutin PdM için daha iyi görünmeyen gelecekteki veri genelleştirmek için zaman çeşitli yönlerini dikkate almanız gerekir.

Birçok makine öğrenimi algoritmaları model performansını önemli ölçüde değiştirebilirsiniz hiperparametreleri sayısına bağlıdır. Bu hiperparametreleri en iyi değerlerini otomatik olarak modeli eğitimindeki hesaplanan değil. Veri bilimi insanı belirtilmelidir. Hiperparametreleri iyi değerlerini bulma birkaç yolu vardır.

En yaygın biridir _k mızın çapraz doğrulama_ örnekler rastgele halinde böler _k_ hatları. Öğrenme algoritmasını her hiperparametreleri değerleri kümesi için çalıştırma _k_ kez. Her yinelemede, örneklerin geçerli kat doğrulama kümesi ve örneklerin geri kalanında bir eğitim kümesi olarak kullanın. Eğitim örnekler algoritmasını eğitmek ve performans ölçümlerini doğrulama örnekler işlem. Bu döngüye sonunda, ortalama işlem _k_ performans ölçümleri. Her hiper parametre değerleri kümesi için en iyi ortalama performans sahip olanları seçin. Hiperparametreleri seçme genellikle doğası gereği Deneysel bir görevdir.

PdM sorunları, verileri çeşitli veri kaynaklarından gelen olayları zaman serisi olarak kaydedilir. Bu kayıtlar etiketleme zaman göre sıralı olabilir. Bu nedenle, veri kümesini bölmeniz _rastgele_ eğitim ve doğrulama kümesine _eğitim örneklerden bazıları zaman içinde bazı doğrulama örnekleri sonraki olabilir_. Gelecekteki performans hiper parametre değerlerinin tahmini gelen bazı verileri temel alarak _önce_ modeli eğitilir. Bu tahminler zaman serisi özellikle sabit değildir ve zaman içinde gelişse aşırı iyimser olabilir. Sonuç olarak, seçilen hiper parametre değerleri yetersiz olabilir.

Eğitim ve doğrulama kümesindeki örneklerde bölmek için önerilen yoldur bir _zamana bağımlı_ tüm doğrulama örnekler olduğu zaman tüm eğitim örnekleri sonraki bir şekilde. Her hiper parametre değerleri kümesi için algoritma eğitim veri kümesi üzerinde eğitin. Modelin performans aynı doğrulama küme üzerinde ölçün. En iyi performans gösteren hiper parametre değerlerini seçin. Rastgele çapraz doğrulama tarafından seçmiş değerlerle daha iyi gelecekteki model performansını train/doğrulama bölünmüş sonucu olarak seçilen hiper parametre değerleri.

Son modelin tüm eğitim veriler üzerinde en iyi hiper parametre değerlerini kullanarak bir öğrenme algoritması eğitim tarafından oluşturulabilir.

### <a name="testing-for-model-performance"></a>Model performansını test etme
Yeni veri çubuğunda gelecekteki performansını tahmini bir model oluşturulduktan sonra gereklidir. İyi bir performans ölçümü, Hiper parametre değerlerini doğrulama küme üzerinde hesaplanan veya çapraz doğrulama bir ortalama performans ölçümü hesaplanan tahmin edin. Bu tahminler, çoğunlukla aşırı iyimser olur. İş, genellikle bunlar modeli test yönteminizi bazı ek yönergeler sahip olabilir.

Doğrulama, eğitim kursunda örnekler bölmek için PdM için önerilen yöntem olduğundan ve test veri kümelerine bir _zamana bağımlı_ şekilde. Tüm test örnekleri tüm eğitim ve doğrulama örnekler sürede daha sonra olmalıdır. Bölünmüş sonra daha önce açıklandığı gibi performansını ölçmek ve modeli oluşturur.

Zaman serisi sabit ve kolayca tahmin edilebilir olduğunda, rastgele ve zamana bağımlı yaklaşımları gelecekteki performans benzer tahminler oluşturur. Ancak zaman serisi ileti örneği olmayan ve/veya tahmin etmek zor olduğunda, zamana bağlı yaklaşım gelecekteki performansını daha gerçekçi tahminler oluşturur.

### <a name="time-dependent-split"></a>Zamana bağlı ayırma
Bu bölümde, zamana bağlı ayırma uygulamak için en iyi uygulamalar açıklanmaktadır. Eğitim ve test kümelerine arasında iki yönlü zamana bağımlı bölme aşağıda açıklanmıştır.

Bir akış gibi çeşitli sensörlerden alınan ölçümleri zaman damgalı olay varsayılır. Birden çok olay içeren zaman çerçeveleri özellikleri ve eğitim ve test örnekler etiketlerini tanımlayın. Örneğin, ikili sınıflandırma için, geçmiş olaylara göre özellikler oluşturun ve gelecekte "X" birim içinde gelecek olaylara göre Etiketler oluşturun (bkz. [özellik Mühendisliği](#feature-engineering) ve modelleme teknikleri bölümlerine bakın). Bu nedenle, bir örnek etiketleme zaman diliminde özelliklerini zaman diliminde daha sonra gelir.

Zamana bağlı ayırma için çekme bir _kesme zamanı T eğitim<sub>c</sub>_  hiperparametreleri T kadar geçmiş verileri kullanarak ayarlanmış olan bir modeli eğitmek withintext<sub>c</sub>. T gelecekteki etiketleri sızdırılmasını önlemek için<sub>c</sub> eğitim verileri, etiket eğitim örnekleri X olması için en yeni saati seçin T önce birimleri<sub>c</sub>. Şekil 7'de gösterilen örnekte, bir kayıt özellikleri ve etiketleri yukarıda açıklanan şekilde burada hesaplanır veri kümesindeki her kare temsil eder. Şekil X = 2, W = 3 test etme ve eğitim gideceğine kayıtları gösterir:

![Şekil 7. Zamana bağlı ikili sınıflandırma için bölme](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png) Şekil 7. İkili sınıflandırma için zamana bağımlı Böl

Yeşil kareler eğitim için kullanılan zaman birimi ait kayıtları temsil eder. Daha önce göz önünde bulundurularak her eğitim örnek oluşturulan özellik oluşturma için üç nokta ve iki gelecek dönemlere önce T etiketleme<sub>c</sub>. İki gelecek dönemlere herhangi bir bölümünü T dışında olduğunda<sub>c</sub>, T dışında hiçbir görünürlük varsayıldığından eğitim veri kümesi, örnek dışlama<sub>c</sub>.

Siyah kareler kayıtlarını yukarıdaki kısıtlama verilen eğitim veri kümesinde kullanılmamalıdır son etiketli veri kümesini temsil eder. Bu kayıtları da T önce olduğundan veri testinde kullanılmayacak<sub>c</sub>. Ayrıca, etiketleme zaman çerçevelerine kısmen eğitim zaman karesinde ideal değil bağlıdır. Verileri eğitim ve test çerçevelerini ayrı ayrı etiketleme zaman etiketi bilgi sızdırılmasını önlemek için sahip olması gerekir.

Şu ana kadar bahsedilen teknik eğitim ve test T yakın zaman damgalarına sahip örnekler arasında örtüşme sağlayan<sub>c</sub>. Daha fazla ayrılmasını elde etmek için bir çözüm içinde t W zaman birimleri örnekler dışlamaktır<sub>c</sub> testten ayarlayın. Ancak, agresif bir bölünmüş bol miktarda veri kullanılabilirliğine bağlıdır.

RUL tahmin etmek için kullanılan regresyon modellerini daha ciddi bir şekilde sızıntı sorundan etkilenen. Rastgele split yöntemini kullanarak, aşırı fazla sığdırma yol açar. Regresyon sorunlar için bölme olmalıdır şekilde T önce hataları varlıklarla ait kayıtları<sub>c</sub> eğitim kümesine gidin. Kesme sonra hataları olan varlıklar kayıtlarını test kümesine gidin.

Eğitim ve test için veri bölmek için başka bir en iyi uygulama varlığı kimliğe göre bölme kullanmaktır. Eğitim kümesinde kullanılan varlıkları hiçbiri modeli performans testinde kullanılan şekilde bölme olmalıdır. Bu yaklaşımı kullanarak bir model ile yeni varlıkları daha gerçekçi sonuçları sağlamak daha iyi belirtme şansı olur.

### <a name="handling-imbalanced-data"></a>İmbalanced verileri işleme
Bir sınıf başkalarının, daha fazla örnek varsa sınıflandırma sorunları, veri kümesinin olduğu söylenir _imbalanced_. İdeal olarak, her sınıfta eğitim verileri, yeterli temsilcileri, farklı sınıflar arasında ayrım etkinleştirmek için tercih edilen. Bir sınıf % 10'dan az veri varsa veri imbalanced olarak kabul edilir. Bulamamış sınıfı olarak adlandırılan bir _azınlık sınıfı_.

PdM karşılaşılan birçok sorun diğer sınıf veya sınıflar için karşılaştırıldığında burada bir sınıf ciddi bir şekilde bulamamış gibi imbalanced veri kümeleri, yüz tanıma. Bazı durumlarda, yalnızca %0,001 toplam veri noktası azınlık sınıfı oluşturan. Sınıf dengesizliği PdM için benzersiz değil. Hataları ve anomalileri nadir oluşum olduğu diğer etki alanları, örnekler, sahtekarlık algılama ve ağa yetkisiz erişim için benzer bir sorun karşı karşıyadır. Bu hatalar azınlık sınıf örnekleri yapın.

Bunlar genel hata oranının en aza indirmeyi amaçlamanız beri verilerde sınıf dengesizliği ile birçok standart öğrenme algoritmalarını performansını, güvenliği aşıldı. %99 negatif ve pozitif %1 örnek bir veri kümesi için tüm örnekleri negatif olarak etiketleme tarafından % 99 doğruluğunu sağlamak için bir model gösterilebilir. Ancak modeli yanlış pozitif tüm örnekler sınıflandırır. doğruluğunun yüksek olsa bile, bu nedenle algoritma yararlı bir değildir. Sonuç olarak, geleneksel değerlendirme gibi ölçümler _hata fiyat genel doğruluğu_ imbalanced öğrenme için yeterli değil. İmbalanced veri kümeleri ile karşı karşıya kalındığında, diğer ölçümleri modeli değerlendirme için kullanılır:
- Duyarlık
- Geri çekme
- F1 puanları
- Ayarlanmış maliyeti ROC (alıcı çalıştırma özellikleri)

Bu ölçümler hakkında daha fazla bilgi için bkz. [model değerlendirme](#model-evaluation).

Ancak, sınıf onarmak yardımcı bazı yöntemler vardır dengesizliği sorun. İki önemli olanlardır _teknikleri örnekleme_ ve _hassas öğrenme maliyet_.

#### <a name="sampling-methods"></a>Örnekleme yöntemi
İmbalanced learning eğitim veri kümesi için dengeli bir veri kümesini değiştirmek için yöntemleri örnekleme kullanımını içerir. Örnekleme test kümesine değil uygulanacak yöntemlerdir. Çeşitli örnekleme teknikler olsa da, çoğu anlaşılır olanlardır _rastgele oversampling_ ve _örnekleme altında_.

_Rastgele oversampling_ rastgele oluşturulmuş bir örnek azınlık sınıftan seçerek, bu örnekler çoğaltma ve eğitim veri kümesi için eklemeden içerir. Sonuç olarak, azınlık sınıf örnekleri sayısı artar ve sonunda farklı sınıfların örnekleri sayısını dengelemek. Oversampling bir dezavantajı, bazı örnekleri birden çok örneğini atlayarak sığdırma önde gelen çok belirli olacak sınıflandırıcı neden olabilir ' dir. Model eğitim yüksek doğruluk gösterebilir, ancak performansını görünmeyen test veri çubuğunda yetersiz olabilir.

Buna karşılık, _örnekleme altında rastgele_ rastgele oluşturulmuş bir örnek, bir temel sınıftan seçme ve bu örnekler eğitim veri kümesinden kaldırılıyor. Ancak, çoğu sınıf örnekleri kaldırma çoğu sınıf için ilgili önemli kavramları gözden kaçırabilirsiniz sınıflandırıcı neden olabilir. _Karma örnekleme_ burada azınlık sınıfı aşırı örneklenen ve çoğu sınıf altında aynı anda alt örneklenen zaman başka bir uygun bir yaklaşımdır.

Birçok gelişmiş örnekleme teknikler vardır. Veri özellikleri ve veri Bilimcisi tarafından yinelemeli denemeleri sonuçlarını seçilen teknik bağlıdır.

#### <a name="cost-sensitive-learning"></a>Hassas öğrenme maliyeti
PdM içinde azınlık sınıfı oluşturan normal örnekler değerinden daha fazla ilgi hatalarıdır. Bu nedenle hatalarda algoritması'nın performansına çoğunlukla biridir. Hatalı pozitif bir sınıf negatif bir sınıf olarak tahmin etmeye yönelik birden çok tersi maliyeti olabilir. Bu durum genellikle farklı sınıfları için eşit kaybı veya asimetrik maliyetini yanlış sınıflandırma öğeler olarak adlandırılır. İdeal sınıflandırıcı yüksek tahmin doğruluğunu çoğunluğu sınıfı kesinliğini ödün vermeden azınlık sınıfı teslim.

Bu dengeyi elde etmek için birden çok yolu vardır. Sorun eşit kaybı riskini azaltmak için yüksek bir maliyet azınlık sınıfın hatalı sınıflandırmayla atayın ve ilişkin genel maliyeti en aza indirmek deneyin. Algoritmalar ister _SVMs (Destek vektör makineler)_ bu yöntem, doğası gereği, pozitif ve negatif örnekleri, eğitim sırasında belirtilmelidir maliyetini vererek benimseyin. Benzer şekilde, yöntemler gibi artırma _artırılmış karar ağaçları_ genellikle imbalanced verilerle iyi performans gösterir.

## <a name="model-evaluation"></a>Modeli değerlendirme
Hatalı Sınıflandırma, yanlış alarm işletmeye maliyeti yüksek olduğu PdM senaryolar için önemli bir sorundur. Örneğin, altyapısı hata yanlış bir öngörü üzerinde temel Uçağın yerde kararını zamanlamaları dengesini bozarsınız hem seyahat planlarını. Bir makineden çevrimdışı bir montaj hattı alma gelir kaybına yol açabilir. Bu nedenle yeni test verilerini karşı doğru performans ölçümleri ile model değerlendirme kritik öneme sahiptir.

PdM modelleri değerlendirmek için kullanılan tipik bir performans ölçümleri, aşağıda ele alınmıştır:

- [Doğruluk](https://en.wikipedia.org/wiki/Accuracy_and_precision) sınıflandırıcının performans tanımlamak için kullanılan en popüler ölçüm olan. Ancak doğruluk veri dağıtımları için duyarlıdır ve verimsiz bir ölçüdür imbalanced veri kümeleriyle senaryolar için. Diğer ölçümleri yerine kullanılır. Gibi araçları [karışıklık matrisi](https://en.wikipedia.org/wiki/Confusion_matrix) işlem ve model doğruluğunu hakkında nedeni için kullanılır.
- [Duyarlık](https://en.wikipedia.org/wiki/Precision_and_recall) PdM yanlış alarm oranını modelleri ilgilidir. Daha düşük precision modelinin genellikle atamasından hatalı karşılık gelir.
- [Geri çağırma](https://en.wikipedia.org/wiki/Precision_and_recall) oranı test kümesindeki hataları ne kadar doğru model tarafından tanımlanan gösterir. Daha yüksek bir geri çağırma oranları modelin gerçek kod hataları tanımlamak başarılı olduğu anlamına gelir.
- [F1 puanı](https://en.wikipedia.org/wiki/F1_score) duyarlık ve değeriyle 0 (kötü) ile 1 (iyi) arasında değişen bir geri çağırma harmonic ortalamadır.

İkili sınıflandırma için
- [Eğrileri (ROC) işletim alıcı](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) de popüler bir unsurdur. Model performansını ROC Eğriler bir sabit işletim noktası üzerinde ROC göre yorumlanır.
- Ancak PdM sorunları _decile tabloları_ ve _grafikleri lift_ daha bilgilendirici olan. Bunlar yalnızca pozitif sınıfta (hata) odaklanın ve algoritması performans, daha karmaşık bir resmini ROC eğrileri daha sağlar.
  - _Decile tabloları_ test örnekleri bir hata olasılığını azalan sırada kullanılarak oluşturulur. Sıralı örnekleri ardından deciles gruplandırılır (10 oranında olasılığı en yüksek olan bir örnek daha sonra % %20 30 ve benzeri). Her decile için oranı (gerçek pozitif sonuç oranı) /(random baseline) her decile algoritması performansı tahmin etmenize yardımcı olur. Rastgele taban 0.1, 0.2 vb. değerlerine göre alır.
  - [Grafikler lift](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) decile gerçek pozitif sonuç oranı tüm deciles için rastgele gerçek pozitif sonuç oranına karşılık çizim. En büyük kazanç gösterdikleri ilk deciles genellikle odağı sonucu olduğundan. İlk deciles ayrıca "riskli", için temsili olarak PdM için kullanıldığında görülebilir.

## <a name="model-operationalization-for-predictive-maintenance"></a>Tahmine dayalı bakım için modeli kullanıma hazır hale getirme

Veri bilimi alıştırmadır avantajı, yalnızca, eğitilen model işletimsel yapılan gerçekleşmiş. Diğer bir deyişle, modelin yeni, daha önce görünmeyen verilerine dayalı olarak tahminlerde bulunmak üzere iş sistemleri uygulamasına dağıtılması gerekir.  Yeni verileri tam olarak uymalıdır _modeli imza_ iki yolla eğitilen modelin:
- Tüm özellikleri yeni veriler her mantıksal örneğinde (örneğin bir tablosunda bir satıra) bulunması gerekir.
- Yeni verilerin önceden işlenmesi gerekir ve her bir özelliğin, eğitim verileri tam olarak aynı şekilde tasarlanmıştır.

Yukarıdaki işlem, akademik ve sektör belgeleri Birçok bakımdan belirtilir. Ancak, aşağıdaki ifadeleri de aynı anlama gelir:
- _Yeni verileri puanlamak_ modelini kullanma
- _Model geçerli_ yeni veriler için
- _Kullanıma hazır hale getirme_ modeli
- _Dağıtma_ modeli
- _Çalıştırmayı_ yeni verilere karşı

Daha önce belirtildiği gibi PdM için modeli kullanıma hazır hale getirme eşlerine farklıdır. Anomali algılama ve hata algılama genellikle ilgili senaryolarını uygulayan _çevrimiçi Puanlama_ (olarak da adlandırılan _gerçek zamanlı Puanlama_). Burada, modeli _puanları_ gelen her kaydı ve tahmin döndürür. Anomali algılama için tahmini bir anomali ortaya çıktığını göstergesidir (örnek: bir sınıf SVM). Hata algılama için türü ya da hata sınıfı olacaktır.

Buna karşılık, PdM içerir _toplu Puanlama_. Model imza uymak için yeni verileri özelliklerinde eğitim verilerini aynı şekilde mühendislik gerekir. Yeni veriler için tipik olan büyük veri kümeleri için özelliklerini zaman pencereleri toplanır ve toplu işlemde puanlanması. Toplu Puanlama tipik olarak yapıldığı gibi dağıtılmış sistemlerdeki [Spark](https://spark.apache.org/) veya [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Birkaç alternatifleri - yetersiz hem de vardır:
- Akış veri altyapıları, bellek içinde windows üzerinde toplama destekler. Bu nedenle çevrimiçi Puanlama destekledikleri tartışılabilir. Ancak bu sistemler üzerinde daha geniş windows için saat veya seyrek öğe dar Windows yoğun veri uygundur. Bunlar için de yoğun veri geniş zaman pencereleri PdM senaryolarında görülen ölçeği değil.
- Toplu Puanlama kullanılabilir değilse, aynı anda küçük partiler halinde yeni verileri işlemek için çevrimiçi Puanlama uyum çözümüdür.

## <a name="solution-templates-for-predictive-maintenance"></a>Tahmine dayalı bakım çözüm şablonları

Bu kılavuz son bölümü PdM çözüm şablonları, öğreticiler ve Azure'da gerçekleştirilen denemeleri listesini sağlar. Bazı durumlarda dakikalar içinde bir Azure aboneliğine bu PdM uygulamaları dağıtılabilir. Kavram kanıtı tanıtımlar sanal alternatifleri veya Hızlandırıcılar gerçek üretim uygulamaları için deneme amaçlı olarak kullanılabilir. Bu şablonlar bulunan [Azure AI Gallery](https://gallery.azure.ai) veya [Azure GitHub](https://github.com/Azure). Bu farklı örnekleri, zaman içinde bu çözüm şablonu alınacaktır.

| # | Başlık | Açıklama |
|--:|:------|-------------|
| 2 | [Azure Tahmine dayalı bakım çözüm şablonu](https://github.com/Azure/AI-PredictiveMaintenance) | ML model ve eksiksiz bir Azure altyapı Tahmine dayalı bakım senaryolarını IOT Uzaktan izleme bağlamında destekleyebildiğini gösterir ve açık kaynaklı çözüm şablonu. |
| 3 | [Tahmine dayalı bakım için derin öğrenme](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure not defteri ile Tahmine dayalı bakım için LSTM (uzun kısa vadeli bellek) ağları (yinelenen sinir ağları sınıfı) kullanarak bir demo çözümüyle bir [Bu örnek Web günlüğü gönderisini](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [R ile Tahmine dayalı bakım modelleme Kılavuzu](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | R betiklerini ile PdM modelleme Kılavuzu|
| 5 | [Azure, Havacılık için Tahmine dayalı bakım](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Azure ML v1.0 uçak bakım göre ilk PdM çözüm şablonlarından biri. Bu kılavuz, bu projeden geldiğini. |
| 6 | [IOT Edge için Azure yapay ZEKA Araç Seti](https://github.com/Azure/ai-toolkit-iot-edge) | Yapay ZEKA TensorFlow kullanarak IOT edge'de; Araç Seti paketler derin öğrenme modellerini Azure IOT Edge ile uyumlu Docker kapsayıcılarında ve bu modelleri REST API'leri olarak kullanıma sunar.
| 7 | [Azure IOT Tahmine dayalı bakım](https://github.com/Azure/azure-iot-predictive-maintenance) | Bilgisayarlar - Azure IOT paketi önceden yapılandırılmış çözümü. IOT paketi ile uçak bakım PdM şablonu. [Başka bir belgede](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) ve [izlenecek](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) aynı projeye ilgili. |
| 8 | [SQL Server R Services kullanarak Tahmine dayalı bakım şablonu](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | R hizmetlerini temel alarak kalan faydalı ömrü senaryo Tanıtımı. |
| 9 | [Tahmine dayalı bakım modelleme Kılavuzu](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Uçak bakım veri kümesi özelliği ile R kullanarak mühendislik [denemeleri](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) ve [veri kümeleri](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) ve [Azure not defteri](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) ve [denemeleri](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)AzureML v1.0,|

## <a name="training-resources-for-predictive-maintenance"></a>Tahmine dayalı bakım için eğitim kaynakları

Microsoft Azure, PdM teknikleri, içerik ve yapay ZEKA kavramları ve uygulama Genel eğitim yanı sıra temel kavramları öğrenme yollarını sunar.

| Eğitim kaynağı  | Kullanılabilirlik |
|:-------------------|--------------|
| [PdM ağaçları ile rastgele orman için öğrenme yolu](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Genel | 
| [PdM kullanarak derin öğrenme için öğrenme yolu](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Genel |
| [Azure'da yapay ZEKA geliştiricisi](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Genel |
| [Microsoft yapay ZEKA Okul](https://aischool.microsoft.com/learning-paths) | Genel |
| [Github'dan Azure yapay ZEKA öğrenme](https://github.com/Azure/connectthedots/blob/master/readme.md) | Genel |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Genel |
| [Microsoft AI YouTube web seminerleri](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Genel |
| [Microsoft yapay ZEKA Göster](https://channel9.msdn.com/Shows/AI-Show) | Genel |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | İş Ortakları |
| [Microsoft İş Ortağı Ağı](https://learningportal.microsoft.com) | İş Ortakları |

Ayrıca, yapay ZEKA MOOCS (açık çevrimiçi kurslara) ücretsiz Stanford ve MIT gibi akademik kurumları tarafından çevrimiçi sunulan ve diğer eğitim şirketlerdir.
