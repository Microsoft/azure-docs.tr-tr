---
title: Çalışma zamanının cihazları nasıl yönettiğini öğrenin-Azure IoT Edge | Microsoft Docs
description: IoT Edge çalışma zamanının, cihazlarınızda modüller, güvenlik, iletişim ve raporlamayı nasıl yönettiğini öğrenin
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 74cfe4ba3c92d8d96dd196ef6f612b9ed7c0da9d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496261"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Azure IoT Edge çalışma zamanını ve mimarisini anlayın

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge çalışma zamanı, bir cihazı bir IoT Edge cihazına dönüştüren program koleksiyonudur. Toplu olarak, IoT Edge çalışma zamanı bileşenleri, IoT Edge cihazların kenarda çalışacak kodu almasını ve sonuçları iletmelerini sağlar.

IoT Edge çalışma zamanı, IoT Edge cihazlarda aşağıdaki işlevlerden sorumludur:

* Cihaza iş yüklerini yükleyip güncelleştirin.

* Cihazda Azure IoT Edge güvenlik standartlarının bakımını yapın.

* [IoT Edge modüllerinin](iot-edge-modules.md) her zaman çalıştığından emin olun.

* Uzaktan izleme için modül durumunu buluta bildirin.

* Aşağı akış cihazları ve IoT Edge cihazları arasındaki iletişimi yönetin.

* IoT Edge cihazındaki modüller arasındaki iletişimi yönetin.

* IoT Edge bir cihaz ve bulut arasındaki iletişimi yönetin.
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* IoT Edge cihazlar arasındaki iletişimi yönetin.
::: moniker-end

![Çalışma zamanı, Öngörüler ve modül sistem durumunu IoT Hub ile iletişim kurar](./media/iot-edge-runtime/Pipeline.png)

IoT Edge çalışma zamanının sorumlulukları iki kategoriye ayrılır: iletişim ve modül yönetimi. Bu iki rol IoT Edge çalışma zamanının parçası olan iki bileşen tarafından gerçekleştirilir. *IoT Edge Aracısı* modülleri dağıtır ve izler, ancak *IoT Edge merkezi* iletişimden sorumludur.

IoT Edge Aracısı ve IoT Edge hub 'ı, tıpkı bir IoT Edge cihazında çalışan diğer modüller gibi modüllerdir. Bazen *çalışma zamanı modülleri* olarak anırlar.

## <a name="iot-edge-agent"></a>IoT Edge Aracısı

IoT Edge Aracısı, Azure IoT Edge çalışma zamanını oluşturan iki modülden biridir. Modüllerin çalıştırılmasından ve modüllerin durumunun IoT Hub 'e geri bildirilmesinden emin olmanın bir sorumlusu vardır. Bu yapılandırma verileri, ikizi IoT Edge Agent modülünün bir özelliği olarak yazılmıştır.

[IoT Edge güvenlik arka plan programı](iot-edge-security-manager.md) , cihaz başlangıcında IoT Edge aracısını başlatır. Aracı, ikizi modülünü IoT Hub alır ve dağıtım bildirimini inceler. Dağıtım bildirimi, başlatılması gereken modülleri bildiren bir JSON dosyasıdır.

Dağıtım bildirimindeki her öğe, bir modülle ilgili belirli bilgileri içerir ve IoT Edge Aracısı tarafından modülün yaşam döngüsünü denetlemek için kullanılır. IoT Edge Aracısı tarafından modülleri denetlemek için kullanılan tüm özellikler hakkında daha fazla bilgi için, [IoT Edge aracısının özellikleri ve IoT Edge merkezi modül TWINS](module-edgeagent-edgehub.md)' i okuyun.

IoT Edge Aracısı IoT Hub çalışma zamanı yanıtı gönderir. Olası yanıtların bir listesi aşağıda verilmiştir:
  
* 200-TAMAM
* 400-dağıtım yapılandırması hatalı biçimlendirilmiş veya geçersiz.
* 417-cihazda bir dağıtım yapılandırma kümesi yok.
* 412-dağıtım yapılandırmasındaki şema sürümü geçersiz.
* 406-IoT Edge cihaz çevrimdışı veya durum raporları göndermiyor.
* 500-IoT Edge çalışma zamanında bir hata oluştu.

Dağıtım bildirimleri oluşturma hakkında daha fazla bilgi için bkz. [IoT Edge modül dağıtmayı ve yolları oluşturmayı öğrenin](module-composition.md).

### <a name="security"></a>Güvenlik

