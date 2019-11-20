---
title: Azure Site Recovery ile Azure VM olağanüstü durum kurtarma hakkında sık sorulan sorular
description: Bu makalede, Azure VM 'lerinin olağanüstü durum kurtarma ile ilgili yaygın sorular Azure Site Recovery kullanarak başka bir Azure bölgesine yanıt verir
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 04/29/2019
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 5ed501a9f11e790bcc2196d57c6479beb54f1a17
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621063"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Sık sorulan sorular: Azure 'dan Azure 'a olağanüstü durum kurtarma

Bu makalede, Azure VM 'lerinin olağanüstü durum kurtarma hakkında yaygın soruların yanıtlarını [Site Recovery](site-recovery-overview.md)kullanarak başka bir Azure bölgesine bulabilirsiniz. 


## <a name="general"></a>Genel

### <a name="how-is-site-recovery-priced"></a>Site Recovery nasıl fiyatlandırılır?
[Azure Site Recovery fiyatlandırma](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) ayrıntılarını gözden geçirin.
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Azure Site Recovery için ücretsiz katman nasıl çalışır?
Azure Site Recovery ile korunan her örnek, ilk 31 gün boyunca ücretsiz olarak korunur. 32. günden itibaren örneğin korunması yukarıdaki fiyatlarla ücretlendirilir.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>İlk 31 gün boyunca başka herhangi bir Azure hizmeti için ücretlendirilir miyim?
Evet, Azure Site Recovery korunan bir örnek için ilk 31 gün boyunca ücretsiz olsa da Azure Depolama, depolama işlemleri ve veri aktarımı için ücretlendirilmeye devam edebilirsiniz. Korunan bir sanal makine için de Azure işlem ücretleri alınabilir. Fiyatlandırma hakkında ayrıntılı bilgi edinmek için [buraya](https://azure.microsoft.com/pricing/details/site-recovery) bakın

### <a name="where-can-i-find-best-practices-for-azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma için en iyi yöntemleri nereden bulabilirim? 
1. [Azure 'dan Azure 'a mimariyi anlama](azure-to-azure-architecture.md)
2. [Desteklenen ve desteklenmeyen konfigürasyonları gözden geçirin](azure-to-azure-support-matrix.md)
3. [Azure VM 'Leri için olağanüstü durum kurtarmayı ayarlama](azure-to-azure-how-to-enable-replication.md)
4. [Yük devretme testi çalıştırma](azure-to-azure-tutorial-dr-drill.md)
5. [Yük devretme ve birincil bölgeye dönme](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-the-target-region"></a>Hedef bölgede kapasite garantisi nedir?
Site Recovery ekibi, yeterli altyapı kapasitesi planlamak için Azure kapasite yönetimi ekibiyle çalışır ve yük devretme işlemi başlatıldığında Site Recovery tarafından korunan VM 'Lerin hedef bölgeye başarıyla dağıtılmasını sağlamaya yardımcı olur.

## <a name="replication"></a>Çoğaltma

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Azure disk şifrelemesi aracılığıyla etkinleştirilen VM 'Leri çoğaltabilir miyim?

Evet, Site Recovery Azure disk şifrelemesi (ADE) etkinleştirilmiş VM 'lerin olağanüstü durum kurtarmasını destekler. Çoğaltmayı etkinleştirdiğinizde, gereken tüm disk şifreleme anahtarları ve gizli dizileri, kaynak bölgeden Kullanıcı bağlamındaki hedef bölgeye kopyalanır. Uygun izniniz yoksa, anahtar ve gizli dizileri kopyalamak için güvenli bir komut dosyası güvenlik yöneticisine eklenebilir.

- Site Recovery, Windows çalıştıran Azure VM 'Leri için ADE 'yi destekler.
- Site Recovery, AAD olmadan Azure Active Directory (AAD) ve sürüm 1,1 kullanan bir şema ile ADE sürüm 0,1 ' ü destekler. [Daha fazla bilgi edinin](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schemata).
- ADE sürüm 1,1, Windows VM 'lerinin yönetilen diskler kullanılması gerekir.
- Şifrelenmiş VM 'Ler için çoğaltmayı etkinleştirme hakkında [daha fazla bilgi edinin](azure-to-azure-how-to-enable-replication-ade-vms.md) .



### <a name="can-i-replicate-vms-to-another-subscription"></a>VM 'Leri başka bir aboneliğe çoğaltabilir miyim?
Evet, Azure VM 'Leri aynı Azure AD kiracısı içindeki farklı bir aboneliğe çoğaltabilirsiniz.
[Abonelikler arasında](https://azure.microsoft.com/blog/cross-subscription-dr) Dr yapılandırma basittir. Çoğaltma sırasında başka bir abonelik seçebilirsiniz.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Bölgeye sabitlenmiş Azure sanal makinelerini başka bir bölgeye çoğaltabilir miyim?
Evet, [bölgeye sabitlenmiş VM 'leri](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) başka bir bölgeye çoğaltabilirsiniz.

### <a name="can-i-exclude-disks"></a>Diskleri hariç tutabilir miyim?

Evet, PowerShell kullanarak diskleri koruma sırasında dışlayabilirsiniz. Daha fazla bilgi için [makaleye](azure-to-azure-exclude-disks.md) bakın

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Çoğaltılan VM 'lere yeni diskler ekleyebilir ve bu disklere yönelik çoğaltmayı etkinleştirebilir miyim?

Evet, yönetilen diskleri olan Azure VM 'Leri için bu desteklenir. Çoğaltma için etkinleştirilen bir Azure VM 'ye yeni bir disk eklediğinizde, sanal makine için çoğaltma durumu bir uyarı gösterir ve bu, VM 'deki bir veya daha fazla diskin koruma için kullanılabilir olduğunu belirten bir notdır. Eklenen diskler için çoğaltmayı etkinleştirebilirsiniz.
- Eklenen diskler için korumayı etkinleştirirseniz, uyarı ilk çoğaltmadan sonra kaybolacaktır.
- Disk için çoğaltmayı etkinleştiremeyebilir ' i seçerseniz, uyarıyı kapatmak için seçeneğini belirleyebilirsiniz.
- Bir disk eklediğiniz ve kendisi için çoğaltmayı etkinleştirdiğiniz bir VM 'nin yükünü devretmek, çoğaltma noktaları, kurtarma için kullanılabilen diskleri gösterir. Örneğin, bir VM 'nin tek bir diski varsa ve yeni bir tane eklerseniz, diski eklemeden önce oluşturulan çoğaltma noktaları, çoğaltma noktasının "1/2 diskten" oluştuğunu gösterir.

Site Recovery, çoğaltılan bir VM 'den bir diskin "Hot Remove" öğesini desteklemez. VM diskini kaldırırsanız VM için çoğaltmayı devre dışı bırakıp yeniden etkinleştirmeniz gerekir.


### <a name="how-often-can-i-replicate-to-azure"></a>Azure 'a ne sıklıkta çoğaltma yapabilirim?
Azure VM 'lerini başka bir Azure bölgesine çoğaltdığınızda çoğaltma işlemi sürekli olur. Daha fazla bilgi için bkz. [Azure 'Dan Azure 'a çoğaltma mimarisi](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Bir bölgedeki sanal makineleri çoğaltabilir miyim? VM 'Leri geçirmek için buna ihtiyacım var.
Bir bölgedeki VM 'Leri çoğaltmak için Azure 'dan Azure 'a yönelik bir DR çözümü kullanamazsınız.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>VM 'Leri herhangi bir Azure bölgesine çoğaltabilir miyim?
Site Recovery, VM 'Leri aynı coğrafi küme içindeki iki bölge arasında çoğaltabilir ve kurtarabilirsiniz. Coğrafi kümeler, veri gecikmesi ve sogemenlik göz önünde bulundurularak tanımlanmıştır. Daha fazla bilgi için Site Recovery [bölge desteği matrisine](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)bakın.

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery internet bağlantısı gerektiriyor mu?

Hayır, Site Recovery Internet bağlantısı gerektirmez. Ancak, [Bu makalede](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)belirtildiği gibi Site Recovery URL 'ler ve IP aralıkları için erişim gerektirir.

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>Uygulamayı ayrı katmanlar için ayrı kaynak grubuna sahip olacak şekilde çoğaltabilir miyim?
Evet, uygulamayı çoğaltabilir ve olağanüstü durum kurtarma yapılandırmasını ayrı kaynak grubu ile de koruyabilirsiniz.
Örneğin, her bir katman uygulaması, DB ve Web 'i ayrı kaynak grubunda içeren bir uygulamanız varsa, tüm katmanları korumak için [Çoğaltma Sihirbazı](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) ' na tıklamanız gerekir. Site Recovery, bu üç katmanı üç farklı kaynak grubunda çoğaltacaktır.

## <a name="replication-policy"></a>Çoğaltma ilkesi

### <a name="what-is-a-replication-policy"></a>Çoğaltma ilkesi nedir?
Kurtarma noktalarının bekletme geçmişine ve uygulamayla tutarlı anlık görüntülerin sıklığına ilişkin ayarları tanımlar. Varsayılan olarak, Azure Site Recovery varsayılan ayarları ile yeni bir çoğaltma ilkesi oluşturur:

* Kurtarma noktalarının bekletme geçmişi için 24 saat.
* uygulamayla tutarlı anlık görüntülerin sıklığı için 60 dakika.

[Daha fazla bilgi edinin](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Kilitlenme ile tutarlı bir kurtarma noktası nedir?
Kilitlenme ile tutarlı bir kurtarma noktası, sanal makine kilitlenmişse veya güç kablosu anlık görüntünün alındığı sırada sunucudan çekilmişse disk üzerindeki verileri temsil eder. Anlık görüntü çekilirken bellekte olan herhangi bir şeyi içermez.

Günümüzde, çoğu uygulama kilitlenme ile tutarlı anlık görüntülerden iyi bir şekilde kurtarabilir. Kilitlenme ile tutarlı bir kurtarma noktası, genellikle veritabanı olmayan işletim sistemleri ve dosya sunucuları, DHCP sunucuları ve yazdırma sunucuları gibi uygulamalar için yeterlidir.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Kilitlenme ile tutarlı kurtarma noktası oluşturma sıklığı nedir?
Site Recovery 5 dakikada bir çökme ile tutarlı bir kurtarma noktası oluşturur.

### <a name="what-is-an-application-consistent-recovery-point"></a>Uygulamayla tutarlı kurtarma noktası nedir?
Uygulamayla tutarlı kurtarma noktaları uygulamayla tutarlı anlık görüntülerden oluşturulur. Uygulamayla tutarlı kurtarma noktaları, tüm verilerin bellekte ve işlemdeki tüm işlemlerin eklenmesiyle birlikte çökme ile tutarlı anlık görüntülerle aynı verileri yakalar.
Ek içerikleri nedeniyle uygulamayla tutarlı anlık görüntüler en iyi şekilde gerçekleştirilir ve en uzun sürer. Veritabanı işletim sistemleri ve SQL Server gibi uygulamalar için uygulamayla tutarlı kurtarma noktaları öneririz.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Uygulamayla tutarlı kurtarma noktalarının uygulama performansı üzerindeki etkisi nedir?
Uygulamayla tutarlı kurtarma noktalarını, bellekteki tüm verileri yakalar ve işlem içinde Windows üzerinde VSS gibi bir çerçeve uygulamayı sessiz moda almak için gerekir. Bu, çok sık yapıldığında iş yükü zaten çok meşgulse performans etkisine sahip olabilir. Veritabanı olmayan iş yükleri için uygulamayla tutarlı kurtarma noktaları için düşük sıklık kullanılması ve hatta veritabanı iş yükü 1 saati yeterince fazla olması önerilir.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Uygulamayla tutarlı kurtarma noktası oluşturma işlemi için en düşük sıklık nedir?
Site Recovery, en az 1 saat içinde sıklığıyla uygulamayla tutarlı bir kurtarma noktası oluşturabilir.

### <a name="how-are-recovery-points-generated-and-saved"></a>Kurtarma noktaları nasıl oluşturulup kaydedilir?
Site Recovery kurtarma noktaları oluşturma hakkında bilgi edinmek için, kurtarma noktası bekletme penceresine sahip bir çoğaltma ilkesine bir örnek alalım ve 1 saatlik uygulamayla tutarlı bir sıklık anlık görüntüsü elde edelim.

Site Recovery 5 dakikada bir çökme ile tutarlı bir kurtarma noktası oluşturur. Kullanıcı bu sıklığı değiştiremez. Bu nedenle, son 1 saat için Kullanıcı 12 çökme ile tutarlı noktalara ve 1 uygulamayla tutarlı bir noktaya sahip olacaktır. Zaman ilerledikçe Site Recovery, son 1 saatin ötesinde tüm kurtarma noktalarını ayıklar ve saat başına yalnızca 1 kurtarma noktası kaydeder.

Aşağıdaki ekran görüntüsünde örnek gösterilmektedir. Ekran görüntüsünde:

1. Son 1 saatten daha az süre boyunca, 5 dakikalık bir sıklıkta kurtarma noktaları vardır.
2. Son 1 saatin ötesinde zaman Site Recovery, yalnızca 1 kurtarma noktası tutar.

   ![Oluşturulan kurtarma noktalarının listesi](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Ne kadar geri kurtarabilirim?
Kullanabileceğiniz en eski kurtarma noktası 72 saattir.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>24 saat olan bir çoğaltma ilkenizin varsa ve bir sorun Site Recovery 24 saatten uzun bir kurtarma noktası oluşturmasını engelliyorsa ne olur? Önceki kurtarma noktalarım kaybedilir mi?
Hayır, Site Recovery önceki tüm kurtarma noktalarınızı tutacaktır. Kurtarma noktaları saklama penceresine bağlı olarak, bu durumda 24 saat Site Recovery, yalnızca yeni noktaların oluşturulması durumunda en eski noktanın yerini alır. Bu durumda, bazı sorunlar nedeniyle yeni bir kurtarma noktası oluşturulmayacağından, bekletme penceresine ulaştıktan sonra tüm eski noktaları bozulmadan kalır.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>VM 'de çoğaltma etkinleştirildikten sonra, çoğaltma ilkesini nasıl değiştirebilirim?
Altyapı > **çoğaltma ilkelerine** **Site Recovery** **Site Recovery kasa** > gidin. Düzenlemek istediğiniz ilkeyi seçin ve değişiklikleri kaydedin. Tüm değişiklikler, mevcut tüm çoğaltmalar için de geçerli olacaktır.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Tüm kurtarma noktaları VM 'nin ya da bir Farklıdan oluşan tüm bir kopyası mı?
Oluşturulan ilk kurtarma noktasının tamamen kopyası vardır. Tüm ardışık kurtarma noktalarında delta değişiklikleri vardır.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Kurtarma noktalarının bekletme döneminin artırılması depolama maliyetini arttırır mi?
Evet. Bekletme süresini 24 saat ile 72 saate artırırsanız Site Recovery, kurtarma noktalarını ek 48 saat boyunca kaydeder. Eklenen süre, depolama ücretlerine tabi olacaktır. Örneğin, tek bir kurtarma noktasında Delta değişikliği 10 GB ve GB başına maliyet $0,16 ise, ek ücretler ayda $1,6 * 48 olur.

## <a name="multi-vm-consistency"></a>Çoklu VM tutarlılığı

### <a name="what-is-multi-vm-consistency"></a>Çoklu VM tutarlılığı nedir?
Bu, kurtarma noktasının tüm çoğaltılan sanal makinelerde tutarlı olmasını sağlamak anlamına gelir.
Site Recovery, bir "çoklu VM tutarlılığı" seçeneği sağlar ve bunu seçtiğinizde grubun parçası olan tüm makineleri çoğaltmak için bir çoğaltma grubu oluşturulur.
Tüm sanal makinelerin yük devrettikleri zaman, kilitlenmeyle tutarlı ve uygulamayla tutarlı kurtarma noktalarına sahip olması gerekir.
[Çoklu VM tutarlılığını etkinleştirmek](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm)için Öğreticiye gidin.

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Çoklu VM tutarlılığı çoğaltma grubu dahilinde tek bir sanal makine için yük devredebilirim miyim?
"Çoklu VM tutarlılığı" seçeneğini belirleyerek, uygulamanın bir grup içindeki tüm sanal makinelere bağımlılığı olduğunu siz belirtmektir. Bu nedenle, tek sanal makineye yük devretmeye izin verilmez.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Çoklu VM tutarlılığı çoğaltma grubunun bir parçası olarak kaç sanal makine çoğaltırım?
Bir çoğaltma grubunda, 16 sanal makineyi birlikte çoğaltabilirsiniz.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Çoklu VM tutarlılığını ne zaman etkinleştirmem gerekir?
CPU yoğun olduğundan, çoklu VM tutarlılığı etkinleştirilmesi iş yükü performansını etkileyebilir. Yalnızca makineler aynı iş yükünü çalıştırıyorsa ve birden çok makine arasında tutarlılık gerekiyorsa kullanılmalıdır. Örneğin, bir uygulamada iki SQL Server örneğe ve iki Web sunucusuna sahipseniz, yalnızca SQL Server örnekleri için çoklu VM tutarlılığı olmalıdır.


## <a name="failover"></a>Yük devretme

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Azure VM 'Leri için hedef bölgede kapasite nasıl garanti edilir?
Site Recovery ekibi, yük devretme işlemi başlatıldığında olağanüstü durum kurtarma için etkinleştirilen VM 'Lerin hedef bölgede başarılı bir şekilde dağıtılmasını sağlamaya yardımcı olmak amacıyla Azure kapasite yönetimi ekibi ile birlikte çalışır.

### <a name="is-failover-automatic"></a>Yük devretme işlemi otomatik midir?

Yük devretme işlemi otomatik değildir. Yük devretme işlemini portalda tek bir tıklama ile başlatır veya [PowerShell](azure-to-azure-powershell.md) 'i kullanarak yük devretmeyi tetikleyebilirsiniz.

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Yük devretmeden sonra genel bir IP adresi tutabilir miyim?

Üretim uygulamasının genel IP adresi, yük devretmeden sonra korunmaz.
- Yük devretme işleminin bir parçası olarak getirilen iş yükleri, hedef bölgede kullanılabilir olan bir Azure genel IP kaynağı atanmalıdır.
- Bunu el ile yapabilir veya bir kurtarma planıyla otomatikleştirebilir.
- [Yük devretmeden sonra genel IP adreslerini ayarlamayı](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)öğrenin.  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Yük devretme sırasında özel bir IP adresi tutabilir miyim?
Evet, özel bir IP adresi tutabilirsiniz. Varsayılan olarak, Azure VM 'Leri için olağanüstü durum kurtarmayı etkinleştirdiğinizde Site Recovery kaynak kaynak ayarlarına dayalı olarak hedef kaynakları oluşturur. -Statik IP adresleriyle yapılandırılmış Azure VM 'Leri için Site Recovery, kullanımda değilse hedef VM için aynı IP adresini sağlamaya çalışır.
[Yük devretme SıRASıNDA IP adreslerini tutma](site-recovery-retain-ip-azure-vm-failover.md)hakkında bilgi edinin.

### <a name="after-failover-why-is-the-server-assigned-a-new-ip-address"></a>Yük devretmeden sonra sunucu neden yeni bir IP adresi atamış?

Site Recovery, yük devretme sırasında IP adresini sağlamaya çalışır. Başka bir sanal makine bu adresi alıyorsa, Site Recovery hedef olarak bir sonraki kullanılabilir IP adresini ayarlar.
[Ağ eşlemesini ayarlama ve sanal ağlar IÇIN IP adresleme](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)hakkında daha fazla bilgi edinin.

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>**En son (en düşük RPO)** kurtarma noktası nedir?
**En son (en düşük RPO)** seçeneği ilk olarak Site Recovery hizmetine gönderilen tüm verileri işleyerek, yük devretmadan önce her VM için bir kurtarma noktası oluşturur. Yük devretmeden sonra oluşturulan VM, yük devretme tetiklendiğinde Site Recovery için çoğaltılan tüm verilere sahip olduğundan, bu seçenek en düşük kurtarma noktası hedefini (RPO) sağlar.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>**En son (en düşük RPO)** kurtarma noktalarının yük devretme RTO üzerinde bir etkisi var mı?
Evet. Site Recovery, yük devretmeye başlamadan önce tüm bekleyen verileri işler, bu nedenle bu seçeneğin diğer seçeneklerle karşılaştırıldığında daha yüksek bir kurtarma süresi hedefi (RTO) vardır.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Kurtarma noktalarında **en son işlenen** seçeneği ne anlama geliyor?
**Son işlenen** seçeneği, plandaki tüm VM 'leri, Site Recovery işlenen en son kurtarma noktasına devreder. Belirli bir sanal makine için en son kurtarma noktasını görmek üzere VM ayarlarındaki **en son kurtarma noktalarını** kontrol edin. İşlenmemiş verileri işlemek için zaman harcanmadığından, bu seçenek düşük bir RTO sağlar.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Birincil bölgem beklenmeyen bir kesinti yaşıyorsa ne olur?
Kesinti sonrasında yük devretme tetikleyebilirsiniz. Yük devretmeyi gerçekleştirmek için Site Recovery birincil bölgeden bağlantı gerekmez.

### <a name="what-is-a-rto-of-a-vm-failover-"></a>VM yük devretmesinin RTO nedir?
Site Recovery, [RTO 2 saatlık SLA 'ya](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)sahiptir. Ancak, çoğu zaman Site Recovery sanal makinelerin yükünü dakikalar içinde devreder. VM 'yi getirmek için geçen süreyi gösteren yük devretme Işlerine giderek RTO 'ı hesaplayabilirsiniz. Kurtarma planı RTO için bölümüne bakın.

## <a name="recovery-plans"></a>Kurtarma planları

### <a name="what-is-a-recovery-plan"></a>Kurtarma planı nedir?
Site Recovery bir kurtarma planı, VM 'lerin yük devretme kurtarmasını düzenler. Kurtarmaya sürekli olarak doğru, yinelenebilir ve otomatik hale getirme konusunda yardımcı olur. Kurtarma planı, kullanıcının aşağıdaki ihtiyaçlarını ele alınmaktadır:

- Birlikte yük devretme yapan bir sanal makine grubu tanımlama
- Uygulamanın doğru olması için sanal makineler arasındaki bağımlılıkları tanımlama
- Sanal makinelerin yük devretmesi dışında görevler elde etmek için el ile yapılan özel eylemlerle birlikte kurtarmayı otomatikleştirme

Kurtarma planları hakkında [daha fazla bilgi edinin](site-recovery-create-recovery-plans.md) .

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Bir kurtarma planında sıralama nasıl sağlanır?

Bir kurtarma planında sıralama elde etmek için birden çok grup oluşturabilirsiniz. Her grup, bir seferde devredildi. Aynı grubun parçası olan VM 'Ler birlikte yük devreder ve sonra başka bir grup. Bir kurtarma planı kullanarak bir uygulamanın nasıl modelleyeceğinizi öğrenmek için bkz. [kurtarma planları hakkında](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Bir kurtarma planının RTO 'ı nasıl bulabilirim?
Kurtarma planının RTO 'ı denetlemek için kurtarma planı için bir yük devretme testi yapın ve **Site Recovery işler**' e gidin.
Aşağıdaki örnekte, SAPTestRecoveryPlan adlı iş, tüm sanal makinelerin yükünü devretmek ve belirtilen eylemleri gerçekleştirmek için 8 dakika ve 59 saniye sürdü.

![Site Recovery işlerin listesi](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Kurtarma planına Otomasyon Runbook 'ları ekleyebilir miyim?
Evet, Azure Otomasyonu runbook 'larını kurtarma planınızla tümleştirebilirsiniz. [Daha fazla bilgi edinin](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Yeniden koruma ve yeniden çalışma

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Birincil bölgeden bir olağanüstü durum kurtarma bölgesine yük devretme yapıldıktan sonra, bir DR bölgesindeki VM 'Ler otomatik olarak korunur mi?
Hayır. Azure VM 'lerinden bir bölgeden diğerine [Yük devretmek](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) Için sanal makineler, korumasız BIR durumdaki Dr bölgesinde başlatılır. VM 'Leri birincil bölgeye yeniden devretmek için ikincil bölgedeki VM 'Leri [yeniden korumanız](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) gerekir.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Yeniden koruma sırasında, tüm verileri ikincil bölgeden birincil bölgeye çoğaltarak Site Recovery.
Duruma bağlıdır. Örneğin, kaynak bölgesi VM varsa, yalnızca kaynak disk ve hedef disk arasındaki değişiklikler eşitlenir. Site Recovery, diskleri karşılaştırarak farklıları hesaplar ve ardından verileri aktarır. Bu işlem genellikle birkaç saat sürer. Yeniden koruma sırasında ne olacağı hakkında daha fazla bilgi için bkz. [Azure VM 'leri yük devredilen birincil bölgeye yeniden koruma]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Yeniden başarısızlık süresi ne kadar sürer?
Yeniden koruma sonrasında, yeniden çalışma süresi genellikle birincil bölgeden ikincil bir bölgeye yük devretme için gereken zamana benzer.

## <a name="capacity"></a>Kü

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Azure VM 'Leri için hedef bölgede kapasite nasıl garanti edilir?
Site Recovery ekibi, yük devretme işlemi başlatıldığında olağanüstü durum kurtarma için etkinleştirilen VM 'Lerin hedef bölgede başarılı bir şekilde dağıtılmasını sağlamaya yardımcı olmak amacıyla Azure kapasite yönetimi ekibi ile birlikte çalışır.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery ayrılmış örneklerle birlikte çalışır mı?
Evet, olağanüstü durum kurtarma bölgesinde [ayrılmış örnekler](https://azure.microsoft.com/pricing/reserved-vm-instances/) satın alabilir ve Site Recovery yük devretme işlemleri bunları kullanır. </br> Ek yapılandırma gerekmez.


## <a name="security"></a>Güvenlik

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Çoğaltılan veriler Site Recovery hizmetine gönderilir mi?
Hayır, Site Recovery çoğaltılan verileri engellemez ve sanal makinelerinizdeki neleri çalıştırdıklarınız hakkında bilgi içermez. Yalnızca çoğaltma ve yük devretme işlemlerini düzenlemek için gereken meta veriler Site Recovery hizmetine gönderilir.  
Site Recovery ISO 27001:2013, 27018, HIPAA, DPA sertifikalı ve SOC2 ve Fedrajab değerlendirmesi sürecinde.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery çoğaltma işlemini şifreleyebilir mi?
Evet, hem çapraz geçiş hem de [Azure 'da bekleyen](https://docs.microsoft.com/azure/storage/storage-service-encryption) şifreleme destekleniyor.

## <a name="next-steps"></a>Sonraki adımlar
* Destek gereksinimlerini [gözden geçirin](azure-to-azure-support-matrix.md) .
* [Ayarla](azure-to-azure-tutorial-enable-replication.md) Azure 'dan Azure 'a çoğaltma.
- Bu makaleyi okuduktan sonra sorularınız varsa, bunları [Azure kurtarma hizmetleri forumuna](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)gönderin.
