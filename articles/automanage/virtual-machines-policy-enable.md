---
title: Azure Ilkesi aracılığıyla sanal makineler için oto yönetimini etkinleştir
description: Azure portal yerleşik bir Azure Ilkesi aracılığıyla VM 'Ler için Azure oto yönetimi 'ni nasıl etkinleştirebileceğinizi öğrenin.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8846efa3619cec383809cdbd6efe70e3622fa007
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365204"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Azure Ilkesi aracılığıyla sanal makineler için oto yönetimini etkinleştir

Çok sayıda VM için bir oto yönetimi etkinleştirmek istiyorsanız, bunu yerleşik bir [Azure ilkesi](..\governance\azure-management.md)kullanarak yapabilirsiniz. Bu makale, doğru ilkeyi bulmada ve Azure portal ' de oto yönetimi etkinleştirmek üzere nasıl atayacağınızı size kılavuzluk eder.


## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

> [!NOTE]
> Ücretsiz deneme hesaplarının bu öğreticide kullanılan sanal makinelere erişimi yoktur. Lütfen Kullandıkça Öde aboneliğine yükseltin.

> [!IMPORTANT]
> Aşağıdaki Azure RBAC izni, **Kullanıcı erişimi yönetici** rolleriyle birlikte, oto yönetimi: **Owner** rolü veya **katkıda bulunan** ' i etkinleştirmek için gereklidir.

## <a name="direct-link-to-policy"></a>Ilkeye doğrudan bağlantı
Azure portal, tekrar Yönet ilke tanımı, [sanal makinelerin yapılandırma adı Ile Azure tarafından eklendi Için yapılandırılacak şekilde](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3)bulunabilir. Bu bağlantıya tıklarsanız, aşağıdaki [Ilkeyi bulup atamak](#locate-and-assign-the-policy) için doğrudan 8. adıma atlayın.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com/) oturum açın.


## <a name="locate-and-assign-the-policy"></a>İlkeyi bulun ve atayın

1. Azure portal **ilkeye** git
1. **Tanımlar** bölmesine git
1. Kullanılabilir seçenekleri görmek için **Kategoriler** açılan listesine tıklayın
1. **Automanage 'ı etkinleştir – Azure sanal makinesi en iyi yöntemler** seçeneğini belirleyin
1. Artık liste, bir *ada sahip yerleşik* bir ilkeyi görüntülemek için güncelleştirilecek,
1. *Oto yönetimi etkinleştir-Azure sanal makine en iyi uygulamaları* yerleşik ilke adı ' na tıklayın.
1. İlkeye tıkladıktan sonra, **tanım** sekmesini artık görebilirsiniz

    > [!NOTE]
    > Azure Ilke tanımı, yapılandırma profili veya hesabı gibi oto yönetimi parametrelerini ayarlamak için kullanılır. Ayrıca, ilkenin yalnızca doğru VM 'Ler için geçerli olmasını sağlayan filtreler de ayarlar.

1. Atama oluşturmak için **ata** düğmesine tıklayın
1. **Temel bilgiler** sekmesinde, *aboneliği* ve *kaynak grubunu* ayarlayarak **kapsamı** doldurun

    > [!NOTE]
    > Kapsam, bu ilkenin hangi VM 'Ler için geçerli olduğunu tanımlamanızı sağlar. Uygulamayı, abonelik düzeyinde veya kaynak grubu düzeyinde ayarlayabilirsiniz. Bir kaynak grubu ayarlarsanız, o anda o kaynak grubundaki veya buna eklediğimiz tüm VM 'lerde otomatik yönetilecek otomatik olarak etkin olur.

1. **Parametreler** sekmesine tıklayın ve **oto Yönet hesabını** ve istenen **Yapılandırma profilini** ayarlayın
1. **Gözden geçir + oluştur** sekmelerinde ayarları gözden geçirin
1. **Oluştur** 'A tıklayarak atamayı uygulayın
1. **Atamalar sekmesinde,** **tanım** ' ın yanındaki atamalarınızı görüntüleyin

> [!NOTE]
> Bu ilkenin kaynak grubunda veya abonelikte bulunan VM 'Ler üzerinde etkili bir şekilde başlaması biraz zaman alır.


## <a name="next-steps"></a>Sonraki adımlar

Azure portal aracılığıyla sanal makineler için Azure oto yönetimi 'ni etkinleştirmenin başka bir yolunu öğrenin.

> [!div class="nextstepaction"]
> [Azure portal sanal makineler için oto yönetimini etkinleştir](quick-create-virtual-machines-portal.md)