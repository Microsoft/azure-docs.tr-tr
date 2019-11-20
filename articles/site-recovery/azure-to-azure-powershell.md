---
title: Azure PowerShell ve Azure Site Recovery kullanarak Azure VM 'Leri için olağanüstü durum kurtarma
description: Azure PowerShell kullanarak Azure Site Recovery Azure sanal makineleri için olağanüstü durum kurtarma ayarlamayı öğrenin.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: aa91725daf36113334849dd15dd01b6ce6ed4389
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621086"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Azure PowerShell kullanarak Azure sanal makineleri için olağanüstü durum kurtarmayı ayarlama


Bu makalede, Azure PowerShell kullanarak Azure sanal makineleri için olağanüstü durum kurtarmayı ayarlama ve test etme hakkında bilgi alırsınız.

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> - Kurtarma Hizmetleri kasası oluşturun.
> - PowerShell oturumu için kasa bağlamını ayarlayın.
> - Azure sanal makinelerini Çoğaltmaya başlamak için kasayı hazırlayın.
> - Ağ eşlemeleri oluşturun.
> - Sanal makineleri çoğaltmak için depolama hesapları oluşturun.
> - Olağanüstü durum kurtarma için Azure sanal makinelerini bir kurtarma bölgesine çoğaltın.
> - Yük devretme testi, doğrulama ve test yük devretmesini Temizleme gerçekleştirin.
> - Kurtarma bölgesine yük devretme.

> [!NOTE]
> Portal üzerinden sunulan senaryo yetilerinin hepsi Azure PowerShell aracılığıyla kullanılabilir olabilir. Şu anda Azure PowerShell aracılığıyla desteklenmeyen senaryo özellikleri şunlardır:
> - Bir sanal makinedeki tüm disklerin, sanal makinenin her diskini açık bir şekilde belirtmek zorunda kalmadan çoğaltılması gerektiğini belirtme özelliği.  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce:
- [Senaryo mimarisini ve bileşenlerini ](azure-to-azure-architecture.md) anladığınızdan emin olun.
- Tüm bileşenler için [destek gereksinimlerini](azure-to-azure-support-matrix.md) gözden geçirin.
- Azure PowerShell `Az` modüle sahipsiniz. Azure PowerShell yüklemeniz veya yükseltmeniz gerekiyorsa, [Azure PowerShell yüklemek ve yapılandırmak için bu kılavuzu](/powershell/azure/install-az-ps)izleyin.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure aboneliğinizde oturum açın

Connect-AzAccount cmdlet 'ini kullanarak Azure aboneliğinizde oturum açın

```azurepowershell
Connect-AzAccount
```
Azure aboneliğinizi seçin. Erişiminiz olan Azure aboneliklerinin listesini almak için Get-AzSubscription cmdlet 'ini kullanın. Select-AzSubscription cmdlet 'ini kullanarak çalışmak için Azure aboneliğini seçin.

```azurepowershell
Select-AzSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Çoğaltılacak sanal makinelerin ayrıntılarını al

Bu makaledeki örnekte, Doğu ABD bölgesindeki bir sanal makine Batı ABD 2 bölgesinde çoğaltılır ve kurtarılacak. Çoğaltılan sanal makine, işletim sistemi diskine ve tek bir veri diskine sahip bir sanal makinedir. Örnekte kullanılan sanal makinenin adı AzureDemoVM ' dir.

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Sanal makinenin diskleri için disk ayrıntılarını alın. Disk ayrıntıları, sanal makine için çoğaltma başlatılırken daha sonra kullanılacaktır.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

Kurtarma Hizmetleri kasasının oluşturulacağı bir kaynak grubu oluşturun.

> [!IMPORTANT]
> * Kurtarma Hizmetleri kasası ve korunan sanal makineler, farklı Azure konumlarında olmalıdır.
> * Kurtarma Hizmetleri kasasının kaynak grubu ve korunan sanal makineler, farklı Azure konumlarında olmalıdır.
> * Kurtarma Hizmetleri kasası ve ait olduğu kaynak grubu aynı Azure konumunda olabilir.

Bu makaledeki örnekte, korunmakta olan sanal makine Doğu ABD bölgesidir. Olağanüstü durum kurtarma için seçilen kurtarma bölgesi Batı ABD 2 bölgesidir. Kurtarma Hizmetleri kasası ve kasanın kaynak grubu her ikisi de kurtarma bölgesinde (Batı ABD 2)

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Bir kurtarma hizmetleri Kasası oluşturun. Aşağıdaki örnekte, Batı ABD 2 bölgesinde a2aDemoRecoveryVault adlı bir kurtarma hizmetleri Kasası oluşturulur.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```
## <a name="set-the-vault-context"></a>Kasa bağlamını ayarla


