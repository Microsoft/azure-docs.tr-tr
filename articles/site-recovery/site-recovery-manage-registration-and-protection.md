---
title: Sunucuları kaldırma ve korumayı devre dışı bırakma | Microsoft Docs
description: Bu makalede, Site Recovery kasasından sunucularının kaydını silme ve sanal makineler ve fiziksel sunucular için korumayı devre dışı bırakma açıklanır.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: rajanaki
ms.openlocfilehash: a411fc9a95bef595a8fc49cad77189bb88fb7661
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875793"
---
# <a name="remove-servers-and-disable-protection"></a>Sunucuları kaldırma ve korumayı devre dışı bırakma

Bu makalede, bir kurtarma hizmetleri kasasından sunucularının kaydını silme ve Site Recovery tarafından korunan makineler için korumanın devre dışı bırakılması açıklanmaktadır.


## <a name="unregister-a--configuration-server"></a>Yapılandırma sunucusunun kaydını silme

VMware VM 'lerini veya Windows/Linux fiziksel sunucularını Azure 'a çoğaltdıysanız, bağlı olmayan bir yapılandırma sunucusunun kaydını bir kasadan aşağıdaki gibi silebilirsiniz:

1. [Sanal makinelerin korumasını devre dışı bırakın](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. Çoğaltma ilkelerinin [Ilişkisini kaldırın veya silin](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) .
3. [Yapılandırma sunucusunu Sil](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>VMM sunucusunun kaydını silme

1. Kaldırmak istediğiniz VMM sunucusundaki bulutlardaki sanal makinelerin çoğaltılmasını durdurun.
2. Silmek istediğiniz VMM sunucusunda bulutlar tarafından kullanılan tüm ağ eşlemelerini silin. **System Center VMM** >   Ağ > **eşlemesi**için Site Recovery altyapısında, ağ eşleme > Sil ' e sağ tıklayın.
3. VMM sunucusunun KIMLIĞINI aklınızda edin.
4. Kaldırmak istediğiniz VMM sunucusundaki bulutlardan çoğaltma ilkelerinin ilişkisini kaldırın.  **System Center VMM** >  çoğaltmailkeleriiçinSiteRecoveryaltyapısında,ilişkili >  ilkeye çift tıklayın. Buluta sağ tıklayın > **Ilişkiyi kaldırın**.
5. VMM sunucusunu veya etkin düğümü silin. **System Center VMM** >   VMM > **sunucuları**için Site Recovery altyapısı ' nda, Sil > sunucuya sağ tıklayın.
6. VMM sunucunuz bağlantısı kesik durumdaysa, [Temizleme BETIĞINI](https://aka.ms/asr-cleanup-script-vmm) VMM sunucusunda indirip çalıştırın. Varsayılan (LocalMachine) kapsamının Yürütme ilkesini değiştirmek için PowerShell 'i **yönetici olarak çalıştır** seçeneğiyle açın. Komut dosyasında, kaldırmak istediğiniz VMM sunucusunun KIMLIĞINI belirtin. Betik kaydı ve bulut eşleştirme bilgilerini sunucudan kaldırır.
5. Temizleme betiğini herhangi bir ikincil VMM sunucusunda çalıştırın.
6. Temizleme betiğini, sağlayıcının yüklü olduğu diğer tüm pasif VMM küme düğümlerinde çalıştırın.
7. Sağlayıcıyı VMM sunucusunda el ile kaldırın. Bir kümeniz varsa, tüm düğümlerden kaldırın.
8. Sanal makineleriniz Azure 'a çoğaltıyorsanız, Microsoft Kurtarma Hizmetleri aracısını silinen bulutların Hyper-V konaklarından kaldırmanız gerekir.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Hyper-V sitesinde bir Hyper-V konağının kaydını silme

VMM tarafından yönetilmeyen Hyper-V konakları bir Hyper-V sitesine toplanır. Hyper-V sitesindeki bir konağı aşağıdaki gibi kaldırın:

1. Konakta bulunan Hyper-V VM 'lerinin çoğaltmasını devre dışı bırakın.
2. Hyper-V sitesi için ilkelerin ilişkisini kaldırın.  > **Hyper-V siteleri** >  **çoğaltma ilkeleri**için **Site Recovery altyapısında**, ilişkili ilkeye çift tıklayın. **Ilişkiyi kaldırmak**> siteye sağ tıklayın.
3. Hyper-V konaklarınızı silin.  > **Hyper-v siteleri**   > **Hyper-v Konakları**için Site Recovery altyapısında, Sil > sunucuya sağ tıklayın.
4. Hyper-V sitesini, tüm Konakları kaldırıldıktan sonra silin.  > **Hyper-v siteleri**   > **Hyper-v siteleri**için Site Recovery altyapısında, Sil > siteye sağ tıklayın.
5. Hyper-V konağınız **bağlantısı kesik** durumdaysa, kaldırdığınız her Hyper-v konağında aşağıdaki betiği çalıştırın. Betik, sunucudaki ayarları temizler ve kasanın kaydını siler.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>VMware VM veya fiziksel sunucu için korumayı devre dışı bırakma (VMware 'den Azure 'a)

1. **Korunan öğeler** > **çoğaltılan öğeler**bölümünde, **çoğaltmayı devre dışı bırakmak**> makineye sağ tıklayın.
2. **Çoğaltmayı devre dışı bırak** sayfasında, şu seçeneklerden birini seçin:
    - **Çoğaltmayı devre dışı bırak ve Kaldır (önerilir)** -Bu seçenek, çoğaltılan öğeyi Azure Site Recovery kaldırır ve makinenin çoğaltması durdurulur. Yapılandırma sunucusundaki çoğaltma yapılandırması temizlenir ve bu korumalı sunucu için faturalandırma Site Recovery durdurulur. Bu seçeneğin yalnızca yapılandırma sunucusu bağlı durumdayken kullanılabileceğini unutmayın.
    - **Remove** -bu seçeneğin yalnızca kaynak ortam silinirse veya erişilebilir değilse (bağlı değil) kullanılması gerekir. Bu, çoğaltılan öğeyi Azure Site Recovery kaldırır (Faturalandırma durdurulur). Yapılandırma **sunucusundaki çoğaltma** yapılandırması temizlenmeyecektir. 

> [!NOTE]
> Her iki seçenek de Mobility hizmeti korunan sunuculardan kaldırılmayacak, el ile kaldırmanız gerekir. Sunucuyu aynı yapılandırma sunucusunu kullanarak yeniden korumayı planlıyorsanız Mobility hizmetini kaldırmayı atlayabilirsiniz.

> [!NOTE]
> Zaten bir VM yük devretiyorsa ve Azure 'da çalışıyorsa, korumayı devre dışı bırakma işlemi yük devredilen VM 'nin kaldırılmasını/etkilenmeyeceğini unutmayın.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Azure VM için korumayı devre dışı bırakma (Azure 'dan Azure 'a)

-  **Korunan öğeler** > **çoğaltılan öğeler**bölümünde, **çoğaltmayı devre dışı bırakmak**> makineye sağ tıklayın.
> [!NOTE]
> Mobility hizmeti korumalı sunuculardan kaldırılmaz, el ile kaldırmanız gerekir. Sunucuyu yeniden korumayı planlıyorsanız Mobility hizmetini kaldırmayı atlayabilirsiniz.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Hyper-V sanal makinesi için korumayı devre dışı bırakma (Hyper-V, Azure 'a)

> [!NOTE]
> Hyper-V VM 'lerini VMM sunucusu olmadan Azure 'a çoğaltırken bu yordamı kullanın. **System Center VMM 'Den Azure 'a** sanal makinelerinizi çoğaltmanız durumunda, System Center VMM 'den Azure 'a çoğaltma senaryosunu kullanarak Hyper-V sanal makinesi Için korumayı devre dışı bırakma yönergelerini izleyin.

1. **Korunan öğeler** > **çoğaltılan öğeler**bölümünde, **çoğaltmayı devre dışı bırakmak**> makineye sağ tıklayın.
2. **Çoğaltmayı devre dışı bırak**' da, aşağıdaki seçenekleri belirleyebilirsiniz:
   - **Çoğaltmayı devre dışı bırak ve Kaldır (önerilir)** -Bu seçenek, çoğaltılan öğeyi Azure Site Recovery kaldırır ve makinenin çoğaltması durdurulur. Şirket içi sanal makinedeki çoğaltma yapılandırması temizlenir ve bu korumalı sunucu için faturalandırma Site Recovery durdurulur.
   - **Remove** -bu seçeneğin yalnızca kaynak ortam silinirse veya erişilebilir değilse (bağlı değil) kullanılması gerekir. Bu, çoğaltılan öğeyi Azure Site Recovery kaldırır (Faturalandırma durdurulur). Şirket içi **sanal makinede çoğaltma** yapılandırması temizlenmeyecektir. 

 > [!NOTE]
     > **Kaldır** seçeneğini belirlediyseniz, şirket içi Hyper-V Server çoğaltma ayarlarını temizlemek için aşağıdaki komut dosyası kümesini çalıştırın.

> [!NOTE]
> Zaten bir VM yük devretiyorsa ve Azure 'da çalışıyorsa, korumayı devre dışı bırakma işlemi yük devredilen VM 'nin kaldırılmasını/etkilenmeyeceğini unutmayın.

1. Kaynak Hyper-V ana bilgisayar sunucusunda, sanal makine çoğaltmasını kaldırmak için. SQLVM1 değerini sanal makinenizin adıyla değiştirin ve betiği bir yönetim PowerShell 'den çalıştırın

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>System Center VMM 'den Azure 'a çoğaltma için bir Hyper-V sanal makinesi için korumayı devre dışı bırakma

1. **Korunan öğeler** > **çoğaltılan öğeler**bölümünde, **çoğaltmayı devre dışı bırakmak**> makineye sağ tıklayın.
2. **Çoğaltmayı devre dışı bırak**' da şu seçeneklerden birini seçin:

   - **Çoğaltmayı devre dışı bırak ve Kaldır (önerilir)** -Bu seçenek, çoğaltılan öğeyi Azure Site Recovery kaldırır ve makinenin çoğaltması durdurulur. Şirket içi sanal makinede çoğaltma yapılandırması temizlenir ve bu korumalı sunucu için faturalandırma Site Recovery durdurulur.
   - **Remove** -bu seçeneğin yalnızca kaynak ortam silinirse veya erişilebilir değilse (bağlı değil) kullanılması gerekir. Bu, çoğaltılan öğeyi Azure Site Recovery kaldırır (Faturalandırma durdurulur). Şirket içi **sanal makinede çoğaltma** yapılandırması temizlenmeyecektir. 

     > [!NOTE]
     > Kaldır seçeneğini belirlediyseniz, şirket içi VMM sunucusu çoğaltma ayarlarını temizlemek için aşağıdaki komut dosyalarını tun altına **çıkarın** .
3. Bu betiği, VMM konsolundan PowerShell (yönetici ayrıcalıkları gerekir) kullanarak kaynak VMM sunucusunda çalıştırın. **SQLVM1** yer tutucusunu sanal makinenizin adıyla değiştirin.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Yukarıdaki adımlar VMM sunucusundaki çoğaltma ayarlarını temizler. Hyper-V konak sunucusunda çalışan sanal makine için çoğaltmayı durdurmak üzere bu betiği çalıştırın. SQLVM1 değerini sanal makinenizin adı ve host01.contoso.com ile birlikte Hyper-V ana bilgisayar sunucusunun adını ile değiştirin.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>System Center VMM 'den VMM 'ye kullanarak ikincil VMM sunucusuna çoğaltılan bir Hyper-V sanal makinesi için korumayı devre dışı bırakma

1. **Korunan öğeler** > **çoğaltılan öğeler**bölümünde, **çoğaltmayı devre dışı bırakmak**> makineye sağ tıklayın.
2. **Çoğaltmayı devre dışı bırak**' da şu seçeneklerden birini seçin:

   - **Çoğaltmayı devre dışı bırak ve Kaldır (önerilir)** -Bu seçenek, çoğaltılan öğeyi Azure Site Recovery kaldırır ve makinenin çoğaltması durdurulur. Şirket içi sanal makinede çoğaltma yapılandırması temizlenir ve bu korumalı sunucu için faturalandırma Site Recovery durdurulur.
   - **Remove** -bu seçeneğin yalnızca kaynak ortam silinirse veya erişilebilir değilse (bağlı değil) kullanılması gerekir. Bu, çoğaltılan öğeyi Azure Site Recovery kaldırır (Faturalandırma durdurulur). Şirket içi **sanal makinede çoğaltma** yapılandırması temizlenmeyecektir. Şirket içi sanal makineleri çoğaltma ayarlarını temizlemek için aşağıdaki komut dosyası kümesini çalıştırın.
     > [!NOTE]
     > Kaldır seçeneğini belirlediyseniz, şirket içi VMM sunucusu çoğaltma ayarlarını temizlemek için aşağıdaki komut dosyalarını tun altına **çıkarın** .

3. Bu betiği, VMM konsolundan PowerShell (yönetici ayrıcalıkları gerekir) kullanarak kaynak VMM sunucusunda çalıştırın. **SQLVM1** yer tutucusunu sanal makinenizin adıyla değiştirin.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. İkincil VMM sunucusunda, ikincil sanal makinenin ayarlarını temizlemek için şu betiği çalıştırın:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. İkincil VMM sunucusunda, Hyper-V ana bilgisayar sunucusundaki sanal makineleri yenileyerek VMM konsolunda ikincil VM 'nin yeniden algılanmasını sağlayın.
6. Yukarıdaki adımlar VMM sunucusundaki çoğaltma ayarlarını temizler. Sanal makine için çoğaltmayı durdurmak istiyorsanız, birincil ve ikincil VM 'Ler için aşağıdaki betiği çalıştırın. SQLVM1 değerini sanal makinenizin adıyla değiştirin.

        Remove-VMReplication –VMName “SQLVM1”




