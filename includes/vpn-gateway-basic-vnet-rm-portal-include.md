---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/20/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a66596ecb926c1bf3c6b61cc99e1eb1b56e99158
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92328459"
---
1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Arama kaynakları, hizmet ve belgeler (G +/)**' de *sanal ağ* yazın.

   ![Sanal ağ kaynağını bul sayfası](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Sanal ağ kaynağını bul sayfası")
1. **Market** sonuçlarından **sanal ağ** ' ı seçin.

   ![Sanal ağ seçin](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Sanal ağ kaynağını bul sayfası")
1. **Sanal ağ** sayfasında **Oluştur**' u seçin.

   ![sanal ağ sayfası](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Oluştur’u seçin")
1. **Oluştur**' u seçtiğinizde, **sanal ağ oluştur** sayfası açılır.
1. **Temel bilgiler** sekmesinde, **proje ayrıntıları** ve **örnek ayrıntıları** VNET ayarları ' nı yapılandırın.

   ![Temel bilgiler sekmesi](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Temel bilgiler sekmesi") Alanları doldururken, alana girdiğiniz karakterler doğrulandığında yeşil bir onay işareti görürsünüz. Bazı değerler, kendi değerlerinizle değiştirmek için tekrar doldurulur.

   - **Abonelik**: Listelenen aboneliğin doğru olduğunu onaylayın. Açılan listeyi kullanarak abonelikleri değiştirebilirsiniz.
   - **Kaynak grubu**: var olan bir kaynak grubunu seçin veya yeni bir tane oluşturmak Için **Yeni oluştur** ' a tıklayın. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure Resource Manager'a genel bakış](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Ad**: Sanal ağınızın adını girin.
   - **Bölge**: VNET 'iniz için konumu seçin. Konum, bu sanal ağa dağıttığınız kaynakların canlı olacağını belirler.

1. **IP adresleri** sekmesinde değerlerini yapılandırın. Aşağıdaki örneklerde gösterilen değerler tanıtım amaçlıdır. Bu değerleri, ihtiyacınız olan ayarlara göre ayarlayın.

   ![IP adresleri sekmesi](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "IP adresleri sekmesi")  
   - **IPv4 adres alanı**: varsayılan olarak, bir adres alanı otomatik olarak oluşturulur. Adres alanına tıklayarak kendi değerlerinizi yansıtacak şekilde ayarlayabilirsiniz. Ayrıca, ek adres alanları da ekleyebilirsiniz.
   - **Alt ağ**: varsayılan adres alanını kullanırsanız otomatik olarak varsayılan bir alt ağ oluşturulur. Adres alanını değiştirirseniz, bir alt ağ eklemeniz gerekir. **Alt ağ ekle** penceresini açmak için **+ alt ağ ekle** ' yi seçin. Aşağıdaki ayarları yapılandırın ve ardından **Ekle** ' yi seçerek değerleri ekleyin:
      - **Alt ağ adı**: Bu örnekte "ön uç" alt ağını adlandırdık.
      - **Alt ağ adres aralığı**: Bu alt ağın adres aralığı.

1. **Güvenlik** sekmesinde, şu anda varsayılan değerleri bırakın:

   - **DDoS koruması**: temel
   - **Güvenlik duvarı**: devre dışı
1. Sanal ağ ayarlarını doğrulamak için **gözden geçir + oluştur** ' u seçin.
1. Ayarlar doğrulandıktan sonra **Oluştur**' u seçin.
