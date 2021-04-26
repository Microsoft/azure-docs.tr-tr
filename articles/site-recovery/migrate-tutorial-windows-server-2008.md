---
title: Azure geçişi/Site Recovery ile Windows Server 2008 sunucularını Azure 'a geçirme
description: Bu makalede, şirket içi Windows Server 2008 makinelerini Azure 'a nasıl geçirebileceğiniz ve Azure geçişi önerilmektedir.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: MVC
ms.openlocfilehash: 492bcddaca3cbec2877343c37a665ae62fadcabb
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581260"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Windows Server 2008 çalıştıran sunucuları Azure'a geçirme

Bu öğreticide, Azure Site Recovery kullanarak Windows Server 2008 veya 2008 R2 çalıştıran şirket içi sunucuları Azure 'a nasıl geçirebileceğiniz gösterilmektedir. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Şirket içi ortamınızı geçiş için hazırlayın.
> * Hedef ortamı ayarlayın.
> * Çoğaltma ilkesi ayarlayın.
> * Çoğaltmayı etkinleştirin.
> * Her şeyin beklendiği gibi çalıştığından emin olmak için bir test geçişi çalıştırın.
> * Azure 'a yük devretmek ve geçişi tamamladıktan sonra.

## <a name="migrate-with-azure-migrate"></a>Azure geçişi ile geçiş

[Azure geçişi](../migrate/migrate-services-overview.md) hizmetini kullanarak makineleri Azure 'a geçirmeniz önerilir. 

- Azure geçişi, sunucu geçişi için oluşturulmuştur.
- Azure geçişi, şirket içi makinelerin Azure 'a bulunması, değerlendirilmesi ve geçirilmesi için merkezi bir merkez sağlar. Azure Site Recovery, yalnızca olağanüstü durum kurtarma için kullanılmalıdır, geçiş değildir.
- Azure geçişi, Windows Server 2008 çalıştıran sunucuların geçirilmesini destekler.


## <a name="migrate-with-site-recovery"></a>Site Recovery ile geçir

### <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri


|Operating System  | Ortam  |
|---------|---------|
|Windows Server 2008 SP2 - 32 bit ve 64 bit (IA-32 ve x86-64)</br>- Standard</br>- Enterprise</br>- Datacenter   |     VMware VM'leri, Hyper-V VM'leri ve Fiziksel Sunucular    |
|Windows Server 2008 R2 SP1 - 64 bit</br>- Standard</br>- Enterprise</br>- Datacenter     |     VMware VM'leri, Hyper-V VM'leri ve Fiziksel Sunucular|

> [!WARNING]
> - Server Core sürümünü çalıştıran sunucular geçiş için desteklenmez.
> - Geçiş öncesinde en son hizmet paketini ve Windows güncelleştirmelerini yüklediğinizden emin olun.


### <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, [VMware ve fiziksel sunucu geçişi](vmware-azure-architecture.md) veya [Hyper-V sanal makine geçişi](hyper-v-azure-architecture.md) Azure Site Recovery mimarisini gözden geçirmeniz yararlı olabilir. 

