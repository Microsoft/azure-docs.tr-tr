---
title: Sanal makinelerin içeriğini denetleme hakkında bilgi edinin
description: Azure Ilkesi 'nin bir Azure makinesi içindeki ayarları denetlemek için konuk yapılandırmasını nasıl kullandığını öğrenin.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 5148ecb2f10a2ac517c5cf6c7f682a0f25808910
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959772"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure İlkesi'nin Konuk yapılandırma anlama

Azure Ilkesi, Azure kaynaklarını denetlemeye ve yeniden [düzeltmelere](../how-to/remediate-resources.md) göre, bir makine içindeki ayarları denetleyebilir. Doğrulama, Konuk Yapılandırması uzantısı ve istemcisi tarafından gerçekleştirilir. Uzantı, istemci aracılığıyla şunun gibi ayarları doğrular:

- İşletim sisteminin yapılandırması
- Uygulama yapılandırması veya varlığı
- Ortam ayarları

Şu anda Azure İlkesi Konuk Yapılandırması yalnızca makinenin içindeki ayarları denetlemektedir. Yapılandırma uygulamamaktadır.

## <a name="extension-and-client"></a>Uzantı ve istemci

Bir makine içindeki ayarları denetlemek için, bir [sanal makine uzantısı](../../../virtual-machines/extensions/overview.md) etkindir. Uzantı geçerli ilke ataması ve karşılık gelen yapılandırma tanımını indirir.

### <a name="limits-set-on-the-extension"></a>Uzantı üzerinde ayarlanan sınırlar

Uzantının makinede çalışan etkileyen uygulamalarla sınırlandırılacağından, Konuk yapılandırmasının CPU kullanımının %5 ' inden fazla olmasına izin verilmez. Bu sınırlama hem yerleşik hem de özel tanımlar için vardır.

## <a name="register-guest-configuration-resource-provider"></a>Konuk yapılandırma kaynak sağlayıcısını kaydetme

Konuk yapılandırma kullanabilmeniz için kaynak sağlayıcısını kaydetmeniz gerekir. Portal veya PowerShell aracılığıyla kaydedebilirsiniz. Bir konuk yapılandırma ilkesinin atanması Portal üzerinden yapıldığında kaynak sağlayıcı otomatik olarak kaydedilir.

### <a name="registration---portal"></a>Kayıt - Portal

Azure portalı üzerinden Konuk yapılandırması için kaynak sağlayıcısını kaydetmek için aşağıdaki adımları izleyin:

1. Azure portalını başlatma ve tıklayarak **tüm hizmetleri**. Arayın ve seçin **abonelikleri**.

1. Bulun ve Konuk yapılandırma için etkinleştirmek istediğiniz aboneliğe tıklayın.

1. Soldaki menüde **abonelik** sayfasında **kaynak sağlayıcıları**.

1. Filtre uygulamak veya bulduktan kadar kaydırın **Microsoft.GuestConfiguration**, ardından **kaydetme** aynı satırda.

### <a name="registration---powershell"></a>Kayıt - PowerShell

PowerShell üzerinden Konuk yapılandırması için kaynak sağlayıcısını kaydetmek için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Doğrulama Araçları

Makinenin içinde, Konuk yapılandırma istemcisi denetimi çalıştırmak için yerel araçları kullanır.

Aşağıdaki tabloda, desteklenen her işletim sisteminde kullanılan yerel Araçlar listesi gösterilmektedir:

|İşletim sistemi|Doğrulama Aracı|Notlar|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby ve Python Konuk Configuration uzantısı tarafından yüklenir. |

### <a name="validation-frequency"></a>Doğrulama sıklığı

