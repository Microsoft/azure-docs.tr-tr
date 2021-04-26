---
title: Azure aboneliklerini aboneler ile CSP'ler arasında aktarma
description: Azure aboneliklerini aboneler ile CSP'ler arasında nasıl aktarabileceğinizi öğrenin.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: 687db46ea2f6c9c4fae6e5355e3236cde3d7a401
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567256"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Azure aboneliklerini aboneler ile CSP'ler arasında aktarma

Bu makalede Azure aboneliklerini Bulut Çözümü Sağlayıcıları (CSP) iş ortakları ile müşterileri arasında aktarmayla ilgili üst düzey adımlara yer verilmiştir. Buradaki bilgiler Azure abonesinin iş ortağıyla eşgüdümlü çalışmasına yardım etmek için hazırlanmıştır. Microsoft iş ortaklarının aktarım işleminde kullandığı bilgiler [Müşterinin Azure aboneliklerini başka bir iş ortağına aktarmayı öğrenin](/partner-center/switch-azure-subscriptions-to-a-different-partner) başlığı altında belgelenmiştir.

Aktarım isteğine başlamadan önce, saklamak istediğiniz tüm maliyet ve faturalama bilgilerini indirmeniz veya dışarı aktarmanız gerekir. Faturalama ve kullanım bilgileri abonelikle birlikte aktarılmaz. Maliyet yönetimi verilerini dışarı aktarma hakkında daha fazla bilgi için bkz. [Dışarı aktarılan verileri oluşturma ve yönetme](../costs/tutorial-export-acm-data.md). Faturanızı ve kullanım verilerinizi indirme hakkında daha fazla bilgi için bkz. [Azure faturanızı ve günlük kullanım verilerinizi indirme veya görüntüleme](download-azure-invoice-daily-usage-date.md).

Mevcut rezervasyonlar varsa, bir aboneliği aktardıktan sonra 90 gün uygulamaktan sonra bu işlemleri durdurur. 90 gün yetkisiz kullanım süresinden sonra ücretlerden kaçınmak için, bir aboneliği aktarmadan önce [Tüm rezervasyonları iptal ettiğinizden ve bunları geri ödediğinizden](../reservations/exchange-and-refund-azure-reservations.md) emin olun.

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>CSP iş ortağına EA aboneliklerinin aktarılması

