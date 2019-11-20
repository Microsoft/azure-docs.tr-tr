---
title: Sertifika için Ready bir IoT Tak ve Kullan önizleme cihazı oluşturun | Microsoft Docs
description: Bir cihaz geliştiricisi olarak, sertifika için HAZIRAN IoT Tak ve Kullan önizleme cihazını nasıl oluşturabileceğiniz hakkında bilgi edinin.
author: tbhagwat3
ms.author: tanmayb
ms.date: 06/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e4dd5215812f0fd1a43afe0923601417bc8e6916
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569633"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Sertifika için hazırlamış bir IoT Tak ve Kullan önizleme cihazı oluşturun

Bu öğreticide, bir cihaz geliştiricisi olarak, sertifika için kullanılabilecek bir IoT Tak ve Kullan önizleme cihazı nasıl oluşturabileceğiniz açıklanmaktadır.

Sertifika sınamaları şunları denetler:

- IoT Tak ve Kullan cihaz kodunuz cihazınıza yüklendi.
- IoT Tak ve Kullan cihaz kodunuz Azure IoT SDK ile oluşturulmuştur.
- Cihaz kodunuz [Azure IoT Hub cihaz sağlama hizmetini](../iot-dps/about-iot-dps.md)destekler.
- Cihaz kodunuz cihaz bilgileri arabirimini uygular.
- Yetenek modeli ve cihaz kodu IoT Central birlikte çalışır.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

