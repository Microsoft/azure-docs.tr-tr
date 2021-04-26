---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7f51b7fe95445cbd3a8aff530f89ce55b5abfb1e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629882"
---
Giden istekler oluşturmak için bir HTTP proxy 'si yapılandırmanız gerekiyorsa şu iki bağımsız değişkeni kullanın:

| Name | Veri türü | Açıklama |
|--|--|--|
|HTTP_PROXY|string|Kullanılacak proxy, örneğin, `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|string|Proxy için kimlik doğrulaması yapmak için gereken tüm kimlik bilgileri, örneğin, `username:password` . Bu değer **küçük harf** olmalıdır. |
|`<proxy-user>`|string|Proxy için Kullanıcı.|
|`<proxy-password>`|string|`<proxy-user>`Ara sunucu için ile ilişkili parola.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
