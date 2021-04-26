---
title: Windows için Konuk Yapılandırma ilkeleri oluşturma
description: Windows için Azure Ilke Konuk yapılandırma ilkesi oluşturmayı öğrenin.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 6eaefdbc28b8efc53dc7c4d46eb5d8a56d5be141
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096606"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Windows için Konuk Yapılandırma ilkeleri oluşturma

Özel ilke tanımları oluşturmadan önce, [Azure Ilke Konuk yapılandırması](../concepts/guest-configuration.md)sayfasında kavramsal genel bakış bilgilerini okumak iyi bir fikirdir.
 
Linux için konuk yapılandırma ilkeleri oluşturma hakkında bilgi edinmek için bkz. [Linux Için Konuk yapılandırma ilkeleri oluşturma](./guest-configuration-create-linux.md) sayfası

Windows’u denetlerken, Konuk Yapılandırması yapılandırma dosyasını oluşturmak için [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) kaynak modülünü kullanır. DSC yapılandırması makinenin olması gereken durumu tanımlar. Yapılandırmanın değerlendirmesi başarısız olursa, **Auditınotexists** ilke efekti tetiklenir ve makine **uyumlu** değil olarak kabul edilir.

[Azure Ilke Konuk yapılandırması](../concepts/guest-configuration.md) , yalnızca makineler içindeki ayarları denetlemek için kullanılabilir. Makinelerin içindeki ayarların düzeltilmesi henüz kullanılamamaktadır.

Bir Azure veya Azure dışı makinenin durumunu doğrulamak üzere kendi yapılandırmanızı oluşturmak için aşağıdaki eylemleri kullanın.

> [!IMPORTANT]
> Azure Kamu ve Azure Çin ortamlarında Konuk yapılandırması olan özel ilke tanımları bir önizleme özelliğidir.
>
> Konuk Yapılandırma uzantısı Azure sanal makinelerinde denetim gerçekleştirmek için gereklidir. Uzantıyı tüm Windows makineleri genelinde ölçekli olarak dağıtmak için aşağıdaki ilke tanımlarını atayın: `Deploy prerequisites to enable Guest Configuration Policy on Windows VMs`
> 
> Özel içerik paketlerinde gizli dizileri veya gizli bilgileri kullanmayın.

## <a name="install-the-powershell-module"></a>PowerShell modülünü yükleme

Konuk yapılandırma modülü, aşağıdakiler dahil olmak üzere özel içerik oluşturma sürecini otomatikleştirir:

- Konuk yapılandırması içerik yapıtı (. zip) oluşturma
- Yapıtın otomatikleştirilmiş testi
- İlke tanımı oluşturma
- İlke yayımlanıyor

