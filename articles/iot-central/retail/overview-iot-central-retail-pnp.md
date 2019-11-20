---
title: Azure IoT Central perakende çözümleri oluşturma | Microsoft Docs
description: Uygulama şablonlarını kullanarak bağlı lojistik, dijital dağıtım merkezi, mağaza içi analiz koşulu izleme, kullanıma alma ve akıllı envanter yönetimi, Azure IoT Central perakende çözümleri oluşturmayı öğrenin.
author: KishorIoT
ms.author: nandab
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 7f82e2a539c968d0dde44cbc2ed411710054c290
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890699"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Azure IoT Central ile perakende çözümler oluşturma

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Azure IoT Central, ölçeklenebilir uygulamaları yönetme & oluşturma sorunlarını kolaylaştıran çözüm oluşturucuları için bir Nesnelerin İnterneti (IoT) uygulama platformudur. Bu makalede, IoT Central içinde, perakende olarak belirli çeşitli uygulama şablonlarını vurgulayacağız. Çözüm oluşturucular, tedarik zincirini iyileştirmek, müşteriler için mağaza içi deneyimi iyileştirmek ve envanteri daha verimli bir şekilde izlemek üzere IoT çözümleri oluşturmak için yayımlanmış şablonlardan yararlanabilir.

> [!div class="mx-imgBorder"]
> ![Azure IoT perakende](./media/overview-iot-central-retail/retail-app-templates.png) genel bakış


## <a name="what-is-connected-logistics-solution"></a>Bağlı lojistik çözümü nedir?
Küresel lojistik harcamanın, sektöre göre en büyük GDP 2020 ' de $10.6 trn 'e ulaşması beklenmektedir. Malların taşınması büyük (%70) Toplam lojistik masrafın. Gönderim sağlayıcıları, yoğun rekabetçi basınç ve kısıtlamalardır. 3PL sağlayıcıları, sürekli artan zaman çerçevelerinden ve dengeleme maliyetlerine karşı devam etmeklerdir. Lojistik, geopolitik, çok büyük olaylar ve SUI tarafından oluşan riskler tarafından daha fazla sınırlanmıştır. 

IoT algılayıcılarının yardımıyla, çok kalıcı dönüşümde, hava, su ve zemin arasında & Monitor ortam koşullarını (örneğin, sıcaklık, nem, eğim, darbe, ışık ve sevkıyatın konumunu) toplayabiliriz. Sensörlerden toplanan veriler, cihazlar, hava durumu, & olayları bulut tabanlı Iş zekası sistemleriyle tümleştirilebilir. Bağlı lojistik çözümü avantajları şunlardır
* Gerçek zamanlı izleme & izleme ile sevkiyat transferine 
* Gerçek zamanlı çevresel koşullara göre sevkiyat tutarlılığı & soğuk zinciri izleme
* Hırsızlık, kayıp veya sevkıyatın zarar verdiği güvenlik
* Coğrafi sınırlama, rota Iyileştirmesi, Fleet yönetimi. Araç Analizi
* Kalkış & varış ve & tahmine dayalı olarak tahmin etme 

### <a name="out-of-box-experience"></a>Kutudan çıkan deneyim
İş ortakları, ana hatlı avantajlar & uçtan uca bağlı lojistik çözümlerini geliştirmek için şablondan yararlanabilir. Bu yayımlanmış şablon, IoT Central cihazların cihaz bağlantısı, yapılandırma & yönetimi üzerine odaklanır. 

