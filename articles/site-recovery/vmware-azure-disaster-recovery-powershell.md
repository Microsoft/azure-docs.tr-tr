---
title: Azure Site 'da PowerShell kullanarak VMware olağanüstü durum kurtarmayı ayarlama
description: Azure Site Recovery 'de PowerShell kullanarak VMware VM 'lerinin olağanüstü durum kurtarması için Azure 'a çoğaltma ve yük devretme ayarlamayı öğrenin.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 06/30/2019
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: 9546ae590918cdf6f3a6a95b9a68e9208054dcee
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953941"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>PowerShell ile Azure 'da VMware VM 'lerinin olağanüstü durum kurtarması ayarlama

Bu makalede, Azure PowerShell kullanarak VMware sanal makinelerini Azure 'a nasıl çoğaltacağınızı ve yük devredecağınızı görürsünüz.

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> - Bir kurtarma hizmetleri Kasası oluşturun ve kasa bağlamını ayarlayın.
> - Kasadaki sunucu kaydını doğrulayın.
> - Çoğaltma İlkesi de dahil olmak üzere çoğaltmayı ayarlayın. VCenter sunucunuzu ekleyin ve VM 'Leri bulun.
> - VCenter sunucusu ekleme ve bulma
> - Çoğaltma günlüklerini veya verilerini tutacak depolama hesapları oluşturun ve VM 'Leri çoğaltın.
> - Yük devretme gerçekleştirin. Yük devretme ayarlarını yapılandırın, sanal makineleri çoğaltmak için bir ayar yapın.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce:

- [Senaryo mimarisini ve bileşenlerini ](vmware-azure-architecture.md) anladığınızdan emin olun.
- Tüm bileşenler için [destek gereksinimlerini](site-recovery-support-matrix-to-azure.md) gözden geçirin.
- Azure PowerShell `Az` modüle sahipsiniz. Azure PowerShell yüklemeniz veya yükseltmeniz gerekiyorsa, [Azure PowerShell yüklemek ve yapılandırmak için bu kılavuzu](/powershell/azure/install-az-ps)izleyin.

## <a name="log-into-azure"></a>Azure'da oturum açın

Connect-AzAccount cmdlet 'ini kullanarak Azure aboneliğinizde oturum açın:

```azurepowershell
Connect-AzAccount
```
VMware sanal makinelerinizi çoğaltmak istediğiniz Azure aboneliğini seçin. Erişiminiz olan Azure aboneliklerinin listesini almak için Get-AzSubscription cmdlet 'ini kullanın. Select-AzSubscription cmdlet 'ini kullanarak çalışmak için Azure aboneliğini seçin.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası ayarlama

