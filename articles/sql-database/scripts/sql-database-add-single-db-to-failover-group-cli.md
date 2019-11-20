---
title: CLı örneği-yük devretme grubuna tek veritabanı ekleme-Azure SQL veritabanı
description: Azure CLı örnek betiği Azure SQL veritabanı tek veritabanı oluşturma, bir yük devretme grubuna ekleme ve yük devretme testi.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 8e3c525230c3de530a93bd61a9227e9a4d7ed10b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933424"
---
# <a name="use-cli-to-add-an-azure-sql-database-single-database-into-a-failover-group"></a>CLı kullanarak bir yük devretme grubuna Azure SQL veritabanı tek veritabanı ekleme

Bu PowerShell betiği örneği tek bir veritabanı oluşturur, bir yük devretme grubu oluşturur, veritabanını buna ekler ve yük devretmeyi sınar. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu konu, Azure CLI 2.0 veya sonraki bir sürümünü kullanmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yi yükleme]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to failover group")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu kullanın.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az Account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Aboneliği geçerli etkin abonelik olacak şekilde ayarlar. | 
| [az group create](/cli/azure/group#az-group-create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Tek veritabanları ve elastik havuzlar barındıran bir SQL veritabanı sunucusu oluşturur. |
| [az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Sunucunun güvenlik duvarı kurallarını oluşturur. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Bir veritabanı oluşturur. | 
| [az SQL yük devretme-Grup oluşturma](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Bir yük devretme grubu oluşturur. | 
| [az SQL yük devretme-Grup listesi](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Bir sunucudaki yük devretme gruplarını listeler. |
| [az SQL yük devretme-grup kümesi-birincil](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Geçerli birincil sunucudaki tüm veritabanlarının yükünü devretmek için yük devretme grubunun birincil kısmını ayarlayın. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure).

Ek SQL Veritabanı CLI betiği örnekleri, [Azure SQL Veritabanı belgeleri](../sql-database-cli-samples.md) içinde bulunabilir.
