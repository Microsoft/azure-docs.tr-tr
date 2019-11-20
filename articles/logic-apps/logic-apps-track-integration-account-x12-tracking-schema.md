---
title: B2B iletilerini - Azure Logic Apps için şemalar izleme X12 | Microsoft Docs
description: İzleme için Azure Logic Apps Enterprise Integration Pack ile tümleştirme hesaplarındaki B2B iletilerini izleme şemaları X12 oluşturma
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.date: 01/27/2017
ms.openlocfilehash: 1db324006e1e6332b5fdd8afd28ebed8a32ac707
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845775"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Oluşturma X12 izleme şemaları tümleştirme hesapları Azure Logic Apps için iletileri

Yardımcı olması için İzleyici başarı, hataları ve işletmeler arası (B2B) işlemleri, ileti özelliklerini şemaları tümleştirme hesabı izleme bu X12 kullanabilirsiniz:

* X12 işlem kümesi izleme şeması
* X12 işlem kümesi bildirim izleme şeması
* X12 değişim izleme şeması
* X12 değişim bildirim izleme şeması
* X12 işlevsel Grup izleme şeması
* İzleme şeması bildirim X12 işlevsel Grup

## <a name="x12-transaction-set-tracking-schema"></a>X12 işlem kümesi izleme şeması

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages":  "",
      "segmentsCount": ""
   }
}
```

| Özellik | Tür | Açıklama |
| --- | --- | --- |
| senderPartnerName | String | Gönderen iş ortağı adı X12 ileti. (İsteğe bağlı) |
| receiverPartnerName | String | Alıcı iş ortağı adı X12 ileti. (İsteğe bağlı) |
| senderQualifier | String | İş ortağı niteleyicisi gönderin. (Zorunlu) |
| senderıdentifier | String | İş ortağı tanımlayıcısı gönderin. (Zorunlu) |
| receiverQualifier | String | İş ortağı niteleyicisi alırsınız. (Zorunlu) |
| receiverıdentifier | String | İş ortağı tanımlayıcısını alır. (Zorunlu) |
| agreementName | String | X12 adını, iletileri çözümlenen sözleşme. (İsteğe bağlı) |
| direction | Enum | İleti akışı yönünü almak veya göndermek. (Zorunlu) |
| interchangeControlNumber | String | Değişim denetim numarası. (İsteğe bağlı) |
| functionalGroupControlNumber | String | İşlevsel denetim numarası. (İsteğe bağlı) |
| transactionSetControlNumber | String | İşlem kümesi denetim numarası. (İsteğe bağlı) |
| CorrelationMessageId | String | Bağıntı ileti kimliği. {AgreementName} birleşimi {*GroupControlNumber*} {TransactionSetControlNumber}. (İsteğe bağlı) |
| messageType | String | İşlem kümesi veya belge türü. (İsteğe bağlı) |
| isMessageFailed | Boolean | Olmadığını X12 ileti başarısız oldu. (Zorunlu) |
| isTechnicalAcknowledgmentExpected | Boolean | Teknik Bildirim X12 içinde yapılandırılmış olup olmadığı sözleşme. (Zorunlu) |
| isFunctionalAcknowledgmentExpected | Boolean | İşlev bildirim X12 içinde yapılandırılmış olup olmadığı sözleşme. (Zorunlu) |
| needAk2LoopForValidMessages | Boolean | AK2 döngü için geçerli bir ileti gerekli olup olmadığı. (Zorunlu) |
| segmentsCount | Integer | Kesim X12 içinde işlem kümesi. (İsteğe bağlı) |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 işlem kümesi bildirim izleme şeması

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Özellik | Tür | Açıklama |
| --- | --- | --- |
| senderPartnerName | String | Gönderen iş ortağı adı X12 ileti. (İsteğe bağlı) |
| receiverPartnerName | String | Alıcı iş ortağı adı X12 ileti. (İsteğe bağlı) |
| senderQualifier | String | İş ortağı niteleyicisi gönderin. (Zorunlu) |
| senderıdentifier | String | İş ortağı tanımlayıcısı gönderin. (Zorunlu) |
| receiverQualifier | String | İş ortağı niteleyicisi alırsınız. (Zorunlu) |
| receiverıdentifier | String | İş ortağı tanımlayıcısını alır. (Zorunlu) |
| agreementName | String | X12 adını, iletileri çözümlenen sözleşme. (İsteğe bağlı) |
| direction | Enum | İleti akışı yönünü almak veya göndermek. (Zorunlu) |
| interchangeControlNumber | String | Değişim denetim numarası, işlev bildirim. Değer doldurur burada işlevsel bildirim alındığında gönderilen iletileri gönderme tarafı için iş ortağı için. (İsteğe bağlı) |
| functionalGroupControlNumber | String | İşlev bildirim işlevsel Grup denetim numarası. Değer doldurur burada işlevsel bildirim alındığında gönderilen iletileri gönderme tarafı için iş ortağı için. (İsteğe bağlı) |
| isaSegment | String | ISA segmenti iletinin. Değer doldurur burada işlevsel bildirim alındığında gönderilen iletileri gönderme tarafı için iş ortağı için. (İsteğe bağlı) |
| gsSegment | String | İletinin GS kesimi. Değer doldurur burada işlevsel bildirim alındığında gönderilen iletileri gönderme tarafı için iş ortağı için. (İsteğe bağlı) |
| respondingfunctionalGroupControlNumber | String | Değişim denetim numarası yanıt. (İsteğe bağlı) |
| respondingFunctionalGroupId | String | İçinde bildirim için AK101 eşleştiren işlevsel Grup Kimliği yanıt. (İsteğe bağlı) |
| respondingtransactionSetControlNumber | String | Yanıt veren işlem kümesi denetim numarası. (İsteğe bağlı) |
| respondingTransactionSetId | String | Yanıt veren işlem AK201 için bildirim içinde eşleştiren kimliği ayarlayın. (İsteğe bağlı) |
| statusCode | Boolean | Onay durum kodunu işlem kümesi. (Zorunlu) |
| segmentsCount | Enum | Onay durum kodunu. İzin verilen değerler **Accepted**, **Rejected**, ve **AcceptedWithErrors**. (Zorunlu) |
| processingStatus | Enum | İşlem durumu alındı. İzin verilen değerler **alınan**, **oluşturulan**, ve **gönderilen**. (Zorunlu) |
| CorrelationMessageId | String | Bağıntı ileti kimliği. {AgreementName} birleşimi {*GroupControlNumber*} {TransactionSetControlNumber}. (İsteğe bağlı) |
| isMessageFailed | Boolean | Olmadığını X12 ileti başarısız oldu. (Zorunlu) |
| ak2Segment | String | Bildirim için bir işlem içinde alınan işlevsel grup kümesi. (İsteğe bağlı) |
| ak3Segment | String | Veri segmenti hataları bildirir. (İsteğe bağlı) |
| ak5Segment | String | İşlem AK2 segment tanımlanan kümesi kabul ya da reddedilen ve neden bildirir. (İsteğe bağlı) |
||||

## <a name="x12-interchange-tracking-schema"></a>X12 değişim izleme şeması

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| Özellik | Tür | Açıklama |
| --- | --- | --- |
| senderPartnerName | String | Gönderen iş ortağı adı X12 ileti. (İsteğe bağlı) |
| receiverPartnerName | String | Alıcı iş ortağı adı X12 ileti. (İsteğe bağlı) |
| senderQualifier | String | İş ortağı niteleyicisi gönderin. (Zorunlu) |
| senderıdentifier | String | İş ortağı tanımlayıcısı gönderin. (Zorunlu) |
| receiverQualifier | String | İş ortağı niteleyicisi alırsınız. (Zorunlu) |
| receiverıdentifier | String | İş ortağı tanımlayıcısını alır. (Zorunlu) |
| agreementName | String | X12 adını, iletileri çözümlenen sözleşme. (İsteğe bağlı) |
| direction | Enum | İleti akışı yönünü almak veya göndermek. (Zorunlu) |
| interchangeControlNumber | String | Değişim denetim numarası. (İsteğe bağlı) |
| isaSegment | String | İleti ISA segmenti. (İsteğe bağlı) |
| isTechnicalAcknowledgmentExpected | Boolean | Teknik Bildirim X12 içinde yapılandırılmış olup olmadığı sözleşme. (Zorunlu) |
| isMessageFailed | Boolean | Olmadığını X12 ileti başarısız oldu. (Zorunlu) |
| isa09 | String | X12 belge değişim tarih. (İsteğe bağlı) |
| isa10 | String | Değişim zaman X12 belgeleyin. (İsteğe bağlı) |
| ısa11 | String | X12 Değişim Denetimi standartları tanımlayıcısı. (İsteğe bağlı) |
| ısa12 | String | X12 değişim denetim sürüm numarası. (İsteğe bağlı) |
| isa14 | String | X12 bildirim istendi. (İsteğe bağlı) |
| ısa15 | String | Test veya üretim için göstergesi. (İsteğe bağlı) |
| isa16 | String | Öğe ayırıcı. (İsteğe bağlı) |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 değişim bildirim izleme şeması

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| Özellik | Tür | Açıklama |
| --- | --- | --- |
| senderPartnerName | String | Gönderen iş ortağı adı X12 ileti. (İsteğe bağlı) |
| receiverPartnerName | String | Alıcı iş ortağı adı X12 ileti. (İsteğe bağlı) |
| senderQualifier | String | İş ortağı niteleyicisi gönderin. (Zorunlu) |
| senderıdentifier | String | İş ortağı tanımlayıcısı gönderin. (Zorunlu) |
| receiverQualifier | String | İş ortağı niteleyicisi alırsınız. (Zorunlu) |
| receiverıdentifier | String | İş ortağı tanımlayıcısını alır. (Zorunlu) |
| agreementName | String | X12 adını, iletileri çözümlenen sözleşme. (İsteğe bağlı) |
| direction | Enum | İleti akışı yönünü almak veya göndermek. (Zorunlu) |
| interchangeControlNumber | String | Değişim denetim numarası, iş ortaklarından alınan Teknik Bildirim. (İsteğe bağlı) |
| isaSegment | String | İş ortaklarından alınan Teknik Bildirim için ISA segmenti. (İsteğe bağlı) |
| respondingInterchangeControlNumber |String | Değişim denetim numarası için iş ortaklarından alınan Teknik Bildirim. (İsteğe bağlı) |
| isMessageFailed | Boolean | Olmadığını X12 ileti başarısız oldu. (Zorunlu) |
| statusCode | Enum | Onay durum kodunu değişimi. İzin verilen değerler **Accepted**, **Rejected**, ve **AcceptedWithErrors**. (Zorunlu) |
| processingStatus | Enum | Bildirim durumu. İzin verilen değerler **alınan**, **oluşturulan**, ve **gönderilen**. (Zorunlu) |
| ta102 | String | Tarih değişimi. (İsteğe bağlı) |
| ta103 | String | Zaman değişimi. (İsteğe bağlı) |
| ta105 | String | Not kod değişim. (İsteğe bağlı) |
||||

## <a name="x12-functional-group-tracking-schema"></a>X12 işlevsel Grup izleme şeması

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| Özellik | Tür | Açıklama |
| --- | --- | --- |
| senderPartnerName | String | Gönderen iş ortağı adı X12 ileti. (İsteğe bağlı) |
| receiverPartnerName | String | Alıcı iş ortağı adı X12 ileti. (İsteğe bağlı) |
| senderQualifier | String | İş ortağı niteleyicisi gönderin. (Zorunlu) |
| senderıdentifier | String | İş ortağı tanımlayıcısı gönderin. (Zorunlu) |
| receiverQualifier | String | İş ortağı niteleyicisi alırsınız. (Zorunlu) |
| receiverıdentifier | String | İş ortağı tanımlayıcısını alır. (Zorunlu) |
| agreementName | String | X12 adını, iletileri çözümlenen sözleşme. (İsteğe bağlı) |
| direction | Enum | İleti akışı yönünü almak veya göndermek. (Zorunlu) |
| interchangeControlNumber | String | Değişim denetim numarası. (İsteğe bağlı) |
| functionalGroupControlNumber | String | İşlevsel denetim numarası. (İsteğe bağlı) |
| gsSegment | String | İleti GS kesimi. (İsteğe bağlı) |
| isTechnicalAcknowledgmentExpected | Boolean | Teknik Bildirim X12 içinde yapılandırılmış olup olmadığı sözleşme. (Zorunlu) |
| isFunctionalAcknowledgmentExpected | Boolean | İşlev bildirim X12 içinde yapılandırılmış olup olmadığı sözleşme. (Zorunlu) |
| isMessageFailed | Boolean | Olmadığını X12 ileti başarısız oldu. (Zorunlu)|
| gs01 | String | İşlev tanımlayıcı kod. (İsteğe bağlı) |
| gs02 | String | Uygulama gönderen kodu. (İsteğe bağlı) |
| gs03 | String | Uygulama alıcının kodu. (İsteğe bağlı) |
| gs04 | String | İşlevsel Grup tarih. (İsteğe bağlı) |
| gs05 | String | İşlevsel Grup süre. (İsteğe bağlı) |
| gs07 | String | Sorumlu Ajans kodu. (İsteğe bağlı) |
| gs08 | String | Sürüm/yayın/sektör tanımlayıcı kod. (İsteğe bağlı) |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>İzleme şeması bildirim X12 işlevsel Grup

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| Özellik | Tür | Açıklama |
| --- | --- | --- |
| senderPartnerName | String | Gönderen iş ortağı adı X12 ileti. (İsteğe bağlı) |
| receiverPartnerName | String | Alıcı iş ortağı adı X12 ileti. (İsteğe bağlı) |
| senderQualifier | String | İş ortağı niteleyicisi gönderin. (Zorunlu) |
| senderıdentifier | String | İş ortağı tanımlayıcısı gönderin. (Zorunlu) |
| receiverQualifier | String | İş ortağı niteleyicisi alırsınız. (Zorunlu) |
| receiverıdentifier | String | İş ortağı tanımlayıcısını alır. (Zorunlu) |
| agreementName | String | X12 adını, iletileri çözümlenen sözleşme. (İsteğe bağlı) |
| direction | Enum | İleti akışı yönünü almak veya göndermek. (Zorunlu) |
| interchangeControlNumber | String | İş ortaklarından Teknik Bildirim alındığında için gönderme tarafı doldurur değişim denetim numarası. (İsteğe bağlı) |
| functionalGroupControlNumber | String | İş ortaklarından Teknik Bildirim alındığında için gönderme tarafı doldurur Teknik Bildirim işlevsel Grup denetim numarası. (İsteğe bağlı) |
| isaSegment | String | Değişim aynı numarası, ancak yalnızca belirli durumlarda doldurulmuş denetler. (İsteğe bağlı) |
| gsSegment | String | İşlevsel Grup aynı numarası, ancak yalnızca belirli durumlarda doldurulmuş denetler. (İsteğe bağlı) |
| respondingfunctionalGroupControlNumber | String | Özgün işlevsel Grup denetim numarası. (İsteğe bağlı) |
| respondingFunctionalGroupId | String | AK101 eşlenir bildirim işlevsel grubun kimliği. (İsteğe bağlı) |
| isMessageFailed | Boolean | Olmadığını X12 ileti başarısız oldu. (Zorunlu) |
| statusCode | Enum | Onay durum kodunu. İzin verilen değerler **Accepted**, **Rejected**, ve **AcceptedWithErrors**. (Zorunlu) |
| processingStatus | Enum | İşlem durumu alındı. İzin verilen değerler **alınan**, **oluşturulan**, ve **gönderilen**. (Zorunlu) |
| ak903 | String | Alınan işlem kümesi sayısı. (İsteğe bağlı) |
| ak904 | String | İşlem kümesi sayısı içinde tanımlanan işlevsel Grup kabul edildi. (İsteğe bağlı) |
| ak9Segment | String | AK1 kesimdeki tanımlanan işlevsel Grup kabul ya da reddedilen ve neden. (İsteğe bağlı) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>B2B Protokolü izleme şemaları

B2B protokol şemaları izleme hakkında daha fazla bilgi için bkz:

* [AS2 izleme şemaları](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [B2B özel izleme şemaları](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Sonraki adımlar

* Daha fazla bilgi edinin [B2B iletilerini izleme](logic-apps-monitor-b2b-message.md).
* Hakkında bilgi edinin [Azure İzleyici günlüklerine B2B iletilerini izleme](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