> [!div class="mx-imgBorder"]
> ![bağlı lojistik panosu](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![bağlı lojistik panosu](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Yukarıdaki panonun örnek bir deneyim olduğunu ve bu uygulamayı istediğiniz kullanım örneğine uyacak şekilde tam olarak özelleştirebileceğinizi lütfen unutmayın.

Bağlı lojistik çözüm şablonlarından birini kullanarak bir çözüm oluşturma konusunda size yol gösteren [uçtan uca öğretici](./tutorial-iot-central-connected-logistics-pnp.md) ile çalışmaya başlayın.



## <a name="what-is-digital-distribution-center-solution"></a>Dijital dağıtım merkezi çözümü nedir?
Daha fazla üretici ve perakendeciler, dünya genelindeki her zamankinden daha karmaşık hale gelmesi için, arz zincirlerinin dallandırıldı. Dağıtım merkezleri birincil bir zorluk haline geliyor. Dağıtım Merkezi/ambarlar, e-ticaret ile basıncının bruntini çok iyi hale kaz. Müşteriler artık çok sayıda ürün seçimi yapmanızı ve bu malların bir veya satın alma için iki güne ulaşmasını bekliyor. Dağıtım merkezleri, var olan verimsizlikleri üzerine gelirken bu eğilimleri uyarmalıdır. 

Bugün, el ile çalışan işçiliği, dağıtım merkezi maliyetlerinin% 55-65 ' i için çekme ve ambalaj hesapları anlamına gelir. El ile işçilikinin dağıtım merkezini yavaşlatabilse de, ekip oluşturma ihtiyaçlarını hızla dalgalanarak (tatil işçiliği 10 x 'e kadar gider), nakliye hacimlerini daha da zorlaştırır. Bu mevsimlik, yüksek ciro ve hata olasılığını ve pahalı yeniden çalışma ihtiyacını de arttığı sonuçları artırır.
IoT özellikli kameraları temel alan çözümler, dijital bir geri bildirim döngüsünü etkinleştirerek dönüştürülebilir avantajlar sunabilir. Dağıtım Merkezi genelindeki bu veriler, daha iyi veri oluşmasına neden olan, eyleme dönüştürülebilir içgörülere yol açar.

Avantajlar şunlardır 
* Kameralar, geldikçe malları izler ve taşıyıcı sistem üzerinden taşınır
* Hatalı malları tanımla ve bunları onarmak üzere gönder
* Siparişlerin etkili bir şekilde izlenmesini sağlayın
* Maliyeti azaltın, geliştirilmiş üretkenlik & kullanımı en üst düzeye çıkarın

### <a name="out-of-box-experience"></a>Kutudan çıkan deneyim
İş ortakları, yukarıda açıklanan avantajlardan & eyleme dönüştürülebilir içgörüler kazanmak için dijital dağıtım merkezi oluşturmak üzere bu uygulama şablonundan yararlanabilir. Yayımlanan şablon, IoT Central kamera ve uç cihazlarının cihaz bağlantısı yapılandırması & yönetimine odaklanır. 

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center panosu](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Yukarıdaki panonun örnek bir deneyim olduğunu ve bu uygulamayı istediğiniz kullanım örneğine uyacak şekilde tam olarak özelleştirebileceğinizi lütfen unutmayın.

Dijital dağıtım merkezi şablonlarından birini kullanarak bir çözüm oluşturma konusunda size yol gösteren [uçtan uca öğreticiye](./tutorial-iot-central-digital-distribution-center-pnp.md) başlayın.



## <a name="what-is-in-store-analytics-condition-monitoring"></a>Mağaza Analytics Condition izleme nedir?
Günümüzün rekabet dünyası 'nda, perakendecilerin fiziksel depolarıyla trafiği kullanabilmesi için kullanıcılara benzersiz veya özel bir şeyler sunmanın yeni yollarını arıyor. Birçok perakende, rakiplerinin bir araç olarak mağazalarıyla ayırt edilmesine yönelik çevresel koşulların önemini kabul etmektedir. Perakendeciler, müşterilerine rahat bir deneyim sunmak için her zaman mağazalarındaki önemli koşulları korudıklarından emin olmak ister.  

IoT Central içindeki mağaza Içi analiz koşulu Izleme uygulaması şablonu, çözüm oluşturucusuna bir uçtan uca çözüm oluşturmak için kullanılabilecek bir tuval sağlar. Uygulama şablonu, çeşitli algılayıcı cihazları kullanarak bir perakende mağaza ortamını dijital olarak bağlamanıza ve izlemesine olanak sağlar. Bu algılayıcı cihazları, perakendecilerin işletim maliyetlerini azaltmasına ve müşterilerinin sevdiği deneyimler oluşturmalarına olanak tanıyan anlamlı sinyalleri yakalar.

Uygulama şablonu şunları yapmanıza olanak sağlar:

*  Birçok IoT sensöri IoT Central uygulama örneğine sorunsuz bir şekilde bağlayın.
*  Ağ Geçidi cihazlarının yanı sıra algılayıcı ağının sistem durumunu izleyin ve yönetin.
*  Uygun uyarıları tetiklemek için bir depodaki çevresel koşulların etrafında özel kurallar oluşturun.
*  Mağazalarınızın içindeki ortam koşullarını perakende mağaza ekibi tarafından yararlanılabilir olabilecek öngörülere dönüştürün.
* Toplu öngörüleri, perakende personeli üyelerini güçlendirin mevcut veya yeni iş uygulamalarına dışarı aktarın.

### <a name="out-of-box-experience"></a>Kutudan çıkan deneyim
Uygulama şablonu, bir dizi cihaz şablonu ve bir operatör deneyimiyle birlikte gelir. Pano öğelerini doldurmak için bir sanal cihaz kümesinden yararlanır. [Mağaza Içi analiz koşulu izleme](https://aka.ms/conditiontemplate) uygulaması şablonunu kullanarak bir IoT Central uygulaması dağıttıktan sonra, varsayılan uygulama panosuna aşağıda gösterildiği gibi gidecaksınız. 

> [!div class="mx-imgBorder"]
> Mağaza Içi analiz koşulu Izleme](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png) ![

Yukarıdaki panonun örnek bir deneyim olduğunu ve bu uygulamayı istediğiniz kullanım örneğine uyacak şekilde tam olarak özelleştirebileceğinizi lütfen unutmayın. 

Store Analytics Condition izleme şablonlarından birini kullanarak bir çözüm oluşturma konusunda size yol gösteren [uçtan uca öğreticiye](./tutorial-in-store-analytics-create-app-pnp.md) başlayın.



## <a name="what-is-in-store-analytics-checkout"></a>Mağaza analizlerinin kullanıma alınması nedir?
Giderek daha fazla rekabet eden modern, modern bir şekilde, müşteri beklentilerini aşan ve geri gelmelerini engelleyen bir mağaza deneyimi sunma konusunda sürekli büyüdü. Bu gereksinimi karşılamak için teknolojiyi dağıtmaya başladığımız için, büyük ölçüde fark edilmemiş bir alan kullanıma alma deneyimidir.

IoT Central içindeki mağaza içi analiz kullanıma alma uygulaması şablonu, çözüm oluşturucularının mağazalarının teslim alma bölgesi etrafında anlamlı Öngörüler sunan, perakende personeli güçlendiren deneyimler oluşturmasına olanak sağlar. Bir perakende mağaza içindeki her bir kullanıma alma kulvarın doluluk durumunu öğrenmek için bir sanal cihaz kümesini kullanır. Algılayıcılar, kişi sayısını ve her bir kullanıma alma kulvarın ortalama bekleme süresini yakalamanızı sağlar.

Şablon, çözüm oluşturucusunun, şunları yapmasına olanak tanıyan bir temel IoT çözümü sunarak pazara sunma planlarını hızlandırmasına yardımcı olur: 

* Birçok IoT sensöri IoT Central uygulama örneğine sorunsuz bir şekilde bağlayın.
* Ağ Geçidi cihazlarının yanı sıra algılayıcı ağının sistem durumunu izleyin ve yönetin.
* Uygun uyarıları tetiklemek için bir mağaza içindeki kullanıma alma koşulunun etrafında özel kurallar oluşturun.
* Mağazalarınızın içindeki kullanıma alma koşullarını perakende mağaza ekibi tarafından yararlanılabilir olabilecek öngörülere dönüştürün.
* Toplu öngörüleri, perakende personeli üyelerini güçlendirin mevcut veya yeni iş uygulamalarına dışarı aktarın.

### <a name="out-of-box-experience"></a>Kutudan çıkan deneyim
Uygulama şablonu, bir dizi cihaz şablonu ve bir operatör deneyimiyle birlikte gelir. Pano öğelerini doldurmak için bir sanal cihaz kümesinden yararlanır. [Mağaza Analizi kullanıma alma](https://aka.ms/checkouttemplate) uygulama şablonunu kullanarak bir IoT Central uygulaması dağıttıktan sonra, varsayılan uygulama panosuna aşağıda gösterildiği gibi gidecaksınız. 

> [!div class="mx-imgBorder"]
> Mağaza Analizi kullanıma alma](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png) ![

Yukarıdaki panonun örnek bir deneyim olduğunu ve bu uygulamayı istediğiniz kullanım örneğine uyacak şekilde tam olarak özelleştirebileceğinizi lütfen unutmayın. 


Store Analytics kullanıma alma şablonlarından birini kullanarak bir çözüm oluşturma konusunda size yol gösteren [uçtan uca öğreticiye](./tutorial-in-store-analytics-create-app-pnp.md) başlayın.


## <a name="what-is-smart-inventory-management-solution"></a>Smart Inventory Management çözümü nedir?
"Stok", bir satıcı tarafından tutulan malların envanterini. Her perakendecinin tedarik ve lojistik sağlama süresini karşılamak için stoğa ihtiyacı vardır. Envanter, her satıcıdan ticareti gereken en değerli kaynaktır. Bugünün omnichannel dünyasında, doğru zamanda doğru ürünün doğru yerde olduğundan emin olmak için stok yönetimi önemli bir gereksinimdir. Satıcıdan çok fazla veya çok az stok depolamak, perakendecinin işletmesidir. Her yıl perakende, envanter yönetimi özellikleri olmaması nedeniyle gelirin% 8-10 ' unu kaybediyor.

Radyo frekansı kimliği (RFıD) tarafından etkinleştirilen IoT verileri, Işaretleri & Kamera, bu büyük ölçüde bu zorluk ölçeği ölçeğinde bir fırsattır. IoT sinyallerine ait bağlantı ve gerçek zamanlı analizler, perakendecinin stok Woes için oyun değiştirici haline geldi.  Sensörlerden toplanan veriler, cihazlar, hava durumu, & olayları bulut tabanlı Iş zekası sistemleriyle tümleştirilebilir.  
Akıllı envanter yönetimi avantajları şunlardır 
* Organizasyonu, stok aşımlarını karşı korur ve istenen müşteri hizmeti düzeyini sağlar. 
* Ayrıntılı analiz & neredeyse gerçek zamanlı olarak envanter doğruluğu hakkında öngörüler elde edin
* Müşteri siparişlerini karşılayan doğru stok miktarına karar verin

### <a name="out-of-box-experience"></a>Kutudan çıkan deneyim
İş ortakları, ana hatlı avantajlar & uçtan uca akıllı envanter yönetimi çözümlerini geliştirmek için şablondan yararlanabilir. Bu yayımlanmış şablon, IoT Central ' deki RFıD &, Bluetooth düşük enerji (BLE) okuyucularına yönelik cihaz bağlantısı, yapılandırma & yönetimi üzerine odaklanır. 

> [!div class="mx-imgBorder"]
> Akıllı envanter yönetimi panosu ![](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Yukarıdaki panonun örnek bir deneyim olduğunu ve bu uygulamayı istediğiniz kullanım örneğine uyacak şekilde tam olarak özelleştirebileceğinizi lütfen unutmayın. 

Akıllı envanter yönetimi şablonlarından birini kullanarak bir çözüm oluşturma konusunda size yol gösteren [uçtan uca öğretici](./tutorial-iot-central-smart-inventory-management-pnp.md) ile çalışmaya başlayın.


## <a name="next-steps"></a>Sonraki adımlar
Bir perakende çözümü oluşturmaya başlamak için:
* Store Analytics Uygulama şablonlarından birini kullanarak bir çözüm oluşturma konusunda size yol gösteren [uçtan uca öğreticiye](./tutorial-in-store-analytics-create-app-pnp.md) başlayın.
* [Bağlı lojistik çözüm şablonunu](./tutorial-iot-central-connected-logistics-pnp.md) dağıtmayı öğrenin
* [Dijital dağıtım merkezi şablonunu](./tutorial-iot-central-digital-distribution-center-pnp.md) dağıtmayı öğrenin
* [Akıllı envanter yönetimi şablonunu](./tutorial-iot-central-smart-inventory-management-pnp.md) dağıtmayı öğrenin
* IoT Central hakkında daha fazla bilgi edinin [IoT Central genel bakış](../preview/overview-iot-central.md)
