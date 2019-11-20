---
title: Site Recovery ile fiziksel sunucular için yük devretme ve yeniden çalışma ayarlama
description: Fiziksel sunucuların yükünü Azure 'a devretmek ve Azure Site Recovery ile olağanüstü durum kurtarma için şirket içi siteye geri dönme hakkında bilgi edinin
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 2c0d2e57a34286f65be45a95403a32de42c51908
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084563"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Yük devretme ve geri dönme fiziksel sunucuları Azure 'a çoğaltma

Bu öğreticide, fiziksel bir sunucunun yükünü Azure 'a nasıl devretmek açıklanmaktadır. Yük devretdikten sonra, kullanılabilir olduğunda sunucuyu şirket içi sitenize geri devreder.

## <a name="preparing-for-failover-and-failback"></a>Yük devretme ve yeniden çalışma için hazırlanma

Site Recovery kullanılarak Azure 'a çoğaltılan fiziksel sunucular yalnızca VMware VM 'Leri olarak yeniden çalışabilir. Yeniden yük devredebilmek için bir VMware altyapısına ihtiyacınız vardır.

Yük devretme ve yeniden çalışma dört aşamalıdır:

1. **Azure’a yük devretme**: Makineleri şirket içi siteden Azure’a devredin.
2. **Azure VM 'Lerini yeniden koruma**: Azure VM 'leri yeniden koruyun, böylece şirket Içi VMware VM 'lerine tekrar çoğaltmaya başlar.
3. **Şirket içine yük devretme**: Azure’dan yeniden çalıştırmak için bir yük devretme işlemi çalıştırın.
4. Şirket **Içi VM 'Leri yeniden koruma**: veriler yeniden başlatıldıktan sonra, geri aldığınız şirket Içi VMware VM 'lerini yeniden koruyarak Azure 'a çoğaltmaya başlarlar.

## <a name="verify-server-properties"></a>Sunucu özelliklerini doğrulama

Sunucu özelliklerini doğrulayın ve Azure VM 'Leri için [Azure gereksinimleriyle](vmware-physical-azure-support-matrix.md#replicated-machines) uyumlu olduğundan emin olun.

1. **Korunan öğeler**' de **çoğaltılan öğeler**' e tıklayın ve makineyi seçin.

2. **Çoğaltılan öğe** bölmesinde makine bilgilerinin, sistem durumunun ve en son kullanılabilir kurtarma noktalarının bir özeti vardır. Daha fazla ayrıntı görüntülemek için **Özellikler**’e tıklayın.
3. **İşlem ve ağ**bölümünde Azure adını, kaynak grubunu, hedef boyutunu, [kullanılabilirlik kümesini](../virtual-machines/windows/tutorial-availability-sets.md)ve yönetilen disk ayarlarını değiştirebilirsiniz
4. Ağ ayalarını, yük devretmeden sonra Azure VM’nin yerleştirileceği ağ/alt ağ ve VM’ye atanacak IP adresi dahil olmak üzere görüntüleyebilir ve değiştirebilirsiniz.
5. **Diskler**' de makine işletim sistemi ve veri diskleri hakkında bilgi görebilirsiniz.

## <a name="run-a-failover-to-azure"></a>Azure'a yük devretme işlemini çalıştırma

1. **Ayarlar** > **Çoğaltılan öğeler** bölümünde makine > **Yük devretme**’ye tıklayın.
2. **Yük devretme**’de yük devretmenin yapılacağı bir **Kurtarma Noktası** seçin. Aşağıdaki seçeneklerden birini kullanabilirsiniz:
   - **Varsayılan**: Bu seçenekte öncelikle Site Recovery’ye gönderilen tüm veriler işlenir. Yük devretmeden sonra oluşturulan Azure VM, yük devretme tetiklendiğinde Site Recovery’ye çoğaltılan tüm verilere sahip olduğundan en düşük RPO (Kurtarma Noktası Hedefi) sağlanır.
   - **En son işlenen**: Bu seçenek makineyi Site Recovery tarafından işlenen en son kurtarma noktasına devreder. İşlenmemiş verileri işlemek için zaman harcanmadığından bu seçenekte düşük bir RTO (Kurtarma Süresi Hedefi) sağlanır.
   - **En son uygulamayla tutarlı**: Bu seçenek, makineyi Site Recovery tarafından işlenen en son uygulamayla tutarlı kurtarma noktasına devreder.
   - **Özel**: Bir kurtarma noktası belirtin.

