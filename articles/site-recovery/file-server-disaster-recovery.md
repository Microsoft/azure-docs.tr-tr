---
title: Azure Site Recovery kullanarak bir dosya sunucusunu koruma
description: Bu makalede Azure Site Recovery kullanarak bir dosya sunucusunu koruma adımları anlatılmaktadır
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 780db0cc5a99adfd2e7f8cd5be20a191bba009e8
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708130"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Azure Site Recovery kullanarak bir dosya sunucusunu koruma 

[Azure Site Recovery](site-recovery-overview.md), planlı ve plansız kesintiler sırasında iş uygulamalarınızı çalışır durumda tutarak, iş sürekliliğinize ve olağanüstü durum kurtarma (BCDR) stratejinize katkıda bulunur. Site Recovery, şirket içi makinelerin ve Azure sanal makinelerinin olağanüstü durum kurtarma işlemlerini yönetir ve düzenler. Olağanüstü durum kurtarma, farklı iş yüklerinin çoğaltma, yük devretme ve kurtarma süreçlerini kapsar.

Bu makalede Site Recovery kullanarak bir dosya sunucusunu koruma altına alma adımlarına ek olarak farklı ortamlara uygun önerilere yer verilmiştir. 

- [Azure IaaS dosya sunucusu makinelerini çoğaltma](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Site Recovery kullanarak bir şirket içi dosya sunucusunu çoğaltma](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Dosya sunucusu mimarisi
Açık dağıtılmış dosya paylaşım sisteminin amacı, coğrafi olarak farklı yerlerde bulunan kullanıcılara sahip gruplara dosyalar üzerinde verimli bir şekilde çalışma yapabilmek için işbirliği yapabilecekleri ve veri bütünlüğü gereksinimlerine uygun bir ortam sağlamaktır. Çok sayıda eşzamanlı kullanıcıyı ve büyük miktarda içerik öğesini destekleyen tipik bir şirket içi dosya sunucusu ekosisteminde çoğaltma zamanlama ve bant genişliği azaltma işlevleri için Dağıtılmış Dosya Sistemi Çoğaltma (DFSR) sisteminden faydalanılır. 

DFSR, sınırlı bant genişliğine sahip ağlarda dosyaların verimli bir şekilde güncelleştirilmesi için kullanılabilen ve Uzaktan Değişiklikleri Sıkıştırma (RDC) olarak bilinen bir sıkıştırma algoritması kullanır. Bu algoritma, dosyalardaki verilerde gerçekleştirilen ekleme, kaldırma ve düzenleme işlemlerini algılar. Dosyalar güncelleştirildiğinde yalnızca değiştirilen dosya bloklarının çoğaltılması için DFSR etkinleştirilir. Ayrıca olağanüstü durum kurtarma gereksinimlerini karşılamak için yoğun olmayan zamanlarda günlük yedeklerin alındığı dosya sunucusu ortamları da vardır. DFSR uygulanmaz.

Aşağıdaki diyagramda DFSR sisteminin etkin olduğu bir dosya sunucusu ortamı gösterilmiştir.
                
![DFSR mimarisi](media/site-recovery-file-server/dfsr-architecture.JPG)

Yukarıdaki diyagramda üye olarak adlandırılan birden fazla dosya sunucusu, dosyaların bir çoğaltma grubu üzerinden çoğaltılması konusunda etkin rol almaktadır. Üyelerden biri çevrimdışı duruma geçse dahi çoğaltılmış klasörün içeriği üyelerden herhangi birine istek gönderen tüm istemciler tarafından kullanılabilir.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Dosya sunucuları için olağanüstü durum kurtarma önerileri

* **Site Recovery kullanarak bir dosya sunucusunu çoğaltma**: Dosya sunucuları, Site Recovery kullanılarak Azure 'a çoğaltılabilir. Şirket içi dosya sunucularından biri veya daha fazlası erişilemez duruma geldiğinde Azure'da kurtarma VM'leri devreye alınabilir. Daha sonra siteler arası VPN bağlantısı bulunması ve Azure'da Active Directory yapılandırılmasının tamamlanmış olması şartıyla bu VM'ler şirket içi istemcilerden gelen isteklere yanıt verebilir. Bu yöntemi DFSR ile yapılandırılmış bir ortamda kullanabileceğiniz gibi DFSR yapılandırması olmayan basit bir dosya sunucusu ortamında da bundan faydalanabilirsiniz. 

* **DFSR 'yi bir Azure IaaS VM 'Sine genişletin**: DFSR 'yi uygulayan kümelenmiş bir dosya sunucusu ortamında, şirket içi DFSR 'yi Azure 'a genişletebilirsiniz. Bunu yaptığınızda bir Azure VM, dosya sunucusu rolü için görevlendirilir. 

    * Siteler arası VPN bağlantısı ve Active Directory bağımlılıkları karşılandıktan ve DFSR uygulamaya alındıktan sonra bir veya daha fazla şirket içi dosya sunucusu erişilemez duruma geldiğinde istemciler Azure VM'ye bağlanarak isteklerine yanıt alabilir.

    * VM'leriniz, Site Recovery tarafından desteklenmeyen bir yapılandırmaya sahipse bu yaklaşımdan faydalanabilirsiniz. Bir diğer örnek de dosya sunucusu ortamlarında sıklıkla kullanılan paylaşılan küme diskidir. DFSR, düşük bant genişliğine sahip ortamlarda da orta düzey veri değişim sıklığı ile iyi bir performans sergiler. Sürekli çalışacak olan bir Azure VM'nin getireceği ek maliyeti dikkate almanız gerekir. 

* **Dosyalarınızı çoğaltmak için Azure dosya eşitleme kullanın**: Bulutu kullanmayı veya zaten bir Azure VM 'yi kullanmayı planlıyorsanız Azure Dosya Eşitleme kullanabilirsiniz. Azure Dosya Eşitleme tamamen yönetilen dosya paylaşımlarının bulutla eşitlenmesini sağlar. Bu dosyalara sektör standardı olan [Sunucu İleti Bloğu](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (SMB) protokolü aracılığıyla erişilebilir. Ardından Azure dosya paylaşımları Windows, Linux ve macOS bulut ve şirket içi dağıtımları tarafından aynı anda bağlanabilir. 

Aşağıdaki diyagram, dosya sunucusu ortamınız için kullanmanız gereken stratejiyi belirleme konusunda yardımcı olmayı amaçlamaktadır.

![Karar ağacı](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Olağanüstü durum kurtarma için Azure'ı kullanma kararını alırken dikkat etmeniz gereken noktalar

|Ortam  |Öneri  |Dikkat edilmesi gereken noktalar |
|---------|---------|---------|
|DFSR'yi kullanan veya kullanmayan dosya sunucusu ortamı|   [Çoğaltma için Site Recovery'yi kullanın](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Site Recovery, paylaşılan disk kümelerini veya ağa bağlı depolama alanını (NAS) desteklemez. Ortamınızda bu yapılandırmalar varsa diğer yaklaşımlardan birini tercih edebilirsiniz. <br> Site Recovery, SMB 3.0 desteği sunmaz. Çoğaltılan VM yalnızca değişiklik yapılan dosyaların özgün konumda güncelleştirilmesi durumunda değişiklikleri uygular.<br>  Site Recovery, neredeyse zaman uyumlu bir veri çoğaltma işlemi sunar ve bu nedenle planlanmamış bir yük devretme senaryosu durumunda olası veri kaybı olabilir ve USN uyuşmazlığı sorunları oluşturabilir.
|DFSR'yi kullanan dosya sunucusu ortamı     |  [DFSR'yi bir Azure IaaS sanal makinesi ile genişletin](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      DFSR, bant genişliğinin oldukça düşük olduğu ortamlarda iyi performans sergiler. Bu yaklaşım için bir Azure VM'nin sürekli çalışır durumda olması gerekir. Planlamalarınıza VM'nin maliyetini de eklemeniz gerekir.         |
|Azure IaaS VM     |     Dosya Eşitleme    |     Olağanüstü durum kurtarma senaryosunda Dosya Eşitleme hizmetini kullanmanız durumunda yük devretme sırasında dosya paylaşımlarının istemci makineler tarafından fark edilmeden erişilebilir duruma gelmesini sağlamak için el ile yapmanız gereken eylemler vardır. Dosya Eşitleme için istemci makinenizde 445 numaralı bağlantı noktasının açık olması gerekir.     |


### <a name="site-recovery-support"></a>Site Recovery desteği
Site Recovery çoğaltma işlemi, uygulamadan bağımsız olduğu için bu önerilerin aşağıdaki senaryolar için geçerli olması beklenmektedir.

| Kaynak    |İkincil siteye    |Azure’a
|---------|---------|---------|
|Azure| -|Yes|
|Hyper-V|   Yes |Yes
|VMware |Yes|   Yes
|Fiziksel sunucu|   Yes |Yes
 

> [!IMPORTANT]
> Aşağıdaki üç yaklaşımdan birini seçmeden önce bu bağımlılıkların karşılandığından emin olun.



**Siteden siteye bağlantı**: Sunucular arasında iletişime izin vermek için şirket içi site ile Azure ağı arasında doğrudan bir bağlantı kurulması gerekir. Olağanüstü durum kurtarma sitesi olarak kullanılan Azure sanal ağı ile güvenli bir siteler arası VPN bağlantısı kurun. Daha fazla bilgi için bkz. [Şirket içi site ile Azure sanal ağı arasında siteler arası VPN bağlantısı oluşturma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Active Directory**: DFSR Active Directory bağımlıdır. Başka bir deyişle yerel etki alanı denetleyicilerine sahip olan bir Active Directory ormanı, Azure'da bulunan olağanüstü durum kurtarma sitesiyle genişletilir. DFSR kullanmıyor olsanız dahi erişim verilmesi veya erişim doğrulanması söz konusu olan kullanıcılar için bu adımları gerçekleştirmeniz gerekir. Daha fazla bilgi için bkz. [Şirket içi Active Directory ortamını Azure ile genişletme](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Azure IaaS sanal makineleri için olağanüstü durum kurtarma önerisi

Azure IaaS VM'lerinde barındırılan dosya sunucularında olağanüstü durum kurtarma yapılandırması ve yönetimi gerçekleştiriyorsanız [Azure Dosyaları](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)'na geçip geçmeme kararınıza göre iki seçenekten birini kullanabilirsiniz:

* [Dosya Eşitleme'yi kullanma](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Site Recovery'yi kullanma](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Bir IaaS sanal makinesinde barındırılan dosyaları Dosya Eşitleme ile çoğaltma

Azure Dosyaları geleneksel şirket içi dosya sunucularını veya NAS cihazlarını tamamen değiştirmek veya desteklemek için kullanılabilir. Azure dosya paylaşımları ayrıca verilerin kullanıldığı yerde performanslı ve dağıtılmış bir şekilde önbelleğe alınması için Dosya Eşitleme ile şirket içi veya bulut üzerindeki Windows sunucularda çoğaltılabilir. Aşağıdaki adımlarda, geleneksel dosya sunucularıyla aynı işlevi gören Azure VM'leri için olağanüstü durum kurtarma önerileri anlatılmaktadır:
* Makineleri Site Recovery kullanarak koruyun. [Bir Azure VM’yi başka bir Azure bölgesine çoğaltma](azure-to-azure-quickstart.md) bölümündeki adımları izleyin.
* Dosya Eşitleme'yi kullanarak dosya sunucusu olarak kullanılan VM'deki dosyaları buluta çoğaltın.
* Site Recovery [kurtarma planı](site-recovery-create-recovery-plans.md) özelliğini kullanarak [Azure dosya paylaşımını](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) bağlamak ve paylaşıma sanal makinenizden erişmek için betikler ekleyin.

Aşağıdaki adımlar, Dosya Eşitleme özelliğinin kullanımıyla ilgili özet bilgiler sunmaktadır:

1. [Azure'da bir depolama hesabı oluşturun](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Depolama hesaplarınız için okuma erişimli coğrafi olarak yedekli depolama seçeneğini kullanırsanız olağanüstü durum gerçekleştiğinde ikincil bölgedeki verilerinize okuma erişimine sahip olursunuz. Daha fazla bilgi için bkz. [Azure Storage 'Da olağanüstü durum kurtarma ve zorlamalı yük devretme (Önizleme)](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json).
2. [Dosya paylaşımı oluşturun](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. Azure dosya sunucunuzda [Dosya Eşitleme'yi başlatın](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide).
4. Eşitleme grubu oluşturun. Bir eşitleme grubu içindeki uç noktalar, birbiriyle eşitlenmiş durumda tutulur. Eşitleme grubu, bir Azure dosya paylaşımını temsil eden bir bulut uç noktası içermelidir. Eşitleme grubu ayrıca bir Windows sunucusu üzerindeki yolu temsil eden bir sunucu uç noktası içermelidir.
5. Dosyalarınız artık Azure dosya paylaşımında ve şirket içi sunucunuzda eşitlenmiş durumdadır.
6. Şirket içi ortamınızda olağanüstü durum oluşması halinde bir [kurtarma planı](site-recovery-create-recovery-plans.md) kullanarak yük devretme gerçekleştirin. [Azure dosya paylaşımını takma](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) ve paylaşıma sanal makinenizden erişme betiğini ekleyin.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Bir IaaS dosya sunucusu sanal makinesini Site Recovery ile çoğaltma

IaaS dosya sunucusu sanal makinesine erişen şirket içi istemcileriniz varsa aşağıdaki adımların tümünü gerçekleştirin. Aksi takdirde 3. adıma geçin.

1. Şirket içi ağınız ile Azure ağı arasında siteler arası VPN bağlantısı kurun.
2. Şirket içi Active Directory ortamını genişletin.
3. IaaS dosya sunucusu makinesi için ikincil bölgeye [olağanüstü durum kurtarma kurulumu](azure-to-azure-tutorial-enable-replication.md) gerçekleştirin.


İkincil bölgeye olağanüstü durum kurtarma gerçekleştirme hakkında daha fazla bilgi için [bu makaleyi](concepts-azure-to-azure-architecture.md) inceleyin.


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Site Recovery kullanarak bir şirket içi dosya sunucusunu çoğaltma

Aşağıdaki adımlar VMware VM'ye çoğaltmayı göstermektedir. Hyper-V VM'ye çoğaltma adımları için [bu öğreticiye](tutorial-hyper-v-to-azure.md) bakın.

1. Şirket içi makinelerin çoğaltması için [Azure kaynaklarını hazırlayın](tutorial-prepare-azure.md).
2. Şirket içi ağınız ile Azure ağı arasında siteler arası VPN bağlantısı kurun. 
3. Şirket içi Active Directory ortamını genişletin.
4. [Şirket içi VMware sunucuları hazırlayın](tutorial-prepare-on-premises-vmware.md).
5. Şirket içi VM’ler için Azure’da [olağanüstü durum kurtarmayı ayarlayın](tutorial-vmware-to-azure.md).

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>DFSR'yi bir Azure IaaS sanal makinesi ile genişletme

1. Şirket içi ağınız ile Azure ağı arasında siteler arası VPN bağlantısı kurun. 
2. Şirket içi Active Directory ortamını genişletin.
3. Azure sanal ağında [bir dosya sunucusu VM'si oluşturun ve sağlayın](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
Sanal makinenin şirket içi ortama bağlanabilen Azure sanal ağına eklendiğinden emin olun. 
4. Windows Server'a [DFSR'yi yükleyin ve yapılandırın](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx).
5. [Bir DFS ad alanı uygulayın](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. DFS ad alanı uygulandığında paylaşılan klasörlerin üretim ortamından olağanüstü durum kurtarma sitelerine yük devretme işlemi, DFS ad alanı klasör hedeflerinin güncelleştirilmesiyle gerçekleştirilebilir. Yapılan DFS ad alanı değişiklikleri Active Directory ile çoğaltıldıktan sonra kullanıcılar herhangi bir kesinti yaşamadan uygun klasör hedeflerine bağlanır.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Dosya Eşitleme ile şirket içi dosyaları çoğaltma
Dosyaları bulutta çoğaltmak için Dosya Eşitleme hizmetinden faydalanabilirsiniz. Olağanüstü durumlarda ve şirket içi dosya sunucunuz kullanım dışı olduğunda buluttan gerekli dosya konumlarını takabilir ve istemci makinelerinden gelen isteklere yanıt vermeye devam edebilirsiniz.
Dosya Eşitleme'yi Site Recovery ile tümleştirmek için:

* Site Recovery'yi kullanarak dosya sunucusu makinelerini koruyun. [Bu öğreticideki](tutorial-vmware-to-azure.md) adımları izleyin.
* Dosya Eşitleme'yi kullanarak dosya sunucusu olarak kullanılan makinedeki dosyaları buluta çoğaltın.
* Site Recovery'deki kurtarma planı özelliğini kullanarak Azure dosya paylaşımını Azure'daki yük devretme dosya sunucusu VM'sine takın.

Dosya Eşitleme'yi kullanmak için şu adımları izleyin:

1. [Azure'da bir depolama hesabı oluşturun](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Depolama hesaplarınız için okuma erişimli coğrafi olarak yedekli depolama seçeneğini kullanırsanız (önerilir) olağanüstü durum gerçekleştiğinde ikincil bölgedeki verilerinize okuma erişimine sahip olursunuz. Daha fazla bilgi için bkz. [Azure Storage 'Da olağanüstü durum kurtarma ve zorlamalı yük devretme (Önizleme)](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json).
2. [Dosya paylaşımı oluşturun](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Dosya Eşitleme'yi](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) şirket içi dosya sunucunuza dağıtın.
4. Eşitleme grubu oluşturun. Bir eşitleme grubu içindeki uç noktalar, birbiriyle eşitlenmiş durumda tutulur. Eşitleme grubu, bir Azure dosya paylaşımını temsil eden bir bulut uç noktası içermelidir. Eşitleme grubu ayrıca şirket içi Windows sunucusu üzerindeki yolu temsil eden bir sunucu uç noktası içermelidir.
5. Dosyalarınız artık Azure dosya paylaşımında ve şirket içi sunucunuzda eşitlenmiş durumdadır.
6. Şirket içi ortamınızda olağanüstü durum oluşması halinde bir [kurtarma planı](site-recovery-create-recovery-plans.md) kullanarak yük devretme gerçekleştirin. Azure dosya paylaşımını takma ve paylaşıma sanal makinenizden erişme betiğini ekleyin.

> [!NOTE]
> 445 numaralı bağlantı noktasının açık olduğundan emin olun. Azure Dosyaları SMB protokolünü kullanır. SMB, 445 numaralı TCP bağlantı noktası üzerinden iletişim kurar. Bir istemci makinesinden deneme yaparak güvenlik duvarınızın 445 numaralı TCP bağlantı noktasını engellemediğinden emin olun.


## <a name="do-a-test-failover"></a>Yük devretme testi gerçekleştirin

1. Azure portala gidin ve Kurtarma Hizmetleri kasanızı seçin.
2. Dosya sunucusu ortamı için oluşturulan kurtarma planını seçin.
3. **Yük Devretme Testi**'ni seçin.
4. Yük devretme sınamasını başlatmak için kurtarma noktasını ve Azure sanal ağını seçin.
5. İkincil ortam ayarlandıktan sonra doğrulama işlemlerini gerçekleştirin.
6. Doğrulama işlemleri tamamlandıktan sonra kurtarma planında **Yük devretme testini temizle**'yi seçin. Yük devretme testi ortamı temizlenir.

Yük devretme testi gerçekleştirme hakkında daha fazla bilgi için bkz. [Site Recovery yük devretme testi](site-recovery-test-failover-to-azure.md).

Active Directory ve DNS için yük devretme testi hakkında daha fazla bilgi için bkz. [Active Directory ve DNS için yük devretme testi konusunda dikkat edilmesi gerekenler](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Yük devretme gerçekleştirin

1. Azure portala gidin ve Kurtarma Hizmetleri kasanızı seçin.
2. Dosya sunucusu ortamı için oluşturulan kurtarma planını seçin.
3. **Yük devretme**'yi seçin.
4. Yük devretme işlemini başlatmak için kurtarma noktası seçin.

Yük devretme gerçekleştirme hakkında daha fazla bilgi için bkz. [Site Recovery'de yük devretme](site-recovery-failover.md).
