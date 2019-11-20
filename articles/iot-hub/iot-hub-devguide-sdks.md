---
title: Azure IoT SDK 'larını anlayın | Microsoft Docs
description: Geliştirici Kılavuzu-cihaz uygulamaları ve arka uç uygulamaları oluşturmak için kullanabileceğiniz çeşitli Azure IoT cihaz ve hizmet SDK 'Ları hakkında bilgi ve bağlantılar.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 22734d29973f0d7102307aa9038b6ebe7cc2521b
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903322"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Azure IoT Hub SDK 'larını anlama ve kullanma

IoT Hub ile çalışmak için yazılım geliştirme setlerinin (SDK) iki kategorisi vardır:

* **IoT Hub cihaz SDK 'ları** , cihaz istemcisi veya modül Istemcisi kullanarak IoT cihazlarınızda çalışan uygulamalar oluşturmanıza olanak tanır. Bu uygulamalar IoT hub’ınıza telemetri gönderir ve isteğe bağlı olarak iletileri, işleri, yöntemleri veya ikiz güncelleştirmeleri IoT hub’ınızdan alır.  Modül istemcisini, [Azure IoT Edge çalışma zamanı](../iot-edge/about-iot-edge.md)için [modülleri](../iot-edge/iot-edge-modules.md) yazmak üzere de kullanabilirsiniz.

* **IoT Hub hizmet SDK 'ları** , IoT Hub 'ınızı yönetmek için arka uç uygulamaları oluşturmanızı ve isteğe bağlı olarak ileti göndermenizi, işleri zamanlamayı, doğrudan yöntemleri çağırmayı veya IoT cihazlarınıza veya modüllerinize istenen özellik güncelleştirmelerini göndermenizi sağlar.

Ayrıca, [cihaz sağlama hizmeti](../iot-dps/about-iot-dps.md)ile çalışmaya yönelik bir SDK kümesi de sunuyoruz.
* **Cihaz SDK 'Larını sağlamak** , cihaz sağlama hizmeti ile iletişim kurmak için IoT cihazlarınızda çalışan uygulamalar oluşturmanıza olanak tanır.

* **Sağlama hizmeti SDK 'ları** , cihaz sağlama hizmeti 'nde kayıtlarınızı yönetmek için arka uç uygulamaları oluşturmanızı sağlar.

[Azure IoT SDK 'larını kullanarak geliştirme avantajları](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)hakkında bilgi edinin.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>İşletim sistemi platformu ve donanım uyumluluğu

SDK 'lar için desteklenen platformlar [Azure IoT SDK 'Ları platformu desteği](iot-hub-device-sdk-platform-support.md)' nde bulunabilir.

