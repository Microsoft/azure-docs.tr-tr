---
title: include dosyası
description: include dosyası
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0e2acb346fad87e0c1c7fd7de1389d8fc86206d0
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642117"
---
### <a name="create-a-user-assigned-identity"></a>Kullanıcı tarafından atanan kimlik oluşturma

[Az Identity Create][az-identity-create] komutunu kullanarak aboneliğinizde *Myacrtasksıd* adlı bir kimlik oluşturun. Daha önce bir kapsayıcı kayıt defteri oluşturmak için kullandığınız kaynak grubunu kullanabilir veya farklı bir tane oluşturabilirsiniz.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Aşağıdaki adımlarda Kullanıcı tarafından atanan kimliği yapılandırmak için, kimliğin kaynak KIMLIĞI, hizmet sorumlusu KIMLIĞI ve istemci KIMLIĞINI değişkenlerde depolamak üzere [az Identity Show][az-identity-show] komutunu kullanın.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show