---
title: Azure 'da Windows sanal makinelerini yeniden dağıtma | Microsoft Docs
description: Azure 'da Windows sanal makinelerini, RDP bağlantısı sorunlarını azaltmak için yeniden dağıtma.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 36af0eeb43fb209ed65f950576f2dc9e97ec3633
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058635"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Windows sanal makinesini yeni Azure düğümüne yeniden dağıtın
Windows tabanlı Azure sanal makinesine (VM) yönelik uzak masaüstü (RDP) bağlantısı veya uygulama erişimi sorunlarını gidermeye yönelik zorluklarla karşılaşıyorsanız, VM 'nin yeniden dağıtılması yardımcı olabilir. Bir VM 'yi yeniden dağıttığınızda Azure, VM 'yi kapatır, VM 'yi Azure altyapısı içindeki yeni bir düğüme taşır ve ardından yeniden, tüm yapılandırma seçeneklerinizi ve ilişkili kaynaklarınızı korur. Bu makalede, Azure PowerShell veya Azure portal kullanarak bir VM 'yi yeniden dağıtma gösterilmektedir.

> [!NOTE]
> Bir VM 'yi yeniden dağıttığdıktan sonra, geçici disk kaybedilir ve sanal ağ arabirimiyle ilişkili dinamik IP adresleri güncellenir. 


## <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma
Makinenizde en son Azure PowerShell 1. x ' in yüklü olduğundan emin olun. Daha fazla bilgi için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/overview).

Aşağıdaki örnek, adlı kaynak `myVM` `myResourceGroup`grubunda adlı VM 'yi dağıtır:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Sonraki adımlar
Sanal makinenize bağlanmayla ilgili sorun yaşıyorsanız, [RDP bağlantılarında sorun gidermeye](troubleshoot-rdp-connection.md) veya [ayrıntılı RDP sorun giderme adımlarına](detailed-troubleshoot-rdp.md)yönelik belirli yardım bulabilirsiniz. VM 'niz üzerinde çalışan bir uygulamaya erişemiyorsanız, [uygulama sorunlarını giderme sorunlarını](../windows/troubleshoot-app-connection.md)da okuyabilirsiniz.

