---
title: Azure IoT Central perakende çözümleri nelerdir? | Microsoft Docs
description: Bağlı lojistik, dijital dağıtım merkezi, mağaza içi analiz, koşul izleme, kullanıma alma, akıllı envanter yönetimi ve perakende çözümleri oluşturmak için Azure IoT Central uygulama şablonlarını kullanma hakkında bilgi edinin.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 56198cf661001bcfcec45d483d0f691234851894
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832292"
---
# <a name="what-are-the-iot-central-retail-solutions"></a>IoT Central perakende çözümleri nelerdir?

Azure IoT Central, kurumsal düzeyde IoT çözümlerini geliştirme, yönetme ve sürdürme ile ilişkili yükü ve maliyeti azaltan bir IoT uygulama platformudur. Azure IoT Central ile oluşturmayı seçtiğinizde, yalnızca karmaşık ve sürekli gelişen bir IoT altyapısını korumak ve güncelleştirmek yerine, işletmenizin IoT verileriyle dönüştürülmesi için zaman, para ve enerji tasarrufu sağlayabilirsiniz.

Bu makalede, perakende satış için belirli IoT Central uygulama şablonları açıklanmaktadır. Bir çözüm Oluşturucusu olarak, arz zincirlerini optimize eden IoT çözümleri oluşturmak, müşteriler için mağaza içi deneyimleri iyileştirmek ve envanteri daha verimli bir şekilde izlemek için bu şablonları kullanabilirsiniz.

:::image type="content" source="media/overview-iot-central-retail/retail-app-templates.png" alt-text="Azure IoT perakende genel bakış":::

Aşağıdaki bölümler, bu uygulama şablonlarının yeteneklerini anlatmaktadır:

## <a name="connected-logistics"></a>Bağlı lojistik

Küresel lojistik harcamanın 2020 ' de $10.600.000.000.000 'e ulaşması beklenir. Bu harcama ve nakliye sağlayıcılarının çoğunluğu için mal hesaplarının taşınması, yoğun rekabet baskısı ve kısıtlamalarıdır.

Sıcaklık, nem, eğim, darbe, ışık ve sevkıyatın bulunduğu konum gibi çevresel koşulları toplamak ve izlemek için IoT sensörlerini kullanabilirsiniz. IoT sensörlerinden ve cihazlarından toplanan Telemetriyi, bulut tabanlı iş zekası sistemlerinde hava durumu ve trafik bilgileri gibi diğer veri kaynaklarıyla birleştirebilirsiniz.

Bağlı bir lojistik çözümünün avantajları şunlardır:

* Gerçek zamanlı izleme ve takip özellikleriyle gönderileri izleme. 
* Ortam koşullarını gerçek zamanlı olarak izleyerek gönderinin bütünlüğünü denetleme.
* Gönderileri hırsızlık, kayıp veya hasara karşı koruma.
* Coğrafi sınırlama, rota iyileştirme, filo yönetimi ve araç analizi.
* Gönderilerin tahmini ayrılma ve varış zamanlarını belirleme.

Aşağıdaki ekran görüntüleri, uygulama şablonundaki kullanıma hazır panoyu gösterir. Pano, özel çözüm gereksinimlerinizi karşılayacak şekilde tamamen özelleştirilebilir:

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard1.png" alt-text="Bağlı lojistik işlemleri panosunun üst yarısını gösteren ekran görüntüsü.":::

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard2.png" alt-text="Bağlı lojistik işlemleri panosunun alt yarısını gösteren ekran görüntüsü.":::

Daha fazla bilgi edinmek için bkz. [dağıtım ve bağlı bir lojistik uygulama şablonu](./tutorial-iot-central-connected-logistics.md) öğreticisini gözden geçir.

## <a name="digital-distribution-center"></a>Dijital dağıtım merkezi

Üreticiler ve perakendeciler dünya çapında bir şekilde kullanıma suntıkça, arz zincirlerinin dalı de daha karmaşık hale gelir. Müşteriler artık büyük miktarda ürünlerin kullanılabilir olmasını ve bu malların satın alma için bir veya iki gün içinde ulaşmasını bekliyor. Dağıtım merkezleri, var olan verimsizlikleri üzerine gelirken bu eğilimleri uyarmalıdır.

Günümüzde, el ile işçiye güvenmek, dağıtım merkezi maliyetlerinin% 55-65 ' ı için çekme ve ambalaj hesapları anlamına gelir. El ile çekme ve paketleme işlemleri genellikle otomatik sistemlerden daha yavaştır ve ekip oluşturma ihtiyaçlarını hızlı bir şekilde dalgalanmak, nakliye hacimlerini de zorlaştırır. Bu mevsimler, yüksek personel cirosu elde eden sonuçlara neden olur ve maliyetli hata olasılığını artırır.

