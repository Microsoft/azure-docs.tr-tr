---
title: Mxyongaıot DevKit Kullanıcı ıŞıĞıNı denetlemek için Azure cihaz TWINS kullanma | Microsoft Docs
description: Bu öğreticide, DevKit durumlarını izlemeyi ve Azure IoT Hub cihaz TWINS ile Kullanıcı ıŞıĞıNı denetlemeyi öğrenin.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: deb1ea8c7b41ad48bddebfbed1b15c667ee0071a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73483941"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Bu örneği Mxyonga IoT DevKit WiFi bilgilerini ve algılayıcı durumlarını izlemek ve Azure IoT Hub cihaz TWINS 'i kullanarak kullanıcının rengini denetlemek için kullanabilirsiniz.

## <a name="what-you-learn"></a>Öğrenecekleriniz

- Mxyongaıot DevKit algılayıcı durumlarını izleme.

- DevKit 'in RGB 'nin rengini denetlemek için Azure cihaz TWINS 'i kullanma.

## <a name="what-you-need"></a>Ne gerekiyor

- [Başlarken Kılavuzunu](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)izleyerek geliştirme ortamınızı ayarlayın.

- GitBash Terminal pencerenize (veya başka bir git komut satırı arabiriminden) aşağıdaki komutları yazın:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Azure hizmetlerini sağlama

1. Visual Studio Code ' deki **Görevler** açılır menüsüne tıklayın ve **görevi çalıştır...**  - **bulut sağlama**' yı seçin.

2. İlerleme durumu, **hoş geldiniz** panelinin **TERMINAL** sekmesi altında görüntülenir.

3. *Hangi aboneliğin seçmek istediğiniz*iletiyle ilgili sorulduğunda bir abonelik seçin.

4. Bir kaynak grubu seçin veya seçin. 
 
   > [!NOTE]
   > Zaten ücretsiz bir IoT Hub varsa, bu adımı atlayabilirsiniz.

5. *Hangi IoT Hub 'ını seçmek*istediğinizi iletiyle birlikte sorulduğunda IoT Hub seçin veya oluşturun.

6. İşlev uygulamasına benzer bir şey *: işlev uygulaması adı: xxx*, görüntülenir. İşlev uygulama adını yazın; Bu, sonraki bir adımda kullanılacaktır.

7. Azure Resource Manager şablonu dağıtımının bitmesini bekleyin, bu, ileti *Kaynak Yöneticisi Şablon dağıtımı: bitti* görüntüleniyorsa gösterilir.

## <a name="deploy-function-app"></a>İşlev Uygulaması dağıt

1. Visual Studio Code ' deki **Görevler** açılır menüsüne tıklayın ve **görevi çalıştır...**  - **bulut-dağıtma**' yı seçin.

2. İşlev uygulaması kodu karşıya yükleme işleminin bitmesini bekleyin; uygulama dağıtımı *: bitti ileti işlevi* görüntülenir.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>DevKit 'te cihaz bağlantı dizesi IoT Hub yapılandırma

1. Mxyongaıot DevKit 'nizi bilgisayarınıza bağlayın.

2. Visual Studio Code ' deki **Görevler** açılır menüsüne tıklayın ve **görevi çalıştır...**  - **yapılandırma-cihaz-bağlantı** ' yı seçin.

3. Mxyonun IoT DevKit ' te, **bir düğme a**'ya basın ve basılı tutun, **Sıfırla** düğmesine basın ve ardından **A** düğmesine basarak masaüstü oluşturma modunu yapılandırma moduna alın.

4. Bağlantı dizesi yapılandırma işleminin tamamlanmasını bekleyin.

## <a name="upload-arduino-code-to-devkit"></a>Arduino kodunu DevKit 'e yükle

Mxyonun IoT DevKit ile bilgisayarınıza bağlı:

1. Visual Studio Code ' deki **Görevler** açılır menüsüne tıklayın ve **Yapı görevini Çalıştır..** . seçeneğini belirleyin. Arduino taslağı derlenir ve DevKit 'e yüklenir.

2. Taslak başarıyla karşıya yüklendiğinde, bir *derleme & taslağı karşıya yükle: başarı* iletisi görüntülenir.

## <a name="monitor-devkit-state-in-browser"></a>DevKit durumunu tarayıcıda izle

1. Bir Web tarayıcısında, ihtiyacınız olan adım sırasında oluşturulan `DevKitState\web\index.html` dosyasını açın.

2. Aşağıdaki Web sayfası görüntülenir:![İşlev uygulaması adını belirtin.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Daha önce yazdığınız işlev uygulaması adını girin.

4. **Bağlan** düğmesine tıklayın

5. Birkaç saniye içinde, sayfa yenilenir ve DevKit 'in WiFi bağlantısı durumunu ve ekleme sensörlerinden her birinin durumunu görüntüler.

## <a name="control-the-devkits-user-led"></a>DevKit 'in Kullanıcı ıŞıĞıNı denetleme

1. Web sayfası çiziminde Kullanıcı tarafından LED grafiğine tıklayın.

2. Birkaç saniye içinde, ekran yenilenir ve kullanıcının geçerli renk durumunu gösterir.

3. RGB kaydırıcı denetimlerinde çeşitli konumlara tıklayarak RGB 'nin renk değerini değiştirmeyi deneyin.

## <a name="example-operation"></a>Örnek işlem

![Örnek test yordamı](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Cihaz ikizi ham verilerini Azure portal: IoT Hub-\> IoT cihazları-\> *cihaz*\<\>cihaz ikizi -.\>

## <a name="next-steps"></a>Sonraki adımlar

Nasıl yapılacağını öğrendiniz:
- Bir Mxyonga IoT DevKit cihazını Azure IoT uzaktan Izleme çözüm hızlandırıcısına bağlayın.
- Devkit 'in RGB 'nin rengini algılaması ve denetlemek için Azure IoT cihaz ikizlerini işlevini kullanın.

Önerilen sonraki adımlar aşağıda verilmiştir:

* [Azure IoT uzaktan Izleme çözüm hızlandırıcısının genel bakış](https://docs.microsoft.com/azure/iot-suite/)
* [Bir Mxyonga IoT DevKit cihazını Azure IoT Central uygulamanıza bağlama](/azure/iot-central/core/howto-connect-devkit)
