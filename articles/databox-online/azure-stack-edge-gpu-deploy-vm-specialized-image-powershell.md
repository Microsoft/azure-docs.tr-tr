---
title: Azure Stack Edge Pro GPU cihazınız için Windows VHD 'nin özelleşmiş görüntüsünden VM görüntüleri oluşturma
description: Bir Windows VHD veya VHDX 'ten başlayarak özelleştirilmiş görüntülerden VM görüntülerinin nasıl oluşturulacağını açıklar. Azure Stack Edge Pro GPU cihazınızda dağıtılan VM 'lerle kullanılacak VM görüntülerini oluşturmak için bu özel görüntüyü kullanın.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: 6bfa42e99f295b429eba40a27eb59becb8aa80a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575958"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>Azure PowerShell aracılığıyla Azure Stack Edge Pro GPU cihazınızdan özelleştirilmiş bir görüntüden VM dağıtma 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede, bir sanal makineyi (VM) Azure Stack Edge Pro GPU cihazınızda özelleştirilmiş bir görüntüden dağıtmak için gereken adımlar açıklanmaktadır. 

Azure Stack Edge Pro GPU 'da VM dağıtmak için genelleştirilmiş bir görüntü hazırlamak üzere bkz. [WINDOWS VHD 'den Genelleştirilmiş görüntü hazırlama](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) veya [bir ISO 'Dan Genelleştirilmiş görüntü hazırlama](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md).

## <a name="about-vm-images"></a>VM görüntüleri hakkında

Bir Windows VHD veya VHDX, *özel* bir görüntü veya *Genelleştirilmiş* görüntü oluşturmak için kullanılabilir. Aşağıdaki tabloda, *özelleştirilmiş* ve *Genelleştirilmiş* görüntüler arasındaki temel farklılıklar özetlenmektedir.

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>İş akışı

Özel görüntüden bir VM dağıtmak için üst düzey iş akışı:

1. VHD 'yi Azure Stack Edge Pro GPU cihazındaki yerel bir depolama hesabına kopyalayın.
1. VHD 'den yeni bir yönetilen disk oluşturun.
1. Yönetilen diskten yeni bir sanal makine oluşturun ve yönetilen diski bağlayın.

## <a name="prerequisites"></a>Önkoşullar

Bir VM 'yi PowerShell aracılığıyla cihazınıza dağıtabilmeniz için önce aşağıdakileri yaptığınızdan emin olun:

- Cihazınıza bağlanmak için kullanacağınız bir istemciye erişiminiz var.
    - İstemciniz [desteklenen bir işletim sistemi](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)çalıştırıyor.
    - İstemciniz, [cihazınız için Azure Resource Manager bağlanma](azure-stack-edge-gpu-connect-resource-manager.md)bölümündeki yönergelere göre cihazınızın yerel Azure Resource Manager bağlanacak şekilde yapılandırılmıştır.

## <a name="verify-the-local-azure-resource-manager-connection"></a>Yerel Azure Resource Manager bağlantısını doğrulama

İstemcinizin yerel Azure Resource Manager bağlanabildiğini doğrulayın. 

1. Kimlik doğrulamak için yerel cihaz API 'Lerini çağırın:

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. `EdgeArmUser`Azure Resource Manager üzerinden bağlanmak için Kullanıcı adını ve parolayı belirtin. Parolayı geri çağırmıyorsanız, [Azure Resource Manager parolasını sıfırlayın](azure-stack-edge-gpu-set-azure-resource-manager-password.md) ve bu parolayı kullanarak oturum açın.

## <a name="deploy-vm-from-specialized-image"></a>Özelleştirilmiş görüntüden VM dağıtma

Aşağıdaki bölümler, özelleştirilmiş bir görüntüden VM dağıtmak için adım adım yönergeleri içerir.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>VHD 'yi cihazdaki yerel depolama hesabına Kopyala

VHD 'yi yerel depolama hesabına kopyalamak için aşağıdaki adımları izleyin:

1. Kaynak VHD 'yi Azure Stack Kenarunuzdaki yerel bir BLOB depolama hesabına kopyalayın.

1. Elde edilen URI 'yi bir yere göz atın. Bu URI 'yi sonraki bir adımda kullanacaksınız.

    Yerel bir depolama hesabı oluşturmak ve erişmek için, makaleye bir [VHD yükle](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) aracılığıyla bir [depolama hesabı oluşturma](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) başlıklı bölümlere bakın. [Azure Stack Edge cihazınızda VM 'leri Azure PowerShell aracılığıyla dağıtın](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). 

## <a name="create-a-managed-disk-from-vhd"></a>VHD 'den yönetilen disk oluşturma

Daha önce depolama hesabına yüklediğiniz bir VHD 'den yönetilen disk oluşturmak için aşağıdaki adımları izleyin:

1. Bazı parametreleri ayarlayın.

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    Örnek bir çıktı aşağıda verilmiştir.

    ```powershell
    PS C:\WINDOWS\system32> $VHDURI = "https://myasevmsa.blob.myasegpudev.wdshcsso.com/vhds/WindowsServer2016Datacenter.vhd"
    PS C:\WINDOWS\system32> $DiskRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $DiskName = "myasemd1"
    ```
1. Yeni bir yönetilen disk oluşturun.

    ```powershell
    $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VhdURI
    $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    ```

    Örnek bir çıktı aşağıda verilmiştir. Buradaki konum yerel depolama hesabının konumuna ayarlanır ve `DBELocal` Azure Stack Edge Pro GPU cihazındaki tüm yerel depolama hesapları için her zaman kullanılır. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Yönetilen diskten VM oluşturma

Yönetilen bir diskten VM oluşturmak için aşağıdaki adımları izleyin:

1. Bazı parametreleri ayarlayın.

    ```powershell
    $NicRG = <NIC resource group>
    $NicName = <NIC name>
    $IPConfigName = <IP config name>
    $PrivateIP = <IP address> #Optional
    
    $VMRG = <VM resource group>
    $VMName = <VM name>
    $VMSize = <VM size> 
    ```

    >[!NOTE]
    > `PrivateIP`Parametresi isteğe bağlıdır. Statik IP atamak için bu parametreyi kullanın diğer varsayılan değer DHCP kullanan dinamik bir IP 'dir.

    Örnek bir çıktı aşağıda verilmiştir. Bu örnekte, gerekirse kaynaklar için ayrı kaynak grupları oluşturup belirtseniz de tüm VM kaynakları için aynı kaynak grubu belirtilir.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. Sanal ağ bilgilerini alın ve yeni bir ağ arabirimi oluşturun.

    Bu örnek, varsayılan kaynak grubuyla ilişkili varsayılan sanal ağda tek bir ağ arabirimi oluşturduğunuzu varsayar `ASEVNET` `ASERG` . Gerekirse, alternatif bir sanal ağ belirtebilir veya birden çok ağ arabirimi oluşturabilirsiniz. Daha fazla bilgi için bkz. [Azure Portal aracılığıyla BIR VM 'ye ağ arabirimi ekleme](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md).

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    Örnek bir çıktı aşağıda verilmiştir.

    ```powershell
    PS C:\WINDOWS\system32> $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    PS C:\WINDOWS\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    WARNING: The output object type of this cmdlet will be modified in a future release.
    PS C:\WINDOWS\system32>    
    ```
1. Yeni bir VM yapılandırma nesnesi oluşturun.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. Ağ arabirimini sanal makineye ekleyin.

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. VM üzerinde işletim sistemi diski özelliklerini ayarlayın.

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    Bu komutta son bayrak, `-Windows` `-Linux` VM 'niz için kullandığınız işletim sistemine bağlı olarak ya da olur.

1. VM 'yi oluşturun.

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    Örnek bir çıktı aşağıda verilmiştir. 

    ```powershell
    PS C:\WINDOWS\system32> $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    PS C:\WINDOWS\system32> $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    PS C:\WINDOWS\system32> $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach -Windows
    PS C:\WINDOWS\system32> New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasevmsa, is used for
    boot diagnostics.    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK        
    PS C:\WINDOWS\system32>
    ```

## <a name="delete-vm-and-resources"></a>VM ve kaynakları silme

Bu makale, tüm VM kaynağını oluşturmak için yalnızca bir kaynak grubu kullandı. Kaynak grubunun silinmesi, VM 'yi ve tüm ilişkili kaynakları silecektir. 

1. İlk olarak kaynak grubu altında oluşturulan tüm kaynakları görüntüleyin.

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    Örnek bir çıktı aşağıda verilmiştir.
    
    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResource -ResourceGroupName myasevm1rg
    
    
    Name              : myasemd1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/disks
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/disk
                        s/myasemd1
    
    Name              : myasetestvm1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/virt
                        ualMachines/myasetestvm1
    
    Name              : myasevmnic1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Network/networkInterfaces
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Network/netw
                        orkInterfaces/myasevmnic1
    
    Name              : myasevmsa
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Storage/stor
                        ageaccounts/myasevmsa
    
    PS C:\WINDOWS\system32>
    ```

1. Kaynak grubunu ve tüm ilişkili kaynakları silin.

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    Örnek bir çıktı aşağıda verilmiştir.
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. Kaynak grubunun silindiğini doğrulayın. Cihazda mevcut olan tüm kaynak gruplarını alın. 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    Örnek bir çıktı aşağıda verilmiştir.

    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResourceGroup

    ResourceGroupName : ase-image-resourcegroup
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ase-image-resourcegroup
    
    ResourceGroupName : ASERG
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ASERG
    
    ResourceGroupName : myaserg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg
        
    PS C:\WINDOWS\system32>
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro GPU üzerinde VM dağıtmak için Windows VHD 'den genelleştirilmiş bir görüntü hazırlama](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [Azure Stack Edge Pro GPU d üzerinde VM dağıtmak için BIR ISO 'dan Genelleştirilmiş görüntü hazırlama](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)