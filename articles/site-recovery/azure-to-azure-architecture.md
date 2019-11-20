---
title: Azure 'dan Azure 'a olağanüstü durum kurtarma mimarisi Azure Site Recovery
description: Azure Site Recovery hizmetini kullanarak Azure VM 'Leri için Azure bölgeleri arasında olağanüstü durum kurtarmayı ayarlarken kullanılan mimariye genel bakış.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: e83c14e5ce337e8a3c4c119acc2397b98afd5b56
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621115"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Azure'dan Azure'a olağanüstü durum kurtarma mimarisi


Bu makalede, [Azure Site Recovery](site-recovery-overview.md) hizmetini kullanarak Azure sanal makineleri (VM) için olağanüstü durum kurtarma dağıtırken kullanılan mimari, bileşenler ve süreçler açıklanmaktadır. Olağanüstü durum kurtarma kurulumu ile Azure VM 'Leri, farklı bir hedef bölgeye sürekli olarak çoğaltılır. Bir kesinti oluşursa, VM 'Lerin yükünü ikincil bölgeye devreder ve oradan bunlara erişebilirsiniz. Her şey normal şekilde çalıştığında, yeniden devreder ve birincil konumda çalışmaya devam edebilirsiniz.



## <a name="architectural-components"></a>Mimari bileşenler

Azure VM 'Leri için olağanüstü durum kurtarma ile ilgili bileşenler aşağıdaki tabloda özetlenmiştir.

