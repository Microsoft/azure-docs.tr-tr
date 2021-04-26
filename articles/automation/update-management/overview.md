---
title: Azure Otomasyonu Güncelleştirme Yönetimi Genel Bakış
description: Bu makalede, Windows ve Linux makineleriniz için güncelleştirmeleri uygulayan Güncelleştirme Yönetimi özelliğine bir genel bakış sunulmaktadır.
services: automation
ms.subservice: update-management
ms.date: 04/01/2021
ms.topic: conceptual
ms.openlocfilehash: 62ae2eab33063416fdd6265b14dd8c30da55e174
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166709"
---
# <a name="update-management-overview"></a>Güncelleştirme Yönetimi’ne genel bakış

Azure Otomasyonu 'ndaki Güncelleştirme Yönetimi kullanarak Azure 'daki Windows ve Linux sanal makineleriniz için işletim sistemi güncelleştirmelerini, şirket içi ortamları ve diğer bulut ortamlarında yönetebilirsiniz. Tüm aracı makinelerde kullanılabilir güncelleştirmelerin durumunu hızlı bir şekilde değerlendirebilir ve sunucular için gerekli güncelleştirmeleri yükleme işlemini yönetebilirsiniz.

Hizmet sağlayıcı olarak, [Azure açık Thouse](../../lighthouse/overview.md)için birden fazla müşteri kiracısından eklendi olabilirsiniz. Azure Mathouse, her seferinde çok sayıda Azure Active Directory (Azure AD) kiracılarının ölçeklendirilmesine yönelik işlemler gerçekleştirmenize olanak tanır ve Güncelleştirme Yönetimi gibi yönetim görevlerini, sorumlu olduğunuz kiracılar arasında daha verimli hale getirir.

> [!NOTE]
> Azure Otomasyonu 'ndan özel betikleri çalıştırmak için Güncelleştirme Yönetimi ile yapılandırılmış bir makine kullanamazsınız. Bu makine, yalnızca Microsoft tarafından imzalanmış güncelleştirme betiğini çalıştırabilir.

> [!NOTE]
> Şu anda, bir yay etkin sunucusundan Güncelleştirme Yönetimi doğrudan etkinleştirme desteklenmez. Gereksinimleri anlamak ve sunucunuz için nasıl etkinleştireceğinizi anlamak için bkz. [Otomasyon hesabınızdan güncelleştirme yönetimi etkinleştirme](../../automation/update-management/enable-from-automation-account.md) .

Kullanılabilir *kritik* ve *güvenlik* düzeltme eklerini Azure sanal makinenize otomatik olarak Indirip yüklemek için Windows VM 'leri için [Otomatik VM Konuk düzeltme eki uygulamayı](../../virtual-machines/automatic-vm-guest-patching.md) gözden geçirin.

Güncelleştirme Yönetimi dağıtılmadan ve makinelerinizi yönetim için etkinleştirmeden önce, aşağıdaki bölümlerde yer alan bilgileri anladığınızdan emin olun.

## <a name="about-update-management"></a>Güncelleştirme Yönetimi hakkında

Güncelleştirme Yönetimi tarafından yönetilen makineler, değerlendirmesi gerçekleştirmek ve güncelleştirmeleri dağıtmak için aşağıdakileri kullanır:

* Windows veya Linux için [Log Analytics Aracısı](../../azure-monitor/agents/log-analytics-agent.md)
* Linux için PowerShell İstenen Durum Yapılandırması (DSC)
* Otomasyon karma Runbook Worker (makinede Güncelleştirme Yönetimi etkinleştirdiğinizde otomatik olarak yüklenir)
* Windows makineleri için Microsoft Update veya [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS)
* Linux makineleri için özel veya ortak bir güncelleştirme deposu

Aşağıdaki diyagramda, bir çalışma alanındaki tüm bağlı Windows Server ve Linux sunucularına nasıl Güncelleştirme Yönetimi değerlendirir ve güvenlik güncelleştirmelerinin uygulandığı gösterilmektedir:

![Güncelleştirme Yönetimi iş akışı](./media/overview/update-mgmt-updateworkflow.png)

Güncelleştirme Yönetimi, aynı Kiracıdaki birden çok abonelikteki makinelere veya [Azure tarafından atanan kaynak yönetimini](../../lighthouse/concepts/azure-delegated-resource-management.md)kullanarak kiracılar arasında yerel olarak dağıtım yapmak için kullanılabilir.

