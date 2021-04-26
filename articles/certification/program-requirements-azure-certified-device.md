---
title: Azure Sertifikalı cihaz gereksinimleri
description: Azure Sertifikalı cihaz programı gereksinimleri
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Azure Certified Device Certification Requirements
ms.service: certification
ms.openlocfilehash: 497ffa4b3026491d6aa95df87708b3b1f2f1619e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308294"
---
# <a name="azure-certified-device-requirements"></a>Azure Sertifikalı cihaz gereksinimleri 
(daha önce IoT Hub olarak bilinirdi)

Bu belgede, Azure Sertifikalı cihaz kataloğunda temsil edilecek cihaza özgü yetenekler özetlenmektedir. Bir özellik, yazılım uygulaması veya yazılım ve donanım uygulamalarının birleşimi olabilecek tekil bir cihaz özniteliğidir. 

## <a name="program-purpose"></a>Program amacı

Microsoft, IoT 'yi basitleştirecek ve Azure Sertifikalı cihaz sertifikası, hiçbir cihaz türünün Azure IoT Hub güvenli bir şekilde sağlandığından emin olmak için temel sertifika programıdır.

Azure Sertifikalı cihaz sertifikası taahhüdüne:

1. IoT Hub ile birlikte çalışarak cihaz desteği telemetrisi
2.  Azure 'da güvenli bir şekilde sağlanmış cihaz sağlama hizmeti 'nin (DPS) cihaz desteği IoT Hub IoT Hub
3.  Cihaz, Kullanıcı katıştırılmış kodu yeniden derlemenize gerek kalmadan hedef DPS KIMLIK kapsamı aktarımının kolay girişini destekler.
4.  İsteğe bağlı olarak, buluta cihaz iletileri, doğrudan Yöntemler ve cihaz ikizi gibi diğer öğeleri doğrular 

## <a name="requirements"></a>Gereksinimler

**Istenir Cihazdan buluta: testin amacı, telemetri gönderen cihazların IoT Hub ile çalıştığından emin olmak için**

| **Ad**                | Azurecerbağlı. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Kullanıma sunuldu                                                |
| **Uygulama hedefi**          | Yaprak cihaz/kenar aygıtı                                      |
| **İşletim sistemi**                  | Belirsiz                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | Cihazın IoT Hub için herhangi bir telemetri şeması gönderebilmesi gerekir. Microsoft, testleri yürütmek için [Portal iş akışını](https://certify.azure.com/) sağlar. Cihazdan buluta (gerekli): **1.** Cihazın IoT Hub 2 tarafından yönetilen bir ileti gönderemediğini doğrular **.** Kullanıcının ileti sayısını ve sıklığını belirtmesi gerekir. **3.** AICS, Telemetriyi, hub örneği tarafından alındığını doğrular |
| **Kaynaklar**           | [Sertifika adımları](./overview.md) (tüm ek kaynaklara sahiptir) |

**Istenir DPS: testin amacı, cihazın uygulayıp uygulamadığını denetlerken, üç kanıtlama yönteminden biriyle cihaz sağlama hizmeti IoT Hub destekler**

| **Ad**                | Azurecerbağlı. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Yeni                                                          |
| **Uygulama hedefi**          | Herhangi bir cihaz                                                   |
| **İşletim sistemi**                  | Belirsiz                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | Cihaz, katıştırılmış kodu yeniden derlemek gerekmeden hedef DPS KIMLIK kapsamı sahipliğinin kolay girişi destekler. Microsoft, cihazın DPS **1 ' i** desteklediğini doğrulamak için sınamaları yürütmek üzere [Portal iş akışı](https://certify.azure.com) sağlar. Kullanıcı, kanıtlama yöntemlerinden birini (X. 509.440, TPM ve SAS anahtarı) 2 olarak seçmelidir **.** Kanıtlama yöntemine bağlı olarak, kullanıcının a gibi ilgili eylemi yapması gerekir **)** X. 509.952 CERT 'yi şirket IÇINDE yönetilen DPS kapsamına Yükle **b)** cihaza SAS anahtarı veya onay anahtarı uygulama |
| **Kaynaklar**           | [Cihaz sağlama hizmetine genel bakış](../iot-dps/about-iot-dps.md) |

