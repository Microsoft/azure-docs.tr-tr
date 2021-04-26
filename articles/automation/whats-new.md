---
title: Azure Otomasyonu 'ndaki yenilikler
description: Her ay Azure Otomasyonu için önemli güncelleştirmeler güncelleştirildi.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 04/09/2021
ms.custom: references_regions
ms.openlocfilehash: f8b4d6965a8a1f046fd2459ce9fe5cce8ea45443
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531079"
---
# <a name="whats-new-in-azure-automation"></a>Azure Otomasyonu 'ndaki yenilikler nelerdir?

Azure Otomasyonu, iyileştirmeleri sürekli olarak alır. En son gelişmelerden haberdar olmak için, bu makalede hakkında bilgi verilmektedir:

- En son yayınlar
- Bilinen sorunlar
- Hata düzeltmeleri

Bu sayfa aylık olarak güncelleştirildiğinden düzenli olarak yeniden ziyaret edin.

## <a name="march-2021"></a>Mart 2021

### <a name="new-azure-automation-built-in-policies"></a>Yeni Azure Otomasyonu yerleşik ilkeleri

**Şunu yazın:** Yeni Özellik

Azure Otomasyonu, 5 yeni yerleşik ilke ekledi:

- Otomasyon hesapları, genel ağ erişimini devre dışı bırakmalıdır,
- Azure Otomasyonu hesapları, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır
- Genel ağ erişimini devre dışı bırakmak için Azure Otomasyonu hesaplarını yapılandırma
- Azure Otomasyonu hesaplarında özel uç nokta bağlantılarını yapılandırma
- Otomasyon hesaplarında özel uç nokta bağlantıları etkinleştirilmelidir.

Daha fazla bilgi için bkz. [ilke başvurusu](./policy-reference.md) makalesi.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>Güney Hindistan 'de GA tarafından tanımlanan Otomasyon ve durum yapılandırması desteği

**Şunu yazın:** Yeni Özellik

Güney Hindistan 'de Işlem otomasyonu ve durum yapılandırma yeteneklerini kullanın. Daha fazla bilgi için [duyuruyu](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/) okuyun.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>UK Batı 'de GA tarafından tanımlanan Otomasyon ve durum yapılandırması desteği

**Şunu yazın:** Yeni Özellik

UK Batı 'de Işlem otomasyonu ve durum yapılandırma yeteneklerini kullanın. Daha fazla bilgi için [duyuruyu](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/)okuyun.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>BAE Orta 'de GA tarafından tanımlanan Otomasyon ve durum yapılandırması desteği

**Şunu yazın:** Yeni Özellik

BAE Orta 'de Işlem otomasyonu ve durum yapılandırma yeteneklerini kullanın. Daha fazla bilgi için [duyuruyu](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/) okuyun.

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2--norway-west-and-france-south"></a>Avustralya Orta 2, Norveç Batı ve Fransa Güney 'da sunulan Otomasyon ve durum yapılandırması için destek

**Şunu yazın:** Yeni Özellik

