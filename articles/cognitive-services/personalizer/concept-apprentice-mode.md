---
title: Apprenlet modu-kişiselleştirici
description: Herhangi bir kodu değiştirmeden bir modelde güvenle kazanmak için apprence modunu nasıl kullanacağınızı öğrenin.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 531917d9c48915f71354b4cd35747ecd9d33a6f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100385039"
---
# <a name="use-apprentice-mode-to-train-personalizer-without-affecting-your-existing-application"></a>Mevcut uygulamanızı etkilemeden Kişiselleştiriciye eğitme için Apprenlet modunu kullanın

**Gerçek dünya** pekiştirmeye dayalı öğrenimi doğası nedeniyle, bir kişiselleştirici model yalnızca bir üretim ortamında eğitilmiş olabilir. Yeni bir kullanım durumu dağıtıldığında, modelin yeterince eğitilme süresi sürdiğinden, kişiselleştirici modeli verimli bir şekilde gerçekleşmez.  **Apprenlet modu** , bu durumu kolaylaştırır ve herhangi bir kodu değiştiren geliştirici olmadan modelde güven elde etmenizi sağlayan bir öğrenme davranışıdır.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="what-is-apprentice-mode"></a>Apprenlet modu nedir?

Bir apprenlet 'in bir ana bilgisayardan öğrenilmesine benzer ve deneyimle birlikte daha iyi bir sonuç alabilir; Apprenlet modu, mevcut uygulama mantığından elde edilen sonuçları gözlemleyerek kişiselleştirerek öğrenmelerini sağlayan bir _davranıştır_ .

Uygulamayla aynı çıktıyı inceleyerek kişiselleştirilmez. Daha fazla olay akışı olduğunda, kişiselleştirici mevcut mantığı ve sonuçları etkilemeden mevcut uygulamaya _yakalayabilir_ . Azure portal ve API 'de bulunan ölçümler, model öğrenerek performansı anlamanıza yardımcı olur.

Kişiselleştirici, belirli bir anlama düzeyi öğrenmiş ve daha fazla anlaşıladıktan sonra, geliştirici Apprence modundan davranışını çevrimiçi moda değiştirebilir. Bu sırada, kişiselleştirici, işlemleri derecelendirme API 'sindeki eylemleri etkili bir şekilde geliştirmeye başlar.

## <a name="purpose-of-apprentice-mode"></a>Apprenlet modunun amacı

Apprenlet modu, kişiselleştirici hizmetinde ve makine öğrenimi özelliklerinden güvende olmanızı sağlar ve hizmetin, çevrimiçi trafiğe karşı daha fazla bilgi sahibi olmak zorunda kalmadan, ' den öğrenilebilir bilgiler gönderdiğini da sağlar.

Apprence modunu kullanmanın iki ana nedeni şunlardır:

* Azaltıcı **soğuk başlar**: apprence modu, "yeni" modelinin öğrenimi zamanının maliyetinin yönetilmesine ve değerlendirilmesine yardımcı olur. Bu, en iyi eylemi döndürmezme ve% 60-80 etrafında bir verimlilik düzeyi elde etmez.
* **Eylem ve bağlam özellikleri doğrulanıyor**: eylemler ve bağlam içinde gönderilen Özellikler yetersiz veya hatalı, çok fazla, yanlış veya çok büyük olabilir. Özelliklerle ilgili sorunları bulmak ve gidermek için [özellik değerlendirmeleri](concept-feature-evaluation.md) kullanın.

## <a name="when-should-you-use-apprentice-mode"></a>Apprenlet modunu ne zaman kullanmalısınız?

Kişiselleştirici 'yi aşağıdaki senaryolar aracılığıyla geliştirmek için Apprenlet modunu kullanın, böylece kullanıcılarınızın deneyimi kişiselleştirici tarafından etkilenmeden bırakın:

* Yeni bir kullanım durumunda kişiselleştirici uygulamayı uygulamakta olursunuz.
* Bağlam veya eylemlerde göndereceğiniz özellikleri önemli ölçüde değiştirmiş olursunuz.
* Ve bunların ne zaman ve nasıl hesaplanacağını önemli ölçüde değiştirirsiniz.

Apprence modu, etki kişiselleştirmesini bir şekilde ölçmek için etkili bir yöntem değildir. Etkin kişiselleştirmenin, her bir derecelendirme çağrısı için olası en iyi eylemi seçme konusunda ne olduğunu ölçmek için [çevrimdışı değerlendirmeler](concepts-offline-evaluation.md)kullanın.

