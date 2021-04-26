---
title: Azure Site Recovery 'de fiziksel sunucu olağanüstü durum kurtarma mimarisi
description: Bu makalede, şirket içi fiziksel sunucuların olağanüstü durum kurtarması sırasında Azure Site Recovery hizmetiyle Azure 'da kullanılan bileşenlere ve mimariye ilişkin bir genel bakış sunulmaktadır.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 966636e269043d81912b552711635f34b412f22f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654736"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Fiziksel sunucudan Azure'a olağanüstü durum kurtarma mimarisi

Bu makalede, [Azure Site Recovery](site-recovery-overview.md) hizmetini kullanarak şirket içi bir site ile Azure arasında fiziksel Windows ve Linux sunucularını çoğalttığınızda, yük devretmek ve kurtardığınızda kullanılan mimari ve süreçler açıklanmaktadır.

## <a name="architectural-components"></a>Mimari bileşenler

Aşağıdaki tablo ve grafik, Azure 'a fiziksel sunucu çoğaltması için kullanılan bileşenlerin üst düzey bir görünümünü sağlar.

| **Bileşen** | **Gereksinim** | **Ayrıntılar** |
| --- | --- | --- |
| **Azure** | Bir Azure aboneliği ve bir Azure ağı. | Şirket içi fiziksel makinelerden çoğaltılan veriler Azure yönetilen disklerinde depolanır. Şirket içinden Azure 'a yük devretme gerçekleştirdiğinizde, çoğaltılan verilerle Azure VM 'Ler oluşturulur. Azure VM’leri oluşturulduğunda Azure sanal ağına bağlanır. |
| **İşlem sunucusu** | Yapılandırma sunucusu ile birlikte varsayılan olarak yüklenir. | Çoğaltma ağ geçidi olarak davranır. Çoğaltma verilerini alıp bu verileri önbelleğe alma, sıkıştırma ve şifreleme işlemleriyle iyileştirir ve Azure depolama alanına gönderir.<br/><br/> İşlem sunucusu Ayrıca, çoğaltmak istediğiniz sunuculara Mobility hizmetini de yüklüyor.<br/><br/> Dağıtımınız büyüdükçe, daha büyük hacimli çoğaltma trafiğini işlemek için ek ve ayrı işlem sunucuları ekleyebilirsiniz. |
| **Ana hedef sunucu** | Yapılandırma sunucusu ile birlikte varsayılan olarak yüklenir. | Azure 'dan geri dönme sırasında çoğaltma verilerini işler.<br/><br/> Büyük dağıtımlar için yeniden çalışma için ek ve ayrı bir ana hedef sunucu ekleyebilirsiniz. |
| **Çoğaltılan sunucular** | Mobility hizmeti, çoğaltılan her bir sunucuya yüklenir. | İşlem sunucusundan otomatik yüklemeye izin vermeniz önerilir. Ya da hizmeti el ile yükleyebilir veya Configuration Manager gibi bir otomatik dağıtım yöntemi kullanabilirsiniz. |

**Fiziksel sunucu-Azure arası mimari**

