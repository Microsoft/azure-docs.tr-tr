---
title: Çevrimdışı değerlendirme yöntemini kullanma-kişiselleştirici
titleSuffix: Azure Cognitive Services
description: Bu makalede, uygulamanızın verimliliğini ölçmek ve öğrenme döngünüzü çözümlemek için çevrimdışı değerlendirmeyi kullanma açıklanmaktadır.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 627f511bb12c16c8f54935d1f782cb7c2c962163
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "87132764"
---
# <a name="offline-evaluation"></a>Çevrimdışı değerlendirme

Çevrimdışı değerlendirme, kodunuzu değiştirmeden veya Kullanıcı deneyimini etkilemeden, kişiselleştirici hizmetin verimliliğini test etmenize ve değerlendirmenize olanak tanıyan bir yöntemdir. Çevrimdışı değerlendirme, farklı derecelendirmelerinin nasıl gerçekleştirildiğini karşılaştırmak için uygulamanızdan derecelendirme ve yeniden kullanma API 'Lerine gönderilen geçmiş verileri kullanır.

Çevrimdışı değerlendirme bir tarih aralığında gerçekleştirilir. Aralık, geçerli saat kadar geç bitebilirler. Aralığın başı, [veri saklama](how-to-settings.md)için belirtilen gün sayısından daha fazla olamaz.

Çevrimdışı değerlendirme aşağıdaki soruları yanıtlamanıza yardımcı olabilir:

* Başarıyla kişiselleştirmeye yönelik kişiselleştirici dereceleri ne kadar etkili?
    * Kişiselleştirici çevrimiçi makine öğrenme ilkesi tarafından gerçekleştirilen ortalama yeniden ödüller nelerdir?
    * Kişiselleştirici, uygulamanın varsayılan olarak ne kadar yapıldığına ilişkin verimliliğinden nasıl karşılaştırılır?
    * Kişiselleştirme için rastgele bir seçeneğin karşılaştırılma verimliliği ne olur?
    * Farklı öğrenme ilkelerinin karşılaştırılma verimliliği el ile belirtilmiş olabilir mi?
* İçeriğin hangi özellikleri başarılı kişiselleştirmeye daha fazla veya daha az katkıda bulunur?
* Eylemlerin hangi özellikleri başarılı kişiselleştirmeye daha fazla veya daha az katkıda bulunur?

Ayrıca, çevrimdışı değerlendirme, kişiselleştirmenin gelecekte sonuçları geliştirmek için kullanabileceği daha iyileştirilmiş öğrenme ilkelerini keşfetmede kullanılabilir.

Çevrimdışı değerlendirmeler, araştırma için kullanılacak olayların yüzdesine ilişkin rehberlik sağlamaz.

## <a name="prerequisites-for-offline-evaluation"></a>Çevrimdışı değerlendirme önkoşulları

Aşağıda, temsili çevrimdışı değerlendirme için önemli noktalar verilmiştir:

* Yeterli veri yok. Önerilen minimum değer en az 50.000 olaydır.
* Temsilci Kullanıcı davranışı ve trafiği ile dönemlerden veri toplayın.

## <a name="discovering-the-optimized-learning-policy"></a>İyileştirilmiş öğrenme ilkesini keşfetme

Kişiselleştirici, otomatik olarak daha iyi bir öğrenme ilkesi bulmaya yönelik çevrimdışı değerlendirme işlemini kullanabilir.

Çevrimdışı değerlendirmeyi gerçekleştirdikten sonra, geçerli çevrimiçi ilkeyle karşılaştırıldığında, bu yeni ilkeyle kişiselleştirici 'nin karşılaştırılma verimliliğini görebilirsiniz. Daha sonra bu öğrenme ilkesini, indirerek ve ardından modeller ve Ilke paneline yükleyerek, kişiselleştirmede hemen etkili hale getirmek için uygulayabilirsiniz. Ayrıca, gelecekteki analizler veya kullanımı için de indirebilirsiniz.

Değerlendirmede yer alan geçerli ilkeler:

| Öğrenme ayarları | Amaç|
|--|--|
|**Çevrimiçi Ilke**| Kişiselleştirici içinde kullanılan geçerli öğrenme Ilkesi |
|**Taban çizgisi**|Uygulamanın varsayılan (derece çağrılarında gönderilen ilk eylem tarafından belirlendiği şekilde)|
|**Rastgele Ilke**|Her zaman sağlanan Işlemlerden rastgele eylem seçimi döndüren sanal bir sıra davranışı.|
|**Özel Ilkeler**|Değerlendirme başlatılırken ek öğrenme Ilkeleri karşıya yüklendi.|
|**İyileştirilmiş Ilke**|Değerlendirme, iyileştirilmiş bir ilkeyi bulma seçeneği ile başlatıldıysa, bu da karşılaştırılır ve bunu indirebilir veya çevrimiçi öğrenme ilkesi haline getirmek için geçerli olanı değiştirin.|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Çevrimdışı değerlendirme sonuçlarının uygunluğunu anlama

Çevrimdışı bir değerlendirme çalıştırdığınızda sonuçların _güvenilirlik sınırlarını_ çözümlemek çok önemlidir. Bunlar genişlerse, uygulamanızın kesin veya önemli olması için yeterli miktarda veri almamış olması anlamına gelir. Sistem daha fazla veri biriktirdiği ve çevrimdışı değerlendirmeleri daha uzun süreler üzerinde çalıştırtıkça, güven aralıkları daha dar olur.

## <a name="how-offline-evaluations-are-done"></a>Çevrimdışı değerlendirmeler nasıl yapılır?

Çevrimdışı değerlendirmeler, **onay verilen değerlendirme** adlı bir yöntem kullanılarak yapılır.

Kişiselleştirici, kullanıcıların davranışının (ve dolayısıyla rediklerin) geriye dönük olarak tahmin edilmesi imkansız olduğu varsayımına dayanır (Kullanıcı gördüklerden farklı bir şeyler gösterilseydi ve yalnızca ölçülen depolardan bilgi edinmek için).

Bu, değerlendirmeler için kullanılan kavramsal işlemdir:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call

        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.

    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

Çevrimdışı değerlendirme yalnızca gözlemlenen Kullanıcı davranışını kullanır. Bu işlem, özellikle uygulamanız çok sayıda eylemden çağrı yapar durumunda büyük hacimlerdeki verileri atar.


## <a name="evaluation-of-features"></a>Özelliklerin değerlendirmesi

Çevrimdışı değerlendirmeler, eylemler veya bağlam için belirli özelliklerin ne kadarının daha yüksek bir kapsamda dengelendiği hakkında bilgi sağlayabilir. Bilgiler, belirtilen süre ve verilere karşı değerlendirme kullanılarak hesaplanır ve zaman içinde değişebilir.

Özellik değerlendirmelerini ve şunları yapmanızı öneririz:

* Uygulamanız veya sisteminiz, daha etkili olanlar hakkında daha fazla bilgi sağlar.
* Düşük verimlilik nedeniyle hangi özellikler kaldırılabileceği? Düşük verimlilik özellikleri makine öğrenimine _gürültü_ ekler.
* Yanlışlıkla dahil edilen herhangi bir özellik var mı? Bunlara örnek olarak şunlar verilebilir: Kullanıcı tarafından tanımlanabilen bilgiler, yinelenen kimlikler, vb.
* Yasal veya sorumlu kullanım konuları nedeniyle kişiselleştirmek için kullanılmaması gereken istenmeyen özellikler var mı? İstenmeyen özelliklerle ara sunucu (diğer bir deyişle, yakından yansıtabilir veya bunlarla ilişkilendirilebilir) olan özellikler var mı?


## <a name="next-steps"></a>Sonraki adımlar

[Kişiselleştirici yapılandırma](how-to-settings.md) 
 [Çevrimdışı değerlendirmeleri Çalıştır](how-to-offline-evaluation.md) [Kişiselleştirici 'ın nasıl çalıştığını](how-personalizer-works.md) anlama
