---
title: Kurumsal senaryolarda Azure Lighthouse
description: Azure açık Thouse özellikleri, birden çok Azure AD kiracısının kullanıldığı bir kuruluşta çapraz kiracı yönetimini basitleştirmek için kullanılabilir.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 97b44f71750bdb533e889546f370a9b36ea5d3b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419363"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Kurumsal senaryolarda Azure Lighthouse

[Azure ıthouse](../overview.md) için yaygın bir senaryo, bir hizmet sağlayıcısı müşterilere ait olan Azure Active Directory (Azure AD) kiracılarındaki kaynakları yönetmektedir. Azure ışıklı kullanım özellikleri, birden çok Azure AD kiracısının kullanıldığı bir kuruluşta çapraz kiracı yönetimini basitleştirmek için de kullanılabilir.

## <a name="single-vs-multiple-tenants"></a>Tek ve birden çok kiracı

Çoğu kuruluş için, yönetim tek bir Azure AD kiracısıyla daha kolay olur. Tek bir kiracının içindeki tüm kaynakların olması, bu Kiracıdaki kullanıcılara, Kullanıcı gruplarına veya hizmet sorumlularına göre yönetim görevlerinin merkezileşmeyi sağlar. Mümkün olduğunda kuruluşunuz için bir kiracı kullanmanızı öneririz.

Bazı kuruluşların birden çok Azure AD kiracıları kullanması gerekebilir. Bu, alımların gerçekleştiği ve uzun süreli bir kiracı birleştirme stratejisi henüz tanımlanmadığı gibi geçici bir durum olabilir. Diğer zamanlarda, kuruluşların bağımsız olarak bağımsız yan kuruluşlar, coğrafi veya yasal gereksinimler ya da başka hususlar nedeniyle sürekli olarak birden çok kiracının bakımını yapması gerekebilir.

Çok kiracılı bir mimarinin gerekli olduğu durumlarda, Azure ışıklı kullanımı yönetim işlemlerini merkezileştirmek ve kolaylaştırmaya yardımcı olabilir. [Azure Temsilcili kaynak yönetimini](azure-delegated-resource-management.md)kullanarak, tek bir yönetim kiracısındaki kullanıcılar [çapraz kiracı yönetim işlevlerini](cross-tenant-management-experience.md) merkezi ve ölçeklenebilir bir şekilde gerçekleştirebilir.

## <a name="tenant-management-architecture"></a>Kiracı Yönetimi mimarisi

Azure açık Thouse 'ı bir kuruluşta kullanmak için, diğer kiracılarda yönetim işlemlerini gerçekleştiren kullanıcıları hangi kiracının dahil edileceğini belirlemeniz gerekir. Diğer bir deyişle, diğer kiracılar için bir kiracıyı yöneten kiracı olarak atamanız gerekecektir.

Örneğin, kuruluşunuzun *kiracı a*'yı çağıracağımız tek bir kiracıya sahip olduğunu varsayalım. Kuruluşunuz daha sonra *B* ve *kiracı* kiracı 'yı alır ve bunları ayrı kiracılar olarak korumanız gereken iş nedenleriniz vardır. Ancak, aynı ilke tanımlarını, yedekleme uygulamalarını ve bunların tümü için aynı kullanıcı kümesi tarafından gerçekleştirilen yönetim görevleri ile birlikte güvenlik süreçlerini kullanmak istersiniz.

Kiracıda kuruluşunuzda A kiracısı için bu görevleri gerçekleştiren kullanıcıları zaten içerdiği için, kiracı B ' deki ve kiracı C 'nin içindeki abonelikleri ekleyebilirsiniz ve bu da Kiracıdaki aynı kullanıcıların tüm kiracılar genelinde bu görevleri gerçekleştirmesini sağlar.