![Bileşenler](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Giden ağ bağlantısını ayarlama

Site Recovery beklendiği gibi çalışması için, ortamınızın çoğaltılmasını sağlamak üzere giden ağ bağlantısını değiştirmeniz gerekir.

> [!NOTE]
> Site Recovery, ağ bağlantısını denetlemek için bir kimlik doğrulama proxy 'si kullanmayı desteklemez.

### <a name="outbound-connectivity-for-urls"></a>URL'ler için giden bağlantı

Giden bağlantıyı denetlemek için URL tabanlı bir güvenlik duvarı proxy 'si kullanıyorsanız, bu URL 'Lere erişim izni verin:

| **Ad**                  | **Ticari**                               | **Kamu**                                 | **Açıklama** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Depolama                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Verilerin VM’den kaynak bölgedeki önbellek depolama hesabına yazılmasına izin verir. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Site Recovery hizmet URL’leri için yetkilendirme ve kimlik doğrulama özellikleri sağlar. |
| Çoğaltma               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | VM’nin Site Recovery hizmetiyle iletişim kurmasına izin verir. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | VM’nin Site Recovery izleme ve tanılama verilerini yazmasına izin verir. |


## <a name="replication-process"></a>Çoğaltma işlemi

1. Dağıtım, şirket içi ve Azure bileşenleri dahil olmak üzere ayarlanır. Kurtarma Hizmetleri kasasında çoğaltma kaynağını ve hedefini belirtin, yapılandırma sunucusunu ayarlayın, bir çoğaltma ilkesi oluşturun ve çoğaltmayı etkinleştirin.
1. Makineler çoğaltma ilkesi kullanılarak çoğaltılır ve sunucu verilerinin ilk kopyası Azure Storage 'a çoğaltılır.
1. İlk çoğaltma tamamlandıktan sonra, Delta değişikliklerinin Azure 'a çoğaltılması başlar. Bir makine için izlenen değişiklikler _. HRL_ uzantılı bir dosyada tutulur.
   - Makineler, çoğaltma yönetimi için HTTPS bağlantı noktası 443 gelen yapılandırma sunucusuyla iletişim kurar.
   - Makineler, çoğaltma verilerini HTTPS bağlantı noktası 9443 gelen (değiştirilebilir) üzerinde işlem sunucusuna gönderir.
   - Yapılandırma sunucusu, Azure ile çoğaltma yönetimini HTTPS bağlantı noktası 443 giden üzerinden düzenler.
   - İşlem sunucusu kaynak makinelerden veri alır, bunları iyileştirir ve şifreler ve HTTPS bağlantı noktası 443 giden üzerinden Azure depolama 'ya gönderir.
   - Çoklu VM tutarlılığını etkinleştirirseniz çoğaltma grubundaki makineler birbiriyle 20004 numaralı bağlantı noktası üzerinden iletişim kurar. Çoklu VM, birden çok makineyi yük devrettikleri zaman kilitlenmeyle tutarlı ve uygulamayla tutarlı kurtarma noktalarını paylaşan çoğaltma grupları halinde gruplandırdığınızda kullanılır. Bu gruplar, makineler aynı iş yükünü çalıştırıyorsa ve tutarlı olması gerekiyorsa yararlıdır.
1. Trafik İnternet üzerinden Azure depolama genel uç noktalarına çoğaltılır. Alternatif olarak, Azure ExpressRoute [genel eşliğini](../expressroute/about-public-peering.md) kullanabilirsiniz.

   > [!NOTE]
   > Çoğaltma, şirket içi bir siteden siteye VPN üzerinden veya Azure ExpressRoute [özel eşlemeden](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)desteklenmez.

**Fizikselden Azure 'a çoğaltma işlemi**

![Çoğaltma işlemi](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Yük devretme ve yeniden çalışma işlemi

Çoğaltma kurulduktan sonra her şeyin beklendiği gibi çalışıp çalışmadığını denetlemek için bir olağanüstü durum kurtarma detayına (yük devretme testi) çalıştırabilirsiniz. Ardından, gerektiğinde yük devreder ve yeniden yük devreolursunuz. Aşağıdaki öğeleri göz önünde bulundurun:

- Planlanan yük devretme desteklenmez.
- Şirket içi VMware VM 'sine geri dönme gerekir. Şirket içi fiziksel sunucuları Azure 'a çoğalttığınızda bile, şirket içi bir VMware altyapısına ihtiyacınız vardır.
- Birden fazla makinenin yükünü birlikte devretmek için tek bir makine üzerinden yük devretmek veya kurtarma planları oluşturmanız gerekir.
- Yük devretme çalıştırdığınızda Azure sanal makineleri, Azure depolama 'daki çoğaltılan verilerden oluşturulur.
- İlk yük devretme tetiklendikten sonra, Azure VM 'den iş yüküne erişmeye başlamak için bunu yürütün.
- Birincil şirket içi siteniz yeniden kullanılabilir olduğunda siteyi yeniden çalıştırabilirsiniz.
- Şunları içeren bir yeniden çalışma altyapısı ayarlayın:
  - **Azure 'Da geçici işlem sunucusu**: Azure 'dan yeniden yük devretmek Için BIR Azure VM 'yi işlem sunucusu olarak davranacak şekilde ayarlayarak Azure 'dan çoğaltmayı işleyebilirsiniz. Yük devretme tamamlandıktan sonra bu VM 'yi silebilirsiniz.
  - **VPN bağlantısı**: geri dönmek Için, Azure ağından şirket içi sıteye bir VPN bağlantısına (veya Azure ExpressRoute) sahip olmanız gerekir.
  - **Ana hedef sunucuyu ayır**: varsayılan olarak, yük devretme işlemi, şirket ıçı VMware VM üzerinde yapılandırma sunucusu ile yüklenen ana hedef sunucu tarafından işlenir. Büyük hacimlerde trafik geri yüklemeniz gerekiyorsa, ayrı bir şirket içi ana hedef sunucusu ayarlamanız gerekir.
  - **Yeniden çalışma ilkesi**: Şirket içi sitenize geri çoğaltmak için bir yeniden çalışma ilkeniz olmalıdır. İlke, Şirket içinden Azure 'a çoğaltma ilkenizi oluşturduğunuzda otomatik olarak oluşturulmuştur.
  - **VMware altyapısı**: yeniden yük devretmek Için bir VMware altyapısına ihtiyacınız vardır. Bir fiziksel sunucuda yeniden çalışamazsınız.
- Bileşenler olduktan sonra geri dönme üç aşamada gerçekleşir:
  - **1. aşama**: Azure 'dan şirket Içi VMware VM 'lerine geri Çoğaltılbilmeleri Için Azure VM 'lerini yeniden koruyun.
  - **2. aşama**: şirket içi sitede yük devretme çalıştırın.
  - **3. aşama**: iş yükleri yeniden başlatıldıktan sonra çoğaltmayı yeniden etkinleştirin.

**Azure 'dan VMware yeniden çalışma**

![Yeniden çalışma](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Sonraki adımlar

Fiziksel sunucular için olağanüstü durum kurtarmayı Azure 'a ayarlamak için bkz. [nasıl yapılır Kılavuzu](physical-azure-disaster-recovery.md).
