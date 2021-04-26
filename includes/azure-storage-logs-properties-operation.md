---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 2474b8920c5387c7896b413f229c2f5b06cdafb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011192"
---
| Özellik | Açıklama |
|:--- |:---|
|**ışınızda** | İsteğin depolama tarafından alındığı evrensel zaman eşgüdümlü (UTC) saati. Örneğin: `2018/11/08 21:09:36.6900118`.|
|**RESOURCEID** | Depolama hesabının kaynak KIMLIĞI. Örnek: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**alan** | İstenen işlemin kategorisi. Örneğin: `StorageRead` , `StorageWrite` , veya `StorageDelete` .|
|**operationName** | Gerçekleştirilen REST işleminin türü. <br> İşlemlerin tüm listesi için bkz. [depolama Analizi günlüğe kaydedilmiş işlemler ve durum iletileri konusu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | İstek yapıldığında belirtilen depolama hizmeti sürümü. Bu, **x-MS-Version** üst bilgisinin değerine eşdeğerdir. Örneğin: `2017-04-17`.|
|**schemaVersion** | Günlüğün şema sürümü. Örneğin: `1.0`.|
|**statusCode** | İsteğin HTTP durum kodu. İstek kesintiye uğrarsa, bu değer olarak ayarlanabilir `Unknown` . <br> Örnek: `206` |
|**statusText** | İstenen işlemin durumu.  Durum iletilerinin tamamı listesi için bkz. [depolama Analizi günlüğe kaydedilmiş işlemler ve durum iletileri konusu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). Sürüm 2017-04-17 ve sonrasında durum iletisi `ClientOtherError` kullanılmaz. Bunun yerine, bu alan bir hata kodu içerir. Örnek: `SASSuccess`  |
|**Ort** | İstenen işlemi gerçekleştirmek için milisaniye olarak ifade edilen toplam süre. Bu, gelen isteği okuma ve yanıtı istek sahibine gönderme süresini içerir. Örneğin: `12`.|
|**callerIpAddress** | İstek sahibinin IP adresi (bağlantı noktası numarası dahil). Örneğin: `192.100.0.102:4362`. |
|**ID** | Kaynaklar arasında günlükleri ilişkilendirmek için kullanılan KIMLIK. Örneğin: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**konumuna** | Depolama hesabının konumu. Örneğin: `North Europe`. |
|**Protocol**|İşlemde kullanılan protokol. Örneğin: `HTTP` ,, `HTTPS` `SMB` , veya `NFS`|
| **kullanılmamışsa** | İstenen Tekdüzen Kaynak tanımlayıcısı. |