---
title: Azure VMware Çözüm VM 'lerinin olağanüstü durum kurtarması için Azure Site Recovery kaynakları hazırlama
description: Azure Site Recovery kullanarak Azure VMware Çözüm makinelerinde olağanüstü durum kurtarma için Azure kaynaklarını nasıl hazırlayacağınızı öğrenin.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 2bd305e3760a8c3d743037e7d90b71f5e9579eda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395487"
---
# <a name="prepare-azure-site-recovery-resources-for-disaster-recovery-of-azure-vmware-solution-vms"></a>Azure VMware Çözüm VM 'lerinin olağanüstü durum kurtarması için Azure Site Recovery kaynakları hazırlama

Bu makalede, [Azure Site Recovery](site-recovery-overview.md) hizmetini kullanarak Azure VMware Çözüm VM 'leri için olağanüstü durum kurtarmayı ayarlayabilmeniz için Azure kaynakları ve bileşenlerinin nasıl hazırlanacağı açıklanmaktadır. [Azure VMware çözümü](../azure-vmware/introduction.md) , Azure 'da özel bulutlar sağlar. Bu özel bulutlar, adanmış çıplak Azure altyapısından oluşturulan vSphere kümelerini içerir.

Bu makale, Azure VMware Çözüm VM 'Leri için olağanüstü durum kurtarmayı ayarlamayı gösteren bir serideki ilk öğreticidir. 


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure hesabının çoğaltma izinlerine sahip olduğunu doğrulayın.
> * Kurtarma Hizmetleri kasası oluşturun. Kasada VM'lerin meta veri ve yapılandırma bilgileri ile diğer çoğaltma bileşenleri tutulur.
> * Bir Azure sanal ağı (VNet) ayarlayın. Yük devretmeden sonra Azure VM 'Leri oluşturulduğunda, bu ağa katılır.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir. Mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı yönergeler için Site Recovery Içindekiler tablosunun nasıl yapılır bölümündeki makaleyi gözden geçirin.

> [!NOTE]
> Azure VMware çözümü için Azure Site Recovery kullanmanın bazı kavramlarından bazıları, şirket içi VMware VM 'lerinin olağanüstü durum kurtarması ile çakışıyor ve bu nedenle belgeler, buna göre çapraz başvurulmalıdır.

## <a name="before-you-start"></a>Başlamadan önce

- Azure 'da Azure VMware çözümü özel bulutu [dağıtma](../azure-vmware/tutorial-create-private-cloud.md)
- [VMware](vmware-azure-architecture.md) olağanüstü durum kurtarma mimarisini gözden geçirme
- [VMware](vmware-azure-common-questions.md) için genel soruları okuyun

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun. Ardından [Azure Portal](https://portal.azure.com)oturum açın.


## <a name="verify-account-permissions"></a>Hesap izinlerini doğrulama

Ücretsiz Azure hesabınızı oluşturduysanız aboneliğinizin yöneticisi olursunuz ve ihtiyacınız olan izinleriniz vardır. Abonelik yöneticisi değilseniz, ihtiyaç duyduğunuz izinleri atamak için yöneticiyle birlikte çalışın. Yeni bir sanal makineye yönelik çoğaltmayı etkinleştirmek için şunları yapma iznine sahip olmalısınız:

- Seçilen kaynak grubunda sanal makine oluşturma.
- Seçilen sanal ağda sanal makine oluşturma.
- Bir Azure depolama hesabına yazın.
- Azure yönetilen diskine yazın.

Bu görevleri tamamlamak için hesabınıza Sanal Makine Katkıda Bulunan yerleşik rolünün atanması gerekir. Ayrıca, bir kasadaki Site Recovery işlemleri yönetmek için hesabınıza Site Recovery katkıda bulunan yerleşik rolü atanmalıdır.


## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

1. Azure portal menüsünde **kaynak oluştur**' u seçin ve markette **Kurtarma** için arama yapın.
2. **Yedekleme ve** arama sonuçlarından Site Recovery seçin ve yedekleme ve Site Recovery sayfasında **Oluştur**' a tıklayın. 
3. **Kurtarma Hizmetleri Kasası oluştur** sayfasında, **aboneliği** seçin. **Contoso aboneliği** kullanıyorsunuz.
4. **Kaynak grubu**' nda, var olan bir kaynak grubunu seçin veya yeni bir tane oluşturun. Bu öğreticide **contosoRG** kullandık.
5. **Kasa adı** alanına kasayı tanımlamak için bir kolay ad girin. Bu öğretici dizisi için **ContosoVMVault**’u kullanacağız.
6. **Bölge** bölümünde, kasasının bulunduğu bölgeyi seçin. **Batı Avrupa** kullanacağız.
7. **Gözden geçir ve oluştur**’u seçin.

   ![Kurtarma Hizmetleri Kasası oluşturma sayfasının ekran görüntüsü.](./media/tutorial-prepare-azure/new-vault-settings.png)

   Yeni kasa artık **Pano**  >  **tüm kaynaklar** bölümünde ve ana **Kurtarma Hizmetleri kasaları** sayfasında listelenir.

## <a name="set-up-an-azure-network"></a>Azure ağı ayarlama

 Azure VMware Çözüm VM 'Leri Azure yönetilen disklere çoğaltılır. Yük devretme gerçekleştiğinde, Azure VM 'Leri bu yönetilen disklerden oluşturulur ve bu yordamda belirttiğiniz Azure ağına eklenir.

1. [Azure portalında](https://portal.azure.com)**Kaynak oluştur** > **Ağ** > **Sanal ağ** seçeneklerini belirleyin.
2. Dağıtım modeli olarak **Kaynak Yöneticisi** seçili tut.
3. **Ad** bölümünde bir ağ adı girin. Ad, Azure kaynak grubu içinde benzersiz olmalıdır. Bu öğreticide **ContosoASRnet** kullanıyoruz.
4. **Adres alanı**' nda, CDR gösteriminde sanal ağın adres aralığını girin. **10.1.0.0/24** kullanıyorsunuz.
5. **Abonelik** bölümünde ağın oluşturulacağı aboneliği seçin.
6. Ağın oluşturulacağı **kaynak grubunu** belirtin. Biz mevcut **contosoRG** kaynak grubunu kullanıyoruz.
7. **Konum**' da, kurtarma hizmetleri kasasının oluşturulduğu bölgeyi seçin. Öğreticimizde **Batı Avrupa**. Ağın kasada aynı bölgede olması gerekir.
8. **Adres aralığı** alanına ağ aralığını girin. Bir alt ağ kullanmadığınız için **10.1.0.0/24** kullanıyoruz.
9. Temel DDoS korumasının varsayılan seçeneklerini hizmet uç noktası olmayan veya ağ üzerinde güvenlik duvarı olmadan terk ediyoruz.
9. **Oluştur**’u seçin.

   ![Sanal ağ oluşturma seçeneklerinin ekran görüntüsü.](media/tutorial-prepare-azure/create-network.png)

Sanal ağın oluşturulması birkaç saniye sürer. Oluşturulduktan sonra, Azure portal panosunda görürsünüz.




## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Altyapıyı hazırla](avs-tutorial-prepare-avs.md)
- Azure ağları [hakkında bilgi edinin](../virtual-network/virtual-networks-overview.md).
- Yönetilen diskler [hakkında bilgi edinin](../virtual-machines/managed-disks-overview.md) .
