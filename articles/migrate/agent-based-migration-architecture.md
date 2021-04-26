---
title: Azure geçişi sunucu geçişine aracı tabanlı geçiş
description: Azure geçişi 'nde aracı tabanlı VMware VM geçişine genel bir bakış sağlar.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: f4f79725d0eda65ba00a44e9e7fc2a51c024eccf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864066"
---
# <a name="agent-based-migration-architecture"></a>Aracı tabanlı geçiş mimarisi

Bu makalede, [Azure geçişi: sunucu geçiş](migrate-services-overview.md#azure-migrate-server-migration-tool) aracı Ile VMware VM 'lerinin aracı tabanlı çoğaltması için kullanılan mimari ve işlemlere genel bakış sunulmaktadır.

Azure geçişi: sunucu geçişi kullanarak, VMware VM 'lerini birkaç seçenekten çoğaltabilirsiniz:

- Bu makalede açıklandığı gibi, aracı tabanlı çoğaltma kullanarak VM 'Leri geçirin.
- Aracısız çoğaltma kullanarak VMware VM 'lerini geçirin. Bu işlem, VM 'Leri bunlara hiçbir şey yüklemeye gerek kalmadan geçirir.

VMware VM 'Leri için geçiş yöntemlerini [seçme ve karşılaştırma](server-migrate-overview.md) hakkında daha fazla bilgi edinin. 


## <a name="agent-based-migration"></a>Aracı tabanlı geçiş

Şirket içi VMware VM 'lerini ve fiziksel sunucuları Azure 'a geçirmek için aracı tabanlı geçiş kullanılır. Ayrıca, diğer şirket içi sanallaştırılmış sunucuların yanı sıra AWS örnekleri ve GCP VM 'Leri dahil özel ve genel bulut VM 'Leri geçirmek için de kullanılabilir. Azure geçişi 'nde aracı tabanlı geçiş [Azure Site Recovery](../site-recovery/site-recovery-overview.md) hizmetten bazı arka uç işlevleri kullanır.


## <a name="architectural-components"></a>Mimari bileşenler

Diyagram, aracı tabanlı geçişe dahil olan bileşenleri gösterir.

![Diyagramda, bir tabloda açıklanan aracı tabanlı geçişin bileşenleri gösterilmektedir.](./media/agent-based-replication-architecture/architecture.png)

Tablo, aracı tabanlı geçiş için kullanılan bileşenleri özetler.

**Bileşen** | **Ayrıntılar** | **Yükleme**
--- | --- | ---
**Çoğaltma gereci** | Çoğaltma gereci (yapılandırma sunucusu/işlem sunucusu), şirket içi ortam ve sunucu geçişi arasında köprü görevi gören bir şirket içi sunucusudur. Sunucu geçişinin çoğaltma ve geçişi düzenleyebilmesi için gereç şirket içi sunucu envanterini bulur. Gereç iki bileşene sahiptir:<br/><br/> **Yapılandırma sunucusu**: sunucu geçişine bağlanır ve çoğaltmaları koordine eder.<br/> **İşlem sunucusu**: veri çoğaltmasını işler. İşlem sunucusu sunucu verilerini alır, sıkıştırır ve şifreler ve Azure 'a gönderir. Azure 'da, sunucu geçişi verileri yönetilen disklere yazar. | Varsayılan olarak, işlem sunucusu çoğaltma gereci üzerinde yapılandırma sunucusu ile birlikte yüklenir.
**Mobility hizmeti** | Mobility hizmeti, çoğaltmak ve geçirmek istediğiniz her bir sunucuda yüklü bir aracıdır. Sunucudan işlem sunucusuna çoğaltma verileri gönderir. | Mobility hizmetinin farklı sürümleri için yükleme dosyaları, çoğaltma gereci üzerinde bulunur. Çoğaltmak istediğiniz sunucunun işletim sistemine ve sürümüne uygun olarak, ihtiyacınız olan aracıyı indirip yüklersiniz.

## <a name="mobility-service-installation"></a>Mobility hizmeti yüklemesi

Mobility hizmetini aşağıdaki yöntemleri kullanarak dağıtabilirsiniz:

- **Anında yükleme**: bir sunucu için korumayı etkinleştirdiğinizde Mobility hizmeti işlem sunucusu tarafından yüklenir. 
- **El Ile yüklensin**: Mobility hizmetini her bir sunucuya kullanıcı arabirimi veya komut istemi aracılığıyla el ile yükleyebilirsiniz.

Mobility hizmeti, çoğaltma gereci ve çoğaltılan sunucularıyla iletişim kurar. Çoğaltma gereci, işlem sunucularında veya çoğaltmakta olan sunucularda çalışan bir virüsten koruma yazılımınız varsa, aşağıdaki klasörlerin taramanın dışında tutulması gerekir:


- C:\Program Files\Microsoft Azure kurtarma hizmetleri Aracısı
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (Mobility hizmetinin yüklü olduğu Windows sunucularında)

## <a name="replication-process"></a>Çoğaltma işlemi

1. Bir sunucu için çoğaltmayı etkinleştirdiğinizde, Azure 'a ilk çoğaltma başlar.
2. İlk çoğaltma sırasında Mobility hizmeti, sunucu disklerinden verileri okur ve işlem sunucusuna gönderir.
3. Bu veriler, Azure aboneliğinizdeki diskin bir kopyasını temel almak için kullanılır. 
4. İlk çoğaltma tamamlandıktan sonra, Delta değişikliklerinin Azure 'a çoğaltılması başlar. Çoğaltma, blok düzeyi ve neredeyse sürekli.
4. Mobility hizmeti, işletim sisteminin depolama alt sistemiyle tümleştirerek disk belleğine yazma işlemlerini karşılar. Bu yöntem, Çoğaltma sunucusunda, artımlı çoğaltma için disk g/ç işlemlerini önler. 
5. Sunucu için izlenen değişiklikler, HTTPS 9443 gelen bağlantı noktasında işlem sunucusuna gönderilir. Bu bağlantı noktası değiştirilebilir. İşlem sunucusu bu dosyayı sıkıştırır ve şifreler ve Azure 'a gönderir. 

## <a name="ports"></a>Bağlantı noktaları

**Cihaz** | **Bağlantı**
--- | --- 
**Sunucuları çoğaltma** | VM 'lerde çalışan Mobility hizmeti, çoğaltma yönetimi için HTTPS 443 gelen bağlantı noktasında şirket içi çoğaltma gereci ile iletişim kurar.<br/><br/> Sunucular, çoğaltma verilerini işlem sunucusuna HTTPS 9443 gelen bağlantı noktası üzerinden gönderir. Bu bağlantı noktası değiştirilebilir.
**Çoğaltma gereci** | Çoğaltma gereci, HTTPS 443 giden bağlantı noktası üzerinden Azure ile çoğaltmayı düzenler.
**İşlem sunucusu** | İşlem sunucusu çoğaltma verilerini alır, bunları iyileştirir ve şifreler ve 443 giden bağlantı noktası üzerinden Azure depolama 'ya gönderir.


## <a name="performance-and-scaling"></a>Performans ve ölçeklenebilirlik

Varsayılan olarak, hem yapılandırma sunucusunu hem de işlem sunucusunu çalıştıran tek bir çoğaltma gereci dağıtırsınız. Yalnızca birkaç sunucuyu çoğaltıyorsanız bu dağıtım yeterlidir. Ancak, yüzlerce sunucuyu çoğaltırken geçirdiğinizde tek bir işlem sunucusu, tüm çoğaltma trafiğini işleyemeyebilir. Bu durumda, ek, genişleme işlem sunucuları dağıtabilirsiniz.

### <a name="plan-vmware-deployment"></a>VMware dağıtımını planlayın

VMware VM 'lerini çoğaltırken, günlük veri değişim oranı ve ihtiyacınız olan işlem sunucuları dahil olmak üzere performans gereksinimlerini belirlemenize yardımcı olması için [VMware için Site Recovery dağıtım planlayıcısı](../site-recovery/site-recovery-deployment-planner.md)kullanabilirsiniz.

### <a name="replication-appliance-capacity"></a>Çoğaltma gereç kapasitesi

Dağıtımınızda ek bir işlem sunucusu gerekip gerekmediğini anlamak için bu tablodaki değerleri kullanın.

- Günlük değişiklik hızı (karmaşıklık oranı) 2 TB 'den fazla ise, ek bir işlem sunucusu dağıtın.
- 200 'den fazla sunucu çoğaltdıysanız, ek bir çoğaltma gereci dağıtın.

**CPU** | **Bellek** | **Boş alan-veri önbelleğe alma** | **Karmaşıklık oranı** | **Çoğaltma sınırları**
--- | --- | --- | --- | ---
8 vCPU (2 yuva * 4 çekirdek \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB veya daha az | < 100 sunucuları 
12 vCPU (2 yuva * 6 çekirdek \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB ila 1 TB | 100-150 sunucu.
16 vCPU (2 yuva * 8 çekirdek \@ 2,5 GHz) | 32 GB |  1 TB | 1 TB-2 TB | 151-200 sunucu.

### <a name="sizing-scale-out-process-servers"></a>Genişleme işlem sunucularını boyutlandırma

Genişleme işlem sunucusu dağıtmanız gerekiyorsa, sunucu boyutunu anlamak için bu tabloyu kullanın.

**İşlem sunucusu** | **Veri önbelleğe alma için boş alan** | **Karmaşıklık oranı** | **Çoğaltma sınırları**
--- | --- | --- | --- 
4 vCPU (2 yuva * 2 çekirdek \@ 2,5 GHz), 8 GB bellek | 300 GB | 250 GB veya daha az | 85 sunucuya kadar 
8 vCPU (2 yuva * 4 çekirdek \@ 2,5 GHz), 12 GB bellek | 600 GB | 251 GB ila 1 TB | 86-150 sunucu.
12 vCPU (2 yuva * 6 çekirdek \@ 2,5 GHz), 24 GB bellek | 1 TB | 1-2 TB | 151-225 sunucu.

## <a name="throttle-upload-bandwidth"></a>Karşıya yükleme bant genişliğini azaltma.

Azure 'a çoğaltılan VMware trafiği belirli bir işlem sunucusundan geçer. İşlem sunucuları olarak çalışan sunuculardaki bant genişliğini azaltarak karşıya yükleme verimini sınırlayabilirsiniz. Bu kayıt defteri anahtarını kullanarak bant genişliğini etkileyebilirsiniz:

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM kayıt defteri değeri, bir diskin veri aktarımı (başlangıç veya değişim çoğaltması) için kullanılan iş parçacıklarının sayısını belirtir. Daha yüksek bir değer, çoğaltma için kullanılan ağ bant genişliğini artırır. Varsayılan değer 4 ' tür. En büyük değer 32 ' dir. Değeri iyileştirmek için trafiği izleyin.
- Ayrıca, işlem sunucusu üzerindeki bant genişliğini şu şekilde kısıtlama edebilirsiniz:

    1. İşlem sunucusunda, Azure Backup MMC ek bileşenini açın. Masaüstünde veya C:\Program Files\Microsoft Azure kurtarma hizmetleri servisleri \ bin klasöründe bir kısayol bulunur. 
    2. Ek bileşende **Özellikleri Değiştir**' i seçin.
    3. **Daraltma** bölümünde, **yedekleme işlemleri için internet bant genişliği kullanımını azaltmayı etkinleştir**' i seçin. Çalışma ve çalışılmayan saatlerin sınırlarını ayarlayın. Geçerli aralıklar 512 kbps ile 1.023 Mbps arasındadır.


## <a name="next-steps"></a>Sonraki adımlar

[VMware](tutorial-migrate-vmware-agent.md) veya [fiziksel sunucular](tutorial-migrate-physical-virtual-machines.md)için [aracı tabanlı geçiş](tutorial-migrate-vmware-agent.md) yapmayı deneyin.
