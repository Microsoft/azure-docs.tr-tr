---
title: Azure harcama limiti | Microsoft Docs
description: Bu makalede, Azure harcama limitinin nasıl çalıştığı ve nasıl kaldırıldığı açıklanmaktadır.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 97e6e04bf7fab3a48f1ce0552e1b653b208be77b
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375429"
---
# <a name="azure-spending-limit"></a>Azure harcama limiti

Azure’daki harcama limiti, harcamanızın kredi tutarınızın limitini aşmasını engeller. Ücretsiz Azure hesabına veya birden çok aya yayılan krediler içeren abonelik türlerine kaydolan tüm yeni müşterilerin harcama limiti, varsayılan olarak açık olacaktır. Harcama limiti, kredi miktarına eşittir ve değiştirilemez. Örneğin, ücretsiz Azure hesabı için kaydolduysanız harcama limiti 200 USD olur ve bunu 500 USD olarak değiştiremezsiniz. Ancak harcama limitini kaldırabilirsiniz. Bu nedenle, herhangi bir limitiniz yoktur veya kredi miktarına eşit bir limitiniz vardır. Bu limit birçok türde harcamayı yapmanıza engel olur. Harcama limiti, taahhüt planları veya kullandıkça öde fiyatlandırmasının geçerli olduğu abonelikler için kullanılamaz. [Tüm Azure abonelik türlerini ve harcama limiti kullanılabilirliğini gösteren listeye](https://azure.microsoft.com/support/legal/offer-details/) göz atın.

## <a name="reaching-a-spending-limit"></a>Harcama limitine ulaşma

Kullanımınız harcama limitinizi bitiren ücretlere neden olduğunda, dağıttığınız hizmetler ilgili faturalama döneminin geri kalanı boyunca devre dışı bırakılır.

Örneğin, ücretsiz Azure hesabınıza dahil edilen tüm kredileri harcadığınızda, dağıttığınız Azure kaynakları üretimden kaldırılır ve Azure sanal makineleriniz durdurulup serbest bırakılır. Depolama hesaplarınızdaki veriler salt okunur olarak kullanılabilir.

Abonelik türünüz birkaç aya yayılan krediler içeriyorsa, bir sonraki faturalama döneminin başlangıcında aboneliğiniz otomatik olarak yeniden etkinleştirilir. Daha sonra Azure kaynaklarınızı yeniden dağıtabilir ve depolama hesaplarınıza ve veritabanlarınıza tam erişim elde edebilirsiniz.

Harcama limitine ulaştığınızda Azure e-posta bildirimleri gönderir. Harcama limitine ulaşan abonelikler hakkında bildirimleri görmek için [Azure portalında](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oturum açın.

Ücretsiz Azure hesabına kaydolduysanız ve harcama limitine ulaşırsanız, harcama limitini kaldırmak ve aboneliği otomatik olarak yeniden etkinleştirmek için [kullandıkça öde](billing-upgrade-azure-subscription.md) fiyatlandırmasına yükseltme yapabilirsiniz.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-azure-portal"></a>Azure portalında harcama limitini kaldırma

Azure aboneliğinizle ilişkili geçerli bir ödeme yöntemi olduğu sürece, harcama limitini istediğiniz zaman kaldırabilirsiniz. Visual Studio Enterprise ve Visual Studio Professional gibi birden çok aya yayılan krediler içeren abonelik türleri için, bir sonraki faturalama döneminizin başlangıcında harcama limitini etkinleştirebilirsiniz.

Harcama limitinizi kaldırmak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oturum açın.
1. Bir abonelik seçin. Aboneliğin harcama limitine ulaşılırsa abonelik devre dışı bırakılır.
1. Sayfanın üst kısmından **Harcama limitini kaldır**’ı seçin.
1. Size uygun bir seçenek belirleyin.

![Harcama limitini kaldırma seçeneğini belirleme](./media/billing-spending-limit/remove-spending-limit.PNG)

| Seçenek | Etki |
| --- | --- |
| Harcama limitini süresiz olarak kaldır | Harcama limiti kaldırılır ve bir sonraki faturalama döneminin başlangıcında otomatik olarak yeniden açılmaz. |
| Geçerli fatura dönemi için harcama limitini kaldır | Harcama limiti kaldırılır ve bir sonraki fatura döneminin başlangıcında otomatik olarak yeniden açılır. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Harcama limitini neden kaldırmak isteyebilirsiniz?

Harcama limiti, belirli üçüncü taraf ve Microsoft hizmetlerini dağıtmanızı veya kullanmanızı engelleyebilir. Aboneliğinizdeki harcama limitini kaldırmanızı gerektiren senaryolar aşağıda belirtilmiştir.

-  Oracle gibi birinci taraf görüntülerini veya Azure DevOps Services gibi hizmetleri dağıtmayı planlıyorsunuz. Bu durumda, harcama limitinize neredeyse anında ulaşılacak ve bunun sonucunda aboneliğiniz devre dışı bırakılacaktır.
- Kesintiye uğramasını istemediğiniz hizmetleriniz var. Harcama limitinize ulaştığında, dağıttığınız Azure kaynakları üretimden kaldırılır, ayrıca Azure sanal makineleriniz durdurulur ve serbest bırakılır. Kesintiye uğramasını istemediğiniz hizmetler varsa harcama limitinizi kaldırmanız gerekir.
- Sanal IP adresleri gibi ayarlar içeren, kaybetmek istemediğiniz hizmet ve kaynaklarınız var. Bu ayarlar harcama limitinize ulaştığınızda kaybolur ve hizmetler ile kaynaklar yeniden tahsis edilir.

## <a name="turn-on-the-spending-limit-after-removing"></a>Kaldırdıktan sonra harcama limitini açma

Bu özellik yalnızca birden çok aya yayılan krediler içeren abonelik türleri için harcama limiti süresiz olarak kaldırıldığında kullanılabilir. Bu özelliği, bir sonraki fatura döneminin başlangıcında harcama limitini otomatik olarak etkinleştirmek için kullanabilirsiniz.

1. [Hesap Merkezi](https://account.windowsazure.com/Subscriptions)’nde oturum açın.
1. Harcama limiti seçeneğini değiştirmek için sarı başlığa tıklayın.
1. **Sonraki faturalama döneminde (\<faturalama döneminin başlangıç tarihi\>) harcama limitini aç** seçeneğini belirleyin

## <a name="custom-spending-limit"></a>Özel harcama limiti

Özel harcama limitleri kullanılamaz.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Harcama limiti tüm ücretleri engellemez

[Azure Market’te yayımlanan bazı dış hizmetler](billing-understand-your-azure-marketplace-charges.md), abonelik kredilerinizle kullanılamaz ve harcama limitiniz ayarlanmış olduğunda bile ayrıca ücretlere neden olabilir. Visual Studio lisansları, Azure Active Directory Premium, destek planları ve üçüncü taraf markalı hizmetlerin çoğu örnek olarak gösterilebilir. Yeni bir dış hizmet sağladığınızda, hizmetlerin ayrı olarak faturalandığını bildiren bir uyarı gösterilir:

![Market satın alma uyarısı](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar
- [Kullandıkça öde](billing-upgrade-azure-subscription.md) fiyatlandırmasına tabi bir plana yükseltin.
