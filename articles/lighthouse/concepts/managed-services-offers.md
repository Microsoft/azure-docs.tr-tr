---
title: Azure Market’teki Yönetilen Hizmet teklifleri
description: Yönetilen hizmet teklifleri, Azure Market 'te müşterilere kaynak yönetimi teklifleri satmanızı sağlar.
ms.date: 02/17/2021
ms.topic: conceptual
ms.openlocfilehash: e66a87751e0375804031e777a40c5fc1d0e8a7e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093001"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Azure Market’teki Yönetilen Hizmet teklifleri

Bu makalede, [Azure Marketi](https://azuremarketplace.microsoft.com)'Ndeki **yönetilen hizmet** teklifi türü açıklanmaktadır. Yönetilen hizmet teklifleri, [Azure açık Thouse](../overview.md)aracılığıyla müşterilere kaynak yönetimi hizmetleri sunmanıza olanak tanır. Bu teklifleri tüm potansiyel müşteriler için veya yalnızca bir veya daha fazla belirli müşteriye kullanılabilir hale getirebilirsiniz. Müşterileri bu yönetilen hizmetlerle ilgili maliyetlerle doğrudan faturalandırınızdan, Microsoft tarafından ücretlendirilmez.

## <a name="understand-managed-service-offers"></a>Yönetilen hizmet tekliflerini anlama

Yönetilen hizmet, müşterileri Azure 'un kullanımına ekleme sürecini kolaylaştırır. Bir müşteri Azure Marketi 'nde bir teklif satın aldığında, hangi aboneliklerin ve/veya kaynak gruplarının eklendi olması gerektiğini belirleyebilecektir.

Bundan sonra, kuruluşunuzdaki kullanıcılar, teklifi oluştururken tanımladığınız erişime göre, [Azure tarafından atanan kaynak yönetimi](azure-delegated-resource-management.md)aracılığıyla bu kaynaklar üzerinde yönetim kiracının içinden çalışabilecektir. Bu işlem, erişim düzeyini tanımlayan [rollerle](tenants-users-roles.md) birlikte müşteri kaynaklarına erişimi olacak Azure Active Directory (Azure AD) kullanıcılarını, gruplarını ve hizmet sorumlularını belirten bir bildirimde yapılır.

> [!NOTE]
> Yönetilen hizmet teklifleri, Azure Kamu ve diğer ulusal bulutlarda kullanılamaz.

## <a name="public-and-private-offers"></a>Ortak ve özel teklifler

Her yönetilen hizmet teklifi bir veya daha fazla plan içerir. Planlar özel veya genel olabilir.

Teklifinizi belirli müşterilerle sınırlandırmak istiyorsanız, özel bir plan yayımlayabilirsiniz. Bunu yaptığınızda, plan yalnızca sağladığınız belirli abonelik kimlikleri için satın alınabilir. Daha fazla bilgi için bkz. [özel teklifler](../../marketplace/private-offers.md).

> [!NOTE]
> Özel teklifler, bulut çözümü sağlayıcısı (CSP) programının satıcısı aracılığıyla oluşturulan aboneliklerle desteklenmez.

Genel planlar, hizmetlerinizi yeni müşterilere yükseltemenizi sağlar. Bunlar genellikle müşterinin kiracısına sınırlı erişim istediğinizde daha uygundur. Bir müşteriyle ilişki kurduktan sonra, kuruluşunuza ek erişim vermeye karar verirse, bu, yalnızca söz konusu müşteri için yeni bir özel plan yayımlayarak veya [Azure Resource Manager şablonları kullanarak daha fazla erişim sağlamak için bunları](../how-to/onboard-customer.md)ekleyebilirsiniz.

Uygunsa, hem genel hem de özel planları aynı teklifine dahil edebilirsiniz.

> [!IMPORTANT]
> Bir plan ortak olarak yayımlandıktan sonra, özel olarak değiştiremezsiniz. Hangi müşterilerin teklif ve temsilci kaynaklarınızı kabul edeceğini denetlemek için özel bir plan kullanın. Ortak bir plan sayesinde, kullanılabilirliği belirli müşterilerle veya hatta belirli sayıda müşteriye kısıtlayamazsınız (ancak bunu yapmak isterseniz planı tamamen satmaya devam edebilirsiniz). Bir müşteri bir teklifi kabul ettikten sonra, teklifi yayımladığınızda rol **tanımıyla** [yönetilen hizmetler kayıt ataması silme rolü](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) olarak ayarlanmış bir **Yetkilendirme** eklediyseniz, bir [temsilciye erişimi kaldırabilirsiniz](../how-to/remove-delegation.md) . Ayrıca müşteriye ulaşabilir ve [erişiminizi kaldırmasını](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)isteyebilirsiniz.

## <a name="publish-managed-service-offers"></a>Yönetilen hizmet tekliflerini yayımlama

Yönetilen hizmet teklifini yayımlamayı öğrenmek için bkz. [Azure Market 'Te yönetilen hizmet teklifi yayımlama](../how-to/publish-managed-services-offers.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Temsilcili kaynak yönetimi](azure-delegated-resource-management.md) ve [çapraz kiracı yönetim deneyimleri](cross-tenant-management-experience.md)hakkında bilgi edinin.
- [Yönetilen hizmet tekliflerini](../how-to/publish-managed-services-offers.md) Azure Market 'te yayımlayın.
