---
title: Azure Site Recovery ile şirket içi uygulamalar için olağanüstü durum kurtarma hakkında
description: Azure Site Recovery hizmetiyle olağanüstü durum kurtarma kullanılarak korunabilecek iş yüklerini açıklar.
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: raynew
ms.openlocfilehash: ad989a28e150635637a477d07803107a9fefff63
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255550"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Şirket içi uygulamalar için olağanüstü durum kurtarma hakkında

Bu makalede, [Azure Site Recovery](site-recovery-overview.md) hizmetiyle olağanüstü durum kurtarma için koruyabileceğiniz şirket içi iş yükleri ve uygulamalar açıklanmaktadır.



## <a name="overview"></a>Genel bakış

Kuruluşlar, iş yüklerinin ve verilerin planlanmış ve planlanmamış kapalı kalma süresi boyunca güvende ve kullanılabilir durumda tutulması ve normal çalışma koşullarına en kısa sürede kurtarılmasını sağlamak için iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejisine ihtiyaç duyar.

Site Recovery, BCDR stratejinize katkıda bulunan bir Azure hizmetidir. Site Recovery kullanarak, buluta veya ikincil bir siteye uygulama duyarlı çoğaltma dağıtabilirsiniz. Uygulamalarınız Windows veya Linux tabanlı, fiziksel sunucular, VMware veya Hyper-V ' d e n çalışıyor olsun, çoğaltmayı düzenlemek, olağanüstü durum kurtarma testi gerçekleştirmek ve yük devretme ve yeniden çalışma işlemlerini çalıştırmak için Site Recovery kullanabilirsiniz.

Site Recovery, SharePoint, Exchange, Dynamics, SQL Server ve Active Directory dahil olmak üzere Microsoft uygulamalarıyla tümleşir. Microsoft ayrıca, Oracle, SAP ve Red hat gibi önde gelen satıcılarla de birlikte çalışmaktadır. Uygulama temelinde, çoğaltma çözümlerini özelleştirebilirsiniz.

## <a name="why-use-site-recovery-for-application-replication"></a>Uygulama çoğaltma için neden Site Recovery kullanmalıyım?

Site Recovery uygulama düzeyi korumaya ve kurtarmaya şu şekilde katkıda bulunur:

