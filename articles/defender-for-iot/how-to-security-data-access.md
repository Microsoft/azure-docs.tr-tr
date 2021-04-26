---
title: Güvenlik & öneri verilerine erişin
description: IoT için Defender kullanırken güvenlik uyarısına ve öneri verilerinize erişme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 160f7c014c890f5d8c4dd6366d3acca70f21ad11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781696"
---
# <a name="access-your-security-data"></a>Güvenlik verilerinize erişin

IoT için Defender, Log Analytics çalışma alanınıza güvenlik uyarılarını, önerileri ve ham güvenlik verilerini (kaydetmeyi seçerseniz) depolar.

## <a name="log-analytics"></a>Log Analytics

Hangi Log Analytics çalışma alanının kullanıldığını yapılandırmak için:

1. IoT Hub 'ınızı açın.
1. **Güvenlik** bölümünün altındaki **Ayarlar** dikey penceresine tıklayın.
1. **Veri toplama**' yı tıklatın ve Log Analytics çalışma alanı yapılandırmanızı değiştirin.

Yapılandırma sonrasında Log Analytics çalışma alanınızdaki uyarılarınıza ve önerilerinizi erişmek için:

1. IoT için Defender 'da bir uyarı veya öneri seçin.
1. **Daha fazla araştırma**' ya tıklayın, ardından **Bu uyarıya hangi cihazların olduğunu görmek için tıklayın ve ardından DeviceID sütununu görüntüleyin**.

Log Analytics verileri sorgulama hakkında daha fazla bilgi için bkz. [Log Analytics sorguları kullanmaya başlama](../azure-monitor/logs/get-started-queries.md).

## <a name="security-alerts"></a>Güvenlik uyarıları

Güvenlik uyarıları,, IoT için Defender çözümü için yapılandırılmış Log Analytics çalışma alanındaki _AzureSecurityOfThings. SecurityAlert_ tablosunda depolanır.

Güvenlik uyarılarını keşfetmeye başlamanıza yardımcı olacak birkaç faydalı sorgu sunuyoruz.

### <a name="sample-records"></a>Örnek kayıtlar

Birkaç rastgele kayıt seçin

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | Iothubıd                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Description                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Yüksek          | Deneme yanılma saldırısı başarılı           | Cihaza yönelik bir deneme yanılma saldırısı başarılı oldu        |    {"Tam kaynak adresi": "[ \" 10.165.12.18: \" ]", "Kullanıcı adları": "[ \" \" ]", "DeviceID": "IoT-Device-Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Yüksek          | Cihazda başarılı yerel oturum açma      | Cihazda başarılı bir yerel oturum açma algılandı     | {"Uzak adres": "?", "uzak bağlantı noktası": "", "yerel bağlantı noktası": "", "oturum açma kabuğu": "/bin/su", "oturum açma Işlemi kimliği": "28207", "Kullanıcı adı": "saldırgan", "DeviceID": "IoT-Device-Linux"} |
| 2018-11-19T12:40:31.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Yüksek          | Cihazda yerel oturum açma denemesi başarısız  | Cihaza başarısız bir yerel oturum açma denemesi algılandı |    {"Uzak adres": "?", "uzak bağlantı noktası": "", "yerel bağlantı noktası": "", "oturum açma kabuğu": "/bin/su", "oturum açma Işlemi kimliği": "22644", "Kullanıcı adı": "saldırgan", "DeviceID": "IoT-Device-Linux"} |

### <a name="device-summary"></a>Cihaz Özeti

Son haftada algılanan ve IoT Hub, cihaz, uyarı önem derecesi, uyarı türüne göre gruplandırılan ayrı güvenlik uyarılarının sayısını alın.

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| Iothubıd                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Yüksek          | Deneme yanılma saldırısı başarılı           | 9   |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Orta        | Cihazda yerel oturum açma denemesi başarısız  | 242 |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Yüksek          | Cihazda başarılı yerel oturum açma      | 31  |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Orta        | Şifreleme para Miner                     | 4   |

### <a name="iot-hub-summary"></a>IoT Hub Özeti

Son hafta içinde, IoT Hub, uyarı önem derecesine ve uyarı türüne göre uyarı olan ayrı sayıda farklı cihaz seçin

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| Iothubıd                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Yüksek          | Deneme yanılma saldırısı başarılı           | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Orta        | Cihazda yerel oturum açma denemesi başarısız  | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Yüksek          | Cihazda başarılı yerel oturum açma      | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Orta        | Şifreleme para Miner                     | 1          |

## <a name="security-recommendations"></a>Güvenlik önerileri

Güvenlik önerileri,, IoT için Defender çözümü için yapılandırılmış Log Analytics çalışma alanındaki _AzureSecurityOfThings. Securityöneriyi_ tablosunda depolanır.

Güvenlik önerilerini keşfetmeye başlamanıza yardımcı olacak birkaç faydalı sorgu sağladık.

### <a name="sample-records"></a>Örnek kayıtlar

Birkaç rastgele kayıt seçin

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | Iothubıd | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Description | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Orta | Etkin | Giriş zincirindeki izin veren güvenlik duvarı kuralı bulundu | Çok sayıda IP adresi veya bağlantı noktası aralığı için izin veren bir model içeren güvenlik duvarındaki bir kural bulundu | {"Rules": "[{ \" sourceAddress \" : \" \" , \" sourceport \" : \" \" , \" DestinationAddress \" : \" \" , \" destinationport \" : \" 1337 \" }]"} |
| 2019-03-22T10:50:27.237 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Orta | Etkin | Giriş zincirindeki izin veren güvenlik duvarı kuralı bulundu | Çok sayıda IP adresi veya bağlantı noktası aralığı için izin veren bir model içeren güvenlik duvarındaki bir kural bulundu | {"Rules": "[{ \" sourceAddress \" : \" \" , \" sourceport \" : \" \" , \" DestinationAddress \" : \" \" , \" destinationport \" : \" 1337 \" }]"} |

### <a name="device-summary"></a>Cihaz Özeti

IoT Hub, cihaz, öneri önem derecesi ve türe göre gruplandırılan farklı etkin güvenlik önerileri sayısını alın.

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| Iothubıd                                                                                                       | DeviceId      | RecommendationSeverity | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Yüksek          | 2   |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Orta        | 1 |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Yüksek          | 1  |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Orta        | 4   |

## <a name="next-steps"></a>Sonraki adımlar

- IoT için Defender 'ı okuyun [genel bakış](overview.md)
- IoT [mimarisi](architecture.md) için Defender hakkında bilgi edinin
- [IoT uyarıları Için Defender 'ı](concept-security-alerts.md) anlayın ve araştırın
- [IoT önerileri Için Defender 'ı](concept-recommendations.md) anlayın ve araştırın