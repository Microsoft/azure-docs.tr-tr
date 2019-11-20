---
title: Bulut Çözüm Sağlayıcısı programı hakkında önemli noktalar
description: CSP iş ortakları için, Azure tarafından atanan kaynak yönetimi, ayrıntılı izinleri etkinleştirerek güvenliğin ve denetimin artırılmasına yardımcı olur.
ms.date: 10/23/2019
ms.topic: overview
ms.openlocfilehash: 3ea32418cdf6808ad311d343d28ba2778740dbe4
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132516"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure açık Thouse ve bulut çözümü sağlayıcısı programı

Bir [CSP (bulut çözümü sağlayıcısı)](https://docs.microsoft.com/partner-center/csp-overview) iş ortağıysanız, Kullanıcı [adına yönetme (Aobo)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) işlevini kullanarak, CSP programı aracılığıyla müşterileriniz için oluşturulan Azure aboneliklerine zaten erişebilirsiniz. Bu erişim, müşterilerinizin aboneliklerini doğrudan desteketmenize, yapılandırmanıza ve yönetmenize olanak sağlar.

[Azure](../overview.md)'da açık olan Azure Kaynak YÖNETIMINI, Aoinin yanı bir şekilde kullanabilirsiniz. Bu sayede, kullanıcılarınız için daha ayrıntılı izinler etkinleştirerek güvenliği artırmaya ve gereksiz erişimi azaltmanıza yardımcı olur. Ayrıca, kullanıcılarınızın kiracınızda tek bir oturum açma kullanarak birden çok müşteri aboneliği arasında çalışabilmesi için daha fazla verimlilik ve ölçeklenebilirlik sağlar.

> [!TIP]
> Müşteri kaynaklarının korunmasına yardımcı olmak için, [Önerilen güvenlik uygulamalarınızı](recommended-security-practices.md) , [iş ortağı güvenlik gereksinimleriyle](https://docs.microsoft.com/partner-center/partner-security-requirements)birlikte gözden geçirip izlediğinizden emin olun.

## <a name="administer-on-behalf-of-aobo"></a>Adına yönetme (AOBO)

AOLE, kiracınızda [yönetici aracı](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) rolüne sahip herhangi bir Kullanıcı, CSP programı aracılığıyla oluşturduğunuz Azure aboneliklerine YÖNELIK olarak aocektir. Müşterilerin aboneliklerine erişmesi gereken tüm kullanıcılar bu grubun bir üyesi olmalıdır. AOIMIZ, farklı müşterilerle çalışan ayrı gruplar oluşturma veya gruplar veya kullanıcılar için farklı rolleri etkinleştirme esnekliği sağlar.

![AOI kullanarak Kiracı Yönetimi](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Azure tarafından atanan temsilcinin kaynak yönetimi

Azure Temsilcili kaynak yönetimini kullanarak, aşağıdaki diyagramda gösterildiği gibi farklı müşterilere veya rollere farklı gruplar atayabilirsiniz. Kullanıcılar, Azure tarafından atanan kaynak yönetimi aracılığıyla uygun düzeyde erişime sahip olacağı için, Yönetim Aracısı rolüne sahip kullanıcı sayısını azaltabilir (ve bu nedenle tam AOBO erişimi vardır). Bu, müşterilerinizin kaynaklarına gereksiz erişimi sınırlayarak güvenliği artırmaya yardımcı olur. Ayrıca, birden çok müşteriyi ölçekli olarak yönetme konusunda daha fazla esneklik sağlar.

CSP programı aracılığıyla oluşturduğunuz bir aboneliği [eklemek, Azure 'un Temsilcili kaynak yönetimine abonelik](../how-to/onboard-customer.md)ekleme bölümünde açıklanan adımları izler. Kiracınızda yönetici Aracısı rolüne sahip olan tüm kullanıcılar bu ekleme işlemini gerçekleştirebilir.

![AOI ve Azure tarafından atanan kaynak yönetimini kullanan Kiracı Yönetimi](../media/csp-2.jpg)

> [!NOTE]
> Azure portal [ **müşterilerimiz** sayfasında](../how-to/view-manage-customers.md) , [MICROSOFT Müşteri Sözleşmesi 'ni (MCA) imzalayan](https://docs.microsoft.com/partner-center/confirm-customer-agreement) ve Azure planı kapsamında olan CSP müşterilerinin fatura bilgilerini ve kaynaklarını görüntüleyen bir **bulut çözümü sağlayıcısı (Önizleme)** bölümü vardır. Daha fazla bilgi için bkz. [Microsoft Iş ortağı sözleşmesi Faturalandırma hesabınızı kullanmaya başlama](https://docs.microsoft.com/azure/billing/mpa-overview).
>
> CSP müşterileri, Azure tarafından yetkilendirilen kaynak yönetimi için eklendi olup olmadığı konusunda bu bölümde görünebilir. Sahip olmaları durumunda **müşteriler bölümünde,** [müşterileri görüntüleme ve yönetme](../how-to/view-manage-customers.md)bölümünde açıklandığı gibi da görünür.

## <a name="next-steps"></a>Sonraki adımlar

- [Çapraz kiracı yönetim deneyimleri](cross-tenant-management-experience.md)hakkında bilgi edinin.
- [Azure 'un Temsilcili kaynak yönetimine nasıl bir abonelik](../how-to/onboard-customer.md)ekleneceğini öğrenin.
- [Bulut çözümü sağlayıcısı programı](https://docs.microsoft.com/partner-center/csp-overview)hakkında bilgi edinin.
