---
title: Teklif yayımlama | Azure Marketi
description: Belirtilen teklifi yayımlamak için API.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b7ad8086c417cf1f14d9116fa4abcb0a88030922
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819651"
---
<a name="publish-an-offer"></a>Teklif yayımlama
================

Belirtilen teklif için yayımlama işlemini başlatır. Bu çağrı uzun süredir çalışan bir işlemdir.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI parametreleri
--------------

|  **Ad**      |    **Açıklama**                               |  **Veri türü** |
|  ------------- |  ------------------------------------            |   -----------  |
|  PublisherId   | Yayımcı tanımlayıcı, örneğin `contoso`      |   Dize       |
|  OfferId       | Teklif tanımlayıcısı                                 |   Dize       |
|  api sürümü   | API 'nin en son sürümü                        |   Tarih         |
|  |  |


<a name="header"></a>Üst bilgi
------

|  **Ad**        |    **Değer**          |
|  --------        |    ---------          |
|  İçerik türü    | `application/json`    |
|  Yetkilendirme   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Gövde örneği
------------

### <a name="request"></a>İstek

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>İstek gövdesi özellikleri

|  **Ad**               |   **Açıklama**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  bildirim-e-postalar    | Yayımlama işleminin ilerleme durumunun bildirilmesi için, virgülle ayrılmış e-posta adresleri listesi. |
|  |  |


### <a name="response"></a>Yanıt

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Yanıt Üst Bilgisi

|  **Ad**             |    **Değer**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| İşlem-konum    | İşlemin geçerli durumunu öğrenmek için sorgulanabilen URL.    |
|  |  |


### <a name="response-status-codes"></a>Yanıt durum kodları

| **Kodudur** |  **Açıklama**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`-istek başarıyla kabul edildi. Yanıt, başlatılan işlemi izlemek için kullanılabilecek bir konum içerir. |
| 400   | `Bad/Malformed request`-hata yanıtı gövdesi daha fazla bilgi sağlayabilir.                                                               |
| 422   | `Un-processable entity`-yayımlanacak varlığın başarısız olduğunu belirtir.                                                        |
| 404   | `Not found`-istemci tarafından belirtilen varlık yok.                                                                              |
|  |  |