Modül, Windows, macOS veya Linux çalıştıran bir makineye PowerShell 6,2 veya sonraki bir sürümü veya [Azure Cloud Shell](https://shell.azure.com)ya da [Azure PowerShell Core Docker görüntüsü](https://hub.docker.com/r/azuresdk/azure-powershell-core)ile yüklenebilir.

> [!NOTE]
> Yapılandırmaların derlenmesi Linux üzerinde henüz desteklenmiyor.

### <a name="base-requirements"></a>Temel gereksinimler

Modülün yüklenebildiği işletim sistemleri:

- Linux
- macOS
- Windows

Konuk yapılandırması kaynak modülü için aşağıdaki yazılımlar gereklidir:

- PowerShell 6,2 veya sonraki bir sürümü. Henüz yüklenmiş değilse, [bu yönergeleri](/powershell/scripting/install/installing-powershell) izleyin.
- Azure PowerShell 1.5.0 veya üzeri. Henüz yüklenmiş değilse, [bu yönergeleri](/powershell/azure/install-az-ps) izleyin.
  - Yalnızca az modüller ' az. Accounts ' ve ' az. resources ' gereklidir.

### <a name="install-the-module"></a>Modülü yükler

PowerShell 'e **Guestconfiguration** modülünü yüklemek için:

1. Bir PowerShell isteminden aşağıdaki komutu çalıştırın:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Modülün içeri aktarıldığını doğrulayın:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Windows için konuk yapılandırma yapıtları ve ilkesi

Konuk yapılandırması, Windows 'da denetlenecek denetimi yazmak için dil soyutlama olarak PowerShell Istenen durum yapılandırmasını kullanır. Aracı, PowerShell 6,2 'nin tek başına bir örneğini yükler, bu nedenle Windows PowerShell 5,1 ' de PowerShell DSC kullanımıyla ilgili bir çakışma yoktur ve PowerShell 6,2 veya üstünü önceden yükleme gereksinimi yoktur.

DSC kavramlarına ve terimlere genel bakış için bkz. [POWERSHELL DSC 'ye genel bakış](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Konuk yapılandırma modüllerinin Windows PowerShell DSC modülleriyle farkı

Konuk yapılandırma bir makineyi denet,, olay sırası Windows PowerShell DSC 'den farklıdır.

1. Aracı ilk olarak `Test-TargetResource` yapılandırmanın doğru durumda olup olmadığını belirlemede çalışır.
1. İşlevin döndürdüğü Boole değeri, Konuk atamasının Azure Resource Manager durumunun uyumlu olup olmadığını belirler.
1. Sağlayıcı `Get-TargetResource` her ayarın geçerli durumunu döndürmek için çalışır, böylece ayrıntılar, makinenin neden uyumlu olmaması ve geçerli durumun uyumlu olduğunu doğrulamak için geçerlidir.

Azure Ilkesindeki, verileri Konuk yapılandırma atamalarına geçiren parametreler _dize_ türünde olmalıdır. DSC kaynağı dizileri desteklediğinden bile, dizileri parametreler aracılığıyla geçirmek mümkün değildir.

### <a name="get-targetresource-requirements"></a>Get-TargetResource gereksinimleri

İşlevi, `Get-TargetResource` Windows Istenen durum yapılandırması için gerekli olmayan konuk yapılandırması için özel gereksinimlere sahiptir.

- Döndürülen Hashtable, **nedenler** adlı bir özellik içermelidir.
- Nedenler özelliği bir dizi olmalıdır.
- Dizideki her öğe, **kod** ve **tümcecik** adlı anahtarlar içeren bir Hashtable olmalıdır.

Nedenler özelliği, bir makine uyumsuz olduğunda bilgilerin nasıl sunulduğunu standartlaştırmak üzere hizmet tarafından kullanılır. Kaynağın uyumlu olmadığı bir "Neden" gibi nedenlerle her bir öğeyi düşünebilirsiniz. Bir kaynak bir nedenle uyumsuz olabileceğinden, bu özellik bir dizidir.

Hizmet tarafından Özellikler **kodu** ve **tümceciği** bekleniyor. Özel bir kaynak yazarken, **ifadenin** değeri olarak kaynağın uyumlu olmadığı bir nedenden dolayı göstermek istediğiniz metni (genellikle stdout) ayarlayın. **Kodun** , denetimi yapmak için kullanılan kaynakla ilgili bilgileri açıkça görüntülemesi için özel biçimlendirme gereksinimleri vardır. Bu çözüm, Konuk yapılandırmasını Genişletilebilir hale getirir. Çıktı, **tümcecik** özelliği için bir dize değeri olarak döndürülemedikçe, herhangi bir komut çalıştırılabilir.

- **Kod** (dize): kaynağın adı, tekrarlanması ve bir tanımlayıcı olarak boşluk olmayan kısa bir ad. Bu üç değer, boşluk olmadan iki nokta ile sınırlandırılmalıdır.
  - Örnek şöyle olabilir `registry:registry:keynotpresent`
- **Tümcecik** (dize): ayarın neden uyumlu olmadığını açıklamak için okunabilir metin.
  - Örnek şöyle olabilir `The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

Nedenler özelliği, kaynak için şema MOF öğesine eklenmiş bir sınıf olarak eklenmelidir.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

Kaynak gerekli özelliklere sahipse, bu özelliklerin de `Get-TargetResource` sınıfıyla paralel olarak döndürülmesi gerekir `reasons` . `reasons`Dahil edilmemişse, hizmet, girdi değerlerini ve tarafından döndürülen değerleri karşılaştıran bir "catch-all" davranışı içerir `Get-TargetResource` `Get-TargetResource` ve olarak ayrıntılı bir karşılaştırma sağlar `reasons` .

### <a name="configuration-requirements"></a>Yapılandırma gereksinimleri

Özel yapılandırmanın adı her yerde tutarlı olmalıdır. İçerik paketi için. zip dosyasının adı, MOF dosyasındaki yapılandırma adı ve Azure Resource Manager şablonundaki (ARM şablonu) Konuk atama adı aynı olmalıdır.

### <a name="policy-requirements"></a>İlke gereksinimleri

Konuk yapılandırma `metadata` atamalarının sağlanması ve raporlanmasını otomatikleştirmek için, ilke tanımı bölümünde Konuk yapılandırma hizmeti 'nin iki özelliği bulunmalıdır. `category`Özelliği "Konuk yapılandırması" olarak ayarlanmalıdır ve adlı bir bölüm `Guest Configuration` , Konuk yapılandırma ataması hakkında bilgi içermelidir. `New-GuestConfigurationPolicy`Cmdlet bu metni otomatik olarak oluşturur.
Bu sayfada adım adım yönergelere bakın.

Aşağıdaki örnek `metadata` bölümünü gösterir.

```json
    "metadata": {
      "category": "Guest Configuration",
      "guestConfiguration": {
        "name": "test",
        "version": "1.0.0",
        "contentType": "Custom",
        "contentUri": "CUSTOM-URI-HERE",
        "contentHash": "CUSTOM-HASH-VALUE-HERE",
        "configurationParameter": {}
      }
    },
```

### <a name="scaffolding-a-guest-configuration-project"></a>Konuk yapılandırma projesi yapı iskelesi

Çalışmaya başlama ve örnek koddan çalışma sürecini hızlandırmak isteyen geliştiriciler, **Konuk yapılandırma projesi** adlı bir topluluk projesi yükleyebilir. Proje, [plaster](https://github.com/powershell/plaster) PowerShell modülü için bir şablon yüklüyor. Bu araç, çalışan bir yapılandırma ve örnek kaynak dahil olmak üzere bir projeyi ve projeyi doğrulamak için bir dizi [pester](https://github.com/pester/pester) testini de kapsayan bir projeyi dolandırarak kullanılabilir. Şablon, Konuk yapılandırma paketini oluşturma ve doğrulamaya otomatik hale getirmek için Visual Studio Code görev çalıştıranlar de içerir. Daha fazla bilgi için bkz. GitHub proje [Konuk yapılandırma projesi](https://github.com/microsoft/guestconfigurationproject).

Genel olarak yapılandırmalarda çalışma hakkında daha fazla bilgi için bkz. [yazma, derleme ve yapılandırma uygulama](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Konuk yapılandırma yapıtı için beklenen içerik

Tamamlanmış paket, Azure Ilke tanımlarını oluşturmak için konuk yapılandırması tarafından kullanılır. Paket aşağıdakilerden oluşur:

- MOF olarak derlenen DSC yapılandırması
- Modüller klasörü
  - GuestConfiguration modülü
  - Dscnativeresonakl modülü
  - Pencerelerin MOF için gereken DSC kaynak modülleri

PowerShell cmdlet 'leri, paketi oluşturmaya yardımcı olur. Kök düzey klasörü veya sürüm klasörü gerekli değil. Paket biçimi bir. zip dosyası olmalıdır ve sıkıştırılmamış olarak 100 MB 'lık toplam boyutu aşamaz.

### <a name="storing-guest-configuration-artifacts"></a>Konuk yapılandırma yapıtları depolanıyor

. Zip paketinin yönetilen sanal makineler tarafından erişilebilen bir konumda depolanması gerekir.
GitHub depoları, bir Azure deposu veya Azure Storage örnekleri gösterilebilir. Paketi genel yapmayı tercih ediyorsanız, URL 'ye bir [SAS belirteci](../../../storage/common/storage-sas-overview.md) ekleyebilirsiniz. Ayrıca, özel bir ağdaki makineler için [hizmet uç noktası](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) da uygulayabilirsiniz, ancak bu yapılandırma yalnızca pakete erişim için geçerlidir ve hizmetle iletişim kurmamakla kalmaz.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Adım adım, Windows için özel konuk yapılandırma denetim ilkesi oluşturma

Ayarları denetlemek için bir DSC yapılandırması oluşturun. Aşağıdaki PowerShell betiği örneği, **Auditbitlocker** adlı bir yapılandırma oluşturur, **Psdscresources** kaynak modülünü içeri aktarır ve `Service` çalışan bir hizmeti denetlemek için kaynağını kullanır. Yapılandırma betiği bir Windows veya macOS makinesinden yürütülebilir.

```powershell
# Add PSDscResources module to environment
Install-Module 'PSDscResources'

# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

Bu betiği bir PowerShell terminalinde çalıştırın veya bu dosyayı `config.ps1` Proje klasörüne adıyla kaydedin. Terminalde yürüterek PowerShell 'de çalıştırın `./config.ps1` . Yeni bir MOF dosyası oluşturulur.

`Node AuditBitlocker`Komut Teknik olarak gerekli değildir `AuditBitlocker.mof` , ancak varsayılan olarak değil adlı bir dosya oluşturur `localhost.mof` . . Mof dosya adının yapılandırılması, ölçeklendirmeye çalışırken birçok dosyayı düzenlemeyi kolaylaştırır.

MOF derlendikten sonra destekleyici dosyaların birlikte paketlenmesi gerekir. Tamamlanmış paket, Azure Ilke tanımlarını oluşturmak için konuk yapılandırması tarafından kullanılır.

`New-GuestConfigurationPackage`Cmdlet 'i paketi oluşturur. Yapılandırma için gereken modüller ' de kullanılabilir olmalıdır `$Env:PSModulePath` . `New-GuestConfigurationPackage`Windows içeriği oluştururken cmdlet parametreleri:

- **Ad**: Konuk yapılandırma paketi adı.
- **Yapılandırma**: derlenen DSC yapılandırma belgesi tam yolu.
- **Yol**: çıkış klasörü yolu. Bu parametre isteğe bağlıdır. Belirtilmezse, paket geçerli dizinde oluşturulur.

Önceki adımda verilen yapılandırmayı kullanarak bir paket oluşturmak için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './AuditBitlocker/AuditBitlocker.mof'
```

Yapılandırma paketini oluşturduktan, ancak Azure 'a yayımlamadan önce, paketi iş istasyonunuzdan veya sürekli tümleştirme ve sürekli dağıtım (CI/CD) ortamınızdan test edebilirsiniz. GuestConfiguration cmdlet 'i, `Test-GuestConfigurationPackage` Azure makinelerinde kullanıldığı gibi geliştirme ortamınızda aynı aracıyı içerir. Bu çözümü kullanarak, faturalandırılan bulut ortamlarına bırakmadan önce tümleştirme testini yerel olarak gerçekleştirebilirsiniz.

Aracı gerçekten yerel ortamı değerlendirdiğinden, çoğu durumda test-cmdlet 'ini, denetlemeyi planladığınız aynı işletim sistemi platformunda çalıştırmanız gerekir. Test yalnızca içerik paketine dahil edilen modülleri kullanır.

`Test-GuestConfigurationPackage`Cmdlet parametreleri:

- **Ad**: Konuk yapılandırma ilkesi adı.
- **Parametre**: Hashtable biçiminde belirtilen ilke parametreleri.
- **Yol**: Konuk yapılandırma paketinin tam yolu.

Önceki adım tarafından oluşturulan paketi test etmek için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Cmdlet 'i PowerShell ardışık düzeninde girişi de destekler. Cmdlet 'inin çıkışını `New-GuestConfigurationPackage` `Test-GuestConfigurationPackage` cmdlet 'ine boru.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./AuditBitlocker/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

Sonraki adım, dosyayı Azure Blob depolama alanına yayımlamaktır. Depolama hesabı için özel bir gereksinim yoktur, ancak dosyayı makinelerinizin yakınında bir bölgede barındırmak iyi bir fikirdir. Depolama hesabınız yoksa, aşağıdaki örneği kullanın. Aşağıdaki komutlar, ve dahil olmak üzere `Publish-GuestConfigurationPackage` `Az.Storage` modülü gerektirir.

```azurepowershell-interactive
# Creates a new resource group, storage account, and container
New-AzResourceGroup -name myResourceGroupName -Location WestUS
New-AzStorageAccount -ResourceGroupName myResourceGroupName -Name myStorageAccountName -SkuName 'Standard_LRS' -Location 'WestUs' | New-AzStorageContainer -Name guestconfiguration -Permission Blob
```

`Publish-GuestConfigurationPackage`Cmdlet parametreleri:

- **Yol**: yayımlanacak paketin konumu
- **Resourcegroupname**: depolama hesabının bulunduğu kaynak grubunun adı
- **StorageAccountName**: paketin yayımlanması gereken depolama hesabının adı
- **Storagecontainername**: (varsayılan: _guestconfiguration_) depolama hesabındaki depolama kapsayıcısının adı
- **Zorla**: aynı ada sahip depolama hesabındaki mevcut paketin üzerine yaz

Aşağıdaki örnek, paketi ' guestconfiguration ' depolama kapsayıcısı adına yayımlar.

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
```

Konuk yapılandırması özel ilke paketi oluşturulduktan ve karşıya yüklendikten sonra, Konuk yapılandırma ilkesi tanımını oluşturun. `New-GuestConfigurationPolicy`Cmdlet 'i özel bir ilke paketi alır ve bir ilke tanımı oluşturur.

`New-GuestConfigurationPolicy`Cmdlet parametreleri:

- **ContentUri**: Konuk yapılandırması içerik paketinin genel HTTP URI 'si.
- **DisplayName**: ilke görünen adı.
- **Açıklama**: ilke açıklaması.
- **Parametre**: Hashtable biçiminde belirtilen ilke parametreleri.
- **Sürüm**: ilke sürümü.
- **Yol**: ilke tanımlarının oluşturulduğu hedef yol.
- **Platform**: Konuk yapılandırma ilkesi ve içerik paketi için hedef platform (Windows/Linux).
- **Etiket** , ilke tanımına bir veya daha fazla etiket filtresi ekler
- **Kategori** , ilke tanımındaki kategori meta verileri alanını ayarlar

Aşağıdaki örnek, özel bir ilke paketinden belirtilen yolda ilke tanımlarını oluşturur:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

Aşağıdaki dosyalar tarafından oluşturulmuştur `New-GuestConfigurationPolicy` :

- **ÜzerindeauditIfNotExists.js**

Cmdlet çıktısı, ilke dosyalarının girişim görünen adını ve yolunu içeren bir nesne döndürür.

Son olarak, cmdlet 'ini kullanarak ilke tanımlarını yayımlayın `Publish-GuestConfigurationPolicy` . Cmdlet 'i yalnızca tarafından oluşturulan JSON dosyalarının konumuna işaret eden **Path** parametresine sahiptir `New-GuestConfigurationPolicy` .

Yayımla komutunu çalıştırmak için Azure 'da ilke oluşturma erişiminizin olması gerekir. Belirli yetkilendirme gereksinimleri, [Azure Ilkesine genel bakış](../overview.md) sayfasında belgelenmiştir. En iyi yerleşik rol, **kaynak Ilkesi katılımcısı**' dir.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

`Publish-GuestConfigurationPolicy`Cmdlet 'ı PowerShell işlem hattının yolunu kabul eder. Bu özellik, ilke dosyalarını oluşturabileceğiniz ve bunları tek bir dizi komut dosyası içinde yayımlayabileceği anlamına gelir.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Azure 'da oluşturulan ilkeyle, son adım tanımlamayı atayacaktır. Bkz. tanımı [Portal](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)ve [Azure PowerShell](../assign-policy-powershell.md)ile atama.

### <a name="filtering-guest-configuration-policies-using-tags"></a>Etiketleri kullanarak Konuk yapılandırma ilkelerini filtreleme

Konuk yapılandırma modülündeki cmdlet 'ler tarafından oluşturulan ilke tanımları, isteğe bağlı olarak etiketler için bir filtre içerebilir. Öğesinin **Tag** parametresi, `New-GuestConfigurationPolicy` tek bir etiket dizesi içeren bir diyez tabloları dizisini destekler. Etiketler, `If` ilke tanımının bölümüne eklenir ve bir ilke ataması tarafından değiştirilemez.

Aşağıdaki etiketlere filtre uygulayan bir ilke tanımının örnek parçacığı aşağıda verilmiştir.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content
    }
  ]
}
```

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Özel Konuk yapılandırma ilkesi tanımlarında parametreleri kullanma

Konuk yapılandırması, çalışma zamanında bir yapılandırmanın özelliklerini geçersiz kılmayı destekler. Bu özellik, paketteki MOF dosyasındaki değerlerin statik olarak değerlendirilmesi gerekmediği anlamına gelir. Geçersiz kılma değerleri Azure Ilkesi aracılığıyla sağlanır ve yapılandırmaların nasıl yazıldığı veya derlendiğini değiştirmez.

Cmdlet 'ler `New-GuestConfigurationPolicy` ve `Test-GuestConfigurationPolicyPackage` **parametresi** adlı bir parametre ekleyin. Bu parametre, her parametre hakkında tüm ayrıntılar dahil olmak üzere bir Hashtable tanımı alır ve Azure Ilke tanımı için kullanılan her bir dosyanın gerekli bölümlerini oluşturur.

Aşağıdaki örnek, kullanıcının ilke ataması sırasında bir listeden seçtiği bir hizmeti denetlemek için bir ilke tanımı oluşturur.

```azurepowershell-interactive
# This DSC Resource text:
Service 'UserSelectedNameExample'
      {
          Name = 'ParameterValue'
          Ensure = 'Present'
          State = 'Running'
      }

