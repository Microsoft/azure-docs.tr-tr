---
title: Aracı yerel yapılandırma dosyası için Azure Güvenlik Merkezi 'ni anlama C# | Microsoft Docs
description: İçin C#aracı yerel yapılandırma dosyası Için Azure Güvenlik Merkezi hakkında bilgi edinin.
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
ms.openlocfilehash: cc7b9f0b6e537ca3bdcbb82a357b2f2b9451fab0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600637"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Yerel yapılandırma dosyasını anlama (C# aracı)


IoT güvenlik Aracısı için Azure Güvenlik Merkezi, yerel bir yapılandırma dosyasından yapılandırmaları kullanır.

Güvenlik Aracısı, Aracı başladığında yapılandırma dosyasını bir kez okur. Yerel yapılandırma dosyasında bulunan yapılandırmalar hem kimlik doğrulama yapılandırmasını hem de aracıyla ilgili diğer yapılandırmaları içerir.

C# Güvenlik Aracısı birden çok yapılandırma dosyası kullanır:

- **Genel. config** aracıyla ilgili yapılandırma.
- **Authentication. config** -kimlik doğrulaması ile ilgili yapılandırma (kimlik doğrulama ayrıntıları dahil).
- **Securityıotınterface. config** -IoT ile ilgili yapılandırma.

Yapılandırma dosyaları varsayılan yapılandırmayı içerir. Kimlik doğrulama yapılandırması aracı yüklemesi sırasında doldurulur ve aracı yeniden başlatıldığında yapılandırma dosyasında yapılan değişiklikler yapılır. 

## <a name="configuration-file-location"></a>Yapılandırma dosyası konumu
Linux için:
- İşletim sistemi yapılandırma dosyaları konumunda `/var/ASCIoTAgent`bulunur.

Windows için:
- İşletim sistemi yapılandırma dosyaları, güvenlik aracısının dizininde bulunur. 

### <a name="generalconfig-configurations"></a>Genel. config yapılandırması

| Yapılandırma Adı | Olası değerler | Ayrıntılar | 
|:-----------|:---------------|:--------|
| agentId | GUID | Aracı benzersiz tanımlayıcısı |
| readRemoteConfigurationTimeout | TimeSpan | IoT Hub uzak yapılandırmayı getirmeye yönelik zaman aralığı. Aracı belirtilen süre içinde yapılandırmayı getiremez, işlem zaman aşımına uğrar.|
| schedulerInterval | TimeSpan | İç Zamanlayıcı aralığı. |
| producerInterval | TimeSpan | Olay üreticisi çalışan aralığı. |
| consumerınterval | TimeSpan | Olay tüketicisi çalışan aralığı. |
| highPriorityQueueSizePercentage | 0 < numarası < 1 | Yüksek öncelikli iletiler için ayrılan toplam önbelleğin bölümü. |
| logLevel | "Off", "Fatal", "Error", "Warning", "Information", "Debug"  | Bu önem derecesine sahip ve yukarıdaki günlük iletileri hata ayıklama konsolunda günlüğe kaydedilir (Linux 'ta syslog). |
| fileLogLevel |  "Off", "Fatal", "Error", "Warning", "Information", "Debug"| Bu önem derecesine sahip olan ve yukarıdaki günlük iletileri, dosyaya (Linux 'ta syslog) kaydedilir. |
| diagnosticVerbosityLevel | "None", "Some", "All", | Tanılama olaylarının ayrıntı düzeyi. Hiçbiri-tanılama olayları gönderilmez, yüksek önem taşıyan bazı tanılama olayları gönderilir, tüm Günlükler de tanılama olayları olarak gönderilir. |
| logFilePath | Dosya yolu | FileLogLevel > kapalıysa Günlükler bu dosyaya yazılır. |
| defaultEventPriority | "High", "Low", "Off" | Varsayılan olay önceliği. |

### <a name="generalconfig-example"></a>General. config örneği
```XML
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication. config

| Yapılandırma adı | Olası değerler | Ayrıntılar | 
|:-----------|:---------------|:--------|
| moduleName | string | Güvenlik modülü kimliğinin adı. Bu ad, cihazdaki modül kimliği adına karşılık gelmelidir. |
| deviceId | string | Cihazın KIMLIĞI (Azure IoT Hub kayıtlı olarak). || schedulerInterval | TimeSpan dizesi | İç Zamanlayıcı aralığı. |
| gatewayHostname | string | Azure IoT Hub 'ının konak adı. Genellikle <-Hub >. Azure-devices.net |
| filePath | string - dosyanın yolu | Kimlik doğrulama parolasını içeren dosyanın yolu.|
| type | "SymmetricKey", "SelfSignedCertificate" | Kimlik doğrulaması için Kullanıcı parolası. *SymmetricKey* ' i seçin Kullanıcı parolası bir simetrik anahtaralıysa, gizli dizi otomatik olarak imzalanan bir sertifikadır, *otomatik olarak imzalanan sertifika* seçeneğini belirleyin. |
| identity | "DPS", "Module", "Device" | Kimlik doğrulaması kimliği-kimlik doğrulama, DPS aracılığıyla yapılırsa, modül kimlik bilgileri kullanılarak kimlik doğrulaması yapıldığında modül veya cihaz kimlik bilgileri kullanılarak kimlik doğrulaması yapılmışsa kullanılır.
| certificateLocationKind |  "LocalFile", "Store" | YerelDosya sertifika bir dosyada depolanıyorsa, sertifika bir sertifika deposunda bulunuyorsa mağaza. |
| idScope | string | DPS KIMLIK kapsamı |
| registrationId | string  | DPS cihaz kayıt KIMLIĞI. |
|

### <a name="authenticationconfig-example"></a>Authentication. config örneği
```XML
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```
### <a name="securityiotinterfaceconfig"></a>Securityıotınterface. config

| Yapılandırma Adı | Olası değerler | Ayrıntılar | 
|:-----------|:---------------|:--------|
| transportType | "Ampq" "MQTT" | IoT Hub taşıma türü. |
|

### <a name="securityiotinterfaceconfig-example"></a>Securityıotınterface. config örneği
```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Sonraki adımlar
- IoT hizmetine [genel bakış](overview.md) Için Azure Güvenlik Merkezi 'ni okuyun
- IoT [mimarisi](architecture.md) Için Azure Güvenlik Merkezi hakkında daha fazla bilgi edinin
- IoT [hizmeti](quickstart-onboard-iot-hub.md) Için Azure Güvenlik Merkezi 'ni etkinleştirme
- IoT hizmeti için Azure Güvenlik Merkezi [SSS](resources-frequently-asked-questions.md) makalesini okuyun
- [Ham güvenlik verilerine](how-to-security-data-access.md) erişme hakkında bilgi edinin
- [Önerileri](concept-recommendations.md) anlama
- Güvenlik [uyarılarını](concept-security-alerts.md) anlama