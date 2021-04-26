---
title: Windows sanal masaüstü (klasik) için hizmet uyarılarını ayarlama-Azure
description: Azure hizmet durumunu Windows sanal masaüstü (klasik) için hizmet bildirimleri alacak şekilde ayarlama.
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: aac22dfb95cc099a5d1243e120834415dfa61a4b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444267"
---
# <a name="tutorial-set-up-service-alerts-for-windows-virtual-desktop-classic"></a>Öğretici: Windows sanal masaüstü için hizmet uyarılarını ayarlama (klasik)

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../set-up-service-alerts.md)bakın.

Windows sanal masaüstü için hizmet sorunlarını ve sistem durumu belgelerini izlemek üzere Azure hizmet durumu ' nu kullanabilirsiniz. Azure hizmet durumu, farklı Uyarı türleri (örneğin, e-posta veya SMS) ile size bildirimde bulunabilir, bir sorunun etkisini anlamanıza yardımcı olur ve sorun çözümlendiği sürece güncelleştirmiş olursunuz. Azure hizmet durumu, kapalı kalma süresini azaltmanıza ve kaynaklarınızın kullanılabilirliğini etkileyebilecek planlı bakım ve değişiklikler için hazırlık yapmanıza da yardımcı olabilir.

Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:

> [!div class="checklist"]
> * Hizmet uyarıları oluşturun ve yapılandırın.

Azure hizmet durumu hakkında daha fazla bilgi edinmek için bkz. [Azure sistem durumu belgeleri](../../service-health/index.yml).

## <a name="prerequisites"></a>Önkoşullar

- [Öğretici: Windows sanal masaüstünde kiracı oluşturma](tenant-setup-azure-active-directory.md)
- [Öğretici: PowerShell ile hizmet sorumluları ve rol atamaları oluşturma](create-service-principal-role-powershell.md)
- [Öğretici: Azure Marketi ile bir konak havuzu oluşturma](create-host-pools-azure-marketplace-2019.md)

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

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **Hizmet durumunu seçin.**
3. Uyarı ve bildirimlerinizi ayarlamak için [hizmet bildirimlerinde etkinlik günlüğü uyarıları oluşturma](../../service-health/alerts-activity-log-service-notifications-portal.md?toc=%2fazure%2fservice-health%2ftoc.json) ' daki yönergeleri kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Windows sanal masaüstü için hizmet sorunlarını ve sistem durumu belgelerini izlemek üzere Azure hizmet durumunu ayarlamayı ve kullanmayı öğrendiniz. Windows sanal masaüstü 'nde oturum açma hakkında bilgi edinmek için Windows sanal masaüstü nasıl yapılır-TOS ' a bağlanma konusuna geçin.

> [!div class="nextstepaction"]
> [Windows 7 ve Windows 10 ' da uzak masaüstü istemcisine bağlanma](connect-windows-7-10-2019.md)