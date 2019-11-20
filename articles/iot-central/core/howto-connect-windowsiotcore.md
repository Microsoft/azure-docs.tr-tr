---
title: Windows IoT Core 'ı Azure IoT Central uygulamanıza bağlama | Microsoft Docs
description: Bir cihaz geliştiricisi olarak, bir Mxyonga IoT DevKit cihazını Azure IoT Central uygulamanıza bağlamayı öğrenin.
author: miriambrus
ms.author: miriamb
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 43e99c54249738436f24369ed3525e78ff971a12
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930202"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Windows IoT çekirdek cihazını Azure IoT Central uygulamanıza bağlama

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu makalede, bir cihaz geliştiricisi olarak Windows IoT çekirdek cihazını Microsoft Azure IoT Central uygulamanıza nasıl bağlayabileceğinizi açıklanmaktadır.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaledeki adımları tamamlayabilmeniz için şunlar gereklidir:

- **Örnek Devkits** uygulama şablonundan oluşturulan bir Azure IoT Central uygulaması. Daha fazla bilgi için bkz. [Uygulama oluşturma hızlı başlangıcı](quick-deploy-iot-central.md).

- Windows 10 IoT Core işletim sistemini çalıştıran bir cihaz. Daha fazla bilgi için bkz. [Windows 10 IoT çekirdek cihazınızı ayarlama](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- [Node. js](https://nodejs.org/) sürüm 8.0.0 veya üzeri yüklü bir geliştirme makinesi. Sürümünüzü denetlemek için komut satırında `node --version` çalıştırabilirsiniz. Node.js çeşitli işletim sistemleri için kullanılabilir.

## <a name="the-sample-devkits-application"></a>Örnek Devkits uygulaması

**Örnek Devkits** uygulama şablonundan oluşturulan bir uygulama, aşağıdaki özelliklere sahip bir **Windows IoT Core** cihaz şablonu içerir:

- Cihaz için telemetri ölçümleri: **nem**, **sıcaklık**ve **basınç**.
- **Fan hızını**denetleme ayarı.
- Bir cihaz özelliği **Die numarası** ve bir bulut özelliği **konumu**.

Cihaz şablonunun yapılandırmasıyla ilgili tüm ayrıntılar için bkz. [Windows IoT çekirdek cihaz şablonu ayrıntıları](#device-template-details).

## <a name="add-a-real-device"></a>Gerçek cihaz ekleme

Azure IoT Central uygulamanızda, **Windows 10 IoT çekirdek** cihaz şablonundan gerçek bir cihaz eklemek için **Device Explorer** sayfasını kullanın. Cihaz bağlantısı ayrıntılarını (**kapsam kimliği**, **cihaz kimliği**ve **birincil anahtar**) bir yere unutmayın.

## <a name="prepare-the-device"></a>Cihazı hazırlama

Cihazın IoT Central bağlanmasına yönelik bir bağlantı dizesi gerekir:

1. Bir bağlantı dizesi oluşturmak için `dps-keygen` komut satırı yardımcı programını kullanın:

    [Anahtar Oluşturucu yardımcı programını](https://github.com/Azure/dps-keygen)yüklemek için şu komutu çalıştırın:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Bir bağlantı dizesi oluşturmak için, daha önce not ettiğiniz bağlantı ayrıntılarını kullanarak aşağıdaki komutu çalıştırın:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Bağlantı dizesini, cihaz kodunuzda kullanmak üzere `dps-keygen` çıktısından kopyalayın.

Cihaz kodunun bağlantı dizesine erişmesi için, Windows 10 IoT çekirdek cihazınızdaki `C:\Data\Users\DefaultAccount\Documents\` klasörde yer alan **Connection. String. ıothub** adlı bir dosyaya kaydedin.

**Bağlantı. String. ıothub** dosyasını masaüstü makinenizden cihazınızdaki `C:\Data\Users\DefaultAccount\Documents\` klasöre kopyalamak Için [Windows cihaz portalını](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)kullanabilirsiniz:

1. Cihazınızda Windows cihaz portalına gitmek için Web tarayıcınızı kullanın.
1. Cihazınızdaki dosyalara gitmek için, **dosya gezgini > uygulamalar**' ı seçin.
1. **Kullanıcı klasörü \ belgeler**sayfasına gidin. Daha sonra **Connection. String. ıothub** dosyasını karşıya yükleyin:

    ![Bağlantı dizesini karşıya yükle](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Dağıt ve Çalıştır

Örnek uygulamayı cihazınızda dağıtmak ve çalıştırmak için, [Windows cihaz portalını](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)kullanabilirsiniz:

1. Cihazınızda Windows cihaz portalına gitmek için Web tarayıcınızı kullanın.
1. **Azure IoT Hub istemci** uygulamasını dağıtmak ve çalıştırmak Için, **Uygulamalar > hızlı Çalıştır örnekleri**' ni seçin. Ardından **Azure IoT Hub Client**' ı seçin.
1. Ardından **Dağıt ve Çalıştır '** ı seçin.

    ![Dağıt ve Çalıştır](media/howto-connect-windowsiotcore/quick-run.png)

Birkaç dakika sonra, IoT Central uygulamanızda Telemetriyi izleyebilirsiniz.

[Windows cihaz portalı](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) , cihazınızın sorunlarını gidermek için kullanabileceğiniz araçları içerir:

- **Apps Manager** sayfası, cihazınızda çalışan uygulamaları denetlemenize olanak tanır.
- Cihazınıza bağlı bir monitörünüz yoksa, cihazınızdaki ekran görüntülerini yakalamak için **cihaz ayarları** sayfasını kullanabilirsiniz. Örneğin:

    ![Uygulama ekran görüntüsü](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Kaynak kodunu indirin

İstemci uygulaması için kaynak kodu araştırmak ve değiştirmek istiyorsanız, [Windows-ıotcore-Samples GitHub deposundan](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients)indirebilirsiniz.

## <a name="device-template-details"></a>Cihaz şablonu ayrıntıları

**Örnek Devkits** uygulama şablonundan oluşturulan bir uygulama, aşağıdaki özelliklere sahip bir **Windows IoT Core** cihaz şablonu içerir:

### <a name="telemetry-measurements"></a>Telemetri ölçümleri

| Alan adı     | Birimler  | Minimum | Maksimum | Ondalık basamak sayısı |
| -------------- | ------ | ------- | ------- | -------------- |
| Nem oranı       | %      | 0       | 100     | 0              |
| kopyalar           | 20     | -40     | 120     | 0              |
| basınç       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Ayarlar

Sayısal ayarlar

| Görünen ad | Alan adı | Birimler | Ondalık basamak sayısı | Minimum | Maksimum | Başlatma |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Fan hızı    | Fanın hızı   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Özellikler

| Tür            | Görünen ad | Alan adı | Veri türü |
| --------------- | ------------ | ---------- | --------- |
| Cihaz özelliği | Zar numarası   | dieNumber  | number    |
| Metin            | Konum     | location   | Yok       |

## <a name="next-steps"></a>Sonraki adımlar

Artık bir Windows IoT Core cihazını Azure IoT Central uygulamanıza bağlamayı öğrendiğinize göre, önerilen sonraki adım, kendi IoT cihazınız için [özel bir cihaz şablonu ayarlamayı](howto-set-up-template.md) öğrenmektedir.
