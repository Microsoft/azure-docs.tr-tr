---
title: Uygulamalar için trafiği yönlendirme-Azure PowerShell-Traffic Manager
description: Azure PowerShell betik örneği-uygulamalar için yüksek kullanılabilirlik trafiği yönlendirme
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumudD
editor: ''
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: duau
ms.openlocfilehash: 114041a6ad0b311a1e01a692b9e6c36b0a8fb900
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185385"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-powershell"></a>Azure PowerShell kullanarak yüksek uygulamaların kullanılabilirliği için trafiği yönlendirme

Bu betik bir kaynak grubu, iki App Service planı, iki Web uygulaması, bir Traffic Manager profili ve iki Traffic Manager uç noktası oluşturur. Traffic Manager, trafiği birincil bölge olarak bir bölgedeki uygulamaya ve birincil bölgedeki uygulama kullanılamadığında ikincil bölgeye yönlendirir. Betiği yürütmeden önce, MyWebApp, MyWebAppL1 ve MyWebAppL2 değerlerini Azure 'daki benzersiz değerlerle değiştirmeniz gerekir. Betiği çalıştırdıktan sonra, birincil bölgedeki uygulamaya mywebapp.trafficmanager.net URL 'SI ile erişebilirsiniz.

Gerekirse, [Azure PowerShell kılavuzunda](/powershell/azure)bulunan yönergeyi kullanarak Azure PowerShell yükleyip `Connect-AzAccount` Azure ile bağlantı oluşturmak için öğesini çalıştırın.

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

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/).

Ek ağ PowerShell betiği örnekleri, [Azure Ağına Genel Bakış belgeleri](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) içinde bulunabilir.