[Azure Uzman Yönetilen Hizmetler Sağlayıcısı (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) sertifikalı CSP doğrudan faturalandırma iş ortakları, Doğrudan Kurumsal Anlaşma (EA) sahibi olan müşterilerinin Azure aboneliklerinin aktarılması isteğinde bulunabilirler. Abonelik aktarma işlemleri yalnızca kabul edilen bir Microsoft Müşteri Sözleşmesi'ne (MCA) sahip olan ve CSP Programı kapsamında bir Azure planı satın almış olan müşteriler tarafından gerçekleştirilebilir.

İstek onaylandıktan sonra CSP, müşterilerine birleştirilmiş fatura sunabilir. CSP'lerin abonelikleri aktarması hakkında daha fazla bilgi için bkz. [MPA hesabınız için Azure aboneliklerinin fatura sahipliğini alma](mpa-request-ownership.md).

>[!IMPORTANT]
> EA aboneliğini CSP iş ortağına aktardıktan sonra, daha önce EA aboneliğine uygulanan tüm kota artışları varsayılan değere sıfırlanır. Abonelik aktarımından sonra kota artışı gerekiyorsa, CSP sağlayıcınızın [kota artışı](../../azure-portal/supportability/regional-quota-requests.md) isteği göndermesini sağlayabilirsiniz. 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>CSP iş ortağına diğer aboneliklerin aktarılması

Diğer Azure aboneliklerini CSP iş ortağına aktarmak isteyen abonenin kaynak aboneliklerindeki kaynakları CSP aboneliklerine taşıması gerekir. Kaynakları abonelikler arasında taşımak için aşağıdaki yönergeleri izleyin.

1. Müşteriyle bir [Satıcı ilişkisi](/partner-center/request-a-relationship-with-a-customer) oluşturun. Hem müşteri hem de Iş ortağı kiracının aynı yetkili bölgelerde olduğundan emin olmak için [CSP bölgesel yetkilendirme genel bakışını](/partner-center/regional-authorization-overview) gözden geçirin.
1. CSP iş ortağınızla birlikte çalışarak hedef Azure CSP aboneliklerini oluşturun.
1. Kaynak ve hedef CSP aboneliklerinin aynı Azure Active Directory (Azure AD) kiracısında olduğundan emin olun.  
    Azure CSP aboneliğinin Azure AD kiracısını değiştiremezsiniz. Bunun yerine kaynak aboneliği CSP Azure AD kiracısına eklemeniz veya onunla ilişkilendirmeniz gerekir. Daha fazla bilgi için bkz. [Azure Active Directory kiracınıza bir Azure aboneliğini ekleme veya ilişkilendirme](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - Aboneliği farklı bir Azure AD dizini ile ilişkilendirdiğinizde [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) ile atanmış rollere sahip olan kullanıcılar erişimlerini kaybeder. Hizmet Yöneticisi ve Ortak Yöneticiler dahil olmak üzere klasik abonelik yöneticileri de erişimi kaybeder.
    > - Abonelik farklı bir dizinle ilişkilendirildiğinde İlke Atamaları da abonelikten kaldırılır.
1. Aktarımı gerçekleştirmek için kullandığınız kullanıcı hesabının iki abonelikte de [Azure RBAC](add-change-subscription-administrator.md) sahip erişimi olması gerekir.
1. Başlamadan önce tüm Azure kaynaklarının kaynak abonelikten hedef aboneliğe taşınabileceğini [doğrulayın](/rest/api/resources/resources/validatemoveresources).  
    Bazı Azure kaynakları farklı aboneliklere taşınamaz. Taşınabilecek Azure kaynaklarının tam listesini görüntülemek için bkz. [Kaynaklar için taşıma işlemi desteği](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - Azure CSP yalnızca Azure Resource Manager kaynaklarını destekler. Kaynak abonelikte Azure klasik dağıtım modeli kullanılarak oluşturulan Azure kaynağı varsa geçişten önce [Azure Resource Manager](/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm)'a geçirilmesi gerekir. Web sayfasını görüntülemek için iş ortağı olmanız gerekir.

1. Kaynak abonelik hizmetlerinin tümünün Azure Resource Manager modelini kullandığını doğrulayın. Daha sonra [Azure Kaynağını Taşıma](../../azure-resource-manager/management/move-resource-group-and-subscription.md) bölümünü kullanarak kaynak abonelikteki kaynakları hedef aboneliğe aktarın.
    > [!IMPORTANT]
    >  - Azure kaynaklarını farklı aboneliklere taşımak, aboneliklerinizdeki kaynaklara göre hizmette kesintiye neden olabilir.

## <a name="transfer-csp-subscription-to-other-offer"></a>CSP aboneliğini başka bir teklife aktarma

CSP iş ortağındaki diğer abonelikleri başka bir Azure teklifine taşımak için abonenin kaynak CSP aboneliklerindeki kaynakları hedef aboneliklere taşıması gerekir. Bu, bir iş ortağı ve müşteri tarafından gerçekleştirilen çalışmadır; bir Microsoft temsilcisi tarafından yapılmaz.

1. Müşteri, hedef Azure abonelikleri oluşturur.
1. Kaynak ve hedef aboneliklerin aynı Azure Active Directory (Azure AD) kiracısında olduğundan emin olun. Azure AD kiracısı değiştirme hakkında daha fazla bilgi için bkz. [Azure Active Directory kiracınıza bir Azure aboneliğini ekleme veya ilişkilendirme](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    Dizin değiştirme seçeneğinin CSP aboneliğinde desteklenmediğini unutmayın. Örneğin CSP’den kullandıkça öde aboneliğine aktarıyorsunuz. Kullandıkça öde aboneliğinin dizinini, dizinle eşleşecek şekilde değiştirmelisiniz.

    > [!IMPORTANT]
    >  - Bir aboneliği farklı bir dizin ile ilişkilendirdiğinizde [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) ile atanmış rollere sahip olan kullanıcılar erişimlerini kaybeder. Hizmet Yöneticisi ve Ortak Yöneticiler dahil olmak üzere klasik abonelik yöneticileri de erişimi kaybeder.
    >  - Abonelik farklı bir dizinle ilişkilendirildiğinde İlke Atamaları da abonelikten kaldırılır.

1. Aktarımı yapmak için kullandığınız müşteri Kullanıcı hesabının her iki abonelik üzerinde [Azure RBAC](add-change-subscription-administrator.md) sahibi erişimi olmalıdır.
1. Başlamadan önce tüm Azure kaynaklarının kaynak abonelikten hedef aboneliğe taşınabileceğini [doğrulayın](/rest/api/resources/resources/validatemoveresources).
    > [!IMPORTANT]
    >  - Bazı Azure kaynakları farklı aboneliklere taşınamaz. Taşınabilecek Azure kaynaklarının tam listesini görüntülemek için bkz. [Kaynaklar için taşıma işlemi desteği](../../azure-resource-manager/management/move-support-resources.md).

1. [Azure Kaynağını Taşıma](../../azure-resource-manager/management/move-resource-group-and-subscription.md) bölümünü kullanarak kaynak abonelikteki kaynakları hedef aboneliğe aktarın.
    > [!IMPORTANT]
    >  - Azure kaynaklarını farklı aboneliklere taşımak, aboneliklerinizdeki kaynaklara göre hizmette kesintiye neden olabilir.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure aboneliklerinin fatura sahipliğini MPA hesabınıza alma](mpa-request-ownership.md).
- Bkz. [Azure Faturalandırma ile hesapları ve abonelikleri yönetme](../index.yml).
