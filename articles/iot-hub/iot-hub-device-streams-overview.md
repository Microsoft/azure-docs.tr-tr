---
title: Azure IoT Hub cihaz akışları | Microsoft Docs
description: Çok sayıda buluttan cihaza iletişim senaryosu için güvenli çift yönlü TCP tünellerini kolaylaştıran Azure IoT Hub cihaz akışlarına genel bakış.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.openlocfilehash: ff738e56226f7cbb720a754573a9d8607e0e3247
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890465"
---
# <a name="iot-hub-device-streams-preview"></a>Cihaz akışlarını IoT Hub (Önizleme)

Azure IoT Hub *cihaz akışları* , çok sayıda buluttan cihaza iletişim senaryosu için güvenli ıkı yönlü TCP tünellerinin oluşturulmasını kolaylaştırır. Cihaz akışı, cihazınız ve hizmet uç noktalarınız arasında proxy görevi gören bir IoT Hub *akış uç noktası* tarafından dağıtılır. Aşağıdaki diyagramda gösterilen bu kurulum özellikle, cihazlar bir ağ güvenlik duvarının arkasındaysa veya bir özel ağın içinde olduğunda faydalıdır. Bu nedenle, IoT Hub cihaz akışları müşterilerin IoT cihazlarına bir güvenlik duvarı kullanımı kolay bir şekilde ulaşmasını ve gelen veya giden ağ güvenlik duvarı bağlantı noktalarını genel olarak açmaya gerek kalmadan yardımcı olur.

