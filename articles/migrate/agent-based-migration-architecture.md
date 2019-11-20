---
title: Azure 'da aracı tabanlı geçiş mimarisi sunucu geçişini geçirme
description: Azure geçişi sunucu geçişi ile aracı tabanlı VMware VM geçişine genel bir bakış sağlar.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: f5ad3aa0fc51f47942750d3745ffef1d6e4a087d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232589"
---
# <a name="agent-based-migration-architecture"></a>Aracı tabanlı geçiş mimarisi

Bu makalede, Azure geçiş sunucusu geçiş aracı ile aracı tabanlı çoğaltma için kullanılan mimari ve işlemlere genel bakış sunulmaktadır.

[Azure geçişi](migrate-services-overview.md) , şirket içi uygulamalarınızı ve iş yüklerinizi ve AWS/GCP sanal makine örneklerini Azure 'a bulmayı, değerlendirmeyi ve geçirmeyi izlemek için bir merkezi Merkez sağlar. Hub, değerlendirme ve geçiş için Azure geçiş araçları ve ayrıca üçüncü taraf bağımsız yazılım satıcısı (ISV) teklifleri sağlar.

## <a name="agent-based-replication"></a>Aracı tabanlı çoğaltma

Azure geçişi sunucu çoğaltma aracında aracı tabanlı çoğaltma, şirket içi VMware VM 'lerini ve fiziksel sunucuları Azure 'a geçirmek için kullanılır. Ayrıca, diğer şirket içi sanallaştırılmış sunucuların yanı sıra AWS örnekleri ve GCP VM 'Leri dahil özel ve genel bulut VM 'Leri geçirmek için de kullanılabilir.

VMware geçişi için Azure geçişi sunucu geçiş aracı, birkaç seçenek sunar:

- Bu makalede açıklandığı gibi, aracı tabanlı çoğaltma kullanılarak geçiş.
- Üzerinde herhangi bir şey yüklemeye gerek kalmadan sanal makineleri geçirmek için aracısız çoğaltma.

[VMware için geçiş yöntemi seçme](server-migrate-overview.md)hakkında daha fazla bilgi edinin.

## <a name="server-migration-and-azure-site-recovery"></a>Sunucu geçişi ve Azure Site Recovery

Azure geçişi sunucu geçişi, şirket içi ve genel bulut iş yüklerini Azure 'a geçirmeye yönelik bir araçtır. Geçiş için en iyi duruma getirilmiştir. Site Recovery bir olağanüstü durum kurtarma aracıdır. Azure Server Geçişi ve Site Recovery veri çoğaltma için kullanılan bazı yaygın teknoloji bileşenlerini paylaşır, ancak farklı amaçlara hizmet eder.

## <a name="architectural-components"></a>Mimari bileşenler

![Mimari](./media/agent-based-replication-architecture/architecture.png)

Tablo, aracı tabanlı geçiş için kullanılan bileşenleri özetler.

**Bileşen** | **Ayrıntılar** | **Yükleme**
--- | --- | ---
**Çoğaltma gereci** | Çoğaltma gereci (yapılandırma sunucusu), şirket içi ortam arasında köprü görevi gören şirket içi bir makinedir ve Azure geçiş sunucusu geçiş aracı. Gereç, şirket içi VM envanterini bulur, böylece Azure Server geçişi çoğaltmayı ve geçişi düzenleyebilir. Gereç iki bileşene sahiptir:<br/><br/> **Yapılandırma sunucusu**: Azure geçişi sunucu geçişine bağlanır ve çoğaltmaları koordine eder.<br/> **İşlem sunucusu**: Veri çoğaltmasını işler. VM verilerini alır, sıkıştırır ve şifreler ve Azure aboneliğine gönderir. Burada, sunucu geçişi verileri yönetilen disklere yazar. | Varsayılan olarak, işlem sunucusu çoğaltma gereci üzerinde yapılandırma sunucusu ile birlikte yüklenir.
**Ulaşım hizmeti** | Mobility hizmeti, çoğaltmak ve geçirmek istediğiniz her makinede yüklü bir aracıdır. Makineden işlem sunucusuna çoğaltma verileri gönderir. Birçok farklı Mobility hizmeti Aracısı mevcuttur. | Mobility hizmeti için yükleme dosyaları, çoğaltma gereci 'nda bulunur. Çoğaltmak istediğiniz makinenin işletim sistemine ve sürümüne uygun olarak, ihtiyacınız olan aracıyı indirip yüklersiniz.