IoT özellikli kameraları temel alan çözümler, dijital bir geri bildirim döngüsünü etkinleştirerek dönüştürülebilir avantajlar sunabilir. Dağıtım Merkezi genelindeki veriler, daha iyi veri ile sonuçlanarak eyleme dönüştürülebilir içgörülere yol açar.

Dijital bir dağıtım merkezinin avantajları şunlardır:

* Kameralar, geldikçe malları izler ve taşıyıcı sistem üzerinden taşınır.
* Hatalı malların otomatik tanımlanması.
* Etkili sıra izleme.
* Düşük maliyetler, geliştirilmiş üretkenlik ve iyileştirilmiş kullanım.

Aşağıdaki ekran görüntüsünde, uygulama şablonundaki kullanıma hazır pano görüntülenir. Pano, özel çözüm gereksinimlerinizi karşılayacak şekilde tamamen özelleştirilebilir: 

:::image type="content" source="media/overview-iot-central-retail/digital-distribution-center-dashboard.png" alt-text="Dijital dağıtım merkezi panosu":::

Daha fazla bilgi edinmek için bkz. [bir Digital Distribution Center uygulama şablonu öğreticisine dağıtım ve İzlenecek yol](./tutorial-iot-central-digital-distribution-center.md) .

## <a name="in-store-analytics---condition-monitoring"></a>Mağaza içi analiz-koşul izleme

Birçok perakende için, mağazaların içindeki çevresel koşullar rakiplerinden önemli bir farklımalardır. Perakendeciler, müşterilerinin avantajlarından yararlanmak için mağazalarındaki parçalar dahilinde bakım koşullarını sürdürmek ister.  

Bir çözüm Oluşturucusu olarak, uçtan uca bir çözüm oluşturmak için mağaza içi Analytics Condition izleme uygulaması şablonunu IoT Central kullanabilirsiniz. Uygulama şablonu, farklı türlerde algılayıcı cihazlarından yararlanarak bir perakende mağaza ortamına dijital olarak bağlanmanızı ve bunları izlemenizi sağlar. Bu algılayıcı cihazları, iş maliyetlerini azaltmaya ve müşterileri için harika bir deneyim oluşturmanıza yardımcı olan iş öngörülerine dönüştürebileceğiniz telemetri oluşturur.

Uygulama şablonunu kullanarak şunları yapın:

* Farklı türlerde IoT algılayıcılarının IoT Central bir uygulama örneğine bağlanmasını sağlar.
* Ağ algılayıcısı ağının ve ortamdaki tüm ağ geçidi cihazlarının sistem durumunu izleyin ve yönetin.
* Mağaza yöneticileri için uyarıları tetiklemek üzere bir depodaki ortam koşullarının etrafında özel kurallar oluşturun.
* Mağazalarınızın içindeki çevresel koşulları, Retail Store ekibinin müşteri deneyimini geliştirmek için kullanabileceği öngörülere dönüştürün.
* Toplu öngörüleri mevcut veya yeni iş uygulamalarına dışarı aktarıp perakende personeli için yararlı ve zamanında bilgiler sağlayın.

Uygulama şablonu bir cihaz şablonları kümesiyle birlikte gelir ve panoyu doldurmak için bir sanal cihaz kümesi kullanır. 

Aşağıdaki ekran görüntüsünde, uygulama şablonundaki kullanıma hazır pano görüntülenir. Pano, özel çözüm gereksinimlerinizi karşılayacak şekilde tamamen özelleştirilebilir: 

:::image type="content" source="media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png" alt-text="Mağaza içi analiz koşulu Izleme":::

Daha fazla bilgi edinmek için bkz. [Azure 'da bir mağaza 'da analiz uygulaması oluşturma IoT Central](./tutorial-in-store-analytics-create-app.md) öğreticisi.

## <a name="in-store-analytics---checkout"></a>Mağaza içi analiz-kullanıma alma

Bazı perakendeciler için mağazaların içindeki kullanıma alma deneyimi rakiplerinden önemli bir farklıdır. Perakendeciler, müşterilerin döndürmesini teşvik etmek için mağazalarında sorunsuz bir kullanıma alma deneyimi sunmak ister.  

Bir çözüm Oluşturucusu olarak, bir mağazanın teslim alma bölgesinin perakende personeline ait kullanıma sunduğu içgörüler sunan bir çözüm oluşturmak için IoT Central mağaza Analizi kullanıma alma uygulaması şablonunu kullanabilirsiniz. Örneğin, sensörler, her bir kullanıma alma şeridi için sıra uzunlukları ve ortalama bekleme süreleri hakkında bilgi verebilir.

