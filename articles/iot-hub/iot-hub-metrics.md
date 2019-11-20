---
title: Azure IoT Hub izlemek için ölçümleri kullanma | Microsoft Docs
description: IoT Hub 'larınızın genel durumunu değerlendirmek ve izlemek için Azure IoT Hub ölçümlerini kullanma.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: f0bcf12a43a4732b371dd2d64c0b174a0087bea9
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098949"
---
# <a name="understand-iot-hub-metrics"></a>IoT Hub ölçümlerini anlayın

IoT Hub ölçümler, Azure aboneliğinizdeki Azure IoT kaynaklarının durumu hakkında daha iyi veriler sağlar. IoT Hub ölçümler, IoT Hub hizmetinin ve ona bağlı cihazların genel sistem durumunu değerlendirmenizi sağlar. IoT Hub 'ınız ile neler olduğunu görmenizi ve Azure desteği ile iletişim kurmaya gerek kalmadan köke neden olan sorunları belirlemenize yardımcı olduklarından, kullanıcıya yönelik istatistikler önemlidir.

Ölçümler varsayılan olarak etkindir. Azure portal IoT Hub ölçümlerini görüntüleyebilirsiniz.

> [!NOTE]
> IoT Hub bağlı IoT Tak ve Kullan cihazları hakkındaki bilgileri görüntülemek için IoT Hub ölçümlerini kullanabilirsiniz. IoT Tak ve Kullan cihazları [ıot Tak ve Kullan genel önizlemenin](../iot-pnp/overview-iot-plug-and-play.md)bir parçasıdır.

## <a name="how-to-view-iot-hub-metrics"></a>IoT Hub ölçümlerini görüntüleme

1. IoT Hub 'ı oluşturun. [Bir cihazdan Telemetriyi nasıl oluşturacağınız hakkında yönergeler için bir cihazdan IoT Hub](quickstart-send-telemetry-dotnet.md) Kılavuzu ' na bakın.

2. IoT Hub 'ınızın dikey penceresini açın. Buradan **ölçümler**' e tıklayın.
   
    ![Ölçüm seçeneğinin IoT Hub kaynak sayfasında olduğunu gösteren ekran görüntüsü](./media/iot-hub-metrics/enable-metrics-1.png)