Windows Server 2008 veya Windows Server 2008 R2 çalıştıran Hyper-V sanal makinelerini geçirmek için [şirket içi makineleri Azure'a geçirme](migrate-tutorial-on-premises-azure.md) öğreticisindeki adımları izleyin.

Bu öğreticinin geri kalan bölümünde Windows Server 2008 veya 2008 R2 çalıştıran şirket içi VMware sanal makinelerini ve Fiziksel sunucuları geçirme adımları gösterilmektedir.
> [!TIP]
> VMware VM 'lerini Azure 'a geçirmeyi daha az bir şekilde mi arıyorsunuz? [Buraya tıklayın](../migrate/tutorial-migrate-vmware.md)


### <a name="limitations-and-known-issues"></a>Sınırlamalar ve bilinen sorunlar

- Windows Server 2008 SP2 sunucuları geçirmek için kullanılan Yapılandırma Sunucusu, ek işlem sunucuları ve mobility hizmetinin Azure Site Recovery yazılımının 9.19.0.0 veya üzeri sürümünü kullanıyor olması gerekir.

- Windows Server 2008 SP2 çalıştıran sunucuların çoğaltılması sırasında uygulamayla tutarlı kurtarma noktaları ve çoklu VM tutarlılığı özelliği desteklenmez. Windows Server 2008 SP2 sunucuları kilitlenmeyle tutarlı bir kurtarma noktasına geçirilmelidir. Kilitlenmeyle tutarlı kurtarma noktası varsayılan olarak 5 dakikada bir oluşturulur. Uygulamayla tutarlı anlık görüntü sıklığı yapılandırılmış bir çoğaltma ilkesinin kullanılması, uygulamayla tutarlı kurtarma noktalarının bulunmaması nedeniyle çoğaltma durumunun kritik düzeye geçmesine neden olabilir. Hatalı pozitif sonuçlardan kaçınmak için çoğaltma ilkesindeki uygulamayla tutarlı anlık görüntü sıklığını "Kapalı" olarak ayarlayın.

- Mobility hizmetinin çalışması için geçirilen sunucularda .NET Framework 3.5 Service Pack 1 sürümünün yüklü olması gerekir.

- Sunucunuzda dinamik diskler varsa belirli yapılandırmalarda bu disklerin yük devredilen sunucularda çevrimdışı olarak işaretlendiğini veya yabancı disk olarak gösterildiğini görebilirsiniz. Ayrıca dinamik disklerdeki yansıtılmış birimlerin yansıtılmış küme durumunun "Sorunlu boşluk" şeklinde olduğunu da fark edebilirsiniz. Bu sorunu düzeltmek için diskmgmt.msc aracında bu diskleri el ile içeri aktarıp yeniden etkinleştirebilirsiniz.

- Geçirilen sunucularda vmstorfl.sys sürücüsünün bulunması gerekir. Geçirilen sunucuda bu sürücünün olmaması durumunda yük devretme başarısız olabilir. 
  > [!TIP]
  >Sürücünün "C:\Windows\system32\drivers\vmstorfl.sys" konumunda mevcut olup olmadığını kontrol edin. Sürücü bulunamadıysa geçici çözüm olarak aynı konumda işlevsiz bir dosya oluşturabilirsiniz. 
  >
  > Komut istemini açın (Çalıştır > cmd) ve şu komutu çalıştırın: "copy nul c:\Windows\system32\drivers\vmstorfl.sys"

- 32 bit işletim sistemi çalıştıran Windows Server 2008 SP2 sunuculara Azure'a yük devretme işleminin veya yük devretme testinin hemen sonrasında RDP ile bağlantı kuramayabilirsiniz. Yük devreden sanal makineyi Azure portalda yeniden başlatın ve bağlanmayı tekrar deneyin. Yine bağlanamıyorsanız sunucunun uzak masaüstü bağlantılarına izin verecek şekilde yapılandırılıp yapılandırılmadığını denetleyin ve bağlantıyı engelleyen güvenlik duvarı kuralı veya ağ güvenlik grubu olmadığından emin olun. 
  > [!TIP]
  > Sunucuları geçirmeden önce yük devretme testi gerçekleştirmeniz önerilir. Geçirdiğiniz her sunucuda en az bir başarılı test yük devretmesi gerçekleştirdiğinizden emin olun. Yük devretme testinin bir parçası olarak yük devretme testi gerçekleştirilen makineye bağlanın ve tüm bileşenlerin beklendiği gibi çalıştığından emin olun.
  >
  >Yük devretme testi işlemi kesintiye neden olmaz ve kendi seçtiğiniz yalıtılmış bir ağda sanal makine oluşturarak geçiş testi yapmanıza yardımcı olur. Yük devretme işleminden farklı olarak yük devretme testi sırasında veri çoğaltma devam eder. Geçişe hazır olduğunuzdan emin olana kadar istediğiniz sayıda yük devretme testi gerçekleştirebilirsiniz. 
  >
  


### <a name="get-started"></a>başlarken

Azure aboneliğini ve şirket içi VMware/Fiziksel ortamı hazırlamak için aşağıdaki görevleri gerçekleştirin:

1. [Azure’u hazırlama](tutorial-prepare-azure.md)
2. Şirket içi [VMware](vmware-azure-tutorial-prepare-on-premises.md) 'yi hazırlama


### <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

1. [Azure Portal](https://portal.azure.com)  >  **Kurtarma hizmetlerinde** oturum açın.
2. **Kaynak**  >  **yönetimi araçları**  >  **yedeklemesi ve Site Recovery** oluştur ' a tıklayın.
3. **Ad** bölümünde **W2K8-migration** kolay adını belirtin. Birden fazla aboneliğiniz varsa uygun olanı seçin.
4. **w2k8migrate** adlı bir kaynak grubu oluşturun.
5. Bir Azure bölgesi belirtin. Desteklenen bölgeleri kontrol etmek için [Azure Site Recovery Fiyatlandırma Ayrıntıları](https://azure.microsoft.com/pricing/details/site-recovery/) bölümündeki coğrafi kullanılabilirlik kısmına bakın.
6. Panodan kasaya hızlıca erişmek için önce **Panoya sabitle** seçeneğine ve sonra **Oluştur**’a tıklayın.

   ![Yeni kasa oluşturma seçeneklerini gösteren ekran görüntüsü.](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

Yeni kasa, **Pano**’da **Tüm kaynaklar** bölümüne ve ana **Kurtarma Hizmetleri kasaları** sayfasına eklenir.


### <a name="prepare-your-on-premises-environment-for-migration"></a>Ortamınızı geçiş için hazırlama

- VMware üzerinde çalışan Windows Server 2008 sanal makinelerini geçirmek için [VMware'de şirket içi Yapılandırma Sunucusu'nu kurun](vmware-azure-tutorial.md#set-up-the-source-environment).
- Yapılandırma Sunucusu bir VMware sanal makinesi olarak kurulamıyorsa [Yapılandırma Sunucusu'nu şirket içindeki bir fiziksel sunucuya veya sanal makineye kurun](physical-azure-disaster-recovery.md#set-up-the-source-environment).

### <a name="set-up-the-target-environment"></a>Hedef ortamı ayarlama

Hedef kaynaklarını seçin ve doğrulayın.

1. **Altyapı hedefini hazırla**' ya tıklayın  >  ve kullanmak istediğiniz Azure aboneliğini seçin.
2. Kaynak Yöneticisi dağıtım modelini belirtin.
3. Site Recovery, bir veya birden çok uyumlu Azure depolama hesabınızın ve ağınızın olup olmadığını denetler.


### <a name="set-up-a-replication-policy"></a>Çoğaltma ilkesi ayarlama

1. Yeni bir çoğaltma ilkesi oluşturmak için, **Site Recovery altyapı**  >  **çoğaltma ilkeleri**  >  **+ Çoğaltma İlkesi**' ne tıklayın.
2. **Çoğaltma ilkesi oluştur** bölümünde bir ilke adı belirtin.
3. **RPO eşiği** bölümünde kurtarma noktası hedefi (RPO) sınırını belirtin. Çoğaltma RPO değeri bu sınırı aştığında bir uyarı oluşturulur.
4. **Kurtarma noktası bekletme** bölümünde, her kurtarma noktası için bekletme süresinin ne kadar olacağını (saat) belirtin. Çoğaltılan sunucular, Bu penceredeki herhangi bir noktaya kurtarılabilir. Premium depolama alanına çoğaltılan makineler için 24 saate, standart depolama için de 72 saate kadar bekletme desteklenir.
5. **Uygulamayla tutarlı anlık görüntü sıklığı** ayarını **Kapalı** olarak ayarlayın. İlkeyi oluşturmak için **Tamam**’a tıklayın.

İlke, yapılandırma sunucusu ile otomatik olarak ilişkilendirilir.

> [!WARNING]
> Çoğaltma ilkesinin Uygulamayla tutarlı anlık görüntü sıklığı ayarını **KAPALI** olarak belirlediğinizden emin olun. Windows Server 2008 çalıştıran sunucularının çoğaltılması sırasında yalnızca kilitlenmeyle tutarlı kurtarma noktaları desteklenir. Uygulamayla tutarlı anlık görüntü sıklığı için başka bir değer belirtmek, uygulamayla tutarlı kurtarma noktalarının olmaması nedeniyle sunucunun çoğaltma durumunu kritik olarak açıp hatalı uyarılarla sonuçlanır.

   ![Çoğaltma ilkesi oluşturma seçeneklerini gösteren ekran görüntüsü.](media/migrate-tutorial-windows-server-2008/create-policy.png)

### <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

Geçirilen Windows Server 2008 SP2/Windows Server 2008 R2 SP1 sunucusu için [çoğaltmayı etkinleştirin](physical-azure-disaster-recovery.md#enable-replication).
   
   ![Fiziksel makine ekleme seçeneklerini gösteren ekran görüntüsü.](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Çoğaltmayı etkinleştirme seçeneklerini gösteren ekran görüntüsü.](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

### <a name="run-a-test-migration"></a>Geçiş testi çalıştırma

İlk çoğaltma tamamlandıktan ve sunucu durumu **Korumalı** olduktan sonra çoğaltılan sunucularda yük devretme testi gerçekleştirebilirsiniz.

Her şeyin beklendiği gibi çalıştığından emin olmak için bir Azure’a [yük devretme testi](tutorial-dr-drill-azure.md) çalıştırın.

   ![Sınama yük devretmesi komutunu gösteren ekran görüntüsü.](media/migrate-tutorial-windows-server-2008/testfailover.png)


### <a name="migrate-to-azure"></a>Azure’a geçiş

Geçirmek istediğiniz makineler için yük devretmeyi çalıştırın.

1. **Ayarlar**  >  **çoğaltılan öğeler** ' de **Yük devretme**> makine ' ye tıklayın.
2. **Yük devretme**’de yük devretmenin yapılacağı bir **Kurtarma Noktası** seçin. En son kurtarma noktasını seçin.
3. **Yük devretmeyi başlatmadan önce makineyi kapatın** seçeneğini belirleyin. Site Recovery, yük devretmeyi tetiklemeden önce sunucuyu kapatmaya çalışır. Kapatma işlemi başarısız olsa bile yük devretme devam eder. **İşler** sayfasında yük devretme ilerlemesini izleyebilirsiniz.
4. Azure VM’nin Azure’da beklendiği gibi görüntülenip görüntülenmediğini kontrol edin.
5. **Çoğaltılan öğeler**' de, sunucuyu sağ tıklatın > **geçişi Tamam**' ı seçin. Bu, şunları yapar:

    - Geçiş işlemini sonlandırır, sunucu için çoğaltmayı durduruyor ve hizmeti için Site Recovery faturalandırmayı durduruyor.
    - Bu adım, çoğaltma verilerini temizler. Geçirilen VM 'Leri silmez.

   ![Tüm geçiş komutunu gösteren ekran görüntüsü.](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Devam eden bir yük devretme işlemini Iptal etmeyin**: yük devretme başlatılmadan önce sunucu çoğaltması durdurulur. Devam eden bir yük devretme işlemini iptal ederseniz, yük devretme işlemi duraklar, ancak sunucu çoğaltılmaya devam eder.

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> Azure geçişi ile ilgili [sık sorulan soruları gözden geçirin](../migrate/resources-faq.md) .