---
title: Azure 'da Güncelleştirme Yönetimi çözümü
description: Bu makalede, Windows ve Linux bilgisayarlarınıza yönelik güncelleştirmeleri yönetmek için Azure Güncelleştirme Yönetimi çözümünün nasıl kullanılacağı açıklanır.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 66acb1284f0814eec91715284259272a065dbae2
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690886"
---
# <a name="update-management-solution-in-azure"></a>Azure 'da Güncelleştirme Yönetimi çözümü

Azure 'da, şirket içi ortamlarda ve diğer bulut sağlayıcılarında Windows ve Linux bilgisayarlarınıza yönelik işletim sistemi güncelleştirmelerini yönetmek için Azure Otomasyonu 'nda Güncelleştirme Yönetimi çözümünü kullanabilirsiniz. Tüm aracı bilgisayarlardaki kullanılabilir güncelleştirmelerin durumunu hızla değerlendirebilir ve sunucular için gerekli güncelleştirmeleri yükleme işlemini yönetebilirsiniz.

Sanal makineler (VM) için Güncelleştirme Yönetimi doğrudan Azure Otomasyonu hesabınızdan etkinleştirebilirsiniz. Nasıl yapılacağını öğrenmek için bkz. [birden çok sanal makine için güncelleştirmeleri yönetme](manage-update-multi.md). Ayrıca, bir VM için Güncelleştirme Yönetimi, Azure portal sanal makine sayfasından da etkinleştirebilirsiniz. Bu senaryo, [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) ve [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) VM 'leri için kullanılabilir.

> [!NOTE]
> Güncelleştirme Yönetimi çözümü, Otomasyon hesabınıza bir Log Analytics çalışma alanı bağlamayı gerektirir. Desteklenen bölgelerin kesin listesi için bkz. [Azure çalışma alanı eşlemeleri](./how-to/region-mappings.md). Bölge eşlemeleri, Otomasyon hesabınızdan ayrı bir bölgedeki VM 'Leri yönetme özelliğini etkilemez.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Çözüme genel bakış

Güncelleştirme Yönetimi tarafından yönetilen bilgisayarlar, değerlendirme yapmak ve dağıtımları güncelleştirmek için aşağıdaki konfigürasyonları kullanır:

* Windows veya Linux için Microsoft Monitoring Agent (MMA)
* Linux için PowerShell İstenen Durum Yapılandırması (DSC)
* Otomasyon Karma Runbook Çalışanı
* Windows bilgisayarlar için Microsoft Update veya Windows Server Update Services (WSUS)

Aşağıdaki diyagramda, çözümün çalışma alanındaki tüm bağlı Windows Server ve Linux bilgisayarlarına nasıl değerlendirir ve güvenlik güncelleştirmeleri uyguladığı gösterilmektedir:

![İşlem akışı Güncelleştirme Yönetimi](./media/automation-update-management/update-mgmt-updateworkflow.png)

Güncelleştirme Yönetimi, makineleri aynı kiracıda birden çok aboneliğe yerel olarak eklemek için kullanılabilir.

Bir paket yayımlandıktan sonra, düzeltme ekinin değerlendirme için Linux makinelere gösterilmesi 2 ile 3 saat sürer. Windows makinelerinde, düzeltme ekinin yayımlandıktan sonra değerlendirmesi göstermesi için 12-15 saat sürer.

Bir bilgisayar güncelleştirme uyumluluğu taramasını tamamladıktan sonra, aracı bilgileri toplu olarak Azure Izleyici günlüklerine iletir. Bir Windows bilgisayarda, uyumluluk taraması varsayılan olarak her 12 saatte bir çalıştırılır.

Tarama zamanlamasının yanı sıra, güncelleştirme uyumluluğuna yönelik tarama, güncelleştirme yüklemesinden önce ve güncelleştirme yüklemesinden sonra, 15 dakika içinde başlatılır.

Bir Linux bilgisayar için, uyumluluk taraması varsayılan olarak saatte bir gerçekleştirilir. MMA Aracısı yeniden başlatılırsa, 15 dakika içinde bir uyumluluk taraması başlatılır.

