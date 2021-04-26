---
title: "CLı: GitHub 'dan sürekli dağıtım"
description: Azure CLı kullanarak App Service uygulamanızın dağıtımını ve yönetimini otomatik hale getirmeyi öğrenin. Bu örnek, GitHub 'dan CI/CD ile bir uygulamanın nasıl oluşturulacağını gösterir.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/02/2019
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: b67d05951a4fc1a0ed2d625b2406ca5d0bc0fe34
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762308"
---
# <a name="create-an-app-service-app-with-continuous-deployment-from-github-using-cli"></a>CLı kullanarak GitHub 'dan sürekli dağıtım ile bir App Service uygulaması oluşturma

Bu örnek betik, ilgili kaynaklarıyla birlikte App Service bir uygulama oluşturur ve sonra bir GitHub deposundan sürekli dağıtımı ayarlar. Sürekli dağıtım olmadan GitHub dağıtımı için bkz. [bir uygulama oluşturma ve GitHub 'dan kod dağıtma](cli-deploy-github.md). Bu örnekte şunlar gereklidir:

* Yönetme izinlerine sahip olduğunuz uygulama kodu ile bir GitHub deposu. Otomatik derlemeler almak için, deponuzu [hazırlama](../deploy-continuous-deployment.md#prepare-your-repository) tablonuza göre yapısını yapın.
* GitHub hesabınız için bir [Kişisel Erişim Belirteci (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "Create an app with continuous deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | App Service planı oluşturur. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | App Service uygulaması oluşturur. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Bir App Service uygulamasını bir git veya Mercurial deposu ile ilişkilendirir. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek App Service CLI betik örnekleri, [Azure App Service belgelerinde](../samples-cli.md) bulunabilir.
