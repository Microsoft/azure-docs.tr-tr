---
title: Azure Otomasyonu veri güvenliği
description: Bu makale, Azure Otomasyonu 'nun gizliliğinizi nasıl koruduğunu ve verilerinizin güvenliğini nasıl sağladığını öğrenmenize yardımcı olur.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cb18cca782b85e608c3c7ddb001ecb03b86055f6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833535"
---
# <a name="management-of-azure-automation-data"></a>Azure Otomasyonu verilerinin yönetilmesi

Bu makalede, Azure Otomasyonu ortamında verilerin nasıl korunduğu ve güvenliğinin sağlandığı hakkında çeşitli konular yer almaktadır.

## <a name="tls-12-enforcement-for-azure-automation"></a>Azure Otomasyonu için TLS 1,2 zorlaması

Azure Otomasyonu 'na geçişte verilerin güvenliğini sağlamak için, Aktarım Katmanı Güvenliği (TLS) 1,2 kullanımını yapılandırmanızı kesinlikle öneririz. Aşağıda, HTTPS üzerinden Automation hizmetine iletişim kuran yöntemlerin veya istemcilerin listesi verilmiştir:

* Web kancası çağrıları

* Güncelleştirme Yönetimi ve Değişiklik İzleme ve envanter tarafından yönetilen makineleri içeren karma runbook çalışanları.

* DSC düğümleri

TLS/Güvenli Yuva Katmanı (SSL) uygulamasının güvenlik açığı olduğu ve geriye dönük uyumlulukla hala çalışmaya devam eden daha eski sürümlerinin **kullanılması önerilmez**. Gerekli olmadığı sürece aracınızı yalnızca TLS 1,2 kullanacak şekilde ayarlamayı önermiyoruz, çünkü TLS 1,3 gibi daha yeni bir daha güvenli protokolde otomatik olarak algılamanıza ve bu protokollerin avantajlarından yararlanmanızı sağlar.

Karma Runbook Worker rolüne yönelik bir bağımlılık olan Windows ve Linux için Log Analytics aracısıyla TLS 1,2 desteği hakkında bilgi için bkz. [Log Analytics Agent genel bakış-TLS 1,2](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol).

### <a name="platform-specific-guidance"></a>Platforma özgü kılavuz

