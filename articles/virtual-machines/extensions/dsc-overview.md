---
title: Azure için istenen durum yapılandırması genel bakış
description: PowerShell Istenen durum yapılandırması (DSC) için Microsoft Azure uzantısı işleyicisini nasıl kullanacağınızı öğrenin. Makale önkoşulları, mimarisi ve cmdlet 'leri içerir.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: DSC
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: 7e309237589dfaf037114401172fc8f928a30077
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176643"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Azure Istenen durum yapılandırması uzantı işleyicisine giriş

Azure VM Aracısı ve ilişkili uzantılar Microsoft Azure altyapı hizmetlerinin bir parçasıdır. VM uzantıları, VM işlevlerini genişleten ve çeşitli VM yönetimi işlemlerini basitleştiren yazılım bileşenleridir.

Azure Istenen durum yapılandırması (DSC) uzantısı için birincil kullanım örneği, bir VM 'yi [Azure Otomasyonu durum yapılandırması (DSC) hizmetine](../../automation/automation-dsc-overview.md)önkullanmaktır.
Hizmet, VM yapılandırmasının sürekli yönetimini ve Azure Izleme gibi diğer işletimsel araçlarla tümleştirmeyi kapsayan [avantajlar](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) sağlar.
VM 'leri hizmetine kaydetme uzantısının kullanılması, Azure aboneliklerinde bile çalışacak esnek bir çözüm sağlar.

DSC uzantısını Automation DSC hizmetinden bağımsız olarak kullanabilirsiniz.
Ancak, bu yalnızca VM 'ye bir yapılandırma gönderir.
Sanal makinede yerel olarak bulunmayan sürekli raporlama yok.

Bu makalede her iki senaryo hakkında bilgi verilmektedir: Otomasyon ekleme için DSC uzantısını kullanma ve DSC uzantısını Azure SDK kullanarak VM 'lere yapılandırma atamak için bir araç olarak kullanma.

## <a name="prerequisites"></a>Önkoşullar

- **Yerel makine**: Azure VM uzantısıyla etkileşim kurmak için Azure Portal ya da Azure PowerShell SDK 'sını kullanmanız gerekir.
- **Konuk Aracısı**: DSC yapılandırması tarafından YAPıLANDıRıLAN Azure VM, Windows Management Framework (WMF) 4,0 veya üstünü destekleyen bir işletim sistemi olmalıdır. Desteklenen işletim sistemi sürümlerinin tam listesi için [DSC Uzantısı Sürüm geçmişine](/powershell/scripting/dsc/getting-started/azuredscexthistory)bakın.

## <a name="terms-and-concepts"></a>Hüküm ve kavramlar

Bu kılavuzda, aşağıdaki kavramlarla benzerlik varsayılmaktadır:

- **Yapılandırma**: bir DSC yapılandırma belgesi.
- **Düğüm**: DSC yapılandırması için bir hedef. Bu belgede, *düğüm* her zaman BIR Azure VM 'sine başvurur.
- **Yapılandırma verileri**: bir yapılandırma için çevresel verileri olan bir. psd1 dosyası.

## <a name="architecture"></a>Mimarisini

Azure DSC Uzantısı, Azure VM 'lerde çalışan DSC yapılandırmasını teslim etmek, uygulamak ve raporlamak için Azure VM Aracısı çerçevesini kullanır. DSC Uzantısı bir yapılandırma belgesini ve bir dizi parametreyi kabul eder. Dosya sağlanmazsa, uzantıya sahip bir [varsayılan yapılandırma betiği](#default-configuration-script) eklenir. Varsayılan yapılandırma betiği yalnızca [yerel Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)meta verileri ayarlamak için kullanılır.

Uzantı ilk kez çağrıldığında, aşağıdaki mantığı kullanarak WMF 'in bir sürümünü yüklenir:

- Azure VM OS Windows Server 2016 ise hiçbir işlem yapılmaz. Windows Server 2016 ' de zaten PowerShell 'in en son sürümü yüklü.
- **Wmfversion** özelliği belirtilmişse, bu sürüm VM 'nin işletim sistemi ile uyumlu olmadığı IÇIN bu WMF sürümü yüklenir.
- **Wmfversion** özelliği belirtilmemişse, en son geçerli WMF sürümü yüklenir.