# Would require the following hashtable:
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'UserSelectedNameExample'                          # DSC configuration resource id (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>Üçüncü taraf araçlarla Konuk yapılandırmasını genişletme

Konuk yapılandırması için yapıt paketleri, üçüncü taraf araçları içerecek şekilde genişletilebilir.
Konuk yapılandırmasını genişletme iki bileşenin geliştirilmesini gerektirir.

- Üçüncü taraf aracının yönetimiyle ilgili tüm etkinlikleri işleyen Istenen bir durum yapılandırma kaynağı
  - Yükleme
  - Çağır
  - Çıkışı Dönüştür
- Yerel olarak kullanılacak araç için içerik doğru biçimde

Bir topluluk çözümü zaten yoksa DSC kaynağı için özel geliştirme gerekir.
Topluluk çözümleri, etiket [Guestconfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22)için PowerShell Galerisi arayarak bulunabilir.

> [!Note]
> Konuk yapılandırma genişletilebilirliği "kendi lisansını getir" senaryosudur. Kullanmadan önce herhangi bir üçüncü taraf aracının hüküm ve koşullarını karşıladığınızı doğrulayın.

Geliştirme ortamında DSC kaynağı yüklendikten sonra,  `New-GuestConfigurationPackage` içerik yapıtının üçüncü taraf platformu için Içerik eklemek Için filestoınclude parametresini kullanın.

