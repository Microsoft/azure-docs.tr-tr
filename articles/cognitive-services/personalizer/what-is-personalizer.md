---
title: Kişiselleştirme nedir?
description: Kişiselleştirici, gerçek zamanlı davranışlarından öğrenerek kullanıcılarınıza gösterilecek en iyi deneyimi seçmenize olanak tanıyan bulut tabanlı bir hizmettir.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: kişiselleştirici, Azure kişiselleştirici, makine öğrenimi
ms.openlocfilehash: b2577502907b69e134651c93ab7a98fc51e9aaa6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169258"
---
# <a name="what-is-personalizer"></a>Kişiselleştirme nedir?

Azure kişiselleştirici, uygulamalarınızın kullanıcılarınızı göstermek için en iyi içerik öğesini seçmesini sağlayan bulut tabanlı bir hizmettir. Alışverişçilere hangi ürünün önereceğini belirlemek veya bir tanıtım için en iyi konumu öğrenmek için kişiselleştirici hizmetini kullanabilirsiniz. İçerik kullanıcıya gösterildikten sonra, uygulamanız kullanıcının yeniden eylemini izler ve kişiselleştirici hizmetine geri dönüş puanı bildirir. Bu, makine öğrenimi modelinin sürekli gelişmesini ve kişiselleştirici 'nin aldığı bağlamsal bilgileri temel alan en iyi içerik öğesini seçme yeteneğini sağlar.

> [!TIP]
> İçerik, metin, görüntü, URL, e-posta veya arasından seçim yapmak istediğiniz herhangi bir şey veya kullanıcılarınıza göstermek istediğiniz herhangi bir bilgi birimidir.

Bu belge aşağıdaki makale türlerini içerir:  

* [**Hızlı**](quickstart-personalizer-sdk.md) başlangıçlarda, hizmette istek yapma konusunda size kılavuzluk eden başlangıç yönergeleri bulunur.  
* [**Nasıl yapılır kılavuzlarında**](how-to-settings.md) , hizmetin daha belirli veya özelleştirilmiş yollarla kullanılmasına ilişkin yönergeler bulunur.  
* [**Kavramlar**](how-personalizer-works.md) , hizmet işlevselliği ve özelliklerinin ayrıntılı açıklamalarını sağlar.  
* [**Öğreticiler**](tutorial-use-personalizer-web-app.md) daha fazla iş çözümlerinde hizmeti bir bileşen olarak nasıl kullanacağınızı gösteren kılavuzlardır.  

