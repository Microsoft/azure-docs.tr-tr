---
title: include dosyası
description: include dosyası
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e2d63ab38bad341400538c5079fee22737cf0b8e
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562959"
---
## <a name="deploy-the-function-app-project-to-azure"></a>İşlev uygulaması projesini Azure'a dağıtma

İşlev uygulaması Azure 'da oluşturulduktan sonra, proje kodunuzu Azure 'a dağıtmak için [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) çekirdek Araçlar komutunu kullanabilirsiniz. Bu örneklerde, önceki adımda `<APP_NAME>` uygulamanızın adıyla değiştirin.

### <a name="c--javascript"></a>C\# /JavaScript

```command
func azure functionapp publish <APP_NAME>
```

### <a name="python"></a>Python

```command
func azure functionapp publish <APP_NAME> --build remote
```

### <a name="typescript"></a>TypeScript

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Aşağıdakine benzer bir çıktı görürsünüz ve bu, okunabilirlik için kesildi:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Yalnızca Azure 'da işlevinizi `HttpTrigger`test etmek için kullanabileceğiniz değerinikopyalayın.`Invoke url` URL, işlev anahtarınız `code` olan bir sorgu dizesi değeri içeriyor. Bu anahtar başkalarının Azure 'da HTTP tetikleyici uç noktanızı aramasını zorlaştırır.