* Desteklenen bir makinede çalışan herhangi bir iş yükü için çoğaltma sağlayan, uygulama açısından belirsiz.
* En kritik iş uygulamalarının ihtiyaçlarını karşılamak için, RPOs 'un süresi 30 saniye olan neredeyse zaman uyumlu çoğaltma.
* Tek veya çok katmanlı uygulamalar için uygulamayla tutarlı anlık görüntüler.
* AD Çoğaltma, SQL AlwaysOn, Exchange veritabanı kullanılabilirlik grupları (dag 'ler) dahil olmak üzere SQL Server AlwaysOn ve diğer uygulama düzeyi çoğaltma teknolojileriyle iş ortaklığı ile tümleştirme.
* Tek bir tıklama ile tüm uygulama yığınını kurtarmanıza ve dış betikleri ve el ile gerçekleştirilen eylemleri plana eklemeyi sağlayan esnek kurtarma planları.
* Site Recovery ve Azure 'da gelişmiş ağ yönetimi, IP adreslerini ayırabilme, yük dengelemeyi yapılandırma ve düşük RTO ağ geçiş işlemleri için Azure Traffic Manager ile tümleştirme gibi uygulama ağ gereksinimlerini basitleştirmeye yöneliktir.
* Kurtarma planlarıyla indirilebilen ve tümleştirilebilen üretime özgü, uygulamaya özel betikler sağlayan zengin bir Otomasyon kitaplığı.

## <a name="workload-summary"></a>İş yükü Özeti
Site Recovery, desteklenen bir makinede çalışan herhangi bir uygulamayı çoğaltabilir. Ayrıca, tabloda belirtilen uygulamalar için ek test yürütmek üzere ürün ekipleriyle işbirliği yaptık.

| **Yüküne** |**Azure VM 'lerini Azure 'a çoğaltma** |**Hyper-V VM 'lerini ikincil bir siteye çoğaltma** | **Hyper-V VM 'lerini Azure 'a çoğaltma** | **VMware VM 'lerini ikincil bir siteye çoğaltma** | **VMware VM 'lerini Azure 'a çoğaltma** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Y |Y |Y |Y |Y|
| Web uygulamaları (IIS, SQL) |Y |Y |Y |Y |Y|
| System Center Operations Manager |Y |Y |Y |Y |Y|
| SharePoint |Y |Y |Y |Y |Y|
| SAP<br/><br/>Küme dışı için SAP sitesini Azure 'a çoğaltma |Y (Microsoft tarafından test edilmiştir) |Y (Microsoft tarafından test edilmiştir) |Y (Microsoft tarafından test edilmiştir) |Y (Microsoft tarafından test edilmiştir) |Y (Microsoft tarafından test edilmiştir)|
| Exchange (DAG olmayan) |Y |Y |Y |Y |Y|
| Uzak Masaüstü/VDı |Y |Y |Y |Y |Y|
| Linux (işletim sistemi ve uygulamalar) |Y (Microsoft tarafından test edilmiştir) |Y (Microsoft tarafından test edilmiştir) |Y (Microsoft tarafından test edilmiştir) |Y (Microsoft tarafından test edilmiştir) |Y (Microsoft tarafından test edilmiştir)|
| Dynamics AX |Y |Y |Y |Y |Y|
| Windows dosya sunucusu |Y |Y |Y |Y |Y|
| Citrix XenApp ve XenDesktop |Y|Yok |Y |Yok |Y |

## <a name="replicate-active-directory-and-dns"></a>Active Directory ve DNS 'yi çoğaltma
Active Directory ve DNS altyapısı çoğu kurumsal uygulama için gereklidir. Olağanüstü durum kurtarma sırasında, iş yüklerinizi ve uygulamalarınızı kurtarmadan önce Bu altyapı bileşenlerini korumanız ve kurtarmanız gerekir.

Active Directory ve DNS için tamamen bir otomatik olağanüstü durum kurtarma planı oluşturmak için Site Recovery kullanabilirsiniz. Örneğin, SharePoint ve SAP 'nin yükünü birincil bir siteden ikincil bir siteye devretmek istiyorsanız, öncelikle Active Directory yük devri yapan bir kurtarma planı ayarlayabilir ve ardından Active Directory bağımlı olan diğer uygulamaların yükünü devretmek için uygulamaya özgü ek bir kurtarma planı ayarlayabilirsiniz.

Active Directory ve DNS 'yi koruma hakkında [daha fazla bilgi edinin](site-recovery-active-directory.md) .

## <a name="protect-sql-server"></a>SQL Server'ı koruma
SQL Server, şirket içi veri merkezindeki birçok iş uygulaması için veri Hizmetleri için veri Hizmetleri temeli sağlar.  Site Recovery, SQL Server kullanan çok katmanlı kurumsal uygulamaları korumak için SQL Server HA/DR teknolojileriyle birlikte kullanılabilir. Site Recovery şunları sağlar:

* SQL Server için basit ve ekonomik bir olağanüstü durum kurtarma çözümü. Tek başına sunucu ve kümelerin SQL Server birden çok sürümünü Azure 'a veya ikincil bir siteye çoğaltın.  
* Yük devretmeyi yönetmek ve Azure Site Recovery kurtarma planlarıyla yeniden çalışma yapmak için SQL AlwaysOn Kullanılabilirlik Grupları ile tümleştirme.
* SQL Server veritabanları da dahil olmak üzere bir uygulamadaki tüm katmanlar için uçtan uca kurtarma planları.
* En üst düzey yüklere yönelik SQL Server, Azure 'da daha büyük IaaS sanal makine boyutlarına "gömülmüş" olarak ölçeklendirerek, Site Recovery.
* SQL Server olağanüstü durum kurtarmanın kolay testi. Üretim ortamınızı etkilemeden verileri analiz etmek ve uyumluluk denetimlerini çalıştırmak için yük devretme testi çalıştırabilirsiniz.

SQL Server 'ı koruma hakkında [daha fazla bilgi edinin](site-recovery-sql.md) .

## <a name="protect-sharepoint"></a>SharePoint 'i koruma
Azure Site Recovery, SharePoint dağıtımlarının korunmasına şu şekilde yardımcı olur:

* Olağanüstü durum kurtarma için tek bir grubun gereksinimini ve ilişkili altyapı maliyetlerini ortadan kaldırır. Tüm grubu (Web, uygulama ve veritabanı katmanları) Azure 'a veya ikincil bir siteye çoğaltmak için Site Recovery kullanın.
* Uygulama dağıtımını ve yönetimini basitleştirir. Birincil siteye dağıtılan güncelleştirmeler otomatik olarak çoğaltılır ve bu nedenle ikincil bir sitede bir grubun yük devretmesinin ve kurtarmasından sonra kullanılabilir. Ayrıca, tek bir grubu güncel tutmaya ilişkin yönetim karmaşıklığını ve maliyetlerini düşürür.
* Test ve hata ayıklama için isteğe bağlı bir kopya çoğaltma ortamı oluşturarak SharePoint uygulama geliştirmeyi ve testini basitleştirir.
* SharePoint dağıtımlarını Azure 'a geçirmek için Site Recovery kullanarak buluta geçişi basitleştirir.

SharePoint 'i koruma hakkında [daha fazla bilgi edinin](site-recovery-sharepoint.md) .

## <a name="protect-dynamics-ax"></a>Dynamics AX 'i koruma
Azure Site Recovery, Dynamics AX ERP çözümünüzü şu şekilde korumanıza yardımcı olur:

* Tüm Dynamics AX ortamınızı (Web ve AOS katmanları, veritabanı katmanları, SharePoint) Azure 'a veya ikincil bir siteye çoğaltmayı düzenleme.
* Dynamics AX dağıtımlarının buluta geçişini basitleştirecek (Azure).
* Test ve hata ayıklama için isteğe bağlı üretim benzeri bir kopya oluşturarak Dynamics AX uygulama geliştirme ve testini basitleştirir.

Dinamik AX 'i koruma hakkında [daha fazla bilgi edinin](site-recovery-dynamicsax.md) .

## <a name="protect-rds"></a>RDS 'yi koruma
Uzak Masaüstü Hizmetleri (RDS), sanal masaüstü altyapısı (VDı), oturum tabanlı masaüstleri ve uygulamalar sunarak kullanıcıların her yerde çalışmasına izin verir. Azure Site Recovery ile şunları yapabilirsiniz:

* Yönetilen veya yönetilmeyen havuza alınmış sanal masaüstlerini ikincil bir siteye ve uzak uygulamaları ve oturumları ikincil bir siteye veya Azure 'a çoğaltın.

* Yineleyebilir şunları yapabilirsiniz:

| **RDS** |**Azure VM 'lerini Azure 'a çoğaltma** | **Hyper-V VM 'lerini ikincil bir siteye çoğaltma** | **Hyper-V VM 'lerini Azure 'a çoğaltma** | **VMware VM 'lerini ikincil bir siteye çoğaltma** | **VMware VM 'lerini Azure 'a çoğaltma** | **Fiziksel sunucuları ikincil bir siteye çoğaltma** | **Fiziksel sunucuları Azure 'a çoğaltma** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Havuza alınmış sanal masaüstü (yönetilmeyen)** |Hayır|Evet |Hayır |Evet |Hayır |Evet |Hayır |
| **Havuza alınmış sanal masaüstü (yönetilen ve UPD 'siz)** |Hayır|Evet |Hayır |Evet |Hayır |Evet |Hayır |
| **Uzak uygulamalar ve masaüstü oturumları (UPD olmadan)** |Evet|Evet |Evet |Evet |Evet |Evet |Evet |

[Azure Site Recovery kullanarak RDS için olağanüstü durum kurtarmayı ayarlayın](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure).

RDS 'yi koruma hakkında [daha fazla bilgi edinin](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) .

## <a name="protect-exchange"></a>Exchange 'i koruma
Site Recovery, Exchange 'i şu şekilde korumanıza yardımcı olur:

* Tek veya tek başına sunucu gibi küçük Exchange dağıtımları için Site Recovery Azure 'a veya ikincil bir siteye çoğaltabilir ve yük devredebilirler.
* Daha büyük dağıtımlar için Site Recovery Exchange DAG 'leri ile tümleşir.
* Exchange DAG 'leri, bir kuruluşta Exchange olağanüstü durum kurtarma için önerilen çözümdür.  Site Recovery kurtarma planları, siteler arasında DAG yük devretmesini düzenlemek için dag 'ler içerebilir.

Exchange 'i koruma hakkında [daha fazla bilgi edinin](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) .

## <a name="protect-sap"></a>SAP 'yi koruma
SAP dağıtımınızı şu şekilde korumak için Site Recovery kullanın:

* Bileşenleri Azure 'a çoğaltarak şirket içinde çalışan SAP NetWeaver ve NetWeaver olmayan üretim uygulamalarının korumasını etkinleştirin.
* Azure çalıştıran SAP NetWeaver ve NetWeaver olmayan üretim uygulamalarının korunmasını, bileşenleri başka bir Azure veri merkezine çoğaltarak etkinleştirin.
* SAP dağıtımınızı Azure 'a geçirmek için Site Recovery kullanarak bulut geçişini kolaylaştırın.
* SAP uygulamalarını test etmek için isteğe bağlı olarak bir üretim kopyası oluşturarak SAP Proje yükseltmeleri, test ve prototip oluşturma işlemlerini kolaylaştırın.

SAP 'yi koruma hakkında [daha fazla bilgi edinin](site-recovery-sap.md) .

## <a name="protect-iis"></a>IIS 'yi koruma
IIS dağıtımınızı şu şekilde korumak için Site Recovery kullanın:

Azure Site Recovery, ortamınızdaki kritik bileşenleri soğuk uzak bir siteye veya Microsoft Azure gibi bir genel buluta çoğaltarak olağanüstü durum kurtarma sağlar. Web sunucusu ve veritabanı ile sanal makineler kurtarma sitesine çoğaltıldıklarından, yapılandırma dosyalarını veya sertifikaları ayrı olarak yedekleme gereksinimi yoktur. Yük devretme sonrası değiştirilen ortam değişkenlerine bağımlı uygulama eşlemeleri ve bağlamaları, olağanüstü durum kurtarma planlarına tümleştirilmiş betikler aracılığıyla güncelleştirilebilirler. Sanal makineler yalnızca yük devretme durumunda kurtarma sitesine getirilir. Yalnızca bu Azure Site Recovery, aşağıdaki özellikleri sağlayarak uçtan uca yük devretmeyi düzenlemenize da yardımcı olur:

-   Farklı katmanlardaki sanal makinelerin kapatılmasını ve başlatılmasını sıralama.
-   Sanal makinelerde başlatıldıktan sonra uygulama bağımlılıklarının ve bağlamaların güncelleştirilmesini sağlamak için betikler ekleme. Betikler, DNS sunucusunu kurtarma sitesini işaret etmek üzere güncelleştirmek için de kullanılabilir.
-   Birincil ve kurtarma ağlarını eşleyerek ve bu nedenle yük devretmenin güncelleştirilmesini gerektirmeyen betikleri kullanarak sanal makinelere IP adreslerini önceden yük devretmeye ayırın.
-   Web sunucularındaki birden çok Web uygulaması için tek tıklamayla yük devretme yeteneği, bu sayede bir olağanüstü durum durumunda karışıklık için kapsamı ortadan kaldırır.
-   Kurtarma planlarını, DR ayrıntılarına ls için yalıtılmış bir ortamda test etme yeteneği.

IIS Web grubunu koruma hakkında [daha fazla bilgi edinin](https://aka.ms/asr-iis) .

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Citrix XenApp ve XenDesktop koruma
Citrix XenApp ve XenDesktop dağıtımlarınızı korumak için aşağıdaki gibi Site Recovery kullanın:

* Citrix XenApp ve XenDesktop dağıtımının korunmasını etkinleştirerek, (AD DNS sunucusu, SQL veritabanı sunucusu, Citrix Delivery Controller, StoreFront Server, XenApp Master (VDA), Citrix XenApp lisans sunucusu) gibi farklı dağıtım katmanlarını çoğaltarak Mavisi.
* Citrix XenApp ve XenDesktop dağıtımınızı Azure 'a geçirmek için Site Recovery kullanarak bulut geçişini kolaylaştırın.
* Test ve hata ayıklama için isteğe bağlı üretim benzeri bir kopya oluşturarak Citrix XenApp/XenDesktop testini kolaylaştırın.
* Bu çözüm yalnızca, istemci sanal masaüstlerini Azure 'da lisanslama için henüz desteklenmediğinden, istemci sanal masaüstleri için değil, yalnızca Windows Server işletim sistemi sanal masaüstleri için geçerlidir.
Azure 'da istemci/sunucu masaüstleri için lisanslama hakkında [daha fazla bilgi edinin](https://azure.microsoft.com/pricing/licensing-faq/) .

Citrix XenApp ve XenDesktop dağıtımlarını koruma hakkında [daha fazla bilgi edinin](site-recovery-citrix-xenapp-and-xendesktop.md) . Alternatif olarak, [Citrix ile ayrıntılandıran teknik incelemeye](https://aka.ms/citrix-xenapp-xendesktop-with-asr) de başvurabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure VM çoğaltma ile [çalışmaya](azure-to-azure-quickstart.md) başlayın.
