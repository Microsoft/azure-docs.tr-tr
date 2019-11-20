---
title: Ortak arabirimler-IoT Tak ve Kullan önizlemesi | Microsoft Docs
description: IoT Tak ve Kullan geliştiricileri için ortak arabirimlerin açıklaması
author: ChrisGMsft
ms.author: chrisgre
ms.date: 09/08/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 2eae778230fa5fce1be095106a02b2b643ff436e
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935336"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT Tak ve Kullan önizleme ortak arabirimleri

Tüm IoT Tak ve Kullan cihazlarının bazı ortak arabirimleri uygulaması beklenir. Ortak arabirimler, tutarlı işlevler sağladığından IoT çözümlerinin avantajına sahiptir. [Sertifika](tutorial-build-device-certification.md) , cihazınızın birkaç ortak arabirimi uygulamasını gerektirir. Ortak model deposundan ortak arabirim tanımlarını elde edebilirsiniz.

## <a name="summary-of-common-interfaces"></a>Ortak arabirimlerin Özeti

| Name | ID | Açıklama | Azure IoT SDK tarafından uygulandı | Yetenek modelinde bildirilmelidir |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Model bilgileri | urn: azureiot: ModelDiscovery: ModelInformation: 1 | Cihazların yetenek modeli KIMLIĞINI ve arabirimlerini bildirmesi için. Tüm IoT Tak ve Kullan cihazları için gereklidir. | Evet | Hayır |
| Digital Ikizi Istemci SDK bilgileri | urn: azureiot: Client: Sdkınformation: 1 | Cihazı Azure 'a bağlamak için istemci SDK. [Sertifika](tutorial-build-device-certification.md) için gerekli | Evet | Hayır |
| Cihaz bilgisi | urn: azureiot: DeviceManagement: Deviceınformation: 1 | Cihazla ilgili donanım ve işletim sistemi bilgileri. [Sertifika](tutorial-build-device-certification.md) için gerekli | Hayır | Evet |
| Model tanımı | urn: azureiot: ModelDiscovery: ModelDefinition: 1 | Cihazların yetenek modeli ve arabirimleri için tam tanımı bildirme. Model tanımları bir model deposunda barındırılamadıklarında uygulanmamalıdır. | Hayır | Evet |
| Dijital Ikizi | urn: azureiot: ModelDiscovery: DigitalTwin: 1 | Çözüm geliştiricilerinin dijital bir ikizi için yetenek modeli KIMLIĞI ve arabirim kimliklerini alması için. Bu arabirim bir IoT Tak ve Kullan cihazı tarafından bildirilmemiş veya uygulanmadı. | Hayır | Hayır |

- Azure IoT SDK tarafından uygulanan-Azure IoT SDK 'sının arabirimlerde belirtilen özellikleri uygulayıp uygulamadığını belirtir. Azure IoT SDK 'sını kullanan IoT Tak ve Kullan cihazlarının bu arabirimi uygulaması gerekmez.
- Yetenek modelinde bildirilmelidir-Eğer ' Yes ' ise bu arabirim, bu IoT Tak ve kullan cihazının cihaz yeteneği modelinin `"implements":` bölümü içinde bildirilmelidir.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Genel depodan arabirim tanımlarını al

### <a name="cli"></a>CLI

Ortak model deposundan ortak arabirimleri almak için Azure CLı için Azure IoT uzantısı 'nı kullanabilirsiniz.

```cmd/sh
az iot pnp interface show --interface {InterfaceID}
```

```cmd/sh
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS Code

1. Komut paletini açmak için **CTRL + SHIFT + P** tuşlarını kullanın.

1. **Tak ve kullan** girin ve ardından **IoT Tak ve Kullan seçin: Model deposu** komutunu açın. **Ortak depoyu**seçin. Ortak model deposu VS Code açılır.

1. Ortak model deposunda, ara alanına arabirim adını girin.

1. Arabirimin yerel bir kopyasını oluşturmak için arama sonuçlarında bunu seçin ve ardından **İndir**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Artık ortak arabirimler hakkında bilgi edindiğinize göre, bazı ek kaynaklar aşağıda verilmiştir:

- [Digital Ikizi tanım dili (DTDL)](https://aka.ms/DTDL)
- [C cihaz API’si](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
