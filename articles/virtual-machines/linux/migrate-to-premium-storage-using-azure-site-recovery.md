---
title: Linux VM 'lerinizi Azure Site Recovery Azure Premium depolamaya geçirin
description: Site Recovery kullanarak mevcut sanal makinelerinizi Azure Premium depolamaya geçirin. Premium Depolama, Azure sanal makinelerinde çalışan g/ç yoğunluklu iş yükleri için yüksek performanslı ve düşük gecikmeli disk desteği sunar.
services: virtual-machines-linux,storage
cloud: Azure
author: luywang
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: b8c299869da003d8dc4e9ac111ba8c5e8bb27345
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035823"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Azure Site Recovery kullanarak Premium depolamaya geçiş

[Azure Premium SSD](disks-types.md) 'ler, g/ç yoğunluklu iş yüklerini çalıştıran sanal makineler (VM) için yüksek performanslı ve düşük gecikmeli disk desteği sunar. Bu kılavuz, [Azure Site Recovery](../../site-recovery/site-recovery-overview.md)kullanarak, sanal makine disklerinizi standart bir depolama hesabından Premium depolama hesabına geçirmenize yardımcı olur.

Site Recovery, şirket içi fiziksel sunucuların ve sanal makinelerin buluta (Azure) veya ikincil bir veri merkezine çoğaltılmasını düzenleyerek iş sürekliliği ve olağanüstü durum kurtarma stratejinize katkıda bulunan bir Azure hizmetidir. Birincil konumunuzda kesintiler meydana geldiğinde, uygulamaları ve iş yüklerini kullanılabilir durumda tutmak için ikincil konuma yük devreder. Normal bir işleme döndüğünde birincil konumunuza geri dönebilirsiniz. 

Site Recovery, üretim ortamlarını etkilemeden olağanüstü durum kurtarma detaylarını desteklemek için test yük devretmeleri sağlar. Yük devretmeleri, beklenmeyen olağanüstü durumlar için minimum veri kaybından (çoğaltma sıklığına bağlı olarak) çalıştırabilirsiniz. Premium Depolama 'ya geçiş senaryosunda, hedef diskleri bir Premium depolama hesabına geçirmek için [Site Recovery 'de yük devretmeyi](../../site-recovery/site-recovery-failover.md) kullanabilirsiniz.

Bu seçenek en az kapalı kalma süresi sağladığından Site Recovery kullanarak Premium depolamaya geçiş yapmanızı öneririz. Bu seçenek ayrıca, disklerin kopyalanmasını ve yeni VM 'Ler oluşturmayı el ile yürütmeyi önler. Site Recovery, yük devretme sırasında disklerinizi sistematik olarak kopyalayacak ve yeni VM 'Ler oluşturacak. 

Site Recovery, en az bir kesinti süresi olan veya olmayan yük devretme türlerini destekler. Kapalı kalma süresini planlamak ve veri kaybını tahmin etmek için [Site Recovery 'de yük devretme türlerine](../../site-recovery/site-recovery-failover.md)bakın. [Yük devretmeden sonra Azure VM 'lerine bağlanmaya hazırlandıysanız](../../site-recovery/vmware-walkthrough-overview.md), yük DEVRETMEDEN sonra RDP kullanarak Azure VM 'ye bağlanabilirsiniz.

![Olağanüstü durum kurtarma diyagramı][1]

## <a name="azure-site-recovery-components"></a>Azure Site Recovery bileşenleri

Bu Site Recovery bileşenleri bu geçiş senaryosu ile ilgilidir:

* **Yapılandırma sunucusu** , iletişimi koordine eden ve veri çoğaltma ile kurtarma işlemlerini yöneten BIR Azure VM 'dir. Bu VM 'de, yapılandırma sunucusunu ve bir işlem sunucusu olarak adlandırılan ek bir bileşeni çoğaltma ağ geçidi olarak yüklemek için tek bir kurulum dosyası çalıştırırsınız. [Yapılandırma sunucusu önkoşulları](../../site-recovery/vmware-walkthrough-overview.md)hakkında bilgi edinin. Yapılandırma sunucusunu yalnızca bir kez ayarlarsınız ve aynı bölgedeki tüm geçişler için kullanabilirsiniz.