## <a name="who-should-use-apprentice-mode"></a>Apprenlet modunu kimler kullanmalıdır?

Apprence modu geliştiriciler, veri uzmanları ve iş karar mekanizmaları için yararlıdır:

* **Geliştiriciler** , derece ve Reward API 'lerinin uygulamada doğru şekilde kullanıldığından emin olmak Için apprensu modunu kullanabilir ve uygulamadan kişiselleştiriciye bir hata ya da bir zaman damgası ya da UserID öğesi gibi ilgili olmayan özellikleri içermez.

* **Veri bilimcileri** , özelliklerin kişiselleştirici modellerini eğitme açısından etkili olduğunu doğrulamak Için Apprence modunu kullanabilir, böylece bekleme süreleri çok uzun veya kısa olamaz.

* **Iş karar mekanizmaları** , mevcut iş mantığı ile karşılaştırıldığında sonuçları (yani yeniden ödüller) geliştirmek üzere kişiselleştirici potansiyelini değerlendirmek Için Apprence modunu kullanabilir. Bu sayede, gerçek gelir ve Kullanıcı memnuniyeti sürekli olduğu için Kullanıcı deneyimini etkileyen bilinçli bir karar vermesini sağlar.

## <a name="comparing-behaviors---apprentice-mode-and-online-mode"></a>Davranışları karşılaştırma-Apprence modu ve çevrimiçi mod

Apprence modunun, aşağıdaki yollarla çevrimiçi moddan ne zaman farklılık gösterir.

|Alan|Acemi modu|Çevrimiçi mod|
|--|--|--|
|Kullanıcı deneyimine etkisi|Mevcut kullanıcı davranışını, **varsayılan eylediklerinizin** ne olduğunu ve elde ettiği bir şeyi gözlemleyerek (etkilenmemesini), kişiselleştirerek eğitmenize izin vererek, kişisel bir şekilde eğitme yapmak için kullanabilirsiniz. Bu, kullanıcılarınızın deneyimi ve bunların iş sonuçlarının etkilenmeyeceği anlamına gelir.|Kullanıcı davranışını etkilemek için derece çağrısından döndürülen en iyi eylemi görüntüle.|
|Öğrenme hızı|Kişiselleştirici, çevrimiçi modda öğrenirken Apprenlet modundayken daha yavaş bilgi ediniyor. Apprenlet modu, hiçbir araştırma gerçekleştirilemediği için öğrenme hızını sınırlayan **varsayılan eyleminiz** tarafından elde edilen rekişlerinizi gözlemleyerek öğreniyor.|Her ikisi de geçerli modelden yararlanabileceğinden ve yeni eğilimleri keşfedebildiğinden daha hızlı bir şekilde öğrenir.|
|Öğrenme verimliliği "tavan"|Kişiselleştirici, temel iş mantığınızın performansını tahmin edebilir, çok nadiren eşleştirebilir ve hiçbir şekilde hiçbir şekilde aşmaz (her bir derece çağrısının **varsayılan eylemi** ile elde edilen toplam). Bu yaklaşık tavan araştırma tarafından azaltılır. Örneğin, %20 ' de araştırmayla ilgili çok büyük olasılıkla apprence modu performansı %80 ' ü aşacak ve %60%, çevrimiçi moda mezun olacak şekilde makul bir hedeftir.|Kişiselleştirmede uygulamalar taban çizgisini aşmalı ve zaman içinde, modelde iyileştirmeler almaya devam etmek için çevrimdışı değerlendirme ve özellik değerlendirmesi yapmanız gerekir. |
|Rewarterctionıd için derecelendirme API değeri|_Rewarterctionıd_ her zaman sıralama isteğinde göndereceğiniz ilk eylem olduğundan, kullanıcıların deneyimi etkilenmez. Diğer bir deyişle, sıralama API 'SI Apprensu modu sırasında uygulamanız için hiçbir şey görünmez. Uygulamanızdaki Reward API 'Leri, bir mod ve diğeri arasında Reward API 'YI nasıl kullandığını değiştirmemelidir.|Kullanıcıların deneyimi, kişiselleştirici uygulamanız için seçtiği _Rewarterctionıd_ tarafından değiştirilir. |
|Lerim|Kişiselleştirici, varsayılan iş mantığınızın elde edilen ödül toplamlarını bir karşılaştırmasına devam eder ve bu noktada çevrimiçi modda elde edilen toplam kişiselleştirici, daha fazla zaman oluşturur. Bu kaynak için Azure portal bir karşılaştırma kullanılabilir|Toplam yeniden çalışma kişiselleştirmesini, uygulamanın temel aldığı potansiyel depoların elde ettiği bir şekilde karşılaştırmanızı sağlayan [çevrimdışı değerlendirmeler](concepts-offline-evaluation.md)çalıştırarak kişiselleştirici verimliliğini değerlendirin.|

