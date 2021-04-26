---
title: Mxyongaıot DevKit 'i Azure 'a bağlama IoT Hub uzaktan Izleme
description: Bu öğreticide IoT DevKit AZ3166 üzerinde sensörlerinin durumunu Azure IoT uzaktan Izleme çözüm hızlandırıcısına nasıl göndereceğiniz hakkında bilgi edinebilirsiniz.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 6912124ce8d1741731d625dccfea445417b2488d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785025"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Mxyongaıot DevKit 'i Azure IoT uzaktan Izleme çözüm hızlandırıcısına bağlama

Bu öğreticide, Azure IoT uzaktan Izleme çözüm hızlandırıcısına algılayıcı verileri göndermek için DevKit 'te örnek bir uygulamayı nasıl çalıştıracağınızı öğreneceksiniz.

[Mxyongaıot DevKit](https://aka.ms/iot-devkit) , zengin çevre birimleri ve sensörlerle birlikte hepsi bir arada bir uyumlu panotir. [Arduino için Visual Studio Code uzantısını](https://aka.ms/arduino)kullanarak bu BT için geliştirme yapabilirsiniz. Ayrıca, Microsoft Azure hizmetlerinden yararlanan prototip Nesnelerin İnterneti (IoT) çözümlerine kılavuzluk eden büyüyen bir [Proje kataloğu](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) ile birlikte sunulur.

## <a name="what-you-need"></a>Gerekenler

[Başlarken Kılavuzunu](./iot-hub-arduino-iot-devkit-az3166-get-started.md) şu şekilde sona erdirin:

* DevKit 'in Wi-Fi bağlı olmasını sağlama
* Geliştirme ortamını hazırlama

Etkin bir Azure aboneliği. Bir tane yoksa, bu iki yöntemden birini kullanarak kaydedebilirsiniz:

* [30 günlük ücretsiz deneme Microsoft Azure hesabını](https://azure.microsoft.com/free/) etkinleştirin

* MSDN veya Visual Studio abonesi olduğunuzda [Azure kredinizi](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) talep edin

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Azure IoT uzaktan Izleme çözümü Hızlandırıcısı oluşturma

1. [Azure IoT Çözüm Hızlandırıcıları sitesine](https://www.azureiotsolutions.com/) gidin ve **yeni çözüm oluştur ' a** tıklayın.

   ![Azure IoT Çözüm Hızlandırıcısı türünü seçin](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Bu örnek, bir IoT uzaktan Izleme çözüm Hızlandırıcısını oluşturduktan sonra varsayılan olarak bir S2 IoT Hub oluşturur. Bu IoT Hub 'ı çok sayıda cihaz ile kullanılmıyorsa, bu dosyayı S2 'den S1 'e indirgemenizi ve IoT uzaktan Izleme çözüm Hızlandırıcısını silmenizi öneririz; Böylece artık ihtiyacınız kalmadığında ilgili IoT Hub de silinebilir. 

2. **Uzaktan izleme**' yi seçin.

3. Bir çözüm adı girin, bir abonelik ve bölge seçin ve ardından **çözüm oluştur**' a tıklayın. Çözümün sağlanması biraz zaman alabilir.
  
   ![Çözüm oluşturma](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Sağlama bittikten sonra **Başlat**' a tıklayın. Sağlama işlemi sırasında çözüm için bazı sanal cihazlar oluşturulur. **Cihazlar** ' a tıklayarak bunları kontrol edin.

   ![Pano](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Konsol](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. **Cihaz Ekle**' ye tıklayın.

6. **Özel cihaz** Için **Yeni Ekle** ' ye tıklayın.
  
   ![Yeni cihaz ekleme](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. **Kendi cihaz kimliğimi tanımlamama Izin ver**' e tıklayın, girin `AZ3166` ve ardından **Oluştur**' a tıklayın.
  
   ![KIMLIĞI olan cihaz oluştur](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. **IoT Hub ana bilgisayar adı**' nı bir yere getirin ve **bitti**' ye tıklayın.

## <a name="open-the-remotemonitoring-sample"></a>RemoteMonitoring örneğini açın

1. Bağlantı kurulduktan sonra DevKit 'in bilgisayarınızdan bağlantısını kesin.

2. VS Code'u başlatın.

3. DevKit 'i bilgisayarınıza bağlayın. VS Code, DevKit kodunuzu otomatik olarak algılar ve aşağıdaki sayfaları açar:

   * DevKit tanıtım sayfası.
   * Arduino örnekleri: DevKit ile çalışmaya başlamak için uygulamalı örnekler.

4. Sol taraf **Arduino örnekleri** bölümünü GENIŞLETIN, **mxyongaAZ3166 > AzureIoT için örneklere** gidin ve **RemoteMonitoring**' yi seçin. İçinde proje klasörü olan yeni bir VS Code penceresi açar.

   > [!NOTE]
   > Bölmeyi kapatmanız durumunda yeniden açabilirsiniz. `Ctrl+Shift+P` `Cmd+Shift+P` Komut paletini açmak için (MacOS:) kullanın, **Arduino** yazın ve **Arduino: örnekleri** bulun ve seçin.

## <a name="provision-required-azure-services"></a>Gerekli Azure hizmetlerini sağlayın

Çözüm penceresinde, `Ctrl+P` `Cmd+P` sunulan metin kutusuna girerek (MacOS:) görevini çalıştırın `task cloud-provision` .

VS Code terminalinde, etkileşimli bir komut satırı gerekli Azure hizmetlerini sağlama sırasında size rehberlik eder.

![Azure kaynaklarını sağlama](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Cihaz kodunu derleyin ve karşıya yükleyin

1. `Ctrl+P`(MacOS: `Cmd + P` ) ve tür **görevi yapılandırması-cihaz-bağlantısı** kullanın.

2. Terminal, adımla aldığı bağlantı dizesini kullanmak isteyip istemediğinizi sorar `task cloud-provision` . Ayrıca, ' Yeni oluştur... ' seçeneğine tıklayarak kendi cihaz Bağlantı dizenizi de girebilirsiniz.

3. Terminal sizden yapılandırma moduna girmenizi ister. Bunu yapmak için, düğme A ' yı basılı tutun ve sıfırlama düğmesini gönderin ve serbest bırakın. Bu ekranda DevKit KIMLIĞI ve ' Configuration ' görüntülenir.

   ![Giriş bağlantı dizesi](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. `task config-device-connection`Tamamlandıktan sonra `F1` vs Code komutları yüklemek ve seçmek için tıklayın `Arduino: Upload` . VS Code, Arduino taslağını doğrulamaya ve karşıya yüklemeye başlar.
  
   ![Arduino taslağı için doğrulama ve karşıya yükleme](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit yeniden başlatılır ve kodu çalıştırmaya başlar.

## <a name="test-the-project"></a>Projeyi test etme

Örnek uygulama çalıştığında, DevKit, Azure IoT uzaktan Izleme çözüm hızlandırıcısına WiFi üzerinden algılayıcı verileri gönderir. Sonucu görmek için şu adımları izleyin:

1. Azure IoT uzaktan Izleme çözüm hızlandırıcısına gidin ve **Pano**' ya tıklayın.

2. Uzaktan Izleme çözüm konsolunda, DevKit algılayıcısı durumunuzu görürsünüz.

   ![Azure IoT uzaktan Izleme çözüm hızlandırıcısında algılayıcı verileri](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Cihaz KIMLIĞINI Değiştir

Sabit kodlanmış **AZ3166** öğesini koddaki özelleştirilmiş BIR cihaz kimliğiyle değiştirmek istiyorsanız, [Uzaktan izleme örneğinde](/previous-versions/azure/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2)görüntülenecek kod satırını değiştirin.

## <a name="problems-and-feedback"></a>Sorunlar ve geri bildirim

Sorunlarla karşılaşırsanız [IoT Geliştirici seti SSS](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) bölümüne başvurun veya aşağıdaki kanalları kullanarak bize ulaşın:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Sonraki adımlar

Artık bir DevKit cihazını Azure IoT uzaktan Izleme çözüm hızlandırıcısına bağlamayı ve algılayıcı verilerini görselleştirmeyi öğrendiğinize göre, önerilen sonraki adımlar aşağıda verilmiştir:

* [Azure IoT çözüm hızlandırıcılarına genel bakış](/azure/iot-suite/)

* [IoT Geliştirici seti](https://microsoft.github.io/azure-iot-developer-kit/)