Her bölge için coğrafya seçerek [veri yerleşimi sayfası](https://azure.microsoft.com/global-infrastructure/data-residency/) hakkında daha fazla bilgi görüntüleyin.

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Windows ve Linux 'ta karma çalışanı yüklemek için eklenen yeni betikler

**Şunu yazın:** Yeni Özellik

Azure Otomasyonu [GitHub deposuna](https://github.com/azureautomation) , bir Windows veya Linux makinesinde karma runbook çalışanı ayarlamaya yönelik anahtar senaryolarından birini adresleyen iki yeni komut dosyası eklenmiştir. Betik yeni bir VM oluşturur veya var olan bir VM 'yi kullanır, gerekirse bir Log Analytics çalışma alanı oluşturur, Linux için Windows veya Log Analytics Aracısı için Log Analytics Aracısı 'nı yüklerse ve makineyi Log Analytics çalışma alanına kaydeder. Windows betiği, **Create Automation Windows HybridWorker** olarak adlandırılır ve Linux betiği **Automation Linux hybridworker oluşturur**.

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Azure Resource Manager şablon Web Kancası aracılığıyla runbook 'u çağırma

**Şunu yazın:** Yeni Özellik

Daha fazla ayrıntı için bkz. [ARM şablonundan Web kancası kullanma](./automation-webhooks.md#use-a-webhook-from-an-arm-template) .

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Azure Güncelleştirme Yönetimi artık CentOS 8. x, Red Hat Enterprise Linux Server 8. x ve SUSE Linux Enterprise Server 15 ' i desteklemektedir

**Şunu yazın:** Yeni Özellik

Daha fazla ayrıntı için desteklenen Linux işletim sistemlerinin [tam listesine](./update-management/overview.md#supported-operating-systems) bakın.

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>Brezilya Güney ve Güney Doğu Asya için bölge içi veri yerleşimi desteği 

**Şunu yazın:** Yeni Özellik

Brezilya Güney ve Güneydoğu Asya dışındaki tüm bölgelerde Azure Otomasyonu verileri, Iş sürekliliği ve olağanüstü durum kurtarma (BCDR) sağlamak için farklı bir bölgede (Azure eşleştirilmiş bölge) depolanır. Yalnızca Brezilya ve Güneydoğu Asya bölgeleri için Azure Otomasyonu verilerini, bu bölgelere yönelik veri fazlalığını karşılamak üzere aynı bölgede depolarız. Daha fazla ayrıntı için bkz. [Azure Otomasyonu 'Nda coğrafi çoğaltma](./automation-managing-data.md#geo-replication-in-azure-automation) .

## <a name="february-2021"></a>Şubat 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Japonya Batı 'de GA tarafından tanımlanan Otomasyon ve durum yapılandırması desteği

**Şunu yazın:** Yeni Özellik

Japonya Batı bölgesindeki Otomasyon hesabı ve durum yapılandırması kullanılabilirliği. Daha fazla bilgi için [duyuruyu](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/)okuyun.

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Karma çalışanında runbook yürütmeyi zorlamak için özel Azure Ilke uyumluluğu sunuldu

**Şunu yazın:** Yeni Özellik

Yeni Azure Ilkesi uyumluluk kuralını kullanarak, iş, Web kancaları ve iş zamanlamalarının yalnızca karma çalışan gruplarında çalıştırılmasını sağlayabilirsiniz.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Doğu ABD, Fransa Orta ve Kuzey Avrupa bölgelerinde kullanılabilirlik Güncelleştirme Yönetimi

**Şunu yazın:** Yeni Özellik

Otomasyon Güncelleştirme Yönetimi özelliği Doğu ABD, Fransa Orta ve Kuzey Avrupa bölgelerinde kullanılabilir. Bu değişikliği yansıtan belgelerde güncelleştirmeler için bkz. [desteklenen bölge eşlemesi](how-to/region-mappings.md) .

## <a name="january-2021"></a>Ocak 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>İsviçre Batı 'de GA tarafından tanımlanan Otomasyon ve durum yapılandırması desteği

**Şunu yazın:** Yeni Özellik

İsviçre Batı bölgesindeki Otomasyon hesabı ve durum yapılandırması kullanılabilirliği. Daha fazla bilgi için [duyuruyu](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/)okuyun.

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Birden çok bağımlılığı olan modülün içeri aktarılması için Python 3 betiği eklendi

**Şunu yazın:** Yeni Özellik

Betik, [GitHub deponuzdan](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py)indirilebilir. 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>CentOS 8. x/RHEL 8. x/SLES 15 için karma Runbook Worker rolü desteği

**Türüyle.** Yeni Özellik

Karma Runbook Worker özelliği, karma runbook çalışanları üzerinde yalnızca işlem otomasyonu için CentOS 8. x, REHL 8. x ve SLES 15 dağıtımlarını destekler. Bu değişiklikleri yansıtmak için belgelerdeki güncelleştirmeler için [desteklenen işletim sistemleri](automation-linux-hrw-install.md#supported-linux-operating-systems) bölümüne bakın.

### <a name="update-management-and-change-tracking-availability-in-australia-east-east-asia-west-us-and-central-us-regions"></a>Avustralya Doğu, Doğu Asya, Batı ABD ve Orta ABD bölgelerinde Güncelleştirme Yönetimi ve Değişiklik İzleme kullanılabilirliği

**Şunu yazın:** Yeni Özellik

Otomasyon hesabı, Değişiklik İzleme ve stok ve Güncelleştirme Yönetimi Avustralya Doğu, Doğu Asya, Batı ABD ve Orta ABD bölgelerinde kullanılabilir. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>ABD kamu bulutunda Python 3 runbook 'larının genel önizlemesi kullanıma sunuldu

**Şunu yazın:** Yeni özellik Azure Otomasyonu, ABD kamu bulut bölgelerinde Python 3 bulutu ve hibrit runbook yürütmesi için genel önizleme desteği sunuyor. Daha fazla bilgi için [duyurusuna](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/)bakın.

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Azure Otomasyonu runbook 'ları TechNet Betik merkezinden GitHub 'a taşındı

**Şunu yazın:** Değişiklik planı

TechNet Betik Merkezi devre dışı bırakıldı ve Runbook galerisinde barındırılan tüm runbook 'lar [Otomasyon GitHub kuruluşumuza](https://github.com/azureautomation)taşındı. Daha fazla bilgi için [GitHub 'a taşınan Azure Otomasyonu runbook 'larını](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)okuyun.

## <a name="december-2020"></a>Aralık 2020

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Otomasyonu ve Güncelleştirme Yönetimi özel bağlantısı GA

**Şunu yazın:** Yeni Özellik

Azure Otomasyonu ve Güncelleştirme Yönetimi desteği, Azure küresel ve kamu bulutları için GA olarak duyurulmuştur. Azure Otomasyonu etkin özel bağlantı, bir runbook 'un bir karma çalışan rolünde yürütülmesini güvenli hale getirmek için Güncelleştirme Yönetimi, bir Web Kancası aracılığıyla runbook 'u çağırmak ve makinelerinizi şikayet etmek için durum yapılandırma hizmeti 'ni kullanmak için destekler. Daha fazla bilgi için [Azure Otomasyonu özel bağlantı desteğini](https://azure.microsoft.com/updates/azure-automation-private-link) okuyun

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Erişilebilirlik 'de C sınıfı sertifikalı olarak sınıflandırılan Azure Otomasyonu

**Şunu yazın:** Yeni Özellik

Microsoft ürünlerinin erişilebilirlik özellikleri, küresel erişilebilirlik gereksinimlerini ele almanıza yardımcı olur. [Blog duyurusu](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) sayfasında, Otomasyon Hizmeti için erişilebilirlik uyumluluk raporunu okumak üzere **Azure Otomasyonu** ' nu arayın.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>BAE Kuzey 'de Otomasyon ve durum yapılandırması GA desteği

**Şunu yazın:** Yeni Özellik

BAE Kuzey bölgesindeki Otomasyon hesabı ve durum yapılandırması kullanılabilirliği. Daha fazla bilgi için [duyuruyu](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/)okuyun.

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Almanya Orta Batı 'de Otomasyon ve durum yapılandırması GA desteği

**Şunu yazın:** Yeni Özellik

Almanya Batı bölgesindeki Otomasyon hesabı ve durum yapılandırması kullanılabilirliği. Daha fazla bilgi için [duyuruyu](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/)okuyun.

### <a name="dsc-support-for-oracle-6-and-7"></a>Oracle 6 ve 7 için DSC desteği

**Şunu yazın:** Yeni Özellik

Oracle Linux 6 ve 7 makineyi Automation durum yapılandırması ile yönetin. Bu değişiklikleri yansıtmak için belgelerdeki güncelleştirmeler için [desteklenen Linux destekleri](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) bölümüne bakın.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Otomasyon 'da Python3 runbook 'lar için genel önizleme

**Şunu yazın:** Yeni Özellik

Azure Otomasyonu artık Azure genel bulutundaki tüm bölgelerde genel önizlemede Python 3 bulutunu ve karma runbook yürütmesini desteklemektedir. Bkz. [Duyuru] ( https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) daha fazla ayrıntı için).

## <a name="november-2020"></a>Kasım 2020

### <a name="dsc-support-for-ubuntu-1804"></a>Ubuntu 18,04 için DSC desteği

**Şunu yazın:** Yeni Özellik

Bu değişiklikleri yansıtan belgelerde güncelleştirmeler için [desteklenen Linux destekleri](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) bölümüne bakın.

## <a name="october-2020"></a>Ekim 2020

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>İsviçre Kuzey 'de Otomasyon ve durum yapılandırması GA desteği

**Şunu yazın:** Yeni Özellik

İsviçre Kuzey 'de Otomasyon hesabı ve durum yapılandırması kullanılabilirliği. Daha fazla bilgi için [duyuruyu](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/)okuyun.

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Brezilya Güney Doğu 'da Otomasyon ve durum yapılandırması GA desteği

**Şunu yazın:** Yeni Özellik

Brezilya Güney Doğu ' de Otomasyon hesabı ve durum yapılandırması kullanılabilirliği. Daha fazla bilgi için [duyuruyu](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/)okuyun.

### <a name="update-management-availability-in-south-central-us"></a>Orta Güney ABD Güncelleştirme Yönetimi kullanılabilirliği

**Şunu yazın:** Yeni Özellik

Azure Otomasyonu bölgesi eşleme, Orta Güney ABD bölgesindeki Güncelleştirme Yönetimi özelliğini destekleyecek şekilde güncelleştirildi. Bu değişikliği yansıtmak için belgelerdeki güncelleştirmeler için bkz. [desteklenen bölge eşlemesi](how-to/region-mappings.md#supported-mappings) .

## <a name="september-2020"></a>Eylül 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>VM'leri çalışma saatleri dışında başlat/durdur runbook 'ları Azure az modules kullanacak şekilde güncelleştirildi

**Şunu yazın:** Yeni Özellik

VM runbook 'larını Başlat/Durdur, Azure Resource Manager modülleri yerine az modüller kullanacak şekilde güncelleştirilmiştir. Bu değişiklikleri yansıtmak için belgelerde yapılan güncelleştirmelerin [VM'leri çalışma saatleri dışında Başlat/Durdur](automation-solution-vm-management.md) genel bakış bölümüne bakın.

## <a name="august-2020"></a>Ağustos 2020

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Azure yayı 'yi desteklemek için DSC Uzantısı yayımlandı

**Şunu yazın:** Yeni Özellik

Azure Otomasyonu durum yapılandırması 'nı kullanarak yapılandırmaları merkezi olarak depolar ve Azure Arc etkin sunucular DSC VM Uzantısı aracılığıyla etkinleştirilen karma bağlantılı makinelerin istenen durumunu koruyun. Daha fazla bilgi için, bkz. [yay etkin SUNUCULARı VM uzantılarına genel bakış](../azure-arc/servers/manage-vm-extensions.md).

### <a name="july-2020"></a>Temmuz 2020

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Otomasyon 'da özel bağlantı desteğinin genel önizlemesi kullanıma sunuldu

**Şunu yazın:** Yeni Özellik

Özel uç noktaları kullanarak sanal ağları güvenli bir şekilde Azure Otomasyonu 'na bağlamak için Azure özel bağlantısı 'nı kullanın. Daha fazla bilgi için [duyuruyu](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/)okuyun.

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Windows Server 2008 R2 için karma Runbook Worker desteği

**Şunu yazın:** Yeni Özellik

Otomasyon karma Runbook Worker, Windows Server 2008 R2 işletim sistemini destekler. Bu değişiklikleri yansıtmak için belgelerdeki güncelleştirmeler için [desteklenen işletim sistemleri](automation-windows-hrw-install.md#supported-windows-operating-system) bölümüne bakın.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Windows Server 2008 R2 için Güncelleştirme Yönetimi desteği

**Şunu yazın:** Yeni Özellik

Güncelleştirme Yönetimi, Windows Server 2008 R2 işletim sistemi için değerlendirme ve düzeltme eki uygulamayı destekler. Bu değişiklikleri yansıtmak için belgelerdeki güncelleştirmeler için [desteklenen işletim sistemleri](update-management/overview.md#clients) bölümüne bakın.

### <a name="automation-diagnostic-logs-schema-update"></a>Otomasyon tanılama günlükleri şema güncelleştirmesi

**Şunu yazın:** Yeni Özellik

Log Analytics hizmetindeki Azure Otomasyonu günlük verilerinin şeması değiştirildi. Daha fazla bilgi edinmek için bkz. [Azure Otomasyonu iş verilerini Azure izleyici günlüklerine iletme](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object).

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Athouse Otomasyon Güncelleştirme Yönetimi destekler

**Şunu yazın:** Yeni Özellik

Azure Aydınlathouse, hizmet sağlayıcıları ve müşteriler için Güncelleştirme Yönetimi ile yetkilendirilmiş kaynak yönetimine izin vermez. Daha fazla bilgi için [buraya](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/) bakabilirsiniz.

## <a name="june-2020"></a>Haziran 2020

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>US Gov Arizona bölgesinde Otomasyon ve Güncelleştirme Yönetimi kullanılabilirliği

**Şunu yazın:** Yeni Özellik

Otomasyon hesabı ve Güncelleştirme Yönetimi US Gov Arizona kullanılabilir. Daha fazla bilgi için bkz. [duyuru](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/).

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Karma Runbook Worker ekleme betiği az modüller kullanacak şekilde güncelleştirildi

**Şunu yazın:** Yeni Özellik

New-OnPremiseHybridWorker runbook az modülleri destekleyecek şekilde güncelleştirilmiştir. Daha fazla bilgi için [PowerShell Galerisi](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7)pakete bakın.

### <a name="update-management-availability-in-china-east-2"></a>Çin Doğu 2 ' de Güncelleştirme Yönetimi kullanılabilirliği

**Şunu yazın:** Yeni Özellik

Azure Otomasyonu bölge eşlemesi, Çin Doğu 2 bölgesindeki Güncelleştirme Yönetimi özelliği destekleyecek şekilde güncelleştirildi. Bu değişikliği yansıtmak için belgelerdeki güncelleştirmeler için bkz. [desteklenen bölge eşlemesi](how-to/region-mappings.md#supported-mappings) .

## <a name="may-2020"></a>Mayıs 2020

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Belirli bir bölgedeki Otomasyon Hizmeti DNS kayıtları, Otomasyon hesabına özgü URL 'lere güncelleştirildi

**Şunu yazın:** Yeni Özellik

Azure Otomasyonu DNS kayıtları, özel bağlantıları destekleyecek şekilde güncelleştirilmiştir. Daha fazla bilgi için [duyuruyu](https://azure.microsoft.com/updates/azure-automation-updateddns-records/)okuyun.

### <a name="added-capability-to-keep-automation-runbooks-and-dsc-scripts-encrypted-by-default"></a>Automation runbook 'ların ve DSC betikleri varsayılan olarak şifrelenmiş olarak kalsın özelliği eklendi

**Şunu yazın:** Yeni Özellik

Varlıkların güvenliğini artırmanın yanı sıra, runbook 'lar ve DSC betikleri de Azure Otomasyonu güvenliğini geliştirmek için şifrelenir.

## <a name="april-2020"></a>Nisan 2020

### <a name="retirement-of-the-automation-watcher-task"></a>Otomasyon İzleyicisi görevinin kullanımdan kaldırılması

**Şunu yazın:** Değişiklik planı

Azure Logic Apps artık olayları izlemek, yinelenen görevleri zamanlamak ve eylemleri tetiklemek için önerilen ve desteklenen bir yoldur. Izleyici görevi işlevselliğinde daha fazla yatırım olmayacaktır. Daha fazla bilgi için bkz. [yinelenen otomatikleştirilmiş görevleri zamanlama ve çalıştırma Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

## <a name="march-2020"></a>Mart 2020

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Azure ticari ve kamu bulutu 'nda etki düzeyi 5 (IL5) işlem yalıtımına yönelik destek

**Türüyle**

Azure Otomasyonu karma Runbook Worker, Azure Kamu 'da, etki düzeyi 5 iş yüklerini desteklemek için kullanılabilir. Daha fazla bilgi edinmek için [belgelerimize](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5)bakın.

## <a name="february-2020"></a>Şubat 2020

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Azure sanal ağ hizmeti etiketleri için sunulan destek

**Şunu yazın:** Yeni Özellik

Hizmet etiketlerinin Otomasyon desteği, bir senaryo alt kümesi için Otomasyon Hizmeti trafiğine izin verir veya reddeder. Daha fazla bilgi edinmek için [belgelerine](automation-hybrid-runbook-worker.md#service-tags)bakın.

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Azure Otomasyonu hizmeti için TLS 1,2 desteğini etkinleştir

**Şunu yazın:** Değişiklik planı

Azure Otomasyonu, TLS 1,2 ve tüm istemci çağrılarını (Web kancaları, DSC düğümleri ve karma çalışan aracılığıyla) tam olarak destekler. TLS 1,1 ve TLS 1,0, müşteriler standartlaştırır ve TLS 1,2 'e tam olarak geçiş yapılıncaya kadar eski istemcilerle geriye dönük uyumluluk için de desteklenmektedir.

## <a name="january-2020"></a>Ocak 2020

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Azure Otomasyonu için müşteri tarafından yönetilen anahtarların genel önizlemesi kullanıma sunuldu

**Şunu yazın:** Yeni Özellik

Müşteriler, kendi yönetilen anahtarlarını kullanarak Azure Otomasyonu varlıklarının şifrelemesini yönetebilir ve güvenli hale getirebilirsiniz. Daha fazla bilgi için bkz. [müşteri tarafından yönetilen anahtarların kullanımı](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Azure Otomasyonu için Azure hizmet yönetimi (ASM) REST API 'Lerinin kullanımdan kaldırılması

**Şunu yazın:** Bırakmak

Azure Otomasyonu için Azure hizmet yönetimi (ASM) REST API 'Leri kullanımdan kaldırılır ve 30 Ocak 2020 sonrasında desteklenmez. Daha fazla bilgi edinmek için [duyurusuna](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/)bakın.

## <a name="next-steps"></a>Sonraki adımlar

Azure Otomasyonu belgelerine katkıda bulunmak istiyorsanız [docs katılımcısı Kılavuzu](/contribute/)' na bakın.
