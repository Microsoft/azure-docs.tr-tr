---
title: Azure sanal makineleri için bölgeyi bölgeye olağanüstü durum kurtarmaya etkinleştirme
description: Bu makalede, Azure sanal makinelerinde bölge olağanüstü durum kurtarma için bölgenin ne zaman ve nasıl kullanılacağı açıklanır.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 786d877328b1ab3d0f03a75604b7345dba14aa9d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713417"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Kullanılabilirlik alanları arasında Azure VM olağanüstü durum kurtarmayı etkinleştirme

Bu makalede, Azure sanal makinelerini aynı Azure bölgesindeki bir kullanılabilirlik bölgesinden diğerine çoğaltma, yük devretme ve yeniden çalışma işlemlerinin nasıl yapılacağı açıklanır.

>[!NOTE]
>
>- Bölge olağanüstü durum kurtarma için olan destek şu bölgelerle sınırlıdır: Güneydoğu Asya, Japonya Doğu, Avustralya Doğu, JIO Hindistan Batı, UK Güney, Batı Avrupa, Kuzey Avrupa, Orta ABD, Doğu ABD, Doğu ABD 2 ve Batı ABD 2.  
>- Site Recovery, müşteri verileri, sistem olağanüstü durum kurtarma için bölge kullanırken dağıtıldığı bölgenin dışına taşımaz veya depolamaz. Müşteriler, bir kurtarma hizmetleri kasasını tercih ettikleri farklı bir bölgeden seçebilirler. Kurtarma Hizmetleri Kasası meta veriler içeriyor ancak gerçek müşteri verisi yok.

Site Recovery hizmeti, planlı ve plansız kesintiler sırasında iş uygulamalarınızı çalışır durumda tutarak iş sürekliliği ve olağanüstü durum kurtarma stratejinize katkıda bulunur. Bölgesel kesintiler varsa uygulamalarınızın çalışır durumda tutulması için önerilen olağanüstü durum kurtarma seçeneğidir.

Kullanılabilirlik Alanları, Azure bölgesi içinde fiziksel olarak benzersiz konumlardır. Her bölgede bir veya daha fazla veri merkezi bulunur. 

VM 'Leri farklı bir bölgedeki bir kullanılabilirlik bölgesine taşımak istiyorsanız, [Bu makaleyi gözden geçirin](../resource-mover/move-region-availability-zone.md).

## <a name="using-availability-zones-for-disaster-recovery"></a>Olağanüstü durum kurtarma için Kullanılabilirlik Alanları kullanma 

Genellikle, Kullanılabilirlik Alanları yüksek kullanılabilirliğe sahip bir yapılandırmada VM dağıtmak için kullanılır. Doğal olağanüstü durum durumunda olağanüstü durum kurtarma çözümü olarak kullanılmak üzere birbirlerine çok yakın olabilirler.

Ancak bazı senaryolarda Kullanılabilirlik Alanları olağanüstü durum kurtarma için yararlanılabilir olabilir:

- Şirket içinde uygulamaları barındırırken bir metro olağanüstü durum kurtarma stratejisi olan birçok müşteri bazen uygulamaları Azure 'a geçirdikten sonra bu stratejiyi taklit etmek için de görünür. Bu müşteriler, büyük ölçekli bir fiziksel olağanüstü durum oluşması durumunda Metro olağanüstü durum kurtarma stratejisinin çalışmamasının yanı sıra bu riski kabul edebilir. Bu tür müşteriler için bölge olağanüstü durum kurtarma seçeneği bir olağanüstü durum kurtarma seçeneği olarak kullanılabilir.

- Diğer birçok müşteri, karmaşık ağ altyapısına sahiptir ve ilişkili maliyet ve karmaşıklık nedeniyle ikincil bir bölgede yeniden oluşturmak istemiyor. Bölgeden bölgeye olağanüstü durum kurtarma, Kullanılabilirlik Alanları yapılandırmanın çok daha basit hale getirilmesi sırasında gereksiz ağ kavramlarından yararlandığından karmaşıklığı azaltır. Bu tür müşteriler basitlik ve olağanüstü durum kurtarma için Kullanılabilirlik Alanları da kullanabilir.

