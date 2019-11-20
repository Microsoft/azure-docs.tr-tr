---
title: Özel İzleme şemaları için B2B iletilerini - Azure Logic Apps | Microsoft Docs
description: Oluşturmak için Azure Logic Apps Enterprise Integration Pack ile tümleştirme hesaplarındaki B2B iletilerini izleme özel izleme şemaları
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.date: 01/27/2017
ms.openlocfilehash: 76a9ece9e925543e856136a798a60038316caad9
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203050"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Azure Logic Apps iş akışlarında uçtan uca izleme özel izleme şemaları oluşturma

AS2 izleme veya X12 gibi işletmeler arası akışınızın farklı bölümleri için iletileri etkinleştirebilirsiniz, izleme yerleşik yoktur. Olayları ve iş akışınızı sonuna kadar baştan günlükleri özel izleme etkinleştirebilirsiniz sonra iş akışları oluşturduğunuzda bir mantıksal uygulama, BizTalk Server, SQL Server veya diğer herhangi bir katman içerir. 

Bu makalede, mantıksal uygulamanızı dışında Katmanlar kullanabileceğiniz özel kod sağlanır. 

## <a name="custom-tracking-schema"></a>Özel izleme şeması

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": "",
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {                
         }
      }
   ]
}
```

| Özellik | Gerekli | Tür | Açıklama |
| --- | --- | --- | --- |
| sourceType | Evet |   | Çalıştırma kaynağı türü. İzin verilen değerler **Microsoft.Logic/workflows** ve **custom**. |
| source | Evet |   | Kaynak türü ise **Microsoft.Logic/workflows**, kaynak bilgileri bu şemayı izlemesi gerekir. Kaynak türü ise **özel**, bir JToken şemadır. |
| systemId | Evet | String | Mantıksal uygulama sistem kimliği |
| runId | Evet | String | Mantıksal uygulama çalıştırması kimliği. |
| operationName | Evet | String | (Örneğin, bir eylem veya tetikleyici) işlemin adı. |
| repeatItemScopeName | Evet | String | Eylem içinde ise öğe adı yineleyin bir `foreach` / `until` döngü. |
| repeatItemIndex | Evet | Integer | Eylem içinde olup olmadığını bir `foreach` / `until` döngü. Yinelenen öğe dizini belirtir. |
| trackingId | Hayır | String | İletileri ilişkilendirmek için izleme kimliği. |
| correlationId | Hayır | String | İletileri ilişkilendirmek için bağıntı kimliği. |
| clientRequestId | Hayır | String | İstemci iletileri ilişkilendirmek için doldurabilirsiniz. |
| eventLevel | Evet |   | Olay düzeyi. |
| eventTime | Evet |   | Etkinliğin UTC biçiminde YYYY-AA-DDTHH:MM:SS.00000Z saati. |
| recordType | Evet |   | İzleme kayıt türü. Değer izin verilen **özel**. |
| record | Evet |   | Özel bir kayıt türü. İzin verilen biçim JToken ' dir. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B Protokolü izleme şemaları

B2B protokol şemaları izleme hakkında daha fazla bilgi için bkz:

* [AS2 izleme şemaları](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [X12 izleme şemaları](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Sonraki adımlar

* Daha fazla bilgi edinin [B2B iletilerini izleme](logic-apps-monitor-b2b-message.md)
* Hakkında bilgi edinin [Azure İzleyici günlüklerine B2B iletilerini izleme](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
