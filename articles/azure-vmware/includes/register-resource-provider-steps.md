---
title: Azure VMware çözüm kaynağı sağlayıcısını kaydetme
description: Azure VMware Çözüm kaynak sağlayıcısını kaydetme adımları.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: d2363ca2672f7f668d8f9b3816447f316d8b7347
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555960"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Azure VMware çözümünü kullanmak için, öncelikle kaynak sağlayıcısını aboneliğinize kaydetmeniz gerekir. Kaynak sağlayıcıları hakkında daha fazla bilgi için bkz. [Azure kaynak sağlayıcıları ve türleri](../../azure-resource-manager/management/resource-providers-and-types.md).


### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure portalı menüsünde **Tüm hizmetler**’i seçin.

1. **Tüm hizmetler** kutusunda, **abonelik** girin ve ardından **abonelikler**' i seçin.

1. Görüntülenecek abonelik listesinden aboneliği seçin.

1. **Kaynak sağlayıcıları** ' nı seçin ve aramaya **Microsoft. AVS** girin. 
 
1. Kaynak sağlayıcı kayıtlı değilse, **Kaydet**' i seçin.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı kullanmaya başlamak için:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure CLı aracılığıyla Azure VMware Çözüm dağıtımı için kullandığınız Azure aboneliğinde oturum açın. `Microsoft.AVS` [Az Provider Register](/cli/azure/provider#az_provider_register) komutuyla kaynak sağlayıcısını kaydedin:

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Kullanılabilir tüm sağlayıcıları görmek için [az Provider List](/cli/azure/provider#az_provider_list) komutunu kullanabilirsiniz.

---


 
