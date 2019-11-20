---
title: MABS & System Center DPM destek matrisi
description: Bu makalede, şirket içi ve Azure VM kaynaklarını yedeklemek için Microsoft Azure Backup sunucusu (MABS) veya System Center DPM kullandığınızda Azure Backup desteği özetlenmektedir.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 2bec2818eaabaa2d2d74ab7181db0eabcba092ec
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172039"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Microsoft Azure Backup Server veya System Center DPM ile yedekleme için destek matrisi

[Azure Backup hizmetini](backup-overview.md) şirket içi makineleri ve iş yüklerini ve Azure sanal makinelerini (VM) yedeklemek için kullanabilirsiniz. Bu makalede, Microsoft Azure Backup Server (MABS) veya System Center Data Protection Manager (DPM) ve Azure Backup kullanarak makineleri yedeklemeye yönelik destek ayarları ve sınırlamaları özetlenmektedir.

## <a name="about-dpmmabs"></a>DPM/MABS hakkında

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) , kurumsal makinelerin ve verilerin yedeklenmesini ve kurtarılmasını yapılandıran, kolaylaştıran ve yöneten bir kurumsal çözümdür. [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) ürün paketi 'nin bir parçasıdır.

MABS, şirket içi fiziksel sunucuları, VM 'Leri ve üzerinde çalışan uygulamaları yedeklemek için kullanılabilen bir sunucu ürünüdür.

MABS, System Center DPM 'yi temel alır ve birkaç farklılık ile benzer işlevler sağlar:

- MABS çalıştırmak için herhangi bir System Center lisansı gerekmez.
- Azure, hem MABS hem de DPM için uzun süreli yedekleme depolama alanı sağlar. Ayrıca DPM, banttaki uzun süreli depolamaya yönelik verileri yedeklemenizi sağlar. MABS bu işlevselliği sağlamaz.
- Birincil DPM sunucusunu ikincil bir DPM sunucusuyla yedekleyebilirsiniz. İkincil sunucu, birincil sunucu veritabanını ve birincil sunucuda depolanan veri kaynağı çoğaltmalarını korur. Birincil sunucu başarısız olursa, birincil sunucu yeniden kullanılabilir olana kadar ikincil sunucu, birincil sunucu tarafından korunan iş yüklerini korumaya devam edebilir.  MABS bu işlevselliği sağlamaz.

MABS 'yi [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=57520)' nden indirebilirsiniz. Şirket içinde veya bir Azure sanal makinesinde çalıştırılabilir.

DPM ve MABS, çok çeşitli uygulamaları ve sunucu ve istemci işletim sistemlerini yedeklemeyi destekler. Birden çok yedekleme senaryosu sağlarlar:

- Makine düzeyinde sistem durumu veya çıplak yedekleme ile yedekleyebilirsiniz.
- Belirli birimleri, paylaşımları, klasörleri ve dosyaları yedekleyebilirsiniz.
- Belirli uygulamaları, en iyileştirilmiş uygulama duyarlı ayarları kullanarak yedekleyebilirsiniz.

## <a name="dpmmabs-backup"></a>DPM/MABS yedeklemesi

DPM/MABS ve Azure Backup kullanarak yedekleme aşağıdaki gibi çalışmaktadır:

1. Yedeklenecek her makineye DPM/MABS koruma Aracısı yüklenir.
1. Makineler ve uygulamalar, DPM/MABS üzerinde yerel depolamaya yedeklenir.
1. Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı DPM sunucusuna/MABS 'ye yüklenir.
1. MARS Aracısı, Azure Backup kullanarak DPM/MABS disklerini Azure 'daki bir yedekleme kurtarma hizmetleri kasasına yedekler.

Daha fazla bilgi için:

