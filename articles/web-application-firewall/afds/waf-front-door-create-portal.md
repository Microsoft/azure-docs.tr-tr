---
title: 'Öğretici: Azure ön kapısı için WAF ilkesi oluşturma-Azure portal'
description: Bu öğreticide, Azure portal kullanarak bir Web uygulaması güvenlik duvarı (WAF) ilkesi oluşturmayı öğreneceksiniz.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: victorh
ms.openlocfilehash: e7b4544530dc9c0c894ae7a0f2b1d2830f895928
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122339"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Öğretici: Azure ön kapıda Azure portal kullanarak Web uygulaması güvenlik duvarı ilkesi oluşturma

Bu öğreticide, temel bir Azure Web uygulaması güvenlik duvarı (WAF) ilkesi oluşturma ve Azure ön kapısının bir ön uç konağına uygulama işlemi gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * WAF ilkesi oluşturma
> * Ön uç konakla ilişkilendirin
> * WAF kurallarını yapılandırma

## <a name="prerequisites"></a>Önkoşullar

[Ön kapı](../../frontdoor/quickstart-create-front-door.md) veya [ön kapı Standart/Premium](../../frontdoor/standard-premium/create-front-door-portal.md) profili oluşturun. 

## <a name="create-a-web-application-firewall-policy"></a>Web uygulaması güvenlik duvarı ilkesi oluşturma

İlk olarak, portalı kullanarak yönetilen varsayılan kural kümesi (DRS) ile temel bir WAF ilkesi oluşturun. 

1. Ekranın sol üst kısmında, **kaynak oluştur** ' u seçin > **WAF** araması yapın > **Web uygulaması güvenlik duvarı (WAF)** seçin > **Oluştur**' u seçin.

1. **BIR WAF Ilkesi oluştur** sayfasının **temel bilgiler** sekmesinde aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | İlke              | **Global WAF (ön kapı)** seçeneğini belirleyin. |
    | Ön kapı SKU 'SU          | Temel, standart ve Premium SKU arasında seçim yapın. |
    | Abonelik            | Ön kapı abonelik adınızı seçin.|
    | Kaynak grubu          | Ön kapı kaynak grubu adınızı seçin.|
    | İlke adı             | WAF ilkeniz için benzersiz bir ad girin.|
    | İlke durumu            | **Etkin** olarak ayarlayın. | 

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="Abonelik, kaynak grubu ve ilke adı için Inceleme + oluştur düğmesi ve liste kutuları içeren bir W A F ilkesi oluşturma sayfasının ekran görüntüsü.":::

1. **BIR WAF Ilkesi oluştur** sayfasının **Ilişkilendirme** sekmesinde, **ön kapı profilini ilişkilendir**' i seçin, aşağıdaki ayarları girin ve ardından **Ekle**' yi seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Ön kapı profili              | Ön kapı profili adınızı seçin. |
    | Etki Alanları          | WAF ilkesini ilişkilendirmek istediğiniz etki alanlarını seçin ve ardından **Ekle**' yi seçin. |

    :::image type="content" source="../media/waf-front-door-create-portal/associate-profile.png" alt-text="Ön kapı profilini ilişkilendir sayfasının ekran görüntüsü.":::
    
    > [!NOTE]
    > Etki alanı bir WAF ilkesiyle ilişkiliyse, gri renkte gösterilir. Önce etki alanını ilişkili ilkeden kaldırmanız ve ardından etki alanını yeni bir WAF ilkesiyle yeniden ilişkilendirmeniz gerekir.

1. **Gözden Geçir + oluştur**’u ve sonra da **Oluştur**’u seçin.

## <a name="configure-web-application-firewall-rules-optional"></a>Web uygulaması güvenlik duvarı kurallarını yapılandırma (isteğe bağlı)

### <a name="change-mode"></a>Modu Değiştir

Bir WAF ilkesi oluşturduğunuzda, varsayılan WAF ilkesi **algılama** modundadır. **Algılama** modunda WAF herhangi bir isteği engellemez, bunun yerine WAF kurallarıyla eşleşen istekler WAF günlüklerinde günlüğe kaydedilir.
WAF 'yi eylemde görmek için, mod ayarlarını **algılamayı** **engelleme** olarak değiştirebilirsiniz. **Önleme** modunda, varsayılan kural KÜMESI (DRS) içinde tanımlanan kurallarla eşleşen istekler engellenir ve WAF günlüklerinde günlüğe kaydedilir.

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="Ilke ayarları bölümünün ekran görüntüsü. Mod değiştirme, önleme olarak ayarlanır.":::

### <a name="custom-rules"></a>Özel kurallar

Özel **kurallar** bölümünün altında **özel kural ekle** seçeneğini belirleyerek özel bir kural oluşturabilirsiniz. Bu, özel kural yapılandırma sayfasını başlatır. 

:::image type="content" source="../media/waf-front-door-create-portal/custom-rules.png" alt-text="Özel kurallar sayfasının ekran görüntüsü.":::

Sorgu dizesi **blok içeriyorsa,** bir isteği engellemek için özel bir kural yapılandırmanın bir örneği aşağıda verilmiştir.

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="QueryString değişkeninin değeri blok değerini içerip içermediğini denetleyen özel kural yapılandırma sayfasının ekran görüntüsü.":::

### <a name="default-rule-set-drs"></a>Varsayılan kural kümesi (DRS)

Azure tarafından yönetilen varsayılan kural kümesi varsayılan olarak etkindir. Geçerli varsayılan sürüm DefaultRuleSet_1.0 ' dır. WAF tarafından **yönetilen kurallardan**, **ata**, son kullanılabilir RuleSet Microsoft_DefaultRuleSet_1 1. aşağı açılan listede kullanılabilir.

Tek bir kuralı devre dışı bırakmak için, kural numarasının önündeki **onay kutusunu** seçin ve sayfanın üst kısmındaki **devre dışı bırak** ' ı seçin. Kural kümesindeki tek tek kuralların eylem türlerini değiştirmek için, kural numarasının önündeki onay kutusunu seçin ve ardından sayfanın en üstündeki **değişiklik eylemini** seçin.

:::image type="content" source="../media/waf-front-door-create-portal/managed-rules.png" alt-text="Bir kural kümesi, kural grupları, kurallar ve Etkinleştir, devre dışı bırak ve eylemi Değiştir düğmelerini gösteren yönetilen kurallar sayfasının ekran görüntüsü." lightbox="../media/waf-front-door-create-portal/managed-rules-expanded.png":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, kaynak grubunu ve tüm ilgili kaynakları kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure ön kapısı](../../frontdoor/front-door-overview.md) 
>  hakkında daha fazla bilgi edinin [Azure ön kapısının Standart/Premium hakkında daha fazla bilgi edinin](../../frontdoor/standard-premium/overview.md)
