---
title: Azure Site Recovery ile şirket içi uygulamalar için olağanüstü durum kurtarma hakkında
description: Azure Site Recovery hizmeti ile olağanüstü durum kurtarma kullanılarak korunabilen iş yüklerini açıklar.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 1a5d20e6feacfe72052142c07dc45753b9bc3138
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599120"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Şirket içi uygulamalar için olağanüstü durum kurtarma hakkında

Bu makalede, [Azure Site Recovery](site-recovery-overview.md) hizmetiyle olağanüstü durum kurtarma için koruyabileceğiniz şirket içi iş yükleri ve uygulamalar açıklanmaktadır.

## <a name="overview"></a>Genel Bakış

Kuruluşların iş yüklerini ve verileri güvenli ve planlanmamış kapalı kalma süresi boyunca güvenli ve kullanılabilir halde tutmaya yönelik bir iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejisi gerekir. Ve normal çalışma koşullarına kurtarın.

Site Recovery, BCDR stratejinize katkıda bulunan bir Azure hizmetidir. Site Recovery’yi kullanarak, buluta veya ikincil bir siteye uygulamayla tutarlı çoğaltma dağıtabilir. Çoğaltmayı yönetmek, olağanüstü durum kurtarma testi gerçekleştirmek ve yük devretme ve yeniden çalışma işlemlerini çalıştırmak için Site Recovery kullanabilirsiniz. Uygulamalarınız Windows veya Linux tabanlı bilgisayarlarda, fiziksel sunucularda, VMware 'de veya Hyper-V ' d i çalışabilir.

Site Recovery, SharePoint, Exchange, Dynamics, SQL Server ve Active Directory gibi Microsoft uygulamalarıyla tümleşir. Microsoft, Oracle, SAP ve Red hat gibi önde gelen satıcılarla yakından birlikte çalışmaktadır. Çoğaltma çözümlerini her uygulama için ayrı olarak özelleştirebilirsiniz.

## <a name="why-use-site-recovery-for-application-replication"></a>Uygulama çoğaltma için neden Site Recovery'yi kullanmam gerekir?

Site Recovery, uygulama düzeyinde koruma ve kurtarmaya şu yollarla katkıda bulunur:

- App-agstik ve desteklenen bir makinede çalışan tüm iş yükleri için çoğaltma sağlar.
- En kritik iş uygulamalarının ihtiyaçlarını karşılamak için, kurtarma noktası hedefleri (RPO) ile en fazla 30 saniye olan neredeyse zaman uyumlu çoğaltma.
- Tek veya çok katmanlı uygulamalar için uygulamayla tutarlı anlık görüntüler.
- SQL Server AlwaysOn ile tümleştirme ve diğer uygulama düzeyi çoğaltma teknolojileriyle iş ortaklığı. Örneğin, Active Directory çoğaltma, SQL AlwaysOn ve Exchange veritabanı kullanılabilirlik grupları (dag 'ler).
- Tek bir tıklama ile tüm uygulama yığınını kurtarmanıza ve bu plana dış betikler ve manuel eylemleri dahil etmek için esnek kurtarma planları.
- Uygulama Ağı gereksinimlerini basitleştirmek için Site Recovery ve Azure 'da gelişmiş ağ yönetimi. Düşük kurtarma süresi hedeflerini (RTO) ağ geçiş işlemleri için IP adreslerini ayırabilme, yük dengelemeyi yapılandırma ve Azure Traffic Manager tümleştirme gibi ağ yönetimi.
- İndirilebilen ve kurtarma planlarıyla tümleştirilebilen üretime hazır ve uygulamaya özgü betikler sağlayan zengin bir otomasyon kitaplığı.

## <a name="workload-summary"></a>İş yükü özeti

Site Recovery, desteklenen bir makinede çalışan herhangi bir uygulamayı çoğaltabilir. Aşağıdaki tabloda belirtilen uygulamalar için ek testler yapmak üzere ürün ekipleriyle işbirliği yaptık.

| **İş yükü** |**Azure VM’lerini Azure’a çoğaltma** |**Hyper-V VM'lerini ikincil bir siteye çoğaltma** | **Hyper-V VM'lerini Azure'a çoğaltma** | **VMware VM'lerini ikincil bir siteye çoğaltma** | **VMware VM'lerini Azure'a çoğaltma** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Yes |Yes |Yes |Yes |Yes|
| Web uygulamaları (IIS, SQL) |Yes |Yes |Yes |Yes |Yes|
| System Center Operations Manager |Yes |Yes |Yes |Yes |Yes|
| SharePoint |Yes |Yes |Yes |Yes |Yes|
| SAP<br/><br/>Küme olmayan seçenekler için SAP sitesini Azure'a çoğaltma |Evet (Microsoft tarafından test edilmiştir) |Evet (Microsoft tarafından test edilmiştir) |Evet (Microsoft tarafından test edilmiştir) |Evet (Microsoft tarafından test edilmiştir) |Evet (Microsoft tarafından test edilmiştir)|
| Exchange (DAG olmayan) |Yes |Yes |Yes |Yes |Yes|
| Uzak Masaüstü/VDI |Yes |Yes |Yes |Yes |Yes|
| Linux (işletim sistemi ve uygulamalar) |Evet (Microsoft tarafından test edilmiştir) |Evet (Microsoft tarafından test edilmiştir) |Evet (Microsoft tarafından test edilmiştir) |Evet (Microsoft tarafından test edilmiştir) |Evet (Microsoft tarafından test edilmiştir)|
| Dynamics AX |Yes |Yes |Yes |Yes |Yes|
| Windows Dosya Sunucusu |Yes |Yes |Yes |Yes |Yes|
| Citrix XenApp ve XenDesktop |No|Yok |No |Yok |No |

## <a name="replicate-active-directory-and-dns"></a>Active Directory'yi ve DNS'yi çoğaltma

Çoğu kurumsal uygulama için Active Directory ve DNS gereklidir. Olağanüstü durum kurtarma sırasında, iş yüklerini ve uygulamaları kurtarmadan önce Bu altyapı bileşenlerini korumanız ve kurtarmanız gerekir.

Site Recovery'yi kullanarak Active Directory ve DNS için tamamen otomatik bir olağanüstü durum kurtarma planı oluşturabilirsiniz. Örneğin, birincil bir siteden ikincil bir siteye SharePoint ve SAP yük devri yapmak için, Active Directory Yük devretmek için bir kurtarma planı ayarlayabilirsiniz. Ardından, Active Directory bağımlı olan diğer uygulamaların yükünü devretmek için uygulamaya özgü ek bir kurtarma planı kullanın.

Active Directory ve DNS için olağanüstü durum kurtarma hakkında [daha fazla bilgi edinin](site-recovery-active-directory.md) .

## <a name="protect-sql-server"></a>SQL Server'ı koruma

SQL Server, şirket içi veri merkezinde birçok iş uygulaması için bir veri Hizmetleri temeli sağlar. Site Recovery, SQL Server kullanan çok katmanlı kurumsal uygulamaları korumak için SQL Server HA/DR teknolojileriyle birlikte kullanılabilir.

Site Recovery şunları sağlar:

- SQL Server için basit ve ekonomik bir olağanüstü durum kurtarma çözümü. SQL Server tek başına sunucularının ve kümelerinin birden çok sürümünü Azure'a veya ikincil bir siteye çoğaltma olanağı.
- Azure Site Recovery kurtarma planları ile yük devretme ve yeniden çalışma gibi işlemleri yönetmek için SQL AlwaysOn Kullanılabilirlik Grupları ile tümleştirme olanağı.
- SQL Server veritabanları dahil olmak üzere bir uygulamadaki tüm katmanlara yönelik uçtan uca kurtarma planları.
- Azure 'daki daha büyük IaaS sanal makine _boyutlarına kaydederek,_ en üst düzey yüklere yönelik SQL Server ölçeklendirin Site Recovery.
- Kolay SQL Server olağanüstü durum kurtarma testi Üretim ortamınızı etkilemeden uyumluluk denetimlerini çalıştırmak ve verileri analiz etmek için test amaçlı yük devretme işlemini çalıştırabilirsiniz.

SQL Server için olağanüstü durum kurtarma hakkında [daha fazla bilgi edinin](site-recovery-sql.md) .

## <a name="protect-sharepoint"></a>SharePoint'i koruma

Azure Site Recovery, SharePoint dağıtımlarının korunmasına şu şekilde yardımcı olur:

- Olağanüstü durum kurtarma için yedek bir gruba olan ihtiyacı ve bununla ilgili altyapı maliyetini ortadan kaldırır. Tüm grubu (Web, uygulama ve veritabanı katmanları) Azure 'a veya ikincil bir siteye çoğaltmak için Site Recovery kullanın.
- Uygulama dağıtımını ve yönetimini basitleştirir. Birincil siteye dağıtılan güncelleştirmeler otomatik olarak çoğaltılır. İkincil bir sitedeki bir grubun yük devretme ve kurtarmasından sonra güncelleştirmeler kullanılabilir. Tek bir grubu güncel tutmaya ilişkin yönetim karmaşıklığını ve maliyetlerini düşürür.
- Test ve hata ayıklama işlemleri için üretim benzeri isteğe bağlı bir kopya çoğaltma ortamı oluşturarak SharePoint uygulaması için geliştirme sürecini basitleştirir.
- SharePoint dağıtımlarını Azure'a geçirmek için Site Recovery'yi kullanarak buluta geçişi basitleştirir.

SharePoint için olağanüstü durum kurtarma hakkında [daha fazla bilgi edinin](site-recovery-sharepoint.md) .

## <a name="protect-dynamics-ax"></a>Dynamics AX'i koruma

Azure Site Recovery, Dynamics AX ERP çözümünüzün korunmasına şu şekillerde yardımcı olur:

- Tüm Dynamics AX ortamınızı (Web ve AOS katmanları, veritabanı katmanları, SharePoint) Azure 'a veya ikincil bir siteye çoğaltmasını yönetme.
- Dynamics AX dağıtımlarının buluta (Azure'a) geçişini basitleştirir.
- Test ve hata ayıklama işlemleri için üretim benzeri isteğe bağlı bir kopya oluşturarak Dynamics AX uygulamasının geliştirme ve test sürecini basitleştirir.

Dinamik AX için olağanüstü durum kurtarma hakkında [daha fazla bilgi edinin](site-recovery-dynamicsax.md) .

## <a name="protect-remote-desktop-services"></a>Uzak Masaüstü Hizmetleri koru

Uzak Masaüstü Hizmetleri (RDS), kullanıcıların her yerde çalışmasına izin veren sanal masaüstü altyapısı (VDı), oturum tabanlı masaüstü bilgisayarlar ve uygulamalar sağlar.

Azure Site Recovery, aşağıdaki hizmetleri çoğaltabilirsiniz:

- Yönetilen veya yönetilmeyen havuza alınmış sanal masaüstlerini ikincil bir siteye çoğaltın.
- Uzak uygulamaları ve oturumları ikincil bir siteye veya Azure 'a çoğaltın.

Aşağıdaki tabloda çoğaltma seçenekleri gösterilmektedir:

| **RDS** |**Azure VM’lerini Azure’a çoğaltma** | **Hyper-V VM'lerini ikincil bir siteye çoğaltma** | **Hyper-V VM'lerini Azure'a çoğaltma** | **VMware VM'lerini ikincil bir siteye çoğaltma** | **VMware VM'lerini Azure'a çoğaltma** | **Fiziksel sunucuları ikincil bir siteye çoğaltma** | **Fiziksel sunucuları Azure'a çoğaltma** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Havuza Alınmış Sanal Masaüstü (yönetilmeyen)** |Hayır|Yes |Hayır |Yes |Hayır |Yes |Hayır |
| **Havuza Alınmış Sanal Masaüstü (yönetilen ve UPD'siz)** |Hayır|Yes |Hayır |Yes |Hayır |Yes |Hayır |
| **Uzak uygulamalar ve Masaüstü oturumları (UPD'siz)** |Yes|Yes |Yes |Yes |Yes |Yes |Yes |

RDS için olağanüstü durum kurtarma hakkında [daha fazla bilgi edinin](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) .

## <a name="protect-exchange"></a>Exchange'i koruma

Site Recovery, Exchange'in korunmasına şu şekilde yardımcı olur:

- Site Recovery, tek veya tek başına sunucu gibi küçük Exchange dağıtımları için Azure'a veya ikincil bir siteye çoğaltma ve yük devretme yapabilir.
- Site Recovery, daha büyük dağıtımlar için Exchange DAG'leri ile tümleşir.
- Exchange DAG'leri, kuruluşlarda Exchange olağanüstü durum kurtarma işlemleri için önerilen çözümdür.  Site Recovery kurtarma planlarında siteler arası DAG yük devretmelerini düzenlemek üzere DAG'ler bulunabilir.

Exchange için olağanüstü durum kurtarma hakkında daha fazla bilgi için bkz. [Exchange DAG](/Exchange/high-availability/database-availability-groups/database-availability-groups) 'Ler ve [Exchange olağanüstü durum kurtarma](/Exchange/high-availability/disaster-recovery/disaster-recovery).

## <a name="protect-sap"></a>SAP'yi koruma

Site Recovery'yi kullanarak SAP dağıtımınızı şu şekilde koruyun:

- Bileşenleri Azure'a çoğaltarak, şirket içinde çalışan SAP NetWeaver ve NetWeaver dışı üretim uygulamalarının korumasını etkinleştirin.
- Bileşenleri başka bir Azure veri merkezine çoğaltarak,Azure çalıştıran SAP NetWeaver ve NetWeaver dışı üretim uygulamalarının korumasını etkinleştirin.
- SAP dağıtımınızı Azure'a geçirmek için Site Recovery'yi kullanarak buluta geçişi basitleştirin.
- SAP uygulamaları test etmek için talep üzerine üretim kopyası oluşturarak SAP proje yükseltmelerini, testlerini ve prototip oluşturma işlemlerini basitleştirin.

SAP için olağanüstü durum kurtarma hakkında [daha fazla bilgi edinin](site-recovery-sap.md) .

## <a name="protect-internet-information-services"></a>Internet Information Services koru

Internet Information Services (IIS) dağıtımınızı şu şekilde korumak için Site Recovery kullanın:

Azure Site Recovery, ortamınızdaki kritik bileşenleri soğuk bir uzak konuma veya Microsoft Azure gibi bir genel buluta çoğaltarak olağanüstü durum kurtarma sağlar. Web sunucusuna ve veritabanına sahip sanal makineler kurtarma sitesine çoğaltıldıklarından, yapılandırma dosyaları veya sertifikaları için ayrı bir yedekleme gereksinimi yoktur. Yük devretme sonrasında değişen ortam değişkenlerine bağımlı uygulama eşlemeleri ve bağlamaları, olağanüstü durum kurtarma planlarına tümleştirilmiş betikler aracılığıyla güncelleştirilebilir. Sanal makineler yalnızca yük devretme sırasında kurtarma sitesine getirilir. Azure Site Recovery Ayrıca, aşağıdaki özellikleri sağlayarak uçtan uca yük devretmeyi düzenlemenize yardımcı olur:

- Çeşitli katmanlardaki sanal makinelerin kapatma ve başlatma sıralaması.
- Sanal makinelerde başlatıldıktan sonra uygulama bağımlılıkları ve bağlamaların güncelleştirmelerine izin vermek için betikler ekleme. Betikler ayrıca DNS sunucusunu kurtarma konumunu işaret edecek şekilde güncelleştirmek için kullanılabilir.
- Birincil ve kurtarma ağlarını eşleyerek ve yük devretmenin güncelleştirilmesini gerektirmeyen komut dosyalarını kullanarak sanal makinelerin ön yük devretmesini IP adresleri atayın.
- Olağanüstü durum sırasında karışıklığı ortadan kaldıran birden çok Web uygulaması için tek tıklamayla yük devretme özelliği.
- DR ayrıntıları için yalıtılmış bir ortamda kurtarma planlarını test edebilme olanağı.

IIS için olağanüstü durum kurtarma hakkında [daha fazla bilgi edinin](site-recovery-iis.md) .

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Citrix XenApp ve XenDesktop’u koruma

2020 Mart itibariyle Citrix, genel bulut tarafından barındırılan iş yükleri için kullanımdan kaldırma ve destek sonu duyurmuştur. Bu nedenle, Citrix iş yüklerini korumak için Site Recovery kullanmanızı önermiyoruz.

## <a name="next-steps"></a>Sonraki adımlar

Azure VM için olağanüstü durum kurtarma hakkında [daha fazla bilgi edinin](azure-to-azure-quickstart.md) .
