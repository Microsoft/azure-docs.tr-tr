---
title: Azure Windows VM için ağ arabirimini sıfırlama | Microsoft Docs
description: Azure Windows VM için ağ arabiriminin nasıl sıfırlandığını gösterir
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 1c49c6221e9b310a1b14a4e06a296befc7f6da4d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111730"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Azure Windows VM için ağ arabirimini sıfırlama 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Bu makalede, sonrasında Microsoft Azure Windows sanal makinesine (VM) bağlanamadığınızda sorunları çözümlemek için Azure Windows VM 'nin ağ arabiriminin nasıl sıfırlanacağı gösterilmektedir:

* Varsayılan ağ arabirimini (NIC) devre dışı bırakabilirsiniz. 
* NIC için el ile statik IP ayarlarsınız. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Ağ arabirimini sıfırlayın

### <a name="for-vms-deployed-in-resource-group-model"></a>Kaynak grubu modelinde dağıtılan VM 'Ler için

1.  [Azure Portal](https://ms.portal.azure.com) gidin.
2.  Etkilenen sanal makineyi seçin.
3.  **Ağ** ' ı seçin ve ardından VM 'Nin ağ arabirimini seçin.

    ![Ağ arabirimi konumu](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  **IP yapılandırması**' nı seçin.
5.  IP 'yi seçin. 
6.  **Özel IP ataması** **statik**değilse, **statik**olarak değiştirin.
7.  **IP adresini** alt ağda kullanılabilir olan başka bir IP adresiyle değiştirin.
8. Sanal makine, yeni NIC 'yi sisteme başlatacak şekilde yeniden başlatılacak.
9.  Makinenize RDP 'yi deneyin. Başarılı olursa, isterseniz özel IP adresini özgün olacak şekilde değiştirebilirsiniz. Aksi takdirde, bunu koruyabilirsiniz. 

#### <a name="use-azure-powershell"></a>Azure PowerShell’i kullanma

1. [En son Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) yüklü olduğundan emin olun
2. Yükseltilmiş bir Azure PowerShell oturumu açın (yönetici olarak çalıştır). Aşağıdaki komutları çalıştırın:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ResourceGroupName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Makinenize RDP 'yi deneyin.  Başarılı olursa, isterseniz özel IP adresini özgün olacak şekilde değiştirebilirsiniz. Aksi takdirde, bunu koruyabilirsiniz.

### <a name="for-classic-vms"></a>Klasik VM 'Ler için

Ağ arabirimini sıfırlamak için şu adımları izleyin:

#### <a name="use-azure-portal"></a>Azure portalı kullanma

1.  [Azure Portal]( https://ms.portal.azure.com) gidin.
2.  **Sanal makineler (klasik)** öğesini seçin.
3.  Etkilenen sanal makineyi seçin.
4.  **IP adreslerini**seçin.
5.  **Özel IP ataması** **statik**değilse, **statik**olarak değiştirin.
6.  **IP adresini** alt ağda kullanılabilir olan başka bir IP adresiyle değiştirin.
7.  **Kaydet**’i seçin.
8.  Sanal makine, yeni NIC 'yi sisteme başlatacak şekilde yeniden başlatılacak.
9.  Makinenize RDP 'yi deneyin. Başarılı olursa, özel IP adresini özgün durumuna geri döndürmeyi seçebilirsiniz.  

#### <a name="use-azure-powershell"></a>Azure PowerShell’i kullanma

1. [En son Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) yüklü olduğundan emin olun.
2. Yükseltilmiş bir Azure PowerShell oturumu açın (yönetici olarak çalıştır). Aşağıdaki komutları çalıştırın:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ResourceGroupName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Makinenize RDP 'yi deneyin. Başarılı olursa, isterseniz özel IP adresini özgün olacak şekilde değiştirebilirsiniz. Aksi takdirde, bunu koruyabilirsiniz. 

## <a name="delete-the-unavailable-nics"></a>Kullanılamayan NIC 'Leri silme
Makineye Uzak Masaüstü 'nü etkinleştirdikten sonra olası sorundan kaçınmak için eski NIC 'Leri silmeniz gerekir:

1.  Aygıt Yöneticisi açın.
2.  **Gizli aygıtları göstermek** > **görüntüle** ' yi seçin.
3.  **Ağ bağdaştırıcılarını**seçin. 
4.  "Microsoft Hyper-V ağ bağdaştırıcısı" olarak adlandırılan bağdaştırıcıları denetleyin.
5.  Gri kullanılamayan bir bağdaştırıcı görebilirsiniz. Bağdaştırıcıyı sağ tıklatın ve ardından Kaldır ' ı seçin.

    ![NIC görüntüsü](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Yalnızca "Microsoft Hyper-V ağ bağdaştırıcısı" adlı kullanılamayan bağdaştırıcıları kaldırın. Diğer gizli bağdaştırıcıların birini kaldırırsanız ek sorunlara neden olabilir.
    >
    >

6.  Artık kullanılamayan tüm bağdaştırıcıların sisteminizde temizlenmesi gerekir.
