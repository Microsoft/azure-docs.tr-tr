---
title: Azure CLı ile denetim günlüklerini yapılandırma-bir MySQL için Azure veritabanı-esnek sunucu
description: Bu makalede, Azure CLı 'dan MySQL için Azure veritabanı esnek sunucusu 'nda denetim günlüklerinin nasıl yapılandırılacağı ve erişebileceği açıklanır.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: 757d765f44f09eeb6f7a24644abeb1ce4577f664
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509158"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Azure CLı kullanarak MySQL için Azure veritabanı için Denetim günlüklerini yapılandırma ve erişme-esnek sunucu

> [!IMPORTANT]
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Makalede, Azure CLı kullanarak MySQL esnek sunucunuz için [Denetim günlüklerini](concepts-audit-logs.md) yapılandırma gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar
- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.
- Azure CLı 'yı en son sürüme yükler veya yükseltin. Bkz. [Azure CLI 'Yi yüklemeyi](/cli/azure/install-azure-cli).
-  [Az Login](/cli/azure/reference-index#az-login) komutunu kullanarak Azure hesabında oturum açın. Azure hesabınızın **ABONELIK kimliğini** ifade eden **ID** özelliğine göz önüne alın.

    ```azurecli-interactive
    az login
    ````

- Birden çok aboneliğiniz varsa, komutunu kullanarak sunucuyu oluşturmak istediğiniz uygun aboneliği seçin ```az account set``` .
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Komutu kullanarak henüz bir tane oluşturmadıysanız, MySQL esnek sunucusu oluşturun ```az mysql flexible-server create``` .

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="configure-audit-logging"></a>Denetim günlüğünü yapılandırma

>[!IMPORTANT]
> Sunucunuzun performansının yoğun bir şekilde etkilenmemesini sağlamak için, yalnızca denetim amaçlarıyla gerekli olay türlerini ve kullanıcıları günlüğe kaydetmek önerilir.

Denetim günlüğünü etkinleştirin ve yapılandırın.

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="next-steps"></a>Sonraki adımlar
- [Denetim günlükleri](concepts-audit-logs.md) hakkında daha fazla bilgi edinin
