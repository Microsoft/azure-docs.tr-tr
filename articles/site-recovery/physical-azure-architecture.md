---
title: Azure Site Recovery 'de fiziksel sunucu olağanüstü durum kurtarma mimarisi
description: Bu makalede, şirket içi fiziksel sunucuların olağanüstü durum kurtarması sırasında Azure Site Recovery hizmetiyle Azure 'da kullanılan bileşenlere ve mimariye ilişkin bir genel bakış sunulmaktadır.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 23e8e4f9a092e871e62da27c8bf0c58a3bb8eb5b
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084681"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Fiziksel sunucudan Azure olağanüstü durum kurtarma mimarisi

Bu makalede, [Azure Site Recovery](site-recovery-overview.md) hizmetini kullanarak şirket içi bir site ile Azure arasında fiziksel Windows ve Linux sunucularını çoğalttığınızda, yük devretmek ve kurtardığınızda kullanılan mimari ve süreçler açıklanmaktadır.


## <a name="architectural-components"></a>Mimari bileşenler

Aşağıdaki tablo ve grafik, Azure 'a fiziksel sunucu çoğaltması için kullanılan bileşenlerin üst düzey bir görünümünü sağlar.  

**Bileşen** | **Gereksinim** | **Ayrıntılar**
--- | --- | ---
**Azure** | Bir Azure aboneliği ve bir Azure ağı. | Şirket içi fiziksel makinelerden çoğaltılan veriler Azure yönetilen disklerinde depolanır. Şirket içinden Azure 'a yük devretme gerçekleştirdiğinizde, çoğaltılan verilerle Azure VM 'Ler oluşturulur. Azure VM’leri oluşturulduğunda Azure sanal ağına bağlanır.
**Yapılandırma sunucusu** | Şirket içi Site Recovery bileşenlerini çalıştırmak için tek bir şirket içi fiziksel makine veya VMware VM dağıtılır. VM, yapılandırma sunucusunu, işlem sunucusunu ve ana hedef sunucuyu çalıştırır. | Yapılandırma sunucusu yerinde bileşenler ile Azure arasındaki iletişimi düzenler ve veri çoğaltma işlemlerini yönetir.
 **İşlem sunucusu**:  | Yapılandırma sunucusu ile birlikte varsayılan olarak yüklenir. | Çoğaltma ağ geçidi olarak davranır. Çoğaltma verilerini alıp bu verileri önbelleğe alma, sıkıştırma ve şifreleme işlemleriyle iyileştirir ve Azure depolama alanına gönderir.<br/><br/> İşlem sunucusu Ayrıca, çoğaltmak istediğiniz sunuculara Mobility hizmetini de yüklüyor.<br/><br/> Dağıtımınız büyüdükçe, daha büyük hacimli çoğaltma trafiğini işlemek için ek ve ayrı işlem sunucuları ekleyebilirsiniz.
 **Ana hedef sunucu** | Yapılandırma sunucusu ile birlikte varsayılan olarak yüklenir. | Azure’dan yeniden çalışma sırasında çoğaltma verilerini işler.<br/><br/> Büyük dağıtımlar için yeniden çalışma için ek ve ayrı bir ana hedef sunucu ekleyebilirsiniz.
**Çoğaltılan sunucular** | Mobility hizmeti, çoğaltılan her bir sunucuya yüklenir. | İşlem sunucusundan otomatik yüklemeye izin vermeniz önerilir. Alternatif olarak, hizmeti el ile yükleyebilir veya System Center Configuration Manager gibi otomatikleştirilmiş bir dağıtım yöntemi kullanabilirsiniz.

**Fizikselden Azure mimarisine**

