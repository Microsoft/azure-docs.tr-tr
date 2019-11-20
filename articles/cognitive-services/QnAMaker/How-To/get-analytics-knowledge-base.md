---
title: Bilgi Bankası 'nda analiz-Soru-Cevap Oluşturma
titleSuffix: Azure Cognitive Services
description: Soru-Cevap Oluşturma, Soru-Cevap Oluşturma hizmetinizin oluşturulması sırasında Application Insights 'ı etkinleştirdiyseniz tüm sohbet günlüklerini ve diğer telemetrileri depolar. Sohbet günlüklerinizi App Insights 'tan almak için örnek sorguları çalıştırın.
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 72d2598c1ff17f80fc264e6d547a799ab74a163f
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621971"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Bilgi bankanız hakkında analizler alma

Soru-Cevap Oluşturma, [soru-cevap oluşturma hizmetinizin oluşturulması](./set-up-qnamaker-service-azure.md)sırasında Application Insights 'ı etkinleştirdiyseniz tüm sohbet günlüklerini ve diğer telemetrileri depolar. Sohbet günlüklerinizi App Insights 'tan almak için örnek sorguları çalıştırın.

1. App Insights kaynağına gidin.

    ![Application Insights kaynağınızı seçin](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. **Günlük (Analiz)** öğesini seçin. Soru-Cevap Oluşturma Telemetriyi Sorgulayabileceğiniz yeni bir pencere açılır.

3. Aşağıdaki sorguyu yapıştırın ve çalıştırın.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    Sorguyu çalıştırmak için **Çalıştır** ' ı seçin.

    [kullanıcılardan gelen soruları, yanıtları ve puanı öğrenmek için ![sorgu Çalıştır](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Soru-Cevap Oluşturma bilgi bankasında diğer analizler için sorgular çalıştırma

### <a name="total-90-day-traffic"></a>Toplam 90 günlük trafik

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer" 
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Belirli bir dönemdeki toplam soru trafiği

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST" 
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer" 
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Kullanıcı trafiği

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId 
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Soruların gecikme dağılımı

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>Yanıtlanmayan sorular

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0"
| project timestamp, KbId, question, answer, score 
| order  by timestamp  desc
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Capactiy seçin](../tutorials/choosing-capacity-qnamaker-deployment.md)