Uygulama şablonunu kullanarak şunları yapın:

* Farklı türlerde IoT algılayıcılarının IoT Central bir uygulama örneğine bağlanmasını sağlar.
* Ağ algılayıcısı ağının ve ortamdaki tüm ağ geçidi cihazlarının sistem durumunu izleyin ve yönetin.
* Retail personele yönelik uyarıları tetiklemek için bir mağaza içindeki kullanıma alma koşulunun etrafında özel kurallar oluşturun.
* Mağaza içindeki kullanıma alma koşullarını, Retail Store ekibinin müşteri deneyimini geliştirmek için kullanabileceği öngörülere dönüştürün.
* Toplu öngörüleri mevcut veya yeni iş uygulamalarına dışarı aktarıp perakende personeli için yararlı ve zamanında bilgiler sağlayın.

Uygulama şablonu bir cihaz şablonları kümesiyle birlikte gelir ve panoyu Lane doluluk verileriyle doldurmak için bir dizi sanal cihaz kullanır. 

Aşağıdaki ekran görüntüsünde, uygulama şablonundaki kullanıma hazır pano görüntülenir. Pano, özel çözüm gereksinimlerinizi karşılayacak şekilde tamamen özelleştirilebilir: 

:::image type="content" source="media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png" alt-text="Mağaza içi analiz kullanıma alma":::

Daha fazla bilgi edinmek için bkz. [Azure 'da bir mağaza 'da analiz uygulaması oluşturma IoT Central](./tutorial-in-store-analytics-create-app.md) öğreticisi.

## <a name="smart-inventory-management"></a>Akıllı envanter yönetimi

Stok, bir perakendecinin elinde tutulduğu malların envanterini alır. Envanter yönetimi, doğru ürünün doğru zamanda doğru yerde olduğundan emin olmak için önemlidir. Satıcı, talebi karşılamak için stokta yeterli miktarda öğe bulunmayan maliyetlere göre çok fazla envanteri depolamanın maliyetlerini dengelemenize gerek vermelidir.

Radyo frekansı tanımlama (RFıD) etiketlerinden, işaretleriyle ve kameralardan oluşturulan IoT verileri, envanter yönetimi süreçlerini geliştirmek için fırsat sağlar. IoT sensörlerinden ve cihazlarından toplanan Telemetriyi, bulut tabanlı iş zekası sistemlerinde hava durumu ve trafik bilgileri gibi diğer veri kaynaklarıyla birleştirebilirsiniz.

Akıllı envanter yönetimi 'nin avantajları şunlardır:

* Maddelerin stokunda risk azaltma ve istenen müşteri hizmeti düzeyini sağlama. 
* Neredeyse gerçek zamanlı olarak stok doğruluğu hakkında ayrıntılı analiz ve Öngörüler.
* Müşteri emirlerini karşılamak üzere tutulacak doğru envanter miktarına karar vermenize yardımcı olacak araçlar.

Bu uygulama şablonu cihaz bağlantısına odaklanır ve RFıD ve Bluetooth düşük enerji (BLE) okuyucu cihazlarının yapılandırma ve yönetimine odaklanır.

Aşağıdaki ekran görüntüsünde, uygulama şablonundaki kullanıma hazır pano görüntülenir. Pano, özel çözüm gereksinimlerinizi karşılayacak şekilde tamamen özelleştirilebilir:

:::image type="content" source="media/overview-iot-central-retail/smart-inventory-management-dashboard.png" alt-text="Akıllı envanter yönetimi panosu":::

Daha fazla bilgi edinmek için bkz. [dağıtım ve bir akıllı envanter yönetimi uygulama şablonu](./tutorial-iot-central-smart-inventory-management.md) öğreticisini gözden geçir.

## <a name="micro-fulfillment-center"></a>Mikro karşılama merkezi

Daha fazla rekabet eden perakende yatay, perakendeciler, talep ve yerine getirme arasındaki boşluğu kapatmak için sürekli olarak bir basınç. Büyüyen tüketici talebini ele almak için ortaya çıktı olan yeni bir eğilim, son müşterilere ve ziyaret ettikleri depolara yaklaşmaktadır.

IoT Central mikro Karşılama Merkezi uygulama şablonu, çözüm oluşturucularının tamamen otomatikleştirilmiş karşılama merkezlerinin tüm yönlerini izlemesine ve yönetmesine olanak sağlar. Şablon, çözüm geliştirme sürecini hızlandırmak için bir dizi sanal durum izleme algılayıcı ve robot taşıyıcılar içerir. Bu algılayıcı cihazları, perakendecilerin kendi işletim maliyetlerini azaltmasına ve müşterilerine yönelik deneyimler oluşturmalarına olanak tanıyan anlamlı sinyalleri yakalar.

