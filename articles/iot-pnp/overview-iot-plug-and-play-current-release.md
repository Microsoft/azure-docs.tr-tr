---
title: IoT Tak ve Kullan geçerli sürümü | Microsoft Docs
description: Geçerli IoT Tak ve Kullan sürümüne nelerin dahil olduğunu öğrenin.
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7b6669ca55dc9b94dc5d702e54b42011120b8812
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831885"
---
# <a name="what-is-in-the-current-iot-plug-and-play-release"></a>Geçerli IoT Tak ve Kullan sürümü nedir?

Bu makalede, geçerli IoT Tak ve Kullan sürümünü destekleyen araçlar, SDK 'Lar ve API 'Ler özetlenmektedir. Gösterilen sürüm numaraları, IoT Tak ve Kullan genel kullanıma sunulmakta olduğu zaman sürüm numarasını yansıtır. Sürüm numaraları sürümden sonra arttırılıp artmasını sağlayabilir.

## <a name="modeling-language"></a>Modelleme dili

[Dijital TWINS tanım dili (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl).

IoT Tak ve Kullan cihazlarının DTDL ile nasıl çalıştığı hakkında daha fazla bilgi edinmek için bkz. [ıot Tak ve kullan kuralları](concepts-convention.md).

## <a name="tools-and-utilities"></a>Araçlar ve Yardımcı Programlar

- Azure IoT Explorer 0.12.0.

    Daha fazla bilgi için bkz. [Azure IoT Explorer 'ı yüklemek ve kullanmak](howto-use-iot-explorer.md).

- VS Code uzantısı 1.0.0.

    Daha fazla bilgi için bkz. [DTDL yazma araçlarını yüklemek ve kullanmak](howto-use-dtdl-authoring-tools.md).

- Visual Studio 2019 Extension 1.0.0.

    Daha fazla bilgi için bkz. [DTDL yazma araçlarını yüklemek ve kullanmak](howto-use-dtdl-authoring-tools.md).

- Azure CLı IoT uzantısı 0.10.0.

    Azure IoT uzantısı, cihazların sertifikalandırmaya yardımcı olmak için komutlar içerir. Bkz. `az iot product -h`.

## <a name="libraries-and-sdks"></a>Kitaplıklar ve SDK’lar

Kitaplıklar ve SDK 'lar hakkında daha fazla bilgi edinmek için bkz. [IoT Için Microsoft sdk Tak ve kullan](libraries-sdks.md).

- C cihaz SDK [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md)
- Katıştırılmış C cihaz SDK [GitHub](https://github.com/Azure/azure-sdk-for-c/)
- .NET cihaz SDK 'Sı [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client)
- Java cihaz SDK 'Sı [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Python cihaz SDK [PIP 2.3.0](https://pypi.org/project/azure-iot-device/)
- Node.js cihaz SDK [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)
- .NET-IoT Hub Service [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- Java-IoT Hub hizmeti [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0)
- Node.js-IoT Hub hizmeti [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python-IoT Hub/dijital TWINS hizmeti [PIP 2.2.3](https://pypi.org/project/azure-iot-hub)
- DTDL model ayrıştırıcısı [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser).

## <a name="rest-apis"></a>REST API'leri

REST API [2020-09-30](/rest/api/iothub).

Daha fazla bilgi için bkz. [ıot Tak ve kullan Geliştirici Kılavuzu](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>IoT Hub

IoT Tak ve Kullan, tüm bölgelerde IoT Hub tarafından desteklenir. IoT Tak ve Kullan yalnızca standart veya ücretsiz katman IoT Hub 'ları tarafından desteklenir.

## <a name="announcements"></a>Duyurular

Geçerli ve önceki IoT Tak ve Kullan duyuruları için aşağıdaki blog gönderilerine bakın:

- [Genel Önizleme yenileme (29 Ağustos 2020 tarihinde gönderilir)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Cihazlarınızı IoT Tak ve Kullan hazırlayın ve onaylayın (26 Ağustos 2020 tarihinde gönderilir)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [IoT Tak ve Kullan artık önizleme sürümünde sunulmaktadır (22 Ağustos 2019 tarihinde gönderilir)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Azure IoT Central ve IoT Tak ve Kullan oluşturun (7 Mayıs 2019 tarihinde gönderilir)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

## <a name="next-steps"></a>Sonraki adımlar

Önerilen sonraki adım [ıot Tak ve Kullan nedir?](overview-iot-plug-and-play.md)konusunu gözden geçirmek mi?