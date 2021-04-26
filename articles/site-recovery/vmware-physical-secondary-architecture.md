---
title: Mimari-Azure Site Recovery ile ikincil bir siteye VMware/fiziksel olağanüstü durum kurtarma
description: Bu makalede, şirket içi VMware VM 'lerinin veya fiziksel Windows/Linux sunucularının, Azure Site Recovery ile ikincil bir VMware sitesine olağanüstü durum kurtarması sırasında kullanılan bileşenlere ve mimariye ilişkin genel bir bakış sunulmaktadır.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 032f8619a1f8d4299af1eb069fdccba1307542d4
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579505"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>İkincil şirket içi siteye VMware/fiziksel sunucu çoğaltması için mimari

Bu makalede, şirket içi VMware sanal makinelerinin (VM 'Ler) veya fiziksel Windows/Linux sunucularının [Azure Site Recovery](site-recovery-overview.md)kullanarak Ikincil bir VMware sitesine olağanüstü durum kurtarma çoğaltması, yük devretme ve kurtarma kurulumu sırasında kullanılan mimari ve süreçler açıklanmaktadır.


## <a name="architectural-components"></a>Mimari bileşenler

**Alan** | **Bileşen** | **Ayrıntılar**
--- | --- | ---
**Azure** | Bu senaryoyu, InMage Scout kullanarak dağıtırsınız. | InMage Scout elde etmek için bir Azure aboneliğine sahip olmanız gerekir.<br/><br/> Bir Kurtarma Hizmetleri kasası oluşturduktan sonra InMage Scout hizmetini indirip dağıtımı ayarlamak üzere en son güncelleştirmeleri yüklersiniz.
**İşlem sunucusu** | Birincil sitede bulunur | Önbelleğe alma, sıkıştırma ve veri iyileştirme işlemlerini yürütmek için işlem sunucusunu dağıtırsınız.<br/><br/> Ayrıca bu sunucu, Birleşik Aracı'nın korumak istediğiniz makinelere göndermeli yükleme işlemini yürütür.
**Yapılandırma sunucusu** | İkincil sitede bulunur | Yapılandırma sunucusu, yönetim Web sitesini veya vContinuum konsolunu kullanarak dağıtımınızı yönetir, yapılandırır ve izler.
**vContinuum sunucusu** | İsteğe bağlı. Yapılandırma sunucusuyla aynı konuma yüklenir. | Korunan ortamınızı yönetmeye ve izlemeye yönelik bir konsol sağlar.
**Ana hedef sunucu** | İkincil sitede bulunur | Ana hedef sunucu çoğaltılan verileri tutar. İşlem sunucusundan verileri alır, ikincil sitede çoğaltılan bir makine oluşturur ve veri bekletme noktalarını tutar.<br/><br/> İhtiyacınız olan ana hedef sunucusu sayısı koruduğunuz makine sayısına bağlıdır.<br/><br/> Birincil sitede yeniden çalıştırmak isterseniz burada da bir ana hedef sunucusuna sahip olmanız gerekir. Birleşik Aracı bu sunucuya yüklenir.
**VMware ESX/ESXi ve vCenter sunucusu** |  VM’ler ESX/ESXi ana bilgisayarlarında barındırılır. Ana bilgisayarlar bir vCenter sunucusu ile yönetilir | VMware VM’lerini çoğaltmak için bir VMware altyapınız olmalıdır.
**VM’ler/fiziksel sunucular** |  Çoğaltmak istediğiniz VMware VM’leri veya fiziksel sunucularda yüklü Birleşik Aracı. | Aracı,tüm bileşenler arasındaki bir iletişim sağlayıcısı gibi davranır.

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

1. Her sitede bileşen sunucularını (yapılandırma, işlem, ana hedef) ayarlayıp çoğaltmak istediğiniz makinelere Birleşik Aracı'yı yükleyin.
2. İlk çoğaltmanın ardından makinelerdeki aracılar çoğaltma değişimleri işlem sunucusuna gönderir.
3. İşlem sunucusu verileri iyileştirir ve ikincil sitedeki ana hedef sunucusuna aktarır. Yapılandırma sunucusu çoğaltma sürecini yönetir.

![VMware VM 'lerinin ve fiziksel sunucuların ikincil veri merkezinde çoğaltılmasını gösteren diyagram](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Sonraki adımlar

VMware VM 'Leri ve fiziksel sunucuları ikincil bir siteye olağanüstü durum kurtarmayı [ayarlayın](vmware-physical-secondary-disaster-recovery.md) .
