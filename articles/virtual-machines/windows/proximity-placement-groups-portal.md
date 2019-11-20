---
title: Portalı kullanarak bir yakınlık yerleşimi grubu oluşturma
description: Azure portal kullanarak yakınlık yerleşimi grubu oluşturmayı öğrenin.
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180257"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Portalı kullanarak bir yakınlık yerleşimi grubu oluşturma

Olası en düşük gecikme süresini elde etmek üzere VM 'Leri olabildiğince yakın bir şekilde almak için, bunları bir [yakınlık yerleşimi grubu](co-location.md#proximity-placement-groups)içinde dağıtmanız gerekir.

Yakınlık yerleşimi grubu, Azure işlem kaynaklarının fiziksel olarak birbirlerine yakın bir yerde bulunduğundan emin olmak için kullanılan mantıksal bir gruplandırmadır. Yakınlık yerleşimi grupları, düşük gecikme süresinin gereksinim olduğu iş yükleri için faydalıdır.


## <a name="create-the-proximity-placement-group"></a>Yakınlık yerleşimi grubunu oluşturma

1. Aramada **yakınlık yerleşimi grubunu** yazın.
1. Arama sonuçlarındaki **Hizmetler** ' ın altında **yakınlık yerleşimi grupları**' nı seçin.
1. **Yakınlık yerleşimi grupları** sayfasında **Ekle**' yi seçin.
1. **Temel bilgiler** sekmesinde, **proje ayrıntıları**' nın altında, doğru aboneliğin seçildiğinden emin olun.
1. **Kaynak grubu** ' nda yeni **Oluştur** ' u seçin veya açılan listeden var olan bir kaynak grubunu seçin.
1. **Bölge** bölümünde, yakınlık yerleşimi grubunun oluşturulmasını istediğiniz konumu seçin.
1. **Yakınlık yerleşimi grup adı** ' na bir ad yazın ve ardından **gözden geçir + oluştur**' u seçin.
1. Doğrulama geçtikten sonra, yakınlık yerleşimi grubunu oluşturmak için **Oluştur** ' u seçin.

    ![Yakınlık yerleşimi grubu oluşturma ekran görüntüsü](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>VM oluşturma

1. Portalda bir VM oluştururken **Gelişmiş** sekmesine gidin. 
1. **Yakınlık yerleşimi grubu** seçiminde doğru yerleştirme grubunu seçin. 

    ![Portalda yeni bir VM oluşturulurken yakınlık yerleşimi grup bölümünün ekran görüntüsü](./media/ppg/vm-ppg.png)

1. Diğer tüm gerekli seçimleri yapmayı tamamladığınızda, **gözden geçir + oluştur**' u seçin.
1. Doğrulamayı geçtikten sonra, sanal makineyi yerleştirme grubuna dağıtmak için **Oluştur** ' u seçin.




## <a name="next-steps"></a>Sonraki adımlar

Yakınlık yerleştirme grupları oluşturmak için [Azure PowerShell](proximity-placement-groups.md) de kullanabilirsiniz.