Uygulama şablonu şunları yapmanızı sağlar: 

- IoT Central bir uygulama örneğine robots veya koşul izleme algılayıcıları gibi farklı IoT sensörleri türlerini sorunsuzca bağlayın.
- Algılayıcı ağının sistem durumunu ve ortamdaki tüm ağ geçidi cihazlarını izleyin ve yönetin.
- Uygun uyarıları tetiklemek için bir karşılama Merkezi içindeki çevresel koşulların etrafında özel kurallar oluşturun.
- Karşılama merkezinizdeki çevresel koşulları, perakende ambar ekibi tarafından yararlanılabilir olabilecek öngörülere dönüştürün.
- Toplu öngörüleri, perakende personeli üyelerinin avantajı için mevcut veya yeni iş uygulamalarına dışarı aktarın.

Aşağıdaki ekran görüntüsünde, uygulama şablonundaki kullanıma hazır pano görüntülenir. Pano, özel çözüm gereksinimlerinizi karşılayacak şekilde tamamen özelleştirilebilir:

:::image type="content" source="media/overview-iot-central-retail/MFC-Dashboard.png" alt-text="Mikro Karşılama Merkezi":::

Daha fazla bilgi edinmek için bkz. [dağıtım ve Micro-Karşılama Merkezi uygulama şablonu](./tutorial-micro-fulfillment-center.md) öğreticisini inceleyin.

## <a name="video-analytics---object-and-motion-detection"></a>Video analizi - nesne ve hareket algılama

*IoT Central video analizi-nesne ve hareket algılama* uygulama şablonu, nesneleri ve hareketi algılamak için akıllı sınır kameralarını kullanan bir çözümün nasıl dağıtılacağını, yönetileceğini ve izleneceğini hızlı bir şekilde görmenizi sağlar.

Video analizi uygulaması, IoT Edge çalıştıran [canlı bir video analizi (LVA)](#live-video-analytics) modülünü kullanır. LVA modülü, kenarı ve bulutu kapsayan akıllı video uygulamaları oluşturmanız için bir platform sağlar. Platformu, video analizi uygulaması gibi IoT çözümlerini, nesne ve hareket algılama ile geliştirmek için kullanabilirsiniz.

Uygulama şablonu dört uygulama panosu içerir:

* **Başlarken,** uygulama şablonunu kullanmaya başlamanıza yardımcı olacak kaynaklara bağlantılar sağlar.
* **Demo panosu** , bağlı kameralardan görüntülenebilecek bilgi türlerinin bir resmini sağlar.
* **(Örnek) gerçek kamera yönetimi** , sanal kameraları uygulamadan kullanarak nasıl yönetebileceğinizi gösterir.
* **(Örnek) gerçek kamera İzleyicisi** , sanal kameralarınızı uygulamadan nasıl izleyekullanabileceğinizi göstermek için sanal kameraları kullanır.

:::image type="content" source="media/overview-iot-central-retail/live-video-analytics.png" alt-text="Video analizi - nesne ve hareket algılama":::

Çözümün mimarisi hakkında daha fazla bilgi edinmek için bkz. [video analizi uygulama mimarisi](architecture-video-analytics.md).

Çözümü dağıtmayı öğrenmek için bkz. [Azure 'da video Analytics uygulaması oluşturma IoT Central](tutorial-video-analytics-deploy.md) öğreticisi.

### <a name="live-video-analytics"></a>Canlı video analizi

[Canlı video analizi](https://github.com/Azure/live-video-analytics) , kenarı ve bulutu kapsayan akıllı video uygulamaları oluşturmanız için bir platform sağlar. Platform, canlı videoyu yakalama, kaydetme, çözümleme ve sonuçları video veya video analizi olabilecek sonuçları Azure hizmetlerine yayımlama özelliği sunar. Azure Hizmetleri, bulutta veya kenarda çalışıyor olabilir. Platformu, video analiziyle IoT çözümlerini geliştirmek için kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bir perakende çözümü oluşturmaya başlamak için:

* Azure IoT Central, mağaza içi analitik uygulama şablonlarından biriyle bir çözüm oluşturma konusunda size yol gösteren [Azure 'da bir yerleşik analiz uygulaması oluşturma](./tutorial-in-store-analytics-create-app.md) öğreticisini kullanmaya başlayın.
* [Video analizi uygulama şablonunu dağıtın ve gözden geçir](./tutorial-video-analytics-deploy.md).
* [Bağlı bir lojistik uygulama şablonunu dağıtın ve gözden geçir](./tutorial-iot-central-connected-logistics.md).
