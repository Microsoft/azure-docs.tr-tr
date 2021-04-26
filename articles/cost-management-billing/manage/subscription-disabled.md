---
title: Devre dışı bırakılmış bir Azure aboneliğini yeniden etkinleştirme
description: Bir Azure aboneliğinizin ne zaman devre dışı bırakılabileceğini ve nasıl yeniden etkinleştirilebileceğini açıklar.
keywords: azure aboneliği devre dışı bırakıldı
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/30/2021
ms.author: banders
ms.openlocfilehash: 37093900bf49e8a7613e2e3f4311548675791ceb
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220892"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>Devre dışı bırakılmış bir Azure aboneliğini yeniden etkinleştirme

Kredinizin süresi dolduğu, harcama limitinize ulaştığınız, süresi geçmiş bir faturanız olduğu, kredi kartı limitinize ulaştığınız veya abonelik Hesap Yöneticisi tarafından iptal edildiği için Azure aboneliğiniz devre dışı bırakılabilir. Hangi sorunun sizin için geçerli olduğunu görün ve aboneliğinizin yeniden etkinleştirilmesini sağlamak için bu makaledeki adımları izleyin.

## <a name="your-credit-is-expired"></a>Kredinizin süresi doldu

Ücretsiz bir Azure hesabı için kaydolduğunuzda, 30 gün ve 12 aylık ücretsiz hizmet için faturalandırma para biriminizde 200 ABD Doları Azure kredisi sağlayan ücretsiz bir deneme aboneliği alırsınız. 30 günün sonunda Azure aboneliğinizi devre dışı bırakır. Aboneliğiniz, abonelikle sağlanan krediyi ve ücretsiz hizmetleri yanlışlıkla aşan kullanım için ücretlendirilmeye karşı sizi korumak için devre dışı bırakılır. Azure hizmetlerini kullanmaya devam etmek için [aboneliğinizi yükseltmelisiniz](upgrade-azure-subscription.md). Yükseltme sonrasında aboneliğinizin yine 12 ay boyunca ücretsiz hizmetlere erişimi olacaktır. Yalnızca ücretsiz hizmet miktarı limitlerinin ötesinde kullanım ücreti alınır.

## <a name="you-reached-your-spending-limit"></a>Harcama limitinize ulaştınız

Ücretsiz Deneme ve Visual Studio Enterprise gibi krediler içeren Azure abonelikleri için harcama limitleri vardır. Yalnızca dahil edilen krediye kadar hizmetleri kullanabilirsiniz. Kullanımınız harcama sınırına ulaştığında, Azure bu fatura döneminin geri kalanı için aboneliğinizi devre dışı bırakır. Aboneliğiniz, abonelikle sağlanan krediyi yanlışlıkla aşan kullanım için ücretlendirilmeye karşı sizi korumak için devre dışı bırakılır. Harcama sınırınızı kaldırmak için, [Azure Portal harcama limitini kaldırma](spending-limit.md#remove)bölümüne bakın.

> [!NOTE]
> Ücretsiz Deneme aboneliğiniz varsa ve harcama limitini kaldırırsanız, aboneliğiniz Ücretsiz Deneme süresinin sonunda kullandıkça öde fiyatlarına tabi bireysel aboneliğe dönüştürülür. Kalan kredinizi, aboneliği oluşturduğunuz tarihten itibaren 30 gün boyunca korursunuz. Ayrıca 12 ay boyunca ücretsiz hizmetlere erişmeye devam edebilirsiniz.

Azure'ın faturalama etkinliğini izlemek ve yönetmek için bkz. [Azure maliyetlerini yönetmeyi planlama](../understand/plan-manage-costs.md).

## <a name="your-bill-is-past-due"></a>Faturanızın ödeme süresi geçmiş

Vadesi geçmiş bir bakiyeyi çözümlemek için aşağıdaki makalelerden birine bakın:

- Kullandıkça Öde dahil Microsoft çevrimiçi abonelik programı abonelikleri için bkz. [Azure 'dan bir e-posta aldıktan sonra Azure aboneliğiniz için vadesi geçmiş bakiyeyi çözümleme](resolve-past-due-balance.md).
- Microsoft Müşteri Sözleşmesi abonelikleri için bkz. [Microsoft Azure faturanızı ödeyin](../understand/pay-bill.md).

## <a name="the-bill-exceeds-your-credit-card-limit"></a>Fatura, kredi kartı limitinizi aşıyor

Sorunu çözmek için [farklı bir kredi kartına geçin](change-credit-card.md). Öte yandan bir işletmeyi temsil ediyorsanız [faturayla ödemeye geçebilirsiniz](pay-by-invoice.md).

## <a name="the-subscription-was-accidentally-canceled"></a>Abonelik yanlışlıkla iptal edildi

Hesap yöneticisiyseniz ve yanlışlıkla bir Kullandıkça Öde aboneliğini iptal ederseniz, Azure portal yeniden etkinleştirebilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Abonelikler ' e gidin ve ardından iptal edilen aboneliği seçin.
1. **Yeniden etkinleştir**' i seçin.
1. **Tamam ' ı** seçerek yeniden etkinleştirmeyi onaylayın.  
    :::image type="content" source="./media/subscription-disabled/reactivate-sub.png" alt-text="Yeniden etkinleştirmeyi Onayla gösteren ekran görüntüsü" :::

Diğer abonelik türleri için [destek ekibiyle iletişime geçerek](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aboneliğinizin yeniden etkinleştirilmesini sağlayın.

## <a name="after-reactivation"></a>Yeniden etkinleştirme sonrasında

Aboneliğiniz yeniden etkinleştirildikten sonra kaynakların oluşturulmasında ve yönetilmesinde gecikme olabilir. Gecikme 30 dakikayı aşarsa, yardım için [Azure Faturalandırma desteği](https://go.microsoft.com/fwlink/?linkid=2083458) 'ne başvurun. Azure kaynaklarının çoğu otomatik olarak serbest bırakılır ve hiçbir eylem gerektirmez. Bununla birlikte Azure hizmet kaynaklarınızı denetlemenizi ve otomatik olarak serbest bırakılmamış olanları yeniden başlatmanızı öneririz.

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar
- [Azure maliyetlerini yönetmeyi planlama](../understand/plan-manage-costs.md) işleminin nasıl yapıldığını öğrenin.
