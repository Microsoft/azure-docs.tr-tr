---
title: Excel 'den analiz geçirme
titleSuffix: ML Studio (classic) - Azure
description: Excel 'deki ve Azure Machine Learning Studio (klasik) doğrusal regresyon modelleriyle karşılaştırma
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 5f4169753e653a1c6c82c997d37769d8548e76ff
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839432"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>Analizi Excel 'den Azure Machine Learning Studio 'ye geçirme (klasik)

> *Kate Baroni* ve *ben Boatman* , Microsoft 'un veri öngörüleri merkezinde üstün olan kurumsal çözüm mimarları. Bu makalede, var olan regresyon analizi paketini Azure Machine Learning Studio (klasik) kullanarak bulut tabanlı bir çözüme geçirme deneyimlerini anlatmaktadır.

## <a name="goal"></a>Hedef

Projemiz iki hedefle birlikte başlatıldı: 

1. Kuruluşunuzun aylık gelir projeksiyımızın doğruluğunu artırmak için tahmine dayalı analiz kullanın 
2. Azure Machine Learning Studio klasik sürümünü kullanarak sonuçlarımızdan emin olun, iyileştirin, hızlarımızı artırın ve ölçeklendirin. 

Kurumumuz birçok işletme gibi aylık bir gelir tahmin sürecinden geçer. İş analistlerinin küçük takımımız, işlemi desteklemek ve tahmin doğruluğunu artırmak için Azure Machine Learning Studio klasik sürümünü kullanmakla birlikte oluşturulmuştur. Takım, birden fazla kaynaktan veri toplamayı ve hizmet satış tahminiyle ilgili anahtar özniteliklerini tanımlayan istatistiksel analizler aracılığıyla veri özniteliklerini çalıştırmayı birkaç ay harcadı. Sonraki adım, Excel 'deki veriler üzerinde istatistiksel regresyon modellerini prototip oluşturmaya başlamamıştı. Birkaç hafta içinde, geçerli alanı ve finans tahmin işlemlerini gerçekleştiren bir Excel regresyon modeli vardı. Bu, taban çizgisi tahmin sonucu haline geldi. 

Daha sonra, klasik .NET sürümünün tahmine dayalı performans üzerinde nasıl iyileştirebileceğimizi öğrenmek için tahmine dayalı analizlerimizi Studio 'nun klasik sürümüne taşımaya yönelik bir sonraki adımı aldık.

## <a name="achieving-predictive-performance-parity"></a>Tahmine dayalı performans eşliği elde etme
İlk önceliğimiz, klasik .NET sürümü ve Excel regresyon modelleri arasında eşlik elde etmek idi. Aynı veriler ve eğitim ve test verileri için aynı bölme verildiğinde, Excel ve klasik Studio sürümü arasında tahmine dayalı performans eşliği elde etmek istiyorduk. Başlangıçta başarısız oldu. Excel modeli outgerçekleştirdi Studio (klasik) modeli. Hata, klasik Studio sürümündeki temel araç ayarını anlayamasından kaynaklanır. Studio ürün ekibinin klasik sürümüyle eşitlemeden sonra, veri kümelerimiz için gereken temel ayarı daha iyi anlamak ve iki model arasında eşlik elde ediyoruz. 

### <a name="create-regression-model-in-excel"></a>Excel 'de regresyon modeli oluşturma
Excel gerileme, Excel Analizi araç takımı 'nda bulunan standart doğrusal regresyon modelini kullandı. 

*% Absolute bir ortalama hata* ve model için Performans ölçüsü olarak kullandınız. Excel kullanarak çalışma modeline ulaşmak 3 ay sürdü. Son olarak, gereksinimleri anlamak için yararlı olan klasik Studio deneme sürümü için öğrendiğimiz kadarını geliştirdik.

### <a name="create-comparable-experiment-in-studio-classic"></a>Studio 'da karşılaştırılabilir deneme oluşturma (klasik)
Bu adımlar, Studio 'nun klasik sürümünde denemenizi oluşturmak için aşağıdaki adımları izliyoruz: 

1. Veri kümesi, klasik Studio sürümüne (çok küçük dosya) CSV dosyası olarak yüklendi
2. Yeni bir deneme oluşturuldu ve Excel 'de kullanılan veri özelliklerini seçmek için [veri kümesinde sütunları seçme][select-columns] modülünde kullanıldı 
3. Verileri, Excel 'de yapılan aynı eğitim veri kümelerine bölmek için [bölünmüş veri][split] modülünü ( *göreli ifade* moduyla birlikte) kullandı 
4. [Doğrusal regresyon][linear-regression] modülüyle denedik (yalnızca varsayılan seçenekler), belgelenir ve sonuçları Excel regresyon modelinize göre karşılaştırılır

### <a name="review-initial-results"></a>İlk sonuçları İncele
İlk olarak, Excel modeli Studio (klasik) modelini açıkça gerçekleştirdi: 

