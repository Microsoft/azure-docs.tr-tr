---
title: Azure DevTest Labs ağ yalıtımı
description: Azure DevTest Labs 'de ağ yalıtımı hakkında bilgi edinin.
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: 28bfa048a8a6666deb58a8ecfff38779c91d95b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96352941"
---
# <a name="network-isolation-in-devtest-labs"></a>DevTest Labs 'de ağ yalıtımı

[Azure sanal ağı](../virtual-network/virtual-networks-overview.md) , Azure kaynaklarınızı genel İnternet 'ten yalıtmak için bir güvenlik sınırı görevi görür. Ayrıca, şirket içi kaynaklarınıza güvenli bir şekilde bağlanabilmelerini sağlamak için Azure sanal ağını şirket içi ağınıza da katabilirsiniz. DevTest Labs 'de, laboratuar kaynaklarının kurumsal ağ ilkelerini izlemesini sağlamak için [Tüm laboratuar sanal makinelerini](devtest-lab-configure-vnet.md) ve [ortamlarını ağınıza](connect-environment-lab-virtual-network.md) ayırabilirsiniz. 

Laboratuvar sahibi olarak Ayrıca, sanal makineleri ve ortamları seçili ağa yalıtmaya yönelik olarak Laboratuvarı tamamen yalıtmayı da seçebilirsiniz. Ayrıca, abonelikinizde oluşturulan laboratuvar depolama hesabı ve anahtar kasalarını yalıtabilirsiniz. Bu makalede, ağ yalıtımlı Laboratuvar oluşturma işlemi adım adım açıklanmaktadır. 

Ayrıca aşağıdaki makaleleri gözden geçirin:

- [DevTest Labs laboratuvar depolama hesabını nasıl kullanır?](encrypt-storage.md)
- [DevTest Labs anahtar kasalarını nasıl kullanır?](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> Ağ yalıtımı Şu anda yalnızca yeni Labs oluşturma işlemleri için destekleniyor.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>Laboratuvar oluşturma sırasında Ağ yalıtımını etkinleştirme adımları

1. Laboratuvar oluşturma sırasında **ağ** sekmesine gidin.
1. Laboratuvara sizin için oluşturacağınız bir **varsayılan** ağ seçebilir veya açılan listeden mevcut bir ağı seçebilirsiniz. Yalnızca laboratuvarın bulunduğu bölgedeki ve abonelikteki ağları seçebileceksiniz. 

    > [!div class="mx-imgBorder"]
    > ![Laboratuvar oluşturma](./media/network-isolation/create-lab.png)
1. Bir alt ağ seçin.

    > [!div class="mx-imgBorder"]
    > ![Alt ağ oluşturma](./media/network-isolation/create-lab-subnet.png)
1. Laboratuvar kaynaklarını (Laboratuvar depolama hesabı ve Anahtar Kasası) varsayılan ağa yalıtmak istiyorsanız, başka bir eylem gerekmez ve laboratuvar, ileri doğru kaynakları yalıtma işlemini işleymeyecektir.
 
    > [!div class="mx-imgBorder"]
    > ![Ağ yalıtımı](./media/network-isolation/isolate-lab-resources.png)
1. Laboratuvar kaynaklarını (Laboratuvar depolama hesabı ve Anahtar Kasası) seçtiğiniz mevcut bir ağa yalıtmak istiyorsanız, laboratuvarın yalıtılmış modda çalışmaya devam ettiğinden emin olmak için aşağıdaki adımları izlemeniz gerekir. 
 
    > [!div class="mx-imgBorder"]
    > ![Kaynağı ayır](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > Laboratuvar sahibinin, laboratuvardaki kaynakları yapılandırmadan ve/veya oluşturmadan önce bu adımları tamamlaması gerekir.

### <a name="steps-to-follow-post-lab-creation"></a>Laboratuvar oluşturmayı izlemeye yönelik adımlar

1. Laboratuvarın giriş sayfasında **genel bakış** sayfasında **kaynak grubunu** seçin. Laboratuvarın bulunduğu kaynak grubu için **kaynak grubu** sayfasını görmeniz gerekir. 
 
   > [!div class="mx-imgBorder"]
   > ![Contoso Laboratuvarı](./media/network-isolation/contoso-lab.png)
1. Laboratuvarın Azure Storage hesabını seçin. Laboratuvar depolama hesabının adlandırma kuralı: bir<*labnamewithoutınvalidcharacters* > *<4 basamaklı sayı*>. Örneğin, laboratuvar adı contosolab ise, depolama hesabı adı acontosolab1234 olabilir.
 
   > [!div class="mx-imgBorder"]
   > ![Contoso testi](./media/network-isolation/contoso-test.png)
1. Depolama hesabında güvenlik duvarları ve sanal ağlar ' a gidin ve ' güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine Izin ver ' onay kutusunun işaretli olduğundan emin olun. [DevTest Labs güvenilir bir Microsoft hizmeti olduğu](../storage/common/storage-network-security.md#trusted-microsoft-services)için bu seçenek, laboratuvarın bir ağ yalıtılmış modunda çalışmasını sağlayacaktır. 

   > [!div class="mx-imgBorder"]
   > ![Contoso laboratuvar güvenlik duvarları](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. Ardından, ve **var olan sanal ağı ekle**' ye tıklayın, Laboratuvarı oluştururken seçtiğiniz sanal ağı ve alt ağı seçin ve **Etkinleştir**' e tıklayın. 

   > [!div class="mx-imgBorder"]
   > ![Contoso sanal ağı](./media/network-isolation/contoso-lab-my-vnet.png)
5.  Seçilen sanal ağ için hizmet uç noktası başarıyla etkinleştirildikten sonra **Ekle**' ye tıklayın. 

   > [!div class="mx-imgBorder"]
   > ![Ekle](./media/network-isolation/contoso-firewall-add.png)
 
Bu şekilde, Azure depolama, eklenen sanal ağdan gelen bağlantılara izin verir ve Laboratuvarı ağ yalıtılmış modunda başarıyla çalışacak şekilde etkinleştirir. 

Bu ayarı birden çok laboratuvarda yapılandırmak için bu adımları otomatik hale getirmeyi seçebilirsiniz. 

[PowerShell ve CLı kullanarak Azure depolama için varsayılan ağ erişim kurallarını yönetme hakkında daha fazla bilgi edinin](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>Ağ yalıtımlı modda laboratuvar kullanırken hatırlamaları gerekenler

### <a name="accessing-labs-storage-account-outside-the-lab"></a>Laboratuvar dışında laboratuvarın depolama hesabına erişme 

Ağ yalıtımlı laboratuvarda, ' den özel görüntü oluşturmak için bir VHD 'yi laboratuvarın depolama hesabına yükleme gibi eylemler için, laboratuvar sahibinin, depolama hesabına izin verilen bir uç noktadan erişimi açıkça etkinleştirmeleri gerekir. Bunu, bir sanal makine oluşturarak ve bu sanal makineden laboratuvar depolama hesabına güvenli bir şekilde erişerek yapabilirsiniz. 

[Bir sanal makineden özel olarak bir depolama hesabına erişme hakkında daha fazla bilgi edinin](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>Laboratuvar 'ten kullanım verilerini dışarı aktarma 

Ağ yalıtımlı laboratuvara, [laboratuvarın kişisel kullanım verilerini dışarı aktarmak](personal-data-delete-export.md)için, laboratuvar sahibinin doğrudan depolama hesabı sağlaması ve verileri depolamak için hesap içinde bir blob oluşturması gerekir. 

Bir depolama hesabı sağlanmazsa, laboratuvarın depolama hesabına müşteri tarafından sağlanmayan bir depolama hesabı durumunda kullanabilmesi için bu işlem ağ yalıtılmış modunda başarısız olur. 

[Belirtilen depolama hesabında laboratuvar kullanım verilerini dışa aktarma hakkında daha fazla bilgi edinin](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>Sonraki adımlar

[Azure Resource Manager şablonlarını ve PowerShell 'i kullanarak laboratuvarları otomatik olarak oluşturma veya değiştirme](devtest-lab-use-arm-and-powershell-for-lab-resources.md)