- [Visual Studio Code](https://code.visualstudio.com/download)
- [Vs Code Uzantı paketi Için Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

Ayrıca, hızlı başlangıçta oluşturduğunuz IoT Tak ve Kullan cihazının olması gerekir: cihaz [oluşturmak için bir cihaz yetenek modeli kullanın](quickstart-create-pnp-device.md).

## <a name="store-a-capability-model-and-interfaces"></a>Yetenek modeli ve arabirimleri depolayın

IoT Tak ve Kullan cihazlarda, cihazın yeteneklerini JSON dosyaları olarak tanımlayan bir yetenek modeli ve arabirimler yazmanız gerekir.

Bu JSON dosyalarını üç farklı konuma kaydedebilirsiniz:

- Ortak model deposu.
- Şirket modeli deponuz.
- Cihazınızda.

Şu anda, cihazınızı onaylamak için dosyaların şirket modeli deponuzda veya ortak model deposunda depolanması gerekir.

## <a name="include-the-required-interfaces"></a>Gerekli arabirimleri dahil et

Sertifika sürecini geçirmek için, **cihaz bilgileri** arabirimini yetenek modelinize dahil etmeniz ve uygulamanız gerekir. Bu arabirim aşağıdaki tanıma sahiptir:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> [Hızlı başlangıç: bir cihaz oluşturmak için bir cihaz yetenek modeli kullanın](quickstart-create-pnp-device.md), modelinize **cihaz bilgileri** arabirimini eklemiş oldunuz.

Cihaz modelinize **cihaz bilgileri** arabirimini dahil etmek için, arabirim kimliğini yetenek modelinin `implements` özelliğine ekleyin:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

**Cihaz bilgileri** arabirimini vs Code görüntülemek için:

1. Komut paletini açmak için **CTRL + SHIFT + P** tuşlarını kullanın.

1. **Tak ve kullan** girin ve ardından **IoT Tak ve kullan Open model deposu** komutunu seçin. **Ortak model deposunu aç**' ı seçin. Ortak model deposu VS Code açılır.

1. Ortak model deposunda, **arabirimler** sekmesini seçin, filtre simgesini seçin ve **cihaz bilgilerini** filtre alanına girin.

1. **Cihaz bilgileri** arabiriminin yerel bir kopyasını oluşturmak için filtrelenmiş listeden seçin ve ardından **İndir**' i seçin. VS Code arabirim dosyasını görüntüler.

Azure CLı kullanarak **cihaz bilgileri** arabirimini görüntülemek için:

1. [Azure ıOT CLI uzantısını yükler](howto-install-pnp-cli.md).

1. Aşağıdaki Azure CLı komutunu kullanarak cihaz bilgileri arabirim KIMLIĞIYLE bir arabirim gösterebilirsiniz:

    ```cmd/sh
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Daha fazla bilgi için bkz. [Azure CLI Için Azure IoT uzantısını yükleyip kullanma](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Cihaz kodunu Güncelleştir

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Azure IoT cihaz sağlama hizmeti (DPS) aracılığıyla cihaz sağlamayı etkinleştirme

Cihazı onaylamak için, [Azure IoT cihaz sağlama hizmeti (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)aracılığıyla sağlamayı etkinleştirmesi gerekir. DPS kullanma yeteneğini eklemek için, VS Code 'da C kodu saplaması oluşturabilirsiniz. Şu adımları uygulayın:

1. VS Code içinde DCM dosyası olan klasörü açın, **CTRL + SHIFT + P** tuşlarına basarak komut paletini açın, **IoT Tak ve kullan**girin ve **cihaz kodu saplama oluştur**' u seçin.

1. Cihaz kodu saplaması oluşturmak için kullanmak istediğiniz DCM dosyasını seçin.

1. Proje adını girin, bu, cihaz uygulamanızın adıdır.

1. Dil olarak **ANSI C** 'yi seçin.

1. Bağlantı yöntemi olarak, **DPS (cihaz sağlama hizmeti) ile simetrik anahtar** arasında seçim yapın.

1. Cihaz işletim sistemine bağlı olarak, Linux 'ta **CMake projesini Windows** veya **CMake** projesinde proje şablonu olarak seçin.

1. VS Code, oluşturulan cihaz kodu saplama dosyaları ile yeni bir pencere açar.

1. Kodu oluşturduktan sonra, uygulama için parametreler olarak DPS kimlik bilgilerini (**DPS kimlik kapsamı**, **DPS simetrik anahtar**, **cihaz kimliği**) girin. Sertifika portalından kimlik bilgilerini almak için bkz. [ıot Tak ve kullan cihazınızı bağlama ve test](tutorial-certification-test.md#connect-and-discover-interfaces)etme.

    ```cmd/sh
    .\your_pnp_app.exe [DPS ID Scope] [DPS symmetric key] [device ID]
    ```

### <a name="implement-standard-interfaces"></a>Standart arabirimleri Uygula

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Model bilgilerini ve SDK bilgi arabirimlerini uygulama

Azure IoT cihaz SDK 'Sı, model bilgilerini ve SDK bilgi arabirimlerini uygular. VS Code içinde kod üretme işlevini kullanırsanız, cihaz kodunuz IoT Tak ve Kullan cihaz SDK 'sını kullanır.

Azure IoT cihaz SDK 'sını kullanmayı tercih ederseniz, kendi uygulamanız için SDK kaynak kodunu başvuru olarak kullanabilirsiniz.

#### <a name="implement-the-device-information-interface"></a>Cihaz bilgileri arabirimini uygulama

Cihaza cihaz **bilgileri** arabirimini uygulayın ve çalışma zamanında cihazdan cihaza özgü bilgiler sağlayın.

[Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) Için **cihaz bilgisi** arabiriminin örnek bir uygulamasını başvuru olarak kullanabilirsiniz.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Modelinizde tanımlanan tüm özellikleri uygulayın

Sertifika sırasında, cihazınız, arabirimlerinde tanımlanan özellikleri uyguladığından emin olmak için programlı olarak test edilir. Cihazınız uygulamadıysanız okuma yazma özelliğine ve komut isteklerine yanıt vermek için 501 HTTP durum kodunu kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Artık IoT Tak ve Kullan bir cihaz oluşturduğunuzdan, önerilen sonraki adım şunları yapmanız gerekir:

> [!div class="nextstepaction"]
> [Cihazınızı nasıl doğrulayacağınızı öğrenin](tutorial-certification-test.md)
