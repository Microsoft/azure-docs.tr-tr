---
title: Chocolatey ile Azure Otomasyonu durum yapılandırması sürekli dağıtımı
description: Azure Otomasyonu durum yapılandırması, DSC ve Chocolatey Paket Yöneticisi kullanarak DevOps sürekli dağıtımı.  Tam JSON Kaynak Yöneticisi şablonu ve PowerShell kaynağı içeren örnek.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f4512b79873d7f770b32a452a02c53bc5575bdac
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243595"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Kullanım örneği: Automation durum yapılandırması ve Chocolatey kullanarak sanal makinelere sürekli dağıtım

DevOps dünyasında, sürekli tümleştirme ardışık düzeninde çeşitli noktalarda yardımcı olacak birçok araç vardır. Azure Otomasyonu durum yapılandırması, DevOps takımlarının kullanabildiği seçeneklere yeni bir hoş geldiniz. Bu makalede bir Windows bilgisayarı için sürekli dağıtımı (CD) ayarlama gösterilmektedir. Yöntemi, rol (bir Web sitesi gibi) ve ek roller de dahil olmak üzere gerektiği kadar çok sayıda Windows bilgisayarı içerecek şekilde kolayca genişletebilirsiniz.

![IaaS VM 'Leri için sürekli dağıtım](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Yüksek düzeyde

Burada oldukça bir bit vardır ancak iki ana işleme ayrılabilir:

- Kod yazma ve test etme, sonra sistemin birincil ve ikincil sürümleri için yükleme paketleri oluşturma ve yayımlama.
- Paketlerdeki kodu yükleyecek ve yürüteceği VM 'Leri oluşturma ve yönetme.  

Bu temel işlemlerin her ikisi de gerçekleştikten sonra, yeni sürümler oluşturulup dağıtıldığı sürece belirli bir VM 'de çalışan paketi otomatik olarak güncellemek için kısa bir adımdır.

## <a name="component-overview"></a>Bileşene genel bakış

[Apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) gibi paket yöneticileri, Linux dünyasında oldukça iyi bilinir, ancak Windows dünyasında çok daha fazla değildir.
[Chocolatey](https://chocolatey.org/) böyle bir şeydir ve konu başlığı üzerinde Scott Hanselman 'ın [blogu](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) harika bir giriş. Bir Nutshell 'de, Chocolatey bir paket deposundaki paketleri komut satırını kullanarak bir Windows sistemine yüklemenize izin verir. Kendi deponuzu oluşturabilir ve yönetebilirsiniz ve Chocolatey, belirlediğiniz sayıda depodaki paketleri yükleyebilir.

İstenen Durum Yapılandırması (DSC) ([genel bakış](/powershell/scripting/dsc/overview/overview)) bir makine için istediğiniz yapılandırmayı bildirmenize olanak sağlayan bir PowerShell aracıdır. Örneğin, "Chocolatey yüklenmesini istiyorum, IIS 'nin yüklenmesini istiyorum, bağlantı noktası 80 ' ün açılmasını istiyorum, Web sitemin sürüm 1.0.0 'ın yüklü olmasını istiyorum" deyin. DSC yerel Configuration Manager (LCM) bu yapılandırmayı uygular. DSC çekme sunucusu, makineleriniz için bir yapılandırma deposu tutar. Her makinedeki LCM, yapılandırmasının depolanan yapılandırmayla eşleşip eşleşmediğini görmek için düzenli aralıklarla kontrol eder. Bu durum, durumu bildirebilir veya depolanan yapılandırma ile makineyi yeniden hizalı hale getirmeyi deneyebilir. Bir makine veya makine kümesinin değiştirilen yapılandırmayla hizalı olmasına neden olmak için, istek sunucusunda depolanan yapılandırmayı düzenleyebilirsiniz.

Azure Otomasyonu, runbook 'ları, düğümleri, kimlik bilgilerini, kaynakları ve zamanlamalar ve genel değişkenler gibi varlıkları kullanarak çeşitli görevleri otomatikleştirmenizi sağlayan Microsoft Azure yönetilen bir hizmettir.
Azure Otomasyonu durum yapılandırması, bu Otomasyon özelliğini PowerShell DSC araçları içerecek şekilde genişletir. Harika bir [genel bakış](automation-dsc-overview.md).

DSC kaynağı, ağ, Active Directory veya SQL Server yönetimi gibi belirli özellikleri olan bir kod modüldür. Chocolatey DSC kaynağı bir NuGet sunucusuna erişmeyi (diğerleri arasında), paketleri indirmeyi, paketleri yüklemeyi vb. bilir. [PowerShell Galerisi](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)DIĞER birçok DSC kaynağı vardır.
Bu modüller, Azure Otomasyonu durum yapılandırması çekme sunucunuza (sizin tarafınızdan) yüklenir ve bu sayede yapılandırmalar tarafından kullanılabilirler.

Kaynak Yöneticisi şablonlar; ağlar, alt ağlar, ağ güvenliği ve yönlendirme, yük dengeleyiciler, NIC 'Ler, VM 'Ler vb. gibi altyapınızı oluşturmak için bildirim temelli bir yol sağlar. Azure hizmet yönetimi (ASM veya klasik) dağıtım modeli (zorunlu) ile Kaynak Yöneticisi dağıtım modelini (beyan) karşılaştıran ve çekirdek kaynak sağlayıcıları, işlem, depolama ve ağ ile ilgili bir [Makale](../azure-resource-manager/resource-manager-deployment-model.md) aşağıda açıklanmaktadır.

Kaynak Yöneticisi şablonun bir anahtar özelliği, sağlanan VM 'ye bir VM Uzantısı yükleyebilmesidir. Bir VM uzantısının özel bir komut dosyası çalıştırma, virüsten koruma yazılımı yükleme veya bir DSC yapılandırma betiği çalıştırma gibi belirli yetenekleri vardır. Birçok farklı VM uzantısı türü vardır.

## <a name="quick-trip-around-the-diagram"></a>Diyagram etrafında hızlı seyahat

En üstten başlayarak kodunuzu yazar, oluşturup test edin ve ardından bir yükleme paketi oluşturun.
Chocolatey, MSI, MSU, ZIP gibi çeşitli yükleme paketi türlerini işleyebilir. Ayrıca, Chocolateys Native özellikleri tam olarak BT değilse gerçek yüklemeyi yapmak için PowerShell 'in tam gücünden yararlanabilirsiniz. Paketi bir paket deposu erişilebilir bir yere yerleştirin. Bu kullanım örneği, bir Azure Blob depolama hesabında ortak bir klasör kullanır, ancak herhangi bir yerde olabilir. Chocolatey, NuGet sunucularıyla yerel olarak, diğer bir deyişle paket meta verisinin yönetimi için de kullanılır. [Bu makalede](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) seçenekler açıklanmaktadır. Bu kullanım örneği NuGet kullanır. Bir Nuspec, paketleriniz hakkındaki meta verilerlerdir. Nuspec 'ler, NuPkg 'da "derlenir" ve bir NuGet sunucusunda depolanır. Yapılandırmanız adına göre bir paket istediğinde ve bir NuGet sunucusuna başvurduğunda, Chocolatey DSC kaynağı (Şu anda VM 'de) paketi ve sizin için yüklerse. Ayrıca, bir paketin belirli bir sürümünü isteyebilirsiniz.

Resmin sol alt kısmında bir Azure Resource Manager şablonu vardır. Bu kullanım örneğinde, VM Uzantısı VM 'yi Azure Otomasyonu durum yapılandırması çekme sunucusuna (yani bir çekme sunucusu) düğüm olarak kaydeder. Yapılandırma, çekme sunucusunda depolanır.
Aslında, iki kez depolanır: bir kez düz metin, ve bir MOF dosyası olarak derlendikten sonra (böyle şeyler hakkında bilgi sahibi olanlar için). Portalda MOF bir "düğüm yapılandırması" (yalnızca "yapılandırma" ın aksine) olur. Bu, düğümün yapılandırmasını bilmesi için bir düğümle ilişkili yapıtı. Aşağıdaki ayrıntılar, düğüm yapılandırmasını düğüme atamayı gösterir.

Zaten en üstte veya en çok bir bit daha gelişiyoruz. Nuspec 'in oluşturulması, derlenmesi ve bir NuGet sunucusunda depolanması küçük bir şeydir. VM 'Leri zaten yönetiyorsunuz. Sonraki adımı sürekli dağıtım için almak, çekme sunucusunu ayarlamayı (bir kez), düğümlerinizi (bir kez) kaydederek (başlangıçta) yapılandırmayı oluşturup depolamayı gerektirir. Paketler yükseltilene ve depoya dağıtıldıktan sonra, çekme sunucusunda yapılandırma ve düğüm yapılandırmasını yenileyin (gerektiğinde yineleyin).

Kaynak Yöneticisi şablonuyla başlıyorsanız de bu da Tamam ' a tıklayın. VM 'lerinizi çekme sunucusu ve tüm rest ile kaydetmenize yardımcı olmak üzere tasarlanan PowerShell cmdlet 'leri vardır. Daha fazla ayrıntı için bkz. [Azure Otomasyonu durum yapılandırmasına göre yönetim için makineleri ekleme](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>1\. Adım: çekme sunucusunu ve otomasyon hesabını ayarlama

Kimliği doğrulanmış (`Connect-AzureRmAccount`) PowerShell komut satırı: (çekme sunucusu ayarlandığında birkaç dakika sürebilir)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Otomasyon hesabınızı şu bölgelerden birine (diğer adıyla) koyabilirsiniz: Doğu ABD 2, Orta Güney ABD, US Gov Virginia, Batı Avrupa, Güneydoğu Asya, Japonya Doğu, Orta Hindistan ve Avustralya Güneydoğu, Kanada Orta Kuzey Avrupa.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>2\. Adım: Kaynak Yöneticisi şablonuna yönelik VM Uzantısı tnak 'leri

Bu [Azure hızlı başlangıç şablonunda](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)belirtilen VM kaydı (POWERSHELL DSC VM uzantısı kullanılarak) için ayrıntılar.
Bu adım, yeni VM 'nizi durum yapılandırması düğümleri listesindeki çekme sunucusuna kaydeder. Bu kaydın bir parçası, düğüme uygulanacak düğüm yapılandırmasını belirtmektir. Bu düğüm yapılandırmasının çekme sunucusunda mevcut olması gerekmez, bu nedenle adım 4 ' ün ilk kez yapıldığı yerde olması gerekir. Ancak adım 2 ' de, düğüm adına ve yapılandırmanın adına karar vermiş olmanız gerekir. Bu kullanım örneğinde, düğüm ' isvbox ' ve yapılandırma ' ISVBoxConfig '. Bu nedenle, düğüm yapılandırma adı (DeploymentTemplate. JSON içinde belirtilmelidir) ' ISVBoxConfig. isvbox '.

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>3\. Adım: gerekli DSC kaynaklarını çekme sunucusuna ekleme

PowerShell Galerisi, DSC kaynaklarını Azure Otomasyonu hesabınıza yüklemek için işaretlenir.
İstediğiniz kaynağa gidin ve "Azure Otomasyonu 'na dağıt" düğmesine tıklayın.

![PowerShell Galerisi örneği](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Azure portalına yakın zamanda eklenen başka bir teknik, yeni modüller almanıza veya mevcut modülleri güncelleştirmenize olanak tanır. Otomasyon hesabı kaynağına, varlıklar kutucuğuna ve son olarak modüller kutucuğuna tıklayın. Galeriye göz at simgesi, galerideki modüllerin listesini görmenizi, Ayrıntılar halinde detaya gitmeyi ve sonuç olarak otomasyon hesabınıza aktarmayı sağlar. Bu, modüllerinizi zaman zaman güncel tutmanın harika bir yoludur. Ve içeri aktarma özelliği, hiçbir şeyin eşitlenmemiş olmamasını sağlamak için diğer modüllerle bağımlılıkları denetler.

Ya da el ile yaklaşım vardır. Bir Windows bilgisayarı için PowerShell tümleştirme modülünün klasör yapısı, Azure Otomasyonu tarafından beklenen klasör yapısından biraz farklıdır.
Bu, bölümlük için biraz daha fazla alan gerektirir. Ancak bu zor değildir ve kaynak başına yalnızca bir kez yapılır (gelecekte yükseltmek istemediğiniz durumlar dışında). PowerShell tümleştirme modüllerini yazma hakkında daha fazla bilgi için şu makaleye bakın: [Azure Otomasyonu Için tümleştirme modülleri yazma](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- İş istasyonunuzda gereken modülü aşağıdaki şekilde yükleyebilirsiniz:
  - [Windows Management Framework](https://aka.ms/wmf5latest) 'Ü (Windows 10 için gerekli değildir) yükler
  - `Install-Module –Name MODULE-NAME` < — modülü PowerShell Galerisi dönüştürür
- Modül klasörünü `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` ' dan geçici bir klasöre kopyalayın
- Ana klasörden örnek ve belge silme
- Ana klasör olarak, ZIP dosyasını tamamen klasörle aynı olarak adlandırarak 
- ZIP dosyasını, bir Azure depolama hesabındaki BLOB depolama gibi erişilebilir bir HTTP konumuna koyun.
- Bu PowerShell 'i Çalıştır:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

Dahil edilen örnek, cChoco ve xNetworking için bu adımları gerçekleştirir. CChoco için özel işleme için [notlara](#notes) bakın.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>4\. Adım: düğüm yapılandırmasını çekme sunucusuna ekleme

Yapılandırmanızı çekme sunucusuna ilk kez aktardığınızda ve derlemenizde özel bir şey yoktur. Aynı yapılandırmanın sonraki tüm içeri aktarma/derleme işlemi tamamen aynı şekilde görünür. Paketinizi her güncelleştirdiğinizde ve üretime göndermeniz gerektiğinde, paketinizin yeni sürümü de dahil olmak üzere, yapılandırma dosyasının doğru olduğundan emin olduktan sonra bu adımı gerçekleştirebilirsiniz. Yapılandırma dosyası ve PowerShell şu şekildedir:

ISVBoxConfig. ps1:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

New-ConfigurationScript. ps1:

```powershell
Import-AzureRmAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Bu adımlar, çekme sunucusuna yerleştirilmiş "ISVBoxConfig. isvbox" adlı yeni bir düğüm yapılandırmasının oluşmasına neden olacak. Düğüm yapılandırma adı "configurationName. Düğadı" olarak oluşturulur.

## <a name="step-5-creating-and-maintaining-package-metadata"></a>5\. Adım: paket meta verilerini oluşturma ve sürdürme

Paket deposuna yerleştirdiğiniz her bir paket için, onu açıklayan bir nuspec gerekir.
Bu nuspec 'in NuGet sunucunuzda derlenmesi ve depolanması gerekir. Bu işlem [burada](https://docs.nuget.org/create/creating-and-publishing-a-package)açıklanmıştır. MyGet.org 'i bir NuGet sunucusu olarak kullanabilirsiniz. Bu hizmeti satmakla kalmaz, ücretsiz bir başlatıcı SKU 'SU vardır. NuGet.org adresinde, özel paketleriniz için kendi NuGet sunucunuzu yükleme yönergelerini bulacaksınız.

## <a name="step-6-tying-it-all-together"></a>6\. Adım: tümünü bir araya bağlama

Bir sürüm QA 'i her geçtiğinde ve dağıtım için onaylandığında, paket oluşturulur, nuspec ve nupkg güncelleştirilir ve NuGet sunucusuna dağıtılır. Ayrıca, yapılandırmanın (yukarıdaki 4. adım) yeni sürüm numarasını kabul etmek için güncelleştirilmeleri gerekir. Çekme sunucusuna gönderilmesi ve derlenmesi gerekir.
Bu noktadan itibaren, güncelleştirmeyi çekmek ve yüklemek için bu yapılandırmaya bağlı olan sanal makinelere bağımlıdır. Bu güncelleştirmelerin her biri basit-yalnızca bir çizgi veya PowerShell 'in ikisi vardır. Azure DevOps söz konusu olduğunda, bazıları bir derlemede birlikte zincirlenebilir derleme görevlerinde kapsüllenir. Bu [makalede](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) daha fazla ayrıntı sağlanmaktadır. Bu [GitHub deposu](https://github.com/Microsoft/vso-agent-tasks) , kullanılabilir çeşitli yapı görevlerinin ayrıntılarını oluşturur.

## <a name="notes"></a>Notlar

Bu kullanım örneği, Azure galerisinden genel bir Windows Server 2012 R2 görüntüsünden VM ile başlar. Depolanan herhangi bir görüntüden başlayabilir ve ardından DSC yapılandırması ile ince ayar.
Ancak, bir görüntüye bakan yapılandırmanın değiştirilmesi, DSC kullanılarak yapılandırmanın dinamik olarak güncelleştirilmekten çok daha zordur.

Bu tekniği sanal makinelerinize kullanabilmek için Kaynak Yöneticisi şablonu ve VM uzantısını kullanmanız gerekmez. Ve sanal makinelerinizin, CD yönetimi altında olması için Azure 'da olması gerekmez. Bu, Chocolatey ' nin yüklenmesi ve sanal makine üzerinde, istek sunucusunun nerede olduğunu bilmesi için yapılandırılmış LCM ' dir.

Kuşkusuz, üretimde bir sanal makinede bulunan bir paketi güncelleştirdiğinizde, güncelleştirme yüklenirken o VM 'yi döndürme dışında getirmeniz gerekir. Bunu nasıl yapabileceğiniz çok farklılık vardır. Örneğin, bir Azure Load Balancer arkasındaki bir VM ile özel bir araştırma ekleyebilirsiniz. VM güncelleştirilirken, araştırma uç noktasının bir 400 döndürmesini sağlayabilirsiniz. Bu değişikliğe neden olması için gerekli olan ince ayar, güncelleştirme tamamlandıktan sonra, ince ayar geri dönmek üzere bir 200 döndürmek için, bu değişikliğin içinde olabilir.

Bu kullanım örneği için tam kaynak, GitHub 'daki [Bu Visual Studio projem](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) .

## <a name="related-articles"></a>İlgili makaleler
* [Azure Automation DSC genel bakış](automation-dsc-overview.md)
* [Azure Automation DSC cmdlet 'leri](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Azure Automation DSC göre yönetim için makineleri ekleme](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [Azure Otomasyonu durum yapılandırması](automation-dsc-overview.md)
- Başlamak için bkz. [Azure Otomasyonu durum yapılandırması ile çalışmaya](automation-dsc-getting-started.md) başlama
- Hedef düğümlere atayabilmeniz için DSC yapılandırmalarını derleme hakkında bilgi edinmek için bkz. [Azure Otomasyonu durum yapılandırmasında yapılandırmaları derleme](automation-dsc-compile.md)
- PowerShell cmdlet başvurusu için bkz. [Azure Otomasyonu durum yapılandırması cmdlet 'leri](/powershell/module/azurerm.automation/#automation)
- Fiyatlandırma bilgileri için bkz. [Azure Otomasyonu durum yapılandırması fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/)
- Azure Otomasyonu durum yapılandırması 'nı sürekli bir dağıtım ardışık düzeninde kullanmaya ilişkin bir örnek görmek için bkz. [Azure Otomasyonu durum yapılandırması ve Chocolatey kullanarak sürekli dağıtım](automation-dsc-cd-chocolatey.md)
