---
title: Linux için Konuk Yapılandırma ilkelerini oluşturma
description: Linux için Azure Ilkesi Konuk yapılandırma ilkesi oluşturmayı öğrenin.
ms.date: 03/31/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d356960987ecfe9a1e1858a28b93060dbf4aa634
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096572"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Linux için Konuk Yapılandırma ilkelerini oluşturma

Özel ilkeler oluşturmadan önce [Azure Ilke Konuk yapılandırması](../concepts/guest-configuration.md)' nın genel bakış bilgilerini okuyun.
 
Windows için konuk yapılandırma ilkeleri oluşturma hakkında bilgi edinmek için bkz. [Windows Için Konuk yapılandırma ilkeleri oluşturma](./guest-configuration-create.md) sayfası

Linux’ı denetlerken Konuk Yapılandırması [Chef InSpec](https://www.inspec.io/) kullanır. InSpec profili makinenin olması gereken durumu tanımlar. Yapılandırmanın değerlendirmesi başarısız olursa, **Auditınotexists** ilke efekti tetiklenir ve makine **uyumlu** değil olarak kabul edilir.

[Azure Ilke Konuk yapılandırması](../concepts/guest-configuration.md) , yalnızca makineler içindeki ayarları denetlemek için kullanılabilir. Makinelerin içindeki ayarların düzeltilmesi henüz kullanılamamaktadır.

Bir Azure veya Azure dışı makinenin durumunu doğrulamak üzere kendi yapılandırmanızı oluşturmak için aşağıdaki eylemleri kullanın.

> [!IMPORTANT]
> Azure Kamu ve Azure Çin ortamlarında Konuk yapılandırması olan özel ilke tanımları bir önizleme özelliğidir.
>
> Konuk Yapılandırma uzantısı Azure sanal makinelerinde denetim gerçekleştirmek için gereklidir. Uzantıyı tüm Linux makinelerinde ölçeklendirerek dağıtmak için aşağıdaki ilke tanımını atayın: `Deploy prerequisites to enable Guest Configuration Policy on Linux VMs`
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
> Yapılandırmaların derlenmesi Linux üzerinde desteklenmez.

### <a name="base-requirements"></a>Temel gereksinimler

Modülün yüklenebildiği işletim sistemleri:

- Linux
- macOS
- Windows

> [!NOTE]
> `Test-GuestConfigurationPackage`OMI üzerindeki bir bağımlılık nedeniyle cmdlet, OpenSSL sürüm 1,0 gerektirir. Bu, OpenSSL 1,1 veya üzeri ile herhangi bir ortamda hata oluşmasına neden olur.
>
> Cmdlet 'i çalıştırmak `Test-GuestConfigurationPackage` yalnızca Windows for Guest yapılandırma modülü sürüm 2.1.0 'de desteklenir.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Linux için konuk yapılandırma yapıtları ve ilkesi

Linux ortamlarında bile, Konuk yapılandırması, Istenen durum yapılandırmasını dil soyutlama olarak kullanır. Uygulama yerel kod (C++) tabanlıdır, bu nedenle PowerShell 'i yüklemek gerekmez. Bununla birlikte, ortamla ilgili ayrıntıları açıklayan bir yapılandırma MOF gerektirir.
DSC, InSpec 'in nasıl yürütüldüğünü, parametrelerin nasıl sağlandığını ve çıktının hizmete nasıl döndürüldüğünü standartlaştırmaya yönelik sarmalayıcı olarak davranır. Özel InSpec içeriğiyle çalışırken çok az DSC bilgisi gerekir.

#### <a name="configuration-requirements"></a>Yapılandırma gereksinimleri

Özel yapılandırmanın adı her yerde tutarlı olmalıdır. İçerik paketi için. zip dosyasının adı, MOF dosyasındaki yapılandırma adı ve Azure Resource Manager şablonundaki (ARM şablonu) Konuk atama adı aynı olmalıdır.

PowerShell cmdlet 'leri, paketi oluşturmaya yardımcı olur. Kök düzey klasörü veya sürüm klasörü gerekli değil. Paket biçimi bir. zip dosyası olmalıdır. ve sıkıştırılmamış olarak 100 MB 'lık toplam boyutu aşamaz.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux üzerinde özel konuk yapılandırma yapılandırması

Linux üzerinde Konuk yapılandırması, `ChefInSpecResource` altyapıyı [InSpec profilinin](https://www.inspec.io/docs/reference/profiles/)adını sağlamak için kaynağını kullanır. **Ad** , tek gerekli kaynak özelliğidir. Aşağıda açıklandığı gibi bir YaML dosyası ve Ruby betiği dosyası oluşturun.

İlk olarak, InSpec tarafından kullanılan YaML dosyasını oluşturun. Dosya, ortam hakkında temel bilgileri sağlar. Aşağıda bir örnek verilmiştir:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Bu dosyayı adı ile `inspec.yml` Proje dizininizde adlı bir klasöre kaydedin `linux-path` .

Sonra, makineyi denetlemek için kullanılan InSpec Language soyutlama ile Ruby dosyasını oluşturun.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Bu dosyayı `linux-path.rb` , dizin içinde adlı yeni bir klasöre kaydedin `controls` `linux-path` .

Son olarak, bir yapılandırma oluşturun, **Psdesiredstateconfiguration** kaynak modülünü içeri aktarın ve yapılandırmayı derleyin.

```powershell
# import PSDesiredStateConfiguration module
import-module PSDesiredStateConfiguration

# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
AuditFilePathExists -out ./Config
```

Bu dosyayı `config.ps1` Proje klasörüne kaydedin. Terminalde yürüterek PowerShell 'de çalıştırın `./config.ps1` . Yeni bir MOF dosyası oluşturulacak.

`Node AuditFilePathExists`Komut Teknik olarak gerekli değildir `AuditFilePathExists.mof` , ancak varsayılan olarak değil adlı bir dosya oluşturur `localhost.mof` . . Mof dosya adının yapılandırılması, ölçeklendirmeye çalışırken birçok dosyayı düzenlemeyi kolaylaştırır.

Artık aşağıdaki gibi bir proje yapısına sahip olmanız gerekir:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        inspec.yml
        / controls
            linux-path.rb 
```

Destekleyici dosyaların birlikte paketlenmesi gerekir. Tamamlanmış paket, Azure Ilke tanımlarını oluşturmak için konuk yapılandırması tarafından kullanılır.

`New-GuestConfigurationPackage`Cmdlet 'i paketi oluşturur. `New-GuestConfigurationPackage`Linux içeriği oluşturulurken cmdlet 'in parametreleri:

- **Ad**: Konuk yapılandırma paketi adı.
- **Yapılandırma**: derlenen yapılandırma belgesi tam yolu.
- **Yol**: çıkış klasörü yolu. Bu parametre isteğe bağlıdır. Belirtilmezse, paket geçerli dizinde oluşturulur.
- **Chefinspecprofilepath**: InSpec profiline yönelik tam yol. Bu parametre yalnızca Linux 'u denetlemek için içerik oluşturulurken desteklenir.

Önceki adımda verilen yapılandırmayı kullanarak bir paket oluşturmak için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

Yapılandırma paketini oluşturduktan, ancak Azure 'a yayımlamadan önce, paketi iş istasyonunuzdan veya sürekli tümleştirme ve sürekli dağıtım (CI/CD) ortamınızdan test edebilirsiniz. GuestConfiguration cmdlet 'i, `Test-GuestConfigurationPackage` Azure makinelerinde kullanıldığı gibi geliştirme ortamınızda aynı aracıyı içerir. Bu çözümü kullanarak, faturalandırılan bulut ortamlarına bırakmadan önce tümleştirme testini yerel olarak gerçekleştirebilirsiniz.

Aracı gerçekten yerel ortamı değerlendirdiğinden, çoğu durumda test-cmdlet 'ini, denetlemeyi planladığınız aynı işletim sistemi platformunda çalıştırmanız gerekir.

`Test-GuestConfigurationPackage`Cmdlet parametreleri:

- **Ad**: Konuk yapılandırma ilkesi adı.
- **Parametre**: Hashtable biçiminde belirtilen ilke parametreleri.
- **Yol**: Konuk yapılandırma paketinin tam yolu.

Önceki adım tarafından oluşturulan paketi test etmek için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

Cmdlet 'i PowerShell ardışık düzeninde girişi de destekler. Cmdlet 'inin çıkışını `New-GuestConfigurationPackage` `Test-GuestConfigurationPackage` cmdlet 'ine boru.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefInspecProfilePath './' | Test-GuestConfigurationPackage
```

Sonraki adım, dosyayı Azure Blob depolama alanına yayımlamaktır. Komut `Publish-GuestConfigurationPackage` `Az.Storage` modülü gerektiriyor.

`Publish-GuestConfigurationPackage`Cmdlet parametreleri:

- **Yol**: yayımlanacak paketin konumu
- **Resourcegroupname**: depolama hesabının bulunduğu kaynak grubunun adı
- **StorageAccountName**: paketin yayımlanması gereken depolama hesabının adı
- **Storagecontainername**: (varsayılan: _guestconfiguration_) depolama hesabındaki depolama kapsayıcısının adı
- **Zorla**: aynı ada sahip depolama hesabındaki mevcut paketin üzerine yaz

Aşağıdaki örnek, paketi ' guestconfiguration ' depolama kapsayıcısı adına yayımlar.

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditFilePathExists/AuditFilePathExists.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Aşağıdaki dosyalar tarafından oluşturulmuştur `New-GuestConfigurationPolicy` :

- **ÜzerindeauditIfNotExists.js**

Cmdlet çıktısı, ilke dosyalarının girişim görünen adını ve yolunu içeren bir nesne döndürür.

Son olarak, cmdlet 'ini kullanarak ilke tanımlarını yayımlayın `Publish-GuestConfigurationPolicy` . Cmdlet 'i yalnızca tarafından oluşturulan JSON dosyalarının konumuna işaret eden **Path** parametresine sahiptir `New-GuestConfigurationPolicy` .

Yayımla komutunu çalıştırmak için Azure 'da Ilke oluşturma erişiminizin olması gerekir. Belirli yetkilendirme gereksinimleri, [Azure Ilkesine genel bakış](../overview.md) sayfasında belgelenmiştir. En iyi yerleşik rol, **kaynak Ilkesi katılımcısı**' dir.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path './policies'
```

 `Publish-GuestConfigurationPolicy`Cmdlet 'ı PowerShell işlem hattının yolunu kabul eder. Bu özellik, ilke dosyalarını oluşturabileceğiniz ve bunları tek bir dizi komut dosyası içinde yayımlayabileceği anlamına gelir.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

Azure 'da oluşturulan ilkeyle, son adım tanımlamayı atayacaktır. Bkz. tanımı [Portal](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)ve [Azure PowerShell](../assign-policy-powershell.md)ile atama.

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Özel Konuk yapılandırma ilkelerinde parametreleri kullanma

Konuk yapılandırması, çalışma zamanında bir yapılandırmanın özelliklerini geçersiz kılmayı destekler. Bu özellik, paketteki MOF dosyasındaki değerlerin statik olarak değerlendirilmesi gerekmediği anlamına gelir. Geçersiz kılma değerleri Azure Ilkesi aracılığıyla sağlanır ve yapılandırmaların nasıl yazıldığı veya derlendiğini değiştirmez.

InSpec ile parametreler tipik olarak çalışma zamanında ya da öznitelikler kullanılarak kod olarak işlenir. Konuk yapılandırması bu işlemi, ilke atandığında giriş sağlanabileceği şekilde gizleme. Makine içinde bir öznitelik dosyası otomatik olarak oluşturulur. Projenizde bir dosya oluşturmanız ve eklemeniz gerekmez. Linux Denetim projenize parametre eklemenin iki adımı vardır.

Girişi, makinede nelerin denetleneceğini betiğinizdeki Ruby dosyasında tanımlayın. Aşağıda bir örnek verilmiştir.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Yapılandırmanızda **Attributesymlcontent** özelliğini, değer olarak herhangi bir dizeyle ekleyin.
Konuk yapılandırma Aracısı, öznitelikleri depolamak için InSpec tarafından kullanılan YAML dosyasını otomatik olarak oluşturur. Aşağıdaki örneğe bakın.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "fromParameter"
        }
    }
}
```

MOF dosyasını bu belgede verilen örnekleri kullanarak yeniden derleyin.

Cmdlet 'ler `New-GuestConfigurationPolicy` ve `Test-GuestConfigurationPolicyPackage` **parametresi** adlı bir parametre ekleyin. Bu parametre, her bir parametre hakkında tüm ayrıntıları içeren bir Hashtable alır ve her bir Azure Ilke tanımını oluşturmak için kullanılan dosyaların tüm gerekli bölümlerini otomatik olarak oluşturur.

Aşağıdaki örnek, bir dosya yolunu denetlemek için bir ilke tanımı oluşturur; burada Kullanıcı, ilke ataması sırasında yolu sağlar.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = 'File path to be audited.'      # Policy parameter description (optional)
        ResourceType = 'ChefInSpecResource'           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = 'AttributesYmlContent' # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

$uri = 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D'

New-GuestConfigurationPolicy -ContentUri $uri `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameter $PolicyParameterInfo `
    -Platform 'Linux' `
    -Version 1.0.0
```


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

### <a name="filtering-guest-configuration-policies-using-tags"></a>Etiketleri kullanarak Konuk yapılandırma ilkelerini filtreleme

Konuk yapılandırma modülündeki cmdlet 'ler tarafından oluşturulan ilkeler, isteğe bağlı olarak etiketler için bir filtre içerebilir. Öğesinin **-Tag** parametresi, `New-GuestConfigurationPolicy` tek bir etiket dizesi içeren bir diyez tabloları dizisini destekler. Etiketler, `If` ilke tanımının bölümüne eklenecektir ve bir ilke ataması tarafından değiştirilemez.

Aşağıda etiketi filtreleyecek bir ilke tanımının örnek parçacığı aşağıda verilmiştir.

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
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>İsteğe bağlı: Konuk yapılandırma paketleri Imzalanıyor

Konuk yapılandırması özel ilkeleri, ilke paketinin değiştirilmediğini doğrulamak için SHA256 karmasını kullanır.
İsteğe bağlı olarak, müşteriler paketleri imzalamak ve konuk yapılandırma uzantısını yalnızca imzalı içeriğe izin verecek şekilde zorlamak için bir sertifika da kullanabilir.

Bu senaryoyu etkinleştirmek için, gerçekleştirmeniz gereken iki adım vardır. İçerik paketini imzalamak için cmdlet 'ini çalıştırın ve kodun imzalanmasını gerektirecek makinelere bir etiket ekleyin.

Imza doğrulama özelliğini kullanmak için, `Protect-GuestConfigurationPackage` paketini yayımlanmadan önce imzalamak üzere cmdlet 'ini çalıştırın. Bu cmdlet ' kod Imzalama ' sertifikası gerektirir.

`Protect-GuestConfigurationPackage`Cmdlet parametreleri:

- **Yol**: Konuk yapılandırma paketinin tam yolu.
- **Publicgpgkeypath**: genel GPG anahtar yolu. Bu parametre yalnızca Linux için içerik imzalanırken desteklenir.

Linux makinelerle kullanılmak üzere GPG anahtarları oluşturmaya yönelik iyi bir başvuru, GitHub 'daki bir makale tarafından sağlanır ve [Yeni BIR gpg anahtarı](https://help.github.com/en/articles/generating-a-new-gpg-key)oluşturur.

GuestConfiguration Aracısı, Linux makinelerdeki yolda sertifika ortak anahtarının bulunmasını bekler `/usr/local/share/ca-certificates/extra` . İmzalanan içeriğin doğrulanması için düğüm için, özel ilkeyi uygulamadan önce makineye ortak anahtarı yükler. Bu işlem, VM içindeki herhangi bir teknik veya Azure Ilkesi kullanılarak gerçekleştirilebilir. Burada örnek bir şablon [verilmiştir](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Key Vault erişim ilkesi, dağıtım sırasında Işlem Kaynak sağlayıcısının sertifikalara erişmesine izin vermelidir. Ayrıntılı adımlar için bkz. [Azure Resource Manager sanal makineler için Key Vault ayarlama](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

İçeriğiniz yayımlandıktan sonra, `GuestConfigPolicyCertificateValidation` `enabled` kod imzasının gerekli olması gereken tüm sanal makinelere ad ve değer içeren bir etiket ekleyin. Etiketlerin Azure Ilkesi kullanılarak nasıl ölçeklenebilmesini için [etiket örneklerine](../samples/built-in-policies.md#tags) bakın. Bu etiket oluşturulduktan sonra cmdlet kullanılarak oluşturulan ilke tanımı, `New-GuestConfigurationPolicy` Konuk yapılandırma uzantısı aracılığıyla gereksinimi mümkün bir şekilde sunar.

## <a name="next-steps"></a>Sonraki adımlar

- [Konuk yapılandırması](../concepts/guest-configuration.md)olan VM 'leri denetleme hakkında bilgi edinin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](./programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](./get-compliance-data.md)öğrenin.
