---
title: AS2 izleme şemaları için B2B iletilerini - Azure Logic Apps | Microsoft Docs
description: Oluşturmak için Azure Logic Apps Enterprise Integration Pack ile tümleştirme hesaplarındaki B2B iletilerini izleme AS2 izleme şemaları
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 180d90450497b38f107f3601944385a003f50282
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845792"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>AS2 iletilerini ve Mdn'leri tümleştirme hesapları için Azure Logic Apps izleme şemaları oluşturma

Yardımcı olmak için başarı, hatalar ve ileti özelliklerini işletmeden işletmeye (B2B) işlemleri için izleme, bu AS2 izleme şemaları tümleştirme hesabınızdaki kullanabilirsiniz:

* AS2 ileti izleme şeması
* AS2 MDN izleme şeması

## <a name="as2-message-tracking-schema"></a>AS2 ileti izleme şeması

```json
{
   "agreementProperties": {  
      "senderPartnerName": "",  
      "receiverPartnerName": "",  
      "as2To": "",  
      "as2From": "",  
      "agreementName": ""  
   },  
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Özellik | Tür | Açıklama |
| --- | --- | --- |
| senderPartnerName | String | AS2 ileti gönderen iş ortağı adı. (İsteğe bağlı) |
| receiverPartnerName | String | AS2 ileti alıcısı'nın ortak adı. (İsteğe bağlı) |
| as2To | String | AS2 iletisinin başlıklarından AS2 ileti alıcısı'nın adı. (Zorunlu) |
| as2From | String | AS2 iletisinin başlıklarından AS2 iletiyi gönderenin adı. (Zorunlu) |
| agreementName | String | İletileri çözümlendiği AS2 sözleşmesi adı. (İsteğe bağlı) |
| direction | String | İleti akışı yönünü almak veya göndermek. (Zorunlu) |
| messageId | String | AS2 ileti kimliği, üstbilgileri AS2 iletisinin (isteğe bağlı) |
| dispositionType |String | İleti değerlendirme bildirim (MDN) değerlendirme türü değeri. (İsteğe bağlı) |
| fileName | String | AS2 iletisinin üstbilgisinden dosya adı. (İsteğe bağlı) |
| isMessageFailed |Boolean | AS2 iletisinin mi başarısız. (Zorunlu) |
| isMessageSigned | Boolean | AS2 iletisinin imzalanmış olup. (Zorunlu) |
| isMessageEncrypted | Boolean | AS2 iletisinin şifrelenmiş olan. (Zorunlu) |
| isMessageCompressed |Boolean | AS2 iletisinin sıkıştırılmış olan. (Zorunlu) |
| correlationMessageId | String | İletileri Mdn'leri ile ilişkilendirmek için AS2 ileti kimliği. (İsteğe bağlı) |
| incomingHeaders |JToken sözlüğü | Gelen AS2 ileti üst bilgisi ayrıntıları. (İsteğe bağlı) |
| outgoingHeaders |JToken sözlüğü | AS2 ileti üst bilgisi ayrıntıları giden. (İsteğe bağlı) |
| isNrrEnabled | Boolean | Değer olmayan biliniyorsa varsayılan değeri kullanın. (Zorunlu) |
| isMdnExpected | Boolean | Değer olmayan biliniyorsa varsayılan değeri kullanın. (Zorunlu) |
| mdnType | Enum | İzin verilen değerler **NotConfigured**, **Sync**, ve **Async**. (Zorunlu) |
||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN izleme şeması

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": "g"
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Özellik | Tür | Açıklama |
| --- | --- | --- |
| senderPartnerName | String | AS2 ileti gönderen iş ortağı adı. (İsteğe bağlı) |
| receiverPartnerName | String | AS2 ileti alıcısı'nın ortak adı. (İsteğe bağlı) |
| as2To | String | AS2 iletisinin aldığı iş ortağı adı. (Zorunlu) |
| as2From | String | AS2 ileti gönderen iş ortağı adı. (Zorunlu) |
| agreementName | String | İletileri çözümlendiği AS2 sözleşmesi adı. (İsteğe bağlı) |
| direction |String | İleti akışı yönünü almak veya göndermek. (Zorunlu) |
| messageId | String | AS2 ileti kimliği. (İsteğe bağlı) |
| originalMessageId |String | AS2 özgün ileti kimliği. (İsteğe bağlı) |
| dispositionType | String | MDN değerlendirme türü değeri. (İsteğe bağlı) |
| isMessageFailed |Boolean | AS2 iletisinin mi başarısız. (Zorunlu) |
| isMessageSigned |Boolean | AS2 iletisinin imzalanmış olup. (Zorunlu) |
| isNrrEnabled | Boolean | Değer olmayan biliniyorsa varsayılan değeri kullanın. (Zorunlu) |
| statusCode | Enum | İzin verilen değerler **kabul edilen**, **reddedildi**, ve **AcceptedWithErrors**. (Zorunlu) |
| micVerificationStatus | Enum | İzin verilen değerler **çıktı**, **başarılı**, ve **başarısız**. (Zorunlu) |
| correlationMessageId | String | Bağıntı Kimliği Özgün kimliği messaged (ileti kimliği iletinin MDN yapılandırılır). (İsteğe bağlı) |
| incomingHeaders | JToken sözlüğü | Gelen ileti üst bilgisi ayrıntıları gösterir. (İsteğe bağlı) |
| outgoingHeaders |JToken sözlüğü | Giden ileti üst bilgisi ayrıntıları gösterir. (İsteğe bağlı) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B Protokolü izleme şemaları

B2B protokol şemaları izleme hakkında daha fazla bilgi için bkz:

* [X12 izleme şemaları](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B özel izleme şemaları](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Sonraki adımlar

* Hakkında bilgi edinin [B2B iletilerini izleme](logic-apps-monitor-b2b-message.md)
* Hakkında bilgi edinin [Azure İzleyici günlüklerine B2B iletilerini izleme](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)