---
title: Canlı git | Azure Marketi
description: Go Live API 'SI teklifi canlı Listeleme işlemini başlatır.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 30500e9dfae9411563fc727290d0569998ba3550
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819679"
---
<a name="go-live"></a>Canlı git
=======

Bu API, bir uygulamayı üretime iletme işlemini başlatır. Bu işlem genellikle uzun süredir çalışır. Bu çağrı, API 'yi [Yayımla](./cloud-partner-portal-api-publish-offer.md) işlemindeki bildirim e-posta listesini kullanır.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI parametreleri
--------------

|  **Ad**      |   **Açıklama**                                                           | **Veri türü** |
|  --------      |   ---------------                                                           | ------------- |
| PublisherId    | Örneğin `contoso` alma teklifinin yayımcı tanımlayıcısı       |  Dize       |
| OfferId        | Alma teklifinin teklif tanımlayıcısı                                   |  Dize       |
| api sürümü    | API 'nin en son sürümü                                                   |  Tarih         |
|  |  |  |


<a name="header"></a>Üst bilgi
------

|  **Ad**       |     **Değer**       |
|  ---------      |     ----------      |
| İçerik türü    | `application/json`  |
| Yetkilendirme   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Gövde örneği
------------

### <a name="response"></a>Yanıt

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Yanıt Üst Bilgisi

|  **Ad**             |      **Değer**                                                            |
|  --------             |      ----------                                                           |
| İşlem-konum    |  İşlemin geçerli durumunu öğrenmek için Sorgulanacak URL            |
|  |  |


### <a name="response-status-codes"></a>Yanıt durum kodları

| **Kodudur** |  **Açıklama**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`-istek başarıyla kabul edildi. Yanıt, işlem durumunu izlemek için bir konum içerir. |
|  400     | `Bad/Malformed request`-yanıt gövdesi içinde ek hata bilgileri bulunur. |
|  404     |  `Not found`-belirtilen varlık yok.                                       |
|  |  |
