---
title: Bulut Çözüm Sağlayıcısı programı hakkında önemli noktalar
description: CSP iş ortakları için, Azure tarafından atanan kaynak yönetimi, ayrıntılı izinleri etkinleştirerek güvenliğin ve denetimin artırılmasına yardımcı olur.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 8736cf913739f2bd16fb519aed98fd336f6876a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419398"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure açık Thouse ve bulut çözümü sağlayıcısı programı

Bir [CSP (bulut çözümü sağlayıcısı)](/partner-center/csp-overview) iş ortağıysanız, Kullanıcı [adına yönetme (Aobo)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) işlevini kullanarak, CSP programı aracılığıyla müşterileriniz için oluşturulan Azure aboneliklerine zaten erişebilirsiniz. Bu erişim, müşterilerinizin aboneliklerini doğrudan desteketmenize, yapılandırmanıza ve yönetmenize olanak sağlar.

[Azure](../overview.md)'da açık olan Azure Kaynak YÖNETIMINI, Aoinin yanı bir şekilde kullanabilirsiniz. Bu sayede, kullanıcılarınız için daha ayrıntılı izinler etkinleştirerek güvenliği artırmaya ve gereksiz erişimi azaltmanıza yardımcı olur. Ayrıca, kullanıcılarınızın kiracınızda tek bir oturum açma kullanarak birden çok müşteri aboneliği arasında çalışabilmesi için daha fazla verimlilik ve ölçeklenebilirlik sağlar.

> [!TIP]
> Müşteri kaynaklarının korunmasına yardımcı olmak için, [Önerilen güvenlik uygulamalarınızı](recommended-security-practices.md) , [iş ortağı güvenlik gereksinimleriyle](/partner-center/partner-security-requirements)birlikte gözden geçirip izlediğinizden emin olun.

## <a name="administer-on-behalf-of-aobo"></a>Adına yönetme (AOBO)

AOLE, kiracınızda [yönetici aracı](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) rolüne sahip herhangi bir Kullanıcı, CSP programı aracılığıyla oluşturduğunuz Azure aboneliklerine YÖNELIK olarak aocektir. Müşterilerin aboneliklerine erişmesi gereken tüm kullanıcılar bu grubun bir üyesi olmalıdır. AOIMIZ, farklı müşterilerle çalışan ayrı gruplar oluşturma veya gruplar veya kullanıcılar için farklı rolleri etkinleştirme esnekliği sağlar.

![AOI kullanarak kiracı yönetimini gösteren diyagram.](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Azure tarafından atanan temsilcinin kaynak yönetimi

Azure Athouse kullanarak, aşağıdaki diyagramda gösterildiği gibi farklı müşterilere veya rollere farklı gruplar atayabilirsiniz. Kullanıcılar, Azure tarafından atanan kaynak yönetimi aracılığıyla uygun düzeyde erişime sahip olacağı için, Yönetim Aracısı rolüne sahip kullanıcı sayısını azaltabilir (ve bu nedenle tam AOBO erişimi vardır). Bu, müşterilerinizin kaynaklarına gereksiz erişimi sınırlayarak güvenliği artırmaya yardımcı olur. Ayrıca, birden çok müşteriyi ölçekli olarak yönetme konusunda daha fazla esneklik sağlar.

CSP programı aracılığıyla oluşturduğunuz bir aboneliği [eklemek, Azure 'da bir abonelik](../how-to/onboard-customer.md)ekleme ' de açıklanan adımları izler. Kiracınızda yönetici Aracısı rolüne sahip olan tüm kullanıcılar bu ekleme işlemini gerçekleştirebilir.

![AOI ve Azure tarafından atanan kaynak yönetimini kullanan kiracı yönetimini gösteren diyagram.](../media/csp-2.jpg)

> [!TIP]
> Özel planlar içeren [yönetilen hizmet teklifleri](managed-services-offers.md) , bulut çözümü sağlayıcısı (CSP) programının satıcısı aracılığıyla oluşturulan aboneliklerle desteklenmez. Bu abonelikleri, [Azure Resource Manager şablonları kullanarak](../how-to/onboard-customer.md)Azure açık thouse öğesine ekleyebilirsiniz.

> [!NOTE]
> Azure portal [ **müşterilerimiz** sayfasında](../how-to/view-manage-customers.md) , [MICROSOFT Müşteri Sözleşmesi 'ni (MCA) imzalayan](/partner-center/confirm-customer-agreement) ve [Azure planı kapsamında](/partner-center/azure-plan-get-started)olan CSP müşterilerinin fatura bilgilerini ve kaynaklarını görüntüleyen bir **bulut çözümü sağlayıcısı (Önizleme)** bölümü vardır. Daha fazla bilgi için bkz. [Microsoft Iş ortağı sözleşmesi Faturalandırma hesabınızı kullanmaya başlama](../../cost-management-billing/understand/mpa-overview.md).
>
> CSP müşterileri, bu bölümde eklendi de Azure 'un açık olarak kullanılması gerekip gerekmediğini belirtir. Varsa [müşterileri ve temsilcileri yönetme](../how-to/view-manage-customers.md)bölümünde açıklandığı gibi **müşteriler** bölümünde da görünürler. Benzer şekilde, bir CSP müşterisi, Azure açık Thouse 'a eklemek için **müşterilerimin** **bulut çözümü sağlayıcısı (Önizleme)** bölümünde gözükmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Çapraz kiracı yönetim deneyimleri](cross-tenant-management-experience.md)hakkında bilgi edinin.
- [Azure açık Thouse aboneliği ekleme](../how-to/onboard-customer.md)hakkında bilgi edinin.
- [Bulut çözümü sağlayıcısı programı](/partner-center/csp-overview)hakkında bilgi edinin.
