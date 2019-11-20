---
title: Müşteri karmaşıklığını çözümle
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasik) kullanarak müşteri karmaşıklığı çözümlemek ve Puanlama yapmak için tümleşik bir model geliştirmeye yönelik örnek olay incelemesi.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 8fd88c3bfad962f264efa030d0a3aea44e95dc8c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839757"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio kullanarak müşteri karmaşıklığını çözümleme (klasik)
## <a name="overview"></a>Genel Bakış
Bu makale, Azure Machine Learning Studio (klasik) kullanılarak oluşturulan bir müşteri karmaşıklığı analiz projesinin başvuru uygulamasını gösterir. Bu makalede, endüstriyel müşteri karmaşıklığı sorununu çözmek için ilişkili genel modeller tartışıyoruz. Ayrıca, Machine Learning kullanılarak oluşturulan modellerin doğruluğunu ölçyoruz ve daha fazla geliştirme için yönergeleri değerlendirdik.  

### <a name="acknowledgements"></a>Onayları
Bu deneme, daha önce Microsoft Azure Machine Learning Studio (klasik) için üretim yöneticisi olan Serge Berger, Microsoft 'ta sorumlu veri Bilimcu ve Roger Barga tarafından geliştirilmiştir ve test edilmiştir. Azure belge ekibi, uzmanlığını tamamen onaylar ve bu teknik incelemeyi paylaşmak için teşekkürler.

