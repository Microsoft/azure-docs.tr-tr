---
author: baanders
description: Azure dijital TWINS örneği temizlemek için ekleme dosyası
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0d8cc30c0511098caf7b6c47d7f7bd400dc32f1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800084"
---
* **Bu öğreticide oluşturduğunuz kaynakların hiçbirine ihtiyacınız** yoksa, [az Group Delete](/cli/azure/group#az_group_delete) komutuyla bu makaledeki Azure dijital TWINS örneğini ve diğer tüm kaynakları silebilirsiniz. Bu, kaynak grubunun yanı sıra kaynak grubundaki tüm Azure kaynaklarını da siler.
    
    > [!IMPORTANT]
    > Silinen kaynak grupları geri alınamaz. Kaynak grubu ve içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun.
    
    [Azure Cloud Shell](https://shell.azure.com)açın ve kaynak grubunu ve içerdiği her şeyi silmek için aşağıdaki komutu çalıştırın.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```