* **İşlem sunucusu** şu şekilde bir çoğaltma ağ geçididir: 

  1. Kaynak VM 'lerden çoğaltma verileri alır.
  2. Önbelleğe alma, sıkıştırma ve şifreleme ile verileri iyileştirir.
  3. Verileri bir depolama hesabına gönderir. 

  Ayrıca, Mobility hizmetinin kaynak VM 'lere göndererek yüklenmesini işler ve kaynak VM 'lerin otomatik olarak bulunmasını gerçekleştirir. Varsayılan işlem sunucusu yapılandırma sunucusuna yüklenir. Dağıtımınızı ölçeklendirmek için, ek tek başına işlem sunucuları dağıtabilirsiniz. İşlem sunucusu dağıtımı ve [ek işlem sunucularının dağıtımı](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers) [için en iyi yöntemler](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) hakkında bilgi edinin. İşlem sunucusunu yalnızca bir kez ayarlarsınız ve aynı bölgedeki tüm geçişler için kullanabilirsiniz.

* **Mobility hizmeti** , çoğaltmak istediğiniz her standart sanal makineye dağıtılan bir bileşendir. Standart sanal makinede veri yazmaları yakalar ve bunları işlem sunucusuna iletir. [Çoğaltılan makine önkoşulları](../../site-recovery/vmware-walkthrough-overview.md)hakkında bilgi edinin.

Bu grafik, bu bileşenlerin nasıl etkileşime gireceğini gösterir:

![Site Recovery bileşenlerinin etkileşimi][15]

> [!NOTE]
> Site Recovery, depolama alanları disklerinin geçirilmesini desteklemez.

