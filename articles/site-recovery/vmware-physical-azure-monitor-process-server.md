---
title: Azure Site Recovery işlem sunucusunu izleme
description: Bu makalede, VMware VM/fiziksel sunucu olağanüstü durum kurtarma için kullanılan Azure Site Recovery işlem sunucusunun nasıl izleneceği açıklanır
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082238"
---
# <a name="monitor-the-process-server"></a>İşlem sunucusunu izleme

Bu makalede [Site Recovery](site-recovery-overview.md) işlem sunucusunun nasıl izleneceği açıklanır.

- İşlem sunucusu, şirket içi VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarmayı ayarlarken kullanılır.
- Varsayılan olarak, işlem sunucusu yapılandırma sunucusunda çalışır. Yapılandırma sunucusunu dağıtırken varsayılan olarak yüklenir.
- İsteğe bağlı olarak, çoğaltılan makinelerin daha büyük sayısını ve daha yüksek sayıda çoğaltma trafiğini ölçeklendirmek ve işlemek için ek, genişleme işlem sunucuları dağıtabilirsiniz.

İşlem sunucularının rolü ve dağıtımı hakkında [daha fazla bilgi edinin](vmware-physical-azure-config-process-server-overview.md) .

## <a name="monitoring-overview"></a>İzlemeye genel bakış

İşlem sunucusu bu kadar çok rol içerdiğinden, özellikle çoğaltılan verileri önbelleğe alma, sıkıştırma ve Azure 'a aktarma işlemlerinin devam ettiğinden, işlem sunucusu durumunun sürekli olarak izlenmesi önemlidir.

Genellikle işlem sunucusu performansını etkileyen bazı durumlar vardır. Performansı etkileyen sorunların VM sistem durumu üzerinde basamaklı bir etkisi olur, sonunda hem işlem sunucusu hem de çoğaltılan makineler kritik bir duruma gönderilir. Durumlar şunlardır:

- Yüksek sayıda sanal makine, önerilen sınırlamalara yaklaşarak veya aşarak bir işlem sunucusu kullanır.
- İşlem sunucusunu kullanan VM 'Lerde yüksek bir karmaşıklık oranı vardır.
- VM 'Ler ile işlem sunucusu arasındaki ağ aktarım hızı, çoğaltma verilerini işlem sunucusuna yüklemek için yeterli değil.
- İşlem sunucusu ile Azure arasındaki ağ aktarım hızı, çoğaltma verilerini işlem sunucusundan Azure 'a yüklemek için yeterli değildir.

Bu sorunların tümü, VM 'lerin kurtarma noktası hedefini (RPO) etkileyebilir. 

**Kaydol?** Bir VM için bir kurtarma noktası oluşturmak, sanal makinenin tüm disklerinin ortak bir noktaya sahip olmasını gerektirir. Bir disk yüksek bir karmaşıklık hızına sahipse, çoğaltma yavaşsa veya işlem sunucusu en iyi durumda değilse, kurtarma noktalarının ne şekilde oluşturulduğunu etkiler.

## <a name="monitor-proactively"></a>Proaktif izleme

İşlem sunucusuyla ilgili sorunları önlemek için şunları yapmanız gerekir:

- İşlem sunucuları için [Kapasite ve boyutlandırma kılavuzunu](site-recovery-plan-capacity-vmware.md#capacity-considerations)kullanarak belirli gereksinimleri anlayın ve işlem sunucularının önerilere göre dağıtıldığından ve çalıştığından emin olun.
- İşlem sunucularının verimli bir şekilde çalışmasını sağlamak için uyarıları izleyin ve ortaya çıkan sorunları giderin.


## <a name="process-server-alerts"></a>İşlem sunucusu uyarıları

İşlem sunucusu, aşağıdaki tabloda özetlenen bir dizi sistem durumu uyarısı oluşturur.

**Uyarı türü** | **Ayrıntılar**
--- | ---
![Sorunsuz][green] | İşlem sunucusu bağlandı ve sağlıklı.
![Uyarı][yellow] | Son 15 dakika boyunca %80 > CPU kullanımı
![Uyarı][yellow] | Son 15 dakika boyunca bellek kullanımı > %80
![Uyarı][yellow] | Son 15 dakika boyunca %30 < önbellek klasörü boş alanı
![Uyarı][yellow] | Her beş dakikada bir bekleyen/giden verileri izler Site Recovery ve işlem sunucusu önbelleğindeki verilerin 30 dakika içinde Azure 'a yüklenemediğini tahmin eder.
![Uyarı][yellow] | İşlem sunucusu Hizmetleri son 15 dakika boyunca çalışmıyor
![Kritik][red] | Son 15 dakika boyunca %95 > CPU kullanımı
![Kritik][red] | Son 15 dakika boyunca bellek kullanımı > %95
![Kritik][red] | Son 15 dakika boyunca %25 < önbellek klasörü boş alanı
![Kritik][red] | Her beş dakikada bir bekleyen/giden verileri izler Site Recovery ve işlem sunucusu önbelleğindeki verilerin 45 dakika içinde Azure 'a yüklenemediğini tahmin eder.
![Kritik][red] | 15 dakika boyunca işlem sunucusundan sinyal alınmadı.

![Tablo anahtarı](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> İşlem sunucusunun genel sistem durumu, üretilen en kötü uyarıyı temel alır.



## <a name="monitor-process-server-health"></a>İşlem sunucusu sistem durumunu izleme

İşlem sunucularınızın sistem durumunu aşağıdaki şekilde izleyebilirsiniz: 

1. Çoğaltılan bir makinenin ve işlem sunucusunun çoğaltma durumunu ve durumunu izlemek için, > **çoğaltılan öğeler**kasa bölümünde izlemek istediğiniz makineye tıklayın.
2. **Çoğaltma durumu**' nda VM sistem durumunu izleyebilirsiniz. Hata ayrıntılarına gitmek için duruma tıklayın.

    ![VM panosundaki işlem sunucusu durumu](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. **Işlem sunucusu sistem durumu**' nda, işlem sunucusunun durumunu izleyebilirsiniz. Ayrıntılar için detaya gidin.

    ![VM panosundaki sunucu ayrıntılarını işleme](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Durum, VM sayfasındaki grafik temsili kullanılarak da izlenebilir.
    - Bir genişleme işlem sunucusu kendisiyle ilişkili uyarılar varsa Turuncu renkle vurgulanır ve herhangi bir kritik sorun varsa kırmızı olur. 
    - İşlem sunucusu yapılandırma sunucusundaki varsayılan dağıtımda çalışıyorsa, yapılandırma sunucusu uygun şekilde vurgulanacaktır.
    - Ayrıntıya gitmek için yapılandırma sunucusuna veya işlem sunucusuna tıklayın. Tüm sorunları ve düzeltme önerilerini aklınızda edin.

Ayrıca, **Site Recovery altyapı**altında kasadaki işlem sunucularını da izleyebilirsiniz. **Site Recovery altyapınızı yönetme**bölümünde **yapılandırma sunucuları**' na tıklayın. İşlem sunucusuyla ilişkili yapılandırma sunucusunu seçin ve işlem sunucusu ayrıntıları ' na gidin.


## <a name="next-steps"></a>Sonraki adımlar

- Herhangi bir işlem sunucusu sorununuz varsa, [sorun giderme kılavuzumuzu](vmware-physical-azure-troubleshoot-process-server.md) izleyin
- Daha fazla yardıma ihtiyacınız varsa sorunuzu [Azure Site Recovery forumuna](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)gönderin. 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
