---
title: MABS & System Center DPM destek matrisi
description: Bu makalede, şirket içi ve Azure VM kaynaklarını yedeklemek için Microsoft Azure Backup sunucusu (MABS) veya System Center DPM kullandığınızda Azure Backup desteği özetlenmektedir.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: e888b43ea5641f1943a096f045747d547c52fcfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609762"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Microsoft Azure Backup Server veya System Center DPM ile yedekleme için destek matrisi

[Azure Backup hizmetini](backup-overview.md) şirket içi makineleri ve iş yüklerini ve Azure sanal makinelerini (VM) yedeklemek için kullanabilirsiniz. Bu makalede, Microsoft Azure Backup Server (MABS) veya System Center Data Protection Manager (DPM) ve Azure Backup kullanarak makineleri yedeklemeye yönelik destek ayarları ve sınırlamaları özetlenmektedir.

## <a name="about-dpmmabs"></a>DPM/MABS hakkında

[System Center DPM](/system-center/dpm/dpm-overview) , kurumsal makinelerin ve verilerin yedeklenmesini ve kurtarılmasını yapılandıran, kolaylaştıran ve yöneten bir kurumsal çözümdür. [System Center](https://www.microsoft.com/system-center/pricing) ürün paketi 'nin bir parçasıdır.

MABS, şirket içi fiziksel sunucuları, VM 'Leri ve üzerinde çalışan uygulamaları yedeklemek için kullanılabilen bir sunucu ürünüdür.

MABS, System Center DPM 'yi temel alır ve birkaç farklılık ile benzer işlevler sağlar:

- MABS çalıştırmak için herhangi bir System Center lisansı gerekmez.
- Azure, hem MABS hem de DPM için uzun süreli yedekleme depolama alanı sağlar. Ayrıca DPM, banttaki uzun süreli depolamaya yönelik verileri yedeklemenizi sağlar. MABS bu işlevselliği sağlamaz.
- [BIRINCIL DPM sunucusunu ikincil BIR DPM sunucusuyla yedekleyebilirsiniz](/system-center/dpm/back-up-the-dpm-server). İkincil sunucu, birincil sunucu veritabanını ve birincil sunucuda depolanan veri kaynağı çoğaltmalarını korur. Birincil sunucu başarısız olursa, birincil sunucu tekrar kullanılabilir hale gelene kadar ikincil sunucu, birincil sunucu tarafından korunan iş yüklerini korumaya devam edebilir  MABS bu işlevselliği sağlamaz.

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

**Senaryo** | **Aracısı** | **Konum**
--- | --- | ---
**Şirket içi makineleri/iş yüklerini yedekleme** | DPM/MABS koruma Aracısı, yedeklemek istediğiniz makinelerde çalışır.<br/><br/> DPM/MABS sunucusundaki MARS Aracısı.<br/> Bu özelliği etkinleştirmek için gereken Microsoft Azure Kurtarma Hizmetleri aracısının veya Azure Backup aracısının en düşük sürümü 2.0.8719.0 ' dir.  | DPM/MABS 'nin şirket içinde çalışıyor olması gerekir.

## <a name="supported-deployments"></a>Desteklenen dağıtımlar

DPM/MABS, aşağıdaki tabloda özetlenen şekilde dağıtılabilir.

**Dağıtım** | **Destek** | **Ayrıntılar**
--- | --- | ---
**Şirket içinde dağıtıldı** | Fiziksel bir kümede değil, fiziksel sunucu.<br/><br/>Hyper-V VM. MABS 'yi, tek başına bir hiper yönetici veya kümede konuk makine olarak dağıtabilirsiniz. Bir kümenin veya tek başına hiper yöneticinin düğümüne dağıtılamaz. Azure Backup Sunucusu, özel, tek amaçlı bir sunucuda çalışmak üzere tasarlanmıştır.<br/><br/> Bir VMware ortamında Windows sanal makinesi olarak. | Şirket içi MABS sunucuları, Azure tabanlı iş yüklerini koruyamaz. <br><br> Daha fazla bilgi için bkz. [koruma matrisi](backup-mabs-protection-matrix.md).
**Azure Stack VM olarak dağıtıldı** | Yalnızca MABS | DPM, Azure Stack VM 'Leri yedeklemek için kullanılamaz.
**Azure VM olarak dağıtıldı** | Bu VM 'lerde çalışan Azure sanal makinelerini ve iş yüklerini korur | Azure 'da çalışan DPM/MABS, şirket içi makineleri yedekleyebilir. Yalnızca Azure IaaS VM 'lerinde çalışan iş yüklerini koruyabilir.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Desteklenen MABS ve DPM işletim sistemleri

Azure Backup, aşağıdaki işletim sistemlerinden herhangi birini çalıştıran DPM/MABS örneklerini yedekleyebilir. İşletim sistemleri en son hizmet paketlerini ve güncelleştirmelerini çalıştırıyor olmalıdır.

**Senaryo** | **DPM/MABS**
--- | ---
**Azure VM 'de MABS** |  Windows 2016 Datacenter.<br/><br/> Windows 2019 Datacenter.<br/><br/> Market 'ten bir görüntüyle başlamanız önerilir.<br/><br/> Dört çekirdek ve 8 GB RAM içeren minimum Standard_A4_v2.
**Azure VM 'de DPM** | Güncelleştirme 3 veya sonraki sürümleri ile System Center 2012 R2.<br/><br/> [System Center için gereken](/system-center/dpm/prepare-environment-for-dpm#dpm-server)Windows işletim sistemi.<br/><br/> Market 'ten bir görüntüyle başlamanız önerilir.<br/><br/> Dört çekirdek ve 8 GB RAM içeren minimum Standard_A4_v2.
**Şirket içi MABS** |  MABS v3 ve üzeri: Windows Server 2016 veya Windows Server 2019
**Şirket içi DPM** | Fiziksel sunucu/Hyper-V VM: System Center 2012 SP1 veya üzeri.<br/><br/> VMware VM: güncelleştirme 5 veya sonraki sürümleri ile System Center 2012 R2.

>[!NOTE]
>Azure Backup Sunucusu yüklemek Windows Server Core veya Microsoft Hyper-V Server 'da desteklenmez.

## <a name="management-support"></a>Yönetim desteği

**Sorun** | **Ayrıntılar**
--- | ---
**Yükleme** | Tek amaçlı bir makineye DPM/MABS 'yi yükler.<br/><br/> DPM/MABS 'yi bir etki alanı denetleyicisine, uygulama sunucusu rolü yüklemesine sahip bir makineye, Microsoft Exchange Server veya System Center Operations Manager çalıştıran bir makineye veya bir küme düğümüne yüklemeyin.<br/><br/> [Tüm DPM sistem gereksinimlerini gözden geçirin](/system-center/dpm/prepare-environment-for-dpm#dpm-server).
**Etki alanı** | DPM/MABS bir etki alanına katılmalıdır. Önce yüklemeyi, sonra DPM/MABS 'i bir etki alanına katın. Dağıtımdan sonra DPM/MABS 'yi yeni bir etki alanına taşıma desteklenmiyor.
**Depolama** | Modern yedekleme depolaması (MB), DPM 2016/MABS v2 ve sonrasında desteklenir. MABS v1 için kullanılamaz.
**MABS yükseltmesi** | Mabs v3 'i doğrudan yükleyebilir veya mabs v2 'den MABS v3 'e yükseltebilirsiniz. [Daha fazla bilgi edinin](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**MABS taşınıyor** | MB kullanıyorsanız, depolama alanını korurken MABS 'i yeni bir sunucuya taşımak desteklenir.<br/><br/> Sunucu orijinalde aynı ada sahip olmalıdır. Aynı depolama havuzunu korumak istiyorsanız adı değiştiremezsiniz ve veri kurtarma noktalarını depolamak için aynı MABS veritabanını kullanabilirsiniz.<br/><br/> Geri yüklemeniz gereken için MABS veritabanının bir yedeklemesi gerekir.

>[!NOTE]
>DPM/MABS sunucusunu yeniden adlandırma desteklenmiyor.

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
**Desteklenen yedekleme** | Bu işletim sistemleri yedeklemek istediğiniz VM 'Ler için desteklenir:<br/><br/> Windows Server Semi-Annual kanalı (Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Azure Stack VM 'Ler için SQL Server desteği** | Yedekleme SQL Server 2016, SQL Server 2014 SQL Server 2012 SP1.<br/><br/> Veritabanını yedekleme ve kurtarma.
**Azure Stack VM 'Ler için SharePoint desteği** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Bir grup, veritabanı, ön uç ve Web sunucusunu yedekleyin ve kurtarın.
**Yedeklenen VM 'Ler için ağ gereksinimleri** | Azure Stack iş yükündeki tüm sanal makineler aynı sanal ağa ait ve aynı aboneliğe ait olmalıdır.

## <a name="dpmmabs-networking-support"></a>DPM/MABS ağ desteği

### <a name="url-access"></a>URL erişimi

DPM sunucusu/MABS sunucusunun bu URL 'Lere ve IP adreslerine erişmesi gerekir:

* URL’ler
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP adresleri
  * 20.190.128.0/18
  * 40.126.0.0/18:

### <a name="azure-expressroute-support"></a>Azure ExpressRoute desteği

Azure ExpressRoute üzerinden verilerinizi, genel eşleme (eski devreler için kullanılabilir) ve Microsoft eşlemesi ile birlikte yedekleyebilirsiniz. Özel eşleme üzerinde yedekleme desteklenmez.

Ortak eşleme ile: aşağıdaki etki alanlarına/adreslere erişim sağlayın:

* URL’ler
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP adresleri
  * 20.190.128.0/18
  * 40.126.0.0/18

Microsoft eşlemesi ile aşağıdaki hizmetleri/bölgeleri ve ilgili topluluk değerlerini seçin:

- Azure Active Directory (12076:5060)
- Microsoft Azure bölgesi (Kurtarma Hizmetleri kasanızın konumuna göre)
- Azure depolama (Kurtarma Hizmetleri kasanızın konumuna göre)

Daha fazla bilgi için bkz. [ExpressRoute yönlendirme gereksinimleri](../expressroute/expressroute-routing.md).

>[!NOTE]
>Ortak eşleme, yeni devreler için kullanım dışıdır.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Azure Backup DPM/MABS bağlantısı

Yedeklemelerin düzgün çalışması için Azure Backup hizmetine bağlantı gereklidir ve Azure aboneliği etkin olmalıdır. Aşağıdaki tabloda, bu iki şey gerçekleşmezseniz davranış gösterilmektedir.

**MABS 'ten Azure 'a** | **Abonelik** | **Yedekleme/geri yükleme**
--- | --- | ---
Bağlı | Etkin | DPM/MABS diskine yedekleme.<br/><br/> Azure 'a yedekleyin.<br/><br/> Diskten geri yükle.<br/><br/> Azure 'dan geri yükleyin.
Bağlı | Süre dolma/sağlaması kaldırılmış | Diske veya Azure 'a yedekleme yok.<br/><br/> Aboneliğin geçerliliği dolmuşsa diskten veya Azure 'dan geri yükleme yapabilirsiniz.<br/><br/> Abonelik kullanımdan bulunursa diskten veya Azure 'dan geri yükleme yapamazsınız. Azure kurtarma noktaları silinir.
15 günden fazla bağlantı yok | Etkin | Diske veya Azure 'a yedekleme yok.<br/><br/> Diskten veya Azure 'dan geri yükleme yapabilirsiniz.
15 günden fazla bağlantı yok | Süre dolma/sağlaması kaldırılmış | Diske veya Azure 'a yedekleme yok.<br/><br/> Aboneliğin geçerliliği dolmuşsa diskten veya Azure 'dan geri yükleme yapabilirsiniz.<br/><br/> Abonelik kullanımdan bulunursa diskten veya Azure 'dan geri yükleme yapamazsınız. Azure kurtarma noktaları silinir.

## <a name="domain-and-domain-trusts-support"></a>Etki alanı ve etki alanı güvenleri desteği

|Gereksinim |Ayrıntılar |
|---------|---------|
|Etki alanı    | DPM/MABS sunucusunun bir Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 etki alanında olması gerekir.        |
|Etki alanı güveni   |  Ayrı ormanlar arasında orman düzeyinde, çift yönlü bir güven kurarsınız, DPM/MABS, ormanlar genelinde veri korumasını destekler.   <BR><BR>   DPM/MABS, DPM/MABS sunucu etki alanıyla çift yönlü bir güven ilişkisine sahip olan bir ormandaki etki alanlarında bulunan sunucuları ve iş istasyonlarını koruyabilir. Çalışma gruplarındaki veya güvenilmeyen etki alanlarındaki bilgisayarları korumak için bkz [. çalışma gruplarında ve güvenilmeyen etki alanlarında iş yüklerini yedekleme ve geri yükleme.](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains) <br><br> Hyper-V sunucu kümelerini yedeklemek için, bunların MABS sunucusuyla aynı etki alanında veya güvenilen ya da alt etki alanında bulunması gerekir. Güvenilmeyen bir etki alanı veya iş yükündeki sunucuları ve kümeleri NTLM ya da tek bir sunucu için sertifika kimlik doğrulaması veya bir küme için yalnızca sertifika kimlik doğrulaması kullanarak yedekleyebilirsiniz.  |

## <a name="dpmmabs-storage-support"></a>DPM/MABS depolama desteği

DPM/MABS 'e yedeklenen veriler yerel disk depolama alanında depolanır.

USB veya çıkarılabilir sürücüler desteklenmez.

DPM/MABS birimlerinde NTFS sıkıştırması desteklenmez.

BitLocker yalnızca disk depolama havuzunu ekledikten sonra etkinleştirilebilir. Eklemeden önce BitLocker 'ı etkinleştirmeyin.

Ağa bağlı depolama (NAS), DPM depolama havuzunda kullanım için desteklenmiyor.

**Depolama** | **Ayrıntılar**
--- | ---
**MB** | Modern yedekleme depolaması (MB), DPM 2016/MABS v2 ve sonrasında desteklenir. MABS v1 için kullanılamaz.
**Azure VM 'de MABS depolaması** | Veriler, DPM/MABS sanal makinesine bağlı olan ve DPM/MABS üzerinde yönetilen Azure disklerinde depolanır. DPM/MABS depolama havuzu için kullanılabilecek disk sayısı, VM 'nin boyutuyla sınırlıdır.<br/><br/> A2 VM: 4 disk; A3 VM: 8 disk; A4 VM: her disk için en fazla 1 TB boyutunda 16 disk. Bu, kullanılabilir toplam yedekleme depolama havuzunu belirler.<br/><br/> Yedekleyebileceğiniz veri miktarı, eklenen disklerin sayısına ve boyutuna bağlıdır.
**Azure VM 'de MABS veri saklama** | Verileri DPM/MABS Azure diskinde bir gün bekletmenizi ve DPM/MABS 'ten daha uzun bekletme için kasaya yedeklemenizi öneririz. Bu şekilde, daha büyük miktarda veriyi Azure Backup devrederleyerek koruyabilirsiniz.

### <a name="modern-backup-storage-mbs"></a>Modern yedekleme depolama alanı (MB)

DPM 2016/MABS v2 'den (Windows Server 2016 ' de çalışan) ve sonraki sürümlerde modern yedekleme depolama (MB) özelliğinden yararlanabilirsiniz.

- MB yedeklemeler dayanıklı bir dosya sistemi (ReFS) diskinde depolanır.
- MB, daha hızlı yedekleme ve depolama alanının daha verimli kullanımı için ReFS blok kopyalamayı kullanır.
- Yerel DPM/MABS depolama havuzuna birim eklediğinizde, bunları sürücü harfleriyle yapılandırırsınız. Daha sonra iş yükü depolama alanını farklı birimlerde yapılandırabilirsiniz.
- Verileri DPM/MABS 'e yedeklemek için koruma grupları oluşturduğunuzda, kullanmak istediğiniz sürücüyü seçersiniz. Örneğin, SQL veya diğer yüksek ıOPS iş yükleri için yedeklemeleri yüksek performanslı bir sürücüde saklayabilir ve daha düşük bir performans sürücüsünde daha az sıklıkta yedeklenen iş yüklerini depokalabilirsiniz.

## <a name="supported-backups-to-mabs"></a>MABS için desteklenen yedeklemeler

Azure Backup Sunucusu ile koruyabileceğiniz çeşitli sunucular ve iş yükleri hakkında daha fazla bilgi için [Azure Backup sunucusu koruma matrisine](./backup-mabs-protection-matrix.md#protection-support-matrix)bakın.

## <a name="supported-backups-to-dpm"></a>DPM için desteklenen yedeklemeler

Data Protection Manager ile koruyabileceğiniz çeşitli sunucular ve iş yükleri hakkında daha fazla bilgi için [DPM 'Nin neleri yedekleyebileceği](/system-center/dpm/dpm-protection-matrix)makalesine başvurun.

- DPM/MABS tarafından yedeklenen kümelenmiş iş yükleri, DPM/MABS ile aynı etki alanında veya alt/güvenilen bir etki alanında olmalıdır.
- Güvenilmeyen etki alanları veya çalışma gruplarındaki verileri yedeklemek için NTLM/sertifika kimlik doğrulaması kullanabilirsiniz.

## <a name="deduplicated-volumes-support"></a>Yinelenenleri kaldırılan birimler desteği

>[!NOTE]
> MABS için Yinelenenleri kaldırma desteği, işletim sistemi desteğine bağlıdır.

### <a name="for-ntfs-volumes"></a>NTFS birimlerinde

| Korumalı sunucunun işletim sistemi  | MABS sunucusunun işletim sistemi  | MABS sürümü  | Yinelenenleri kaldırma desteği |
| ------------------------------------------ | ------------------------------------- | ------------------ | -------------------- |
| Windows Server 2019                       | Windows Server 2019                  | MABS v3            | Y                    |
| Windows Server 2016                       | Windows Server 2019                  | MABS v3            | Iz                   |
| Windows Server 2012 R2                    | Windows Server 2019                  | MABS v3            | N                    |
| Windows Server 2012                       | Windows Server 2019                  | MABS v3            | N                    |
| Windows Server 2019                       | Windows Server 2016                  | MABS v3            | Y * *                  |
| Windows Server 2016                       | Windows Server 2016                  | MABS v3            | Y                    |
| Windows Server 2012 R2                    | Windows Server 2016                  | MABS v3            | Y                    |
| Windows Server 2012                       | Windows Server 2016                  | MABS v3            | Y                    |

- \* WS 2019 üzerinde çalışan MABS v3 ile WS 2016 NTFS yinelenenleri kaldırılmış bir birimi korurken, kurtarma işlemleri etkilenebilir. Yinelenenleri kaldırılmış bir şekilde kurtarma işlemi gerçekleştirmek için bir düzeltmemiz var. MABS v3 UR1 üzerinde bu düzeltmeyle karşılaşırsanız MABS desteğine ulaşın.
- \** WS 2016 üzerinde MABS v3 ile WS 2019 NTFS yinelenenleri kaldırılmış bir birimi korurken, yedeklemeler ve geri yüklemeler yinelenenleri kaldırma işlemi geri alınamaz. Bu, yedeklemelerin MABS sunucusunda özgün NTFS yinelenenleri kaldırılmış birimden daha fazla alan tükettiği anlamına gelir.

**Sorun**: korumalı sunucu Işletim sistemini windows Server 2016 ' den windows Server 2019 ' e yükseltirseniz, yinelenenleri kaldırma mantığındaki değişiklikler nedenıyle, NTFS yinelenenleri kaldırılmış birimin yedeklemesi etkilenecektir.

**Geçici çözüm**: mabs v3 UR1 için bu düzeltmeyle ihtiyacınız olduğunda mabs desteğine ulaşın.

### <a name="for-refs-volumes"></a>ReFS birimleri için

>[!NOTE]
> Yinelenenleri kaldırılmış ReFS birimlerinin yedeklemeleriyle ilgili birkaç sorun tespit ettik. Bunları düzeltmek için çalışıyoruz ve kullanılabilir bir düzeltme olduğunda bu bölümü güncelliyoruz. Bundan sonra, MABS v3 'den yinelenenleri kaldırılmış ReFS birimlerinin yedeklenme desteğini kaldırdık.
>
> MABS v3 UR1 ve üzeri, normal ReFS birimlerinin korunmasını ve kurtarılmasını desteklemeye devam etmektedir.

## <a name="next-steps"></a>Sonraki adımlar

- MABS mimarisi hakkında [daha fazla bilgi edinin](backup-architecture.md#architecture-back-up-to-dpmmabs) .
- MARS Aracısı için nelerin desteklendiğini [gözden geçirin](backup-support-matrix-mars-agent.md) .
- MABS sunucusunu [ayarlayın](backup-azure-microsoft-azure-backup.md) .
- [DPM 'Yi ayarlayın](/system-center/dpm/install-dpm).