IoT Edge Aracısı bir IoT Edge cihazının güvenliğine kritik bir rol oynar. Örneğin, başlamadan önce bir modülün görüntüsünü doğrulama gibi eylemler gerçekleştirir.

Azure IoT Edge güvenlik çerçevesi hakkında daha fazla bilgi için [IoT Edge Güvenlik Yöneticisi](iot-edge-security-manager.md)hakkında makalesini okuyun.

## <a name="iot-edge-hub"></a>IoT Edge hub 'ı

IoT Edge hub, Azure IoT Edge çalışma zamanını oluşturan diğer modüldür. Aynı protokol uç noktalarını IoT Hub olarak kullanıma sunarak IoT Hub için yerel bir ara sunucu işlevi görür. Bu tutarlılık, istemcilerin IoT Edge çalışma zamanına yalnızca IoT Hub gibi bağlanabildikleri anlamına gelir.

IoT Edge hub 'ı yerel olarak çalışan IoT Hub tam bir sürümü değildir. IoT Edge hub, IoT Hub için bazı görevleri sessizce devreder. Örneğin, IoT Edge hub, bir cihazın bağlanmasını sağlamak üzere ilk bağlantısı üzerindeki IoT Hub yetkilendirme bilgilerini otomatik olarak indirir. İlk bağlantı kurulduktan sonra, yetkilendirme bilgileri IoT Edge hub tarafından yerel olarak önbelleğe alınır. Bu cihazdan gelecek bağlantıların, buluttan yeniden yetkilendirme bilgileri indirmesi gerekmeden yetkilendirilir.

### <a name="cloud-communication"></a>Bulut iletişimi

IoT Edge çözümünüzün kullandığı bant genişliğini azaltmak için, IoT Edge hub 'ı buluta kaç tane gerçek bağlantı yapıldığını en iyi duruma getirir. IoT Edge hub, modüller veya aşağı akış cihazlarından mantıksal bağlantılar alır ve bunları buluta tek bir fiziksel bağlantı için birleştirir. Bu işlemin ayrıntıları çözümün geri kalanına saydamdır. İstemciler, hepsi aynı bağlantı üzerinden gönderilse de, buluta kendilerine ait bağlantıları olduğunu düşündüler. IoT Edge hub 'ı, aşağı akış cihazları tarafından kullanılan protokollerden bağımsız olarak, buluta yukarı akış ile iletişim kurmak için AMQP veya MQTT protokolünü kullanabilir. Ancak, IoT Edge merkezi şu anda yalnızca AMQP 'yi yukarı akış protokolü ve çoğullama özellikleri olarak kullanarak tek bir fiziksel bağlantı halinde birleştirmeyi destekler. AMQP varsayılan yukarı akış protokolüdür.

![IoT Edge hub, fiziksel cihazlar ve IoT Hub arasında bir ağ geçididir](./media/iot-edge-runtime/gateway-communication.png)

IoT Edge hub, IoT Hub bağlanıp bağlanmadığını belirleyebilir. Bağlantı kaybolursa IoT Edge hub iletileri veya ikizi güncelleştirmelerini yerel olarak kaydeder. Bir bağlantı yeniden kurulduktan sonra tüm verileri eşitler. Bu geçici önbellek için kullanılan konum, IoT Edge hub 'ının modül ikizi bir özelliği tarafından belirlenir. Önbelleğin boyutu artmaz ve cihazın depolama kapasitesi olduğu sürece büyüyecektir. Daha fazla bilgi için bkz. [çevrimdışı yetenekler](offline-capabilities.md).

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>Modül iletişimi

IoT Edge hub, modülü modül iletişimine kolaylaştırır. İleti Aracısı olarak IoT Edge hub 'ı kullanmak, modülleri birbirinden bağımsız olarak tutar. Modüller yalnızca iletileri kabul ettikleri girişleri ve ileti yazdıkları çıkışları belirtmeleri gerekir. Bir çözüm geliştiricisi, bu girdileri ve çıkışları birlikte birleştirerek modüllerin bu çözüme özgü sırada verileri işlemesini sağlar.

![IoT Edge hub, modüle modül iletişimini kolaylaştırır](./media/iot-edge-runtime/module-endpoints.png)

IoT Edge hub 'ına veri göndermek için, bir modül SendEventAsync yöntemini çağırır. İlk bağımsız değişken iletinin hangi çıktıda gönderileceğini belirtir. Aşağıdaki sözde kod **output1** üzerine bir ileti gönderir:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Bir ileti almak için, belirli bir girişte gelen iletileri işleyen bir geri çağırma işlemini kaydettirin. Aşağıdaki sözde kod, **input1** üzerinde alınan tüm iletileri işlemek Için kullanılacak MessageProcessor işlevini kaydeder:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Moduleclient sınıfı ve iletişim yöntemleri hakkında daha fazla bilgi için bkz. tercih ettiğiniz SDK dili için API başvurusu: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)veya [Node.js](/javascript/api/azure-iot-device/moduleclient).

