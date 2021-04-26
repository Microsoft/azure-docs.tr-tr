---
title: Yakınlık yerleşimi gruplarında çalışan Azure VM 'lerini çoğaltma
description: Azure Site Recovery kullanarak yakınlık yerleşimi gruplarında çalışan Azure sanal makinelerini nasıl çoğaltacağınızı öğrenin.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: a58ec80c13ee9ae0eceb019ab2fd7909fd6f369b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889289"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Yakın Yerleştirilen Gruplarda çalıştırılan Azure sanal makinelerini başka bir bölgeye çoğaltma

Bu makalede, bir yakınlık yerleştirme grubunda çalışan sanal makinelerin ikincil bir bölgeye nasıl çoğaltılacağı, yük devretmeyle ve yeniden çalıştırıldığı açıklanmaktadır.

[Yakınlık yerleştirme grupları](../virtual-machines/windows/proximity-placement-groups-portal.md) , UYGULAMALARıNıZLA ilişkili VM 'ler arası ağ gecikmesini azaltmak için kullanabileceğiniz bir Azure sanal makine mantıksal gruplandırma özelliğidir. VM 'Ler aynı yakınlık yerleşimi grubu içinde dağıtıldığında, fiziksel olarak birbirlerine mümkün olduğunca yakın şekilde bulunur. Yakınlık yerleşimi grupları özellikle gecikme süresine duyarlı iş yüklerinin gereksinimlerini karşılamak için yararlıdır.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Yakınlık yerleştirme gruplarıyla olağanüstü durum kurtarma

Tipik bir senaryoda, uygulamanızın çeşitli katmanları arasındaki ağ gecikmesini önlemek için sanal makineleriniz bir yakınlık yerleşimi grubunda çalışıyor olabilir. Bu, uygulamanızın en iyi ağ gecikmesini sağlayabildiği sürece, herhangi bir bölge düzeyindeki başarısızlık için Site Recovery kullanarak bu uygulamaları korumak istersiniz. Site Recovery, verileri bir bölgeden başka bir Azure bölgesine çoğaltır ve yük devretme durumunda makineleri olağanüstü durum kurtarma bölgesinde getirir.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

- En iyi çaba, sanal makineleri bir yakınlık yerleşimi grubuna devretmek/yeniden çalıştırmanız olacaktır. Ancak, VM yük devretme/yeniden çalışma sırasında yakınlık yerleşimi içinde getirilemeiyorsa, yük devretme/yeniden çalışma işlemi devam eder ve sanal makineler bir yakınlık yerleşimi grubu dışında oluşturulur.
- Bir kullanılabilirlik kümesi bir yakınlık yerleşimi grubuna sabitlenmiştir ve kullanılabilirlik kümesindeki yük devretme/yeniden çalışma VM 'Leri için bir ayırma kısıtlaması varsa, sanal makineler hem kullanılabilirlik kümesi hem de yakınlık yerleşimi grubu dışında oluşturulur.
- Yönetilmeyen diskler için Site Recovery yakınlık yerleştirme grupları desteklenmez.

> [!NOTE]
> Azure Site Recovery, Hyper-V ' d e Azure senaryolarına yönelik yönetilen disklerden yeniden çalışmayı desteklemez. Bu nedenle, Azure 'daki yakınlık yerleşimi grubundan Hyper-V ' d e yeniden çalışma desteklenmez.

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-portal"></a>Portal aracılığıyla yakınlık yerleştirme gruplarındaki VM 'Ler için olağanüstü durum kurtarmayı ayarlama

### <a name="azure-to-azure-via-portal"></a>Portal aracılığıyla Azure 'dan Azure 'a

VM olağanüstü durum kurtarma sayfasından bir sanal makine için çoğaltmayı etkinleştirmeyi veya önceden oluşturulmuş bir kasaya gidip Site Recovery bölümüne gidip çoğaltmayı etkinleştirerek çoğaltmayı etkinleştirmenizi tercih edebilirsiniz. Her iki yaklaşımdan de bir PPG içindeki VM 'Ler için Site Recovery nasıl ayarlantığınızın bir bakalım:

