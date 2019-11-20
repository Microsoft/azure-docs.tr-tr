---
title: Azure Site Recovery ve PowerShell kullanarak Hyper-V VM olağanüstü durum kurtarma
description: PowerShell ve Azure Resource Manager kullanarak Hyper-V VM 'lerinin olağanüstü durum kurtarma işlemini Azure Site Recovery hizmetiyle Azure 'a otomatikleştirin.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 06/18/2019
ms.author: sutalasi
ms.openlocfilehash: 73f5f64a64ab28cdb4b57d0904911f62c2020cf0
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082689"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>PowerShell ve Azure Resource Manager kullanarak Hyper-V VM 'Leri için Azure 'da olağanüstü durum kurtarmayı ayarlama

[Azure Site Recovery](site-recovery-overview.md) , Azure sanal makinelerini (VM) ve şirket Içi VM 'leri ve fiziksel sunucuları çoğaltma, yük devretme ve kurtarma işlemlerini düzenleyerek iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejinize katkıda bulunur.

Bu makalede, Hyper-V VM 'lerini Azure 'a çoğaltmak için Azure Resource Manager ile birlikte Windows PowerShell 'in nasıl kullanılacağı açıklanır. Bu makalede kullanılan örnek, Hyper-V ana bilgisayarında çalışan tek bir VM 'yi Azure 'a nasıl çoğaltacağınızı gösterir.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell, Windows PowerShell kullanarak Azure 'ı yönetmek için cmdlet 'ler sağlar. Azure Resource Manager için Azure PowerShell kullanılabilen PowerShell cmdlet 'leri Site Recovery Azure 'da sunucularınızı korumanıza ve kurtarmanıza yardımcı olur.

Bu makaleyi kullanmak için PowerShell uzmanı olmanız gerekmez, ancak modüller, cmdlet 'ler ve oturumlar gibi temel kavramları anlamanız gerekir. [Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)'i kullanmaya başlama ve [Azure Resource Manager Azure PowerShell kullanma](../powershell-azure-resource-manager.md).

> [!NOTE]
> Bulut çözümü sağlayıcısı (CSP) programındaki Microsoft iş ortakları, müşteri sunucularının korumasını kendi CSP aboneliklerine (kiracı abonelikleri) yapılandırabilir ve yönetebilir.
>
>

## <a name="before-you-start"></a>Başlamadan önce
Bu önkoşulların yerinde olduğundan emin olun:

* [Microsoft Azure](https://azure.microsoft.com/) hesabı. [Ücretsiz deneme sürümüyle](https://azure.microsoft.com/pricing/free-trial/) başlayabilirsiniz. Ayrıca, [Azure Site Recovery Manager fiyatlandırması](https://azure.microsoft.com/pricing/details/site-recovery/)hakkında bilgi edinebilirsiniz.
* Azure PowerShell. Bu sürüm ve nasıl yükleneceğine ilişkin bilgiler için bkz. [ınstall Azure PowerShell](/powershell/azure/install-az-ps).

Ayrıca, bu makalede açıklanan belirli örnek aşağıdaki önkoşullara sahiptir:

* Bir veya daha fazla VM içeren Windows Server 2012 R2 veya Microsoft Hyper-V Server 2012 R2 çalıştıran Hyper-V konağı. Hyper-V sunucuları doğrudan veya bir ara sunucu üzerinden Internet 'e bağlanmalıdır.
* Çoğaltmak istediğiniz VM 'Ler [Bu önkoşullara](hyper-v-azure-support-matrix.md#replicated-vms)uymalıdır.

## <a name="step-1-sign-in-to-your-azure-account"></a>1\. Adım: Azure hesabınızda oturum açın

1. Bir PowerShell konsolu açın ve Azure hesabınızda oturum açmak için bu komutu çalıştırın. Cmdlet 'i bir Web sayfası getirir hesap kimlik bilgilerinizi ister: **Connect-AzAccount**.
    - Alternatif olarak, **-Credential** parametresini kullanarak, hesap kimlik bilgilerinizi **Connect-azaccount** cmdlet 'ine bir parametre olarak dahil edebilirsiniz.
    - CSP iş ortağı olarak bir kiracı adına çalışıyorsanız, istemci Tenantıd veya kiracı birincil etki alanı adını kullanarak müşteriyi kiracı olarak belirtin. Örneğin: **Connect-AzAccount-Tenant "fabrikam.com"**
2. Hesap birden çok aboneliğe sahip olduğundan, hesapla birlikte kullanmak istediğiniz aboneliği ilişkilendirin:

    `Select-AzSubscription -SubscriptionName $SubscriptionName`

3. Aşağıdaki komutları kullanarak, aboneliğinizin kurtarma hizmetleri ve Site Recovery için Azure sağlayıcılarını kullanmak üzere kaydedildiğini doğrulayın:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

4. Komut çıkışında, **Registrationstate** ' in **kayıtlı**olarak ayarlandığını doğrulayın. Adım 2 ' ye geçebilirsiniz. Aksi takdirde, aşağıdaki komutları çalıştırarak, eksik sağlayıcıyı aboneliğinize kaydetmeniz gerekir:

    `Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Aşağıdaki komutları kullanarak sağlayıcıların başarıyla kaydedildiğini doğrulayın:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

## <a name="step-2-set-up-the-vault"></a>2\. Adım: kasayı ayarlama

1. Kasanın oluşturulacağı bir Azure Resource Manager kaynak grubu oluşturun veya var olan bir kaynak grubunu kullanın. Yeni bir kaynak grubunu aşağıda gösterildiği gibi oluşturun. $ResourceGroupName değişkeni, oluşturmak istediğiniz kaynak grubunun adını içerir ve $Geo değişkeni, kaynak grubunun oluşturulacağı Azure bölgesini içerir (örneğin, "Brezilya Güney").

    `New-AzResourceGroup -Name $ResourceGroupName -Location $Geo`

2. Aboneliğinizdeki kaynak gruplarının bir listesini almak için **Get-AzResourceGroup** cmdlet 'ini çalıştırın.
2. Yeni bir Azure kurtarma hizmetleri Kasası oluşturmak için aşağıdaki adımları izleyin:

        $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    **Get-Azrecoveryserviceskasa** cmdlet 'ini kullanarak mevcut kasaların bir listesini alabilirsiniz.


## <a name="step-3-set-the-recovery-services-vault-context"></a>3\. Adım: kurtarma hizmetleri Kasası bağlamını ayarlama

Kasa bağlamını aşağıdaki şekilde ayarlayın:

`Set-AsrVaultSettings -Vault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>4\. Adım: Hyper-V sitesi oluşturma

1. Yeni bir Hyper-V sitesini şu şekilde oluşturun:

        $sitename = "MySite"                #Specify site friendly name
        New-AsrFabric -Type HyperVSite -Name $sitename

2. Bu cmdlet, siteyi oluşturmak için bir Site Recovery işi başlatır ve bir Site Recovery iş nesnesi döndürür. İşin tamamlanmasını bekleyin ve işin başarıyla tamamlandığını doğrulayın.
3. İş nesnesini almak ve işin geçerli durumunu kontrol etmek için **Get-AsrJob cmdlet 'ini**kullanın.
4. Site için bir kayıt anahtarı oluşturun ve şu şekilde indirin:

    ```
    $SiteIdentifier = Get-AsrFabric -Name $sitename | Select -ExpandProperty SiteIdentifier
    $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
    ```

5. İndirilen anahtarı Hyper-V konağına kopyalayın. Hyper-V konağını siteye kaydetmek için anahtar gereklidir.

## <a name="step-5-install-the-provider-and-agent"></a>5\. Adım: sağlayıcıyı ve aracıyı yükler

1. [Microsoft](https://aka.ms/downloaddra)'tan sağlayıcının en son sürümüne yönelik yükleyiciyi indirin.
2. Yükleyiciyi Hyper-V konağında çalıştırın.
3. Yüklemenin sonunda kayıt adımına devam edin.
4. İstendiğinde, indirilen anahtarı sağlayın ve Hyper-V konağının kaydını yapın.
5. Hyper-V konağının siteye şu şekilde kaydedildiğini doğrulayın:

        $server =  Get-AsrFabric -Name $siteName | Get-AsrServicesProvider -FriendlyName $server-friendlyname

Hyper-V çekirdek sunucusu çalıştırıyorsanız, kurulum dosyasını indirin ve şu adımları izleyin:
1. Şu komutu çalıştırarak dosyaları AzureSiteRecoveryProvider. exe ' den yerel bir dizine ayıklayın: ```AzureSiteRecoveryProvider.exe /x:. /q```
2. Çalıştırma ```.\setupdr.exe /i``` sonuçları%ProgramData%\asrlogs\drasetupwizard.log günlüğüne kaydedilir.

3. Şu komutu çalıştırarak sunucuyu kaydedin:

    ```cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"```


## <a name="step-6-create-a-replication-policy"></a>6\. Adım: çoğaltma ilkesi oluşturma

Başlamadan önce, belirtilen depolama hesabının kasa ile aynı Azure bölgesinde olması gerektiğini ve coğrafi çoğaltmanın etkin olması gerektiğini unutmayın.

1. Çoğaltma ilkesini aşağıdaki gibi oluşturun:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Çoğaltma ilkesi oluşturma işleminin başarılı olduğundan emin olmak için döndürülen işi denetleyin.

3. Sitesine karşılık gelen koruma kapsayıcısını aşağıdaki gibi alın:

        $protectionContainer = Get-AsrProtectionContainer
3. Koruma kapsayıcısını şu şekilde çoğaltma ilkesiyle ilişkilendirin:

        $Policy = Get-AsrPolicy -FriendlyName $PolicyName
        $associationJob  = New-AsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
4. İlişkilendirme işinin başarıyla tamamlanmasını bekleyin.

5. Koruma kapsayıcısı eşlemesini alın.

        $ProtectionContainerMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $protectionContainer

## <a name="step-7-enable-vm-protection"></a>7\. Adım: VM korumasını etkinleştirme

1. Korumak istediğiniz VM 'ye karşılık gelen korunabilir öğeyi aşağıdaki gibi alın:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $ProtectableItem = Get-AsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. VM 'yi koruyun. Koruduğunuz sanal makineye birden fazla disk ekli ise, *Osdiskname* parametresini kullanarak işletim sistemi diskini belirtin.

        $OSType = "Windows"                                 # "Windows" or "Linux"
        $DRjob = New-AsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID

3. İlk çoğaltmadan sonra VM 'Lerin korumalı duruma ulaşmasını bekleyin. Bu işlem, çoğaltılacak veri miktarı ve Azure için kullanılabilir yukarı akış bant genişliği gibi etkenlere bağlı olarak biraz zaman alabilir. Korunan bir durum olduğunda, iş durumu ve StateDescription aşağıdaki gibi güncelleştirilir:

        PS C:\> $DRjob = Get-AsrJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Yük devretmeden sonra VM NIC 'in ekleneceği kurtarma özelliklerini (VM rolü boyutu,) ve Azure ağı güncelleştirme.

        PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AsrReplicationProtectedItem --InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AsrJob -Job $UpdateJob

        PS C:\> $UpdateJob| select -ExpandProperty state
        Get-AsrJob -Job $job | select -ExpandProperty state

        Succeeded



## <a name="step-8-run-a-test-failover"></a>8\. Adım: yük devretme testi çalıştırma
1. Yük devretme testini aşağıdaki şekilde çalıştırın:

        $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        $TFjob =Start-AsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Test VM 'sinin Azure 'da oluşturulduğunu doğrulayın. Test yük devretmesi işi, Azure 'da test VM oluşturulduktan sonra askıya alındı.
3. Yük devretme testini temizlemek ve gerçekleştirmek için şunu çalıştırın:

        $TFjob = Start-AsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"

## <a name="next-steps"></a>Sonraki adımlar
Azure Resource Manager PowerShell cmdlet 'leriyle Azure Site Recovery hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/powershell/module/az.recoveryservices) .
