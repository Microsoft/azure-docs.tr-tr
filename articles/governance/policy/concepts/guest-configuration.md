---
title: Sanal makinelerin içeriğini denetleme hakkında bilgi edinin
description: Azure Ilkesi 'nin sanal makineler içindeki ayarları denetlemek için konuk yapılandırma istemcisini nasıl kullandığını öğrenin.
ms.date: 01/14/2021
ms.topic: conceptual
ms.openlocfilehash: 6fb3ed3644ccdb5de8f03bedf56943a91570322b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105733035"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure İlkesi’nin Konuk Yapılandırmasını anlama


Azure Ilkesi, hem Azure 'da hem de [yay bağlantılı makinelerde](../../../azure-arc/servers/overview.md)çalışan makineler için bir makine içindeki ayarları denetleyebilir. Doğrulama, Konuk Yapılandırması uzantısı ve istemcisi tarafından gerçekleştirilir. Uzantı, istemci aracılığıyla şunun gibi ayarları doğrular:

- İşletim sisteminin yapılandırması
- Uygulama yapılandırması veya varlığı
- Ortam ayarları

Şu anda, çoğu Azure Ilkesi Konuk yapılandırma ilkesi tanımı yalnızca makinenin içindeki ayarları denetler. Yapılandırma uygulamamaktadır. Özel durum [aşağıda başvurulan](#applying-configurations-using-guest-configuration)bir yerleşik ilkedir.

[Bu belgenin üzerinde bir video izlenecek yol var](https://youtu.be/Y6ryD3gTHOs).

## <a name="enable-guest-configuration"></a>Konuk yapılandırmasını etkinleştir

Azure 'daki makineler ve yay bağlantılı makineler dahil ortamınızdaki makinelerin durumunu denetlemek için aşağıdaki ayrıntıları gözden geçirin.

## <a name="resource-provider"></a>Kaynak sağlayıcısı

Konuk yapılandırması 'nı kullanabilmeniz için önce kaynak sağlayıcısını kaydetmeniz gerekir. Bir konuk yapılandırma ilkesinin atanması Portal üzerinden yapılır veya abonelik Azure Güvenlik Merkezi 'ne kaydedildiyse, kaynak sağlayıcısı otomatik olarak kaydedilir. [Portal](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure POWERSHELL](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)veya [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)aracılığıyla el ile kayıt yapabilirsiniz.

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Azure sanal makineleri için gereksinimleri dağıtma

Bir makine içindeki ayarları denetlemek için, bir [sanal makine uzantısı](../../../virtual-machines/extensions/overview.md) etkinleştirilir ve makinede sistem tarafından yönetilen bir kimlik olması gerekir. Uzantı, uygulanabilir ilke atamasını ve buna karşılık gelen yapılandırma tanımını indirir. Kimlik, Konuk yapılandırma hizmetine okuduğu ve yazdığı makinenin kimliğini doğrulamak için kullanılır. Arc bağlantılı makine aracısına eklendiğinden, bu uzantı, Arc bağlantılı makineler için gerekli değildir.

> [!IMPORTANT]
> Azure sanal makinelerini denetlemek için konuk yapılandırma uzantısı ve yönetilen bir kimlik gereklidir. Uzantıyı ölçekli olarak dağıtmak için aşağıdaki ilke girişim atamasını yapın:
> 
> `Deploy prerequisites to enable Guest Configuration policies on virtual machines`

### <a name="limits-set-on-the-extension"></a>Uzantı üzerinde ayarlanan sınırlar

Uzantının makinede çalışan etkileyen uygulamalarla sınırlandırılacağından, Konuk yapılandırmasının CPU 'nun %5 ' inden fazlasını aşmasına izin verilmez. Bu sınırlama hem yerleşik hem de özel tanımlar için vardır. Aynı değer, yay bağlantılı makine aracısındaki Konuk yapılandırma hizmeti için de geçerlidir.

### <a name="validation-tools"></a>Doğrulama araçları

Makinenin içinde, Konuk yapılandırma istemcisi denetimi çalıştırmak için yerel araçları kullanır.

Aşağıdaki tabloda, desteklenen her bir işletim sisteminde kullanılan yerel araçların bir listesi gösterilmektedir. Yerleşik içerikte, Konuk yapılandırma bu araçları otomatik olarak yüklemeyi işler.

|İşletim sistemi|Doğrulama Aracı|Notlar|
|-|-|-|
|Windows|[PowerShell Istenen durum yapılandırması](/powershell/scripting/dsc/overview/overview) v2| Yan yana yalnızca Azure Ilkesi tarafından kullanılan bir klasöre yüklenir. Windows PowerShell DSC ile çakışmaz. PowerShell Core, sistem yoluna eklenmedi.|
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Chef InSpec Version 2.2.61 'i varsayılan konuma yükleyip sistem yoluna eklenir. Ruby ve Python dahil InSpec paketine yönelik bağımlılıklar da yüklenir. |

### <a name="validation-frequency"></a>Doğrulama sıklığı

Konuk yapılandırması istemcisi, her 5 dakikada bir yeni veya değiştirilmiş Konuk atamalarını denetler. Konuk ataması alındıktan sonra, bu yapılandırmanın ayarları 15 dakikalık bir aralıkta yeniden denetlenir. Sonuçlar, denetim tamamlandığında Konuk yapılandırma kaynak sağlayıcısına gönderilir. Bir ilke [değerlendirme tetikleyicisi](../how-to/get-compliance-data.md#evaluation-triggers) gerçekleştiğinde, makinenin durumu Konuk yapılandırma kaynak sağlayıcısına yazılır. Bu güncelleştirme, Azure Ilkesinin Azure Resource Manager özelliklerini değerlendirmesini sağlar. İsteğe bağlı bir Azure Ilke değerlendirmesi, Konuk yapılandırma kaynak sağlayıcısından en son değeri alır. Ancak, makine içindeki yapılandırmanın yeni bir denetimini tetiklemez. Durum, Azure Kaynak grafiğine aynı anda yazılır.

## <a name="supported-client-types"></a>Desteklenen istemci türleri

Konuk yapılandırma ilkesi tanımları yeni sürümlere dahildir. Konuk yapılandırma istemcisi uyumlu değilse, Azure Marketi 'nde kullanılabilen işletim sistemlerinin daha eski sürümleri hariç tutulur. Aşağıdaki tabloda, Azure görüntülerinde desteklenen işletim sistemlerinin bir listesi gösterilmektedir:

|Publisher|Name|Sürümler|
|-|-|-|
|Canonical|Ubuntu Server|14,04-20,04|
|Credavtiv|Debian|8 - 10|
|Microsoft|Windows Server|2012-2019|
|Microsoft|Windows İstemcisi|Windows 10|
|OpenLogic|CentOS|7,3-8|
|Red Hat|Red Hat Enterprise Linux|7,4-8|
|SUSE|SLES|12 SP3-SP5, 15|

Özel sanal makine görüntüleri, Konuk yapılandırma ilkesi tanımları tarafından, yukarıdaki tablodaki işletim sistemlerinden biri oldukları sürece desteklenir.

## <a name="network-requirements"></a>Ağ gereksinimleri

Azure 'daki sanal makineler, Konuk Yapılandırma hizmetiyle iletişim kurmak için yerel ağ bağdaştırıcısı veya özel bir bağlantı kullanabilir.

Azure Arc makineleri, Azure hizmetlerine ulaşmak ve uyumluluk durumunu raporlamak için şirket içi ağ altyapısını kullanarak bağlanır.

### <a name="communicate-over-virtual-networks-in-azure"></a>Azure 'da sanal ağlar üzerinden iletişim kurma

Sanal ağları iletişim için kullanan sanal makineler, bağlantı noktasındaki Azure veri merkezlerine giden erişim gerektirir `443` . Azure 'da giden trafiğe izin veren özel bir sanal ağ kullanıyorsanız, ağ güvenlik grubu kuralları ile özel durumlar yapılandırın. "GuestAndHybridManagement" hizmet etiketi, Konuk yapılandırma hizmetine başvurmak için kullanılabilir.

### <a name="communicate-over-private-link-in-azure"></a>Azure 'da özel bağlantı üzerinden iletişim kurma

Sanal makineler, Konuk yapılandırma hizmeti ile iletişim kurmak için [özel bağlantıyı](../../../private-link/private-link-overview.md) kullanabilir. `EnablePrivateNeworkGC`Bu özelliği etkinleştirmek için adı (ağda "t" olmadan) ve değeri olan etiketi uygular `TRUE` . Bu etiket, Konuk yapılandırma ilkesi tanımlarının makineye uygulanmadan önce veya sonra uygulanabilir.

Azure platform kaynaklarıyla güvenli ve kimliği doğrulanmış bir kanal oluşturmak için Azure [sanal genel IP adresi](../../../virtual-network/what-is-ip-address-168-63-129-16.md) kullanılarak trafik yönlendirilir.

### <a name="azure-arc-connected-machines"></a>Azure Arc bağlantılı makineler

Azure Arc tarafından bağlanan Azure dışında bulunan düğümlerin Konuk yapılandırma hizmetine bağlantısı olması gerekir. [Azure Arc belgelerinde](../../../azure-arc/servers/overview.md)sunulan ağ ve ara sunucu gereksinimleriyle ilgili ayrıntılar.

Azure 'daki Konuk yapılandırma kaynak sağlayıcısıyla iletişim kurmak için makineler **443** numaralı bağlantı noktasında Azure veri merkezlerine giden erişim gerektirir. Azure 'daki bir ağ giden trafiğe izin vermezse, [ağ güvenlik grubu](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) kuralları ile özel durumlar yapılandırın. "Guestandhybridmanagement" [hizmet etiketi](../../../virtual-network/service-tags-overview.md) , Konuk yapılandırma hizmetine başvurmak için kullanılabilir.

Özel veri merkezlerinde yay bağlantılı sunucular için aşağıdaki desenleri kullanarak trafiğe izin verin:

- Bağlantı noktası: giden internet erişimi için yalnızca TCP 443 gerekir
- Genel URL: `*.guestconfiguration.azure.com`

## <a name="managed-identity-requirements"></a>Yönetilen kimlik gereksinimleri

Girişim _dağıtımı önkoşulları sanal makinelerde Konuk yapılandırma ilkelerini etkinleştirmek için_ ilke tanımları, bir tane yoksa, sistem tarafından atanan bir yönetilen kimliği etkinleştirir. Girişimde kimlik oluşturmayı yöneten iki ilke tanımı vardır. İlke tanımlarındaki koşullar, Azure 'daki makine kaynağının geçerli durumuna bağlı olarak doğru davranışı güvence altına alır.

Makinenin Şu anda herhangi bir yönetilen kimliği yoksa, etkin ilke şu şekilde olur: [kimliği olmayan sanal makinelerde Konuk yapılandırma atamalarını etkinleştirmek için sistem tarafından atanan yönetilen kimlik Ekle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

Makinenin Şu anda Kullanıcı tarafından atanan bir sistem kimliği varsa, etkin ilke şu şekilde olur: [Kullanıcı tarafından atanan bir kimliğe sahip VM 'Lerde Konuk yapılandırma atamalarını etkinleştirmek için sistem tarafından atanan yönetilen kimlik Ekle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="guest-configuration-definition-requirements"></a>Konuk yapılandırma tanımı gereksinimleri

Konuk yapılandırma ilkesi tanımları, **Auditınotexists** efektini kullanır. Tanım atandığında, bir arka uç hizmeti Azure Kaynak sağlayıcısındaki tüm gereksinimlerin yaşam döngüsünü otomatik olarak işler `Microsoft.GuestConfiguration` .

**Auditınotexists** ilke tanımları, makinede tüm gereksinimler karşılanana kadar uyumluluk sonuçları döndürmez. Gereksinimler, [Azure sanal makineler için bölüm dağıtma gereksinimleri](#deploy-requirements-for-azure-virtual-machines) bölümünde açıklanmaktadır.

> [!IMPORTANT]
> Konuk yapılandırmasının önceki bir sürümünde, **Deployifnot Exists** ve **Auditınotexists** tanımlarını birleştirmek için bir girişim gerekiyordu. **Deployifnotexists** tanımları artık gerekli değildir. Tanımlar ve girişimler etiketlidir `[Deprecated]` ancak var olan atamalar çalışmaya devam edecektir. Bilgi için blog gönderisine bakın: [Konuk yapılandırma denetim ilkeleri Için önemli değişiklik yayınlandı](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

### <a name="what-is-a-guest-assignment"></a>Konuk atama nedir?

Bir Azure Ilkesi atandığında, bu kategori "Konuk yapılandırması" ise, bir konuk atamasını açıklamaya eklenen meta veriler vardır.
Konuk atamasını, bir makine ve Azure Ilkesi senaryosu arasında bağlantı olarak düşünebilirsiniz.
Örneğin, aşağıdaki kod parçacığı Azure Windows taban çizgisi yapılandırmasını ilke kapsamındaki makinelere en düşük sürümle ilişkilendirir `1.0.0` . Varsayılan olarak, Konuk ataması yalnızca makinenin bir denetimini yapar.

```json
"metadata": {
    "category": "Guest Configuration",
    "guestConfiguration": {
        "name": "AzureWindowsBaseline",
        "version": "1.*"
    }
//additional metadata properties exist
```

Konuk atamaları, Konuk yapılandırma hizmeti tarafından makine başına otomatik olarak oluşturulur. Kaynak türü `Microsoft.GuestConfiguration/guestConfigurationAssignments` şeklindedir.
Azure Ilkesi, uyumluluk durumunu raporlamak için konuk atama kaynağının **Karmaşıkstatus** özelliğini kullanır. Daha fazla bilgi için bkz. [uyumluluk verilerini alma](../how-to/get-compliance-data.md).

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Sektör temellerini izleyen işletim sistemi ayarlarını denetleme

Azure Ilkesindeki bir girişim, işletim sistemi ayarlarını bir "taban çizgisi" izleyerek denetler. Tanım, _\[ Önizleme \] : Windows makineleri Azure Güvenlik temeli için gereksinimleri karşılamalıdır_ Active Directory Grup İlkesi dayalı bir kurallar kümesi içerir.

Ayarların çoğu parametre olarak kullanılabilir. Parametreler, denetlendiğini özelleştirmenizi sağlar.
İlkeyi gereksinimlerinize göre hizalayın veya ilkeyi sektör mevzuatı standartları gibi üçüncü taraf bilgilerle eşleyin.

Bazı parametreler bir tamsayı değer aralığını destekler. Örneğin, en fazla parola yaşı ayarı geçerli grup ilkesi ayarını denetleyebilir. "1, 70" bir Aralık, kullanıcıların parolalarını en az 70 günde bir, ancak bir günden az değiştirmeme gerektiğini doğrulayacağından emin olur.

İlkeyi bir Azure Resource Manager şablonu (ARM şablonu) kullanarak atarsanız, özel durumları yönetmek için bir parametre dosyası kullanın. Dosyaları git gibi bir sürüm denetim sistemine iade edin. Dosya değişiklikleriyle ilgili açıklamalar, bir atamanın beklenen değer için bir özel durum olduğunu kanıtlamadır.

#### <a name="applying-configurations-using-guest-configuration"></a>Konuk yapılandırması kullanılarak yapılandırmaları uygulama

Yalnızca _Windows makinelerinde saat dilimini yapılandıran_ tanım, saat dilimini yapılandırarak makinede değişiklik yapar. Makinelerin içinde ayarları yapılandırmak için özel ilke tanımları desteklenmez.

_Yapılandırma_ ile başlayan tanımları atarken, _Windows VM 'Lerde Konuk yapılandırma ilkesini etkinleştirmek için tanım dağıtma ön koşullarını_ da atamanız gerekir. Seçeneğini belirlerseniz bu tanımları bir girişim içinde birleştirebilirsiniz.

> [!NOTE]
> Yerleşik saat dilimi ilkesi, makineler içindeki ayarları yapılandırmayı destekleyen tek tanımdır ve makineler içindeki ayarları yapılandıran özel ilke tanımları desteklenmez.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Azure dışındaki makinelere ilke atama

Konuk yapılandırması için kullanılabilen Denetim ilkesi tanımları, **Microsoft. HybridCompute/machines** kaynak türünü içerir. İlke atamasının kapsamındaki [sunucular Için Azure yaya](../../../azure-arc/servers/overview.md) eklendi tüm makineler otomatik olarak eklenir.

## <a name="troubleshooting-guest-configuration"></a>Konuk yapılandırması sorunlarını giderme

Konuk yapılandırma sorunlarını giderme hakkında daha fazla bilgi için bkz. [Azure İlkesi sorunlarını giderme](../troubleshoot/general.md).

### <a name="multiple-assignments"></a>Birden çok atama

Konuk yapılandırma ilkesi tanımları, Ilke ataması farklı parametreler kullanıyor olsa bile, şu anda yalnızca makine başına aynı Konuk atamasını atamayı destekler.

### <a name="client-log-files"></a>İstemci günlük dosyaları

Konuk yapılandırma uzantısı, günlük dosyalarını aşağıdaki konumlara Yazar:

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux

- Azure VM: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`
- Azure VM: `/var/lib/GuestConfig/arc_policy_logs/gc_agent.log`

### <a name="collecting-logs-remotely"></a>Günlükleri uzaktan toplama

Konuk yapılandırma yapılandırmalarının veya modüllerinin sorunlarını gidermenin ilk adımı, `Test-GuestConfigurationPackage` [Windows Için özel konuk yapılandırma denetim ilkesi oluşturma](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)adımlarını izleyerek cmdlet 'i kullanmalıdır.
Bu başarılı olmazsa, istemci günlüklerinin toplanması sorunları tanılamanıza yardımcı olabilir.

#### <a name="windows"></a>Windows

[Azure VM Run komutunu](../../../virtual-machines/windows/run-command.md)kullanarak günlük dosyalarından bilgi yakala, aşağıdaki örnek PowerShell betiği yararlı olabilir.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

[Azure VM Run komutunu](../../../virtual-machines/linux/run-command.md)kullanarak günlük dosyalarından bilgi yakala, aşağıdaki örnek Bash betiği yararlı olabilir.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

### <a name="client-files"></a>İstemci dosyaları

Konuk yapılandırma istemcisi içerik paketlerini bir makineye indirir ve içeriği ayıklar.
Hangi içeriğin indirilip depolandığını doğrulamak için aşağıda verilen klasör konumlarını görüntüleyin.

Windows: `c:\programdata\guestconfig\configuration`

Linux: `/var/lib/GuestConfig/Configuration`

## <a name="guest-configuration-samples"></a>Konuk yapılandırma örnekleri

Konuk yapılandırması yerleşik ilke örnekleri aşağıdaki konumlarda bulunur:

- [Yerleşik ilke tanımları-Konuk yapılandırma](../samples/built-in-policies.md#guest-configuration)
- [Yerleşik girişimler-Konuk yapılandırma](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Ilke örnekleri GitHub deposu](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

### <a name="video-overview"></a>Genel bakış videosu

Azure Ilkesi Konuk yapılandırması 'na aşağıdaki genel bakış, ıüsttalks 2021 ' den biridir.

[Azure Ilke Konuk yapılandırması kullanılarak hibrit sunucu ortamlarında temelleri yöneten](https://techcommunity.microsoft.com/t5/itops-talk-blog/ops114-governing-baselines-in-hybrid-server-environments-using/ba-p/2109245)

## <a name="next-steps"></a>Sonraki adımlar

- [Konuk yapılandırması uyumluluk görünümünden](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) her ayarın ayrıntılarını görüntülemeyi öğrenin
- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [Azure İlkesi tanımı yapısını](./definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](./effects.md) konusunu gözden geçirin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](../how-to/programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](../how-to/get-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.