- Aynı yasal vergi dairesi içinde eşleştirilmiş bir bölgesi olmayan bazı bölgelerde (örneğin, Güneydoğu Asya), bölge olağanüstü durum kurtarma çözümü, uygulamalarınız ve verileriniz ulusal sınırlar arasında hareket etmediğinden yasal uyumluluk sağlanmasına yardımcı olduğundan olağanüstü durum kurtarma çözümü olarak görev yapabilir. 

- Bölgeden bölgeye olağanüstü durum kurtarma, Azure ile Azure olağanüstü durum kurtarma ile karşılaştırıldığında verilerin daha kısa uzaklıklarla çoğaltılmasını sağlar ve bu nedenle, daha düşük gecikme süresi ve sonuç olarak daha düşük RPO görebilirsiniz.

Bunlar güçlü avantajlar olmakla kalmaz, bölgenin olağanüstü durum kurtarma için bölge genelinde bir doğal olağanüstü durum durumunda esnekliği gereksinimlerinin kısa bir süre içinde kalabilmesini sağlayan bir olasılık vardır.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Bölgeden bölge olağanüstü durum kurtarma için ağ oluşturma

Yukarıda belirtildiği gibi, bölgeye karşı bölge olağanüstü durum kurtarma, Kullanılabilirlik Alanları yapılandırmanın çok daha basit hale getirilmesi sırasında gereksiz ağ kavramlarından yararlandığından karmaşıklığı azaltır. Bölgeden bölge olağanüstü durum kurtarma senaryosunda ağ bileşenlerinin davranışı aşağıda özetlenmiştir: 

- Sanal ağ: gerçek yük devretme işlemleri için kaynak ağ ile aynı sanal ağı kullanabilirsiniz. Yük devretme testi için kaynak sanal ağa farklı bir sanal ağ kullanın.

- Alt ağ: aynı alt ağa yük devretme desteklenir.

- Özel IP adresi: statik IP adresleri kullanıyorsanız, bunları böyle bir şekilde yapılandırmayı tercih ediyorsanız, hedef bölgede aynı IP 'Leri kullanabilirsiniz.

- Hızlandırılmış ağ: Azure ile Azure olağanüstü durum kurtarmaya benzer şekilde, VM SKU 'SU destekliyorsa hızlandırılmış ağı etkinleştirebilirsiniz.

- Genel IP adresi: daha önce oluşturulmuş bir standart genel IP adresini hedef VM 'ye aynı bölgede ekleyebilirsiniz. Temel genel IP adresleri, kullanılabilirlik bölgesiyle ilgili senaryoları desteklemez.

- Yük dengeleyici: Standart yük dengeleyici bir bölgesel kaynaktır ve bu nedenle hedef VM aynı yük dengeleyicinin arka uç havuzuna iliştirilebilir. Yeni bir yük dengeleyici gerekli değildir.

- Ağ güvenlik grubu: kaynak VM 'ye uygulanan aynı ağ güvenlik gruplarını kullanabilirsiniz.

## <a name="pre-requisites"></a>Ön koşullar

Sanal makinelerinize bölge olağanüstü durum kurtarmaya dağıtım yapmadan önce, sanal makinede etkinleştirilen diğer özelliklerin bölge olağanüstü durum kurtarma ile birlikte kullanılabildiğinden emin olmak önemlidir.

|Özellik  | Support bildirisi  |
|---------|---------|
|Klasik VM'ler   |     Desteklenmez    |
|ARM VM 'Leri    |    Desteklenir    |
|Azure disk şifrelemesi v1 (Azure Active Directory (Azure AD) ile ikili geçiş)     |     Desteklenir   |
|Azure disk şifrelemesi v2 (Azure AD olmadan tek pass)    |    Desteklenir    |
|Yönetilmeyen diskler    |    Desteklenmez    |
|Yönetilen diskler    |    Desteklenir    |
|Müşteri tarafından yönetilen anahtarlar    |    Desteklenir    |
|Yakınlık yerleştirme grupları    |    Desteklenir    |
|Yedeklemeye birlikte çalışabilirlik    |    Dosya düzeyinde yedekleme ve geri yükleme desteklenir. Disk ve VM düzeyinde yedekleme ve geri yükleme desteklenmez.    |
|Sık erişimli Ekle/Kaldır    |    Bölgeler bölge çoğaltmasına etkinleştirildikten sonra, diskler eklenebilir. Bölgeyi bölge çoğaltmaya etkinleştirdikten sonra disklerin kaldırılması desteklenmez.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Bölge olağanüstü durum kurtarma için Site Recovery bölgesi ayarlama