Çözüm geliştiricisi, IoT Edge hub 'ının iletileri modüller arasında nasıl geçireceğini belirleyen kuralları belirtmekten sorumludur. Yönlendirme kuralları bulutta tanımlanmıştır ve modülünde IoT Edge hub 'ına dağıtılır ikizi. IoT Hub yollar için aynı söz dizimi, Azure IoT Edge modüller arasındaki yolları tanımlamak için kullanılır. Daha fazla bilgi için bkz. [IoT Edge modül dağıtmayı ve yollar oluşturmayı öğrenin](module-composition.md).

![Modüller arasındaki rotalar IoT Edge hub 'ına gider](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>Yerel iletişim

IoT Edge hub, yerel iletişimi kolaylaştırır. Cihazları ve modülleri birbirinden bağımsız tutmak üzere iletiler sunarak cihazdan modüle, modüle modüle, cihazdan cihaza iletişimler sağlar.

>[!NOTE]
> MQTT Aracısı özelliği, IoT Edge sürüm 1,2 ile genel önizlemede. Açıkça etkin olmalıdır.

IoT Edge hub 'ı iki aracılı mekanizmayı destekler:

1. [IoT Hub ve tarafından desteklenen ileti yönlendirme özellikleri](../iot-hub/iot-hub-devguide-messages-d2c.md)
2. [MQTT standart v 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html) karşılayan genel amaçlı MQTT Aracısı

#### <a name="using-routing"></a>Yönlendirmeyi kullanma

İlk aracı mekanizması, iletilerin cihazlar veya modüller arasında nasıl geçtiğini belirtmek için IoT Hub ile aynı yönlendirme özelliklerinden yararlanır. İlk cihazlar veya modüller iletileri kabul ettikleri girişleri ve ileti yazdıkları çıkışları belirtir. Daha sonra bir çözüm geliştiricisi, iletileri bir kaynak, örneğin çıktılar ve bir hedef (örn. girişler) gibi olası filtrelerle yönlendirebilir.

![Modüller arasındaki rotalar IoT Edge hub 'ına gider](./media/iot-edge-runtime/module-endpoints-routing.png)

Yönlendirme, AMQP veya MQTT protokolü aracılığıyla Azure IoT cihaz SDK 'Ları ile oluşturulan cihazlar veya modüller tarafından kullanılabilir. Tüm mesajlaşma IoT Hub temel elemanlar, örn. telemetri, doğrudan Yöntemler, C2D, TWINS desteklenir, ancak kullanıcı tanımlı konularda iletişim desteklenmez.

Rotalar hakkında daha fazla bilgi için bkz. [IoT Edge modül dağıtmayı ve yolları oluşturmayı öğrenin](module-composition.md)

#### <a name="using-the-mqtt-broker"></a>MQTT aracısını kullanma

İkinci aracılı Aracı, standart bir MQTT aracısına dayalıdır. MQTT, kaynak kısıtlı cihazlarda en iyi performansı garantileyen ve popüler bir yayımlama ve abone olma standardı olan basit bir ileti aktarım protokolüdür. Cihazlar veya modüller, diğer cihazlar veya modüller tarafından yayınlanan iletileri almak için konulara abone olur. IoT Edge hub [, MQTT sürümü 3.1.1 belirtimlerini](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)izleyen kendi MQTT aracısını uygular.

MQTT Aracısı, yönlendirme ile karşılaştırıldığında iki ek iletişim desenini sağlar: yerel yayın ve noktadan noktaya iletişim. Yerel yayın, bir cihaz ya da modülün birden çok diğer cihazı veya modülü yerel olarak uyarması gerektiğinde faydalıdır. Noktadan noktaya iletişim, iki IoT Edge cihazın veya iki IoT aygıtının buluta gidiş dönüş olmadan yerel olarak iletişim kurmasına olanak sağlar.

![IoT Edge hub ile yerel olarak yayımlama ve abone olma](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

MQTT Aracısı, MQTT Protokolü veya genel amaçlı MQTT istemcileri üzerinden iletişim kuran Azure IoT cihaz SDK 'Ları ile oluşturulmuş cihazlar veya modüller tarafından kullanılabilir. C2D tüm mesajlaşma IoT Hub özel durumları, örneğin telemetri, doğrudan Yöntemler, TWINS desteklenir. IoT Hub IoT Hub temel elemanlar tarafından kullanılan özel konular desteklenir ve Kullanıcı tanımlı konulardır.
Bu konu, IoT Hub özel bir konu veya Kullanıcı tanımlı konu olabilir.

Yönlendirme mekanizmasından farklı olarak, ileti sıralaması yalnızca en iyi çaba olur ve ileti filtrelemesi aracı tarafından desteklenmez. Bu özelliklerin olmaması ancak MQTT aracısının yönlendirmeden daha hızlı olmasını sağlar.

MQTT Aracısı hakkında daha fazla bilgi için bkz. [IoT Edge yayımlama ve abone olma](how-to-publish-subscribe.md)

#### <a name="comparison-between-brokering-mechanisms"></a>Aracılı mekanizmalar arasındaki karşılaştırma

Her aracılı mekanizmasıyla kullanılabilen özellikler şunlardır:

|Özellikler  | Yönlendirme  | MQTT Aracısı  |
|---------|---------|---------|
|D2C telemetrisi    |     &#10004;    |         |
|Yerel telemetri     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|İkizi     |    &#10004;     |    &#10004;     |
|Cihazlar için C2D     |   &#10004;      |         |
|Sıralama     |    &#10004;     |         |
|Filtreleme     |     &#10004;    |         |
|Kullanıcı tanımlı konular     |         |    &#10004;     |
|Cihazdan cihaza     |         |    &#10004;     |
|Yerel yayın     |         |    &#10004;     |
|Performans     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>IoT Edge hub 'ına bağlanma

IoT Edge hub, MQTT protokolü ya da AMQP protokolü üzerinden cihaz veya modül istemcilerinden gelen bağlantıları kabul eder.

>[!NOTE]
> IoT Edge hub, MQTT veya AMQP kullanarak bağlanan istemcileri destekler. HTTP kullanan istemcileri desteklemez.

İstemci IoT Edge hub 'ına bağlandığı zaman şunlar olur:

1. Aktarım Katmanı Güvenliği (TLS) kullanılıyorsa (önerilir), istemci ile IoT Edge hub arasında şifrelenmiş bir iletişim kurmak için bir TLS kanalı oluşturulur.
2. Kimlik doğrulama bilgileri istemciden kendisini tanımlamak için IoT Edge hub 'a gönderilir.
3. IoT Edge hub, yetkilendirme ilkesini temel alarak bağlantıyı yetkilendirir veya reddeder.

#### <a name="secure-connections-tls"></a>Güvenli bağlantılar (TLS)

Varsayılan olarak, IoT Edge merkezi yalnızca Aktarım Katmanı Güvenliği (TLS) ile güvenliği sağlanmış bağlantıları kabul eder, örneğin, üçüncü bir tarafın şifresini çözemediği şifreli bağlantılar.

İstemci, IoT Edge hub 'ına 8883 (MQTTS) veya 5671 (AMQPS) bağlantı noktasına bağlanırsa, bir TLS kanalının oluşturulması gerekir. TLS anlaşması sırasında, IoT Edge hub 'ı istemcinin doğrulanması gereken sertifika zincirini gönderir. Sertifika zincirini doğrulamak için, IoT Edge hub 'ının kök sertifikasının, istemcide güvenilir bir sertifika olarak yüklenmesi gerekir. Kök sertifikaya güvenilmiyorsa, istemci kitaplığı IoT Edge hub 'ı tarafından bir sertifika doğrulama hatası ile reddedilir.

Aracının bu kök sertifikasını cihaz istemcilerine yüklemek için izlenecek adımlar, [saydam ağ geçidinde](how-to-create-transparent-gateway.md) ve [bir aşağı akış cihazı hazırlama](how-to-connect-downstream-device.md#prepare-a-downstream-device) belgelerinde açıklanmıştır. Modüller, IoT Edge Daemon API 'sinden yararlanarak IoT Edge hub 'ı ile aynı kök sertifikayı kullanabilir.

#### <a name="authentication"></a>Kimlik Doğrulaması

IoT Edge merkezi yalnızca IoT Hub kimliği olan cihazlardan veya modüllerden (örn. IoT Hub kayıtlı) ve kimlik doğrulaması için IoT Hub tarafından desteklenen üç istemci kimlik doğrulama yönteminden birine sahip olan bağlantıları kabul eder: [simetrik anahtar kimlik doğrulaması](how-to-authenticate-downstream-device.md#symmetric-key-authentication), [x. 509.440 kendinden imzalı kimlik](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)doğrulama, [x. 509.440 CA imzalı kimlik doğrulaması](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).  Bu IoT Hub kimlikleri, IoT Edge hub 'ı tarafından yerel olarak doğrulanabilir, bu nedenle bağlantılar çevrimdışıyken da yapılabilir.

Notlar:

* IoT Edge modüller Şu anda yalnızca simetrik anahtar kimlik doğrulamasını destekliyor.
* Yalnızca Yerel Kullanıcı adı ve parolalara sahip MQTT istemcileri IoT Edge hub MQTT Aracısı tarafından kabul edilmez, IoT Hub kimliklerini kullanmaları gerekir.

#### <a name="authorization"></a>Yetkilendirme

Kimliği doğrulandıktan sonra, IoT Edge hub 'ının istemci bağlantılarını yetkilendirmek için iki yolu vardır:

* Bir istemcinin, IoT Hub tanımlı güvenilen istemciler kümesine ait olduğunu doğrulayarak. Güvenilen istemci kümesi, IoT Hub ' de üst/alt veya cihaz/modül ilişkileri ayarlanarak belirtilir. IoT Edge bir modül oluşturulduğunda, bu modülle IoT Edge cihazı arasında bir güven ilişkisi otomatik olarak oluşturulur. Bu, yönlendirme aracı tarafından desteklenen tek yetkilendirme modelidir.

* Bir yetkilendirme ilkesi ayarlayarak. Bu yetkilendirme ilkesi, IoT Edge hub 'ında kaynaklara erişebilen tüm yetkilendirilmiş istemci kimliklerini listelerken bir belgedir. Bu, IoT Edge hub MQTT Aracısı tarafından kullanılan birincil yetkilendirme modelidir, ancak üst/alt ve cihaz/modül ilişkileri, IoT Hub konuları için MQTT Aracısı tarafından da anlaşılabilirler.

### <a name="remote-configuration"></a>Uzaktan yapılandırma

IoT Edge hub 'ı tamamen bulut tarafından denetlenir. IoT Hub, yapılandırmasını kendi [module ikizi](iot-edge-modules.md#module-twins)aracılığıyla alır. Şunları içerir:

* Rota yapılandırması
* Yetkilendirme ilkeleri
* MQTT köprü yapılandırması

Ayrıca, [IoT Edge hub 'ında ortam değişkenleri](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md)ayarlanarak birçok yapılandırma yapılabilir.
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>Çalışma zamanı kalite telemetrisi

IoT Edge, ürün kalitesini geliştirmek için konak çalışma zamanı ve sistem modülleriyle adsız telemetri toplar. Bu bilgilere çalışma zamanı kalite telemetrisi adı verilir. Toplanan telemetri, IoT Edge aracısından IoT Hub için düzenli olarak cihazdan buluta iletiler olarak gönderilir. Bu iletiler müşterinin normal telemetrisinde görünmez ve herhangi bir ileti kotasını tüketmez.

IoT Edge Aracısı ve hub, cihaz performansını anlamak için toplayabileceğinizi ölçümlerle üretir. Bu ölçümlerin bir alt kümesi, IoT Edge Aracısı tarafından çalışma zamanı kalite telemetrinin bir parçası olarak toplanır. Çalışma zamanı kalite telemetrisi için toplanan ölçümler etiketiyle etiketlidir `ms_telemetry` . Tüm kullanılabilir ölçümler hakkında daha fazla bilgi için bkz. [yerleşik ölçümlere erişme](how-to-access-built-in-metrics.md).

Çalışma zamanı kalite telemetrisine yönelik anonim doğası sağlamak için, cihaz ve modül adları gibi tüm kişisel veya kuruluş tanımlanabilir bilgiler karşıya yüklenmeden önce kaldırılır.

IoT Edge Aracısı her saat telemetri toplar ve her 24 saatte bir IoT Hub bir ileti gönderir.

Cihazlarınızdan çalışma zamanı telemetrisi göndermeyi devre dışı bırakmak istiyorsanız, bunu iki şekilde yapabilirsiniz:

* `SendRuntimeQualityTelemetry` `false` **Edgeagent** için ortam değişkenini olarak ayarlayın veya
* Dağıtım sırasında Azure portal seçeneğinin işaretini kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure IoT Edge modüllerini anlama](iot-edge-modules.md)
* [IoT Edge'de modülleri dağıtmayı ve yolları oluşturmayı öğrenin](module-composition.md)
* [IoT Edge yayımlamayı ve abone olmayı öğrenin](how-to-publish-subscribe.md)
* [IoT Edge çalışma zamanı ölçümleri hakkında bilgi edinin](how-to-access-built-in-metrics.md)
