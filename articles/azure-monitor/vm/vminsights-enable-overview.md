---
title: VM öngörülerini etkinleştir genel bakış
description: VM öngörülerini dağıtmayı ve yapılandırmayı öğrenin. Sistem gereksinimlerini öğrenin.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: bb2e12082b80c397eec27409b1177379a92fdd7d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102634167"
---
# <a name="enable-vm-insights-overview"></a>VM öngörülerini etkinleştir genel bakış

Bu makalede, VM öngörülerinin sistem durumunu ve performansını izlemek için kullanılabilen seçeneklere genel bir bakış sunulmaktadır:

- Azure sanal makineleri 
- Azure sanal makine ölçek kümeleri
- Azure Arc ile bağlantılı karma sanal makineler
- Şirket içi sanal makineler
- Başka bir bulut ortamında barındırılan sanal makineler.  

VM öngörülerini ayarlamak için:

* Azure portal doğrudan menüsünden **Öngörüler** ' i seçerek tek bir Azure sanal makinesini, Azure sanal makine ölçek kümesini veya Azure yay makinesini etkinleştirin.
* Azure Ilkesi 'ni kullanarak birden çok Azure sanal makinesini, Azure sanal makinelerini veya Azure yay makinelerini etkinleştirin. Bu yöntem, mevcut ve yeni VM 'lerde ve ölçek kümelerinde gerekli bağımlılıkların yüklü ve düzgün şekilde yapılandırıldığından emin olmanızı sağlar. Uyumsuz sanal makineler ve ölçek kümeleri raporlanır, bu sayede onları etkinleştirip etkinleştirmemeye karar verebilirsiniz.
* PowerShell kullanarak, belirtilen bir abonelik veya kaynak grubundaki birden çok Azure sanal makinesini, Azure Arc sanal makinelerini, Azure sanal makine ölçek kümelerini veya Azure Arc makinelerini etkinleştirin.
* VM öngörülerini, kurumsal ağınızda veya diğer bulut ortamınızda barındırılan VM 'Leri veya fiziksel bilgisayarları izlemek için etkinleştirin.

## <a name="supported-machines"></a>Desteklenen makineler
VM öngörüleri aşağıdaki makineleri destekler:

- Azure sanal makine
- Azure sanal makine ölçek kümesi
- Azure Arc ile bağlantılı karma sanal makine


## <a name="supported-azure-arc-machines"></a>Desteklenen Azure yay makineleri
Sanal makine öngörüleri, Arc uzantı hizmetinin kullanılabildiği bölgelerde Azure Arc etkin sunucuları için kullanılabilir. Arc aracısının 0,9 veya sonraki bir sürümünü çalıştırıyor olmanız gerekir.

