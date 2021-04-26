---
title: VPN ağ geçitleri sorunlarını giderme ve izleme-Azure Otomasyonu
titleSuffix: Azure Network Watcher
description: Bu makalede, Azure Otomasyonu ve ağ Izleyicisi ile şirket içi bağlantıyı tanılama açıklanmaktadır
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: af671996722524de9af1a90ae8dfde27f814c8c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011821"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Ağ Izleyicisi sorunlarını giderme ile VPN ağ geçitlerini izleme

Müşterilere güvenilir hizmetler sağlamak için ağ performansından derin Öngörüler elde etmek önemlidir. Bu nedenle, ağ kesintisi koşullarını hızlı bir şekilde algılamak ve kesinti koşulunu azaltmak için düzeltici eylemler gerçekleştirmek önemlidir. Azure Otomasyonu, runbook 'ları kullanarak bir görevi programlı bir şekilde uygulamanıza ve çalıştırmanıza olanak sağlar. Azure Otomasyonu 'nu kullanarak sürekli ve proaktif ağ izleme ve uyarı gerçekleştirmeye yönelik kusursuz bir tarif oluşturulur.

## <a name="scenario"></a>Senaryo

Aşağıdaki görüntüde yer alan senaryo, şirket içi bağlantı VPN Gateway ve tünel kullanılarak kurulabilen çok katmanlı bir uygulamadır. VPN Gateway çalıştığından ve çalışır duruma, uygulamaların performansı için kritik öneme sahip olma.

Bağlantı tüneli durumunu denetlemek için kaynak sorun giderme API 'sini kullanarak VPN tünelinin bağlantı durumunu denetlemek üzere bir komut dosyası ile bir runbook oluşturulur. Durum sağlıklı değilse, yöneticilere bir e-posta tetikleyicisi gönderilir.

![Senaryo örneği][scenario]

Bu senaryo şunları olacaktır:

- `Start-AzureRmNetworkWatcherResourceTroubleshooting`Bağlantı durumu sorunlarını gidermek için cmdlet 'i çağıran bir runbook oluşturma
- Bir zamanlamayı runbook 'a bağlama

## <a name="before-you-begin"></a>Başlamadan önce

Bu senaryoya başlamadan önce, aşağıdaki önkoşulların olması gerekir:

- Azure 'da bir Azure Otomasyonu hesabı. Otomasyon hesabının en son modüller içerdiğinden ve ayrıca Azurerd. Network modülüne sahip olduğundan emin olun. Azurere. Network modülü, Otomasyon hesabınıza eklemeniz gerekiyorsa modül galerisinde kullanılabilir.
- Azure Otomasyonu 'nda yapılandırma bir kimlik bilgileri kümesine sahip olmanız gerekir. [Azure Otomasyonu güvenliği](../automation/automation-security-overview.md) hakkında daha fazla bilgi edinin
- Azure Otomasyonu 'nda tanımlı geçerli bir SMTP sunucusu (Microsoft 365, şirket içi e-posta veya başka bir) ve kimlik bilgileri
- Azure 'da yapılandırılmış bir sanal ağ geçidi.
- Günlükleri depolamak için mevcut bir kapsayıcıya sahip mevcut bir depolama hesabı.

> [!NOTE]
> Önceki görüntüde gösterilen altyapı, illüstrasyon amaçlıdır ve bu makalede yer alan adımlarla oluşturulmamıştır.

### <a name="create-the-runbook"></a>Runbook 'u oluşturma

Örneği yapılandırmanın ilk adımı, runbook 'u oluşturmaktır. Bu örnek, bir farklı çalıştır hesabı kullanır. Farklı Çalıştır hesapları hakkında bilgi edinmek için bkz. [Azure farklı çalıştır hesabı Ile runbook 'Ları kimlik doğrulama](../automation/manage-runas-account.md)

### <a name="step-1"></a>1. Adım

