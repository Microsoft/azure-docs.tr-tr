---
title: Azure Site Recovery 'de VMware VM olağanüstü durum kurtarma mimarisi
description: Bu makalede, Azure Site Recovery ile şirket içi VMware VM 'lerinin olağanüstü durum kurtarması ayarlanırken kullanılan bileşenlere ve mimariye ilişkin genel bir bakış sunulmaktadır
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.openlocfilehash: 24333ccc5096e7f04f016444de2b0a7e13ae7bfa
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579819"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware 'den Azure 'a olağanüstü durum kurtarma mimarisi

Bu makalede, [Azure Site Recovery](site-recovery-overview.md) hizmetini kullanarak şirket Içi bir VMware sitesi ve Azure arasında VMware sanal makinelerini (VM 'ler) kullanarak bir olağanüstü durum kurtarma çoğaltması, yük devretme ve kurtarma işlemi gerçekleştirdiğinizde kullanılan mimari ve süreçler açıklanmaktadır.


## <a name="architectural-components"></a>Mimari bileşenler

Aşağıdaki tablo ve grafik, Azure 'da VMware olağanüstü durum kurtarma için kullanılan bileşenlerin üst düzey bir görünümünü sağlar.

**Bileşen** | **Gereksinim** | **Ayrıntılar**
--- | --- | ---
**Azure** | Bir Azure aboneliği, önbellek, yönetilen disk ve Azure ağı için Azure depolama hesabı. | Şirket içi VM 'lerden çoğaltılan veriler Azure depolama 'da depolanır. Şirket içinden Azure 'a yük devretme gerçekleştirdiğinizde, çoğaltılan verilerle Azure VM 'Ler oluşturulur. Azure VM’leri oluşturulduğunda Azure sanal ağına bağlanır.
**Yapılandırma sunucusu makinesi** | Tek bir şirket içi makine. Bunu, indirilen bir OVF şablonundan dağıtılabilecek bir VMware VM olarak çalıştırmanızı öneririz.<br/><br/> Makine, yapılandırma sunucusu, işlem sunucusu ve ana hedef sunucu dahil olmak üzere tüm şirket içi Site Recovery bileşenlerini çalıştırır. | **Yapılandırma sunucusu**: Şirket Içi ve Azure arasındaki iletişimleri koordine eder ve veri çoğaltmasını yönetir.<br/><br/> **İşlem sunucusu**: yapılandırma sunucusunda varsayılan olarak yüklenir. Çoğaltma verilerini alır; önbelleğe alma, sıkıştırma ve şifreleme ile en iyi duruma getirir; ve bunu Azure depolama 'ya gönderir. İşlem sunucusu aynı zamanda çoğaltmak istediğiniz VM’lere Azure Site Recovery Mobility Hizmetini yükler ve şirket içi makinelerinin otomatik olarak bulunmasını sağlar. Dağıtımınız büyüdükçe, daha büyük hacimli çoğaltma trafiğini işlemek için ek ve ayrı işlem sunucuları ekleyebilirsiniz.<br/><br/> **Ana hedef sunucusu**: yapılandırma sunucusunda varsayılan olarak yüklenir. Azure 'dan yeniden çalışma sırasında çoğaltma verilerini işler. Büyük dağıtımlar için yeniden çalışma için ek ve ayrı bir ana hedef sunucu ekleyebilirsiniz.
**VMware sunucuları** | VMware VM 'Leri, şirket içi vSphere ESXi sunucularında barındırılır. Konakları yönetmek için bir vCenter sunucusu önerilir. | Site Recovery dağıtımı sırasında, kurtarma hizmetleri kasasına VMware sunucuları eklersiniz.
**Çoğaltılan makineler** | Mobility hizmeti, çoğaltılan her VMware VM 'ye yüklenir. | İşlem sunucusundan otomatik yüklemeye izin vermeniz önerilir. Alternatif olarak, hizmeti el ile yükleyebilir veya Configuration Manager gibi bir otomatik dağıtım yöntemi kullanabilirsiniz.

![VMware 'den Azure 'a çoğaltma mimarisi ilişkilerini gösteren diyagram.](./media/vmware-azure-architecture/arch-enhanced.png)

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
| Çoğaltma               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.us`   | VM’nin Site Recovery hizmetiyle iletişim kurmasına izin verir. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | VM’nin Site Recovery izleme ve tanılama verilerini yazmasına izin verir. |

Şirket içi Azure Site Recovery altyapısı ve Azure hizmetleri arasındaki iletişim için filtrelenecek URL 'lerin ayrıntılı listesi için, [Önkoşullar makalesindeki ağ gereksinimleri bölümüne](vmware-azure-deploy-configuration-server.md#prerequisites)bakın.

## <a name="replication-process"></a>Çoğaltma işlemi

1. Bir sanal makine için çoğaltmayı etkinleştirdiğinizde, Azure Storage 'a ilk çoğaltma, belirtilen çoğaltma ilkesi kullanılarak başlar. Şunlara dikkat edin:
    - VMware VM 'Leri için çoğaltma, sanal makine üzerinde çalışan Mobility hizmeti Aracısı kullanılarak, blok düzeyinde, neredeyse sürekli çalışır.
    - Herhangi bir çoğaltma ilkesi ayarı uygulanır:
        - **RPO eşiği**. Bu ayar çoğaltmayı etkilemez. İzlemeye yardımcı olur. Geçerli RPO belirttiğiniz eşik sınırını aşarsa bir olay oluşur ve isteğe bağlı olarak bir e-posta gönderilir.
        - **Kurtarma noktası bekletme**. Bu ayar, bir kesinti oluştuğunda ne kadar süre geri dönmek istediğinizi belirtir. Premium depolamada maksimum bekletme 24 saattir. Standart depolamada 72 saat olur. 
        - **Uygulamayla tutarlı anlık görüntüler**. Uygulama gereksinimlerinize bağlı olarak, uygulamayla tutarlı anlık görüntü her 1 ile 12 saat arasında alınabilir. Anlık görüntüler standart Azure Blob anlık görüntüleridir. Bir VM üzerinde çalışan Mobility Aracısı, bu ayara uygun olarak bir VSS anlık görüntüsü ister ve o zaman, çoğaltma akışında uygulamayla tutarlı bir nokta olarak zaman içinde yer alır.

2. Trafik, internet üzerinden Azure depolama genel uç noktalarına çoğaltılır. Alternatif olarak, Azure ExpressRoute 'u [Microsoft eşlemesi](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)ile de kullanabilirsiniz. Şirket içi siteden Azure 'a siteden siteye sanal özel ağ (VPN) üzerinden trafik çoğaltma desteklenmez.
3. İlk çoğaltma işlemi, çoğaltma etkinleştirme sırasında makinedeki tüm verilerin Azure 'a gönderilmesini sağlar. İlk çoğaltma tamamlandıktan sonra, Delta değişikliklerinin Azure 'a çoğaltılması başlar. Bir makine için izlenen değişiklikler işlem sunucusuna gönderilir.
4. İletişim şu şekilde gerçekleşir:

    - VM 'Ler, çoğaltma yönetimi için HTTPS 443 gelen bağlantı noktasında şirket içi yapılandırma sunucusu ile iletişim kurar.
    - Yapılandırma sunucusu, HTTPS 443 giden bağlantı noktası üzerinden Azure ile çoğaltmayı düzenler.
    - VM 'Ler, çoğaltma verilerini işlem sunucusuna (yapılandırma sunucusu makinesinde çalışan), HTTPS 9443 gelen bağlantı noktası üzerinden gönderir. Bu bağlantı noktası değiştirilebilir.
    - İşlem sunucusu çoğaltma verilerini alır, bu verileri iyileştirir ve şifreler ve 443 giden bağlantı noktası üzerinden Azure depolama 'ya gönderir.
5. Çoğaltma verileri günlüğü, ilk olarak Azure 'da bir önbellek depolama hesabında yer açar. Bu Günlükler işlenir ve veriler bir Azure yönetilen diskinde depolanır (ASR çekirdek diski olarak adlandırılır). Kurtarma noktaları bu diskte oluşturulur.

![VMware 'den Azure 'a çoğaltma işlemini gösteren diyagram.](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Yeniden eşitleme işlemi

1. Her zaman, ilk çoğaltma sırasında veya Delta değişikliklerini aktarırken, kaynak makine ile işlem sunucusu arasında veya Azure 'a işlem sunucusu arasında ağ bağlantısı sorunları olabilir. Bunlardan biri, Azure 'a veri aktarımında anlık olarak hatalara neden olabilir.
2. Veri bütünlüğü sorunlarını önlemek ve veri aktarımı maliyetlerini en aza indirmek için Site Recovery bir makineyi yeniden eşitleme için işaretler.
3. Bir makine, Azure 'da depolanan kaynak makine ve veriler arasında tutarlılık sağlamak için aşağıdakiler gibi durumlarda yeniden eşitleme için de işaretlenebilir
    - Bir makine, zorla zorla zorlayarak kapanmaya başlarsa
    - Bir makine, disk yeniden boyutlandırma (disk boyutunu 2 TB ile 4 TB arasında değiştirme) gibi değişikliklere dayalı değişiklikler varsa
4. Yeniden eşitleme yalnızca Azure 'a Delta verileri gönderir. Kaynak makine ve Azure 'da depolanan veriler arasındaki veri toplamlarını hesaplayarak, şirket içi ve Azure arasında veri aktarımı, en aza indirilmiştir.
5. Varsayılan olarak yeniden eşitleme, ofis saatleri dışında otomatik olarak çalışacak şekilde zamanlanır. Saatlerin dışında varsayılan yeniden eşitleme beklemek istemiyorsanız, bir VM 'yi el ile yeniden eşitleyebilirsiniz. Bunu yapmak için Azure portal adresine gidin, yeniden **eşitle**> VM 'yi seçin.
6. Varsayılan yeniden eşitleme, ofis saatleri dışında başarısız olursa ve el ile müdahale gerekliyse, Azure portal içindeki belirli bir makinede bir hata oluşturulur. Hatayı çözümleyebilir ve yeniden eşitlemeyi el ile tetikleyebilirsiniz.
7. Yeniden eşitleme tamamlandıktan sonra, Delta değişikliklerinin çoğaltılması sürdürülecek.

## <a name="replication-policy"></a>Çoğaltma ilkesi 

Azure VM çoğaltmasını etkinleştirdiğinizde, varsayılan olarak Site Recovery tabloda özetlenen varsayılan ayarlarla yeni bir çoğaltma ilkesi oluşturur.

**İlke ayarı** | **Ayrıntılar** | **Varsayılanını**
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

**Açıklama** | **Ayrıntılar** | **Öneri**
--- | --- | ---
Kilitlenme ile tutarlı bir anlık görüntü, anlık görüntü çekilirken diskteki verileri yakalar. Bellekte herhangi bir şey içermez.<br/><br/> VM kilitlenirse veya güç kablosu anlık görüntünün alındığı anlık sunucudan çekilmişse mevcut olan disk üzerindeki verilerin eşdeğerini içerir.<br/><br/> Kilitlenme tutarlılığı, işletim sistemi veya VM 'deki uygulamalar için veri tutarlılığı garantisi vermez. | Site Recovery, varsayılan olarak her beş dakikada bir çökme ile tutarlı kurtarma noktaları oluşturur. Bu ayar değiştirilemez.<br/><br/>  | Günümüzde, çoğu uygulama kilitlenme ile tutarlı noktalarından iyi bir şekilde kurtarabilir.<br/><br/> Kilitlenme tutarlı kurtarma noktaları, genellikle işletim sistemlerinin ve DHCP sunucuları ve yazdırma sunucuları gibi uygulamaların çoğaltılması için yeterlidir.

### <a name="app-consistent"></a>Uygulamayla tutarlı

**Açıklama** | **Ayrıntılar** | **Öneri**
--- | --- | ---
Uygulamayla tutarlı kurtarma noktaları, uygulamayla tutarlı anlık görüntülerden oluşturulur.<br/><br/> Uygulamayla tutarlı bir anlık görüntü, kilitlenme ile tutarlı bir anlık görüntüdeki tüm bilgileri, ayrıca bellekteki tüm verileri ve devam eden işlemleri içerir. | Uygulamayla tutarlı anlık görüntüler Birim Gölge Kopyası Hizmeti (VSS) kullanır:<br/><br/>   1) Azure Site Recovery Microsoft SQL 'in işlem günlüğü yedekleme süresini ve sıra numarasını değiştirmayan yalnızca kopyalama yedekleme (VSS_BT_COPY) yöntemini kullanır </br></br> 2) bir anlık görüntü başlatıldığında VSS, birimde bir kopyalama-yazma (COW) işlemi gerçekleştirir.<br/><br/>   3) COW 'yı gerçekleştirmeden önce VSS, makinede bellekte yerleşik verileri diske temizlemesi için gereken her uygulamayı bilgilendirir.<br/><br/>   4) VSS daha sonra yedekleme/olağanüstü durum kurtarma uygulamasının (Bu durumda Site Recovery) anlık görüntü verilerini okumasını ve devam etmesini sağlar. | Uygulamayla tutarlı anlık görüntüler, belirttiğiniz sıklığa göre yapılır. Bu sıklık, kurtarma noktalarını saklamak için ayarlamış olduğunuz her zaman daha az olmalıdır. Örneğin, varsayılan 24 saat ayarını kullanarak kurtarma noktalarını koruuyorsanız, sıklığı 24 saatten az olacak şekilde ayarlamanız gerekir.<br/><br/>Daha karmaşıktır ve kilitlenmeyle tutarlı anlık görüntülerden daha uzun sürer.<br/><br/> Çoğaltma için etkin bir VM üzerinde çalışan uygulamaların performansını etkiler. 

## <a name="failover-and-failback-process"></a>Yük devretme ve yeniden çalışma işlemi

Çoğaltma kurulduktan sonra ve her şeyin beklendiği gibi çalıştığını denetlemek için bir olağanüstü durum kurtarma detayına (yük devretme testi) çalıştırmanızın ardından, gerektiğinde yük devretme ve yeniden çalışma işlemi gerçekleştirebilirsiniz.

1. Tek bir makine için başarısız olarak çalıştırın veya aynı anda birden fazla VM 'nin yükünü devretmek için bir kurtarma planı oluşturun. Tek makineli yük devretme yerine kurtarma planının avantajı şunlardır:
    - Tek bir kurtarma planında uygulama genelinde tüm VM 'Leri dahil ederek uygulama bağımlılıklarını modelleyebilirsiniz.
    - El ile gerçekleştirilen eylemler için betikler, Azure runbook 'lar ekleyebilir ve duraklatabilirsiniz.
2. İlk yük devretmeyi tetikledikten sonra, Azure VM 'den iş yüküne erişmeye başlamak için bunu işleyin.
3. Birincil şirket içi siteniz yeniden kullanılabilir olduğunda, tekrar yük devretmek için hazırlık yapabilirsiniz. Yeniden yük devretmek için aşağıdakiler de dahil olmak üzere bir yeniden çalışma altyapısı ayarlamanız gerekir:

    * **Azure 'Da geçici işlem sunucusu**: Azure 'dan yeniden yük devretmek Için BIR Azure VM 'yi, Azure 'dan çoğaltmayı işleyecek bir işlem sunucusu olarak davranacak şekilde ayarlarsınız. Yeniden çalışma sona erdikten sonra bu VM'yi silebilirsiniz.
    * **VPN bağlantısı**: geri dönmek Için, Azure ağından şirket içi sıteye bir VPN bağlantısına (veya ExpressRoute) sahip olmanız gerekir.
    * **Ayrı ana hedef sunucusu**: varsayılan olarak, şirket ıçı VMware VM 'de yapılandırma sunucusu ile yüklenen ana hedef sunucu yeniden çalışmayı işler. Büyük hacimlerde trafik geri yüklemeniz gerekiyorsa, bu amaçla ayrı bir şirket içi ana hedef sunucusu ayarlayın.
    * **Yeniden çalışma ilkesi**: Şirket içi sitenize geri çoğaltmak için bir yeniden çalışma ilkeniz olmalıdır. Bu ilke, Şirket içinden Azure 'a bir çoğaltma ilkesi oluşturduğunuzda otomatik olarak oluşturulur.
4. Bileşenler oluşturulduktan sonra, yeniden çalışma üç eylem halinde gerçekleşir:

    - 1. Aşama: Azure 'dan şirket içi VMware VM 'lerine geri çoğaltılbilmeleri için Azure VM 'lerini yeniden koruyun.
    -  2. Aşama: şirket içi sitede yük devretme çalıştırın.
    - 3. Aşama: iş yükleri yeniden başlatıldıktan sonra, şirket içi VM 'Ler için çoğaltmayı yeniden etkinleştirin.
    
 

![Azure 'dan VMware yeniden çalışma gösteren diyagram.](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Sonraki adımlar

VMware 'den Azure 'a çoğaltma özelliğini etkinleştirmek için [Bu öğreticiyi](vmware-azure-tutorial.md) izleyin.
