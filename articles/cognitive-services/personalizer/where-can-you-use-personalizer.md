---
title: Senaryo değerlendirmesi-kişiselleştirici
titleSuffix: Azure Cognitive Services
description: Kişiselleştirici, uygulamanızın, deneyimi daha iyi hale getirmek, daha iyi iş sonuçları elde etmek veya üretkenliği artırmak için görüntülenecek doğru öğe, eylem veya ürünü seçebileceğiniz herhangi bir durumda uygulanabilir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: diberry
ms.openlocfilehash: 246e76a0ab94624945723b500ef136e038ab40ec
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155224"
---
# <a name="where-can-you-use-personalizer"></a>Kişiselleştirme’yi nerelerde kullanabilirsiniz?

Uygulamanızın, deneyimi daha iyi hale getirmek, daha iyi iş sonuçları elde etmek veya üretkenliği artırmak için görüntülenecek doğru öğe, eylem veya ürünü seçmesini gerektiren herhangi bir durumda kişiselleştirici kullanın. 

Kişiselleştirici, kullanıcıyı hangi eylemin gösterebileceği belirlemek için makine öğrenimini kullanır. Seçim, hizmete gönderilen verilerin miktarına, kalitesine ve dağıtımına bağlı olarak büyük ölçüde farklılık gösterebilir.

### <a name="checklist-for-applying-personalizer"></a>Kişiselleştirici uygulama için denetim listesi


Şu durumlarda kişiselleştirici uygulayabilirsiniz:

* Uygulamanız için bir iş veya kullanılabilirlik hedefi vardır.
* Uygulamanızda, kullanıcıların bu hedefi iyileştirebilecekleri bağlamsal kararı veren bir yerdir.
* En iyi seçenek, toplu Kullanıcı davranışından ve toplam ödül puanından öğrenilmesi gerekir.
* Kişiselleştirme için makine öğrenmesinin kullanımı, sizin belirlediğiniz [kullanım kılavuzları](ethics-responsible-use.md) ve seçimlerdir.
* Bağlamsal karar, sınırlı bir seçenek kümesinden en iyi seçenek (eylem) sıralaması olarak ifade edilebilir.
* Uygulamanız için ne kadar uygun olan dereceli seçim, Kullanıcı davranışının bazı yönlerinin ölçüleceği ve bir _[ödül puanında](concept-rewards.md)_ ifade edilebilir.
* Ödül puanı, çok fazla sayıda bağlama veya dış etken getirmez. Deneme süresi, hala uygun olduğu sürece, yeniden elde edilen puanınızın hesaplanabileceği kadar düşüktür.
* En az 5 [özelliğin](concepts-features.md) bağlamını, doğru seçim yapmaya yardımcı olacağını düşündüğünüz ve kişisel olarak tanımlanabilen bilgileri içermeyen bir liste olarak ifade edebilirsiniz. (PII).
* Her içerik seçimi, eylem ve, kişiselleştirmenin doğru seçimi yapmasını sağlayacak en az 5 [özellik](concepts-features.md) listesi olarak, _eylem_hakkında bilgi sahibi olursunuz.
* Uygulamanız, en az 100.000 etkileşimin geçmişini biriktirmek için verileri yeterince uzun bir süre koruyabilir.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Kişiselleştirici uygulamaya yönelik makine öğrenimi konuları

Kişiselleştirici, bir makine öğrenimine, size verdiğiniz geri bildirimde bulunarak bir yaklaşım olan pekiştirmeye dayalı öğrenimini temel alır. 

Kişiselleştirici, şu durumlarda en iyi şekilde bilgi edineceksiniz:

* Sorun zaman içinde Drifts, en iyi kişiselleştirmeye devam etmek için yeterli olay vardır (haber veya şekilde Tercihler gibi). Kişiselleştirici gerçek dünyada sürekli değişikliğe uyum sağlar, ancak yeni desenleri bulma ve kapatma hakkında bilgi almak için yeterli olay ve veri yoksa sonuçlar en uygun olmayacaktır. Genellikle yeterince gerçekleşen bir kullanım durumu seçmeniz gerekir. Günde en az 500 kez gerçekleşen kullanım örneklerini aramak için göz önünde bulundurun.
* Bağlam ve eylemlerin öğrenmesini kolaylaştırmak için yeterli [özelliği](concepts-features.md) vardır.
* Çağrı başına derece 50 ' den az eylem vardır.
* Veri saklama ayarlarınız, kişiselleştirmeye, çevrimdışı değerlendirmeler ve ilke iyileştirmesi gerçekleştirmek için yeterli veri toplamasına izin verir. Bu genellikle en az 50.000 veri noktası olur.

## <a name="monitor-effectiveness-of-personalizer"></a>Kişiselleştirici verimliliğini izleme

[Çevrimdışı değerlendirmeler](concepts-offline-evaluation.md)gerçekleştirerek, kişiselleştirmede düzenli aralıklarla etkili bir şekilde izleyebilirsiniz.

## <a name="use-personalizer-with-recommendation-engines"></a>Öneri altyapılarıyla kişiselleştirici kullanma

Birçok şirket öneri altyapıları, pazarlama ve kampanya araçları, kitle segmentleme ve kümeleme, birlikte çalışan filtreleme ve diğer yollarla müşterilerin büyük bir katalogdan müşterilere yönelik ürünler önermesini sağlar.

[Microsoft Recommenders GitHub deposu](https://github.com/Microsoft/Recommenders) , jupi Not defterleri olarak sağlanan öneri sistemleri oluşturmak için örnekler ve en iyi uygulamalar sağlar. Bu, Xderin FM, çın ÖIB, ALS, RBM, DKN gibi yaygın birçok yaklaşım için, verileri hazırlamaya, model oluşturmaya, model oluşturmaya, değerlendirmeye, ayarlamaya ve kullanıma hazır hale getirmeye yönelik örnekler sağlar.

Kişiselleştirici, mevcut olduğunda bir öneri altyapısıyla çalışabilir.

* Öneri motorları büyük miktarda öğe alır (örneğin, 500.000) ve yüzlerce veya binlerce seçenekten oluşan bir alt küme (ilk 20 gibi) önerilir.
* Kişiselleştirmede, aralarında çok fazla bilgi içeren az sayıda eylem sürer ve belirli bir zengin bağlam için bunları gerçek zamanlı olarak derecelendirirken, çoğu öneri altyapısı yalnızca kullanıcılar, ürünler ve etkileşimleri hakkında birkaç öznitelik kullanır.
* Kişiselleştirici, Kullanıcı tercihlerini her zaman keşfetmeye olarak çalışabilen üzere tasarlanmıştır. Bu, örneğin Haberler, canlı olaylar, canlı topluluk içeriği, günlük güncelleştirme içeren içerik veya mevsimsel içerik gibi içeriğin hızlı şekilde değiştiği daha iyi sonuçlar verir.

Yaygın olarak kullanılan bir kullanım, bir öneri altyapısının çıkışını (örneğin, belirli bir müşteri için ilk 20 ürün) almak ve bunu, kişiselleştirici için giriş eylemleri olarak kullanmaktır.

## <a name="next-steps"></a>Sonraki adımlar

[Etics & sorumlu kullanımı](ethics-responsible-use.md).