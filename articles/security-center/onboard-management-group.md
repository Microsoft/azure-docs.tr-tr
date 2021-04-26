---
title: Azure Güvenlik Merkezi 'ne bir yönetim grubu ekleme
description: Bir yönetim grubundaki tüm abonelikler için Azure Güvenlik Merkezi 'ni etkinleştirmek üzere sağlanan bir Azure Ilke tanımını nasıl kullanacağınızı öğrenin.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.author: memildin
ms.openlocfilehash: 01036343e2585adf7c09ad3f0d236948a537fc29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016568"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>Yönetim grubundaki tüm aboneliklerde güvenlik merkezini etkinleştir

Azure Güvenlik Merkezi 'ni aynı yönetim grubu (MG) içindeki tüm Azure aboneliklerinde etkinleştirmek için Azure Ilkesi 'ni kullanabilirsiniz. Bu, portaldan ayrı ayrı erişmekle daha uygundur ve abonelikler farklı sahiplere ait olsa bile işe yarar. 

Bir yönetim grubunu ve tüm aboneliklerini eklemek için:

1. **Güvenlik Yöneticisi** izinlerine sahip bir kullanıcı olarak Azure ilkesini açın ve **aboneliğinizde Azure Güvenlik Merkezi 'ni etkinleştirin**' i arayın.

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="Azure Ilke tanımı aboneliğinizde Azure Güvenlik Merkezi 'ni etkinleştirin":::

1. **Ata** ' yı seçin ve kapsamı mg düzeyine ayarlamış olduğunuzdan emin olun.

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="Tanımı atama aboneliğinizde Azure Güvenlik Merkezi 'ni etkinleştirme":::

    > [!TIP]
    > Kapsam dışında, gerekli parametreler yoktur.

1. Güvenlik Merkezi 'nin etkinleştirildiği tüm mevcut aboneliklerinizin eklendi almasını sağlamak için **Düzeltme görevi oluştur** ' u seçin.

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="Azure Ilke tanımı için bir düzeltme görevi oluşturma aboneliğinizde Azure Güvenlik Merkezi 'ni etkinleştirin":::

1. Tanım atandığında şunları yapmanız gerekir:

    1. Yalnızca güvenlik merkezi 'ne kayıtlı olmayan MG içindeki tüm abonelikleri algıla.
    1. Bu abonelikleri "uyumlu değil" olarak işaretleyin.
    1. Tüm kayıtlı abonelikler (Azure Defender 'ın açık veya kapalı olmasından bağımsız olarak) "uyumlu" olarak işaretleyin.

    Ardından düzeltme görevi, uyumlu olmayan aboneliklerde güvenlik merkezini ücretsiz olarak etkinleştirir.

> [!IMPORTANT]
> İlke tanımı yalnızca **mevcut** aboneliklerde güvenlik merkezini etkinleştirir. Yeni oluşturulan abonelikleri kaydetmek için, Uyumluluk sekmesini açın, ilgili uyumlu olmayan abonelikleri seçin ve bir düzeltme görevi oluşturun. Güvenlik Merkezi ile izlemek istediğiniz bir veya daha fazla yeni aboneliğiniz varsa bu adımı yineleyin.

## <a name="optional-modifications"></a>İsteğe bağlı değişiklikler

Azure Ilke tanımını değiştirmek için seçebileceğiniz çeşitli yollar vardır: 

- **Uyumluluğu farklı tanımlama** -sağlanan ilke, kayıt öncesi olarak güvenlik merkezi 'ne kayıtlı olmayan tüm abonelikleri "uyumsuz" olarak sınıflandırır. Azure Defender olmadan tüm abonelikler için ayarlamayı tercih edebilirsiniz.

    Sağlanan tanım, aşağıdaki ' fiyatlandırma ' *ayarlarından birini uyumlu olarak tanımlar.* ' Standard ' veya ' Free ' olarak ayarlanan bir aboneliğin uyumlu olduğu anlamına gelir.

    > [!TIP]
    > Bir Azure Defender planı etkinleştirildiğinde, bu, ' standart ' ayarında olduğu gibi bir ilke tanımında açıklanmaktadır. Devre dışı bırakıldığında, ' ücretsiz ' olur. Bu planlar arasındaki farklar hakkında bilgi edinmek için bkz. [Security Center Ücretsiz vs Azure Defender etkin](security-center-pricing.md). 

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    Bunu aşağıdaki şekilde değiştirirseniz, yalnızca ' standart ' olarak ayarlanan abonelikler uyumlu olarak sınıflandırılacaktır:

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Güvenlik Merkezi 'ni etkinleştirirken uygulanacak bazı Azure Defender planlarını tanımlayın** -sağlanan ilke, Isteğe bağlı Azure Defender planlarını olmadan güvenlik merkezini etkinleştirir. Bunlardan birini veya birkaçını etkinleştirmeyi tercih edebilirsiniz.

    Sağlanan tanımın `deployment` bölümü bir parametreye sahip `pricingTier` . Varsayılan olarak, bu olarak ayarlanır `free` , ancak bunu değiştirebilirsiniz. 


## <a name="next-steps"></a>Sonraki adımlar:

Artık bir yönetim grubunun tamamını eklendi, Azure Defender 'ın gelişmiş korumalarını etkinleştirin. 

> [!div class="nextstepaction"]
> [Azure Defender’ı etkinleştirme](enable-azure-defender.md)