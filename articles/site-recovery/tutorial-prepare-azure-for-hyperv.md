---
title: Azure Site Recovery ile Hyper-V olağanüstü durum kurtarma için Azure 'u hazırlama
description: Azure Site Recovery kullanarak şirket içi Hyper-V VM 'lerinin olağanüstü durum kurtarması için Azure 'u nasıl hazırlayacağınızı öğrenin
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.custom: MVC
ms.openlocfilehash: ec1c41935d200ad220716940f898549d835510ba
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579288"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Hyper-V olağanüstü durum kurtarma için Azure kaynaklarını hazırlama

 [Azure Site Recovery](site-recovery-overview.md) , planlı ve plansız kesintiler sırasında iş uygulamalarınızı çalışır durumda tutarak iş sürekliliği ve olağanüstü durum kurtarma (BCDR) sağlar. Site Recovery, şirket içi makinelerin ve Azure sanal makinelerinin çoğaltma, yük devretme ve kurtarma gibi olağanüstü durum kurtarma işlemlerini yönetir ve düzenler.

Bu öğretici, şirket içi Hyper-V VM 'Leri için olağanüstü durum kurtarmayı ayarlamayı açıklayan bir serinin ilk ilkidir.

> [!NOTE]
> Bir senaryo için en basit dağıtım yolunu göstermek üzere öğreticiler tasarlıyoruz. Bu öğreticiler mümkün olduğunda varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Daha fazla bilgi için, ilgili her senaryo için "nasıl yapılır" bölümüne bakın.

Bu öğreticide, şirket içi VM 'Leri (Hyper-V) Azure 'a çoğaltmak istediğinizde Azure bileşenlerini nasıl hazırlayacağınız gösterilmektedir. Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Azure hesabınızın çoğaltma izinlerine sahip olduğunu doğrulayın.
> * Çoğaltılan makinelerin görüntülerini depolayan bir Azure depolama hesabı oluşturun.
> * VM 'Ler ve diğer çoğaltma bileşenleri için meta verileri ve yapılandırma bilgilerini depolayan bir kurtarma hizmetleri Kasası oluşturun.
> * Bir Azure ağı ayarlayın. Yük devretmeden sonra Azure VM 'Leri oluşturulduğunda, bu ağa katılır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.

## <a name="sign-in"></a>Oturum açın

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="verify-account-permissions"></a>Hesap izinlerini doğrulama

Henüz ücretsiz bir Azure hesabı oluşturduysanız, bu aboneliğin yöneticisi olursunuz. Yönetici değilseniz, ihtiyaç duyduğunuz izinleri atamak için yöneticiyle birlikte çalışın. Yeni bir sanal makineye yönelik çoğaltmayı etkinleştirmek için şunları yapma iznine sahip olmalısınız:

- Seçilen kaynak grubunda sanal makine oluşturma.
- Seçilen sanal ağda sanal makine oluşturma.
- Seçilen depolama hesabına yazma.

Bu görevleri gerçekleştirmek için hesabınıza sanal makine katılımcısı yerleşik rolü atanmalıdır. Bir kasadaki Site Recovery işlemlerini yönetmek için hesabınıza Site Recovery katkıda bulunan yerleşik rolü atanmalıdır.

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Çoğaltılan makinelerin görüntüleri Azure depolama alanında tutulur. Şirket içinden Azure’a yük devretme gerçekleştirdiğinizde depolama alanından Azure sanal makineleri oluşturulur. Depolama hesabının, Kurtarma Hizmetleri kasasıyla aynı bölgede olması gerekir.

