---
title: Teklif durumunu al | Azure Marketi
description: API, teklifin geçerli durumunu alır.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5ce546d79497f462f6c262de738036d7e3a30226
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819659"
---
<a name="retrieve-offer-status"></a>Teklif durumunu alma 
=====================

Teklifin geçerli durumunu alır.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>URI parametreleri
--------------

|  **Ad**       |   **Açıklama**                            |  **Veri türü** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  PublisherId    | Yayımcı tanımlayıcı, örneğin `Contoso`  |     Dize     |
|  OfferId        | Teklifi benzersiz bir şekilde tanımlayan GUID      |     Dize     |
|  api sürümü    | En son API sürümü                        |     Tarih       |
|  |  |


<a name="header"></a>Üst bilgi
------

|  Ad           |  Değer               |
|  -------------  | -------------------  |
|  İçerik türü   |  `application/json`  |
|  Yetkilendirme  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Gövde örneği
------------

### <a name="response"></a>Yanıt

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
      "notificationEmails": "jdoe@contoso.com"
  } 
```


### <a name="response-body-properties"></a>Yanıt gövdesi özellikleri

|  **Ad**             |    **Açıklama**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Teklifin durumu. Olası değerler listesi için bkz. [teklif durumu](#offer-status) aşağıdaki. |
|  sayısı             | Teklifle ilişkili ileti dizisi                                                    |
|  adımlar                | Teklifin bir teklif yayımlaması sırasında gittiği adımların dizisi                      |
|  Estimatedzaman çerçevesi   | Bu adımın tamamlanması için gereken süre (kolay biçimde)                       |
|  id                   | Adımın tanımlayıcısı                                                                         |
|  stepName             | Adımın adı                                                                               |
|  açıklama          | Adımın açıklaması                                                                        |
|  status               | Adımın durumu. Olası değerler listesi için aşağıdaki [adım durumuna](#step-status) bakın.    |
|  sayısı             | Adımla ilgili ileti dizisi                                                          |
|  processPercentage    | Adımın tamamlanma yüzdesi                                                              |
|  Önizleme bağlantıları         | *Şu anda uygulanmadı*                                                                    |
|  Livelmürekkepler            | *Şu anda uygulanmadı*                                                                    |
|  Notificationepostalar   | İşlemin ilerleme durumunun bildirilmesi için, virgülle ayrılmış e-posta adresleri listesi        |
|  |  |


### <a name="response-status-codes"></a>Yanıt durum kodları

| **Kodudur** |   **Açıklama**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK`-istek başarıyla işlendi ve teklifin geçerli durumu döndürüldü. |
|  400     | `Bad/Malformed request`-hata yanıtı gövdesinde daha fazla bilgi bulunabilir.                 |
|  404     | `Not found`-belirtilen varlık yok.                                                |
|  |  |


### <a name="offer-status"></a>Teklif durumu

|  **Ad**                    |    **Açıklama**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  Neveryayınlandı              | Teklif hiç yayımlanmadı.                          |
|  NotStarted                  | Teklif yenidir ve başlatılmamaktadır.                            |
|  WaitingForPublisherReview   | Teklif, yayımcı onayını bekliyor.                 |
|  Çalışıyor                     | Teklif gönderimi işleniyor.                     |
|  Başarılı oldu                   | Teklif gönderimi işlemeyi tamamladı.               |
|  İptal edildi                    | Teklif Gönderimi iptal edildi.                           |
|  Başarısız                      | Teklif gönderimi başarısız oldu.                                 |
|  |  |


### <a name="step-status"></a>Adım durumu

|  **Ad**                    |    **Açıklama**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | Adım başlatılmadı.                        |
|  Ediyor                  | Adım çalışıyor.                             |
|  WaitingForPublisherReview   | Adım, yayımcı onayını bekliyor.      |
|  WaitingForApproval          | Adım, işlem onayı bekliyor.        |
|  Engellendiğini                     | Adım engellendi.                             |
|  Reddedilecek                    | Adım reddedildi.                            |
|  Tamamlama                    | Adım tamamlanmıştır.                            |
|  İptal edildi                    | Adım iptal edildi.                           |
|  |  |
