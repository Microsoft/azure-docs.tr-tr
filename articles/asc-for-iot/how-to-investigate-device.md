---
title: IoT cihaz araştırma Kılavuzu için Azure Güvenlik Merkezi | Microsoft Docs
description: Bu kılavuzda, Log Analytics kullanarak kuşkulu bir IoT cihazını araştırmak için IoT için Azure Güvenlik Merkezi 'nin nasıl kullanılacağı açıklanmaktadır.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8d2fe8d63c7ece6f3b3426d8fc5a3454a61826f8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596242"
---
# <a name="investigate-a-suspicious-iot-device"></a>Şüpheli bir IoT cihazını araştırın

IoT Hizmetleri uyarıları için Azure Güvenlik Merkezi, IoT cihazlarının şüpheli etkinliklerdeki katılımın veya bir cihazın tehlikede olduğu göstergeler olduğu durumlarda açık göstergeler sağlar. 

Bu kılavuzda, kuruluşunuza yönelik olası riskleri belirlemek için sağlanan araştırma önerilerini kullanın, daha sonra benzer saldırıları önlemenin en iyi yollarını bulun.  

> [!div class="checklist"]
> * Cihaz verilerinizi bulun
> * KQL sorgularını kullanarak araştır


## <a name="how-can-i-access-my-data"></a>Verilerinize nasıl erişebilirim?

Varsayılan olarak, IoT için Azure Güvenlik Merkezi, Log Analytics çalışma alanınızdaki güvenlik uyarılarınızı ve önerilerinizi depolar. Ham güvenlik verilerinizi depolamayı da tercih edebilirsiniz.

Veri depolamaya yönelik Log Analytics çalışma alanınızı bulmak için:

1. IoT Hub 'ınızı açın, 
1. **Güvenlik**altında **genel bakış**' a tıklayın ve ardından **Ayarlar**' ı seçin.
1. Log Analytics çalışma alanı yapılandırma ayrıntılarınızı değiştirin. 
1. **Kaydet**’e tıklayın. 

Aşağıdaki yapılandırma, Log Analytics çalışma alanınızda depolanan verilere erişmek için aşağıdakileri yapın:

1. IoT Hub IoT uyarısı için bir Azure Güvenlik Merkezi ' ni seçin ve tıklayın. 
1. **Daha fazla araştırma**öğesine tıklayın. 
1. **Bu uyarıya hangi cihazların olduğunu görmek için seçin öğesini seçin ve DeviceID sütununu görüntüleyin**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Şüpheli IoT cihazları için araştırma adımları

IoT cihazlarınızla ilgili öngörüleri ve ham verileri görüntülemek için [verilerinize erişmek üzere](#how-can-i-access-my-data)Log Analytics çalışma alanınıza gidin.

Cihazınızdaki uyarıları ve etkinlikleri araştırmaya başlamak için aşağıdaki örnek KQL sorgularını inceleyin.

### <a name="related-alerts"></a>İlgili uyarılar

Aynı anda başka uyarıların tetiklendiği hakkında bilgi edinmek için aşağıdaki KQL sorgusunu kullanın:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Erişimi olan kullanıcılar

Bu cihaza hangi kullanıcıların erişimi olduğunu öğrenmek için aşağıdaki KQL sorgusunu kullanın: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
Bu verileri kullanarak şunları bulun: 
- Cihaza hangi kullanıcıların erişimi var?
- Erişim izni olan kullanıcılar beklenen izin düzeylerine sahip mi?

### <a name="open-ports"></a>Bağlantı noktalarını aç

Cihazdaki hangi bağlantı noktalarının kullanılmakta olduğunu veya kullanıldığını öğrenmek için aşağıdaki KQL sorgusunu kullanın: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

Bu verileri kullanarak şunları bulun:
- Şu anda cihazda hangi dinleme yuvaları etkin?
- Şu anda etkin olan dinleme yuvaları izin verilmelidir mi?
- Cihaza bağlı şüpheli uzak adresler var mı?

### <a name="user-logins"></a>Kullanıcı oturumu açma

Cihazda oturum açan kullanıcıları bulmak için aşağıdaki KQL sorgusunu kullanın: 
 
 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

Keşif yapmak için sorgu sonuçlarını kullanın:
- Cihazda hangi kullanıcılar oturum açtı?
- Oturum açan kullanıcılardır, oturum açmamı gerekir?
- Oturum açan kullanıcılar beklenen veya beklenmeyen IP adreslerinden bağlandık mi?
  
### <a name="process-list"></a>İşlem listesi

İşlem listesinin beklenildiği hakkında bilgi edinmek için aşağıdaki KQL sorgusunu kullanın: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

Keşif yapmak için sorgu sonuçlarını kullanın:

- Cihazda çalışan şüpheli işlem var mı?
- İşlem uygun kullanıcılar tarafından yürütüldü mi?
- Herhangi bir komut satırı yürütmeleri doğru ve beklenen bağımsız değişkenleri içeriyor mu?

## <a name="next-steps"></a>Sonraki adımlar

Bir cihazı araştırdıktan ve risklerinizi daha iyi anlamak için, IoT çözümü güvenlik duruşunuzu geliştirmek üzere [özel uyarılar yapılandırmayı](quickstart-create-custom-alerts.md) düşünmek isteyebilirsiniz. Zaten bir cihaz aracınız yoksa, sonuçlarınızı geliştirmek için [bir güvenlik Aracısı dağıtımı](how-to-deploy-agent.md) veya [mevcut bir cihaz aracısının yapılandırmasını değiştirmeyi](how-to-agent-configuration.md) düşünün. 
