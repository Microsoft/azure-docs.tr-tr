---
title: Azure IoT Hub kotalarını ve azaltmayı anlayın | Microsoft Docs
description: Geliştirici Kılavuzu-IoT Hub için uygulanan kotaların açıklaması ve beklenen daraltma davranışı.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 1ed1b105f64d109284de441af1bcaee5f0827d75
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331373"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Başvuru IoT Hub kotaları ve azaltma

Bu makalede bir IoT Hub kotaları açıklanmakta ve azaltmasının nasıl çalıştığını anlamanıza yardımcı olacak bilgiler sağlanmaktadır.

## <a name="quotas-and-throttling"></a>Kotalar ve azaltma

Her Azure aboneliği en fazla 50 IoT Hub 'ına ve 1 ücretsiz hub 'a sahip olabilir.

Her IoT hub'ı belirli bir katmanda belirli bir birim sayısıyla sağlanır. Katman ve birim sayısı, gönderebilmeniz gereken en fazla günlük ileti kotasını tespit edebilir. Günlük kotayı hesaplamak için kullanılan ileti boyutu, ücretsiz bir katman hub 'ı için 0,5 KB ve diğer tüm katmanlar için 4KB 'dir. Daha fazla bilgi için bkz. [Azure IoT Hub fiyatlandırması](https://azure.microsoft.com/pricing/details/iot-hub/).

Katman Ayrıca IoT Hub tüm işlemlerde zorladığı azaltma sınırlarını belirler.

### <a name="iot-plug-and-play"></a>IoT Tak ve Kullan

Genel Önizleme sırasında IoT Tak ve Kullan cihazları Arabirim başına ayrı mesajlar gönderir ve bu da ileti kotasından doğru olarak sayılan ileti sayısını artırabilir.

## <a name="operation-throttles"></a>İşlem kısıtları