- IaaS VM DR dikey penceresinde çoğaltmayı etkinleştirirken DR bölgesinde PPG 'yi seçme:
  1. Sanal makineye gidin. Sol taraftaki dikey pencerede, ' Işlemler ' altında ' olağanüstü durum kurtarma ' ' yı seçin.
  2. ' Temel bilgiler ' sekmesinde, VM 'yi çoğaltmak istediğiniz DR bölgesini seçin. ' Gelişmiş ayarlar 'a git
  3. Burada, sanal makinenizin yakınlık yerleşimi grubunu ve DR bölgesinde bir PPG 'yi seçme seçeneğini görebilirsiniz. Site Recovery Ayrıca, bu varsayılan seçeneği kullanmayı seçerseniz, sizin için oluşturduğu yeni bir yakınlık yerleşimi grubu kullanma seçeneği sunar. İstediğiniz yakınlık yerleşimi grubunu seçip ' gözden geçir + çoğaltmayı Başlat ' ' a gidip son olarak çoğaltmayı etkinleştirebilirsiniz.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-1.png" alt-text="Çoğaltmayı etkinleştirin.":::

- Kasa dikey penceresinde çoğaltmayı etkinleştirirken DR bölgesinde PPG 'yi seçme:
  1. Kurtarma Hizmetleri kasanıza gidin ve Site Recovery sekmesine gidin
  2. ' + Site Recovery etkinleştir ' düğmesine tıklayın ve ardından Azure sanal makineler altında ' 1: çoğaltmayı etkinleştir ' seçeneğini belirleyin (bir Azure VM 'yi çoğaltmak istediğinizde)
  3. ' Kaynak ' sekmesinde gerekli alanları doldurup ' Ileri ' seçeneğine tıklayın
  4. ' Sanal makineler ' sekmesinde çoğaltmayı etkinleştirmek istediğiniz VM 'lerin listesini seçin ve ' Ileri ' seçeneğine tıklayın
  5. Burada, DR bölgesinde bir PPG seçme seçeneğini görebilirsiniz. Site Recovery Ayrıca, bu varsayılan seçeneği kullanmayı seçerseniz, sizin için oluşturduğu yeni bir PPG kullanma seçeneği sunar. İstediğiniz PPG 'yi seçip çoğaltmayı etkinleştirmeye devam edebilirsiniz.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-2.png" alt-text="Kasa aracılığıyla çoğaltmayı etkinleştirin.":::

VM için çoğaltma etkinleştirildikten sonra, DR bölgesindeki PPG seçimini kolayca güncelleştirebileceğinizi unutmayın.

1. Sanal makineye gidin ve sol taraftaki dikey pencerede ' Işlemler ' altında ' olağanüstü durum kurtarma ' ' yı seçin.
2. ' Işlem ve ağ ' dikey penceresine gidip sayfanın en üstündeki ' Düzenle ' seçeneğine tıklayın
3. Hedef PPG dahil olmak üzere birden çok hedef ayarı düzenleme seçeneklerini görebilirsiniz. VM 'nin yük devretmesini istediğiniz PPG 'yi seçin ve ' Kaydet 'e tıklayın.

### <a name="vmware-to-azure-via-portal"></a>Portal aracılığıyla VMware 'den Azure 'a

Hedef VM için yakınlık yerleşimi grubu VM için çoğaltma etkinleştirildikten sonra ayarlanabilir. Lütfen gereksinimlerinize göre hedef bölgede PPG 'yi ayrı olarak oluşturduğunuzdan emin olun. Daha sonra, VM için çoğaltma etkinleştirildikten sonra DR bölgesindeki PPG seçimini kolayca güncelleştirebilirsiniz.

1. Kasadan sanal makineyi seçin ve sol taraftaki dikey pencerede ' Işlemler ' altında ' olağanüstü durum kurtarma ' ' yı seçin.
2. ' Işlem ve ağ ' dikey penceresine gidip sayfanın en üstündeki ' Düzenle ' seçeneğine tıklayın
3. Hedef PPG dahil olmak üzere birden çok hedef ayarı düzenleme seçeneklerini görebilirsiniz. VM 'nin yük devretmesini istediğiniz PPG 'yi seçin ve ' Kaydet 'e tıklayın.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-v2a.png" alt-text="PPG V2A Güncelleştir":::

### <a name="hyper-v-to-azure-via-portal"></a>Portal üzerinden Azure 'da Hyper-V

Hedef VM için yakınlık yerleşimi grubu VM için çoğaltma etkinleştirildikten sonra ayarlanabilir. Lütfen gereksinimlerinize göre hedef bölgede PPG 'yi ayrı olarak oluşturduğunuzdan emin olun. Daha sonra, VM için çoğaltma etkinleştirildikten sonra DR bölgesindeki PPG seçimini kolayca güncelleştirebilirsiniz.

1. Kasadan sanal makineyi seçin ve sol taraftaki dikey pencerede ' Işlemler ' altında ' olağanüstü durum kurtarma ' ' yı seçin.
2. ' Işlem ve ağ ' dikey penceresine gidip sayfanın en üstündeki ' Düzenle ' seçeneğine tıklayın
3. Hedef PPG dahil olmak üzere birden çok hedef ayarı düzenleme seçeneklerini görebilirsiniz. VM 'nin yük devretmesini istediğiniz PPG 'yi seçin ve ' Kaydet 'e tıklayın.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-h2a.png" alt-text="PPG H2A Güncelleştir":::

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-powershell"></a>Yakınlık yerleştirme gruplarındaki VM 'Ler için PowerShell aracılığıyla olağanüstü durum kurtarmayı ayarlama

### <a name="prerequisites"></a>Önkoşullar 

1. Azure PowerShell az modüle sahip olduğunuzdan emin olun. Azure PowerShell yüklemeniz veya yükseltmeniz gerekiyorsa, [Azure PowerShell yüklemek ve yapılandırmak için bu kılavuzu](/powershell/azure/install-az-ps)izleyin.
2. En az Azure PowerShell az sürüm 4.1.0 olmalıdır. Geçerli sürümü denetlemek için aşağıdaki komutu kullanın-

    ```
    Get-InstalledModule -Name Az
    ```

### <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Yakınlık yerleştirme grubundaki sanal makineler için Site Recovery ayarlama

> [!NOTE]
> Hedef yakınlık yerleşimi grubunun benzersiz KIMLIĞINE sahip olduğunuzdan emin olun. Yeni bir yakınlık yerleşimi grubu oluşturuyorsanız, [burada](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group) komutu kontrol edin ve var olan bir yakınlık yerleşimi grubunu kullanıyorsanız [buradaki](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups)komutu kullanın.

### <a name="azure-to-azure"></a>Azure-Azure arası

1. Hesabınızda [oturum açın](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription) ve aboneliğinizi ayarlayın.
2. Çoğaltmak istediğiniz sanal makinenin ayrıntılarını [burada](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated)belirtildiği gibi alın.
3. Kurtarma Hizmetleri kasanızı [oluşturun](./azure-to-azure-powershell.md#create-a-recovery-services-vault) ve kasa bağlamını [ayarlayın](./azure-to-azure-powershell.md#set-the-vault-context) .
4. Kasayı çoğaltma sanal makinesini başlatacak şekilde hazırlayın. Bu, hem birincil hem de kurtarma bölgeleri için bir [Service Fabric nesnesi](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) oluşturulmasını içerir.
5. Hem birincil hem de kurtarma yapıları için Site Recovery bir koruma kapsayıcısı [oluşturun](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric) .
6. Çoğaltma İlkesi [oluşturun](./azure-to-azure-powershell.md#create-a-replication-policy) .
7. [Bu](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) adımları ve [burada](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)belirtildiği gibi yeniden çalışma için bir koruma kapsayıcısı eşlemesini kullanarak birincil ve kurtarma koruma kapsayıcısı arasında bir koruma kapsayıcısı eşlemesi oluşturun.
8. [Aşağıdaki adımları izleyerek](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account) önbellek depolama hesabı oluşturun.
9. [Burada](./azure-to-azure-powershell.md#create-network-mappings)belirtildiği gibi gerekli ağ eşlemelerini oluşturun.
10. Azure sanal makinesini yönetilen disklerle çoğaltmak için aşağıdaki PowerShell cmdlet 'ini kullanın-

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

#Make sure to replace the variables $datadiskName with data disk name.

#Data disk
$datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
$datadiskId1 = $datadisk[0].Id
$RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
$RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

Birden çok veri diski için çoğaltmayı etkinleştirirken, aşağıdaki PowerShell cmdlet 'ini kullanın-

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig

#Data disk

# Add data disks
Foreach( $disk in $VM.StorageProfile.DataDisks)
{
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $dataDiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
    $diskconfigs += $DataDisk1ReplicationConfig
}

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

PPG ile bölge çoğaltmasına bölge 'yi etkinleştirirken çoğaltma başlatma komutu PowerShell cmdlet 'i ile değiştirilir.

```azurepowershell
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
```

Çoğaltma başlatma işlemi başarılı olduktan sonra, sanal makine verileri kurtarma bölgesine çoğaltılır.

Çoğaltma işlemi başlangıçta, kurtarma bölgesindeki sanal makinenin çoğaltılan disklerinin bir kopyasını dengeli olarak başlatır. Bu aşamaya ilk çoğaltma aşaması denir.

İlk çoğaltma tamamlandıktan sonra çoğaltma, fark eşitleme aşamasına gider. Bu noktada, sanal makine korunur ve üzerinde bir test yük devretmesi işlemi gerçekleştirilebilir. İlk çoğaltma tamamlandıktan sonra, sanal makineyi temsil eden çoğaltılan öğenin çoğaltma durumu korunan duruma geçer.

Kendisine karşılık gelen çoğaltma korumalı öğenin ayrıntılarını alarak sanal makinenin çoğaltma durumunu ve çoğaltma durumunu izleyin.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Yük devretme testini doğrulamak, test yük devretmesini doğrulamak ve temizlemek için [aşağıdaki](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover) adımları izleyin.
12. Yük devretme için [burada](./azure-to-azure-powershell.md#fail-over-to-azure)bahsedilen adımları izleyin.
13. Kaynak bölgeye yeniden koruma ve yeniden çalışma için aşağıdaki PowerShell cmdlet 'ini kullanın:

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```

14. Çoğaltmayı devre dışı bırakmak için [buradaki](./azure-to-azure-powershell.md#disable-replication)adımları izleyin.

### <a name="vmware-to-azure-via-powershell"></a>PowerShell aracılığıyla VMware 'den Azure 'a

1. Şirket [Içi VMware sunucularını](./vmware-azure-tutorial-prepare-on-premises.md) Azure 'a olağanüstü durum kurtarma için hazırladığınızdan emin olun.
2. Hesabınızda oturum açın ve aboneliğinizi [burada](./vmware-azure-disaster-recovery-powershell.md#log-into-azure)belirtilen şekilde ayarlayın.
3. Bir kurtarma hizmetleri Kasası [kurun](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault) ve [kasa bağlamını ayarlayın](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context).
4. Kasa kaydınızı [doğrulayın](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration) .
5. Çoğaltma İlkesi [oluşturun](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy) .
6. Bir vCenter sunucusu [ekleyin](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms) ve sanal makineleri bulun ve çoğaltma için depolama hesapları [oluşturun](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication) .
7. VMware sanal makinelerini çoğaltmak için buradaki ayrıntıları denetleyin ve aşağıdaki PowerShell cmdlet 'ini izleyin:

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
```

8. Get-ASRReplicationProtectedItem cmdlet 'ini kullanarak sanal makinenin çoğaltma durumunu ve çoğaltma durumunu kontrol edebilirsiniz.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

9. [Buradaki](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings)adımları izleyerek yük devretme ayarlarını yapılandırın.
10. Yük devretme testi [çalıştırın](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover) .
11. [Bu](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure) adımları kullanarak Azure 'a yük devretme.

### <a name="hyper-v-to-azure-via-powershell"></a>PowerShell aracılığıyla Hyper-V-Azure arası

1. Şirket [Içi Hyper-V sunucularınızı](./hyper-v-prepare-on-premises-tutorial.md) Azure 'a olağanüstü durum kurtarma için hazırlayın.
2. Azure ['Da oturum açın](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account) .
3. Kasanızı [ayarlayın](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault) ve kurtarma hizmetleri Kasası bağlamını [ayarlayın](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context) .
4. Hyper-V sitesi [oluşturun](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site) .
5. Sağlayıcıyı ve aracıyı [yükler](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent) .
6. Çoğaltma İlkesi [oluşturun](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy) .
7. Aşağıdaki adımları kullanarak çoğaltmayı etkinleştirin – 
    
    a. Korumak istediğiniz VM 'ye karşılık gelen korunabilir öğeyi aşağıdaki gibi alın:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. VM 'yi koruyun. Koruduğunuz sanal makineye birden fazla disk ekli ise, OSDiskName parametresini kullanarak işletim sistemi diskini belirtin.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```
    c. İlk çoğaltmadan sonra VM 'Lerin korumalı duruma ulaşmasını bekleyin. Bu işlem, çoğaltılacak veri miktarı ve Azure için kullanılabilir yukarı akış bant genişliği gibi etkenlere bağlı olarak biraz zaman alabilir. Korunan bir durum olduğunda, iş durumu ve StateDescription aşağıdaki gibi güncelleştirilir: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Yük devretmeden sonra kurtarma özelliklerini (VM rolü boyutu gibi) ve VM NIC 'nin ekleneceği Azure ağını güncelleştirin.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. [Yük devretme](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover)testi çalıştırın.


## <a name="next-steps"></a>Sonraki adımlar

VMware 'den Azure 'a yeniden koruma ve yeniden çalışma gerçekleştirmek için [burada](./vmware-azure-prepare-failback.md)özetlenen adımları izleyin.

Azure 'da Hyper-V ' d e yük devretme gerçekleştirmek için [burada](./site-recovery-failover.md) özetlenen adımları izleyin ve yeniden çalışma gerçekleştirmek için [burada](./hyper-v-azure-failback.md)özetlenen adımları izleyin.

Daha fazla bilgi için bkz. [Site Recovery 'de yük devretme](site-recovery-failover.md).
