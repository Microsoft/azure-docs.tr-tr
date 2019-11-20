---
title: Azure IoT cihaz SDK 'Ları platform desteği | Microsoft Docs
description: Açık kaynaklı cihaz SDK 'Ları, cihazları Azure IoT Hub ve cihaz sağlama hizmeti 'neC#(DPS) bağlamak için C, .net (), Java, Node. js ve Python 'da GitHub 'da kullanılabilir.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: d39fd95f8704650db16c1dc1553efaba2f727052
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889418"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Azure IoT cihaz SDK 'Ları platform desteği

Microsoft, Azure IoT Hub özellikli cihazların Universe sürekli olarak genişlemesine kadar çaba harcar. Microsoft, cihazların Azure IoT Hub ve cihaz sağlama hizmeti 'ne bağlanmasına yardımcı olmak için GitHub üzerinde açık kaynaklı cihaz SDK 'Ları yayımlar. Cihaz SDK 'Ları C, .NET (C#), Java, Node. js ve Python için kullanılabilir. Microsoft, [Microsoft SDK 'ları ve cihaz platformu desteği](#microsoft-sdks-and-device-platform-support) bölümünde bu yapılandırmanın desteklediği desteklenen yapılandırmalarda çalıştığından emin olmak için her SDK 'yı sınar.

Microsoft, cihaz SDK 'larının yanı sıra müşterilerin ve geliştiricilerin cihazlarını Azure IoT 'ye bağlanmasını sağlamak için çeşitli diğer hususlar sağlar:

* Microsoft, donanım platformları için Azure IoT C SDK 'Sı temel alınarak geliştirme setleri yayımlamasına yardımcı olmak üzere birkaç iş ortağı şirketi ile işbirliği yapar.

* Microsoft, Azure IoT için sınanmış ve sertifikalı bir sürekli genişleyen cihaz kümesi sağlamak üzere Microsoft güvenilir iş ortaklarıyla birlikte çalışır. Bu cihazların geçerli bir listesi için bkz. [IoT Için Azure Sertifikalı cihaz kataloğu](https://catalog.azureiotsolutions.com/).

* Microsoft, geliştiricilerin SDK 'ya kolayca SDK 'nın bağlantı noktası oluşturmasını sağlayan Azure IoT Hub Device C SDK 'sında bir platform soyutlama katmanı (PAL) sağlar. Daha fazla bilgi edinmek için bkz. [C SDK taşıma Kılavuzu](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

Bu konuda, Microsoft SDK 'Ları ve destekledikleri platform yapılandırmalarının yanı sıra yukarıda listelenen diğer seçeneklerin her biri hakkında bilgi verilmektedir.

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft SDK 'Ları ve cihaz platformu desteği

Microsoft, GitHub 'da açık kaynaklı SDK 'Ları şu diller için yayımlar: C, .NET (C#), Node. js, Java ve Python. SDK 'lar ve bağımlılıkları bu bölümde listelenmiştir. SDK 'lar, bu bağımlılıkları karşılayan herhangi bir cihaz platformunda desteklenir.

Listelenen her SDK 'nın her biri için Microsoft:

* , Çeşitli popüler platformlarda GitHub 'da ilgili SDK 'nın ana dalında sürekli olarak uçtan uca testler oluşturur ve çalıştırır.  Farklı derleyici sürümleri arasında test kapsamı sağlamak için genellikle en son LTS sürümüne ve en popüler sürüme karşı test ediyoruz.

* Uygulanabilirse yükleme kılavuzunu veya yükleme paketlerini sağlar.

* Açık kaynaklı kodla GitHub 'daki SDK 'Ları, müşteri katkılarına yönelik bir yolu ve GitHub sorunlarıyla birlikte ürün ekibi katılımını tam olarak destekler.

### <a name="c-sdk"></a>C SDK

[Azure IoT Hub C cihaz SDK 'sı](https://github.com/Azure/azure-iot-sdk-c) ile test edilmiştir ve aşağıdaki konfigürasyonları destekler.

| İşletim Sistemi                  | TLS kitaplığı                  | Ek gereksinimler                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL veya Yatassl | Berkeley Yuvaları</br></br>Taşınabilir Işletim sistemi arabirimi (POSIX)                       |
| iOS 12,2            | OpenSSL veya yerel OSX        | OSX 10.13.4 'te öykünülmüş XCode                                                               |
| Windows 10 ailesi   | SChannel                     |                                                                                             |
| Mbed OS 5,4         | Mbed TLS 2                   | [Mxyongaıot geliştirme seti](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere İşletim Sistemi     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |

### <a name="python-sdk"></a>Python SDK'sı

[Azure IoT Hub Python cihaz SDK 'sı](https://github.com/Azure/azure-iot-sdk-python) ile test edilmiştir ve aşağıdaki konfigürasyonları destekler.

| İşletim Sistemi                  | Derleyici                          |
|---------------------|-----------------------------------|
| Linux               | Python 2,7 *, 3,4*, 3,5 *, 3,6, 3,7 |
| MacOS High Sierra   | Python 2,7 *, 3,4*, 3,5 *, 3,6, 3,7 |
| Windows 10 ailesi   | Python 2,7 *, 3,4*, 3,5 *, 3,6, 3,7 |

\* Yalnızca Python sürüm 3.5.3 veya üzeri zaman uyumsuz API 'Leri destekliyorsa, 3,7 veya sonraki bir sürümünü kullanmanızı öneririz.

### <a name="net-sdk"></a>.NET SDK

[Azure IoT Hub .net (C#) cihaz SDK 'sı](https://github.com/Azure/azure-iot-sdk-csharp) ile test edilmiştir ve aşağıdaki konfigürasyonları destekler.

| İşletim Sistemi                                   | Standart                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 Masaüstü ve sunucu SKU 'Ları   | .NET Core 2,1, .NET Framework 4.5.1 veya .NET Framework 4,7 |

.NET SDK, [Azure cihaz Aracısı](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) veya [UWP uygulamalarıyla ILETIŞIM kurmak için RPC kullanan özel bir NTService](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/)ile Windows IoT Core ile de kullanılabilir.

### <a name="nodejs-sdk"></a>Node.js SDK'sı

[Azure IoT Hub Node. js cihaz SDK 'sı](https://github.com/Azure/azure-iot-sdk-node) ile test edilmiştir ve aşağıdaki konfigürasyonları destekler.

| İşletim Sistemi                  | Düğüm sürümü    |
|---------------------|-----------------|
| Linux               | LTS ve geçerli |
| Windows 10 ailesi   | LTS ve geçerli |

### <a name="java-sdk"></a>Java SDK

[Azure IoT Hub Java cihaz SDK 'sı](https://github.com/Azure/azure-iot-sdk-java) ile test edilmiştir ve aşağıdaki konfigürasyonları destekler.

| İşletim Sistemi                     | Java sürümü |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10 ailesi x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>İş ortağı tarafından desteklenen geliştirme setleri

Microsoft, birkaç mikro işlemci mimarisi için geliştirme setleri sağlamak üzere çeşitli iş ortaklarıyla çalışır. Bu iş ortakları, platformlarındaki Azure IoT C SDK 'sını geçiyor. İş ortakları SDK 'nın platform soyutlama katmanını (PAL) oluşturur ve korur. Microsoft, genişletilmiş destek sağlamak için bu iş ortaklarıyla birlikte çalışır.

| Partner             | Cihazlar                            | Bağlantı                     | Destek |
|---------------------|------------------------------------|--------------------------|---------|
| Espressıo           | ESP32 <br/> ESP8266                              | [ESP-Azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT modem     | [IoT SDK için Qualcomm LTE](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST mikro elektronik | STM32L4 serisi <br/> STM32F4 serisi <br/>  STM32F7 serisi <br/>  IoT düğümü için STM32L4 Discovery Kit    | [X-KÜP-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Destek](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF LaunchPad </br> CC3220S LaunchPad </br> CC3235SF LaunchPad </br> CC3235S LaunchPad </br> MSP432E4 LaunchPad | [SimpleLink için Azure IoT eklentisi](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E Forumu](https://e2e.ti.com) <br/> [CC3220 için tı E2E Forumu](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [MSP432E4 için tı E2E Forumu](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Microsoft Azure IoT C SDK 'Sı taşıma

Cihaz platformunuz önceki bölümlerden biri kapsamında değilse, Azure IoT C SDK 'Sı için taşıma yapmayı düşünebilirsiniz. C SDK 'Sı taşıma öncelikle SDK 'nın platform soyutlama katmanını (PAL) uygulamayı içerir. PAL, cihazınızı ve SDK 'daki daha üst düzey işlevleri arasında Tutkallamayı sağlayan temel türleri tanımlar. Daha fazla bilgi için bkz. [taşıma Kılavuzu](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Microsoft iş ortakları ve sertifikalı Azure IoT cihazları

Microsoft, Azure IoT test ve sertifikalı cihazlarla Azure IoT Universe 'ı sürekli bir şekilde genişletmek için çok sayıda iş ortaklarıyla çalışır.

* Azure IoT Sertifikalı cihazlara gitmek için bkz. [cihaz kataloğu IoT için Microsoft Azure Sertifikalı](https://catalog.azureiotsolutions.com/).

* Microsoft güvenilir iş ortakları hakkında daha fazla bilgi edinmek veya Microsoft 'un güvenilir iş ortağı olma hakkında bilgi edinmek için bkz. [Microsoft Azure sertifikalı nesnelerin interneti güvenilir Iş ortakları](https://azure.microsoft.com/marketplace/certified-iot-partners/).

## <a name="connecting-to-iot-hub-without-an-sdk"></a>SDK olmadan IoT Hub bağlanma

IoT Hub cihaz SDK 'Lardan birini kullanabiletirseniz, HTTPS istekleri ve yanıtları gönderip alma yeteneğine sahip herhangi bir uygulamadan [IoT Hub REST API 'lerini](https://docs.microsoft.com/rest/api/iothub/) kullanarak doğrudan IoT Hub bağlanabilirsiniz.

## <a name="support-and-other-resources"></a>Destek ve diğer kaynaklar

Azure IoT cihaz SDK 'larını kullanırken sorunlarla karşılaşırsanız, destek almak için çeşitli yollar vardır. Aşağıdaki kanallardan birini deneyebilirsiniz:

**Hataları raporlama** – cihaz SDK 'lerinde hatalar ilgili GitHub projesinin sorunlar sayfasında bildirilebilir. Düzeltmeler, ' deki projeden ürün güncelleştirmelerinde hızlı bir şekilde yapılır.

* [Azure IoT Hub C SDK sorunları](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT Hub .NET (C#) SDK sorunları](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Azure IoT Hub Java SDK sorunları](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Azure IoT Hub Node. js SDK sorunları](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Azure IoT Hub Python SDK sorunları](https://github.com/Azure/azure-iot-sdk-python/issues)

**Microsoft müşteri destek ekibi** – [Destek planına](https://azure.microsoft.com/support/plans/) sahip olan kullanıcılar, doğrudan [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)yeni bir destek isteği oluşturarak Microsoft müşteri destek ekibine geçebilir.

**Özellik istekleri** – Azure IoT özelliği Istekleri ürünün [Kullanıcı sesi sayfası](https://feedback.azure.com/forums/321918-azure-iot)aracılığıyla izlenir.

## <a name="next-steps"></a>Sonraki adımlar

* [Cihaz ve hizmet SDK’ları](iot-hub-devguide-sdks.md)
* [Taşıma Kılavuzu](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
