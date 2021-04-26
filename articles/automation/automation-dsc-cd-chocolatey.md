---
title: Chocolatey ile Azure Otomasyonu sürekli dağıtımını ayarlama
description: Bu makalede durum yapılandırması ve Chocolatey Paket Yöneticisi ile sürekli dağıtımı nasıl ayarlayacakları açıklanır.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 717561614a3e42995bbce6746839fd9b7cbca37e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834867"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Chocolatey ile sürekli dağıtım ayarlama

DevOps dünyasında, sürekli tümleştirme ardışık düzeninde çeşitli noktalarda yardımcı olacak birçok araç vardır. Azure Otomasyonu [Durum Yapılandırması](automation-dsc-overview.md) , DevOps takımlarının kullanabildiği seçeneklere yeni bir hoş geldiniz. 

Azure Otomasyonu, kimlik bilgileri, zamanlamalar ve genel değişkenler gibi runbook 'ları, düğümleri ve paylaşılan kaynakları kullanarak çeşitli görevleri otomatikleştirmenizi sağlayan Microsoft Azure yönetilen bir hizmettir. Azure Otomasyonu durum yapılandırması, bu Otomasyon özelliğini PowerShell Istenen durum yapılandırması (DSC) araçlarını içerecek şekilde genişletir. Harika bir [genel bakış](automation-dsc-overview.md).

Bu makalede, bir Windows bilgisayarı için sürekli dağıtımı (CD) ayarlama gösterilmektedir. Yöntemi, rol için gereken sayıda Windows bilgisayarı (örneğin, bir Web sitesi) dahil etmek için ve ek rollere giderek daha kolay bir şekilde genişletebilirsiniz.

