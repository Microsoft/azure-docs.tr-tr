---
title: Onaylanan bir tabandan Azure sanal makine teklifi (VM) oluşturma, Azure Marketi
description: Onaylanan bir tabandan bir sanal makine (VM) teklifi oluşturmayı öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 19ae4b929964aaeb971bef75a2a620e40e4667f5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727177"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Onaylanan bir temel kullanılarak sanal makine oluşturma

Bu makalede, önceden yapılandırılmış, onaylı bir işletim sistemi içeren bir sanal makine (VM) oluşturmak için Azure 'un nasıl kullanılacağı açıklanır. Çözümünüz ile uyumlu değilse, onaylanmış bir işletim sistemi kullanarak [Şirket içi BIR VM oluşturmak ve yapılandırmak](azure-vm-create-using-own-image.md) mümkündür.

> [!NOTE]
> Bu yordama başlamadan önce, sanal sabit disk (VHD) gereksinimleri de dahil olmak üzere Azure VM teklifleri için [teknik gereksinimleri](marketplace-virtual-machines.md#technical-requirements) gözden geçirin.

## <a name="select-an-approved-base-image"></a>Onaylanan bir temel görüntü seçin

Tabanınız olarak aşağıdaki Windows veya Linux görüntülerinden birini seçin.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Azure, onaylı bir Linux dağıtımları yelpazesi sunar. Geçerli bir liste için bkz. [Azure tarafından onaylanan dağıtımlara Linux](../virtual-machines/linux/endorsed-distros.md).

## <a name="create-vm-on-the-azure-portal"></a>Azure portal VM oluşturma

1. [Azure portalında](https://ms.portal.azure.com/) oturum açın.
2. **Sanal makineler**'i seçin.
3. **+ Ekle** ' yi seçerek **sanal makine oluştur** ekranını açın.
4. Açılan listeden görüntüyü seçin veya tüm kullanılabilir sanal makine görüntülerini aramak veya taramak için **tüm ortak ve özel görüntülere gözatıp '** yi seçin.
5. **Gen 2** VM oluşturmak için **Gelişmiş** sekmesine gidin ve **Gen 2** seçeneğini belirleyin.

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Gen 1 veya Gen 2 ' yi seçin.":::

6. Dağıtılacak VM 'nin boyutunu seçin.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Seçilen görüntü için önerilen bir VM boyutu seçin.":::

7. VM oluşturmak için gereken diğer ayrıntıları sağlayın.
8. Seçimlerinizi gözden geçirmek için **gözden geçir + oluştur** ' u seçin. **Doğrulama başarılı** Iletisi göründüğünde **Oluştur**' u seçin.

Azure, belirttiğiniz sanal makineyi sağlamaya başlar. Sol menüdeki **sanal makineler** sekmesini seçerek ilerleme durumunu izleyin. Oluşturulduktan sonra, sanal makinenin durumu **çalışıyor** olarak değişir.

## <a name="configure-the-vm"></a>VM'yi yapılandırma

Bu bölümde bir Azure VM 'nin nasıl boyutlandırılacağını, güncelleştirileceğini ve genelleştirirsiniz. Bu adımlar, VM 'nizi Azure Marketi 'nde dağıtılacak şekilde hazırlamak için gereklidir.

### <a name="connect-to-your-vm"></a>Sanal makinenize bağlanma

[Windows](../virtual-machines/windows/connect-logon.md) veya [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM 'nize bağlanmak için aşağıdaki belgelere bakın.

### <a name="install-the-most-current-updates"></a>En güncel güncelleştirmeleri yükler

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Ek güvenlik denetimleri gerçekleştirme

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Özel yapılandırma ve zamanlanmış görevler gerçekleştirme

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Sonraki adımlar

- Önerilen sonraki adım: Azure Marketi yayımlama gereksinimlerini karşıladığından emin olmak için [VM görüntünüzü test edin](azure-vm-image-test.md) . Bu isteğe bağlıdır.
- VM görüntünüzü test etmek istemiyorsanız, görüntünüzü yayımlamak için [Iş Ortağı Merkezi](https://partner.microsoft.com/) ' nde oturum açın.
- Yeni Azure tabanlı VHD 'nizi oluştururken zorluk yaşıyorsanız bkz. [Azure Market Için VM SSS](azure-vm-create-faq.md).
