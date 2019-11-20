---
title: Operations API 'sini alma | Azure Marketi
description: Belirtilen operationId için teklif üzerindeki tüm işlemleri alır veya belirli bir işlem al.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: c3eb77744d61322ca0aed20bb2b3f486cc02ac70
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819605"
---
<a name="retrieve-operations"></a>İşlemleri alma
===================

Belirtilen operationId için teklif üzerindeki tüm işlemleri alır veya belirli bir işlem al. İstemci, çalışan işlemleri filtrelemek için sorgu parametrelerini kullanabilir.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI parametreleri
--------------

|  **Ad**          |      **Açıklama**                                                                                           | **Veri türü** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  PublisherId       |  Yayımcı tanımlayıcı, örneğin `Contoso`                                                                   |  Dize       |
|  OfferId           |  Teklif tanımlayıcısı                                                                                              |  Dize       |
|  operationId       |  Teklif üzerinde işlemi benzersiz bir şekilde tanımlayan GUID. OperationId bu API kullanılarak alınabilir ve ayrıca, [teklif](./cloud-partner-portal-api-publish-offer.md) API 'si gibi uzun süre çalışan herhangi bir işlem IÇIN yanıtın http üst bilgisinde döndürülür.  |   Guid   |
|  filteredStatus    | Bu API tarafından döndürülen koleksiyonda duruma göre filtrelemek için kullanılan isteğe bağlı sorgu parametresi (örneğin `running`).  |   Dize |
|  api sürümü       | En son API sürümü                                                                                           |    Tarih      |
|  |  |  |


<a name="header"></a>Üst bilgi
------

|  **Ad**          |  **Değer**           |
|  ---------------   | -------------------- |
|  İçerik türü      | `application/json`   |
|  Yetkilendirme     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Gövde örneği
------------

### <a name="response"></a>Yanıt

#### <a name="get-operations"></a>İşlemleri al

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>İşlemi al

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
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
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
                "notificationEmails": "jondoe@contoso.com"
            } 
        }
    ]
```


### <a name="response-body-properties"></a>Yanıt gövdesi özellikleri

|  **Ad**                    |  **Açıklama**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | İşlemi benzersiz bir şekilde tanımlayan GUID                                                       |
|  submissionType              | Teklif için bildirilen işlem türünü tanımlar, örneğin `Publish/GGoLive`      |
|  Saati             | İşlem oluşturulduğunda UTC tarih saati                                                       |
|  lastActionDateTime          | İşlemdeki son güncelleştirme tamamlandığında UTC tarih/saati                                       |
|  status                      | İşlemin durumu, `not started` \| `running` \| `failed` \| `completed`. Tek seferde yalnızca bir işlemin durum `running` olabilir. |
|  error                       | Başarısız işlemler için hata iletisi                                                               |
|  |  |


### <a name="response-status-codes"></a>Yanıt durum kodları

| **Kodudur**  |   **Açıklama**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK`-istek başarıyla işlendi ve istenen işlem (ler) döndürüldü.        |
|  400      | `Bad/Malformed request`-hata yanıtı gövdesinde daha fazla bilgi bulunabilir.                    |
|  403      | `Forbidden`-istemcinin belirtilen ad alanına erişimi yok.                          |
|  404      | `Not found`-belirtilen varlık yok.                                                 |
|  |  |
