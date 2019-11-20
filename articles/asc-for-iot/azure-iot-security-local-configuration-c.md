---
title: C için yerel aracı yapılandırmalarına yönelik Azure Güvenlik Merkezi 'ni anlama | Microsoft Docs
description: C için yerel aracı yapılandırmalarına yönelik Azure Güvenlik Merkezi hakkında bilgi edinin.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 2725a824da26dafcbc215e4c302ec38ad4b5a699
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600546"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>LocalConfiguration. JSON dosyası-C aracısını anlama

IoT güvenlik Aracısı için Azure Güvenlik Merkezi, yerel bir yapılandırma dosyasından yapılandırmaları kullanır.
Güvenlik Aracısı, aracıyı başlatma sırasında yapılandırmayı bir kez okur.
Yerel yapılandırma dosyasında bulunan yapılandırma, kimlik doğrulama yapılandırması ve aracıyla ilgili diğer yapılandırmaları içerir.
Dosya, JSON gösteriminde "anahtar-değer" çiftleri içinde yapılandırma içerir ve aracı yüklendiğinde yapılandırma doldurulur. 

Varsayılan olarak, dosya şu konumda bulunur:/var/asciotagent/localconfiguration.exe

Yapılandırma dosyasındaki değişiklikler aracı yeniden başlatıldığında gerçekleşir. 

## <a name="security-agent-configurations-for-c"></a>C için güvenlik Aracısı yapılandırması
| Yapılandırma Adı | Olası değerler | Ayrıntılar | 
|:-----------|:---------------|:--------|
| AgentId | GUID | Aracı benzersiz tanımlayıcısı |
| TriggerdEventsInterval | ISO8601 dizesi | Tetiklenen olaylar koleksiyonu için Zamanlayıcı aralığı |
| ConnectionTimeout | ISO8601 dizesi | Iothub bağlantısı zaman aşımına uğramadan önce geçen süre |
| Authentication | JsonObject | Kimlik doğrulama yapılandırması. Bu nesne, ıothub 'e göre kimlik doğrulaması için gereken tüm bilgileri içerir |
| Kimlik | "DPS", "SecurityModule", "cihaz" | Kimlik doğrulama kimliği-DPS, kimlik doğrulaması, cihaz kimlik bilgileriyle kimlik doğrulama yapılırsa güvenlik modülü kimlik bilgileri veya cihaz aracılığıyla yapılırsa SecurityModule |
| AuthenticationMethod | "SasToken", "SelfSignedCertificate" | kimlik doğrulaması için Kullanıcı parolası-gizli anahtar kullan seçeneği bir simetrik anahtaralıysa, sır otomatik olarak imzalanan bir sertifika ise, otomatik olarak imzalanan sertifika seçeneğini belirleyin  |
| FilePath | Dosya yolu (dize) | Kimlik doğrulama parolasını içeren dosyanın yolu |
| ana bilgisayar adı | dize | Azure IoT Hub 'ının konak adı. genellikle <-Hub >. Azure-devices.net |
| DeviceId | dize | Cihazın KIMLIĞI (Azure IoT Hub kayıtlı olarak) |
| DPS | JsonObject | DPS ilgili yapılandırma |
| IDScope | dize | DPS KIMLIK kapsamı |
| RegistrationId | dize  | DPS cihaz kayıt KIMLIĞI |
| Günlüğe Kaydetme | JsonObject | Aracı günlükçüsü ile ilgili yapılandırma |
| Systemloggerminimumönem derecesi | 0 < = sayı < = 4 | Bu önem derecesine sahip ve yukarıdaki günlük iletileri,/var/log/Syslog dizinine kaydedilir (en düşük önem derecesi 0 ' dır) |
| DiagnosticEventMinimumSeverity | 0 < = sayı < = 4 | Bu önem derecesine sahip olan ve yukarıdaki günlük iletileri, tanılama olayları olarak gönderilir (en düşük önem derecesi 0 ' dır) |

## <a name="security-agent-configurations-code-example"></a>Güvenlik Aracısı yapılandırma kodu örneği
```JSON
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
- IoT hizmetine [genel bakış](overview.md) Için Azure Güvenlik Merkezi 'ni okuyun
- IoT [mimarisi](architecture.md) Için Azure Güvenlik Merkezi hakkında daha fazla bilgi edinin
- IoT [hizmeti](quickstart-onboard-iot-hub.md) Için Azure Güvenlik Merkezi 'ni etkinleştirme
- IoT hizmeti için Azure Güvenlik Merkezi [SSS](resources-frequently-asked-questions.md) makalesini okuyun
- [Ham güvenlik verilerine](how-to-security-data-access.md) erişme hakkında bilgi edinin
- [Önerileri](concept-recommendations.md) anlama
- Güvenlik [uyarılarını](concept-security-alerts.md) anlama