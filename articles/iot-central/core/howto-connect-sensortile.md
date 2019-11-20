---
title: SensorTile. Box cihazını Azure IoT Central uygulamanıza bağlama | Microsoft Docs
description: Bir cihaz geliştiricisi olarak, bir SensorTile. Box cihazını Azure IoT Central uygulamanıza bağlamayı öğrenin.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: e46d4f99075a0d3fa326c84e17792b8959e23517
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930231"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>SensorTile. Box cihazını Azure IoT Central uygulamanıza bağlama

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu makalede, bir cihaz geliştiricisi olarak, bir SensorTile. Box cihazını Microsoft Azure IoT Central uygulamanıza nasıl bağlayabileceğinizi açıklar.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaledeki adımları tamamlayabilmeniz için aşağıdaki kaynaklara ihtiyacınız vardır:

* SensorTile. Box cihazı. Daha fazla bilgi için bkz. [Sensortile. Box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* Android cihazınızda yüklü olan uygun olmayan algılayıcı uygulaması [buradan indirebilirsiniz](https://play.google.com/store/apps/details?id=com.st.bluems). Daha fazla bilgi için şu adresi ziyaret edin: [TILENEBILIR algılayıcı](https://www.st.com/stblesensor)
* **Devkits** uygulama şablonundan oluşturulan bir Azure IoT Central uygulaması. Daha fazla bilgi için bkz. [Uygulama oluşturma hızlı başlangıcı](quick-deploy-iot-central.md).
* **Cihaz şablonları** sayfasını ziyaret edin ve **+ Yeni**' ye tıklayıp **sensortile. Box** şablonunu seçerek **sensortile. Box** cihaz şablonunu IoT Central uygulamanıza ekleyin.

### <a name="get-your-device-connection-details"></a>Cihaz bağlantınızın ayrıntılarını alın

Azure IoT Central uygulamanızda, **Sensortile. Box** cihaz şablonundan gerçek bir cihaz ekleyin ve cihaz bağlantısı ayrıntılarını ( **Kapsam KIMLIĞI**, **cihaz kimliği**ve **birincil anahtar**) bir yere göz önünde oluşturun:

1. Cihazlardan cihaz ekleyin. Gerçek bir cihaz eklemek için **+ yeni > Real** ' ı seçin.

    * Küçük bir **CIHAZ kimliği**girin veya ÖNERILEN **cihaz kimliğini**kullanın.
    * Bir **Cihaz adı**girin veya önerilen adı kullanın

    ![Cihaz Ekleme](media/howto-connect-sensortile/real-device.png)

1. Cihaz bağlantısı ayrıntılarını, **kapsam kimliğini**, **cihaz kimliğini**ve **birincil anahtarı**almak için cihaz sayfasında **Bağlan** ' ı seçin.

    ![Bağlantı ayrıntıları](media/howto-connect-sensortile/connect-device.png)

1. Bağlantı ayrıntılarını bir yere getirin. Bir sonraki adımda DevKit cihazınızı hazırlarken internet bağlantısı geçici olarak kesilir.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Mobil uygulamayla SensorTile. Box 'ı ayarlama

Bu bölümde, uygulama bellenimini cihaza nasıl gönderebileceğinizi öğreneceksiniz. Daha sonra, Bluetooth düşük enerji (BLE) bağlantısı kullanarak, ıBLE algılayıcı mobil uygulaması aracılığıyla IoT Central cihaz verileri gönderebilirsiniz.

1. KULLANILABILIR algılayıcı uygulamasını açın ve **Yeni uygulama oluştur** düğmesine basın.

    ![Uygulama oluştur](media/howto-connect-sensortile/create-app.png)

1. **Barometer** uygulamasını seçin.
1. Karşıya Yükle düğmesine basın.

    ![Barometer karşıya yükle](media/howto-connect-sensortile/barometer-upload.png)

1. Sensortuğunuz. Box ile ilişkili Play düğmesine basın.
1. İşlem tamamlandığında SensorTile. Box sıcaklığın, basınç ve nem duyduğunu bir şekilde akıtılabilir.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>SensorTile. Box 'ı buluta bağlayın

Bu bölümde, SensorTile. Box ' ı mobil uygulamaya bağlamayı ve mobil uygulamayı buluta bağlamayı öğreneceksiniz.

1. Sol bölmeyi kullanarak, **bulut günlüğü** düğmesini seçin.

    ![Bulut günlüğü](media/howto-connect-sensortile/cloud-logging.png)

1. Bulut sağlayıcısı olarak **Azure IoT Central** ' yi seçin.
1. Daha önce belirtilen cihaz KIMLIĞINI ve kapsam KIMLIĞINI ekleyin.

    ![Kimlik Bilgileri](media/howto-connect-sensortile/credentials.png)

1. **Uygulama anahtarı** radyo düğmesini seçin.
1. **Bağlan** ' a tıklayın ve karşıya yüklemek istediğiniz telemetri verilerini seçin.
1. Birkaç saniye sonra, veriler IoT Central uygulama panosunda görüntülenir.

## <a name="sensortilebox-device-template-details"></a>SensorTile. Box cihaz şablonu ayrıntıları

SensorTile. Box cihaz şablonundan oluşturulan, aşağıdaki özelliklere sahip bir uygulama:

### <a name="telemetry"></a>Telemetri

| Alan adı     | Birimler  | Minimum | Maksimum | Ondalık basamak sayısı |
| -------------- | ------ | ------- | ------- | -------------- |
| Nem oranı       | %      | 30       | 90     | 1              |
| kopyalar           | 20     | 0     | 40     | 1              |
| basınç       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| Ivometerx | mg     | -2000   | 2000    | 0              |
| Iventery | mg     | -2000   | 2000    | 0              |
| Ivometerz | mg     | -2000   | 2000    | 0              |
| Jroscopex     | DPS   | -3276   | 3276    | 1              |
| Jroscopey     | DPS   | -3276   | 3276    | 1              |
| Jroscopez     | DPS   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanıza bir SensorTile. Box bağlamayı öğrendiğinize göre, önerilen sonraki adım, kendi IoT cihazınız için [özel bir cihaz şablonu ayarlamayı](howto-set-up-template.md) öğrenmektedir.
