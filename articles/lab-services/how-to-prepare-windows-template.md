---
title: Bir Windows şablon makinesi ayarlamaya yönelik kılavuz | Microsoft Docs
description: Laboratuvar hizmetlerinde bir Windows şablon makinesi hazırlamak için genel adımlar.  Bu adımlar, Windows Update zamanlama, OneDrive yükleme ve Office yükleme ayarlarını içerir.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 216dc843b31eac355e1d818014f3d70b2ef83132
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647911"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Azure Lab Services bir Windows şablon makinesi ayarlamaya yönelik kılavuz

Azure Lab Services için bir Windows 10 şablon makinesi ayarlıyorsanız, göz önünde bulundurmanız gereken bazı en iyi uygulamalar ve ipuçları aşağıda verilmiştir. Aşağıdaki yapılandırma adımları isteğe bağlıdır.  Ancak, bu hazırlık adımları öğrencilerinizin daha üretken olmasını, sınıf süresi kesintilerini en aza indirmenize ve en son teknolojileri kullandığından emin olmanıza yardımcı olabilir.

>[!IMPORTANT]
>Bu makale, makine şablonu değişiklik sürecini kolaylaştırmak için PowerShell parçacıkları içerir.  Gösterilen tüm PowerShell betikleri için, bunları Windows PowerShell 'de yönetici ayrıcalıklarıyla çalıştırmak isteyeceksiniz. Windows 10 ' da, bunu yapmanın hızlı bir yolu, Başlat menüsüne sağ tıklayıp "Windows PowerShell (yönetici)" seçeneğini kullanmaktır.

## <a name="install-and-configure-onedrive"></a>OneDrive 'ı yükleyin ve yapılandırın

Bir sanal makine sıfırlandığında öğrenci verilerinin kaybedilmesini korumak için öğrencilerinin verileri buluta geri yedeklemesini öneririz.  Microsoft OneDrive, öğrencilerin verilerini korumalarına yardımcı olabilir.  

### <a name="install-onedrive"></a>OneDrive 'ı yükleyin

OneDrive 'ı el ile indirip yüklemek için [OneDrive](https://onedrive.live.com/about/download/) veya [OneDrive iş](https://onedrive.live.com/about/business/) indirme sayfalarına bakın.

Aşağıdaki PowerShell betiğini de kullanabilirsiniz.  Bu, OneDrive 'ın en son sürümünü otomatik olarak indirip yükleyecek.  OneDrive istemcisi yüklendikten sonra yükleyiciyi çalıştırın.  Örneğimizde, `/allUsers` OneDrive 'ı makinedeki tüm kullanıcılar için yüklemek üzere anahtarını kullanıyoruz. Ayrıca, `/silent` OneDrive 'ı sessizce yüklemek için anahtarını da kullanırız.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>OneDrive özelleştirmeleri