!["Cihaz akışlarına genel bakış" IoT Hub](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Cihaz akışlarını IoT Hub kullanarak, cihazlar güvenli kalır ve yalnızca IoT Hub 'ın 443 numaralı bağlantı noktası üzerinden akış uç noktasına giden TCP bağlantılarını açması gerekecektir. Bir akış kurulduktan sonra, hizmet tarafı ve cihaz tarafı uygulamaların her biri, bir WebSocket istemci nesnesine bir diğerine ham bayt gönderme ve alma için programlı erişim sağlar. Bu tünelle sunulan güvenilirlik ve sipariş garantisi TCP ile aynıdır.

## <a name="benefits"></a>Avantajlar

IoT Hub cihaz akışları aşağıdaki avantajları sağlar:

* **Güvenlik duvarı kolay güvenli bağlantı:** Cihaz veya ağ duvarlar gelen güvenlik duvarı bağlantı noktası açılmadan, IoT cihazlarına hizmet uç noktalarından ulaşılabilir (443 numaralı bağlantı noktası üzerinden yalnızca IoT Hub giden bağlantı gerekir).

* **Kimlik doğrulama:** Tünelin hem cihaz hem de hizmet tarafları, ilgili kimlik bilgilerini kullanarak IoT Hub kimlik doğrulaması yapması gerekir.

* **Şifreleme:** Varsayılan olarak, IoT Hub cihaz akışları TLS özellikli bağlantıları kullanır. Bu, uygulamanın şifreleme kullanıp kullanmadığına bakılmaksızın trafiğin her zaman şifrelenmesini sağlar.

* **Bağlantı basitliği:** Birçok durumda, cihaz akışlarının kullanımı, IoT cihazlarına bağlantı sağlamak için sanal özel ağların karmaşık kurulumunun gereksinimini ortadan kaldırır.

* **TCP/IP yığını Ile uyumluluk:** IoT Hub cihaz akışları TCP/IP uygulama trafiğine uyum sağlayabilir. Bu, standart tabanlı protokollerin çok çeşitli mülkiyet ve bu özellikten yararlanabilen anlamına gelir.

* **Özel ağ kurulumlarına kullanım kolaylığı:** Hizmet, cihazın IP adresi yerine cihaz KIMLIĞINE başvurarak bir cihazla iletişim kurabilir. Bu, bir cihazın özel bir ağda bulunduğu ve özel bir IP adresine sahip olduğu ve IP adresinin dinamik olarak atandığı ve hizmet tarafında bilinmediği durumlarda faydalıdır.

## <a name="device-stream-workflows"></a>Cihaz akışı iş akışları

Hizmet, cihaz KIMLIĞI sağlayarak bir cihaza bağlanmak istediğinde bir cihaz akışı başlatılır. Bu iş akışı özellikle SSH ve RDP dahil olmak üzere bir istemci/sunucu iletişim modeline uyum sağlar. Bu, kullanıcının bir SSH veya RDP istemci programı kullanarak cihazda çalışan SSH veya RDP sunucusuna uzaktan bağlanmasını amaçladığı bir istemci/sunucu iletişim modeline uyar.

Cihaz akışı oluşturma işlemi, cihaz, hizmet, IoT Hub 'ın ana ve akış uç noktaları arasında bir anlaşmasını içerir. IoT Hub 'ının ana uç noktası bir cihaz akışının oluşturulmasını düzenler, akış uç noktası hizmet ve cihaz arasında akan trafiği işler.

### <a name="device-stream-creation-flow"></a>Cihaz akışı oluşturma akışı

SDK kullanarak bir cihaz akışının programlı bir şekilde oluşturulması, aşağıdaki şekilde de gösterildiği gibi aşağıdaki adımları içerir:

!["Cihaz akışı el sıkışma işlemi"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. Cihaz uygulaması, cihaza yeni bir cihaz akışı başlatıldığında bildirim almak için önceden bir geri çağırma kaydeder. Bu adım genellikle cihaz önyüklendiğinde ve IoT Hub bağlandığı zaman gerçekleşir.

2. Hizmet tarafı programı, cihaz KIMLIĞINI (IP adresi_değil_ ) sağlayarak gerektiğinde bir cihaz akışı başlatır.

3. IoT Hub, 1. adımda kaydedilen geri aramayı çağırarak cihaz tarafı programını bilgilendirir. Cihaz, akış başlatma isteğini kabul edebilir veya reddedebilir. Bu mantık, Uygulama senaryonuz için özel olabilir. Akış isteği cihaz tarafından reddedilirse, IoT Hub hizmete uygun şekilde bildirir; Aksi takdirde, aşağıdaki adımlar izleyin.

4. Cihaz, bağlantı noktası 443 üzerinden akış uç noktasına güvenli bir giden TCP bağlantısı oluşturur ve bir WebSocket bağlantısını yükseltir. Kimlik doğrulaması için kullanılacak kimlik bilgilerinin yanı sıra akış uç noktasının URL 'SI, adım 3 ' te gönderilen isteğin bir parçası olarak IoT Hub tarafından, her ikisi de cihaza sağlanır.

5. Hizmet, akışı kabul eden cihazın ve akış uç noktasına kendi WebSocket istemcisini oluşturmaya devam eden bir sonuç olduğunu bilgilendirilir. Benzer şekilde, akış uç noktası URL 'sini ve IoT Hub kimlik doğrulama bilgilerini alır.

Yukarıdaki el sıkışma sürecinde:

* El sıkışma işlemi 60 saniye içinde tamamlanmalıdır (2 ila 5. adım), aksi takdirde el sıkışma zaman aşımı ile başarısız olur ve hizmet buna uygun şekilde bildirilir.

* Yukarıdaki akış oluşturma akışı tamamlandıktan sonra, akış uç noktası bir ara sunucu görevi görür ve hizmet ile cihaz arasındaki trafiği ilgili WebSockets üzerinden aktarır.

* Hem cihaz hem de hizmetin ana uç noktasına giden IoT Hub bağlantı ve 443 numaralı bağlantı noktası üzerinden akış uç noktası olması gerekir. Bu uç noktaların URL 'SI IoT Hub portalındaki *genel bakış* sekmesinde kullanılabilir.

* Belirlenen akışın güvenilirlik ve sıralama garantisi TCP ile aynıdır.

* IoT Hub ve akış uç noktasına yapılan tüm bağlantılar TLS kullanır ve şifrelenir.

### <a name="termination-flow"></a>Sonlandırma akışı

Oluşturulan akış, ağ geçidine yönelik TCP bağlantılarının bağlantısı kesildiğinde (hizmet veya cihaz tarafından) sona erer. Bu, bir ağ bağlantısı zaman aşımı ya da işlem arızası durumunda WebSocket 'in cihaz veya hizmet programlarında veya involuntarily karşı bir süre içinde kapatılması yoluyla gönüllü olarak gerçekleşebilir. Cihazın veya hizmetin akış uç noktasına bağlantısı sonlandırdıktan sonra, diğer TCP bağlantısı da (zorla) sonlandırılır ve hizmet ve cihaz gerektiğinde akışı yeniden oluşturmaktan sorumludur.

## <a name="connectivity-requirements"></a>Bağlantı gereksinimleri

Bir cihaz akışının cihaz ve hizmet tarafları, IoT Hub ve akış uç noktası için TLS özellikli bağlantı kurma yeteneğine sahip olmalıdır. Bu uç noktalara 443 numaralı bağlantı noktası üzerinden giden bağlantı gerekir. Bu uç noktalarla ilişkili ana bilgisayar adı, aşağıdaki şekilde gösterildiği gibi IoT Hub *genel bakış* sekmesinde bulunabilir:

!["Cihaz akış uç noktaları"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Alternatif olarak, uç nokta bilgileri, hub 'ın Özellikler bölümü, özellikle de `property.hostname` ve `property.deviceStreams` anahtarları altında Azure CLı kullanılarak alınabilir.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

Çıktı, hub 'ın cihazının ve hizmetinin bir cihaz akışı oluşturmak için bağlanması gerekebilecek tüm uç noktaların JSON nesnesidir.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Azure CLı sürüm 2.0.57 veya daha yeni bir sürümü yüklediğinizden emin olun. [Azure CLI 'Yı yükle](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) sayfasından en son sürümü indirebilirsiniz.
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Cihaz akış uç noktalarına giden bağlantılara izin ver

Bu makalenin başlangıcında belirtildiği gibi, cihazınız cihaz akışları başlatma işlemi sırasında akış uç noktası IoT Hub giden bir bağlantı oluşturur. Cihazdaki veya ağındaki güvenlik duvarlarınız, bağlantı noktası 443 üzerinden akış ağ geçidine giden bağlantıya izin vermelidir (iletişim, TLS kullanılarak şifrelenmiş bir WebSocket bağlantısı üzerinden yapılır).

Cihaz akış uç noktasının ana bilgisayar adı, genel bakış sekmesinin altındaki Azure IoT Hub portalında bulunabilir. "cihaz akış uç noktaları" ![](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Alternatif olarak, Azure CLı kullanarak bu bilgileri bulabilirsiniz:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Azure CLı sürüm 2.0.57 veya daha yeni bir sürümü yüklediğinizden emin olun. [Azure CLI 'Yı yükle](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) sayfasından en son sürümü indirebilirsiniz.
>

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Cihaz akışları etkinlik günlükleri aracılığıyla sorun giderme

Azure Izleyici günlüklerini, IoT Hub cihaz akışlarının etkinlik günlüğünü toplayacak şekilde ayarlayabilirsiniz. Sorun giderme senaryolarında bu çok faydalı olabilir.

IoT Hub cihaz akışı etkinlikleri için Azure Izleyici günlüklerini yapılandırmak üzere aşağıdaki adımları izleyin:

1. IoT Hub *Tanılama ayarları* sekmesine gidin ve *Tanılama bağlantısını aç* ' a tıklayın.

   !["Tanılama günlüklerini etkinleştirme"](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings-pane.png)

2. Tanılama ayarlarınıza bir ad verin ve *Log Analytics gönder* seçeneğini belirleyin. Mevcut bir Log Analytics çalışma alanı kaynağı seçmek veya yeni bir tane oluşturmak için size kılavuzluk edecek. Ayrıca, listeden *Devicestreams* ' ı kontrol edin.

    !["Cihaz akışları günlüklerini etkinleştir"](./media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png)

3. Artık, IoT Hub portalınızdaki *Günlükler* sekmesinden cihaz akışları günlüklerine erişebilirsiniz. Cihaz akışı etkinlik günlükleri `AzureDiagnostics` tablosunda görünür ve `Category=DeviceStreams`olur.

   Aşağıda gösterildiği gibi, hedef cihazın kimliği ve işlemin sonucu da günlüklerde bulunur.

   !["Cihaz akış günlüklerine erişin"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

## <a name="regional-availability"></a>Bölgesel Kullanılabilirlik

Genel Önizleme sırasında IoT Hub cihaz akışları Orta ABD, Orta ABD EUAP, Kuzey Avrupa ve Güneydoğu Asya bölgelerinde kullanılabilir. Lütfen hub 'ınızı bu bölgelerden birinde oluşturduğunuzdan emin olun.

## <a name="sdk-availability"></a>SDK kullanılabilirliği

Her akışın iki tarafı (cihaz ve hizmet tarafında), tüneli kurmak için IoT Hub SDK 'sını kullanın. Genel Önizleme sırasında, müşteriler aşağıdaki SDK dillerinden seçim yapabilir:

* C ve C# SDK 'nın destek cihazı cihaz tarafında akışları.

* NodeJS ve C# SDK, hizmet tarafında cihaz akışlarını destekler.

## <a name="iot-hub-device-stream-samples"></a>IoT Hub cihaz akışı örnekleri

IoT Hub sayfasında iki [hızlı başlangıç örneği](/azure/iot-hub) bulunur. Bunlar, cihaz akışlarının uygulamalar tarafından kullanımını gösterir.

* *Echo* örnek, cihaz akışlarının programlı kullanımını gösterır (SDK API 'si doğrudan çağırarak).

* *Yerel proxy* örneği, cihaz akışları aracılığıyla raf dışı istemci/sunucu uygulama TRAFIĞININ (SSH, RDP veya Web gibi) tünelini gösterir.

Bu örnekler aşağıda daha ayrıntılı bir şekilde ele alınmıştır.

### <a name="echo-sample"></a>Yankı örneği

Echo örnek, hizmet ve cihaz uygulamaları arasında bayt göndermek ve almak için cihaz akışlarının programlı kullanımını gösterir. Hizmet ve cihaz programlarını farklı dillerde kullanabileceğinizi unutmayın. Örneğin, C# servis programı ile C cihaz programını kullanabilirsiniz.

Yankı örnekleri aşağıda verilmiştir:

* [C#hizmet ve hizmet programı](quickstart-device-streams-echo-csharp.md)

* [Node. js hizmet programı](quickstart-device-streams-echo-nodejs.md)

* [C cihaz programı](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Yerel proxy örneği (SSH veya RDP için)

Yerel proxy örneği, var olan bir uygulamanın, bir istemci ile sunucu programı arasındaki iletişimi içeren trafiğinin tünellenmesini olanaklı hale getirmenin bir yolunu gösterir. Bu kurulum, hizmet tarafının bir istemci olarak (SSH veya RDP istemci programları çalıştıran) ve cihaz tarafında sunucu gibi davranan (ssh daemon veya RDP sunucu programlarını çalıştıran) SSH ve RDP gibi istemci/sunucu protokolleri için geçerlidir.

Bu bölümde, kullanıcının cihaz akışları üzerinden bir cihaza SSH kullanmasını sağlamak için cihaz akışlarının kullanımı açıklanmaktadır (RDP veya diğer istemci/sunucu uygulaması için protokolün karşılık gelen bağlantı noktası kullanılarak benzerdir).

Kurulum, aşağıdaki şekilde gösterilen iki *yerel proxy* programını kullanır; bu şekilde *cihaz yerel proxy* ve *hizmet yerel ara sunucu*. Yerel proxy programları, IoT Hub ile [cihaz akışı başlatma el sıkışması](#device-stream-creation-flow) gerçekleştirmekten ve normal istemci/sunucu YUVALARıNı kullanarak SSH ISTEMCISI ve ssh daemon ile etkileşimde bulunmaktan sorumludur.

!["SSH/RDP için cihaz akışı proxy kurulumu"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. Kullanıcı, cihaza bir cihaz akışı başlatmak için hizmet yerel ara sunucusunu çalıştırır.

2. Cihaz yerel proxy, akış başlatma isteğini kabul eder ve tünel IoT Hub akış uç noktası (yukarıda anlatıldığı gibi) için oluşturulur.

3. Cihaz yerel proxy, cihazda 22 numaralı bağlantı noktasında dinleme yapan SSH Daemon uç noktasına bağlanır.

4. Hizmet yerel proxy, kullanıcıdan yeni SSH bağlantıları bekleyen belirlenmiş bir bağlantı noktasını dinler (örnekte kullanılan bağlantı noktası 2222), ancak bu, kullanılabilir başka bir bağlantı noktasına yapılandırılabilir. Kullanıcı, SSH istemcisini localhost üzerinde hizmet yerel ara sunucu bağlantı noktasına yönlendirir.

### <a name="notes"></a>Notlar

* Yukarıdaki adımlarda SSH istemcisi (sağdaki) ile SSH Daemon (solda) arasında uçtan uca bir tünel tamamlanmıştır. Bu uçtan uca bağlantının bir parçası, IoT Hub için bir cihaz akışı üzerinden trafik gönderilmesini içerir.

* Şekildeki oklar, uç noktalar arasında bağlantıların kurulduğu yönü belirtir. Özellikle, cihaza giden bir gelen bağlantı olmadığını unutmayın (Bu genellikle bir güvenlik duvarı tarafından engellenir).

* Hizmet yerel proxy üzerinde 2222 bağlantı noktasını kullanma seçeneği, rastgele bir seçimdir. Ara sunucu, kullanılabilir başka herhangi bir bağlantı noktasını kullanacak şekilde yapılandırılabilir.

* 22 numaralı bağlantı noktası seçeneği, bu durumda protokole bağımlıdır ve SSH 'ye özgüdür. RDP söz konusu olduğunda, 3389 numaralı bağlantı noktası kullanılmalıdır. Bu, belirtilen örnek programlarda yapılandırılabilir.

Yerel ara sunucu programlarının tercih ettiğiniz dilde nasıl çalıştırılacağı hakkında yönergeler için aşağıdaki bağlantıları kullanın. [Yankı örneğine](#echo-sample)benzer şekilde, cihaz ve hizmet yerel ara sunucu programlarını tamamen birlikte çalışabilen farklı dillerde çalıştırabilirsiniz.

* [C#hizmet ve hizmet programı](quickstart-device-streams-proxy-csharp.md)

* [Node. js hizmet programı](quickstart-device-streams-proxy-nodejs.md)

* [C cihaz programı](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Sonraki adımlar

Cihaz akışları hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kullanın.

> [!div class="nextstepaction"]
> [IoT Show 'da cihaz akışları (Channel 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