## <a name="policy-lifecycle"></a>İlke yaşam döngüsü

İlkede bir güncelleştirme yayınlamak isterseniz, hem Konuk yapılandırma paketi hem de Azure Ilke tanımı ayrıntıları için değişikliği yapın.

> [!NOTE]
> `version`Konuk yapılandırma atamasının özelliği yalnızca Microsoft tarafından barındırılan etkiler. Özel içerik sürümü oluşturma için en iyi yöntem, dosyanın dosya adına dahil edileceğini içerir.

İlk olarak, çalışırken `New-GuestConfigurationPackage` , paket için önceki sürümlerden benzersiz olan bir ad belirtin. Adında bir sürüm numarası dahil edebilirsiniz `PackageName_1.0.0` . Bu örnekteki sayı yalnızca paketin benzersiz olması için kullanılır, paketin diğer paketlerden daha yeni veya daha eski olarak değerlendirilmesi gerektiğini belirtmemelidir.

İkinci olarak, `New-GuestConfigurationPolicy` aşağıdaki açıklamaları izleyerek cmdlet ile birlikte kullanılan parametreleri güncelleştirin.

- **Sürüm**: `New-GuestConfigurationPolicy` cmdlet 'ini çalıştırdığınızda, şu anda yayımlanmış olandan daha büyük bir sürüm numarası belirtmeniz gerekir.
- **contentUri**: `New-GuestConfigurationPolicy` cmdlet 'ini çalıştırdığınızda, PAKETIN konumuna bir URI belirtmeniz gerekir. Dosya adında bir paket sürümü de dahil olmak üzere, bu özelliğin değeri her sürümde değişir.
- **contentHash**: Bu özellik, cmdlet 'i tarafından otomatik olarak güncelleştirilir `New-GuestConfigurationPolicy` . Tarafından oluşturulan paketin karma değeridir `New-GuestConfigurationPackage` . Özelliği, yayımladığınız dosya için doğru olmalıdır `.zip` . Yalnızca **contentUri** özelliği güncelleştirilirse, uzantı içerik paketini kabul etmez.