3. Ölçümler dikey penceresinde IoT Hub 'ınız için ölçümleri görüntüleyebilir ve ölçümlerinizin özel görünümlerini oluşturabilirsiniz. 
   
    ![IoT Hub için ölçüm sayfasını gösteren ekran görüntüsü](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. **Tanılama ayarları**' na ve ardından **Tanılama ayarı Ekle** ' ye tıklayarak ölçüm verilerinizi bir Event Hubs uç noktasına veya bir Azure depolama hesabına göndermenizi seçebilirsiniz

   ![Tanılama Ayarları düğmesinin nerede olduğunu gösteren ekran görüntüsü](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub ölçümleri ve bunların nasıl kullanılacağı

IoT Hub, hub 'ınızın sistem durumuna ve bağlı cihazların toplam sayısına ilişkin bir genel bakış sunmak için çeşitli ölçümler sunar. IoT Hub 'ın durumunun büyük bir resmini boyamak için birden fazla ölçüden bilgi birleştirebilirsiniz. Aşağıdaki tabloda, her bir IoT Hub 'ının izlediği ölçümler ve her bir ölçümün IoT Hub 'ının genel durumuyla nasıl ilişkili olduğu açıklanır.

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|D2C<br>. telemetri<br>giriş.<br>allProtocol|Telemetri iletisi gönderme denemeleri|Count|Toplam|IoT Hub 'ınıza gönderilmeye çalışılan cihazdan buluta telemetri iletisi sayısı|Boyut yok|
|D2C<br>. telemetri<br>. giriş<br>. başarılı|Gönderilen telemetri iletileri|Count|Toplam|IoT Hub 'ınıza başarıyla gönderilen cihazdan buluta telemetri iletisi sayısı|Boyut yok|
|C2D<br>. Commands<br>. çıkış<br>. Tamam<br>. başarılı|Komutlar tamamlandı|Count|Toplam|Cihaz tarafından başarıyla tamamlanan buluttan cihaza yönelik komutların sayısı|Boyut yok|
|C2D<br>. Commands<br>. çıkış<br>. Abandon<br>. başarılı|Komutlar bırakıldı|Count|Toplam|Cihaz tarafından bırakılan buluttan cihaza yönelik komutların sayısı|Boyut yok|
|C2D<br>. Commands<br>. çıkış<br>. reddetme<br>. başarılı|Reddedilen komutlar|Count|Toplam|Cihaz tarafından reddedilen buluttan cihaza yönelik komutların sayısı|Boyut yok|
|cihazlar<br>. totalDevices|Toplam cihaz (kullanım dışı)|Count|Toplam|IoT Hub 'ınıza kayıtlı cihazların sayısı|Boyut yok|
|cihazlar<br>. connectedDevices<br>. allProtocol|Bağlı cihazlar (kullanım dışı) |Count|Toplam|IoT Hub 'ınıza bağlı cihazların sayısı|Boyut yok|
|D2C<br>. telemetri<br>. çıkış<br>. başarılı|Yönlendirme: teslim edilen telemetri iletileri|Count|Toplam|IoT Hub yönlendirme kullanılarak iletilerin tüm uç noktalara başarıyla ulaştırılan ileti sayısı. Bir ileti birden çok uç noktaya yönlendirilmemişse, her başarılı teslimat için bu değer bir artar. Bir ileti aynı uç noktaya birden çok kez teslim edildiğinde, her başarılı teslimat için bu değer bir artar.|Boyut yok|
|D2C<br>. telemetri<br>. çıkış<br>. bırakıldı|Yönlendirme: telemetri iletileri bırakıldı |Count|Toplam|İleti, ölü uç noktalar nedeniyle IoT Hub yönlendirmenin kaç kez bırakılmakta olduğunu. Bu değer, bırakılan iletiler buraya teslim edimediğinden geri dönüş rotasına teslim edilen iletileri saymaz.|Boyut yok|
|D2C<br>. telemetri<br>. çıkış<br>. yalnız bırakılmış|Yönlendirme: telemetri iletileri yalnız bırakılmış |Count|Toplam|Herhangi bir yönlendirme kuralıyla eşleşmediğinden (geri dönüş kuralı dahil) iletilerin IoT Hub yönlendirme tarafından yalnız bırakılmış olduğu zaman sayısı. |Boyut yok|
|D2C<br>. telemetri<br>. çıkış<br>. geçersiz|Yönlendirme: telemetri iletileri uyumsuz|Count|Toplam|Uç nokta ile uyumsuzluk nedeniyle IoT Hub yönlendirmenin iletileri teslim etme başarısız olduğu zaman sayısı. Bu değer yeniden denemeler içermez.|Boyut yok|
|D2C<br>. telemetri<br>. çıkış<br>. Fallback|Yönlendirme: geri dönüşe teslim edilen iletiler|Count|Toplam|Geri dönüş rotası ile ilişkili uç noktaya gönderilen iletileri yönlendirmenin IoT Hub sayısı.|Boyut yok|
|D2C<br>. uç noktalar<br>. çıkış<br>. eventHubs|Yönlendirme: Olay Hub 'ına teslim edilen iletiler|Count|Toplam|IoT Hub yönlendirmenin Olay Hub 'ı uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Boyut yok|
|D2C<br>. uç noktalar<br>. gecikme süresi<br>. eventHubs|Yönlendirme: Olay Hub 'ı için ileti gecikmesi|Milisaniye|Average|İleti girişi ile bir olay hub 'ı uç noktasına giriş IoT Hub ve ileti girişi arasındaki ortalama gecikme (milisaniye).|Boyut yok|
|D2C<br>. uç noktalar<br>. çıkış<br>. serviceBusQueues|Yönlendirme: Service Bus kuyruğuna teslim edilen iletiler|Count|Toplam|IoT Hub yönlendirmenin iletileri Service Bus sıra uç noktalarına başarıyla teslim dağıttığı zaman sayısı.|Boyut yok|
|D2C<br>. uç noktalar<br>. gecikme süresi<br>. serviceBusQueues|Yönlendirme: Service Bus kuyruğu için ileti gecikmesi|Milisaniye|Average|İleti girişi IoT Hub ve telemetri iletisi arasındaki ortalama gecikme süresi (milisaniye) Service Bus kuyruğu uç noktasına giriş.|Boyut yok|
|D2C<br>. uç noktalar<br>. çıkış<br>. Servicebuskonulardaki konuları|Yönlendirme: Service Bus konuya teslim edilen iletiler|Count|Toplam|IoT Hub yönlendirmenin Service Bus konu uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Boyut yok|
|D2C<br>. uç noktalar<br>. gecikme süresi<br>. Servicebuskonulardaki konuları|Yönlendirme: Service Bus konusu için ileti gecikmesi|Milisaniye|Average|İleti girişi IoT Hub ve telemetri iletisi arasındaki ortalama gecikme süresi (milisaniye) Service Bus konu uç noktasına giriş.|Boyut yok|
|D2C<br>. uç noktalar<br>. çıkış<br>. builtIn<br>. olaylar|Yönlendirme: iletilere/olaylara teslim edilen iletiler|Count|Toplam|Yönlendirmenin, yerleşik uç noktaya (iletiler/olaylar) başarıyla ileti teslim IoT Hub sayısı. Bu ölçüm yalnızca Yönlendirme etkinken çalışmaya başlar (https://aka.ms/iotrouting) IoT Hub 'ı için).|Boyut yok|
|D2C<br>. uç noktalar<br>. gecikme süresi<br>. builtIn. Events|Yönlendirme: iletiler/olaylar için ileti gecikmesi|Milisaniye|Average|Yerleşik uç noktaya (iletiler/olaylar) gelen ileti girişi IoT Hub ve telemetri iletisi arasındaki ortalama gecikme süresi (milisaniye). Bu ölçüm yalnızca Yönlendirme etkinken çalışmaya başlar (https://aka.ms/iotrouting) IoT Hub 'ı için).|Boyut yok|
|D2C<br>. uç noktalar<br>. çıkış<br>. Storage|Yönlendirme: depolamaya teslim edilen iletiler|Count|Toplam|IoT Hub yönlendirmenin depolama uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Boyut yok|
|D2C<br>. uç noktalar<br>. gecikme süresi<br>. Storage|Yönlendirme: depolama için ileti gecikmesi|Milisaniye|Average|İleti girişi IoT Hub ve telemetri iletisine bir depolama uç noktasına giriş arasındaki ortalama gecikme süresi (milisaniye).|Boyut yok|
|D2C<br>. uç noktalar<br>. çıkış<br>. Storage<br>. bayt|Yönlendirme: depolamaya teslim edilen veriler|Bayt|Toplam|Depolama uç noktalarına teslim edilen veri miktarı (bayt) IoT Hub.|Boyut yok|
|D2C<br>. uç noktalar<br>. çıkış<br>. Storage<br>. blob 'lar|Yönlendirme: depolamaya teslim edilen Bloblar|Count|Toplam|IoT Hub blob 'ları depolama uç noktalarına yönlendirmenin kaç kez yönlendirtığı sayısıdır.|Boyut yok|
|Eventgridteslimler|Event Grid teslimler (Önizleme)|Count|Toplam|Event Grid yayımlanan IoT Hub olaylarının sayısı. Başarılı ve başarısız isteklerin sayısı için sonuç boyutunu kullanın. EventType boyutu olay türünü gösterir (https://aka.ms/ioteventgrid). İsteklerin nereden geldiği hakkında bilgi için EventType boyutunu kullanın.|Sonuç, EventType|
|EventGridLatency|Event Grid gecikme süresi (Önizleme)|Milisaniye|Average|Olay Event Grid yayımlandığında IoT Hub olayının oluşturulduğu sürenin ortalama gecikme süresi (milisaniye). Bu sayı, tüm olay türleri arasındaki bir ortalama değer. Belirli bir olay türünün gecikmesini görmek için EventType boyutunu kullanın.|EventType|
|D2C<br>. ikizi<br>. okuma<br>. başarılı|Cihazlardan başarılı ikizi okumaları|Count|Toplam|Tüm başarılı cihaz tarafından başlatılan ikizi okuma sayısı.|Boyut yok|
|D2C<br>. ikizi<br>. okuma<br>. hata|Cihazlardan başarısız ikizi okumaları|Count|Toplam|Tüm başarısız cihaz tarafından başlatılan ikizi okumaların sayısı.|Boyut yok|
|D2C<br>. ikizi<br>. okuma<br>. size|Cihazlardan gelen ikizi okumaların yanıt boyutu|Bayt|Average|Tüm başarılı cihaz tarafından başlatılan ikizi okumalarının ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|D2C<br>. ikizi<br>. güncelleştirme<br>. başarılı|Cihazlardan başarılı ikizi güncelleştirmeleri|Count|Toplam|Tüm başarılı cihaz tarafından başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|D2C<br>. ikizi<br>. güncelleştirme<br>. hata|Cihazlardan ikizi güncelleştirmeleri başarısız oldu|Count|Toplam|Tüm başarısız cihaz tarafından başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|D2C<br>. ikizi<br>. güncelleştirme<br>. size|Cihazlardan ikizi güncelleştirmelerinin boyutu|Bayt|Average|Tüm başarılı cihaz tarafından başlatılan ikizi güncelleştirmelerinin ortalama, en düşük ve en büyük boyutu.|Boyut yok|
|C2D<br>. Yöntemler<br>. başarılı|Başarılı doğrudan yöntem etkinleştirmeleri|Count|Toplam|Tüm başarılı doğrudan yöntem çağrılarının sayısı.|Boyut yok|
|C2D<br>. Yöntemler<br>. hata|Başarısız doğrudan yöntem etkinleştirmeleri|Count|Toplam|Tüm başarısız doğrudan yöntem çağrılarının sayısı.|Boyut yok|
|C2D<br>. Yöntemler<br>. requestSize|Doğrudan yöntem etkinleştirmeleri istek boyutu|Bayt|Average|Tüm başarılı doğrudan yöntem isteklerinin ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|C2D<br>. Yöntemler<br>. responseSize|Doğrudan yöntem etkinleştirmeleri yanıt boyutu|Bayt|Average|Tüm başarılı doğrudan yöntem yanıtlarının ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|C2D<br>. ikizi<br>. okuma<br>. başarılı|Arka uçtan başarılı ikizi okumaları|Count|Toplam|Tüm başarılı arka uç tarafından başlatılan ikizi okumaların sayısı. Bu sayı, ikizi sorgularından başlatılan ikizi okumaları içermez.|Boyut yok|
|C2D<br>. ikizi<br>. okuma<br>. hata|Arka uçtan başarısız ikizi okumaları|Count|Toplam|Tüm başarısız arka uç tarafından başlatılan ikizi okumaların sayısı.|Boyut yok|
|C2D<br>. ikizi<br>. okuma<br>. size|Arka uçtan gelen ikizi okumaların yanıt boyutu|Bayt|Average|Tüm başarılı arka uç başlatılan ikizi okumalarının ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|C2D<br>. ikizi<br>. güncelleştirme<br>. başarılı|Arka uçtan başarılı ikizi güncelleştirmeleri|Count|Toplam|Tüm başarılı arka uç ile başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|C2D<br>. ikizi<br>. güncelleştirme<br>. hata|Arka uçtan başarısız ikizi güncelleştirmeleri|Count|Toplam|Tüm başarısız arka uç başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|C2D<br>. ikizi<br>. güncelleştirme<br>. size|Arka uçtan ikizi güncelleştirmelerinin boyutu|Bayt|Average|Tüm başarılı arka uç ile başlatılan ikizi güncelleştirmelerinin ortalama, en düşük ve en büyük boyutu.|Boyut yok|
|twinQueries<br>. başarılı|Başarılı ikizi sorguları|Count|Toplam|Tüm başarılı ikizi sorgularının sayısı.|Boyut yok|
|twinQueries<br>. hata|Başarısız ikizi sorguları|Count|Toplam|Tüm başarısız ikizi sorgularının sayısı.|Boyut yok|
|twinQueries<br>. resultSize|İkizi sorguları sonuç boyutu|Bayt|Average|Tüm başarılı ikizi sorgularının sonuç boyutunun ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|işler<br>.createTwinUpdateJob<br>. başarılı|İkizi Update işlerinin başarılı oluşturmaları|Count|Toplam|Tüm başarılı ikizi Update işlerinin oluşturma sayısı.|Boyut yok|
|işler<br>.createTwinUpdateJob<br>. hata|İkizi Update işlerinin başarısız oluşturmaları|Count|Toplam|İkizi Update işlerinin başarısız olan tüm oluşturma sayısı.|Boyut yok|
|işler<br>. createDirectMethodJob<br>. başarılı|Yöntem çağırma işlerinin başarılı oluşturmaları|Count|Toplam|Tüm başarılı doğrudan yöntem çağırma işlerinin oluşturulma sayısı.|Boyut yok|
|işler<br>. createDirectMethodJob<br>. hata|Yöntem çağırma işlerinin oluşturma işlemi başarısız oldu|Count|Toplam|Doğrudan yöntem çağırma işlerinin tüm başarısız oluşturma sayısı.|Boyut yok|
|işler<br>. listJobs<br>. başarılı|İşleri listelemek için başarılı çağrılar|Count|Toplam|İşleri listelemek için başarılı olan tüm çağrıların sayısı.|Boyut yok|
|işler<br>. listJobs<br>. hata|İşleri listelemek için başarısız çağrılar|Count|Toplam|Listeleme işleri için başarısız olan tüm çağrıların sayısı.|Boyut yok|
|işler<br>. cancelJob<br>. başarılı|Başarılı iş iptalleri|Count|Toplam|İşi iptal etmek için tüm başarılı çağrı sayısı.|Boyut yok|
|işler<br>. cancelJob<br>. hata|Başarısız iş iptalleri|Count|Toplam|İşi iptal etmek için başarısız olan tüm çağrıların sayısı.|Boyut yok|
|işler<br>. queryJobs<br>. başarılı|Başarılı iş sorguları|Count|Toplam|Sorgu işlerine yapılan tüm başarılı çağrıların sayısı.|Boyut yok|
|işler<br>. queryJobs<br>. hata|Başarısız iş sorguları|Count|Toplam|Sorgu işlerine yapılan tüm başarısız çağrıların sayısı.|Boyut yok|
|işler<br>. tamamlandı|Tamamlanan işler|Count|Toplam|Tamamlanan tüm işlerin sayısı.|Boyut yok|
|işler<br>. başarısız|Başarısız işler|Count|Toplam|Başarısız olan tüm işlerin sayısı.|Boyut yok|
|D2C<br>. telemetri<br>. giriş<br>. Sendkısıtlaması|Daraltma hatası sayısı|Count|Toplam|Cihaz performansı azaltmasından kaynaklanan azaltma hatası sayısı|Boyut yok|
|Günlükiletisi<br>Kullanılan Quota|Kullanılan toplam ileti sayısı|Count|Average|Bugün kullanılan toplam ileti sayısı. Bu, her gün 00:00 UTC tarihinde sıfıra sıfırlanan toplu bir değerdir.|Boyut yok|
|deviceDataUsage|Toplam cihaz verisi kullanımı|Bayt|Toplam|Iothub 'e bağlı tüm cihazlardan ve bunlardan aktarılan baytlar|Boyut yok|
|totalDeviceCount|Toplam cihaz (Önizleme)|Count|Average|IoT Hub 'ınıza kayıtlı cihazların sayısı|Boyut yok|
|Yapıldı<br>DeviceCount|Bağlı cihazlar (Önizleme)|Count|Average|IoT Hub 'ınıza bağlı cihazların sayısı|Boyut yok|
|konfigürasyonları|Yapılandırma ölçümleri|Count|Toplam|Yapılandırma Işlemleri için ölçümler|Boyut yok|

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub ölçümlerine genel bir bakış gördüğünüze göre, Azure IoT Hub yönetimi hakkında daha fazla bilgi edinmek için bu bağlantıyı izleyin:

* [İşlemleri izleme](iot-hub-operations-monitoring.md)

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)

* [Azure IoT Edge ile uç cihazlara AI dağıtma](../iot-edge/tutorial-simulate-device-linux.md)
