---
title: Azure geçişi sunucu geçiş ile Hyper-V geçişi nasıl çalışır? | Microsoft Docs
description: Hyper-V geçiş Azure geçişi sunucusu geçişine genel bakış sağlar
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9148e76a9f2abd369ae595422d785a347e58dfab
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811719"
---
# <a name="how-does-hyper-v-replication-work"></a>Hyper-V çoğaltma nasıl çalışır?

Bu makalede, mimari ve işlemlerdeki Hyper-V Vm'lerini Azure geçişi Server Geçiş Aracı ile geçiş sırasında kullanılan genel bir bakış sağlar.

[Azure geçişi](migrate-services-overview.md) bulma, değerlendirme ve şirket içi uygulamalarınızı ve iş yükleri ve private/public bulut Vm'lerinden azure'a geçişini izlemek için merkezi bir nokta sağlar. Hub araçları Azure geçişi, değerlendirme ve geçiş, ek olarak üçüncü taraf bağımsız yazılım satıcısı (ISV) teklifleri sağlar.

## <a name="agentless-migration"></a>Aracısız geçiş

Azure geçişi Server Geçiş Aracı, Hyper-V için en iyi duruma getirilmiş bir geçiş iş akışı kullanarak, şirket içi Hyper-V Vm'leri için aracısız çoğaltma sağlar. Yazılım Aracısı, yalnızca Hyper-V konakları veya küme düğümlerine yükleyin. Hiçbir şey, Hyper-V Vm'lerinde yüklü olması gerekir.

## <a name="server-migration-and-azure-site-recovery"></a>Sunucu geçiş ve Azure Site Recovery

Azure geçişi sunucusu geçişi geçirme şirket içi iş yüklerini ve azure'a, bulut tabanlı VM'ler için bir araçtır. Site kurtarma, olağanüstü durum kurtarma aracıdır. Araçları, veri çoğaltma için kullanılan bazı yaygın teknolojisi bileşenleri paylaşır, ancak farklı amaçlara hizmet eder. 


## <a name="architectural-components"></a>Mimari bileşenler

![Mimari](./media/hyper-v-replication-architecture/architecture.png)



**Bileşen** | **Dağıtım** | 
--- | --- 
**Çoğaltma sağlayıcısı** | Microsoft Azure Site Recovery sağlayıcısı, Hyper-V konaklarında yüklü ve Azure geçişi sunucu geçişi ile kayıtlı.<br/> Sağlayıcıyı Hyper-V Vm'leri için çoğaltmayı düzenler.
**Kurtarma Hizmetleri Aracısı** | Microsoft Azure kurtarma Hizmetleri Aracısı, veri çoğaltma işlemini gerçekleştirir. Azure'a Hyper-V sanal makinelerinden veri sağlayıcısı ile çalışır.<br/> Çoğaltılan veriler, Azure Aboneliğinize bir depolama hesabına yüklenir. Sunucu geçişinin çoğaltılan veriler işlemler aracı ve Abonelikteki çoğaltma diskleri geçerli olur. Çoğaltma diskleri geçiş yaptığınızda Azure Vm'leri oluşturmak için kullanılır.

- Bileşenler tarafından tek bir kurulum dosyası, Azure geçişi Server Geçiş Portalı'nda karşıdan yüklenir.
- Gereç ve sağlayıcı Azure geçişi sunucusu geçişi ile iletişim kurmak için giden HTTPS bağlantı noktası 443 bağlantıları kullanın.
- Sağlayıcı ve aracı arasındaki iletişimler şifrelenir ve güvenli alınır.


## <a name="replication-process"></a>Çoğaltma işlemi

1. Bir Hyper-V sanal makine için çoğaltmayı etkinleştirdiğinizde, ilk çoğaltma başlar.
2. Bir Hyper-V VM anlık görüntüsü alınır.
3. Tümünü Azure'a kopyalanana kadar sanal makine VHD'leri çoğaltılan tek tek, ' dir. İlk çoğaltma süresi VM boyutu ve ağ bant genişliğine bağlıdır.
4. İlk çoğaltma sırasında meydana gelen disk değişiklikleri, Hyper-V çoğaltma kullanarak ve günlük dosyalarında (hrl dosyaları) depolanan izlenir.
    - Günlük dosyaları disklerle aynı klasörde olduğundan.
    - Her diskin ikincil depolamaya gönderilir bir ilişkili hrl dosyası vardır.
    - İlk çoğaltma sırasında anlık görüntü ve günlük dosyaları disk kaynaklarını kullanır.
4. İlk çoğaltma tamamlandığında VM anlık görüntüsü silinir ve değişiklik çoğaltması başlar.
5. Artımlı disk değişiklikleri hrl dosyalarında izlenir. Çoğaltma günlükleri için bir Azure depolama hesabı kurtarma Hizmetleri aracısı tarafından düzenli aralıklarla geri yüklenir.


## <a name="performance-and-scaling"></a>Performans ve ölçeklenebilirlik

Hyper-v çoğaltma performansı Azure'da VM boyutu, VM veri değişim hızı (erime), günlük dosya depolama, çoğaltma verileri için karşıya yükleme bant genişliğinin ve hedef depolama için Hyper-V konağında kullanılabilir alanı içeren faktörler tarafından etkilenir.

- Aynı anda birden çok makine çoğaltma yapıyorsanız kullanmak [Azure Site Recovery dağıtım Planlayıcısı](../site-recovery/hyper-v-deployment-planner-overview.md) çoğaltma iyileştirilmesine yardımcı olmak üzere Hyper-v,.
- Hyper-V çoğaltma planlama ve çoğaltma kapasite uygun olarak, Azure depolama hesapları üzerinden dağıtın.

### <a name="control-upload-throughput"></a>Denetim karşıya yükleme verimi

Her Hyper-V konağında veri yüklemek için kullanılan bant genişliği miktarını sınırlayabilirsiniz. Dikkat et. Çoğaltma ve gecikme geçiş olumsuz etkiler çok düşük değerleri ayarlarsanız.


1. Hyper-V konak veya küme düğümünde oturum açın.
2. Çalıştırma **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, Windows Azure Backup MMC ek bileşenini açın.
3. Ek bileşeninde, seçin **özelliklerini değiştirme**.
4. İçinde **azaltma**seçin **yedekleme işlemleri için internet bant genişliği kullanımını azaltmayı etkinleştir**. Ve çalışma dışı saatler için sınırları ayarlayın. Geçerli aralıklar 512 Kbps ila 1,023 MB/sn olan.
I

### <a name="influence-upload-efficiency"></a>Karşıya yükleme verimliliği etkisi

Çoğaltma için yedek bant genişliğine sahip ve karşıya artırmak istiyorsanız, karşıya yükleme görevi gibi ayrılan iş parçacığı sayısını artırabilirsiniz:

1. Kayıt defteri Regedit ile açın.
2. HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM anahtarına gidin
3. Her bir çoğaltma VM için karşıya veri yükleme için kullanılan iş parçacıklarının sayısı değerini artırın. Varsayılan değer 4'tür ve maksimum değer 32'dir. 




## <a name="next-steps"></a>Sonraki adımlar

Denemenin [Hyper-V geçişi](tutorial-migrate-hyper-v.md) Azure geçişi sunucusu geçişi kullanan.