Çözüm, bilgisayarın güncel olduğunu, hangi kaynağa eşitlemek üzere yapılandırılacağını bildirir. Windows bilgisayarı WSUS 'e rapor verecek şekilde yapılandırıldıysa, WSUS Microsoft Update ile en son ne zaman eşitlendiğine bağlı olarak sonuçlar Microsoft Update gösterdiği gibi farklılık gösterebilir. Bu davranış, bir genel depoya değil yerel depoya raporlamak üzere yapılandırılmış Linux bilgisayarları için aynıdır.

> [!NOTE]
> Hizmete doğru bir şekilde raporlamak için, Güncelleştirme Yönetimi belirli URL 'Lerin ve bağlantı noktalarının etkinleştirilmesini gerektirir. Bu gereksinimler hakkında daha fazla bilgi edinmek için bkz. [karma çalışanlar Için ağ planlaması](automation-hybrid-runbook-worker.md#network-planning).

Zamanlanmış bir dağıtım oluşturarak, yazılım güncelleştirmelerinin gerekli olduğu bilgisayarlara güncelleştirmeleri dağıtabilir ve yükleyebilirsiniz. *Isteğe bağlı* olarak sınıflandırılan güncelleştirmeler Windows bilgisayarları için dağıtım kapsamına dahil edilmez. Dağıtım kapsamında yalnızca gerekli güncelleştirmeler bulunur.