Belirli donanım cihazlarıyla SDK uyumluluğu hakkında daha fazla bilgi için bkz. [IoT Için Azure Sertifikalı cihaz kataloğu](https://catalog.azureiotsolutions.com/) veya tek depo.

## <a name="azure-iot-hub-device-sdks"></a>Azure IoT Hub cihaz SDK 'Ları

Microsoft Azure IoT cihaz SDK 'Ları, Azure IoT Hub hizmetleri tarafından yönetilen ve yönetilen uygulamaları oluşturmayı kolaylaştıran kod içerir.

.NET için Azure IoT Hub cihaz SDK 'Sı: 

* [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)'den indirin.  Ad alanı, IoT Hub cihaz Istemcileri (DeviceClient, ModuleClient) içeren Microsoft. Azure. Devices. clients ' dır.
* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-csharp)
* [API başvurusu](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Modül başvurusu](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

C için Azure IoT Hub cihaz SDK 'Sı (ANSI C-C99):

* [Apt-get, MBED, Arduino IDE veya iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries) 'tan yüklemesi
* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-c)
* [C cihaz SDK 'sını derle](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [API başvurusu](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Modül başvurusu](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [C SDK 'sını diğer platformlara taşıma](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* Çapraz derleme, farklı platformlarda çalışmaya başlama vb. hakkında bilgi için [geliştirici belgeleri](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) .
* [Azure IoT Hub C SDK kaynak tüketim bilgileri](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Java için Azure IoT Hub cihaz SDK 'Sı: 

* [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) projesine ekle
* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-java)
* [API başvurusu](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Modül başvurusu](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Node. js için Azure IoT Hub cihaz SDK 'Sı: 

* [NPM](https://www.npmjs.com/package/azure-iot-device) 'den yüklensin
* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-node)
* [API başvurusu](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Modül başvurusu](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Python için Azure IoT Hub cihaz SDK 'Sı: 

* [PIP](https://pypi.org/project/azure-iot-device/) 'den yüklensin
* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-python)
* [API başvurusu](https://docs.microsoft.com/python/api/azure-iot-device)

İOS için Azure IoT Hub cihaz SDK 'Sı: 

* [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) adresinden Install
* [Örnekler](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API başvurusu: bkz. [C API başvurusu](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Azure IoT Hub hizmeti SDK 'Ları

Azure IoT hizmeti SDK 'Ları, cihazları ve güvenliği yönetmek için IoT Hub doğrudan etkileşim kuran uygulamalar oluşturmayı kolaylaştırmaya yönelik kod içerir.

.NET için Azure IoT Hub hizmeti SDK 'Sı:

* [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/)'den indirin.  Ad alanı, IoT Hub hizmet Istemcileri (RegistryManager, ServiceClients) içeren Microsoft. Azure. Devices.
* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-csharp)
* [API başvurusu](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Java için Azure IoT Hub Service SDK: 

* [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projesine ekle
* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-java)
* [API başvurusu](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Node. js için Azure IoT Hub hizmeti SDK 'Sı: 

* [NPM](https://www.npmjs.com/package/azure-iothub) 'den indir
* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-node)
* [API başvurusu](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Python için Azure IoT Hub Service SDK: 

* [PIP](https://pypi.python.org/pypi/azure-iothub-service-client/) 'den indir
* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated)

C için Azure IoT Hub hizmeti SDK 'Sı: 

* [Apt-get, MBED, Arduino IDE veya NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md) 'den indirin
* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-c)

İOS için Azure IoT Hub Service SDK: 

* [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient) adresinden Install
* [Örnekler](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Geliştirme makinenize ikili dosyaları ve bağımlılıkları yüklemek üzere dil ve platforma özel paket yöneticileri kullanma hakkında bilgi için GitHub depolarındaki Benioku dosyalarına bakın.

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure sağlama SDK 'Ları

**Microsoft Azure sağlama SDK 'ları** , [cihaz sağlama hizmeti](../iot-dps/about-iot-dps.md)'ni kullanarak IoT Hub cihazları sağlamanıza olanak tanır.

İçin C#Azure sağlama cihaz ve hizmet SDK 'ları:

* NuGet 'den [CIHAZ SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) ve [Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) 'dan indirin.
* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-csharp/)
* [API başvurusu](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

C için Azure sağlama cihaz ve hizmet SDK 'Ları:

* [Apt-get, MBED, Arduino IDE veya iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries) 'tan yüklemesi
* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [API başvurusu](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Java için Azure sağlama cihaz ve hizmet SDK 'Ları:

* [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projesine ekle
* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [API başvurusu](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Node. js için Azure sağlama cihaz ve hizmet SDK 'Ları:

* [Kaynak kod](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [API başvurusu](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* NPM 'den [CIHAZ SDK](https://badge.fury.io/js/azure-iot-provisioning-device) ve [hizmet SDK 'sını](https://badge.fury.io/js/azure-iot-provisioning-service) indirin

Python için Azure sağlama cihaz ve hizmet SDK 'Ları:

* [Kaynak kod](https://github.com/Azure/azure-iot-device)
* PIP 'den [CIHAZ SDK](https://pypi.org/project/azure-iot-device/) 'Sı ve [hizmet SDK 'sını](https://pypi.org/project/azure-iothub-provisioningserviceclient/) indirin

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT SDK 'Ları, geliştirmeye yardımcı olmak için bir araç kümesi de sağlar:
* [ıothub-Diagnostics](https://github.com/Azure/iothub-diagnostics): IoT Hub bağlantıyla ilgili sorunları tanılamaya yardımcı olan platformlar arası komut satırı aracı.
* [Azure-IoT-Explorer](https://github.com/Azure/azure-iot-explorer): IoT Hub bağlanmak ve IoT cihazlarıyla eklemek/yönetmek/iletişim kurmak için platformlar arası bir masaüstü uygulaması.

Azure IoT SDK 'Ları kullanılarak geliştirmeyle ilgili ilgili belgeler:
* IoT Hub SDK 'Ları kullanarak [bağlantı ve güvenilir mesajlaşma yönetimi](iot-hub-reliability-features-in-sdks.md) hakkında bilgi edinin.
* İOS ve Android gibi [mobil platformlar için geliştirme](iot-hub-how-to-develop-for-mobile-devices.md) hakkında bilgi edinin.
* [Azure IoT SDK Platformu desteği](iot-hub-device-sdk-platform-support.md)


Bu IoT Hub geliştirici kılavuzundaki diğer başvuru konuları şunları içerir:

* [IoT Hub uç noktaları](iot-hub-devguide-endpoints.md)
* [Cihaz TWINS, işler ve ileti yönlendirme için sorgu dili IoT Hub](iot-hub-devguide-query-language.md)
* [Kotalar ve azaltma](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT desteği](iot-hub-mqtt-support.md)
* [IoT Hub REST API başvurusu](/rest/api/iothub/)
