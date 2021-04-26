---
title: Azure Stack Edge Pro GPU cihazındaki VM etiketlerini Azure PowerShell aracılığıyla yönetme
description: Azure PowerShell kullanarak Azure Stack Edge 'de çalışan sanal makineler için sanal makine etiketlerinin nasıl oluşturulacağını ve yönetileceğini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: be4348359e6b53c3e7454e9ab7c1af8ce8a7020a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305570"
---
# <a name="manage-vm-tags-on-azure-stack-edge-via-azure-powershell"></a>Azure Stack Edge üzerinde VM etiketlerini Azure PowerShell aracılığıyla yönetme

Bu makalede, Azure PowerShell kullanarak Azure Stack Edge Pro GPU cihazlarınızda çalışan sanal makinelerin (VM 'Ler) nasıl etiketleneceğini açıklanmaktadır.

## <a name="about-tags"></a>Etiketler hakkında

Etiketler, bir kaynağa veya kaynak grubuna atanabilecek Kullanıcı tanımlı anahtar-değer çiftleridir. Bunları bir taksonomiye mantıksal olarak düzenlemek için cihazınızda çalışan VM 'lere Etiketler uygulayabilirsiniz. Oluşturma sırasında bir kaynağa etiket yerleştirebilir veya var olan bir kaynağa ekleyebilirsiniz. Örneğin, `Organization` `Engineering` kuruluşunuzdaki Mühendislik departmanı tarafından kullanılan tüm VM 'lere adı ve değeri uygulayabilirsiniz.

Etiketler hakkında daha fazla bilgi için bkz. [Azurerd PowerShell aracılığıyla etiketleri yönetme](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).

## <a name="prerequisites"></a>Önkoşullar

Bir VM 'yi PowerShell aracılığıyla cihazınıza dağıtabilmeniz için önce aşağıdakileri yaptığınızdan emin olun:

- Cihazınıza bağlanmak için kullanacağınız bir istemciye erişiminiz var.
    - İstemciniz [desteklenen bir işletim sistemi](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)çalıştırıyor.
    - İstemciniz, [cihazınız için Azure Resource Manager bağlanma](azure-stack-edge-gpu-connect-resource-manager.md)bölümündeki yönergelere göre cihazınızın yerel Azure Resource Manager bağlanacak şekilde yapılandırılmıştır.


## <a name="verify-connection-to-local-azure-resource-manager"></a>Yerel Azure Resource Manager bağlantıyı doğrulama

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]


## <a name="add-a-tag-to-a-vm"></a>VM 'ye etiket ekleme

1. Bazı parametreleri ayarlayın.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. VM nesnesini ve etiketlerini alın.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. Etiketi ekleyin ve VM 'yi güncelleştirin. VM 'nin güncelleştirilmesi birkaç dakika sürebilir.

    Komutu kullanıcı onayı olmadan çalıştırmak için isteğe bağlı **-zorla** bayrağını kullanabilirsiniz.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```

    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzureRmResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    
    Name              : myasetestvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
                        lMachines/myasetestvm1
    ResourceName      : myasetestvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg2
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {Organization}
    Properties        : @{vmId=958c0baa-e143-4d8a-82bd-9c6b1ba45e86; hardwareProfile=; storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

Daha fazla bilgi için bkz. [Add-AzureRMTag](/powershell/module/azurerm.tags/remove-azurermtag?view=azurermps-6.13.0&preserve-view=true).

## <a name="view-tags-of-a-vm"></a>Bir VM 'nin etiketlerini görüntüleme

Cihazınızda çalışan belirli bir sanal makineye uygulanan etiketleri görüntüleyebilirsiniz. 

1. Etiketlerini görüntülemek istediğiniz VM ile ilişkili parametreleri tanımlayın.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```
1. VM nesnesini alın ve etiketlerini görüntüleyin.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine

    ResourceGroupName : myaserg2
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
    lMachines/myasetestvm1
    VmId              : 958c0baa-e143-4d8a-82bd-9c6b1ba45e86
    Name              : myasetestvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Sales"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
## <a name="view-tags-for-all-resources"></a>Tüm kaynaklar için etiketleri görüntüle

Cihazınızın yerel Azure Resource Manager aboneliğindeki (Azure aboneliğinizden farklı) tüm kaynaklar için geçerli etiket listesini görüntülemek için `Get-AzureRMTag` komutunu kullanın.


Aşağıda, cihazınızda birden çok VM çalışırken ve tüm VM 'lerde tüm etiketleri görüntülemek istediğinizde örnek bir çıktı verilmiştir.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag

Name         Count
----         -----
Organization 3

PS C:\WINDOWS\system32>
```

Yukarıdaki çıkış, `Organization` cihazınızda çalışan VM 'lerde üç etiket olduğunu gösterir.

Daha fazla ayrıntı görüntülemek için parametresini kullanın `-Detailed` .

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Engineering  2
              Sales        1

Count       : 3
Values      : {Engineering, Sales}

PS C:\WINDOWS\system32>
```

Yukarıdaki çıktı, üç etiketten, 2 sanal makine olarak etiketlendiği `Engineering` ve bir birinin öğesine ait olarak etiketlendiği anlamına gelir `Sales` .

## <a name="remove-a-tag-from-a-vm"></a>Bir VM 'den bir etiketi kaldırma

1. Bazı parametreleri ayarlayın. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. VM nesnesini Al.

    ```powershell
    $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    ResourceGroupName : myaserg1
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselinuxvm1
    VmId              : 290b3fdd-0c99-4905-9ea1-cf93cd6f25ee
    Name              : myaselinuxvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Engineering"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
                                                                              PS C:\WINDOWS\system32> 
    ```
3. Etiketi kaldırın ve VM 'yi güncelleştirin. `-Force`Komutu kullanıcı onayı olmadan çalıştırmak için isteğe bağlı bayrağını kullanın.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```
    Örnek bir çıktı aşağıda verilmiştir:

    ```powershell
    PS C:\WINDOWS\system32> $tags = $Virtualmachine.Tags
    PS C:\WINDOWS\system32> $tags
    Key          Value
    ---          -----
    Organization Engineering
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzureRMResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    Name              : myaselinuxvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGrou
                        ps/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselin
                        uxvm1
    ResourceName      : myaselinuxvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg1
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {}
    Properties        : @{vmId=290b3fdd-0c99-4905-9ea1-cf93cd6f25ee; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    PS C:\WINDOWS\system32>
    ```


## <a name="next-steps"></a>Sonraki adımlar

[Azurerd PowerShell aracılığıyla etiketleri yönetmeyi](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true)öğrenin.
