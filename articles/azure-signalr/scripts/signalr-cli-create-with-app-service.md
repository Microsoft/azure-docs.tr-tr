---
title: Azure CLı kullanarak App Service ile SignalR hizmeti oluşturma
description: App Service ile SignalR hizmeti oluşturmak için Azure CLı 'yi kullanın. Azure SignalR hizmeti için tüm CLı komutlarını öğrenin.
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e6e2484dc7fffdcecb64ef3b3afa3a7a4343d29c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787314"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>App Service ile SignalR Hizmeti Oluşturma

Bu örnek betik, istemcilere gerçek zamanlı içerik güncelleştirmeleri göndermek için kullanılan yeni bir Azure SignalR Hizmeti kaynağı oluşturur. Bu betik, SignalR Hizmetini kullanan ASP.NET Core Web Uygulamanızı barındırmak için yeni bir Web Uygulaması ve Uygulama Hizmeti planı da ekler. Web uygulaması, yeni SignalR hizmet kaynağına bağlanmak için *AzureSignalRConnectionString* adlı bir Uygulama Ayarı ile yapılandırılır.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Bu öğretici, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="sample-script"></a>Örnek betik

Bu betik, Azure CLI için *signalr* uzantısını kullanır. Bu örnek betiği kullanmadan önce, aşağıdaki komutu yürüterek Azure CLI için *signalr* uzantısını yükleyin:

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate unique names for the SignalR service, resource group, 
# app service, and app service plan
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"
myWebAppName=SignalRTestWebApp$randomNum
myAppSvcPlanName=$myAppSvcName"Plan"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Create an App Service plan.
az appservice plan create --name $myAppSvcPlanName --resource-group $myResourceGroupName --sku FREE

# Create the Web App
az webapp create --name $myWebAppName --resource-group $myResourceGroupName --plan $myAppSvcPlanName  

# Get the SignalR primary connection string
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "AzureSignalRConnectionString=$primaryConnectionString"
```

Yeni kaynak grubu için oluşturulan gerçek adı not edin. Çıktıda gösterilecektir. Tüm grup kaynaklarını silmek istediğinizde bu kaynak grubu adını kullanacaksınız.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Tablodaki her komut, komuta özgü belgelere yönlendirir. Bu betik şu komutları kullanır:

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az signalr create](/cli/azure/signalr#az_signalr_create) | Bir Azure SignalR Hizmeti kaynağı oluşturur. |
| [az signalr key list](/cli/azure/signalr/key#az_signalr_key_list) | SignalR ile gerçek zamanlı içerik güncelleştirmeleri gönderilirken uygulamanız tarafından kullanılacak anahtarları listeler. |
| [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) | Web uygulamalarını barındırmak için bir Azure App Service Planı oluşturur. |
| [az webapp create](/cli/azure/webapp#az_webapp_create) | App Service barındırma planını kullanarak bir Azure Web uygulaması oluşturur. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Web uygulaması için yeni bir uygulama ayarı ekler. Bu uygulama ayarı, SignalR bağlantı dizesini depolamak için kullanılır. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek Azure SignalR Hizmeti CLI betik örnekleri, [Azure SignalR Hizmeti belgelerinde](../signalr-reference-cli.md) bulunabilir.