PowerShell oturumunda kullanılacak kasa bağlamını ayarlayın. Bir kez ayarlandıktan sonra, PowerShell oturumunda izleyen Azure Site Recovery işlemleri seçili kasa bağlamında gerçekleştirilir.

 ```azurepowershell
#Setting the vault context.
Set-AsrVaultSettings -Vault $vault

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

Azure 'dan Azure 'a geçiş için kasa bağlamını yeni oluşturulan kasaya ayarlayabilirsiniz: 

```azurepowershell

#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault

```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Azure sanal makinelerini Çoğaltmaya başlamak için kasayı hazırlama

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Birincil (kaynak) bölgeyi temsil eden bir Site Recovery Fabric nesnesi oluşturma

Kasadaki yapı nesnesi bir Azure bölgesini temsil eder. Birincil yapı nesnesi, kasala korunan sanal makinelerin ait olduğu Azure bölgesini temsil etmek için oluşturulur. Bu makaledeki örnekte, korunmakta olan sanal makine Doğu ABD bölgesidir.

- Her bölge için yalnızca bir yapı nesnesi oluşturulabilir.
- Daha önce Azure portal bir VM için Site Recovery çoğaltmasını etkinleştirdiyseniz Site Recovery otomatik olarak bir Fabric nesnesi oluşturur. Bir bölge için bir yapı nesnesi varsa yeni bir tane oluşturamazsınız.


Başlamadan önce Site Recovery işlemlerinin zaman uyumsuz olarak yürütüldüğünü unutmayın. Bir işlemi başlattığınızda bir Azure Site Recovery işi gönderilir ve bir iş izleme nesnesi döndürülür. İş için en son durumu almak üzere iş izleme nesnesini kullanın (Get-ASRJob) ve işlemin durumunu izlemek için.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
Birden çok Azure bölgesindeki sanal makineler aynı kasaya korunuyorsa, her kaynak Azure bölgesi için bir yapı nesnesi oluşturun.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Kurtarma bölgesini temsil eden bir Site Recovery Fabric nesnesi oluşturma

Recovery Fabric nesnesi kurtarma Azure konumunu temsil eder. Sanal makineler, kurtarma dokusuna göre temsil edilen kurtarma bölgesinin (bir yük devretme durumunda) öğesine çoğaltılır ve bu durumdan kurtarılacak. Bu örnekte kullanılan kurtarma Azure bölgesi Batı ABD 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Birincil dokuda Site Recovery koruma kapsayıcısı oluşturma

Koruma kapsayıcısı, çoğaltılan öğeleri bir yapı içinde gruplamak için kullanılan bir kapsayıcıdır.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Kurtarma dokusunda Site Recovery koruma kapsayıcısı oluşturma

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>Çoğaltma ilkesi oluşturma

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Birincil ve kurtarma koruma kapsayıcısı arasında bir koruma kapsayıcısı eşlemesi oluşturma

Koruma kapsayıcısı eşlemesi, birincil koruma kapsayıcısını bir kurtarma koruma kapsayıcısı ve bir çoğaltma ilkesiyle eşleştirir. Bir koruma kapsayıcısı çifti arasında sanal makineleri çoğaltmak için kullanacağınız her çoğaltma ilkesi için bir eşleme oluşturun.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Yeniden çalışma için bir koruma kapsayıcısı eşlemesi oluştur (yük devretmeden sonra çoğaltmayı tersine çevir)

Yük devretmenin ardından, yükü devredilen sanal makineyi özgün Azure bölgesine geri getirmeye hazırsanız, yeniden çalışma işlemi yapılır. Yeniden çalışma için, yük devredilen sanal makine, yük devredilen bölgeden özgün bölgeye ters çoğaltılır. Ters çoğaltma için özgün bölgenin ve kurtarma bölgesi anahtarının rolleri. Özgün bölge artık yeni kurtarma bölgesi haline gelir ve başlangıçta kurtarma bölgesi artık birincil bölge haline gelir. Çoğaltmayı tersine çevirme için koruma kapsayıcısı eşlemesi, özgün ve kurtarma bölgelerinin anahtarlamalı rollerini temsil eder.

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Önbellek depolama hesapları ve hedef depolama hesapları oluştur

Önbellek depolama hesabı, çoğaltılan sanal makine ile aynı Azure bölgesindeki standart bir depolama hesabıdır. Önbellek depolama hesabı, değişiklikler kurtarma Azure bölgesine taşınmadan önce geçici olarak çoğaltma değişikliklerini tutmak için kullanılır. Bir sanal makinenin farklı diskleri için farklı önbellek depolama hesapları belirtmeniz (ancak gerekmez) seçeneğini belirleyebilirsiniz.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

**Yönetilen diskler kullanmayan**sanal makinelerde, hedef depolama hesabı, sanal makinenin disklerinin çoğaltılacağı kurtarma bölgesindeki depolama hesabıdır. Hedef depolama hesabı bir standart depolama hesabı ya da bir Premium depolama hesabı olabilir. Diskler için veri değişim oranı (GÇ Yazma hızı) ve depolama türü için desteklenen Azure Site Recovery dalgalanma sınırları temelinde gereken depolama hesabı türünü seçin.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Ağ eşlemeleri oluşturma

Ağ eşlemesi, birincil bölgedeki sanal ağları kurtarma bölgesindeki sanal ağlara eşler. Ağ eşlemesi, kurtarma bölgesindeki, birincil sanal ağdaki bir sanal makinenin yük devretmesinin gerektiği Azure sanal ağını belirtir. Bir Azure sanal ağı, bir kurtarma bölgesindeki yalnızca tek bir Azure sanal ağıyla eşleştirilebilir.

- Yük devretme için kurtarma bölgesinde bir Azure sanal ağı oluşturun

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Birincil sanal ağı alma (sanal makinenin bağlandığı VNET)
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- Birincil sanal ağ ile kurtarma sanal ağı arasında ağ eşlemesi oluşturma
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State

   ```
