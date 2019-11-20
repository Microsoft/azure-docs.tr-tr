---
title: Ödül puanı-kişiselleştirici
titleSuffix: Azure Cognitive Services
description: Ödül puanı, kişiselleştirme seçiminin, rewarterctionıd 'nin Kullanıcı için ne kadar iyi olduğunu gösterir. Ödül puanı değeri, Kullanıcı davranışının gözlemlerini temel alarak iş mantığınızla belirlenir. Kişiselleştirici, remarları değerlendirerek makine öğrenimi modellerini ister.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: a47d6014e51dce81c9caf82f8624896c439f050d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490889"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Reward puanları, kişiselleştirmenin başarısını gösterir

Ödül puanı, kişiselleştirme seçiminin, [rewarterctionıd](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response)'nin Kullanıcı için ne kadar iyi olduğunu gösterir. Ödül puanı değeri, Kullanıcı davranışının gözlemlerini temel alarak iş mantığınızla belirlenir.

Kişiselleştirici, remarları değerlendirerek makine öğrenimi modellerini ister. 

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Kişiselleştiriciye yeniden Puanlama göndermek için ödül API kullanma

Reward, [Reward API](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward)tarafından kişiselleştiriciye gönderilir. Genellikle, bir ödül 0 ve 1 ' den bir sayıdır. -1 değeri ile negatif bir ödül, bazı senaryolarda mümkündür ve yalnızca pekiştirmeye dayalı Learning (RL) ile karşılaşırsanız kullanılmalıdır. Kişiselleştirici, zaman içinde mümkün olan en yüksek miktarda yeniden elde etmek için modeli gözleyin.