![Bileşenler](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Çoğaltma işlemi

1. Dağıtım, şirket içi ve Azure bileşenleri dahil olmak üzere ayarlanır. Kurtarma Hizmetleri kasasında çoğaltma kaynağını ve hedefini belirtin, yapılandırma sunucusunu ayarlayın, bir çoğaltma ilkesi oluşturun ve çoğaltmayı etkinleştirin.
2. Makineler, çoğaltma ilkesiyle uyumlu olarak çoğaltılır ve sunucu verilerinin ilk kopyası Azure Storage 'a çoğaltılır.
3. İlk çoğaltma tamamlandıktan sonra, Delta değişikliklerinin Azure 'a çoğaltılması başlar. Bir makine için izlenen değişiklikler bir .hrl dosyasında saklanır.
    - Makineler, çoğaltma yönetimi için HTTPS 443 gelen bağlantı noktasında yapılandırma sunucusuyla iletişim kurar.
    - Makineler, çoğaltma verilerini, HTTPS 9443 gelen bağlantı noktasında işlem sunucusuna gönderir (değiştirilebilir).
    - Yapılandırma sunucusu, HTTPS 443 giden bağlantı noktası üzerinden Azure ile çoğaltma yönetimini düzenler.
    - İşlem sunucusu kaynak makinelerden gelen verileri alır, iyileştirip şifreler ve 443 giden bağlantı noktası üzerinden Azure depolamaya gönderir.
    - Çoklu VM tutarlılığını etkinleştirirseniz çoğaltma grubundaki makineler birbiriyle 20004 numaralı bağlantı noktası üzerinden iletişim kurar. Çoklu VM, birden çok makineyi yük devrettikleri zaman kilitlenmeyle tutarlı ve uygulamayla tutarlı kurtarma noktalarını paylaşan çoğaltma grupları halinde gruplandırdığınızda kullanılır. Bu özellik, makinelerin aynı iş yükünü çalıştırdığı ve tutarlı olmasının gerektiği durumlarda kullanışlıdır.
4. Trafik İnternet üzerinden Azure depolama genel uç noktalarına çoğaltılır. Alternatif olarak, Azure ExpressRoute [genel eşliğini](../expressroute/expressroute-circuit-peerings.md#publicpeering) kullanabilirsiniz. Trafiğin siteden siteye bir VPN aracılığıyla şirket içi bir siteden Azure’a çoğaltılması desteklenmez.


**Fizikselden Azure 'a çoğaltma işlemi**

![Çoğaltma işlemi](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Yük devretme ve yeniden çalışma işlemi

Çoğaltma kurulduktan sonra ve her şeyin beklendiği gibi çalıştığından emin olmak için bir olağanüstü durum kurtarma detayına (yük devretme testi) çalıştırmanızın ardından, gerektiğinde yük devretme ve yeniden çalışma işlemi gerçekleştirebilirsiniz. Şunlara dikkat edin:

- Planlanan yük devretme desteklenmez.
- Şirket içi bir VMware VM 'sine yeniden yük devredebilmeniz gerekir. Bu, şirket içi fiziksel sunucuları Azure 'a çoğalttığınızda bile, şirket içi bir VMware altyapısına ihtiyacınız olduğu anlamına gelir.
- Birden fazla makinenin yükünü birlikte devretmek için tek bir makine üzerinden yük devretmek veya kurtarma planları oluşturmanız gerekir.
- Yük devretme çalıştırdığınızda Azure sanal makineleri, Azure depolama 'daki çoğaltılan verilerden oluşturulur.
- İlk yük devretmeyi tetikledikten sonra, Azure VM 'den iş yüküne erişmeye başlamak için bunu işleyin.
- Birincil şirket içi siteniz yeniden kullanılabilir olduğunda siteyi yeniden çalıştırabilirsiniz.
- Aşağıdakiler dahil olmak üzere bir yeniden çalışma altyapısı ayarlamanız gerekir:
    - **Azure 'Da geçici işlem sunucusu**: Azure 'dan yeniden yük devretmek Için BIR Azure VM 'yi işlem sunucusu olarak davranacak şekilde ayarlayarak Azure 'dan çoğaltmayı işleyebilirsiniz. Yeniden çalışma sona erdikten sonra bu VM'yi silebilirsiniz.
    - **VPN bağlantısı**: geri dönmek Için, Azure ağından şirket içi sıteye bir VPN bağlantısına (veya Azure ExpressRoute) sahip olmanız gerekir.
    - **Ayrı ana hedef sunucusu**: varsayılan olarak, şirket ıçı VMware VM 'de yapılandırma sunucusu ile yüklenen ana hedef sunucu, yeniden çalışmayı işler. Ancak, büyük hacimli trafiği yeniden açmanız gerekiyorsa, bu amaçla ayrı bir şirket içi ana hedef sunucusu ayarlamanız gerekir.
    - **Yeniden çalışma ilkesi**: Şirket içi sitenize geri çoğaltmak için bir yeniden çalışma ilkeniz olmalıdır. Bu, Şirket içinden Azure 'a çoğaltma ilkenizi oluşturduğunuzda otomatik olarak oluşturulmuştur.
    - **VMware altyapısı**: yeniden çalışma Için bir VMware altyapısına ihtiyacınız vardır. Bir fiziksel sunucuda yeniden çalışamazsınız.
- Bileşenler oluşturulduktan sonra, yeniden çalışma üç aşamada gerçekleşir:
    - 1\. Aşama: Azure 'dan şirket içi VMware VM 'lerine geri çoğaltılbilmeleri için Azure VM 'lerini yeniden koruyun.
    - 2\. Aşama: şirket içi sitede yük devretme çalıştırın.
    - 3\. Aşama: iş yükleri yeniden başlatıldıktan sonra çoğaltmayı yeniden etkinleştirin.

**Azure 'dan VMware yeniden çalışma**

![Yeniden çalışma](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Sonraki adımlar

Fiziksel sunucuyu Azure çoğaltmaya etkinleştirmek için [Bu öğreticiyi](physical-azure-disaster-recovery.md) izleyin.
