---
title: Terminoloji-kişiselleştirici
description: Kişiselleştirici, pekiştirmeye dayalı Learning 'teki terminolojiyi kullanır. Bu terimler Azure portal ve API 'lerde kullanılır.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: cd0d85be5447aad0f2a3c37041e7d5d5d047a468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91777301"
---
# <a name="personalizer-terminology"></a>Kişiselleştirici terminolojisi

Kişiselleştirici, pekiştirmeye dayalı Learning 'teki terminolojiyi kullanır. Bu terimler Azure portal ve API 'lerde kullanılır.

## <a name="conceptual-terminology"></a>Kavramsal terminoloji

* **Öğrenme döngüsü**: uygulamanızın, kişiselleştirmeye faydalanabilir her bölümü için, _öğrenme döngüsü_ olarak adlandırılan bir kişiselleştirici kaynağı oluşturursunuz. Kişiselleştirmek için birden fazla deneyim varsa, her biri için bir döngü oluşturun.

* **Model**: bir kişiselleştirici modeli, Kullanıcı davranışı hakkında öğrenilen tüm verileri yakalar, daha fazla bilgi almak için göndereceğiniz bağımsız değişkenlerin birleşiminden eğitim verileri alma ve öğrenme ilkesi tarafından belirlenen bir eğitim davranışı.

* **Çevrimiçi mod**: öğrenme döngünüz için **en iyi eylemi** tahmin eden bir model oluşturmak için Machine Learning 'i kullanan kişiselleştirici için varsayılan [öğrenme davranışı](#learning-behavior) .

