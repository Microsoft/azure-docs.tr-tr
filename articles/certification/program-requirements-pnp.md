---
title: IoT Tak ve Kullan sertifika gereksinimleri
description: IoT Tak ve Kullan sertifika programı gereksinimleri
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: IoT Plug and Play Certification Requirements
ms.service: certification
ms.openlocfilehash: b26fab6f8b92e3cb996f545f1f6201d32b1eaced
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310521"
---
# <a name="iot-plug-and-play-certification-requirements"></a>IoT Tak ve Kullan sertifika gereksinimleri

Bu belgede, Azure IoT cihaz kataloğunda temsil edilecek cihaza özgü yetenekler özetlenmektedir. Bir özellik, yazılım uygulaması veya yazılım ve donanım uygulamalarının birleşimi olabilecek tekil bir cihaz özniteliğidir.

## <a name="program-purpose"></a>Program amacı

IoT Tak ve Kullan önizlemesi, çözüm oluşturucuların akıllı cihazları el ile herhangi bir yapılandırma olmadan çözümleriyle tümleştirmelerini sağlar. IoT Tak ve Kullan 'nin temel tarafında, bir cihazın, yeteneklerini IoT Tak ve Kullan özellikli bir uygulamaya tanıtmak için kullandığı bir cihaz modelidir. Bu model bir dizi öğe olarak yapılandırılmış: telemetri, Özellikler ve komutlar.

IoT Tak ve Kullan sertifikası Promise:

1.  Tanımlanan cihaz modelleri ve arabirimleri [dijital Ikizi tanım diliyle](https://github.com/Azure/opendigitaltwins-dtdl) uyumludur  
2.  Cihaz sağlama hizmetlerinde KIMLIK kapsamı sahipliğinin güvenli sağlaması ve kolay aktarılması
3.  [Digital Ikizi API 'lerini](../iot-pnp/concepts-digital-twin.md) kullanarak Azure IoT tabanlı çözümlerle kolay tümleştirme: Azure IoT Hub ve Azure IoT Central
4.  Sertifikalı cihazlarda doğrulanan ürün gerçeği

## <a name="requirements"></a>Gereksinimler

**Istenir Cihazdan buluta: testin amacı, telemetri gönderen cihazların IoT Hub ile çalıştığından emin olmak için**

| **Ad**                | Iotpnp. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Kullanıma sunuldu                                                |
| **Uygulama hedefi**          | Yaprak cihaz/kenar aygıtı                                      |
| **İşletim sistemi**                  | Belirsiz                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | Cihazın IoT Hub için herhangi bir telemetri şeması gönderebilmesi gerekir. Microsoft, testleri yürütmek için [Portal iş akışını](https://certify.azure.com) sağlar. Cihazdan buluta (gerekli): **1.** Cihazın IoT Hub 2 tarafından yönetilen bir ileti gönderemediğini doğrular **.** Kullanıcının ileti sayısını ve sıklığını belirtmesi gerekir. **3.** AICS, Telemetriyi, hub örneği tarafından alındığını doğrular |
| **Kaynaklar**           | [Sertifika adımları](./overview.md) (tüm ek kaynaklara sahiptir) |

**Istenir DPS: testin amacı, cihazın uygulayıp uygulamadığını denetlerken, üç kanıtlama yönteminden biriyle cihaz sağlama hizmeti IoT Hub destekler**

| **Ad**                | Iotpnp. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Kullanıma sunuldu                                                |
| **Uygulama hedefi**          | Herhangi bir cihaz                                                   |
| **İşletim sistemi**                  | Belirsiz                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | Cihazın, ekli kodu yeniden derlemek gerekmeden DPS KIMLIK kapsamı sahipliğinin kolayca aktarımını uygulaması gerekir. Microsoft, cihazın DPS **1 ' i** desteklediğini doğrulamak için sınamaları yürütmek üzere [Portal iş akışı](https://certify.azure.com) sağlar. Kullanıcı, kanıtlama yöntemlerinden birini (X. 509.440, TPM ve SAS anahtarı) 2 olarak seçmelidir **.** Kanıtlama yöntemine bağlı olarak, kullanıcının a gibi ilgili eylemi yapması gerekir **)** X. 509.952 CERT 'yi şirket IÇINDE yönetilen DPS kapsamına Yükle **b)** cihaza SAS anahtarı veya onay anahtarı uygulama |
| **Kaynaklar**           | **a) bir** [cihaz sağlama hizmetine genel bakış](../iot-dps/about-iot-dps.md), **b)** [DPS kimlik kapsamı aktarımı için örnek yapılandırma dosyası](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview-pnp/serializer/samples/devicetwin_simplesample) |