**Bileşen** | **Gereksinimler**
--- | ---
**Kaynak bölgedeki sanal makineler** | [Desteklenen bir kaynak bölgedeki](azure-to-azure-support-matrix.md#region-support)Azure VM 'lerinden biridir.<br/><br/> Sanal makineler desteklenen herhangi bir [işletim sistemini](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)çalıştırıyor olabilir.
**Kaynak VM depolaması** | Azure VM 'Leri yönetilebilir veya depolama hesaplarına dağılmış yönetilmeyen disklere sahip olabilir.<br/><br/>Desteklenen Azure depolama [hakkında bilgi edinin](azure-to-azure-support-matrix.md#replicated-machines---storage) .
**Kaynak VM ağları** | VM 'Ler kaynak bölgedeki bir sanal ağdaki (VNet) bir veya daha fazla alt ağda yer alabilir. Ağ gereksinimleri hakkında [daha fazla bilgi edinin](azure-to-azure-support-matrix.md#replicated-machines---networking) .
**Önbellek depolama hesabı** | Kaynak ağda bir önbellek depolama hesabı gerekir. Çoğaltma sırasında, VM değişiklikleri hedef depolamaya gönderilmeden önce önbellekte depolanır.  Önbellek depolama hesaplarının standart olması gerekir.<br/><br/> Önbellek kullanmak, bir VM 'de çalışan üretim uygulamaları üzerinde en düşük etkiyi sağlar.<br/><br/> Önbellek depolama gereksinimleri hakkında [daha fazla bilgi edinin](azure-to-azure-support-matrix.md#cache-storage) . 
**Hedef kaynaklar** | Hedef kaynaklar çoğaltma sırasında ve bir yük devretme gerçekleştiğinde kullanılır. Site Recovery, varsayılan olarak hedef kaynağı ayarlayabilir veya bunları oluşturabilir/özelleştirebilirsiniz.<br/><br/> Hedef bölgede, VM 'Ler oluşturabiliyor ve aboneliğinizin hedef bölgede gerekli olacak VM boyutlarını desteklemek için yeterli kaynağa sahip olup olmadığını denetleyin. 

![Kaynak ve hedef çoğaltma](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Hedef kaynaklar

Bir sanal makine için çoğaltmayı etkinleştirdiğinizde Site Recovery, hedef kaynakları otomatik olarak oluşturma seçeneğini sağlar. 

**Hedef kaynak** | **Varsayılan ayar**
--- | ---
**Hedef abonelik** | Kaynak abonelikle aynı.
**Hedef kaynak grubu** | VM 'Lerin yük devretmeden sonra ait olduğu kaynak grubu.<br/><br/> Kaynak bölgesi dışında herhangi bir Azure bölgesinde olabilir.<br/><br/> Site Recovery, hedef bölgede "ASR" sonekine sahip yeni bir kaynak grubu oluşturur.<br/><br/>
**Hedef VNet** | Çoğaltılan VM 'Lerin yük devretme sonrasında bulunduğu sanal ağ (VNet). Kaynak ve hedef sanal ağlar arasında bir ağ eşlemesi oluşturulur ve tam tersi de geçerlidir.<br/><br/> Site Recovery, "ASR" sonekine sahip yeni bir VNet ve alt ağ oluşturur.
**Hedef depolama hesabı** |  VM, yönetilen bir disk kullanmıyorsa, verilerin çoğaltılacağı depolama hesabıdır.<br/><br/> Site Recovery, kaynak depolama hesabını yansıtmak için hedef bölgede yeni bir depolama hesabı oluşturur.
**Yönetilen çoğaltma diskleri** | VM yönetilen bir disk kullanıyorsa, bu, verilerin çoğaltılacağı yönetilen disklerdir.<br/><br/> Site Recovery, kaynağı yansıtmak için depolama bölgesinde çoğaltma tarafından yönetilen diskler oluşturur.
**Hedef kullanılabilirlik kümeleri** |  Çoğaltma VM 'lerinin yük devretme sonrasında bulunduğu kullanılabilirlik kümesi.<br/><br/> Site Recovery, kaynak konumdaki bir kullanılabilirlik kümesinde bulunan VM 'Ler için, hedef bölgede "ASR" sonekine sahip bir kullanılabilirlik kümesi oluşturur. Kullanılabilirlik kümesi varsa, kullanılır ve yeni bir tane oluşturulmaz.
**Hedef kullanılabilirlik alanları** | Hedef bölge kullanılabilirlik bölgelerini destekliyorsa Site Recovery, kaynak bölgede kullanılan aynı bölge numarasını atar.

### <a name="managing-target-resources"></a>Hedef kaynakları yönetme

Hedef kaynakları aşağıdaki şekilde yönetebilirsiniz:

- Çoğaltmayı etkinleştirdiğinizde hedef ayarları değiştirebilirsiniz.
- Hedef ayarları, çoğaltma zaten çalıştıktan sonra değiştirebilirsiniz. Özel durum kullanılabilirlik türüdür (tek örnek, küme veya bölge). Bu ayarı değiştirmek için çoğaltmayı devre dışı bırakmanız, ayarı değiştirmeniz ve ardından yeniden etkinleştirmeniz gerekir.



## <a name="replication-policy"></a>Çoğaltma ilkesi 

Azure VM çoğaltmasını etkinleştirdiğinizde, varsayılan olarak Site Recovery tabloda özetlenen varsayılan ayarlarla yeni bir çoğaltma ilkesi oluşturur.

**İlke ayarı** | **Ayrıntılar** | **Varsayılan**
--- | --- | ---
**Kurtarma noktası bekletme** | Site Recovery kurtarma noktalarını ne kadar süreyle tutacağını belirtir | 24 saat
**Uygulamayla tutarlı anlık görüntü sıklığı** | Site Recovery ne sıklıkta uygulamayla tutarlı bir anlık görüntü alır. | Her dört saatte bir

### <a name="managing-replication-policies"></a>Çoğaltma ilkelerini yönetme

Varsayılan çoğaltma ilkeleri ayarlarını yönetebilir ve şu şekilde değiştirebilirsiniz:
- Çoğaltmayı etkinleştirdiğiniz sürece ayarları değiştirebilirsiniz.
- Dilediğiniz zaman bir çoğaltma ilkesi oluşturabilir ve sonra çoğaltmayı etkinleştirdiğinizde uygulayabilirsiniz.

### <a name="multi-vm-consistency"></a>Çoklu VM tutarlılığı

VM 'Lerin birlikte çoğaltılmasını ve yük devretmeyle tutarlı ve uygulamayla tutarlı kurtarma noktalarına sahip olmasını istiyorsanız, bunları bir çoğaltma grubunda toplayabilirsiniz. Çoklu VM tutarlılığı, iş yükü performansını etkiler ve yalnızca tüm makineler arasında tutarlılık gerektiren iş yüklerini çalıştıran VM 'Ler için kullanılmalıdır. 



## <a name="snapshots-and-recovery-points"></a>Anlık görüntüler ve kurtarma noktaları

Kurtarma noktaları, belirli bir zaman noktasında alınan VM disklerinin anlık görüntülerinden oluşturulur. Bir VM 'nin yükünü devretmek için, hedef konumdaki VM 'yi geri yüklemek üzere bir kurtarma noktası kullanırsınız.

Yük devretme sırasında, genellikle VM 'nin bozulma veya veri kaybı olmadan başlamasını ve VM verilerinin işletim sistemi için ve VM 'de çalışan uygulamalar için tutarlı olmasını sağlamak istiyoruz. Bu, alınan anlık görüntülerin türüne bağlıdır.

Site Recovery anlık görüntüleri aşağıdaki gibi alır:

1. Site Recovery, varsayılan olarak verilerin çökme ile tutarlı anlık görüntülerini ve bunlar için bir sıklık belirtirseniz uygulamayla tutarlı anlık görüntüleri alır.
2. Kurtarma noktaları anlık görüntülerden oluşturulur ve çoğaltma ilkesindeki bekletme ayarlarına uygun olarak depolanır.

### <a name="consistency"></a>Tutarlılık

Aşağıdaki tabloda farklı türlerde tutarlılık açıklanmaktadır.

### <a name="crash-consistent"></a>Kilitlenme ile tutarlı

**Açıklama** | **Ayrıntılar** | **Önerilen**
--- | --- | ---
Kilitlenme ile tutarlı bir anlık görüntü, anlık görüntü çekilirken diskteki verileri yakalar. Bellekte herhangi bir şey içermez.<br/><br/> VM kilitlenirse veya güç kablosu anlık görüntünün alındığı anlık sunucudan çekilmişse mevcut olan disk üzerindeki verilerin eşdeğerini içerir.<br/><br/> Kilitlenme tutarlılığı, işletim sistemi veya VM 'deki uygulamalar için veri tutarlılığı garantisi vermez. | Site Recovery, varsayılan olarak her beş dakikada bir çökme ile tutarlı kurtarma noktaları oluşturur. Bu ayar değiştirilemez.<br/><br/>  | Günümüzde, çoğu uygulama kilitlenme ile tutarlı noktalarından iyi bir şekilde kurtarabilir.<br/><br/> Kilitlenme tutarlı kurtarma noktaları, genellikle işletim sistemlerinin ve DHCP sunucuları ve yazdırma sunucuları gibi uygulamaların çoğaltılması için yeterlidir.

### <a name="app-consistent"></a>Uygulamayla tutarlı

**Açıklama** | **Ayrıntılar** | **Önerilen**
--- | --- | ---
Uygulamayla tutarlı kurtarma noktaları, uygulamayla tutarlı anlık görüntülerden oluşturulur.<br/><br/> Uygulamayla tutarlı bir anlık görüntü, kilitlenme ile tutarlı bir anlık görüntüdeki tüm bilgileri, ayrıca bellekteki tüm verileri ve devam eden işlemleri içerir. | Uygulamayla tutarlı anlık görüntüler Birim Gölge Kopyası Hizmeti (VSS) kullanır:<br/><br/>   1) bir anlık görüntü başlatıldığında VSS, birimde bir kopyalama-yazma (COW) işlemi gerçekleştirir.<br/><br/>   2) COW 'yı gerçekleştirmeden önce VSS, makinede bellekte yerleşik verileri diske temizlemesi için gereken her uygulamayı bilgilendirir.<br/><br/>   3) VSS daha sonra yedekleme/olağanüstü durum kurtarma uygulamasının (Bu durumda Site Recovery) anlık görüntü verilerini okumasını ve devam etmesini sağlar. | Uygulamayla tutarlı anlık görüntüler, belirttiğiniz sıklığa göre yapılır. Bu sıklık, kurtarma noktalarını saklamak için ayarlamış olduğunuz her zaman daha az olmalıdır. Örneğin, varsayılan 24 saat ayarını kullanarak kurtarma noktalarını koruuyorsanız, sıklığı 24 saatten az olacak şekilde ayarlamanız gerekir.<br/><br/>Daha karmaşıktır ve kilitlenmeyle tutarlı anlık görüntülerden daha uzun sürer.<br/><br/> Çoğaltma için etkin bir VM üzerinde çalışan uygulamaların performansını etkiler. 

