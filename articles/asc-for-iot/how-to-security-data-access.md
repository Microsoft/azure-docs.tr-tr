---
title: IoT için Azure Güvenlik Merkezi 'ni kullanarak verilere erişme | Microsoft Docs
description: IoT için Azure Güvenlik Merkezi 'ni kullanırken güvenlik uyarısına ve öneri verilerinize erişme hakkında bilgi edinin.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597186"
---
# <a name="access-your-security-data"></a>Güvenlik verilerinize erişin 

IoT için Azure Güvenlik Merkezi, Log Analytics çalışma alanınıza güvenlik uyarılarını, önerileri ve ham güvenlik verilerini (kaydetmeyi seçerseniz) depolar.

## <a name="log-analytics"></a>Log Analytics

Hangi Log Analytics çalışma alanının kullanıldığını yapılandırmak için:

1. IoT Hub 'ınızı açın.
1. **Güvenlik** bölümünün altındaki **genel bakış** dikey penceresine tıklayın
2. **Ayarlar**' a tıklayın ve Log Analytics çalışma alanı yapılandırmanızı değiştirin.

Yapılandırma sonrasında Log Analytics çalışma alanınızdaki uyarılarınıza ve önerilerinizi erişmek için:

1. IoT için Azure Güvenlik Merkezi 'nde bir uyarı veya öneri seçin. 
2. **Daha fazla araştırma**' ya tıklayın, ardından **Bu uyarıya hangi cihazların olduğunu görmek için tıklayın ve ardından DeviceID sütununu görüntüleyin**.

Log Analytics verileri sorgulama hakkında daha fazla bilgi için bkz. [Log Analytics sorguları kullanmaya başlama](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Güvenlik uyarıları

Güvenlik uyarıları, IoT çözümü için Azure Güvenlik Merkezi için yapılandırılmış Log Analytics çalışma alanındaki _AzureSecurityOfThings. SecurityAlert_ tablosunda depolanır.

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

| TimeGenerated           | Iothubıd                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Açıklama                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < aygıt_adı > | Yüksek          | Deneme yanılma saldırısı başarılı           | Cihaza yönelik bir deneme yanılma saldırısı başarılı oldu        |    {"Tam kaynak adresi": "[\"10.165.12.18:\"]", "Kullanıcı adları": "[\"\"]", "DeviceID": "IoT-Device-Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < aygıt_adı > | Yüksek          | Cihazda başarılı yerel oturum açma      | Cihazda başarılı bir yerel oturum açma algılandı     | {"Uzak adres": "?", "uzak bağlantı noktası": "", "yerel bağlantı noktası": "", "oturum açma kabuğu": "/bin/su", "oturum açma Işlemi kimliği": "28207", "Kullanıcı adı": "saldırgan", "DeviceID": "IoT-Device-Linux"} |
| 2018-11-19T12:40:31.000 | /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < aygıt_adı > | Yüksek          | Cihazda yerel oturum açma denemesi başarısız  | Cihaza başarısız bir yerel oturum açma denemesi algılandı |  {"Uzak adres": "?", "uzak bağlantı noktası": "", "yerel bağlantı noktası": "", "oturum açma kabuğu": "/bin/su", "oturum açma Işlemi kimliği": "22644", "Kullanıcı adı": "saldırgan", "DeviceID": "IoT-Device-Linux"} |

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
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < aygıt_adı > | Yüksek          | Deneme yanılma saldırısı başarılı           | 9   |   
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < aygıt_adı > | Orta        | Cihazda yerel oturum açma denemesi başarısız  | 242 |    
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < aygıt_adı > | Yüksek          | Cihazda başarılı yerel oturum açma      | 31  |
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < aygıt_adı > | Orta        | Şifreleme para Miner                     | 4   |

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
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | Yüksek          | Deneme yanılma saldırısı başarılı           | 1\.          |    
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | Orta        | Cihazda yerel oturum açma denemesi başarısız  | 1\.          | 
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | Yüksek          | Cihazda başarılı yerel oturum açma      | 1\.          |
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | Orta        | Şifreleme para Miner                     | 1          |

## <a name="security-recommendations"></a>Güvenlik önerileri

Güvenlik önerileri, IoT çözümü için Azure Güvenlik Merkezi için yapılandırılmış Log Analytics çalışma alanındaki _AzureSecurityOfThings. Securityöneriyi_ tablosunda depolanır.

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
    
| TimeGenerated | Iothubıd | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Açıklama | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < aygıt_adı > | Orta | Etkin | Giriş zincirindeki izin veren güvenlik duvarı kuralı bulundu | Güvenlik duvarında geniş bir IP adresi veya Bağlantı Noktası aralığı için esnek bir desen içeren bir kural bulundu | {"Rules": "[{\"sourceAddress\":\"\",\"sourceport\":\"\",\"DestinationAddress:\" \"\" ,\"Hedef bağlantı\"noktası\":1337\"}] "} |
| 2019-03-22T10:50:27.237 | /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < aygıt_adı > | Orta | Etkin | Giriş zincirindeki izin veren güvenlik duvarı kuralı bulundu | Güvenlik duvarında geniş bir IP adresi veya Bağlantı Noktası aralığı için esnek bir desen içeren bir kural bulundu | {"Rules": "[{\"sourceAddress\":\"\",\"sourceport\":\"\",\"DestinationAddress:\" \"\" ,\"Hedef bağlantı\"noktası\":1337\"}] "} |

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
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < aygıt_adı > | Yüksek          | 2   |    
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < aygıt_adı > | Orta        | 1\. |  
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < aygıt_adı > | Yüksek          | 1\.  |
| /Subscriptions/< subscription_id >/resourceGroups/< resource_group >/providers/Microsoft.Devices/IotHubs/< iot_hub > | < aygıt_adı > | Orta        | 4   |


## <a name="next-steps"></a>Sonraki adımlar

- IoT için Azure Güvenlik Merkezi ['Ne genel bakış](overview.md) konusunu okuyun
- IoT [mimarisi](architecture.md) Için Azure Güvenlik Merkezi hakkında bilgi edinin
- [IoT uyarıları Için Azure Güvenlik Merkezi 'ni](concept-security-alerts.md) anlama ve araştırma
- [IoT önerisi Için Azure Güvenlik Merkezi 'ni](concept-recommendations.md) anlayın ve araştırın