### <a name="mobility-service-installation"></a>Mobility hizmeti yüklemesi

Mobility hizmetini aşağıdaki yöntemleri kullanarak dağıtabilirsiniz:

- **Gönderim yükleme**: Bir makine için korumayı etkinleştirdiğinizde Mobility hizmeti işlem sunucusu tarafından yüklenir. 
- **El Ile yüklensin**: Mobility hizmetini her makineye Kullanıcı arabirimi veya komut istemi aracılığıyla el ile yükleyebilirsiniz.

Mobility hizmeti, çoğaltma gereci ve çoğaltılan makinelerle iletişim kurar. Çoğaltma gereci, işlem sunucularında veya çoğaltılan makinelerde çalışan bir virüsten koruma yazılımınız varsa, aşağıdaki klasörlerin taramanın dışında tutulması gerekir:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (Mobility hizmetinin yüklü olduğu Windows makinelerde)

## <a name="replication-process"></a>Çoğaltma işlemi

1. Bir sanal makine için çoğaltmayı etkinleştirdiğinizde, Azure 'a ilk çoğaltma başlar.
2. İlk çoğaltma sırasında, Mobility hizmeti makine disklerinden verileri okur ve işlem sunucusuna gönderir.
3. Bu veriler, Azure aboneliğinizdeki diskin bir kopyasını temel almak için kullanılır. 
4. İlk çoğaltma tamamlandıktan sonra, Delta değişikliklerinin Azure 'a çoğaltılması başlar. Çoğaltma, blok düzeyi ve neredeyse sürekli.
4. Mobility hizmeti, işletim sisteminin depolama alt sistemiyle tümleştirerek VM disk belleğine yazma işlemleri durdurur. Bu yöntem, çoğaltılan makinede bulunan disk g/ç işlemlerini artımlı çoğaltma için engeller. 
5. Bir makine için izlenen değişiklikler, HTTPS 9443 gelen bağlantı noktasında işlem sunucusuna gönderilir. Bu bağlantı noktası değiştirilebilir. İşlem sunucusu bu dosyayı sıkıştırır ve şifreler ve Azure 'a gönderir. 

## <a name="ports"></a>Bağlantı Noktaları

**cihaz** | **bağlantı**
--- | --- 
VM | VM 'lerde çalışan Mobility hizmeti, çoğaltma yönetimi için HTTPS 443 gelen bağlantı noktasında şirket içi çoğaltma gereci ile iletişim kurar.<br/><br/> VM 'Ler, çoğaltma verilerini işlem sunucusuna gönderir (varsayılan olarak çoğaltma gereci üzerinde çalışır), HTTPS 9443 gelen bağlantı noktası üzerinden. Bu bağlantı noktası değiştirilebilir.
Çoğaltma gereci | Çoğaltma gereci, HTTPS 443 giden bağlantı noktası üzerinden Azure ile çoğaltmayı düzenler.
İşlem sunucusu | İşlem sunucusu çoğaltma verilerini alır, bunları iyileştirir ve şifreler ve 443 giden bağlantı noktası üzerinden Azure depolama 'ya gönderir.


## <a name="performance-and-scaling"></a>Performans ve ölçeklenebilirlik

Varsayılan olarak, hem yapılandırma sunucusunu hem de işlem sunucusunu çalıştıran tek bir çoğaltma gereci dağıtırsınız. Yalnızca birkaç makineyi çoğaltdıysanız bu dağıtım yeterlidir. Ancak, yüzlerce makineyi çoğaltırken geçiriyorsanız, tek bir işlem sunucusu tüm çoğaltma trafiğini işleyemeyebilir. Bu durumda, ek, genişleme işlem sunucuları dağıtabilirsiniz.

### <a name="site-recovery-deployment-planner-for-vmware"></a>VMware için Site Recovery Dağıtım Planlayıcısı

