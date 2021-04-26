---
title: 'CLı: Web sunucusu günlükleri ile bir uygulamayı Izleme'
description: Azure CLı kullanarak App Service uygulamanızın dağıtımını ve yönetimini otomatik hale getirmeyi öğrenin. Bu örnek, Web sunucusu günlükleri ile bir uygulamanın nasıl izleneceğini gösterir.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0887656f-611c-4627-8247-b5cded7cef60
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 3d8442179ecec72d47e770d823bbfd5795f5c4dc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787710"
---
# <a name="monitor-an-app-service-app-with-web-server-logs-using-azure-cli"></a>Azure CLı kullanarak Web sunucusu günlükleri ile bir App Service uygulamasını izleme

Bu örnek betik bir kaynak grubu, App Service planı ve uygulama oluşturur ve uygulamayı Web sunucusu günlüklerini etkinleştirecek şekilde yapılandırır. Ardından, gözden geçirmek üzere günlük dosyalarını indirir.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Bu öğretici, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/monitor-with-logs/monitor-with-logs.sh "Monitor Logs")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik bir kaynak grubu, App Service uygulaması ve tüm ilgili kaynakları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | App Service planı oluşturur. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | App Service uygulaması oluşturur. |
| [`az webapp log config`](/cli/azure/webapp/log#az_webapp_log_config) | App Service bir uygulamanın hangi günlük olarak devam etmediğini yapılandırır. |
| [`az webapp log download`](/cli/azure/webapp/log#az_webapp_log_download) | App Service uygulamasının günlüklerini yerel makinenize indirir. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek App Service CLI betik örnekleri, [Azure App Service belgelerinde](../samples-cli.md) bulunabilir.