**Istenir DTDL v2: tanımlanan cihaz modellerinin ve arabirimlerinin, dijital TWINS tanım dili v2 ile uyumlu olduğundan emin olmak için testin amacı.**                                                              

| **Ad**                | Iotpnp. DTDL                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Kullanıma sunuldu                                                |
| **Uygulama hedefi**          | Herhangi bir cihaz                                                   |
| **İşletim sistemi**                  | Belirsiz                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | [Portal iş akışı](https://certify.azure.com) şunları doğrular: **1.** Model KIMLIĞI duyurusu ve cihazın WebSockets Protokolü 2 üzerinden MQTT veya MQTT kullanılarak bağlı olduğundan emin olun **.** Modeller DTDL v2 3 ile uyumludur **.** Telemetri, Özellikler ve komutlar, cihazdaki IoT Hub Digital Ikizi ile Device Ikizi arasında düzgün şekilde uygulanır ve etkileşime geçin |
| **Kaynaklar**           | [Genel Önizleme güncelleştirmelerini yenileme](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Istenir Cihaz modelleri ortak model deposunda yayımlandı**

| **Ad**                | Iotpnp. ModelRepo                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Kullanıma sunuldu                                                |
| **Uygulama hedefi**          | Herhangi bir cihaz                                                   |
| **İşletim sistemi**                  | Belirsiz                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | Tüm cihaz modellerinin ortak depoda yayımlanması gerekir. Cihaz modelleri, ortak depo 1 ' de kullanılabilen modeller aracılığıyla çözümlenir **.** Sertifika için göndermeden önce kullanıcının modelleri ortak depoya el ile yayımlaması gerekir. **2.** Modeller yayımlandıktan sonra, sabit olduğunu unutmayın. Yalnızca modeller ve katıştırılmış cihaz kodu sonlandırıldığında yayımlamayı kesinlikle öneririz. * 1 * 1 Kullanıcı, model deposu 3 ' te yayımlandıktan sonra modelleri iptal etmek için Microsoft desteğiyle iletişim kurmanız gerekir **.** Cihaz sertifika hizmetine bağlıyken, [Portal iş akışı](https://certify.azure.com) ortak depodaki modellerin varlığını denetler |
| **Kaynaklar**           | [Model deposu](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Istenir GSG kullanarak fiziksel cihaz doğrulaması**

| **Ad**                                  | Iotpnp. PhysicalDevice                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği**                   | Kullanıma sunuldu                                                |
| **Uygulama hedefi**                            | Herhangi bir cihaz                                                   |
| **İşletim sistemi**                                    | Belirsiz                                                     |
| **Doğrulama türü**                       | El ile                                                       |
| **Doğrulama**                            | [iotcert@microsoft.com](mailto:iotcert@microsoft.com)Fiziksel cihazda ek doğrulamalar gerçekleştirmek üzere düzenlemeler yapmak için iş ortakları Microsoft Contact () ile birlikte çalışmalıdır. COVıD-19 durumu nedeniyle, cihazı Microsoft 'a teslim etmeden fiziksel cihaz doğrulaması gerçekleştirmek için çeşitli yollar araştırıyoruz. |
| **Kaynaklar**                             | Ayrıntılar daha sonra kullanılabilir                                 |
| **Azure önerilir**       | Yok    |

**[Uygulanmışsa] Cihaz bilgisi arabirimi: testin amacı cihaz bilgileri arabiriminin cihaz kodunda düzgün şekilde uygulandığını doğrulamaktır**

| **Ad**                | Iotpnp. Deviceınfoınterface                                   |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Kullanıma sunuldu                                                |
| **Uygulama hedefi**          | Herhangi bir cihaz                                                   |
| **İşletim sistemi**                  | Belirsiz                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | [Portal iş akışı](https://certify.azure.com) , cihaz kodu uygulayan cihaz bilgisi arabirimi **1 ' i doğrular.** Değerin cihaz kodu tarafından IoT Hub 2 ' ye yayıldığını denetler **.** Arabirimin DCM 'de uygulandığını denetler (Bu uygulama DTDL v2 'de değişir) **3.** Denetimlerin özellikleri yazma özellikli değil (salt okuma) **4.** Şema türünün dize ve/veya uzun ve null olduğunu denetler |
| **Kaynaklar**           | [Microsoft tanımlı arabirim](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |
| **Azure önerilir**  | Yok                                                          |

**[Uygulanmışsa] Buluttan cihaza: testin amacı, iletilerin buluttan cihazlara gönderilebileceği şekilde olduğundan emin olmak için**

| **Ad**                | Iotpnp. C2D                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Kullanıma sunuldu                                                |
| **Uygulama hedefi**          | Yaprak cihaz/kenar aygıtı                                      |
| **İşletim sistemi**                  | Belirsiz                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | Cihazın IoT Hub 'den cihaz iletilerine bulut yapabilmesi gerekir. Microsoft, bu testleri yürütmek için [Portal iş akışını](https://certify.azure.com) sağlar. Buluttan cihaza (uygulanmışsa): **1.** Cihazın IoT Hub 2 ' den ileti alabileceğini doğrular **.** AICS rastgele ileti gönderir ve cihazdan ileti ACK aracılığıyla doğrular |
| **Kaynaklar**           | **1.** [sertifika adımları](./overview.md) (tüm ek kaynaklara sahip), **2.** [IoT Hub bulutu cihaz iletilerine gönder](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Uygulanmışsa] Doğrudan Yöntemler: testin amacı, cihazların IoT Hub çalıştığından ve doğrudan metotları desteklediğinden emin olmak için kullanılır**

| **Ad**                | Iotpnp. DirectMethods                                     |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Kullanıma sunuldu                                                |
| **Uygulama hedefi**          | Yaprak cihaz/kenar aygıtı                                      |
| **İşletim sistemi**                  | Belirsiz                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | Cihazın IoT Hub komut isteklerini alabilmesi ve yanıtlayabilmesi gerekir. Microsoft, testleri yürütmek için [Portal iş akışını](https://certify.azure.com) sağlar. Doğrudan Yöntemler (uygulanmışsa): **1.** Kullanıcının doğrudan metodun Yöntem yükünü belirtmesi. **2.** AICS belirtilen yük isteği, cihaz tarafından alınan merkez ve ACK iletisinden gönderildiğini doğrular |
| **Kaynaklar**           | **1.** [sertifika adımları](./overview.md) (tüm ek kaynaklara sahip), **2.** [IoT Hub doğrudan yöntemleri anlama](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Uygulanmışsa] Device ikizi özelliği: testin amacı, telemetri gönderen cihazların IoT Hub ile çalıştığından ve doğrudan Yöntemler ve Device ikizi özelliği gibi bazı IoT Hub özellikleri desteklediğinden emin olmak için geçerlidir.**

| **Ad**                | Iotpnp. DeviceTwin                                        |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Kullanıma sunuldu                                                |
| **Uygulama hedefi**          | Yaprak cihaz/kenar aygıtı                                      |
| **İşletim sistemi**                  | Belirsiz                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | Cihazın IoT Hub için herhangi bir telemetri şeması gönderebilmesi gerekir. Microsoft, testleri yürütmek için [Portal iş akışını](https://certify.azure.com) sağlar. Device ikizi özelliği (uygulanmışsa): **1.** AICS, ikizi JSON 2 cihazında Read/Write özellikli özelliğini doğrular **.** Kullanıcının, değiştirilecek JSON yükünü belirtmesi ( **3).** AICS, cihaz tarafından alınan IoT Hub ve ACK iletisinden gönderilen istenen özellikleri doğrular |
| **Kaynaklar**           | **1.** [sertifika adımları](./overview.md) (tüm ek kaynaklara sahip), **2.** [IoT Hub ile cihaz ikizlerini kullanma](../iot-hub/iot-hub-devguide-device-twins.md) |
