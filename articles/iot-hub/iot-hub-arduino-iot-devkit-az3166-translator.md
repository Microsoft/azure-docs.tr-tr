---
title: Çeviriciyi w/Azure Işlevleri, bilişsel hizmetler, IoT DevKit yapın
description: Bir IoT DevKit üzerinde mikrofonu kullanarak bir sesli ileti alın ve ardından Ingilizce dilinde, çevrilmiş metinde işlenmek üzere Azure bilişsel hizmetler 'i kullanın
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: be26c6fe03dac9b9ff9dbff4a2bdce391ec0837e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96024172"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Azure Işlevleri ve bilişsel hizmetler ile IoT DevKit AZ3166 kullanarak bir dil Çeviricisi oluşturun

Bu makalede, Azure bilişsel [Hizmetler](https://azure.microsoft.com/services/cognitive-services/)'ı kullanarak IoT devkit 'in bir dil çeviricisi olarak nasıl yapılacağını öğreneceksiniz. Sesinizi kaydeder ve DevKit ekranında gösterilen Ingilizce metne çevirir.

[Mxyongaıot DevKit](https://aka.ms/iot-devkit) , zengin çevre birimleri ve sensörlerle birlikte hepsi bir arada bir uyumlu panotir. Visual Studio Code 'de [Azure IoT cihaz çalışma ekranı](https://aka.ms/iot-workbench) veya [Azure IoT araçları](https://aka.ms/azure-iot-tools) Uzantı paketi ' ni kullanarak bu BT için geliştirme yapabilirsiniz. [Projeler kataloğu](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) , IoT çözümlerini prototiplerinizde yardımcı olacak örnek uygulamalar içerir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu öğreticideki adımları tamamlayabilmeniz için önce aşağıdaki görevleri yapın:

* [IoT Hub IoT DEVKIT AZ3166 'e bağlanma bölümündeki adımları Bulutta Azure 'a](./iot-hub-arduino-iot-devkit-az3166-get-started.md)hazırlayın.

## <a name="create-azure-cognitive-service"></a>Azure bilişsel hizmeti oluşturma

1. Azure portal, **kaynak oluştur ' a** tıklayın ve **konuşma** için arama yapın. Konuşma Hizmeti oluşturmak için formu doldurun.
  ![Konuşma hizmeti](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Yeni oluşturduğunuz konuşma hizmetine gidin, **KEY1** for devkit 'e erişimi kopyalamak ve ona erişmek için **anahtarlar** Bölümü ' ne tıklayın.
  ![Anahtarları Kopyala](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Örnek projeyi aç

1. IoT DevKit 'in bilgisayarınıza **bağlı** olmadığından emin olun. Önce VS Code başlatın ve ardından DevKit 'i bilgisayarınıza bağlayın.

1. `F1`Komut paletini açmak için tıklayın, yazın ve **Azure IoT cihaz çalışma ekranı ' nı seçin: örnekleri aç...**. Ardından, pano olarak **IoT DevKit** ' i seçin.

1. IoT çalışma ekranı örnekleri sayfasında, **devkit çeviricisini** bulun ve **örnek aç**' a tıklayın. Ardından örnek kodu indirmek için varsayılan yolu seçer.
  ![Örnek Aç](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Azure Işlevleri ile konuşma hizmetini kullanma

1. VS Code ' a tıklayın `F1` , yazın ve **Azure IoT cihaz çalışma ekranı: Azure hizmetleri sağla...** ![ seçeneğini belirleyin. Azure hizmetlerini sağlama](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Azure IoT Hub ve Azure Işlevlerini sağlamayı tamamlayacak adımları izleyin.
   ![Adımları sağla](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Oluşturduğunuz Azure IoT Hub cihaz adını not edin.

1. `Functions\DevKitTranslatorFunction.cs`Aşağıdaki kod satırlarını açın ve not ettiğiniz cihaz adı ve konuşma hizmeti anahtarıyla güncelleştirin.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. `F1`Azure IoT cihaz çalışma ekranı ' na tıklayın, yazın ve seçin **: Azure 'a dağıt...**. VS Code yeniden dağıtım için onay isterse, **Evet**' e tıklayın.
   ![Dağıtım uyarısı](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Dağıtımın başarılı olduğundan emin olun.
   ![Dağıtım başarılı](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. Azure portal, **Işlevler uygulamalar** bölümüne gidin, yeni oluşturduğunuz Azure işlevi uygulamasını bulun. `devkit_translator`URL 'yi kopyalamak için **</> işlev URL 'sini al** ' a tıklayın.
   ![İşlev URL 'sini Kopyala](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. URL 'YI dosyaya yapıştırın `azure_config.h` .
   ![Azure yapılandırması](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Işlev uygulaması düzgün çalışmıyorsa, bu [SSS](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) bölümüne bakın.

## <a name="build-and-upload-device-code"></a>Cihaz kodunu derleme ve karşıya yükleme

1. DevKit 'i **yapılandırma moduna** şu şekilde değiştirin:
   * Düğme **A** tuşunu basılı tutun.
   * Basın ve serbest bırak **sıfırlama** düğmesi.

   Bu ekranda, DevKit KIMLIĞI ve **yapılandırmasını** görürsünüz.

   ![DevKit yapılandırma modu](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Tıklayın `F1` ve **Azure IoT cihaz çalışma ekranı: cihaz ayarlarını yapılandır... > config cihaz bağlantı dizesini** seçin. **IoT Hub cihaz bağlantı dizesi Seç** ' i seçerek devkit 'e yapılandırın.
   ![Bağlantı dizesini Yapılandır](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Başarılı bir şekilde tamamlandıktan sonra bildirimi görürsünüz.
   ![Bağlantı dizesi başarısını yapılandırma](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. `F1`Yeniden tıklayın, yazın ve **Azure IoT cihaz çalışma ekranı: Cihaz kodunu karşıya yükle**' yi seçin. Derlemeyi başlatır ve kodu DevKit 'e yükler.
   ![Cihaz karşıya yükleme](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Projeyi test etme

Uygulama başlatıldıktan sonra DevKit ekranındaki yönergeleri izleyin. Varsayılan kaynak dil Çincedir.

Çeviriye yönelik başka bir dil seçmek için:

1. Kurulum moduna girmek için A düğmesine basın.

2. Desteklenen tüm kaynak dillerini kaydırmak için B düğmesine basın.

3. Kaynak dili seçiminizi onaylamak için düğme A 'ya basın.

4. Konuşurken B düğmesini basılı tutun, ardından çeviriyi başlatmak için B düğmesini bırakın.

5. Çevrilmiş metin ekranda İngilizce dilinde görüntülenir.

![Dil Seç ' e kaydır](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Çeviri sonucu](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Çeviri sonucu ekranında şunları yapabilirsiniz:

- Kaynak dili kaydırıp seçmek için A ve B düğmelerine basın.

- Konuşmak için B düğmesine basın. Sesi gönderip çeviri metnini almak için B düğmesini bırakın.

## <a name="how-it-works"></a>Nasıl çalışır?

![Mini çözüm-sesle Tweet-diyagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

IoT DevKit, sesinizi kaydeder ve sonra Azure Işlevleri tetiklemeye yönelik bir HTTP isteği gönderir. Azure Işlevleri, çeviri yapmak için bilişsel hizmet konuşma çevirmeni API 'sini çağırır. Azure Işlevleri çeviri metnini aldıktan sonra, cihaza bir C2D iletisi gönderir. Ardından çeviri ekranda görüntülenir.

## <a name="problems-and-feedback"></a>Sorunlar ve geri bildirim

Sorunlarla karşılaşırsanız [IoT DevKit SSS](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) sayfasına bakın veya aşağıdaki kanalları kullanarak bize ulaşın:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Sonraki adımlar

IoT DevKit 'in Azure Işlevleri ve bilişsel hizmetler 'i kullanarak çevirmen olarak nasıl kullanılacağını öğrendiniz. Bu nasıl yapılır bölümünde şunları öğrendiniz:

> [!div class="checklist"]
> * Visual Studio Code görevi kullanarak bulut sağlamasını otomatikleştirme
> * Azure IoT cihaz bağlantı dizesini yapılandırma
> * Azure Işlevini dağıtma
> * Sesli mesaj çevirisini test etme

Daha fazla bilgi edinmek için Diğer öğreticilere ilerleyin:

> [!div class="nextstepaction"]
> [IoT DevKit AZ3166 'i Azure IoT uzaktan Izleme çözüm hızlandırıcısına bağlama](./iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring.md)