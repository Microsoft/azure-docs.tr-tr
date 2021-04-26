---
title: Azure Site Recovery ile Azure VM olağanüstü durum kurtarmayı ayarlama öğreticisi
description: Bu öğreticide, Azure sanal makineleri için Site Recovery hizmetini kullanarak başka bir Azure bölgesine olağanüstü durum kurtarmayı ayarlayın.
ms.topic: tutorial
ms.date: 11/03/2020
ms.custom: mvc
ms.openlocfilehash: 473a264ef497cab4bd4f88372600161b33178099
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656878"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>Öğretici: Azure VM 'Leri için olağanüstü durum kurtarmayı ayarlama

Bu öğreticide, [Azure Site Recovery](site-recovery-overview.md)kullanarak Azure VM 'leri için olağanüstü durum kurtarmayı ayarlama gösterilmektedir. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Azure ayarlarını ve izinlerini doğrulama
> * Çoğaltmak istediğiniz VM 'Leri hazırlayın
> * Kurtarma Hizmetleri kasası oluşturma
> * VM çoğaltmasını etkinleştir

VM 'nin olağanüstü durum kurtarma ayarlaması için çoğaltma etkinleştirdiğinizde, Site Recovery Mobility hizmeti uzantısı sanal makineye yüklenir ve Azure Site Recovery kaydeder. Çoğaltma sırasında, VM disk yazmaları kaynak bölgedeki bir önbellek depolama hesabına gönderilir. Veriler buradan hedef bölgeye gönderilir ve kurtarma noktaları verilerden oluşturulur. Olağanüstü durum kurtarma sırasında bir VM 'nin yükünü devretmek için, hedef bölgedeki VM 'yi geri yüklemek için bir kurtarma noktası kullanılır.

> [!NOTE]
> Öğreticiler en basit varsayılan ayarlarla ilgili yönergeler sağlar. Azure VM olağanüstü durum kurtarma 'yı özelleştirilmiş ayarlarla ayarlamak istiyorsanız [Bu makaleyi](azure-to-azure-how-to-enable-replication.md)gözden geçirin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

