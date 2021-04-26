---
title: Öğretici-Azure 'da VMware özel bulutunuz için ağı yapılandırma
description: Özel bulutunuzu Azure 'da dağıtmak için gereken ağı oluşturmayı ve yapılandırmayı öğrenin
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: c609d191191cd9de111e83fe62761b149405cccc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103494385"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Öğretici: Azure 'da VMware özel bulutunuz için ağı yapılandırma

Azure VMware çözümü özel bulutu, bir Azure sanal ağı gerektirir. Azure VMware çözümü şirket içi vCenter 'ı desteklemediğinden, şirket içi ortamınıza tümleştirme için ek adımlar gereklidir. ExpressRoute bağlantı hattını ve bir sanal ağ geçidini ayarlama de gereklidir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal ağ oluşturma
> * Sanal ağ geçidi oluşturma
> * ExpressRoute devrenizi ağ geçidine bağlama
> * VCenter ve NSX Manager için URL 'Leri bulma

## <a name="prerequisites"></a>Önkoşullar 
[Azure VMware çözümü özel bulutu](tutorial-create-private-cloud.md)oluşturduğunuz bir sanal ağ. 

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. [Özel bulut oluşturma öğreticisinde](tutorial-create-private-cloud.md) oluşturduğunuz kaynak grubuna gidin ve yeni bir kaynak tanımlamak Için **+ Ekle** ' yi seçin. 

1. Market 'te **Ara** metin kutusuna **sanal ağ** yazın. Sanal ağ kaynağını bulup seçin.

1. **Sanal ağ sayfasında,** özel bulutunuz için Sanal ağınızı kurmak üzere **Oluştur** ' u seçin.

1. **Sanal ağ oluştur** sayfasında, sanal ağınızın ayrıntılarını girin.

1. **Temel bilgiler** sekmesinde, sanal ağ için bir ad girin, uygun bölgeyi seçin ve **Ileri: IP adresleri**' ni seçin.

1. **IP adresleri** sekmesinde, **IPv4 adres alanı** altında, önceki öğreticide oluşturduğunuz adres alanını girin.

   > [!IMPORTANT]
   > Önceki öğreticide özel bulutunuzu oluştururken kullandığınız adres alanı **ile örtüşmeyen bir** adres alanı kullanmanız gerekir.

1. **+ Alt ağ** Ekle ' yi seçin ve **alt ağ ekle** sayfasında, alt ağa bir ad ve uygun adres aralığı verin. Bittiğinde **Ekle**’yi seçin.

1. **Gözden geçir ve oluştur**’u seçin.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Gözden geçir + oluştur ' u seçin." border="true":::

1. Bilgileri doğrulayıp **Oluştur**' u seçin. Dağıtım tamamlandıktan sonra, Sanal ağınızı kaynak grubunda görürsünüz.

## <a name="create-a-virtual-network-gateway"></a>Sanal ağ geçidi oluşturma

Artık bir sanal ağ oluşturduğunuza göre, bir sanal ağ geçidi oluşturacaksınız.

1. Kaynak grubunuzda, yeni bir kaynak eklemek için **+ Ekle** ' yi seçin.

1. **Market** metin kutusunda, **sanal ağ geçidi** yazın. Sanal ağ kaynağını bulup seçin.

1. **Sanal ağ geçidi** sayfasında **Oluştur**' u seçin.

1. **Sanal ağ geçidi oluştur** sayfasının temel bilgiler sekmesinde, alanlar için değerler girin ve ardından **gözden geçir + oluştur**' u seçin. 

   | Alan | Değer |
   | --- | --- |
   | **Abonelik** | Kaynak grubunun ait olduğu abonelikle önceden doldurulmuş değer. |
   | **Kaynak grubu** | Geçerli kaynak grubu için önceden doldurulmuş değer. Değer, önceki bir testte oluşturduğunuz kaynak grubu olmalıdır. |
   | **Ad** | Sanal ağ geçidi için benzersiz bir ad girin. |
   | **Bölge** | Sanal ağ geçidinin coğrafi konumunu seçin. |
   | **Ağ geçidi türü** | **ExpressRoute**' ı seçin. |
   | **SKU** | Varsayılan değeri bırakın: **Standart**. |
   | **Sanal ağ** | Daha önce oluşturduğunuz sanal ağı seçin. Sanal ağı görmüyorsanız, ağ geçidinin bölgesinin sanal ağınızın bölgesiyle eşleştiğinden emin olun. |
   | **Ağ geçidi alt ağ adres aralığı** | Bu değer, sanal ağı seçtiğinizde doldurulur. Varsayılan değeri değiştirmeyin. |
   | **Genel IP adresi** | **Yeni oluştur**’u seçin. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Alanlar için değerler sağlayın ve ardından gözden geçir + oluştur ' u seçin." border="true":::

1. Ayrıntıların doğru olduğundan emin olun ve sanal ağ geçidinizin dağıtımını başlatmak için **Oluştur** ' u seçin. 
1. Dağıtım tamamlandıktan sonra, ExpressRoute bağlantınızı Azure VMware Çözüm özel bulutunuzu içeren sanal ağ geçidine bağlamak için sonraki bölüme geçin.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>ExpressRoute 'ı sanal ağ geçidine bağlama

Artık bir sanal ağ geçidi dağıttığınıza göre, Azure VMware çözümünüz özel bulutunuz arasında bir bağlantı ekleyeceksiniz.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>VCenter ve NSX Manager için URL 'Leri bulma

VCenter ve NSX Manager 'da oturum açmak için vCenter web istemcisinin ve NSX-T Manager sitesinin URL 'Lerine ihtiyacınız vardır. 

Azure VMware çözümünüz özel buluta gidin, **Yönet** bölümünde **kimlik**' i seçin, burada gereken bilgileri bulabilirsiniz.

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="VCenter ve NSX-T kimlik bilgileri ve Web istemcisi URL 'Lerinin ekran görüntüsü." border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Sanal ağ oluşturma
> * Sanal ağ geçidi oluşturma
> * ExpressRoute devrenizi ağ geçidine bağlama
> * VCenter ve NSX Manager için URL 'Leri bulma

VCenter 'daki VM 'Ler için kullanılan NSX-T ağ kesimlerini oluşturma hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [NSX-T ağ segmenti oluşturma](tutorial-nsx-t-network-segment.md)