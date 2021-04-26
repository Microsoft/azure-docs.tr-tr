---
title: Windows sanal masaüstü-Azure 'da Windows 10 VM 'lerine dil paketleri yüklemesi
description: Windows sanal masaüstü 'nde Windows 10 çoklu oturum VM 'Leri için dil paketleri nasıl yüklenir.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: db1ac3f5de507a5cfdbfec7216afea9a0f4ac541
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515048"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Windows 10 çoklu oturum görüntüsüne dil paketleri ekleme

Windows sanal masaüstü, kullanıcılarınızın her zaman ve her yerde dağıtabileceğiniz bir hizmettir. Bu nedenle, kullanıcılarınızın Windows 10 Kurumsal Çoklu oturum görüntüsünü görüntüleyen dili özelleştirebilmesi önemlidir.

Kullanıcılarınızın dil ihtiyaçlarını karşılamak için kullanabileceğiniz iki yol vardır:

- Her dil için özelleştirilmiş bir görüntüyle adanmış konak havuzları oluşturun.
- Aynı konak havuzunda farklı dile ve yerelleştirme gereksinimlerine sahip olan kullanıcılara, ihtiyaç duydukları dili seçmesini sağlamak için görüntülerini özelleştirin.

İkinci yöntem daha verimli ve ekonomik maliyetli bir yoldur. Ancak, hangi yöntemin gereksinimlerinize en uygun olduğuna karar vermek sizin için önemlidir. Bu makalede, görüntüleriniz için dillerin nasıl özelleştirileceği gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Birden çok dil eklemek için Windows 10 Kurumsal Çoklu oturum görüntülerini özelleştirmek üzere aşağıdaki şeylere ihtiyacınız vardır:

- Windows 10 Enterprise multi-session, sürüm 1903 veya üzeri bir Azure sanal makinesi (VM)

