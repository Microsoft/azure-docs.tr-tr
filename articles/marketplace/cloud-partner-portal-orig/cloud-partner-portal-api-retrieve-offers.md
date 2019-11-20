---
title: Teklif API 'sini alma | Azure Marketi
description: API, bir yayımcı ad alanı altında tekliflerin özetlenmiş bir listesini alır.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5c94c03a63936be2b086085a1e52064dedf214b0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819609"
---
<a name="retrieve-offers"></a>Teklifleri alma
===============

Bir yayımcı ad alanı altındaki tekliflerin özetlenen listesini alır.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>URI parametreleri
--------------

| **Ad**         |  **Açıklama**                         |  **Veri türü** |
| -------------    |  ------------------------------------    |  -----------   |
|  PublisherId     | Yayımcı tanımlayıcı, örneğin `contoso` |   Dize    |
|  api sürümü     | En son API sürümü                    |    Tarih        |
|  |  |


<a name="header"></a>Üst bilgi
------

|  **Ad**        |         **Değer**       |
|  --------------- |       ----------------  |
|  İçerik türü    | `application/json`      |
|  Yetkilendirme   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Gövde örneği
------------

### <a name="response"></a>Yanıt

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Yanıt gövdesi özellikleri

|  **Ad**       |       **Açıklama**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  Offertypeıd    | Teklifin türünü tanımlar                                                                                           |
|  PublisherId    | Yayımcıyı benzersiz bir şekilde tanımlayan tanımlayıcı                                                                      |
|  status         | Teklifin durumu. Olası değerler listesi için bkz. [teklif durumu](#offer-status) aşağıdaki.                         |
|  id             | Yayımcıyı yayımcı ad alanında benzersiz şekilde tanımlayan GUID.                                                    |
|  version        | Teklifin geçerli sürümü. Sürüm özelliği istemci tarafından değiştirilemiyor. Her yayımladıktan sonra artırılır. |
|  Tanımı     | İş yükünün gerçek tanımının özetlenen görünümünü içerir. Ayrıntılı bir tanım almak için, belirli bir [teklifi al](./cloud-partner-portal-api-retrieve-specific-offer.md) API 'sini kullanın. |
|  changedTime    | Teklifin en son değiştirildiği UTC saati                                                                              |
|  |  |


### <a name="response-status-codes"></a>Yanıt durum kodları

| **Kodudur**  |  **Açıklama**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`-istek başarıyla işlendi ve yayımcının altındaki tüm teklifler istemciye geri döndü.  |
|  400      | `Bad/Malformed request`-hata yanıtı gövdesinde daha fazla bilgi bulunabilir.                                    |
|  403      | `Forbidden`-istemcinin belirtilen ad alanına erişimi yok.                                          |
|  404      | `Not found`-belirtilen varlık yok.                                                                 |
|  |  |


### <a name="offer-status"></a>Teklif durumu

|  **Ad**                    | **Açıklama**                                  |
|  ------------------------    | -----------------------------------------------  |
|  Neveryayınlandı              | Teklif hiç yayımlanmadı.                  |
|  NotStarted                  | Teklif yenidir ancak başlatılmamış.                 |
|  WaitingForPublisherReview   | Teklif, yayımcı onayını bekliyor.         |
|  Çalışıyor                     | Teklif gönderimi işleniyor.             |
|  Başarılı oldu                   | Teklif gönderimi işlemeyi tamamladı.       |
|  İptal edildi                    | Teklif Gönderimi iptal edildi.                   |
|  Başarısız                      | Teklif gönderimi başarısız oldu.                         |
|  |  |
