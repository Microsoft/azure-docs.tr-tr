---
title: Azure Market’teki yönetilen hizmetler teklifleri
description: Yönetilen hizmetler, hizmet sağlayıcılarının Azure Marketi 'ndeki müşterilere kaynak yönetimi tekliflerini satmasını sağlar.
ms.date: 09/19/2019
ms.topic: overview
ms.openlocfilehash: 0f1ba749a5477f0c006e6666b841e82f4eeb193f
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131864"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Azure Market’teki yönetilen hizmetler teklifleri

Bu makalede, [Azure Marketi](https://azuremarketplace.microsoft.com)'Nde yeni **yönetilen hizmetler** teklif türü açıklanmaktadır. Yönetilen hizmetler teklifi, Azure tarafından atanan kaynak yönetimine sahip müşterilere kaynak yönetimi hizmetleri sunmanıza olanak tanır. Bu teklifleri tüm potansiyel müşteriler için veya yalnızca bir veya daha fazla belirli müşteriye kullanılabilir hale getirebilirsiniz. Müşterileri bu yönetilen hizmetlerle ilgili maliyetlerle doğrudan faturalandırınızdan, Microsoft tarafından ücretlendirilmez.

## <a name="understand-managed-services-offers"></a>Yönetilen hizmet tekliflerini anlama

Yönetilen hizmetler, Azure tarafından atanan kaynak yönetimi için müşterileri ekleme sürecini kolaylaştırır. Bir müşteri Azure Marketi 'nde bir teklif satın aldığında, hangi aboneliklerin ve/veya kaynak gruplarının eklendi olması gerektiğini belirleyebilecektir. Aboneliğin öncelikle **Microsoft. ManagedServices** kaynak sağlayıcısını el ile kaydederek ekleme için yetkilendirilmiş olması gerektiğini unutmayın.

Bundan sonra kuruluşunuzdaki kullanıcılar, [bulut iş ortağı portalı](https://cloudpartner.azure.com/)teklifi oluştururken tanımladığınız erişime göre kuruluşunuzun kiracısından bu kaynaklar için yönetim görevleri gerçekleştirebilir. Bu işlem, Azure tarafından atanan kaynak yönetimi kullanılarak müşteri kaynaklarına erişimi olacak Azure AD kullanıcılarını, gruplarını ve hizmet sorumlularını belirten bir bildirimde ve erişim düzeyini tanımlayan rollerle birlikte yapılır. Bir dizi Kullanıcı veya uygulama hesabı yerine bir Azure AD grubuna izinler atayarak, erişim gereksinimleriniz değiştiğinde bireysel kullanıcı ekleyebilir veya kaldırabilirsiniz.

## <a name="public-and-private-offers"></a>Ortak ve özel teklifler

Her yönetilen hizmet teklifi bir veya daha fazla plan içerir. Bu planlar özel veya genel olabilir.

Teklifinizi belirli müşterilerle sınırlandırmak istiyorsanız, özel bir plan yayımlayabilirsiniz. Bunu yaptığınızda, plan yalnızca sağladığınız belirli abonelik kimlikleri için satın alınabilir. Daha fazla bilgi için bkz. [özel teklifler](https://docs.microsoft.com/azure/marketplace/private-offers).

Genel planlar, hizmetlerinizi yeni müşterilere yükseltemenizi sağlar. Bunlar genellikle müşterinin kiracısına sınırlı erişim istediğinizde daha uygundur. Bir müşteriyle ilişki kurduktan sonra, kuruluşunuza ek erişim vermeye karar verirse, bu, yalnızca söz konusu müşteri için yeni bir özel plan yayımlayarak veya [Azure Resource Manager şablonları kullanarak daha fazla erişim sağlamak için bunları](../how-to/onboard-customer.md)ekleyebilirsiniz.

Bir plana ortak olarak yayımlandıktan sonra özel olarak değiştiremeyeceğini aklınızda bulundurun. Ayrıca, bir genel planın belirli müşterilere veya hatta belirli sayıda müşteriye yönelik kullanılabilirliğini kısıtlayamazsınız, ancak bunu yapmak isterseniz planı tamamen satmaya de durdurabilirsiniz.

Uygunsa, hem genel hem de özel planları aynı teklifine dahil edebilirsiniz.

## <a name="publish-managed-service-offers"></a>Yönetilen hizmet tekliflerini yayımlama

Yönetilen bir hizmet teklifini yayımlamayı öğrenmek için bkz. [Azure Market 'Te yönetilen hizmetler teklifi yayımlama](../how-to/publish-managed-services-offers.md). Bulut İş Ortağı Portalı kullanarak Azure Marketi 'Nde yayımlama hakkında genel bilgi için bkz. [Azure Market ve Appsource Yayımlama Kılavuzu](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) ve [Azure ve appsource Market tekliflerini yönetme](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Temsilcili kaynak yönetimi](azure-delegated-resource-management.md) ve [çapraz kiracı yönetim deneyimleri](cross-tenant-management-experience.md)hakkında bilgi edinin.
- [Yönetilen hizmet tekliflerini](../how-to/publish-managed-services-offers.md) Azure Market 'te yayımlayın.
