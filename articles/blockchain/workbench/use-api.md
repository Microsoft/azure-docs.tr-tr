---
title: Azure Blockchain Workbench REST API'sini kullanma
description: Azure blok zinciri çalışma ekranı önizlemesi 'nin nasıl kullanılacağına yönelik senaryolar REST API
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 2f751cc4f0037b8d60034636a0c512989113e441
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72328867"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Azure blok zinciri çalışma ekranı önizleme 'yi kullanma REST API

Azure blok zinciri çalışma ekranı önizlemesi REST API geliştiricilere ve bilgi çalışanlarına, blok zinciri uygulamalarına yönelik zengin tümleştirmeler oluşturma olanağı sağlar. Bu belgede size, Workbench REST API'sinin önemli bazı yöntemlerinde yol gösterilir. Örneğin, bir geliştiricinin özel bir blok zinciri istemcisi oluşturmak istediğini varsayalım. Bu blok zinciri istemcisi, oturum açan kullanıcıların atanan blok zinciri uygulamalarını görüntülemesine ve bunlarla etkileşime geçmesini sağlar. İstemci kullanıcıların anlaşma örneklerini görüntülemesine ve akıllı anlaşmalarla ilgili işlemler yapmasına olanak tanır. İstemci, aşağıdaki işlemleri yapmak için oturum açmış kullanıcı bağlamında REST API çalışma ekranı kullanır:

* Uygulamaları listeleme
* Uygulama için iş akışlarını listeleme
* İş akışı için akıllı anlaşma örneklerini listeleme
* Anlaşma için kullanılabilir eylemleri listeleme
* Anlaşma için bir eylem yürütme

Senaryolarda kullanılan örnek blok zinciri uygulamaları [GitHub 'dan indirilebilir](https://github.com/Azure-Samples/blockchain).

## <a name="list-applications"></a>Uygulamaları listeleme

Bir Kullanıcı blok zinciri istemcisinde oturum açtıktan sonra, ilk görev Kullanıcı için tüm blok zinciri çalışma ekranı uygulamalarını almak olur. Bu senaryoda, kullanıcının iki uygulamaya erişimi vardır:

1. [Varlık aktarımı](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [Soğutma ulaşım](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

[Applications GET API'sini](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) kullanın:

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

Yanıt, bir kullanıcının blok zinciri çalışma ekranı 'nda erişebileceği tüm blok zinciri uygulamalarını listeler. Blok zinciri çalışma ekranı yöneticileri tüm blok zinciri uygulamasını alır. Çalışma ekranı olmayan yöneticiler, en az bir ilişkili uygulama rolüne ya da ilişkili bir akıllı sözleşme örnek rolüne sahip oldukları tüm blok zincirlerini alırlar.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Uygulama için iş akışlarını listeleme

Kullanıcı uygun blok zinciri uygulamasını (örneğin, **varlık aktarımı**) seçtikten sonra, blok zinciri istemcisi belirli blok zinciri uygulamasının tüm iş akışlarını alır. Ardından, iş akışının tüm akıllı anlaşma örneklerinin gösterilmesi için kullanıcılar uygun iş akışını seçer. Her blok zinciri uygulamasının bir veya birden çok iş akışı vardır ve her iş akışının sıfır veya akıllı anlaşma örnekleri bulunur. Yalnızca bir iş akışına sahip bir blok zinciri istemci uygulaması için, kullanıcıların uygun iş akışını seçmesini sağlayan kullanıcı deneyimi akışının atlanmasını öneririz. Bu durumda, **varlık aktarımının** **varlık aktarımı**olarak da bilinen yalnızca bir iş akışı vardır.

[Applications Workflows GET API'sini](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) kullanın:

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

Yanıtta kullanıcının Blockchain Workbench'te erişimi olan belirli blok blok zinciri uygulamasının tüm iş akışları listelenir. Blok zinciri çalışma ekranı yöneticileri tüm blok zinciri iş akışını alır. Çalışma ekranı olmayan yöneticiler, en az bir ilişkili uygulama rolüne sahip oldukları veya bir akıllı sözleşme örneği rolüyle ilişkilendirilen tüm iş akışlarını alırlar.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>İş akışı için akıllı anlaşma örneklerini listeleme

Kullanıcı ilgili iş akışını seçtikten sonra, bu durum **varlık aktarımı**, blok zinciri istemcisi belirtilen iş akışı için tüm akıllı sözleşme örneklerini alır. Bu bilgileri iş akışının tüm akıllı sözleşme örneklerini göstermek için kullanabilirsiniz. Ya da kullanıcıların gösterilen akıllı sözleşme örneklerinin herhangi birine derinlemesine bir şekilde gidebilsin. Bu örnekte, kullanıcının bir eylem yapmak için akıllı anlaşma örneklerinden biriyle etkileşimli çalışmak istediğini düşünün.

[Contracts GET API'sini](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) kullanın:

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

Yanıtta belirtilen iş akışının tüm akıllı anlaşma örnekleri listelenir. Çalışma ekranı yöneticileri tüm akıllı sözleşme örneklerini alır. Çalışma ekranı olmayan yöneticiler, en az bir ilişkili uygulama rolüne sahip oldukları veya bir akıllı sözleşme örneği rolüyle ilişkilendirilen her akıllı sözleşme örneğini alır.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Anlaşma için kullanılabilir eylemleri listeleme

Bir Kullanıcı bir sözleşmeye derinlemesine bakış yapmaya karar verdiğinde, blok zinciri istemcisi sözleşmenin durumuna verilen kullanıcı eylemlerini gösterebilir. Bu örnekte, kullanıcı oluşturduğu yeni akıllı anlaşmanın tüm kullanılabilir eylemlerine bakıyor:

* Modify: Kullanıcının, bir varlığın açıklamasını ve fiyatını değiştirmesine olanak tanır.
* Sonlandır: kullanıcının varlık sözleşmesini sonlandırmasına Izin verir.

[Contract Action GET API'sini](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) kullanın:

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

Yanıtta belirtilen akıllı anlaşma örneğinin geçerli durumuna göre kullanıcının gerçekleştirebileceği tüm eylemler listelenir. Belirtilen akıllı anlaşma örneğinin geçerli durumuna göre ilişkili bir uygulama rolü olan veya akıllı anlaşma örneği rolüyle ilişkili olan kullanıcılar tüm uygun eylemleri alır.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Anlaşma için bir eylem yürütme

Ardından kullanıcı belirtilen akıllı anlaşma örneği için bir eylem gerçekleştirmeye karar verebilir. Bu durumda, bir kullanıcının bir varlığın açıklamasını ve fiyatını aşağıdaki eyleme değiştirmek istediğiniz senaryoyu göz önünde bulundurun:

* Açıklama: "My updated car"
* Fiyat: 54321

[Contract Action POST API'sini](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) kullanın:

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Kullanıcılar ancak belirtilen akıllı anlaşma örneğinin geçerli durumuna ve kullanıcının ilişkili uygulama rolüne veya akıllı anlaşma örneği rolüne göre eylem yürütebilir. Gönderi başarılı olursa, yanıt gövdesi olmayan bir HTTP 200 OK yanıtı döndürülür.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench REST API'si başvurusu](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)