WMF 'nin yüklenmesi için yeniden başlatma gerekir. Yeniden başlatıldıktan sonra uzantı, sağlanmışsa, **modulesUrl** özelliğinde belirtilen. zip dosyasını indirir. Bu konum Azure Blob depolama alanında ise, dosyaya erişmek için **Sastoken** ÖZELLIĞINDE bir SAS belirteci belirtebilirsiniz. . Zip indirildikten ve yüklendikten sonra, **configurationfunction** içinde tanımlanan yapılandırma işlevi bir. mof dosyası oluşturmak için çalışır. Daha sonra uzantı, oluşturulan. mof dosyasını kullanarak `Start-DscConfiguration -Force` ' ı çalıştırır. Uzantı çıktıyı yakalar ve Azure durum kanalına yazar.

### <a name="default-configuration-script"></a>Varsayılan yapılandırma betiği

Azure DSC Uzantısı, Azure Automation DSC hizmetine bir VM eklediğinizde kullanılması amaçlanan bir varsayılan yapılandırma betiği içerir. Betik parametreleri, [yerel Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)yapılandırılabilir özellikleriyle hizalanır. Betik parametreleri için, [Azure Resource Manager şablonlar Ile Istenen durum yapılandırma uzantısında](dsc-template.md) [varsayılan yapılandırma betiği](dsc-template.md#default-configuration-script) ' ne bakın. Tam betik için [GitHub 'Da Azure hızlı başlangıç şablonu](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true)' na bakın.

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Azure Otomasyonu durum yapılandırması (DSC) hizmeti ile kaydolma bilgileri

Bir düğümü durum yapılandırma hizmetine kaydetmek için DSC uzantısını kullanırken üç değerin sağlanması gerekecektir.

- RegistrationUrl-Azure Otomasyonu hesabının https adresi
- RegistrationKey-düğümleri hizmete kaydetmek için kullanılan paylaşılan bir gizli dizi
- NodeConfigurationName-sunucu rolünü yapılandırmak için hizmetten çekilecek düğüm yapılandırmasının (MOF) adı

Bu bilgiler [Azure Portal](../../automation/automation-dsc-onboarding.md#azure-portal) görünebilir veya PowerShell kullanabilirsiniz.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

Düğüm yapılandırma adı için, düğüm yapılandırmasının Azure Durum Yapılandırması 'nda bulunduğundan emin olun.  Değilse, uzantı dağıtımı bir hata döndürür.  Ayrıca, yapılandırma değil, *düğüm yapılandırmasının* adını kullandığınızdan emin olun.
Bir yapılandırma, [düğüm yapılandırmasını (MOF dosyası) derlemek için](https://docs.microsoft.com/azure/automation/automation-dsc-compile)kullanılan bir betikte tanımlanır.
Ad her zaman yapılandırma olacaktır `.` ve `localhost` ya da belirli bir bilgisayar adı.

## <a name="dsc-extension-in-resource-manager-templates"></a>Kaynak Yöneticisi şablonlarda DSC Uzantısı

Çoğu senaryoda, Kaynak Yöneticisi dağıtım şablonları DSC uzantısıyla çalışmanın beklenen yoludur. Daha fazla bilgi ve Kaynak Yöneticisi dağıtım şablonlarına DSC uzantısını ekleme örnekleri için, [Azure Resource Manager şablonlarla Istenen durum yapılandırma uzantısı](dsc-template.md)' na bakın.

## <a name="dsc-extension-powershell-cmdlets"></a>DSC Uzantısı PowerShell cmdlet 'leri

DSC uzantısını yönetmek için kullanılan PowerShell cmdlet 'leri, etkileşimli sorun giderme ve bilgi toplama senaryolarında en iyi şekilde kullanılır. DSC uzantı dağıtımlarını paketlemek, yayımlamak ve izlemek için cmdlet 'lerini kullanabilirsiniz. DSC uzantısının cmdlet 'leri henüz [varsayılan yapılandırma betiği](#default-configuration-script)ile çalışacak şekilde güncellenmez.

**Publish-AzVMDscConfiguration** cmdlet 'i bir yapılandırma dosyası alır, bunu bağımlı DSC kaynakları için tarar ve bir. zip dosyası oluşturur. . Zip dosyası yapılandırmayı uygulamak için gereken yapılandırma ve DSC kaynaklarını içerir. Cmdlet 'i *-OutputArchivePath* parametresini kullanarak paketi yerel olarak da oluşturabilir. Aksi takdirde, cmdlet. zip dosyasını BLOB depolama alanına yayımlar ve ardından bir SAS belirteci ile güvenliğini sağlar.

Cmdlet 'in oluşturduğu. ps1 yapılandırma betiği Arşiv klasörünün kökündeki. zip dosyasında bulunur. Modül klasörü, kaynaklardaki arşiv klasörüne yerleştirilir.

**Set-AzVMDscExtension** cmdlet 'ı PowerShell DSC uzantısının gerektirdiği ayarları bir VM yapılandırma nesnesi olarak çıkarır.

**Get-AzVMDscExtension** cmdlet 'i belirli bir sanal makinenin DSC uzantı durumunu alır.

**Get-AzVMDscExtensionStatus** cmdlet 'ı, DSC uzantı işleyicisi tarafından Işlem yapılan DSC yapılandırmasının durumunu alır. Bu eylem, tek bir VM veya bir VM grubu üzerinde gerçekleştirilebilir.

**Remove-AzVMDscExtension** cmdlet 'i belırlı bir VM 'den uzantı işleyicisini kaldırır. Bu cmdlet yapılandırmayı *kaldırmaz, WMF 'yi kaldırmaz veya* uygulanan ayarları sanal makinede değiştirmez. Yalnızca uzantı işleyicisini kaldırır. 

Kaynak Yöneticisi DSC Uzantısı cmdlet 'leri hakkında önemli bilgiler:

- Azure Resource Manager cmdlet 'leri zaman uyumludur.
- *Resourcegroupname*, *VMName*, *ArchiveStorageAccountName*, *Version*ve *Location* parametrelerinin hepsi gereklidir.
- *ArchiveResourceGroupName* isteğe bağlı bir parametredir. Depolama Hesabınız VM 'nin oluşturulduğu sunucudan farklı bir kaynak grubuna aitse, bu parametreyi belirtebilirsiniz.
- Uzantı işleyicisini **, kullanılabilir** olduğunda en son sürüme otomatik olarak güncelleştirmek için otomatik güncelleştirme anahtarını kullanın. Bu parametrenin yeni bir WMF sürümü yayınlandığında VM 'de yeniden başlatmalara neden olma olasılığı vardır.

### <a name="get-started-with-cmdlets"></a>Cmdlet 'leri kullanmaya başlama

Azure DSC Uzantısı, dağıtım sırasında doğrudan Azure VM 'Leri yapılandırmak için DSC yapılandırma belgelerini kullanabilir. Bu adım, düğümü Otomasyon 'a kaydetmez. Düğüm merkezi olarak *yönetilmez* .

Aşağıdaki örnek, bir yapılandırmaya ilişkin basit bir örnek gösterir. Yapılandırmayı ıısınstall. ps1 olarak yerel olarak kaydedin.

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

Aşağıdaki komutlar, ıısınstall. ps1 betiğini belirtilen sanal makineye yerleştirir. Komutlar Ayrıca yapılandırmayı yürütür ve sonra durum ' u yeniden bildirir.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Azure CLı dağıtımı

Azure CLı, DSC uzantısını var olan bir sanal makineye dağıtmak için kullanılabilir.

Windows çalıştıran bir sanal makine için:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Linux çalıştıran bir sanal makine için:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Azure portal işlevselliği

Portalda DSC 'yi ayarlamak için:

1. Bir VM 'ye gidin.
2. **Ayarlar**altında **Uzantılar**' ı seçin.
3. Oluşturulan yeni sayfada **+ Ekle**' yi seçin ve ardından **PowerShell İstenen Durum Yapılandırması**' nı seçin.
4. Uzantı bilgileri sayfasının alt kısmındaki **Oluştur** ' a tıklayın.

Portal aşağıdaki girişi toplar:

- **Yapılandırma modülleri veya betiği**: Bu alan zorunludur (form [varsayılan yapılandırma betiği](#default-configuration-script)için güncelleştirilmemiş). Yapılandırma modülleri ve betikler, bir yapılandırma betiğine sahip bir. ps1 dosyası veya kökte. ps1 yapılandırma betiği içeren bir. zip dosyası gerektirir. Bir. zip dosyası kullanıyorsanız, tüm bağımlı kaynakların. zip içindeki modül klasörlerine dahil olması gerekir. . Zip dosyasını, Azure PowerShell SDK 'ya dahil olan **Publish-AzureVMDscConfiguration-OutputArchivePath** cmdlet 'ini kullanarak oluşturabilirsiniz. . Zip dosyası, Kullanıcı BLOB depolama alanına yüklenir ve bir SAS belirteci tarafından güvenli hale getirilir.

- **Modülün nitelikli adı**: bir. ps1 dosyasına birden çok yapılandırma işlevi ekleyebilirsiniz. Configuration. ps1 betiğinin adını, ardından \\ ve yapılandırma işlevinin adını girin. Örneğin,. ps1 betiğinizin adı Configuration. ps1 ise ve yapılandırma **ıisınstall**ise **Configuration. ps1\IisInstall**girin.

- **Yapılandırma bağımsız değişkenleri**: yapılandırma işlevi bağımsız değişkenler alırsa, bunları **argumentName1 = değer1, argumentName2 = değer2**biçiminde girin. Bu biçim, PowerShell cmdlet 'lerinde veya Kaynak Yöneticisi şablonlarda yapılandırma bağımsız değişkenlerinin kabul edildiği farklı bir biçimdir.

- **Yapılandırma VERILERI PSD1 dosyası**: Bu alan isteğe bağlıdır. Yapılandırmanız için. psd1 içinde bir yapılandırma veri dosyası gerekiyorsa, bu alanı kullanarak veri alanını seçin ve Kullanıcı BLOB depolama alanına yükleyin. Yapılandırma veri dosyası, BLOB depolama alanındaki bir SAS belirteci ile korunmuş olur.

- **WMF sürümü**: sanal makinenize yüklenmesi gereken Windows Management Framework (WMF) sürümünü belirtir. Bu özelliğin en son olarak ayarlanması WMF 'nin en son sürümünü yüklüyor. Şu anda bu özellik için olası tek değerler 4,0, 5,0, 5,1 ve en son değerlerdir. Bu olası değerler güncelleştirmelere tabidir. Varsayılan değer **en**sonuncusudur.

- **Veri toplama**: uzantının telemetri toplayıp toplayacağını belirler. Daha fazla bilgi için bkz. [Azure DSC Uzantısı veri toplama](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/).

- **Sürüm**: yüklenecek DSC uzantısının sürümünü belirtir. Sürümler hakkında bilgi için bkz. [DSC Uzantısı sürüm geçmişi](/powershell/scripting/dsc/getting-started/azuredscexthistory).

- Alt **sürümü otomatik Yükselt**: Bu alan cmdlet **'lerde otomatik güncelleştirme anahtarına eşlenir** ve uzantının yükleme sırasında en son sürüme otomatik olarak güncelleştirilmesini sağlar. **Evet** seçeneği, uzantı işleyicisinin kullanılabilir en son sürümü kullanmasını ve **Hayır** 'ın, belirtilen **sürümü** yüklemeye zormasını sağlar. Hayır **Evet** veya **Hayır** seçeneği belirlendiğinde **Hayır**seçeneği belirlenmeyecektir.

## <a name="logs"></a>Logs

Uzantıya ait Günlükler şu konumda depolanır: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Sonraki adımlar

- PowerShell DSC hakkında daha fazla bilgi için [PowerShell belge merkezine](/powershell/scripting/dsc/overview/overview)gidin.
- [DSC uzantısının Kaynak Yöneticisi şablonunu](dsc-template.md)inceleyin.
- PowerShell DSC 'yi kullanarak yönetebileceğiniz daha fazla işlevsellik ve daha fazla DSC kaynağı için [PowerShell Galerisi](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)' ne gözatabileceğinizi unutmayın.
- Hassas parametreleri yapılandırmalara geçirme hakkında ayrıntılı bilgi için bkz. [DSC uzantı işleyicisiyle kimlik bilgilerini güvenli bir şekilde yönetme](dsc-credentials.md).