1. Kurtarma Hizmetleri kasasının oluşturulacağı bir kaynak grubu oluşturun. Aşağıdaki örnekte, kaynak grubunun adı VMwareDRtoAzurePS ve Doğu Asya bölgesinde oluşturulur.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Bir kurtarma hizmetleri Kasası oluşturun. Aşağıdaki örnekte, kurtarma hizmetleri Kasası VMwareDRToAzurePs olarak adlandırılmıştır ve Doğu Asya bölgesinde ve önceki adımda oluşturulan kaynak grubunda oluşturulur.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ```

3. Kasa için kasa kayıt anahtarını indirin. Kasa kayıt anahtarı, şirket içi yapılandırma sunucusunu bu kasaya kaydetmek için kullanılır. Kayıt, yapılandırma sunucusu yazılım yükleme işleminin bir parçasıdır.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. İndirilen kasa kayıt anahtarını kullanın ve yapılandırma sunucusunun yüklemesini ve kaydını tamamlayın ve aşağıda verilen makalelerdeki adımları izleyin.
   - [Koruma hedeflerinizi seçin](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Kaynak ortamını ayarlama](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>Kasa bağlamını ayarla

Set-ASRVaultContext cmdlet 'ini kullanarak kasa bağlamını ayarlayın. Bir kez ayarlandıktan sonra, PowerShell oturumunda izleyen Azure Site Recovery işlemleri seçili kasa bağlamında gerçekleştirilir.

> [!TIP]
> Azure Site Recovery PowerShell modülü (az. RecoveryServices modülü) çoğu cmdlet için kullanımı kolay diğer adlarla birlikte gelir. Modüldeki cmdlet 'ler, *\<işlemi >-**Azrecoveryservicesasr**\<nesne >* ve *\<Işlem >-**ASR**\<nesne >* biçimini alan eşdeğer diğer adlara sahiptir. Kullanım kolaylığı için cmdlet diğer adlarını değiştirebilirsiniz.

Aşağıdaki örnekte, $vault değişkeninden gelen kasa ayrıntıları, PowerShell oturumunun kasa bağlamını belirtmek için kullanılır.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Set-ASRVaultContext cmdlet 'ine alternatif olarak, bir diğeri de kasa bağlamını ayarlamak için Import-AzRecoveryServicesAsrVaultSettingsFile cmdlet 'ini kullanabilir. Içeri aktarma-AzRecoveryServicesAsrVaultSettingsFile cmdlet 'inin-path parametresi olarak kasa kayıt anahtarı dosyasının bulunduğu yolu belirtin. Örneğin:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
Bu makalenin sonraki bölümlerinde Azure Site Recovery işlemler için kasa bağlamının ayarlandığı varsayılır.

## <a name="validate-vault-registration"></a>Kasa kaydını doğrula

Bu örnekte, şunlar vardır:

- Bu kasaya bir yapılandırma sunucusu (**configurationserver**) kaydedildi.
- *Configurationserver* 'a ek bir işlem sunucusu (**scaleout-processserver**) kaydedildi
- Hesap (**vCenter_account**, **WindowsAccount**, **linuxaccount**) yapılandırma sunucusunda ayarlanmıştır. Bu hesaplar, vCenter Server 'ı eklemek, sanal makineleri bulmaya ve çoğaltılacak Windows ve Linux sunucularına Mobility hizmeti yazılımını göndermek için kullanılır.

1. Kayıtlı yapılandırma sunucuları Site Recovery bir yapı nesnesi tarafından temsil edilir. Kasadaki yapı nesnelerinin listesini alın ve yapılandırma sunucusunu tanısayın.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. Makineleri çoğaltmak için kullanılabilecek işlem sunucularını belirler.

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   Yukarıdaki çıktıdan ***$ProcessServers [0]*** , *scaleout-processserver* 'a karşılık gelir ve ***$ProcessServers [1]*** , *configurationserver* 'daki işlem sunucusu rolüne karşılık gelir

3. Yapılandırma sunucusunda ayarlanan hesapları belirleyin.

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   Yukarıdaki çıkışta ***$AccountHandles [0]*** , hesap *vCenter_account*, ***[1]*** öğesini *WindowsAccount*hesabına $AccountHandles ve ***$AccountHandles [2]*** , *linuxaccount* hesabına karşılık geliyor

## <a name="create-a-replication-policy"></a>Çoğaltma ilkesi oluşturma

Bu adımda, iki çoğaltma ilkesi oluşturulur. VMware sanal makinelerini Azure 'a çoğaltmak için bir ilke ve diğer Azure 'da çalışan sanal makineleri şirket içi VMware sitesine geri çoğaltma.

> [!NOTE]
> Çoğu Azure Site Recovery işlem zaman uyumsuz olarak yürütülür. Bir işlemi başlattığınızda bir Azure Site Recovery işi gönderilir ve bir iş izleme nesnesi döndürülür. Bu iş izleme nesnesi, işlemin durumunu izlemek için kullanılabilir.

1. VMware sanal makinelerini Azure 'a çoğaltmak için belirtilen özelliklerle birlikte *replicationpolicy* adlı bir çoğaltma ilkesi oluşturun.

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){
           sleep 10;
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. Azure 'dan şirket içi VMware sitesine yeniden çalışma için kullanmak üzere bir çoğaltma ilkesi oluşturun.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   İşlemin tamamlanmasını izlemek için *$Job _FailbackPolicyCreate* iş ayrıntılarını kullanın.

   * Yapılandırma sunucusu ile çoğaltma ilkelerini eşlemek için bir koruma kapsayıcısı eşlemesi oluşturun.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>VCenter sunucusu ekleme ve VM 'Leri bulma

IP adresine veya ana bilgisayar adına göre vCenter Server ekleyin. **-Port** parametresi, bağlanılacak vCenter Server üzerindeki bağlantı noktasını belirtir, **-Name** parametresi vCenter sunucusu için kullanılacak kolay bir ad belirtir ve **-Account** parametresi, vCenter Server tarafından yönetilen sanal makineleri bulacak şekilde kullanılacak yapılandırma sunucusundaki hesap tanıtıcısını belirtir.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) {
        sleep 30;
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>Çoğaltma için depolama hesapları oluşturma

**Yönetilen diske yazmak için [PowerShell az. RecoveryServices Module 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) onsürümleri ' ni kullanın.** Yalnızca bir günlük depolama hesabı oluşturulması gerekir. Yalnızca geçici günlükleri depolamak için kullanıldığından standart hesap türü ve LRS artıklığı kullanılması önerilir. Depolama hesabının kasa ile aynı Azure bölgesinde oluşturulduğundan emin olun.

Az. RecoveryServices modülünün 2.0.0 'den eski bir sürümünü kullanıyorsanız, depolama hesapları oluşturmak için aşağıdaki adımları kullanın. Bu depolama hesapları daha sonra sanal makineleri çoğaltmak için kullanılır. Depolama hesaplarının kasada aynı Azure bölgesinde oluşturulduğundan emin olun. Çoğaltma için mevcut bir depolama hesabını kullanmayı planlıyorsanız bu adımı atlayabilirsiniz.

> [!NOTE]
> Şirket içi sanal makineleri Premium depolama hesabına çoğaltırken, ek bir standart depolama hesabı (günlük depolama hesabı) belirtmeniz gerekir. Günlük depolama hesabı, Günlükler Premium Depolama hedefine uygulanıncaya kadar bir ara mağaza olarak çoğaltma günlüklerini barındırır.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>VMware VM 'lerini çoğaltma

Sanal makinelerin vCenter Server 'dan bulunması yaklaşık 15-20 dakika sürer. Keşfedildiğinde, bulunan her bir sanal makine için Azure Site Recovery korunabilir bir öğe nesnesi oluşturulur. Bu adımda, bulunan sanal makinelerin üçü, önceki adımda oluşturulan Azure depolama hesaplarına çoğaltılır.

Bulunan bir sanal makineyi korumak için aşağıdaki ayrıntılara ihtiyacınız olacaktır:

* Çoğaltılacak korunabilir öğe.
* Sanal makineyi çoğaltmaya yönelik depolama hesabı (yalnızca depolama hesabına çoğaltma yapıyorsanız). 
* Bir günlük depolama alanı, sanal makineleri bir Premium depolama hesabına veya yönetilen bir diske karşı korumak için gereklidir.
* Çoğaltma için kullanılacak Işlem sunucusu. Kullanılabilir işlem sunucularının listesini alınan ve kaydedilen ***$ProcessServers [0]***  *(genişletme dosya)* ve ***$ProcessServers [1]*** *(ConfigurationServer)* değişkenleri.
* Mobility hizmeti yazılımını makinelere göndermek için kullanılacak hesap. Kullanılabilir hesapların listesi alındı ve ***$AccountHandles*** değişkeninde depolandı.
* Çoğaltma için kullanılacak çoğaltma ilkesi için koruma kapsayıcısı eşlemesi.
* Yük devretmede sanal makinelerin oluşturulması gereken kaynak grubu.
* İsteğe bağlı olarak, yük devredilen sanal makinenin bağlı olması gereken Azure sanal ağı ve alt ağı.

Şimdi bu tabloda belirtilen ayarları kullanarak aşağıdaki sanal makineleri çoğaltın


|Sanal makine  |İşlem sunucusu        |Depolama Hesabı              |Günlük depolama hesabı  |İlke           |Mobility hizmeti yüklemesi için hesap|Hedef kaynak grubu  | Hedef sanal ağ  |Hedef alt ağ  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer   |Yok| logstorageaccount1                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-VNET                 |Alt ağ-1       |
|Win2K12VM1       |Genişleme-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-VNET                 |Alt ağ-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| Yok                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-VNET                 |Alt ağ-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Çoğaltma işini etkinleştirme işlemi başarıyla tamamlandıktan sonra, sanal makineler için ilk çoğaltma başlatılır. İlk çoğaltma, çoğaltılacak veri miktarına ve çoğaltma için kullanılabilir bant genişliğine bağlı olarak biraz zaman alabilir. İlk çoğaltma tamamlandıktan sonra, sanal makine korumalı duruma gider. Sanal makine korunan bir duruma ulaştığında, sanal makine için yük devretme testi gerçekleştirebilir, bunu kurtarma planlarına ekleyebilirsiniz.

Get-Asrreplicationkorunabilir Dıdıtem cmdlet 'ini kullanarak sanal makinenin çoğaltma durumunu ve çoğaltma durumunu kontrol edebilirsiniz.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Yük devretme ayarlarını yapılandırma

Korumalı makineler için yük devretme ayarları, set-Asrreplicationkorunabilir Dıdıtem cmdlet 'i kullanılarak güncelleştirilemeyebilir. Bu cmdlet aracılığıyla güncelleştirilebilen ayarlardan bazıları şunlardır:
* Yük devretmede oluşturulacak sanal makinenin adı
* Yük devretmede oluşturulacak sanal makinenin VM boyutu
* Sanal makinenin NIC 'lerinin yük devretmeyle bağlantılı olması gereken Azure sanal ağı ve alt ağı
* Yönetilen disklere yük devretme
* Azure hibrit kullanım teklifi uygulama
* Yük devretmede sanal makineye atanacak hedef sanal ağdan statik bir IP adresi atayın.

Bu örnekte, sanal makine *Win2K12VM1* için yük devretmede oluşturulacak sanal makinenin VM boyutunu güncelleştiririz ve sanal makinenin yük devretmede yönetilen diskler kullanmasını belirtmeniz gerekir.

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>Yük devretme testi çalıştırma

1. Bir DR detayına (yük devretme testi) aşağıdaki gibi çalıştırın:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Yük devretme testi işi başarıyla tamamlandıktan sonra, *"-test"* (Bu durumda Win2K12VM1-test) ile bir sanal makinenin, adının Azure 'da oluşturulduğunu fark edeceksiniz.
3. Artık yük devretme testi sanal makinesine bağlanabilir ve test yük devretmesini doğrulayabilirsiniz.
4. Start-ASRTestFailoverCleanupJob cmdlet 'ini kullanarak yük devretme testini temizleyin. Bu işlem, test yük devretmesi işleminin bir parçası olarak oluşturulan sanal makineyi siler.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Azure 'a yük devretme

Bu adımda, Win2K12VM1 sanal makinesini belirli bir kurtarma noktasına devreder.

1. Yük devretme için kullanılacak kullanılabilir kurtarma noktalarının bir listesini alın:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. Başarılı bir şekilde yük devretmek için yük devretme işlemini gerçekleştirebilir ve Azure 'dan şirket içi VMware sitesine ters çoğaltma ayarlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
[Azure Site Recovery PowerShell başvurusunu](https://docs.microsoft.com/powershell/module/Az.RecoveryServices)kullanarak daha fazla görevi otomatikleştirmeyi öğrenin.
