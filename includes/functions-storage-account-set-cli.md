---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 07780c94960c581ce69e4fdd45b346c75ab8b098
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329548"
---
### <a name="set-the-storage-account-connection"></a>Depolama hesabı bağlantısını ayarla

Local. Settings. json dosyasını açın ve depolama hesabı bağlantı dizesi olan `AzureWebJobsStorage` değerini kopyalayın. Bu Bash komutunu kullanarak `AZURE_STORAGE_CONNECTION_STRING` ortam değişkenini bağlantı dizesine ayarlayın:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

@No__t-0 ortam değişkeninde bağlantı dizesini ayarladığınızda, her seferinde kimlik doğrulaması sağlamak zorunda kalmadan depolama hesabınıza erişebilirsiniz.