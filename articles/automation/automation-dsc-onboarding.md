---
title: Azure Otomasyonu durum yapılandırmasını etkinleştirme
description: Bu makalede, Azure Otomasyonu durum yapılandırması ile yönetim için makinelerin nasıl ayarlanacağı açıklanır.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: d338c5f34d49663345582198ff53ba50a2919d7e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829431"
---
# <a name="enable-azure-automation-state-configuration"></a>Azure Otomasyonu durum yapılandırmasını etkinleştirme

Bu konu başlığı altında, Azure Otomasyonu durum yapılandırması ile makinelerinizi yönetim için nasıl ayarlayabileceğinizi anlatmaktadır. Bu hizmetin ayrıntıları için bkz. [Azure Otomasyonu durum yapılandırmasına genel bakış](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Azure VM 'lerini etkinleştirin

Azure Otomasyonu durum yapılandırması, Azure portal, Azure Resource Manager şablonlarını veya PowerShell 'i kullanarak Azure sanal makinelerini yapılandırma yönetimi için kolayca etkinleştirmenizi sağlar. Yönetim altında ve bir yöneticinin bir VM 'ye uzak olarak gerek kalmadan, Azure VM Istenen durum yapılandırma uzantısı VM 'yi Azure Otomasyonu durum yapılandırması ile kaydeder. Azure uzantısı zaman uyumsuz olarak çalıştığından, ilerleme durumunu izlemek için gereken adımlar [VM kurulumunun denetim durumu](#check-status-of-vm-setup)bölümünde verilmiştir.

> [!NOTE]
>DSC 'yi bir Linux düğümüne dağıtmak **/tmp** klasörünü kullanır. Gibi modüller, `nxautomation` uygun konumlarına yüklemeden önce, doğrulama için geçici olarak indirilir. Modüllerin doğru bir şekilde yüklendiğinden emin olmak için, Linux Log Analytics aracısına **/tmp** klasöründe okuma/yazma izinleri gerekir.<br><br>
>Linux için Log Analytics Aracısı kullanıcı olarak çalışır `omsagent` . Kullanıcıya >yazma izni vermek için `omsagent` komutunu çalıştırın `setfacl -m u:omsagent:rwx /tmp` .

### <a name="enable-a-vm-using-azure-portal"></a>Azure portal kullanarak VM 'yi etkinleştirme

Azure VM 'nin [Azure Portal](https://portal.azure.com/)aracılığıyla yapılandırma durumunu etkinleştirmek için:

1. VM 'Leri etkinleştirmek için Azure Otomasyonu hesabına gidin. 

2. Durum Yapılandırması sayfasında **düğümler** sekmesini seçin ve ardından **Ekle**' ye tıklayın.

3. Etkinleştirilecek bir VM seçin.

4. Makinede PowerShell istenen durum uzantısı yüklü değilse ve güç durumu çalışıyorsa, **Bağlan**' a tıklayın.

5. **Kayıt**' ın altında, kullanım durumu Için gereken [PowerShell DSC yerel Configuration Manager değerlerini](/powershell/scripting/dsc/managing-nodes/metaConfig) girin. İsteğe bağlı olarak, VM 'ye atanacak bir düğüm yapılandırması girebilirsiniz.

![VM etkinleştiriliyor](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Azure Resource Manager şablonları kullanarak VM 'yi etkinleştirme

Azure Resource Manager şablonlarını kullanarak bir VM 'yi durum yapılandırması için yükleyebilir ve etkinleştirebilirsiniz. Var olan bir VM 'nin durum yapılandırması için izin veren örnek bir şablon için bkz. [Istenen durum yapılandırma hizmeti tarafından yönetilen sunucu](https://azure.microsoft.com/resources/templates/101-automation-configuration/) . Bir sanal makine ölçek kümesini yönetiyorsanız, [sanal makine ölçek kümesi yapılandırmasında Azure Otomasyonu tarafından yönetilen](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)örnek şablona bakın.

### <a name="enable-machines-using-powershell"></a>PowerShell kullanarak makineleri etkinleştirme

VM 'Leri durum yapılandırması için etkinleştirmek üzere PowerShell 'de [register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet 'ini kullanabilirsiniz. 

> [!NOTE]
>`Register-AzAutomationDscNode`Cmdlet yalnızca Windows uzantısını tetiklediği için, yalnızca Windows çalıştıran makineler için uygulanır.

### <a name="register-vms-across-azure-subscriptions"></a>VM 'Leri Azure abonelikleri arasında kaydetme

Diğer Azure aboneliklerinden VM 'Lerin kaydedileceği en iyi yol, Azure Resource Manager dağıtım şablonunda DSC uzantısını kullanmaktır. Örnek olarak, [Istenen durum yapılandırma uzantısında Azure Resource Manager şablonlarıyla birlikte](../virtual-machines/extensions/dsc-template.md)verilmiştir.

Şablonda parametreler olarak kullanılacak kayıt anahtarını ve kayıt URL 'sini bulmak için bkz. [kayıt kullanarak makineleri güvenle etkinleştirin](#enable-machines-securely-using-registration).

## <a name="enable-physicalvirtual-windows-machines"></a>Fiziksel/sanal Windows makinelerini etkinleştir

Şirket içinde veya diğer bulut ortamlarında (AWS EC2 örnekleri dahil) çalışan Windows Server 'ları Azure Otomasyonu durum yapılandırması ' na etkinleştirebilirsiniz. Sunucuların [Azure 'a giden erişimi](automation-dsc-overview.md#network-planning)olmalıdır.

1. Durum yapılandırması için etkinleştirmek üzere makinelerde [WMF 5](https://aka.ms/wmf5latest) ' in en son sürümünün yüklü olduğundan emin olun. Bunlara ek olarak,, makineleri etkinleştirmek için kullandığınız bilgisayarda WMF 5 ' ın yüklü olması gerekir.
1. Gerekli DSC meta yapılandırmalarının bulunduğu bir klasör oluşturmak için [DSC Metaconfigurations oluşturma](#generate-dsc-metaconfigurations) bölümündeki yönergeleri izleyin. 
1. PowerShell DSC metaconfigurations 'ı etkinleştirilecek makinelere uzaktan uygulamak için aşağıdaki cmdlet 'i kullanın. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. PowerShell DSC metayapılandırmalarının uzaktan uygulanmamadıysanız, **metaconfigurations** klasörünü, etkinleştirçalıştığınız makinelere kopyalayın. Ardından, makinelerde yerel olarak [set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) çağırmak için kod ekleyin.
1. Azure portal veya cmdlet 'lerini kullanarak, makinelerin Azure Otomasyonu hesabınızda kayıtlı durum yapılandırma düğümleri olarak göründüğünü doğrulayın.

## <a name="enable-physicalvirtual-linux-machines"></a>Fiziksel/sanal Linux makinelerini etkinleştir

Şirket içinde veya diğer bulut ortamlarında çalışan Linux sunucularını durum yapılandırması için etkinleştirebilirsiniz. Sunucuların [Azure 'a giden erişimi](automation-dsc-overview.md#network-planning)olmalıdır.

1. Durum yapılandırması için etkinleştirmek üzere makinelerde [Linux Için istenen en son PowerShell durum yapılandırması](https://github.com/Microsoft/PowerShell-DSC-for-Linux) sürümünün yüklü olduğundan emin olun.
2. [POWERSHELL DSC yerel Configuration Manager varsayılan](/powershell/scripting/dsc/managing-nodes/metaConfig4) olarak kullanım durumumızdan eşleşiyorsa ve makineleri hem çekme hem de durum yapılandırmasına göre etkinleştirmek istiyorsanız:

   - Etkinleştirilecek her Linux makinesinde, `Register.py` makineyi POWERSHELL DSC yerel Configuration Manager varsayılanlarına etkinleştirmek için kullanın.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Otomasyon hesabınızın kayıt anahtarını ve kayıt URL 'sini bulmak için bkz. [kayıt kullanarak makineleri güvenle etkinleştirin](#enable-machines-securely-using-registration).

3. PowerShell DSC yerel Configuration Manager (LCM) Varsayılanları kullanım durumumuzla eşleşmiyorsa veya yalnızca Azure Otomasyonu durum yapılandırmasına rapor veren makineleri etkinleştirmek istiyorsanız, 4-7 adımlarını izleyin. Aksi takdirde, doğrudan 7. adıma geçin.

4. Gerekli DSC meta yapılandırmalarının bulunduğu bir klasör oluşturmak için [DSC Metaconfigurations oluşturma](#generate-dsc-metaconfigurations) bölümündeki yönergeleri izleyin.

5. Makinelerinizi durum yapılandırması için etkinleştirmek üzere kullanılan bilgisayarda [WMF 5](https://aka.ms/wmf5latest) ' in en son sürümünün yüklü olduğundan emin olun.

6. PowerShell DSC metaconfigurations 'ı etkinleştirilecek makinelere uzaktan uygulamak için aşağıdaki şekilde kod ekleyin.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. PowerShell DSC metaconfigurations 'ı uzaktan uygulayadıysanız, uzak makinelere karşılık gelen metaconfigurations 'ı 4. adım bölümünde açıklanan klasörden Linux makinelere kopyalayın.

8. `Set-DscLocalConfigurationManager.py`Durum yapılandırmasını etkinleştirmek için her bir Linux makinesinde yerel olarak çağrılacak kodu ekleyin.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Azure portal veya cmdlet 'lerini kullanarak, etkinleşen makinelerin Azure Automation hesabınızda kayıtlı DSC düğümü olarak gösterilmesini sağlayın.

## <a name="generate-dsc-metaconfigurations"></a>DSC metaconfigurations oluşturma

Herhangi bir makineyi durum yapılandırması için etkinleştirmek üzere [DSC metaconfiguration](/powershell/scripting/dsc/managing-nodes/metaConfig)oluşturabilirsiniz. Bu yapılandırma DSC aracısının Azure Otomasyonu durum yapılandırması üzerinden ve/veya rapor vermesini söyler. PowerShell DSC yapılandırması ya da Azure Otomasyonu PowerShell cmdlet 'lerini kullanarak Azure Otomasyonu durum yapılandırması için DSC metaconfiguration oluşturabilirsiniz.

> [!NOTE]
> DSC metaconfigurations, bir Otomasyon hesabındaki bir makineyi yönetim için etkinleştirmek üzere gereken gizli dizileri içerir. Oluşturduğunuz DSC metayapılandırmalarının doğru şekilde korunmasını sağlayın veya kullandıktan sonra silin.

Metaconfigurations için proxy desteği, Windows PowerShell DSC altyapısı olan [yerel Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)tarafından denetlenir. LCM, tüm hedef düğümlerde çalışır ve bir DSC metaconfiguration betiğine dahil olan yapılandırma kaynaklarını çağırmadan sorumludur. `ProxyURL` `ProxyCredential` `ConfigurationRepositoryWeb` , `ResourceRepositoryWeb` , Ve blokları için gereken tanımları ve özellikleri ekleyerek bir metaconfiguration 'da proxy desteği ekleyebilirsiniz `ReportServerWeb` . URL ayarına bir örnek `ProxyURL = "http://172.16.3.6:3128";` . `ProxyCredential`Özelliği, `PSCredential` [Azure Automation 'Da kimlik bilgilerini yönetme](shared-resources/credentials.md)bölümünde açıklandığı gibi bir nesne olarak ayarlanır. 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>DSC yapılandırması kullanarak DSC metayapılandırmaları oluşturma

1. VSCode (veya en sevdiğiniz düzenleyiciyi) yerel ortamınızdaki bir makinede yönetici olarak açın. Makinede [WMF 5](https://aka.ms/wmf5latest) ' in en son sürümü yüklü olmalıdır.
1. Aşağıdaki betiği yerel olarak kopyalayın. Bu betik meta yapılandırmalar oluşturmak için bir PowerShell DSC yapılandırması ve metaconfiguration oluşturmayı başlatma komutu içerir.

    > [!NOTE]
    > Durum yapılandırma düğümü yapılandırma adları Azure portal büyük/küçük harfe duyarlıdır. Büyük/küçük harf uyuşayarlanırsa düğüm **düğümler** sekmesinin altında gösterilmez.

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Otomasyon hesabınızın kayıt anahtarını ve URL 'sini, ayrıca etkinleştirilecek makinelerin adlarını da girin. Diğer tüm parametreler isteğe bağlıdır. Otomasyon hesabınızın kayıt anahtarını ve kayıt URL 'sini bulmak için bkz. [kayıt kullanarak makineleri güvenle etkinleştirin](#enable-machines-securely-using-registration).

1. Makinelerin DSC durum bilgilerini Azure Automation durum yapılandırmasına bildirmesini, ancak istek temelli yapılandırma veya PowerShell modülleri olmadığını istiyorsanız, `ReportOnly` parametresini true olarak ayarlayın.

1. `ReportOnly`Ayarlanmamışsa, MAKINELER DSC durum bilgilerini Azure Otomasyonu durum yapılandırması ve çekme yapılandırması ya da PowerShell modüllerine bildirir. Parametreleri,, `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` ve bloklarına göre ayarlayın `ReportServerWeb` .

1. Betiği çalıştırın. Artık, etkinleştirilecek makineler için PowerShell DSC metayapılandırmalarını içeren **Dscmetaconfigs** adlı bir çalışma dizini klasörünüz olmalıdır (yönetici olarak).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Azure Otomasyonu cmdlet 'lerini kullanarak DSC metaconfigurations oluşturma

PowerShell DSC LCM varsayılan değerleri kullanım durumumuzun eşleşiyorsa ve makinelerin Azure Otomasyonu durum yapılandırmasına hem çekme hem de rapor oluşturmasına olanak tanımak istiyorsanız, Azure Otomasyonu cmdlet 'lerini kullanarak gereken DSC metayapılandırmalarını daha da oluşturabilirsiniz.

1. PowerShell konsolunu veya VSCode 'u yerel ortamınızdaki bir makinede yönetici olarak açın.
2. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount)kullanarak Azure Resource Manager bağlanın.
3. Etkinleştirmek istediğiniz makineler için, düğümleri ayarladığınız Otomasyon hesabından PowerShell DSC metaconfigurations ' i indirin.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Artık etkinleştirilecek makineler için PowerShell DSC metayapılandırmalarını içeren bir **Dscmetaconfigs** klasörünüz olmalıdır (yönetici olarak).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Kayıt kullanarak makineleri güvenle etkinleştirin

Bir Azure Otomasyonu hesabına yönelik makineleri WMF 5 DSC kayıt protokolü aracılığıyla güvenli bir şekilde etkinleştirebilirsiniz. Bu protokol, bir DSC düğümünün Azure Otomasyonu durum yapılandırması da dahil olmak üzere bir PowerShell DSC çekme veya rapor sunucusunda kimlik doğrulamasına olanak tanır. Düğüm, kayıt URL 'sindeki sunucuya kaydolur ve kayıt anahtarı kullanarak kimlik doğrular. Kayıt sırasında DSC düğümü ve DSC çekme/rapor sunucusu, düğüm için sunucu kayıt sonrası kimlik doğrulaması için kullanılacak benzersiz bir sertifika üzerinde anlaşır. Bu işlem, etkin düğümlerin bir diğerinin taklit etmesini engeller, örneğin, bir düğüm tehlikeye girerse ve kötü amaçlı olarak davranmışsa. Kayıttan sonra, kayıt anahtarı kimlik doğrulaması için yeniden kullanılmaz ve düğümden silinir.

Azure portal durum yapılandırması kayıt Protokolü için gereken bilgileri, **Hesap ayarları** altındaki **anahtarlar** ' dan edinebilirsiniz. 

![Azure Otomasyonu anahtarları ve URL 'SI](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Kayıt URL 'si, anahtarlar sayfasındaki URL alanıdır.
- Kayıt anahtarı, anahtarlar sayfasındaki **birincil erişim anahtarı** alanının veya **ikincil erişim anahtarı** alanının değeridir. Her iki anahtar de kullanılabilir.

Ek güvenlik için, bir Otomasyon hesabının birincil ve ikincil erişim anahtarlarını anahtarlar sayfasında dilediğiniz zaman yeniden oluşturabilirsiniz. Anahtar yeniden oluşturma, gelecekteki düğüm kayıtlarının önceki anahtarları kullanmasını önler.

## <a name="re-register-a-node"></a>Bir düğümü yeniden kaydetme

Bir makineyi Azure Otomasyonu durum yapılandırması 'nda DSC düğümü olarak kaydettikten sonra, bu düğümü gelecekte yeniden kaydetmeniz gerekebileceği birkaç neden vardır.

- **Sertifika yenileme.** Windows Server 2019 ' den önceki Windows Server sürümleri için her düğüm, bir yıl sonra süresi dolan kimlik doğrulaması için otomatik olarak benzersiz bir sertifika olarak anlaşır. Bir sertifikanın süresi Yenilemesiz olarak dolarsa, düğüm Azure Otomasyonu ile iletişim kuramaz ve işaretlenir `Unresponsive` . Şu anda PowerShell DSC kayıt Protokolü, sertifikaları süresi dolmak üzere otomatik olarak yenileyemez ve bir yılın zamanından sonra düğümleri yeniden kaydetmeniz gerekir. Yeniden kaydolmadan önce, her bir düğümün WMF 5 RTM çalıştırdığından emin olun. 

    Yeniden kayıt, sertifika sona erme zamanından 90 gün veya daha az bir süre önce veya sertifika geçerlilik süresi dolduktan sonra herhangi bir noktada, oluşturulup kullanılmakta olan yeni bir sertifikayla sonuçlanır. Windows Server 2019 ve üzeri sürümlerde bu soruna yönelik bir çözüm bulunur.

- **DSC LCM değerlerinde yapılan değişiklikler.** Örneğin, düğümün ilk kaydı sırasında ayarlanmış [POWERSHELL DSC LCM değerlerini](/powershell/scripting/dsc/managing-nodes/metaConfig4) değiştirmeniz gerekebilir `ConfigurationMode` . Şu anda, bu DSC Aracısı değerlerini yalnızca yeniden kayıt yoluyla değiştirebilirsiniz. Tek istisna, düğüme atanan düğüm yapılandırma değeridir. Bunu Azure Automation DSC doğrudan üzerinde değiştirebilirsiniz.

Bu belgede açıklanan yöntemlerden herhangi birini kullanarak düğümü başlangıçta hemen kaydettiğiniz şekilde yeniden kaydedebilirsiniz. Bir düğümün yeniden kaydolmadan önce Azure Otomasyonu durum yapılandırması 'ndan kaydını kaldırmanız gerekmez.

## <a name="check-status-of-vm-setup"></a>VM kurulumunun durumunu denetleme

Durum Yapılandırması, Azure Windows VM 'lerini yapılandırma yönetimi için kolayca etkinleştirmenizi sağlar. Azure VM 'nin Istenen durum yapılandırma uzantısı, Azure Otomasyonu durum yapılandırması ile VM 'yi kaydetmek için kullanılır. Azure VM Istenen durum yapılandırma uzantısı zaman uyumsuz olarak çalıştığından, ilerleme durumunu izlemek ve yürütme sorunlarını gidermek önemli olabilir.

> [!NOTE]
> Azure VM Istenen durum yapılandırması uzantısının kullanıldığı durum yapılandırması için Azure Windows VM 'Leri etkinleştirme yöntemi, Azure Otomasyonu 'nun VM 'Leri kayıtlı olarak göstermesi için bir saate kadar zaman alabilir. Bu gecikme, VM 'nin durum yapılandırması için VM 'Leri etkinleştirmek için gerekli olan Azure VM Istenen durum yapılandırma uzantısı tarafından sanal makinede WMF 5 yüklemesi nedeniyle oluşur.

Azure VM Istenen durum yapılandırma uzantısının durumunu görüntülemek için:

1. Azure portal, etkinleştirilen VM 'ye gidin.
2. **Ayarlar** altında **Uzantılar** ' a tıklayın. 
3. Şimdi işletim sisteminize bağlı olarak **DSC** veya **dscforlinux**' u seçin. 
4. Daha fazla ayrıntı için **ayrıntılı durumu görüntüle**' ye tıklayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Başlamak için bkz. [Azure Otomasyonu durum yapılandırmasını kullanmaya başlama](automation-dsc-getting-started.md).
- Hedef düğümlere atayabilmeniz için DSC yapılandırmalarını derleme hakkında bilgi edinmek için bkz. [Azure Otomasyonu durum YAPıLANDıRMASıNDA DSC yapılandırmalarını derleme](automation-dsc-compile.md).
- PowerShell cmdlet başvurusu için bkz. [az. Automation](/powershell/module/az.automation).
- Fiyatlandırma bilgileri için bkz. [Azure Otomasyonu durum yapılandırması fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/).
- Azure Otomasyonu durum yapılandırması 'nı sürekli bir dağıtım ardışık düzeninde kullanmanın bir örneği için bkz. [Chocolatey ile sürekli dağıtımı ayarlama](automation-dsc-cd-chocolatey.md).
- Sorun giderme bilgileri için bkz. [Azure Otomasyonu durum yapılandırması sorunlarını giderme](./troubleshoot/desired-state-configuration.md).