3. Yük devretmeyi tetiklemeden önce Site Recovery kaynak makineyi kapatmayı denemek istiyorsanız, **yük devretmeye başlamadan önce makineyi Kapat ' ı** seçin. Kapatma işlemi başarısız olsa bile yük devretme devam eder. Yük devretme işlemini **İşler** sayfasında takip edebilirsiniz.
4. Azure VM’ye bağlanmaya hazırsanız, yük devretmeden sonra VM’yi doğrulamak için bağlanın.
5. Doğruladıktan sonra yük devretmeyi **Yürütün**. Bu, tüm kullanılabilir kurtarma noktalarını siler.

> [!WARNING]
> Devam eden bir yük devretme işlemini iptal etmeyin. Yük devretme başlamadan önce makine çoğaltması durduruluyor. Yük devretmeyi iptal ederseniz, yanıt vermez, ancak makine tekrar çoğaltılmaz.
> Fiziksel sunucular için ek yük devretme işleminin tamamlanması yaklaşık sekiz ila on dakika sürebilir.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Yük devretmeden sonra Azure VM'lerine bağlanmak için hazırlık yapma

Yük devretme sonrasında RDP/SSH kullanarak Azure VM'lerine bağlanmak istiyorsanız [buradaki](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) tabloda özetlenen gereksinimleri izleyin.

Yük devretme sonrasında karşılaştığınız bağlantı sorunlarını gidermek için [burada](site-recovery-failover-to-azure-troubleshoot.md) anlatılan adımları izleyin.

## <a name="create-a-process-server-in-azure"></a>Azure’da bir işlem sunucusu oluşturma

İşlem sunucusu verileri Azure VM’den alır ve şirket içi siteye gönderir. İşlem sunucusu ve korunan makine arasında düşük gecikmeli bir ağ gerekir.

- Bir Azure ExpressRoute bağlantınız varsa test etmek için yapılandırma sunucusuna otomatik olarak kurulan şirket içi işlem sunucusunu kullanabilirsiniz.
- Bir VPN bağlantınız varsa veya üretim ortamında yeniden çalışma gerçekleştiriyorsanız, yeniden çalışma için Azure tabanlı işlem sunucusu olarak bir Azure VM’si ayarlamanız gerekir.
- Azure 'da bir işlem sunucusu kurmak için [Bu makaledeki](vmware-azure-set-up-process-server-azure.md) yönergeleri izleyin.

## <a name="configure-the-master-target-server"></a>Ana hedef sunucuyu yapılandırma

Varsayılan olarak, ana hedef sunucusu yeniden çalışma verileri alır. Şirket içi yapılandırma sunucusunda çalışır.

- Geri aldığınız VMware VM, VMware vCenter Server tarafından yönetilen bir ESXi ana bilgisayarı üzerinde ise, ana hedef sunucunun, sanal makinenin veri deposuna (VMDK) erişimi olması gerekir ve bu da çoğaltılan verileri VM disklerine yazar. VM veri deposunun, ana hedefin ana bilgisayarına okuma/yazma erişimi ile bağlı olduğundan emin olun.
- Bir vCenter sunucusu tarafından yönetilmeyen ESXi ana bilgisayarı Site Recovery hizmeti yeniden koruma sırasında yeni bir sanal makine oluşturur. VM, ana hedef VM 'yi oluşturduğunuz ESX konağında oluşturulur. VM’nin sabit diski, üzerinde ana hedef sunucunun çalıştığı ana bilgisayar tarafından erişilebilen bir veri deposunda olmalıdır.
- Yeniden çalışan fiziksel makineler için, makineyi yeniden koruyabilmeniz için ana hedef sunucunun çalıştığı ana bilgisayarın bulunmasını tamamlamalısınız.
- Diğer bir seçenek olan şirket içi VM yeniden çalışma için zaten mevcutsa, yeniden çalışma yapmadan önce onu silmektir. Yeniden çalışma, ana hedef ESX sunucusu ile aynı ana bilgisayarda yeni bir VM oluşturur. Farklı bir konuma yeniden çalışma gerçekleştirdiğinizde, veriler şirket içi ana hedef sunucu tarafından kullanılan aynı veri deposuna ve aynı ESX ana bilgisayarına kurtarılır.
- Ana hedef sunucuda Storage vMotion kullanamazsınız. Kullandığınız takdirde diskler işlem için kullanılabilir olmadığından yeniden çalışma gerçekleştirilemez. Ana hedef sunucuları vMotion listenizin dışında tutun.

