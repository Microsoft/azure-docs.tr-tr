---
title: Azure 'da Hyper-V geçişi nasıl çalışır?
description: Azure geçişi ile Hyper-V geçişi hakkında bilgi edinin
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 239918cc19eefbef9e3c3f12d5ddd3bb5434b490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751028"
---
# <a name="how-does-hyper-v-replication-work"></a>Hyper-V çoğaltma nasıl çalışır?

Bu makalede, Hyper-V VM 'lerini Azure geçiş sunucusu geçiş aracı ile geçirdiğinizde kullanılan mimari ve işlemlere genel bakış sunulmaktadır.

[Azure geçişi](migrate-services-overview.md) , şirket içi uygulamalarınızı, iş yüklerinizi ve özel/genel bulut VM 'lerini Azure 'a bulmayı, değerlendirmeyi ve geçirmeyi izlemek için bir merkezi Merkez sağlar. Hub, değerlendirme ve geçiş için Azure geçiş araçları ve ayrıca üçüncü taraf bağımsız yazılım satıcısı (ISV) teklifleri sağlar.

## <a name="agentless-migration"></a>Aracısız geçiş

Azure geçişi sunucu geçiş aracı, Hyper-V için iyileştirilmiş bir geçiş iş akışı kullanarak şirket içi Hyper-V VM 'Leri için aracısız çoğaltma sağlar. Yalnızca Hyper-V konaklarına veya küme düğümlerine yazılım Aracısı yüklersiniz. Hyper-V VM 'lerine hiçbir şeyin yüklenmesi gerekmez.

## <a name="server-migration-and-azure-site-recovery"></a>Sunucu geçişi ve Azure Site Recovery

Azure geçişi sunucu geçişi, şirket içi iş yüklerini ve bulut tabanlı VM 'Leri Azure 'a geçirmeye yönelik bir araçtır. Site Recovery bir olağanüstü durum kurtarma aracıdır. Araçlar, veri çoğaltma için kullanılan bazı yaygın teknoloji bileşenlerini paylaşır, ancak farklı amaçlara hizmet eder. 


## <a name="architectural-components"></a>Mimari bileşenler

![Diyagramda, bir tabloda açıklanan ayrıntılarla Microsoft Azure için H T T P S veri kanalına sahip bir kaynak Hyper-V ağı gösterilmektedir.](./media/hyper-v-replication-architecture/architecture.png)



**Bileşen** | **Dağıtım** | 
--- | --- 
**Çoğaltma sağlayıcısı** | Microsoft Azure Site Recovery sağlayıcı, Hyper-V konaklarına yüklenir ve Azure geçiş sunucusu geçişine kaydedilir.<br/> Sağlayıcı, Hyper-V VM 'lerinin çoğaltmasını düzenler.
**Kurtarma Hizmetleri Aracısı** | Microsoft Azure kurtarma hizmeti Aracısı veri çoğaltmasını işler. Hyper-V VM 'lerinden Azure 'a veri çoğaltmak için sağlayıcı ile birlikte kullanılır.<br/> Çoğaltılan veriler Azure aboneliğinizdeki bir depolama hesabına yüklenir. Sunucu geçiş aracı, çoğaltılan verileri işler ve aboneliğin çoğaltma disklerine uygular. Çoğaltma diskleri, geçiş yaparken Azure VM 'Leri oluşturmak için kullanılır.

- Bileşenler tek bir kurulum dosyası tarafından yüklenir, bu, portalda Azure geçiş sunucusu geçişi ' nden indirilir.
- Sağlayıcı ve gereç, Azure geçişi sunucu geçişi ile iletişim kurmak için giden HTTPS bağlantı noktası 443 bağlantılarını kullanır.
- Sağlayıcıdan ve aracıdaki iletişimler güvenlidir ve şifrelenir.


## <a name="replication-process"></a>Çoğaltma işlemi

