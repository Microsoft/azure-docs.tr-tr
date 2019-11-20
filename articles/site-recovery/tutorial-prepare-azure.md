---
title: Azure Site Recovery ile şirket içi olağanüstü durum kurtarma için Azure 'u hazırlama
description: Azure Site Recovery ile şirket içi makinelerin olağanüstü durum kurtarma işlemleri için Azure’ın nasıl hazırlanacağını öğrenin.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1ec668fac087773001ca401eefb5ca8bc10ea2b8
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620590"
---
# <a name="prepare-azure-for-on-premises-disaster-recovery-to-azure"></a>Azure 'da şirket içi olağanüstü durum kurtarma için Azure 'u hazırlama

Bu makalede, [Azure Site Recovery](site-recovery-overview.md) hizmetini kullanarak şirket Içi VMware VM 'Leri, Hyper-V VM 'Leri veya Windows/Linux fiziksel sunucularının Azure 'da olağanüstü durum kurtarmayı ayarlayabilmeniz için Azure kaynakları ve bileşenlerinin nasıl hazırlanacağı açıklanmaktadır.

Bu makale, şirket içi sanal makineler için olağanüstü durum kurtarmanın nasıl ayarlanacağını gösteren serideki ilk öğreticidir. 


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure hesabının çoğaltma izinlerine sahip olduğunu doğrulayın.
> * Kurtarma Hizmetleri kasası oluşturun. Kasada VM'lerin meta veri ve yapılandırma bilgileri ile diğer çoğaltma bileşenleri tutulur.
> * Bir Azure sanal ağı (VNet) ayarlayın. Yük devretmeden sonra Azure VM 'Leri oluşturulduğunda, bu ağa katılır.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir. Mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı yönergeler için Site Recovery Içindekiler tablosunun nasıl yapılır bölümündeki makaleyi gözden geçirin.

## <a name="before-you-start"></a>Başlamadan önce

- [VMware](vmware-azure-architecture.md), [Hyper-V](hyper-v-azure-architecture.md)ve [fiziksel sunucu](physical-azure-architecture.md) olağanüstü durum kurtarma mimarisini gözden geçirin.
- [VMware](vmware-azure-common-questions.md) ve [Hyper-V](hyper-v-azure-common-questions.md) için genel soruları okuyun

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun. Ardından [Azure Portal](https://portal.azure.com)oturum açın.


## <a name="verify-account-permissions"></a>Hesap izinlerini doğrulama

Ücretsiz Azure hesabınızı oluşturduysanız aboneliğinizin yöneticisi olursunuz ve ihtiyacınız olan izinleriniz vardır. Abonelik yöneticisi değilseniz, ihtiyaç duyduğunuz izinleri atamak için yöneticiyle birlikte çalışın. Yeni bir sanal makineye yönelik çoğaltmayı etkinleştirmek için şunları yapma iznine sahip olmalısınız:

- Seçilen kaynak grubunda sanal makine oluşturma.
- Seçilen sanal ağda sanal makine oluşturma.
- Bir Azure depolama hesabına yazın.
- Azure yönetilen diskine yazın.

Bu görevleri tamamlamak için hesabınıza Sanal Makine Katkıda Bulunan yerleşik rolünün atanması gerekir. Ayrıca, bir kasadaki Site Recovery işlemleri yönetmek için hesabınıza Site Recovery katkıda bulunan yerleşik rolü atanmalıdır.


## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

1. Azure portal menüsünde **kaynak oluştur**' u seçin ve markette **Kurtarma**için arama yapın.
2. **Yedekleme ve** arama sonuçlarından Site Recovery seçin ve yedekleme ve Site Recovery sayfasında **Oluştur**' a tıklayın. 
3. **Kurtarma Hizmetleri Kasası oluştur** sayfasında, **aboneliği**seçin. **Contoso aboneliği**kullanıyorsunuz.
4. **Kaynak grubu**' nda, var olan bir kaynak grubunu seçin veya yeni bir tane oluşturun. Bu öğreticide **contosoRG**kullandık.
5. **Kasa adı**alanına kasayı tanımlamak için bir kolay ad girin. Bu öğretici dizisi için **ContosoVMVault**’u kullanacağız.
6. **Bölge**bölümünde, kasasının bulunduğu bölgeyi seçin. **Batı Avrupa** kullanacağız.
7. **İncele ve oluştur**’u seçin.

   ![Yeni kasa oluştur](./media/tutorial-prepare-azure/new-vault-settings.png)

   Yeni kasa artık **pano** > **tüm kaynaklar**' da ve ana **Kurtarma Hizmetleri kasaları** sayfasında listelenir.

## <a name="set-up-an-azure-network"></a>Azure ağı ayarlama

Şirket içi makineler Azure yönetilen disklere çoğaltılır. Yük devretme gerçekleştiğinde, Azure VM 'Leri bu yönetilen disklerden oluşturulur ve bu yordamda belirttiğiniz Azure ağına eklenir.

1. [Azure portalında](https://portal.azure.com) **Kaynak oluştur** > **Ağ** > **Sanal ağ** seçeneklerini belirleyin.
2. Dağıtım modeli olarak **Kaynak Yöneticisi** seçili tut.
3. **Ad** bölümünde bir ağ adı girin. Ad, Azure kaynak grubu içinde benzersiz olmalıdır. Bu öğreticide **ContosoASRnet** kullanıyoruz.
4. **Adres alanı**' nda, CDR gösteriminde sanal ağın adres aralığını girin. **10.1.0.0/24**kullanıyorsunuz.
5. **Abonelik** bölümünde ağın oluşturulacağı aboneliği seçin.
6. Ağın oluşturulacağı **kaynak grubunu** belirtin. Biz mevcut **contosoRG** kaynak grubunu kullanıyoruz.
7. **Konum**' da, kurtarma hizmetleri kasasının oluşturulduğu bölgeyi seçin. Öğreticimizde **Batı Avrupa**. Ağın kasada aynı bölgede olması gerekir.
8. **Adres aralığı**alanına ağ aralığını girin. Bir alt ağ kullanmadığınız için **10.1.0.0/24**kullanıyoruz.
9. Temel DDoS korumasının varsayılan seçeneklerini hizmet uç noktası olmayan veya ağ üzerinde güvenlik duvarı olmadan terk ediyoruz.
9. **Oluştur**'u seçin.

   ![Sanal ağ oluşturun](media/tutorial-prepare-azure/create-network.png)

Sanal ağın oluşturulması birkaç saniye sürer. Oluşturulduktan sonra, Azure portal panosunda görürsünüz.




## <a name="next-steps"></a>Sonraki adımlar

- VMware olağanüstü durum kurtarma için, [Şirket Içi VMware altyapısını hazırlayın](tutorial-prepare-on-premises-vmware.md).
- Hyper-V olağanüstü durum kurtarma için, Şirket [Içi Hyper-v sunucularını hazırlayın](hyper-v-prepare-on-premises-tutorial.md).
- Fiziksel sunucu olağanüstü durum kurtarma için [yapılandırma sunucusunu ve kaynak ortamı ayarlama](physical-azure-disaster-recovery.md)
- Azure ağları [hakkında bilgi edinin](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).
- Yönetilen diskler [hakkında bilgi edinin](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) .