İşlem kısıtlar, dakika aralıklarında uygulanan ve kötüye kullanımı engellemek için tasarlanan hız sınırlamalarıdır. Ayrıca [trafik şekillendirmeye](#traffic-shaping)tabidir.

Aşağıdaki tabloda zorlanan kısıtlar gösterilmektedir. Değerler tek bir hub 'a başvurur.

| Kısıtlama | Ücretsiz, B1 ve S1 | B2 ve S2 | B3 ve S3 | 
| -------- | ------- | ------- | ------- |
| [Kimlik kayıt defteri işlemleri](#identity-registry-operations-throttle) (oluşturma, alma, listeleme, güncelleştirme, silme) | 1.67/sn/birim (100/dak/Unit) | 1.67/sn/birim (100/dak/Unit) | 83.33/sn/birim (5000/dk/birim) |
| [Yeni cihaz bağlantıları](#device-connections-throttle) (Bu sınır, toplam bağlantı sayısı değil, _Yeni bağlantı_oranı için geçerlidir) | 100/sn veya 12/sn/birim üzerinde daha yüksek <br/> Örneğin, iki S1 birimi 2 @ no__t-012 = 24 yeni bağlantı/sn olmakla kalmaz, birimlerinizde en az 100 yeni bağlantı/sn vardır. Dokuz S1 birimiyle, birimlerinizde 108 yeni bağlantı/sn (9 @ no__t-012) vardır. | 120 yeni bağlantı/sn/birim | 6\.000 yeni bağlantı/sn/birim |
| Cihazdan buluta gönderim | 100 gönderme işlemi/sn veya 12 gönderme işlemi/sn/birim daha yüksek <br/> Örneğin, iki S1 birimi 2 @ no__t-012 = 24/sn olmakla kalmaz, birimleriniz arasında en az 100 gönderme işlemi/sn vardır. Dokuz S1 birimiyle, birimleriniz arasında 108 gönderme işlemi/sn (9 @ no__t-012) vardır. | 120 gönderme işlemi/sn/birim | 6\.000 gönderme işlemi/sn/birim |
| Buluttan cihaza cihaz gönderme<sup>1</sup> | 1,67 gönderme işlemi/sn/birim (100 ileti/dk/birim) | 1,67 gönderme işlemi/sn/birim (100 gönderme işlemi/dk/birim) | 83,33 gönderme işlemi/sn/birim (5.000 gönderme işlemi/dk/birim) |
| Buluttan cihaza<sup>1</sup> alır <br/> (yalnızca cihaz HTTPS kullandığında)| 16,67 alma işlemi/sn/birim (1.000 alma işlemi/dk/birim) | 16,67 alma işlemi/sn/birim (1.000 alma işlemi/dk/birim) | 833,33 alma işlemi/sn/birim (50.000 alma işlemi/dk/birim) |
| Karşıya dosya yükleme | 1,67 dosya yükleme başlatma/sn/birim (100/dak/Unit) | 1,67 dosya yükleme başlatma/sn/birim (100/dak/Unit) | 83,33 dosya yükleme başlatma/sn/birim (5000/dk/birim) |
| Doğrudan Yöntemler<sup>1</sup> | 160KB/sn/birim<sup>2</sup> | 480KB/sn/birim<sup>2</sup> | 24 MB/sn/birim<sup>2</sup> | 
| Sorgular | 20/dak/birim | 20/dak/birim | 1000/dak/Unit |
| İkizi (cihaz ve modül) okur<sup>1</sup> | 100/sn | 100/sn veya 10/sn/birim üzerinde daha yüksek | 500/sn/birim |
| İkizi güncelleştirmeleri (cihaz ve modül)<sup>1</sup> | 50/sn | 50/sn veya 5/sn/birim üzerinde daha yüksek | 250/sn/birim |
| İş işlemleri<sup>1</sup> <br/> (oluşturma, güncelleştirme, listeleme, silme) | 1.67/sn/birim (100/dak/Unit) | 1.67/sn/birim (100/dak/Unit) | 83.33/sn/birim (5000/dk/birim) |
| İşler cihaz işlemi<sup>1</sup> <br/> (Update ikizi, doğrudan yöntemi çağır) | 10/sn | 10/sn veya 1/sn/birim üzerinde daha yüksek | 50/sn/birim |
| Yapılandırma ve kenar dağıtımları<sup>1</sup> <br/> (oluşturma, güncelleştirme, listeleme, silme) | 0.33/sn/birim (20/dak/Unit) | 0.33/sn/birim (20/dak/Unit) | 0.33/sn/birim (20/dak/Unit) |
| Cihaz akışı başlatma oranı<sup>1</sup> | 5 yeni akış/sn | 5 yeni akış/sn | 5 yeni akış/sn |
| Eşzamanlı olarak bağlanılan cihaz akışlarının maksimum sayısı<sup>1</sup> | 50 | 50 | 50 |
| Maksimum cihaz akışı veri aktarımı<sup>1</sup> (Toplam birim/gün) | 300 MB | 300 MB | 300 MB |

<sup>1</sup> Bu özellik IoT Hub temel katmanında kullanılamaz. Daha fazla bilgi için bkz. [doğru IoT Hub seçme](iot-hub-scaling.md). <br/><sup>2</sup> Azaltma ölçer boyutu 4 KB 'tır.

### <a name="throttling-details"></a>Kısıtlama ayrıntıları

* Ölçüm boyutu, azaltma limitinin hangi artışlara harcandığını belirler. Doğrudan çağrınızın yükü 0 ile 4 KB arasında ise, 4 KB olarak sayılır. 160 KB/sn/birim sınırına geçmeden önce birim başına saniye başına en fazla 40 çağrı yapabilirsiniz.

   Benzer şekilde, yükünüzü 4 KB ile 8 KB arasında olursa, her 8 KB 'lik çağrı hesabı ve maksimum sınıra geçmeden önce birim başına saniyede 20 çağrı yapabilirsiniz.

   Son olarak, yük boyutunuz 156KB ve 160 KB arasındaysa, 160 KB/sn/birim sınırına geçmeden önce hub 'ınızdaki birim başına yalnızca bir saniyede 1 çağrı yapabilirsiniz.

*  S2 için *iş cihazı işlemleri (güncelleştirme ikizi, doğrudan yöntemini çağır)* , yalnızca işleri kullanarak yöntemleri çağırdığınızda 50/sn/birim için geçerlidir. Doğrudan metotları doğrudan çağırdığınızda, 24 MB/sn/birim (S2 için) için özgün azaltma sınırı uygulanır.

*  **Kota** , *her gün*hub 'ınızda gönderebilmeniz için toplam ileti sayısıdır. Hub 'ın kota sınırını [IoT Hub fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/iot-hub/) **toplam ileti sayısı/gün** sütununda bulabilirsiniz.

*  Buluttan cihaza ve cihazdan buluta, iletileri gönderebilmeniz için, 4 KB 'lık öbekten bağımsız olarak ileti sayısını belirleme. Her ileti en fazla 256 KB olabilir ve bu [en fazla ileti boyutudur](iot-hub-devguide-quotas-throttling.md#other-limits).

*  Azaltma sınırlarını vurmadan/aşmamak için aramalarınızı kısıtlamak iyi bir uygulamadır. Sınıra ulaşırsanız, IoT Hub 429 hata koduyla yanıt verir ve istemci yeniden denenmelidir. Bu sınırlar hub başına (veya hub/birim başına bazı durumlarda). Daha fazla bilgi için bkz. [bağlantı ve güvenilir mesajlaşma/yeniden deneme düzenlerini yönetme](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Trafik şekillendirme

IoT Hub, aşırı ara trafiğe uyum sağlamak için, sınırlı bir süre için azaltma üzerindeki istekleri kabul eder. Bu isteklerin ilk azı hemen işlenir. Ancak, istek sayısı azaltma oranını ihlal ederse IoT Hub, istekleri bir sıraya yerleştirmeyi ve sınır hızında işlenmesini başlatır. Bu etkiye *trafik şekillendirme*adı verilir. Ayrıca, bu sıranın boyutu sınırlı olur. Kısıtlama ihlali devam ederse, sonunda sıra doldurulur ve IoT Hub `429 ThrottlingException` ile istekleri reddetme başlatılır.

Örneğin, bir saniyede 200 cihazdan buluta ileti göndermek için bir sanal cihaz kullanın. S1 IoT Hub (bir sınırı 100/sn D2C gönderir). İlk dakika veya ikisi için iletiler hemen işlenir. Ancak, cihaz kısıtlama sınırından daha fazla ileti gönderilmeye devam ettiğinden, IoT Hub saniyede yalnızca 100 ileti işleme başlar ve geri kalanı bir sıraya koyar. Yaşıyorsanız artırılan gecikme süresi başlar. Sonuç olarak, kuyruk dolduğunda @no__t alma başlar ve [IoT Hub ölçümlerinde](iot-hub-metrics.md) "kısıtlama hatası sayısı" artmaya başlar.

### <a name="identity-registry-operations-throttle"></a>Kimlik kayıt defteri işlemleri kısıtlama

Cihaz kimliği kayıt defteri işlemleri, cihaz yönetimi ve sağlama senaryolarında çalışma zamanı kullanımı için tasarlanmıştır. Çok sayıda cihaz kimliği okumak veya güncelleştirmek, [içeri ve dışarı aktarma işleri](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)aracılığıyla desteklenir.

### <a name="device-connections-throttle"></a>Cihaz bağlantıları kısıtlaması

*Cihaz bağlantıları* kısıtlama, yeni cihaz bağlantılarının bir IoT Hub 'ı ile nasıl kurulabileceği oranını yönetir. *Cihaz bağlantıları* kısıtlaması, eşzamanlı olarak bağlanan en fazla cihaz sayısını yönetmez. *Cihaz bağlantısı* hız kısıtlaması, IoT Hub için sağlanan birim sayısına bağlıdır.

Örneğin, tek bir S1 birimi satın aldıysanız, saniyede 100 bağlantı kısıtlaması alırsınız. Bu nedenle, 100.000 cihazlarını bağlamak için en az 1.000 saniye sürer (yaklaşık 16 dakika). Ancak, kimlik kayıt defterinizde kayıtlı cihazlarınızla aynı anda bağlı olan çok sayıda cihaza sahip olabilirsiniz.

## <a name="other-limits"></a>Diğer sınırlar

IoT Hub diğer işlem sınırlarını zorlar:

| İşlem | Sınır |
| --------- | ----- |
| Cihazlar | Tek bir IoT Hub 'ına kaydedilenebilir cihazların toplam sayısı ve modülleri 1.000.000 adresinden alınır. Bu sınırı artırmanın tek yolu [Microsoft desteği](https://azure.microsoft.com/support/options/)ile iletişim kurmaya yönelik bir yoldur.|
| Dosya karşıya yükleme | cihaz başına 10 eşzamanlı dosya karşıya yüklemesi. |
| İşler<sup>1</sup> | Maksimum eşzamanlı iş 1 (ücretsiz ve S1 için), 5 (S2 için) ve 10 (S3 için). Ancak, tüm katmanlar için maksimum eşzamanlı [cihaz içeri/dışarı aktarma işi](iot-hub-bulk-identity-mgmt.md) 1 ' dir. <br/>İş geçmişi 30 güne kadar tutulur. |
| Ek uç noktalar | Ücretli SKU hub 'ları 10 ek uç nokta içerebilir. Ücretsiz SKU hub 'ları, ek bir uç noktaya sahip olabilir. |
| İleti yönlendirme sorguları | Ücretli SKU hub 'ları 100 yönlendirme sorgusuna sahip olabilir. Ücretsiz SKU hub 'ları beş yönlendirme sorgusuna sahip olabilir. |
| İleti zenginleştirmeleri | Ücretli SKU hub 'ları en fazla 10 ileti zenginleştirme olabilir. Ücretsiz SKU hub 'ları en fazla 2 ileti zenginleştirme olabilir.|
| Cihazdan buluta mesajlaşma | En büyük ileti boyutu 256 KB |
| Buluttan cihaza mesajlaşma<sup>1</sup> | En büyük ileti boyutu 64 KB. Dağıtım için maksimum bekleyen ileti sayısı, cihaz başına 50 ' dir. |
| Doğrudan yöntem<sup>1</sup> | En yüksek doğrudan Yöntem yük boyutu 128 KB 'tır. |
| Otomatik cihaz yapılandırması<sup>1</sup> | ücretli SKU hub 'ı başına 100 yapılandırma. Ücretsiz SKU hub 'ı başına 20 yapılandırma. |
| IoT Edge otomatik dağıtımlar<sup>1</sup> | Dağıtım başına 20 modül. ücretli SKU hub 'ı başına 100 dağıtım. Ücretsiz SKU hub 'ı başına 10 dağıtım. |
| TWINS<sup>1</sup> | İkizi bölümü başına en büyük boyut (Etiketler, istenen özellikler, bildirilen Özellikler) 8 KB 'tır |

<sup>1</sup> Bu özellik IoT Hub temel katmanında kullanılamaz. Daha fazla bilgi için bkz. [doğru IoT Hub seçme](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Kota veya kısıtlama sınırını artırma

Belirli bir zamanda, [bir IoT Hub 'ında sağlanan birim sayısını artırarak](iot-hub-upgrade.md)kotaları veya kısıtlama sınırlarını artırabilirsiniz.

## <a name="latency"></a>Gecikme süresi

IoT Hub tüm işlemler için düşük gecikme süresine sahiptir. Ancak, ağ koşulları ve diğer öngörülemeyen faktörlere bağlı olarak belirli bir gecikmeyi garanti edemez. Çözümünüzü tasarlarken şunları yapmalısınız:

* Herhangi bir IoT Hub işlemi için en fazla gecikme süresi hakkında varsayımlar yapmaktan kaçının.
* IoT Hub 'ınızı cihazlarınıza en yakın Azure bölgesinde sağlayın.
* Cihazda veya cihaza yakın bir ağ geçidinde gecikme süresine duyarlı işlemler gerçekleştirmek için Azure IoT Edge kullanmayı düşünün.

Birden çok IoT Hub birimi, daha önce açıklandığı gibi azaltmayı etkiler, ancak ek gecikme veya garanti vermez.

İşlem gecikmesi sırasında beklenmeyen artışlar görürseniz [Microsoft desteği](https://azure.microsoft.com/support/options/)başvurun.

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub azaltma davranışının derinlemesine bir tartışması için bkz. blog gönderisi [IoT Hub azaltma ve siz](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Bu IoT Hub geliştirici kılavuzundaki diğer başvuru konuları şunları içerir:

* [IoT Hub uç noktaları](iot-hub-devguide-endpoints.md)
