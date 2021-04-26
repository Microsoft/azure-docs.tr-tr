---
title: CIS kıyaslama önerisini araştır
description: İşletim sistemi taban çizgisi önerilerine göre temel ve gelişmiş araştırmalar gerçekleştirin.
ms.date: 1/21/2021
ms.topic: how-to
ms.openlocfilehash: 01ca6e1fecddff9800872a3e5495a5cac578a74f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782036"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>İşletim sistemi taban çizgisini araştır (CIS kıyaslaması tabanlı) önerisi 

İşletim sistemi taban çizgisi önerilerine göre temel ve gelişmiş araştırmalar gerçekleştirin.

## <a name="basic-os-baseline-security-recommendation-investigation"></a>Temel işletim sistemi temel güvenlik önerisi araştırması  

**IoT Hub** altında IoT portalında Azure Defender ' a giderek işletim sistemi taban çizgisi önerilerini inceleyebilirsiniz. Daha fazla bilgi için bkz. [güvenlik önerilerini araştırma](quickstart-investigate-security-recommendations.md).

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>Gelişmiş işletim sistemi temel güvenlik önerisi araştırması  

Bu bölümde, işletim sistemi taban çizgisi test sonuçlarının nasıl daha iyi anlaşılması ve olayları Azure Log Analytics sorgulama açıklanmaktadır.  

Gelişmiş işletim sistemi temel güvenlik önerisi araştırması yalnızca Log Analytics kullanılarak desteklenir. Devam etmeden önce IoT için Defender 'ı bir Log Analytics çalışma alanına bağlayın. Gelişmiş işletim sistemi temel güvenlik önerileri hakkında daha fazla bilgi için bkz. [IoT Aracısı tabanlı çözüm Için Azure Defender 'ı yapılandırma](how-to-configure-agent-based-solution.md).

Uyarılar için Log Analytics IoT güvenlik olaylarınızı sorgulamak için:

1. **Uyarılar** sayfasına gidin.

1. **Log Analytics çalışma alanında önerileri Araştır '** ı seçin.

Öneriler için Log Analytics IoT güvenlik olaylarınızı sorgulamak için:

1. **Öneriler** sayfasına gidin.

1. **Log Analytics çalışma alanında önerileri Araştır '** ı seçin.

1. Belirli bir cihazın ayrıntılarını görmek için **öneri ayrıntıları** hızlı görünümü sayfasından **Işlem sistemi (OS) taban çizgisi kuralları ayrıntılarını göster** ' i seçin.

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="Belirli bir cihazın ayrıntılarına bakın."::: 

Log Analytics çalışma alanındaki IoT güvenlik olaylarınızı doğrudan sorgulamak için:

1. **Günlükler** sayfasına gidin.

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="Sol taraftaki bölmeden Günlükler ' i seçin.":::

1. **Uyarıları araştır** ' ı seçin veya herhangi bir güvenlik önerisi veya uyarısından **Log Analytics Uyarıları araştır** seçeneğini belirleyin.   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>İşletim sistemi taban çizgisi kaynaklarını araştırmak için yararlı sorgular: 

> [!Note]
> `<device-id>`Aşağıdaki sorguların her birinde cihazınızı verdiğiniz ad (ler) i Ile değiştirdiğinizden emin olun. 


### <a name="retrieve-the-latest-information"></a>En son bilgileri alma

- **Cihaz filo hatası**: cihaz filo genelinde başarısız olan denetimler hakkında en son bilgileri almak için aşağıdaki sorguyu çalıştırın: 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Belirli cihaz hatası** -belirli bir cihazda başarısız olan denetimler hakkında en son bilgileri almak için aşağıdaki sorguyu çalıştırın:  

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```

- **Belirli cihaz hatası** -belirli bir cihazda hata olan denetimler hakkında en son bilgileri almak için bu sorguyu çalıştırın: 

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "ERROR" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Belirli bir denetimi başarısız olan cihaz fillerine yönelik cihaz listesini güncelleştir** -bu sorguyu, belirli bir denetim başarısız olan cihazların güncelleştirilmiş listesini (cihaz filo) almak için çalıştırın:  
 
    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    where event.CceId contains "6.2.8" | 
    
    project DeviceId; 
    ```
 
## <a name="next-steps"></a>Sonraki adımlar

[Güvenlik önerilerini araştırın](quickstart-investigate-security-recommendations.md).
 