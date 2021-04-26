---
title: Azure geçiş 'de Hyper-V değerlendirmesi desteği
description: Azure geçişi bulma ve değerlendirmesi ile Hyper-V değerlendirmesi desteği hakkında bilgi edinin
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 8531d0b2252e6ddff75509046b5a4576b99d339f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870798"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-V değerlendirmesi için destek matrisi

Bu makalede, Azure geçişi [: bulma ve değerlendirme](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) Aracı kullanılarak Azure 'a geçiş Için bir Hyper-V ortamında çalışan şirket içi sunucuları bulup değerlendirdikten sonra Önkoşullar ve destek gereksinimleri özetlenmektedir. Hyper-V ' d e çalıştıran sunucuları Azure 'a geçirmek istiyorsanız, [geçiş desteği matrisini](migrate-support-matrix-hyper-v-migration.md)gözden geçirin.

Hyper-V ' d e çalışan sunucuların bulmayı ve değerlendirmesini ayarlamak için bir proje oluşturun ve projeye Azure geçişi: bulma ve değerlendirme aracını ekleyin. Araç eklendikten sonra [Azure geçişi](migrate-appliance.md)gereci dağıtırsınız. Gereç, şirket içi sunucuları sürekli bulur ve Azure 'a sunucu meta verilerini ve performans verilerini gönderir. Bulma işlemi tamamlandıktan sonra keşfedilen sunucuları gruplar halinde toplar ve bir grup için değerlendirme çalıştırırsınız.

## <a name="limitations"></a>Sınırlamalar

