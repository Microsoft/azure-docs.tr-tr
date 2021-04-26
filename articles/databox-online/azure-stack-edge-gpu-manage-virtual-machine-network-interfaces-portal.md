---
title: Azure Stack Edge Pro 'da VM ağı arabirimlerini Azure portal aracılığıyla yönetme
description: Azure portal aracılığıyla Azure Stack Edge Pro GPU 'ya dağıtılan VM 'lerde ağ arabirimlerini yönetmeyi öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 3e709b04b4eac60e6cc0ba3e53eb77583162dfef
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078902"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU 'unuzdaki VM 'lerde ağ arabirimlerini yönetmek için Azure portal kullanın

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure portal, şablonlar, Azure PowerShell cmdlet 'leri ve Azure CLı/Python betikleri aracılığıyla Azure Stack Edge cihazında sanal makineler (VM) oluşturabilir ve yönetebilirsiniz. Bu makalede, Azure portal kullanarak Azure Stack Edge cihazınızda çalışan bir VM 'deki ağ arabirimlerinin nasıl yönetileceği açıklanır. 

Bir VM oluşturduğunuzda, oluşturulacak bir sanal ağ arabirimi belirtirsiniz. Oluşturulduktan sonra sanal makineye bir veya daha fazla ağ arabirimi eklemek isteyebilirsiniz. Ayrıca, var olan bir ağ arabirimi için varsayılan ağ arabirimi ayarlarını da değiştirmek isteyebilirsiniz.

Bu makalede, mevcut bir VM 'ye ağ arabirimi ekleme, IP türü (statik vs. dinamik) gibi var olan ayarları değiştirme ve son olarak var olan bir arabirimi kaldırma veya ayırma işlemleri açıklanmaktadır. 

        
## <a name="about-network-interfaces-on-vms"></a>VM 'lerde ağ arabirimleri hakkında

Ağ arabirimi, Azure Stack Edge Pro cihazınızda çalışan bir sanal makinenin (VM) Azure ve şirket içi kaynaklarla iletişim kurmasını sağlar. Cihazınızda işlem ağı için bir bağlantı noktası etkinleştirdiğinizde, bu ağ arabiriminde bir sanal anahtar oluşturulur. Bu sanal anahtar daha sonra, cihazınızda VM 'Ler veya Kapsayıcılı uygulamalar gibi işlem iş yüklerini dağıtmak için kullanılır. 

Cihazınız yalnızca bir sanal anahtarı ancak birden çok sanal ağ arabirimini destekliyor. SANAL makinenizin her bir ağ arabirimine atanmış statik veya dinamik bir IP adresi vardır. VM 'niz üzerinde birden çok ağ arabirimine atanan IP adresleriyle, VM 'niz üzerinde belirli yetenekler etkinleştirilmiştir. Örneğin, VM 'niz tek bir sunucuda farklı IP adresleriyle ve SSL sertifikalarıyla birden çok Web sitesini veya hizmeti barındırabilir. Cihazınızdaki bir VM, güvenlik duvarı veya yük dengeleyici gibi bir ağ sanal gereci olarak görev yapabilir. <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>Önkoşullar

Azure portal aracılığıyla cihazınızdaki VM 'Leri yönetmeye başlamadan önce şunları yaptığınızdan emin olun:

1. Etkinleştirilmiş bir Azure Stack Edge Pro GPU cihazına erişirsiniz. Cihazınızda işlem için bir ağ arabirimi etkinleştirdiniz. Bu eylem, VM 'niz üzerinde bu ağ arabiriminde bir sanal anahtar oluşturur. 
    1. Cihazınızın yerel kullanıcı arabiriminde, **işlem**' a gidin. Sanal anahtar oluşturmak için kullanacağınız ağ arabirimini seçin.

        > [!IMPORTANT] 
        > İşlem için yalnızca bir bağlantı noktası yapılandırabilirsiniz.

    1. Ağ arabiriminde işlem etkinleştirin. Azure Stack Edge Pro GPU, bu ağ arabirimine karşılık gelen bir sanal anahtar oluşturur ve yönetir.

