---
title: Mimari-Azure Site Recovery bir ikincil siteye Hyper-V olağanüstü durum kurtarma
description: Bu makalede, şirket içi Hyper-V VM 'lerinin Azure Site Recovery olan ikincil bir System Center VMM sitesine olağanüstü durum kurtarma mimarisine genel bakış sunulmaktadır.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: b33e60036108eb92b7b64b4b3ec3bcc47ad1ade1
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580226"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Mimari-ikincil siteye Hyper-V çoğaltma

Bu makalede, Azure portalından [Azure Site Recovery](site-recovery-overview.md) hizmeti kullanılarak, System Center Virtual Machine Manager (VMM) bulutlarındaki Hyper-V sanal makinelerini (VM) ikincil bir VMM sitesine çoğaltırken kullanılan bileşenler ve işlemler açıklanmaktadır.
a

## <a name="architectural-components"></a>Mimari bileşenler

Aşağıdaki tablo ve grafik, ikincil bir siteye Hyper-V çoğaltması için kullanılan bileşenlerin üst düzey bir görünümünü sağlar.

**Bileşen** | **Gereksinim** | **Ayrıntılar**
--- | --- | ---
**Azure** | Azure aboneliği | VMM konumları arasında çoğaltmayı düzenleyip yönetmek için Azure aboneliğinde bir Kurtarma Hizmetleri kasası oluşturun.
**VMM sunucusu** | Birincil ve ikincil VMM konumu gerekir. | Bir tane birincil sitede, bir tane de ikincil sitede VMM sunucusu olması önerilir.
**Hyper-V sunucusu** |  Birincil ve ikincil VMM bulutlarında bir veya daha fazla Hyper-V konak sunucusu. | Verilerin, Kerberos veya sertifika kimlik doğrulaması kullanılarak, LAN ya da VPN üzerinden birincil ve ikincil Hyper-V ana bilgisayar sunucuları arasında çoğaltılması gerekir.  
**Hyper-V Sanal Makineleri** | Hyper-V ana bilgisayar sunucusunda. | Kaynak ana bilgisayar sunucusunda çoğaltmak istediğiniz en az bir VM olması gerekir.

**Şirket içinden şirket içi mimari**

![Şirket içinden şirket içine korumayı gösteren diyagram.](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Çoğaltma işlemi

1. İlk çoğaltma tetiklendiğinde, bir [Hyper-V VM anlık](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560637(v=ws.10)) görüntüsü alınır.
2. VM 'deki sanal sabit diskler, ikincil konuma tek tek çoğaltılır.
3. İlk çoğaltma devam ederken disk değişiklikleri oluşursa, Hyper-V çoğaltma çoğaltma Izleyicisi değişiklikleri Hyper-V çoğaltma günlükleri (. HRL) olarak izler. Bu günlük dosyaları, disklerle aynı klasörde bulunur. Her diskin ikincil konuma gönderilen ilişkili bir. HRL dosyası vardır. İlk çoğaltma sırasında anlık görüntü ve günlük dosyaları disk kaynaklarını kullanır.
4. İlk çoğaltma tamamlandığında VM anlık görüntüsü silinir ve Delta çoğaltma başlar.
5. Günlükteki değişim disk değişiklikleri eşitlenir ve üst diske birleştirilir.


## <a name="failover-and-failback-process"></a>Yük devretme ve yeniden çalışma işlemi

- Birden fazla makinenin yükünü yönetmek için tek bir makinenin yükünü devreder veya kurtarma planları oluşturabilirsiniz.
- Şirket içi siteler arasında planlanmış veya planlanmamış bir yük devretme gerçekleştirebilirsiniz. Planlı bir yük devretme çalıştırırsanız, veri kaybı olmaması için kaynak VM’ler kapatılır.
    - İkincil bir sitede planlanmamış yük devretme gerçekleştirirseniz, ikincil konumdaki yük devretme makineleri korunduktan sonra.
    - Planlı bir yük devretme gerçekleştirdiyseniz, işlemden sonra ikincil konumdaki makineler korunur.
- İlk yük devretme çalıştıktan sonra, çoğaltma VM 'sinden iş yüküne erişmeye başlamak için bunu yürütün.
- Birincil konum yeniden kullanılabilir olduğunda, tekrar başarısız olabilirsiniz.
    - İkincil siteden birinciye Çoğaltmaya başlamak için çoğaltmayı tersine çevirme işlemini başlatın. Ters çoğaltma sanal makineleri korumalı bir duruma getirir, ancak ikincil veri merkezi hala etkin konumdur.
    - Birincil siteyi yeniden etkin konum durumuna getirmek için ikincil siteden birincil siteye planlı yük devretme başlatır ve arkasından başka bir ters çoğaltma gerçekleştirirsiniz.



## <a name="next-steps"></a>Sonraki adımlar


VMM bulutları arasında Hyper-V çoğaltmasını etkinleştirmek için [Bu öğreticiyi](hyper-v-vmm-disaster-recovery.md) izleyin.