- Dil ISO, Istek üzerine Özellik (FOD) disk 1 ve görüntünün kullandığı işletim sistemi sürümüne ait gelen kutusu uygulamaları ISO. Buradan indirebilirsiniz:
     
     - Dil ISO:
        - [Windows 10, sürüm 1903 veya 1909 dil paketi ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, sürüm 2004 veya 20H2 dil paketi ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - FOD disk 1 ISO:
        - [Windows 10, sürüm 1903 veya 1909 FOD disk 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, sürüm 2004 veya 20H2 FOD disk 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - Gelen kutusu uygulamaları ISO:
        - [Windows 10, sürüm 1903 veya 1909 gelen kutusu uygulamaları ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, sürüm 2004 gelen kutusu uygulamaları ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, sürüm 20H2 gelen kutusu uygulamaları ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Görüntülerinizi yerelleştirmek için yerel deneyim paketi (LXP) ISO dosyaları kullanırsanız, en iyi dil deneyimi için uygun LXP ISO dosyasını da indirmeniz gerekir
        - Windows 10, sürüm 1903 veya 1909 kullanıyorsanız:
          - [Windows 10, sürüm 1903 veya 1909 LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Windows 10, sürüm 2004 veya 20H2 kullanıyorsanız, [Windows 10 ' da dil ekleme](/windows-hardware/manufacture/desktop/language-packs-known-issue) içindeki bilgileri kullanın: aşağıdaki LXP isos ' nin sizin için uygun olduğunu anlamak için bilinen sorunlar:
          - [Windows 10, sürüm 2004 veya 20H2 **9B** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10, sürüm 2004 veya 20H2 **9C** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10, sürüm 2004 veya 20H2 **10C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10, sürüm 2004 veya 20H2 **11C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10, sürüm 2004 veya 20H2 **1C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10, sürüm 2004 veya 20H2 **2C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)
          - [Windows 10, sürüm 2004 veya 20H2 **3c** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2103C.iso)

- Windows dosya sunucusu sanal makinesindeki bir Azure dosya paylaşma veya dosya paylaşma

>[!NOTE]
>Dosya paylaşımının (depo) özel görüntü oluşturmak için kullanmayı planladığınız Azure VM 'den erişilebilir olması gerekir.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Dil paketleri ve isteğe bağlı özellikler için bir içerik deposu oluşturun

Dil paketleri için içerik deposunu ve gelen kutusu uygulama paketleri için bir depoyu oluşturmak için:

1. Azure VM 'de, [Önkoşullar](#prerequisites)'Daki bağlantılardan Windows 10 Enterprise multi-session, sürüm 1903/1909 ve 2004 görüntüleri için Windows 10 çok dilli ISO, fods ve gelen kutusu uygulamalarını indirin.

2. ISO dosyalarını sanal makineye açın ve bağlayın.

3. Dil paketi ISO dosyasına gidin ve içeriği **Localexperiencepacks** ve **x64 \\ Langpacks** klasörlerinden kopyalayıp dosya paylaşımının içeriğini yapıştırın.

4. **FOD ISO dosyasına** gidin, tüm içeriğini kopyalayın ve dosya paylaşımında yapıştırın.
5. Gelen kutusu uygulamaları ISO 'daki **amd64fre** klasörüne gidin ve hazırladığınız gelen kutusu uygulamaları için depodaki içeriği kopyalayın.

     >[!NOTE]
     > Sınırlı depolamayla çalışıyorsanız, yalnızca kullanıcılarınızın ihtiyacı olan dillerin dosyalarını kopyalayın. Dosya adlarında dil kodlarına bakarak dosyaları birbirinden ayırt edebilirsiniz. Örneğin, Fransızca dosyası adında "fr-FR" koduna sahiptir. Tüm kullanılabilir dillerin dil kodlarının tüm listesi için bkz. [Windows Için kullanılabilir dil paketleri](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

     >[!IMPORTANT]
     > Bazı diller, farklı adlandırma kurallarını izleyen uydu paketlerine dahil edilen ek yazı tiplerini gerektirir. Örneğin, Japonca yazı tipi dosya adları "Jpan" içerir.
     >
     > [!div class="mx-imgBorder"]
     > ![Dosya adlarında "Jpan" dili etiketiyle Japonca dil paketlerine bir örnek.](media/language-pack-example.png)

6. Özel görüntü oluşturmak için kullanacağınız VM 'den okuma erişiminizin olması için dil içeriği depo paylaşımında izinleri ayarlayın.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Özel bir Windows 10 Kurumsal Çoklu oturum görüntüsünü el ile oluşturma

Özel bir Windows 10 Kurumsal Çoklu oturum görüntüsünü el ile oluşturmak için:

1. Bir Azure VM dağıtın, ardından Azure galerisine gidin ve kullanmakta olduğunuz Windows 10 Enterprise çoklu oturumunun geçerli sürümünü seçin.
2. VM 'yi dağıttıktan sonra, RDP 'yi yerel yönetici olarak kullanarak buna bağlanın.
3. SANAL makinenizin en son Windows güncelleştirmelerine sahip olduğundan emin olun. Gerekirse güncelleştirmeleri indirin ve VM 'yi yeniden başlatın.
4. Dil paketi, FOD ve gelen kutusu uygulamaları dosya paylaşma deposuna bağlanın ve bir mektup sürücüsüne (örneğin, E sürücüsüne) bağlayın.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Özel bir Windows 10 Kurumsal Çoklu oturum görüntüsünü otomatik olarak oluşturma

Dilleri otomatik bir işlem aracılığıyla yüklemeyi tercih ediyorsanız, PowerShell 'de bir betik ayarlayabilirsiniz. Windows 10 Enterprise multi-session, sürüm 2004 için Ispanyolca (Ispanya), Fransızca (Fransa) ve Çince (PRC) dil paketleri ve uydu paketleri yüklemek için aşağıdaki betik örneğini kullanabilirsiniz. Betik, dil arabirimi paketini ve tüm gerekli uydu paketlerini görüntüyle tümleştirir. Bununla birlikte, diğer dilleri yüklemek için bu betiği de değiştirebilirsiniz. Yalnızca betiği yükseltilmiş bir PowerShell oturumundan çalıştırdığınızdan emin olun, aksi takdirde çalışmaz.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Betik, yüklemeniz gereken dillerin sayısına bağlı olarak biraz zaman alabilir.

Betiğin çalışması tamamlandıktan sonra, **Başlangıç**  >  **ayarları**  >  **zaman & dil**  >  **dili**' ne giderek dil paketlerinin doğru şekilde yüklendiğinden emin olun. Dil dosyaları varsa, her şey ayarlanır.

Windows görüntüsüne ek diller ekledikten sonra, eklenen dilleri desteklemek için gelen kutusu uygulamalarının de güncelleştirilmeleri gerekir. Bu, önceden yüklenmiş uygulamaların gelen kutusu Apps ISO 'daki içerikle yenilenerek yapılabilir. Bu yenilemeyi, bağlantısı kesilen bir ortamda gerçekleştirmek için (sanal makineden Internet erişimi olmadan), işlemi otomatikleştirmek için aşağıdaki PowerShell betiği örneğini kullanabilirsiniz.

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>ISO 'da bulunan gelen kutusu uygulamaları önceden yüklenmiş Windows uygulamalarının en son sürümleri değildir. Tüm uygulamaların en son sürümünü almak için Windows Mağazası uygulamasını kullanarak uygulamaları güncelleştirmeniz ve ek dilleri yükledikten sonra güncelleştirmeler için el ile arama yapmanız gerekir.

İşiniz bittiğinde paylaşımın bağlantısını kesdiğinizden emin olun.

## <a name="finish-customizing-your-image"></a>Görüntünüzü özelleştirmeyi tamamlama

Dil paketlerini yükledikten sonra, özelleştirilmiş yansımanıza eklemek istediğiniz diğer yazılımları yükleyebilirsiniz.

Görüntünüzü özelleştirmeyi tamamladıktan sonra, Sistem Hazırlama Aracı 'nı (Sysprep) çalıştırmanız gerekir.

Sysprep çalıştırmak için:

1. Yükseltilmiş bir komut istemi açın ve görüntüyü genelleştirmek için aşağıdaki komutu çalıştırın:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. VM 'yi durdurun, ardından [Azure 'da Genelleştirilmiş BIR VM 'nin yönetilen bir görüntüsünü oluşturma](../virtual-machines/windows/capture-image-resource.md)bölümündeki yönergeleri izleyerek bir yönetilen görüntüde yakalayın.

3. Artık özelleştirilmiş görüntüyü Windows sanal masaüstü ana bilgisayar havuzunu dağıtmak için kullanabilirsiniz. Bir konak havuzunu dağıtmayı öğrenmek için bkz. [öğretici: Azure Portal bir konak havuzu oluşturma](create-host-pools-azure-marketplace.md).

## <a name="enable-languages-in-windows-settings-app"></a>Windows ayarları uygulamasında dilleri etkinleştirme

Son olarak, ana bilgisayar havuzunu dağıttıktan sonra, Ayarlar menüsünde tercih edilen dillerini seçebilmeniz için dili her kullanıcının dil listesine eklemeniz gerekir.

Kullanıcılarınızın yüklediğiniz dilleri seçmesini sağlamak için, Kullanıcı olarak oturum açın ve ardından, yüklü dil paketlerini diller menüsüne eklemek için aşağıdaki PowerShell cmdlet 'ini çalıştırın. Bu betiği, kullanıcı oturumunda oturum açtığında etkinleştiren otomatik bir görev veya oturum açma betiği olarak da ayarlayabilirsiniz.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Bir Kullanıcı dil ayarlarını değiştirdikten sonra, değişikliklerin etkili olması için Windows Sanal Masaüstü oturumunda oturumu kapatıp yeniden oturum açması gerekir. 

## <a name="next-steps"></a>Sonraki adımlar

Dil paketlerine yönelik bilinen sorunları merak ediyorsanız, bkz. [Windows 10 ' da dil paketleri ekleme, sürüm 1803 ve sonraki sürümler: bilinen sorunlar](/windows-hardware/manufacture/desktop/language-packs-known-issue).

Windows 10 Enterprise çoklu oturum hakkında başka sorularınız varsa, [SSS sorusuna](windows-10-multisession-faq.yml)göz atın.
