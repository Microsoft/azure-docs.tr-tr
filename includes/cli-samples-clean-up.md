---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/21/2018
ms.author: cephalin
ms.openlocfilehash: e7494db94c7d8f0fc610ab297798749bffd55e7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997976"
---
## <a name="clean-up-resources"></a>Kaynakları temizleme

Önceki adımlarda, bir kaynak grubunda Azure kaynakları oluşturdunuz. Bu kaynakların gelecekte gerekli olacağını düşünmüyorsanız, Cloud Shell’de aşağıdaki komutu çalıştırarak kaynak grubunu silin:

```azurecli-interactive
az group delete --name myResourceGroup
```

Bu komutun çalıştırılması bir dakika sürebilir.