Apprence modunun verimliliğine ilişkin bir Note:

* Apprence modunda kişiselleştiriciye ait verimlilik, uygulamanın temel aldığı %100 ' a yakın bir şekilde ulaşacaktır; ve hiç aşmaz.
* En iyi uygulamalar %100 ' a ulaşmak için denenmez; Ancak kullanım örneğine bağlı olarak %60, %80 arasında bir Aralık sağlanmalıdır.

## <a name="using-apprentice-mode-to-train-with-historical-data"></a>Geçmiş verilerle eğmesi için Apprenlet modunu kullanma

Önemli miktarda geçmiş veriniz varsa, kişiselleştirici 'yı eğmek için kullanmak istersiniz, verileri kişiselleştirici aracılığıyla yeniden oynatmak için Apprence modunu kullanabilirsiniz.

Apprence modunda kişiselleştirici ayarlayın ve geçmiş verilerdeki eylemlerle ve bağlam özellikleriyle Rank 'i çağıran bir betik oluşturun. Bu verilerdeki kayıtların hesaplamalarını temel alarak Reward API 'yi çağırın. Bazı sonuçları görmek için yaklaşık 50.000 geçmiş olay gerekecektir, ancak sonuçlarda daha yüksek güvenilirlikle ilgili 500.000 önerilir.

Geçmiş verilerden eğitim yaparken, içinde gönderilen verilerin (bağlam ve eylemler için özellikler, sıralama istekleri için kullanılan JSON 'daki yerleşimi ve bu eğitim veri kümesinde yeniden ödül hesaplama) kullanılması önerilir. var olan uygulamadan kullanılabilen verilerle (yeniden yapılan Özellikler ve yeniden hesaplamalar) eşleşir.

Çevrimdışı ve Faks sonrası verileri, daha fazla eksiklik ve yanıt olarak farklılık gösterir ve biçimde farklıdır. Geçmiş verilerden eğitim yapılacağından, bu işlemi yapmanın sonuçları, özellikle de Özellikler geçmiş veriler ve mevcut uygulama arasında farklılık gösteren iyi bir tahmine sahip olabilir.

Genellikle kişiselleştirici için, geçmiş verilerle eğitimle karşılaştırıldığında, davranışı Apprenlet moduna değiştirme ve mevcut bir uygulamadan öğrenme işlemleri, daha az işgücü, veri Mühendisliği ve temizleme işi ile etkili bir model sağlamak için daha etkili bir yoldur.

## <a name="using-apprentice-mode-versus-ab-tests"></a>Apprenlet modunu ve A/B testlerini kullanma

Bu, bir veya daha fazla çevrimiçi modda öğrentikten sonra kişiselleştirici anlaşmalar için yalnızca bir/B testi yapmanız yararlı olur. Apprenlet modunda, yalnızca **varsayılan eylem** kullanılır. Bu, tüm kullanıcıların Denetim deneyimini etkin bir şekilde görebildikleri anlamına gelir.

Kişiselleştirici yalnızca _işleme_ olsa bile, verileri doğrularken eğitim kişiselleştirici için iyi hale geldiğinde aynı zorluk vardır. Bunun yerine, %100 trafik ve tüm kullanıcılar denetim (etkilenmemiştir) deneyimi ile birlikte apprenlet modu kullanılabilir.

Kişiselleştirici ve öğrenimi kullanarak bir kullanım örneğine sahip olduktan sonra, bir/B denemeleri, reklerde kullanılan sinyallerden daha karmaşık olabilecek sonuçlar ile denetimli ve bilimsel bir şekilde karşılaştırma yapmanıza olanak sağlar. A/B testi yanıt verebilen örnek bir soru şudur: `In a retail website, Personalizer optimizes a layout and gets more users to _check out_ earlier, but does this reduce total revenue per transaction?`

## <a name="next-steps"></a>Sonraki adımlar

* [Etkin ve etkin olmayan olaylar](concept-active-inactive-events.md) hakkında bilgi edinin
