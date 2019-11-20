---
title: Azure CLı betik örneği-uygulamalar için yüksek kullanılabilirlik trafiği yönlendirme | Microsoft Docs
description: Azure CLı betik örneği-yüksek uygulamaların kullanılabilirliği için trafiği yönlendirme
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 06/26/2018
ms.author: allensu
ms.openlocfilehash: 2a083520a0da324d7033d4597a6b734b43c025b7
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049267"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-cli"></a>Yüksek uygulamaların kullanılabilirliği için trafiği yönlendirme-Azure CLı

Bu betik bir kaynak grubu, iki App Service planı, iki Web uygulaması, bir Traffic Manager profili ve iki Traffic Manager uç noktası oluşturur. Traffic Manager, trafiği birincil bölge olarak bir bölgedeki uygulamaya ve birincil bölgedeki uygulama kullanılamadığında ikincil bölgeye yönlendirir. Betiği yürütmeden önce, MyWebApp, MyWebAppL1 ve MyWebAppL2 değerlerini Azure 'daki benzersiz değerlerle değiştirmeniz gerekir. Betiği çalıştırdıktan sonra, birincil bölgedeki uygulamaya mywebapp.trafficmanager.net URL 'SI ile erişebilirsiniz.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Dağıtımı temizleme 

Betik örneği çalıştırıldıktan sonra, kaynak grubunu, App Service uygulamayı ve tüm ilgili kaynakları kaldırmak için izle komutu kullanılabilir.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik bir kaynak grubu, web uygulaması, traffic manager profili ve tüm ilgili kaynakları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | App Service planı oluşturur. Bu, Azure Web uygulamanız için bir sunucu grubu gibidir. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | App Service planı içinde bir Azure Web uygulaması oluşturur. |
| [az Network Traffic-Manager profili oluşturma](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Bir Azure Traffic Manager profili oluşturur. |
| [az Network Traffic-Manager uç noktası oluştur](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Azure Traffic Manager profiline bir uç nokta ekler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure).

Ek App Service CLı betiği örnekleri, [Azure ağ belgelerinde](../cli-samples.md)bulunabilir.
