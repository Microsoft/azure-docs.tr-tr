---
title: İşlem API 'sini iptal et | Azure Marketi
description: İşlemleri iptal et.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 374425dbd2abacb2114b5792d7476bc341fa353a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819778"
---
# <a name="cancel-operation"></a>İşlemi iptal et 

Bu API, teklifte sürmekte olan bir işlemi iptal eder. Bu API 'ye geçirilecek bir `operationId` almak için [OPERATIONS API 'Sini alma işlemini](./cloud-partner-portal-api-retrieve-operations.md) kullanın. İptali genellikle zaman uyumlu bir işlemdir, ancak bazı karmaşık senaryolarda var olan bir işlemin iptal edilmesi için yeni bir işlem gerekebilir. Bu durumda, HTTP yanıt gövdesi, durumu sorgulamak için kullanılması gereken işlemin konumunu içerir.

İsteğe ait bir e-posta adresi listesi sağlayabilirsiniz ve API, işlemin ilerleme durumuyla ilgili olarak bu adresleri bilgilendirir.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>URI parametreleri
--------------

|  **Ad**    |      **Açıklama**                                  |    **Veri türü**  |
| ------------ |     ----------------                                  |     -----------   |
| PublisherId  |  Yayımcı tanımlayıcısı, örneğin, `contoso`         |   Dize          |
| OfferId      |  Teklif tanımlayıcısı                                     |   Dize          |
| api sürümü  |  Geçerli API sürümü                               |    Tarih           |
|  |  |  |


<a name="header"></a>Üst bilgi
------

|  **Ad**              |  **Değer**         |
|  ---------             |  ----------        |
|  İçerik türü          |  uygulama/json  |
|  Yetkilendirme         |  BELIRTECINIZI taşıyıcı |
|  |  |


<a name="body-example"></a>Gövde örneği
------------

### <a name="request"></a>İstek

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>İstek gövdesi özellikleri

|  **Ad**                |  **Açıklama**                                               |
|  --------                |  ---------------                                               |
|  bildirim-e-postalar     | Yayımlama işleminin ilerleme durumunun bildirilmesi için e-posta kimliklerinin virgülle ayrılmış listesi. |
|  |  |


### <a name="response"></a>Yanıt

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Yanıt Üst Bilgisi

|  **Ad**             |    **Değer**                       |
|  ---------            |    ----------                      |
| İşlem-konum    | İşlemin geçerli durumunu öğrenmek için sorgulanabilen URL. |
|  |  |


### <a name="response-status-codes"></a>Yanıt durum kodları

| **Kodudur**  |  **Açıklama**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Tamam. İstek başarıyla işlendi ve işlem zaman uyumlu olarak iptal edildi. |
|  202      | Eden. İstek başarıyla işlendi ve işlem iptal ediliyor sürecinde. İptal işleminin konumu yanıt üst bilgisinde döndürülür. |
|  400      | Hatalı/hatalı biçimlendirilmiş istek. Hata yanıtı gövdesi daha fazla bilgi sağlayabilir.  |
|  403      | Erişim yasak. İstemcinin istekte belirtilen ad alanına erişimi yok. |
|  404      | Bulunamadı. Belirtilen varlık yok. |
|  |  |