- Ters yön (yeniden çalışma) için ağ eşlemesi oluşturma
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Azure sanal makinesini çoğaltma

Azure sanal makinesini **yönetilen disklerle**çoğaltın.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1  = $vm.StorageProfile.DataDisks[0].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[0].StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

```

Azure sanal makinesini **yönetilmeyen disklerle**çoğaltın.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.  
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Çoğaltma başlatma işlemi başarılı olduktan sonra, sanal makine verileri kurtarma bölgesine çoğaltılır.

Çoğaltma işlemi başlangıçta, kurtarma bölgesindeki sanal makinenin çoğaltılan disklerinin bir kopyasını dengeli olarak başlatır. Bu aşamaya ilk çoğaltma aşaması denir.

İlk çoğaltma tamamlandıktan sonra çoğaltma, fark eşitleme aşamasına gider. Bu noktada, sanal makine korunur ve üzerinde bir test yük devretmesi işlemi gerçekleştirilebilir. İlk çoğaltma tamamlandıktan sonra, sanal makineyi temsil eden çoğaltılan öğenin çoğaltma durumu "korumalı" durumuna geçer.

Kendisine karşılık gelen çoğaltma korumalı öğenin ayrıntılarını alarak sanal makinenin çoğaltma durumunu ve çoğaltma durumunu izleyin.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Yük devretme testi, doğrulama ve temizlik testi yük devretmesini gerçekleştirme

Sanal makine için çoğaltma korumalı bir duruma ulaştığında, sanal makinede (sanal makinenin çoğaltma korumalı öğesinde) bir sınama yük devretmesi işlemi gerçekleştirebilirsiniz.

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Yük devretme testi gerçekleştirin.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

Yük devretme testi işleminin tamamlanmasını bekleyin.
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```
Yük devretme testi işi başarıyla tamamlandıktan sonra, yük devretme testi sanal makinesine bağlanabilir ve test yük devretmesini doğrulayabilirsiniz.

Yük devretme testi sanal makine üzerinde test tamamlandıktan sonra, temizleme testi yük devretmesi işlemini başlatarak test kopyasını temizleyin. Bu işlem, test yük devretmesi tarafından oluşturulan sanal makinenin test kopyasını siler.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Azure'a yük devretme

Sanal makineyi belirli bir kurtarma noktasına devreder.

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

Başarılı bir şekilde yük devretmek için yük devretme işlemini gerçekleştirebilirsiniz.
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-failback-to-source-region"></a>Kaynak bölgeye yeniden koruma ve yeniden çalışma

Yük devretmenin ardından, özgün bölgeye geri dönmek için hazırsanız, Update-AzRecoveryServicesAsrProtectionDirection cmdlet 'ini kullanarak çoğaltma korumalı öğe için çoğaltmayı tersine çevirme işlemini başlatın.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage accountin West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $RecoveryProtContainer -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.Id
```

Yeniden koruma tamamlandıktan sonra, yük devretmeyi ters yönde başlatabilir (Doğu ABD Batı ABD) ve kaynak bölgeye yeniden çalışma sağlayabilirsiniz.

## <a name="disable-replication"></a>Çoğaltmayı devre dışı bırakma

Remove-Asrreplicationkorunabilir Dıdıtem cmdlet 'ini kullanarak çoğaltmayı devre dışı bırakabilirsiniz.

```azurepowershell
Remove-ASRReplicationProtectedItem -ReplicationProtectedItem $ReplicatedItem
```

## <a name="next-steps"></a>Sonraki adımlar
Kurtarma planlarını oluşturma ve PowerShell aracılığıyla kurtarma planlarının yük devretmesini test etme gibi diğer görevleri nasıl gerçekleştirebileceğinizi öğrenmek için [Azure Site Recovery PowerShell başvurusunu](https://docs.microsoft.com/powershell/module/az.RecoveryServices) görüntüleyin.
