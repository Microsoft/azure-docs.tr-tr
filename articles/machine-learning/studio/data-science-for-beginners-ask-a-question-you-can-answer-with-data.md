---
title: Soru verilerinin yanıt verebilir olduğunu sorma
titleSuffix: ML Studio (classic) - Azure
description: Yeni başlayanlar video 3 için veri bilimi ile net bir veri bilimi sorusunu nasıl formülleyeceğinizi öğrenin. Sınıflandırma ve regresyon sorularının bir karşılaştırmasını içerir.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 26837337b49d79a26404fd6709b036f6907720f8
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838849"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Verilerle yanıtlayabileceğiniz bir soru sorun
## <a name="video-3-data-science-for-beginners-series"></a>Video 3: yeni başlayanlar için veri bilimi serisi
Bir veri bilimi sorununu, yeni başlayanlar için veri bilimi video 3 ' teki bir soruya nasıl formülleyeceğinizi öğrenin. Bu videoda, sınıflandırma ve regresyon algoritmalarıyla ilgili soruların karşılaştırması bulunur.

Seriden en iyi şekilde yararlanmak için tümünü izleyin. [Videolar listesine gidin](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Bu serideki diğer videolar
*Yeni başlayanlar Için veri bilimi* , beş kısa videodaki veri bilimine hızlı bir giriş niteliğindedir.

* Video 1: [5 soruda veri bilimi yanıtları](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 dakika 14 sn)*
* Video 2: verileriniz [veri bilimi için hazırlanıyor mu?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 dakikalık 56 sn)*
* Video 3: verilerle yanıtlayacağınız bir soru sorun
* Video 4: [basit bir modelle bir yanıt tahmin](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) etme *(7 dakikalık 42 sn)*
* Video 5: [diğer insanların işini veri bilimi olarak kopyalama](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 dakikalık 18 sn)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Döküm: verilerle yanıtlayacağınız bir soru sorun
"Yeni başlayanlar için veri bilimi" serisindeki üçüncü videoya hoş geldiniz.  

Bu arada, verilerle yanıt alabileceğiniz bir soruyu formülle ilgili bazı ipuçları alacaksınız.

Bu videodan daha eski olan iki videoyu ilk kez izlediğiniz takdirde daha fazla bilgi edinebilirsiniz: "5 soru veri bilimi yanıt verebilir" ve "Verileriniz veri bilimi için hazır mi?"

## <a name="ask-a-sharp-question"></a>Keskin bir soru sorun
Veri bilimi, bir soruya cevap tahmin etmek için adları (kategoriler veya Etiketler olarak da anılır) ve sayıları kullanma sürecimiz hakkında konuşdu. Ancak, yalnızca herhangi bir soru olamaz; Bu, *keskin bir soru olmalıdır.*

Bir soru sorusunun bir ad veya sayıyla yanıtlanması gerekmez. Keskin bir soru olmalıdır.

Her türlü soruyu eksiksiz bir şekilde yanıtlayacağınız Genie ile bir sihirli lamba bulduğunuzu varsayalım. Ancak, bu, kendi cevaplarını bir yandan da gelişdikleri kadar kafa karıştırıcı olarak yapmaya çalışacak harika bir Genie. Bunları bir soru ile sabitlemek istersiniz, bu sayede yardım istemediklerinden, ancak neyi bilmek istediğinizi söylüreceğiz.

"Stokum ile neler olur?" gibi bir soru sormanız durumunda Genie yanıt verebilir "fiyat değişecektir". Bu çok iyi bir yanıt, ancak yararlı değildir.

Ancak "stokumun satış fiyatı ertesi hafta ne olur?" gibi bir keskin soru soruyorsa, Genie size yardımcı olamaz, ancak size belirli bir yanıt verebilir ve satış fiyatı tahmin eder.

## <a name="examples-of-your-answer-target-data"></a>Yanıtınızı örnekler: hedef veriler
Sorunuzu girdikten sonra verilerinizde yanıt örneklerine sahip olup olmadığınızı kontrol edin.

Sorumız "hisse senedi satış fiyatından sonraki hafta ne olacak?" ise daha sonra, verilerimizin hisse senedi fiyat geçmişini içerdiğinden emin olmak istiyoruz.

Sorumız "ilk kez hangi arabadan önce başarısız oluyor?" ise " daha sonra verilerimizin önceki hatalarla ilgili bilgiler içerdiğinden emin olmak istiyoruz.

![Hedef veri-yanıtınıza örnek olarak verilebilir. Bir veri bilimi sorusunu formülleştirmek.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Bu yanıt örneklerine bir hedef denir. Bir hedef, bir kategori veya sayı olup olmadığı, gelecekteki veri noktalarıyla ilgili tahmin etmeye çalışıyoruz.

Herhangi bir hedef veriniz yoksa bir miktar almanız gerekir. Sorunuzu bu olmadan yanıtlayamaz.

## <a name="reformulate-your-question"></a>Sorunuzu yeniden formülleştirmek
Bazen daha kullanışlı bir yanıt almak için sorunuzu tekrar deneyebilirsiniz.

"Bu veri noktası A veya B?" sorusu bir şeyin kategorisini (veya adını veya etiketini) tahmin eder. Yanıt vermek için bir *Sınıflandırma algoritması*kullanıyoruz.

"Ne kadar çok?" sorusu ya da "kaç tane?" bir miktarı tahmin eder. Yanıt vermek için bir *gerileme algoritması*kullanıyoruz.

Bunları nasıl dönüştürebileceğinizi görmek için, "Bu okuyucuya en ilginç haber hikayesi nedir?" sorusuna göz atalım. Birçok olasılıktan tek bir seçim tahminini ister. diğer bir deyişle, "Bu A veya B veya C ya da D mi?" -ve bir sınıflandırma algoritması kullanır.

Ancak, bu soruyu "Bu listedeki her hikaye için ne kadar ilginç olsun?" olarak yeniden belirlerseniz yanıt vermek daha kolay olabilir. Artık her makaleye sayısal bir puan verebilir ve en yüksek skor makalesini kolayca belirleyebilirsiniz. Bu, sınıflandırma sorusunun bir gerileme sorusuna veya ne kadar çok olduğunu ifade edemidir?

![Sorunuzu yeniden formüledin. Sınıflandırma sorusu ile gerileme sorusu.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Bir soruya nasıl yanıt verebileceği konusunda bir soru sorabilirsiniz.

Haber hikayemizdeki örnekteki gibi bazı algoritmaların benzer olduğunu fark edeceksiniz. Size en faydalı yanıtı veren algoritmayı kullanmak için sorunuzu yeniden formüle ekleyebilirsiniz.

Ancak, en önemli öneme sahip olmak için, verilerle yanıtlayacağınız soru-cevap sorusuna sorun. Ve yanıt vermek için doğru verilere sahip olduğunuzdan emin olun.

Verilerle cevaplandığınız bir soruyu sormak için bazı temel ilkeler hakkında konuşuyoruz.

"Yeni başlayanlar için veri bilimi" bölümündeki diğer videoları Microsoft Azure Machine Learning Studio (klasik) ' den kontrol ettiğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar
* [Machine Learning Studio (klasik) ile ilk veri bilimi denemesini deneyin](create-experiment.md)
* [Microsoft Azure Machine Learning bir giriş alın](/azure/machine-learning/preview/overview-what-is-azure-ml)
