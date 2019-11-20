---
title: Azure Site Recovery ile Azrue VM 'lerinin devam eden çoğaltma sorunlarını giderme
description: Olağanüstü durum kurtarma için Azure sanal makinelerini çoğaltma sırasında oluşan hataları ve sorunları giderme
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.author: asgang
ms.openlocfilehash: 7b9da202704b20e5770343f857c044ea19ae696a
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620892"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Azure 'dan Azure VM çoğaltmasında devam eden sorunları giderme

Bu makalede, Azure sanal makinelerini bir bölgeden başka bir bölgeye çoğaltırken ve kurtarırken Azure Site Recovery karşılaşılan yaygın sorunlar açıklanmaktadır. Ayrıca, bu sorunların nasıl giderileceği açıklanmaktadır. Desteklenen konfigürasyonlar hakkında daha fazla bilgi için bkz. [Azure VM 'leri çoğaltmak için destek matrisi](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery, verileri düzenli olarak kaynak bölgesinden olağanüstü durum kurtarma bölgesine çoğaltır ve 5 dakikada bir çökme ile tutarlı bir kurtarma noktası oluşturur. Site Recovery 60 dakika boyunca kurtarma noktaları oluşturamıyorum, şu bilgilerle sizi uyarır:

Hata iletisi: "son 60 dakika içinde VM için kilitlenmeyle tutarlı bir kurtarma noktası yok."</br>
Hata KIMLIĞI: 153007 </br>

Aşağıdaki bölümlerde nedenler ve çözümler açıklanır.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Kaynak sanal makinede yüksek veri değişim oranı
Kaynak sanal makinedeki veri değişim oranı desteklenen limitlerden fazlaysa Azure Site Recovery bir olayı harekete geçirilir. Sorunun yüksek karmaşıklık nedeniyle olup olmadığını denetlemek için, **çoğaltılan öğeler** > **VM** > **Olaylar-Son 72 saat**' e gidin.
"Veri değişim oranı desteklenen limitlerin ötesinde" olayını görmeniz gerekir:

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Olayı seçerseniz, tam disk bilgilerini görmeniz gerekir:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Azure Site Recovery limitleri
Aşağıdaki tablo, Azure Site Recovery sınırlarını sağlar. Bu sınırlar, testlerimize dayalıdır, ancak olası tüm uygulama g/ç birleşimlerini kapsayamazlar. Gerçek sonuçlar, uygulamanızın G/Ç karışımına göre değişebilir. 

Göz önünde bulundurulması gereken iki sınır vardır, sanal makine başına disk başına veri dalgalanması ve veri değişim sıklığı vardır. Örneğin, aşağıdaki tabloda Premium P20 diskine göz atalım. Site Recovery VM başına en fazla beş MB 'lık disk başına 5 MB/s değişim işleyebilir, sanal makine başına 25 MB/sn toplam dalgalanma sınırı vardır.

**Çoğaltma depolama hedefi** | **Kaynak disk için Ortalama g/ç boyutu** |**Kaynak disk için Ortalama veri karmaşası** | **Kaynak veri diski için günlük toplam veri değişim sıklığı**
---|---|---|---
Standart depolama | 8 KB | 2 MB/sn | Disk başına 168 GB
Premium P10 veya P15 disk | 8 KB  | 2 MB/sn | Disk başına 168 GB
Premium P10 veya P15 disk | 16 KB | 4 MB/sn |  Disk başına 336 GB
Premium P10 veya P15 disk | 32 KB veya daha büyük | 8 MB/sn | Disk başına 672 GB
Premium P20 veya P30 veya P40 veya P50 disk | 8 KB    | 5 MB/sn | Disk başına 421 GB
Premium P20 veya P30 veya P40 veya P50 disk | 16 KB veya daha büyük |10 MB/sn | Disk başına 842 GB

### <a name="solution"></a>Çözüm
Azure Site Recovery, disk türüne göre veri değişim oranı için sınırlara sahiptir. Bu sorunun yinelenen veya kopan olduğunu anlamak için, etkilenen sanal makinenin veri değişikliği oranını bulun. Kaynak sanal makineye gidin, **izleme**bölümündeki ölçümleri bulun ve bu ekran görüntüsünde gösterildiği gibi ölçümleri ekleyin:

![Veri değişikliği oranını bulmak için üç adımlı işlem](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. **Ölçüm Ekle**' yi seçin ve **Işletim sistemi diski yazma bayt/sn** ve **veri diski yazma bayt/sn**ekleyin.
2. Ekran görüntüsünde gösterildiği gibi ani artış izleyin.
3. İşletim sistemi disklerinde oluşan toplam yazma işlemlerini ve tüm veri disklerini görüntüleyin. Bu ölçümler size disk başına düzeyde bilgi veremeyebilir, ancak toplam veri değişim modelini gösterir.

Ani bir veri patlama ve veri değişim oranı 10 MB/sn 'den (Premium için) ve 2 MB/sn 'den (Standart için) daha büyükse, çoğaltma gerçekleştirilir. Ancak, karmaşıklığın çoğu zaman desteklenen sınırın ötesinde, mümkünse şu seçeneklerden birini göz önünde bulundurun:

* **Yüksek veri değişim hızına neden olan diski hariç tutun**: [PowerShell](./azure-to-azure-exclude-disks.md)kullanarak diski dışlayabilirsiniz. Diski dışlamak için önce çoğaltmayı devre dışı bırakmanız gerekir. 
* **Olağanüstü durum kurtarma depolama diskinin katmanını değiştirme**: Bu seçenek yalnızca disk verileri KARMAŞıKLıĞı 20 MB/sn 'den az olduğunda mümkündür. P10 diskine sahip bir VM 'nin, 8 MB/sn 'den büyük ancak 10 MB/sn 'tan küçük bir veri dalgalanmasına sahip olduğunu varsayalım. Müşteri, koruma sırasında hedef depolama için bir P30 disk kullanıyorsa, sorun çözülebilir. Bu çözümün yalnızca Premium yönetilen diskleri kullanan makineler için mümkün olduğunu unutmayın. Aşağıdaki adımları izleyin:
    - Etkilenen çoğaltılan makinenin diskler dikey penceresine gidin ve çoğaltma diski adını kopyalayın
    - Bu çoğaltma yönetilen diskine git
    - Genel Bakış dikey penceresinde bir SAS URL 'sinin oluşturulduğunu söyleyen bir başlık görebilirsiniz. Bu başlık üzerine tıklayın ve dışarı aktarmayı iptal edin. Başlığı görmüyorsanız bu adımı yoksayın.
    - SAS URL 'SI iptal edildiğinde, yönetilen diskin yapılandırma dikey penceresine gidin ve ASR 'nin kaynak diskte gözlemlenen dalgalanma oranını desteklemesi için boyutu artırın

## <a name="Network-connectivity-problem"></a>Ağ bağlantısı sorunları

### <a name="network-latency-to-a-cache-storage-account"></a>Önbellek depolama hesabına yönelik ağ gecikmesi
Site Recovery, çoğaltılan verileri önbellek depolama hesabına gönderir. Bir sanal makineden önbellek depolama hesabına veri yükleme işlemi 3 saniye içinde 4 MB 'den daha yavaşsa ağ gecikme süresi görebilirsiniz. 

Gecikmeyle ilgili bir sorun olup olmadığını denetlemek için [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) komutunu kullanarak sanal makineden önbellek depolama hesabına veri yükleyin. Gecikme süresi yüksekse, VM 'lerden giden ağ trafiğini denetlemek için bir ağ sanal gereci (NVA) kullanıp kullandığınızı kontrol edin. Tüm çoğaltma trafiği NVA üzerinden geçerse gereç azalmasıyla karşılaşabilirsiniz. 

Çoğaltma trafiğinin NVA 'ya gitmemesi için, sanal ağınızda "depolama" için bir ağ hizmeti uç noktası oluşturmanız önerilir. Daha fazla bilgi için bkz. [ağ sanal gereç yapılandırması](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Ağ bağlantısı
Site Recovery çoğaltmanın çalışması için, VM 'den belirli URL 'Lere veya IP aralıklarına giden bağlantı gerekir. VM 'niz bir güvenlik duvarının arkasındaysa veya giden bağlantıyı denetlemek için ağ güvenlik grubu (NSG) kuralları kullanıyorsa, bu sorunlardan biri yüz yüze gelebilir. Tüm URL 'Lerin bağlı olduğundan emin olmak için bkz. [Site Recovery URL 'ler Için giden bağlantı](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Hata KIMLIĞI 153006-son ' XXX ' dakika içinde VM için uygulamayla tutarlı bir kurtarma noktası yok

En yaygın sorunlardan bazıları aşağıda listelenmiştir

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Neden 1: SQL Server 2008/2008 R2 'de bilinen sorun 
**Nasıl düzeltileceğini öğrenin** : SQL Server 2008/2008 R2 ile ilgili bilinen bir sorun var. Lütfen bu KB makalesine bakın [Azure Site Recovery aracı veya bileşen olmayan DIĞER VSS yedeklemesi SQL Server 2008 R2 barındıran bir sunucu için başarısız oluyor](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>2\. neden: Azure Site Recovery işlerin, AUTO_CLOSE DBs ile SQL Server örneklerinin herhangi bir sürümünü barındıran sunucularda başarısız olması 
**Nasıl düzeltilir** : KB [makalesine](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) başvurun 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Neden 3: SQL Server 2016 ve 2017 ' de bilinen sorun
**Nasıl düzeltilir** : KB [makalesine](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) başvurun 

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>Neden 4: depolama alanları doğrudan yapılandırması kullanıyorsunuz
**Nasıl düzeltileceğini öğrenin** : Azure Site Recovery depolama alanları doğrudan yapılandırması için uygulamayla tutarlı bir kurtarma noktası oluşturamıyor. [Çoğaltma ilkesini](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms) doğru şekilde yapılandırmak için lütfen makaleye başvurun

### <a name="more-causes-due-to-vss-related-issues"></a>VSS ile ilgili sorunlardan kaynaklanan nedenler:

Daha fazla sorun gidermek için, hata kodunu tam olarak almak için kaynak makinedeki dosyaları kontrol edin:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Dosyadaki hatalar nasıl konumlandırsın?
Bir düzenleyicide boş olan bir dosyayı açarak "boş" dizesini arayın
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Yukarıdaki örnekte **2147754994** , hatayı aşağıda gösterildiği gibi bildiren hata kodudur

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS yazıcısı yüklü değil-hata 2147221164 

*Nasıl düzeltilir*: uygulama tutarlılığı etiketi oluşturmak için, Azure Site Recovery Microsoft birim gölge kopyası hizmeti 'NI (VSS) kullanır. Uygulama tutarlılığı anlık görüntülerini almak için işlemi için bir VSS sağlayıcısı yüklenir. Bu VSS sağlayıcısı bir hizmet olarak yüklendi. VSS sağlayıcısı hizmetinin yüklü olmaması durumunda, uygulama tutarlılığı anlık görüntüsü oluşturma işlemi, 0x80040154 "sınıf kayıtlı değil" hata kimliğiyle başarısız olur. </br>
[VSS yazıcı yükleme sorunlarını giderme makalesine](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) bakın 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS yazıcı devre dışı-hata 2147943458

**Nasıl düzeltilir**: uygulama tutarlılığı etiketi oluşturmak için, Azure Site Recovery Microsoft birim gölge kopyası hizmeti 'NI (VSS) kullanır. Uygulama tutarlılığı anlık görüntülerini almak için işlemi için bir VSS sağlayıcısı yüklenir. Bu VSS sağlayıcısı bir hizmet olarak yüklendi. VSS sağlayıcısı hizmetinin devre dışı bırakılması durumunda, uygulama tutarlılığı anlık görüntüsü oluşturma işlemi hata kimliğiyle başarısız olur "belirtilen hizmet devre dışı bırakıldı ve başlatılamıyor (0x80070422)". </br>

- VSS devre dışıysa,
    - VSS sağlayıcı hizmetinin başlangıç türünün **Otomatik**olarak ayarlandığını doğrulayın.
    - Aşağıdaki hizmetleri yeniden başlatın:
        - VSS hizmeti
        - VSS sağlayıcısı Azure Site Recovery
        - VDS hizmeti

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS sağlayıcısı NOT_REGISTERED-hata 2147754756

**Nasıl düzeltilir**: uygulama tutarlılığı etiketi oluşturmak için, Azure Site Recovery Microsoft birim gölge kopyası hizmeti 'NI (VSS) kullanır. Azure Site Recovery VSS sağlayıcısı hizmeti 'nin yüklü olup olmadığını denetleyin. </br>

- Aşağıdaki komutları kullanarak sağlayıcı yüklemesini yeniden deneyin:
- Mevcut sağlayıcıyı kaldır: C:\Program Files (x86) \Microsoft Azure Site Recovery\lationt\ınmagevssprovider_uninstall.cmd
- Yeniden yükle: C:\Program Files (x86) \Microsoft Azure Site Recovery\lationt\ınmagevssprovider_ınstall.cmd
 
VSS sağlayıcı hizmetinin başlangıç türünün **Otomatik**olarak ayarlandığını doğrulayın.
    - Aşağıdaki hizmetleri yeniden başlatın:
        - VSS hizmeti
        - VSS sağlayıcısı Azure Site Recovery
        - VDS hizmeti
