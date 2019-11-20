---
title: include dosyası
description: include dosyası
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: ac5599a38c3fcbc4a6c331d2b14c6b6c55c31d82
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73850521"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Azure portal klasik VNet oluşturma
Önceki senaryoya bağlı olarak klasik bir sanal ağ oluşturmak için aşağıdaki adımları izleyin.

1. Tarayıcıdan https://portal.azure.com adresine gidin ve gerekiyorsa Azure hesabınızla oturum açın.
2. **Kaynak oluştur** > **Ağ** > **Sanal Ağ** seçeneğine tıklayın. **Dağıtım modeli Seç** listesinin zaten **Klasik**olduğunu görürsünüz. 3. Aşağıdaki şekilde gösterildiği gibi **Oluştur** ' a tıklayın.
   
    ![Azure portalında VNet oluşturma](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. **Sanal ağ** bölmesine VNET 'in **adını** yazın ve **Adres alanı**' na tıklayın. VNet ve ilk alt ağı için adres alanı ayarlarınızı yapılandırın ve ardından **Tamam**' a tıklayın. Aşağıdaki şekilde, senaryonuza yönelik CıDR blok ayarları gösterilmektedir.
   
    ![Adres alanı bölmesi](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. **Kaynak grubu** ' na tıklayın ve sanal ağı eklemek için bir kaynak grubu seçin veya yeni kaynak grubu **Oluştur** ' a tıklayarak VNET 'i yeni bir kaynak grubuna ekleyin. Aşağıdaki şekilde, **Testrg**adlı yeni bir kaynak grubu için kaynak grubu ayarları gösterilmektedir. Kaynak grupları hakkında daha fazla bilgi için [Azure Resource Manager’a Genel Bakış](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) sayfasını ziyaret edin.
   
    ![Kaynak Grup bölmesi oluştur](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. Gerekiyorsa, VNet’inizle ilgili **Abonelik** ve **Konum** ayarlarını değiştirin. 
7. VNet’i bir kutucuk olarak görmek istemiyorsanız **Başlangıç panosu**’nda **Başlangıç panosuna sabitlemek** öğesini devre dışı bırakın. 
8. **Oluştur** ' a tıklayın ve aşağıdaki şekilde gösterildiği gibi **sanal ağ oluşturma** adlı kutucuğa dikkat edin.
   
    ![Portalda VNet oluşturma](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. VNet 'in oluşturulmasını bekleyin ve kutucuğu gördüğünüzde daha fazla alt ağ eklemek için tıklatın.
   
    ![Portalda VNet oluşturma](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. VNet 'iniz için gösterilen şekilde **yapılandırmayı** görmeniz gerekir. 
   
    ![Portalda VNet oluşturma](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. **Ekle** > **alt ağlar** ' a tıklayın, ardından bir **ad** yazın ve alt ağınız için bir **adres aralığı (CIDR bloğu)** belirtin ve ardından **Tamam**' a tıklayın. Aşağıdaki şekilde, geçerli senaryomızın ayarları gösterilmektedir.
    
    ![Azure portalında VNet oluşturma](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