Diğer senaryolar için ek bileşenler için bkz. [senaryo mimarisi](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Azure Essentials

Bunlar, bu geçiş senaryosuna yönelik Azure gereksinimleridir:

* Azure aboneliği.
* Çoğaltılan verileri depolamak için bir Azure Premium depolama hesabı.
* Yük devretmede oluşturulduklarında VM 'Lerin bağlanacağı bir Azure sanal ağı. Azure sanal ağı, Site Recovery çalıştığı ile aynı bölgede olmalıdır.
* Çoğaltma günlüklerini depolamak için bir Azure Standart depolama hesabı. Bu, geçirilmekte olan VM diskleri için aynı depolama hesabı olabilir.

## <a name="prerequisites"></a>Önkoşullar

* Yukarıdaki bölümde ilgili geçiş senaryosu bileşenlerini anlayın.
* [Site Recovery 'de yük devretme](../../site-recovery/site-recovery-failover.md)hakkında bilgi edinmek için kapalı kalma süresini planlayın.

## <a name="setup-and-migration-steps"></a>Kurulum ve geçiş adımları

Site Recovery, Azure IaaS VM 'lerini bölgeler arasında veya aynı bölge içinde geçirmek için kullanabilirsiniz. Aşağıdaki yönergeler, [VMware VM 'lerini veya fiziksel sunucuları Azure 'A çoğaltma](../../site-recovery/vmware-walkthrough-overview.md)makalesindeki bu geçiş senaryosuna yönelik olarak tasarlanmıştır. Lütfen bu makaledeki yönergelere ek olarak ayrıntılı adımlar için bağlantıları izleyin.

### <a name="step-1-create-a-recovery-services-vault"></a>1\. Adım: kurtarma hizmetleri Kasası oluşturma

1. [Azure portalı](https://portal.azure.com) açın.
2. ** > ** **yedekleme** ve **Site Recovery (OMS)**  > **kaynak oluştur** ' u seçin. Alternatif olarak **, > ** **kurtarma hizmetleri kasasını** > **Ekle**' yi de seçebilirsiniz. 
3. VM 'Lerin çoğaltılacağı bir bölge belirtin. Aynı bölgedeki geçiş amacıyla, kaynak sanal makinelerinizin ve kaynak depolama hesaplarınızın bulunduğu bölgeyi seçin. 

### <a name="step-2-choose-your-protection-goals"></a>2\. Adım: koruma hedeflerinizi seçin 

1. Yapılandırma sunucusunu yüklemek istediğiniz VM 'de [Azure Portal](https://portal.azure.com)açın.
2. **Kurtarma Hizmetleri kasaları** > **ayarlar** > **Site Recovery** > **1. Adım: altyapıyı hazırlama** > **koruma hedefini**hazırlayın.

   ![Koruma hedefi bölmesine göz atma][2]

3. **Koruma hedefi**altında, ilk açılan listede **Azure**' ı seçin. İkinci açılan listede, **sanallaştırılmamış/diğer**' i seçin ve ardından **Tamam**' ı seçin.

   ![Doldurulmuş kutular ile koruma hedefi bölmesi][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>3\. Adım: kaynak ortamı ayarlama (yapılandırma sunucusu)

1. **Altyapıyı hazırla** > **kaynak** > **sunucu bölmeleri ekleme** ' ye giderek **birleşik kurulum 'u** ve kasa kayıt anahtarını Azure Site Recovery indirin. 
 
   Birleşik kurulumu çalıştırmak için kasa kayıt anahtarına ihtiyacınız olacak. Anahtar, oluşturulduktan sonra beş gün boyunca geçerlidir.

   ![Sunucu Ekle bölmesine göz atma][4]

2. **Sunucu Ekle** bölmesinde bir yapılandırma sunucusu ekleyin.

   ![Yapılandırma sunucusu seçiliyken Sunucu bölmesi Ekle][5]

3. Yapılandırma sunucusu olarak kullandığınız sanal makinede, yapılandırma sunucusunu ve işlem sunucusunu yüklemek için Birleşik kurulum 'U çalıştırın. Yüklemeyi tamamlamaya yönelik [ekran görüntülerini](../../site-recovery/vmware-walkthrough-overview.md) izleyebilirsiniz. Bu geçiş senaryosu için belirtilen adımlarla ilgili olarak aşağıdaki ekran görüntülerine başvurabilirsiniz.

   1. **Başlamadan önce**' de, **yapılandırma sunucusunu ve işlem sunucusunu yükler**' i seçin.

      ![Başlamadan önce sayfasına][6]

   2. **Kayıt**bölümünde, kasadan indirdiğiniz kayıt anahtarını bulup seçin.

      ![Kayıt sayfası][7]

   3. **Ortam Ayrıntıları**’nda VMware sanal makinelerini çoğaltıp çoğaltmayacağınızı seçin. Bu geçiş senaryosunda **Hayır**' ı seçin.

      ![Ortam ayrıntıları sayfası][8]

4. Yükleme tamamlandıktan sonra, **Microsoft Azure Site Recovery yapılandırma sunucusu** penceresinde şunları yapın:
 
   1. Site Recovery otomatik bulma için kullanabileceği hesabı oluşturmak için **hesapları Yönet** sekmesini kullanın. (Fiziksel makineleri koruma senaryosunda, hesabı ayarlama ilgili değildir ancak aşağıdaki adımlardan birini etkinleştirmek için en az bir hesabınız olması gerekir. Bu durumda, hesabı ve parolayı dilediğiniz şekilde adlandırın.) 
   2. Kasa kimlik bilgileri dosyasını karşıya yüklemek için **kasa kaydı** sekmesini kullanın.

      ![Kasa kaydı sekmesi][9]

### <a name="step-4-set-up-the-target-environment"></a>4\. Adım: hedef ortamı ayarlama

**Altyapıyı hazırla** > **hedefi**' ni seçin ve yük devretmeden sonra VM 'ler için kullanmak istediğiniz dağıtım modelini belirtin. Senaryonuza bağlı olarak **Klasik** veya **Kaynak Yöneticisi**seçebilirsiniz.

![Hedef bölme][10]

Site Recovery, bir veya birden çok uyumlu Azure depolama hesabınızın ve ağınızın olup olmadığını denetler. 

> [!NOTE]
> Çoğaltılan veriler için bir Premium depolama hesabı kullanıyorsanız, çoğaltma günlüklerini depolamak için ek bir standart depolama hesabı ayarlamanız gerekir.

### <a name="step-5-set-up-replication-settings"></a>5\. Adım: çoğaltma ayarlarını ayarlama

Yapılandırma sunucunuzun oluşturduğunuz çoğaltma ilkesiyle başarıyla ilişkilendirildiğini doğrulamak için [çoğaltma ayarlarını ayarla](../../site-recovery/vmware-walkthrough-overview.md)' yı izleyin.

### <a name="step-6-plan-capacity"></a>6\. Adım: kapasiteyi planlayın

1. Çoğaltma gereksinimlerinizi karşılamak için ağ bant genişliğini, depolamayı ve diğer gereksinimleri doğru bir şekilde tahmin etmek üzere [Kapasite planlayıcısı](../../site-recovery/site-recovery-capacity-planner.md) 'nı kullanın. 
2. İşiniz bittiğinde **Evet ' i** seçtiğinizde **Kapasite planlamasını tamamladım**.

   ![Kapasite planlamasını tamamladığınızı onaylama kutusu][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>7\. Adım: Mobility hizmetini yükler ve çoğaltmayı etkinleştirin

1. [Yüklemeyi](../../site-recovery/vmware-walkthrough-overview.md) kaynak sanal makinelerinize göndermeyi veya Mobility hizmetini kaynak sanal makinelerinize [el ile yüklemeyi](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) tercih edebilirsiniz. Yükleme ve el ile yükleyicinin yolunu, belirtilen bağlantıda iletme gereksinimini fark edebilirsiniz. El ile yükleme gerçekleştiriyorsanız, yapılandırma sunucusunu bulmak için bir iç IP adresi kullanmanız gerekebilir.

   ![Yapılandırma sunucusu Ayrıntılar sayfası][12]

   Yük devredilen VM 'nin iki geçici diski vardır: biri birincil VM 'den diğeri, kurtarma bölgesinde VM sağlama sırasında oluşturulur. Çoğaltmayı etkinleştirmeden önce geçici diski dışlamak için, çoğaltmayı etkinleştirmeden önce Mobility hizmetini yükleyebilirsiniz. Geçici diski dışarıda bırakma hakkında daha fazla bilgi edinmek için bkz. [diskleri çoğaltmanın dışında tutma](../../site-recovery/vmware-walkthrough-overview.md).

2. Aşağıda belirtilen şekilde çoğaltmayı etkinleştirin:
   1. Uygulama > **kaynağını** **Çoğalt** ' ı seçin. Çoğaltmayı ilk kez etkinleştirdikten sonra ek makineler için çoğaltmayı etkinleştirmek üzere kasada **+ Çoğalt** ' ı seçin.
   2. Adım 1 ' de, **kaynak** işlemini işlem sunucunuz olarak ayarlayın.
   3. 2\. adımda, yük devretme sonrası dağıtım modeli, geçirilecek bir Premium depolama hesabı, günlüklerin kaydedileceği bir standart depolama hesabı ve başarısız olacak bir sanal ağ belirtin.
   4. 3\. adımda korumalı VM 'Leri IP adresine göre ekleyin. (Bunları bulmak için bir iç IP adresi gerekebilir.)
   5. 4\. adımda, daha önce işlem sunucusunda ayarladığınız hesapları seçerek özellikleri yapılandırın.
   6. 5\. adımda, daha önce oluşturduğunuz çoğaltma ilkesini "5. Adım: çoğaltma ayarlarını ayarlama" bölümünde seçin.
   7. **Tamam**’ı seçin.

   > [!NOTE]
   > Bir Azure VM serbest bırakılır ve yeniden başlatıldığında aynı IP adresini alacak bir garanti yoktur. Yapılandırma sunucusu/işlem sunucusu veya korumalı Azure VM 'lerinin IP adresi değişirse, Bu senaryodaki çoğaltma düzgün çalışmayabilir.

   ![Kaynak seçiliyken çoğaltma bölmesini etkinleştir][13]

Azure Storage ortamınızı tasarlarken, bir kullanılabilirlik kümesindeki her VM için ayrı depolama hesapları kullanmanızı öneririz. [Her kullanılabilirlik kümesi için birden çok depolama hesabı kullanmak](../linux/manage-availability.md)üzere depolama katmanındaki en iyi uygulamayı izlemenizi öneririz. VM disklerini birden çok depolama hesabına dağıtmak, depolama kullanılabilirliğinin artırılmasına ve g/ç 'yi Azure Storage altyapısına dağıtmanıza yardımcı olur.

Sanal makinelerleriniz, tüm VM 'lerin disklerini tek bir depolama hesabında çoğaltmak yerine bir kullanılabilirlik kümesinde ise, birden çok VM 'yi birden çok kez geçirmeyi kesinlikle öneririz. Bu şekilde, aynı Kullanılabilirlik kümesindeki VM 'Ler tek bir depolama hesabını paylaşmaz. Her VM için tek seferde bir hedef depolama hesabı ayarlamak için **çoğaltmayı etkinleştir** bölmesini kullanın.
 
Yük devretme sonrası dağıtım modelini gereksinimize göre seçebilirsiniz. Yük devretme sonrası dağıtım modeliniz olarak Azure Resource Manager ' yi seçerseniz, VM 'nin yükünü bir VM 'ye (Kaynak Yöneticisi) Kaynak Yöneticisi devretmek veya VM (klasik) yükünü bir VM 'ye (Kaynak Yöneticisi) devretmek ().

### <a name="step-8-run-a-test-failover"></a>8\. Adım: yük devretme testi çalıştırma

Çoğaltmanın tamamlanıp tamamlanmadığını kontrol etmek için Site Recovery örneğinizi seçin ve ardından **ayarlar** > **çoğaltılan öğeler**' i seçin. Çoğaltma işleminizin durumunu ve yüzdesini görürsünüz. 

İlk çoğaltma tamamlandıktan sonra, çoğaltma stratejinizi doğrulamak için bir yük devretme testi çalıştırın. Yük devretme testi hakkında ayrıntılı adımlar için bkz. [Site Recovery yük devretme testi çalıştırma](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Herhangi bir yük devretmeyi çalıştırmadan önce, VM 'lerinizin ve çoğaltma stratejinizin gereksinimleri karşıladığından emin olun. Yük devretme testi çalıştırma hakkında daha fazla bilgi için bkz. [Azure 'a yük devretmeyi test etme Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Test yük devretmenin durumunu **ayarlar** > **işler** > *YOUR_FAILOVER_PLAN_NAME*görebilirsiniz. Bölmesinde, adımların ve başarı/başarısızlık sonuçlarının bir dökümünü görebilirsiniz. Yük devretme testi herhangi bir adımda başarısız olursa, hata iletisini denetlemek için adımı seçin. 

### <a name="step-9-run-a-failover"></a>9\. Adım: yük devretmeyi çalıştırma

Yük devretme sınamasını tamamladıktan sonra, disklerinizi Premium depolamaya geçirmek ve sanal makine örneklerini çoğaltmak için bir yük devretme çalıştırın. [Yük devretme çalıştırma](../../site-recovery/site-recovery-failover.md#run-a-failover)bölümündeki ayrıntılı adımları izleyin. 

**VM 'Leri Kapat ' ı seçtiğinizden ve en son verileri eşitlediğinizden**emin olun. Bu seçenek Site Recovery korunan VM 'Leri kapatmaya ve verilerin en son sürümünün yük devretmeleri için verileri eşitlemeye çalışacak şekilde çalışır. Bu seçeneği seçmezseniz veya deneme başarılı olmazsa, yük devretme VM için kullanılabilir en son kurtarma noktasından olur. 

Site Recovery, türü Premium depolama özellikli bir VM ile aynı veya buna benzer bir sanal makine örneği oluşturur. [Windows sanal makineleri fiyatlandırma](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) veya [Linux sanal makineleri fiyatlandırmasına](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)giderek çeşitli sanal makine örneklerinin performansını ve fiyatını kontrol edebilirsiniz.

## <a name="post-migration-steps"></a>Geçiş sonrası adımlar

1. Geçerliyse, **çoğaltılan VM 'leri kullanılabilirlik kümesine yapılandırın**. Site Recovery, VM 'Lerin kullanılabilirlik kümesiyle birlikte geçirilmesini desteklemez. Çoğaltılan sanal makinenizin dağıtımına bağlı olarak, aşağıdakilerden birini yapın:
   * Klasik dağıtım modeli aracılığıyla oluşturulan bir VM için: VM 'yi Azure portal kullanılabilirlik kümesine ekleyin. Ayrıntılı adımlar için, [mevcut bir sanal makineyi bir kullanılabilirlik kümesine ekleme](../linux/classic/configure-availability-classic.md)bölümüne gidin.
   * Kaynak Yöneticisi dağıtım modeli aracılığıyla oluşturulan bir VM için: VM yapılandırmanızı kaydedin ve ardından kullanılabilirlik kümesindeki VM 'Leri silip yeniden oluşturun. Bunu yapmak için, [set Azure Resource Manager VM kullanılabilirlik kümesi](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)konumundaki betiği kullanın. Bu betiği çalıştırmadan önce, sınırlamalarını denetleyin ve kapalı kalma süresini planlayın.

2. **Eski VM 'leri ve diskleri silin**. Premium disklerin kaynak disklerle tutarlı olduğundan ve yeni VM 'Lerin kaynak VM 'lerle aynı işlevi gerçekleştirdiğinizden emin olun. VM 'yi silin ve Azure portal kaynak depolama hesaplarınızdan diskleri silin. VM 'yi silmiş olsanız bile diskin silinmediği bir sorun varsa bkz. [depolama kaynak silme hatalarında sorun giderme](storage-resource-deletion-errors.md).

3. **Azure Site Recovery altyapısını temizleyin**. Site Recovery artık gerekmiyorsa altyapısını temizleyebilirsiniz. Çoğaltılan öğeleri, yapılandırma sunucusunu ve kurtarma ilkesini silin ve ardından Azure Site Recovery kasasını silin.

## <a name="troubleshooting"></a>Sorun giderme

* [Sanal makineler ve fiziksel sunucular için koruma izleme ve sorun giderme](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Microsoft Azure Site Recovery Forumu](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Sonraki adımlar

Sanal makineleri geçirmeye yönelik belirli senaryolar için aşağıdaki kaynaklara bakın:

* [Azure sanal makinelerini depolama hesapları arasında geçirme](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Linux sanal sabit diskini karşıya yükleme](upload-vhd.md)
* [Sanal makineleri Amazon AWS 'den Microsoft Azure 'ye geçirme](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Ayrıca, Azure depolama ve Azure sanal makineleri hakkında daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [Azure depolama alanı](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Sanal Makineler](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [IaaS VM 'Leri için bir disk türü seçin](disks-types.md)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png