|Platform/dil | Destek | Daha Fazla Bilgi |
| --- | --- | --- |
|Linux | Linux dağıtımları, TLS 1,2 desteği için [OpenSSL](https://www.openssl.org) 'yi kullanır.  | OpenSSL sürümünüzü doğrulamak için [OpenSSL changelog](https://www.openssl.org/news/changelog.html) ' yı denetleyin.|
| Windows 8,0-10 | Desteklenir ve varsayılan olarak etkindir. | Hala [varsayılan ayarları](/windows-server/security/tls/tls-registry-settings)kullandığınızdan emin olun.  |
| Windows Server 2012-2016 | Desteklenir ve varsayılan olarak etkindir. | [Varsayılan ayarları](/windows-server/security/tls/tls-registry-settings) hala kullandığınızı doğrulamak için |
| Windows 7 SP1 ve Windows Server 2008 R2 SP1 | Desteklenir, ancak varsayılan olarak etkinleştirilmez. | ' Nin nasıl etkinleştirileceği hakkında ayrıntılı bilgi için bkz. [Aktarım Katmanı Güvenliği (TLS) kayıt defteri ayarları](/windows-server/security/tls/tls-registry-settings) sayfası.  |

## <a name="data-retention"></a>Veri saklama

Azure Otomasyonu 'nda bir kaynağı sildiğinizde, kalıcı kaldırma işleminden önce denetim amacıyla birçok gün boyunca tutulur. Bu süre içinde kaynağı göremez veya kullanamazsınız. Bu ilke, silinen bir Otomasyon hesabına ait olan kaynaklar için de geçerlidir. Bekletme ilkesi tüm kullanıcılar için geçerlidir ve şu anda özelleştirilemiyor. Ancak, verileri daha uzun bir süre tutmanız gerekiyorsa [Azure Otomasyonu iş verilerini Azure izleyici günlüklerine iletebilirsiniz](automation-manage-send-joblogs-log-analytics.md).

Aşağıdaki tabloda, farklı kaynaklar için bekletme ilkesi özetlenmektedir.

| Veriler | İlke |
|:--- |:--- |
| Hesaplar |Bir hesap, bir kullanıcı tarafından silindikten sonra 30 gün sonra kalıcı olarak kaldırılır. |
| Varlıklar |Bir varlık, bir Kullanıcı onu sildikten 30 gün sonra veya bir kullanıcı varlığı tutan bir hesabı sildikten sonra 30 gün sonra kalıcı olarak kaldırılır. Varlıklar arasında değişkenler, zamanlamalar, kimlik bilgileri, sertifikalar, Python 2 paketleri ve bağlantılar bulunur. |
| DSC düğümleri |DSC düğümü, Windows PowerShell 'de Azure portal veya [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) cmdlet 'ini kullanarak bir Otomasyon hesabından silinen 30 gün sonra kalıcı olarak kaldırılır. Düğüm, düğüm tutan hesabı sildikten sonra 30 gün sonra da kalıcı olarak kaldırılır. |
| İşler |Bir iş silinir ve değişiklik sonrasında 30 gün sonra kalıcı olarak kaldırılır. Örneğin, iş tamamlandıktan sonra, durdurulur veya askıya alınır. |
| Modül |Bir modül, bir Kullanıcı onu sildikten 30 gün sonra veya bir kullanıcı modülü tutan hesabı sildikten sonra 30 gün sonra kalıcı olarak kaldırılır. |
| Düğüm konfigürasyonları/MOF dosyaları |Yeni bir düğüm yapılandırması oluşturulduktan sonra, eski bir düğüm yapılandırması 30 gün sonra kalıcı olarak kaldırılır. |
| Düğüm raporları |Düğüm raporu, bu düğüm için yeni rapor oluşturulduktan 90 gün sonra kalıcı olarak kaldırılır. |
| Runbook'lar |Bir runbook, bir Kullanıcı kaynağı sildikten 30 gün sonra veya bir Kullanıcı kaynak<sup>1</sup>' i tutan hesabı sildikten sonra 30 gün sonra kalıcı olarak kaldırılır. |

<sup>1</sup> Runbook, Microsoft Azure desteğiyle bir Azure destek olayı kaydederek 30 günlük bir pencere içinde kurtarılabilir. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **destek isteği gönder**' i seçin.

## <a name="data-backup"></a>Veri yedekleme

Azure 'da bir Otomasyon hesabını sildiğinizde, hesaptaki tüm nesneler silinir. Nesneler runbook 'ları, modülleri, konfigürasyonları, ayarları, işleri ve varlıkları içerir. Hesap silindikten sonra kurtarılamaz. Otomasyon hesabınızın içeriğini silmeden önce yedeklemek için aşağıdaki bilgileri kullanabilirsiniz.

### <a name="runbooks"></a>Runbook'lar

Windows PowerShell 'de Azure portal veya [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) cmdlet 'ini kullanarak runbook 'larınızı betik dosyalarına aktarabilirsiniz. Bu komut dosyalarını, [Azure Otomasyonu 'nda runbook 'Ları yönetme](manage-runbooks.md)bölümünde anlatıldığı gibi başka bir Otomasyon hesabına içeri aktarabilirsiniz.

### <a name="integration-modules"></a>Tümleştirme modülleri

Tümleştirme modüllerini Azure Otomasyonu 'ndan dışarı aktaralamazsınız, bu, Otomasyon hesabı dışında kullanılabilir hale getirilmeleri gerekir.

### <a name="assets"></a>Varlıklar

Azure Otomasyonu varlıklarını dışarı veremezsiniz: sertifikalar, bağlantılar, kimlik bilgileri, zamanlamalar ve değişkenler. Bunun yerine, Azure portal ve Azure cmdlet 'lerini kullanarak bu varlıkların ayrıntılarını görebilirsiniz. Daha sonra, başka bir Otomasyon hesabına aktardığınız runbook 'lar tarafından kullanılan varlıkları oluşturmak için bu ayrıntıları kullanın.

Cmdlet 'leri kullanarak, şifrelenmiş değişkenlerin veya kimlik bilgilerinin parola alanlarının değerlerini alamazsınız. Bu değerleri bilmiyorsanız bir runbook 'ta alabilirsiniz. Değişken değerlerini almak için bkz. [Azure Otomasyonu 'Nda değişken varlıkları](shared-resources/variables.md). Kimlik bilgisi değerlerini alma hakkında daha fazla bilgi edinmek için bkz. [Azure Automation 'Da kimlik bilgisi varlıkları](shared-resources/credentials.md).

### <a name="dsc-configurations"></a>DSC yapılandırması

Windows PowerShell 'de Azure portal veya [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) cmdlet 'INI kullanarak DSC yapılandırmalarını betik dosyalarına aktarabilirsiniz. Bu konfigürasyonları başka bir Otomasyon hesabında içeri aktarabilir ve kullanabilirsiniz.

## <a name="geo-replication-in-azure-automation"></a>Azure Otomasyonu 'nda coğrafi çoğaltma

Coğrafi çoğaltma, Azure Otomasyonu hesaplarında standarttır. Hesabınızı ayarlarken bir birincil bölge seçersiniz. İç otomasyon coğrafi çoğaltma hizmeti, hesaba otomatik olarak bir ikincil bölge atar. Hizmet daha sonra birincil bölgedeki hesap verilerini ikincil bölgeye sürekli olarak yedekler. Birincil ve ikincil bölgelerin tam listesi [iş sürekliliği ve olağanüstü durum kurtarma (BCDR): Azure eşlenmiş bölgeler '](../best-practices-availability-paired-regions.md)de bulunabilir.

Automation coğrafi çoğaltma hizmeti tarafından oluşturulan yedekleme, Otomasyon varlıklarının, yapılandırmaların ve benzeri tam bir kopyasıdır. Bu yedekleme, birincil bölge aşağı gittiğinde ve verileri kaybederse kullanılabilir. Birincil bir bölgenin verilerinin kaybedilmesi olası olmayan olayda, Microsoft bunu kurtarmaya çalışır.

> [!NOTE]
> Azure Otomasyonu müşteri verilerini müşteri tarafından seçilen bölgede depolar. BCDR için, Brezilya Güney ve Güneydoğu Asya dışındaki tüm bölgelerde Azure Otomasyonu verileri farklı bir bölgede (Azure eşleştirilmiş bölge) depolanır. Asya Pasifik Coğrafya 'un yalnızca Brezilya Coğrafya ve Güneydoğu Asya Bölgesi (Singapur) Brezilya Güney (Sao Paulo Eyaleti) bölgesi için, Azure Otomasyonu verilerini bu bölgelerin veri fazlalığını karşılamak üzere aynı bölgede depolarız.

Bir bölgesel hata varsa, Otomasyon coğrafi çoğaltma hizmetine doğrudan dış müşteriler erişemez. Bölgesel hatalarda Otomasyon yapılandırmasını ve Runbook 'larını sürdürmek istiyorsanız:

1. Birincil Otomasyon hesabınızın coğrafi bölgesiyle eşleştirmek için bir ikincil bölge seçin.

2. İkincil bölgede bir Otomasyon hesabı oluşturun.

3. Birincil hesapta, runbook 'larınızı betik dosyası olarak dışarı aktarın.

4. Runbook 'ları ikincil bölgedeki Otomasyon hesabınıza aktarın.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Automation 'da güvenli varlıklar hakkında daha fazla bilgi edinmek için bkz. [Azure Automation 'da güvenli varlıkların şifrelenmesi](automation-secure-asset-encryption.md).

* Coğrafi çoğaltma hakkında daha fazla bilgi edinmek için bkz. [etkin coğrafi çoğaltma oluşturma ve kullanma](../azure-sql/database/active-geo-replication-overview.md).
