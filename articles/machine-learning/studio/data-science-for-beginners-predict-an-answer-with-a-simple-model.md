---
title: Gerileme modelleriyle yanıtları tahmin etme
titleSuffix: ML Studio (classic) - Azure
description: Yeni başlayanlar için veri bilimi video 4 ' te bir fiyat tahmin etmek için basit regresyon modeli oluşturma. Hedef verilerle doğrusal bir gerileme içerir.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 6ad9c7912eee6c3f5ec55b9cd7ab340bc79c9db7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837767"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Basit model ile yanıtı tahmin etme
## <a name="video-4-data-science-for-beginners-series"></a>Video 4: yeni başlayanlar için veri bilimi serisi
Yeni başlayanlar için veri bilimi video 4 ' te bir elmas fiyatını tahmin etmek için basit bir regresyon modeli oluşturmayı öğrenin. Hedef verilerle bir regresyon modeli çizeceğiz.

Seriden en iyi şekilde yararlanmak için tümünü izleyin. [Videolar listesine gidin](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Bu serideki diğer videolar
*Yeni başlayanlar Için veri bilimi* , beş kısa videodaki veri bilimine hızlı bir giriş niteliğindedir.

* Video 1: [5 soruda veri bilimi yanıtları](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 dakika 14 sn)*
* Video 2: verileriniz [veri bilimi için hazırlanıyor mu?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 dakikalık 56 sn)*
* Video 3: [verilerle yanıt vereceğiniz bir soru sorun](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 dakikalık 17 sn)*
* Video 4: basit bir modelle bir yanıt tahmin etme
* Video 5: [diğer insanların işini veri bilimi olarak kopyalama](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 dakikalık 18 sn)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Dökümü: basit bir modelle bir yanıt tahmin etme
"Yeni başlayanlar için veri bilimi" serisinde dördüncü videoya hoş geldiniz. Bu, basit bir model oluşturacağız ve tahmin yapacağız.

Bir *model* , verilerimiz hakkında Basitleştirilmiş bir hikayeye sahiptir. Ne anlama geldiğini göstereceğiz.

## <a name="collect-relevant-accurate-connected-enough-data"></a>İlgili, doğru, bağlı, yeterli miktarda veri toplayın
Elmas için alışveriş yapmam istiyorum. 1,35 simgeyi seçtiğinizde dörtgeni için bir ayarla ve ne kadar ücretlendirildiğine ilişkin bir fikir almak istiyorum. Bir not defteri ve kalem ' i, jekaynak Mağazası 'na götürür ve bu durumda tüm elmasların fiyatını ve karatların ne kadarının olduğunu bir yere yazdım. İlk elmas ile başlayan 1,01 karayı ve $7.366.

Şimdi bunu, depodaki tüm diğer eşkenar dörtgende devam ediyor.

![Elmas veri sütunları](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Listemizin iki sütuna sahip olduğuna dikkat edin. Her sütunda farklı Karaların ve fiyata göre farklı bir öznitelik ağırlığı vardır ve her satır tek bir baklava temsil eden tek bir veri noktasıdır.

Aslında bir tablo olan küçük bir veri kümesi oluşturduk. Kalite ölçütlerinizle buluşduğuna dikkat edin:

* Veriler, **ilgili** ağırlıklarla kesinlikle ilgilidir
* Bu, yazdığımız fiyatlar için **doğru** denetliyoruz
* **Bağlantı kuruldu** -bu sütunlardan birinde boşluk yok
* Ayrıca, göreceğiniz gibi, sorularınızı yanıtlamak için **yeterli veri yok**

## <a name="ask-a-sharp-question"></a>Keskin bir soru sorun
Artık sorularımıza keskin bir şekilde değineceğiz: "1,35 simgeyi seçtiğinizde baklava ücreti alma maliyeti ne kadar olacaktır?"

Listenizde bir 1,35 simgeyi seçtiğinizde dörtgeni yok, bu nedenle sorunun yanıtını almak için verilerimizin geri kalanını kullanmanız gerekir.

## <a name="plot-the-existing-data"></a>Mevcut verileri çiz
Yapacağımız ilk şey, ağırlıklarını grafik olarak adlandırılan bir yatay numara çizgisi çizmektir. Ağırlıkların aralığı 0 ' dan 2 ' dir; bu nedenle, her yarı Carat için bu aralığı ve put işaretlerini içeren bir çizgi çizeceğiz.

Ardından, fiyatı kaydetmek ve yatay ağırlık eksenine bağlamak için dikey bir eksen çizeceğiz. Bu, dolar birimi cinsinden olacaktır. Şimdi bir koordinat eksenleri kümesi sunuyoruz.

![Ağırlık ve fiyat eksenleri](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Bu verileri şimdi ele alacak ve bir *dağılım*çiziminde etkinleştireceğiz. Bu, sayısal veri kümelerini görselleştirmenin harika bir yoludur.

İlk veri noktası için 1,01 karada dikey bir çizgi sunuyoruz. Ardından, $7.366 adresinden yatay bir çizgi duyduk. Nerede karşılıyorsa bir nokta çiztik. Bu, ilk baklava mizi temsil eder.

Şimdi bu listedeki her bir baklava aşamasından ve aynı şeyi yapacağız. Bu işlemi yaptığımız zaman, her elmas için bir tane olmak üzere bir dizi nokta.

![Dağılım çizimi](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Modeli veri noktaları aracılığıyla çizme
Artık noktalara ve squınt 'e baktığınızda, koleksiyon FAT, benzer bir satır gibi görünür. Markemizi ele geçirebilir ve üzerinden düz bir çizgi çiziyoruz.

Bir çizgi çizerek bir *model*oluşturduk. Bunu gerçek dünya alarak ve uyarlaması bir çizgi üzerine getirerek düşünün. Artık çizgi yanlış; satır tüm veri noktalarına gitmez. Ancak kullanışlı bir basitleştirmesi.

![Doğrusal regresyon çizgisi](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Tüm noktaların tam olarak çizgi üzerinden gitmeyeceği aslında Tamam ' dır. Veri bilimcileri bunu, modelin bu olduğunu, ve ardından her noktanın onunla ilişkili bir *gürültü* veya *varyans* olduğunu söyleyerek anlamıştır. Temel alınan mükemmel ilişki bulunur ve ardından gürültü ve belirsizlik ekleyen griyi, gerçek dünyasın.

Soruyu ne *kadar çok ne kadar* yanıtlamaya çalıştığımız için bu bir *gerileme*adı verilir. Düz bir çizgi kullandığımızda *Doğrusal bir gerileme*.

## <a name="use-the-model-to-find-the-answer"></a>Yanıtı bulmak için modeli kullanın
Şimdi bir modelimiz var ve bu soruyu sorduk: 1,35 simgeyi seçtiğinizde baklava maliyeti ne kadar olacaktır?

Sorularınızı yanıtlamak için, 1,35 Karatları ve dikey bir çizgi çiztik. Model çizgisini nereden kesiştireceğiz, dolar ekseninin yatay bir çizgiyi duyduk. 10.000 ' de hemen isabetle gelir. Hazırım! Yanıt: 1,35 simgeyi seçtiğinizde, $10.000 hakkında elmas maliyetlerinden bahseden fazla.

![Modeldeki yanıtı bulma](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Güven aralığı oluşturma
Bu tahmine ne kadar duyarlı olduğunu merak etmek doğal bir değer. 1,35 simgeyi seçtiğinizde dörtgeni 'nin $10.000 ' e yakın olacağını veya çok daha yüksek veya daha düşük bir değere sahip olup olmadığını bilmemiz yararlı olur. Bu sorunu anlamak için, noktaların çoğunu içeren regresyon çizgisinin etrafında bir zarf çizelim. Bu zarf, *güven sıklığımızda*adı verilir: Bu, geçmişte olduğu gibi fiyatların bu zarfa düştiğinden emin veriyoruz. 1,35 simgeyi seçtiğinizde çizgisinin, bu zarfın üst ve alt kısmının kesiştiği yerin üzerinde iki yatay çizgi çizebiliriz.

![Güvenirlik aralığı](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Artık güven sıklığımız hakkında bir şey olabileceğini varsayalım: 1,35 simgeyi seçtiğinizde dörtgeni 'nin fiyatının $10.000 hakkında olduğunu, ancak bu değer en az $8.000 kadar olabilir ve bu, $12.000 kadar yüksek olabilir.

## <a name="were-done-with-no-math-or-computers"></a>Hiç matematik veya bilgisayar olmadan yaptık
Veri bilimcilerinin ne yapması gerektiğini aldık ve bunu çizim yaparken yapmanız gerekenler:

* Verilerle yanıtlayabilmemiz için bir soru sorduk
* *Doğrusal regresyon* kullanarak bir *model* oluşturduk
* *Güven aralığı* ile bir *tahmin*yaptık

Ve bunu yapmak için matematik veya bilgisayar kullandık.

Şimdi... gibi daha fazla bilgi edinmemiz halinde

* elmas kesilmiş
* renk çeşitlemeleri (baklava 'nın beyaz olması için ne kadar yakın olduğunu)
* elmas içindeki ekleme sayısı

... daha sonra daha fazla sütun vardı. Bu durumda, matematik yararlı hale gelir. İkiden fazla sütun varsa, kağıt üzerine noktalar çizmek zordur. Matematik, bu satırı veya düzlemi verilerinize Çok fazla uydurmanıza imkan tanır.

Ayrıca, yalnızca bir eşkenar dörtgeniz yerine 2000 veya 2.000.000 vardı, bu işi bir bilgisayar ile çok daha hızlı bir şekilde gerçekleştirebilirsiniz.

Bugün, doğrusal regresyon yapma hakkında konuşduk ve verileri kullanarak bir tahmin yaptık.

"Yeni başlayanlar için veri bilimi" bölümündeki diğer videoları Microsoft Azure Machine Learning Studio (klasik) ' den kontrol ettiğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar
* [Machine Learning Studio (klasik) ile ilk veri bilimi denemesini deneyin](create-experiment.md)
* [Microsoft Azure Machine Learning bir giriş alın](/azure/machine-learning/preview/overview-what-is-azure-ml)
