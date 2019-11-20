---
title: Kurumsal senaryolarda Azure Lighthouse
description: Azure açık Thouse özellikleri, birden çok Azure AD kiracısının kullanıldığı bir kuruluşta çapraz kiracı yönetimini basitleştirmek için kullanılabilir.
ms.date: 09/25/2019
ms.topic: overview
ms.openlocfilehash: ae2241048e6ae481e319154beedab74c23c91163
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132507"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Kurumsal senaryolarda Azure Lighthouse

[Azure ışıklı kullanım](../overview.md) için en yaygın senaryo, müşterilerinin ' Azure Active Directory (Azure AD) kiracılarındaki kaynakları yöneten bir hizmet sağlayıcıdır. Ancak, Azure ışıklı kullanım özellikleri, birden çok Azure AD kiracısının kullanıldığı bir kuruluşta çapraz kiracı yönetimini basitleştirmek için de kullanılabilir.

## <a name="single-vs-multiple-tenants"></a>Tek ve birden çok kiracı

Çoğu kuruluş için, yönetim tek bir Azure AD kiracısıyla daha kolay olur. Tek bir kiracının içindeki tüm kaynakların olması, bu Kiracıdaki kullanıcılara, Kullanıcı gruplarına veya hizmet sorumlularına göre yönetim görevlerinin merkezileşmeyi sağlar. Mümkün olduğunda kuruluşunuz için bir kiracı kullanmanızı öneririz.

Aynı zamanda, bir kuruluşun birden çok Azure AD kiracının bakımını gerektirebileceği durumlar vardır. Bazı durumlarda bu geçici bir durum olabilir. Bu durumda, alımlar gerçekleştiği sırada ve uzun süreli bir kiracı birleştirme stratejisinin tanımlanması zaman alabilir. Kuruluşun aynı zamanda birden fazla kiracının devam edebilmesi için (tamamen bağımsız bağlı kuruluşlar, coğrafi veya yasal gereksinimler vb.). Çok kiracılı bir mimarinin gerekli olduğu durumlarda, yönetim işlemlerini merkezileştirmek ve kolaylaştırmak için Azure tarafından atanan kaynak yönetimi kullanılabilir. Birden çok kiracıdan abonelikler, Azure tarafından atanan [kaynak yönetimi](azure-delegated-resource-management.md)için eklendi olabilir. Bu, bir yönetim kiracısındaki belirlenen kullanıcıların, merkezi ve ölçeklenebilir bir şekilde [platformlar arası yönetim işlevleri](cross-tenant-management-experience.md) gerçekleştirmesini sağlar.

## <a name="tenant-management-architecture"></a>Kiracı Yönetimi mimarisi

Birden çok kiracının yönetim işlemlerini merkezileştirirken, diğer kiracılara yönelik yönetim işlemlerini gerçekleştiren kullanıcıların hangi kiracının dahil edileceğini belirlemeniz gerekir. Diğer bir deyişle, diğer kiracılar için hangi kiracının yönetim kiracısı olacağını belirlemeniz gerekir.

Örneğin, kuruluşunuzun *kiracı a*'yı çağıracağımız tek bir kiracıya sahip olduğunu varsayalım. Daha sonra kuruluşunuz iki ek kiracı, *kiracı B* ve *kiracı C*alır ve bunları ayrı kiracılar olarak tutmanızı gerektiren iş nedenleriniz vardır.

Kuruluşunuz, tüm kiracıların tamamında aynı ilke tanımlarını, yedekleme uygulamalarını ve güvenlik süreçlerini kullanmak istiyor. Kiracı A içinde bu görevleri gerçekleştirmekten sorumlu olan kullanıcılarınız (Kullanıcı grupları ve hizmet sorumluları dahil) zaten mevcut olduğundan, Kiracıdaki aynı kullanıcıların bu işlemleri gerçekleştirmesini sağlamak için kiracı B ve kiracı C içindeki tüm abonelikleri ekleyebilirsiniz. görevlerinize.

