---
title: Azure sanal makineler için Azure portal kullanan bakım denetimi
description: Bakım denetimini ve Azure portal kullanarak Azure VM 'lerinize bakım uygulandığını nasıl denetleyeceğinizi öğrenin.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 0dea30723a3ebe1598d6304ac3c98bfe1b55b466
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563022"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>Bakım denetimi ve Azure portal güncelleştirmelerini denetleme

Bakım denetimi, yalıtılmış sanal makinelerinize ve Azure adanmış ana bilgisayarlara güncelleştirmelerin ne zaman uygulanacağına karar vermenizi sağlar. Bu konu, bakım denetimi için Azure portal seçeneklerini içerir. Bakım denetimini, sınırlamalarını ve diğer yönetim seçeneklerini kullanmanın avantajları hakkında daha fazla bilgi için bkz. [Platform güncelleştirmelerini bakım denetimiyle yönetme](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Bakım yapılandırması oluşturun

1. Azure portalında oturum açın.

1. **Bakım yapılandırması** arayın.

   ![Bakım yapılandırmalarının nasıl açılacağını gösteren ekran görüntüsü](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. **Ekle**'ye tıklayın.

   ![Bakım yapılandırmasının nasıl ekleneceğini gösteren ekran görüntüsü](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. Bir abonelik ve kaynak grubu seçin, yapılandırma için bir ad girin ve bir bölge seçin. **İleri**’ye tıklayın.

   ![Bakım yapılandırması temel bilgilerini gösteren ekran görüntüsü](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. Etiket ve değer ekleyin. **İleri**’ye tıklayın.

   ![Bakım yapılandırmasına etiketlerin nasıl ekleneceğini gösteren ekran görüntüsü](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. Özeti gözden geçirin. **Oluştur**’a tıklayın.

   ![Bakım yapılandırması oluşturmayı gösteren ekran görüntüsü](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. Dağıtım tamamlandıktan sonra **Kaynağa Git ' e** tıklayın.

   ![Bakım yapılandırması dağıtımının tamamlandığını gösteren ekran görüntüsü](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>Yapılandırmayı ata

Bakım yapılandırmasının ayrıntılar sayfasında, atamalar ' a ve ardından **kaynak ata**' ya tıklayın. 

![Bir kaynağın nasıl atanacağını gösteren ekran görüntüsü](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Bakım yapılandırmasının atanmasını istediğiniz kaynakları seçin ve **Tamam**' a tıklayın. **Tür** sütunu, kaynağın YALıTıLMıŞ bir VM mi yoksa Azure adanmış ana bilgisayar mı olduğunu gösterir. Yapılandırmayı atamak için VM 'nin çalışıyor olması gerekir. Durdurulmuş bir VM 'ye bir yapılandırma atamayı denerseniz bir hata oluşur. 

<!---Shantanu to add details about the error case--->

![Kaynak seçme şeklini gösteren ekran görüntüsü](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>Yapılandırmayı denetle

Yapılandırmanın doğru şekilde uygulandığını doğrulayabilirsiniz veya **bakım yapılandırmalarını** kullanarak şu anda atanmış olan tüm bakım yapılandırmalarını görmek için denetleyin. **Tür** sütunu, yapılandırmanın YALıTıLMıŞ bir VM 'ye mi yoksa Azure ayrılmış ana bilgisayarına mi atandığını gösterir. 

![Bakım yapılandırmasının nasıl kontrol etileceğini gösteren ekran görüntüsü](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

Ayrıca, belirli bir sanal makinenin yapılandırmasını Özellikler sayfasında da denetleyebilirsiniz. Bu sanal makineye atanan yapılandırmayı görmek için **bakım** ' a tıklayın.

![Bir konak için bakımın nasıl kontrol etileceğini gösteren ekran görüntüsü](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>Bekleyen güncelleştirmeleri denetle

Bakım yapılandırması için güncelleştirmelerin beklenip beklenmediğini denetlemek için iki yol da vardır. **Bakım yapılandırmaları**' nda, yapılandırma ayrıntılarında **atamalar** ' a tıklayın ve **bakım durumunu** denetleyin.

![Bekleyen güncelleştirmelerin nasıl kontrol etileceğini gösteren ekran görüntüsü](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

Ayrıca, belirli bir konağı, **sanal makineleri** veya adanmış konağın özelliklerini kullanarak da denetleyebilirsiniz. 

![Vurgulanan bakım durumunu gösteren ekran görüntüsü.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>Güncelleştirme uygulama

**Sanal makineler** kullanarak istek üzerine bekleyen güncelleştirmeleri uygulayabilirsiniz. VM ayrıntılarında **bakım** ' a tıklayın ve **Bakımı Şimdi Uygula**' ya tıklayın.

![Bekleyen güncelleştirmelerin nasıl uygulanacağını gösteren ekran görüntüsü](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>Güncelleştirmelerin uygulama durumunu denetleyin 

**Bakım yapılandırmalarında** veya **sanal makineler** kullanarak yapılandırma için güncelleştirmelerin ilerlemesini denetleyebilirsiniz. VM ayrıntılarında **bakım**' a tıklayın. Aşağıdaki örnekte, **bakım durumu** bir güncelleştirmenin **bekliyor** olduğunu gösterir.

![Bekleyen güncelleştirmelerin durumunun nasıl kontrol etileceğini gösteren ekran görüntüsü](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>Bakım yapılandırmasını silme

Bir yapılandırmayı silmek için yapılandırma ayrıntılarını açın ve **Sil**' e tıklayın.

![Yapılandırmanın nasıl silineceğini gösteren ekran görüntüsü.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [bakım ve güncelleştirmeler](maintenance-and-updates.md).
