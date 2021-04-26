---
title: Windows sanal masaüstü MALTı uygulama PowerShell iliştirme-Azure
description: PowerShell kullanarak Windows sanal masaüstü için MSIX uygulama iliştirme 'yi ayarlama.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: ebc403553443a9ea04525323b751fbdb51d23c6e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500592"
---
# <a name="set-up-msix-app-attach-using-powershell"></a>PowerShell kullanarak MSIX uygulama eklemeyi ayarlama

Azure portal ek olarak, PowerShell ile MSIX uygulamasını el ile de ekleyebilirsiniz. Bu makalede, PowerShell kullanarak MSIX uygulama iliştirme 'yi ayarlama işleminde size kılavuzluk edilir.

## <a name="requirements"></a>Gereksinimler

İşte MALTı uygulama eklemeyi yapılandırmak için gerekenler şunlardır:

- Çalışan bir Windows sanal masaüstü dağıtımı. Windows sanal masaüstü 'Nü (klasik) dağıtmayı öğrenmek için bkz. [Windows sanal masaüstü 'nde kiracı oluşturma](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Windows sanal masaüstü Azure Resource Manager tümleştirme ile nasıl dağıtılacağını öğrenmek için, bkz. [Azure Portal bir konak havuzu oluşturma](./create-host-pools-azure-marketplace.md).
- En az bir etkin oturum ana bilgisayarı olan bir Windows sanal masaüstü konak Havuzu.
- Masaüstü uzak uygulama grubu.
- MSIX paketleme aracı.
- Bir dosya paylaşımında karşıya yüklenen bir MSIX görüntüde genişletilen, MSIX paketlenmiş bir uygulama.
- Windows sanal masaüstü dağıtımınızda MSIX paketinin depolanacağı bir dosya paylaşımıdır.
- MSIX görüntüsünü karşıya yüklediğiniz dosya paylaşımının konak havuzundaki tüm sanal makineler (VM) için de erişilebilir olması gerekir. Kullanıcılar görüntüye erişmek için salt okuma izinlerine sahip olacaktır.
- PowerShell Core indirin ve yükleyin.
- Genel Önizleme Azure PowerShell modülünü indirin ve yerel bir klasöre genişletin.
- Aşağıdaki cmdlet 'i çalıştırarak Azure modülünü yükledikten sonra:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Azure 'da oturum açın ve modülü içeri aktarın

Tüm gereksinimleri hazırladıktan sonra, PowerShell Core 'u yükseltilmiş bir komut isteminde açın ve şu cmdlet 'i çalıştırın:

```powershell
Connect-AzAccount
```

Çalıştırdıktan sonra, kimlik bilgilerinizi kullanarak hesabınızın kimliğini doğrulayın. Bu durumda, bir cihaz URL 'SI veya belirteç istenebilir.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Az. WindowsVirtualDesktop modülünü içeri aktarma

Bu makaledeki yönergeleri izlemek için az. DesktopVirtualization modülünün olması gerekir.

>[!NOTE]
>Genel önizleme için, modülü el ile içeri aktarmanız gereken ayrı ZIP dosyaları olarak sağlayacağız.

Başlamadan önce, az. DesktopVirtualization modülünün oturumunuz veya VM 'niz üzerinde zaten yüklü olup olmadığını görmek için aşağıdaki cmdlet 'i çalıştırabilirsiniz:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Bir modülün var olan bir kopyasını kaldırmak ve baştan başlamak için WAN kullanıyorsanız şu cmdlet 'i çalıştırın:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

SANAL makinenizde modül engellenirse, engelini kaldırmak için bu cmdlet 'i çalıştırın:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Bu temizlik sayesinde modülün içeri aktarılması zaman olur.

1. Aşağıdaki cmdlet 'i çalıştırın, sonra özel kodu çalıştırmak için kabul etmeniz istendiğinde **R** tuşuna basın.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. İçeri aktarma cmdlet 'ini çalıştırdığınızda, aşağıdaki cmdlet 'i çalıştırarak MSIX cmdlet 'lerinin olup olmadığını denetleyin:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Cmdlet 'ler varsa, çıkış şöyle görünmelidir:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Bu çıktıyı görmüyorsanız tüm PowerShell ve PowerShell Çekirdek oturumlarını kapatın ve yeniden deneyin.

## <a name="set-up-helper-variables"></a>Yardımcı değişkenleri ayarlama

Modülü içeri aktardıktan sonra yardımcı değişkenleri ayarlamanız gerekir. Aşağıdaki örneklerde, her birinin nasıl yapılacağı gösterilecektir.

Abonelik KIMLIĞINIZI almak için:

```powershell
Get-AzContext -ListAvailable | fl
```

Bir Azure kiracısı ve abonelik bağlamını bir adla seçmek için:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

Abonelik değişkenini ayarlamak için:

```powershell
$subId = $obj.Subscription.Id
```

Çalışma alanı adını ayarlamak için:

```powershell
$ws = "<WorksSpaceName>"
```

Konak havuzu adını ayarlamak için:

```powershell
$hp = "<HostPoolName>"
```

Oturum Ana bilgisayar VM 'lerinin yapılandırıldığı kaynak grubunu ayarlamak için:

```powershell
$rg = "<ResourceGroupName>"
```

Son olarak, tüm değişkenleri doğru şekilde ayarlamış diğinizi doğrulamak için:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Bir konak havuzuna bir MSIX paketi ekleme

Her şeyi ayarladıktan sonra, MSIX paketinin bir konak havuzuna eklenmesi zaman olur. Bunu yapmak için ilk olarak MSIX görüntüsünün UNC yolunu almanız gerekir.

UNC yolunu kullanarak, MSIX görüntüsünü genişletmek için bu cmdlet 'i çalıştırın:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

İstenen konak havuzunuza MSIX paketini eklemek için bu cmdlet 'i çalıştırın:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

İşiniz bittiğinde paketin bu cmdlet ile oluşturulduğunu doğrulayın:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Bir ana bilgisayar havuzundan bir MSIX paketini kaldırma

Bir konak havuzundan bir paketi kaldırmak için:

Bu cmdlet ile bir konak havuzuyla ilişkili tüm paketlerin bir listesini alın, ardından çıkışta kaldırmak istediğiniz paketin adını bulun:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

Alternatif olarak, bu cmdlet ile görünen adına göre belirli bir paketi de alabilirsiniz:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Paketi kaldırmak için şu cmdlet 'i çalıştırın:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>MSIX uygulamalarını bir uygulama grubuna yayımlayın

Bu bölümdeki yönergeleri yalnızca önceki bölümlerdeki yönergeleri takip ediyorsanız izleyebilirsiniz. Etkin bir oturum ana bilgisayarı olan, en az bir masaüstü uygulama grubuna sahip bir konak havuzunuz varsa ve konak havuzuna bir MSIX paketi eklediyseniz, başlamaya hazırsınız demektir.

Bir uygulamayı MSIX paketinden bir uygulama grubuna yayımlamak için, adını bulmanız ve ardından bu adı yayımlama cmdlet 'inde kullanmanız gerekir.

Bir uygulamayı yayımlamak için:

Tüm kullanılabilir uygulama gruplarını listelemek için bu cmdlet 'i çalıştırın:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Uygulamaları yayımlamak istediğiniz uygulama grubunun adını bulduğunuz bu cmdlet içinde adını kullanın:

```powershell
$grName = "<AppGroupName>"
```

Son olarak, uygulamayı yayımlamanız gerekir.

- MSIX uygulamasını bir masaüstü uygulama grubuna yayımlamak için şu cmdlet 'i çalıştırın:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Uygulamayı uzak bir uygulama grubuna yayımlamak için bunun yerine aşağıdaki cmdlet 'i çalıştırın:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Bir kullanıcı aynı konak havuzunda hem uzak bir uygulama grubuna hem de bir masaüstü uygulama grubuna atanırsa, Kullanıcı kendi uzak masaüstüne bağlandıklarında, her iki gruptan da MALTÝ uygulama görür.

## <a name="next-steps"></a>Sonraki adımlar

Bu özellikle ilgili topluluk sorularımıza [Windows sanal masaüstü TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)' de sorun.

Windows sanal masaüstü [Geri Bildirim Hub 'ında](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)Windows sanal masaüstü geri bildirimini de bırakabilirsiniz.

Yararlı bulabileceğiniz bazı makaleler aşağıda verilmiştir:

- [MSIX uygulama iliştirme sözlüğü](app-attach-glossary.md)
- [MSIX uygulama iliştirme SSS](app-attach-faq.md)