![Kiracıdaki kullanıcılar kiracı B ve kiracı C 'de kaynakları yönetme](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Güvenlik ve erişim konuları

Çoğu kurumsal senaryoda, bir abonelik içinde yalnızca belirli kaynak gruplarını temsil edebilir, ancak Azure tarafından atanan kaynak yönetimi için tam bir abonelik atamak isteyeceksiniz.

Her iki durumda da, [kaynaklara hangi kullanıcıların erişebileceğini tanımlarken en az ayrıcalık ilkesini izlediğinizden](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)emin olun. Bunun yapılması, kullanıcıların yalnızca gerekli görevleri gerçekleştirmek için gerekli izinlere sahip olmasını sağlamaya yardımcı olur ve yanlışlıkla hata olasılığını azaltır.

Azure Mathouse ve Azure Temsilcili kaynak yönetimi, verileri veya kaynakları fiziksel olarak taşımak yerine yalnızca bir kiracı ve yönetilen kiracılar arasında mantıksal bağlantılar sağlar. Ayrıca erişim, yönetim kiracısından yönetilen kiracılar 'a her zaman tek bir yönde gider.  Yönetim kiracısındaki kullanıcılar ve gruplar, yönetilen kiracı kaynaklarında yönetim işlemlerini gerçekleştirirken Multi-Factor Authentication kullanmaya devam etmelidir.

İç veya dış idare ve uyumluluk guardları olan kuruluşlar, saydamlık gereksinimlerini karşılamak için [Azure etkinlik günlüklerini](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) kullanabilir. Kurumsal kiracılar yönetim ve yönetilen kiracı ilişkileri oluştururken, her Kiracıdaki kullanıcılar günlüğe kaydedilen etkinliği görüntüleyerek diğer Kiracıdaki kullanıcılar tarafından gerçekleştirilen eylemleri izleyebilir ve görünürlük elde edebilir.

## <a name="onboarding-process-considerations"></a>Ekleme süreci konuları

Abonelikler (veya bir abonelik içindeki kaynak grupları), Azure Resource Manager şablonları dağıtarak veya Azure Marketi 'nde yayınlanan yönetilen hizmet teklifleri aracılığıyla veya özel olarak ya da Hazırlayan.

Kurumsal kullanıcılar normalde kuruluşun kiracılarına doğrudan erişim elde edebilecekler ve bir yönetim teklifini pazarlamaya veya yükseltmeye gerek duymadığından, genellikle Azure Resource Manager şablonlarıyla doğrudan daha hızlı ve daha basit hale gelir. [Ekleme](../how-to/onboard-customer.md)kılavuzundaki hizmet sağlayıcılarına ve müşterilere başvurduğumuz sürece kuruluşlar aynı işlemlerin aynısını kullanabilir.

İsterseniz, bir kuruluştaki kiracılar [Azure Market 'e yönetilen bir hizmet teklifi yayımlayarak](../how-to/publish-managed-services-offers.md)eklendi olabilir. Teklifin yalnızca uygun kiracılar için kullanılabilir olduğundan emin olmak için, planlarınızın özel olarak işaretlendiğinden emin olun. Özel bir plan sayesinde, sunmayı planladığınız her kiracı için abonelik kimliklerini sağlayabilir ve teklifinizi başka hiç kimse sağlayamayacak.

## <a name="terminology-notes"></a>Terminoloji notları

Kuruluştaki çapraz Kiracı Yönetimi için, Azure açık belgeleri belgelerindeki hizmet sağlayıcılarına yapılan başvurular, bir kuruluşta yönetim kiracısına (yani, kaynakları yönetecek kullanıcıları içeren kiracı) uygulanabilir. Azure tarafından atanan kaynak yönetimi aracılığıyla diğer kiracılarda. Benzer şekilde, müşterilere yapılan başvurular, yönetim kiracısındaki kullanıcılar aracılığıyla yönetilecek kaynak temsilcisi olan kiracılar için de kullanılabilir.

Örneğin, yukarıda açıklanan örnekte, kiracı A, hizmet sağlayıcı kiracısı (yöneten kiracı) ve B kiracısı ile kiracı C 'nin müşteri kiracıları olarak düşünülebilir.

Bu örnekte, uygun izinlere sahip bir Kullanıcı kiracıya, Azure portal **müşteriler** sayfasında, [temsilci kaynakları görüntüleyebilir ve yönetebilir](../how-to/view-manage-customers.md) . Benzer şekilde, B ve uygun izinlere sahip kiracı C kullanıcıları, Azure portal **hizmet sağlayıcıları** sayfasında kiracıya [atanmış kaynakları görüntüleyebilir ve yönetebilir](../how-to/view-manage-service-providers.md) .

## <a name="next-steps"></a>Sonraki adımlar

- [Çapraz kiracı yönetim deneyimleri](cross-tenant-management-experience.md)hakkında bilgi edinin.
- [Azure tarafından atanan temsilcinin kaynak yönetimi](azure-delegated-resource-management.md) hakkında bilgi edinin.