* **Apprenlet modu**: uygulamalar arası sonuçları ve eylemleri etkilemeden eğitede bir kişiselleştirici model başlatmaya yardımcı olan bir [öğrenme davranışı](#learning-behavior) .

## <a name="learning-behavior"></a>Öğrenme davranışı:

* **Çevrimiçi mod**: en iyi eylemi döndürür. Modeliniz en iyi eylemle birlikte işlem çağrılarına yanıt verir ve seçimlerini zaman içinde öğrenmek ve geliştirmek için yeniden çağrılar kullanacaktır.
* **[Apprenlet modu](concept-apprentice-mode.md)**: apprenlet olarak öğrenme. Modelinize, mevcut sisteminizin davranışını gözlemleyerek bilgi edineceksiniz. Derecelendirme çağrıları her zaman uygulamanın **varsayılan eylemini** (taban çizgisi) döndürür.

## <a name="personalizer-configuration"></a>Kişiselleştirici yapılandırması

Kişiselleştirici [Azure Portal](https://portal.azure.com)bir şekilde yapılandırılmıştır.

* **Ödül**: yeniden geçen bekleme süresi, varsayılan değer ve ödül toplama ilkesi için varsayılan değerleri yapılandırın.

* **Araştırma**: araştırma Için kullanılacak derece çağrılarının yüzdesini yapılandırma

* **Model güncelleştirme sıklığı**: modelin ne sıklıkta geri çekilmesi.

* **Veri saklama**: kaç gün değer depolayacak veri. Bu, öğrenme döngünüzü geliştirmek için kullanılan çevrimdışı değerlendirmeleri etkileyebilir.

## <a name="use-rank-and-reward-apis"></a>Derecelendirme ve Reward API 'Leri kullanma

* **Derece**: Özellikler ve bağlam özellikleriyle eylemler verildiğinde, en üstteki eylemi (içerik öğesi) döndürmek için keşfet veya Exploit kullanın.

    * **Eylemler**: Eylemler, ürün veya promosyon gibi, aralarından seçim yapabileceğiniz içerik öğeleridir. Kişiselleştirme API 'SI aracılığıyla kullanıcılarınıza göstermek için, kişiselleştirici, en iyi eylemi (döndürülen geri dönüş eylem KIMLIĞI) seçer.

    * **Bağlam**: daha doğru bir derece sağlamak için, içeriğiniz hakkında bilgi sağlayın, örneğin:
        * Kullanıcı.
        * Bulundukları cihaz.
        * Geçerli saat.
        * Geçerli durumla ilgili diğer veriler.
        * Kullanıcı veya bağlamla ilgili geçmiş verileri.

        Özel uygulamanız farklı bağlam bilgilerine sahip olabilir.

    * **[Özellikler](concepts-features.md)**: bir içerik öğesi veya Kullanıcı bağlamı hakkındaki bilgi birimi. Yalnızca toplanmış özellikleri kullandığınızdan emin olun. Belirli zamanları, Kullanıcı kimliklerini veya diğer toplu olmayan verileri özellik olarak kullanmayın.

        * Bir _eylem özelliği_ , içerik hakkında meta verilerdir.
        * _Bağlam özelliği_ , içeriğin sunulduğu bağlamla ilgili meta verilerdir.

* **Araştırma**: kişiselleştirici hizmeti, en iyi eylemi döndürmek yerine, Kullanıcı için farklı bir eylem seçerse, ne zaman araştırma yapılır. Kişiselleştirici hizmeti, ve ' yi inceleyerek devam eden kullanıcı davranışına uyum sağlayabilir.

* **Yararlanma**: kişiselleştirici hizmeti, geçmiş verilere göre en iyi eyleme karar vermek için geçerli modeli kullanır.

* **Deneme süresi**: Bu etkinlik için sıralama çağrısının başladığı andan Itibaren, kişiselleştirici hizmetin bir azalma için beklediği süre miktarı.

* **Etkin olmayan olaylar**: etkin olmayan bir olay, derece olarak adlandırduğunuz bir olaydır, ancak istemci uygulama kararlarından dolayı kullanıcının sonucu göreceğinden emin olmadığınızı unutmayın. Etkin olmayan olaylar, kişiselleştirme sonuçları oluşturup depolamanıza olanak tanır ve ardından makine öğrenimi modelini etkilemeden daha sonra atmayı seçebilirsiniz.


* **Ödül**: kullanıcının sıralama API 'si tarafından döndürülen geri alma eylemi kimliğine nasıl yanıt verdiğini gösteren bir ölçü, 0 ile 1 arasında bir puan olarak. 0-1 değeri, iş mantığınızla belirlenir ve bu seçenek, nasıl kişiselleştirmenin iş hedeflerine nasıl ulaştığından yardımcı olur. Öğrenme döngüsü, bu yeniden Kullanıcı Geçmişi olarak depolamaz.

## <a name="evaluations"></a>Lerim

### <a name="offline-evaluations"></a>Çevrimdışı değerlendirmeler

* **Değerlendirme**: çevrimdışı bir değerlendirme, uygulamanızın verilerine göre döngünüz için en iyi öğrenme ilkesini belirler.

* **Öğrenme ilkesi**: her olay üzerinde kişiselleştirici, makine öğrenimi algoritmasının nasıl çalıştığını etkileyen bazı parametrelere göre belirlenir. Yeni bir öğrenme döngüsü, varsayılan bir **öğrenme ilkesiyle** başlar ve bu, orta düzeyde performans sağlayabilir. [Değerlendirme](concepts-offline-evaluation.md)çalıştırılırken, kişiselleştirici, özel olarak döngülerinizin kullanım örneklerine iyileştirilmiş yeni öğrenme ilkeleri oluşturur. Kişiselleştirmede, değerlendirme sırasında oluşturulan her bir döngü için en iyi duruma getirilmiş ilkelerle önemli ölçüde daha iyi işlem yapılır. Öğrenme ilkesi, Azure portal ' deki kişiselleştirici kaynak için **model ve öğrenme ayarları** ' nda _öğrenme ayarları_ olarak adlandırılır.

### <a name="apprentice-mode-evaluations"></a>Apprence modu değerlendirmeleri

Apprenlet modu aşağıdaki **değerlendirme ölçümlerini** sağlar:
* **Taban çizgisi – ortalama ödül**: uygulamanın varsayılan (taban çizgisi) ortalama yeniden sürümleri.
* **Kişiselleştirici – ortalama ödül**: Toplam Rede kişiselleştiriciye ait ortalama, potansiyel olarak gelmiş olabilir.
* **Ortalama hareketli geri** alma: en son 1000 olay üzerinden normalleştirilmeli ve kişiselleştirici geri alma oranı.

## <a name="next-steps"></a>Sonraki adımlar

* [Ahlak ve sorumlu kullanım](ethics-responsible-use.md) hakkında bilgi edinin