## <a name="replication-process"></a>Çoğaltma işlemi

Azure VM için çoğaltmayı etkinleştirdiğinizde aşağıdakiler olur:

1. Site Recovery Mobility hizmeti uzantısı, sanal makineye otomatik olarak yüklenir.
2. Uzantı, VM 'yi Site Recovery kaydeder.
3. VM için sürekli çoğaltma başlar.  Disk yazmaları, kaynak konumdaki önbellek depolama hesabına hemen aktarılır.
4. Site Recovery önbellekteki verileri işler ve hedef depolama hesabına veya çoğaltma ile yönetilen disklere gönderir.
5. Veriler işlendikten sonra, kilitlenme tutarlı kurtarma noktaları beş dakikada bir oluşturulur. Uygulamayla tutarlı kurtarma noktaları, çoğaltma ilkesinde belirtilen ayara göre oluşturulur.

![Çoğaltma işlemini etkinleştirme, 2. adım](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Çoğaltma işlemi**

## <a name="connectivity-requirements"></a>Bağlantı gereksinimleri

 Çoğaltılan Azure VM 'lerinin giden bağlantısı olması gerekir. Site Recovery VM 'ye yönelik bağlantı bağlantısı gerekmez. 

### <a name="outbound-connectivity-urls"></a>Giden bağlantı (URL 'Ler)

VM 'ler için giden erişim URL 'lerle denetleniyorsa, bu URL 'Lere izin verin.

| **URL** | **Ayrıntılar** |
| ------- | ----------- |
| *.blob.core.windows.net | Verilerin VM’den kaynak bölgedeki önbellek depolama hesabına yazılmasına izin verir. |
| login.microsoftonline.com | Site Recovery hizmet URL’leri için yetkilendirme ve kimlik doğrulama özellikleri sağlar. |
| *.hypervrecoverymanager.windowsazure.com | VM’nin Site Recovery hizmetiyle iletişim kurmasına izin verir. |
| *.servicebus.windows.net | VM’nin Site Recovery izleme ve tanılama verilerini yazmasına izin verir. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP adresi aralıkları için giden bağlantı

IP adreslerini kullanarak VM 'Ler için giden bağlantıyı denetlemek üzere bu adreslere izin verin.
Ağ bağlantısı gereksinimlerinin ayrıntılarının [ağ teknik incelemesi](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) 'nde bulunabileceğini lütfen unutmayın 

#### <a name="source-region-rules"></a>Kaynak bölgesi kuralları

**Kurallar** |  **Ayrıntılar** | **Hizmet etiketi**
--- | --- | --- 
HTTPS giden izin ver: bağlantı noktası 443 | Kaynak bölgedeki depolama hesaplarına karşılık gelen aralıklara izin ver | Depo.\<bölge adı >.
HTTPS giden izin ver: bağlantı noktası 443 | Azure Active Directory (Azure AD) öğesine karşılık gelen aralıklara izin verin.<br/><br/> Daha sonra Azure AD adresleri eklenirse yeni ağ güvenlik grubu (NSG) kuralları oluşturmanız gerekir.  | AzureActiveDirectory
HTTPS giden izin ver: bağlantı noktası 443 | Hedef konuma karşılık gelen [Site Recovery uç noktalarına](https://aka.ms/site-recovery-public-ips) erişime izin verin. 

#### <a name="target-region-rules"></a>Hedef bölge kuralları

**Kurallar** |  **Ayrıntılar** | **Hizmet etiketi**
--- | --- | --- 
HTTPS giden izin ver: bağlantı noktası 443 | Hedef bölgedeki depolama hesaplarına karşılık gelen aralıklara izin verin. | Depo.\<bölge adı >.
HTTPS giden izin ver: bağlantı noktası 443 | Azure AD 'ye karşılık gelen aralıklarına izin verin.<br/><br/> Daha sonra Azure AD adresleri eklenirse yeni NSG kuralları oluşturmanız gerekir.  | AzureActiveDirectory
HTTPS giden izin ver: bağlantı noktası 443 | Kaynak konuma karşılık gelen [Site Recovery uç noktalara](https://aka.ms/site-recovery-public-ips) erişime izin verin. 


#### <a name="control-access-with-nsg-rules"></a>NSG kurallarıyla erişimi denetleme

[NSG kurallarını](https://docs.microsoft.com/azure/virtual-network/security-overview)kullanarak Azure ağlarına/alt ağlarına ağ trafiğini FILTRELEYEREK VM bağlantısını kontrol ederseniz, aşağıdaki gereksinimleri dikkate alın:

- Kaynak Azure bölgesinin NSG kuralları, çoğaltma trafiği için giden erişime izin verilmelidir.
- Bir test ortamında, üretim ortamına girmeden önce kurallar oluşturmanızı öneririz.
- Tek tek IP adreslerine izin vermek yerine [hizmet etiketlerini](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) kullanın.
    - Hizmet etiketleri, güvenlik kuralları oluştururken karmaşıklığı en aza indirmek için birlikte toplanan bir IP adresi önekleri grubunu temsil eder.
    - Microsoft, zaman içinde hizmet etiketlerini otomatik olarak güncelleştirir. 
 
Site Recovery [giden bağlantı](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) hakkında daha fazla bilgi edinin ve [NSG 'ler ile bağlantıyı kontrol](concepts-network-security-group-with-site-recovery.md)edin.


### <a name="connectivity-for-multi-vm-consistency"></a>Çoklu VM tutarlılığı için bağlantı

Çoklu VM tutarlılığını etkinleştirirseniz çoğaltma grubundaki makineler birbiriyle 20004 numaralı bağlantı noktası üzerinden iletişim kurar.
- VM’ler arasında 20004 numaralı bağlantı noktası üzerinden gerçekleştirilen iç iletişimi engelleyen bir güvenlik duvarı gereci olmadığından emin olun.
- Linux VM’lerinin çoğaltma grubunun bir parçası olmasını istiyorsanız, 20004 numaralı bağlantı noktası üzerinden giden trafiğin, belirli Linux sürümünün kılavuzuna göre el ile açıldığından emin olun.




## <a name="failover-process"></a>Yük devretme işlemi

Yük devretme başlattığınızda VM 'Ler hedef kaynak grubunda, hedef sanal ağda, hedef alt ağda ve hedef kullanılabilirlik kümesinde oluşturulur. Yük devretme sırasında, herhangi bir kurtarma noktası kullanabilirsiniz.

![Yük devretme işlemi](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Sonraki adımlar

Bir Azure VM 'yi bir ikincil bölgeye [hızlıca çoğaltın](azure-to-azure-quickstart.md) .
