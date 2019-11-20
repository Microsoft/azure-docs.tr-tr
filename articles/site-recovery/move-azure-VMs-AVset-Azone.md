---
title: Azure Site Recovery kullanarak VM 'Leri kullanılabilirlik alanları içeren bir Azure bölgesine taşıyın
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: c9513ca38c665a53bc14f284e87a1cc01035d135
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693192"
---
# <a name="move-azure-vms-into-availability-zones"></a>Azure VM 'lerini Kullanılabilirlik Alanları içine taşıyın
Azure 'daki Kullanılabilirlik Alanları, veri merkezi hatalarından uygulamalarınızın ve verilerinizin korunmasına yardımcı olur. Her Kullanılabilirlik Alanı, bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Dayanıklılık sağlamak için, tüm etkin bölgelerde en az üç ayrı bölge vardır. Bir bölgedeki Kullanılabilirlik Alanları fiziksel ayrımı, uygulamaların ve verilerin veri merkezi hatalarından korunmasını sağlar. Azure, Kullanılabilirlik Alanları ile sanal makinelerin (VM) çalışma süresi için% 99,99 ' lik bir hizmet düzeyi sözleşmesi (SLA) sunmaktadır. Kullanılabilirlik Alanları, [Azure 'da kullanılabilirlik alanları](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region)olarak bahsedildiği gibi, seçme bölgelerinde desteklenir.

Sanal makinelerinizin belirli bir bölgeye *tek bir örnek* olarak dağıtıldığı ve bu VM 'Leri bir kullanılabilirlik bölgesine taşıyarak kullanılabilirliği artırmak istediğiniz bir senaryoda, Azure Site Recovery kullanarak bunu yapabilirsiniz. Bu eylem, daha sonra kategorilere ayrılabilir:

- Tek örnekli VM 'Leri hedef bölgede Kullanılabilirlik Alanları taşıyın
- Bir kullanılabilirlik kümesindeki VM 'Leri hedef bölgedeki Kullanılabilirlik Alanları taşıyın

> [!IMPORTANT]
> Şu anda Azure Site Recovery, VM 'Lerin bir bölgeden diğerine taşınmasını destekler ancak bölge içinde taşımayı desteklemez.

## <a name="check-prerequisites"></a>Önkoşulları denetleme

- Hedef bölgenin [kullanılabilirlik alanları için desteğe](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region)sahip olup olmadığını denetleyin. [Kaynak bölgesi/hedef bölge bileşiminin desteklenip desteklenmediğini](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)denetleyin. Hedef bölgede bilinçli bir karar alın.
- [Senaryo mimarisini ve bileşenlerini ](azure-to-azure-architecture.md) anladığınızdan emin olun.
- [Destek sınırlamaları ve gereksinimleri](azure-to-azure-support-matrix.md) konusunu inceleyin.
- Hesap izinlerini denetleyin. Ücretsiz Azure hesabınızı oluşturduysanız aboneliğinizin yöneticisi olursunuz. Abonelik yöneticisi değilseniz, ihtiyaç duyduğunuz izinleri atamak için yönetici ile çalışın. Bir VM için çoğaltmayı etkinleştirmek ve sonunda Azure Site Recovery kullanarak verileri hedefe kopyalamak için, şunları yapmanız gerekir:

    1. Azure kaynaklarında VM oluşturma izni. *Sanal makine katılımcısı* yerleşik rolü bu izinlere sahiptir ve şunları içerir:
        - Seçilen kaynak grubunda sanal makine oluşturma izni
        - Seçilen sanal ağda sanal makine oluşturma izni
        - Seçilen depolama hesabına yazma izni

    2. Azure Site Recovery görevlerini yönetme izni. *Site Recovery katkıda* bulunan rolü, bir kurtarma hizmetleri kasasındaki Site Recovery eylemleri yönetmek için gereken tüm izinlere sahiptir.

## <a name="prepare-the-source-vms"></a>Kaynak VM 'Leri hazırlama

1. Site Recovery kullanarak bunları bir kullanılabilirlik bölgesine taşımak istiyorsanız sanal makinelerinizin yönetilen diskleri kullanması gerekir. Yönetilmeyen diskleri kullanan mevcut Windows VM 'lerini yönetilen diskleri kullanmak üzere dönüştürebilirsiniz. [Windows sanal makinesini yönetilmeyen disklerden yönetilen disklere dönüştürme](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)bölümündeki adımları izleyin. Kullanılabilirlik kümesinin *yönetilen*olarak yapılandırıldığından emin olun.
2. Taşımak istediğiniz Azure VM 'lerinde en son kök sertifikaların mevcut olup olmadığını denetleyin. En son kök sertifikalar mevcut değilse, hedef bölgeye veri kopyalama güvenlik kısıtlamaları nedeniyle etkinleştirilemez.

3. Windows VM'lerde, güvenilen kök sertifikaların tamamı makinede mevcut olacak şekilde, VM’ye en son Windows güncelleştirmelerinin tümünü yükleyin. Bağlantısı kesilmiş bir ortamda, kuruluşunuz için standart Windows Update ve sertifika güncelleştirme süreçlerini izleyin.

4. Linux sanal makineleri için, VM 'deki en son güvenilen kök sertifikaları ve sertifika iptal listesini almak için Linux dağıtıcısının sunduğu yönergeleri izleyin.
5. Taşımak istediğiniz VM 'Ler için ağ bağlantısını denetlemek üzere bir kimlik doğrulama proxy 'si kullandığınızdan emin olun.

6. Taşımaya çalıştığınız sanal makinenin internet erişimi yoksa ve giden erişimi denetlemek için bir güvenlik duvarı ara sunucusu kullanıyorsa, [giden ağ bağlantısını yapılandırma](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms)konumundaki gereksinimleri kontrol edin.

7. Yük dengeleyiciler, NSG 'ler ve genel IP dahil olmak üzere kaynak ağ düzeni ve şu anda doğrulama için kullandığınız kaynakları belirler.

## <a name="prepare-the-target-region"></a>Hedef bölgeyi hazırlama

1. Azure aboneliğinizin olağanüstü durum kurtarma için kullanılan hedef bölgede VM 'Ler oluşturmanıza izin olup olmadığını denetleyin. Gerekirse, gerekli kotayı etkinleştirmek için desteğe başvurun.

2. Aboneliğinizin, kaynak VM’lerinize uygun boyutlardaki VM’leri desteklemek için yeterli kaynakları içerdiğinden emin olun. Verileri hedefe kopyalamak için Site Recovery kullanırsanız, hedef VM için aynı boyutu veya mümkün olan en yakın boyutu seçer.

3. Kaynak ağ düzeninde tanımlanan her bileşen için bir hedef kaynak oluşturun. Bu eylem, hedef bölgeye kestikten sonra sanal makinelerinizin kaynakta bulunan tüm işlevsellik ve özelliklere sahip olmasını sağlar.

    > [!NOTE]
    > Azure Site Recovery, kaynak VM için çoğaltmayı etkinleştirdiğinizde otomatik olarak bir sanal ağ ve depolama hesabı bulur ve oluşturur. Ayrıca, bu kaynakları önceden oluşturup, çoğaltmayı etkinleştirme adımının bir parçası olarak VM 'ye atayabilirsiniz. Ancak, daha sonra bahsedilen diğer kaynaklar için, bunları hedef bölgede el ile oluşturmanız gerekir.

     Aşağıdaki belgeler, kaynak VM yapılandırmasına bağlı olarak, sizin için uygun olan en yaygın kullanılan ağ kaynaklarını oluşturmayı söyler.

    - [Ağ güvenlik grupları](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Yük dengeleyiciler](https://docs.microsoft.com/azure/load-balancer)
    - [Genel IP](../virtual-network/virtual-network-public-ip-address.md)
    
   Diğer ağ bileşenleri için ağ [belgelerine](https://docs.microsoft.com/azure/#pivot=products&panel=network)bakın.

    > [!IMPORTANT]
    > Hedefte bölge yedekli yük dengeleyici kullandığınızdan emin olun. [Standart Load Balancer ve kullanılabilirlik alanları](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)daha fazla bilgi edinebilirsiniz.

4. Hedef bölgeye geçmeden önce yapılandırmayı test etmek istiyorsanız hedef bölgede el ile [bir üretim dışı ağ oluşturun](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) . Üretim ortamında en az girişim oluşmasına neden olduğundan bu yaklaşım önerilir.

## <a name="enable-replication"></a>Çoğaltmayı etkinleştirme
Aşağıdaki adımlar, verilerin hedef bölgeye çoğaltılmasını sağlamak için Azure Site Recovery kullandığınızda size rehberlik eder. Bu işlem, sonunda Kullanılabilirlik Alanları.

> [!NOTE]
> Bu adımlar tek bir sanal makine içindir. Aynı şekilde birden çok VM 'ye genişletebilirsiniz. Kurtarma Hizmetleri kasasına gidin, **+ Çoğalt**' ı seçin ve Ilgili VM 'leri birlikte seçin.

1. Azure portal **sanal makineler**' i seçin ve kullanılabilirlik alanları taşımak istediğiniz VM 'yi seçin.
2. **İşlemler** menüsünden **Olağanüstü durum kurtarma** seçeneğini belirleyin.
3. **Olağanüstü durum kurtarma** > **Hedef bölgesini**Yapılandır bölümünde, çoğaltılacak hedef bölgeyi seçin. Bu bölgenin Kullanılabilirlik Alanları [desteklediğinden](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region) emin olun.

    ![Hedef bölgenin seçimi](media/azure-vms-to-zones/enable-rep-1.PNG)

4. **İleri ' yi seçin: Gelişmiş ayarlar**.
5. Hedef abonelik, hedef VM kaynak grubu ve sanal ağ için uygun değerleri seçin.
6. **Kullanılabilirlik** bölümünde, sanal makineyi taşımak Istediğiniz kullanılabilirlik bölgesini seçin. 
   > [!NOTE]
   > Kullanılabilirlik kümesi veya kullanılabilirlik bölgesi seçeneğini görmüyorsanız [önkoşulların](#prepare-the-source-vms) karşılandığından ve kaynak VM 'lerin [hazırlanması](#prepare-the-source-vms) tamamlandığından emin olun.
  
    ![Kullanılabilirlik bölgesi seçme seçimleri](media/azure-vms-to-zones/enable-rep-2.PNG)

7. **Çoğaltmayı Etkinleştir** seçeneğini belirleyin. Bu eylem VM için çoğaltmayı etkinleştirmek üzere bir iş başlatır.

## <a name="check-settings"></a>Ayarları denetle

Çoğaltma işlemi bittikten sonra, çoğaltma durumunu denetleyebilir, çoğaltma ayarlarını değiştirebilir ve dağıtımı test edebilirsiniz.

1. VM menüsünden **Olağanüstü durum kurtarma** seçeneğini belirleyin.
2. Çoğaltma durumunu, oluşturulan kurtarma noktalarını ve kaynak ve haritadaki hedef bölgeleri kontrol edebilirsiniz.

   ![Çoğaltma durumu](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Yapılandırmayı test etme

1. Sanal makine menüsünde **olağanüstü durum kurtarma**' yı seçin.
2. **Yük devretme testi** simgesini seçin.
3. Yük **devretme testi**bölümünde, yük devretme için kullanılacak bir kurtarma noktası seçin:

   - **En son işlenen**: VM’nin yükünü, Site Recovery hizmeti tarafından işlenen en son kurtarma noktasına devreder. Zaman damgası gösterilir. Bu seçenekle veri işlemeye zaman harcanmadığından düşük kurtarma süresi hedefi (RTO) elde edilir.
   - **En son uygulamayla tutarlı**: Bu seçenekte tüm VM yükü, uygulamayla tutarlı en son kurtarma noktasına devredilir. Zaman damgası gösterilir.
   - **Özel**: Herhangi bir kurtarma noktası seçin.

3. Yapılandırmayı sınamak için Azure VM 'lerini taşımak istediğiniz test hedefi Azure sanal ağını seçin. 

    > [!IMPORTANT]
    > VM 'lerinizi taşımak istediğiniz hedef bölgede üretim ağı değil, test hatası için ayrı bir Azure VM ağı kullanmanızı öneririz.

4. Taşımayı test etmeye başlamak için **Tamam**' ı seçin. İlerlemeyi izlemek için VM 'yi seçerek özelliklerini açın. Ya da kasa adı ' nda **Yük devretme testi** işini seçebilirsiniz > **ayarlar** > **işler** > **Site Recovery işleri**.
5. Yük devretme işlemi tamamlandıktan sonra çoğaltma Azure sanal makinesi, Azure portalı > **Sanal Makineler** bölümünde görüntülenir. VM’nin çalıştığından, uygun şekilde boyutlandırıldığından ve uygun ağa bağlı olduğundan emin olun.
6. Taşımayı test etme işleminin bir parçası olarak oluşturulan VM 'yi silmek istiyorsanız, çoğaltılan öğedeki **Yük devretme testini temizle** ' yi seçin. **Notlar**' da, testle ilişkili tüm gözlemlerinizi kaydedin ve kaydedin.

## <a name="move-to-the-target-region-and-confirm"></a>Hedef bölgeye gidin ve onaylayın

1.  Sanal makine menüsünde **olağanüstü durum kurtarma**' yı seçin.
2. **Yük devretme** simgesini seçin.
3. **Yük devretme** bölümünde **En geç** seçeneğini belirleyin. 
4. **Yük devretmeyi başlatmadan önce makineyi kapatın** seçeneğini belirleyin. Site Recovery, yük devretmeyi tetiklemeden önce kaynak sanal makineyi kapatmaya çalışır. Kapatma işlemi başarısız olsa bile yük devretme devam eder. Yük devretme işleminin ilerleme durumunu **İşler** sayfasında takip edebilirsiniz. 
5. İş bittikten sonra, sanal makinenin hedef Azure bölgesinde beklenen şekilde göründüğünden emin olun.
6. **Çoğaltılan öğeler** bölümünde VM’ye sağ tıklayıp **Yürüt**’e tıklayın. Bu, hedef bölgeye taşıma işlemini tamamlar. Tamamlama işi bitene kadar bekleyin.

## <a name="discard-the-resource-in-the-source-region"></a>Kaynak bölgedeki kaynağı at

VM 'ye gidin. **Çoğaltmayı devre dışı bırak**seçeneğini belirleyin. Bu eylem, VM için verileri kopyalama işlemini sonlandırır.  

> [!IMPORTANT]
> Taşıma işleminden sonra Site Recovery çoğaltma için ücretlendirmemek için önceki adımı yapın. Kaynak çoğaltma ayarları otomatik olarak temizlenir. Çoğaltmanın bir parçası olarak yüklenen Site Recovery uzantısının kaldırılmadığını ve el ile kaldırılması gerektiğini unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide bir Azure VM 'nin kullanılabilirliğini, bir kullanılabilirlik kümesi veya kullanılabilirlik bölgesine taşıyarak artırmış olursunuz. Artık taşınan VM için olağanüstü durum kurtarma ayarlayabilirsiniz.

> [!div class="nextstepaction"]
> [Geçişten sonra olağanüstü durum kurtarmayı ayarlama](azure-to-azure-quickstart.md)


