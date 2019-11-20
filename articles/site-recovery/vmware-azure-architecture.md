---
title: Azure Site Recovery 'de VMware VM olağanüstü durum kurtarma mimarisi
description: Bu makalede, Azure Site Recovery ile şirket içi VMware VM 'lerinin olağanüstü durum kurtarması ayarlanırken kullanılan bileşenlere ve mimariye ilişkin genel bir bakış sunulmaktadır
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 8bfbc6783df4f902d25b2a4791708990a327edc8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663058"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware 'den Azure 'a olağanüstü durum kurtarma mimarisi

Bu makalede, [Azure Site Recovery](site-recovery-overview.md) hizmetini kullanarak şirket Içi bir VMware sitesi ve Azure arasında VMware sanal makinelerini (VM 'ler) kullanarak bir olağanüstü durum kurtarma çoğaltması, yük devretme ve kurtarma işlemi gerçekleştirdiğinizde kullanılan mimari ve süreçler açıklanmaktadır.


## <a name="architectural-components"></a>Mimari bileşenler

Aşağıdaki tablo ve grafik, Azure 'da VMware olağanüstü durum kurtarma için kullanılan bileşenlerin üst düzey bir görünümünü sağlar.

**Bileşen** | **Gereksinim** | **Ayrıntılar**
--- | --- | ---
**Azure** | Azure aboneliği, önbellek, yönetilen disk ve Azure ağı için Azure depolama hesabı. | Şirket içi VM 'lerden çoğaltılan veriler Azure depolama 'da depolanır. Şirket içinden Azure 'a yük devretme gerçekleştirdiğinizde, çoğaltılan verilerle Azure VM 'Ler oluşturulur. Azure VM’leri oluşturulduğunda Azure sanal ağına bağlanır.
**Yapılandırma sunucusu makinesi** | Tek bir şirket içi makine. Bunu, indirilen bir OVF şablonundan dağıtılabilecek bir VMware VM olarak çalıştırmanızı öneririz.<br/><br/> Makine, yapılandırma sunucusu, işlem sunucusu ve ana hedef sunucu dahil olmak üzere tüm şirket içi Site Recovery bileşenlerini çalıştırır. | **Yapılandırma sunucusu**: Şirket Içi ve Azure arasındaki iletişimleri koordine eder ve veri çoğaltmasını yönetir.<br/><br/> **İşlem sunucusu**: yapılandırma sunucusunda varsayılan olarak yüklenir. Çoğaltma verilerini alır; önbelleğe alma, sıkıştırma ve şifreleme ile en iyi duruma getirir; ve bunu Azure depolama 'ya gönderir. İşlem sunucusu aynı zamanda çoğaltmak istediğiniz VM’lere Azure Site Recovery Mobility Hizmetini yükler ve şirket içi makinelerinin otomatik olarak bulunmasını sağlar. Dağıtımınız büyüdükçe, daha büyük hacimli çoğaltma trafiğini işlemek için ek ve ayrı işlem sunucuları ekleyebilirsiniz.<br/><br/> **Ana hedef sunucusu**: yapılandırma sunucusunda varsayılan olarak yüklenir. Azure 'dan yeniden çalışma sırasında çoğaltma verilerini işler. Büyük dağıtımlar için yeniden çalışma için ek ve ayrı bir ana hedef sunucu ekleyebilirsiniz.
**VMware sunucuları** | VMware VM 'Leri, şirket içi vSphere ESXi sunucularında barındırılır. Konakları yönetmek için bir vCenter sunucusu önerilir. | Site Recovery dağıtımı sırasında, kurtarma hizmetleri kasasına VMware sunucuları eklersiniz.
**Çoğaltılan makineler** | Mobility hizmeti, çoğaltılan her VMware VM 'ye yüklenir. | İşlem sunucusundan otomatik yüklemeye izin vermeniz önerilir. Alternatif olarak, hizmeti el ile yükleyebilir veya System Center Configuration Manager gibi bir otomatik dağıtım yöntemi kullanabilirsiniz.

**VMware-Azure arası mimari**

![Bileşenler](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Çoğaltma işlemi

1. Bir sanal makine için çoğaltmayı etkinleştirdiğinizde, Azure Storage 'a ilk çoğaltma, belirtilen çoğaltma ilkesi kullanılarak başlar. Şunlara dikkat edin:
    - VMware VM 'Leri için çoğaltma, sanal makine üzerinde çalışan Mobility hizmeti Aracısı kullanılarak, blok düzeyinde, neredeyse sürekli çalışır.
    - Herhangi bir çoğaltma ilkesi ayarı uygulanır:
        - **RPO eşiği**. Bu ayar çoğaltmayı etkilemez. İzlemeye yardımcı olur. Geçerli RPO belirttiğiniz eşik sınırını aşarsa bir olay oluşur ve isteğe bağlı olarak bir e-posta gönderilir.
        - **Kurtarma noktası bekletme**. Bu ayar, bir kesinti oluştuğunda ne kadar süre geri dönmek istediğinizi belirtir. Premium depolamada maksimum bekletme 24 saattir. Standart depolamada 72 saat olur. 
        - **Uygulamayla tutarlı anlık görüntüler**. Uygulama gereksinimlerinize bağlı olarak, uygulamayla tutarlı anlık görüntü her 1 ile 12 saat arasında sürebilir. Anlık görüntüler standart Azure Blob anlık görüntüleridir. Bir VM üzerinde çalışan Mobility Aracısı, bu ayara uygun olarak bir VSS anlık görüntüsü ister ve o zaman, çoğaltma akışında uygulamayla tutarlı bir nokta olarak zaman içinde yer alır.

2. Trafik, internet üzerinden Azure depolama genel uç noktalarına çoğaltılır. Alternatif olarak, Azure ExpressRoute 'u [Microsoft eşlemesi](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)ile de kullanabilirsiniz. Şirket içi siteden Azure 'a siteden siteye sanal özel ağ (VPN) üzerinden trafik çoğaltma desteklenmez.
3. İlk çoğaltma tamamlandıktan sonra, Delta değişikliklerinin Azure 'a çoğaltılması başlar. Bir makine için izlenen değişiklikler işlem sunucusuna gönderilir.
4. İletişim şu şekilde gerçekleşir:

    - VM 'Ler, çoğaltma yönetimi için HTTPS 443 gelen bağlantı noktasında şirket içi yapılandırma sunucusu ile iletişim kurar.
    - Yapılandırma sunucusu, HTTPS 443 giden bağlantı noktası üzerinden Azure ile çoğaltmayı düzenler.
    - VM 'Ler, çoğaltma verilerini işlem sunucusuna (yapılandırma sunucusu makinesinde çalışan), HTTPS 9443 gelen bağlantı noktası üzerinden gönderir. Bu bağlantı noktası değiştirilebilir.
    - İşlem sunucusu çoğaltma verilerini alır, bunları iyileştirir ve şifreler ve 443 giden bağlantı noktası üzerinden Azure depolama 'ya gönderir.
5. Çoğaltma verileri günlüğü, ilk olarak Azure 'da bir önbellek depolama hesabında yer açar. Bu Günlükler işlenir ve veriler bir Azure yönetilen diskinde depolanır (ASR çekirdek diski olarak adlandırılır). Kurtarma noktaları bu diskte oluşturulur.




**VMware 'den Azure 'a çoğaltma işlemi**

![Çoğaltma işlemi](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Yük devretme ve yeniden çalışma işlemi

Çoğaltma kurulduktan sonra ve her şeyin beklendiği gibi çalıştığını denetlemek için bir olağanüstü durum kurtarma detayına (yük devretme testi) çalıştırmanızın ardından, gerektiğinde yük devretme ve yeniden çalışma işlemi gerçekleştirebilirsiniz.

1. Tek bir makine için başarısız olarak çalıştırın veya aynı anda birden fazla VM 'nin yükünü devretmek için bir kurtarma planı oluşturun. Tek makineli yük devretme yerine kurtarma planının avantajı şunlardır:
    - Tek bir kurtarma planında uygulama genelinde tüm VM 'Leri dahil ederek uygulama bağımlılıklarını modelleyebilirsiniz.
    - El ile gerçekleştirilen eylemler için betikler, Azure runbook 'lar ekleyebilir ve duraklatabilirsiniz.
2. İlk yük devretmeyi tetikledikten sonra, Azure VM 'den iş yüküne erişmeye başlamak için bunu işleyin.
3. Birincil şirket içi siteniz yeniden kullanılabilir olduğunda, tekrar yük devretmek için hazırlık yapabilirsiniz. Yeniden yük devretmek için aşağıdakiler de dahil olmak üzere bir yeniden çalışma altyapısı ayarlamanız gerekir:

    * **Azure 'Da geçici işlem sunucusu**: Azure 'dan yeniden yük devretmek Için BIR Azure VM 'yi, Azure 'dan çoğaltmayı işleyecek bir işlem sunucusu olarak davranacak şekilde ayarlarsınız. Yeniden çalışma sona erdikten sonra bu VM'yi silebilirsiniz.
    * **VPN bağlantısı**: geri dönmek Için, Azure ağından şirket içi sıteye bir VPN bağlantısına (veya ExpressRoute) sahip olmanız gerekir.
    * **Ayrı ana hedef sunucusu**: varsayılan olarak, şirket ıçı VMware VM 'de yapılandırma sunucusu ile yüklenen ana hedef sunucu yeniden çalışmayı işler. Büyük hacimlerde trafik geri yüklemeniz gerekiyorsa, bu amaçla ayrı bir şirket içi ana hedef sunucusu ayarlayın.
    * **Yeniden çalışma ilkesi**: Şirket içi sitenize geri çoğaltmak için bir yeniden çalışma ilkeniz olmalıdır. Bu ilke, Şirket içinden Azure 'a bir çoğaltma ilkesi oluşturduğunuzda otomatik olarak oluşturulur.
4. Bileşenler oluşturulduktan sonra, yeniden çalışma üç eylem halinde gerçekleşir:

    - 1\. Aşama: Azure 'dan şirket içi VMware VM 'lerine geri çoğaltılbilmeleri için Azure VM 'lerini yeniden koruyun.
    -  2\. Aşama: şirket içi sitede yük devretme çalıştırın.
    - 3\. Aşama: iş yükleri yeniden başlatıldıktan sonra, şirket içi VM 'Ler için çoğaltmayı yeniden etkinleştirin.
    
 
**Azure 'dan VMware yeniden çalışma**

![Yeniden çalışma](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Sonraki adımlar

VMware 'den Azure 'a çoğaltma özelliğini etkinleştirmek için [Bu öğreticiyi](vmware-azure-tutorial.md) izleyin.