|  | Excel | Studio (klasik) |
| --- |:---:|:---:|
| Performans | | |
| <ul style="list-style-type: none;"><li>Ayarlanmış R karesi</li></ul> |0,96 |Yok |
| <ul style="list-style-type: none;"><li>Katsayısı <br />Belirlemenin</li></ul> |Yok |0,78<br />(düşük doğruluk) |
| Ortalama mutlak hata |11 9,5 milyon |$19.4 d |
| Ortalama mutlak hata (%) |% 6,03 |% 12,2 |

Machine Learning ekipte geliştiriciler ve veri uzmanları tarafından yaptığımız işlem ve sonuçlarımızı çalıştırdığımızda, kolayca bazı faydalı ipuçları sağladık. 

* Klasik bir Studio sürümünde [Doğrusal regresyon][linear-regression] modülünü kullandığınızda iki yöntem sağlanır:
  * Çevrimiçi gradyan tanımı: daha büyük ölçekli sorunlar için daha uygun olabilir
  * Normal en az kareler: Bu yöntem, en çok insanların doğrusal regresyon duyduklarında düşünebilir. Küçük veri kümelerinde, normal en az kareler daha iyi bir seçim olabilir.
* Performansı artırmak için L2 düzenleme Weight parametresini azaltmayı göz önünde bulundurun. Varsayılan olarak 0,001 olarak ayarlanmıştır, ancak küçük veri Ayarlanmamız için performansı artırmak üzere 0,005 olarak ayarlanır. 

### <a name="mystery-solved"></a>Bilinmeyen bir şekilde çözüldü!
Önerileri uyguladığımızda, Excel 'de olduğu gibi klasik Studio sürümünde aynı temel performansı elde ediyoruz: 

|  | Excel | Studio (klasik) (başlangıç) | Studio (klasik) k/en az kareler |
| --- |:---:|:---:|:---:|
| Etiketli değer |Gerçekler (sayısal) |naklettiğiniz |naklettiğiniz |
| Learner |Excel-> Veri Analizi-> gerileme |Doğrusal regresyon. |Çizgisel Regresyon |
| Learner seçenekleri |Yok |Olarak |normal en az kareler<br />L2 = 0,005 |
| Veri kümesi |26 satır, 3 özellik, 1 etiket. Tüm sayısal. |naklettiğiniz |naklettiğiniz |
| Böl: eğitme |Excel ilk 18 satırı üzerinde eğitilen, son 8 satırda test edildi. |naklettiğiniz |naklettiğiniz |
| Böl: test |Son 8 satıra uygulanan Excel regresyon formülü |naklettiğiniz |naklettiğiniz |
| **Performans** | | | |
| Ayarlanmış R karesi |0,96 |Yok | |
| Belirleme katsayısı |Yok |0,78 |0,952049 |
| Ortalama mutlak hata |11 9,5 milyon |$19.4 d |11 9,5 milyon |
| Ortalama mutlak hata (%) |<span style="background-color: 00FF00;">% 6,03</span> |% 12,2 |<span style="background-color: 00FF00;">% 6,03</span> |

Ayrıca, Excel 'In katılacağı Azure eğitilen modeldeki Özellik ağırlıklarla aynı şekilde karşılaştırılır:

|  | Excel katsayıları | Azure Özellik ağırlıkları |
| --- |:---:|:---:|
| Kesme/sapma |19470209,88 |19328500 |
| Özellik A |0,832653063 |0,834156 |
| Özellik B |11071967,08 |11007300 |
| Özellik C |25383318,09 |25140800 |

## <a name="next-steps"></a>Sonraki Adımlar
Machine Learning Web hizmetini Excel içinde kullanmak istiyorduk. İş analistlerimiz Excel 'i kullanır ve bir Excel verileri satırıyla Machine Learning Web hizmetini çağırmak için bir yol gerekiyordu ve tahmin edilen değeri Excel 'e döndürmelidir. 

Ayrıca, Studio 'nun klasik sürümünde mevcut olan seçenekleri ve algoritmaları kullanarak modelinizi iyileştirmek istiyorduk.

### <a name="integration-with-excel"></a>Excel ile tümleştirme
Çözümümüzde, eğitilen modelden bir Web hizmeti oluşturarak Machine Learning gerileme modelimizi çalıştırın. Birkaç dakika içinde Web hizmeti oluşturulmuştur ve tahmin edilen bir gelir değeri döndürecek şekilde doğrudan Excel 'den çağrıyoruz. 

*Web Hizmetleri panosu* bölümü Indirilebilir bir Excel çalışma kitabı içerir. Çalışma kitabı, Web hizmeti API 'SI ve katıştırılmış şema bilgileri ile önceden biçimlendirilir. *Excel çalışma kitabını indir*' e tıkladığınızda çalışma kitabı açılır ve yerel bilgisayarınıza kaydedebilirsiniz. 