**Destek** | **Ayrıntılar**
--- | ---
**Değerlendirme limitleri** | Tek bir [projede](migrate-support-matrix.md#project)en fazla 35.000 sunucu bulabilir ve değerlendirebilirsiniz.
**Proje limitleri** | Bir Azure aboneliğinde birden çok proje oluşturabilirsiniz. Hyper-V üzerindeki sunuculara ek olarak bir proje, VMware ve fiziksel sunuculardaki sunucuları, her biri için değerlendirme sınırlarına kadar içerebilir.
**Bulma** | Azure geçişi gereci, Hyper-V üzerinde çalışan en fazla 5000 sunucuyu bulabilir.<br/><br/> Gereç, 300 adede kadar Hyper-V konaklarına bağlanabilir.
**Değerlendirme** | Tek bir gruba en fazla 35.000 sunucu ekleyebilirsiniz.<br/><br/> Bir grup için tek bir değerlendirmede en fazla 35.000 sunucu değerlendirebilirsiniz.

Değerlendirmeler hakkında [daha fazla bilgi edinin](concepts-assessment-calculation.md) .

## <a name="hyper-v-host-requirements"></a>Hyper-V konağı gereksinimleri

| **Destek**                | **Ayrıntılar**
| :-------------------       | :------------------- |
| **Hyper-V konağı**       | Hyper-V konağı tek başına olabilir veya bir kümede dağıtılabilir.<br/><br/> Hyper-V konağı Windows Server 2019, Windows Server 2016 veya Windows Server 2012 R2 çalıştırabilir. Bu işletim sistemlerinin Sunucu Çekirdeği yüklemeleri de desteklenir. <br/>Windows Server 2012 çalıştıran Hyper-V konaklarında bulunan sunucuları değerlendiremiyoruz.
| **İzinler**           | Hyper-V konağında yönetici izinlerine sahip olmanız gerekir. <br/> Yönetici izinleri atamak istemiyorsanız, bir yerel veya etki alanı kullanıcı hesabı oluşturun ve bu gruplara kullanıcı hesabını ekleyin-uzak yönetim kullanıcıları, Hyper-V yöneticileri ve performans Izleyicisi kullanıcıları. |
| **PowerShell uzaktan iletişim**   | Her Hyper-V konağında [PowerShell uzaktan iletişim](/powershell/module/microsoft.powershell.core/enable-psremoting) özelliğinin etkinleştirilmesi gerekir. |
| **Hyper-V Çoğaltma**       | Hyper-V çoğaltması kullanıyorsanız (veya aynı sunucu tanımlayıcılarına sahip birden fazla sunucunuz varsa) ve Azure geçişi 'ni kullanarak hem özgün hem de çoğaltılan sunucuları keşfetiyorsanız, Azure geçişi tarafından oluşturulan değerlendirme doğru olmayabilir. |

## <a name="server-requirements"></a>Sunucu gereksinimleri

| **Destek**                  | **Ayrıntılar**
| :----------------------------- | :------------------- |
| **İşletim sistemi** | Geçiş için tüm işletim sistemleri değerlendirilenebilir.  |
| **Tümleştirme Hizmetleri**       | [Hyper-V tümleştirme hizmetlerinin](/virtualization/hyper-v-on-windows/reference/integration-services) , işletim sistemi bilgilerini yakalamak için değerlendirmekte olduğunuz sunucularda çalışıyor olması gerekir. |
| **Depolama** | Yerel disk, DAS, JBOD, depolama alanları, CSV, SMB. VHD/VHDX 'in depolandığı bu Hyper-V ana bilgisayar depolama alanları desteklenir. <br/> IDE ve SCSI sanal denetleyicileri destekleniyor|

## <a name="azure-migrate-appliance-requirements"></a>Azure Geçişi aleti gereksinimleri

Azure geçişi, bulma ve değerlendirme için [Azure geçişi](migrate-appliance.md) gereci kullanır. Gereci, portaldan indirtiğiniz sıkıştırılmış bir Hyper-V VHD veya bir [PowerShell betiği](deploy-appliance-script.md)kullanarak dağıtabilirsiniz.

- Hyper-V için [gereç gereksinimleri](migrate-appliance.md#appliance---hyper-v) hakkında bilgi edinin.
- Gereçlerin [ortak](migrate-appliance.md#public-cloud-urls) ve [kamu](migrate-appliance.md#government-cloud-urls) bulutlarında erişmesi gereken URL 'ler hakkında bilgi edinin.
- Azure Kamu 'da, [betiği kullanarak](deploy-appliance-script-government.md)gereci dağıtmanız gerekir.

## <a name="port-access"></a>Bağlantı noktası erişimi

Aşağıdaki tabloda, değerlendirme için bağlantı noktası gereksinimleri özetlenmektedir.

**Cihaz** | **Bağlantı**
--- | ---
**Elektrikli** | TCP bağlantı noktası 3389 üzerindeki gelen bağlantılar, gereci Uzak Masaüstü bağlantılarına izin vermek için.<br/><br/> 44368 numaralı bağlantı noktası üzerinden gereç yönetimi uygulamasına uzaktan erişim için gelen bağlantılar: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Azure geçişi 'ne bulma ve performans meta verileri göndermek için 443 (HTTPS) bağlantı noktalarında giden bağlantılar.
**Hyper-V konağı/kümesi** | Genel Bilgi Modeli (CıM) oturumu kullanarak Hyper-V ' d e sunucular için meta verileri ve performans verilerini çekmek üzere WinRM bağlantı noktası 5985 (HTTP) veya 5986 (HTTPS) üzerinde gelen bağlantı.

## <a name="agent-based-dependency-analysis-requirements"></a>Aracı tabanlı bağımlılık Analizi gereksinimleri

[Bağımlılık Analizi](concepts-dependency-visualization.md) , değerlendirmek ve Azure 'a geçirmek istediğiniz şirket içi sunucular arasındaki bağımlılıkları belirlemenize yardımcı olur. Tablo, aracı tabanlı bağımlılık analizini ayarlamaya yönelik gereksinimleri özetler. Hyper-V Şu anda yalnızca aracı tabanlı bağımlılık görselleştirmesini destekler.

**Gereksinim** | **Ayrıntılar**
--- | --- 
**Dağıtımdan önce** | Projeye Azure geçişi: bulma ve değerlendirme aracı eklenmiş bir projeniz olması gerekir.<br/><br/>  Şirket içi sunucularınızı bulmaya yönelik bir Azure geçiş gereci ayarladıktan sonra bağımlılık görselleştirmesini dağıtırsınız.<br/><br/> İlk kez bir proje oluşturmayı [öğrenin](create-manage-projects.md) .<br/> Mevcut bir projeye Azure geçişi: bulma ve değerlendirme aracı ekleme [hakkında bilgi edinin](how-to-assess.md) .<br/> [Hyper-V ' d e sunucu](how-to-set-up-appliance-hyper-v.md)bulma ve değerlendirme için gereç ayarlamayı öğrenin.
**Azure Devlet Kurumları** | Bağımlılık görselleştirmesi Azure Kamu 'da kullanılamaz.
**Log Analytics** | Azure geçişi, bağımlılık görselleştirmesi için [Azure izleyici günlüklerinde](../azure-monitor/logs/log-query-overview.md) [hizmet eşlemesi](../azure-monitor/vm/service-map.md) çözümünü kullanır.<br/><br/> Yeni veya mevcut bir Log Analytics çalışma alanını projeyle ilişkilendirirsiniz. Bir projenin çalışma alanı eklendikten sonra değiştirilemez. <br/><br/> Çalışma alanı, projeyle aynı abonelikte olmalıdır.<br/><br/> Çalışma alanı Doğu ABD, Güneydoğu Asya veya Batı Avrupa bölgelerinde bulunmalıdır. Diğer bölgelerdeki çalışma alanları bir projeyle ilişkilendirilemez.<br/><br/> Çalışma alanının [hizmet eşlemesi desteklendiği](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)bir bölgede olması gerekir.<br/><br/> Log Analytics, Azure geçişi ile ilişkili çalışma alanı, geçiş projesi anahtarıyla ve proje adıyla etiketlenir.
**Gerekli aracılar** | Çözümlemek istediğiniz her sunucuda, aşağıdaki aracıları yükleyebilirsiniz:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> [Bağımlılık Aracısı](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Şirket içi sunucular internet 'e bağlı değilse, bunlara Log Analytics ağ geçidi indirip yüklemeniz gerekir.<br/><br/> [Bağımlılık aracısını](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) yükleme ve [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)hakkında daha fazla bilgi edinin.
**Log Analytics çalışma alanı** | Çalışma alanı, projeyle aynı abonelikte olmalıdır.<br/><br/> Azure geçişi Doğu ABD, Güneydoğu Asya ve Batı Avrupa bölgelerinde bulunan çalışma alanlarını destekler.<br/><br/>  Çalışma alanının [hizmet eşlemesi desteklendiği](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)bir bölgede olması gerekir.<br/><br/> Bir projenin çalışma alanı eklendikten sonra değiştirilemez.
**Maliyetler** | Hizmet Eşlemesi çözümü ilk 180 gün boyunca ücret almaz (Log Analytics çalışma alanını projeyle ilişkilendirdiğinizden itibaren)/<br/><br/> 180 günden sonra standart Log Analytics ücretleri uygulanır.<br/><br/> İlişkili Log Analytics çalışma alanında Hizmet Eşlemesi dışında herhangi bir çözümün kullanılması Log Analytics için [Standart ücretler](https://azure.microsoft.com/pricing/details/log-analytics/) doğurur.<br/><br/> Proje silindiğinde, çalışma alanı onunla birlikte silinmez. Projeyi sildikten sonra Hizmet Eşlemesi kullanımı ücretsizdir ve her düğüm, Log Analytics çalışma alanının ücretli katmanına göre ücretlendirilir/<br/><br/>Azure genel kullanım (GA-28 Şubat 2018) geçirmeden önce oluşturduğunuz projeleriniz varsa, ek Hizmet Eşlemesi ücretleri tahakkuk etmeyebilirsiniz. Yalnızca 180 günden sonra ödemeyi sağlamak için, GA 'nin mevcut çalışma alanları Ücretlendirilebilir olmaya devam ettiğinden yeni bir proje oluşturmanızı öneririz.
**Yönetim** | Aracıları çalışma alanına kaydettiğinizde, proje tarafından sunulan KIMLIĞI ve anahtarı kullanırsınız.<br/><br/> Log Analytics çalışma alanını Azure geçişi dışında kullanabilirsiniz.<br/><br/> İlişkili projeyi silerseniz, çalışma alanı otomatik olarak silinmez. [El Ile silin](../azure-monitor/logs/manage-access.md).<br/><br/> Projeyi silmediğiniz takdirde, Azure geçişi tarafından oluşturulan çalışma alanını silmeyin. Bunu yaparsanız, bağımlılık görselleştirme işlevselliği beklendiği gibi çalışmaz.
**İnternet bağlantısı** | Sunucular internet 'e bağlı değilse, bunlara Log Analytics ağ geçidini yüklemeniz gerekir.
**Azure Devlet Kurumları** | Aracı tabanlı bağımlılık Analizi desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

[Hyper-V üzerinde çalışan sunucuların değerlendirmesi Için hazırlanma](./tutorial-discover-hyper-v.md).