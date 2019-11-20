---
title: Azure IoT Central uygulamanıza bir Raspberry PI bağlama (Python) | Microsoft Docs
description: Bir cihaz geliştiricisi olarak, Python kullanarak Raspberry Pi 'yi Azure IoT Central uygulamanıza bağlama.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 258410bcd4f916ac381188bb38d90a3b89c87c89
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954246"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Azure IoT Central uygulamanıza bir Raspberry PI bağlama (Python)

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu makalede, bir cihaz geliştiricisi olarak, Python programlama dilini kullanarak bir Raspberry Pi 'yi Microsoft Azure IoT Central uygulamanıza nasıl bağlayabileceğinizi açıklamaktadır.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaledeki adımları tamamlayabilmeniz için aşağıdaki bileşenlere ihtiyacınız vardır:

* **Örnek Devkits** uygulama şablonundan oluşturulan bir Azure IoT Central uygulaması. Daha fazla bilgi için bkz. [Uygulama oluşturma hızlı başlangıcı](quick-deploy-iot-central.md).
* Raspbian işletim sistemini çalıştıran bir Raspberry PI cihazı. Raspberry PI, internet 'e bağlanabilmelidir. Daha fazla bilgi için bkz. [Raspberry PI 'Nizi ayarlama](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

> [!TIP]
> Bir Raspberry PI cihazını ayarlama ve bu cihaza bağlanma hakkında bilgi edinmek için [Raspberry Pi 'yi kullanmaya başlama](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi) makalesini ziyaret edin

## <a name="sample-devkits-application"></a>**Örnek Devkits** uygulaması

**Örnek Devkits** uygulama şablonundan oluşturulan bir uygulama, aşağıdaki özelliklere sahip bir **Raspberry PI** cihaz şablonu içerir:

- Telemetri, cihazın toplayacağı aşağıdaki ölçüleri içerir:
  - Nem oranı
  - Sıcaklık
  - Gereksiniminin
  - Manyetik tometre (X, Y, Z)
  - İvme ölçer (X, Y, Z)
  - Jroscope (X, Y, Z)
- Ayarlar
  - Geril
  - Geçerli
  - Fan hızı
  - IR geçişi.
- Özellikler
  - Zar numarası cihaz özelliği
  - Konum bulutu özelliği

Cihaz şablonunun yapılandırmasının tam ayrıntıları için, [Raspberry PI cihaz şablonu ayrıntılarına](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)bakın.

## <a name="add-a-real-device"></a>Gerçek cihaz ekleme

Azure IoT Central uygulamanızda, **Raspberry PI** cihaz şablonundan gerçek bir cihaz ekleyin. Cihaz bağlantısı ayrıntılarını (**kapsam kimliği**, **cihaz kimliği**ve **birincil anahtar**) bir yere unutmayın. Daha fazla bilgi için bkz. [Azure IoT Central uygulamanıza gerçek bir cihaz ekleme](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Raspberry PI 'yi yapılandırma

Aşağıdaki adımlarda, örnek Python uygulamasının GitHub 'dan nasıl indirileceği ve yapılandırılacağı açıklanır. Bu örnek uygulama:

* Azure IoT Central telemetri ve özellik değerlerini gönderir.
* Azure IoT Central 'da yapılan ayar değişikliklerine yanıt verir.

1. Raspberry PI Desktop 'tan ya da SSH kullanarak uzaktan Raspberry PI 'inizdeki bir kabuk ortamına bağlanın.

1. IoT Central Python istemcisini yüklemek için şu komutu çalıştırın:

    ```bash
    pip install iotc
    ```

1. Örnek Python kodunu indirin:

    ```bash
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. İndirdiğiniz `app.py` dosyasını düzenleyin ve `DEVICE_ID`, `SCOPE_ID`ve `PRIMARY/SECONDARY device KEY` yer tutucuları daha önce bir nota yaptığınız bağlantı değerleriyle değiştirin. Yaptığınız değişiklikleri kaydedin.

    > [!TIP]
    > Raspberry Pi üzerindeki kabukta, **nano** ya da **VI** metin düzenleyicilerini kullanabilirsiniz.

1. Örneği çalıştırmak için aşağıdaki komutu kullanın:

    ```bash
    python app.py
    ```

    Raspberry PI 'niz Azure IoT Central telemetri ölçümleri göndermeye başlıyor.

1. Azure IoT Central uygulamanızda, Raspberry PI üzerinde çalışan kodun uygulamayla nasıl etkileşime gireceğini görebilirsiniz:

    * Gerçek cihazınızın **ölçümler** sayfasında, Raspberry Pi 'den gönderilen Telemetriyi görebilirsiniz.
    * **Özellikler** sayfasında, **zar numarası** cihaz özelliğini görebilirsiniz.
    * **Ayarlar** sayfasında, Raspberry Pi üzerinde voltaj ve fan hızı gibi ayarları değiştirebilirsiniz. Raspberry Pi, değişikliği gösterdiğinde, ayar **eşitlenmiş**olarak gösterilir.

## <a name="raspberry-pi-device-template-details"></a>Raspberry PI cihaz şablonu ayrıntıları

**Örnek Devkits** uygulama şablonundan oluşturulan bir uygulama, aşağıdaki özelliklere sahip bir **Raspberry PI** cihaz şablonu içerir:

### <a name="telemetry-measurements"></a>Telemetri ölçümleri

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
| Metin            | Konum     | location   | Yok       |

## <a name="next-steps"></a>Sonraki adımlar

Artık bir Raspberry Pi 'yi Azure IoT Central uygulamanıza bağlamayı öğrendiğinize göre, önerilen sonraki adım, kendi IoT cihazınız için [özel bir cihaz şablonu ayarlamayı](howto-set-up-template.md) öğrenirsiniz.
