---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 642989df8dca9d4ae121d80e30a9fb6d8dd06286
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800279"
---
### <a name="query-the-storage-queue"></a>Depolama kuyruğunu sorgulama

[`az storage queue list`](/cli/azure/storage/queue#az_storage_queue_list)Aşağıdaki örnekte olduğu gibi, hesabınızdaki depolama kuyruklarını görüntülemek için komutunu kullanabilirsiniz:

```azurecli-interactive
az storage queue list --output tsv
```

Bu komutun çıktısı, işlev çalıştırıldığında oluşturulan kuyruk olan adlı bir kuyruk içerir `outqueue` .

Sonra, bu [`az storage message peek`](/cli/azure/storage/message#az_storage_message_peek) kuyruktaki iletileri görüntülemek için komutunu kullanın:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Döndürülen dize, işlevi test etmek için gönderdiğiniz iletiyle aynı olmalıdır.

> [!NOTE]  
> Önceki örnek, Base64 olan döndürülen dizenin kodunu çözer. Bunun nedeni, kuyruk depolama bağlamalarının Azure depolama 'ya [Base64 dizeleri](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)olarak yazma ve okuma.