![IaaS VM 'Leri için sürekli dağıtım](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Yüksek düzeyde

Burada oldukça bir bit vardır ancak iki ana işleme ayrılabilir:

- Kod yazma ve test etme, sonra sistemin birincil ve ikincil sürümleri için yükleme paketleri oluşturma ve yayımlama.
- Paketlerdeki kodu yükleyen ve çalıştıran VM 'Leri oluşturma ve yönetme.  

Bu temel işlemlerin her ikisi de kurulduktan sonra, yeni sürümler oluşturulup dağıtıldığı için sanal makinelerinizdeki paketi otomatik olarak güncelleştirmek kolaydır.

## <a name="component-overview"></a>Bileşene genel bakış

[Apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) gibi paket yöneticileri, Linux dünyasında da bilinmelidir, ancak bu çok daha fazla Windows dünyasında değildir.
[Chocolatey](https://chocolatey.org/) böyle bir şeydir ve konu başlığı altında Scott Hanselman [blogunuzun](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) çok büyük bir giriş olması gerekir. Bir Nutshell 'de, Chocolatey merkezi bir depodan Windows işletim sistemine paket yüklemek için komut satırını kullanmanıza olanak sağlar. Kendi deponuzu oluşturabilir ve yönetebilirsiniz ve Chocolatey, belirlediğiniz sayıda depodaki paketleri yükleyebilir.

[POWERSHELL DSC](/powershell/scripting/dsc/overview/overview) , bir makine için istediğiniz yapılandırmayı bildirmenize olanak sağlayan bir PowerShell aracıdır. Örneğin, Chocolatey yüklü, IIS yüklü, bağlantı noktası 80 ve Web sitenizin yüklü olduğu sürüm 1.0.0 ' yi istiyorsanız, DSC yerel Configuration Manager (LCM) bu yapılandırmayı uygular. DSC çekme sunucusu, makineleriniz için bir yapılandırma deposu tutar. Her makinedeki LCM, yapılandırmasının depolanan yapılandırmayla eşleşip eşleşmediğini görmek için düzenli aralıklarla kontrol eder. Bu durum, durumu bildirebilir veya depolanan yapılandırma ile makineyi yeniden hizalı hale getirmeyi deneyebilir. Bir makine veya makine kümesinin değiştirilen yapılandırmayla hizalı olmasına neden olmak için, istek sunucusunda depolanan yapılandırmayı düzenleyebilirsiniz.

DSC kaynağı, ağ, Active Directory veya SQL Server yönetimi gibi belirli özellikleri olan bir kod modüldür. Chocolatey DSC kaynağı bir NuGet sunucusuna erişmeyi (diğerleri arasında), paketleri indirmeyi, paketleri yüklemeyi vb. bilir. [PowerShell Galerisi](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)DIĞER birçok DSC kaynağı vardır. Bu modülleri, yapılandırmanızda kullanmak üzere Azure Automation durum yapılandırması çekme sunucunuza yüklersiniz.

Kaynak Yöneticisi şablonlar, altyapınızı oluşturmak için bir bildirim sağlar; örneğin, ağlar, alt ağlar, ağ güvenliği ve yönlendirme, yük dengeleyiciler, NIC 'Ler, VM 'Ler vb. Azure hizmet yönetimi (ASM veya klasik) dağıtım modeli (kesinlik) ile Kaynak Yöneticisi dağıtım modelini (bildirime dayalı) karşılaştıran bir [Makale](../azure-resource-manager/management/deployment-models.md) aşağıda verilmiştir. Bu makale, çekirdek kaynak sağlayıcılarının bir tartışmasını içerir: bilgi işlem, depolama ve ağ.

Kaynak Yöneticisi şablonun tek bir anahtar özelliği, sağlandığı için VM uzantısını sanal makineye yükleyebilmesidir. Bir VM uzantısının özel bir komut dosyası çalıştırma, virüsten koruma yazılımı yükleme ve bir DSC yapılandırma betiği çalıştırma gibi belirli özellikleri vardır. Birçok farklı VM uzantısı türü vardır.

## <a name="quick-trip-around-the-diagram"></a>Diyagram etrafında hızlı seyahat

En üstten başlayarak kodunuzu yazın, derleyin, test edin ve ardından bir yükleme paketi oluşturun. Chocolatey, MSI, MSU, ZIP gibi çeşitli yükleme paketi türlerini işleyebilir. Ayrıca, Chocolatey 'in yerel özellikleri BT değilse gerçek yüklemeyi yapmak için PowerShell 'in tam gücünden yararlanabilirsiniz. Paketi bir paket deposu erişilebilir bir yere yerleştirin. Bu kullanım örneği, bir Azure Blob depolama hesabında ortak bir klasör kullanır, ancak herhangi bir yerde olabilir. Chocolatey, NuGet sunucularıyla yerel olarak, diğer bir deyişle paket meta verisinin yönetimi için de kullanılır. [Bu makalede](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) seçenekler açıklanmaktadır. Kullanım örneği NuGet kullanır. Bir Nuspec, paketleriniz hakkındaki meta verilerlerdir. Nuspec bilgileri bir NuPkg 'da derlenir ve bir NuGet sunucusunda depolanır. Yapılandırmanız ada göre bir paket istediğinde ve bir NuGet sunucusuna başvuruyorsa, VM 'deki Chocolatey DSC kaynağı paketi dönüştürür ve onu yüklemez. Ayrıca, bir paketin belirli bir sürümünü isteyebilirsiniz.

Resmin sol alt kısmında bir Azure Resource Manager şablonu vardır. Bu kullanım örneğinde, VM Uzantısı VM 'yi Azure Otomasyonu durum yapılandırması çekme sunucusuna bir düğüm olarak kaydeder. Yapılandırma, çekme sunucusunda iki kez depolanır: düz metin olarak ve bir MOF dosyası olarak derlendikten sonra. Azure portal, MOF basit bir yapılandırmanın aksine bir düğüm yapılandırmasını temsil eder. Bu, düğümün yapılandırmasını bilmesi için bir düğümle ilişkili yapıtı. Aşağıdaki ayrıntılar, düğüm yapılandırmasını düğüme atamayı gösterir.

Nuspec 'in oluşturulması, derlenmesi ve bir NuGet sunucusunda depolanması küçük bir şeydir. VM 'Leri zaten yönetiyorsunuz. 

Sonraki adımın sürekli dağıtıma alınması, çekme sunucusunun bir kez ayarlanmasını, düğümlerinizin bir kez kaydedilmesini ve ilk yapılandırmanın sunucu üzerinde oluşturulmasını ve depolanmasını gerektirir. Paketler yükseltildiği ve depoya dağıtıldığı için, yalnızca çekme sunucusunda yapılandırma ve düğüm yapılandırmasını gerektiği şekilde yenilemeniz gerekir.

Kaynak Yöneticisi şablonuyla başlıyorsanız, bu kadar iyidir. VM 'lerinizi çekme sunucusuna kaydetmenize yardımcı olacak PowerShell komutları vardır. Daha fazla bilgi için bkz. [Azure Otomasyonu durum yapılandırmasına göre yönetim için makineleri ekleme](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Kullanım örneği hakkında

Bu makaledeki kullanım örneği, Azure galerisinden genel bir Windows Server 2012 R2 görüntüsünden VM ile başlar. Depolanan herhangi bir görüntüden başlayabilir ve ardından DSC yapılandırması ile ince ayar.
Ancak, bir görüntüye bakan yapılandırmanın değiştirilmesi, DSC kullanılarak yapılandırmanın dinamik olarak güncelleştirilmekten çok daha zordur.

Bu tekniği sanal makinelerinize kullanabilmek için Kaynak Yöneticisi şablonu ve VM uzantısını kullanmanız gerekmez. Ve sanal makinelerinizin, CD yönetimi altında olması için Azure 'da olması gerekmez. Bu, Chocolatey ' nin yüklenmesi ve sanal makine üzerinde, istek sunucusunun nerede olduğunu bilmesi için yapılandırılmış LCM ' dir.

Üretimde bir sanal makinede bulunan bir paketi güncelleştirdiğinizde, güncelleştirme yüklenirken söz konusu VM 'yi döndürme dışında getirmeniz gerekir. Bunu nasıl yapabileceğiniz çok farklılık vardır. Örneğin, bir Azure Load Balancer arkasındaki bir VM ile özel bir araştırma ekleyebilirsiniz. VM güncelleştirilirken, araştırma uç noktasının bir 400 döndürmesini sağlayabilirsiniz. Bu değişikliğe neden olması için gerekli olan ince ayar, güncelleştirme tamamlandıktan sonra, ince ayar geri dönmek üzere bir 200 döndürmek için, bu değişikliğin içinde olabilir.

Bu kullanım örneği için tam kaynak, GitHub 'daki [Bu Visual Studio projem](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) .

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>1. Adım: çekme sunucusunu ve otomasyon hesabını ayarlama

Kimliği doğrulanmış ( `Connect-AzAccount` ) PowerShell komut satırında: (çekme sunucusu ayarlandığında birkaç dakika sürebilir)

```azurepowershell-interactive
New-AzResourceGroup -Name MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount -ResourceGroupName MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES -Name MY-AUTOMATION-ACCOUNT
```

Otomasyon hesabınızı şu bölgelerden birine (konum olarak da bilinir) koyabilirsiniz: Doğu ABD 2, Orta Güney ABD, US Gov Virginia, Batı Avrupa, Güneydoğu Asya, Japonya Doğu, Orta Hindistan ve Avustralya Güneydoğu, Kanada Orta, Kuzey Avrupa.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>2. Adım: VM uzantılarını Kaynak Yöneticisi şablonuna oluşturma

Bu [Azure hızlı başlangıç şablonunda](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)belirtilen VM kaydı (POWERSHELL DSC VM uzantısı kullanılarak) için ayrıntılar.
Bu adım, yeni VM 'nizi durum yapılandırması düğümleri listesindeki çekme sunucusuna kaydeder. Bu kaydın bir parçası, düğüme uygulanacak düğüm yapılandırmasını belirtmektir. Bu düğüm yapılandırmasının çekme sunucusunda mevcut olması gerekmez, bu nedenle ilk kez bu işlem yapıldığında 4. adım bu kadar iyidir. Ancak adım 2 ' de, düğüm adına ve yapılandırmanın adına karar vermiş olmanız gerekir. Bu kullanım örneğinde, düğüm ' isvbox ' ve yapılandırma ' ISVBoxConfig '. Bu nedenle, düğüm yapılandırma adı (DeploymentTemplate.jsüzerinde belirtilmelidir) ' ISVBoxConfig. isvbox '.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>3. Adım: gerekli DSC kaynaklarını çekme sunucusuna ekleme

PowerShell Galerisi, DSC kaynaklarını Azure Otomasyonu hesabınıza yüklemek için işaretlenir.
İstediğiniz kaynağa gidin ve "Azure Otomasyonu 'na dağıt" düğmesine tıklayın.

![PowerShell Galerisi örneği](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Son olarak Azure portal eklenen diğer bir teknik, yeni modüller almanıza veya mevcut modülleri güncelleştirmenize olanak tanır. Otomasyon hesabı kaynağına, varlıklar kutucuğuna ve son olarak modüller kutucuğuna tıklayın. Galeriye göz at simgesi, galerideki modüllerin listesini görmenizi, Ayrıntılar halinde detaya gitmeyi ve sonuç olarak otomasyon hesabınıza aktarmayı sağlar. Bu, modüllerinizi zaman zaman güncel tutmanın harika bir yoludur. Ve içeri aktarma özelliği, hiçbir şeyin eşitlenmemiş olmamasını sağlamak için diğer modüllerle bağımlılıkları denetler.

Ayrıca, daha sonra yükseltmek istemediğiniz müddetçe, kaynak başına yalnızca bir kez kullanılan el ile bir yaklaşım da vardır. PowerShell tümleştirme modüllerini yazma hakkında daha fazla bilgi için bkz. [Azure Otomasyonu Için tümleştirme modüllerini yazma](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>Bir Windows bilgisayarı için PowerShell tümleştirme modülünün klasör yapısı, Azure Otomasyonu tarafından beklenen klasör yapısından biraz farklıdır. 

1. [Windows Management Framework v5](https://aka.ms/wmf5latest) 'ı (Windows 10 için gerekli değildir) yükler.

2. Tümleştirme modülünü yükler.

    ```azurepowershell-interactive
    Install-Module -Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Modül klasörünü **C:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** konumundan geçici bir klasöre kopyalayın.

4. Ana klasörden örnekleri ve belgeleri silin.

5. Zip dosyasını klasör adıyla adlandırarak ana klasörü zip olarak adlandırın.

6. ZIP dosyasını, bir Azure depolama hesabındaki BLOB depolama gibi erişilebilir bir HTTP konumuna koyun.

7. Aşağıdaki komutu çalıştırın.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME -ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

Dahil edilen örnek, cChoco ve xNetworking için bu adımları uygular. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>4. Adım: düğüm yapılandırmasını çekme sunucusuna ekleme

Yapılandırmanızı çekme sunucusuna ilk kez aktardığınızda ve derlemenizde özel bir şey yoktur. Aynı yapılandırmanın tüm sonraki içeri aktarmaları veya derlemeleri tamamen aynı şekilde görünür. Paketinizi her güncelleştirdiğinizde ve üretime göndermeniz gerektiğinde, paketinizin yeni sürümü de dahil olmak üzere yapılandırma dosyasının doğru olduğundan emin olduktan sonra bu adımı yapabilirsiniz. Yapılandırma dosyası şu şekildedir **ISVBoxConfig.ps1**:

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

İşte **New-ConfigurationScript.ps1** betiği (az modülünü kullanacak şekilde değiştirilir):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published -Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Bu adımlar, **ISVBoxConfig. isvbox** adlı yeni bir düğüm yapılandırmasının çekme sunucusuna yerleştirilmesine neden olacak. Düğüm yapılandırma adı olarak oluşturulur `configurationName.nodeName` .

## <a name="step-5-create-and-maintain-package-metadata"></a>5. Adım: paket meta verilerini oluşturma ve sürdürme

Paket deposuna yerleştirdiğiniz her bir paket için, onu açıklayan bir Nuspec gerekir. Derlenmesi ve NuGet sunucunuzda depolanması gerekir. Bu işlem [burada](https://docs.nuget.org/create/creating-and-publishing-a-package)açıklanmıştır. 

**MyGet.org** 'ı bir NuGet sunucusu olarak kullanabilirsiniz. Bu hizmeti satın alabilirsiniz, ancak o, ücretsiz bir başlatıcı SKU 'SU. [NuGet](https://www.nuget.org/)'de, özel paketleriniz Için kendi NuGet sunucunuzu yükleme yönergelerini bulacaksınız.

## <a name="step-6-tie-it-all-together"></a>6. Adım: Tümünü birbirine bağlama

Bir sürüm QA 'i her geçtiğinde ve dağıtım için onaylandığında, paket oluşturulur ve nuspec ve nupkg güncelleştirilir ve NuGet sunucusuna dağıtılır. Yapılandırma (4. adım), yeni sürüm numarasını kabul etmek için de güncelleştirilmeleri gerekir. Daha sonra çekme sunucusuna gönderilmelidir ve derlenmelidir.

Bu noktadan itibaren, güncelleştirmeyi çekmek ve yüklemek için bu yapılandırmaya bağlı olan sanal makinelere bağımlıdır. Bu güncelleştirmelerin her biri basit-yalnızca bir çizgi veya PowerShell 'in ikisi de vardır. Azure DevOps için, bazıları bir derlemede birlikte zincirlenebilir derleme görevlerinde kapsüllenir. Bu [makalede](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) daha fazla ayrıntı sağlanmaktadır. Bu [GitHub deposu](https://github.com/Microsoft/vso-agent-tasks) , kullanılabilir derleme görevlerinin ayrıntılarını oluşturur.

## <a name="related-articles"></a>İlgili makaleler:
* [Azure Automation DSC genel bakış](automation-dsc-overview.md)
* [Azure Automation DSC göre yönetim için makineleri ekleme](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [Azure Otomasyonu durum yapılandırmasına genel bakış](automation-dsc-overview.md).
- Özelliği kullanmaya başlamak için bkz. [Azure Automation durum yapılandırması ile çalışmaya](automation-dsc-getting-started.md)başlama.
- Hedef düğümlere atayabilmeniz için DSC yapılandırmalarını derleme hakkında bilgi edinmek için bkz. [Azure Otomasyonu durum YAPıLANDıRMASıNDA DSC yapılandırmalarını derleme](automation-dsc-compile.md).
- PowerShell cmdlet başvurusu için bkz. [az. Automation](/powershell/module/az.automation).
- Fiyatlandırma bilgileri için bkz. [Azure Otomasyonu durum yapılandırması fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/).
