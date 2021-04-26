---
title: Azure Site Recovery ile Azure VM olağanüstü durum kurtarma hakkında sık sorulan sorular
description: Bu makalede Azure Site Recovery kullandığınızda Azure VM olağanüstü durum kurtarma hakkında sık sorulan sorular yanıtlanmaktadır.
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: 5309fd60640c45ade42bab4c5727cf1f0a8d9d70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025484"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Sık sorulan sorular: Azure'dan Azure'a olağanüstü durum kurtarma

Bu makalede, Azure VM 'lerinin olağanüstü durum kurtarma hakkında [Azure Site Recovery](site-recovery-overview.md) hizmeti kullanılarak başka bir Azure bölgesine yönelik sık karşılaşılan sorular yanıtlanmaktadır.

## <a name="general"></a>Genel

### <a name="how-is-site-recovery-priced"></a>Site Recovery nasıl fiyatlandırılır?

Azure VM olağanüstü durum kurtarma için [maliyetler](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) hakkında bilgi edinin.

### <a name="how-does-the-free-tier-work"></a>Ücretsiz katman nasıl çalışır?

Site Recovery ile korunan her örnek, ilk 31 gün koruma için ücretsizdir. Bu dönemden sonra her bir örnek için koruma, [fiyatlandırma ayrıntılarında özetlenen hızlardır](https://azure.microsoft.com/pricing/details/site-recovery/). [Azure Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/?service=site-recovery)' nı kullanarak maliyetleri tahmin edebilirsiniz.

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>İlk 31 gün içinde diğer Azure ücretlerine neden olabilirim?

Evet. Korunan bir örneğin ilk 31 gün içinde Azure Site Recovery boş olsa da, Azure depolama, depolama işlemleri ve veri aktarımları için ücret ödemeniz istenebilir. Kurtarılan bir VM de Azure işlem ücretlerine neden olabilir. G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>Nasıl yaparım? Azure VM olağanüstü durum kurtarma ile çalışmaya başlama

1. Azure VM olağanüstü durum kurtarma mimarisini [anlayın](azure-to-azure-architecture.md) .
2. Destek gereksinimlerini [gözden geçirin](azure-to-azure-support-matrix.md) .
3. Azure VM 'Leri için olağanüstü durum kurtarmayı [ayarlayın](azure-to-azure-how-to-enable-replication.md) .
4. Bir sınama yük devretmesi ile [olağanüstü durum kurtarma detayına sahip çalıştırın](azure-to-azure-tutorial-dr-drill.md) .
5. İkincil bir Azure bölgesine [tam yük devretme çalıştırın](azure-to-azure-tutorial-failover-failback.md) .
6. İkincil bölgeden birincil bölgeye [yeniden yük devreder](azure-to-azure-tutorial-failback.md) .

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Hedef bölgede kapasiteyi nasıl güvence altına alabilirim?

Site Recovery ekibi ve Azure kapasite yönetimi ekibi, yeterli altyapı kapasitesi için plan yapın. Bir yük devretme başlattığınızda takımlar, Site Recovery tarafından korunan sanal makine örneklerinin hedef bölgeye dağıtılmasını sağlamaya de yardımcı olur.

## <a name="replication"></a>Çoğaltma

### <a name="can-i-replicate-vms-with-disk-encryption"></a>VM 'Leri disk şifrelemesiyle çoğaltabilir miyim?

Evet. Site Recovery, Azure disk şifrelemesi (ADE) etkin olan VM 'lerin olağanüstü durum kurtarmasını destekler. Çoğaltmayı etkinleştirdiğinizde, Azure tüm gerekli disk şifreleme anahtarlarını ve gizli dizileri kaynak bölgesinden hedef bölgeye, kullanıcı bağlamında kopyalar. Gerekli izinleriniz yoksa güvenlik yöneticiniz, anahtar ve gizli dizileri kopyalamak için bir komut dosyası kullanabilir.

- Site Recovery, Windows çalıştıran Azure VM 'Leri için ADE 'yi destekler.
- Site Recovery şunları destekler:
    - Azure Active Directory (Azure AD) gerektiren bir şemaya sahip olan ADE sürüm 0,1.
    - Azure AD gerektirmeyen ADE sürüm 1,1. Sürüm 1,1 için, Windows Azure VM 'lerinin yönetilen diskleri olmalıdır.
    - [Daha fazla bilgi edinin](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema). Uzantı şemaları hakkında.

Şifrelenmiş VM 'Ler için çoğaltmayı etkinleştirme hakkında [daha fazla bilgi edinin](azure-to-azure-how-to-enable-replication-ade-vms.md) .

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>Farklı bir kaynak grubundan bir Otomasyon hesabı seçebilir miyim?

Site Recovery, çoğaltılan Azure VM 'lerinde çalışan Mobility hizmeti uzantısı güncelleştirmelerini yönetmesine izin vertiğinizde, bir Azure Otomasyonu hesabı aracılığıyla küresel bir runbook (Azure hizmetleri tarafından kullanılır) dağıtır. Site Recovery oluşturduğu Otomasyon hesabını kullanabilir veya var olan bir Otomasyon hesabını kullanmayı seçebilirsiniz. 

Şu anda portalda, kasala aynı kaynak grubunda yalnızca bir Otomasyon hesabı seçebilirsiniz. PowerShell kullanarak farklı bir kaynak grubundan Otomasyon hesabı seçebilirsiniz. [Daha fazla bilgi edinin](azure-to-azure-autoupdate.md#enable-automatic-updates).

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>Kasa kaynak grubunda olmayan bir müşteri Otomasyonu hesabı kullandığımda, varsayılan runbook 'u silebilir miyim?

Evet, ihtiyacınız yoksa silebilirsiniz. 

### <a name="can-i-replicate-vms-to-another-subscription"></a>VM'leri başka bir aboneliğe çoğaltabilir miyim?

Evet, Azure VM 'Leri aynı Azure AD kiracısı içindeki herhangi bir aboneliğe çoğaltabilirsiniz. VM 'Ler için olağanüstü durum kurtarmayı etkinleştirdiğinizde, varsayılan olarak, kaynak VM 'nin gösterdiği hedef abonelik olur. Hedef aboneliği ve diğer ayarları (örneğin, kaynak grubu ve sanal ağ), seçilen abonelikten otomatik olarak doldurulur.

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>Bir kullanılabilirlik bölgesindeki VM 'Leri başka bir bölgeye çoğaltabilir miyim?

Evet, kullanılabilirlik bölgelerindeki VM 'Leri başka bir Azure bölgesine çoğaltabilirsiniz. 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>Bölge olmayan VM 'Leri aynı bölgedeki bir bölgeye çoğaltabilir miyim? 

Bu, portalda desteklenmez. Bunu yapmak için REST API/PowerShell kullanabilirsiniz.

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>Bölge sanal makinelerini aynı bölgedeki farklı bir bölgeye çoğaltabilir miyim?

Bu, için destek birkaç bölgeyle sınırlıdır. [Daha fazla bilgi edinin](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

### <a name="can-i-exclude-disks-from-replication"></a>Diskleri çoğaltmadan hariç tutabilir miyim?

Evet, çoğaltmayı ayarlarken, PowerShell kullanarak diskleri hariç bırakabilirsiniz. [Daha fazla bilgi edinin](azure-to-azure-exclude-disks.md).

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>Çoğaltılan VM 'lere eklenen yeni diskleri çoğaltabilir miyim?

Yönetilen disklere sahip çoğaltılan VM 'Ler için yeni diskler ekleyebilir ve bu disklere yönelik çoğaltmayı etkinleştirebilirsiniz. Yeni bir disk eklediğinizde, çoğaltılan VM, VM 'deki bir veya daha fazla diskin koruma için kullanılabilir olduğunu belirten bir uyarı iletisi gösterir. 

- Eklenen diskler için çoğaltmayı etkinleştirirseniz, uyarı ilk çoğaltmadan sonra kaybolur.
- Disk için çoğaltmayı etkinleştirmek istemiyorsanız, uyarıyı kapatabilirsiniz.
- Eklenen diskler içeren bir VM 'nin yükünü devretmek çoğaltma noktaları, kurtarma için kullanılabilir diskleri gösterir. Örneğin, bir VM 'ye ikinci bir disk eklerseniz, eklemeden önce oluşturulan bir çoğaltma noktası "1/2 disk" olarak görüntülenir.

Site Recovery, çoğaltılan bir sanal makineden disklerin "etkin kaldırma" olanağını desteklemez. VM diskini kaldırırsanız VM için çoğaltmayı devre dışı bırakıp yeniden etkinleştirmeniz gerekir.

### <a name="how-often-can-i-replicate-to-azure"></a>Azure 'a ne sıklıkta çoğaltma yapabilirim?

Çoğaltma, Azure VM 'lerini başka bir Azure bölgesine çoğaltarak sürekli bir şekilde yapılır. Çoğaltmanın nasıl çalıştığı hakkında [daha fazla bilgi edinin](./azure-to-azure-architecture.md#replication-process) .

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>Bir bölgedeki sanal makineleri çoğaltabilir miyim? 

Bölge içindeki diskleri çoğaltmak için Site Recovery kullanamazsınız.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Sanal makine örneklerini herhangi bir Azure bölgesine çoğaltabilir miyim?

Aynı coğrafi küme içindeki iki bölge arasında VM 'Leri çoğaltıp kurtarabilirsiniz. Coğrafi kümeler, veri gecikmesi ve sogemenlik göz önünde bulundurularak tanımlanmıştır. Bölge desteği hakkında [daha fazla bilgi edinin](./azure-to-azure-support-matrix.md#region-support) .

### <a name="does-site-recovery-need-internet-connectivity"></a>Site Recovery internet bağlantısı gerekiyor mu?

Hayır, ancak VM 'Lerin Site Recovery URL 'Ler ve IP aralıklarına erişmesi gerekir. [Daha fazla bilgi edinin](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>Kaynak grupları arasında katmanlı bir uygulamayı çoğaltabilir miyim?

Evet, uygulamayı çoğaltabilir ve olağanüstü durum kurtarma yapılandırmasını ayrı bir kaynak grubunda tutabilirsiniz.

Örneğin, uygulamalarda farklı kaynak gruplarında üç katman (uygulama/veritabanı/Web) varsa, tüm katmanları korumak için çoğaltmayı üç kez etkinleştirmeniz gerekir. Site Recovery üç katmanı üç farklı kaynak grubuna çoğaltır.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>Depolama hesaplarını kaynak grupları arasında taşıyabilir miyim?

Hayır, bu desteklenmiyor. Depolama hesaplarını yanlışlıkla farklı bir kaynak grubuna taşırsanız ve özgün kaynak grubunu silerseniz, eski kaynak grubuyla aynı ada sahip yeni bir kaynak grubu oluşturabilir ve ardından Depolama hesabını bu kaynak grubuna taşıyabilirsiniz.

## <a name="replication-policy"></a>Çoğaltma ilkesi

### <a name="what-is-a-replication-policy"></a>Çoğaltma ilkesi nedir?

Çoğaltma İlkesi, kurtarma noktalarının bekletme geçmişini ve uygulamayla tutarlı anlık görüntülerin sıklığını tanımlar.  Site Recovery aşağıdaki şekilde varsayılan bir çoğaltma ilkesi oluşturur:

- Kurtarma noktalarını 24 saat boyunca tutun.
- Her dört saatte bir uygulamayla tutarlı anlık görüntüler alın.

Çoğaltma ayarları hakkında [daha fazla bilgi edinin](azure-to-azure-how-to-enable-replication.md#customize-target-resources) .

### <a name="whats-a-crash-consistent-recovery-point"></a>Kilitlenme ile tutarlı bir kurtarma noktası nedir?

Kilitlenme ile tutarlı bir kurtarma noktası, anlık görüntü sırasında sunucudan güç kablosunu çektiği gibi disk üzerindeki verileri içerir. Anlık görüntü çekilirken bellekte olan herhangi bir şeyi içermez.

Günümüzde, çoğu uygulama kilitlenme ile tutarlı anlık görüntülerden iyi bir şekilde kurtarabilir. Veritabanı olmayan işletim sistemleri ve dosya sunucuları, DHCP sunucuları ve yazıcı sunucuları gibi uygulamalar için çökme ile tutarlı bir kurtarma noktası yeterlidir.

Site Recovery otomatik olarak her beş dakikada bir çökme ile tutarlı bir kurtarma noktası oluşturur.

### <a name="whats-an-application-consistent-recovery-point"></a>Uygulamayla tutarlı bir kurtarma noktası nedir?

Uygulamayla tutarlı kurtarma noktaları, uygulamayla tutarlı anlık görüntülerden oluşturulur. Aynı verileri çökme ile tutarlı anlık görüntülerle ve ayrıca bellekte yakalama verilerini ve işlemdeki tüm işlemleri yakalar.

Ekstra içerik nedeniyle, uygulamayla tutarlı anlık görüntüler en iyi duruma geçer ve en uzun sürer. Veritabanı işletim sistemleri ve SQL Server gibi uygulamalar için uygulamayla tutarlı kurtarma noktaları öneririz. Windows için, uygulamayla tutarlı anlık görüntüler Birim Gölge Kopyası Hizmeti (VSS) kullanır.

### <a name="do-app-consistent-recovery-points-impact-performance"></a>Uygulamayla tutarlı kurtarma noktaları performansı etkiler mi?

 Uygulamayla tutarlı kurtarma noktaları, bellek ve işlemdeki tüm verileri düzenli olarak yakalarsa, iş yükü zaten meşgul olduğunda performansı etkileyebilir. Veritabanı olmayan iş yükleri için çok sık yakalamanızı önermiyoruz. Veritabanı iş yükleri için bile bir saat yeterince olmalıdır.

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>Uygulamayla tutarlı kurtarma noktaları oluşturmanın en düşük sıklığı nedir?

Site Recovery, en az bir saatlik sıklıkta uygulamayla tutarlı kurtarma noktaları oluşturabilir.

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>Linux VM 'Ler için uygulamayla tutarlı çoğaltmayı etkinleştirebilir miyim?

Evet. Linux için Mobility Aracısı, uygulama tutarlılığı için özel betikleri destekler. Öncesi ve sonrası seçenekleri içeren özel bir betik, aracı tarafından kullanılır. [Daha fazla bilgi edinin](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>Kurtarma noktaları nasıl oluşturulup kaydedilir?

Site Recovery kurtarma noktaları oluşturmayı anlamak için bir örnek kullanalım. 

- Çoğaltma İlkesi, kurtarma noktalarını 24 saat korur ve her saat uygulamayla tutarlı bir sıklık anlık görüntüsü alır.
- Site Recovery beş dakikada bir çökme ile tutarlı bir kurtarma noktası oluşturur. Bu sıklığı değiştiremezsiniz.
- Site Recovery, kurtarma noktalarını bir saatten sonra ayıklar ve saatte bir nokta kaydederek.

Bu nedenle, son bir saat içinde, grafikte gösterildiği gibi 12 çökme ile tutarlı nokta ve uygulamayla tutarlı bir nokta arasından seçim yapabilirsiniz.

   ![Oluşturulan kurtarma noktalarının listesi](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>Ne kadar geri kurtarabilirim?

Kullanabileceğiniz en eski kurtarma noktası 72 saattir.

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>Site Recovery, 24 saatten uzun bir kurtarma noktası üretemediğinde ne olur? 

24 saatlik bir çoğaltma ilkenize sahipseniz ve Site Recovery 24 saatten uzun bir kurtarma noktası üretemiyor, eski kurtarma noktalarınız kalır. Site Recovery, yalnızca yeni noktaları oluşturursa en eski noktanın yerini alır. Yeni kurtarma noktaları olana kadar, bekletme penceresine erişduktan sonra tüm eski noktaları kalır.

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>Çoğaltma etkinleştirildikten sonra çoğaltma ilkesini değiştirebilir miyim?

Evet. Kasa > **Site Recovery altyapısı**  >  **çoğaltma ilkeleri**' nde ilkeyi seçin ve düzenleyin. Değişiklikler var olan ilkelere da uygulanır.

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>Tüm kurtarma noktaları tamamen bir VM kopyalaması mi?

Oluşturulan ilk kurtarma noktasının tamamen kopyası vardır. Ardışık kurtarma noktalarında delta değişiklikleri vardır.

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>Kurtarma noktasındaki artışlar depolama maliyetlerini artırır mi?

Evet. Örneğin, 24 saatten 72 'e kadar bekletme yaparsanız, Site Recovery kurtarma noktalarını ek 48 saat boyunca kaydeder. Eklenen süre, depolama değişikliklerini doğurur. Yalnızca bir örnek olarak, tek bir kurtarma noktası 10 GB 'lik Delta değişikliklere sahipse aylık $0,16 GB 'lik maliyetle, ek ücretler de ayda $1,60 × 48.

## <a name="multi-vm-consistency"></a>Çoklu VM tutarlılığı

### <a name="what-is-multi-vm-consistency"></a>Çoklu VM tutarlılığı nedir?

Çoklu VM tutarlılığı, kurtarma noktalarının çoğaltılan sanal makineler arasında tutarlı olmasını sağlar.

- Çoklu VM tutarlılığını etkinleştirdiğinizde Site Recovery, seçeneği etkin olan tüm makinelerin çoğaltma grubunu oluşturur. 
- Çoğaltma grubundaki makinelerin yükünü devretmek için, kilitlenmeyle tutarlı ve uygulamayla tutarlı kurtarma noktalarına sahip olurlar.

Çoklu VM tutarlılığını nasıl [etkinleştireceğinizi öğrenin](azure-to-azure-tutorial-enable-replication.md#enable-replication) .

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>Çoğaltma grubundaki tek bir VM 'nin yükünü devreder miyim?

Hayır. Çoklu VM tutarlılığını etkinleştirdiğinizde, bir uygulamanın çoğaltma grubundaki tüm sanal makinelere bağımlılığı olduğunu ve tek VM yük devretmesine izin verilmez.

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>Bir grupta birlikte kaç tane sanal makine çoğaltırım?

Bir çoğaltma grubunda 16 VM 'yi birlikte çoğaltabilirsiniz.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Çoklu VM tutarlılığını ne zaman etkinleştirmem gerekir?

Çoklu VM tutarlılığı CPU yoğun ve etkinleştirilmesi iş yükü performansını etkileyebilir. Yalnızca VM 'Ler aynı iş yükünü çalıştırıyorsa ve birden çok makine arasında tutarlılık gerekiyorsa etkinleştirin. Örneğin, bir uygulamada iki SQL Server örneği ve iki Web sunucunuz varsa, yalnızca SQL Server örnekleri için çoklu VM tutarlılığını etkinleştirin.

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>Çoğaltma grubuna çoğaltılan bir VM ekleyebilir miyim?

Sanal makine için çoğaltmayı etkinleştirdiğinizde, yeni bir çoğaltma grubuna veya var olan bir gruba ekleyebilirsiniz. Zaten bir gruba çoğaltılan bir sanal makine ekleyemezsiniz. 
 
## <a name="failover"></a>Yük devretme

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Hedef bölgede kapasiteyi nasıl güvence altına alabilirim?

Site Recovery ekibi ve Azure kapasite yönetimi ekibi, yeterli altyapı kapasitesi için plan yapın. Bir yük devretme başlattığınızda takımlar, Site Recovery tarafından korunan sanal makine örneklerinin hedef bölgeye dağıtabde sağlanmasına yardımcı olur.

### <a name="is-failover-automatic"></a>Yük devretme işlemi otomatik midir?

Yük devretme işlemi otomatik değildir. Portalda tek bir tıklama ile yük devretme başlatabilir veya yük devretmeyi tetiklemek için  [PowerShell](azure-to-azure-powershell.md) kullanabilirsiniz.

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>Yük devretmeden sonra genel bir IP adresi tutabilir miyim?

Yük devretmeden sonra bir üretim uygulaması için genel IP adresini saklayamıyorum.

Yük devretme sürecinin bir parçası olarak bir iş yükünü aldığınızda, buna bir Azure genel IP adresi kaynağı atamanız gerekir. Kaynak, hedef bölgede kullanılabilir olmalıdır. Azure genel IP adresi kaynağını el ile atayabilir veya bir kurtarma planıyla otomatikleştirebilmeniz gerekir. Yük devretmeden sonra genel IP adreslerini ayarlamayı [öğrenin](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan) .

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>Yük devretmeden sonra özel bir IP adresi tutabilir miyim?

Evet. Varsayılan olarak, Azure VM 'Leri için olağanüstü durum kurtarmayı etkinleştirdiğinizde Site Recovery kaynak kaynak ayarlarına bağlı olarak hedef kaynaklar oluşturur. Statik IP adresleriyle yapılandırılmış Azure VM 'Leri için Site Recovery, kullanımda değilse hedef VM için aynı IP adresini sağlamaya çalışır.
Yük devretmeden sonra IP adreslerini koruma [hakkında daha fazla bilgi edinin](site-recovery-retain-ip-azure-vm-failover.md) .

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>Yük devretme işleminden sonra VM neden yeni bir IP adresi atandı?

Site Recovery, yük devretme sırasında IP adresini sağlamaya çalışır. Bu adresi başka bir VM kullanıyorsa, Site Recovery hedef olarak bir sonraki kullanılabilir IP adresini ayarlar.

Sanal ağlar için ağ eşlemesi ve IP adresleme ayarlama [hakkında daha fazla bilgi edinin](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms) .

### <a name="whats-the-latest-recovery-point"></a>*En son* kurtarma noktası nedir?

*En son (en düşük RPO)* kurtarma noktası seçeneği şunları yapar:

1. İlk olarak Site Recovery gönderilen tüm verileri işler.
2. Hizmet verileri işleyerek, VM 'ye yük devrettikten önce her VM için bir kurtarma noktası oluşturur. Bu seçenek en düşük kurtarma noktası hedefini (RPO) sağlar.
3. Yük devretmeden sonra oluşturulan VM, yük devretmenin tetiklendiği zaman Site Recovery ' a çoğaltılan tüm verileri içerir.

### <a name="do-latest-recovery-points-impact-failover-rto"></a>*En son* kurtarma noktaları yük devretme RTO 'ı etkiler mi?

Evet. Site Recovery, yük devretmeye başlamadan önce tüm bekleyen verileri işler, bu nedenle bu seçeneğin diğer seçeneklere göre daha yüksek bir kurtarma süresi hedefi (RTO) vardır.

### <a name="whats-the-latest-processed-recovery-option"></a>*En son işlenen* kurtarma seçeneği nedir?

*En son işlenen* seçeneği şunları yapar:

1. Tüm VM 'lere, Site Recovery tarafından işlenen en son kurtarma noktasına devreder. İşlenmemiş verileri işlemek için zaman harcanmadığından, bu seçenek düşük bir RTO sağlar.

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>Birincil bölgede beklenmeyen bir kesinti varsa ne olacak?

Yük devretmeyi başlatabilirsiniz. Site Recovery yük devretmeyi yapmak için birincil bölgeden bağlantı gerektirmez.

### <a name="what-is-the-rto-of-a-vm-failover"></a>VM yük devretmesinin RTO nedir?

Site Recovery [iki saatlik](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)bir RTO SLA 'sı vardır. Çoğu zaman Site Recovery, VM 'Lerin üzerinde dakikalar içinde başarısız olur. RTO 'yi hesaplamak için, bir VM 'yi getirmek için geçen süreyi gösteren yük devretme işini gözden geçirin. 

## <a name="recovery-plans"></a>Kurtarma planları

### <a name="whats-a-recovery-plan"></a>Kurtarma planı nedir?

' Deki bir [kurtarma planı](site-recovery-create-recovery-plans.md) , VM 'lerin yük devretmesini ve kurtarılmasına göre Site Recovery. Kurtarmaya sürekli olarak doğru, yinelenebilir ve otomatik hale getirme konusunda yardımcı olur. Şunları yapar:

- Birlikte yük devretme yapan bir VM grubunu tanımlar
- Uygulamanın doğru olması için VM 'Ler arasındaki bağımlılıkları tanımlar.
- VM yük devretmesi dışındaki görevler için özel manuel eylemler seçeneğiyle kurtarmayı otomatikleştirir. 


### <a name="how-does-sequencing-work"></a>Sıralama nasıl çalışır?

Bir kurtarma planında sıralama için birden çok sanal makine grubu oluşturabilirsiniz. Gruplar tek seferde başarısız olur, böylece aynı grubun parçası olan VM 'Lerin birlikte yük devreder. [Daha fazla bilgi edinin](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Bir kurtarma planının RTO 'ı nasıl bulabilirim?

Kurtarma planının RTO 'ı denetlemek için kurtarma planı için yük devretme testi yapın. **Site Recovery işler**' de sınama yük devretmesi süresini denetleyin. Örnek ekran görüntüsünde, **Saptestrecoveryplan** sınama yük devretme işi 8 dakika ve 59 saniye sürdü.

![RTO için test yük devretmesi süresini gösteren işleri Listele](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>Otomasyon Runbook 'larını kurtarma planlarına ekleyebilir miyim?

Evet. [Daha fazla bilgi edinin](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Yeniden koruma ve yeniden çalışma

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>Yük devretmeden sonra, İkincil bölgedeki sanal makineler otomatik olarak korunur mu? 

Hayır. VM 'Lerin yükünü bir bölgeden diğerine devretmek için VM 'Ler, korumalı durumdaki hedef olağanüstü durum kurtarma bölgesinde başlatılır. İkincil bölgedeki VM 'leri [yeniden korumak](./azure-to-azure-how-to-reprotect.md) için çoğaltmayı birincil bölgeye yeniden etkinleştirebilirsiniz.

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>Yeniden koruduğumu ikincil bölgeden birinciye çoğaltılan tüm veriler midir? 

Bu, bağımlıdır. Kaynak bölgesi VM 'si varsa, yalnızca kaynak disk ve hedef disk arasındaki değişiklikler eşitlenir. Site Recovery diskleri farklı olacak şekilde karşılaştırır ve sonra verileri aktarır. Bu işlem genellikle birkaç saat sürer. [Daha fazla bilgi edinin](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-long-does-it-take-fail-back"></a>Ne kadar süre yeniden başarısız oluyor?

Yeniden çalışma, yeniden koruma sonrasında birincil bölgeden ikincil bir bölgeye yük devretmek için geçen süreyi de alır.

## <a name="capacity"></a><a name="capacity"></a>Kapasite

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Hedef bölgede kapasiteyi nasıl güvence altına alabilirim?

Site Recovery Team ve Azure kapasite yönetimi ekibi, yeterli altyapı kapasitesi için plan yapın. Bir yük devretme başlattığınızda takımlar, Site Recovery tarafından korunan sanal makine örneklerinin hedef bölgeye dağıtabde sağlanmasına yardımcı olur.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery ayrılmış örneklerle birlikte çalışır mı?

Evet, olağanüstü durum kurtarma bölgesinde [ayrılmış Azure VM 'leri](https://azure.microsoft.com/pricing/reserved-vm-instances/) satın alabilir ve Site Recovery yük devretme işlemleri bunları kullanır. Ek yapılandırma gerekmez.

## <a name="security"></a>Güvenlik

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Çoğaltılan veriler Site Recovery hizmetine gönderilir mi?

Hayır, Site Recovery çoğaltılan verileri engellemez ve sanal makinelerinizdeki neleri çalıştırdıklarınız hakkında bilgi içermez. Yalnızca çoğaltma ve yük devretme işlemlerini düzenlemek için gereken meta veriler Site Recovery hizmetine gönderilir.

Site Recovery ISO 27001:2013, 27018, HIPAA ve DPA sertifikalı. Hizmet SOC2 ve Fedrampa JAB değerlendirmelerinde.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery çoğaltma işlemini şifreleyebilir mi?

Evet, hem geçiş hem de [Azure 'da bekleyen şifreleme](../storage/common/storage-service-encryption.md) destekleniyor.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'Dan Azure 'a destek gereksinimlerini gözden geçirin](azure-to-azure-support-matrix.md).
- [Azure 'Dan Azure 'a çoğaltma ayarlayın](azure-to-azure-tutorial-enable-replication.md).
- Bu makaleyi okuduktan sonra sorularınız varsa, [Azure kurtarma hizmetleri Için Microsoft Q&soru sayfasında](/answers/topics/azure-site-recovery.html)bu makaleye gönderin.