## <a name="reprotect-azure-vms"></a>Azure VM’lerini yeniden koruma

Bu yordamda şirket içi VM’nin kullanılabilir olmadığı ve farklı bir konuma yeniden koruma gerçekleştirdiğiniz varsayılır.

1. **Ayarlar** > **Çoğaltılan öğeler** bölümünde yük devretme gerçekleştirilen VM’ye sağ tıklayın ve **Yeniden Koru** seçeneğini belirleyin.
2. **Yeniden koru** bölümünde **Azure’dan Şirket içine** seçeneğinin belirlendiğinden emin olun.
3. Şirket içi ana hedef sunucuyu ve işlem sunucusunu belirtin.

4. **Veri deposu**’nda şirket içi diskleri kurtarmak istediğiniz ana hedef veri deposunu seçin. Bu seçeneği, şirket içi VM silindiğinde ve yeni diskler oluşturmanız gerektiğinde kullanın. Diskler zaten varsa, ancak bir değer belirtmeniz gerekiyorsa bu ayarlar yok sayılır.
5. Ana hedef bekletme sürücüsünü seçin. Yeniden çalışma ilkesi otomatik olarak seçilir.
6. Yeniden korumaya başlamak için **Tamam**’a tıklayın. Sanal makineyi Azure’dan şirket içi siteye çoğaltan bir iş başlar. **İşler** sekmesinde ilerleme durumunu izleyebilirsiniz.

> [!NOTE]
> Azure VM’sini mevcut bir şirket içi VM’ye kurtarmak istiyorsanız, şirket içi sanal makinenin veri deposunu okuma/yazma erişimiyle ana hedef sunucunun ESXi ana bilgisayarına bağlayın.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Azure’dan şirket içine yük devretme çalıştırma

Yeniden şirket içine çoğaltma yapmak için bir yeniden çalıştırma ilkesi kullanılır. Bu ilke, Azure’a çoğaltma için bir çoğaltma ilkesi oluşturduğunuzda otomatik olarak yeniden oluşturulur:

- İlke, yapılandırma sunucusu ile otomatik olarak ilişkilendirilir.
- İlke değiştirilemez.
- İlke değerleri:
    - RPO eşiği = 15 dakika
    - Kurtarma noktası bekletme = 24 saat
    - Uygulamayla tutarlı anlık görüntü sıklığı = 60 dakika

Yük devretmeyi şu şekilde çalıştırın:

1. **Çoğaltılan Öğeler** sayfasında makineye sağ tıklayın ve **Planlanmamış Yük Devretme** seçeneğini belirleyin.
2. **Yük Devretmeyi Onayla** bölümünde yük devretme yönünün Azure’dan olduğundan emin olun.

3. Yük devretme için kullanmak istediğiniz kurtarma noktasını seçin. Uygulamayla tutarlı kurtarma noktası, en yakın zaman noktasından öncedir ve bazı verilerin kaybolmasına neden olur. Yük devretme çalıştığında Site Recovery, Azure VM’lerini kapatır ve şirket içi VM’yi başlatır. Hizmet bir süre kapalı kalacağından uygun bir zaman seçin.
4. Makineye sağ tıklayın ve **Yürüt**’e tıklayın. Bu, Azure VM’lerini kaldıran bir işi tetikler.
5. Azure VM’lerinin beklenen şekilde kapatıldığından emin olun.


## <a name="reprotect-on-premises-machines-to-azure"></a>Şirket içi makineleri Azure’da yeniden koruma

Veriler artık şirket içi sitenizde tekrar yerleştirilir, ancak Azure’a çoğaltılmaz. Tekrar Azure’a çoğaltmaya başlamak için şunları yapabilirsiniz:

1. Kasa > **Ayarlar** >**Çoğaltılan Öğeler** bölümünde yeniden çalışan VM’leri seçin ve **Yeniden Koru**’ya tıklayın.
2. Çoğaltılan verileri Azure’a göndermek için kullanılan işlem sunucusunu seçin ve **Tamam**’a tıklayın.

Yeniden koruma bittikten sonra VM tekrar Azure’a çoğaltır ve yük devretmeyi gerektiği gibi çalıştırabilirsiniz.