> [!NOTE]
> Bu deneme için kullanılan veriler genel kullanıma açık değil. Dalgalanma analizi için makine öğrenimi modelinin nasıl oluşturulacağı hakkında bir örnek için, bkz. [Azure yapay zeka Galerisi](https://gallery.azure.ai/) [Perakende değişim modeli şablonu](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Müşteri karmaşıklığı sorunu
Tüketici pazarında ve tüm kurumsal kesimlerdeki işletmeler, karmaşıklık ile uğraşmak zorunda. Bazen karmaşıklık aşırı ve ilke kararlarını etkiler. Geleneksel çözüm, bir danışman hizmeti, pazarlama kampanyaları veya özel dağıtımlar uygulayarak yüksek eğilimini çüleri tahmin etmek ve ihtiyaçlarını karşılamak için tasarlanmıştır. Bu yaklaşımlar sektörden sektöre farklılık gösterebilir. Hatta belirli bir tüketici kümesinden diğerine, tek bir sektör içinde (örneğin, telekomünikasyon) farklılık gösterebilir.

Yaygın faktör, işletmelerin bu özel müşteri saklama çabalarını en aza indirmelerini gerektirir. Bu nedenle, doğal bir metodolojide dalgalanma olasılığının yanı sıra her müşteriyi puanlayıp ilk N. En önde gelen müşteriler en karlı olabilir. Örneğin, daha karmaşık senaryolarda, özel bir senrme için aday seçiminde bir kar işlevi çalışır. Ancak, bu konular yalnızca karmaşıklığa yönelik tüm stratejinin bir parçasıdır. İşletmelerin ayrıca hesap riskini (ve ilişkili risk toleransını), müdahale düzeyini ve maliyetini ve müşteri kesimlemesini ele almanız gerekir.  

## <a name="industry-outlook-and-approaches"></a>Sektör Outlook ve yaklaşımları
Karmaşıklığın gelişmiş işlemesi, Yetişkin sektörün bir imzadır. Klasik örnek, abonelerin sıklıkla bir sağlayıcıdan diğerine geçiş olduğu bilinen telekomünikasyon sektörüdir. Bu gönüllü karmaşıklık, önemli bir konudur. Üstelik, sağlayıcılar, müşterilerin geçiş yapmak için gereken faktörler olan *karmaşıklık sürücüleri*hakkında önemli bilgi edindi.

Örneğin, Ahize veya cihaz seçimi, cep telefonu işletmesinde iyi bilinen bir dalgalanma sürücüsüdür. Sonuç olarak, popüler bir ilke yeni aboneler için bir ahize fiyatını Subsidize ve bir yükseltme için mevcut müşterilerin tam bir fiyatını ücretlendirir. Geçmişte, bu ilke, müşterilerin yeni bir indirim elde etmek için bir sağlayıcıdan diğerine atlaması sağlar. Bu, buna karşılık, stratejilerini iyileştirmek için istenen sağlayıcılara sahiptir.

Ahize tekliflerindeki yüksek volate, geçerli ahize modellerini temel alan karmaşıklığı modellerini hızlı bir şekilde geçersiz kılan bir faktördür. Ayrıca, cep telefonları yalnızca telekomünikasyon cihazlarından değildir ve aynı zamanda moda de (iPhone 'ı göz önünde bulundurun) sahiptir. Bu Sosyal Öngörüler, düzenli telekomünikasyon veri kümelerinin kapsamı dışındadır.

Modellemeye yönelik net sonucu, karmaşıklığın bilinen nedenlerini ortadan kaldırarak bir ses ilkesini devzlenemez. Aslında, kategorik değişkenlerin (karar ağaçları gibi) miktarını karşılayan klasik modeller dahil sürekli bir modelleme stratejisi **zorunludur**.

Kuruluşlar, müşterilerine büyük veri kümeleri kullanarak büyük veri analizlerini (özellikle büyük verileri temel alan dalgalanma algılama), soruna yönelik etkili bir yaklaşım olarak gerçekleştirmektir. ETL bölümündeki öneriler bölümünde dalgalanma sorununa büyük veri yaklaşımı hakkında daha fazla bilgi edinebilirsiniz.  

## <a name="methodology-to-model-customer-churn"></a>Müşteri dalgalanmasını modelme yöntemi
Müşteri karmaşıklığını çözmeye yönelik yaygın bir sorun çözme işlemi, Şekil 1-3 ' de gösterilmiştir:  

1. Risk modeli, eylemlerin olasılık ve riski nasıl etkilediğini düşünebileceğiniz bir işlem sağlar.
2. Bir müdahale modeli, bir müdahale düzeyinin karmaşıklık olasılığını ve müşteri yaşam süresi değeri (CLV) miktarını nasıl etkileyebileceğini düşünebileceğiniz bir işlem sağlar.
3. Bu analizler, en iyi teklifi sunmak üzere müşteri segmentlerini hedefleyen öngörülü bir pazarlama kampanyasına ilerletilen bir nitel analizine sahiptir.  

![Risk toleransı ve karar modellerinin eyleme dönüştürülebilir Öngörüler nasıl oluşturduğunu gösteren diyagram](./media/azure-ml-customer-churn-scenario/churn-1.png)

Bu ileri yönlü yaklaşım, karmaşıklığı değerlendirmek için en iyi yoldur, ancak karmaşıklıkla gelir: modeller arasında çok modelli bir arşiv ve izleme bağımlılıkları geliştirmemiz gerekir. Modeller arasındaki etkileşim Aşağıdaki diyagramda gösterildiği gibi kapsüllenebilir:  

![Dalgalanma modeli etkileşim diyagramı](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Şekil 4: Birleşik çok modelli, arşiv Etype*  

Müşteri bekletmeye yönelik bir bütünsel yaklaşımı sunduğumuz modeller arasındaki etkileşim anahtardır. Her bir modelin zaman içinde düşmesi gerekli; Bu nedenle, mimari kapalı bir döngüdür (net/DM veri araştırma standardı tarafından ayarlanan, [***3***]).  

Risk kararı-pazarlama segmentinin/ayrışmasının genel döngüsünün nedeni, çok sayıda iş sorunu için geçerli olan genelleştirilmiş bir yapıdır. Karmaşıklık analizi, Basitleştirilmiş bir tahmine dayalı çözüme izin vermediği karmaşık bir iş sorununun tüm nitelikleri sergilediğinden, bu sorun grubunun güçlü bir temsilcisidir. Karmaşıklığa yönelik modern yaklaşımın sosyal yönleri özellikle yaklaşımda vurgulanmıştır, ancak sosyal yönler, herhangi bir modelde olduklarından, bu, modelleme ile modellenir.  

Burada ilgi çekici bir ekleme büyük veri analizinden. Günümüzde iletişim ve perakende işletmeler müşterileri hakkında ayrıntılı veriler topturuz ve çok modelli bağlantı ihtiyacını, Nesnelerin İnterneti ve ubititous gibi daha fazla eğilimleri verilen ortak bir eğilim haline getiririz. BT, işletmenizin birden çok katmanda akıllı çözümler kullanmasına izin veren cihazlar.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Machine Learning Studio (klasik) üzerinde modelleyen modelleme 'yi uygulama
Açıklanan sorun verildiğinde, tümleşik modelleme ve Puanlama yaklaşımı uygulamak için en iyi yol nedir? Bu bölümde, Azure Machine Learning Studio klasik sürümünü kullanarak nasıl başardığımlacağını gösteririz.  

Çok modelli yaklaşım, değişim için global bir arşiv ETYPE tasarlanırken bir olmalıdır. Yaklaşımın Puanlama (tahmine dayalı) bölümü de çok modelli olmalıdır.  

Aşağıdaki diyagramda, karmaşıklığın tahmin edilmesi için Machine Learning Studio (klasik) üzerinde dört Puanlama algoritması kullanan oluşturduğumuz prototipi gösterilmektedir. Çok modelli bir yaklaşım kullanmanın nedeni yalnızca doğruluğu artırmak için bir ensekele sınıflandırıcı oluşturmak ve ayrıca, daha fazla Sığdırma özelliği seçimine karşı koruma sağlamak için de kullanılır.  

![Birçok bağlantılı modülle karmaşık bir Studio (klasik) çalışma alanını gösteren ekran görüntüsü](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Şekil 5: bir dalgalanma modelleme yaklaşımını prototip*  

Aşağıdaki bölümler Machine Learning Studio (klasik) kullanarak uyguladığımız prototip Puanlama modeliyle ilgili daha fazla ayrıntı sağlar.  

### <a name="data-selection-and-preparation"></a>Veri seçimi ve hazırlığı
Modelleri oluşturmak için kullanılan veriler ve müşteriler, müşteri gizliliğini korumak üzere verilerle birlikte bir CRM dikey çözümüyle alınmıştır. Veriler ABD 'deki 8.000 abonelikleri hakkında bilgiler içerir ve üç kaynağı birleştirir: sağlama verileri (abonelik meta verileri), etkinlik verileri (sistem kullanımı) ve müşteri destek verileri. Veriler müşterilerle ilgili iş bilgilerini içermez; Örneğin, bağlılık programı meta verilerini veya kredi puanlarını içermez.  

Veri hazırlığının daha önce başka bir yerde yapıldığını varsaydığı için, basitlik, ETL ve veri temizleme işlemlerinde kapsam kalmadı.

Modelleme için özellik seçimi, rastgele orman modülünü kullanan işleme dahil olan tahmine dayalı kümesinin ön anlam puanlamasını temel alır. Machine Learning Studio (klasik) uygulamasında uygulama için, temsilci özellikleri için Ortalama, ortanca ve aralıkları hesapladık. Örneğin, Kullanıcı etkinliği için minimum ve maksimum değerler gibi nitel verilerine yönelik toplamalar ekledik.

Ayrıca, en son altı ay için zamana bağlı bilgiler de yakalandı. Verileri bir yıl boyunca çözümliyoruz ve istatistiksel olarak önemli eğilimler olsa bile, karmaşıklığın etkisi altı aydan sonra büyük ölçüde azalır.  

En önemli nokta, ETL, özellik seçimi ve modelleme dahil olmak üzere tüm işlemin, Microsoft Azure veri kaynakları kullanılarak Machine Learning Studio (klasik) olarak uygulandığına göre belirlenir.   

Aşağıdaki diyagramlarda kullanılan veriler gösterilmektedir.  

![Ham değerlerle kullanılan verilerin bir örneğini gösteren ekran görüntüsü](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Şekil 6: veri kaynağı alıntısı (karıştırılmış)*  

![Veri kaynağından ayıklanan istatistiksel özellikleri gösteren ekran görüntüsü](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Şekil 7: veri kaynağından ayıklanan Özellikler*
 

> Bu verilerin özel olduğunu ve bu nedenle model ve verilerin paylaşılacağını unutmayın.
> Ancak, genel olarak kullanılabilir verileri kullanan benzer bir model için [Azure yapay zeka Galerisi](https://gallery.azure.ai/): [Telco Müşteri karmaşıklığı](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383)' nda bu örnek deneyime bakın.
> 
> Cortana Intelligence Suite kullanarak bir dalgalanma Analizi modeli uygulama hakkında daha fazla bilgi edinmek için [Bu videoyu](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) , üst düzey Program Yöneticisi Wee Hyong tok tarafından da öneririz. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Prototipte kullanılan algoritmalar
Prototip oluşturmak için aşağıdaki dört makine öğrenimi algoritmasını kullandık (özelleştirme yok):  

1. Lojistik regresyon (LR)
2. Artırılmış karar ağacı (BT)
3. Ortalama Perceptron (AP)
4. Destek vektör makinesi (SVM)  

Aşağıdaki diyagramda, deneme tasarım yüzeyinin, modellerin oluşturulduğu sırayı gösteren bir kısmı gösterilmektedir:  

![Studio deneme tuvalinin küçük bir bölümünün ekran görüntüsü](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Şekil 8: Machine Learning Studio modeller oluşturma (klasik)*  

### <a name="scoring-methods"></a>Puanlama yöntemleri
Etiketli eğitim veri kümesini kullanarak dört modeli puanlıyoruz.  

Ayrıca, Puanlama veri kümesini SAS Enterprise Miner 12 ' nin masaüstü sürümünü kullanarak oluşturulan karşılaştırılabilir bir modele gönderdik. SAS modelinin ve dört Machine Learning Studio (klasik) modellerin doğruluğunu ölçüyoruz.  

## <a name="results"></a>Sonuçlar
Bu bölümde, Puanlama veri kümesine bağlı olarak modellerin doğruluğu hakkında bulgularımızı sunuyoruz.  

### <a name="accuracy-and-precision-of-scoring"></a>Skor doğruluğu ve hassasiyeti
Genellikle, Azure Machine Learning Studio klasik sürümündeki uygulama,% 10-15 (eğri veya AUC altındaki alan) ile aynı şekilde SAS 'nin arkasında olur.  

Bununla birlikte, karmaşıklık bakımından en önemli ölçüm, yanlış sınıflandırma oranıdır: Yani sınıflandırıcının tahmin edilebileceği, gerçekten ne kadar **karmaşıklamadığı ve** henüz özel bir işleme aldıkları, en önemli N çüleri. Aşağıdaki diyagramda, tüm modeller için bu hatalı sınıflandırma hızı karşılaştırılır:  

![4 algoritmaların performansını karşılaştıran eğri grafiğinin alanı](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Şekil 9: eğri altındaki Passau prototip alanı*

### <a name="using-auc-to-compare-results"></a>Sonuçları karşılaştırmak için AUC kullanma
Eğri altındaki alan (AUC), pozitif ve negatif popülasyonlar için puanlar dağıtımları *arasındaki genel* bir ölçü ölçüsünü temsil eden bir ölçümdür. Geleneksel alıcı operatörü özelliği (ROC) grafiğine benzerdir, ancak önemli bir farklılık, AUC ölçüsünün bir eşik değeri seçmenizi gerektirmez. Bunun yerine, **Tüm** olası seçimler üzerinde sonuçları özetler. Buna karşılık, geleneksel ROC grafiğinde dikey eksenin pozitif oranı ve yatay eksende hatalı pozitif hız gösterilir ve sınıflandırma eşiği değişir.   

AUC, modellerin AUC değerleri aracılığıyla karşılaştırılmasını sağladığından farklı algoritmalar (veya farklı sistemler) için değer ölçüsü olarak kullanılır. Bu, meteorology ve biyobilimileri gibi sektörlerde popüler bir yaklaşımdır. Bu nedenle AUC, sınıflandırıcı performansını değerlendirmek için popüler bir araç temsil eder.  

### <a name="comparing-misclassification-rates"></a>Hatalı sınıflandırma oranlarını karşılaştırma
Yaklaşık 8.000 aboneliklerin CRM verilerini kullanarak söz konusu veri kümesindeki hatalı sınıflandırma oranlarını karşılaştırdık.  

* SAS hatalı sınıflandırma oranı% 10-15 idi.
* Machine Learning Studio (klasik) yanlış sınıflandırma oranı, en üstteki 200-300 churanlar için% 15-20 ' di.  

Telekomünikasyon sektöründe, yalnızca bir concierge hizmeti veya diğer özel bir işleme sunarak karmaşıklığı en yüksek riske sahip olan müşterileri ele almak önemlidir. Bu şekilde Machine Learning Studio (klasik) uygulama, SAS modeliyle birlikte sonuçlara erişir.  

Benzer bir şekilde doğru sınıflandırmakla ilgilendiğimiz için aynı belirtece sahip doğruluk, duyarlıktan daha önemlidir.  

Vikipten aşağıdaki diyagramda, uyumlu ve kolay anlaşılır bir grafik ile ilişki gösterilmektedir:  

![İki hedef. Bir hedef isabet işaretlerini gevşek gruplanmış, ancak "düşük doğruluk: iyi, zayıf hassasiyet" olarak işaretlenen Bulls-Eye yakın şekilde gösterir. Başka bir hedef sıkı bir şekilde gruplandırılır, ancak "düşük doğruluk: zayıf gerçeği, iyi duyarlık" olarak işaretlendi.](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Şekil 10: doğruluk ve duyarlık arasında zorunluluğunu getirir*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Yeniden artırılmış karar ağacı modeli için doğruluk ve duyarlık sonuçları
Aşağıdaki grafikte, en doğru dört modelde olacak şekilde gerçekleştirilecek olan, daha önce kullanılabilecek karar ağacı modeli için Machine Learning prototipi kullanılarak Puanlama 'tan ham sonuçlar görüntülenmektedir:  

![Dört algoritma için doğruluk, duyarlık, geri çekme, F puanı, AUC, ortalama günlük kaybı ve eğitim günlüğü kaybını gösteren tablo kod parçacığı](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Şekil 11: Artırılmış karar ağacı model özellikleri*

## <a name="performance-comparison"></a>Performans karşılaştırması
Machine Learning Studio (klasik) modellerini ve SAS kurumsal Miner 12,1 Masaüstü sürümü kullanılarak oluşturulan karşılaştırılabilir bir modeli kullanarak verilerin puanlanması hızını karşılaştırdık.  

Aşağıdaki tabloda algoritmaların performansı özetlenmektedir:  

*Tablo 1. Algoritmaların genel performansı (doğruluğu)*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Ortalama model |En Iyi model |Düşük performanslı |Ortalama model |

Machine Learning Studio (klasik) içinde barındırılan modeller yürütme hızı için% 15-25 oranında gerçekleştirilmiş, ancak doğruluk büyük ölçüde ağırlıklıydı.  

## <a name="discussion-and-recommendations"></a>Tartışma ve öneriler
Telekomünikasyon sektöründe, aşağıdakiler de dahil olmak üzere karmaşıklığı çözümlemek için çeşitli yöntemler ortaya çıktı.  

* Dört temel kategori için ölçümleri türet:
  * **Varlık (örneğin, bir abonelik)** . Karmaşıklık konusu olan abonelik ve/veya müşteri hakkında temel bilgiler sağlayın.
  * **Etkinlik**. Varlıkla ilgili tüm olası kullanım bilgilerini (örneğin, oturum açma sayısı) alın.
  * **Müşteri desteği**. Müşterinin destek günlüklerinden, aboneliğin sorun mu yoksa müşteri desteği ile etkileşimler mi olduğunu göstermek için bilgi toplama bilgileri.
  * **Rekabet ve iş verileri**. Müşteriyle ilgili olabilecek herhangi bir bilgi edinin (örneğin, kullanılamayan veya izlemek zor olabilir).
* Özellik seçimini sağlamak için önem derecesi kullanın. Bu, artırılmış karar ağacı modelinin her zaman bir taahhüt yaklaşımı olduğunu gösterir.  

Bu dört kategorinin kullanılması, kategori başına makul faktörlere göre biçimlendirilmiş olan dizinlere dayalı basit bir *belirleyici* yaklaşımın belirlenmesi, müşterileri karmaşıklık açısından riske almak için yeterli olmalıdır. Ne yazık ki bu kavram çok fazla görünse de, bu, yanlış bir anlama gelir. Bunun nedeni, karmaşıklığın zamana bağlı bir etkidir ve karmaşıklığa katkıda bulunan faktörler genellikle geçici durumlardır. Bir müşterinin bugün ayrılmasının ne şekilde olduğunu düşünbilecekleri müşteri adayları yarın farklı olabilir. Bu nedenle, bir *dayalı* modeli bir zorunludur.  

Bu önemli gözlem, genellikle daha kolay bir satış ve admits basit bir Otomasyon olduğundan Analize iş zekası odaklı bir yaklaşımı tercih eden, genellikle iş açısından çok daha fazla baktı.  

Ancak, Machine Learning Studio (klasik) kullanarak Self Servis analizinin taahhüdünü, bölüm veya departmana göre oluşan dört bilgi kategorisinin, karmaşıklık hakkında makine öğrenimi için değerli bir kaynak haline gelmesinin nedeni.  

Azure Machine Learning Studio klasik sürümünde yer alan başka bir heyecan verici yetenek, zaten kullanılabilir olan önceden tanımlanmış modüllerin deposuna özel bir modül ekleyebilmesidir. Temel olarak bu özellik, kitaplıkları seçmek ve dikey pazarlar için şablon oluşturmak üzere bir fırsat oluşturur. Bu, Pazar yerinde Azure Machine Learning Studio klasik sürümünün önemli bir farklıdır.  

Bu konu başlığı altında, özellikle büyük veri analizle ilgili daha sonra devam etmeyi umuyoruz.
  

## <a name="conclusion"></a>Sonuç
Bu raporda, genel bir çerçeve kullanarak müşteri karmaşıklığının yaygın sorununa neden olan bir yaklaşım ele konusu açıklanmaktadır. Puanlama modelleri için bir prototip kabul ettik ve bu Azure Machine Learning Studio klasik sürümünü kullanarak uyguladık. Son olarak, prototip çözümünün sa 'daki karşılaştırılabilir algoritmalarla ilgili doğruluğu ve performansı değerlendirdik.  

 

## <a name="references"></a>Başvurular
[1] tahmine dayalı analiz: tahminlerin ötesinde, W. Mckgece, bilgi yönetimi, Temmuz/Ağustos 2011, p. 18-20.  

[2] Vikipedi makalesi: [doğruluk ve duyarlık](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [net-DM 1,0: adım adım veri araştırma Kılavuzu](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [büyük veri Pazarlaması: müşterilerinize daha etkin ve sürücü değeri de katılın](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Azure yapay zeka Galerisi](https://gallery.azure.ai/) Içindeki [Telco karmaşıklığı modeli şablonu](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) 
 

## <a name="appendix"></a>Ek
![Dalgalanma prototipi üzerinde bir sununun anlık görüntüsü](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Şekil 12: bir sununun dalgalanma prototipi üzerinde anlık görüntüsü*
