---
title: Tahmine dayalı bakım çözümleri için Azure AI Kılavuzu-Team Data Science süreci
description: Tahmine dayalı bakım çözümlerini birden çok dikey sektörde güçlendirir veri bilimi hakkında kapsamlı bir açıklama.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 9b6cf2abfa797965d75bacd0b9c5e2eaf02b57a4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308583"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Tahmine dayalı bakım çözümleri için Azure AI Kılavuzu

## <a name="summary"></a>Özet

Tahmine dayalı bakım (**PDM**), çeşitli sektörlerdeki işletmelerin operasyonel maliyetlerde yüksek miktarda varlık kullanımı ve tasarruf elde etmelerini sağlayan, tahmine dayalı analizler için popüler bir uygulamadır. Bu kılavuz, [MICROSOFT Azure AI platformu](https://azure.microsoft.com/overview/ai-platform) teknolojisini kullanarak PDM çözümlerini başarıyla geliştirmek ve dağıtmak için iş ve analitik yönergeleri ve en iyi yöntemleri sunar.

Başlangıçlara yönelik bu kılavuz, sektöre özgü iş senaryolarını ve bu PdM için bu senaryoları nitelendirme sürecini tanıtır. PdM çözümleri derlemek için veri gereksinimleri ve modelleme teknikleri de sağlanır. Kılavuzun ana içeriği veri bilimi sürecinde, veri hazırlama, özellik Mühendisliği, model oluşturma ve model operationalization adımları dahil olmak üzere. Bu anahtar kavramlarını tamamlamak için bu kılavuzda, PdM uygulama geliştirmeyi hızlandırmaya yardımcı olacak bir dizi çözüm şablonu listelenir. Kılavuz, veri bilimi 'nin arkasındaki AI hakkında daha fazla bilgi edinmek için kullanışlı eğitim kaynaklarına da işaret eder. 

### <a name="data-science-guide-overview-and-target-audience"></a>Veri bilimi kılavuzuna genel bakış ve hedef kitle
Bu kılavuzun ilk yarısında tipik iş sorunları, bu sorunları çözmek için PdM uygulama avantajları ve bazı yaygın kullanım durumları listelenmektedir. İş karar mekanizmaları (BDMs), bu içerikten faydalanır. İkinci yarısında PdM arkasındaki veri bilimi açıklanmakta ve bu kılavuzda özetlenen ilkeler kullanılarak oluşturulan PdM çözümlerinin bir listesi verilmektedir. Eğitim malzemesine yönelik öğrenme yolları ve işaretçiler de sağlar. Teknik karar mekanizmaları (TDMs) bu içeriği yararlı bulacak.

| Başlangıç... | Eğer........ |
|:---------------|:---------------|
| [Tahmine dayalı bakım için iş durumu](#business-case-for-predictive-maintenance) |kapalı kalma süresi ve işlem maliyetlerini azaltmak ve ekipmanın kullanımını geliştirmek için bir iş karar Oluşturucu (BDM) |
| [Tahmine dayalı bakım için veri bilimi](#data-science-for-predictive-maintenance) |tahmine dayalı bakım için benzersiz veri işleme ve AI gereksinimlerini anlamak üzere PdM teknolojilerini değerlendiren teknik karar Oluşturucu (TDM) |
| [Tahmine dayalı bakım için çözüm şablonları](#solution-templates-for-predictive-maintenance)|hızlı bir şekilde tanıtım veya kavram kanıtı sağlamak isteyen bir yazılım mimarı veya AI geliştiricisi |
| [Tahmine dayalı bakım için eğitim kaynakları](#training-resources-for-predictive-maintenance) | Yukarıdakilerden herhangi biri veya tümü ve veri bilimi, araçları ve tekniklerinin arkasındaki temel kavramları öğrenmek ister.

### <a name="prerequisite-knowledge"></a>Önkoşul bilgileri
BDM içeriği okuyucunun önceki bir veri bilimi bilgisine sahip olmasını beklemez. TDM içeriği için, istatistik ve veri bilimi hakkında temel bilgiler yararlıdır. Azure veri ve AI Hizmetleri, Python, R, XML ve JSON hakkında bilgi önerilir. AI teknikleri Python ve R paketlerinde uygulanır. Çözüm şablonları, Azure Hizmetleri, geliştirme araçları ve SDK 'lar kullanılarak uygulanır.

## <a name="business-case-for-predictive-maintenance"></a>Tahmine dayalı bakım için iş durumu

İşletmeler, büyük yatırımların geri dönmesini sağlamak üzere yoğun verimlilik ve kullanım açısından kritik bir ekipman gerektirir. Bu varlıklar uçak altyapılarından, türlerden, yükselmcilere veya endüstriyel chil'e kadar değişebilir. bu maliyet, milyonlarca aşağı doğru photobers, kahve makineleri veya su coolers gibi gündelik gereçilere kadar ücretlendirilir.
- Varsayılan olarak, çoğu işletme, parçaların değiştirildiği ve başarısız olduğu durumlarda _Düzeltme bakımını_ kullanır. Düzeltici bakım, parçaların tamamen kullanılmasını sağlar (Bu nedenle, bileşen ömrünü boşa almaya gerek kalmaz), ancak iş kapalı kalma süresi, işgücü ve zamanlanmamış bakım gereksinimleri (kapalı saatler veya uygun olmayan konumlar).
- Bir sonraki düzeyde, işletmeler, bir bölüm için yararlı ömrü tespit ettikleri ve bir hatadan önce BT 'nin yerini alacak veya yerine geçecek şekilde  _bakım_ yapma alıştırması sağlar. Önleyici bakım zamanlanmamış ve çok önemli hatalardan kaçınır. Ancak, zamanlanan kapalı kalma süresi, bileşenin kullanım ömrü boyunca kullanılması ve işgücü hala devam etmektedir.
- Tahmine _dayalı bakımın_ hedefi, bileşenlerin _yalnızca yerinde_ değiştirilmesini etkinleştirerek, düzeltici ve önleyici bakım arasındaki dengeyi en uygun hale getirmektir. Bu yaklaşım yalnızca bir hataya yaklaştıklarında bu bileşenlerin yerini alır. Bileşen kullanım alanlarını genişleterek (önleyici bakım ile karşılaştırıldığında) ve zamanlanmamış bakım ve işgücü maliyetlerini azaltarak (düzeltici bakımda), işletmeler maliyet tasarrufu ve rekabet avantajları elde edebilir.

## <a name="business-problems-in-pdm"></a>PdM 'de iş sorunları
İşletmeler, beklenmeyen hatalardan dolayı yüksek operasyonel risk taşır ve karmaşık sistemlerdeki sorunların kök nedenine ilişkin sınırlı öngörüler sağlar. Önemli iş sorularından bazıları şunlardır:

- Ekipman veya sistem performansı ya da işlevselliği hakkındaki anormallikleri tespit edin.
- Bir varlığın yakın gelecekte başarısız olup olmadığını tahmin edin.
- Bir varlığın kalan faydalı ömrünü tahmin edin.
- Bir varlık hatasının ana nedenlerini belirler.
- Bir varlık üzerinde ne zaman, ne zaman yapılacağını belirlemek için gereken bakım eylemlerini belirler.

PdM ' dan tipik hedef deyimler şunlardır:

- Görev açısından kritik ekipmanların işletimsel riskini azaltın.
- Arızalardan önce hatalara karşı tahmin yaparak varlıkların dönüş oranını artırın.
- Tam zamanında bakım işlemlerini etkinleştirerek bakım maliyetini denetleyin.
- Daha düşük müşteri attri, marka görüntüsünü geliştirme ve kayıp satış.
- Yeniden düzenleme noktasını tahmine göre stok düzeylerini azaltarak stok maliyetlerini düşürün.
- Çeşitli bakım sorunlarına bağlı desenleri bulur.
- Varlık koşulları için sistem durumu puanları gibi KPI 'Leri (ana performans göstergeleri) sağlayın.
- Varlıkların kalan kullanım süresini tahmin edin.
- Bakım etkinliklerini zamanında öneririz.
- Parçaların yerine geçecek sıralama tarihlerini tahmin ederek yalnızca zaman envanterini etkinleştirin.

Bu hedef deyimler şunlar için başlangıç noktalardır:

- _veri bilimcileri_ , belirli tahmine dayalı sorunları çözümleyip çözmelidir.
- _bulut mimarları ve geliştiricilerin_ uçtan uca çözümü bir araya koymasını sağlar.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Tahmine dayalı bakım için uygun sorunlar
Tüm kullanım durumlarının veya iş sorunlarının PdM tarafından etkin bir şekilde çözülemediğinden emin olmak önemlidir. Sorun seçimi sırasında göz önünde bulundurulması gereken üç önemli ölçüt vardır:

- Sorunun, doğası halinde tahmine dayalı olması gerekmez; Yani tahmin edilecek bir hedef veya sonuç olmalıdır. Bu sorun, algılandıklarında hata oluşmasını engellemek için bir eylemin açık yolunu da içermelidir.
- Sorun, _hem iyi hem de hatalı sonuçları_ içeren donanımların işletimsel geçmişinin bir kaydına sahip olmalıdır. Hatalı sonuçları azaltmak için uygulanan eylemler kümesi, bu kayıtların bir parçası olarak da kullanılabilir olmalıdır. Hata raporları, performans düşüşüne yönelik bakım günlükleri, onarım ve değiştirme günlükleri de önemlidir. Bunlara ek olarak, bunları geliştirmek için yapılan onarımlar ve değiştirme kayıtları da yararlıdır.
- Kayıtlı geçmiş, kullanım durumunu desteklemeye _yetecek kadar kalite_ gerektiren _ilgili_ verilere yansıtılmalıdır. Veri ilgisi ve sufficiency hakkında daha fazla bilgi için bkz. tahmine [dayalı bakım Için veri gereksinimleri](#data-requirements-for-predictive-maintenance).
- Son olarak, işin sorunu açık bir şekilde anlayabilen etki alanı uzmanlarına sahip olması gerekir. Analist 'in verileri anlamasına ve yorumlamasına yardımcı olabilecek iç süreçler ve uygulamalardan haberdar olmaları gerekir. Ayrıca, sorunlara yönelik doğru verileri toplamaya yardımcı olması için mevcut iş işlemlerinde gerekli değişiklikleri yapabilmeleri gerekir.

## <a name="sample-pdm-use-cases"></a>Örnek PdM kullanım örnekleri
Bu bölümde, örneğin, Aerospace, yardımcı programlar ve taşıma gibi çeşitli sektörlerden PdM kullanım örneklerinin bir koleksiyonu ele alınmaktadır. Her bölüm bir iş sorunuyla başlar ve PdM 'in avantajlarını, iş sorununu çevreleyen ilgili verileri ve son olarak bir PdM çözümünün avantajlarını ele alır.

| İş sorunu | PdM avantajlarından yararlanın |
|:-----------------|-------------------|
|**Havacılık**      |                   |
|Mekanik sorunlar nedeniyle _Uçuş gecikmesi ve iptalleri_ . Zamanında onarılamayan arızalar, fışıkların iptal edilmesine ve zamanlama ve işlemleri kesintiye uğramasına neden olabilir. |PdM çözümleri, mekanik arızalara karşı gecikmekte olan veya iptal edilen bir uçak olasılığını tahmin edebilir.|
|_Uçak motoru bölüm hatası_: airi motoru bölüm değişiklikleri hava yolu sektördeki en yaygın bakım görevlerinin arasındadır. Bakım çözümleri, bileşen stok kullanılabilirliğinin, tesliminde ve planlamada dikkatli bir yönetim gerektirir|Bileşen güvenilirliği konusunda zeka bilgileri toplayabilmek, yatırım maliyetlerinde önemli ölçüde azalmaya yol açabilir.|
|**Finans** |                         |
|_ATM hatası_ , bankacılık sektörünün içindeki yaygın bir sorundur. Buradaki sorun, bir ATM nakit çekme işleminin, nakit dağıtıcısındaki bir kağıt sıkıştı veya bir arıza nedeniyle kesintiye uğratılacağını bildirmek için kullanılır. İşlem hatalarının tahminlerini temel alarak, arızaların meydana geldiğini önlemek için ATMs, proaktif olarak hizmet verebilir.| Makinenin bir işlem aracılığıyla yük devri yapmasına izin vermek yerine, istenen alternatif, makineyi tahmine göre hizmeti reddedecek şekilde programlemektir.|
|**Enerji** |                          |
|_Rüzgar türbin hatalarıyla_: Rüzgar türbinler, çevre açısından sorumlu ülkelerde/bölgelerde ana enerji kaynağıdır ve yüksek sermaye maliyetlerini içerir. Rüzgar türbinler içindeki bir anahtar bileşen, hata türbin verimsiz bir şekilde işleyen Oluşturucu motorunındır. Bu, düzeltilmesi de oldukça pahalıdır.|MTTF (başarısızlık süresi) gibi KPI 'Ları tahmin etmek enerji şirketlerinin türbin başarısızlıklarını engellemesine ve en düşük kapalı kalma süresini sağlamanıza yardımcı olabilir. Hata olasılıkların, teknisyenleri yakında başarısız olma olasılığı olan türlerden haberdar etmek ve zamana dayalı bakım Regimes zamanlamak için bilgi sağlayacaktır. Tahmine dayalı modeller hataya katkıda bulunan farklı faktörlerle ilgili öngörüler sağlar ve bu da teknisyenlerin sorunların temel nedenlerini daha iyi anlamasına yardımcı olur.|
|_Devre kesici sorunları: elektrik teslimatlarına_ ve işletmelere enerji dağıtımı sağlamak için güç çizgilerinin her zaman çalışır durumda olmasını gerektirir. Devre kesiciler, aşırı yükleme veya olumsuz hava durumu koşulları sırasında güç satırlarına zarar verme veya bu hatalara engel olma. Buradaki iş sorunu, devre kesici başarısızlıklarını tahmin etmek için kullanılır.| PdM çözümleri, onarım maliyetlerini azaltmaya ve devre kesicileri gibi donanımların kullanım süresini artırmaya yardımcı olur. Bunlar, beklenmeyen hataların ve hizmet kesintilerini azaltarak güç ağının kalitesini artırmaya yardımcı olur.|
|**Ulaşım ve lojistik** |    |
|_Asansör kapısı sorunları_: büyük asansör şirketleri dünyanın dört bir yanındaki milyonlarca işlevsel yükseltme için tam yığın hizmeti sağlar. Asansör güvenliği, güvenilirliği ve çalışma süresi müşterileri için başlıca kaygılardır. Bu şirketler, düzeltici ve önleyici bakımla yardımcı olmak için bu ve diğer diğer öznitelikleri algılayıcılar aracılığıyla izler. Bir Asansör ortamında, en belirgin müşteri sorunu, Asansör kapıdır. Bu durumda iş sorunu, kapı hatalarının olası nedenlerini tahmin eden bir bilgi tabanı tahmine dayalı uygulaması sağlamaktır.| Yükseltme olasılığı, 20-30 yıllık bir yılı için büyük yatırımlardır. Böylece her potansiyel satış yüksek düzeyde rekabet edebilir; Bu nedenle hizmet ve destek beklentileri yüksektir. Tahmine dayalı bakım, bu şirketlere kendi ürün ve hizmet tekliflerindeki rakiplerine yönelik bir avantaj sağlayabilir.|
|_Tekerlek arızaları_: tüm eğitim ve maliyet milyarlarının yarısını küresel RAIL sektörünün yarısı için tekerlek başarısızlığı hesabı. Tekerlek arızaları Ayrıca, bazen demiryolu 'in daha önce kesintiye neden olmasına neden olur. RAIL sonları, derailments gibi çok zararlı olaylara yol açabilir. Bu tür örneklerden kaçınmak için, railyöntemlere tekerlekleri performansını izler ve bunları önleyici bir şekilde değiştirin. Buradaki iş sorunu, tekerlek hatalarının tahminiyle ilgili bir sorundur.| Tekerlekler için tahmine dayalı bakım, tekerlekleri tam zamanında değiştirme konusunda yardımcı olacaktır |
|_Subway kapı başarısızlıklarını eğitme_: alt yönlü işlemlerdeki gecikmelerin ana nedeni, arabaların eğileme arızalarına yöneliktir. Buradaki iş sorunu, kapıların eğitim başarısızlıklarını tahmin etmek için kullanılır.|Bir kapı başarısızlığının erken olarak veya bir kapı arızası kadar gün sayısı, işletmenizin kapı bakım zamanlamalarına yönelik olarak iyileştirmesini sağlamaya yardımcı olur.|

Sonraki bölümde, yukarıda açıklanan PdM avantajlarının nasıl kullanılacağına ilişkin ayrıntılar yer alır.

## <a name="data-science-for-predictive-maintenance"></a>Tahmine dayalı bakım için veri bilimi

Bu bölüm, PdM için veri bilimi ilkelerine ve uygulamasına yönelik genel yönergeler sağlar. Bir TDM, çözüm mimarı veya geliştiricinin, PdM için uçtan uca AI uygulamaları oluşturmaya yönelik önkoşulları ve işlemleri anlamasına yardımcı olmak üzere tasarlanmıştır. Bu bölümü, tahmine [dayalı bakım Için çözüm şablonlarında](#solution-templates-for-predictive-maintenance)listelenen tanıtımlar ve kavram kanıtı şablonlarının incelenmesi ile birlikte okuyabilirsiniz. Daha sonra, bu ilkeleri ve en iyi yöntemleri kullanarak PdM çözümünüzü Azure 'a uygulayabilirsiniz.

> [!NOTE]
> Bu kılavuz, okuyucu veri bilimi öğretmek için tasarlanmamıştır. Tahmine [dayalı bakım için eğitim kaynakları](#training-resources-for-predictive-maintenance)bölümünde daha ayrıntılı bilgi için birkaç faydalı kaynak sunulmaktadır. Kılavuzda listelenen [Çözüm şablonları](#solution-templates-for-predictive-maintenance) , belirli PDM sorunları IÇIN Bu AI tekniklerinden bazılarını göstermektedir.

## <a name="data-requirements-for-predictive-maintenance"></a>Tahmine dayalı bakım için veri gereksinimleri

Tüm Öğrenlerin başarısı (a), ne kadar taöğretmekte olduğunu ve (b) öğrenimi özelliğini bağlıdır. Tahmine dayalı modeller geçmiş verilerden desenler öğrenmekte ve bu gözlemlenen desenleri temel alarak gelecekteki sonuçları belirli bir olasılığa karşı tahmin eder. Bir modelin tahmine dayalı doğruluğu, eğitim ve test verilerinin relet, sufficiency ve kalitesine bağlıdır. Bu model kullanılarak ' puanlanmış ' olan yeni verilerin eğitim/test verileriyle aynı özelliklere ve şemaya sahip olması gerekir. Yeni verilerin Özellik özellikleri (türü, yoğunluğu, dağıtım vb.) eğitim ve test veri kümelerinin türüyle eşleşmelidir. Bu bölümün odağı bu tür veri gereksinimlerinden oluşur.

### <a name="relevant-data"></a>İlgili veriler

İlk olarak, verilerin _sorunla ilgili_ olması gerekir. Yukarıda açıklanan _tekerlek hata_ kullanım durumunu göz önünde bulundurun. eğitim verileri, tekerlek işlemleriyle ilgili özellikler içermelidir. Sorun,  _görmeyebilir sisteminin_ başarısızlığını tahmin etmeye çalışıyorsa eğitim verilerinin, görmeyebilir sisteminin tüm farklı bileşenlerini kapsayacak olması gerekir. İlk durum belirli bir bileşeni hedefler, ikinci durum ise daha büyük bir alt sistem başarısızlığını hedefler. İkincisi daha fazla dağınık veriye sahip olduğundan, genel öneri, daha büyük alt sistemler yerine belirli bileşenler hakkında tahmin sistemleri tasarlayacaktır. Etki alanı uzmanı (tahmine [dayalı bakım Için uygun sorunları](#qualifying-problems-for-predictive-maintenance)gör) analizin için en ilgili veri alt kümelerini seçmeye yardımcı olmalıdır. İlgili veri kaynakları, tahmine [dayalı bakım Için veri hazırlama konusunda](#data-preparation-for-predictive-maintenance)daha ayrıntılı olarak ele alınmıştır.

### <a name="sufficient-data"></a>Yeterli veri
Genellikle hata geçmiş verileriyle ilgili olarak iki soru istenir: (1) "bir modeli eğitmek için kaç hata olayı gerekir?" (2) "kaç kayıt" yeterince "kabul edilir?" Kesin yanıt yoktur, ancak yalnızca Thumb kuralları. (1) için, daha fazla hata olayı sayısı, model daha iyidir. (2) için, hata olaylarının tam sayısı, çözülen sorunun verilerine ve içeriğine bağlıdır. Ancak çevir tarafında, bir makine çok sık başarısız olursa, iş bunu değiştirir ve bu da hata örneklerini azaltır. Burada, etki alanı uzmanından kılavuzluk önem taşımaktadır. Ancak, _nadir olay_ sorunuyla ilgili olarak bu yöntemde olan yöntemler vardır. Bunlar, [imledengelenmiş verileri işleme](#handling-imbalanced-data)bölümünde ele alınmıştır.

### <a name="quality-data"></a>Kalite verileri
Verilerin kalitesi kritiktir-her bir tahmin özniteliği değeri, hedef değişkenin değeriyle birlikte _doğru_ olmalıdır. Veri kalitesi, istatistik ve veri yönetiminde iyi bir araştırdık alanıdır ve bu nedenle bu kılavuzda kapsam dışı olur.

> [!NOTE]
> Kaliteli verileri sunmaya yönelik birkaç kaynak ve kurumsal ürün vardır. Bir başvuru örneği aşağıda verilmiştir:
> - Davsu, T, Johnson, T., araştırmacı veri madenciliği ve veri temizleme, Wiley, 2003.
> - [Araştırmacı veri analizi, Vikipedi](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Büyük veritabanları için Hellerstein, J, nicel veri temizleme](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, Van der ara, M, R ile veri temizlemeye giriş](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Tahmine dayalı bakım için veri hazırlama

### <a name="data-sources"></a>Veri kaynakları

Tahmine dayalı bakım için ilgili veri kaynakları şunlardır, ancak bunlarla sınırlı değildir:
- Hata geçmişi
- Bakım/onarım geçmişi
- Makine işletim koşulları
- Ekipman meta verileri

#### <a name="failure-history"></a>Hata geçmişi
Hata olayları PdM uygulamalarında nadir bir durumdur. Ancak, tahmin modelleri oluştururken, algoritmanın bir bileşenin normal işlem deseni ve hata desenleri hakkında bilgi sahibi olması gerekir. Bu nedenle eğitim verileri her iki kategoriden da yeterli sayıda örnek içermelidir. Bakım kayıtları ve parça değiştirme geçmişi, hata olaylarını bulmak için iyi kaynaklardır. Bazı etki alanı bilgilerinin yardımıyla eğitim verilerinde bozukluklar da bir başarısızlık olarak tanımlanabilir.

#### <a name="maintenancerepair-history"></a>Bakım/onarım geçmişi
Bir varlığın bakım geçmişi, değişen bileşenlerle ilgili ayrıntıları ve gerçekleştirilen onarım etkinliklerini içerir. Bu olaylar, düşme düzenlerini kaydeder. Eğitim verilerinde bu önemli bilgilerin yokluğu, yanıltıcı model sonuçlarına yol açabilir. Hata geçmişi, bakım geçmişi içinde özel hata kodları olarak veya parçalar için sipariş tarihleri olarak da bulunabilir. Hata düzenlerini etkileyen ek veri kaynakları, etki alanı uzmanları tarafından araştırılması ve sağlanması gerekir.

#### <a name="machine-operating-conditions"></a>Makine işletim koşulları
İşlemdeki donanımların, algılayıcı tabanlı (veya diğer) akış verileri, önemli bir veri kaynağıdır. PdM ' deki bir temel varsayım, bir makinenin sistem durumunun rutin işlemi sırasında zaman içinde düşmesidir. Verilerin, bu eskime modelini yakalayan zaman uyumlu özellikler içermesi ve düşüşe neden olan tüm bozukluklar olması beklenir. Zamanın zamana ve başarısızlık olmayan düzenlerini öğrenmek için algoritmanın zamana bağlı yönü gereklidir. Algoritma, bu veri noktalarına bağlı olarak, bir makinenin başarısız olmadan önce çalışmaya devam edebilme süresini tahmin etmek için öğrenir.

#### <a name="static-feature-data"></a>Statik özellik verileri
Statik özellikler, ekipman hakkındaki meta verilerlerdir. Ekipman marka, model, üretilmiş Tarih, hizmet başlangıç tarihi, sistem konumu ve diğer teknik belirtimlerde örnek olarak verilebilir.

[Örnek PDM kullanım örnekleri](#sample-pdm-use-cases) için ilgili verilerin örnekleri aşağıda verilmiştir:

| Kullanım Örneği | İlgili verilerin örnekleri |
|:---------|---------------------------|
|_Uçuş gecikmesi ve iptalleri_ | Uçuş ve sayfa günlükleri biçiminde uçuş rotası bilgileri. Uçuş BAI verileri, ayrılma/varış tarihi, zaman, Havaalanı, layovers vb. yönlendirme ayrıntılarını içerir. Sayfa günlüğü, bir dizi hata ve bir hata ve bakım kodu içerir.|
|_Airi motoru bölüm hatası_ | Çeşitli parçaların koşulu hakkında bilgi sağlayan uçak içindeki sensörlerden toplanan veriler. Bakım kayıtları, bileşen hatalarının ne zaman oluştuğunu ve ne zaman değiştirildiğini belirlemesine yardımcı olur.|
|_ATM hatası_ | Her işlem için (nakit/denetim) ve nakit atan sensörler için algılayıcı okumaları. Notlar, Not kalınlığı, Not varış uzaklığı, denetim öznitelikleri vb. arasında boşluk ölçümü hakkında bilgi. Hata kodları sağlayan bakım kayıtları, onarım bilgileri, nakit dağıtıcısının en son yeniden doldurulduğu zaman.|
|_Rüzgar türbin hatası_ | Algılayıcılar, sıcaklık, Rüzgar yönü, güç oluşturma, Oluşturucu hızı vb. gibi türbin koşullarını izler. Veriler, çeşitli bölgelerde bulunan Rüzgar gruplarından birden çok rüzgar türbinler tarafından toplanır. Genellikle, her bir Turbin, sabit bir zaman aralığında ölçümleri geçiş için birden çok algılayıcı okumaları olacaktır.|
|_Devre kesici sorunları_ | Düzeltici, önleyici ve Systematik eylemleri içeren bakım günlükleri. Açık ve kapalı eylemler gibi devre kesicilerin gönderilen otomatik ve el ile komutları içeren işletimsel veriler. Üretim tarihi, konum, model vb. gibi cihaz meta verileri Voltaj düzeyleri, coğrafi konum, çevresel koşullar gibi devre kesici belirtimleri.|
|_Asansör kapısı başarısızlığı_| Asansör, üretim tarihi, bakım sıklığı, yapı türü vb. gibi Asansör meta verileri. Kapı döngüsü sayısı, ortalama kapı kapatma süresi gibi işletimsel bilgiler. Nedenler ile hata geçmişi.|
|_Tekerlek başarısızlığı_ | Tekerlek hızlandırma, örgü örnekleri, gidiş uzaklığı, hız vb. ölçülü algılayıcı verileri. Üretici ve mamul tarihi gibi tekerlekler hakkında statik bilgiler. Sıra tarihleri ve miktarları izleyen bölüm sırası veritabanından çıkarılan hata verileri.|
|_Subway kapı başarısızlıklarını eğitme_ | Kapıların açılış ve kapanış süreleri, geçerli eğitim kapılarının durumu gibi diğer işletimsel veriler. Statik veriler varlık tanımlayıcı, zaman ve koşul değeri sütunları içerir.|

### <a name="data-types"></a>Veri türleri
Yukarıdaki veri kaynakları verildiğinde, PdM etki alanında gözlemlendi iki ana veri türü şunlardır:

- Zamana bağlı _veriler_: işlem telemetrisi, makine koşulları, iş sırası türleri, kayıt sırasında zaman damgalarına sahip olacak öncelik kodları. Hata, bakım/onarım ve kullanım geçmişi her olayla ilişkili zaman damgalarına sahip olur.
- _Statik veriler_: makine özellikleri ve operatör özellikleri, makinelerin veya işleç özniteliklerinin teknik belirtimlerini tanımladıklarından, statiktir. Bu özellikler zaman içinde değişebilir, ayrıca bunlarla ilişkili zaman damgaları de olmalıdır.

Tahmine ici ve hedef değişkenler, kullanılmakta olan algoritmaya bağlı olarak [, sayısal, kategorik ve diğer veri türlerine](https://www.statsdirect.com/help/basics/measurement_scales.htm) önceden işlenmeli/dönüştürülebilmelidir.

### <a name="data-preprocessing"></a>Veri ön işlemesi
_Özellik mühendisliğinin_ bir önkoşulu olarak, çeşitli akışlardan verileri, kolayca yapı özelliklerinin oluşturulduğu bir şema oluşturmak için hazırlayın. Verileri önce kayıt tablosu olarak görselleştirin. Tablodaki her satır bir eğitim örneğini temsil eder ve sütunlar tahmine _ici_ özelliklerini (bağımsız öznitelikler veya değişkenler olarak da adlandırılır) temsil eder. Son sütun (ler) _hedefin hedefi_ (bağımlı değişken) olduğu gibi verileri düzenleyin. Her eğitim örneği için, bu sütunun değeri olarak bir _etiket_ atayın.

Zamana bağlı veriler için, algılayıcı verilerinin süresini zaman birimlerine bölün. Her kayıt bir varlık için bir zaman birimine ait olmalıdır _ve ayrı bilgiler sunmalıdır_. Zaman birimleri, iş gereksinimlerine göre saniye, dakika, saat, gün, ay gibi bir şekilde tanımlanır. Zaman birimi, _veri toplama sıklığıyla aynı olmak zorunda değildir_. Sıklık yüksekse, veriler bir birimden diğerine önemli bir farkı gösteremeyebilir. Örneğin, ortam sıcaklığının her 10 saniyede bir toplandığını varsayın. Eğitim verileri için aynı aralığın kullanılması, herhangi bir ek bilgi sağlamadan örnek sayısını yalnızca daha da kolaylaştırır. Bu durumda, daha iyi bir strateji, 10 dakikalık verilerin ortalamasını veya iş gerekçesini temel alan bir saati kullanmaktır.

Statik veriler için
- _Bakım kayıtları_: ham bakım verileri, belirli bir noktada gerçekleştirilmiş bakım etkinlikleri hakkında bilgi içeren bir varlık tanımlayıcısı ve zaman damgası içerir. Her kategori tanımlayıcısının belirli bir bakım eylemiyle benzersiz bir şekilde eşlendiği, bakım etkinliklerini _kategorik_ sütunlara dönüştürün. Bakım kayıtlarının şeması varlık tanımlayıcı, saat ve bakım eylemi içerir.

- _Hata kayıtları_: belirli iş koşulları tarafından tanımlanan özel hata kodları veya hata olayları olarak hatalar veya hata nedenleri kaydedilebilir. Ekipmanın birden çok hata koduna sahip olduğu durumlarda, etki alanı uzmanı, hedef değişkeni ile ilgili olanları belirlemenize yardımcı olmalıdır. Bu hatalarla bağıntılı tahmine _ici_ özellikleri oluşturmak için kalan hata kodlarını veya koşullarını kullanın. Hata kayıtlarının şeması, varsa varlık tanımlayıcı, saat, hata veya hata nedenini içerir.

- _Makine ve işleç meta verileri_: bir varlığı, kendi öznitelikleriyle birlikte ilişkilendirmek için makine ve işleç verilerini tek bir şemada birleştirin. Makine koşulları için şema varlık tanımlayıcı, varlık özellikleri, işleç tanımlayıcısı ve işleç özelliklerini içerir.

Diğer veri ön işleme adımları _eksik değerleri işlemeyi_ ve öznitelik değerlerinin _normalleştirmesini_ içerir. Ayrıntılı bir tartışma, bu kılavuzun kapsamı dışındadır. bazı yararlı başvurular için sonraki bölüme bakın.

Yukarıdaki ön işlenmiş veri kaynaklarıyla birlikte, özellik mühendisinin son dönüşümü, varlık tanımlayıcısı ve zaman damgasına göre yukarıdaki tablolara katılamaz. Makine normal işlem içinde olduğunda, sonuç tablosu hata sütunu için null değerler içermelidir. Bu null değerler normal işlem için bir göstergeyle ayarlanabilir. Tahmine _dayalı model için Etiketler_ oluşturmak üzere bu hata sütununu kullanın. Daha fazla bilgi için, tahmine dayalı [bakım için modelleme teknikleri](#modeling-techniques-for-predictive-maintenance)bölümüne bakın.

## <a name="feature-engineering"></a>Özellik mühendisliği
Özellik Mühendisliği, verileri modellemeye başlamadan önce ilk adımdır. Veri bilimi işlemindeki rolü [burada açıklanmıştır](./create-features.md). Bir _özellik_ , model için sıcaklık, basınç, titreşim vb. gibi tahmine dayalı bir özniteliktir. PdM için özellik Mühendisliği, bir makinenin sistem durumunun, boyutlandırılabilir bir süre içinde toplanan geçmiş verileri üzerinden soyutlanmasını içerir. Bu anlamda, uzaktan izleme, anomali algılama ve hata algılama gibi eşlerinden farklıdır. 

### <a name="time-windows"></a>Zaman pencereleri
Uzaktan izleme, _zaman içinde noktadan_ itibaren gerçekleşen olayları raporlamasına sahiptir. Anomali algılama modelleri, zaman içinde noktaları işaretlemek için gelen veri akışlarını değerlendirir (puan). Hata algılama, hataları zaman içinde olduğu gibi belirli türlerde olacak şekilde sınıflandırır. Buna karşılık PdM, _Geçmiş zaman dilimi_ boyunca makine davranışını temsil eden özelliklere bağlı olarak _gelecekteki bir zaman dilimi_ içinde oluşan tahmini sorunları içerir. PdM için, bireysel zaman noktalarından özellik verileri, tahmine dayalı olmak için çok gürültülü değildir. Bu nedenle, her bir özelliğin verilerinin, zaman pencereleri üzerinde veri noktaları toplayarak _düzgün_ bir şekilde ele alınması gerekir.

### <a name="lag-features"></a>Gecikme özellikleri
İş gereksinimleri, modelin ileride ne kadar tahmin edilmesi gerektiğini tanımlar. Bu süre sırasıyla, bu tahminleri yapmak için ' modelin ne kadar geri dönmesi gerektiğini tanımlamaya yardımcı olur. Bu ' geri arama ' dönemine _gecikme_ denir ve bu gecikme süresi boyunca uygulanan özellikler _gecikme özellikleri_ olarak adlandırılır. Bu bölümde, zaman damgalarına sahip veri kaynaklarından ve statik veri kaynaklarından Özellik oluşturmaya oluşturulabilecek gecikme özellikleri anlatılmaktadır. Gecikme özellikleri genellikle doğal olarak _sayısal_ olarak ayarlanır.

> [!IMPORTANT]
> Pencere boyutu deneme aracılığıyla belirlenir ve bir etki alanı uzmanı yardımıyla sonlandırılmalıdır. Aynı desteklenmediği uyarısıyla, öteleme özelliklerinin, toplamaların ve Windows türünün seçimi ve tanımı için de geçerlidir.

#### <a name="rolling-aggregates"></a>Toplama toplamaları
Bir varlığın her kaydı için, "W" boyutundaki bir pencere, toplamaları hesaplamak için zaman birimi sayısı olarak seçilir. Sonra gecikme özellikleri, bu kaydın _tarihinden önce_ W dönemleri kullanılarak hesaplanır. Şekil 1 ' de mavi çizgiler, her bir birim için bir varlık için kaydedilen algılayıcı değerlerini gösterir. Bu değerler, W = 3 boyutundaki bir pencere üzerinde özellik değerlerinin hareketli ortalamasını gösterir. Hareketli ortalama, t<sub>1</sub> (turuncu) ile t<sub>2</sub> (yeşil) aralığındaki zaman damgalarına sahip tüm kayıtlar üzerinden hesaplanır. W değeri, genellikle verilerin doğasına bağlı olarak dakika veya saat cinsinden olur. Ancak bazı sorunlar için, büyük bir W (12 ay) seçilmesi, kayıt zamanına kadar bir varlığın tüm geçmişini sağlayabilir.

![Şekil 1. Birleşik toplama özellikleri](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Şekil 1. Birleşik toplama özellikleri

Bir zaman penceresi üzerinde toplama toplamaları örnekleri şunlardır; Count, Average, CUMESUM (kümülatif Sum) ölçüleri, Min/Max değerleri. Ayrıca, Varyans, standart sapma ve N standart sapmaların ötesinde geçen aykırı ların sayısı genellikle kullanılır. Bu kılavuzdaki [kullanım örnekleri](#sample-pdm-use-cases) için uygulanabilecek toplamaların örnekleri aşağıda listelenmiştir. 
- _Uçuş gecikmesi_: son gün/hafta içindeki hata kodlarının sayısı.
- _Uçak motoru bölüm hatası_: geçen gün, standart sapma ve toplam gün, hafta vb. dahil olmak üzere toplama anlamına gelir. Bu ölçüm, iş etki alanı uzmanı ile birlikte belirtilmelidir.
- _ATM arızaları_: toplama, ortanca, Aralık, standart sapmalar, üç standart sapmanın ötesinde, üst ve alt CUMESUM 'un sayısını belirtir.
- _Alt yol tren kapı arızaları_: geçen gün, hafta, iki hafta içindeki olay sayısı.
- _Devre kesici hataları_: geçen hafta, yıl, üç yıl ve bu yana hata sayısı.

PdM ' deki başka bir yararlı teknik, verilerin eğilimlerini algılayan algoritmalar kullanarak eğilim değişikliklerini, ani artışları ve düzey değişiklikleri yakalamadır.

#### <a name="tumbling-aggregates"></a>Atlayan toplamalar
Bir varlığın etiketlendiği her kayıt için, _w-<sub>k</sub>_ boyutundaki bir pencere tanımlanmıştır; burada _k_ , _w_ boyutundaki pencerelerin sayısıdır. Daha sonra toplamalar, bir kaydın zaman damgasından önceki dönemler için _Windows_ _W-k, w-<sub>(k-1)</sub>,..., w-<sub>2</sub>, w-<sub>1</sub>_ ' n i _n üzerine yazılır_ . _k_ kısa vadeli etkileri yakalamak için küçük bir sayı ya da uzun süreli azalma düzenlerini yakalamak için büyük bir sayı olabilir. (bkz. Şekil 2).

![Şekil 2. Atlayan toplama özellikleri](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Şekil 2. Atlayan toplama özellikleri

Örneğin, Rüzgar türbinler kullanım örneği için gecikme özellikleri W = 1 ve k = 3 ile oluşturulabilir. Son üç aya ait gecikme süresi üst ve alt aykırı değerleri kullanarak gecikirler.

### <a name="static-features"></a>Statik özellikler

Üretim tarihi, model numarası, konum gibi ekipmanların teknik belirtimleri bazı statik özelliklere örnektir. Modelleme için _kategorik_ değişkenler olarak kabul edilir. Devre kesici kullanım örneği için bazı örnekler voltaj, geçerli, güç kapasitesi, transformatör türü ve güç kaynağıdır. Tekerlek hatalarında, tire tekerlekleri (ALAVs çelik) türü bir örnektir.

Şimdiye kadar tartışılan veri hazırlama çabaları aşağıda gösterildiği gibi verilerin düzenlenmesine neden olmalıdır. Eğitim, test ve doğrulama verileri bu mantıksal şemaya sahip olmalıdır (Bu örnek gün cinsinden saati gösterir).

| Varlık KIMLIĞI | Saat | \<Feature Columns> | Etiketle |
| ---- | ---- | --- | --- |
| A123 |Gün 1 | . . . | . |
| A123 |Gün 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Gün 1 | . . . | . |
| B234 |Gün 2 | . . . | . |
| ...  |...   | . . . | . |

Özellik mühendisliğinin son adımı hedef değişkenin **etiketleniyor** . Bu işlem, modelleme tekniğinin bağımlıdır. Sırasıyla, modelleme tekniği iş sorununa ve kullanılabilir verilerin yapısına bağlıdır. Etiketleme, sonraki bölümde ele alınmıştır.

> [!IMPORTANT]
> Veri hazırlama ve özellik Mühendisliği, başarılı PdM çözümlerine ulaşmak için modelleme teknikleri kadar önemli öneme sahiptir. Etki alanı uzmanı ve uygulayıcı, modelin doğru özelliklerine ve verilerine ulaşan önemli bir zaman yatırmalıdır. Özellik Mühendisliği üzerinde birçok defterden küçük bir örnek aşağıda listelenmiştir:
> - Pyle, D. veri madenciliği için veri hazırlama (Veri Yönetimi sistemlerdeki Morgan Kaumann serisi), 1999
> - Zheng, A., Casari, A. feature Mühendisliği Machine Learning: veri bilimcileri için Ilkeler ve teknikler, yani Reilly, 2018.
> - Dong, G. Liu, H. (düzenleyiciler), Machine Learning ve veri analizi için özellik Mühendisliği (Bölümman & salonu/CRC veri madenciliği ve bilgi bulma serisi), CRC basma, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Tahmine dayalı bakım için modelleme teknikleri

Bu bölümde, PdM sorunlar için temel modelleme teknikleri ve bunlara özgü etiket oluşturma yöntemleri açıklanmaktadır. Farklı sektörler genelinde tek bir modelleme tekniğinin kullanılabileceğini fark edebilirsiniz. Modelleme tekniği, her seferinde veri bağlamı yerine veri bilimi sorunuyla eşleştirilmelidir.

> [!IMPORTANT]
> Hata durumları ve etiketleme stratejisi için etiket seçimi  
> , etki alanı uzmanı ile danışmanda belirtilmelidir.

### <a name="binary-classification"></a>İkili sınıflandırma
İkili sınıflandırma, gelecekteki _bir zaman diliminde bir ekipman parçasının başarısız olma olasılığını tahmin_ etmek için kullanılır ve _gelecek ufuk dönemi X_ olarak adlandırılır. X, iş sorunu ve taraftaki veriler tarafından etki alanı uzmanlığına göre belirlenir. Örnekler şunlardır:
- bileşenleri değiştirmek, bakım kaynaklarını dağıtmak, bu dönemde oluşması muhtemel bir sorundan kaçınmak için bakım gerçekleştirmek için gereken _En düşük sağlama süresi_ .
- bir sorun gerçekleşmeden önce gerçekleşebilmeniz gereken _en az olay sayısı_ .

Bu teknikte, iki tür eğitim örneği tanımlanmıştır. _Bir hatayı belirten_, etiket = 1 olan pozitif bir örnek. Etiketi = 0 olan normal işlemleri gösteren negatif bir örnek. Hedef değişkeni ve bu nedenle etiket değerleri _kategorik_' tir. Model, sonraki X zaman birimleri üzerinde başarısız olan veya normal şekilde çalışan her yeni örneği tanımlamalıdır.

#### <a name="label-construction-for-binary-classification"></a>İkili sınıflandırma için etiket oluşturma
Buradaki soru: "varlığın sonraki X birimlerinde başarısız olma olasılığı nedir?" Bu soruyu yanıtlamak için, bir varlığın arızasından önce X kayıtlarını "başarısız olmak üzere" (Label = 1) olarak etiketleyip diğer tüm kayıtları "normal" (etiket = 0) olarak etiketleyin. (bkz. Şekil 3).

![Şekil 3. İkili sınıflandırma için etiketleme](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Şekil 3. İkili sınıflandırma için etiketleme

Bazı kullanım örneklerinin etiketleme stratejilerinin örnekleri aşağıda listelenmiştir.
- _Uçuş gecikmeleri_: X bir gün olarak seçilebilir ve bu da sonraki 24 saat içindeki gecikmeleri tahmin edebilir. Ardından, hatalardan önceki 24 saat içinde olan tüm fışıkların 1 olarak etiketlenmesi.
- _ATM nakit örneği hataları_: bir amaç, bir işlemin sonraki bir saatteki hata olasılığını tespit etmek olabilir. Bu durumda, hatanın son saati içinde gerçekleşen tüm işlemler 1 olarak etiketlenir. Sonraki N para birimi notlarının hata olasılığını tahmin etmek için, bir hatanın son N notlarındaki tüm notlar 1 olarak etiketlenir.
- _Devre kesici hataları_: hedef, sonraki devre kesici komut hatasını tahmin etmek olabilir. Bu durumda, X gelecek bir komut olacak şekilde seçilir.
- _Kapı başarısızlıklarını eğitme_: X iki gün olarak seçilebilir.
- _Rüzgar türbin hatalarıyla_: X iki ay olarak seçilebilir.

### <a name="regression-for-predictive-maintenance"></a>Tahmine dayalı bakım için gerileme
Regresyon modelleri, _bir varlığın kalan faydalı ömrünü (RUL) hesaplamak_ için kullanılır. RUL, bir sonraki başarısızlık gerçekleşmeden önce bir varlığın işletimsel süre miktarı olarak tanımlanır. Her eğitim örneği, bir varlık için _NY_ bir zaman birimine ait olan bir kayıttır; burada _n_ birden çok. Model her yeni örneğin RUL 'sini _sürekli sayı_ olarak hesaplamalıdır. Bu sayı, hatadan önce kalan süreyi gösterir.

#### <a name="label-construction-for-regression"></a>Gerileme için etiket oluşturma
Buradaki soru: "ekipmanın kalan kullanım ömrü (RUL) nedir?" Hatadan önceki her kayıt için, etiketi bir sonraki hatadan önce kalan süre birimi sayısı olacak şekilde hesaplayın. Bu yöntemde, Etiketler sürekli değişkenlerdir. (Bkz. Şekil 4)

![Şekil 4. Gerileme için etiketleme](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Şekil 4. Gerileme için etiketleme

Gerileme için etiketleme, bir başarısızlık noktasına yönelik başvuruya göre yapılır. Varlığın bir hatadan önce ne kadar süreyle sürdüğünü bilmeden hesaplama mümkün değildir. Bu nedenle, ikili sınıflandırmadan dolayı verilerde hatalara sahip olmayan varlıklar modelleme için kullanılamaz. Bu sorun en iyi yöntem [Analizi](https://en.wikipedia.org/wiki/Survival_analysis)adlı başka bir istatistiksel teknik tarafından karşılanır. Ancak, bu tekniği sık aralıklarla zaman içinde değişen verileri içeren PdM kullanım örneklerine uygularken olası zorluklar ortaya çıkabilir. Acil durum analizi hakkında daha fazla bilgi için [Bu tek sayfalayıcı](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf)bölümüne bakın.

### <a name="multi-class-classification-for-predictive-maintenance"></a>Tahmine dayalı bakım için çok sınıflı sınıflandırma
Çoklu Sınıf sınıflandırma teknikleri, PdM çözümlerinde iki senaryo için kullanılabilir:
- _Sonraki iki sonucu_ tahmin edin: ilk sonuç bir varlık için _başarısızlık zaman aralığıdır_ . Varlık, mümkün olan birden çok dönemden birine atanır. İkinci sonuç, _birden çok ana nedenden biri_ nedeniyle gelecekteki bir dönemde hata olma olasılığıdır. Bu tahmin, bakım ekiplerini, belirtiler için izleme ve bakım zamanlamalarını planlayabilmesini sağlar.
- Belirli bir hatanın _en olası temel nedenini_ tahmin edin. Bu sonuç, bir hatayı gidermeye yönelik doğru bakım eylemleri kümesini önerir. Bir hata sonrasında, teknisyenlerin onarım eylemlerine göre öncelik almasına yardımcı olabilecek bir kök nedeni ve önerilen onarımlar listesi.

#### <a name="label-construction-for-multi-class-classification"></a>Çok sınıf sınıflandırması için etiket oluşturma
Buradaki soru şudur: "bir varlığın, bir sonraki _nZ_ biriminde başarısız olma olasılığı, _n_ dönem sayısıdır." Bu soruyu yanıtlamak için, nZ kayıtlarını bir varlık arızasından (3Z, 2Z, Z) kullanarak etiketlemesini etiketleyin. Diğer tüm kayıtları "normal" olarak etiketle (etiket = 0). Bu yöntemde, hedef değişkeni _kategorik_ değerleri barındırır. (Bkz. Şekil 5).

![Şekil 5. Birden çok Lass sınıflandırması için hata zaman tahmini etiketleri](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Şekil 5. Hata süresi tahmini için çok sınıflı sınıflandırmanın etiketlenmesi

Buradaki soru şudur: "kök nedeni/sorunu _P <sub>i</sub>_ nedeniyle varlığın sonraki X biriminde başarısız olmasının olasılığı nedir?" olası ana _nedenlerin sayısıdır._ Bu soruyu yanıtlamak için, "bir varlık hatasından önce X kaydı" kök neden _p <sub>ı</sub>_ nedeniyle başarısız olmak üzere "(Label = _P <sub>ı</sub>_) olarak etiketleyin. Diğer tüm kayıtları "normal" olarak etiketle (etiket = 0). Bu yöntemde Ayrıca Etiketler kategorik (bkz. Şekil 6).

![Şekil 6. Birden çok Lass sınıflandırması için kök neden tahmin etiketleri](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Şekil 6. Kök neden tahmini için çok sınıflı sınıflandırmanın etiketlenmesi

Model her _P <sub>i</sub>_ nedeniyle hata olasılığı ve hata olmaması olasılığını atar. Bu olasılıkların, gelecekte oluşması olası sorunların tahminine izin vermek için boyuta göre sıralanmış olabilir.

Buradaki soru: "bir hatadan sonra hangi bakım eylemleri önerilir?" Bu soruyu yanıtlamak için etiketleme _gelecekteki bir ufuk gerektirmez_, çünkü model gelecekte hata tahmin etmez. _Hatanın zaten gerçekleşmesinin_ ardından en olası temel nedeni tahmin etmek yeterlidir.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Tahmine dayalı bakım için eğitim, doğrulama ve test yöntemleri
[Ekip veri bilimi işlemi](./overview.md) , model eğitimi-test-doğrulama döngüsünün tam kapsamını sağlar. Bu bölümde, PdM 'e özgü noktalar açıklanmaktadır.

### <a name="cross-validation"></a>Çapraz doğrulama
[Çapraz doğrulamanın](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) amacı, eğitim aşamasında modeli "test" olarak belirleyen bir veri kümesi tanımlamaktır. Bu veri kümesine _doğrulama kümesi_ denir. Bu teknik, _fazla ekleme_ gibi sorunları sınırlamaya yardımcı olur ve modelin bağımsız bir veri kümesini genelleştirmek için bir öngörü sağlar. Diğer bir deyişle, gerçek bir sorundan olabilecek bilinmeyen bir veri kümesidir. PdM için eğitim ve test yordamının, görülmeyen bir sonraki verilerde daha iyi genelleştirilmesi için değişen açılardan dikkate alınması gerekir.

Birçok makine öğrenimi algoritması, model performansını önemli ölçüde değiştirebilmesine yönelik bir dizi hiper parametreye bağımlıdır. Modele eğitim yaparken bu hiper parametrelerin en iyi değerleri otomatik olarak hesaplanmaz. Bu kişiler, veri bilimcisi tarafından belirtilmelidir. Hiper parametrelerin iyi değerlerini bulmanın birkaç yolu vardır.

En yaygın bir, örnekleri _k_ _katlara_ rastgele ayıran, Her hiper parametre değeri kümesi için, öğrenme algoritmasını _k_ kez çalıştırın. Her yinelemede, geçerli katlama içindeki örnekleri doğrulama kümesi olarak ve örneklerin geri kalanını bir eğitim kümesi olarak kullanın. Algoritmayı eğitim örnekleri üzerinden eğitme ve doğrulama örnekleri üzerinden performans ölçümlerini hesaplama. Bu döngünün sonunda, _k_ performans ölçümlerinin ortalamasını hesaplayın. Her hiper parametre değeri kümesi için en iyi ortalama performansa sahip olanları seçin. Hiper parametreleri seçme görevi genellikle doğası içinde deneysel bir görevdir.

PdM sorunları, veriler çeşitli veri kaynaklarından gelen bir zaman serisi olarak kaydedilir. Bu kayıtlar etiketleme zamanına göre sıralanmış olabilir. Bu nedenle, veri kümesi _rastgele_ eğitim ve doğrulama kümesine bölündüğünde bazı _eğitim örnekleri bazı doğrulama örneklerinden daha sonra zaman içinde olabilir_. Hiper parametre değerlerinin gelecekteki performansı, modelin eğitilme _işleminden önce_ ulaşan bazı veriler temel alınarak tahmin edilir. Bu tahminler, özellikle zaman serisi sabit değilse ve zaman içinde geliştikçe aşırı iyimser olabilir. Sonuç olarak, seçilen hiper parametre değerleri, daha iyi bir hal olabilir.

Önerilen yöntem, tüm doğrulama örneklerinin daha sonra tüm eğitim örneklerinden daha sonra olduğu _zamana bağlı_ bir şekilde, örnekleri eğitim ve doğrulama kümesine bölmek için önerilir. Her hiper parametre değeri kümesi için, algoritmayı eğitim verileri kümesi üzerinden eğitme. Modelin performansını aynı doğrulama kümesi üzerinden ölçün. En iyi performansı gösteren hiper parametre değerlerini seçin. Tren/doğrulama bölünmesi tarafından seçilen hiper parametre değerleri, çapraz doğrulama tarafından rastgele seçilmiş değerlerle kıyasla daha iyi gelecek model performansına neden olur.

Son model, en iyi hiper parametre değerlerini kullanarak eğitim verilerinin tamamına yönelik bir öğrenme algoritmasına eğitim vererek oluşturulabilir.

### <a name="testing-for-model-performance"></a>Model performansı için test etme
Bir Model derlendikten sonra, yeni veriler üzerinde gelecekteki performansının tahmini olması gerekir. İyi bir tahmin, doğrulama kümesi üzerinden hesaplanan hiper parametre değerlerinin veya çapraz doğrulamadan hesaplanan ortalama bir performans ölçümünün performans ölçümdür. Bu tahminler genellikle aşırı iyimser bir şekilde yapılır. İşletme, genellikle modeli test etmek istedikleri gibi bazı ek yönergeler içerebilir.

PdM için önerilen yol, örnekleri eğitimlere, doğrulamaya ve test veri kümelerine _zamana bağlı_ bir şekilde bölemedir. Tüm test örnekleri, tüm eğitim ve doğrulama örneklerinden daha sonra zaman içinde olmalıdır. Bölme işleminden sonra modeli oluşturun ve daha önce açıklandığı gibi performansını ölçün.

Zaman serisi sabit ve tahmin edilmesi kolay olduğunda, hem rastgele hem de zamana bağlı yaklaşımlar gelecekteki performanstan benzer tahminler oluşturur. Ancak zaman serisi sabit değildir ve/veya tahmin edildiğinde, zamana bağlı yaklaşım gelecekteki performansın daha gerçekçi tahminlerini oluşturacaktır.

### <a name="time-dependent-split"></a>Zamana bağlı bölme
Bu bölümde zamana bağımlı bölme uygulamak için en iyi yöntemler açıklanmaktadır. Eğitim ve test kümeleri arasında zamana bağlı iki yönlü bölme aşağıda açıklanmıştır.

Çeşitli sensörlerden ölçümler gibi zaman damgamış olayların akışını varsayın. Birden çok olay içeren zaman kareleri üzerinde eğitim ve test örneklerinin özelliklerini ve etiketlerini tanımlayın. Örneğin, ikili sınıflandırma için, geçmiş olaylara göre özellikler oluşturun ve gelecekte "X" birim içinde gelecek olaylara göre Etiketler oluşturun (bkz. [özellik Mühendisliği](#feature-engineering) ve modelleme teknikleri bölümlerine bakın). Bu nedenle, bir örneğin etiketleme zaman çerçevesi, özelliklerinin zaman dilimine göre daha sonra gelir.

Zamana bağlı bölme için, bir modelin eğileceği, bir modelin eğitilmesi için bir _eğitim kesme süresi <sub></sub>_ <sub>seçin.</sub> Eğitim verilerine T<sub>c</sub> 'nin ötesinde gelecek etiketlerin sızmasını engellemek için, eğitim örneklerini t<sub>c</sub>'den önceki X birimi olacak şekilde etiketlemek için en son saati seçin. Şekil 7 ' de gösterilen örnekte, her kare, Özellikler ve etiketlerin yukarıda açıklanan şekilde hesaplandığı veri kümesindeki bir kaydı temsil eder. Şekil, X = 2 ve W = 3 için eğitim ve test kümelerine gitmesi gereken kayıtları gösterir:

![Şekil 7. İkili sınıflandırma için zamana bağlı bölme](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Şekil 7. İkili sınıflandırma için zamana bağlı bölme

Yeşil kareler, eğitim için kullanılabilecek zaman birimlerine ait kayıtları temsil eder. Her eğitim örneği, özellik oluşturma için son üç nokta ve T<sub>c</sub>'den önce etiketlemek için sonraki iki nokta dikkate alındığında oluşturulur. Sonraki iki dönemin herhangi bir bölümü T<sub>c</sub>'nin ötesinde, t<sub>c</sub>'nin ötesinde görünürlük olmadığı için bu örneği eğitim verileri kümesinden hariç tutun.

Siyah kareler, yukarıdaki kısıtlama verildiğinde eğitim veri kümesinde kullanılması gereken, son etiketlenen veri kümesinin kayıtlarını temsil eder. Bu kayıtlar T<sub>c</sub>'den önce olduklarından verileri test etmek için de kullanılmaz. Ayrıca, etiketleme zaman kareleri, ideal olmayan eğitim zaman dilimine göre kısmen değişir. Eğitim ve test verileri, etiket bilgilerinin sızıntısını engellemek için ayrı etiketleme zaman çerçevelerine sahip olmalıdır.

Şimdiye kadar tartışılan teknik, eğitim ve test, T<sub>c</sub>yakınında zaman damgalarına sahip örnekler arasında örtüşmeye izin verir. Daha büyük ayrımı elde etmek için bir çözüm, test kümesinden T<sub>c</sub> zaman birimleri içindeki örnekleri hariç tutmasıdır. Ancak bu tür bir agresif bölme, geniş veri kullanılabilirliğine bağlıdır.

RUL tahmini için kullanılan regresyon modelleri, sızıntı sorununa göre daha ciddi bir şekilde etkilendi. Rastgele bölünmüş yöntemi kullanmak, aşırı fazla yerleştirme için yol gösterir. Gerileme sorunları için bölme, T<sub>c</sub> 'den önceki hataları olan varlıklara ait kayıtlar eğitim kümesine gitmelidir. Kesme sonrasında hatalara sahip varlıkların kayıtları test kümesine gider.

Eğitim ve test için verileri bölmek için bir en iyi yöntem, bir bölünmüş varlık KIMLIĞI kullanmaktır. Bölme, model performansının test edilirken Eğitim kümesinde kullanılan varlıkların hiçbirinin kullanılmadığı gibi olmalıdır. Bu yaklaşımı kullanarak, bir modelin yeni varlıklarla daha gerçekçi sonuçlar sağlaması daha iyi bir şansı vardır.

### <a name="handling-imbalanced-data"></a>İmledengelenmiş verileri işleme
Sınıflandırma sorunları ' nda, bir sınıfa ait diğer diğerlerinden daha fazla örnek varsa, veri kümesi _imdengeli_ olarak kabul edilir. İdeal olarak, farklı sınıflar arasında farklılaşmayı etkinleştirmek için eğitim verilerinde her bir sınıfın yeterli temsilcisi tercih edilir. Bir sınıf verilerin %10 ' dan küçükse, verilerin imdende olduğu kabul edilir. Eksik temsil edilen sınıfa _minınlık sınıfı_ denir.

Birçok PdM sorunu, bir sınıfın diğer sınıf veya sınıflarla karşılaştırıldığında ciddi bir şekilde gösterildiği gibi, imdengeli veri kümeleri gibi yüz. Bazı durumlarda, minınlık sınıfı toplam veri noktalarının yalnızca% 0,001 ' i olabilir. Sınıf dengesizliği PdM için benzersiz değil. Hataların ve anormalilerin nadir görülen diğer etki alanları, örnekler, sahtekarlık algılama ve ağ erişimi gibi benzer bir sorun gibi durumlarda Bu hatalarda minınlık sınıfı örnekleri yapılır.

Veride bir sınıf dengesizliği sayesinde, çoğu standart öğrenme algoritmalarının performansı tehlikeye atılırsa, bu da genel hata oranını en aza indirmektir. %99 negatif ve %1 pozitif örnek içeren bir veri kümesi için, bir model tüm örnekleri negatif olarak etiketleyerek %99 kesinliğini sağlamak üzere gösterilebilir. Ancak model tüm olumlu örnekleri yanlış sınıflandırır; doğruluk yüksek olsa da, algoritma kullanışlı bir yöntem değildir. Sonuç olarak, _hata oranına ilişkin genel doğruluk_ gibi geleneksel değerlendirme ölçümleri, imdengeli öğrenme için yetersizdir. İmdengeli veri kümeleriyle birlikte çalışırken model değerlendirmesi için diğer ölçümler kullanılır:
- Duyarlık
- Geri çağırma
- F1 puanları
- Maliyet ayarlandı ROC (alıcı işletim özellikleri)

Bu ölçümler hakkında daha fazla bilgi için bkz. [model değerlendirmesi](#model-evaluation).

Ancak, sınıf dengesizliği sorununu gidermenize yardımcı olan bazı yöntemler vardır. Bu iki önemli örnek, _Örnekleme tekniklerine_ ve _Maliyet duyarlı öğrenimine_ sahiptir.

#### <a name="sampling-methods"></a>Örnekleme yöntemleri
İmdengeli öğrenme, eğitim verileri kümesini dengeli bir veri kümesine değiştirmek için örnekleme yöntemlerinin kullanımını içerir. Örnekleme yöntemleri test kümesine uygulanmaz. Birçok örnekleme tekniği olsa da, çoğu düz iletme _rastgele büyük örneklemedir_ ve _örnekleme aşamasındadır_.

_Rastgele aşırı örnekleme_ , minınma sınıfından rastgele bir örnek seçmeyi, bu örneklerin çoğaltılmasını ve bunları eğitim verileri kümesine eklemeyi içerir. Sonuç olarak, minınlık sınıfındaki örneklerin sayısı artmıştır ve sonuç olarak farklı sınıfların örnek sayısını dengeleyin. Fazla örneklemenin bir dezavantajı, belirli örneklerin birden çok örneğinin sınıflandırıcının çok özgü olmasına neden olabilir ve bu da aşırı sığdırma için önde gelir. Modelde yüksek eğitim doğruluğu gösterilebilir, ancak görülmeyen test verileri performansı, en iyi performans olabilir.

Bunun tersine, _örnekleme altında rastgele_ bir çoğunluk sınıfından rastgele bir örnek seçilir ve bu örnekler eğitim veri kümesinden kaldırılır. Ancak, çoğunluk sınıfından örneklerin kaldırılması, sınıflandırıcının çoğunluk sınıfla ilgili önemli kavramları kaçırmasına neden olabilir. Minının sınıfının üzerine örneklendiği ve çoğunluk sınıfının aynı anda örneklendiği _karma örnekleme_ , başka bir önemli yaklaşım.

Birçok karmaşık örnekleme tekniği vardır. Seçilen teknik, veri bilimccisi tarafından veri özelliklerine ve yinelemeli denemeleri sonuçlarına göre değişir.

#### <a name="cost-sensitive-learning"></a>Maliyet duyarlı öğrenme
PdM ' de, minınlık sınıfını oluşturan hatalardan normal örneklerden daha fazla ilgi vardır. Bu nedenle, genellikle algoritmanın hatalara yönelik performansı temel alır. Pozitif bir sınıfı negatif bir sınıf olarak yanlış tahmin etmek, tam tersi de ücretlendirilir. Bu durum genellikle eşit olmayan kayıp veya farklı sınıflardaki öğelerin asimetrik maliyeti olarak adlandırılır. İdeal sınıflandırıcının, çoğunluk sınıfının doğruluğu üzerinde ödün vermeden, minınlık sınıfı üzerinde yüksek tahmin doğruluğu sunması gerekir.

Bu bakiyeye ulaşmak için birden çok yol vardır. Eşit olmayan kayıpla ilgili sorunu azaltmak için, minınlık sınıfının hatalı sınıflandırmasına yüksek maliyetli bir maliyet atayın ve genel maliyeti en aza indirmeye çalışın. _Svms gibi algoritmalar (vektör makinelerini destekler)_ bu yöntemi doğal olarak benimseyerek, olumlu ve olumsuz örneklerin maliyetini eğitim sırasında belirtmesine izin verir. Benzer şekilde, _Artırılmış karar ağaçları_ gibi yükseltme yöntemleri genellikle imledengeli verilerle iyi performans gösterir.

## <a name="model-evaluation"></a>Modeli değerlendirme
Hatalı sınıflandırma, işle ilgili yanlış alarmlar maliyetinin yüksek olduğu PdM senaryoları için önemli bir sorundur. Örneğin, yanlış bir altyapı hatası tahminini temel alan uçak oluşturma kararı zamanlamaları ve seyahat planlarını kesintiye uğratabilir. Bir derlemeyi derleme satırından çevrimdışına almak, gelir kaybına neden olabilir. Bu nedenle, yeni test verileriyle ilgili doğru performans ölçümleriyle model değerlendirmesi kritik öneme sahiptir.

PdM modellerini değerlendirmek için kullanılan tipik performans ölçümleri aşağıda ele alınmıştır:

- [Doğruluk](https://en.wikipedia.org/wiki/Accuracy_and_precision) , sınıflandırıcının performansını açıklamak için kullanılan en popüler ölçümdür. Ancak doğruluk, veri dağıtımları için duyarlıdır ve imledengeli veri kümelerine sahip senaryolar için verimsiz bir ölçüdür. Bunun yerine diğer ölçümler kullanılır. [Karışıklık matrisi](https://en.wikipedia.org/wiki/Confusion_matrix) gibi araçlar, modelin doğruluğunu ve nedenini hesaplamak için kullanılır.
- PdM modellerinin [Duyarlığı](https://en.wikipedia.org/wiki/Precision_and_recall) , yanlış alarmlar oranı ile ilgilidir. Modelin düşük duyarlığı genellikle yanlış alarmlarının daha yüksek bir hızına karşılık gelir.
- [Geri çağırma](https://en.wikipedia.org/wiki/Precision_and_recall) oranı, test kümesindeki hatalardan kaç tanesi model tarafından doğru şekilde tanımlandığını gösterir. Daha yüksek geri çekme ücretleri, doğru hataların tanımlanmasında modelin başarılı olduğu anlamına gelir.
- [F1 puanı](https://en.wikipedia.org/wiki/F1_score) duyarlık ve geri çekmenin harmonik ortasıdır ve değeri 0 (en kötü) ile 1 (en iyi) arasında değişir.

İkili sınıflandırma için
- [Alıcı işletim eğrileri (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) de popüler bir ölçümdür. ROC eğrilerinde model performansı, ROC 'daki bir sabit işletim noktasına göre yorumlanır.
- Ancak, PdM sorunları için _tablo_ ve kaldırma _grafikleri_ daha bilgilendirici. Bunlar yalnızca pozitif sınıfa (hatalara) odaklanırlar ve bu da,
  - _Decle tabloları_ , test örnekleri kullanılarak, hata olasılıkların azalan sırada oluşturulur. Sıralı örnekler daha sonra (en yüksek olasılığa sahip örneklerin %10 ' u, ardından %20, %30, vb.) halinde gruplandırılır. Her bir deconun için oran (true pozitif oran)/(rastgele taban çizgisi) her seferde algoritma performansının tahmin etmesine yardımcı olur. Rastgele taban çizgisi 0,1, 0,2 vb. değerlerini alır.
  - [Grafikleri kaldırın](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) tüm kaldırmalarda, kaldırma gerçek pozitif oranı ile rastgele gerçek pozitif oranlarla çizim yapın. En büyük kazancı gösterdiklerinden, ilki genellikle sonuçların odaklanmasından kaynaklanır. İlk olarak, PdM için kullanıldığında "risk altında" temsilcisi olarak da görülenebilir.

## <a name="model-operationalization-for-predictive-maintenance"></a>Tahmine dayalı bakım için model işlemleştirme

Veri bilimi alıştırması avantajı yalnızca eğitilen model işletimsel yapıldığında gerçekleştirilir. Diğer bir deyişle, yeni, daha önce görülmeyen veriler temelinde tahmine dayalı hale getirmek için modelin iş sistemlerine dağıtılması gerekir.  Yeni verilerin eğitim modelinin _model imzasına_ tam olarak iki şekilde uyması gerekir:
- Tüm özellikler, yeni verilerin her mantıksal örneğinde (tabloda bir satır olduğunu söyleyin) bulunmalıdır.
- Yeni veriler, eğitim verileriyle tam olarak aynı şekilde, uygulanan özelliklerin ve her birinin önceden işlenmesi gerekir.

Yukarıdaki işlem, akademik ve sektör belgelerinde birçok şekilde belirtilmiştir. Ancak aşağıdaki tüm deyimler aynı şeyi ifade ederler:
- Modeli kullanarak _yeni verileri puan_ edin
- _Modeli_ yeni verilere Uygula
- Modeli _Operationleştir_
- Modeli _dağıtma_
- _Modeli_ yeni verilere karşı Çalıştır

Daha önce belirtildiği gibi, PDM için model işlemleştirme, eşlerinden farklıdır. Anomali algılama ve hata algılamayı kapsayan senaryolar genellikle _çevrimiçi Puanlama_ ( _gerçek zamanlı Puanlama_ olarak da adlandırılır) uygular. Burada, model her bir gelen kaydı _puanlar_ ve bir tahmin döndürür. Anomali algılama için tahmin, bir anomali oluşma göstergesidir (örnek: tek sınıf bir SVM). Hata algılama için, hata türü veya hata sınıfı olacaktır.

Buna karşılık PdM, _Batch Puanlama_ içerir. Model imzasına uymak için, yeni verilerdeki Özellikler eğitim verileriyle aynı şekilde oluşturulmalıdır. Yeni veriler için tipik olan büyük veri kümelerinde, özellikler zaman içindeki pencereler ve toplu iş içinde puanlanır. Toplu Puanlama, genellikle [Spark](https://spark.apache.org/) veya [Azure Batch](../../batch/batch-service-workflow-features.md)gibi dağıtılmış sistemlerde yapılır. Her iki seçenek de vardır:
- Akış veri motorları, bellekte Windows üzerinde toplamayı destekler. Bu nedenle, çevrimiçi Puanlama desteklediklerinden emin olabilir. Ancak bu sistemler, dar Windows veya daha geniş pencereler üzerinde seyrek çalışan veriler için uygundur. PdM senaryolarında görüldüğü gibi, daha geniş zaman pencereleri üzerinde yoğun veriler için de ölçeklenemeyebilir.
- Toplu Puanlama kullanılamıyorsa çözüm, çevrimiçi Puanlama ' i aynı anda küçük toplu işlerle işleyecek şekilde uyarlamanız gerekir.

## <a name="solution-templates-for-predictive-maintenance"></a>Tahmine dayalı bakım için çözüm şablonları

Bu kılavuzun son bölümü, Azure 'da uygulanan PdM çözüm şablonlarının, öğreticilerin ve denemeleri 'in bir listesini sağlar. Bu PdM uygulamaları, bazı durumlarda dakikalar içinde bir Azure aboneliğine dağıtılabilir. Bu kişiler, kavram kanıtı tanıtım gösterileri, alternatiflerle denemeler yapmak için korumalı alanlar veya gerçek üretim uygulamalarına yönelik Hızlandırıcılar olarak kullanılabilirler. Bu şablonlar [Azure yapay zeka Galerisi](https://gallery.azure.ai) veya [Azure GitHub](https://github.com/Azure)' da bulunur. Bu farklı örnekler, zaman içinde bu çözüm şablonuna alınacaktır.

| # | Başlık | Açıklama |
|--:|:------|-------------|
| 2 | [Azure tahmine dayalı bakım çözüm şablonu](https://github.com/Azure/AI-PredictiveMaintenance) | Azure ML modelleme ve IoT uzaktan izleme bağlamında tahmine dayalı bakım senaryolarını destekleyebilen tam bir Azure altyapısını gösteren açık kaynaklı bir çözüm şablonu. |
| 3 | [Tahmine Dayalı Bakım için Derin Öğrenme](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | [Bu örnekteki bir blog gönderisine](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance)sahip, tahmine dayalı bakım için Lstm (uzun Short-Term belleği) ağları (yinelenen sinir Networks sınıfı) kullanan bir demo çözümü olan Azure Not defteri.|
| 4 | [Aerospace için Azure tahmine dayalı bakım](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Airustmaintenance için Azure ML v 1.0 'ı temel alan ilk PdM çözüm şablonlarından biri. Bu kılavuz bu projeden kaynaklı. |
| 5 | [IoT Edge için Azure AI araç seti](https://github.com/Azure/ai-toolkit-iot-edge) | TensorFlow kullanarak IoT Edge AI araç seti, Azure IoT Edge uyumlu Docker kapsayıcılarındaki derin öğrenme modellerini paketler ve bu modelleri REST API 'Ler olarak kullanıma sunar.
| 6 | [Azure IoT tahmine dayalı bakım](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite BILGISAYARLARı-önceden yapılandırılmış çözüm. IoT Suite ile airustmaintenance PdM şablonu. Aynı projeyle ilgili [başka bir belge](/previous-versions/azure/iot-accelerators/about-iot-accelerators) ve [izlenecek yol](/previous-versions/azure/iot-accelerators/iot-accelerators-predictive-walkthrough) . |
| 7 | [SQL Server R Services kullanarak tahmine dayalı bakım şablonu](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | R Services tabanlı kalan yararlı ömür senaryosu gösterimi. |
| 8 | [Tahmine dayalı bakım modelleme Kılavuzu](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | R ile [denemeleri](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) ve [veri kümeleri](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) ile Azure Not defteri ve [denemeleri](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) kullanılarak tasarlanan Airustmaintenance veri kümesi özelliği, AzureML v 1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Tahmine dayalı bakım için eğitim kaynakları

Microsoft Azure, genel AI kavramlarıyla ilgili içerik ve eğitimlere ek olarak PdM tekniklerinin arkasındaki temel kavramlar için öğrenme yolları sunmaktadır.

| Eğitim kaynağı  | Kullanılabilirlik |
|:-------------------|--------------|
| [Microsoft Docs: veri bilimcisi rolü](https://docs.microsoft.com/learn/roles/data-scientist) | Genel |
| [Microsoft Docs: AI mühendisi rolü](https://docs.microsoft.com/learn/roles/ai-engineer) | Genel |
| [Microsoft Docs: veri mühendisi rolü](https://docs.microsoft.com/learn/roles/data-engineer) | Genel |
| [Microsoft AI okul](https://www.microsoft.com/ai/ai-school) | Genel |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Genel |
| [Microsoft: yapay zeka ve analiz için YouTube 'daki çalma listeleri](https://www.youtube.com/c/MicrosoftAzure/playlists?view=50&sort=dd&shelf_id=7) | Genel |
| [Microsoft AI göster](https://channel9.msdn.com/Shows/AI-Show) | Genel |
| [AI platformuna genel bakış](https://azure.microsoft.com/overview/ai-platform/) | Genel |
| [AI Laboratuvarı](https://www.microsoft.com/ai/ai-lab) | Genel |
| [Microsoft AI](https://www.microsoft.com/AI) | Genel |
| [Microsoft İş Ortağı Ağı](https://partner.microsoft.com/training/training-center) | İş Ortakları |

Ayrıca, AI üzerindeki ücretsiz MOOCS (büyük ölçüde açık çevrimiçi kurslar), Stanford ve MıT gibi akademik kurumlar ve diğer eğitim şirketleri tarafından çevrimiçi olarak sunulur.
