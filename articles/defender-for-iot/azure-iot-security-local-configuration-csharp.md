---
title: IoT güvenlik aracısının yerel yapılandırması için Defender (C#)
description: IoT için Defender güvenlik hizmeti, C# için güvenlik Aracısı yerel yapılandırma dosyası hakkında daha fazla bilgi edinin.
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 42cf7f129005f057a9d51ce2e09db735e825f476
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784790"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Yerel yapılandırma dosyasını anlama (C# Aracısı)

IoT güvenlik Aracısı için Defender, yerel bir yapılandırma dosyasından yapılandırmaları kullanır.

Güvenlik Aracısı, aracı çalışmaya başladığında yapılandırma dosyasını okur. Yerel yapılandırma dosyasında bulunan yapılandırmalar hem kimlik doğrulama yapılandırmasını hem de aracıyla ilgili diğer yapılandırmaları içerir.

C# güvenlik Aracısı birden çok yapılandırma dosyası kullanır:

- **General.config** aracıyla ilgili yapılandırma.
- **Authentication.config** -kimlik doğrulaması ile ilgili yapılandırma (kimlik doğrulama ayrıntıları dahil).
- **SecurityIotInterface.config** IoT ile ilgili yapılandırma.

Yapılandırma dosyaları varsayılan yapılandırmayı içerir. Kimlik doğrulama yapılandırması aracı yüklemesi sırasında doldurulur ve aracı yeniden başlatıldığında yapılandırma dosyasında yapılan değişiklikler yapılır.

## <a name="configuration-file-location"></a>Yapılandırma dosyası konumu

Linux için:

- İşletim sistemi yapılandırma dosyaları konumunda bulunur `/var/ASCIoTAgent` .

Windows için:

- İşletim sistemi yapılandırma dosyaları, güvenlik aracısının dizininde bulunur.

### <a name="generalconfig-configurations"></a>General.config yapılandırma

| Yapılandırma adı | Olası değerler | Ayrıntılar |
|:-----------|:---------------|:--------|
| AgentId | GUID | Aracı benzersiz tanımlayıcısı |
| readRemoteConfigurationTimeout | TimeSpan | IoT Hub uzak yapılandırmayı getirmeye yönelik zaman aralığı. Aracı belirtilen süre içinde yapılandırmayı getiremez, işlem zaman aşımına uğrar.|
| schedulerInterval | TimeSpan | İç Zamanlayıcı aralığı. |
| producerInterval | TimeSpan | Olay üreticisi çalışan aralığı. |
| Consumerınterval | TimeSpan | Olay tüketicisi çalışan aralığı. |
| highPriorityQueueSizePercentage | 0 < numarası < 1 | Yüksek öncelikli iletiler için ayrılan toplam önbelleğin bölümü. |
| logLevel | "Off", "önemli", "Error", "Warning", "Information", "Debug"  | Bu önem derecesine sahip ve yukarıdaki günlük iletileri hata ayıklama konsolunda günlüğe kaydedilir (Linux 'ta syslog). |
| Dosya LogLevel |  "Off", "önemli", "Error", "Warning", "Information", "Debug"| Bu önem derecesine sahip olan ve yukarıdaki günlük iletileri, dosyaya (Linux 'ta syslog) kaydedilir. |
| diagnosticVerbosityLevel | "None", "Some", "tümü", | Tanılama olaylarının ayrıntı düzeyi. Hiçbiri-tanılama olayları gönderilmez. Yalnızca yüksek önem taşıyan bazı tanılama olayları gönderilir. Tümü-tüm Günlükler de tanılama olayları olarak gönderilir. |
| logFilePath | Dosya yolu | FileLogLevel > kapalıysa Günlükler bu dosyaya yazılır. |
| defaultEventPriority | "Yüksek", "düşük", "kapalı" | Varsayılan olay önceliği. |

### <a name="generalconfig-example"></a>General.config örneği

```xml
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

### <a name="authenticationconfig"></a>Authentication.config

| Yapılandırma adı | Olası değerler | Ayrıntılar |
|:-----------|:---------------|:--------|
| Ladı | string | Defender-IoT-mikro aracı kimliğinin adı. Bu ad, cihazdaki modül kimliği adına karşılık gelmelidir. |
| deviceId | string | Cihazın KIMLIĞI (Azure IoT Hub kayıtlı olarak). |
| schedulerInterval | TimeSpan dizesi | İç Zamanlayıcı aralığı. |
| gatewayHostname | string | Azure IoT Hub 'ının konak adı. Genellikle <-hub>. azure-devices.net |
| Null | dize-dosyanın yolu | Kimlik doğrulama parolasını içeren dosyanın yolu.|
| tür | "SymmetricKey", "SelfSignedCertificate" | Kimlik doğrulaması için Kullanıcı parolası. *SymmetricKey* ' i seçin Kullanıcı parolası bir simetrik anahtaralıysa, parola otomatik olarak imzalanan bir sertifika ise, *otomatik olarak imzalanan sertifika* seçeneğini belirleyin. |
| identity | "DPS", "Module", "cihaz" | Kimlik doğrulaması kimliği-kimlik doğrulama, DPS aracılığıyla yapılırsa, modül kimlik bilgileri kullanılarak kimlik doğrulaması yapıldığında modül veya cihaz kimlik bilgileri kullanılarak kimlik doğrulaması yapılmışsa kullanılır.
| certificateLocationKind |  "YerelDosya", "Mağaza" | YerelDosya sertifika bir dosyada depolanıyorsa, sertifika bir sertifika deposunda bulunuyorsa mağaza. |
| idScope | string | DPS KIMLIK kapsamı |
| RegistrationId | string  | DPS cihaz kayıt KIMLIĞI. |
|

### <a name="authenticationconfig-example"></a>Authentication.config örneği

```xml
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

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Yapılandırma adı | Olası değerler | Ayrıntılar |
|:-----------|:---------------|:--------|
| transportType | "Ampq" "MQTT" | IoT Hub taşıma türü. |
|

### <a name="securityiotinterfaceconfig-example"></a>SecurityIotInterface.config örneği

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Sonraki adımlar

- IoT hizmetine [genel bakış](overview.md) Için Defender 'ı okuyun
- IoT [mimarisi](architecture.md) için Defender hakkında daha fazla bilgi edinin
- IoT [hizmeti](quickstart-onboard-iot-hub.md) Için Defender 'ı etkinleştirin
- IoT hizmeti için Defender 'ı okuyun [SSS](resources-frequently-asked-questions.md)
- [Ham güvenlik verilerine](how-to-security-data-access.md) erişme hakkında bilgi edinin
- [Önerileri](concept-recommendations.md) anlama
- Güvenlik [uyarılarını](concept-security-alerts.md) anlama
