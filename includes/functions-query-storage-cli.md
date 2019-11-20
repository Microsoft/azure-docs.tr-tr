---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: ca576290ea511dc54b89ecebef72ca2a42e9169f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329573"
---
### <a name="query-the-storage-queue"></a>Depolama kuyruğunu sorgulama

Aşağıdaki örnekte gösterildiği gibi, hesabınızdaki depolama kuyruklarını görüntülemek için [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) komutunu kullanabilirsiniz:

```azurecli-interactive
az storage queue list --output tsv
```

Bu komutun çıktısı, işlev çalıştırıldığında oluşturulan kuyruk olan `outqueue` adlı bir sıra içerir.

Sonra, bu kuyruktaki iletileri görüntülemek için [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) komutunu kullanın, örneğin:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Döndürülen dize, işlevi test etmek için gönderdiğiniz iletiyle aynı olmalıdır.

> [!NOTE]  
> Önceki örnek, Base64 olan döndürülen dizenin kodunu çözer. Bunun nedeni, kuyruk depolama bağlamalarının Azure depolama 'ya [Base64 dizeleri](../articles/azure-functions/functions-bindings-storage-queue.md#encoding)olarak yazma ve okuma.