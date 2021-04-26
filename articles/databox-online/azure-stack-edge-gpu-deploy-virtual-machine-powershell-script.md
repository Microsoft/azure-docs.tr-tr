---
title: Azure PowerShell betiği aracılığıyla Azure Stack Edge Pro GPU cihazınızda VM 'Leri dağıtma
description: Bir Azure PowerShell betiği kullanarak bir Azure Stack Edge Pro cihazında sanal makinelerin (VM 'Ler) nasıl oluşturulacağını ve yönetileceğini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 36c7078a79cf8b0b7414c5031acb79b9a2c2453c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630495"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell-script"></a>Azure PowerShell betiği aracılığıyla Azure Stack Edge Pro GPU cihazınızda VM 'Leri dağıtma

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu öğretici, bir Azure PowerShell betiği kullanarak Azure Stack Edge Pro cihazınızda bir sanal makinenin nasıl oluşturulduğunu ve yönetileceğini açıklar.

## <a name="prerequisites"></a>Önkoşullar

Bu betiği kullanarak Azure Stack Edge Pro cihazınızda bir VM oluşturmaya ve yönetmeye başlamadan önce, aşağıdaki adımlarda listelenen önkoşulları tamamladığınızdan emin olmanız gerekir:

### <a name="for-azure-stack-edge-pro-device-via-the-local-web-ui"></a>Yerel Web Kullanıcı arabirimi aracılığıyla Azure Stack Edge Pro cihazı için

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

### <a name="for-your-windows-client"></a>Windows istemciniz için

1. Değişiklik yaptığınızdan emin olun: 

    - İstemci üzerindeki konak dosyası veya,
    - DNS sunucusu yapılandırması
    
    > [!IMPORTANT]
    > Uç nokta adı çözümlemesi için DNS sunucusu yapılandırmasını değiştirmenizi öneririz.

    1. **Not defteri 'ni** yönetici olarak başlatın (dosyayı kaydetmek için yönetici ayrıcalıkları gereklidir) ve ardından konumunda bulunan **Hosts** dosyasını açın `C:\Windows\System32\Drivers\etc` .
    
        ![Windows Gezgini ana bilgisayarları dosyası](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)
    
    2. Aşağıdaki girdileri, cihazınız için uygun değerlerle değiştirerek **ana bilgisayar** dosyanıza ekleyin:
    
        ```
        <device IP> login.<appliance name>.<DNS domain>
        <device IP> management.<appliance name>.<DNS domain>
        <device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
        Depolama hesabı için, yeni bir depolama hesabı oluşturmak üzere betiğin daha sonra kullanmasını istediğiniz bir ad sağlayabilirsiniz. Komut dosyası, depolama hesabının mevcut olup olmadığını denetlemez.

    3. Başvuru için aşağıdaki görüntüyü kullanın. **Hosts** dosyasını kaydedin.

        ![Not defteri 'nde Hosts dosyası](media/azure-stack-edge-gpu-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

2. Bu yordamda kullanılan [PowerShell betiğini indirin](https://aka.ms/ase-vm-powershell) .

3. Windows istemcinizin PowerShell 5,0 veya sonraki bir sürümü çalıştırdığından emin olun.

4. `Azure.Storage Module version 4.5.0`' Nin sisteminizde yüklü olduğundan emin olun. Bu modülü [PowerShell Galerisi](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0)edinebilirsiniz. Bu modülü yüklemek için şunu yazın:

    `Install-Module -Name Azure.Storage -RequiredVersion 4.5.0`

    Yüklü modülün sürümünü doğrulamak için şunu yazın:

    `Get-InstalledModule -name Azure.Storage`

    Diğer sürüm modüllerini kaldırmak için şunu yazın:

    `Uninstall-Module -Name Azure.Storage`

5. [AzCopy 10](../storage/common/storage-use-azcopy-v10.md#download-azcopy) ' yı Windows istemcisine indirin. Betiği çalıştırırken bir parametre olarak geçitirsiniz, bu konumu unutmayın.

6. Windows istemcinizin TLS 1,2 veya sonraki bir sürümü çalıştırdığından emin olun.


## <a name="create-a-vm"></a>VM oluşturma

1. PowerShell'i yönetici olarak çalıştırın.
1. İstemcinizi betiği indirdiğiniz klasöre gidin.
1. Betiği çalıştırmadan önce, hala cihazın yerel Azure Resource Manager bağlı olduğunuzdan ve bağlantının süresi dolmadığından emin olun.

    ```powershell
    PS C:\windows\system32> login-AzureRMAccount -EnvironmentName aztest1 -TenantId c0257de7-538f-415c-993a-1b87a031879d

    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest1

    PS C:\windows\system32> cd C:\Users\v2
    PS C:\Users\v2>
    ```  
1. Betiği çalıştırmak için aşağıdaki komutu kullanın:
 
    `.\ArmPowershellClient.ps1 -NicPrivateIp <Private IP> -VHDPath <Path> -VHDFile <VHD File, with extension> -StorageAccountName <Name> -OS <Windows/Linux> -VMSize <Supported VM Size> -VMUserName <Username to be used to sign in to VM> -VMPassword <Password for the VM> --AzCopy10Path <Absolute Path>`

    IP 'nin VM 'ye dinamik olarak ayrılmasını istiyorsanız, `-NicPrivateIp` parametresini atlayın.

    Bir Windows VM ve bir Linux VM oluşturmak için betiğin çalıştırıldığı örnekler aşağıda verilmiştir.

    **Bir Windows sanal makinesi için:**

    Oluşturulan bir Windows sanal makinesi için örnek bir çıktı aşağıda verilmiştir.

    ```powershell
    PS C:\Users\v2> .\ArmPowershellClient.ps1 -VHDPath \\asefs\Logs\vmvhd -VHDFile WindowsServer2016Datacenter.vhd -StorageAccountName myasesatest -OS Windows -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\Users\AzCopy10\AzCopy.exe
    New-AzureRmResourceGroup -Name rg201221071831 -Location DBELocal -Force
    Successfully created Resource Group:rg201221071831
    Successfully created Resource Group:StorAccRG
    Get-AzureRmStorageAccount -Name myasesatest -ResourceGroupName StorAccRG -ErrorAction SilentlyContinue
    New-AzureRmStorageAccount -Name myasesatest  -ResourceGroupName StorAccRG -SkuName Standard_LRS -Location DBELocal
    
    Created New Storage Account
    Get-AzureRmStorageAccount -name myasesatest -resourcegroupname
    StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
    ------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
    myasesatest        StorAccRG         DBELocal StandardLRS Storage            12/22/2020 3:18:38 AM Succeeded         False
    myasesatest        StorAccRG         DBELocal StandardLRS Storage            12/22/2020 3:18:38 AM Succeeded         False
    
     Uploading Vhd to Storage Account
    
    New-AzureStorageContext -StorageAccountName myasesatest -StorageAccountKey hyibjhbVlOROgTlU1nQJIlxrg94eGDhF+RIQ71Z7UVZIxoOPMlHP274NUhZtA1hMxGBcpk2BVApiFasFPEhY/A== -Endpoint https://myasesatest.blob.myasegpuvm.wdshcsso.com/
    
    New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission
    
    SAS Token : ?sv=2017-07-29&sig=TXaGbjum9tFFaJnu3SFmDuslJuqNiNQwvuHfpPJMYN0%3D&spr=https&se=2020-12-22T04%3A18%3A43Z&srt=sco&ss=bfqt&sp=racwdl
    
     C:\Users\AzCopy10\AzCopy.exe make https://myasesatest.blob.myasegpuvm.wdshcsso.com/vmimages?sv=2017-07-29&sig=TXaGbjum9tFFaJnu3SFmDuslJuqNiNQwvuHfpPJMYN0%3D&spr=https&se=2020-12-22T04%3A18%3A43Z&srt=sco&ss=bfqt&sp=racwdl
    
    Successfully created the resource.
    
     AzCopy cp \\asefs\Logs\vmvhd\WindowsServer2016Datacenter.vhd https://myasesatest.blob.myasegpuvm.wdshcsso.com/vmimages?sv=2017-07-29&sig=TXaGbjum9tFFaJnu3SFmDuslJuqNiNQwvuHfpPJMYN0%3D&spr=https&se=2020-12-22T04%3A18%3A43Z&srt=sco&ss=bfqt&sp=racwdl
    
    INFO: Scanning...
    
    Job b6f54665-93c4-2f47-4770-5f3b7b0de2dc has started
    Log file is located at: C:\Users\Administrator\.azcopy\b6f54665-93c4-2f47-4770-5f3b7b0de2dc.log
    
    INFO: AzCopy.exe: A newer version 10.8.0 is available to download
    
    99.9 %, 0 Done, 0 Failed, 1 Pending, 0 Skipped, 1 Total,  (Disk may be limiting speed)
    
    Job b6f54665-93c4-2f47-4770-5f3b7b0de2dc summary
    Elapsed Time (Minutes): 12.7717
    Total Number Of Transfers: 1
    Number of Transfers Completed: 1
    Number of Transfers Failed: 0
    Number of Transfers Skipped: 0
    TotalBytesTransferred: 13958644224
    Final Job Status: Completed
    
    VHD Upload Done
    
     Creating a new managed disk
    
      = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri
    
     Microsoft.Azure.Commands.Compute.Automation.Models.PSDisk
    
     New-AzureRmDisk -ResourceGroupName rg201221071831 -DiskName ld201221071831 -Disk
    
    ResourceGroupName  : rg201221071831
    ManagedBy          :
    Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
    Zones              :
    TimeCreated        : 12/21/2020 7:31:35 PM
    OsType             :
    CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
    DiskSizeGB         : 13
    EncryptionSettings :
    ProvisioningState  : Succeeded
    Id                 : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Compute/disks/ld201221071831
    Name               : ld201221071831
    Type               : Microsoft.Compute/disks
    Location           : DBELocal
    Tags               : {}
    
     Created a new managed disk
    
     Creating a new Image out of managed disk
    
    ResourceGroupName    :
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    :
    Id                   :
    Name                 :
    Type                 :
    Location             : DBELocal
    Tags                 :
    
     New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig201221071831  -ResourceGroupName rg201221071831
    
    ResourceGroupName    : rg201221071831
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    : Succeeded
    Id                   : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Compute/images/ig201221071831
    Name                 : ig201221071831
    Type                 : Microsoft.Compute/images
    Location             : dbelocal
    Tags                 : {}
    
     Created a new Image
    
     Using Vnet /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft.Network/virtualNetworks/ASEVNET
    
     Creating a new Newtork Interface
    WARNING: The output object type of this cmdlet will be modified in a future release.
    
    VirtualMachine              :
    IpConfigurations            : {ip201221071831}
    DnsSettings                 : Microsoft.Azure.Commands.Network.Models.PSNetworkInterfaceDnsSettings
    MacAddress                  : 001DD87D7216
    Primary                     :
    EnableAcceleratedNetworking : False
    EnableIPForwarding          : False
    NetworkSecurityGroup        :
    ProvisioningState           : Succeeded
    VirtualMachineText          : null
    IpConfigurationsText        : [
                                    {
                                      "Name": "ip201221071831",
                                      "Etag": "W/\"27785dd5-d12a-4d73-9495-ffad7847261a\"",
                                      "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Network/networkInterfaces/nic201221071831/ipConfigurations/ip201221071831",
                                      "PrivateIpAddress": "10.57.51.61",
                                      "PrivateIpAllocationMethod": "Dynamic",
                                      "Subnet": {
                                        "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft.Network/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                        "ResourceNavigationLinks": [],
                                        "ServiceEndpoints": []
                                      },
                                      "ProvisioningState": "Succeeded",
                                      "PrivateIpAddressVersion": "IPv4",
                                      "LoadBalancerBackendAddressPools": [],
                                      "LoadBalancerInboundNatRules": [],
                                      "Primary": true,
                                      "ApplicationGatewayBackendAddressPools": [],
                                      "ApplicationSecurityGroups": []
                                    }
                                  ]
    DnsSettingsText             : {
                                    "DnsServers": [],
                                    "AppliedDnsServers": [],
                                    "InternalDomainNameSuffix": "qgotb4hjdh4efnhn0vz5adtb3f.a--x.internal.cloudapp.net"
                                  }
    NetworkSecurityGroupText    : null
    ResourceGroupName           : rg201221071831
    Location                    : dbelocal
    ResourceGuid                : e6327ab9-0855-4f04-9b36-17bbf31b5bd8
    Type                        : Microsoft.Network/networkInterfaces
    Tag                         :
    TagsTable                   :
    Name                        : nic201221071831
    Etag                        : W/"27785dd5-d12a-4d73-9495-ffad7847261a"
    Id                          : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Network/networkInterfaces/nic201221071831
    
     Created Network Interface
    
     Creating a new VM
    
     New-AzureRmVMConfig -VMName VM201221071831 -VMSize Standard_D1_v2
    
     Set-AzureRmVMOperatingSystem -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Windows -ComputerName COM201221071831 -Credential System.Management.Automation.PSCredential
    
     Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine = Set-AzureRmVMOSDisk -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Name osld201221071831 -Caching ReadWrite -CreateOption FromImage -Windows -StorageAccountType StandardLRS
    
     Add-AzureRmVMNetworkInterface -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Id /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Network/networkInterfaces/nic201221071831.Id
    
     Set-AzureRmVMSourceImage -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Id /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Compute/images/ig201221071831
    
     New-AzureRmVM -ResourceGroupName rg201221071831 -Location DBELocal -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Verbose
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasesa1, is used for boot
    diagnostics.
    VERBOSE: Performing the operation "New" on target "VM201221071831".
    
    Ticks             : 1533424841
    Days              : 0
    Hours             : 0
    Milliseconds      : 342
    Minutes           : 2
    Seconds           : 33
    TotalDays         : 0.00177479726967593
    TotalHours        : 0.0425951344722222
    TotalMilliseconds : 153342.4841
    TotalMinutes      : 2.55570806833333
    TotalSeconds      : 153.3424841
    
    RequestId           :
    IsSuccessStatusCode : True
    StatusCode          : OK
    ReasonPhrase        : OK

    PS C:\Users\v2>
    ```

    **Bir Linux sanal makinesi için:**

    Bir Linux VM oluşturmak için kullanılan komutun örneği aşağıda verilmiştir.

    ```powershell
    .\ArmPowershellClient.ps1 -VHDPath \\asefs\Logs\vmvhd -VHDFile ubuntu13.vhd -StorageAccountName myasesatest -OS Linux -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\Users\AzCopy10\AzCopy.exe
    New-AzureRmResourceGroup -Name rg201221075546 -Location DBELocal -Force    
    ```

  
1. VM 'Leri başarıyla oluşturduktan sonra, bu VM 'Lerin Azure portal sanal makineler listesinde gösterilmesi gerekir. VM 'Leri görüntülemek için, Azure portal içindeki cihazınızın Azure Stack Edge kaynağında, **uç Hizmetleri sanal makineler >** gidin. 

    ![Sanal makinelerin listesini görüntüle](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell-script/list-virtual-machine-1.png)

    Bir VM 'nin ayrıntılarını görüntülemek için VM adını seçin. Bu VM için IP 'nin dinamik ayırmasını aklınızda yapın.

    ![VM ayrıntılarını görüntüle](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell-script/view-virtual-machine-details-1.png)

1. Betiğin oluşturduğu kaynakları temizlemek için aşağıdaki komutları kullanın:
    
    ```powershell
    Get-AzureRmVM | Remove-AzureRmVM -Force
    Get-AzureRmNetworkInterface | Remove-AzureRmNetworkInterface -Force
    Get-AzureRmImage | Remove-AzureRmImage -Force
    Get-AzureRmDisk | Remove-AzureRmDisk -Force
    Get-AzureRmStorageAccount | Remove-AzureRmStorageAccount -Force
    ```


## <a name="next-steps"></a>Sonraki adımlar

[Azure PowerShell cmdlet 'lerini kullanarak VM 'Leri dağıtma](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)