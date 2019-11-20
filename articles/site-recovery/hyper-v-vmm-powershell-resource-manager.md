---
title: Azure Site Recovery/PowerShell ile Hyper-V (VMM ile) ile ikincil bir siteye olağanüstü durum kurtarmayı ayarlama
description: VMM bulutlarındaki Hyper-V VM 'lerinin Azure Site Recovery ve PowerShell kullanarak ikincil bir VMM sitesine olağanüstü durum kurtarma işlemini nasıl ayarlayabileceğinizi açıklar.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 2fc66514bdf33611f9e6266d35a2d537fe3b9261
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084900"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>PowerShell kullanarak Hyper-V VM 'lerini ikincil bir siteye olağanüstü durum kurtarmayı ayarlama (Kaynak Yöneticisi)

Bu makalede, System Center Virtual Machine Manager bulutlarındaki Hyper-V VM 'lerinin [Azure Site Recovery](site-recovery-overview.md)kullanarak ikincil şirket içi sitede bir Virtual Machine Manager buluta çoğaltılmasına yönelik adımların nasıl otomatikleştirdiği gösterilmektedir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Ön koşullar

- [Senaryo mimarisini ve bileşenlerini](hyper-v-vmm-architecture.md) gözden geçirin.
- Tüm bileşenler için [destek gereksinimlerini](site-recovery-support-matrix-to-sec-site.md) gözden geçirin.
- Virtual Machine Manager sunucularının ve Hyper-V konaklarının [destek gereksinimleriyle](site-recovery-support-matrix-to-sec-site.md)uyumlu olduğundan emin olun.
- Çoğaltmak istediğiniz VM 'Lerin [çoğaltılan makine desteğiyle](site-recovery-support-matrix-to-sec-site.md)uyumlu olduğundan emin olun.


## <a name="prepare-for-network-mapping"></a>Ağ eşlemesi için hazırlanma

Kaynak ve hedef bulutlarda şirket içi Virtual Machine Manager VM ağları arasında [ağ eşleme](hyper-v-vmm-network-mapping.md) haritaları. Eşleme sürecinde şu işlemler gerçekleştirilir:

- VM'ler yük devretme sonrasında uygun hedef VM ağlara bağlanır. 
- Çoğaltma VM'lerini en uygun şekilde hedef Hyper-V konağı sunucularına yerleştirir. 
- Ağ eşlemesini yapılandırmazsanız, yük devretme işleminden sonra çoğaltma VM 'Leri VM ağına bağlanmaz.

Virtual Machine Manager aşağıdaki şekilde hazırlayın:

* Kaynak ve hedef Virtual Machine Manager sunucularında [Virtual Machine Manager Mantıksal ağlarınız](https://docs.microsoft.com/system-center/vmm/network-logical) olduğundan emin olun:

    - Kaynak sunucusundaki mantıksal ağ, Hyper-V konaklarının bulunduğu kaynak bulutla ilişkilendirilmelidir.
    - Hedef sunucudaki mantıksal ağ, hedef bulutla ilişkilendirilmelidir.
* Kaynak ve hedef Virtual Machine Manager sunucularında [VM ağlarının](https://docs.microsoft.com/system-center/vmm/network-virtual) bulunduğundan emin olun. VM ağları, her bir konumdaki mantıksal ağa bağlanmış olmalıdır.
* Kaynak Hyper-V konaklarındaki VM'leri kaynak VM ağına bağlayın. 

## <a name="prepare-for-powershell"></a>PowerShell için hazırlanma

Başlamaya Azure PowerShell olduğunuzdan emin olun:

- Zaten PowerShell kullanıyorsanız, sürüm 0.8.10 veya sonraki bir sürüme yükseltin. PowerShell 'i ayarlama hakkında [daha fazla bilgi edinin](/powershell/azureps-cmdlets-docs) .
- PowerShell 'i ayarladıktan ve yapılandırdıktan sonra, [hizmet cmdlet 'lerini](/powershell/azure/overview)gözden geçirin.
- PowerShell 'de parametre değerlerini, girişleri ve çıkışları kullanma hakkında daha fazla bilgi edinmek için [kullanmaya başlama](/powershell/azure/get-started-azureps) kılavuzunu okuyun.

## <a name="set-up-a-subscription"></a>Abonelik ayarlama
1. PowerShell 'den Azure hesabınızda oturum açın.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzAccount #-Credential $Cred
2. Abonelik kimlikleri ile aboneliklerinizin bir listesini alın. Kurtarma Hizmetleri kasasını oluşturmak istediğiniz aboneliğin KIMLIĞINI aklınızda bulabilirsiniz. 

        Get-AzSubscription
3. Kasa için abonelik ayarlayın.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma
1. Yoksa bir Azure Resource Manager kaynak grubu oluşturun.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Yeni bir kurtarma hizmetleri Kasası oluşturun. Kasa nesnesini daha sonra kullanılacak bir değişkene kaydedin. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    Get-Azrecoveryserviceskasa cmdlet 'ini kullanarak oluşturduktan sonra kasa nesnesini elde edebilirsiniz.

## <a name="set-the-vault-context"></a>Kasa bağlamını ayarla
1. Mevcut bir kasayı alın.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Kasa bağlamını ayarlayın.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Site Recovery sağlayıcıyı yükler
1. Virtual Machine Manager makinede, aşağıdaki komutu çalıştırarak bir dizin oluşturun:

       New-Item c:\ASR -type directory
2. İndirilen sağlayıcı kurulum dosyasını kullanarak dosyaları ayıklayın.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Sağlayıcıyı yükledikten sonra yükleme işleminin bitmesini bekleyin.

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. Sunucuyu kasaya kaydedin.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Çoğaltma ilkesi oluşturma ve ilişkilendirme
1. Bu durumda Hyper-V 2012 R2 için şu şekilde bir çoğaltma ilkesi oluşturun:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > Virtual Machine Manager bulutu, farklı Windows Server sürümlerini çalıştıran Hyper-V konakları içerebilir, ancak çoğaltma ilkesi işletim sisteminin belirli bir sürümü içindir. Farklı işletim sistemlerinde çalışan farklı konaklarınız varsa, her bir sistem için ayrı çoğaltma ilkeleri oluşturun. Örneğin, Windows Server 2012 üzerinde çalışan beş ana bilgisayar ve Windows Server 2012 R2 üzerinde çalışan üç ana bilgisayar varsa, iki çoğaltma ilkesi oluşturun. Her işletim sistemi türü için bir tane oluşturabilirsiniz.

2. Birincil koruma kapsayıcısını (birincil Virtual Machine Manager bulutu) ve kurtarma koruma kapsayıcısını (kurtarma Virtual Machine Manager bulutu) alın.

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Kolay adı kullanarak oluşturduğunuz çoğaltma ilkesini alın.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Çoğaltma ilkesiyle koruma kapsayıcısının (Virtual Machine Manager bulutu) ilişkilendirmesini başlatın.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. İlke ilişkilendirme işinin bitmesini bekleyin. İşin tamamlanıp bitmediğini denetlemek için aşağıdaki PowerShell kod parçacığını kullanın:

       $job = Get-AzSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. İşi işlemeyi tamamladıktan sonra aşağıdaki komutu çalıştırın:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

İşlemin tamamlandığını denetlemek için, [izleme etkinliğindeki](#monitor-activity)adımları izleyin.

##  <a name="configure-network-mapping"></a>Ağ eşlemesini yapılandırma
1. Geçerli kasadaki sunucuları almak için bu komutu kullanın. Komut, Site Recovery sunucularını $Servers dizi değişkeninde depolar.

        $Servers = Get-AzSiteRecoveryServer
2. Kaynak Virtual Machine Manager sunucusu ve hedef Virtual Machine Manager sunucusu için ağları almak üzere bu komutu çalıştırın.

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Kaynak Virtual Machine Manager sunucusu, sunucu dizisindeki birinci veya ikinci bir dizin olabilir. Virtual Machine Manager Server adlarını denetleyin ve ağları uygun şekilde alın.


3. Bu cmdlet, birincil ağ ile kurtarma ağı arasında bir eşleme oluşturur. Birincil ağı $PrimaryNetworks ilk öğesi olarak belirtir. Kurtarma ağını $RecoveryNetworks ilk öğesi olarak belirtir.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>VM 'Ler için korumayı etkinleştirme
Sunucular, bulutlar ve ağlar doğru yapılandırıldıktan sonra buluttaki VM 'Ler için korumayı etkinleştirin.

1. Korumayı etkinleştirmek için, koruma kapsayıcısını almak üzere aşağıdaki komutu çalıştırın:

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Koruma varlığını (VM) aşağıdaki gibi alın:

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. VM için çoğaltmayı etkinleştirin.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Yük devretme testi çalıştırma

Dağıtımınızı test etmek için tek bir sanal makine için yük devretme testi çalıştırın. Ayrıca, birden çok VM içeren ve plan için yük devretme testi çalıştıran bir kurtarma planı da oluşturabilirsiniz. Yük devretme testi, yük devretme işleminizi ve kurtarma mekanizmanızı yalıtılmış bir ortamda benzetimli olarak gerçekleştirir.

1. VM 'Lerin yük devredebileceği VM 'yi alın.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Yük devretme testi gerçekleştirin.

   Tek bir VM için:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   Kurtarma planı için:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

İşlemin tamamlandığını denetlemek için, [izleme etkinliğindeki](#monitor-activity)adımları izleyin.

## <a name="run-planned-and-unplanned-failovers"></a>Planlı ve planlanmamış yük devretme Çalıştır

1. Planlanmış bir yük devretme gerçekleştirin.

   Tek bir VM için:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Kurtarma planı için:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Planlanmamış bir yük devretme gerçekleştirin.

   Tek bir VM için:
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Kurtarma planı için:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Etkinliği izle
Yük devretme etkinliğini izlemek için aşağıdaki komutları kullanın. İşlemler arasında işlemenin bitmesini bekleyin.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Sonraki adımlar

Kaynak Yöneticisi PowerShell cmdlet 'leriyle Site Recovery hakkında [daha fazla bilgi edinin](/powershell/module/az.recoveryservices) .