Bir paket yayımlandıktan sonra, düzeltme ekinin değerlendirme için Linux makinelere gösterilmesi 2 ile 3 saat sürer. Windows makinelerinde, düzeltme ekinin yayımlandıktan sonra değerlendirmesi göstermesi için 12-15 saat sürer. Bir makine, güncelleştirme uyumluluğu için bir tarama tamamladığında, aracı bilgileri toplu olarak Azure Izleyici günlüklerine iletir. Bir Windows makinesinde, uyumluluk taraması varsayılan olarak her 12 saatte bir çalıştırılır. Bir Linux makinesi için, uyumluluk taraması her saat varsayılan olarak gerçekleştirilir. Log Analytics Aracısı yeniden başlatılırsa, 15 dakika içinde bir uyumluluk taraması başlatılır.

Tarama zamanlamasının yanı sıra, güncelleştirme uyumluluğu taraması, Log Analytics aracısının yeniden başlatılmasından sonra, güncelleştirme yüklemesinden önce ve güncelleştirme yüklemesinden sonra başlatılır.

Güncelleştirme Yönetimi, makinenin ne kadar güncel olduğunu, ile eşitlenecek şekilde yapılandırdığınız kaynağı temel alır. Windows makinesi [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) 'a (WSUS) rapor verecek şekilde YAPıLANDıRıLDıYSA, WSUS Microsoft Update ile en son ne zaman eşitlendiğine bağlı olarak sonuçlar Microsoft Update gösterdiği gibi farklılık gösterebilir. Bu davranış, bir genel depoya değil yerel depoya raporlamak üzere yapılandırılmış Linux makineleri için aynıdır.

> [!NOTE]
> Hizmete doğru bir şekilde raporlamak için, Güncelleştirme Yönetimi belirli URL 'Lerin ve bağlantı noktalarının etkinleştirilmesini gerektirir. Bu gereksinimler hakkında daha fazla bilgi edinmek için bkz. [ağ yapılandırması](../automation-hybrid-runbook-worker.md#network-planning).

Zamanlanmış bir dağıtım oluşturarak güncelleştirmeleri gerektiren makinelere yazılım güncelleştirmeleri dağıtabilir ve yükleyebilirsiniz. İsteğe bağlı olarak sınıflandırılan güncelleştirmeler Windows makineler için dağıtım kapsamına dahil edilmez. Dağıtım kapsamında yalnızca gerekli güncelleştirmeler bulunur.

