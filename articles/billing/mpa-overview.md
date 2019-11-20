---
title: Microsoft İş Ortağı Sözleşmesi ödeme hesabınızı kullanmaya başlama - Azure CSP
description: Microsoft İş Ortağı Sözleşmesi ödeme hesabınızı (CSP) anlama
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2019
ms.author: banders
ms.openlocfilehash: 6bb0f3ea1b4e3bf17ed3bf26b838da8f2a694673
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898555"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>Microsoft İş Ortağı Sözleşmesi ödeme hesabınızı kullanmaya başlama

Azure’ı kullanmak için kaydolduğunuzda bir ödeme hesabı oluşturulur. Faturaları, ödemeleri yönetmek ve maliyetleri izlemek için ödeme hesabınızı kullanırsınız. Birden çok ödeme hesabına erişiminiz olabilir. Örneğin, kişisel projeleriniz için Azure’a kaydolmuş olabilirsiniz. Ayrıca kuruluşunuzun Kurumsal Anlaşması, Microsoft Müşteri Sözleşmesi veya Microsoft İş Ortağı Sözleşmesi aracılığıyla da Azure’a erişiminiz olabilir. Bu senaryoların her biri için ayrı bir ödeme hesabınız olur.

Bu makale, Microsoft İş Ortağı Sözleşmesi ödeme hesapları için geçerlidir. Bu hesaplar, Bulut çözümü Sağlayıcılarının (CSP) yeni ticaret deneyiminde müşterilerinin faturalarını yönetmesi için oluşturulmuştur. Yeni deneyim yalnızca bir Microsoft Müşteri Sözleşmesini kabul etmiş ve bir Azure Planına sahip olan en az bir müşteriye sahip iş ortakları tarafından kullanılabilir. [Microsoft İş Ortağı Sözleşmesi’ne erişiminizin olup olmadığını denetleyin](#check-access-to-a-microsoft-partner-agreement). Bir [Azure planı](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ile müşterileriniz Azure hizmetlerine Microsoft Müşteri sözleşmesi kapsamındaki kullandıkça öde ücretleriyle erişebilir.

## <a name="your-billing-account"></a>Ödeme hesabınız

Microsoft İş Ortağı Sözleşmesi için faturalandırma hesabınız, iş yaptığınız her para birimi için bir faturalama profili içerir. Faturalama profili, faturalarınızı kendi para biriminde yönetmenize olanak tanır. Para birimlerine bağlı olarak, müşterilerle ilişkiler oluşturduğunuzda Azure abonelikleri ve diğer satın alma işlemleri ilgili faturalama profillerine fatura edilir.

Aşağıdaki diyagramda faturalama hesabıyla faturalama profilleri, müşteriler ve kurumsal bayiler arasındaki ilişki gösterilir.

![Microsoft İş Ortağı Sözleşmesi faturalama hiyerarşisini gösteren diyagram](./media/mpa-overview/mpa-hierarchy.svg)

Kuruluşunuzda **Genel Yönetici** ve **Yönetici Aracıları** rolüne sahip olan kullanıcılar ödeme hesaplarını, faturalama profillerini ve müşterileri yönetebilir. Daha fazla bilgi için bkz. [İş Ortağı Merkezi - Kullanıcı rollerini ve izinleri atama](https://docs.microsoft.com/partner-center/permissions-overview).

## <a name="billing-profiles"></a>Faturalama profilleri

Faturanızı bir para biriminde yönetmek için faturalama profili kullanın. Hesabınızdaki her bir faturalama profili için ay başında aylık bir fatura oluşturulur. Fatura, önceki aya ait tüm Azure aboneliklerinin ve diğer satın alımların ücretlerini içerir.

Azure portalında faturayı görüntüleyip kullanım dosyası ve fiyat listesi gibi ilgili belgeleri indirebilirsiniz. Daha fazla bilgi için bkz. [Microsoft İş Ortağı Sözleşmesi için faturaları indirme](billing-download-azure-invoice.md).

> [!IMPORTANT]
>
> Faturalama profillerinin faturaları Azure Planları olan müşterilerin ücretlerinin yanı sıra Microsoft Müşteri Sözleşmesini kabul etmemiş ve Azure planları olmayan müşteriler için SaaS, Azure market ve satın alma rezervasyonlarının ücretlerini içerir.

## <a name="customers"></a>Müşteriler

Microsoft Müşteri Sözleşmesini kabul etmiş ve bir Azure Planı olan müşterileri Azure portalında görüntüleyip yönetebilirsiniz. Ücretleri ve işlemleri görüntüleyebilir, bu müşteriler için Azure abonelikleri oluşturabilir ve yönetebilirsiniz.

### <a name="enable-policy-to-give-visibility-into-cost"></a>Maliyet görünürlüğü sağlamak için ilkeyi etkinleştirme

Müşterilerin kuruluşlarındaki kullanıcıların, Azure tüketimine yönelik Kullandıkça Öde tarifesinde maliyeti görüntüleyip çözümleyebilmesi için ilkeyi uygulayın. İlke varsayılan olarak devre dışıdır ve kullanıcılar maliyeti görüntüleyemez. Etkinleştirildikten sonra, bir abonelikte uygun [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) erişimine sahip olan kullanıcılar abonelik maliyetini görüntüleyebilir ve analiz edebilir.

İlkeyi etkinleştirmek için:

1. [Azure Portal](https://portal.azure.com) oturum açın.

1. **Maliyet Yönetimi + Faturalama** araması yapın.

   ![Azure portalı aramasını gösteren ekran görüntüsü](./media/mpa-overview/search-cmb.png)

1. Sol taraftan **Müşteriler**’i seçin ve sonra listeden bir müşteri belirleyin.

   ![Müşterinin seçilmesini gösteren ekran görüntüsü](./media/mpa-overview/mpa-customers.png)

1. Sol taraftan **İlkeler**’i seçin.

   ![İlkeleri gösteren ekran görüntüsü](./media/mpa-overview/mpa-change-policy.png)

1. **Evet**’i seçin.

## <a name="resellers"></a>Kurumsal bayiler

CSP [iki katmanlı modelindeki](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview#azure-csp-direct-and-azure-csp-indirect) dolaylı sağlayıcılar Azure portalında müşteriler için abonelik oluştururken bir kurumsal bayi seçebilir. Oluşturma sonrasında, kurumsal bayiye göre filtrelenmiş abonelik listesini görüntüleyebilir ve Azure maliyet analizinde kurumsal bayiye göre müşterinin maliyetini analiz edebilir.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft İş Ortağı Sözleşmesi’ne erişimi denetleme
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Yardıma mı ihtiyacınız var? Desteğe başvurun

Yardıma ihtiyacınız varsa sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Sonraki adımlar

Ödeme hesabınız hakkında bilgi edinmek için aşağıdaki makalelere bakın:

- [Microsoft İş Ortağı Sözleşmesi için ek Azure aboneliği oluşturma](billing-create-subscription.md)
- [Azure Faturalama API'lerini](https://docs.microsoft.com/rest/api/billing/) kullanarak faturalama verilerini kendi raporlama sisteminizle tümleştirme
- [İş ortakları için Azure Maliyet Yönetimi hızlı başlangıcı](https://go.microsoft.com/fwlink/?linkid=2106482)