[OneDrive 'da yapılabilecek birçok özelleştirme](/onedrive/use-group-policy)vardır. Daha yaygın özelleştirmelerin bazılarını ele alalım.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Windows bilinen klasörlerini sessizce OneDrive 'a taşıyın

Belgeler, Indirmeler ve resimler gibi klasörler genellikle öğrenci dosyalarını depolamak için kullanılır. Bu klasörlerin OneDrive 'a yedeklenmesini sağlamak için, bu klasörleri OneDrive 'a taşımanızı öneririz.

Active Directory kullanmayan bir makineniz varsa, kullanıcılar OneDrive 'a kimlik doğrulamasından geçtiğinde bu klasörleri el ile OneDrive 'a taşıyabilir.

1. Dosya gezginini aç
2. Belgeler, Indirmeler veya Resimler klasörüne sağ tıklayın.
3. Özellikler > konuma gidin.  Klasörü OneDrive dizininde yeni bir klasöre taşıyın.

Sanal makineniz Active Directory bağlıysa, şablon makinesini öğrencilerinizi, bilinen klasörleri OneDrive 'a taşımasını otomatik olarak isteyecek şekilde ayarlayabilirsiniz.  

Önce kuruluş KIMLIĞINIZI almanız gerekir.  Daha fazla yönerge için bkz. [Microsoft 365 kuruluş kimliğinizi bulma](/onedrive/find-your-office-365-tenant-id).  Ayrıca, aşağıdaki PowerShell 'i kullanarak kuruluş KIMLIĞINI de alabilirsiniz.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Kuruluş KIMLIĞINIZ olduktan sonra OneDrive ' ı, aşağıdaki PowerShell kullanarak bilinen klasörleri OneDrive 'a taşımayı isteyecek şekilde ayarlayın.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>OneDrive dosyalarını isteğe bağlı olarak kullanın

Öğrenciler OneDrive hesaplarının içinde çok sayıda dosya içerebilir. Makinedeki alanı kaydetmeye ve indirme süresini azaltmaya yardımcı olmak için, öğrencinin OneDrive hesabında depolanan tüm dosyaların isteğe bağlı olmasını öneririz.  İsteğe bağlı dosyalar yalnızca bir kullanıcı dosyaya eriştiğinde indirilir.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Kullanıcıları OneDrive 'da sessizce oturum açın

OneDrive, oturum açmış kullanıcının Windows kimlik bilgileriyle otomatik olarak oturum açmak üzere ayarlanabilir.  Otomatik oturum açma, öğrencinin okul kimlik bilgileriyle oturum açtığı sınıflar için yararlıdır.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>OneDrive kurulumunun sonunda görüntülenen öğreticiyi devre dışı bırakın

Bu ayar, OneDrive kurulumunun sonunda bir Web tarayıcısında öğreticinin başlatılmasını engellemenizi sağlar.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Otomatik olarak indirilecek bir dosyanın maksimum boyutunu ayarla

Bu ayar, Isteğe bağlı OneDrive dosyaları etkinleştirilmemiş cihazlarda Windows kimlik bilgileri ile OneDrive eşitleme istemcisine kullanıcılar için sessizce oturum açma özelliği ile birlikte kullanılır. OneDrive eşitleme istemcisi (OneDrive.exe) dosyalarını indirmeden önce, belirtilen eşikten (MB) daha büyük bir OneDrive 'A sahip olan herhangi bir kullanıcının eşitlemek istedikleri klasörleri seçmeleri istenir.  Bizim örneğimizde, "1111-2222-3333-4444" kuruluş KIMLIĞIDIR ve 0005000, 5 GB eşiğini belirler.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-365"></a>Microsoft 365 yükleyip yapılandırın

### <a name="install-microsoft-365"></a>Microsoft 365 yüklensin

Şablon makineniz Office 'e ihtiyaç duyuyorsa Office [dağıtım aracı (ODT)](https://www.microsoft.com/download/details.aspx?id=49117)aracılığıyla Office yüklemenizi öneririz. Hangi mimarinin, Office 'te hangi özelliklere ihtiyacınız olacağını ve ne sıklıkta güncelleştirdiklerinizi seçmek için [Microsoft 365 Apps yönetim merkezini](https://config.office.com/) kullanarak yeniden kullanılabilir bir yapılandırma dosyası oluşturmanız gerekir.

1. [Microsoft 365 Apps yönetim merkezi](https://config.office.com/) ' ne gidin ve kendi yapılandırma dosyanızı indirin.
2. [Office dağıtım aracı 'nı](https://www.microsoft.com/download/details.aspx?id=49117)indirin.  İndirilen dosya olacak `setup.exe` .
3. `setup.exe /download configuration.xml`Office bileşenlerini indirmek için ' i çalıştırın.
4. `setup.exe /configure configuration.xml`Office bileşenlerini yüklemek için ' i çalıştırın.

### <a name="change-the-microsoft-365-update-channel"></a>Microsoft 365 güncelleştirme kanalını değiştirme

Office yapılandırma aracını kullanarak, Office 'in güncelleştirmeleri ne sıklıkta alacağını ayarlayabilirsiniz. Ancak, yüklemeden sonra Office 'in güncelleştirmeleri hangi sıklıkta alacağını değiştirmeniz gerekiyorsa, güncelleştirme kanalı URL 'sini değiştirebilirsiniz. Güncelleştirme kanalı URL adresleri [, kuruluşunuzdaki cihazlar için Microsoft 365 Apps Update kanalını değiştirme](/deployoffice/change-update-channels)bölümünde bulunabilir. Aşağıdaki örnekte, Microsoft 365 aylık güncelleştirme kanalını kullanacak şekilde nasıl ayarlanacağı gösterilmektedir.

```powershell
# Update to the Microsoft 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Güncelleştirmeleri yükleyip yapılandırın

### <a name="install-the-latest-windows-updates"></a>En son Windows güncelleştirmelerini yükler

Şablon VM 'yi yayımlamadan önce güvenlik amacıyla en son Microsoft güncelleştirmelerini şablon makinesine yüklemenizi öneririz.  Ayrıca, güncelleştirmeler beklenmeyen saatlerde çalıştırıldığında öğrencilerin çalışmalarını kesintiye uğratan önler.

1. Başlangıç menüsünden **ayarları** Başlat
2. **Güncelleştirme** & güvenlik ' e tıklayın
3. **Güncelleştirmeleri denetle** 'ye tıklayın
4. Güncelleştirmeler indirilir ve yüklenir.

Şablon makinesini güncelleştirmek için PowerShell de kullanabilirsiniz.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Bazı güncelleştirmeler makinenin yeniden başlatılmasını gerektirebilir.  Yeniden başlatma işlemi gerekip gerekmediği sorulur.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Microsoft Store uygulamalar için en son güncelleştirmeleri yükler

Tüm Microsoft Store uygulamalarının en son sürümlerine güncelleştirilmesini öneririz.  Microsoft Store uygulamaları el ile güncelleştirme yönergeleri aşağıda verilmiştir.  

1. **Microsoft Store** uygulamasını başlatın.
2. Uygulamanın üst köşesinde Kullanıcı fotoğrafınızın yanındaki elips (...) seçeneğine tıklayın.
3. Açılır menüden **İndir** ve Güncelleştirmeler ' i seçin.
4. **Güncelleştirme al** düğmesine tıklayın.

Ayrıca, zaten yüklü olan Microsoft Store uygulamaları güncelleştirmek için PowerShell kullanabilirsiniz.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Otomatik Windows güncelleştirmelerini durdur

Windows 'u en son sürüme güncelleştirdikten sonra, Windows güncelleştirmelerini durdurmayı düşünebilirsiniz.  Otomatik Güncelleştirmeler, zamanlanan sınıf zamanına engel olabilir.  Kurağınız daha uzun bir süredir çalışıyorsa, öğrenciler için el ile güncelleştirmeleri denetlemesini veya zamanlanan sınıf saatleri dışında bir süre için otomatik güncelleştirmeleri ayarlamayı düşünün.  Windows Update özelleştirme seçenekleri hakkında daha fazla bilgi için bkz. [ek Windows Update ayarlarını yönetme](/windows/deployment/update/waas-wu-settings).

Otomatik Windows güncelleştirmeleri aşağıdaki PowerShell betiği kullanılarak durdurulmuş olabilir.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Yabancı dil paketlerini yükler

Sanal makinede ek dillerin yüklü olması gerekiyorsa, Microsoft Store aracılığıyla bunları ekleyebilirsiniz.

1. Microsoft Store Başlat
2. "Dil paketi" araması yapın
3. Yüklenecek dili seçin

Zaten şablon VM 'de oturum açtıysanız, `ms-settings:regionlanguage?activationSource=SMC-IA-4027670` uygun ayarlar sayfasına doğrudan gitmek için "dil paketini yükler" kısayolunu () kullanın.

## <a name="remove-unneeded-built-in-apps"></a>Gereksiz yerleşik uygulamaları kaldırma

Windows 10, belirli sınıfınız için gerekli olmayan birçok yerleşik uygulamayla birlikte gelir. Öğrenciler için makine görüntüsünü basitleştirmek amacıyla, bazı uygulamaları şablon makinenizden kaldırmak isteyebilirsiniz.  Yüklü uygulamaların listesini görmek için PowerShell `Get-AppxPackage` cmdlet 'ini kullanın.  Aşağıdaki örnekte, kaldırılabileceği tüm yüklü uygulamalar gösterilmektedir.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Bir uygulamayı kaldırmak için Remove-Appx cmdlet 'ini kullanın.  Aşağıdaki örnekte, XBox ile ilgili her şeyin nasıl kaldırılacağı gösterilmektedir.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Eğitim ile ilgili ortak uygulamaları yükler

Genellikle Windows Mağazası uygulaması aracılığıyla öğretme için kullanılan diğer uygulamaları yükler. Öneriler, [Microsoft beyaz tahta uygulaması](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Microsoft ekipleri](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)ve [minecrat eğitim sürümü](https://education.minecraft.net/)gibi uygulamalar içerir. Bu uygulamaların Windows Mağazası aracılığıyla veya şablon VM 'deki ilgili web siteleri aracılığıyla el ile yüklenmesi gerekir.

## <a name="conclusion"></a>Sonuç

Bu makalede, Windows şablon sanal makinenizin etkin bir sınıf için hazırlanması için isteğe bağlı adımlar gösterilmektedir.  Adımlarda OneDrive yükleme ve Microsoft 365 yükleme, Windows için güncelleştirmeleri yükleme ve Microsoft Store uygulamalar için güncelleştirmeler yükleme sayılabilir.  Ayrıca, bir zamanlamaya yönelik olarak en iyi şekilde çalışacak güncelleştirmeleri nasıl ayarlayabileceğinizi de tartıştık.  

## <a name="next-steps"></a>Sonraki adımlar
Maliyetlerin yönetilmesine yardımcı olmak için Windows 'un kapatılmasını denetleme davranışını denetleme hakkında bilgi için bkz. [Windows kapanıyor davranışını denetleme Kılavuzu](how-to-windows-shutdown.md)