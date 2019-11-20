---
title: Windows sanal masaüstü için hizmet uyarılarını ayarlama-Azure
description: Azure hizmet durumunu Windows sanal masaüstü için hizmet bildirimleri alacak şekilde ayarlama.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
ms.openlocfilehash: b5fa5fc13c8ce0c98c04d2451f3a50e89b986c16
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676441"
---
# <a name="tutorial-set-up-service-alerts"></a>Öğretici: hizmet uyarılarını ayarlama

Windows sanal masaüstü için hizmet sorunlarını ve sistem durumu belgelerini izlemek üzere Azure hizmet durumu ' nu kullanabilirsiniz. Azure hizmet durumu, farklı Uyarı türleri (örneğin, e-posta veya SMS) ile size bildirimde bulunabilir, bir sorunun etkisini anlamanıza yardımcı olur ve sorun çözümlendiği sürece güncelleştirmiş olursunuz. Azure hizmet durumu, kapalı kalma süresini azaltmanıza ve kaynaklarınızın kullanılabilirliğini etkileyebilecek planlı bakım ve değişiklikler için hazırlık yapmanıza da yardımcı olabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Hizmet uyarıları oluşturun ve yapılandırın.

Azure hizmet durumu hakkında daha fazla bilgi edinmek için bkz. [Azure sistem durumu belgeleri](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Önkoşullar

- [Öğretici: Windows sanal masaüstünde kiracı oluşturma](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)
- [Öğretici: PowerShell ile hizmet sorumluları ve rol atamaları oluşturma](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)
- [Öğretici: Azure Marketi ile bir konak havuzu oluşturma](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)

## <a name="create-service-alerts"></a>Hizmet uyarıları oluşturma

Bu bölümde, Azure hizmet durumunu yapılandırma ve Azure portal erişebileceğiniz bildirimlerin nasıl ayarlanacağı gösterilmektedir. Farklı Uyarı türleri ayarlayabilir ve bunları zamanında bilgilendirecek şekilde zamanlayabilirsiniz.

### <a name="recommended-service-alerts"></a>Önerilen hizmet uyarıları

Aşağıdaki sistem durumu olay türleri için hizmet uyarıları oluşturmanızı öneririz:

- **Hizmet sorunu:** Hizmet ile kullanıcılarınızın bağlantısını etkileyen veya Windows sanal masaüstü kiracınızı yönetme özelliği ile ilgili önemli sorunlar hakkında bildirim alın.
- **Durum danışmanlık:** Dikkat etmeniz gereken bildirimleri alın. Aşağıda bu tür bir bildirime örnek verilmiştir:
    - Sanal makineler (VM) açık bağlantı noktası 3389 olarak güvenli yapılandırılmamış
    - İşlevin kullanımdan kaldırılması

### <a name="configure-service-alerts"></a>Hizmet uyarılarını yapılandırma

Hizmet uyarılarını yapılandırmak için:

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.
2. **Hizmet durumunu seçin.**
3. Uyarı ve bildirimlerinizi ayarlamak için [hizmet bildirimlerinde etkinlik günlüğü uyarıları oluşturma](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) ' daki yönergeleri kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Windows sanal masaüstü için hizmet sorunlarını ve sistem durumu belgelerini izlemek üzere Azure hizmet durumunu ayarlamayı ve kullanmayı öğrendiniz. Windows sanal masaüstü 'nde oturum açma hakkında bilgi edinmek için Windows sanal masaüstü nasıl yapılır-TOS ' a bağlanma konusuna geçin.

> [!div class="nextstepaction"]
> [Windows 7 ve Windows 10 ' da uzak masaüstü istemcisine bağlanma](./connect-windows-7-and-10.md)
