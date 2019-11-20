---
title: CloudSimple tarafından Azure VMware çözümü-CloudSimple hizmeti oluşturma
description: Azure portal CloudSimple hizmetinin nasıl oluşturulacağını açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893935"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>CloudSimple hizmeti tarafından Azure VMware çözümünü oluşturma

CloudSimple ile Azure VMware çözümünü kullanmaya başlamak için Azure portal CloudSimple Service tarafından Azure VMware çözümünü oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

Ağ geçidi alt ağı için bir/28 CıDR bloğu ayırın. CloudSimple hizmeti başına bir ağ geçidi alt ağı gerekir ve oluşturulduğu bölge için benzersizdir. Ağ geçidi alt ağı, uç ağ hizmetleri için kullanılır ve/28 CıDR bloğu gerektirir. Ağ geçidi alt ağ adresi alanı benzersiz olmalıdır. CloudSimple ortamıyla iletişim kuran herhangi bir ağla çakışmamalıdır. CloudSimple ile iletişim kuran ağlarda şirket içi ağlar ve Azure sanal ağları bulunur.

## <a name="sign-in-to-azure"></a>Azure'da oturum açın

[Azure Portal](https://portal.azure.com)’ında oturum açın.

## <a name="create-the-service"></a>Hizmeti oluşturma

1. **Tüm Hizmetler**’i seçin.
2. **Cloudsimple Hizmetleri**için arama yapın.
    CloudSimple hizmetinde arama ![](media/create-cloudsimple-service-search.png)
3. **Cloudsimple Hizmetleri**' ni seçin.
4. Yeni bir hizmet oluşturmak için **Ekle** ' ye tıklayın.
    CloudSimple Service](media/create-cloudsimple-service-add.png) ![ekleyin
5. CloudSimple hizmetini oluşturmak istediğiniz aboneliği seçin.
6. Hizmet için kaynak grubunu seçin. Yeni bir kaynak grubu eklemek için **Yeni oluştur**' a tıklayın.
7. Hizmeti tanımlamak için ad girin.
8. Hizmet ağ geçidi için CıDR girin. Şirket içi alt ağlarınızın, Azure alt ağlarının veya planlı CloudSimple alt ağlarının hiçbiriyle çakışmayacak bir/28 alt ağı belirtin. Hizmet oluşturulduktan sonra CıDR 'yi değiştiremezsiniz.

    ![CloudSimple hizmeti oluşturma](media/create-cloudsimple-service.png)
9. **Tamam**’a tıklayın.

Hizmet oluşturulur ve hizmetler listesine eklenir.

## <a name="next-steps"></a>Sonraki adımlar

* [Düğüm sağlamayı](create-nodes.md) öğrenin
* [Özel bulut oluşturmayı](create-private-cloud.md) öğrenin
* [Özel bir bulut ortamını yapılandırmayı](quickstart-create-private-cloud.md) öğrenin
