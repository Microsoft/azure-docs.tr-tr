---
title: Azure tarafından atanan temsilcinin kaynak yönetimi
description: Yönetilen hizmetler, hizmet sağlayıcılarının Azure Marketi 'ndeki müşterilere kaynak yönetimi tekliflerini satmasını sağlar.
ms.date: 07/11/2019
ms.topic: overview
ms.openlocfilehash: e79252d38e5e3363ef1d6a82ec060b896fa247a8
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133378"
---
# <a name="azure-delegated-resource-management"></a>Azure tarafından atanan temsilcinin kaynak yönetimi

Azure Temsilcili kaynak yönetimi, Azure Use 'ın temel bileşenlerinden biridir. Azure Temsilcili kaynak yönetimi sayesinde, hizmet sağlayıcıları müşteri katılımı ve ekleme deneyimlerini basitleştirecek şekilde, yetki verilen kaynakları çeviklik ve duyarlıkla yönetdirebilir.

## <a name="what-is-azure-delegated-resource-management"></a>Azure Temsilcili kaynak yönetimi nedir?

Azure Temsilcili kaynak yönetimi, kaynakların bir kiracıdan başka bir kiracıya mantıksal olarak projeksiyonunu sunar. Bu, bir Azure Active Directory (Azure AD) kiracısındaki yetkili kullanıcıların müşterilerine ait farklı Azure AD kiracılar arasında yönetim işlemleri gerçekleştirmesini sağlar. Hizmet sağlayıcıları kendi Azure AD kiracısında oturum açabilir ve Temsilcili müşteri abonelikleri ve kaynak gruplarında çalışmak için yetkilendirmeye sahip olabilir. Bu, her bir müşteri kiracısında oturum açmaya gerek kalmadan, kendi müşterileri adına yönetim işlemleri gerçekleştirmesini sağlar.

> [!NOTE]
> Azure Temsilcili kaynak yönetimi, platformlar arası yönetimi basitleştirmek için [birden çok Azure AD kiracısına sahip bir kuruluşta](enterprise.md) de kullanılabilir.

Yetkili kullanıcılar, Azure Temsilcili kaynak yönetimi sayesinde, bu müşterinin kiracısında bir hesabı olmadan veya müşterinin kiracısının ortak sahibi olmasına gerek kalmadan doğrudan bir müşteri aboneliği bağlamında çalışabilir. Ayrıca, [Tüm Temsilcili müşteri aboneliklerini, Azure Portal yeni **müşterilerimiz** sayfasında görüntüleyebilir ve yönetebilir](../how-to/view-manage-customers.md) .

[Çapraz kiracı yönetim deneyimi](cross-tenant-management-experience.md) , Azure Ilkesi, Azure Güvenlik Merkezi ve daha fazlası gibi Azure Yönetim Hizmetleri ile daha verimli çalışmanıza yardımcı olur. Tüm hizmet sağlayıcı etkinlikleri, hem hizmet sağlayıcısının hem de müşterinin kiracılarında depolanan etkinlik günlüğünde izlenir. Bu, hem müşteri hem de hizmet sağlayıcının herhangi bir değişiklikle ilişkili kullanıcıyı kolayca tanımlayabileceği anlamına gelir.

Azure tarafından atanan kaynak yönetimine bir müşteri eklediğinizde, bu [kişilerin tekliflerini, hizmet sağlayıcılarını ve Temsilcili kaynakları doğrulayabilecekleri ve yönetebilecekleri](../how-to/view-manage-service-providers.md)Azure Portal yeni **hizmet sağlayıcıları** sayfasına erişimi vardır. Müşteri, bir hizmet sağlayıcısı için erişimi iptal etmek isterse, bunu dilediğiniz zaman yapabilirsiniz.

Azure 'un sunduğu kaynak yönetimine kolayca müşteri eklemek için [Yeni yönetilen hizmet teklifi türünü Azure Market 'e yayımlayabilirsiniz](../how-to/publish-managed-services-offers.md) . Alternatif olarak, [Azure Resource Manager şablonlarını dağıtarak ekleme işlemini tamamlayabilirsiniz](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Azure Temsilcili kaynak yönetimi nasıl kullanılır?

Yüksek düzeyde, Azure tarafından atanan kaynak yönetiminin nasıl çalıştığı aşağıda verilmiştir:

1. Hizmet sağlayıcı olarak, gruplarınızın, hizmet sorumlularının veya kullanıcılarınızın müşterinin Azure kaynaklarını yönetmesi gereken erişimi (rolleri) belirlersiniz. Erişim tanımı, hizmet sağlayıcısının kiracı KIMLIĞINI ve [yerleşik **roledefinition** değerlerine](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (katkıda bulunan, VM) eşlenmiş kiracınızdan **PrincipalId** kimlikleri kullanılarak tanımlanan teklif için gerekli erişimi içerir. Katkıda bulunan, okuyucu vb.).
2. Bu erişimi belirtin ve müşteriyi, Azure tarafından atanan kaynak yönetimine iki şekilde katılın:
   - Müşterinin kabul edeceği [bir Azure Market yönetilen hizmet teklifi](../how-to/publish-managed-services-offers.md) (özel veya ortak) yayımlayın
   - Bir veya daha fazla belirli abonelik veya kaynak grubu için [Azure Resource Manager şablonunu müşterinin kiracısına dağıtma](../how-to/onboard-customer.md)
3. Müşteri eklendi olduktan sonra yetkili kullanıcılar, hizmet sağlayıcı kiracınızda oturum açabilir ve tanımladığınız erişime göre belirtilen müşteri kapsamında yönetim görevleri gerçekleştirebilir.

## <a name="support-for-azure-delegated-resource-management"></a>Azure tarafından yetkilendirilen kaynak yönetimi desteği

Azure Temsilcili kaynak yönetimiyle ilgili yardıma ihtiyacınız varsa Azure portal bir destek isteği açabilirsiniz. **Sorun türü**için **Teknik**' i seçin. Bir abonelik seçin ve ardından **temsilci kaynak yönetimi** ( **izleme & yönetimi**altında) seçeneğini belirleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Çapraz kiracı yönetim deneyimleri](cross-tenant-management-experience.md)hakkında bilgi edinin.
- [Azure Market 'te yönetilen hizmet teklifleri](managed-services-offers.md)hakkında bilgi edinin.