1. [Azure Portal](https://portal.azure.com) menüsünde, **kaynak**  >  **depolama**  >  **depolama hesabı oluştur-blob, dosya, tablo, kuyruk**' u seçin.
2. **Depolama hesabı oluştur** bölümüne hesap için bir ad girin.  Seçtiğiniz ad Azure içinde benzersiz olmalı, 3 ile 24 karakter uzunluğunda olmalı ve yalnızca küçük harf ve rakam kullanmalıdır. Bu öğretici için **contosovmsacct1910171607** kullanın.
3. **Dağıtım modeli** bölümünde **Kaynak Yöneticisi**’ni seçin.
4. **Hesap türü**' nde, **depolama (genel amaçlı v1)** öğesini seçin. Blob depolamayı seçmeyin.
5. **Çoğaltma** bölümünde depolama yedeklemesi için **Okuma Erişimli Coğrafi Olarak Yedekli depolama**’yı seçin.
6. **Performans** bölümünde **Standart**’ı seçin. Ardından, **erişim katmanı**' nda, **etkin**' in varsayılan seçeneğini belirleyin.
7. **Abonelik**' da, yeni depolama hesabını oluşturmak istediğiniz aboneliği seçin.
8. **Kaynak grubu** bölümünde yeni bir kaynak grubu adı girin. Azure Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Bu öğretici için **ContosoRG** kullanın.
9. **Konum**' da, depolama hesabınız için coğrafi konumu seçin. Bu öğretici için **Batı Avrupa** kullanın.
10. Depolama hesabını oluşturmak için **Oluştur**’u seçin.

   ![Depolama hesabı oluşturma seçeneklerinin ekran görüntüsü.](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Kurtarma hizmetleri kasası oluşturma

1. Azure portal **+ kaynak oluştur**' u seçin ve ardından Azure Marketi 'Nde Kurtarma Hizmetleri ' ni arayın.
2. **Backup ve Site Recovery (OMS)** seçin. Ardından, **yedekleme ve Site Recovery** sayfasında **Oluştur**' u seçin.
1. **Kurtarma Hizmetleri kasası > adı**' nda, kasayı tanımlamak için bir kolay ad girin. Bu öğretici için **ContosoVMVault** adını kullanın.
2. **Kaynak grubu**' nda, var olan bir kaynak grubunu seçin veya yeni bir tane oluşturun. Bu öğretici için **contosoRG** kullanın.
3. **Konum**' da, kasasının bulunacağı bölgeyi seçin. Bu öğretici için **Batı Avrupa** kullanın.
4. Panodan kasaya hızlıca erişmek için **panoya sabitle**  >  **Oluştur**' u seçin.

![Kurtarma Hizmetleri Kasası oluşturma sayfasının ekran görüntüsü.](./media/tutorial-prepare-azure/new-vault-settings.png)

Yeni kasa **Pano**  >  **tüm kaynaklar**' da ve ana **Kurtarma Hizmetleri kasaları** sayfasında görüntülenir.

## <a name="set-up-an-azure-network"></a>Azure ağı ayarlama

Yük devretmeden sonra depolamadan Azure sanal makineleri oluşturulduğunda sanal makineler bu ağa katılır.

1. [Azure portalında](https://portal.azure.com)**Kaynak oluştur** > **Ağ** > **Sanal ağ** seçeneklerini belirleyin. Dağıtım modeli olarak Resource Manager’ı seçili bırakın.
2. **Ad** bölümünde bir ağ adı girin. Ad, Azure kaynak grubu içinde benzersiz olmalıdır. Bu öğretici için **Contosoasrnet** kullanın.
3. Ağın oluşturulacağı kaynak grubunu belirtin. Bu öğreticide, var olan **contosoRG** kaynak grubunu kullanın.
4. **Adres aralığı**' nda, ağ aralığı olarak **10.0.0.0/24** girin. Bu ağ için alt ağ yok.
5. **Abonelik** bölümünde ağın oluşturulacağı aboneliği seçin.
6. **Konum** alanında **Batı Avrupa**' yi seçin. Ağın, Kurtarma Hizmetleri kasasıyla aynı bölgede olması gerekir.
7. Temel DDoS korumasının varsayılan seçeneklerini ağ üzerinde hizmet uç noktası olmadan bırakın.
8. **Oluştur**’u seçin.

![Sanal ağ oluşturma seçeneklerinin ekran görüntüsü.](media/tutorial-prepare-azure/create-network.png)

Sanal ağın oluşturulması birkaç saniye sürer. Oluşturulduktan sonra, Azure portal panosunda görürsünüz.

## <a name="useful-links"></a>Yararlı bağlantılar

Şunları öğrenin:
- [Azure ağları](../virtual-network/virtual-networks-overview.md)
- [Yönetilen diskler](../virtual-machines/managed-disks-overview.md)



## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Şirket içi Hyper-V altyapısını Azure 'a olağanüstü durum kurtarma için hazırlama](hyper-v-prepare-on-premises-tutorial.md)
