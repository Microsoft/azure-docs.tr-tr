---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4dd43c5bcc5a0e9a734db4ca9a4b3d7137f85250
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203132"
---
## <a name="create-an-azure-storage-account"></a>Azure Depolama hesabı oluşturma

İşlevler, işlevlerinizin durumunu ve diğer bilgilerini korumak için genel amaçlı bir Azure Depolama hesabı kullanır. Oluşturduğunuz kaynak grubunda [az storage account create](/cli/azure/storage/account#az-storage-account-create) komutunu kullanarak genel amaçlı depolama hesabı oluşturun.

Aşağıdaki komutta `<storage_name>` yer tutucusunu gördüğünüz yere genel olarak benzersiz bir depolama hesabı adı yazın. Depolama hesabı adları 3 ile 24 karakter arasında olmalı ve yalnızca sayıyla küçük harf içermelidir.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
