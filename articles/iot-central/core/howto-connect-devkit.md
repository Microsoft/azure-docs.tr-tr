---
title: DevKit cihazını Azure IoT Central uygulamanıza bağlama | Microsoft Docs
description: Bir cihaz geliştiricisi olarak, bir Mxyonga IoT DevKit cihazını Azure IoT Central uygulamanıza bağlamayı öğrenin.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4e04ae7d9594ac064c9f3707c797fb2709a79cb6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582935"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Bir Mxyonga IoT DevKit cihazını Azure IoT Central uygulamanıza bağlama

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu makalede bir cihaz geliştiricisi olarak, bir Mxyonga IoT DevKit (DevKit) cihazını Microsoft Azure IoT Central uygulamanıza nasıl bağlayabileceğinizi açıklamaktadır.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaledeki adımları tamamlayabilmeniz için aşağıdaki kaynaklara ihtiyacınız vardır:

1. **Örnek Devkits** uygulama şablonundan oluşturulan bir Azure IoT Central uygulaması. Daha fazla bilgi için bkz. [Uygulama oluşturma hızlı başlangıcı](quick-deploy-iot-central.md).
1. Bir DevKit cihazı. Bir DevKit cihazı satın almak için, [Mxyongaıot DevKit](https://microsoft.github.io/azure-iot-developer-kit/)' i ziyaret edin.

## <a name="sample-devkits-application"></a>Örnek Devkits uygulaması

**Örnek Devkits** uygulama şablonundan oluşturulan bir uygulama, aşağıdaki cihaz özelliklerini tanımlayan bir **mxyonga** cihaz şablonu içerir:

- **Nem**, **sıcaklık**, **basınç**, **manyetik tometre** (x, y, z ekseni ve x, y, z ekseni) ve **cayroscope** **(x** , y, z ekseni ile ölçülür) için telemetri ölçümleri.
- **Cihaz durumu**için durum ölçümü.
- **Düğme B düğmesine basıldığında**olay ölçümü.
- **Voltaj**, **geçerli**, **fan hızı**ve **IR** geçişi için ayarlar.
- Cihaz özellikleri, bir konum özelliği olan **zar numarası** ve **Cihaz konumu**.
- **Içinde üretilen**bulut özelliği.
- Komut **echo** ve **geri sayım**. Gerçek bir cihaz bir **echo** komutu aldığında, cihazın görüntüsüne gönderilen değeri gösterir. Gerçek bir cihaz bir **geri sayım** komutu aldığında, geçişli bir düzende döngü yapar ve cihaz geri sayım değerlerini IoT Central geri gönderir.

Yapılandırma hakkında tam Ayrıntılar için bkz. [Mxyonga cihaz şablonu ayrıntıları](#mxchip-device-template-details)

## <a name="add-a-real-device"></a>Gerçek cihaz ekleme

### <a name="get-your-device-connection-details"></a>Cihaz bağlantınızın ayrıntılarını alın

Azure IoT Central uygulamanızda, **Mxyonga** cihaz şablonundan gerçek bir cihaz ekleyin ve cihaz bağlantısı ayrıntılarını şu şekilde bir yere unutmayın: **Kapsam KIMLIĞI, cihaz kimliği ve birincil anahtar**:

1. Device Explorer 'den **gerçek bir cihaz** ekleyin, gerçek bir cihaz eklemek Için **+ Yeni > Real** ' ı seçin.

    * Küçük bir **CIHAZ kimliği**girin veya ÖNERILEN **cihaz kimliğini**kullanın.
    * Bir **Cihaz adı**girin veya önerilen adı kullanın

    ![Cihaz Ekleme](media/howto-connect-devkit/add-device.png)

1. Cihaz bağlantısı ayrıntılarını, **kapsam kimliğini**, **cihaz kimliğini**ve **birincil anahtarı**almak için cihaz sayfasında **Bağlan** ' ı seçin.

    ![Bağlantı ayrıntıları](media/howto-connect-devkit/device-connect.png)

1. Bağlantı ayrıntılarını bir yere getirin. Bir sonraki adımda DevKit cihazınızı hazırlarken internet bağlantısı geçici olarak kesilir.

### <a name="prepare-the-devkit-device"></a>DevKit cihazını hazırlama

Daha önce cihazı kullandıysanız ve farklı bir WiFi ağı, bağlantı dizesi veya telemetri ölçümü kullanmak üzere yeniden yapılandırmak istiyorsanız, aynı anda hem **a** hem de **B** düğmelerine basın. İşe yaramazsa, **Sıfırla** düğmesine basın ve yeniden deneyin.

#### <a name="to-prepare-the-devkit-device"></a>DevKit cihazını hazırlamak için

1. GitHub 'daki [yayınlar](https://aka.ms/iotcentral-docs-MXChip-releases) sayfasından mxyonga için en son önceden oluşturulmuş Azure IoT Central bellenimini indirin.
1. DevKit cihazını bir USB kablosu kullanarak geliştirme makinenize bağlayın. Windows 'da, DevKit cihazında depolama ile eşleştirilmiş bir sürücüde dosya Gezgini penceresi açılır. Örneğin, sürücü **AZ3166 (D:)** olarak adlandırılabilir.
1. **Iotcentral. bin** dosyasını sürücü penceresine sürükleyin. Kopyalama tamamlandığında, cihaz yeni üretici yazılımıyla yeniden başlatılır.

1. DevKit cihazı yeniden başlatıldığında aşağıdaki ekran görüntülenir:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Ekranda başka bir şey görüntüleniyorsa, cihazı yeniden başlatmak için cihazı sıfırlayın ve cihazdaki **A** ve **B** düğmelerine basın.

1. Cihaz artık erişim noktası (AP) modunda. Bu WiFi erişim noktasına bilgisayarınızdan veya mobil cihazdan bağlanabilirsiniz.

1. Bilgisayarınızda, telefonunuzda veya tabletinizde, cihazın ekranında gösterilen WiFi ağ adına bağlanın. Bu ağa bağlandığınızda Internet erişiminiz yok demektir. Bu durum beklenir ve bu ağa yalnızca cihazı yapılandırırken kısa bir süre için bağlanırsınız.

1. Web tarayıcınızı açın ve [http://192.168.0.1/start](http://192.168.0.1/start)gidin. Aşağıdaki Web sayfası görüntülenir:

    ![Cihaz yapılandırma sayfası](media/howto-connect-devkit/configpage.png)

    Web sayfasında, şunu girin:
    - WiFi ağınızın adı
    - WiFi ağ parolanız
    - Cihazın görüntüsünde gösterilen PIN kodu
    - Bağlantı ayrıntıları **kapsam kimliği**, **cihaz kimliği**ve cihazınızın **birincil anahtarı** (Bu adımları daha önce kaydetmiş olmanız gerekir)
    - Tüm kullanılabilir telemetri ölçülerini seçin

1. **Cihazı Yapılandır**' ı seçtikten sonra şu sayfayı görürsünüz:

    ![Cihaz yapılandırıldı](media/howto-connect-devkit/deviceconfigured.png)

1. Cihazınızdaki **Sıfırla** düğmesine basın.

## <a name="view-the-telemetry"></a>Telemetriyi görüntüleme

DevKit cihazı yeniden başlatıldığında, cihazdaki ekran şu şekilde görünür:

* Gönderilen telemetri iletilerinin sayısı.
* Başarısızlık sayısı.
* Alınan istenen özellik sayısı ve gönderilen bildirilen özellik sayısı.

> [!NOTE]
> Bağlantı bağlanmayı denediğinde cihaz döngüye alıyorsa, cihazın IoT Central **engellenip engellenmediğini** kontrol edin ve uygulamanın uygulamaya bağlanabilmesi Için **engelini kaldırın** .

Bildirilen bir özelliği göndermek için cihazı sallayın. Cihaz, **zar numarası** cihaz özelliği olarak rastgele bir sayı gönderir.

Telemetri ölçümlerini ve bildirilen özellik değerlerini görüntüleyebilir ve Azure IoT Central ayarları yapılandırabilirsiniz:

1. Eklediğiniz gerçek Mxyonga cihazının **ölçümler** sayfasına gitmek için **cihazları** kullanın:

    ![Gerçek cihaza git](media/howto-connect-devkit/realdevicenew.png)

1. **Ölçümler** sayfasında, mxyonga cihazından gelen Telemetriyi görebilirsiniz:

    ![Gerçek cihazdan telemetri görüntüleme](media/howto-connect-devkit/devicetelemetrynew.png)

1. **Özellikler** sayfasında, son zar numarasını ve cihaz tarafından bildirilen cihaz konumunu görüntüleyebilirsiniz:

    ![Cihaz özelliklerini görüntüle](media/howto-connect-devkit/devicepropertynew.png)

1. **Ayarlar** sayfasında, mxyonga cihazında ayarları güncelleştirebilirsiniz:

    ![Cihaz ayarlarını görüntüleme](media/howto-connect-devkit/devicesettingsnew.png)

1. **Komutlar** sayfasında, **echo** ve **geri sayım** komutlarını çağırabilirsiniz:

    ![Çağrı komutları](media/howto-connect-devkit/devicecommands.png)

1. **Pano** sayfasında, konum haritasını görebilirsiniz

    ![Cihaz panosunu görüntüle](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>Kaynak kodunu indirin

Cihaz kodunu incelemek ve değiştirmek istiyorsanız, GitHub 'dan indirebilirsiniz. Kodu değiştirmeyi planlıyorsanız, masaüstü işletim sisteminiz için [geliştirme ortamını hazırlamak](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) üzere bu yönergeleri izlemelisiniz.

Kaynak kodunu indirmek için masaüstü makinenizde aşağıdaki komutu çalıştırın:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Önceki komut, kaynak kodu `iot-central-firmware`adlı bir klasöre indirir.

> [!NOTE]
> **Git** , geliştirme ortamınızda yüklü değilse, [https://git-scm.com/download](https://git-scm.com/download)adresinden indirebilirsiniz.

## <a name="review-the-code"></a>Kodu gözden geçirin

`iot-central-firmware` klasöründeki `MXCHIP/mxchip_advanced` klasörünü açmak için Visual Studio Code kullanın:

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Telemetriyi Azure IoT Central uygulamasına nasıl gönderildiğini görmek için, `src` klasöründeki **telemetri. cpp** dosyasını açın:

- İşlev `TelemetryController::buildTelemetryPayload`, cihazdaki sensörlerden alınan verileri kullanarak JSON telemetri yükünü oluşturur.

- İşlevi `TelemetryController::sendTelemetryPayload` JSON yükünü Azure IoT Central uygulamanızın kullandığı IoT Hub göndermek için **AzureIOTClient. cpp** `sendTelemetry` çağırır.

Özellik değerlerinin Azure IoT Central uygulamasına nasıl raporlanacağı görmek için, `src` klasöründe **telemetri. cpp** dosyasını açın:

- İşlevi `TelemetryController::loop` bildirilen **konum** özelliğini yaklaşık 30 saniyede gönderir. **AzureIOTClient. cpp** kaynak dosyasındaki `sendReportedProperty` işlevini kullanır.

- `TelemetryController::loop` işlevi, cihaz hızlandırma, bir çift dokunma algıladığında, **Dienumber** bildirilen özelliğini gönderir. **AzureIOTClient. cpp** kaynak dosyasındaki `sendReportedProperty` işlevini kullanır.

Cihazın IoT Central uygulamasından çağrılan komutlara nasıl yanıt verdiğini görmek için `src` klasöründeki **registeredMethodHandlers. cpp** dosyasını açın:

- **Dmecho** işlevi, **echo** komutunun işleyicisidir. Cihazın ekranındaki yükte dosyalanan **Displayedvalue değerini** gösterir.

- **Dmıngeri sayım** işlevi, **geri sayım** komutunun işleyicisidir. Cihazın LED rengini değiştirir ve geri sayım değerini IoT Central uygulamasına geri göndermek için bildirilen bir özellik kullanır. Bildirilen özellik, komutuyla aynı ada sahip. İşlevi, **AzureIOTClient. cpp** kaynak dosyasındaki `sendReportedProperty` işlevini kullanır.

**AzureIOTClient. cpp** kaynak dosyasındaki kod, IoT Hub etkileşimde bulunmak Için [Microsoft Azure IoT SDK 'ları ve kütüphanelerinin](https://github.com/Azure/azure-iot-sdk-c) işlevlerini kullanır.

Örnek kodu cihazınıza değiştirme, derleme ve yükleme hakkında daha fazla bilgi için `MXCHIP/mxchip_advanced` klasöründeki **README.MD** dosyasına bakın.

## <a name="mxchip-device-template-details"></a>Mxyonga cihaz şablonu ayrıntıları

Örnek Devkits uygulama şablonundan oluşturulan bir uygulama, aşağıdaki özelliklere sahip bir Mxyonga cihaz şablonu içerir:

### <a name="measurements"></a>Ölçümler

#### <a name="telemetry"></a>Telemetri

| Alan adı     | Birimler  | Minimum | Maksimum | Ondalık basamak sayısı |
| -------------- | ------ | ------- | ------- | -------------- |
| Nem oranı       | %      | 0       | 100     | 0              |
| kopyalar           | 20     | -40     | 120     | 0              |
| basınç       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| Ivometerx | mg     | -2000   | 2000    | 0              |
| Iventery | mg     | -2000   | 2000    | 0              |
| Ivometerz | mg     | -2000   | 2000    | 0              |
| Jroscopex     | MDPS   | -2000   | 2000    | 0              |
| Jroscopey     | MDPS   | -2000   | 2000    | 0              |
| Jroscopez     | MDPS   | -2000   | 2000    | 0              |

#### <a name="states"></a>Durumlar 
| Ad          | Görünen ad   | OLAĞAN | DIKKATLI | OLMA TEHLIKESI | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Cihaz Durumu   | Yeşil  | Orange  | Kırmızı    | 

#### <a name="events"></a>Olaylar 
| Ad             | Görünen ad      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Düğme B tuşuna basıldı  | 

### <a name="settings"></a>Ayarlar

Sayısal ayarlar

| Görünen ad | Alan adı | Birimler | Ondalık basamak sayısı | Minimum | Maksimum | Başlatma |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Geril      | Setvoltaj | Çalışmıyorken | 0              | 0       | 240     | 0       |
| Geçerli      | setCurrent | AMPS  | 0              | 0       | 100     | 0       |
| Fan hızı    | Fanın hızı   | RPM   | 0              | 0       | 1000    | 0       |

Ayarları aç

| Görünen ad | Alan adı | Metinde | Kapalı metin | Başlatma |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | Activateır | AÇIK      | KAPALI      | Kapalı     |

### <a name="properties"></a>Özellikler

| Tür            | Görünen ad | Alan adı | Veri türü |
| --------------- | ------------ | ---------- | --------- |
| Cihaz özelliği | Zar numarası   | dieNumber  | number    |
| Cihaz özelliği | Cihaz konumu   | location  | location    |
| Metin            | Üretilmiş     | Üreten Turedın   | Yok       |

### <a name="commands"></a>Komutlar

| Görünen ad | Alan adı | Dönüş türü | Giriş alanı görünen adı | Giriş alanı adı | Giriş alanı türü |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Girdilerinizi         | echo       | metin        | görüntülenecek değer         | displayedValue   | metin             |
| Sayıma    | Sayıma  | number      | Sayım               | Sayaçdan        | number           |

## <a name="next-steps"></a>Sonraki adımlar

Bir Mxyonga IoT DevKit 'i Azure IoT Central uygulamanıza bağlamayı öğrendiğinize göre, önerilen sonraki adım, kendi IoT cihazınız için [özel bir cihaz şablonu ayarlamayı](howto-set-up-template.md) öğrenirsiniz.