Zamanlanan dağıtım, hangi hedef makinelerin geçerli güncelleştirmeleri alacağını tanımlar. Bu, belirli makineleri açıkça belirterek ya da belirli bir makine kümesinin (ya da belirtilen ölçütlere göre dinamik olarak Azure VM 'Leri seçen bir [Azure sorgusundaki](query-logs.md) ) günlük aramalarını temel alan bir [bilgisayar grubu](../../azure-monitor/logs/computer-groups.md) seçerek gerçekleştirir. Bu gruplar, Güncelleştirme Yönetimi etkinleştirmek üzere yapılandırmayı alan makinelerin hedeflenmesini denetlemek için kullanılan [kapsam yapılandırmasından](../../azure-monitor/insights/solution-targeting.md)farklıdır. Bu, güncelleştirme uyumluluğunu gerçekleştirmenizi ve raporlamasını ve onaylanan gerekli güncelleştirmeleri yüklemenizi engeller.

Bir dağıtım tanımlarken, güncelleştirmelerin yüklenebileceği bir zaman aralığını onaylamak ve ayarlamak için bir zamanlama da belirtirsiniz. Bu döneme bakım penceresi denir. Bakım penceresinin 20 dakikalık bir yayılımı yeniden başlatmalar için ayrılmıştır, bir tane gereklidir ve uygun yeniden başlatma seçeneğini seçmiş olursunuz. Düzeltme Eki beklenenden uzun sürüyorsa ve bakım penceresinde 20 dakikadan kısa bir süre sonra yeniden başlatma gerçekleşmez.

Güncelleştirmeler Azure Otomasyonu’nda runbook'lar tarafından yüklenir. Bu runbook 'ları görüntüleyemezsiniz ve herhangi bir yapılandırma gerektirmez. Bir güncelleştirme dağıtımı oluşturulduğunda, dahil edilen makineler için belirtilen zamanda ana güncelleştirme runbook 'unu Başlatan bir zamanlama oluşturur. Ana runbook, gerekli güncelleştirmeleri yüklemek için her bir aracıda bir alt runbook başlatır.

Güncelleştirme dağıtımında belirtilen tarih ve saatte, hedef makineler dağıtımı paralel olarak yürütür. Yüklemeden önce, güncelleştirmelerin hala gerekli olduğunu doğrulamak üzere bir tarama çalıştırılır. WSUS istemci makineleri için güncelleştirmeler WSUS 'ta onaylanmamışsa güncelleştirme dağıtımı başarısız olur.

Birden fazla Log Analytics çalışma alanında (aynı zamanda çoklu kayıt olarak da adlandırılır) Güncelleştirme Yönetimi için bir makinenin kayıtlı olması desteklenmez.

## <a name="clients"></a>İstemciler

### <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Aşağıdaki tabloda güncelleştirme değerlendirmeleri ve düzeltme eki uygulama için desteklenen işletim sistemleri listelenmektedir. Düzeltme eki uygulama, Güncelleştirme Yönetimi tarafından yönetim için sanal makineyi veya sunucuyu etkinleştirdiğinizde otomatik olarak yüklenen bir sistem karma Runbook Worker gerektirir. Karma Runbook Worker sistem gereksinimleri hakkında bilgi için bkz. [Windows karma Runbook Worker dağıtma](../automation-windows-hrw-install.md) ve [Linux karma Runbook Worker](../automation-linux-hrw-install.md)dağıtımı.

> [!NOTE]
> Linux makinelerin güncelleştirme değerlendirmesi yalnızca Otomasyon hesabı ve Log Analytics çalışma alanı [eşlemeleri tablosunda](../how-to/region-mappings.md#supported-mappings)listelenen belirli bölgelerde desteklenir.

|İşletim sistemi  |Notlar  |
|---------|---------|
|Windows Server 2019 (sunucu çekirdeği dahil Datacenter/Standard)<br><br>Windows Server 2016 (sunucu çekirdeğini hariç Datacenter/Standard)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 | |
|Windows Server 2008 R2 (RTM ve SP1 standart)| Güncelleştirme Yönetimi, bu işletim sistemi için değerlendirmeleri ve düzeltme eki uygulamayı destekler. [Karma Runbook Worker](../automation-windows-hrw-install.md) , Windows Server 2008 R2 için desteklenir. |
|CentOS 6, 7 ve 8 (x64)      | Linux aracılarının bir güncelleştirme deposuna erişmesi gerekir. Sınıflandırma tabanlı düzeltme eki `yum` , CentOS 'ıN RTM sürümlerindeki güvenlik verilerini döndürmesini gerektirir. CentOS üzerinde sınıflandırma tabanlı düzeltme eki uygulama hakkında daha fazla bilgi için bkz. [Linux üzerinde sınıflandırmaları güncelleştirme](view-update-assessments.md#linux).          |
|Red Hat Enterprise 6, 7 ve 8 (x64)     | Linux aracılarının bir güncelleştirme deposuna erişmesi gerekir.        |
|SUSE Linux Enterprise Server 12, 15 ve 15,1 (x64)     | Linux aracılarının bir güncelleştirme deposuna erişmesi gerekir. SUSE 15. x için, makinede Python 3 gereklidir.      |
|Ubuntu 14,04 LTS, 16,04 LTS ve 18,04 LTS (x64)      |Linux aracılarının bir güncelleştirme deposuna erişmesi gerekir.         |

> [!NOTE]
> Güncelleştirme Yönetimi, bir Azure sanal makine ölçek kümesindeki tüm örneklerde güncelleştirme yönetimini güvenli bir şekilde otomatikleştirmenizi desteklemez. [Otomatik işletim sistemi görüntüsü yükseltmeleri](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) , ölçek kümesinde işletim sistemi görüntüsü yükseltmelerini yönetmek için önerilen yöntemdir.

### <a name="unsupported-operating-systems"></a>Desteklenmeyen işletim sistemleri

Aşağıdaki tabloda Güncelleştirme Yönetimi tarafından desteklenmeyen işletim sistemleri listelenmektedir:

|İşletim sistemi  |Notlar  |
|---------|---------|
|Windows istemcisi     | İstemci işletim sistemleri (örneğin, Windows 7 ve Windows 10) desteklenmez.<br> Azure Windows sanal masaüstü (WVD) için önerilen yöntem<br> güncelleştirmeleri yönetmek için Windows 10 istemci makinesi düzeltme eki yönetimi için [Microsoft uç nokta Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) . |
|Windows Server 2016 Nano Server     | Desteklenmez.       |
|Azure Kubernetes hizmet düğümleri | Desteklenmez. [Azure Kubernetes Service (AKS) Içindeki Linux düğümlerine güvenlik ve çekirdek güncelleştirmelerini uygulama](../../aks/node-updates-kured.md) bölümünde açıklanan düzeltme eki uygulama sürecini kullanın|

### <a name="system-requirements"></a>Sistem Gereksinimleri

Aşağıdaki bilgiler işletim sistemine özgü gereksinimleri açıklar. Ek rehberlik için bkz. [ağ planlaması](#ports). TLS 1,2 gereksinimlerini anlamak için bkz. [Azure Otomasyonu Için tls 1,2 zorlaması](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

#### <a name="windows"></a>Windows

Yazılım gereksinimleri:

- .NET Framework 4,6 veya üzeri gereklidir. ([.NET Framework indirin](/dotnet/framework/install/guide-for-developers).
- Windows PowerShell 5,1 gereklidir ([Windows Management Framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616)' i indirin.)

Windows aracılarının bir WSUS sunucusuyla iletişim kuracak şekilde yapılandırılması veya Microsoft Update erişmesi gerekir. Karma makinelerde, önce makinenizi [Azure Arc etkin sunucularına](../../azure-arc/servers/overview.md)bağlayarak windows için Log Analytics aracısını yüklemenizi ve ardından Azure ilkesi 'ni kullanarak [Log Analytics aracısını Windows Azure Arc machines](../../governance/policy/samples/built-in-policies.md#monitoring) yerleşik ilkesine atamanız önerilir. Alternatif olarak, makineleri VM'ler için Azure İzleyici ile izlemeyi planlıyorsanız, bunun yerine [Enable VM'ler için Azure izleyici](../../governance/policy/samples/built-in-initiatives.md#monitoring) girişimi kullanın.

Güncelleştirme Yönetimi, Microsoft uç nokta Configuration Manager ile kullanabilirsiniz. Tümleştirme senaryoları hakkında daha fazla bilgi için bkz. [Windows uç nokta Configuration Manager ile güncelleştirme yönetimi tümleştirme](mecmintegration.md). [Windows için Log Analytics Aracısı](../../azure-monitor/agents/agent-windows.md) , Configuration Manager ortamınızdaki siteler tarafından yönetilen Windows sunucuları için gereklidir.

Varsayılan olarak, Azure Marketi 'nden dağıtılan Windows VM 'Leri Windows Update hizmetinden otomatik güncelleştirmeleri alacak şekilde ayarlanır. Bu davranış, çalışma alanınıza Windows VM 'Leri eklediğinizde değişmez. Güncelleştirme Yönetimi kullanarak güncelleştirmeleri etkin bir şekilde yönetmezseniz, varsayılan davranış (güncelleştirmeleri otomatik olarak uygulamak için) geçerlidir.

> [!NOTE]
> Grup ilkesi değiştirerek, makinenin yeniden başlatılması, sistem tarafından değil yalnızca Kullanıcı tarafından gerçekleştirilebilir. Güncelleştirme Yönetimi, kullanıcıdan el ile etkileşim kurmadan makinenin yeniden başlatılması için haklara sahip değilse, yönetilen makineler kalabilir. Daha fazla bilgi için bkz. [Otomatik Güncelleştirmeler için Grup İlkesi ayarlarını yapılandırma](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Yazılım gereksinimleri:

- Makinenin, özel veya ortak bir güncelleştirme deposuna erişmesi gerekir.
- Güncelleştirme Yönetimi etkileşimde bulunmak için TLS 1,1 veya TLS 1,2 gereklidir.
- Python 2. x yüklendi.

> [!NOTE]
> Linux makinelerin güncelleştirme değerlendirmesi yalnızca belirli bölgelerde desteklenir. Otomasyon hesabı ve Log Analytics çalışma alanı [eşlemeleri tablosuna](../how-to/region-mappings.md#supported-mappings)bakın.

Karma makinelerde, ilk olarak makinenizi [Azure Arc etkin sunucularına](../../azure-arc/servers/overview.md)bağlayarak linux için Log Analytics aracısını yüklemenizi ve ardından Azure ilkesi 'ni kullanarak [Log Analytics aracısını Linux Azure Arc makineler](../../governance/policy/samples/built-in-policies.md#monitoring) yerleşik ilkesine atamanız önerilir. Alternatif olarak, makineleri VM'ler için Azure İzleyici ile izlemeyi planlıyorsanız, bunun yerine [Enable VM'ler için Azure izleyici](../../governance/policy/samples/built-in-initiatives.md#monitoring) girişimi kullanın.

Azure Marketi 'nde bulunan isteğe bağlı Red Hat Enterprise Linux (RHEL) görüntülerinden oluşturulan VM 'Ler, Azure 'da dağıtılan [Red Hat güncelleştirme altyapısına (rhuı)](../../virtual-machines/workloads/redhat/redhat-rhui.md) erişmek için kaydedilir. Diğer tüm Linux dağıtımı, dağıtım tarafından desteklenen yöntemler kullanılarak dağıtımın çevrimiçi dosya deposundan güncelleştirilmeleri gerekir.

## <a name="permissions"></a>İzinler

Güncelleştirme dağıtımları oluşturmak ve yönetmek için belirli izinlere sahip olmanız gerekir. Bu izinler hakkında bilgi edinmek için bkz. [rol tabanlı erişim-güncelleştirme yönetimi](../automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Güncelleştirme Yönetimi bileşenleri

Güncelleştirme Yönetimi, bu bölümde açıklanan kaynakları kullanır. Güncelleştirme Yönetimi etkinleştirdiğinizde bu kaynaklar Otomasyon hesabınıza otomatik olarak eklenir.

### <a name="hybrid-runbook-worker-groups"></a>Karma runbook çalışanı grupları

Güncelleştirme Yönetimi etkinleştirdikten sonra, Log Analytics çalışma alanınıza doğrudan bağlı tüm Windows makineleri, Güncelleştirme Yönetimi destekleyen runbook 'ları destekleyecek bir sistem karma Runbook Worker olarak otomatik olarak yapılandırılır.

Güncelleştirme Yönetimi tarafından yönetilen her bir Windows makinesi, Otomasyon hesabı için bir sistem karma çalışanı grubu olarak karma çalışan grupları bölmesinde listelenir. Gruplar, `Hostname FQDN_GUID` adlandırma kuralını kullanır. Bu grupları hesabınızdaki runbook 'lar ile hedefleyebilirsiniz. Denerseniz, deneme başarısız olur. Bu gruplar yalnızca Güncelleştirme Yönetimi desteklemeye yöneliktir. Karma Runbook Worker olarak yapılandırılmış Windows makinelerin listesini görüntüleme hakkında daha fazla bilgi edinmek için bkz. [karma runbook çalışanlarını görüntüleme](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers).

Güncelleştirme Yönetimi ve karma runbook çalışanı grup üyeliği için aynı hesabı kullanırsanız Otomasyon Runbook 'larını desteklemek için Otomasyon hesabınızdaki bir Kullanıcı karma Runbook Worker grubuna Windows makinesini ekleyebilirsiniz. Bu işlev, karma Runbook Worker 'ın 7.2.12024.0 sürümüne eklenmiştir.

### <a name="management-packs"></a>Yönetim paketleri

Operations Manager yönetim grubunuz [bir Log Analytics çalışma alanına bağlıysa](../../azure-monitor/agents/om-agents.md), aşağıdaki yönetim paketleri Operations Manager yüklenir. Bu yönetim paketleri, doğrudan bağlı Windows makinelerinde Güncelleştirme Yönetimi için de yüklenir. Bu yönetim paketlerini yapılandırmanız veya yönetmeniz gerekmez.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* MP Dağıtımını güncelleştirme

> [!NOTE]
> Günlük verilerini toplamak üzere yönetim grubunda yapılandırılmış aracılarla bir Log Analytics çalışma alanına bağlı Operations Manager 1807 veya 2019 yönetim grubunuz varsa, parametreyi geçersiz kılmanız `IsAutoRegistrationEnabled` ve **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** kuralında true olarak ayarlamanız gerekir.

Yönetim paketlerine yönelik güncelleştirmeler hakkında daha fazla bilgi için bkz. [Azure izleyici günlüklerine bağlanma Operations Manager](../../azure-monitor/agents/om-agents.md).

> [!NOTE]
> Log Analytics aracısıyla makineleri tamamen yönetmek için Güncelleştirme Yönetimi için, Windows için Log Analytics aracısına veya Linux için Log Analytics aracısına güncelleştirmeniz gerekir. Aracıyı güncelleştirme hakkında bilgi edinmek için bkz. [Operations Manager aracısını yükseltme](/system-center/scom/deploy-upgrade-agents). Operations Manager kullanan ortamlarda, System Center Operations Manager 2012 R2 UR 14 veya sonraki bir sürümünü çalıştırıyor olmanız gerekir.

## <a name="data-collection"></a>Veri toplama

### <a name="supported-sources"></a>Desteklenen kaynaklar

Aşağıdaki tabloda Güncelleştirme Yönetimi tarafından desteklenen bağlı kaynaklar açıklanmaktadır:

| Bağlı kaynak | Desteklenir | Description |
| --- | --- | --- |
| Windows aracıları |Yes |Güncelleştirme Yönetimi, Windows aracılarından sistem güncelleştirmeleri hakkında bilgi toplar ve gerekli güncelleştirmelerin yüklemesini başlatır. |
| Linux aracıları |Yes |Güncelleştirme Yönetimi, Linux aracılarından sistem güncelleştirmeleriyle ilgili bilgileri toplar ve ardından desteklenen dağıtımlarda gerekli güncelleştirmelerin yüklemesini başlatır. |
| Operations Manager yönetim grubu |Yes |Güncelleştirme Yönetimi bağlı bir yönetim grubundaki aracılardan sistem güncelleştirmeleri hakkında bilgi toplar.<br/><br/>Operations Manager aracısından Azure Izleyici günlüklerine doğrudan bağlantı gerekli değildir. Veriler, yönetim grubundan Log Analytics çalışma alanına iletilir. |

### <a name="collection-frequency"></a>Toplama sıklığı

Güncelleştirme Yönetimi, aşağıdaki kuralları kullanarak veriler için yönetilen makineleri tarar. Panonun yönetilen makinelerden güncelleştirilmiş verileri görüntülemesi 30 dakika ile 6 saat arasında sürebilir.

* Her Windows makinesi-Güncelleştirme Yönetimi her makine için günde iki kez tarama yapar.

* Her Linux makinesi Güncelleştirme Yönetimi her saat bir tarama yapar.

Güncelleştirme Yönetimi kullanan bir makineye yönelik Azure Izleyici günlüklerinin ortalama veri kullanımı yaklaşık olarak ayda 25 MB 'tır. Bu değer yalnızca bir yaklaşık değerdir ve ortamınıza bağlı olarak değişebilir. Tam kullanımınızın izlenmesini sağlamak için ortamınızı izlemenizi öneririz. Azure Izleyici günlüklerinin veri kullanımını çözümleme hakkında daha fazla bilgi için bkz. [kullanımı ve maliyeti yönetme](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Ağ planlama

Güncelleştirme Yönetimi için gereken bağlantı noktaları, URL 'Ler ve diğer ağ ayrıntıları hakkında ayrıntılı bilgi için [Azure Otomasyonu ağ yapılandırması](../automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration) 'nı denetleyin.

Windows makinelerinde, Windows Update için gereken tüm uç noktalara giden trafiğe de izin vermeniz gerekir. [Http/proxy ile Ilgili sorunlarda](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy), gerekli uç noktaların güncelleştirilmiş bir listesini bulabilirsiniz. Yerel bir [Windows Update sunucunuz](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)varsa, [WSUS anahtarınıza](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)belirtilen sunucuya giden trafiğe de izin vermeniz gerekir.

Red Hat Linux makineleri için, gerekli uç noktalar için [rhuı içerik teslim sunucuları Için IP 'ler](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) bölümüne bakın. Diğer Linux dağıtımları için sağlayıcı belgelerinize bakın.

Karma Runbook Worker için gereken bağlantı noktaları hakkında daha fazla bilgi için bkz. [karma Runbook Worker için güncelleştirme yönetimi adresleri](../automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker).

BT güvenlik ilkeleriniz ağdaki makinelerin internet 'e bağlanmasına izin vermediğinden, bir [Log Analytics ağ geçidi](../../azure-monitor/agents/gateway.md) ayarlayabilir ve ardından makineyi ağ geçidinden Azure Otomasyonu ve Azure izleyici 'ye bağlanacak şekilde yapılandırabilirsiniz.

## <a name="update-classifications"></a>Update classifications

Aşağıdaki tabloda, Güncelleştirme Yönetimi Windows güncelleştirmeleri için desteklediği sınıflandırmalar tanımlanmaktadır.

|Sınıflandırma  |Açıklama  |
|---------|---------|
|Kritik güncelleştirmeler     | Kritik, güvenlikle ilgili olmayan bir hatayı ele alan belirli bir sorun için güncelleştirme.        |
|Güvenlik güncelleştirmeleri     | Ürüne özgü, güvenlikle ilgili bir sorun için bir güncelleştirme.        |
|Güncelleştirme paketleri     | Kolay dağıtım için bir arada paketlenmiş toplu bir düzeltme kümesi.        |
|Özellik paketleri     | Ürün sürümü dışında dağıtılan yeni ürün özellikleri.        |
|Hizmet paketleri     | Bir uygulamaya uygulanan toplu bir düzeltme kümesi.        |
|Tanım güncelleştirmeleri     | Virüs veya diğer tanım dosyalarına yönelik bir güncelleştirme.        |
|Araçlar     | Bir veya daha fazla görevi tamamlamaya yardımcı olan bir yardımcı program veya özellik.        |
|Güncelleştirmeler     | Şu anda yüklü olan bir uygulama veya dosyaya yönelik bir güncelleştirme.        |

Sonraki tabloda, Linux güncelleştirmeleri için desteklenen sınıflandırmalar tanımlanmaktadır.

|Sınıflandırma  |Açıklama  |
|---------|---------|
|Kritik güncelleştirmeler ve güvenlik güncelleştirmeleri     | Belirli bir sorun veya ürüne özgü, güvenlikle ilgili bir sorun için güncelleştirmeler.         |
|Diğer güncelleştirmeler     | Doğası gereği önemli olmayan veya güvenlik güncelleştirmeleri olmayan diğer tüm güncelleştirmeler.        |

>[!NOTE]
>Linux makineleri için güncelleştirme sınıflandırması yalnızca desteklenen Azure genel bulut bölgelerinde kullanıldığında kullanılabilir. Aşağıdaki ulusal bulut bölgelerinde Güncelleştirme Yönetimi kullanılırken Linux güncelleştirmelerinin sınıflandırılması yoktur:
>
>* Azure ABD Kamu
>* Çin 'de 21Vianet
>
> ' Nin sınıflandırılmakta olması yerine, **diğer güncelleştirmeler** kategorisi altında güncelleştirmeler raporlanır.
>
> Güncelleştirme Yönetimi, desteklenen dağıtımlar tarafından yayınlanan verileri, özellikle de yayınlanan [oval](https://oval.mitre.org/) (açık güvenlik açığı ve değerlendirme dili) dosyalarını kullanır. Internet erişimi bu ulusal bulutlardan kısıtlandığı için Güncelleştirme Yönetimi dosyalara erişemez.

Linux için Güncelleştirme Yönetimi, bulutta bulunan önemli güncelleştirmeler **ve güvenlik güncelleştirmelerinin** **yanı sıra, bulutta** veri zenginleştirmesi nedeniyle değerlendirme verilerinin görüntülenmesini sağlar. Düzeltme eki uygulama Güncelleştirme Yönetimi makinede bulunan sınıflandırma verilerine bağımlıdır. Diğer dağıtımlardan farklı olarak, CentOS bu bilgileri RTM sürümünde kullanılamaz. Aşağıdaki komut için güvenlik verilerini döndürecek şekilde yapılandırılmış CentOS makineleriniz varsa, Güncelleştirme Yönetimi sınıflandırmalara göre düzeltme eki uygulanabilir.

```bash
sudo yum -q --security check-update
```

Şu anda yerel sınıflandırmanın etkinleştirilmesi için desteklenen bir yöntem yok-CentOS üzerinde veri kullanılabilirliği. Şu anda, bu özelliği kendi kendine etkinleştirmiş olabilecek müşterilere sınırlı destek verilir.

Red Hat Enterprise sürüm 6 ' da güncelleştirmeleri sınıflandırmak için, yıum-güvenlik eklentisini yüklemeniz gerekir. Red Hat Enterprise Linux 7 ' de, eklenti zaten bir de en fazla bir parçasıdır ve herhangi bir şey yüklemeniz gerekmez. Daha fazla bilgi için aşağıdaki Red hat [Bilgi Bankası makalesine](https://access.redhat.com/solutions/10021)bakın.

Bir Linux makinesinde çalışacak şekilde bir güncelleştirme zamanladığınızda, örneğin yalnızca **güvenlik** sınıflandırmasıyla eşleşen güncelleştirmeleri yükleyecek şekilde yapılandırılmışsa, yüklenen güncelleştirmeler öğesinden farklı olabilir veya bu sınıflandırmayla eşleşen güncelleştirmelerin bir alt kümesidir. Linux makineniz için bekleyen bir işletim sistemi güncelleştirmeleri değerlendirmesi gerçekleştirildiğinde, sınıflandırma için Güncelleştirme Yönetimi tarafından sunulan Linux dışı satıcının sunduğu [açık güvenlik açığı ve değerlendirme dili](https://oval.mitre.org/) (oval) dosyaları kullanılır.

Sınıflandırma, güvenlik sorunları veya güvenlik açıklarını gideren güncelleştirmeler de dahil olmak üzere, Linux güncelleştirmeleri için OVAL dosyalarını temel alan **güvenlik** veya **diğerleri** için yapılır. Ancak güncelleştirme zamanlaması çalıştırıldığında, bu, yüklemek için tum, APT veya ZYPPER gibi uygun paket yöneticisini kullanarak Linux makinesinde yürütülür. Linux 'un Paket Yöneticisi, güncelleştirmeleri sınıflandırmak için farklı bir mekanizmaya sahip olabilir. burada sonuçlar, Güncelleştirme Yönetimi, OVAL dosyalarından alındıklarından farklı olabilir. Makineyi el ile denetlemek ve paket yöneticiniz ile ilgili hangi güncelleştirmelerin güvenlik olduğunu anlamak için bkz. [Linux güncelleştirme dağıtımı sorunlarını giderme](../troubleshoot/update-management.md#updates-linux-installed-different).

## <a name="integrate-update-management-with-configuration-manager"></a>Güncelleştirme Yönetimi Configuration Manager ile tümleştirin

Bilgisayarları, sunucuları ve mobil cihazları yönetmek için Microsoft uç nokta Configuration Manager yatırım yapmış müşteriler, yazılım güncelleştirmelerinin yönetilmesine yardımcı olmak için Configuration Manager kuvvetini ve vadede yararlanır. Güncelleştirme Yönetimi Configuration Manager ile tümleştirme hakkında bilgi edinmek için bkz. [Windows Endpoint Configuration Manager Ile tümleştirme güncelleştirme yönetimi](mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Windows 'da üçüncü taraf güncelleştirmeleri

Güncelleştirme Yönetimi, WSUS veya Windows Update desteklenen Windows sistemlerini güncelleştirmek için yerel olarak yapılandırılmış güncelleştirme deposuna bağımlıdır. [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) gibi araçlar, WSUS ile özel güncelleştirmeleri içeri ve yayımlamanıza olanak sağlar. Bu senaryo, Configuration Manager kullanan makineleri üçüncü taraf yazılımlarla güncelleştirme deposu olarak güncelleştirmesine Güncelleştirme Yönetimi olanak tanır. Updates Publisher 'ı yapılandırma hakkında bilgi edinmek için bkz. [Updates Publisher 'ı yüklemek](/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a>Güncelleştirme Yönetimi’ni etkinleştirme

Güncelleştirme Yönetimi etkinleştirip yönetilecek makineleri seçebileceğiniz yollar şunlardır:

- Yeni veya var olan bir Otomasyon hesabına ve Azure Izleyici Log Analytics çalışma alanına Güncelleştirme Yönetimi dağıtmak için bir Azure [Kaynak Yöneticisi şablonu](enable-from-template.md) kullanma. Yönetilmesi gereken makinelerin kapsamını yapılandırmaz, bu, şablonu kullanmaktan sonra ayrı bir adım olarak gerçekleştirilir.

- Bir veya daha fazla Azure ve Azure dışı makineye yönelik [Otomasyon hesabınızdan](enable-from-automation-account.md) , yay özellikli sunucular da dahil olmak üzere.

- **Enable-AutomationSolution** [runbook](enable-from-runbook.md) yöntemi kullanılıyor.

- Seçili bir [Azure VM](enable-from-vm.md) Için Azure Portal **sanal makineler** sayfasından. Bu senaryo, Linux ve Windows VM 'Leri için kullanılabilir.

- [Birden çok Azure VM](enable-from-portal.md) Için Azure Portal **sanal makineler** sayfasında bunları seçerek.

> [!NOTE]
> Güncelleştirme Yönetimi, Otomasyon hesabınıza bir Log Analytics çalışma alanı bağlamayı gerektirir. Desteklenen bölgelerin kesin listesi için bkz. [Azure çalışma alanı eşlemeleri](../how-to/region-mappings.md). Bölge eşlemeleri, Otomasyon hesabınızdan ayrı bir bölgedeki VM 'Leri yönetme özelliğini etkilemez.

## <a name="next-steps"></a>Sonraki adımlar

* Güncelleştirme Yönetimi çalışma ayrıntıları için bkz. [VM 'leriniz için güncelleştirmeleri yönetme](manage-updates-for-vm.md).

* [Azure Otomasyonu hakkında sık sorulan sorulardan](../automation-faq.md)güncelleştirme yönetimi hakkında sıkça sorulan soruları gözden geçirin.