[Azure Portal](https://portal.azure.com) Azure Otomasyonu ' na gidin ve **runbook 'lar** ' a tıklayın.

![Otomasyon hesabına genel bakış][1]

### <a name="step-2"></a>2. Adım

Runbook oluşturma işlemini başlatmak için **runbook Ekle** ' ye tıklayın.

![Runbook 'lar dikey penceresi][2]

### <a name="step-3"></a>3. Adım

Runbook 'u oluşturmak için **hızlı oluştur**' un altında **yeni runbook oluştur ' a** tıklayın.

![Runbook Ekle dikey penceresi][3]

### <a name="step-4"></a>4. Adım

Bu adımda, runbook 'a bir ad veriyoruz; Örneğin, **Get-VPNGatewayStatus** olarak adlandırılır. Runbook 'a açıklayıcı bir ad vermek ve standart PowerShell adlandırma standartlarını izleyen bir ad vermek için önerilir. Bu örnek için Runbook türü **PowerShell**, diğer seçenekler ise grafik, PowerShell iş akışı ve grafik PowerShell iş akışıdır.

![Runbook dikey penceresi][4]

### <a name="step-5"></a>5. Adım

Bu adımda, runbook oluşturulur, aşağıdaki kod örneği örnek için gereken tüm kodu sağlar. İçeren koddaki öğelerin \<value\> , aboneliğinizdeki değerlerle değiştirilmesini gerekir.

**Kaydet** ' e tıklayarak aşağıdaki kodu kullanın

```powershell
# Set these variables to the proper values for your environment
$automationCredential = "<work or school account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for work or school account
$cred = Get-AutomationPSCredential -Name $automationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>6. Adım

Runbook kaydedildikten sonra, runbook 'un başlamasını otomatikleştirmek için bir zamanlamanın bağlanması gerekir. İşlemi başlatmak için **zamanlama**' ya tıklayın.

![6. Adım][6]

## <a name="link-a-schedule-to-the-runbook"></a>Bir zamanlamayı runbook 'a bağlama

Yeni bir zamanlamanın oluşturulması gerekir. **Runbook 'a bir zamanlama bağla**' ya tıklayın.

![7. Adım][7]

### <a name="step-1"></a>1. Adım

**Zamanlama** dikey penceresinde **Yeni Zamanlama Oluştur ' a** tıklayın.

![8. Adım][8]

### <a name="step-2"></a>2. Adım

**Yeni zamanlama** dikey penceresinde zamanlama bilgilerini doldurun. Ayarlanacak değerler aşağıdaki listede yer alabilir:

- **Ad** -zamanlamanın kolay adı.
- **Açıklama** -zamanlamanın açıklaması.
- **Başlar** -bu değer, zamanlamanın tetiklediği zamanı oluşturan Tarih, saat ve saat diliminin bir birleşimidir.
- **Yinelenme** -bu değer zamanlamaları tekrarlamayı belirler.  Geçerli değerler **bir kez** veya **yineleniyor**.
- Zamanlamanın saat, gün, hafta veya ay içinde her yineleme aralığını **Yinele** .
- **Süre sonu ayarla** -değer, zamanlamanın süresinin dolacağını belirler. **Evet** veya **Hayır** olarak ayarlanabilir. Evet seçilirse geçerli bir tarih ve saat sağlanmalıdır.

> [!NOTE]
> Bir runbook 'un her saatten daha sık çalışmasını istiyorsanız, farklı aralıklarda (15, 30, saat sonra 45 dakika) birden çok zamanlama oluşturulması gerekir

![9. Adım][9]

### <a name="step-3"></a>3. Adım

Zamanlamayı runbook 'a kaydetmek için Kaydet ' e tıklayın.

![10. adım][10]

## <a name="next-steps"></a>Sonraki adımlar

Artık ağ Izleyicisi sorunlarını Azure Otomasyonu ile tümleştirme hakkında bilgi sahibi olduğunuza göre, [Azure Ağ İzleyicisi ile bir uyarı tetiklenen bir uyarı oluşturma](network-watcher-alert-triggered-packet-capture.md)' yı zıyaret ederek VM uyarıları üzerinde paket yakalamalarını nasıl tetikleyeceğinizi öğrenin.

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png