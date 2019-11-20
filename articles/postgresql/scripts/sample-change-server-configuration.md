---
title: Azure CLı betiği-sunucu yapılandırmasını değiştirme (PostgreSQL)
description: Bu örnek CLı betiği, tüm kullanılabilir sunucu yapılandırma seçeneklerini listeler ve seçeneklerden birinin değerini günceller.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: d2b54d1173b9591de2482f4b3368d3dde8b8c766
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947790"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Azure CLı kullanarak PostgreSQL için Azure veritabanı sunucusunun yapılandırmasını listeleme ve güncelleştirme
Bu örnek CLı betiği, tüm kullanılabilir yapılandırma parametrelerini ve PostgreSQL için Azure veritabanı sunucusu için izin verilen değerleri listeler ve *log_retention_days* varsayılan değer dışında bir değere ayarlar.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLı 'yi yerel olarak çalıştırmayı seçerseniz bu makale için Azure CLı 2,0 veya sonraki bir sürümü gerekir. @No__t-0 çalıştırarak sürümü denetleyin. Azure CLı sürümünüzü yüklemek veya yükseltmek için bkz. [Azure CLI 'Yı yüklemek]( /cli/azure/install-azure-cli) . 

## <a name="sample-script"></a>Örnek betik
Bu örnek betikte, vurgulanan satırları düzenleyerek yönetici kullanıcı adını ve parolasını kendi kendinize güncelleştirin.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizle
Komut dosyası çalıştırıldıktan sonra kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu kullanın. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Betik açıklaması
Bu betik aşağıdaki tabloda özetlenen komutları kullanır:

| **Komutundaki** | **Notlar** |
|---|---|
| [az Group Create](/cli/azure/group) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az Postgres Server Create](/cli/azure/postgres/server) | Veritabanlarını barındıran bir PostgreSQL sunucusu oluşturur. |
| [az Postgres sunucu yapılandırma listesi](/cli/azure/postgres/server/configuration) | PostgreSQL için Azure veritabanı sunucusunun yapılandırmasını listeleyin. |
| [az Postgres sunucu yapılandırma kümesi](/cli/azure/postgres/server/configuration) | PostgreSQL için Azure veritabanı sunucusunun yapılandırmasını güncelleştirin. |
| [az Postgres Server yapılandırma Show](/cli/azure/postgres/server/configuration) | PostgreSQL için Azure veritabanı sunucusunun yapılandırmasını gösterir. |
| [az Group Delete](/cli/azure/group) | Tüm iç içe geçmiş kaynaklar dahil olmak üzere bir kaynak grubunu siler. |

## <a name="next-steps"></a>Sonraki adımlar
- Azure CLı: [Azure CLI belgeleri](/cli/azure)hakkında daha fazla bilgi edinin.
- Ek betikleri deneyin: [PostgreSQL Için Azure veritabanı Azure CLI örnekleri](../sample-scripts-azure-cli.md)
- Sunucu parametreleri hakkında daha fazla bilgi için bkz. [Azure Portal sunucu parametrelerini yapılandırma](../howto-configure-server-parameters-using-portal.md).
