---
title: Yeni Başlayanlar için Veri Bilimleri
titleSuffix: ML Studio (classic) - Azure
description: Yeni başlayanlar için veri bilimi, 5 Sorulık veri bilimi cevaplarından itibaren 5 kısa videolarda temel kavramları öğretir. Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: feb49b4e12bd304b49e0c50cd43ed0be52761532
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838781"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Yeni Başlayanlar için Veri Bilimi video 1: Veri bilimiyle ilgili 5 sorunun yanıtı
En *Yeni başlayanlar Için veri bilimi* , popüler bir veri bilimcundan beş kısa videoda hızlı bir giriş yapın. Bu videolar temel olmakla birlikte, veri bilimi veya veri bilimcileri ile çalışmanız ile ilgileniyor olsanız da yararlıdır.

Bu ilk videoda, veri bilimi yanıt verebilir soru türleri hakkında bilgi sağlanır. Seriden en iyi şekilde yararlanmak için tümünü izleyin. [Videolar listesine gidin](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Bu serideki diğer videolar
*Yeni başlayanlar Için veri bilimi* , yaklaşık 25 dakika süren veri bilimi için hızlı bir giriş niteliğindedir. Beş videonun tümüne göz atın:

* Video 1:5 soruda veri bilimi yanıtları
* Video 2: verileriniz [veri bilimi için hazırlanıyor mu?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 dakikalık 56 sn)*
* Video 3: [verilerle yanıt vereceğiniz bir soru sorun](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 dakikalık 17 sn)*
* Video 4: [basit bir modelle bir yanıt tahmin](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) etme *(7 dakikalık 42 sn)*
* Video 5: [diğer insanların işini veri bilimi olarak kopyalama](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 dakikalık 18 sn)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Döküm: 5 soruda veri bilimi yanıtları
N! *Yeni başlayanlar için video serisi veri bilimi*'ne hoş geldiniz.

Veri bilimi, bu nedenle herhangi bir denklem veya jargon ile programlama yapmadan ilgili temel bilgileri açıklayacağız.

Bu ilk videoda, "5 sorudaki veri bilimi cevapları" hakkında konuşacağız.

Veri bilimi, soruların yanıtlarını tahmin etmek için sayılar ve adlar (kategoriler veya Etiketler olarak da bilinir) kullanır.

Sizi şaşırtabilir, ancak *veri bilimi yanıt veren beş soru*vardır:

* Bu A veya B mi?
* Bu tuhaf mi?
* Ne kadar – veya – kaç?
* Bu nasıl düzenlenir?
* Bundan sonra ne yapmam gerekir?

Bu sorulardan her biri algoritmalar olarak adlandırılan ayrı bir makine öğrenimi yöntemi ailesiyle yanıtlanmıştır.

Bir algoritmayla ilgili olarak bir algoritmaların yanı sıra verilerinizi göz önünde bulundurmanız yararlı olur. Bir algoritma, yanıt almak için verileri nasıl birleştirip karıştıracağınızı söyler. Bilgisayarlar bir blender gibidir. Bu, algoritmaların büyük bir bölümünü sizin için çok daha hızlı bir şekilde işler ve oldukça hızlıdır.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Soru 1: Bu A veya B? sınıflandırma algoritmalarını kullanır
Sorudan başlayalım: Bu A veya B?

![Sınıflandırma algoritmaları: Bu A veya B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Bu algoritma ailesine iki sınıf sınıflandırma denir.

Yalnızca iki olası yanıt içeren herhangi bir soru için faydalıdır.

Örneğin:

* Bu tire sonraki 1.000 mil içinde başarısız olur: Evet veya Hayır?
* Bu müşterilere daha fazla müşteri getiren: $5 kupon veya %25 indirim?

Bu soru, iki seçenekten fazlasını eklemek için de işlemindeki olabilir: Bu A veya B veya C ya da D, vb.?  Bu, birden çok Lass sınıflandırması olarak adlandırılır ve birkaç (veya birkaç bin) olası yanıt olduğunda faydalıdır. Birden çok Lass sınıflandırması en olası birini seçer.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Soru 2: Bu tuhaf? anomali algılama algoritmalarını kullanır
Sonraki soru veri bilimi yanıt verebilir: Bu tuhaf? Bu soru, anomali algılama adlı bir algoritma ailesiyle yanıtlanmıştır.

![Anomali algılama algoritmaları: Bu tuhaf?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Kredi kartınız varsa, anomali algılamada zaten benefited. Kredi kartı şirketiniz, satın alma modellerinizi analiz ederek olası sahtekarlık konusunda sizi uyarabilir. "Tuhaf" olan ücretler, normalde bir olağandışı pahalı öğesi alışverişi veya satın aldığınız bir mağazada satın alma olabilir.

Bu soru birçok şekilde yararlı olabilir. Örneğin:

* Basınç ölçerli bir otomobil varsa şunları bilmeniz gerekebilir: Bu basınç ölçer normal okunuyor mi?
* Internet 'i izliyorsanız, bu ileti genelde Internet 'ten mi?

Anomali algılama, beklenmeyen veya olağandışı olayları ya da davranışları işaretler. Sorunları nerede aramak için ipuçları verir.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Soru 3: ne kadar? veya kaç? gerileme algoritmalarını kullanır
Makine öğrenimi, yanıtı ne kadar büyük bir zamanda tahmin edebilir? veya kaç? Bu soruyu yanıtlayan algoritma ailesine gerileme adı verilir.

![Gerileme algoritmaları: ne kadar? veya kaç?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regresyon algoritmaları, şu gibi sayısal tahminler yapar:

* Sıcaklığın sonraki Salı günü ne olacak?  
* Dördüncü çeyremin satışları ne olacak?

Bir numara isteyen herhangi bir soruyu yanıtlamaya yardımcı olurlar.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Soru 4: Bu nasıl düzenlenir? kümeleme algoritmalarını kullanır
Son iki soru artık biraz daha gelişmiş.

Bazen bir veri kümesinin yapısını anlamak istiyor musunuz? bu nasıl düzenlenir? Bu soru için, sonuçlarını zaten bildiğiniz örneklere sahip değilsiniz.

Verilerin yapısını yapılandırmak için birçok yol vardır. Bir yaklaşım kümedir. Daha kolay yorum için verileri doğal "clumps" içine ayırır. Kümeleme ile, doğru bir yanıt yoktur.

![Kümeleme algoritmaları: Bu nasıl düzenlenir?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Küme sorularının yaygın örnekleri şunlardır:

* Aynı film türleri hangi izleyicileri beğensin?
* Hangi yazıcı modelleri aynı şekilde başarısız olur?

Verilerin nasıl düzenlendiğini anlayarak, daha iyi anlamış ve tahmin davranışları ve olayları daha iyi anlayabilirsiniz.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Soru 5: Şimdi ne yapmam gerekir? pekiştirmeye dayalı öğrenme algoritmalarını kullanır
Son soru: Şimdi ne yapmam gerekir? – pekiştirmeye dayalı Learning adlı bir algoritma ailesi kullanır.

Pekiştirmeye dayalı Learning, kts ve insanlardan oluşan Fins 'in atlama ve ödüller için nasıl yanıt vereceğini öğreniyor. Bu algoritmalar, sonuçlardan bilgi alır ve sonraki eyleme karar verir.

Genellikle, pekiştirmeye dayalı Learning, insan Kılavuzu olmadan çok sayıda küçük kararlar almak zorunda olan otomatikleştirilmiş sistemler için uygun bir uygulamadır.

![Pekiştirmeye dayalı öğrenme algoritmaları: bundan sonra ne yapmam gerekir?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

BT cevaplarındaki sorulara, genellikle bir makine veya robot tarafından hangi eylemin alınması gerektiği hakkında bilgi verilir. Örnekler şunlardır:

* Bir ev için sıcaklık denetim sistemiysem, sıcaklığını ayarlayın veya olduğu yere bırakın.  
* Kendi kendine yönlendiren bir otomobil: sarı hafif, hızınızı düşüren veya hızlıyım?  
* Robot Vakum için: Vacuuming tutun veya ücretlendirme istasyonuna geri dönün mi?

Pekiştirmeye dayalı öğrenme algoritmaları, verileri giderek, deneme ve hatadan öğrendikleri şekilde toplar.

Yani bu, 5 soruda veri bilimi yanıt verebilir.

## <a name="next-steps"></a>Sonraki adımlar
* [Machine Learning Studio (klasik) ile ilk veri bilimi denemesini deneyin](create-experiment.md)
* [Microsoft Azure Machine Learning bir giriş alın](/azure/machine-learning/preview/overview-what-is-azure-ml)
