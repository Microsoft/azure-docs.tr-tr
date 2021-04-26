---
title: Azure tarafından atanan temsilcinin kaynak yönetimi
description: Azure Temsilcili kaynak yönetimi, Azure Athouse 'ın önemli bir parçasıdır. Bu, hizmet sağlayıcılarının uygun olmayan kaynakları çeviklerle ve duyarlığa göre yönetmesine olanak tanır.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 93a3de257fe88de4915eff3582ff38fc03ef380e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767772"
---
# <a name="azure-delegated-resource-management"></a>Azure tarafından atanan temsilcinin kaynak yönetimi

Azure Temsilcili kaynak yönetimi, [Azure Use](../overview.md)'ın temel bileşenlerinden biridir. Azure Temsilcili kaynak yönetimi sayesinde, hizmet sağlayıcıları müşteri katılımı ve ekleme deneyimlerini basitleştirecek şekilde, yetki verilen kaynakları çeviklik ve duyarlıkla yönetdirebilir. Müşteriler, kiracılarına kimlerin erişebileceklerini, erişebilecek kaynakları ve hangi eylemlerin alınacağını denetler.

## <a name="what-is-azure-delegated-resource-management"></a>Azure Temsilcili kaynak yönetimi nedir?

Azure Temsilcili kaynak yönetimi, kaynakların bir kiracıdan başka bir kiracıya mantıksal olarak projeksiyonunu sunar. Bu, bir Azure Active Directory (Azure AD) kiracısındaki yetkili kullanıcıların müşterilerine ait farklı Azure AD kiracılar arasında yönetim işlemleri gerçekleştirmesini sağlar. Hizmet sağlayıcıları kendi Azure AD kiracısında oturum açabilir ve Temsilcili müşteri abonelikleri ve kaynak gruplarında çalışmak için yetkilendirmeye sahip olabilir. Bu, her bir müşteri kiracısında oturum açmaya gerek kalmadan, kendi müşterileri adına yönetim işlemleri gerçekleştirmesini sağlar.

> [!TIP]
> Azure Temsilcili kaynak yönetimi, platformlar arası yönetimi basitleştirmek için [birden çok Azure AD kiracısına sahip bir kuruluşta](enterprise.md) de kullanılabilir.

Yetkili kullanıcılar, Azure Temsilcili kaynak yönetimi sayesinde, bu müşterinin kiracısında bir hesabı olmadan veya müşterinin kiracısının ortak sahibi olmasına gerek kalmadan doğrudan bir müşteri aboneliği bağlamında çalışabilir.

[Çapraz kiracı yönetim deneyimi](cross-tenant-management-experience.md) , Azure Ilkesi, Azure Güvenlik Merkezi ve daha fazlası gibi Azure Yönetim Hizmetleri ile daha verimli çalışmanıza olanak sağlar. Tüm hizmet sağlayıcı etkinlikleri, müşterinin kiracısında saklanan (ve yönetim kiracısında kullanıcılar tarafından görüntülenebilir) etkinlik günlüğünde izlenir. Bu, hem yönetim hem de yönetilen Kiracıdaki kullanıcıların herhangi bir değişiklikle ilişkili kullanıcıyı kolayca tanımlayabileceği anlamına gelir.

[Yeni yönetilen hizmet teklifi türünü Azure Market 'e yayımlayarak](../how-to/publish-managed-services-offers.md) kolayca Azure 'un açık bir şekilde kullanmasını sağlayabilirsiniz. Alternatif olarak, [Azure Resource Manager şablonlarını dağıtarak ekleme işlemini tamamlayabilirsiniz](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Azure Temsilcili kaynak yönetimi nasıl kullanılır?

Yüksek düzeyde, Azure tarafından atanan kaynak yönetiminin nasıl çalıştığı aşağıda verilmiştir:

1. İlk olarak, gruplarınızın, hizmet sorumlularının veya kullanıcılarınızın müşterinin Azure kaynaklarını yönetmesi gereken erişimi (rolleri) belirlersiniz. Erişim tanımı, kiracınızın [yerleşik **roledefinition** değerleriyle](../../role-based-access-control/built-in-roles.md) (katkıda bulunan, VM katılımcısı, okuyucu vb.) eşlendiği **PrincipalId** kimlikleriyle birlikte Kiracı kimliğini yönetme olanağını içerir.
2. Bu erişimi belirtin ve müşteriyi Azure açık bir şekilde iki şekilde kullanabilirsiniz:
   - Müşterinin kabul edeceği [bir Azure Marketi yönetilen hizmet teklifi](../how-to/publish-managed-services-offers.md) (özel veya ortak) yayımlayın
   - Bir veya daha fazla belirli abonelik veya kaynak grubu için [Azure Resource Manager şablonunu müşterinin kiracısına dağıtma](../how-to/onboard-customer.md)

3. Müşteri eklendi olduktan sonra yetkili kullanıcılar, yönetilen kiracınızda oturum açabilir ve tanımladığınız erişime göre belirtilen müşteri kapsamında görevler gerçekleştirebilir. Müşteriler, hizmet sağlayıcısı eylemlerini gözden geçirebilir ve gerekirse erişimi kaldırma seçeneğine sahip olabilir.

> [!NOTE]
> Farklı [bölgelerde](../../availability-zones/az-overview.md#regions)bulunan temsilcili kaynakları yönetebilirsiniz. Ancak, bir [Ulusal bulut](../../active-directory/develop/authentication-national-cloud.md) ve Azure genel bulutu genelinde veya iki ayrı ulusal bulutta abonelikler temsilciliğini desteklemez.

## <a name="support-for-azure-delegated-resource-management"></a>Azure tarafından yetkilendirilen kaynak yönetimi desteği

Azure Temsilcili kaynak yönetimiyle ilgili yardıma ihtiyacınız varsa Azure portal bir destek isteği açabilirsiniz. **Sorun türü** için **Teknik**' i seçin. Bir **abonelik seçin ve ardından açık (** **& yönetimi** altında) seçeneğini belirleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Çapraz kiracı yönetim deneyimleri](cross-tenant-management-experience.md)hakkında bilgi edinin.
- [Azure Market 'te yönetilen hizmet teklifleri](managed-services-offers.md)hakkında bilgi edinin.
