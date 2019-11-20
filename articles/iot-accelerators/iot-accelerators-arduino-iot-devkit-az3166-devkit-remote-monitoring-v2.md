---
title: IoT DevKit 'i uzaktan Izleme çözümüne bağlama-Azure | Microsoft Docs
description: Bu nasıl yapılır kılavuzunda, IoT DevKit AZ3166 cihazındaki sensörlerden izleme ve görselleştirme için uzaktan Izleme çözüm hızlandırıcısına nasıl telemetri gönderileceğini öğreneceksiniz.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 6e9f9c89cf2e5e40d37a1532e688490aae294181
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888858"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>IoT DevKit cihazını uzaktan Izleme çözüm hızlandırıcısına bağlama

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Bu nasıl yapılır Kılavuzu, IoT DevKit cihazınızda örnek bir uygulamayı nasıl çalıştıracağınızı gösterir. Örnek kod, DevKit cihazındaki sensörlerden çözüm hızlandırıcısına telemetri gönderir.

[Mxyongaıot DevKit](https://aka.ms/iot-devkit) , zengin çevre birimleri ve sensörlerle birlikte hepsi bir arada bir uyumlu panotir. Visual Studio Code 'de [Azure IoT cihaz çalışma ekranı](https://aka.ms/iot-workbench) veya [Azure IoT araçları](https://aka.ms/azure-iot-tools) Uzantı paketi ' ni kullanarak bu BT için geliştirme yapabilirsiniz. [Projeler kataloğu](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) , IoT çözümlerini prototiplerinizde yardımcı olacak örnek uygulamalar içerir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu öğreticideki adımları tamamlayabilmeniz için önce aşağıdaki görevleri yapın:

* [IoT Hub IoT DEVKIT AZ3166 'e bağlanma bölümündeki adımları Bulutta Azure 'a](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)hazırlayın.

## <a name="open-sample-project"></a>Örnek projeyi aç

Uzaktan Izleme örneğini VS Code açmak için:

1. IoT DevKit 'in bilgisayarınızda bulunmadığından emin olun. Önce VS Code başlatın ve ardından DevKit 'i bilgisayarınıza bağlayın.

1. Komut paletini açmak için `F1` ' a tıklayın, yazın ve **Azure IoT cihaz çalışma ekranı ' nı seçin: örnekleri aç...** . Ardından, pano olarak **IoT DevKit** ' i seçin.

1. **Uzaktan izlemeyi** bulun ve **örneği aç**' a tıklayın. Proje klasörünü gösteren yeni bir VS Code penceresi açılır:

   ![IoT çalışma ekranı, uzaktan Izleme örneği seçme](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Cihazı yapılandırma

IoT Hub cihaz bağlantı dizesini DevKit cihazınızda yapılandırmak için:

1. IoT DevKit 'ı **yapılandırma moduna**geçirin:

    * Düğme **A**tuşunu basılı tutun.
    * **Sıfırla** düğmesini gönderin ve serbest bırakın.

1. Ekranda, DevKit KIMLIĞI ve `Configuration`görüntülenir.

    ![IoT DevKit yapılandırma modu](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. **F1** tuşuna basarak komut paletini açın, yazıp **Azure IoT cihaz çalışma ekranı: cihaz ayarlarını yapılandır... seçeneğini belirleyin. > Config cihaz bağlantı dizesi**.

1. Daha önce kopyaladığınız bağlantı dizesini yapıştırın ve cihazı yapılandırmak için **ENTER** tuşuna basın.

## <a name="build-the-code"></a>Kodu oluşturma

Cihaz kodunu derlemek ve karşıya yüklemek için:

1. `F1` tuşuna basarak komut paletini açın, Azure IoT cihaz çalışma ekranı 'nı yazın ve seçin **: Cihaz kodunu karşıya yükle**:

1. VS Code kodu derler ve DevKit cihazınıza yükler:

    ![IoT çalışma ekranı: Cihaz-> karşıya yüklendi](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. DevKit cihazı yeniden başlatılır ve karşıya yüklediğiniz kodu çalıştırır.

## <a name="test-the-sample"></a>Örneği test etme

DevKit cihazına yüklediğiniz örnek uygulamanın çalıştığını doğrulamak için aşağıdaki adımları izleyin:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Uzaktan Izleme çözümüne gönderilen Telemetriyi görüntüleme

Örnek uygulama çalıştığında, DevKit cihazı, ön algılayıcı verilerinden Wi-Fi üzerinden çözüm hızlandırıcısına telemetri gönderir. Telemetriyi görmek için:

1. Çözüm panonuza gidin ve **Device Explorer**' ye tıklayın.

1. DevKit cihazınızın cihaz adına tıklayın. sağ taraftaki sekmede, DevKit 'ten gerçek zamanlı telemetri görebilirsiniz:

    ![Azure IoT Suite 'te algılayıcı verileri](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>DevKit cihazını denetleme

Uzaktan Izleme çözümü hızlandırıcısı, cihazınızı uzaktan denetlemenize olanak tanır. Örnek kod, **Device Explorer** sayfasında cihazı seçerken **Yöntem** bölümünde görebileceğiniz üç yöntemi uygular:

![IoT DevKit yöntemleri](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

DevKit LED 'lerini bir kısmını değiştirmek için, **Ledcolor** yöntemini kullanın:

1. Cihaz listesinden cihaz adı ' nı seçin ve **işler**' e tıklayın:

    ![Iş oluşturma](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Aşağıdaki değerleri kullanarak Işleri yapılandırın ve **Uygula**' ya tıklayın:

   * Iş seçin: **Run yöntemi**
   * Yöntem adı: **Ledcolor**
   * İş adı: **Changeledcolor**

     ![İş ayarları](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Birkaç saniye sonra, DevKit 'teki RGB LED (düğme A 'nın altında) rengi değişir:

    ![IoT DevKit kırmızı ışığı](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticilere devam etmeyi planlıyorsanız Uzaktan İzleme çözümü hızlandırıcısı dağıtımını bırakın.

Çözüm hızlandırıcısına artık ihtiyacınız yoksa, bunu seçip çözümü Sil ' e tıklayarak sağlanan çözümler sayfasından silin.

![Çözümü sil](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Sorunlar ve geri bildirim

Herhangi bir sorunla karşılaşırsanız [IoT DevKit SSS](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) bölümüne başvurun veya aşağıdaki kanalları kullanarak bize ulaşın:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Sonraki adımlar

Artık bir DevKit cihazını uzaktan Izleme çözüm hızlandırıcısına bağlamayı öğrendiğinize göre, aşağıda önerilen birkaç adım verilmiştir:

* [Azure IoT çözüm hızlandırıcılarına genel bakış](https://docs.microsoft.com/azure/iot-accelerators/)
* [Kullanıcı arabirimini özelleştirme](iot-accelerators-remote-monitoring-customize.md)
* [IoT DevKit 'i Azure IoT Central uygulamanıza bağlama](../iot-central/core/howto-connect-devkit.md)