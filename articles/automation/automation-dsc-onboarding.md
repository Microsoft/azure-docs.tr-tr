---
title: Azure Otomasyonu durum yapılandırmasına göre yönetim için makine ekleme
description: Azure Otomasyonu durum yapılandırması ile yönetim için makineleri ayarlama
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: cf95a66cf68cf0b33444a17cf762bae79db4b50c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243430"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Azure Otomasyonu durum yapılandırmasına göre yönetim için makine ekleme

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Azure Otomasyonu durum yapılandırması olan makineleri neden yönetmeliyim?

Azure Otomasyonu durum yapılandırması, herhangi bir bulutta veya şirket içi veri merkezinde DSC düğümleri için bir yapılandırma yönetim hizmetidir.
Merkezi bir güvenli konumdan binlerce makine genelinde hızla ve kolayca ölçeklenebilirlik sunar.
Makineleri kolayca ekleyebilir, bunlara bildirime dayalı yapılandırmalara atayabilir ve her makinenin, belirttiğiniz istenen duruma göre uyumluluğunu gösteren raporları görüntüleyebilirsiniz.
Azure Otomasyonu durum yapılandırma hizmeti, Azure Otomasyonu runbook 'larının PowerShell komut dosyasına ne kadar olduğunu DSC 'ye göre yapılır.
Diğer bir deyişle, Azure Otomasyonu 'nun PowerShell betiklerini yönetmenize yardımcı olduğu şekilde, DSC yapılandırmalarının yönetilmesine de yardımcı olur.
Azure Otomasyonu durum yapılandırması kullanmanın avantajları hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu durum yapılandırmasına genel bakış](automation-dsc-overview.md).

Azure Otomasyonu durum yapılandırması, çeşitli makineleri yönetmek için kullanılabilir:

- Azure sanal makineleri
- Azure sanal makineleri (klasik)
- Amazon Web Services (AWS) EC2 örnekleri
- Şirket içinde veya Azure/AWS dışındaki bir bulutta bulunan fiziksel/sanal Windows makineleri
- Şirket içinde, Azure 'da veya Azure dışındaki bir bulutta bulunan fiziksel/sanal Linux makineleri

Ayrıca, makine yapılandırmasını buluttan yönetmeye hazırsanız, Azure Otomasyonu durum yapılandırması da yalnızca rapor uç noktası olarak kullanılabilir.
Bu, konfigürasyonları DSC aracılığıyla ayarlamanıza ve Azure Otomasyonu 'nda raporlama ayrıntılarını görüntülemenize olanak sağlar.