Konuk yapılandırması istemcisi, her 5 dakikada bir yeni içerik denetler. Konuk ataması alındıktan sonra ayarlar 15 dakikalık bir aralıkta denetlenir. Sonuçlar, Denetim tamamlandıktan hemen sonra Konuk yapılandırma kaynak sağlayıcısına gönderilir. Bir ilke [değerlendirme tetikleyicisi](../how-to/get-compliance-data.md#evaluation-triggers) gerçekleştiğinde, makinenin durumu Konuk yapılandırma kaynak sağlayıcısına yazılır. Bu güncelleştirme, Azure Ilkesinin Azure Resource Manager özelliklerini değerlendirmesini sağlar. İsteğe bağlı bir Azure Ilke değerlendirmesi, Konuk yapılandırma kaynak sağlayıcısından en son değeri alır. Ancak, makine içindeki yapılandırmanın yeni bir denetimini tetiklemez.

## <a name="supported-client-types"></a>Desteklenen istemci türleri

Aşağıdaki tabloda, desteklenen işletim sistemi listesini Azure görüntülerinde gösterilmektedir:

|Yayımcı|Ad|Sürümler|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Windows İstemcisi|Windows 10|
|OpenLogic|CentOS|7.3, 7.4 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SuSE|SLES|12 SP3|

> [!IMPORTANT]
> Konuk yapılandırması, desteklenen bir işletim sistemi çalıştıran düğümleri denetleyebilir. Özel bir görüntü kullanan sanal makineleri denetlemek isterseniz, **Deployifnotexists** tanımını çoğaltmanız ve resim özelliklerinizi dahil etmek için **IF** bölümünü değiştirmeniz gerekir.

### <a name="unsupported-client-types"></a>Desteklenmeyen istemci türleri

Windows Server nano Server hiçbir sürümde desteklenmez.

## <a name="guest-configuration-extension-network-requirements"></a>Konuk yapılandırma uzantısı ağ gereksinimleri

Azure 'daki Konuk yapılandırma kaynak sağlayıcısıyla iletişim kurmak için makineler **443**numaralı bağlantı noktasında Azure veri merkezlerine giden erişim gerektirir. Azure 'da giden trafiğe izin veren özel bir sanal ağ kullanıyorsanız, [ağ güvenlik grubu](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) kuralları ile özel durumlar yapılandırın. Azure Ilke Konuk yapılandırması için şu anda bir hizmet etiketi yok.

IP adresi listelerinde [Microsoft Azure veri MERKEZI IP aralıklarını](https://www.microsoft.com/download/details.aspx?id=41653)indirebilirsiniz. Bu dosya haftalık olarak güncelleştirilir ve şu anda dağıtılmış aralıklar ve IP aralıklarında yaklaşan değişiklikler vardır. Yalnızca sanal makinelerinizin dağıtıldığı bölgelerdeki IP 'lere giden erişime izin vermeniz gerekir.

> [!NOTE]
> Azure veri merkezi IP adresi XML dosyası Microsoft Azure veri merkezlerinde kullanılan IP adresi aralıklarını listeler. Dosya işlem, SQL ve depolama aralıklarını içerir. Güncelleştirilmiş bir dosya haftalık olarak gönderilir. Dosya, şu anda dağıtılmış aralıkları ve IP aralıklarında yaklaşan değişiklikleri yansıtır. Dosyada görüntülenen yeni aralıklar, en az bir hafta için veri merkezlerinde kullanılmıyor. Her hafta yeni XML dosyasını indirmek iyi bir fikirdir. Daha sonra, sitenizi Azure 'da çalışan hizmetleri doğru şekilde belirlemek için güncelleştirin. Azure ExpressRoute kullanıcıları bu dosyanın, her ayın ilk haftasında Azure Space Sınır Ağ Geçidi Protokolü (BGP) tanıtımını güncelleştirmek için kullanıldığını unutmayın.

## <a name="guest-configuration-definition-requirements"></a>Konuk yapılandırma tanımı gereksinimleri

Konuk yapılandırması tarafından çalıştırılan her denetim, bir **Deployifnotexists** tanımı ve bir **auditınotexists** tanımı olmak üzere iki ilke tanımı gerektirir. **Deployifnotexists** tanımı, makineyi Konuk yapılandırma Aracısı ve diğer bileşenleri [doğrulama araçlarını](#validation-tools)destekleyecek şekilde hazırlamak için kullanılır.

**Deployıfnotexists** ilke tanımı doğrular ve düzeltir aşağıdaki öğeleri:

- Makineye değerlendirmek için bir yapılandırma atandığını doğrulayın. Şu anda bir atama yoksa, atamayı alın ve makineyi şu şekilde hazırlayın:
  - [Yönetilen kimlik](../../../active-directory/managed-identities-azure-resources/overview.md) kullanarak makinede kimlik doğrulama
  - En son sürümünü yükleme **Microsoft.GuestConfiguration** uzantısı
  - Yükleme [Doğrulama Araçları](#validation-tools) ve gerekirse bağımlılıkları

**Deployifnotexists** ataması uyumlu değilse, bir [Düzeltme görevi](../how-to/remediate-resources.md#create-a-remediation-task) kullanılabilir.

**Deployifnotexists** ataması uyumlu olduğunda, **auditınotexists** ilke ataması, yapılandırma atamasının uyumlu veya uyumsuz olduğunu anlamak için yerel doğrulama araçlarını kullanır. Doğrulama Aracı sonuçları Konuk yapılandırma istemciye sağlar. İstemci, Konuk yapılandırma kaynak sağlayıcısı kullanılabilir hale getirir Konuk uzantısına sonuçları iletir.

Azure İlkesi kullanan Konuk yapılandırma kaynak sağlayıcıları **complianceStatus** rapor uyumluluk özelliğini **Uyumluluk** düğümü. Daha fazla bilgi için [uyumluluk verilerini alma](../how-to/getting-compliance-data.md).

> [!NOTE]
> **Uıınotexists** ilkesi, sonuçları döndürmek için bu **ilke için** gereklidir. **Deployifnotexists**olmadan, **auditınotexists** ilkesi "0/0" kaynağını durum olarak gösterir.

Tüm yerleşik ilkeleri Konuk yapılandırması için girişim atamaları tanımlarında kullanın grubuna dahil edilmiştir. \[Preview\]adlı yerleşik girişim _: Linux ve Windows makineler Içindeki denetim parolası güvenlik ayarları_ 18 ilke içerir. Altı **Deployıfnotexists** ve **AuditIfNotExists** Windows ve Linux için üç çift çifti. [İlke tanımı](definition-structure.md#policy-rule) mantığı yalnızca hedef işletim sisteminin değerlendirildiğini doğrular.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Sektör temellerini izleyen işletim sistemi ayarlarını denetleme

Azure Ilkesinde bulunan girişimlerden biri, Microsoft 'tan bir "taban çizgisi" dan sonra sanal makineler içindeki işletim sistemi ayarlarını denetleme olanağı sağlar. Tanım, _\[önizleme\]: Azure Güvenlik temeli ayarlarıyla eşleşmeyen Windows VM 'Lerinin denetimi_ , Active Directory Grup İlkesi ayarlarına bağlı olarak tüm denetim kuralları kümesini içerir.

Ayarların çoğu parametre olarak kullanılabilir. Bu işlevsellik, ilkeyi Kurumsal gereksinimlerinize göre hizalamak veya ilkeyi sektör mevzuatı standartları gibi üçüncü taraf bilgilerle eşlemek için nelerin denetleneceğini özelleştirmenizi sağlar.

Bazı parametreler bir tamsayı değer aralığını destekler. Örneğin, en fazla parola yaşı parametresi, makine sahiplerine esneklik sağlamak için bir Aralık işleci kullanılarak ayarlanabilir. Kullanıcıların parolalarını değiştirmesi gereken geçerli grup ilkesi ayarının 70 günden fazla olmaması gerektiğini ve bir günden az olmaması gerektiğini denetleyebilirsiniz. Parametrenin info-kabarcık bölümünde açıklandığı gibi, bu iş ilkesini etkin denetim değeri yapmak için değeri "1, 70" olarak ayarlayın.

İlkeyi bir Azure Resource Manager dağıtım şablonu kullanarak atarsanız, bu ayarları kaynak denetiminden yönetmek için bir parametre dosyası kullanabilirsiniz. Bir atamanın neden beklenen değer için bir özel durum olması gerektiğine dair her bir iade belgesi hakkında yorumlarla denetim ilkelerine yapılan değişiklikleri yönetmek için git gibi bir araç kullanma.

#### <a name="applying-configurations-using-guest-configuration"></a>Konuk yapılandırması kullanılarak yapılandırmaları uygulama

Azure Ilkesinin en son özelliği makineler içindeki ayarları yapılandırır. _Windows makinelerinde saat dilimini yapılandırma_ tanımı, saat dilimini yapılandırarak makinede değişiklikler yapar.

_Yapılandırma_ile başlayan tanımları atarken, _Windows VM 'Lerde Konuk yapılandırma ilkesini etkinleştirmek için tanım dağıtma ön koşullarını_da atamanız gerekir. Seçeneğini belirlerseniz bu tanımları bir girişim içinde birleştirebilirsiniz.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Azure dışındaki makinelere ilke atama

Konuk yapılandırması için kullanılabilen denetim ilkeleri **Microsoft. HybridCompute/machines** kaynak türünü içerir. İlke atamasının kapsamındaki [sunucular Için Azure yaya](../../../azure-arc/servers/overview.md) eklendi tüm makineler otomatik olarak eklenir.

### <a name="multiple-assignments"></a>Birden çok atama

Konuk yapılandırma ilkeleri Şu anda yalnızca makine başına aynı Konuk atamasının atanmasını, Ilke ataması farklı parametreler kullanıyor olsa bile destekler.

## <a name="built-in-resource-modules"></a>Yerleşik kaynak modülleri

Konuk yapılandırma uzantısı yüklenirken, ' GuestConfiguration ' PowerShell modülü DSC kaynak modüllerinin en son sürümüne dahildir. Modül sayfası [Guestconfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/)' dan ' el ile indirme ' bağlantısı kullanılarak bu modül PowerShell Galerisi indirilebilir. '. Nupkg ' dosya biçimi, sıkıştırmayı açmak ve gözden geçirmek için '. zip ' olarak yeniden adlandırılabilir.

## <a name="client-log-files"></a>İstemci günlük dosyaları

Konuk yapılandırma uzantısı, günlük dosyalarını aşağıdaki konumlara Yazar:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

`<version>`, geçerli sürüm numarasını belirtir.

### <a name="collecting-logs-remotely"></a>Günlükleri uzaktan toplama

Konuk yapılandırma yapılandırmalarının veya modülleriyle ilgili sorunları gidermeye yönelik ilk adım, [Konuk yapılandırma paketini test](../how-to/guest-configuration-create.md#test-a-guest-configuration-package)etme bölümündeki adımları izleyerek `Test-GuestConfigurationPackage` cmdlet 'ini kullanmalıdır.
Bu başarılı olmazsa, istemci günlüklerinin toplanması sorunları tanılamanıza yardımcı olabilir.

#### <a name="windows"></a>Windows

Windows makinelerdeki günlük dosyalarından bilgi yakalamak üzere Azure VM çalıştırma komutu özelliğini kullanmak için aşağıdaki örnek PowerShell betiği yararlı olabilir. Daha fazla bilgi için bkz. [Run komutuyla WINDOWS sanal makinenizde PowerShell betikleri çalıştırma](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Linux makinelerdeki günlük dosyalarından bilgi yakalamak için Azure VM çalıştırma komutunu kullanmak üzere aşağıdaki örnek Bash betiği yararlı olabilir. Daha fazla bilgi için bkz. [Run komutuyla LINUX sanal makinenizde Shell betikleri çalıştırma](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Konuk yapılandırma örnekleri

Ilke Konuk yapılandırması için örnekler aşağıdaki konumlarda kullanılabilir:

- [Örnek dizini-Konuk yapılandırması](../samples/index.md#guest-configuration)
- [Azure Ilke örnekleri GitHub deposu](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [Azure İlkesi tanımı yapısını](definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](effects.md) konusunu gözden geçirin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](../how-to/programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](../how-to/getting-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.