- MABS mimarisi hakkında [daha fazla bilgi edinin](backup-architecture.md#architecture-back-up-to-dpmmabs) .
- MARS Aracısı için [nelerin desteklendiğini gözden geçirin](backup-support-matrix-mars-agent.md) .

## <a name="supported-scenarios"></a>Desteklenen senaryolar

**Senaryo** | **Aracı** | **Konum**
--- | --- | ---
**Şirket içi makineleri/iş yüklerini yedekleme** | DPM/MABS koruma Aracısı, yedeklemek istediğiniz makinelerde çalışır.<br/><br/> DPM/MABS sunucusundaki MARS Aracısı.<br/> Bu özelliği etkinleştirmek için gereken Microsoft Azure Kurtarma Hizmetleri aracısının veya Azure Backup aracısının en düşük sürümü 2.0.8719.0 ' dir.  | DPM/MABS 'nin şirket içinde çalışıyor olması gerekir.

## <a name="supported-deployments"></a>Desteklenen dağıtımlar

DPM/MABS, aşağıdaki tabloda özetlenen şekilde dağıtılabilir.

**Dağıtım** | **Destek** | **Ayrıntılar**
--- | --- | ---
**Şirket içinde dağıtıldı** | Fiziksel sunucu<br/><br/>Hyper-V VM<br/><br/> VMware VM | DPM/MABS bir VMware VM olarak yüklenirse, yalnızca bu VM 'lerde çalışan VMware VM 'lerini ve iş yüklerini yedekler.
**Azure Stack VM olarak dağıtıldı** | Yalnızca MABS | DPM, Azure Stack VM 'Leri yedeklemek için kullanılamaz.
**Azure VM olarak dağıtıldı** | Bu VM 'lerde çalışan Azure sanal makinelerini ve iş yüklerini korur | Azure 'da çalışan DPM/MABS, şirket içi makineleri yedekleyebilir.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Desteklenen MABS ve DPM işletim sistemleri

Azure Backup, aşağıdaki işletim sistemlerinden herhangi birini çalıştıran DPM/MABS örneklerini yedekleyebilir. İşletim sistemleri en son hizmet paketlerini ve güncelleştirmelerini çalıştırıyor olmalıdır.

**Senaryo** | **DPM/MABS**
--- | ---
**Azure VM 'de MABS** | Windows Server 2012 R2.<br/><br/> Windows 2016 Datacenter.<br/><br/> Windows 2019 Datacenter.<br/><br/> Market 'ten bir görüntüyle başlamanız önerilir.<br/><br/> İki çekirdek ve 3,5 GB RAM ile en az a2 standart.
**Azure VM 'de DPM** | Güncelleştirme 3 veya sonraki sürümleri ile System Center 2012 R2.<br/><br/> [System Center için gereken](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)Windows işletim sistemi.<br/><br/> Market 'ten bir görüntüyle başlamanız önerilir.<br/><br/> İki çekirdek ve 3,5 GB RAM ile en az a2 standart.
**Şirket içi MABS** | Desteklenen 64 bit işletim sistemleri:<br/><br/> MABS v3 ve üzeri: Windows Server 2019 (Standard, Datacenter, Essentials). <br/><br/> MABS v2 ve üzeri: Windows Server 2016 (Standard, Datacenter, Essentials).<br/><br/> Tüm MABS sürümleri: Windows Server 2012 R2.<br/><br/>Tüm MABS sürümleri: Windows Storage Server 2012 R2.
**Şirket içi DPM** | Fiziksel sunucu/Hyper-V VM: System Center 2012 SP1 veya üzeri.<br/><br/> VMware VM: güncelleştirme 5 veya sonraki sürümleri ile System Center 2012 R2.

## <a name="management-support"></a>Yönetim desteği

**Konuda** | **Ayrıntılar**
--- | ---
**Yükleme** | Tek amaçlı bir makineye DPM/MABS 'yi yükler.<br/><br/> DPM/MABS 'yi bir etki alanı denetleyicisine, uygulama sunucusu rolü yüklemesine sahip bir makineye, Microsoft Exchange Server veya System Center Operations Manager çalıştıran bir makineye veya bir küme düğümüne yüklemeyin.<br/><br/> [Tüm DPM sistem gereksinimlerini gözden geçirin](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Etki alanı** | DPM/MABS bir etki alanına katılmalıdır. Önce yüklemeyi, sonra DPM/MABS 'i bir etki alanına katın. Dağıtımdan sonra DPM/MABS 'yi yeni bir etki alanına taşıma desteklenmiyor.
**Depolama** | Modern yedekleme depolaması (MB), DPM 2016/MABS v2 ve sonrasında desteklenir. MABS v1 için kullanılamaz.
**MABS yükseltmesi** | Mabs v3 'i doğrudan yükleyebilir veya mabs v2 'den MABS v3 'e yükseltebilirsiniz. [Daha fazla bilgi edinin](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**MABS taşınıyor** | MB kullanıyorsanız, depolama alanını korurken MABS 'i yeni bir sunucuya taşımak desteklenir.<br/><br/> Sunucu orijinalde aynı ada sahip olmalıdır. Aynı depolama havuzunu korumak istiyorsanız adı değiştiremezsiniz ve veri kurtarma noktalarını depolamak için aynı MABS veritabanını kullanabilirsiniz.<br/><br/> Geri yüklemeniz gerekeceğinden, MABS veritabanının yedeğine ihtiyacınız olacaktır.

## <a name="mabs-support-on-azure-stack"></a>Azure Stack için MABS desteği

Azure Stack sanal makinelerin ve iş yüklerinin yedeklemesini tek bir konumdan yönetebilmeniz için, MABS 'yi bir Azure Stack VM üzerinde dağıtabilirsiniz.

**Bileşen** | **Ayrıntılar**
--- | ---
**Azure Stack VM 'de MABS** | En az a2. Azure Marketi 'nden bir Windows Server 2012 R2 veya Windows Server 2016 görüntüsü ile başlamanız önerilir.<br/><br/> MABS sanal makinesine başka bir şey yüklemeyin.
**MABS depolaması** | MABS sanal makinesi için ayrı bir depolama hesabı kullanın. MABS üzerinde çalışan MARS aracısının bir önbellek konumu için geçici depolama ve buluttan geri yüklenen verileri tutması gerekir.
**MABS depolama havuzu** | MABS depolama havuzunun boyutu, MABS sanal makinesine bağlı disklerin sayısına ve boyutuna göre belirlenir. Her Azure Stack VM boyutunun en fazla disk sayısı vardır. Örneğin, a2 dört diskdir.
**MABS bekletme** | Beş günden uzun bir süre boyunca yerel MABS disklerinde yedeklenen verileri korumaz.
**MABS ölçeği yukarı** | Dağıtımınızı ölçeklendirmek için, MABS VM 'sinin boyutunu artırabilirsiniz. Örneğin, bir ile D serisi arasında geçiş yapabilirsiniz.<br/><br/> Ayrıca, Azure 'a yedekleme ile verileri düzenli olarak boşalttığınızdan da emin olabilirsiniz. Gerekirse, ek MABS sunucuları dağıtabilirsiniz.
**MABS üzerinde .NET Framework** | MABS VM 'sinin .NET Framework 3,3 SP1 veya sonraki bir sürümü yüklü olması gerekir.
**MABS etki alanı** | MABS VM 'nin bir etki alanına katılması gerekir. Yönetici ayrıcalıklarına sahip bir etki alanı kullanıcısının VM 'ye MABS yüklemesi gerekir.
**VM veri yedeklemesini Azure Stack** | Dosyaları, klasörleri ve uygulamaları yedekleyebilirsiniz.
**Desteklenen yedekleme** | Bu işletim sistemleri yedeklemek istediğiniz VM 'Ler için desteklenir:<br/><br/> Windows Server yarı yıllık kanal (Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Azure Stack VM 'Ler için SQL Server desteği** | Yedekleme SQL Server 2016, SQL Server 2014 SQL Server 2012 SP1.<br/><br/> Veritabanını yedekleme ve kurtarma.
**Azure Stack VM 'Ler için SharePoint desteği** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Bir grup, veritabanı, ön uç ve Web sunucusunu yedekleyin ve kurtarın.
**Yedeklenen VM 'Ler için ağ gereksinimleri** | Azure Stack iş yükündeki tüm sanal makineler aynı sanal ağa ait ve aynı aboneliğe ait olmalıdır.

## <a name="dpmmabs-networking-support"></a>DPM/MABS ağ desteği

### <a name="url-access"></a>URL erişimi

DPM sunucusu/MABS 'nin bu URL 'Lere erişmesi gerekir:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Azure Backup DPM/MABS bağlantısı

Yedeklemelerin düzgün çalışması için Azure Backup hizmetine bağlantı gereklidir ve Azure aboneliği etkin olmalıdır. Aşağıdaki tabloda, bu iki şey gerçekleşmezseniz davranış gösterilmektedir.

**MABS 'ten Azure 'a** | **Abonelik** | **Yedekleme/geri yükleme**
--- | --- | ---
Bağlı | Etkin | DPM/MABS diskine yedekleme.<br/><br/> Azure 'a yedekleyin.<br/><br/> Diskten geri yükle.<br/><br/> Azure 'dan geri yükleyin.
Bağlı | Süre dolma/sağlaması kaldırılmış | Diske veya Azure 'a yedekleme yok.<br/><br/> Aboneliğin geçerliliği dolmuşsa diskten veya Azure 'dan geri yükleme yapabilirsiniz.<br/><br/> Abonelik kullanımdan bulunursa diskten veya Azure 'dan geri yükleme yapamazsınız. Azure kurtarma noktaları silinir.
15 günden fazla bağlantı yok | Etkin | Diske veya Azure 'a yedekleme yok.<br/><br/> Diskten veya Azure 'dan geri yükleme yapabilirsiniz.
15 günden fazla bağlantı yok | Süre dolma/sağlaması kaldırılmış | Diske veya Azure 'a yedekleme yok.<br/><br/> Aboneliğin geçerliliği dolmuşsa diskten veya Azure 'dan geri yükleme yapabilirsiniz.<br/><br/> Abonelik kullanımdan bulunursa diskten veya Azure 'dan geri yükleme yapamazsınız. Azure kurtarma noktaları silinir.

## <a name="dpmmabs-storage-support"></a>DPM/MABS depolama desteği

DPM/MABS 'e yedeklenen veriler yerel disk depolama alanında depolanır.

**Depolama** | **Ayrıntılar**
--- | ---
**MB** | Modern yedekleme depolaması (MB), DPM 2016/MABS v2 ve sonrasında desteklenir. MABS v1 için kullanılamaz.
**Azure VM 'de MABS depolaması** | Veriler, DPM/MABS sanal makinesine bağlı olan ve DPM/MABS üzerinde yönetilen Azure disklerinde depolanır. DPM/MABS depolama havuzu için kullanılabilecek disk sayısı, VM 'nin boyutuyla sınırlıdır.<br/><br/> A2 VM: 4 disk; A3 VM: 8 disk; A4 VM: her disk için en fazla 1 TB boyutunda 16 disk. Bu, kullanılabilir toplam yedekleme depolama havuzunu belirler.<br/><br/> Yedekleyebileceğiniz veri miktarı, eklenen disklerin sayısına ve boyutuna bağlıdır.
**Azure VM 'de MABS veri saklama** | Verileri DPM/MABS Azure diskinde bir gün bekletmenizi ve DPM/MABS 'ten daha uzun bekletme için kasaya yedeklemenizi öneririz. Bu sayede, daha büyük miktarda veriyi Azure Backup devrederleyerek koruyabilirsiniz.

### <a name="modern-backup-storage-mbs"></a>Modern yedekleme depolama alanı (MB)

DPM 2016/MABS v2 'den (Windows Server 2016 ' de çalışan) ve sonraki sürümlerde modern yedekleme depolama (MB) özelliğinden yararlanabilirsiniz.

- MB yedeklemeler dayanıklı bir dosya sistemi (ReFS) diskinde depolanır.
- MB, daha hızlı yedekleme ve depolama alanının daha verimli kullanımı için ReFS blok kopyalamayı kullanır.
- Yerel DPM/MABS depolama havuzuna birim eklediğinizde, bunları sürücü harfleriyle yapılandırırsınız. Daha sonra iş yükü depolama alanını farklı birimlerde yapılandırabilirsiniz.
- Verileri DPM/MABS 'e yedeklemek için koruma grupları oluşturduğunuzda, kullanmak istediğiniz sürücüyü seçersiniz. Örneğin, SQL veya diğer yüksek ıOPS iş yükleri için yedeklemeleri yüksek performanslı bir sürücüde saklayabilir ve daha düşük bir performans sürücüsünde daha az sıklıkta yedeklenen iş yüklerini depokalabilirsiniz.

## <a name="supported-backups-to-mabs"></a>MABS için desteklenen yedeklemeler

Aşağıdaki tabloda, şirket içi makinelerden ve Azure VM 'lerinden MABS 'e nelerin yedeklenebileceği özetlenmektedir.

**Backup** | **Sürümleri** | **MABS** | **Ayrıntılar** |
--- | --- | --- | --- |
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 bit) | MABS v3, v2 | Şirket içi. | Birim/paylaşma/klasör/dosya.<br/><br/> Yinelenenleri kaldırılmış birimler destekleniyor.<br/><br/> Birimler en az 1 GB ve NTFS olmalıdır. |
**Windows Server 2016 (Datacenter, Standard, nano değil)**<br/><br/> 64/32 bit | MABS v3, v2 | Şirket içi/Azure VM.| Birim/paylaşma/klasör/dosya; sistem durumu/çıplak.<br/><br/> Yinelenenleri kaldırılmış birimler destekleniyor. |
**Windows Server 2012 R2 (Datacenter ve Standard)**<br/><br/> 64/32 bit | MABS v3, v2 | Şirket içi/Azure VM. | **Şirket içi koruma**: birim/paylaşma/klasör/dosya; sistem durumu/çıplak.<br/><br/> **Azure VMprotection**: birim/paylaşma/klasör/dosya.<br/><br/> Yinelenenleri kaldırılmış birimler destekleniyor. |
**Windows Server 2012 SP1 (Datacenter ve Standard)**<br/><br/> 64/32 bit | MABS v3, v2 <br/><br/> [Windows Management Framework 4,0](https://www.microsoft.com/download/details.aspx?id=40855) yüklü olmalıdır. | Şirket içi/Azure VM. | **Şirket içi koruma**: birim/paylaşma/klasör/dosya; sistem durumu/çıplak.<br/><br/> **Azure VM koruması**: birim/paylaşma/klasör/dosya.<br/><br/> Yinelenenleri kaldırılmış birimler destekleniyor. |
**SP1 ile Windows 2008 R2 (Standard ve Enterprise)**<br/><br/> 64/32 bit | MABS v3, v2 tarafından desteklenir.<br/><br/> [Windows Management Framework 4,0](https://www.microsoft.com/download/details.aspx?id=40855) yüklü olmalıdır. | Şirket içi/Azure VM. |   **Şirket içi koruma**: birim/paylaşma/klasör/dosya; sistem durumu/çıplak.<br/><br/> **Azure VM koruması**: birim/paylaşma/klasör/dosya.<br/><br/> Yinelenenleri kaldırılmış birimler destekleniyor. |
**Windows 2008 R2 (Standard ve Enterprise)**<br/><br/> 64/32 bit | MABS v3 için, v2 işletim sisteminin SP1 çalıştırması gerekir. | Şirket içi/Azure VM. | **Şirket içi koruma**: birim/paylaşma/klasör/dosya; sistem durumu/çıplak.<br/><br/> **Azure VM koruması**: birim/paylaşma/klasör/dosya.<br/><br/> Yinelenenleri kaldırılmış birimler destekleniyor. |
**SP2 ile Windows Server 2008**<br/><br/> 64/32 bit | MABS v3, v2 | Mabs v2, MABS bir VMware sanal makinesi olarak dağıtıldığında v3 desteklenir.<br/><br/> Azure VM 'de çalışan MABS 'ler için desteklenmez. | Birim/paylaşma/klasör/dosya; sistem durumu/çıplak. |
**Windows Storage Server 2008** | MABS v3, v2 | Şirket içi fiziksel sunucu/Hyper-V VM olarak MABS. <br/><br/> Azure VM 'de çalışan MABS 'ler için desteklenmez. | Birim/paylaşma/klasör/dosya; sistem durumu/çıplak.
**SQL Server 2017** | MABS v3 | Şirket içi/Azure VM.| SQL Server veritabanını yedekleyin.<br/><br/> SQL Server küme yedeklemesi destekleniyor.<br/><br/>CSV 'lerde depolanan veritabanları desteklenmez. |
**SP1 ile SQL Server 2016/2016** | MABS v3, v2 | Şirket içi/Azure VM.| SQL Server veritabanını yedekleyin.<br/><br/> SQL Server küme yedeklemesi destekleniyor.<br/><br/>CSV 'lerde depolanan veritabanları desteklenmez. |
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2 | Şirket içi/Azure VM.| SQL Server veritabanını yedekleyin.<br/><br/> SQL Server küme yedeklemesi destekleniyor.<br/><br/>CSV 'lerde depolanan veritabanları desteklenmez. |
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2 | Şirket içi. | Tek başına Exchange Server 'ı, bir DAG altındaki veritabanını yedekleyin.<br/><br/> Posta kutusu, DAG altındaki posta kutusu veritabanı kurtarma.<br/><br/> ReFS desteklenmiyor.<br/><br/> Paylaşılan olmayan disk kümelerini yedekleyin.<br/><br/> Sürekli çoğaltma için yapılandırılmış Exchange Server 'ı yedekleyin. |
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | MABS v3, v2 | Şirket içi/Azure VM. | Sunucu yedekleme, ön uç Web sunucusu.<br/><br/> Grup, veritabanı, Web uygulaması, dosya veya liste öğesi, SharePoint arama, ön uç Web sunucusu kurtarma.<br/><br/> İçerik veritabanları için SQL Server AlwaysOn kullanarak bir grubu yedekleyemiyoruz. |
**Windows Server 2016 üzerinde Hyper-V**<br/><br/> **Windows Server 2008 R2 (SP1 ile)** | MABS v3, v2 | Şirket içi. | **Hyper-V konağındaki Mabs Aracısı**: tüm VM 'leri ve konak verileri dosyalarını yedekleyin. Yerel depolama ile VM 'leri yedekleme, CSV depolama ile kümedeki VM 'Ler, SMB dosya sunucusu depolama ile VM 'Ler.<br/><br/> **Konuk VM 'de Mabs Aracısı**: VM 'de çalışan iş yüklerini yedekleyin. CSV.<br/><br/> **Kurtarma**: VM, VHD/birim/klasör/dosya kurtarma için VM, öğe düzeyinde kurtarma.<br/><br/> **Linux VM 'leri**: Hyper-V Windows Server 2012 R2 ve sonraki sürümlerde çalışırken yedekleme. Linux VM 'Leri için kurtarma, tüm makine içindir. |
**VMware VM 'Leri: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2 | Şirket içi. | CSVs, NFS ve SAN depolamada VMware VM 'lerini yedekleyin.<br/><br/> Tüm VM 'yi kurtarın.<br/><br/> Windows/Linux yedeklemesi.<br/><br/> Yalnızca Windows VM 'lerinin klasör/dosyalarını öğe düzeyinde kurtarma.<br/><br/> VMware vApps desteklenmez.<br/><br/> Linux VM 'Leri için kurtarma, tüm makine içindir. |

## <a name="supported-backups-to-dpm"></a>DPM için desteklenen yedeklemeler

Aşağıdaki tabloda, şirket içi makineler ve Azure VM 'lerinden DPM 'ye ne yedeklenebileceği özetlenmektedir.

**Backup** | **DPM** | **Ayrıntılar**
--- | --- | ---
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 bit) | Yalnızca şirket içi.<br/><br/> DPM 2012 R2 ile Windows 10 ' u yedeklemek için [güncelleştirme 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager)' i yüklemenizi öneririz. | Birim/paylaşma/klasör/dosya.<br/><br/> Yinelenenleri kaldırılmış birimler destekleniyor.<br/><br/> Birimler en az 1 GB ve NTFS olmalıdır.
**Windows Server 2016 (Datacenter, Standard, nano değil)**<br/><br/> 64/32 bit | Şirket içi/Azure VM.<br/><br/> Yalnızca DPM 2016.| Birim/paylaşma/klasör/dosya; sistem durumu/çıplak.<br/><br/> Yinelenenleri kaldırılmış birimler destekleniyor.
**Windows Server 2012 R2 (Datacenter ve Standard)**<br/><br/> 64/32 bit | Şirket içi/Azure VM. | **Şirket içi koruma**: birim/paylaşma/klasör/dosya; sistem durumu/çıplak.<br/><br/> **Azure VM koruması**: birim/paylaşma/klasör/dosya.<br/><br/> Yinelenenleri kaldırılmış birimler DPM 2012 R2 ve üzeri sürümlerde desteklenir.
**Windows Server 2012 SP1 (Datacenter ve Standard)**<br/><br/> 64/32 bit | Şirket içi/Azure VM. | **Şirket içi koruma**: birim/paylaşma/klasör/dosya; sistem durumu/çıplak.<br/><br/> **Azure VM koruması**: birim/paylaşma/klasör/dosya.<br/><br/> Yinelenenleri kaldırılmış birimler DPM 2012 R2 ve üzeri sürümlerde desteklenir.
**SP1 ile Windows 2008 R2 (Standard ve Enterprise)**<br/><br/> 64/32 bit | Şirket içi/Azure VM.<br/><br/> [Windows Management Framework 4,0](https://www.microsoft.com/download/details.aspx?id=40855) yüklü olmalıdır. |   **Şirket içi koruma**: birim/paylaşma/klasör/dosya; sistem durumu/çıplak.<br/><br/> **Azure VM koruması**: birim/paylaşma/klasör/dosya.
**Windows 2008 R2 (Standard ve Enterprise)**<br/><br/> 64/32 bit | Şirket içi.<br/><br/> DPM, VMware VM olarak yüklenemez.<br/><br/> Azure VM üzerinde çalışan DPM desteklenmez. | **Şirket içi koruma**: birim/paylaşma/klasör/dosya; sistem durumu/çıplak.
**SP2 ile Windows Server 2008**<br/><br/> 64/32 bit | Yalnızca şirket içi.<br/><br/> DPM, VMware VM olarak çalışırken desteklenir. Fiziksel sunucu veya Hyper-V VM 'si olarak çalıştırmak desteklenmez. | Birim/paylaşma/klasör/dosya; sistem durumu/çıplak.
**Windows Storage Server 2008** | Fiziksel sunucu veya Hyper-V VM olarak çalışan DPM şirket içi. | Birim/paylaşma/klasör/dosya; sistem durumu/çıplak.
**SQL Server 2017** | DPM SAC; DPM 2016 güncelleştirme paketi 5 veya üstünü çalıştırıyor.<br/><br/> Şirket içi/Azure VM.| SQL Server veritabanını yedekleyin.<br/><br/> SQL Server küme yedeklemesi destekleniyor.<br/><br/>CSV 'lerde depolanan veritabanları desteklenmez.
**SP1 ile SQL Server 2016** | DPM 2012 R2 için desteklenmez; DPM SAC için desteklenir, güncelleştirme paketi 4 veya üstünü çalıştıran DPM 2016.<br/><br/> Şirket içi/Azure VM.| SQL Server veritabanını yedekleyin.<br/><br/> SQL Server küme yedeklemesi destekleniyor.<br/><br/>CSV 'lerde depolanan veritabanları desteklenmez.
**SQL Server 2016** | DPM 2012 R2 için desteklenmez. Güncelleştirme paketi 2 ve sonrasında DPM SAC, DPM 2016 için desteklenir.<br/><br/> Şirket içi/Azure VM.| SQL Server veritabanını yedekleyin.<br/><br/> SQL Server küme yedeklemesi destekleniyor.<br/><br/>CSV 'lerde depolanan veritabanları desteklenmez.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | 2014 ve üzeri güncelleştirme paketi çalıştıran DPM 2012 R2 ile SQL Server.<br/><br/> Şirket içi/Azure VM.| SQL Server veritabanını yedekleyin.<br/><br/> SQL Server küme yedeklemesi destekleniyor.<br/><br/>CSV 'lerde depolanan veritabanları desteklenmez.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Exchange 2016 için DPM 2012 R2 güncelleştirme paketi 9 veya üzeri bir sürüme ihtiyaç duyuyor.<br/><br/> Şirket içi | Tek başına Exchange Server 'ı, bir DAG altındaki veritabanını yedekleyin.<br/><br/> Posta kutusu, DAG altındaki posta kutusu veritabanı kurtarma.<br/><br/> ReFS desteklenmiyor.<br/><br/> Paylaşılan olmayan disk kümelerini yedekleyin.<br/><br/> Sürekli çoğaltma için yapılandırılmış Exchange Server 'ı yedekleyin.
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | DPM 2016 ve üzeri sürümlerde SharePoint 2016.<br/><br/>Şirket içi/Azure VM. | Sunucu yedekleme, ön uç Web sunucusu.<br/><br/> Grup, veritabanı, Web uygulaması, dosya veya liste öğesi, SharePoint arama, ön uç Web sunucusu kurtarma.<br/><br/> İçerik veritabanları için SQL Server AlwaysOn kullanarak bir grubu yedekleyemiyoruz.
**Windows Server 2016 üzerinde Hyper-V**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (SP1 ile)** | DPM 2016 ve üzeri için 2016 üzerinde Hyper-V destekleniyor.<br/><br/> Şirket içi. | **Hyper-V konağındaki Mabs Aracısı**: tüm VM 'leri ve konak verileri dosyalarını yedekleyin. Yerel depolama ile VM 'leri yedekleme, CSV depolama ile kümedeki VM 'Ler, SMB dosya sunucusu depolama ile VM 'Ler.<br/><br/> **Konuk VM 'de Mabs Aracısı**: VM 'de çalışan iş yüklerini yedekleyin. CSV.<br/><br/> **Kurtarma**: VM, VHD/birim/klasör/dosya kurtarma için VM, öğe düzeyinde kurtarma.<br/><br/> **Linux VM 'leri**: Hyper-V Windows Server 2012 R2 ve sonraki sürümlerde çalışırken yedekleme. Linux VM 'Leri için kurtarma, tüm makine içindir.
**VMware VM 'Leri: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2 <br/><br/> DPM 2012 R2, System Center güncelleştirme paketi 1 gerektirir) <br/><br/>Şirket içi. | CSVs, NFS ve SAN depolamada VMware VM 'lerini yedekleyin.<br/><br/> Tüm VM 'yi kurtarın.<br/><br/> Windows/Linux yedeklemesi.<br/><br/> Yalnızca Windows VM 'lerinin klasör/dosyalarını öğe düzeyinde kurtarma.<br/><br/> VMware vApps desteklenmez.<br/><br/> Linux VM 'Leri için kurtarma, tüm makine içindir.

- DPM/MABS tarafından yedeklenen kümelenmiş iş yükleri, DPM/MABS ile aynı etki alanında veya alt/güvenilen bir etki alanında olmalıdır.
- Güvenilmeyen etki alanları veya çalışma gruplarındaki verileri yedeklemek için NTLM/sertifika kimlik doğrulaması kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- MABS mimarisi hakkında [daha fazla bilgi edinin](backup-architecture.md#architecture-back-up-to-dpmmabs) .
- MARS Aracısı için nelerin desteklendiğini [gözden geçirin](backup-support-matrix-mars-agent.md) .
- MABS sunucusunu [ayarlayın](backup-azure-microsoft-azure-backup.md) .
- [DPM 'Yi ayarlayın](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