Güncelleştirilmiş bir paketi yayımlamanın en kolay yolu, bu makalede açıklanan süreci tekrarlamanız ve güncelleştirilmiş bir sürüm numarası sağlamaktır. Bu işlem, tüm özelliklerin doğru şekilde güncelleştirildiğinden emin garanti eder.

## <a name="optional-signing-guest-configuration-packages"></a>İsteğe bağlı: Konuk yapılandırma paketleri Imzalanıyor

Konuk yapılandırması özel ilkeleri, ilke paketinin değiştirilmediğini doğrulamak için SHA256 karmasını kullanır.
İsteğe bağlı olarak, müşteriler paketleri imzalamak ve konuk yapılandırma uzantısını yalnızca imzalı içeriğe izin verecek şekilde zorlamak için bir sertifika da kullanabilir.

Bu senaryoyu etkinleştirmek için, gerçekleştirmeniz gereken iki adım vardır. İçerik paketini imzalamak için cmdlet 'ini çalıştırın ve kodun imzalanmasını gerektirecek makinelere bir etiket ekleyin.

Imza doğrulama özelliğini kullanmak için, `Protect-GuestConfigurationPackage` paketini yayımlanmadan önce imzalamak üzere cmdlet 'ini çalıştırın. Bu cmdlet ' kod Imzalama ' sertifikası gerektirir.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage`Cmdlet parametreleri:

- **Yol**: Konuk yapılandırma paketinin tam yolu.
- **Sertifika**: paketi imzalamak için kod imzalama sertifikası. Bu parametre yalnızca Windows için içerik imzalanırken desteklenir.

GuestConfiguration Aracısı, sertifika ortak anahtarının Windows makinelerde "güvenilen kök sertifika yetkilileri" bölümünde ve Linux makinelerdeki yolunda bulunmasını bekler `/usr/local/share/ca-certificates/extra` . İmzalanan içeriğin doğrulanması için düğüm için, özel ilkeyi uygulamadan önce makineye ortak anahtarı yükler. Bu işlem, VM içindeki herhangi bir teknik veya Azure Ilkesi kullanılarak gerçekleştirilebilir. Burada örnek bir şablon [verilmiştir](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Key Vault erişim ilkesi, dağıtım sırasında Işlem Kaynak sağlayıcısının sertifikalara erişmesine izin vermelidir. Ayrıntılı adımlar için bkz. [Azure Resource Manager sanal makineler için Key Vault ayarlama](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Aşağıda, bir imzalama sertifikasından ortak anahtarı dışarı aktarmak için makineye aktarmak üzere bir örnek verilmiştir.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

İçeriğiniz yayımlandıktan sonra, `GuestConfigPolicyCertificateValidation` `enabled` kod imzasının gerekli olması gereken tüm sanal makinelere ad ve değer içeren bir etiket ekleyin. Etiketlerin Azure Ilkesi kullanılarak nasıl ölçeklenebilmesini için [etiket örneklerine](../samples/built-in-policies.md#tags) bakın. Bu etiket oluşturulduktan sonra cmdlet kullanılarak oluşturulan ilke tanımı, `New-GuestConfigurationPolicy` Konuk yapılandırma uzantısı aracılığıyla gereksinimi mümkün bir şekilde sunar.

## <a name="next-steps"></a>Sonraki adımlar

- [Konuk yapılandırması](../concepts/guest-configuration.md)olan VM 'leri denetleme hakkında bilgi edinin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](./programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](./get-compliance-data.md)öğrenin.
