---
title: Azure CLı ile denetim günlüklerini ve yavaş sorgu günlüklerini yapılandırma-MySQL için Azure veritabanı-esnek sunucu
description: Bu makalede, Azure CLı 'dan MySQL için Azure veritabanı esnek sunucusu 'nda yavaş sorgu günlüklerinin nasıl yapılandırılacağı ve erişesağlanacağı açıklanır.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: b42aba84dcbff795ee4ca1f8d622527e8039f27a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509170"
---
# <a name="configure-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Azure CLı kullanarak MySQL için Azure veritabanı için yavaş sorgu günlüklerini yapılandırma-esnek sunucu

> [!IMPORTANT]
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Bu makalede, Azure CLı kullanarak MySQL esnek sunucunuz için [yavaş sorgu günlüklerinin](concepts-slow-query-logs.md) nasıl yapılandırılacağı gösterilir. 

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

## <a name="configure-slow-query-logs"></a>Yavaş sorgu günlüklerini yapılandırma

>[!IMPORTANT]
> Sunucunuzun performansının yoğun bir şekilde etkilenmemesini sağlamak için, yalnızca denetim amaçlarıyla gerekli olay türlerini ve kullanıcıları günlüğe kaydetmek önerilir.

Sunucunuz için yavaş sorgu günlüklerini etkinleştirin ve yapılandırın.

```azurecli
# Turn on statement level log
az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec
# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries
az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs
az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON

```

## <a name="next-steps"></a>Sonraki adımlar
- [Yavaş sorgu günlükleri](concepts-slow-query-logs.md) hakkında bilgi edinin