Yeniden ödüller, Kullanıcı davranışı gerçekleştirildikten sonra gönderilir ve bu gün daha sonra günler olabilir. Kişiselleştirilmez bir olay, bir olayın yeniden ele alınmayacak şekilde değerlendirilene veya Azure portal bir varsayılan yeniden [beklenene](#reward-wait-time) kadar bekleneceği maksimum zaman.

Bir olayın ödül puanı, **bekleme süresi**içinde alınmadıysa, **varsayılan ödül** uygulanır. Genellikle, **[varsayılan değer](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** sıfır olacak şekilde yapılandırılır.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Ödüller için göz önünde bulundurulması gereken davranışlar ve veriler

Bu sinyalleri ve davranışları, ödül puanı bağlamı için göz önünde bulundurun:

* Seçenekler dahil edildiğinde öneriler için doğrudan Kullanıcı girişi ("ortalama X?").
* Oturum uzunluğu.
* Oturumlar arasındaki süre.
* Kullanıcı etkileşimlerinin yaklaşım analizi.
* Bot 'ın, kullanıcıdan kullanışlılık, doğruluk hakkında geri bildirim aramasını istediğinde doğrudan sorular ve mini anketler.
* Uyarılara yanıt verme veya uyarılara yanıt verme gecikmesi.

## <a name="composing-reward-scores"></a>Ödül puanları oluşturuluyor

Bir Reward puanı, iş mantığınızdan hesaplanmalıdır. Puan şu şekilde gösterilebilir:

* Tek bir sayı tek bir kez gönderilir 
* Anında gönderilen puan (0,8 gibi) ve daha sonra gönderilen ek bir puan (genellikle 0,2).

## <a name="default-rewards"></a>Varsayılan ödüller

Daha fazla [bekleme süresi](#reward-wait-time)içinde bir yeniden alma işlemi yoksa, bu sıralama çağrısının bu yana, kişiselleştirici, bu sıra olayına ilişkin **varsayılan** değeri örtülü olarak uygular.

## <a name="building-up-rewards-with-multiple-factors"></a>Birden çok faktörle yeniden ödüller oluşturma  

Etkili kişiselleştirme için, daha fazla etkene göre ödül puanı oluşturabilirsiniz. 

Örneğin, video içeriğinin bir listesini kişiselleştirmek için bu kuralları uygulayabilirsiniz:

|Kullanıcı davranışı|Kısmi puan değeri|
|--|--|
|Kullanıcı üst öğeyi tıkladı.|\+ 0,5 ödül|
|Kullanıcı o öğenin gerçek içeriğini açtı.|\+ 0,3 ödül|
|Kullanıcı, içeriğin 5 dakika veya daha uzun bir süre sonra %30 oranında izleniyor.|\+ 0,2 ödül|
|||

Sonra, API 'ye toplam yeniden ödül gönderebilirsiniz.

## <a name="calling-the-reward-api-multiple-times"></a>Reward API 'YI birden çok kez çağırma

Ayrıca, aynı olay kimliğini kullanarak, farklı rehirlar göndererek, ödül API 'sini de çağırabilirsiniz. Kişiselleştirici bu ödüller aldığında, bu olayın, kişiselleştirici yapılandırmasında belirtilen şekilde toplayarak son halini belirler.

Toplama değerleri:

*  **İlk**: olay için ilk geri alma puanı alır ve geri kalanı atar.
* **Sum**: EventID için toplanan tüm yeniden puanları alır ve bunları bir araya getirir.

**Yeniden bekleme zamanından**sonra alınan bir olayın tüm depoları atılır ve modellerin eğitimini etkilemez.

Toplama skorları ekleyerek, son ödül tahmini beklenen puan aralığının dışında olabilir. Bu, hizmetin başarısız olmasına neden olmaz.

## <a name="best-practices-for-calculating-reward-score"></a>Ödül Puanını hesaplamak için en iyi uygulamalar

* **Başarılı bir kişiselleştirmenin gerçek göstergelerini göz önünde bulundurun**: tıklama açısından kolayca düşünmek kolaydır, ancak kullanıcılarınızın ne *yapmak*istediğinize ilişkin ne kadar hızlı bir şekilde *elde* etmelerini istediğinizi temel alır.  Örneğin, tıklatmalar üzerinde yeniden çarpıtma, clickbait lekeli içeriğin seçilmesine neden olabilir.

* **Kişiselleştirmenin ne kadar iyi çalıştığı konusunda bir ödül puanı kullanın**: bir film önerisini kişiselleştirmek, kullanıcının filmi izlemeye ve yüksek bir derecelendirme kullanmasına neden olur. Film derecelendirmesi büyük olasılıkla çok sayıda işleme bağlı olduğundan (hareket kalitesi, kullanıcının ruh derecesi), *kişiselleştirmenin* ne kadar iyi çalıştığı konusunda iyi bir sinyal değildir. Ancak filmin ilk birkaç dakikasını izleyen Kullanıcı, kişiselleştirme verimliliğinden daha iyi bir sinyal alabilir ve 5 dakika sonra 1 ' in bir kez daha iyi bir sinyal olarak gönderilmesini sağlayabilir.

* **Releleler yalnızca rewarterctionıd için geçerlidir**: kişiselleştirici, rewarterctionıd içinde belirtilen eylemin göre etkinliğine anlamak için yeniden ödüller uygular. Diğer eylemleri görüntülemeyi tercih ederseniz ve Kullanıcı bunlara tıkladıysanız, yeniden, sıfır olmalıdır.

* **İstemeden oluşan sonuçları göz önünde bulundurun**: [ahlak ve sorumlu kullanım](ethics-responsible-use.md)ile sorumlu sonuçlara yol açabilecek ödül işlevleri oluşturun.

* **Artımlı yeniden kullanım**: daha küçük kullanıcı davranışları için kısmi reksel ekleme, kişiselleştirmeye daha iyi bir performans sağlamaya yardımcı olur. Bu artımlı yeniden, algoritmanın kullanıcıya son istenen davranışta ilgi çekici olduğunu bilmesini sağlar.
    * Bir film listesi gösteriyorsa, Kullanıcı daha fazla bilgi görüntülemek için ilk bir kez üzerine gelirse, bazı kullanıcı katılımı olduğunu belirleyebilirsiniz. Davranış, 0,1 için bir ödül puanı ile sayabilir. 
    * Kullanıcı sayfayı açtı ve sonra çıkmadıysa, ödül puanı 0,2 olabilir. 

## <a name="reward-wait-time"></a>bekleme süresi

Kişiselleştirici, modeli eğitme çağrılarında, bir derece çağrısının bilgilerini, yeniden aramalarla gönderilen ödüller ile ilişkilendirilecektir. Bunlar, farklı zamanlarda gelebilir. Kişiselleştirici, derece çağrısının ne zaman meydana geldiği, sıralama çağrısının etkin olmayan bir olay olarak yapılmış ve daha sonra etkinleştirilse bile, sınırlı bir süre bekler.

Daha fazla **bekleme süresi** dolarsa ve hiçbir bilgi yoksa, eğitim için bu olaya bir varsayılan değer uygulanır. En uzun bekleme süresi 6 gündür.

## <a name="best-practices-for-reward-wait-time"></a>İlk bekleme süresi için en iyi uygulamalar

Daha iyi sonuçlar için bu önerileri izleyin.

* Kullanıcı geri bildirimi almak için yeterli zaman bırakarak, yeniden bekleme süresini olabildiğince kısa bir hale getirin. 

* Geri bildirim almak için gereken süreden daha kısa bir süre seçmeyin. Örneğin, bir Kullanıcı bir videonun 1 dakikalık bir süre sonra geliyorsa, deneme uzunluğu en az çift olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

* [Pekiştirmeye dayalı öğrenme](concepts-reinforcement-learning.md) 
* [Derecelendirme API 'sini deneyin](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Reward API 'sini deneyin](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