![Kiracıdaki kullanıcıları, B kiracısındaki ve kiracı C 'deki kaynakları yöneten diyagram.](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Güvenlik ve erişim konuları

Çoğu kurumsal senaryoda, Azure açık bir tam aboneliği sağlamak isteyeceksiniz. Ayrıca, bir abonelik içinde yalnızca belirli kaynak gruplarının yetkisini de seçebilirsiniz.

Her iki durumda da, [temsilci atanan kaynaklara hangi kullanıcıların erişebileceğini tanımlarken en az ayrıcalık ilkesini izlediğinizden](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)emin olun. Bunun yapılması, kullanıcıların yalnızca gerekli görevleri gerçekleştirmek için gerekli izinlere sahip olmasını sağlamaya yardımcı olur ve yanlışlıkla hata olasılığını azaltır.

Azure Mathouse yalnızca, verileri veya kaynakları fiziksel olarak taşımak yerine, bir kiracı ve yönetilen kiracılar arasında mantıksal bağlantılar sağlar. Ayrıca erişim, yönetim kiracısından yönetilen kiracılar 'a her zaman tek bir yönde gider.  Yönetim kiracısındaki kullanıcılar ve gruplar, yönetilen kiracı kaynaklarında yönetim işlemlerini gerçekleştirirken Multi-Factor Authentication kullanmaya devam etmelidir.

İç veya dış idare ve uyumluluk guardları olan kuruluşlar, saydamlık gereksinimlerini karşılamak için [Azure etkinlik günlüklerini](../../azure-monitor/essentials/platform-logs-overview.md) kullanabilir. Kurumsal kiracılar yönetim ve yönetilen kiracı ilişkileri oluştururken, her Kiracıdaki kullanıcılar, yönetim kiracısında kullanıcılar tarafından alınan eylemleri görmek için günlüğe kaydedilen etkinliği görüntüleyebilir.

## <a name="onboarding-considerations"></a>Ekleme konuları

Abonelikler (veya bir abonelik içindeki kaynak grupları), Azure Resource Manager şablonları dağıtarak veya Azure Marketi 'nde yayınlanan yönetilen hizmet teklifleri aracılığıyla Azure 'da eklendi için kullanılabilir.

Kurumsal kullanıcılar genellikle kuruluşun kiracılarına doğrudan erişebildiklerinden ve bir yönetim teklifini pazarlamaya veya yükseltmeye gerek duyduğundan, genellikle Azure Resource Manager şablonları dağıtmaya yönelik daha hızlı ve daha basittir. [Ekleme Kılavuzu](../how-to/onboard-customer.md) , hizmet sağlayıcılarına ve müşterilere göndermede olsa da kuruluşlar, kiracılarını eklemek için aynı süreçler kullanabilir.

İsterseniz, bir kuruluştaki kiracılar [Azure Market 'e yönetilen bir hizmet teklifi yayımlayarak](../how-to/publish-managed-services-offers.md)eklendi olabilir. Teklifin yalnızca uygun kiracılar için kullanılabilir olduğundan emin olmak için, planlarınızın özel olarak işaretlendiğinden emin olun. Özel bir plan sayesinde, sunmayı planladığınız her kiracı için abonelik kimliklerini sağlarsınız ve teklifinizin sağlayabilmesi için başka hiç kimse olmayacaktır.

## <a name="terminology-notes"></a>Terminoloji notları

Kuruluştaki çapraz Kiracı Yönetimi için, Azure açık belgeleri belgelerindeki hizmet sağlayıcılarına yapılan başvurular, bir kuruluştaki yönetim kiracısına (diğer bir deyişle, Azure açık Thouse aracılığıyla diğer kiracılardaki kaynakları yönetecek kullanıcıları içeren kiracıya) uygulanabilir. Benzer şekilde, müşterilere yapılan tüm başvurular, yönetim kiracısındaki kullanıcılar aracılığıyla yönetilecek kaynak temsilcisi olan kiracılar için de kullanılabilir.

Örneğin, yukarıda açıklanan örnekte, kiracı A, hizmet sağlayıcı kiracısı (yöneten kiracı) ve B kiracısı ile kiracı C 'nin müşteri kiracıları olarak düşünülebilir.

Bu örneğe devam ettiğinizde, uygun izinlere sahip bir Kullanıcı kiracıya, Azure portal **müşteriler** sayfasında, [temsilci kaynakları görüntüleyebilir ve yönetebilir](../how-to/view-manage-customers.md) . Benzer şekilde, B ve uygun izinlere sahip kiracı C kullanıcıları, Azure portal **hizmet sağlayıcıları** sayfasında kiracıya [atanmış kaynakları görüntüleyebilir ve yönetebilir](../how-to/view-manage-service-providers.md) .

## <a name="next-steps"></a>Sonraki adımlar

- [Çapraz kiracı yönetim deneyimleri](cross-tenant-management-experience.md)hakkında bilgi edinin.
- [Azure tarafından atanan temsilcinin kaynak yönetimi](azure-delegated-resource-management.md) hakkında bilgi edinin.