### <a name="log-in"></a>Oturum açma

Azure portalında oturum açın.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Azure sanal makinesi için çoğaltmayı etkinleştirme

1. Azure portal menüsünde, sanal makineler ' i seçin veya herhangi bir sayfada sanal makineler arayın ve seçin. Çoğaltmak istediğiniz VM 'yi seçin. Bölge için olağanüstü durum kurtarma için bu VM zaten bir kullanılabilirlik bölgesinde olmalıdır.

2. İşlemler menüsünden Olağanüstü durum kurtarma seçeneğini belirleyin.

3. Aşağıda gösterildiği gibi, temel bilgiler sekmesinde, ' Kullanılabilirlik Alanları arasında olağanüstü durum kurtarma için ' Evet ' seçeneğini belirleyin.

    ![Temel ayarlar sayfası](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Tüm Varsayılanları kabul ediyorsanız, ' gözden geçir + çoğaltmayı Başlat ' ' a ve ardından ' çoğaltmayı Başlat ' ' a tıklayın.

5. Çoğaltma ayarlarında değişiklik yapmak istiyorsanız ' Ileri: Gelişmiş ayarlar ' seçeneğine tıklayın.

6. Ayarları uygun olan yerlerde varsayılan olarak değiştirin. Azure 'dan Azure 'a olağanüstü durum kurtarma ile ilgili Bu sayfa tanıdık görünebilir. Bu dikey pencerede sunulan seçeneklerle ilgili daha fazla ayrıntı [burada](./azure-to-azure-tutorial-enable-replication.md) bulunabilir

    ![Gelişmiş ayarlar sayfası](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. ' Ileri: gözden geçir + çoğaltmayı Başlat ' ve ardından ' çoğaltmayı Başlat ' seçeneğine tıklayın.

## <a name="faqs"></a>SSS

**1. fiyatlandırma, bölge olağanüstü durum kurtarma için nasıl çalışır?**
Bölge için olağanüstü durum kurtarma için fiyatlandırma, Azure 'dan Azure 'a olağanüstü durum kurtarma ile aynıdır. [Burada](https://azure.microsoft.com/pricing/details/site-recovery/) fiyatlandırma sayfasında daha fazla ayrıntı [bulabilirsiniz.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) Bölge olağanüstü durum kurtarma bölümünde göreceğiniz çıkış ücretleri bölge olağanüstü durum kurtarma bölgesinden daha düşük olacağını unutmayın.

**2. RTO ve RPO SLA 'Sı nedir?**
RTO SLA, Site Recovery genel olarak aynıdır. 2 saate kadar taahhüdünü taahhüt ediyoruz. RPO için tanımlı SLA yok.

**3. kapasite, ikincil bölgede garanti edilir mi?**
Site Recovery Team ve Azure kapasite yönetimi ekibi, yeterli altyapı kapasitesi için plan yapın. Yük devretme başlattığınızda takımlar, Site Recovery tarafından korunan sanal makine örneklerinin hedef bölgeye dağıtılmasını sağlamaya de yardımcı olur.

**4. hangi işletim sistemleri destekleniyor?**
Bölgeden bölgeye olağanüstü durum kurtarma, Azure ile Azure olağanüstü durum kurtarma ile aynı işletim sistemlerini destekler. [Buradaki](./azure-to-azure-support-matrix.md)destek matrisine bakın.

**5. kaynak ve hedef kaynak grupları aynı olabilir mi?**
Hayır, farklı bir kaynak grubuna yük devretme yapmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Olağanüstü durum kurtarma detaya gitme, yük devretme, yeniden koruma ve yeniden çalışma işlemlerini çalıştırmak için izlenmesi gereken adımlar, Azure 'daki Azure 'da olağanüstü durum kurtarma senaryosunda bulunan adımlarla aynıdır.

Olağanüstü durum kurtarma detayına ulaşmak için lütfen [burada](./azure-to-azure-tutorial-dr-drill.md)özetlenen adımları izleyin.

İkincil bölgedeki VM 'Leri yük devretme ve yeniden koruma işlemleri gerçekleştirmek için [burada](./azure-to-azure-tutorial-failover-failback.md)özetlenen adımları izleyin.

Birincil bölgeye yeniden çalışma için [burada](./azure-to-azure-tutorial-failback.md)özetlenen adımları izleyin.