- [Desteklenen bölgeleri gözden geçirin](azure-to-azure-support-matrix.md#region-support). Aynı coğrafya 'daki iki bölge arasında Azure VM 'Leri için olağanüstü durum kurtarma ayarlayabilirsiniz.
- Bir veya daha fazla Azure sanal makinesi gereklidir. [Windows](azure-to-azure-support-matrix.md#windows) veya [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) VM 'lerinin desteklendiğini doğrulayın.
- VM [işlem](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [depolama](azure-to-azure-support-matrix.md#replicated-machines---storage)ve [ağ](azure-to-azure-support-matrix.md#replicated-machines---networking) gereksinimlerini gözden geçirin.
- Bu öğretici, VM 'Lerin şifrelenmediğini varsayar. Şifrelenmiş VM 'Ler için olağanüstü durum kurtarma ayarlamak istiyorsanız, [Bu makaleye uyun](azure-to-azure-how-to-enable-replication-ade-vms.md).

## <a name="check-azure-settings"></a>Azure ayarlarını denetleyin

Hedef bölgedeki izinleri ve ayarları denetleyin.

### <a name="check-permissions"></a>İzinleri denetle

Azure hesabınızın, bir kurtarma hizmetleri Kasası oluşturmak ve hedef bölgede VM 'Ler oluşturmak için izinleri olması gerekir.

- Henüz ücretsiz bir Azure aboneliği oluşturduysanız, hesap yöneticisi olursunuz ve başka bir işlem yapmanız gerekmez.
- Yönetici değilseniz, ihtiyacınız olan izinleri almak için yönetici ile çalışın.
    - **Bir kasa oluşturun**: abonelikte yönetici veya sahip izinleri. 
    - **Kasadaki Site Recovery Işlemlerini yönetme**: *Site Recovery katılımcısı* yerleşik Azure rolü.
    - **Hedef bölgede Azure VM 'Leri oluşturun**: yerleşik *sanal makine katılımcısı* rolü ya da şunları yapmak için özel izinler:
        - Seçilen sanal ağda sanal makine oluşturma.
        - Bir Azure depolama hesabına yazın.
        - Azure tarafından yönetilen bir diske yazma.

### <a name="verify-target-settings"></a>Hedef ayarlarını doğrulama

Olağanüstü durum kurtarma sırasında kaynak bölgeden yük devretmek için VM 'Ler hedef bölgede oluşturulur. 

Aboneliğinizin hedef bölgede yeterli kaynak olup olmadığını denetleyin. Kaynak bölgedeki VM 'Lerle eşleşen boyutlarda VM 'Ler oluşturabilmeniz gerekir. Olağanüstü durum kurtarma ayarladığınızda Site Recovery hedef VM için aynı boyutu (veya mümkün olan en yakın boyutu) seçer.


## <a name="prepare-vms"></a>VM 'Leri hazırlama

VM 'Lerin giden bağlantılara ve en son kök sertifikalara sahip olduğundan emin olun. 


### <a name="set-up-vm-connectivity"></a>VM bağlantısı kurma

Çoğaltmak istediğiniz VM 'Lerin giden ağ bağlantısı olması gerekir.

> [!NOTE]
> Site Recovery, ağ bağlantısını denetlemek için bir kimlik doğrulama proxy 'si kullanmayı desteklemez.

#### <a name="outbound-connectivity-for-urls"></a>URL'ler için giden bağlantı

Giden bağlantıyı denetlemek için URL tabanlı bir güvenlik duvarı proxy 'si kullanıyorsanız, bu URL 'Lere erişim izni verin:

| **Ad**                  | **Ticari**                               | **Kamu**                                 | **Açıklama** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Depolama                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Verilerin VM’den kaynak bölgedeki önbellek depolama hesabına yazılmasına izin verir. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Site Recovery hizmet URL’leri için yetkilendirme ve kimlik doğrulama özellikleri sağlar. |
| Çoğaltma               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | VM’nin Site Recovery hizmetiyle iletişim kurmasına izin verir. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | VM’nin Site Recovery izleme ve tanılama verilerini yazmasına izin verir. |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP adresi aralıkları için giden bağlantı

Bağlantıyı denetlemek için ağ güvenlik grupları (NSG) kullanıyorsanız, bu [hizmet etiketleri](../virtual-network/service-tags-overview.md#available-service-tags)(IP adresi grupları) için 443 numaralı bağlantı noktasına izin veren hizmet etiketi tabanlı NSG kuralları oluşturun:

**Tag** | **İzin Ver** 
--- | ---
Depolama etiketi  |Verilerin VM 'den önbellek depolama hesabına yazılmasına izin verir.   
Azure AD etiketi | Azure AD 'ye karşılık gelen tüm IP adreslerine erişim sağlar.   
EventsHub etiketi | Site Recovery izlemeye erişim sağlar.  
Azuresterecovery etiketi | Herhangi bir bölgedeki Site Recovery hizmetine erişim sağlar.   
GuestAndHybridManagement etiketi | Çoğaltma için etkinleştirilen VM 'lerde çalışan Site Recovery Mobility aracısını otomatik olarak yükseltmek istiyorsanız kullanın.

Gerekli Etiketler ve etiketleme örnekleri hakkında [daha fazla bilgi edinin](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) .

### <a name="verify-vm-certificates"></a>Sanal makine sertifikalarını doğrulama

VM 'Lerin en son kök sertifikalara sahip olup olmadığını denetleyin. Aksi takdirde, güvenlik kısıtlamaları nedeniyle VM Site Recovery kaydedilemez.

- **Windows VM 'leri**: tüm güvenilen kök sertifikaların makinede olması için, tüm en son WINDOWS güncelleştirmelerini VM 'ye yükler. Bağlantısı kesilmiş bir ortamda, Windows Update ve sertifika güncelleştirmeleri için standart işlemlerinizi izleyin.
- **Linux sanal makineleri**: en son güvenilen kök sertifikaları ve sertifika iptal LISTESINI (CRL) almak için Linux dağıtıcısında sunulan yönergeleri izleyin.

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

VM 'Leri çoğaltmak istediğiniz kaynak bölgesi dışında herhangi bir bölgede bir kurtarma hizmetleri Kasası oluşturun.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Arama kutusuna *Kurtarma* yazın. **Hizmetler**' in altında, **Kurtarma Hizmetleri kasaları**' nı seçin.

    ![Kurtarma Hizmetleri kasalarını ara](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. **Kurtarma Hizmetleri kasaları**' nda **Ekle**' yi seçin.
4. **Kurtarma Hizmetleri Kasası oluşturma**  >  **temelleri**' nde, kasanın oluşturulacağı aboneliği seçin.
5. **Kaynak grubu**' nda, kasa için var olan bir kaynak grubunu seçin veya yeni bir tane oluşturun.
6. **Kasa adı**' nda, kasayı tanımlamak için bir kolay ad belirtin.
7. **Bölge**' de, kasanın yerleştirileceği Azure bölgesini seçin. [Desteklenen bölgeleri denetleyin](https://azure.microsoft.com/pricing/details/site-recovery/).
8. **Gözden geçir ve oluştur**’u seçin.

   ![Yeni kasa oluşturmak için sayfadaki kasa ayarları](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. **Gözden geçir + oluştur** bölümünde **Oluştur**' u seçin.

10. Kasa dağıtımı başlar. Bildirimlerin ilerlemesini izleyin.
11. Kasa dağıtıldıktan sonra, hızlı başvuru için kaydetmek üzere **panoya sabitle** ' yi seçin. Yeni kasayı açmak için **Kaynağa Git** ' i seçin. 
    
    ![Dağıtımdan sonra kasayı açma ve panoya sabitleme düğmeleri](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>Site Recovery etkinleştir

Kasa ayarları ' nda **Site Recovery etkinleştir**' i seçin.

![Kasada Site Recovery etkinleştiren seçim](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

Kaynak ayarlarını seçin ve VM çoğaltmasını etkinleştirin. 

### <a name="select-source-settings"></a>Kaynak ayarlarını seçin

1. Kasa > **Site Recovery** sayfasında, **Azure sanal makineler** altında **çoğaltmayı etkinleştir**' i seçin.

    ![Azure VM 'Leri için çoğaltmayı etkinleştirme seçimi](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. **Kaynak** >  **kaynak konumu**' nda, VM 'lerin çalışmakta olduğu kaynak Azure bölgesini seçin.
3. **Azure sanal makine dağıtım modeli**' nde varsayılan **Kaynak Yöneticisi** ayarını bırakın.
4. **Kaynak aboneliği**' nde, VM 'lerin çalıştığı aboneliği seçin. Kasadaki aynı Azure Active Directory (AD) kiracısındaki herhangi bir aboneliği seçebilirsiniz.
5. **Kaynak kaynak grubu**' nda, VM 'leri içeren kaynak grubunu seçin.
6. **Kullanılabilirlik alanları arasında olağanüstü durum kurtarma** bölümünde varsayılan ayarı **yok** ' u bırakın.

     ![Kaynağı ayarlama](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. **İleri**’yi seçin.

### <a name="select-the-vms"></a>VM’leri seçme

Site Recovery seçili abonelik/kaynak grubuyla ilişkili VM 'Leri alır.

1. **Sanal makinelerde**, olağanüstü durum kurtarma için etkinleştirmek Istediğiniz VM 'leri seçin.

     ![Çoğaltma için VM 'Leri seçmek için sayfa](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. **İleri**’yi seçin.

### <a name="review-replication-settings"></a>Çoğaltma ayarlarını gözden geçirme

1. **Çoğaltma ayarları**' nda ayarları gözden geçirin. Site Recovery, hedef bölge için varsayılan ayarları/ilkeleri oluşturur. Bu öğreticinin amaçları doğrultusunda varsayılan ayarları kullanırız.
2. **Çoğaltmayı etkinleştir** seçeneğini belirleyin.

    ![Ayarları özelleştirmek ve çoğaltmayı etkinleştirmek için sayfa](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. Bildirimlerde çoğaltma ilerlemesini izleyin.

     ![Bildirimlerin ilerlemesini izleme ](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![ başarılı çoğaltma bildirimini izleme](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. Etkinleştirdiğiniz sanal makineler kasa > **çoğaltılan öğeler** sayfasında görünür.

    ![Çoğaltılan öğeler sayfasında VM](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure VM için olağanüstü durum kurtarmayı etkinleştirdiniz. Şimdi, yük devretmenin beklendiği gibi çalışıp çalışmadığını denetlemek için bir detaya git çalıştırın.

> [!div class="nextstepaction"]
> [Olağanüstü durum kurtarma tatbikatı çalıştırma](azure-to-azure-tutorial-dr-drill.md)