1. Cihazınızda en az bir sanal makine dağıtılmış. Bu sanal makineyi oluşturmak için [Azure Portal aracılığıyla Azure Stack Edge Pro 'unuzda VM dağıtma](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)' daki yönergelere bakın.

1. SANAL makinenizin **durdurulmuş** durumda olması gerekir. VM 'nizi durdurmak için **sanal makineler > genel bakış ' a** gidin ve durdurmak istediğiniz VM 'yi seçin. VM özellikleri sayfasında **Durdur** ' u seçin ve onay istendiğinde **Evet** ' i seçin. Ağ arabirimlerini eklemek, düzenlemek veya silmek için önce VM 'yi durdurmanız gerekir.

    ![VM 'yi VM Özellikleri sayfasından durdur](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>Ağ arabirimi ekleme

Cihazınızda dağıtılan bir sanal makineye bir ağ arabirimi eklemek için bu adımları izleyin. 

1. Durdurduğu sanal makineye gidin ve ardından **VM özellikleri** sayfasına gidin. **Ağ**'ı seçin.
    
    ![VM özellikleri sayfasında ağ ' ı seçin](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. **Ağ** dikey penceresinde, komut çubuğundan **+ ağ arabirimi Ekle**' yi seçin.

    ![Ağ arabirimi Ekle ' yi seçin](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. **Ağ arabirimi Ekle** dikey penceresinde aşağıdaki parametreleri girin:

    
    |Sütun1  |Sütun2  |
    |---------|---------|
    |Name     | Kaynak grubu içinde benzersiz bir ad. Ağ arabirimi oluşturulduktan sonra ad değiştirilemez. Birden çok ağ arabirimini kolayca yönetmek için, [adlandırma kuralları](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)'nda belirtilen önerileri kullanın.     |
    |Sanal ağ| Ağ arabiriminde işlem yaptığınızda cihazınızda oluşturulan sanal anahtarla ilişkili sanal ağ. Aygıtınızla ilişkili yalnızca bir sanal ağ vardır.         |         
    |Alt ağ     | Seçilen sanal ağ içindeki bir alt ağ. Bu alan, işlem ' i etkinleştirdiğiniz ağ arabirimiyle ilişkili alt ağ ile otomatik olarak doldurulur.         |       
    |IP ataması   | Ağ arabiriminiz için statik veya dinamik bir IP. Statik IP, belirtilen alt ağ aralığından kullanılabilir ve ücretsiz bir IP olmalıdır. Ortamda bir DHCP sunucusu varsa, dinamik öğesini seçin.        | 

    ![Ağ arabirimi dikey penceresi ekleme](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. Ağ arabirimi oluşturma işleminin devam ettiğini belirten bir bildirim görürsünüz.

    ![Ağ arabirimi oluşturulduğunda bildirim](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  Ağ arabirimi başarıyla oluşturulduktan sonra, ağ arabirimlerinin listesi yeni oluşturulan arabirimi görüntüleyecek şekilde yenilenir.

    ![Ağ arabirimlerinin güncelleştirilmiş listesi](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>Ağ arabirimini düzenleme

Cihazınızda dağıtılan bir sanal makineyle ilişkili bir ağ arabirimini düzenlemek için bu adımları izleyin.

1. Durdurduğu sanal makineye gidin ve **VM özellikleri** sayfasına gidin. **Ağ**'ı seçin.

1. Ağ arabirimleri listesinde, düzenlemek istediğiniz arabirimi seçin. Ağ arabiriminin en sağında seçili olan düzenleme simgesini (kurşun kalem) seçin.  

    ![Düzenlemek için bir ağ arabirimi seçin](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. **Ağ arabirimini Düzenle** dikey penceresinde yalnızca ağ arabiriminin IP atamasını değiştirebilirsiniz. Ağ arabirimiyle ilişkili ad, sanal ağ ve alt ağ, oluşturulduktan sonra değiştirilemez. **IP atamasını** statik olarak değiştirin ve değişiklikleri kaydedin.

    ![Ağ arabirimi için IP atamasını değiştirme](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. Ağ arabirimi 'nin listesi, güncelleştirilmiş ağ arabirimini görüntüleyecek şekilde yenilenir.


## <a name="detach-a-network-interface"></a>Ağ arabirimini ayırma

Cihazınızda dağıtılan bir sanal makineyle ilişkili bir ağ arabirimini ayırmak veya kaldırmak için bu adımları izleyin.

1. Durdurduğu sanal makineye gidin ve **VM özellikleri** sayfasına gidin. **Ağ**'ı seçin.

1. Ağ arabirimleri listesinde, düzenlemek istediğiniz arabirimi seçin. Ağ arabiriminin en sağında seçili değil ayır simgesini (sökme) seçin.  

    ![Ayrımak üzere bir ağ arabirimi seçin](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. Arabirim tamamen ayrıldıktan sonra, kalan arabirimleri göstermek için ağ arabirimlerinin listesi yenilenir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Stack Edge Pro cihazınızda sanal makinelerin nasıl dağıtılacağını öğrenmek için, bkz. [Azure Portal aracılığıyla sanal makineleri dağıtma](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
