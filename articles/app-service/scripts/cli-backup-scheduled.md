---
title: 'CLı: zamanlanmış yedekleme oluşturma'
description: Azure CLı kullanarak App Service uygulamanızın dağıtımını ve yönetimini otomatik hale getirmeyi öğrenin. Bu örnek, bir uygulama için zamanlanmış yedeklemenin nasıl oluşturulacağını gösterir.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: db1d0558f93b203af1605663533847d32afbcffb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782568"
---
# <a name="create-a-scheduled-backup-for-an-app-service-app-using-cli"></a>CLı kullanarak bir App Service uygulaması için zamanlanmış yedekleme oluşturma

Bu örnek betik, App Service içinde ilgili kaynakları içeren bir uygulama oluşturur ve sonra bu için zamanlanmış bir yedekleme oluşturur. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-scheduled/backup-scheduled.sh?highlight=3-7 "Create a scheduled backup for an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [`az storage account create`](/cli/azure/storage/account#az_storage_account_create) | Bir depolama hesabı oluşturur. |
| [`az storage container create`](/cli/azure/storage/container#az_storage_container_create) | Bir Azure depolama kapsayıcısı oluşturur. |
| [`az storage container generate-sas`](/cli/azure/storage/container#az_storage_container_generate_sas) | Azure depolama kapsayıcısı için bir SAS belirteci oluşturur.  |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | App Service planı oluşturur. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | App Service uygulaması oluşturur. |
| [`az webapp config backup update`](/cli/azure/webapp/config/backup#az_webapp_config_backup_update) | App Service uygulaması için yeni bir yedekleme zamanlaması yapılandırır. |
| [`az webapp config backup show`](/cli/azure/webapp/config/backup#az_webapp_config_backup_show) | Bir App Service uygulamasının Yedekleme zamanlamasını gösterir. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az_webapp_config_backup_list) | App Service bir uygulama için yedeklemelerin listesini alır. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek App Service CLI betik örnekleri, [Azure App Service belgelerinde](../samples-cli.md) bulunabilir.
