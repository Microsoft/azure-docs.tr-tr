---
title: VM (klasik) veya Cloud Services rol örneğini farklı bir alt ağa taşıma-Azure PowerShell | Microsoft Docs
description: VM 'Leri (klasik) ve Cloud Services rol örneklerini PowerShell kullanarak farklı bir alt ağa taşımayı öğrenin.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 275d59a7bddd8b2b609169218afcd15e9a0ce913
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058386"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>PowerShell kullanarak VM (klasik) veya Cloud Services rol örneğini farklı bir alt ağa taşıma
VM 'lerinizi (klasik) bir alt ağdan aynı sanal ağ (VNet) içinde diğerine taşımak için PowerShell 'i kullanabilirsiniz. Rol örnekleri, PowerShell kullanmak yerine CSCFG dosyası düzenlenerek taşınabilir.

> [!NOTE]
> Bu makalede yalnızca klasik dağıtım modeli aracılığıyla dağıtılan VM 'Lerin nasıl taşınacağı açıklanır.
> 
> 

VM 'Lerin başka bir alt ağa neden taşınsın? Alt ağ geçişi, eski alt ağ çok küçük olduğunda ve bu alt ağdaki var olan çalışan VM 'Ler nedeniyle genişletilemeyecek yararlıdır. Bu durumda, yeni, daha büyük bir alt ağ oluşturabilir ve VM 'Leri yeni alt ağa geçirebilir, geçiş tamamlandıktan sonra eski boş alt ağı silebilirsiniz.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Bir VM 'yi başka bir alt ağa taşıma
Bir VM 'yi taşımak için aşağıdaki örneği bir şablon olarak kullanarak set-Azuyeniden gönderme net PowerShell cmdlet 'ini çalıştırın. Aşağıdaki örnekte, TestVM 'yi mevcut alt ağından alt ağ 2 ' ye taşıdık. Örneği, ortamınızı yansıtacak şekilde düzenlemediğinizden emin olun. Update-AzureVM cmdlet 'ini bir yordamın parçası olarak çalıştırdığınızda, sanal makineyi güncelleştirme sürecinin bir parçası olarak yeniden başlatacak şekilde aklınızda olursunuz.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

VM 'niz için statik bir iç özel IP belirttiyseniz, VM 'yi yeni bir alt ağa taşıyabilmeniz için önce bu ayarı temizlemeniz gerekir. Bu durumda, aşağıdakileri kullanın:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Rol örneğini başka bir alt ağa taşımak için
Rol örneğini taşımak için CSCFG dosyasını düzenleyin. Aşağıdaki örnekte, sanal ağ *Vada* "Role0" değerini, mevcut alt ağından *alt ağ 2*' ye taşıdık. Rol örneği zaten dağıtıldığı için, yalnızca alt ağ adı = alt ağ-2 ' yi değiştirirsiniz. Örneği, ortamınızı yansıtacak şekilde düzenlemediğinizden emin olun.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
