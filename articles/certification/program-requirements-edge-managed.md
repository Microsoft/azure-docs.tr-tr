---
title: Edge tarafından yönetilen sertifika gereksinimleri
description: Edge tarafından yönetilen sertifika programı gereksinimleri
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Managed Certification Requirements
ms.service: certification
ms.openlocfilehash: 744f81d0544a765f60793ece1aa539c6c37e39cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969400"
---
# <a name="azure-certification-edge-managed"></a>Azure Sertifika Edge yönetiliyor 

Bu belgede, Azure Sertifikalı cihaz kataloğunda temsil edilecek cihaza özgü yetenekler özetlenmektedir. Bir özellik, cihazı betimleyen tekil bir cihaz özniteliğidir. 

## <a name="program-purpose"></a>Program amacı

Uçtan yönetilen sertifika, temel Azure Sertifikalı cihaz sertifikası ötesinde artımlı bir sertifika. Sınır yönetimli Azure bağlı cihazlar için cihaz yönetim standartlarına odaklanılır ve modül dağıtımı ve yönetimi için IoT Edge çalışma zamanı uyumluluğunu doğrular. (Daha önce bu program IoT Edge sertifika programı olarak tanımlanmıştır.) 

Edge tarafından yönetilen sertifika, modül dağıtımı ve yönetimi için IoT Edge çalışma zamanı uyumluluğunu doğrular. Bu program, Azure bağlantılı IoT cihazlarının yönetimine güven sağlar.

## <a name="requirements"></a>Gereksinimler

Edge tarafından yönetilen sertifika, [Azure Sertifikalı cihaz temeli programından](.\program-requirements-azure-certified-device.md)tüm gereksinimlerin yapılmasını gerektirir.

**DPS: testin amacı, cihazın uygulayıp uygulamadığını denetlerken, üç kanıtlama yönteminden biriyle cihaz sağlama hizmeti IoT Hub destekler**

| **Ad**                | AzureReady. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Ignite (önizlemede)                                                |
| **Uygulama hedefi**          | Herhangi bir cihaz                                      |
| **İşletim sistemi**                  | Belirsiz                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | AICS, cihaz kodu destek DPS ' i doğrular. **1.** Kullanıcının kanıtlama yöntemlerinden birini (X. 509.440, TPM ve SAS anahtarı) seçmeniz gerekir. **2.** Kanıtlama yöntemine bağlı olarak, kullanıcının a gibi ilgili eylemi yapması gerekir **)** X. 509.952 CERT 'yi şirket IÇINDE yönetilen DPS kapsamına Yükle **b)** cihaza SAS anahtarı veya onay anahtarı uygulayın. **3.** Daha sonra Kullanıcı, DPS ile yönetilen bir IoT Hub bağlanmak için ' Bağlan ' düğmesine vuracaktır                                                    |
| **Kaynaklar**           |                                                      |
| **Azure önerilir:**     | Yok                                                    |

## <a name="iot-edge"></a>IoT Edge

**Edge çalışma zamanı var: testin amacı, cihazın IoT Edge çalışma zamanı ($edgehub ve $edgeagent) doğru şekilde çalıştığından emin olmak.**

| **Ad**                | EdgeManaged. EdgeRT                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Kullanıma sunuldu                                                          |
| **Uygulama hedefi**          | IoT Edge cihazı                                                   |
| **İşletim sistemi**                  | [Katman1 ve Katman2 OS](../iot-edge/support.md)                                                     |
| **Doğrulama türü**     | Otomatik                                                    |
| **Doğrulama**          | AICS, yüklü IoT Edge RT 'nin dağıtım yeteneğini doğrular. **1.** Kullanıcının belirli bir işletim sistemini belirtmesi gerekir (Katman1/2 listesinde olmayan işletim sistemi kabul edilmez) **2.** AICS, config. YAML dosyasını oluşturur ve kurallı, [benzetimli geçici algılayıcı uç modülünü](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-iot.simulated-temperature-sensor?tab=Overview) dağıtır **.** AICS, Docker uyumlu kapsayıcı alt sisteminin (Moby) cihaz 4 ' te yüklü olduğunu doğrular **.** Test sonucu, sanal geçici algılayıcı uç modülünün başarıyla dağıtımına ve Docker uyumlu kapsayıcı alt sisteminin işlevselliğine göre belirlenir.                                                    |
| **Kaynaklar**           | **a)** [aics blogu](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [sertifika adımları](./overview.md) (tüm ek kaynaklar), **c)** [gereksinimleri](./program-requirements-azure-certified-device.md) |
| **Azure önerilir:**     | Yok                                                    |

### <a name="capability-template"></a>Yetenek şablonu:

**IoT Edge kolay kurulum: testin amacı, IoT Edge cihazın kolayca kurulması ve doğrulanması için IoT Edge çalışma zamanının fiziksel cihaz doğrulaması sırasında önceden yüklenmiş olduğundan emin olmak.**

| **Ad**                | EdgeManaged. PhysicalDevice                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Hedef kullanılabilirliği** | Şimdi kullanılabilir (COVıD-19 nedeniyle şu anda tutuluyor)                                            |
| **Uygulama hedefi**          | IoT Edge cihazı                                                   |
| **İşletim sistemi**                  | [Katman1 ve Katman2 OS](../iot-edge/support.md)                                                     |
| **Doğrulama türü**     | El ile/laboratuvar doğrulandı                                                    |
| **Doğrulama**          | OEM, fiziksel cihazı IoT yönetimine (HCL) teslim etmelidir. HCL, denetlenecek fiziksel cihazda el ile doğrulama gerçekleştirir: **1.** EdgeRT, Moby alt sistemini (izin verilen yeniden dağıtım sürümü) kullanıyor. Docker **2 değil.** Kenarı dağıtma yeteneğini doğrulamak için en son Edge modülünü seçin.                                                     |
| **Kaynaklar**           | **a)** [aics blogu](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [sertifika adımları](./overview.md) , **c)** [gereksinimleri](./program-requirements-azure-certified-device.md) |
| **Azure önerilir:**     | Yok                                                    |