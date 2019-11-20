---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a601b0c40f84832101e97a7abf7dd7418a0a5c69
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673564"
---
Aşağıdaki adımları izleyerek, Resource Manager dağıtım modelini ve Azure portalı ile bir sanal ağ oluşturabilirsiniz. Sanal ağlar hakkında daha fazla bilgi için bkz. [sanal ağa genel bakış](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Bir şirket içi konuma bağlanması sanal ağ için bu sanal ağ için özellikle kullanabileceğiniz bir IP adresi aralığı ayırma işlemini koordine şirket içi ağ yöneticinizle koordine edin. VPN bağlantısının her iki tarafında bir yinelenen adres aralığı varsa trafik beklenmedik bir şekilde yönlendirir. Ayrıca, bu sanal ağı başka bir sanal ağa bağlamak isterseniz adres alanı diğer sanal ağ ile örtüşemez. Ağ yapılandırmanızı uygun şekilde planlayın.
>
>

1. Oturum [Azure portalında](https://portal.azure.com) seçip **kaynak Oluştur**. **Yeni** sayfası açılır.

2. İçinde **markette Ara** alanına *sanal ağ* seçip **sanal ağ** döndürülen listeden. **Sanal ağ** sayfası açılır.

   ![Sanal Ağ kaynağı sayfasını bulma](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Sanal Ağ kaynağı sayfasını bulma")

3. Gelen **dağıtım modeli seçin** seçin sayfanın alt kısmındaki liste **Resource Manager**ve ardından **Oluştur**. **Sanal ağ oluştur** sayfası açılır.

   ![Sanal ağ oluşturma sayfası](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Sanal ağ oluşturma sayfası")

4. **Sanal ağ oluştur** sayfasında sanal ağ ayarlarını yapılandırın. Alanları doldururken, alana girdiğiniz karakterleri doğrulandığında kırmızı ünlem işareti yeşil onay işareti olur. Bazı değerleri kendi değerlerinizle değiştirebildiğiniz autofilled şunlardır:

   - **Ad**: Sanal ağınızın adını girin.

   - **Adres alanı**: Adres alanını girin. Birden fazla adres alanı eklemek için varsa, ilk adres alanınızı buraya girin. Sanal ağ oluşturduktan sonra daha sonra ek adres alanları ekleyebilirsiniz.

   - **Abonelik**: Listelenen aboneliğin doğru olduğunu doğrulayın. Açılan listeyi kullanarak abonelikleri değiştirebilirsiniz.

   - **Kaynak grubu**: Mevcut bir kaynak grubunu seçin veya yeni kaynak grubunuz için bir ad girerek yeni bir tane oluşturun. Yeni bir grup oluşturuyorsanız, planlanan yapılandırma değerlerinize göre kaynak grubunu adlandırın. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure Resource Manager’a genel bakış](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Konum**: Ağınızın konumunu seçin. Bu sanal ağa dağıttığınız kaynakların nerede yaşayacaksa konumunu belirler.

   - **Alt ağ**: Alt ağ Ekle **adı** ve alt ağ **adres aralığı**. Sanal ağ oluşturduktan sonra daha sonra ek alt ağlar ekleyebilirsiniz. 
     
5. **Oluştur**’u seçin.
