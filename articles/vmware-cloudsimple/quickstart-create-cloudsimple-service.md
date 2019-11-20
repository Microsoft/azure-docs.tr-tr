---
title: CloudSimple hızlı başlangıç-Azure VMware çözümü-hizmet oluşturma
description: CloudSimple hizmeti, satın alma düğümleri ve ayrılan düğümleri oluşturma hakkında bilgi edinin
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 228ca0f43322c271b1a6db55a37c852ca1f66799
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828897"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Hızlı başlangıç-CloudSimple hizmeti tarafından Azure VMware çözümü oluşturma

Başlamak için Azure portal Azure VMware çözümünü CloudSimple ile oluşturun.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>CloudSimple-Service 'e göre VMware çözümü

CloudSimple hizmeti, CloudSimple ile Azure VMware çözümünü kullanmanıza olanak sağlar.  Hizmetin oluşturulması, düğüm sağlamanıza, düğümleri ayırmanızı ve özel bulutlar oluşturmanıza olanak sağlar.  Cloudsimple hizmetini, CloudSimple hizmetinin kullanılabildiği her bir Azure bölgesine eklersiniz.  Hizmet, CloudSimple tarafından Azure VMware çözümünün Edge ağını tanımlar.  Bu Edge ağı VPN, ExpressRoute ve özel bulutlarınıza Internet bağlantısı içeren hizmetler için kullanılır.

CloudSimple hizmetini eklemek için bir ağ geçidi alt ağı oluşturmanız gerekir. Ağ geçidi alt ağı, Edge ağı oluşturulurken kullanılır ve bir/28 CıDR bloğu gerektirir. Ağ geçidi alt ağ adresi alanı benzersiz olmalıdır. Şirket içi ağ adresi alanları veya Azure sanal ağ adres alanı ile çakışamaz.

## <a name="before-you-begin"></a>Başlamadan önce

Ağ geçidi alt ağı için bir/28 CıDR bloğu ayırın.  CloudSimple hizmeti başına bir ağ geçidi alt ağı gerekir ve oluşturulduğu bölge için benzersizdir. Ağ geçidi alt ağı, CloudSimple Edge Network Services tarafından Azure VMware çözümü için kullanılır ve bir/28 CıDR bloğu gerektirir. Ağ geçidi alt ağ adresi alanı benzersiz olmalıdır. CloudSimple ortamıyla iletişim kuran herhangi bir ağla çakışmamalıdır.  CloudSimple ile iletişim kuran ağlarda şirket içi ağlar ve Azure sanal ağları bulunur.

[Ağ önkoşullarını](cloudsimple-network-checklist.md)gözden geçirin. 

## <a name="sign-in-to-azure"></a>Azure'da oturum açın

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="create-the-service"></a>Hizmeti oluşturma

1. **Tüm Hizmetler**’i seçin.
2. **Cloudsimple hizmeti**için arama yapın.

    ![CloudSimple hizmeti ara](media/create-cloudsimple-service-search.png)

3. **Cloudsimple Hizmetleri**' ni seçin.
4. Yeni bir hizmet oluşturmak için **Ekle** ' ye tıklayın.

    ![CloudSimple hizmeti Ekle](media/create-cloudsimple-service-add.png)

5. CloudSimple hizmetini oluşturmak istediğiniz aboneliği seçin.
6. Hizmet için kaynak grubunu seçin. Yeni bir kaynak grubu eklemek için **Yeni oluştur**' a tıklayın.
7. Hizmeti tanımlamak için ad girin.
8. Hizmet ağ geçidi için CıDR girin. Şirket içi alt ağlarınızın, Azure alt ağlarının veya planlı CloudSimple alt ağlarının hiçbiriyle çakışmayacak bir/28 alt ağı belirtin. Hizmet oluşturulduktan sonra CıDR 'yi değiştiremezsiniz.

    ![CloudSimple hizmeti oluşturma](media/create-cloudsimple-service.png)

9. **Tamam**’a tıklayın.

Hizmet oluşturulur ve hizmetler listesine eklenir.

## <a name="provision-nodes"></a>Sağlama düğümleri

CloudSimple özel bulut ortamı için Kullandıkça Öde kapasitesini ayarlamak için, önce Azure portal düğümleri sağlayın.

1. **Tüm Hizmetler**’i seçin.
2. **Cloudsimple düğümleri**için arama yapın.

    ![CloudSimple düğümlerinde arama yapın](media/create-cloudsimple-node-search.png)

3. **Cloudsimple düğümlerini**seçin.
4. Düğüm oluşturmak için **Ekle** ' ye tıklayın.

    ![CloudSimple düğümleri ekleme](media/create-cloudsimple-node-add.png)

5. CloudSimple düğümlerini sağlamak istediğiniz aboneliği seçin.
6. Düğümlerin kaynak grubunu seçin. Yeni bir kaynak grubu eklemek için **Yeni oluştur**' a tıklayın.
7. Düğümleri tanımlamak için ön eki girin.
8. Düğüm kaynaklarının konumunu seçin.
9. Düğüm kaynaklarını barındıracak ayrılmış konumu seçin.
10. [Düğüm türünü](cloudsimple-node.md)seçin.
11. Sağlanacak düğüm sayısını seçin.
12. **Gözden geçir + oluştur**' u seçin.
13. Ayarları gözden geçirin. Ayarları değiştirmek için **önceki**'ni tıklatın.
14. **Oluştur**'u seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Özel bulut oluşturma ve ortamı yapılandırma](quickstart-create-private-cloud.md)
* [Cloudsimple hizmeti](https://docs.azure.cloudsimple.com/cloudsimple-service) hakkında daha fazla bilgi edinin
