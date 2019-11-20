---
title: Azure Güvenlik Merkezi 'nde özel güvenlik ilkeleri oluşturma | Microsoft Docs
description: Azure Güvenlik Merkezi tarafından izlenen Azure özel ilke tanımları.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d5432c794c26e350b23fe47aa1574422143ca4b2
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521962"
---
# <a name="using-custom-security-policies-preview"></a>Özel güvenlik ilkeleri kullanma (Önizleme)

Azure Güvenlik Merkezi, sistem ve ortamınızın güvenliğini sağlamaya yardımcı olmak için güvenlik önerileri oluşturur. Bu öneriler, tüm müşterilere sağlanan genel, varsayılan güvenlik ilkesine dahil olan sektör en iyi uygulamalarına dayalıdır. Ayrıca, güvenlik merkezi 'nin sektör ve mevzuat standartları hakkında bilgi edinmeleri de gelebilir.

Bu önizleme özelliği ile kendi *özel* girişimlerinizi ekleyebilirsiniz. Daha sonra, makineleriniz oluşturduğunuz ilkeleri izleyememesi durumunda öneriler alacaksınız.

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Aboneliğinize özel bir girişim eklemek için 

1. Güvenlik Merkezi 'nin kenar çubuğundan **güvenlik ilkesi** sayfasını açın.

1. Özel bir girişim eklemek istediğiniz bir abonelik veya yönetim grubu seçin.

    [Özel ilkenizi oluşturacağınız bir abonelik ![seçin](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Özel bir standart eklediğinizde, bu kapsama bir *girişim* atar. Bu nedenle, bu atama için gereken en geniş kapsamı seçmenizi öneririz. 

1. Güvenlik İlkesi sayfasında, özel girişimlerinizin (Önizleme) altında **özel girişim Ekle**' ye tıklayın.

    [![* * özel girişim Ekle * * öğesine tıklayın.](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Aşağıdaki sayfa görüntülenir:

    ![İlke oluşturma veya ekleme](media/custom-security-policies/create-or-add-custom-policy.png)

1. Özel girişimler Ekle sayfasında, kuruluşunuzda önceden oluşturulmuş özel ilkelerin listesini gözden geçirin. Aboneliğinize atamak istediğiniz bir tane görürseniz **Ekle**' ye tıklayın. Listede gereksinimlerinizi karşılayan bir girişim yoksa, bu adımı atlayın.

1. Yeni bir özel girişim oluşturmak için:

    1. **Yeni oluştur**' a tıklayın.
    1. Tanımın konumunu ve adını girin.
    1. Dahil edilecek ilkeleri seçin ve **Ekle**' ye tıklayın.
    1. İstediğiniz parametreleri girin.
    1. **Kaydet** düğmesine tıklayın.
    1. Özel girişimler Ekle sayfasında Yenile ' ye tıklayın ve yeni girişim kullanılabilir olarak gösterilir.
    1. **Ekle** ' ye tıklayın ve aboneliğinize atayın.

    > [!NOTE]
    > Yeni girişimler oluşturmak için abonelik sahibi kimlik bilgileri gerekir. Azure rolleri hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'Nde izinler](security-center-permissions.md).

1. İlkenize yönelik sonuç önerilerini görmek için kenar çubuğundan **öneriler** ' e tıklayarak öneriler sayfasını açın. Öneriler "özel" bir etiketle birlikte görüntülenir ve 30 dakikaya kadar kullanılabilir.

    [![özel öneriler](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, özel güvenlik ilkeleri oluşturmayı öğrendiniz. 

Diğer ilgili malzemeler için aşağıdaki makalelere bakın: 

- [Güvenlik ilkelerine genel bakış](tutorial-security-policy.md)
- [Yerleşik güvenlik ilkelerinin listesi](security-center-policy-definitions.md)