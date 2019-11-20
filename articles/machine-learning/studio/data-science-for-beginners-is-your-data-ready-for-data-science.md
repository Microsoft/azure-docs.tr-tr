---
title: Veri değerlendirmesi
titleSuffix: ML Studio (classic) - Azure
description: Veri bilimi için hazırlamak için verilerinizin karşılaması gereken dört ölçüt. Bu video ile temel veri değerlendirme yardımcı olmak için somut örnekler vardır.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: ccc422dfb3105fd1e12569a84a4ebfd22182b225
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837810"
---
# <a name="is-your-data-ready-for-data-science"></a>Verileriniz veri bilimi için hazır mı?
## <a name="video-2-data-science-for-beginners-series"></a>Video 2: yeni başlayanlar için veri bilimi serisi
Veri bilimi için hazırlanmaya yönelik temel ölçütlere uyduğundan emin olmak için verilerinizi değerlendirmeyi öğrenin.

Seriden en iyi şekilde yararlanmak için tümünü izleyin. [Videolar listesine gidin](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Bu serideki diğer videolar
*Yeni başlayanlar Için veri bilimi* , beş kısa videodaki veri bilimine hızlı bir giriş niteliğindedir.

* Video 1: [5 soruda veri bilimi yanıtları](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 dakika 14 sn)*
* Video 2: verileriniz veri bilimi için hazırlanıyor mu?
* Video 3: [verilerle yanıt vereceğiniz bir soru sorun](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 dakikalık 17 sn)*
* Video 4: [basit bir modelle bir yanıt tahmin](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) etme *(7 dakikalık 42 sn)*
* Video 5: [diğer insanların işini veri bilimi olarak kopyalama](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 dakikalık 18 sn)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>TRANSCRIPT: verileriniz veri bilimi için hazırlanıyor mu?
"Verileriniz veri bilimi için mi hazırlanıyor?" e hoş geldiniz *Yeni başlayanlar için serinin veri bilimi*'nin ikinci videosu.  

Veri bilimi size istediğiniz yanıtları sunmadan önce, bununla birlikte çalışmak üzere bazı yüksek kaliteli ham malzemeler sağlamanız gerekir. Bir pizza yaparken, ile en iyi şekilde başladığınız malzemeler, son ürünün daha iyi hale getirilmesi. 

## <a name="criteria-for-data"></a>Veri ölçütleri
Veri bilimi 'nde aşağıdakiler dahil olmak üzere birlikte çekilmesi gereken bazı malzemeler vardır:

* Meyle
* Bağlı
* Eksiksiz
* Birlikte çalışmak için yeterli

## <a name="is-your-data-relevant"></a>Verileriniz ilgili mi?
Bu nedenle, ilk olarak ilgili verilerin olması gerekir.

![İlgili veriler ile ilgisiz veriler-verileri değerlendir](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Bu tabloda, bir Boston çubuğu dışında test edilen yedi, son oyunlarda kırmızı Sox batılamaları ve en yakın kullanım mağazasındaki mılların fiyatı gibi, tablo, bir Boston çubuğunun dışında, ortalama

Bu tüm kusursuz veriler. Yalnızca bu, ilgili olmaması hatadır. Bu sayılar arasında belirgin bir ilişki yoktur. Birisi size geçerli MILA 'nın ve kırmızı Sox Bama ortalaması verdiyse, kan alkol içeriğini tahmin etmenin bir yolu yoktur.

Şimdi sağ taraftaki tabloya bakın. Bu kez her bir kişiye ait gövde kütle, sahip oldukları drks sayısını da ölçdü.  Her satırdaki numaralar artık birbirleriyle ilgilidir. Gövdelerimin kütle ve sahip olduğumu olan Margarm sayısını belirtdiğimde, kan alkol İçeriğimde bir tahmin yapabilirsiniz.

## <a name="do-you-have-connected-data"></a>Verileri bağladınız mı?
Sonraki malzemeler bağlantılı veri.

![Bağlı veriler ve bağlantısı kesilen veriler-veri ölçütleri, veriler hazırlanıyor](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Hamburger kalitesindeki bazı ilgili veriler aşağıda verilmiştir: Grill sıcaklığı, Patty ağırlığı ve yerel yiyecek dergisindeki derecelendirme. Ancak soldaki tablodaki boşluklara dikkat edin.

Çoğu veri kümesinde bazı değerler eksik. Bunun gibi boşluklar olması yaygındır ve bunları çözmek için çeşitli yollar vardır. Ancak çok fazla yoksa, verileriniz Isviçre Cheese gibi görünecektir.

Sol taraftaki tabloya bakarsanız, çok fazla veri eksikse, Grill sıcaklığı ve Patty ağırlığı arasında herhangi bir ilişki türüyle karşılaşmaları zordur. Bu örnek, bağlantısı kesilmiş verileri gösterir.

Sağ taraftaki tablo, bağlantılı verilerin tam ve tam bir örneğidir.

## <a name="is-your-data-accurate"></a>Verileriniz doğru mu?
Sonraki malzemeler doğruluk. İsabet için dört hedef aşağıda verilmiştir.

![Doğru veriler ve yanlış veri verileri ölçütü](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Sağ üst köşedeki hedefe bakın. Bulanlar gözlük etrafında sıkı bir gruplama vardır. Kuşkusuz bu, doğru. Veri bilimi dilinde, doğru bir şekilde, aşağıdaki hedefte bulunan performans da doğru olarak değerlendirilir.

Bu okların merkezini eşleştirdiyseniz, bu gözle çok yakın olduğunu görürsünüz. Oklar, tüm hedef çevresinde yayıldığında, bu sayede kesin olarak değerlendirilir, ancak doğru olarak kabul edilir.

Şimdi sol üst hedefe bakın. Burada ok, sıkı bir gruplama olan çok yakın bir şekilde bir araya ulaştı. Bunlar kesindir, ancak Merkez Bulls göz önünde olduğundan doğru değildir. Sol alt hedefteki oklar hem yanlış hem de kesin. Bu arşiv daha fazla uygulamaya ihtiyaç duyuyor.

## <a name="do-you-have-enough-data-to-work-with"></a>Çalışmak için yeterli veri yok mu?
Son olarak, malzemeler #4 yeterli veri.

![Analiz için yeterli veri yok mu? Veri değerlendirmesi](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Tablodaki her bir veri noktasını, boyama içindeki bir fırça darbesi olarak düşünün. Yalnızca birkaç tane varsa boyama, ne olduğunu söylemek zor olabilir.

Biraz daha fazla fırça vuruşu eklerseniz, boyama biraz daha net bir şekilde başlatılır.

Yeterince fazla vuruş yaptığınızda yalnızca bazı geniş kararlar almak için yeterli bilgi alırsınız. Bunu ziyaret etmek istediğim bir yerde mi? Açık su gibi görünen parlak, yani tatilde olduğu gibi görünür.

Daha fazla veri eklediğinizde resim daha net hale gelir ve daha ayrıntılı kararlar alabilirsiniz. Şimdi, sol bankadaki üç otelye bakabilirsiniz. Ön planda bulunan mimari özelliklerine fark edebilirsiniz. Görünümü nedeniyle, üçüncü tabanda kalmak da tercih edebilirsiniz.

İlgili, bağlı, doğru ve yeterince veriler sayesinde, bazı yüksek kaliteli veri bilimi sağlamak için gereken tüm malzemeler vardır.

Microsoft Azure Machine Learning Studio (klasik) *Yeni başlayanlar Için veri bilimi* 'ndeki diğer dört videoyu kullanıma aldığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar
* [Machine Learning Studio (klasik) ile ilk veri bilimi denemesini deneyin](create-experiment.md)
* [Microsoft Azure Machine Learning bir giriş alın](/azure/machine-learning/preview/overview-what-is-azure-ml)
