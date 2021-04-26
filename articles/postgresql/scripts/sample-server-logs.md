---
title: Azure CLI betiği - PostgreSQL için Azure Veritabanı’nda sunucu günlüklerini indirme
description: Bu örnek Azure CLI betiği, bir PostgreSQL için Azure Veritabanı sunucusunun sunucu günlüklerini etkinleştirmeyi ve indirmeyi gösterir.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 3011182794df1c6d60fe286e48c3173dfcfcbe24
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605255"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Azure CLI kullanarak bir PostgreSQL için Azure Veritabanı sunucusunun yavaş sorgu günlüklerini etkinleştirme ve indirme
Bu örnek CLI betiği, tek bir PostgreSQL için Azure Veritabanı sunucusunun yavaş sorgu günlüklerini etkinleştirir ve indirir.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="sample-script"></a>Örnek betik
Bu örnek betikte, vurgulanan satırları düzenleyerek yönetici kullanıcı adını ve parolasını kendi değerlerinizle güncelleştirin. &lt; &gt; `az monitor` Komutdaki Log_file_name kendi sunucu günlük dosyası adınızla değiştirin.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Betik çalıştırıldıktan sonra aşağıdaki komutu kullanarak kaynak grubunu ve bununla ilişkili tüm kaynakları kaldırın. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Betik açıklaması
Bu betik, aşağıdaki tabloda ana hatları verilen komutları kullanır:

| **Komut** | **Notlar** |
|---|---|
| [az group create](/cli/azure/group) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az postgres server create](/cli/azure/postgres/server) | Veritabanlarını barındıran bir PostgreSQL sunucusu oluşturur. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Bir sunucu için yapılandırma değerlerini listeleyin. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Bir sunucunun yapılandırmasını güncelleştirin. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs) | Bir sunucunun günlük dosyalarını listeleyin. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs) | Günlük dosyalarını indirin. |
| [az group delete](/cli/azure/group) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar
- Azure CLI hakkında daha fazla bilgi okuyun: [Azure CLI belgeleri](/cli/azure).
- Ek betikleri deneyin: [PostgreSQL için Azure Veritabanı Azure CLI örnekleri](../sample-scripts-azure-cli.md)
- [Azure portalında sunucu günlüklerini yapılandırma ve bu günlüklere erişme](../howto-configure-server-logs-in-portal.md)