VMware VM 'lerini çoğaltırken, günlük veri değişim oranı ve ihtiyacınız olan işlem sunucuları dahil olmak üzere, VMware için [Site Recovery dağıtım planlayıcısı](../site-recovery/site-recovery-deployment-planner.md) kullanabilirsiniz.

### <a name="replication-appliance-capacity"></a>Çoğaltma gereç kapasitesi

Bu tablodaki değerler, dağıtımınızda ek bir işlem sunucusu gerekip gerekmediğini anlamak için kullanılabilir.

- Günlük değişiklik tarifeniz (karmaşıklık oranı) 2 TB 'den fazla ise, ek bir işlem sunucusu dağıtın.
- 200 'den fazla makine çoğaltıyorsanız, ek bir çoğaltma gereci dağıtın.

**CPU** | **Bellek** | **Veri önbelleğe alma için boş alan** | **Karmaşıklık oranı** | **Çoğaltma sınırları**
--- | --- | --- | --- | ---
8 vCPU (2 yuva * 4 çekirdek \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB veya daha az | < 100 makine 
12 vCPU (2 yuva * 6 çekirdek \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB ila 1 TB | 100-150 makine.
16 vCPU (2 yuva * 8 çekirdek \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB-2 TB | 151-200 makine.

### <a name="scale-out-process-server-sizing"></a>Genişleme işlem sunucusu boyutlandırma

Bir genişleme işlem sunucusu dağıtmanız gerekiyorsa, bu tablo sunucu boyutunu bulmanıza yardımcı olabilir.

**İşlem sunucusu** | **Veri önbelleğe alma için boş alan** | **Karmaşıklık oranı** | **Çoğaltma sınırları**
--- | --- | --- | --- 
4 vCPU (2 yuva * 2 çekirdek \@ 2,5 GHz), 8 GB bellek | 300 GB | 250 GB veya daha az | 85 adede kadar makine 
8 vCPU (2 yuva * 4 çekirdek \@ 2,5 GHz), 12 GB bellek | 600 GB | 251 GB ila 1 TB    | 86-150 makine.
12 vCPU (2 yuva * 6 çekirdek \@ 2,5 GHz), 24 GB bellek | 1 TB | 1-2 TB | 151-225 makine.

## <a name="control-upload-throughput"></a>Yükleme aktarım hızını denetleme


 Azure 'a çoğaltılan VMware trafiği belirli bir işlem sunucusundan geçer. İşlem sunucuları olarak çalışan makinelerdeki bant genişliğini azaltarak karşıya yükleme verimini sınırlayabilirsiniz. Bu kayıt defteri anahtarını kullanarak bant genişliğini etkileyebilirsiniz:

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM kayıt defteri değeri, bir diskin veri aktarımı (başlangıç veya değişim çoğaltması) için kullanılan iş parçacıklarının sayısını belirtir. Daha yüksek bir değer, çoğaltma için kullanılan ağ bant genişliğini artırır. Varsayılan değer 4 ' tür. En büyük değer 32 ' dir. Değeri iyileştirmek için trafiği izleyin.
- Buna ek olarak, işlem sunucusu makinesindeki bant genişliğini şu şekilde kısıtlama edebilirsiniz:

    1. İşlem sunucusu makinesinde, Azure Backup MMC ek bileşenini açın. Masaüstünde veya C:\Program Files\Microsoft Azure kurtarma hizmetleri servisleri \ bin klasöründe bir kısayol bulunur. 
    2. Ek bileşende **Özellikleri Değiştir**' i seçin.
    3. **Daraltma**bölümünde, **yedekleme işlemleri için internet bant genişliği kullanımını azaltmayı etkinleştir**' i seçin. Çalışma ve çalışılmayan saatlerin sınırlarını ayarlayın. Geçerli aralıklar 512 kbps ile 1.023 Mbps arasındadır.


## <a name="next-steps"></a>Sonraki adımlar

Azure geçişi sunucu geçişini kullanarak aracı tabanlı [VMware VM geçişini](tutorial-migrate-vmware-agent.md) deneyin.
