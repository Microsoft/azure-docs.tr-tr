---
title: Azure harcama limiti
description: Bu makalede, Azure harcama limitinin nasıl çalıştığı ve nasıl kaldırıldığı açıklanmaktadır.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.author: banders
ms.openlocfilehash: 47f1a1ed182420852682e908c7cd8b5e094bc085
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932565"
---
# <a name="azure-spending-limit"></a>Azure harcama limiti

Azure’daki harcama limiti, harcamanızın kredi tutarınızın limitini aşmasını engeller. Ücretsiz Azure hesabına veya birden çok aya yayılan krediler içeren abonelik türlerine kaydolan tüm yeni müşterilerin harcama limiti, varsayılan olarak açık olacaktır. Harcama limiti, kredi miktarına eşittir. Harcama limiti miktarını değiştiremezsiniz. Örneğin, ücretsiz Azure hesabı için kaydolduysanız harcama limiti 200 USD olur ve bunu 500 USD olarak değiştiremezsiniz. Ancak harcama limitini kaldırabilirsiniz. Bu nedenle, herhangi bir limitiniz yoktur veya kredi miktarına eşit bir limitiniz vardır. Bu limit birçok türde harcamayı yapmanıza engel olur. Harcama limiti, taahhüt planları veya kullandıkça öde fiyatlandırmasının geçerli olduğu abonelikler için kullanılamaz. [Tüm Azure abonelik türlerini ve harcama limiti kullanılabilirliğini gösteren listeye](https://azure.microsoft.com/support/legal/offer-details/) göz atın.

## <a name="reaching-a-spending-limit"></a>Harcama limitine ulaşma

Kullanımınız harcama limitinizi bitiren ücretlere neden olduğunda, dağıttığınız hizmetler ilgili faturalama döneminin geri kalanı boyunca devre dışı bırakılır.

Örneğin, ücretsiz Azure hesabınıza dahil edilen tüm kredileri harcadığınızda, dağıttığınız Azure kaynakları üretimden kaldırılır ve Azure sanal makineleriniz durdurulup serbest bırakılır. Depolama hesaplarınızdaki veriler salt okunur olarak kullanılabilir.

Abonelik türünüz birkaç aya yayılan krediler içeriyorsa, bir sonraki faturalama döneminin başlangıcında aboneliğiniz otomatik olarak yeniden etkinleştirilir. Daha sonra Azure kaynaklarınızı yeniden dağıtabilir ve depolama hesaplarınıza ve veritabanlarınıza tam erişim elde edebilirsiniz.

Harcama limitine ulaştığınızda Azure e-posta bildirimleri gönderir. Harcama limitine ulaşan abonelikler hakkında bildirimleri görmek için [Azure portalında](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oturum açın.

Ücretsiz Azure hesabına kaydolduysanız ve harcama limitine ulaşırsanız, harcama limitini kaldırmak ve aboneliği otomatik olarak yeniden etkinleştirmek için [kullandıkça öde](upgrade-azure-subscription.md) fiyatlandırmasına yükseltme yapabilirsiniz.

## <a name="remove-the-spending-limit-in-azure-portal"></a>Azure portalında harcama limitini kaldırma

Azure aboneliğinizle ilişkili geçerli bir ödeme yöntemi olduğu sürece, harcama limitini istediğiniz zaman kaldırabilirsiniz. Visual Studio Enterprise ve Visual Studio Professional gibi birden çok aya yayılan krediler içeren abonelik türleri için, harcama limitini tamamen veya yalnızca bir sonraki faturalama dönemini için kaldırabilirsiniz. Yalnızca geçerli faturalama dönemini seçerseniz harcama limiti bir sonraki faturalama döneminizin başında otomatik olarak etkinleştirilir.

Ücretsiz Azure hesabınız varsa harcama limitinizi kaldırmak için bkz. [Azure aboneliğinizi yükseltme](upgrade-azure-subscription.md). Diğer durumlarda harcama limitinizi kaldırmak için şu adımları izleyin:

<a id="remove"></a>

1. [Azure portalında](https://portal.azure.com) Hesap Yöneticisi olarak oturum açın.
1. **Maliyet Yönetimi + Faturalama** araması yapın.

    ![Maliyet yönetimi + faturalama aramasını gösteren ekran görüntüsü ](./media/spending-limit/search-bar.png)

1. **Aboneliklerim** listesinde aboneliğinizi seçin. Örneğin, *Visual Studio Enterprise*.

   ![Aboneliklerim listesine genel bakışı gösteren ekran görüntüsü](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Visual Studio aboneliklerinizden bazılarını burada görmüyorsanız, bunun nedeni abonelik dizinini değiştirmeniz olabilir. Bu abonelikler için, özgün dizine (başlangıçta kaydolduğunuz dizin) geçmeniz gerekir. Ardından 2. adımı tekrarlayın.

1. Harcama limitini kaldırmak için Abonelik genel görünümünde turuncu renkli başlığa tıklayın.

    ![Harcama limitini kaldırma başlığını gösteren ekran görüntüsü](./media/spending-limit/msdn-remove-spending-limit-banner-x.png)

1. Harcama limitini süresiz olarak mı yoksa yalnızca geçerli fatura dönemi için mi kaldırmak istediğinizi seçin.

      ![Harcama limitini kaldırma dikey penceresini gösteren ekran görüntüsü](./media/spending-limit/remove-spending-limit-blade-x.png)

      | Seçenek | Etki |
      | --- | --- |
      | Harcama limitini süresiz olarak kaldır | Harcama limiti, bir sonraki fatura döneminin başlangıcında otomatik olarak yeniden açılmaz. Ancak istediğiniz zaman el ile açabilirsiniz. |
      | Geçerli fatura dönemi için harcama limitini kaldır | Harcama limiti, bir sonraki fatura döneminin başlangıcında otomatik olarak yeniden açılır. |


1. Aboneliğiniz için bir ödeme yöntemi seçmek üzere **Ödeme yöntemini seçin**'e tıklayın. Bu yöntem, aboneliğinizin etkin ödeme yöntemi olur.

1. **Finish (Son)** düğmesine tıklayın.


## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Harcama limitini neden kaldırmak isteyebilirsiniz?

Harcama limiti, belirli üçüncü taraf ve Microsoft hizmetlerini dağıtmanızı veya kullanmanızı engelleyebilir. Aboneliğinizdeki harcama limitini kaldırmanızı gerektiren senaryolar aşağıda belirtilmiştir.

-  Oracle gibi üçüncü taraf görüntülerini veya Azure DevOps Services gibi hizmetleri dağıtmayı planlıyorsunuz. Bu durumda, harcama limitinize neredeyse anında ulaşılacak ve bunun sonucunda aboneliğiniz devre dışı bırakılacaktır.
- Kesintiye uğramasını istemediğiniz hizmetleriniz var. Harcama limitinize ulaştığında, dağıttığınız Azure kaynakları üretimden kaldırılır, ayrıca Azure sanal makineleriniz durdurulur ve serbest bırakılır. Kesintiye uğramasını istemediğiniz hizmetler varsa harcama limitinizi kaldırmanız gerekir.
- Sanal IP adresleri gibi ayarlar içeren, kaybetmek istemediğiniz hizmet ve kaynaklarınız var. Bu ayarlar harcama limitinize ulaştığınızda kaybolur ve hizmetler ile kaynaklar yeniden tahsis edilir.

## <a name="turn-on-the-spending-limit-after-removing"></a>Kaldırdıktan sonra harcama limitini açma

Bu özellik yalnızca birden çok aya yayılan krediler içeren abonelik türleri için harcama limiti süresiz olarak kaldırıldığında kullanılabilir. Bu özelliği, bir sonraki fatura döneminin başlangıcında harcama limitini otomatik olarak etkinleştirmek için kullanabilirsiniz.


1. [Azure portalında](https://portal.azure.com) Hesap Yöneticisi olarak oturum açın.
1. **Maliyet Yönetimi + Faturalama** araması yapın.

    ![Maliyet yönetimi + faturalama aramasını gösteren ekran görüntüsü ](./media/spending-limit/search-bar.png)

1. **Aboneliklerim** listesinde aboneliğinizi seçin. Örneğin, *Visual Studio Enterprise*.

   ![Aboneliklerim listesine genel bakışı gösteren ekran görüntüsü](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Visual Studio aboneliklerinizden bazılarını burada görmüyorsanız, bunun nedeni abonelik dizinini değiştirmeniz olabilir. Bu abonelikler için, özgün dizine (başlangıçta kaydolduğunuz dizin) geçmeniz gerekir. Ardından 2. adımı tekrarlayın.

1. Harcama limitini açmak için Abonelik genel bakış sayfasının en üstündeki başlığa tıklayın.

## <a name="custom-spending-limit"></a>Özel harcama limiti

Özel harcama limitleri kullanılamaz.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Harcama limiti tüm ücretleri engellemez

[Azure Market’te yayımlanan bazı dış hizmetler](../understand/understand-azure-marketplace-charges.md), abonelik kredilerinizle kullanılamaz ve harcama limitiniz ayarlanmış olduğunda bile ayrıca ücretlere neden olabilir. Visual Studio lisansları, Azure Active Directory Premium, destek planları ve üçüncü taraf markalı hizmetlerin çoğu örnek olarak gösterilebilir. Yeni bir dış hizmet sağladığınızda, hizmetlerin ayrı olarak faturalandığını bildiren bir uyarı gösterilir:

![Market satın alma uyarısı](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar
- [Kullandıkça öde](upgrade-azure-subscription.md) fiyatlandırmasına tabi bir plana yükseltin.
