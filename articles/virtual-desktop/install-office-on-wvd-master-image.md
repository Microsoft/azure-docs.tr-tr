---
title: Office 'i ana VHD görüntüsüne yükler-Azure
description: Windows sanal masaüstü ana görüntüsünde Office 'i Azure 'a yüklemek ve özelleştirmek.
author: Heidilohr
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: ce4eb3d556b6dff833129d11b26d439066b63f2e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446868"
---
# <a name="install-office-on-a-master-vhd-image"></a>Ana VHD görüntüsü üzerinde Office yükleme

Bu makalede, Azure 'a yüklemek üzere bir ana sanal sabit disk (VHD) görüntüsüne Enterprise, OneDrive ve diğer yaygın uygulamalar için Microsoft 365 uygulamalarını nasıl yükleyeceğiniz açıklanır. Kullanıcılarınızın belirli iş kolu (LOB) uygulamalarına erişmesi gerekiyorsa, bu makaledeki yönergeleri tamamladıktan sonra bunları yüklemenizi öneririz.

Bu makalede, zaten bir sanal makine (VM) oluşturmuş olduğunuz varsayılmaktadır. Aksi takdirde, bkz. [ana VHD görüntüsünü hazırlama ve özelleştirme](set-up-customize-master-image.md#create-a-vm)

Bu makalede ayrıca, Azure 'da veya Hyper-V Yöneticisi 'nde sağlanmasından bağımsız olarak, VM 'de yükseltilmiş erişiminizin olduğunu varsaymaktadır. Aksi takdirde, [tüm Azure aboneliklerini ve Yönetim gruplarını yönetmek için erişimi yükseltme](../role-based-access-control/elevate-access-global-admin.md)bölümüne bakın.

>[!NOTE]
>Bu yönergeler, kuruluşunuzun mevcut işlemleriyle kullanılabilecek Windows sanal masaüstüne özgü bir yapılandırma içindir.

## <a name="install-office-in-shared-computer-activation-mode"></a>Office 'i paylaşılan bilgisayar etkinleştirme moduna yüklerken

Paylaşılan bilgisayar etkinleştirme, kuruluşunuzda birden çok kullanıcı tarafından erişilen bir bilgisayara Microsoft 365 uygulamalarını dağıtmanıza olanak tanır. Paylaşılan bilgisayar etkinleştirmesi hakkında daha fazla bilgi için bkz. [Microsoft 365 uygulamalar için paylaşılan bilgisayar etkinleştirmeye genel bakış](/deployoffice/overview-shared-computer-activation).

Office 'i yüklemek için [Office dağıtım aracı](https://www.microsoft.com/download/details.aspx?id=49117) 'nı kullanın. Windows 10 Enterprise çoklu oturum yalnızca aşağıdaki Office sürümlerini destekler:

   - Enterprise için Microsoft 365 uygulamalar
   - Microsoft 365 Microsoft 365 İş Premium abonelikle birlikte gelen iş uygulamaları

Office dağıtım aracı bir yapılandırma XML dosyası gerektirir. Aşağıdaki örneği özelleştirmek için, [Office dağıtım aracı yapılandırma seçeneklerine](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)bakın.

Sağladığımız Bu örnek yapılandırma XML 'i şu işlemleri yapar:

   - Aylık kurumsal kanaldan Office 'i yükleyip aylık kurumsal kanaldan güncelleştirmeleri sunun.
   - X64 mimarisini kullanın.
   - Otomatik güncelleştirmeleri devre dışı bırakın.
   - Mevcut Office yüklemelerini kaldırın ve ayarlarını geçirin.
   - Paylaşılan bilgisayar etkinleştirmesini etkinleştirin.

>[!NOTE]
>Visio 'nun şablon arama özelliği, Windows sanal masaüstü 'nde beklendiği gibi çalışmayabilir.

Bu örnek yapılandırma XML 'i şunları yapamayacaklarınız:

   - Skype Kurumsal 'ı yükler
   - OneDrive 'ı Kullanıcı başına moda yükleyin. Daha fazla bilgi için bkz. [OneDrive 'ı makine başına moda yükleyin](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Paylaşılan bilgisayar etkinleştirme, grup ilkesi nesneleri (GPO 'Lar) veya kayıt defteri ayarları aracılığıyla ayarlanabilir. GPO, **\\ \\ Yönetim Şablonları \\ Microsoft Office 2016 (makine) \\ lisanslama ayarlarındaki bilgisayar yapılandırma ilkeleri** ' nde bulunur

Office dağıtım aracı setup.exe içerir. Office 'i yüklemek için komut satırında aşağıdaki komutu çalıştırın:

```cmd
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Örnek configuration.xml

Aşağıdaki XML örneği aylık kurumsal kanal sürümünü yükler.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="MonthlyEnterprise">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level="Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Office ekibi **OfficeClientEdition** parametresi için 64 bitlik yüklemeyi kullanmayı önerir.

Office 'i yükledikten sonra varsayılan Office davranışını güncelleştirebilirsiniz. Davranışı güncelleştirmek için aşağıdaki komutları tek tek veya bir toplu iş dosyasında çalıştırın.

```cmd
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>OneDrive 'ı makine başına moduna yükleyin

OneDrive, normalde Kullanıcı başına yüklenir. Bu ortamda makine başına yüklenmelidir.

OneDrive 'ı makine başına modunda yüklemek için şu adımları uygulayın:

1. İlk olarak, OneDrive yükleyicisinin aşamasına kadar bir konum oluşturun. Yerel disk klasörü veya [ \\ \\ UNC] (File://UNC) konumu iyidir.

2. OneDriveSetup.exe, bu bağlantıyla hazırlanan konumunuza indirin: <https://aka.ms/OneDriveWVD-Installer>

3. Office 'i yok ederek OneDrive 'a yüklediyseniz **\<ExcludeApp ID="OneDrive" /\>** , aşağıdaki komutu çalıştırarak, var olan tüm OneDrive Kullanıcı başına yüklemelerini yükseltilmiş bir komut isteminden kaldırın:

    ```cmd
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. **Allusersınstall** kayıt defteri değerini ayarlamak için bu komutu yükseltilmiş bir komut isteminden çalıştırın:

    ```cmd
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. OneDrive 'ı makine başına modunda yüklemek için şu komutu çalıştırın:

    ```cmd
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. OneDrive 'ı tüm kullanıcılar için oturum açma sırasında başlatılacak şekilde yapılandırmak için bu komutu çalıştırın:

    ```cmd
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Aşağıdaki komutu çalıştırarak **sessizce Kullanıcı hesabını Yapılandır** özelliğini etkinleştirin.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Aşağıdaki komutu çalıştırarak Windows bilinen klasörlerini yeniden yönlendirin ve OneDrive 'a taşıyın.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Microsoft ekipleri ve Skype Kurumsal

Windows sanal masaüstü, Skype Kurumsal 'ı desteklemez.

Microsoft ekipleri yükleme konusunda yardım için bkz. [Windows sanal masaüstü üzerinde Microsoft ekipleri kullanma](teams-on-wvd.md). Windows sanal masaüstündeki Microsoft ekipleri için medya iyileştirmesi önizleme sürümünde kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Artık Office 'i görüntüye eklemişseniz, ana VHD görüntünüzü özelleştirmeye devam edebilirsiniz. Bkz. [ana VHD görüntüsünü hazırlama ve özelleştirme](set-up-customize-master-image.md).