1. Hyper-V VM için çoğaltmayı etkinleştirdiğinizde, ilk çoğaltma başlar.
2. Hyper-V VM anlık görüntüsü alınır.
3. VM 'deki VHD 'ler, hepsi Azure 'a kopyalanana kadar tek tek çoğaltılır. İlk çoğaltma süresi VM boyutuna ve ağ bant genişliğine bağlıdır.
4. İlk çoğaltma sırasında oluşan disk değişiklikleri, Hyper-V çoğaltma kullanılarak izlenir ve günlük dosyalarında (HRL dosyaları) depolanır.
    - Günlük dosyaları diskleriyle aynı klasörslardır.
    - Her disk, ikincil depolamaya gönderilen ilişkili bir HRL dosyasına sahiptir.
    - İlk çoğaltma sırasında anlık görüntü ve günlük dosyaları disk kaynaklarını kullanır.
4. İlk çoğaltma tamamlandıktan sonra VM anlık görüntüsü silinir ve Delta çoğaltma başlar.
5. Artımlı disk değişiklikleri, HRL dosyalarında izlenir. Çoğaltma günlükleri, kurtarma hizmetleri Aracısı tarafından düzenli aralıklarla bir Azure depolama hesabına yüklenir.


## <a name="performance-and-scaling"></a>Performans ve ölçeklenebilirlik

Hyper-V ' d e yönelik çoğaltma performansı, VM boyutu, sanal makinelerin veri değişim oranı (dalgalanması), depolama dosya depolaması için Hyper-V konağındaki kullanılabilir alan ve Azure 'da hedef depolama alanları dahil olmak üzere faktörlerden etkilenir.

- Aynı anda birden çok makineyi çoğaltdıysanız, çoğaltmayı iyileştirmenize yardımcı olmak için Hyper-V [Azure Site Recovery dağıtım planlayıcısı](../site-recovery/hyper-v-deployment-planner-overview.md) kullanın.
- Hyper-V çoğaltmasını planlayın ve Azure depolama hesapları üzerinden çoğaltmayı kapasiteye göre dağıtın.

### <a name="control-upload-throughput"></a>Yükleme aktarım hızını denetleme

Her Hyper-V konağında Azure 'a veri yüklemek için kullanılan bant genişliği miktarını sınırlayabilirsiniz. Dikkat et. Değerleri çok düşük olarak ayarlarsanız çoğaltmayı olumsuz yönde etkiler ve geçişi erteleyebilirsiniz.


1. Hyper-V konağında veya küme düğümünde oturum açın.
2. Windows Azure Backup MMC ek bileşenini açmak için **C:\Program Files\Microsoft Azure Kurtarma Hizmetleri ' ni T\bin\wabadmin.msc**' yi çalıştırın.
3. Ek bileşende **Özellikleri Değiştir**' i seçin.
4. **Daraltma** bölümünde, **yedekleme işlemleri için internet bant genişliği kullanımını azaltmayı etkinleştir**' i seçin. Çalışma ve çalışılmayan saatlerin sınırlarını ayarlayın. Geçerli aralıklar 512 kbps ile 1.023 Mbps arasındadır.
I

### <a name="influence-upload-efficiency"></a>Karşıya yükleme verimliliğini etkiler

Çoğaltma için yedek bant genişliğiniz varsa ve karşıya yüklemeleri artırmak istiyorsanız karşıya yükleme görevi için ayrılan iş parçacığı sayısını aşağıdaki gibi artırabilirsiniz:

1. Regedit ile kayıt defterini açın.
2. Anahtar HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM git
3. Her bir çoğaltma sanal makinesi için veri yüklemesi için kullanılan iş parçacığı sayısı değerini artırın. Varsayılan değer 4 ' dir ve en büyük değer 32 ' dir. 




## <a name="next-steps"></a>Sonraki adımlar

Azure geçişi sunucu geçişini kullanarak [Hyper-V geçişini](tutorial-migrate-hyper-v.md) deneyin.