Başlamadan önce [Bu etkileşimli tanıtım Ile kişiselleştirici](https://personalizationdemo.azurewebsites.net/)kullanmayı deneyin.

## <a name="how-does-personalizer-select-the-best-content-item"></a>Kişiselleştirici en iyi içerik öğesini nasıl seçer?

Kişiselleştirici, tüm kullanıcılar arasında toplu davranışa ve yeniden puanları temel alan en iyi öğeyi (_eylem_) seçmek için **pekiştirmeye dayalı Learning** kullanır. Eylemler, haber makaleleri, belirli filmler veya ürünler gibi içerik öğeleridir.

**Sıralama** çağrısı, eylem öğesini ve eylem özelliklerini ve en üstteki eylem öğesini seçmek için bağlam özelliklerini alır:

* **Özelliklerle Ilgili eylemler** -her bir öğeye özgü özelliklerle içerik öğeleri
* **Bağlam özellikleri** -uygulamanızın kullanımı sırasında kullanıcılarınızın özellikleri, bağlamları veya ortamları

Sıralama çağrısı, **geri dönüş EYLEMI kimliği** alanında kullanıcıya gösterilecek içerik öğesi, __eylem__ kimliğini döndürür.

Kullanıcıya gösterilen __eylem__ makine öğrenimi modelleriyle seçilir ve bu, zaman içinde toplam yeniden kullanım miktarını en üst düzeye çıkarmaya çalışır.

### <a name="sample-scenarios"></a>Örnek senaryolar

Kişiselleştirmenin, bir kullanıcı için işlenecek en iyi içeriği seçmek üzere kullanılabileceği birkaç senaryoya göz atalım.

|İçerik türü|Eylemler (özelliklerle birlikte)|Bağlam özellikleri|Geri alınan geri dönüş eylemi KIMLIĞI<br>(Bu içeriği görüntüle)|
|--|--|--|--|
|Haber listesi|a. `The president...` (Ulusal, politika, [metin])<br>b. `Premier League ...` (küresel, spor, [metin, görüntü, video])<br> c. `Hurricane in the ...` (bölgesel, hava durumu, [metin, görüntü]|Cihaz haberleri okundu<br>Ay veya mevsim<br>|a `The president...`|
|Filmler listesi|1. `Star Wars` (1977, [eylem, Adventure, FI], George Lucas)<br>2. `Hoop Dreams` (1994, [belgesel, spor], Steve James<br>3. `Casablanca` (1942, [Romantik, Drama, war], Michael Curtiz)|Cihaz filminin izlenen<br>ekran boyutu<br>Kullanıcı türü<br>|03. `Casablanca`|
|Ürün listesi|i. `Product A` (3 kg, $ $ $ $, 24 saat içinde teslim et)<br>ii. `Product B` (20 kg, $ $, 2 hafta gümrük ile sevkiyat)<br>iii. `Product C` (3 kg, $ $ $, 48 saat içinde teslim)|Cihaz alışverişi buradan okundu<br>Kullanıcının harcama katmanı<br>Ay veya mevsim|ii. `Product B`|

Kişiselleştirici, _ödül eylem kimliği_ olarak bilinen tek bir en iyi eylemi seçmek için pekiştirmeye dayalı öğrenimi kullandı. Machine Learning modeli şunları kullanır: 

* Makine öğrenimi modelini geliştirmek için kullanılan Kişiselleştir hizmetinden daha önce bir eğitilen model-bilgi alındı
* Özelliklerle ve bağlam özellikleriyle geçerli veriye özgü eylemler

## <a name="when-to-use-personalizer"></a>Kişiselleştirici ne zaman kullanılır?

Kişiselleştirici 'in **sıralama** [API 'si](https://go.microsoft.com/fwlink/?linkid=2092082) , uygulamanız içeriği her sunışınızda çağrılır. Bu, _olay kimliği_ ile belirtilen bir **olay** olarak bilinir.

Kişiselleştiriciye yönelik **Reward** [API 'si](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) gerçek zamanlı olarak çağrılabilir veya altyapınıza daha iyi uyum sağlamak için gecikebilir. İş gereksinimlerinize göre ödül puanı ' nı belirlersiniz. Ödül puanı 0 ile 1 arasındadır. Bu, iyi için 1 ve hatalı için 0 gibi tek bir değer ya da iş hedeflerinizi ve ölçümünüzü göz önünde bulundurarak oluşturduğunuz bir algoritma tarafından üretilen bir sayı olabilir.

## <a name="content-requirements"></a>İçerik gereksinimleri

İçeriğiniz için kişiselleştirici kullanın:

* Her bir kişiselleştirme olayında arasından seçim yapmak için sınırlı bir eylem veya öğe kümesi (en fazla ~ 50) vardır. Daha büyük bir listeniz varsa, kişiselleştirici hizmetinde sıralama her çağırdığınızda listeyi 50 öğe olarak azaltmak için [bir öneri altyapısı kullanın](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) .
* , Derecelendirilir, Özellikler ve _bağlam özellikleriyle_ _Eylemler_ hakkında bilgi içerir.
* Kişiselleştirici için en az ~ 1k/gün içeriğe ilişkin olay ile ilgili olaylar geçerlidir. Kişiselleştirici gereken en düşük trafiği almazsa, hizmet en iyi tek içerik öğesini belirlemede daha uzun sürer.

Kişiselleştirici, tek en iyi içerik öğesini döndürmek için neredeyse gerçek zamanlı olarak toplu bilgiler kullandığından, hizmet şunları yapmaz:
* Kullanıcı profili bilgilerini kalıcı yapın ve yönetin
* Bireysel kullanıcıların tercihlerini veya geçmişini günlüğe kaydet
* Temizlenen ve etiketlenmiş içerik gerektir

## <a name="how-to-design-for-and-implement-personalizer"></a>Kişiselleştirici için tasarlama ve uygulama

1. İçeriği, **_eylemleri_** ve **_bağlamı_** [tasarlayın](concepts-features.md) ve planlayın. **_Ödül_** puanı için yeniden dengeleme algoritmasını saptayın.
1. Oluşturduğunuz her bir [kişiselleştirici kaynağı](how-to-settings.md) bir öğrenme döngüsü olarak kabul edilir. Döngü, bu içerik veya Kullanıcı deneyimi için hem derecelendirme hem de yeniden çağrıları alacaktır.

    |Kaynak türü| Amaç|
    |--|--|
    |[Acemi modu](concept-apprentice-mode.md) `E0`|Mevcut uygulamanızı etkilemeden kişiselleştirici modeli eğitme, ardından çevrimiçi öğrenme davranışına bir üretim ortamına dağıtma|
    |Stand `S0`|Üretim ortamında çevrimiçi öğrenme davranışı|
    |Süz `F0`| Üretim dışı bir ortamda çevrimiçi öğrenme davranışını deneyin|

1. Uygulamanıza, Web sitenize veya sisteminize kişiselleştirici ekleyin:
    1. İçerik kullanıcıya gösterilmeden önce en iyi, tek _içerik_ öğesini belirleyebilmek için, uygulamanızda, Web sitenizde veya sisteminizde kişiselleştiriciye bir **Derecelendirme** çağrısı ekleyin.
    1. Kullanıcıya döndürülen geri dönüş _EYLEMI kimliği_ olan en iyi, tek _içerik_ öğesini görüntüleyin.
    1. Kullanıcı _mantığını_ , kullanıcının nasıl davrandığını öğrenmek, örneğin: 

    |Davranış|Hesaplanan ödül puanı|
    |--|--|
    |Kullanıcı tarafından seçilen en iyi, tek _içerik_ öğesi (Reward eylem kimliği)|**1**|
    |Kullanıcı tarafından seçilen diğer içerik|**0**|
    |Kullanıcı duraklatıldı, en iyi ve tek içerik öğesini seçmeden önce, tek _içerik_ öğesi (Reward eylem kimliği)|**0,5**|

    1. Bir **ödül** çağrısı ekleyerek 0 ile 1 arasında bir ödül puanı gönderiliyor
        * İçeriğinizi gösterdikten hemen sonra
        * Veya sonraki bir süre sonra çevrimdışı bir sistem
    1. Bir kullanım süresinden sonra [çevriminizi](concepts-offline-evaluation.md) çevrimdışı değerlendirmede değerlendirin. Çevrimdışı bir değerlendirme, kodunuzu değiştirmeden veya Kullanıcı deneyimini etkilemeden, kişiselleştirici hizmetin verimliliğini test etmenize ve değerlendirmenize olanak tanır.

## <a name="reference"></a>Başvuru 

* [Kişiselleştirici C#/.NET SDK 'Sı](/dotnet/api/overview/azure/cognitiveservices/client/personalizer)
* [Kişiselleştirici git SDK 'Sı](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview)
* [Kişiselleştirici JavaScript SDK 'Sı](/javascript/api/@azure/cognitiveservices-personalizer/)
* [Kişiselleştirici Python SDK 'Sı](/python/api/overview/azure/cognitiveservices/personalizer)
* [REST API'leri](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kişiselleştirici nasıl kullanılır?](how-personalizer-works.md) 
>  [Pekiştirmeye dayalı Learning nedir?](concepts-reinforcement-learning.md)