> [!NOTE]
> Yüklü sanal makine DSC Uzantısı 2,70 ' den büyükse Azure VM 'lerinin durum yapılandırması ile yönetilmesi ek ücret alınmaz. Daha fazla ayrıntı için [**Otomasyon fiyatlandırması sayfasına**](https://azure.microsoft.com/pricing/details/automation/) bakın.

Aşağıdaki bölümlerde, her makine türünü Azure Otomasyonu durum yapılandırmasına nasıl kullanabileceğiniz gösterilmektedir.

## <a name="azure-virtual-machines"></a>Azure sanal makineleri

Azure Otomasyonu durum yapılandırması, Azure portal, Azure Resource Manager şablonlarını veya PowerShell 'i kullanarak Azure sanal makinelerini yapılandırma yönetimi için kolayca eklemenize olanak tanır. Yönetim altında ve bir yönetici sanal makineye uzak olarak gerek duymadan, Azure VM Istenen durum yapılandırma uzantısı VM 'yi Azure Otomasyonu durum yapılandırması 'na kaydeder.
Azure VM Istenen durum yapılandırma uzantısı zaman uyumsuz olarak çalıştığından, ilerleme durumunu izlemek veya sorunu gidermek için gereken adımlar [**Azure sanal makinesi ekleme**](#troubleshooting-azure-virtual-machine-onboarding) bölümünde verilmiştir.

### <a name="azure-portal"></a>Azure portalı

[Azure Portal](https://portal.azure.com/), sanal makineleri eklemek Istediğiniz Azure Otomasyonu hesabına gidin. Durum Yapılandırması sayfasında ve **düğümler** sekmesinde **+ Ekle**' ye tıklayın.

Eklenecek bir Azure sanal makinesi seçin.

Makinede PowerShell istenen durum uzantısı yüklü değilse ve güç durumu çalışıyorsa, **Bağlan**' a tıklayın.

**Kayıt**bölümüne, kullanım durumu Için gereken [PowerShell DSC yerel Configuration Manager değerlerini](/powershell/scripting/dsc/managing-nodes/metaConfig) ve isteğe bağlı olarak VM 'ye atanacak bir düğüm yapılandırmasını girin.

![ekleme](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager şablonları

Azure sanal makineleri, Azure Resource Manager şablonları aracılığıyla Azure Otomasyonu durum yapılandırmasına dağıtılabilir ve eklendi. Var olan bir VM 'yi Azure Otomasyonu durum yapılandırması 'nda izleyen örnek bir şablon için bkz. [Istenen durum yapılandırma hizmeti tarafından yönetilen sunucu](https://azure.microsoft.com/resources/templates/101-automation-configuration/) .
Bir sanal makine ölçek kümesini yönetiyorsanız, [Azure Otomasyonu tarafından yönetilen örnek şablon VM Ölçek kümesi yapılandırması](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)' na bakın.

### <a name="powershell"></a>PowerShell

[Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet 'ı, PowerShell kullanarak Azure 'daki sanal makineleri eklemek için kullanılabilir.
Ancak bu, şu anda yalnızca Windows çalıştıran makineler için geçerlidir (cmdlet yalnızca Windows uzantısını tetikler).

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Azure abonelikleri arasında sanal makineleri kaydetme

Diğer Azure aboneliklerinden sanal makineleri kaydetmek için en iyi yol, Azure Resource Manager dağıtım şablonunda DSC uzantısını kullanmaktır.
Örnek olarak, [Istenen durum yapılandırma uzantısında Azure Resource Manager şablonlarıyla birlikte](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)verilmiştir.
Şablonda parametreler olarak kullanılacak kayıt anahtarını ve kayıt URL 'sini bulmak için, aşağıdaki [**güvenli kayıt**](#secure-registration) bölümüne bakın.

## <a name="amazon-web-services-aws-virtual-machines"></a>Amazon Web Services (AWS) sanal makineler

AWS DSC araç setini kullanarak Azure Otomasyonu durum yapılandırması ile yapılandırma yönetimi için Amazon Web Services sanal makineleri kolayca ekleyebilirsiniz. Araç seti hakkında [buradan](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/)daha fazla bilgi edinebilirsiniz.

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Şirket içinde veya Azure/AWS dışındaki bir bulutta bulunan fiziksel/sanal Windows makineleri

Şirket içinde veya diğer bulut ortamlarında çalışan Windows Server 'lar, [Azure 'a giden erişimi](automation-dsc-overview.md#network-planning)olduğu sürece Azure Automation durum yapılandırması eklendi de olabilir:

1. Azure Otomasyonu durum yapılandırması 'na eklemek istediğiniz makinelerde [WMF 5](https://aka.ms/wmf5latest) ' in en son sürümünün yüklü olduğundan emin olun.
1. Gerekli DSC meta yapılandırmalarının bulunduğu bir klasör oluşturmak için [**DSC Metaconfigurations oluşturma**](#generating-dsc-metaconfigurations) bölümündeki yönergeleri izleyin.
1. PowerShell DSC metaconfiguration 'ı eklemek istediğiniz makinelere uzaktan uygulayın. **Bu komutun çalıştırıldığı makinenin en son [WMF 5](https://aka.ms/wmf5latest) sürümü yüklü olmalıdır**:

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. PowerShell DSC metaconfigurations 'ı uzaktan uygulayamadıysanız, meta veri Yapılandırması klasörünü 2. adımdaki her bir makineye kopyalayın. Ardından, eklemek üzere her makinede yerel olarak **set-DscLocalConfigurationManager** çağırın.
1. Azure portal veya cmdlet 'lerini kullanarak, artık eklenecek makinelerin Azure Automation hesabınızda kayıtlı durum yapılandırması düğümleri olduğunu kontrol edin.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Şirket içinde veya Azure dışındaki bir bulutta bulunan fiziksel/sanal Linux makineleri

Şirket içinde veya diğer bulut ortamlarında çalışan Linux sunucuları, [Azure 'a giden erişimi](automation-dsc-overview.md#network-planning)olduğu sürece Azure Automation durum yapılandırması eklendi de olabilir:

1. Azure Otomasyonu durum yapılandırması 'na eklemek istediğiniz makinelere [Linux Için Istenen PowerShell Istenen durum yapılandırmasının](https://github.com/Microsoft/PowerShell-DSC-for-Linux) en son sürümünün yüklü olduğundan emin olun.
1. [POWERSHELL DSC yerel Configuration Manager varsayılan](/powershell/scripting/dsc/managing-nodes/metaConfig4) olarak kullanım durumumızdan eşleşiyorsa ve makineleri hem çekme hem **de** Azure Otomasyonu durum yapılandırmasına raporla birlikte eklemek istiyorsanız:

   - Azure Otomasyonu durum yapılandırmasına eklenecek her bir Linux makinesinde, PowerShell DSC yerel Configuration Manager varsayılan değerlerini kullanarak eklemek için `Register.py` kullanın:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Otomasyon hesabınızın kayıt anahtarını ve kayıt URL 'sini bulmak için aşağıdaki [**güvenli kayıt**](#secure-registration) bölümüne bakın.

     PowerShell DSC yerel **Configuration Manager Varsayılanları kullanım** durumumuzla eşleşmezse veya yalnızca Azure Otomasyonu durum yapılandırmasına rapor veren makineler eklemek istiyorsanız, 3-6 adımlarını izleyin. Aksi takdirde, doğrudan 6. adıma geçin.

1. Gerekli DSC metayapılandırmalarının bulunduğu bir klasör oluşturmak için aşağıdaki [**DSC Metaconfigurations oluşturma**](#generating-dsc-metaconfigurations) bölümündeki yönergeleri izleyin.
1. PowerShell DSC metaconfiguration 'ı eklemek istediğiniz makinelere uzaktan uygulayın:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Bu komutun çalıştırıldığı makinenin en son [WMF 5](https://aka.ms/wmf5latest) sürümü yüklü olmalıdır.

1. PowerShell DSC metayapılandırmalarını uzaktan uygulayadıysanız, bu makineye karşılık gelen metaconfiguration 'ı Linux makinesine 5. adımdaki klasörden kopyalayın. Ardından Azure Otomasyonu durum yapılandırması 'na eklemek istediğiniz her bir Linux makinesinde `SetDscLocalConfigurationManager.py` olarak çağırın:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Azure portal veya cmdlet 'lerini kullanarak, artık eklenecek makinelerin Azure Automation hesabınızda kayıtlı DSC düğümü olarak gösterilmesini kontrol edin.

## <a name="generating-dsc-metaconfigurations"></a>DSC metaconfigurations oluşturma

Azure Otomasyonu durum yapılandırmasına herhangi bir makineyi genel olarak eklemek için DSC aracısının Azure Otomasyonu durum yapılandırması üzerinden ve/veya rapor vermesini bildiren bir [DSC metaconfiguration](/powershell/scripting/dsc/managing-nodes/metaConfig) oluşturulabilir. Azure Otomasyonu durum yapılandırması için DSC metaconfigurations, PowerShell DSC yapılandırması veya Azure Otomasyonu PowerShell cmdlet 'leri kullanılarak oluşturulabilir.

> [!NOTE]
> DSC metaconfigurations, yönetim için bir Otomasyon hesabına makine eklemek için gereken gizli dizileri içerir. Oluşturduğunuz DSC metayapılandırmalarının doğru şekilde korunmasını sağlayın veya kullandıktan sonra silin.

### <a name="using-a-dsc-configuration"></a>DSC yapılandırması kullanma

1. VSCode (veya en sevdiğiniz düzenleyiciyi) yerel ortamınızdaki bir makinede yönetici olarak açın. Makinede [WMF 5](https://aka.ms/wmf5latest) ' in en son sürümü yüklü olmalıdır.
1. Aşağıdaki betiği yerel olarak kopyalayın. Bu betik meta yapılandırmalar oluşturmak için bir PowerShell DSC yapılandırması ve metaconfiguration oluşturmayı başlatma komutu içerir.

> [!NOTE]
> Durum yapılandırma düğümü yapılandırma adları portalda büyük/küçük harfe duyarlıdır. Büyük/küçük harf yanlış olursa düğüm **düğümler** sekmesinin altında gösterilmez.

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

1. Otomasyon hesabınızın kayıt anahtarını ve URL 'sini, eklenecek makinelerin adlarını da girin. Diğer tüm parametreler isteğe bağlıdır. Otomasyon hesabınızın kayıt anahtarını ve kayıt URL 'sini bulmak için aşağıdaki [**güvenli kayıt**](#secure-registration) bölümüne bakın.
1. Makinelerin DSC durum bilgilerini Azure Automation durum yapılandırması 'na bildirmesini, ancak çekme yapılandırması ya da PowerShell modüllerini bildirmesini istiyorsanız, **reportonly** parametresini true olarak ayarlayın.
1. Betiği çalıştırın. Artık çalışma dizininizde **Dscmetaconfigs** adlı bir klasörünüz olmalıdır ve bu, eklenecek makinelere YÖNELIK PowerShell DSC metayapılandırmalarını (yönetici olarak) içerir:

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Azure Otomasyonu cmdlet 'lerini kullanma

PowerShell DSC yerel Configuration Manager Varsayılanları kullanım durumumızdan eşleşiyorsa ve her ikisi de Azure Otomasyonu durum yapılandırmasına ve raporlamalarıyla ilgili makineler eklemek istiyorsanız, Azure Otomasyonu cmdlet 'leri basit bir oluşturma yöntemi sağlar gereken DSC metaıd yapılandırması:

1. PowerShell konsolunu veya VSCode 'u yerel ortamınızdaki bir makinede yönetici olarak açın.
2. @No__t kullanarak Azure Resource Manager bağlanma-0
3. Düğüm eklemek istediğiniz Otomasyon hesabından eklemek istediğiniz makineler için PowerShell DSC metaconfigurations 'ı indirin:

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Artık ***Dscmetaconfigs***adlı bir klasörünüz olmalıdır ve eklenecek makineler IÇIN PowerShell DSC metayapılandırmalarını (yönetici olarak) içeren bir klasöre sahipsiniz:

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Güvenli kayıt

Makineler, bir DSC düğümünün bir PowerShell DSC çekme veya Raporlama sunucusunda (Azure Automation durum yapılandırması dahil) kimlik doğrulamasına izin veren WMF 5 DSC kayıt protokolü aracılığıyla bir Azure Otomasyonu hesabına güvenli bir şekilde eklenebilir. Düğüm, kayıt **anahtarı**kullanarak kimlik doğrulaması yapan BIR **kayıt URL**'sindeki sunucuya kaydolur. Kayıt sırasında DSC düğümü ve DSC çekme/raporlama sunucusu, bu düğüm için sunucu kayıt sonrası kimlik doğrulaması için kullanılacak benzersiz bir sertifika üzerinde anlaşır. Bu işlem, bir düğümün tehlikeye düşmesi ve kötü amaçlı olarak davranması gibi eklendi düğümlerinin bir diğerinin taklit etmesini engeller. Kayıttan sonra, kayıt anahtarı kimlik doğrulaması için yeniden kullanılmaz ve düğümden silinir.

Azure portal durum yapılandırması kayıt Protokolü için gereken bilgileri, **Hesap ayarları** altındaki **anahtarlar** ' dan edinebilirsiniz. Otomasyon hesabı için **temel** Bileşenler panelinde anahtar simgesine tıklayarak bu dikey pencereyi açın.

![Azure Otomasyonu anahtarları ve URL 'SI](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Kayıt URL 'si, anahtarları Yönet dikey penceresindeki URL alanıdır.
- Kayıt anahtarı, anahtarları Yönet dikey penceresindeki birincil erişim anahtarı veya Ikincil erişim anahtarıdır. Her iki anahtar de kullanılabilir.

Ek güvenlik için, bir Otomasyon hesabının birincil ve ikincil erişim anahtarları, önceki anahtarları kullanarak gelecekteki düğüm kayıtlarını engellemek için herhangi bir zamanda ( **anahtarları Yönet** sayfasında) yeniden oluşturulabilir.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Azure sanal makinesi ekleme sorunlarını giderme

Azure Otomasyonu durum yapılandırması, Azure Windows VM 'lerini yapılandırma yönetimi için kolayca eklemenizi sağlar. Azure VM 'nin Istenen durum yapılandırma uzantısı, Azure Otomasyonu durum yapılandırması ile VM 'yi kaydetmek için kullanılır. Azure VM Istenen durum yapılandırma uzantısı zaman uyumsuz olarak çalıştığından, ilerleme durumunu izlemek ve yürütme sorunlarını gidermek önemli olabilir.

> [!NOTE]
> Azure VM Istenen durum yapılandırması uzantısını kullanan Azure Otomasyonu durum yapılandırmasına Azure Windows VM 'yi ekleme yöntemi, düğümün Azure Otomasyonu 'nda kayıtlı olarak gösterilmesi için bir saate kadar sürebilir. Bunun nedeni, sanal makinenin Azure Automation durum yapılandırmasına eklenmesi için gereken Azure VM DSC Uzantısı tarafından VM üzerinde Windows Management Framework 5,0 ' nin yüklenmesiyle kaynaklanmaktadır.

Azure VM Istenen durum yapılandırma uzantısının durumunu sorun gidermek veya görüntülemek için Azure portal VM 'nin eklendi olduğu sanal makineye gidin ve **Ayarlar**altında **Uzantılar** ' a tıklayın. Ardından işletim sisteminize bağlı olarak **DSC** veya **dscforlinux** ' a tıklayın. Daha fazla ayrıntı için **ayrıntılı durumu görüntüle**' ye tıklayabilirsiniz.

## <a name="certificate-expiration-and-reregistration"></a>Sertifika süre sonu ve yapılabilir

Bir makineyi Azure Otomasyonu durum yapılandırması 'nda DSC düğümü olarak kaydettikten sonra, bu düğümü ileride yeniden kaydetmeniz gerekebilecek bazı nedenler vardır:

- Windows Server 2019 ' den önceki Windows Server sürümleri için her düğüm, bir yıldan sonra süresi dolan kimlik doğrulaması için otomatik olarak benzersiz bir sertifika sağlar. Şu anda PowerShell DSC kayıt Protokolü, süresi dolmak üzere olan sertifikaları otomatik olarak yenileyemez. bu nedenle, bir yılın zamanından sonra düğümleri yeniden kaydetmeniz gerekir. Reregistering önce, her bir düğümün Windows Management Framework 5,0 RTM çalıştırdığından emin olun. Bir düğümün kimlik doğrulama sertifikasının süresi dolarsa ve düğüm yeniden kaydettirilmediyse, düğüm Azure Otomasyonu ile iletişim kuramaz ve ' yanıt vermez ' olarak işaretlenir. Yapılabilir, sertifika sona erme zamanından 90 gün veya daha az bir süre sonra veya sertifika sona erme zamanından sonra herhangi bir noktada gerçekleştirilen yeni bir sertifika oluşturulmasını ve kullanılmasını neden olur.  Windows Server 2019 ve üzeri sürümlerde bu soruna yönelik bir çözüm bulunur.
- Bir [POWERSHELL DSC yerel Configuration Manager değerini](/powershell/scripting/dsc/managing-nodes/metaConfig4) , düğümün ilk kaydı sırasında ayarlanan configurationmode gibi değiştirmek için. Şu anda, bu DSC Aracısı değerleri yalnızca yapılabilir üzerinden değiştirilebilir. Tek istisna, düğüme atanan düğüm yapılandırmadır; bu, Azure Automation DSC doğrudan değiştirilebilir.

Yapılabilir, bu belgede açıklanan ekleme yöntemlerinden herhangi birini kullanarak ilk olarak düğümü kaydettiğiniz şekilde gerçekleştirilebilir. Reregistering önce Azure Automation durum yapılandırmasından bir düğümün kaydını kaldırmanız gerekmez.

## <a name="next-steps"></a>Sonraki adımlar

- Başlamak için bkz. [Azure Otomasyonu durum yapılandırması ile çalışmaya](automation-dsc-getting-started.md) başlama
- Hedef düğümlere atayabilmeniz için DSC yapılandırmalarını derleme hakkında bilgi edinmek için bkz. [Azure Otomasyonu durum yapılandırmasında yapılandırmaları derleme](automation-dsc-compile.md)
- PowerShell cmdlet başvurusu için bkz. [Azure Otomasyonu durum yapılandırması cmdlet 'leri](/powershell/module/az.automation#automation)
- Fiyatlandırma bilgileri için bkz. [Azure Otomasyonu durum yapılandırması fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/)
- Azure Otomasyonu durum yapılandırması 'nı sürekli bir dağıtım ardışık düzeninde kullanmaya ilişkin bir örnek görmek için bkz. [Azure Otomasyonu durum yapılandırması ve Chocolatey kullanarak sürekli dağıtım](automation-dsc-cd-chocolatey.md)