Zamanlanan dağıtım, hangi hedef bilgisayarların geçerli güncelleştirmeleri alacağını tanımlar. Bu, belirli bilgisayarları açıkça belirterek veya belirli bir bilgisayar kümesinin günlük aramalarını temel alan bir [bilgisayar grubu](../azure-monitor/platform/computer-groups.md) seçerek (veya belirtilen ölçütlere göre Azure VM 'leri dinamik olarak seçen bir [Azure sorgusunda](automation-update-management-query-logs.md) ). Bu gruplar, yalnızca çözümü etkinleştiren yönetim paketlerini hangi makinelerin almasını belirleyen [kapsam yapılandırmasından](../azure-monitor/insights/solution-targeting.md)farklıdır.

Ayrıca, güncelleştirmelerin yüklenebileceği bir zaman aralığını onaylamak ve ayarlamak için bir zamanlama da belirtirsiniz. Bu döneme bakım penceresi denir. Bakım penceresinin 20 dakikalık bir yayılımı yeniden başlatmalar için ayrılmıştır, bir tane gereklidir ve uygun yeniden başlatma seçeneğini seçmiş olursunuz. Düzeltme Eki beklenenden uzun sürüyorsa ve bakım penceresinde 20 dakikadan kısa bir süre sonra yeniden başlatma gerçekleşmez.

Güncelleştirmeler Azure Automation’daki runbook'lar tarafından yüklenir. Bu runbook 'ları görüntüleyemezsiniz ve herhangi bir yapılandırma gerektirmez. Bir güncelleştirme dağıtımı oluşturulduğunda, dahil edilen bilgisayarlar için belirtilen zamanda ana güncelleştirme runbook 'unu Başlatan bir zamanlama oluşturur. Ana runbook, gerekli güncelleştirmeleri yüklemek için her bir aracıda bir alt runbook başlatır.

Güncelleştirme dağıtımında belirtilen tarih ve saatte, hedef bilgisayarlar dağıtımı paralel olarak yürütür. Yüklemeden önce, güncelleştirmelerin hala gerekli olduğunu doğrulamak üzere bir tarama çalıştırılır. WSUS istemci bilgisayarları için güncelleştirmeler WSUS 'ta onaylanmamışsa güncelleştirme dağıtımı başarısız olur.

Birden fazla Log Analytics çalışma alanında (çoklu giriş) Güncelleştirme Yönetimi için kayıtlı bir makine olması desteklenmez.

## <a name="clients"></a>İstemciler

### <a name="supported-client-types"></a>Desteklenen istemci türleri

Aşağıdaki tabloda güncelleştirme değerlendirmeleri için desteklenen işletim sistemleri listelenmektedir. Düzeltme eki uygulama karma Runbook Worker gerektirir. Karma Runbook Worker gereksinimleri hakkında bilgi için, bkz. [Windows karma Runbook Worker](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) ve [Linux hibrit Runbook Worker](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)yüklemek için Yükleme Kılavuzu.

|İşletim sistemi  |Notlar  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 (RTM ve SP1 standart)||
|CentOS 6 (x86/x64) ve 7 (x64)      | Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır. Sınıflandırma tabanlı düzeltme eki uygulama `yum`, CentOS 'ın RTM sürümlerindeki güvenlik verilerini döndürmesini gerektirir. CentOS üzerinde sınıflandırma tabanlı düzeltme eki uygulama hakkında daha fazla bilgi için bkz. [Linux üzerinde sınıflandırmaları güncelleştirme](#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) ve 7 (x64)     | Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır.        |
|SUSE Linux Enterprise Server 11 (x86/x64) ve 12 (x64)     | Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır.        |
|Ubuntu 14,04 LTS, 16,04 LTS ve 18,04 (x86/x64)      |Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır.         |

> [!NOTE]
> Azure sanal makine ölçek kümeleri, Güncelleştirme Yönetimi aracılığıyla yönetilebilir. Güncelleştirme Yönetimi, temel görüntüde değil örneklerin kendileri üzerinde işe yarar. Tüm sanal makine örneklerinin aynı anda güncelleştirilebilmesi için güncelleştirmeleri artımlı bir şekilde zamanlamanız gerekir.
> [Azure dışı bir makine](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)ekleme altındaki adımları izleyerek sanal makine ölçek kümeleri için düğüm ekleyebilirsiniz.

### <a name="unsupported-client-types"></a>Desteklenmeyen istemci türleri

Aşağıdaki tabloda desteklenmeyen işletim sistemleri listelenmektedir:

|İşletim sistemi  |Notlar  |
|---------|---------|
|Windows istemcisi     | İstemci işletim sistemleri (örneğin, Windows 7 ve Windows 10) desteklenmez.        |
|Windows Server 2016 nano sunucu     | Desteklenmiyor.       |
|Azure Kubernetes hizmet düğümleri | Desteklenmiyor. [Azure Kubernetes Service (AKS) Içindeki Linux düğümlerine güvenlik ve çekirdek güncelleştirmelerini uygulama](../aks/node-updates-kured.md) bölümünde açıklanan düzeltme eki uygulama sürecini kullanın|

### <a name="client-requirements"></a>İstemci gereksinimleri

Aşağıdaki bilgiler, işletim sistemine özgü istemci gereksinimlerini açıklamaktadır. Ek rehberlik için bkz. [ağ planlaması](#ports).

#### <a name="windows"></a>Windows

Windows aracılarının bir WSUS sunucusu ile iletişim kurmak için yapılandırılması gerekir veya Microsoft Update erişimi olmalıdır.

System Center Configuration Manager Güncelleştirme Yönetimi kullanabilirsiniz. Tümleştirme senaryoları hakkında daha fazla bilgi için bkz. [Güncelleştirme Yönetimi System Center Configuration Manager tümleştirme](oms-solution-updatemgmt-sccmintegration.md#configuration). [Windows Aracısı](../azure-monitor/platform/agent-windows.md) gereklidir. Bir Azure VM 'yi oluşturuyorsanız, aracı otomatik olarak yüklenir.

Varsayılan olarak, Azure Marketi 'nden dağıtılan Windows VM 'Leri Windows Update hizmetinden otomatik güncelleştirmeleri alacak şekilde ayarlanır. Bu çözümü eklediğinizde veya çalışma alanınıza Windows VM 'Leri eklediğinizde bu davranış değişmez. Bu çözümü kullanarak güncelleştirmeleri etkin bir şekilde yönetmezseniz varsayılan davranış (güncelleştirmeleri otomatik olarak uygulamak için) geçerlidir.

> [!NOTE]
> Bir Kullanıcı, makine yeniden başlatmaları yalnızca Kullanıcı tarafından değil, sistem tarafından gerçekleştirilebileceği şekilde grup ilkesi değiştirebilir. Güncelleştirme Yönetimi, kullanıcıdan el ile etkileşim kurmadan makinenin yeniden başlatılması için haklara sahip değilse, yönetilen makineler kalabilir.
>
> Daha fazla bilgi için bkz. [Otomatik Güncelleştirmeler için Grup İlkesi ayarlarını yapılandırma](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Linux için, makinenin bir güncelleştirme deposuna erişimi olması gerekir. Güncelleştirme deposu özel veya genel olabilir. Güncelleştirme Yönetimi etkileşimde bulunmak için TLS 1,1 veya TLS 1,2 gereklidir. Linux için birden fazla Log Analytics çalışma alanına raporlamak üzere yapılandırılmış bir Log Analytics Aracısı bu çözümle desteklenmez. Makinede Python 2. x de yüklü olmalıdır.

Linux için Log Analytics aracısını yükleme ve en son sürümü indirme hakkında daha fazla bilgi için bkz. [Linux için Log Analytics Aracısı](https://github.com/microsoft/oms-agent-for-linux). Windows için Log Analytics aracısının nasıl yükleneceği hakkında bilgi için bkz. [Windows bilgisayarlarını Azure izleyici 'ye bağlama](../log-analytics/log-analytics-windows-agent.md).

Azure Marketi 'nde bulunan isteğe bağlı Red Hat Enterprise Linux (RHEL) görüntülerinden oluşturulan VM 'Ler, Azure 'da dağıtılan [Red Hat güncelleştirme altyapısına (rhuı)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) erişmek için kaydedilir. Diğer tüm Linux dağıtımı, dağıtımın desteklenen yöntemleri kullanılarak dağıtımın çevrimiçi dosya deposundan güncelleştirilmeleri gerekir.

## <a name="permissions"></a>İzinler

Güncelleştirme dağıtımları oluşturmak ve yönetmek için belirli izinlere sahip olmanız gerekir. Bu izinler hakkında bilgi edinmek için bkz. [rol tabanlı erişim – güncelleştirme yönetimi](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Çözüm bileşenleri

Çözüm aşağıdaki kaynaklardan oluşur. Kaynaklar Otomasyon hesabınıza eklenir. Bunlar doğrudan bağlı aracılardan veya Operations Manager bağlı bir yönetim grubunda yer alırlar.

### <a name="hybrid-worker-groups"></a>Karma Çalışanı grupları

Bu çözümü etkinleştirdikten sonra, Log Analytics çalışma alanınıza doğrudan bağlı olan tüm Windows bilgisayarları, bu çözüme dahil olan runbook 'ları destekleyecek bir karma runbook çalışanı olarak otomatik olarak yapılandırılır.

Çözüm tarafından yönetilen her Windows bilgisayarı, Otomasyon hesabı için bir **sistem karma çalışanı grubu** olarak **karma çalışan grupları** bölmesinde listelenir. Çözümler *konak adı FQDN_GUID* adlandırma kuralını kullanır. Bu grupları hesabınızdaki runbook 'lar ile hedefleyebilirsiniz. Denerseniz, deneme başarısız olur. Bu gruplar yalnızca yönetim çözümünü desteklemek üzere tasarlanmıştır.

Hem çözüm hem de karma runbook çalışanı grup üyeliği için aynı hesabı kullanırsanız Otomasyon Runbook 'larını desteklemek için Otomasyon hesabınızdaki karma Runbook Worker grubuna Windows bilgisayarları ekleyebilirsiniz. Bu işlev, karma Runbook Worker 'ın 7.2.12024.0 sürümüne eklenmiştir.

### <a name="management-packs"></a>Yönetim paketleri

System Center Operations Manager yönetim grubunuz bir Log Analytics çalışma alanına bağlıysa, aşağıdaki yönetim paketleri Operations Manager yüklenir. Bu yönetim paketleri, çözümü ekledikten sonra doğrudan bağlı Windows bilgisayarlarına da yüklenir. Bu yönetim paketlerini yapılandırmanıza veya yönetmenize gerek yoktur.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* MP Dağıtımını güncelleştirme

> [!NOTE]
> Yönetim grubu düzeyinde, aracıları bir çalışma alanıyla ilişkilendirmek üzere yapılandırılmış Operations Manager 1807 veya 2019 yönetim grubunuz olduğunu varsayalım. **Microsoft. ıntelligencepacks. AzureAutomation. Hybridavgent. Init** kuralında, **ıstomatik RegistrationEnabled** özelliğinin **true** olarak ayarlanması için geçerli geçici çözüm.

Çözüm Yönetimi paketlerinin nasıl güncelleştirildiği hakkında daha fazla bilgi için bkz. [Azure izleyici günlüklerine bağlanma Operations Manager](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Operations Manager aracısına sahip sistemler için: bir aracının Güncelleştirme Yönetimi tarafından tam olarak yönetilmesi için aracının MMA 'ya güncelleştirilmeleri gerekir. Aracıyı güncelleştirme hakkında bilgi edinmek için bkz. [Operations Manager aracısını yükseltme](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Operations Manager kullanan ortamlarda, System Center Operations Manager 2012 R2 UR 14 veya sonraki bir sürümünü çalıştırıyor olmanız gerekir.

## <a name="data-collection"></a>Veri toplama

### <a name="supported-agents"></a>Desteklenen aracılar

Aşağıdaki tabloda bu çözüm tarafından desteklenen bağlı kaynaklar açıklanmaktadır:

| Bağlı kaynak | Desteklenen | Açıklama |
| --- | --- | --- |
| Windows aracıları |Yes |Çözüm, Windows aracılarından sistem güncelleştirmeleri hakkında bilgi toplar ve gerekli güncelleştirmelerin yüklemesini başlatır. |
| Linux aracıları |Yes |Çözüm, Linux aracılarından sistem güncelleştirmeleri hakkında bilgi toplar ve desteklenen dağıtımlarda gerekli güncelleştirmelerin yüklemesini başlatır. |
| Operations Manager yönetim grubu |Yes |Çözüm, bağlı bir yönetim grubundaki aracılardan sistem güncelleştirmeleri hakkında bilgi toplar.<br/><br/>Operations Manager aracısından Azure Izleyici günlüklerine doğrudan bağlantı gerekli değildir. Veriler, yönetim grubundan Log Analytics çalışma alanına iletilir. |

### <a name="collection-frequency"></a>Toplama sıklığı

Yönetilen her Windows bilgisayarı için günde iki kez tarama gerçekleştirilir. Her 15 dakikada bir Windows API 'si, durumun değişip değişmediğini belirlemekte son güncelleştirme zamanını sorgulamak için çağırılır. Durum değiştiyse, bir uyumluluk taraması başlatılır.

Her yönetilen Linux bilgisayar için saatte bir tarama gerçekleştirilir.

Panonun yönetilen bilgisayarlardan güncelleştirilmiş verileri görüntülemesi 30 dakika ile 6 saat arasında sürebilir.

Güncelleştirme Yönetimi kullanan bir makineye yönelik Azure Izleyici günlüklerinin ortalama veri kullanımı yaklaşık olarak ayda 25 megabayt (MB). Bu değer yalnızca bir yaklaşık değerdir ve ortamınıza bağlı olarak değişebilir. Tam kullanımınızın izlenmesini sağlamak için ortamınızı izlemenizi öneririz.

## <a name="ports"></a>Ağ planlama

Aşağıdaki adresler Güncelleştirme Yönetimi için özel olarak gereklidir. 443 numaralı bağlantı noktası üzerinden bu adreslerle iletişim oluşur.

|Azure genel  |Azure Devlet Kurumları  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

Windows makinelerinde, Windows Update için gereken tüm uç noktalara giden trafiğe de izin vermeniz gerekir. [Http/proxy ile Ilgili sorunlarda](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy), gerekli uç noktaların güncelleştirilmiş bir listesini bulabilirsiniz. Yerel bir [Windows Update sunucunuz](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)varsa, [WSUS anahtarınıza](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)belirtilen sunucuya giden trafiğe de izin vermeniz gerekir.

Red Hat Linux makineleri için, gerekli uç noktalar için [rhuı içerik teslim sunucuları Için IP 'ler](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) bölümüne bakın. Diğer Linux dağıtımları için sağlayıcı belgelerinize bakın.

Karma Runbook Worker için gereken bağlantı noktaları hakkında daha fazla bilgi için bkz. [karma çalışan rolü bağlantı noktaları](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Özel durumları tanımlarken listelenen adresleri kullanmanızı öneririz. IP adresleri için [Microsoft Azure veri MERKEZI IP aralıklarını](https://www.microsoft.com/download/details.aspx?id=41653)indirebilirsiniz. Bu dosya haftalık olarak güncelleştirilir ve şu anda dağıtılmış aralıkları ve IP aralıklarında yaklaşan değişiklikleri yansıtır.

İnternet erişimi olmayan makineleri yapılandırmak için [İnternet erişimi olmadan bilgisayarları bağlama](../azure-monitor/platform/gateway.md) bölümündeki yönergeleri izleyin.

## <a name="view-update-assessments"></a>Güncelleştirme değerlendirmelerini görüntüleme

Otomasyon hesabınızda, makinelerinizin durumunu görüntülemek için **güncelleştirme yönetimi** ' yi seçin.

Bu görünüm, makineleriniz, eksik güncelleştirmeler, güncelleştirme dağıtımları ve zamanlanmış güncelleştirme dağıtımları hakkında bilgi sağlar. **Uyumluluk** sütununda, makinenin en son değerlendirildiğini görebilirsiniz. **GÜNCELLEŞTIRME ARACıSı hazırlığı** sütununda, güncelleştirme aracısının sistem durumunu kontrol edebilirsiniz. Bir sorun varsa, sorunu düzeltmenize yardımcı olabilecek sorun giderme belgelerine gitmek için bağlantıyı seçin.

Makine, güncelleştirme veya dağıtım hakkında bilgi döndüren bir günlük araması çalıştırmak için, listeden karşılık gelen öğeyi seçin. **Günlük araması** bölmesi, seçilen öğe için bir sorgu ile açılır:

![Güncelleştirme Yönetimi varsayılan görünüm](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Eksik güncelleştirmeleri görüntüle

Makinelerinizde bulunmayan güncelleştirmelerin listesini görüntülemek için **eksik güncelleştirmeler** ' i seçin. Her güncelleştirme listelenir ve seçilebilir. Güncelleştirme, işletim sistemi ve daha fazla bilgi için bir bağlantı gerektiren makine sayısı hakkında bilgi gösterilir. **Günlük araması** bölmesinde güncelleştirmeler hakkında daha fazla ayrıntı görüntülenir.

![Eksik güncelleştirmeler](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Güncelleştirme sınıflandırmaları

Aşağıdaki tablolar, Güncelleştirme Yönetimi ' deki güncelleştirme sınıflandırmalarını her sınıflandırma için bir tanım ile listeler.

### <a name="windows"></a>Windows

|Sınıflandırma  |Açıklama  |
|---------|---------|
|Kritik güncelleştirmeler     | Kritik, güvenlikle ilgili olmayan bir hatayı ele alan belirli bir sorun için güncelleştirme.        |
|Güvenlik güncelleştirmeleri     | Ürüne özgü, güvenlikle ilgili bir sorun için bir güncelleştirme.        |
|Güncelleştirme paketleri     | Kolay dağıtım için bir arada paketlenmiş toplu bir düzeltme kümesi.        |
|Özellik paketleri     | Ürün sürümü dışında dağıtılan yeni ürün özellikleri.        |
|Hizmet paketleri     | Bir uygulamaya uygulanan toplu bir düzeltme kümesi.        |
|Tanım güncelleştirmeleri     | Virüs veya diğer tanım dosyalarına yönelik bir güncelleştirme.        |
|Araçlar     | Bir veya daha fazla görevi tamamlamaya yardımcı olan bir yardımcı program veya özellik.        |
|Güncellemeler     | Şu anda yüklü olan bir uygulama veya dosyaya yönelik bir güncelleştirme.        |

### <a name="linux-2"></a>'Un

|Sınıflandırma  |Açıklama  |
|---------|---------|
|Kritik güncelleştirmeler ve güvenlik güncelleştirmeleri     | Belirli bir sorun veya ürüne özgü, güvenlikle ilgili bir sorun için güncelleştirmeler.         |
|Diğer güncelleştirmeler     | Doğası gereği önemli olmayan veya güvenlik güncelleştirmeleri olmayan diğer tüm güncelleştirmeler.        |

Linux için Güncelleştirme Yönetimi, bulutta veri zenginleştirme nedeniyle değerlendirme verilerini görüntülerken buluttaki kritik güncelleştirmeler ve güvenlik güncelleştirmeleri arasında ayrım yapabilir. Düzeltme eki uygulama Güncelleştirme Yönetimi makinede bulunan sınıflandırma verilerine bağımlıdır. Diğer dağıtımlardan farklı olarak, CentOS bu bilgileri RTM sürümünde kullanılamaz. Aşağıdaki komut için güvenlik verilerini döndürecek şekilde yapılandırılmış CentOS makineleriniz varsa, Güncelleştirme Yönetimi sınıflandırmalara göre düzeltme eki uygulanabilir.

```bash
sudo yum -q --security check-update
```

Şu anda yerel sınıflandırmanın etkinleştirilmesi için desteklenen bir yöntem yok-CentOS üzerinde veri kullanılabilirliği. Şu anda, bu özelliği kendi kendilerine etkinleştirmiş olabilecek müşterilere yalnızca en iyi çaba desteği sağlanır.

## <a name="integrate-with-system-center-configuration-manager"></a>System Center Configuration Manager ile tümleştirme

Bilgisayarları, sunucuları ve mobil cihazları yönetmek için System Center Configuration Manager yatırım yapmış müşteriler, yazılım güncelleştirmelerini yönetmeye yardımcı olmak için Configuration Manager kuvvetini ve vadede yararlanır. Configuration Manager, yazılım güncelleştirme yönetimi (SUM) döngüsünün bir parçasıdır.

Yönetim çözümünü System Center Configuration Manager ile tümleştirmeyi öğrenmek için bkz. [System Center Configuration Manager güncelleştirme yönetimi Ile tümleştirme](oms-solution-updatemgmt-sccmintegration.md).

### <a name="third-party-patches-on-windows"></a>Windows 'da üçüncü taraf düzeltme ekleri

Güncelleştirme Yönetimi, desteklenen Windows sistemlerine yama yapmak için yerel olarak yapılandırılmış güncelleştirme deposuna bağımlıdır. Bu, WSUS veya Windows Update. [System Center Updates Publisher](/sccm/sum/tools/updates-publisher) (Updates Publisher) gibi araçlar, özel güncelleştirmeleri WSUS 'ta yayımlamanıza izin verir. Bu senaryo Güncelleştirme Yönetimi, üçüncü taraf yazılımlarla güncelleştirme deposu olarak System Center Configuration Manager kullanan makinelere yaması sağlar. Updates Publisher 'ı yapılandırma hakkında bilgi edinmek için bkz. [Updates Publisher 'ı yüklemek](/sccm/sum/tools/install-updates-publisher).

## <a name="patch-linux-machines"></a>Linux makinelerini yama

Aşağıdaki bölümlerde, Linux düzeltme ekiyle ilgili olası sorunlar açıklanmaktadır.

### <a name="unexpected-os-level-upgrades"></a>Beklenmeyen işletim sistemi düzeyi yükseltmeleri

Red Hat Enterprise Linux gibi bazı Linux değişkenlerde, işletim sistemi düzeyinde yükseltmeler paketler aracılığıyla gerçekleşebilir. Bu, işletim sistemi sürümü numarasının değiştiği Güncelleştirme Yönetimi çalışmasına yol açabilir. Güncelleştirme Yönetimi, bir yöneticinin Linux bilgisayarda yerel olarak kullanacağı paketleri güncelleştirmek için aynı yöntemleri kullandığından, bu davranış bilerek yapılır.

İşletim sistemi sürümünün Güncelleştirme Yönetimi çalıştırmaları aracılığıyla güncelleştirilmesini önlemek için **dışlama** özelliğini kullanın.

Red Hat Enterprise Linux, dışlanacak paket adı RedHat-Release-Server. x86_64 ' dır.

![Linux için dışlanacak paketler](./media/automation-update-management/linuxpatches.png)

### <a name="criticalsecurity-patches-arent-applied"></a>Kritik/güvenlik düzeltme ekleri uygulanmadı

Güncelleştirmeleri bir Linux makinesine dağıtırken, güncelleştirme sınıflandırmalarını seçebilirsiniz. Bu seçenek, belirtilen ölçütlere uyan makineye uygulanan güncelleştirmeleri filtreler. Bu filtre, güncelleştirme dağıtıldığında makinede yerel olarak uygulanır.

Güncelleştirme Yönetimi bulutta güncelleştirme zenginleştirmesini gerçekleştirdiğinden, bazı güncelleştirmeler, yerel makinede bu bilgilere sahip olmasa bile güvenlik etkisi olan Güncelleştirme Yönetimi olarak işaretlenir. Sonuç olarak, bir Linux makinesine kritik güncelleştirmeler uygularsanız, bu makinede güvenlik etkisi olması halinde işaretlenmemiş güncelleştirmeler olabilir ve bu nedenle güncelleştirmelerin uygulanmadığından emin olabilirsiniz. Ancak, Güncelleştirme Yönetimi ilgili güncelleştirme hakkında ek bilgi içerdiğinden, bu makineyi hala uyumsuz olarak raporlayabilir.

Güncelleştirme sınıflandırmasına göre güncelleştirmelerin dağıtımı, CentOS 'in RTM sürümlerinde çalışmaz. CentOS güncelleştirmelerini doğru bir şekilde dağıtmak için, güncelleştirmelerin uygulandığından emin olmak için tüm sınıflandırmalar ' ı seçin. SUSE için, sınıflandırma olarak *yalnızca* **diğer güncelleştirmelerin** seçilmesi, zypper (Paket Yöneticisi) ile ilgili güvenlik güncelleştirmelerinin veya bağımlılıklarından önce kullanılması durumunda bazı güvenlik güncelleştirmelerinin de yüklenememesine neden olabilir. Bu davranış, zypper kısıtlamasıdır. Bazı durumlarda, güncelleştirme dağıtımını yeniden çalıştırmanız gerekebilir. Doğrulamak için güncelleştirme günlüğünü kontrol edin.

### <a name="multi-tenant"></a>Çapraz kiracı güncelleştirme dağıtımları

Başka bir Azure kiracı raporlarında, düzeltme eki uygulamanız gerektiğini Güncelleştirme Yönetimi makineniz varsa, bunları zamanlanmak için aşağıdaki geçici çözümü kullanmanız gerekir. Bir zamanlama oluşturmak için [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet 'ini `-ForUpdate` anahtarıyla kullanabilir ve [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet 'ini kullanarak diğer Kiracıdaki makineleri `-NonAzureComputer` parametresine geçirebilirsiniz. Aşağıdaki örnek bunun nasıl yapılacağını göstermektedir:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>Güncelleştirme Yönetimi etkinleştir

Düzeltme eki uygulama sistemlerine başlamak için Güncelleştirme Yönetimi çözümünü etkinleştirmeniz gerekir. Güncelleştirme Yönetimi makineleri eklemek için birçok yol vardır. Çözümü eklemek için önerilen ve desteklenen yollar şunlardır:

* [Bir sanal makineden](automation-onboard-solutions-from-vm.md)
* [Birden çok makineye göz atmaya](automation-onboard-solutions-from-browse.md)
* [Otomasyon hesabınızdan](automation-onboard-solutions-from-automation-account.md)
* [Bir Azure Otomasyonu runbook 'u ile](automation-onboard-solutions.md)

## <a name="next-steps"></a>Sonraki adımlar

Windows VM 'leriniz için güncelleştirmelerin nasıl yönetileceğini öğrenmek için aşağıdaki öğreticiyi kullanın:

> [!div class="nextstepaction"]
> [Azure Windows VM 'leriniz için güncelleştirmeleri ve düzeltme eklerini yönetme](automation-tutorial-update-management.md)

* Ayrıntılı güncelleştirme verilerini görüntülemek için [Azure izleyici günlüklerinde](../log-analytics/log-analytics-log-searches.md) günlük aramalarını kullanın.
* Güncelleştirme dağıtım durumu için [uyarı oluştur](automation-tutorial-update-management.md#configure-alerts) .

* REST API üzerinden Güncelleştirme Yönetimi nasıl etkileşim kuracağınızı öğrenmek için bkz. [yazılım güncelleştirme yapılandırması](/rest/api/automation/softwareupdateconfigurations).
* Güncelleştirme Yönetimi sorunlarını giderme hakkında bilgi edinmek için bkz. [güncelleştirme yönetimi sorun giderme](troubleshoot/update-management.md).