![Excel çalışma kitabını Web Hizmetleri panosundan indir](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Çalışma kitabı açıkken, önceden tanımlanmış parametrelerinizi aşağıda gösterildiği gibi mavi parametre bölümüne kopyalayın. Parametreler girildikten sonra Excel Machine Learning Web hizmetine çağrı görür ve tahmin edilen puanlanmış Etiketler yeşil tahmin edilen değerler bölümünde görüntülenir. Çalışma kitabı, parametreler altına girilen tüm satır öğeleri için eğitilen modelinize göre parametreler için tahmine dayalı olarak oluşturulmaya devam edecektir. Bu özelliğin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Excel 'de Azure Machine Learning Web hizmeti](consuming-from-excel.md)kullanma. 

![Dağıtılan Web hizmetine bağlanan şablon Excel çalışma kitabı](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>İyileştirme ve denemeleri
Excel modelimiz ile bir taban çizgisi olduğuna göre, Machine Learning doğrusal regresyon modelinizi en iyi hale getirmeyi sağlamak üzere ilerliyoruz. İlk veri öğelerinin seçimimizde iyileştirebilmek için modül [filtresi tabanlı özellik seçimini][filter-based-feature-selection] kullandık ve% 4,6 oranında bir performans artışı elde etmemize yardımcı oldu. Gelecekteki projelerde, modelleme için kullanılacak doğru özellik kümesini bulmak üzere veri özniteliklerine yineleme yaparken ABD haftalarını tasarruf edebilen bu özelliği kullanacağız. 

Daha sonra, performansı karşılaştırmak için denemeniz için [Bayeme][bayesian-linear-regression] veya daha fazla [karar ağaçları][boosted-decision-tree-regression] gibi ek algoritmalar eklemeyi planlıyoruz. 

Gerileme denemek istiyorsanız, deneyebileceğiniz iyi bir veri kümesi, çok sayıda sayısal özniteliğe sahip olan enerji verimliliği gerileme örnek veri kümesidir. Veri kümesi, Studio 'nun klasik sürümündeki örnek veri kümelerinin bir parçası olarak sağlanır. Birçok farklı öğrenme modülünü, Isıtma Yükü veya soğutma yükünü tahmin etmek için kullanabilirsiniz. Aşağıdaki grafik, farklı regresyonun, hedef değişken soğutma yükünün tahmini için tahmine dayalı veri kümesine karşı öğrenerek performans karşılaştırması: 

| Model | Ortalama mutlak hata | Kök ortalama kare hatası | Göreli mutlak hata | Göreli kare hatası | Belirleme katsayısı |
| --- | --- | --- | --- | --- | --- |
| Artırılmış karar ağacı |0,930113 |1,4239 |0,106647 |0,021662 |0,978338 |
| Doğrusal regresyon (gradyan) |2,035693 |2,98006 |0,233414 |0,094881 |0,905119 |
| Sinir Ağı Regresyonu |1.548195 |2,114617 |0,177517 |0,047774 |0,952226 |
| Doğrusal regresyon (normal en az kareler) |1,428273 |1,984461 |0,163767 |0,042074 |0,957926 |

## <a name="key-takeaways"></a>Anahtar koymalar
Excel gerilemesini ve klasik Studio denemeleri 'ın paralel sürümünü paralel olarak çalıştırmanın çok fazla öğrenildi. Excel 'de temel modeli oluşturma ve bunu Machine Learning [Doğrusal regresyon][linear-regression] kullanarak modellerle karşılaştırma, Studio 'yu (klasik) öğrenmemize yardımcı olur ve veri seçim ve model performansını geliştirmek için fırsatlar tespit ettik. 

Ayrıca, gelecekteki tahmin projelerini hızlandırmak için [filtre tabanlı özellik seçimini][filter-based-feature-selection] kullanmanın önerildiğinden de karşılaştık. Verilerinize Özellik seçimi uygulayarak, daha iyi bir performans ile klasik Studio sürümünde geliştirilmiş bir model oluşturabilirsiniz. 

Tahmine dayalı analitik tahmin 'i Studio 'nun klasik sürümünden Excel 'e aktarma özelliği, geniş bir iş kullanıcısı kitlelerine başarılı bir şekilde sonuç sağlama yeteneğinin önemli bir artış sağlar. 

## <a name="resources"></a>Kaynaklar
Gerileme ile çalışmanıza yardımcı olacak bazı kaynaklar aşağıda verilmiştir: 

* Excel 'de gerileme. Excel 'de gerileme yapmayı hiç denemediyseniz, bu öğretici şunları kolaylaştırır: [https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regresyon ve tahmin karşılaştırması. Tyler Chessman, Excel 'de zaman serisi tahmininin nasıl yapılacağını açıklayan bir blog makalesi yazdı. Bu makale, doğrusal regresyon hakkında iyi bir başlangıç açıklaması içeriyor. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Normal en az kareler doğrusal regresyon: sorunlar, sorunlar ve Ilgili. Gerileme için bir giriş ve Açıklama: [https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