| Bağlı kaynak | Desteklenir | Description |
|:--|:--|:--|
| Windows aracıları | Yes | [Windows aracıların Log Analytics aracı](../agents/log-analytics-agent.md)Ile birlikte bağımlılık aracısına ihtiyacı vardır. Daha fazla bilgi için bkz. [desteklenen işletim sistemleri](../agents/agents-overview.md#supported-operating-systems). |
| Linux aracıları | Yes | Linux aracısının [Log Analytics aracısıyla](../agents/log-analytics-agent.md)birlikte, Linux aracılarının bağımlılık aracısına ihtiyacı vardır. Daha fazla bilgi için bkz. [desteklenen işletim sistemleri](#supported-operating-systems). |
| System Center Operations Manager yönetim grubu | No | |

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

VM öngörüleri, Log Analytics Aracısı ve bağımlılık aracısını destekleyen tüm işletim sistemlerini destekler. Tüm liste için bkz. [Azure izleyici aracılarına genel bakış ](../agents/agents-overview.md#supported-operating-systems) .

> [!IMPORTANT]
> VM öngörüleri Konuk sistem durumu özelliği, genel önizlemede olduğu sürece daha sınırlı işletim sistemi desteğine sahiptir. Ayrıntılı bir liste için bkz. [VM Insights Konuk durumunu etkinleştirme (Önizleme)](../vm/vminsights-health-enable.md) .

### <a name="linux-considerations"></a>Linux konuları
VM öngörülerini destekleyen bağımlılık aracısının Linux desteğiyle ilgili konular listesine bakın:

- Yalnızca varsayılan ve SMP Linux çekirdek sürümleri desteklenir.
- Fiziksel adres uzantısı (PAE) ve Xen gibi standart olmayan çekirdek sürümleri, hiçbir Linux dağıtımı için desteklenmez. Örneğin, *2.6.16.21-0.8-Xen* yayın dizesiyle bir sistem desteklenmez.
- Standart kernels 'lerin yeniden derlemesi dahil olmak üzere özel kernels desteklenmez.
- Sürüm 9,4 ' den başka bir deyişle, harita özelliği desteklenmez ve performans özelliği yalnızca Azure Izleyici menüsünden kullanılabilir. Doğrudan Azure VM 'nin sol bölmesinden kullanılamaz.
- CentOSPlus Kernel destekleniyor.

Linux çekirdeğinin Spectre ve Meltters güvenlik açıkları için düzeltme eki uygulanmış olması gerekir. Daha fazla ayrıntı için lütfen Linux dağıtım satıcınıza başvurun. Spectre/Meltazaltma azaltıldığında kullanılabilir olup olmadığını denetlemek için aşağıdaki komutu çalıştırın:

```
$ grep . /sys/devices/system/cpu/vulnerabilities/*
```

Bu komutun çıktısı aşağıdakine benzer şekilde görünür ve bir makinenin soruna karşı savunmasız olup olmadığını belirtir. Bu dosyalar eksikse, makine Düzeltme Eki.

```
/sys/devices/system/cpu/vulnerabilities/meltdown:Mitigation: PTI
/sys/devices/system/cpu/vulnerabilities/spectre_v1:Vulnerable
/sys/devices/system/cpu/vulnerabilities/spectre_v2:Vulnerable: Minimal generic ASM retpoline
```


## <a name="log-analytics-workspace"></a>Log Analytics çalışma alanı
VM öngörüleri bir Log Analytics çalışma alanı gerektirir. Bu çalışma alanının ayrıntıları ve gereksinimleri için bkz. [VM öngörüleri için Log Analytics çalışma alanını yapılandırma](vminsights-configure-workspace.md) .
## <a name="agents"></a>Aracılar
VM öngörüleri, izlenecek her bir sanal makineye veya sanal makine ölçek kümesine aşağıdaki iki aracının yüklenmesini gerektirir. Kaynağı eklemek için, bu aracıları yükleyip çalışma alanına bağlayın.  Bu aracıların ağ gereksinimleri için [ağ gereksinimlerine](../agents/log-analytics-agent.md#network-requirements) bakın.

- [Log Analytics Aracısı](../agents/log-analytics-agent.md). Sanal makineden veya sanal makine ölçek kümesinden olayları ve performans verilerini toplar ve Log Analytics çalışma alanına gönderir. Azure kaynaklarında Log Analytics aracısına yönelik dağıtım yöntemleri [Windows](../../virtual-machines/extensions/oms-windows.md) ve [Linux](../../virtual-machines/extensions/oms-linux.md)için VM uzantısını kullanır.
- Bağımlılık Aracısı. Sanal makinede çalışan işlemler ve [VM öngörülerinin harita özelliği](../vm/vminsights-maps.md)tarafından kullanılan dış işlem bağımlılıkları hakkında bulunan verileri toplar. Bağımlılık Aracısı, verilerini Azure Izleyici 'ye teslim etmek için Log Analytics aracısına bağımlıdır. Azure kaynaklarında bağımlılık aracısına yönelik dağıtım yöntemleri [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) ve [Linux](../../virtual-machines/extensions/agent-dependency-linux.md)için VM uzantısını kullanır.

> [!NOTE]
> Log Analytics Aracısı, System Center Operations Manager tarafından kullanılan aracıdır. VM öngörüleri, doğrudan bağlanılarsa Operations Manager tarafından izlenen aracıları izleyebilir ve bunlara bağımlılık aracısını yüklersiniz. Bir [Yönetim grubu bağlantısı](../tform/../agents/om-agents.md) aracılığıyla Azure izleyici 'ye bağlı aracılar VM öngörüleri tarafından izlenemez.

Aşağıda, bu aracıları dağıtmaya yönelik birden çok yöntem verilmiştir. 

| Yöntem | Açıklama |
|:---|:---|
| [Azure portalı](../vm/vminsights-enable-portal.md) | Her iki aracıyı tek bir sanal makineye, sanal makine ölçek kümesine veya Azure Arc ile bağlantılı karma sanal makinelere kurun. |
| [Resource Manager şablonları](../vm/vminsights-enable-resource-manager.md) | CLı ve PowerShell dahil Kaynak Yöneticisi şablonu dağıtmak için desteklenen yöntemlerden herhangi birini kullanarak her iki aracıyı de yükler. |
| [Azure İlkesi](../vm/vminsights-enable-policy.md) | Bir sanal makine veya sanal makine ölçek kümesi oluşturulduğunda aracıları otomatik olarak yüklemek için Azure Ilke girişimi atayın. |
| [El ile yüklemesi](../vm/vminsights-enable-hybrid.md) | Veri merkezinize veya diğer bulut ortamlarınıza dahil olmak üzere Azure dışında barındırılan bilgisayarlarda, aracıları Konuk işletim sistemine yükler. |


## <a name="network-requirements"></a>Ağ gereksinimleri

- Log Analytics aracısına yönelik ağ gereksinimleri için [ağ gereksinimlerine](../agents/log-analytics-agent.md#network-requirements) bakın.
- Bağımlılık Aracısı, sanal makineden 169.254.169.254 adresine bağlantı gerektirir. Bu, Azure meta veri hizmeti uç noktasıdır. Güvenlik Duvarı ayarlarının bu uç noktaya bağlantılara izin verildiğinden emin olun.


## <a name="management-packs"></a>Yönetim paketleri
VM öngörüleri için bir Log Analytics çalışma alanı yapılandırıldığında, bu çalışma alanına bağlı tüm Windows bilgisayarlarına iki yönetim paketi iletilir. Yönetim paketleri *Microsoft. ıntelligencepacks. ApplicationDependencyMonitor* ve *Microsoft. ıntelligencepacks. Vminsıghts* olarak adlandırılır ve *%ProgramFiles%\Microsoft Monitoring Fılest\sistem sağlığı hizmeti State\Management paketlerine* yazılır. 

*Applicationdependencymonitor* yönetim paketi tarafından kullanılan veri kaynağı **% Program Files%\Microsoft Monitoring Fılest\sistem sağlığı hizmeti state\resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. *Vminsıghts* yönetim paketi tarafından kullanılan veri kaynağı *% Program Files%\Microsoft Monitoring Fılest\sistem sağlığı hizmeti state\resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Tanılama ve kullanım verileri

Microsoft, Azure Izleyici hizmetini kullanımınız aracılığıyla kullanım ve performans verilerini otomatik olarak toplar. Microsoft bu verileri hizmetin kalitesini, güvenliğini ve bütünlüğünü geliştirmek için kullanır. 

Doğru ve verimli sorun giderme özellikleri sağlamak için, Map özelliği yazılımınızın yapılandırmasıyla ilgili verileri içerir. Veriler, işletim sistemi ve sürümü, IP adresi, DNS adı ve iş istasyonu adı gibi bilgiler sağlar. Microsoft ad, adres veya diğer iletişim bilgileri toplamaz.

Veri toplama ve kullanım hakkında daha fazla bilgi için [Microsoft Online Services gizlilik bildirimi](https://go.microsoft.com/fwlink/?LinkId=512132)' ne bakın.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Sonraki adımlar

Performans izleme özelliğini kullanmayı öğrenmek için bkz. [VM Insights performansını görüntüleme](../vm/vminsights-performance.md). Bulunan uygulama bağımlılıklarını görüntülemek için bkz. [VM Insights haritasını görüntüleme](../vm/vminsights-maps.md).
