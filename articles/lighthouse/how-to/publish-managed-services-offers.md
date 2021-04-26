---
title: Azure Market 'Te yönetilen hizmet teklifi yayımlama
description: Onpanolar müşterilerinin Azure 'da kullanımı için bir yönetilen hizmet teklifini nasıl yayımlayacağınızı öğrenin.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: f59d9708925b52bcf9432663fe6d3137a54b37ad
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107457"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Azure Market 'Te yönetilen hizmet teklifi yayımlama

Bu makalede ortak veya özel yönetilen hizmet teklifini Iş Ortağı Merkezi 'ndeki [ticari Market](../../marketplace/overview.md) programını kullanarak [Azure Marketi](https://azuremarketplace.microsoft.com) 'nde nasıl yayımlayacağınızı öğreneceksiniz. Teklifi satın alan müşteriler, abonelikleri veya kaynak gruplarını devredebilir ve bu da bunları [Azure ışıklı kullanım](../overview.md)aracılığıyla yönetmenizi sağlar.

## <a name="publishing-requirements"></a>Yayımlama gereksinimleri

Teklif oluşturmak ve yayımlamak için [Iş Ortağı Merkezi 'nde](../../marketplace/create-account.md) geçerli bir hesabınız olması gerekir. Zaten bir hesabınız yoksa [kaydolma işlemi](https://aka.ms/joinmarketplace) , Iş Ortağı Merkezi 'nde hesap oluşturma ve ticari Market programına kaydolma adımlarında size yol açacaktır.

Yönetilen hizmet [teklifi sertifika gereksinimleri](/legal/marketplace/certification-policies#700-managed-services)uyarınca, yönetilen bir hizmet teklifi yayımlamak için [gümüş veya altın bir bulut platformu uzmanlığına](/partner-center/learn-about-competencies) sahip olmanız veya bir [Azure uzman msp](https://partner.microsoft.com/membership/azure-expert-msp) olmanız gerekir. Müşterinin teklifinizi dağıttığı her seferinde [CRM sisteminizde bir kayıt oluşturacak bir müşteri adayı hedefi de girmeniz](../../marketplace/plan-managed-service-offer.md#customer-leads) gerekir.

Bir teklifi Azure Marketi 'ne yayımlamak veya tüm gereksinimleri karşılamazsanız, Azure Resource Manager şablonları kullanarak müşterileri el ile ekleyebilirsiniz. Daha fazla bilgi için bkz. [Azure 'da müşteri ekleme](onboard-customer.md).

Aşağıdaki tablo, yönetilen bir hizmet teklifini yayımlayarak veya Azure Resource Manager şablonları kullanarak müşterilerin eklenip eklenmeyeceğini belirlemenize yardımcı olabilir.

|**Değerlendirme**  |**Yönetilen hizmet teklifi**  |**ARM şablonları**  |
|---------|---------|---------|
|[Iş Ortağı Merkezi hesabı](../../marketplace/create-account.md) gerektirir   |Yes         |Hayır        |
|[Gümüş veya altın bulut platformu uzmanlık düzeyi](/partner-center/learn-about-competencies) veya [Azure uzmanı msp](https://partner.microsoft.com/membership/azure-expert-msp) gerektirir      |Yes         |Hayır         |
|Azure Marketi aracılığıyla yeni müşteriler tarafından kullanılabilir     |Yes     |Hayır       |
|Teklifi belirli müşterilerle sınırlayabilir     |Evet (yalnızca özel tekliflerle birlikte, bulut çözümü sağlayıcısı (CSP) programının satıcısı ile belirlenen aboneliklerle birlikte kullanılamaz)         |Yes         |
|Azure portal müşteri kabulünü gerektirir     |Yes     |Hayır   |
|Birden çok abonelik, kaynak grubu veya müşteri eklemek için Otomasyonu kullanabilir |Hayır     |Yes    |
|Yeni yerleşik rollere ve Azure ışıklı kullanım özelliklerine anında erişin     |Her zaman değil (genellikle bir gecikmeden sonra kullanılabilir)         |Yes         |
|Müşteriler Azure portal güncelleştirilmiş teklifleri gözden geçirebilir ve kabul edebilir | Yes | Hayır |

> [!NOTE]
> Yönetilen hizmet teklifleri, Azure Kamu ve diğer ulusal bulutlarda kullanılamaz.

## <a name="create-your-offer"></a>Teklifinizi oluşturma

Sağlamanız gereken tüm bilgiler ve varlıklar dahil olmak üzere teklifinizi oluşturma hakkında ayrıntılı yönergeler için bkz. [yönetilen hizmet teklifi oluşturma](../../marketplace/create-managed-service-offer.md).

Genel yayımlama süreci hakkında bilgi edinmek için [ticari Market belgelerini](../../marketplace/overview.md)gözden geçirin. Ayrıca, özellikle [yönetilen hizmetler](/legal/marketplace/certification-policies#700-managed-services) bölümünü [ticari Market sertifika ilkelerini](/legal/marketplace/certification-policies)gözden geçirmeniz gerekir.

Müşteri teklifinizi ekledikten sonra, bir veya daha fazla abonelik veya kaynak grubu temsilciliğini sağlayacak ve bu, [Azure açık eklendi](#the-customer-onboarding-process).

> [!IMPORTANT]
> Yönetilen bir hizmet teklifinin her bir planı, kiracınızda bu planı satın alan müşteriler için atanan kaynak gruplarına ve/veya aboneliklerine erişimi olacak Azure Active Directory (Azure AD) varlıklarını tanımladığınız bir **bildirim ayrıntıları** bölümü içerir. Eklediğiniz herhangi bir grup (veya Kullanıcı veya hizmet sorumlusu), planı satın alan her müşteri için aynı izinlere sahip olacağını unutmamak önemlidir. Her müşteriyle çalışacak farklı gruplar atamak için, her müşteri için özel olarak özel bir [plan](../../marketplace/private-offers.md) yayımlayabilirsiniz. Özel planların, bulut çözümü sağlayıcısı (CSP) programının satıcısı aracılığıyla oluşturulan aboneliklerle desteklenmediğini göz önünde bulundurun.

## <a name="publish-your-offer"></a>Teklifinizi yayımlayın

Tüm bölümleri tamamladıktan sonra, bir sonraki adımınız teklifi Azure Marketi 'ne yayımlamaktır. Teklifinizi canlı hale getirme sürecini başlatmak için **Yayımla** düğmesini seçin. Bu işlemle ilgili daha fazla bilgi [burada](../../marketplace/review-publish-offer.md)bulunabilir.

[Teklifinizin güncelleştirilmiş bir sürümünü](../..//marketplace/partner-center-portal/update-existing-offer.md) dilediğiniz zaman yayımlayabilirsiniz. Örneğin, daha önce yayımlanmış bir teklifine yeni bir rol tanımı eklemek isteyebilirsiniz. Bunu yaptığınızda, teklifi zaten eklemiş olan müşteriler, bir güncelleştirmenin kullanılabildiğini bilmesini sağlayan Azure portal [**hizmet sağlayıcıları**](view-manage-service-providers.md) sayfasında bir simge görür. Her müşteri [, değişiklikleri gözden](view-manage-service-providers.md#update-service-provider-offers) geçirebilecek ve yeni sürüme güncelleştirmek isteyip istemediğinize karar verecek. 

## <a name="the-customer-onboarding-process"></a>Müşteri ekleme işlemi

Bir müşteri teklifinizi ekledikten sonra, [bir veya daha fazla belirli abonelik veya kaynak grubu temsilciliğini](view-manage-service-providers.md#delegate-resources)sağlayacak ve bu, Azure açık eklendi. Bir müşteri bir teklifi kabul etmiş, ancak henüz hiç kaynak temsilcisi yoksa, Azure portal [**hizmet sağlayıcılar**](view-manage-service-providers.md) sayfasının en üstünde **sağlayıcı teklifleri** bölümünün üst kısmında bir not görür.

> [!IMPORTANT]
> Temsilcinin, izin içeren bir rolü olan `Microsoft.Authorization/roleAssignments/write` (veya eklendi olan kaynak gruplarını içeren) bir rol içeren, müşterinin kiracısında Konuk olmayan [](../../role-based-access-control/built-in-roles.md#owner)bir hesap olması gerekir. Aboneliği temsil edebilen kullanıcıları bulmak için, müşterinin kiracısındaki bir Kullanıcı Azure portal aboneliği seçebilir, **erişim denetimini (IAM)** açabilir ve [sahip rolüne sahip tüm kullanıcıları görüntüleyebilir](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Müşteri bir abonelik (veya bir abonelik içindeki bir veya daha fazla kaynak grubu) atadıktan sonra, bu abonelik için **Microsoft. ManagedServices** kaynak sağlayıcısı kaydedilir ve kiracınızdaki kullanıcılar, teklifinizdeki Yetkilendirmelere göre Temsilcili kaynaklara erişebilir.

Teklifinizin güncelleştirilmiş bir sürümünü yayımlarsanız, müşteri [Azure Portal değişiklikleri gözden geçirebilir ve yeni sürümü kabul](view-manage-service-providers.md#update-service-provider-offers)edebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market](../../marketplace/overview.md)hakkında bilgi edinin.
- Müşteri görevlendirmeleri genelinde etkileri izlemek için [iş ortağı kimliğinizi bağlayın](partner-earned-credit.md) .
- [Çapraz kiracı yönetim deneyimleri](../concepts/cross-tenant-management-experience.md)hakkında bilgi edinin.
- Azure portal **müşterilerime** giderek [müşterileri görüntüleyin ve yönetin](view-manage-customers.md) .