**[Uygulanmışsa] Buluttan cihaza: testin amacı, iletilerin buluttan cihazlara gönderilebileceği şekilde olduğundan emin olmak için**                                                              

| **Ad**                | Azurecerbağlı. C2D                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Kullanıma sunuldu                                            |
| **Uygulama hedefi**          | Yaprak cihaz/kenar aygıtı                                                   |
| **İşletim sistemi**                  | Belirsiz                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | Cihazın IoT Hub 'den cihaz iletilerine bulut yapabilmesi gerekir. Microsoft, bu testleri yürütmek için [Portal iş akışını](https://certify.azure.com) sağlar. Buluttan cihaza (uygulanmışsa): **1.** Cihazın IoT Hub 2 ' den ileti alabileceğini doğrular **.** AICS rastgele ileti gönderir ve cihazdan ileti ACK aracılığıyla doğrular  |
| **Kaynaklar**           | **a)** [sertifika adımları](./overview.md) (tüm ek kaynaklara sahiptir) **b)** [bir IoT Hub bulut cihaz iletilerine gönderir](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Uygulanmışsa] Doğrudan Yöntemler: testin amacı, cihazların IoT Hub çalıştığından ve doğrudan metotları desteklediğinden emin olmak için kullanılır**

| **Ad**                | Azurecerbağlı. DirectMethods                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Kullanıma sunuldu                                            |
| **Uygulama hedefi**          | Yaprak cihaz/kenar aygıtı                                                   |
| **İşletim sistemi**                  | Belirsiz                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | Cihazın IoT Hub komut isteklerini alabilmesi ve yanıtlayabilmesi gerekir. Microsoft, testleri yürütmek için [Portal iş akışını](https://certify.azure.com) sağlar. Doğrudan Yöntemler (uygulanmışsa) **1.** Kullanıcının doğrudan metodun Yöntem yükünü belirtmesi. **2.** AICS belirtilen yük isteği, cihaz tarafından alınan merkez ve ACK iletisinden gönderildiğini doğrular |
| **Kaynaklar**           | **a)** [sertifika adımları](./overview.md) (tüm ek kaynaklara sahiptir) **b)** [IoT Hub doğrudan yöntemleri anlama](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Uygulanmışsa] Device ikizi özelliği: testin amacı, telemetri gönderen cihazların IoT Hub ile çalıştığından ve doğrudan Yöntemler ve Device ikizi özelliği gibi bazı IoT Hub özellikleri desteklediğinden emin olmak için geçerlidir.**

| **Ad**                                  | Azurecerbağlı. DeviceTwin                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği**                   | Kullanıma sunuldu                                            |
| **Uygulama hedefi**                            | Yaprak cihaz/kenar aygıtı                                                   |
| **İşletim sistemi**                                    | Belirsiz                                                     |
| **Doğrulama türü**                       | Otomatik                                                       |
| **Doğrulama**                            | Cihazın IoT Hub için herhangi bir telemetri şeması gönderebilmesi gerekir. Microsoft, testleri yürütmek için [Portal iş akışını](https://certify.azure.com) sağlar. Cihaz ikizi özelliği (uygulanmışsa) **1.** AICS, ikizi JSON 2 cihazında Read/Write özellikli özelliğini doğrular **.** Kullanıcının, değiştirilecek JSON yükünü belirtmesi ( **3).** AICS, cihaz tarafından alınan IoT Hub ve ACK iletisinden gönderilen istenen özellikleri doğrular |
| **Kaynaklar**                             | **a)** [sertifika adımları](./overview.md) (tüm ek kaynaklara sahiptir) **b)** [IoT Hub ile cihaz](../iot-hub/iot-hub-devguide-device-twins.md) iksi kullanın |
