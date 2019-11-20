---
title: Uygulamalar için trafiği yönlendirme-Azure PowerShell-Traffic Manager
description: Azure PowerShell betik örneği-uygulamalar için yüksek kullanılabilirlik trafiği yönlendirme
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 05/16/2017
ms.author: allensu
ms.openlocfilehash: 183599fccfad1806faae3cb90de225d388b77da8
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049245"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-powershell"></a>Uygulamaların yüksek kullanılabilirliği için trafiği yönlendirme-Azure PowerShell

Bu betik bir kaynak grubu, iki App Service planı, iki Web uygulaması, bir Traffic Manager profili ve iki Traffic Manager uç noktası oluşturur. Traffic Manager, trafiği birincil bölge olarak bir bölgedeki uygulamaya ve birincil bölgedeki uygulama kullanılamadığında ikincil bölgeye yönlendirir. Betiği yürütmeden önce, MyWebApp, MyWebAppL1 ve MyWebAppL2 değerlerini Azure 'daki benzersiz değerlerle değiştirmeniz gerekir. Betiği çalıştırdıktan sonra, birincil bölgedeki uygulamaya mywebapp.trafficmanager.net URL 'SI ile erişebilirsiniz.

Gerekirse, [Azure PowerShell kılavuzunda](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) bulunan yönergeleri kullanarak Azure PowerShell’i yükleyin ve ardından Azure ile bağlantı oluşturmak için `Connect-AzAccount` komutunu çalıştırın.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Kaynak grubunu, VM’yi ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```powershell
Remove-AzResourceGroup -Name myResourceGroup1
Remove-AzResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Betik açıklaması

Bu betik bir kaynak grubu, web uygulaması, traffic manager profili ve tüm ilgili kaynakları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | App Service planı oluşturur. Bu, Azure Web uygulamanız için bir sunucu grubu gibidir. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | App Service planı içinde bir Azure Web uygulaması oluşturur. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | App Service planı içinde bir Azure Web uygulaması oluşturur. |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Bir Azure Traffic Manager profili oluşturur. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Azure Traffic Manager profiline bir uç nokta ekler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](https://docs.microsoft.com/powershell/azure/overview).

Ek ağ PowerShell betiği örnekleri, [Azure Ağına Genel Bakış belgeleri](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) içinde bulunabilir.
