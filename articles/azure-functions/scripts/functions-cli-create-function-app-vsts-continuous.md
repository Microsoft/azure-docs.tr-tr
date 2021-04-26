---
title: DevOps dağıtımı ile bir işlev uygulaması oluşturma-Azure CLı
description: Bir İşlev Uygulaması oluşturma ve işlev kodunu Azure DevOps'tan dağıtma
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f31c6a76412939d179cdd282e5e643ab7e8531b5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786234"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Azure DevOps 'tan dağıtılan Azure 'da bir işlev oluşturma

Bu konu başlığı altında, [Tüketim planını](../consumption-plan.md)kullanarak [sunucusuz](https://azure.microsoft.com/solutions/serverless/) bir Işlev uygulaması oluşturmak için Azure işlevlerinin nasıl kullanılacağı gösterilmektedir. İşlevleriniz için bir kapsayıcı olan işlev uygulaması, sürekli olarak bir Azure DevOps deposundan dağıtılır. 

Bu konuyu tamamlamak için şunlara sahip olmalısınız:

* İşlev uygulaması projenizi içeren ve yönetim izinlerine sahip olduğunuz bir Azure DevOps deposu.
* Azure DevOps deponuza erişmek için bir [kişisel erişim belirteci (PAT)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Bu öğretici, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür. 

## <a name="sample-script"></a>Örnek betik

Bu örnek, bir Azure İşlev uygulaması oluşturur ve işlev kodunu Azure DevOps'tan dağıtır.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, bir kaynak grubu, depolama hesabı, işlev uygulaması ve tüm ilgili kaynakları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | İşlev uygulaması için gereken depolama hesabını oluşturur. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Sunucusuz [Tüketim planında](../consumption-plan.md)bir işlev uygulaması oluşturur. |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config) | Bir işlev uygulamasını Git veya Mercurial deposu ile ilişkilendirir. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek Azure İşlevleri CLI betiği örnekleri, [Azure İşlevleri belgelerinde](../functions-cli-samples.md) bulunabilir.
