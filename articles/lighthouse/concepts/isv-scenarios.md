---
title: ISV senaryolarında Azure ışıklı kullanım
description: Azure açık Thouse özellikleri, ISV 'Ler tarafından müşteri teklifleriyle daha fazla esneklik sağlamak için kullanılabilir.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696389"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>ISV senaryolarında Azure ışıklı kullanım

[Azure Pthouse](../overview.md) için yaygın bir senaryo, müşterilerinin ' Azure Active Directory (Azure AD) kiracılarındaki kaynakları yöneten bir hizmet sağlayıcıdır. Azure ınthouse 'ın özellikleri, bağımsız yazılım satıcıları (ISV) tarafından müşterileri ile SaaS tabanlı teklifler kullanılarak da kullanılabilir. Azure ınthouse, özellikle de abonelik kapsamına erişim gerektiren yönetilen hizmetler veya destek sunan ISV 'Ler için yararlı olabilir.

## <a name="managed-service-offers-in-azure-marketplace"></a>Azure Market’teki Yönetilen Hizmet teklifleri

ISV olarak, Azure Marketi 'Nde zaten çözüm yayımlamış olabilirsiniz. Müşterilerinize yönetilen hizmetler sunabilmeniz için bir yönetilen hizmet teklifi yayımlayarak bunu yapabilirsiniz. Bu, ekleme sürecini kolaylaştırır ve hizmetlerinizi gereken sayıda müşteriyle daha ölçeklenebilir hale getirir. Azure Hothouse, müşterilerinize değer sağlamak için kullanılabilecek çok sayıda [yönetim görevini ve senaryosunu](cross-tenant-management-experience.md#enhanced-services-and-scenarios) destekler.

Daha fazla bilgi için bkz. [Azure Market 'Te yönetilen hizmet teklifi yayımlama](../how-to/publish-managed-services-offers.md).

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>Azure 'un yönettiği uygulamalar ile Azure 'un açık kullanımı

[Azure yönetilen uygulamalar](../../azure-resource-manager/managed-applications/overview.md) , ISV 'lerin müşterilerine hizmet sağlayabildiği başka bir yoldur. Gelişmiş senaryoları etkinleştirmek için Azure 'un yönettiği uygulamalarınızın yanında Azure açık Thouse özelliğini kullanabilirsiniz.

Daha fazla bilgi için bkz. [Azure açık Thouse ve Azure yönetilen uygulamalar](managed-applications.md).

## <a name="saas-based-multi-tenant-offerings"></a>SaaS tabanlı çok kiracılı teklifler

ISV 'nin kendi kiracısındaki bir abonelikteki kaynakları barındırdığı ve daha sonra müşterilerin bu kaynaklara erişmesine izin vermek için Azure Mathouse kullandığı ek bir senaryo vardır. Müşteri daha sonra kendi kiracısında oturum açabilir ve gerektiğinde bu kaynaklara erişebilir. ISV 'Ler IP 'lerinin kendi kiracılarında bakımını sağlar ve kendi destek planlarını kullanarak, kiracılarında barındırılan çözümle ilgili biletleri müşterinin planını kullanmak yerine kullanabilir. Kaynaklar ISV 'nin kiracısında olduğundan, tüm eylemler doğrudan ISV tarafından gerçekleştirilebilir; Örneğin, VM 'Ler üzerinde oturum açma, uygulama yükleme ve bakım görevlerini gerçekleştirme gibi işlemler.

Bu senaryoda, müşterinin kiracısındaki kullanıcılara, müşteri ISV 'nin kaynaklarını yönetmese de, "Kiracı Yönetimi" olarak erişim izni verilir. ISV 'nin kiracısına doğrudan eriştiğinden, müşterilerin çözüme veya diğer ISV kaynaklarına yanlışlıkla değişiklik yapamaması için yalnızca gerekli minimum izinleri vermek önemlidir.

Bu mimariyi etkinleştirmek için ISV 'nin, kiracı KIMLIĞIYLE birlikte müşterinin Azure AD kiracısındaki bir Kullanıcı grubu için nesne KIMLIĞINI alması gerekir. ISV daha sonra bu kullanıcı grubuna uygun izinleri veren bir ARM şablonu oluşturur ve müşterinin erişebileceği kaynakları içeren [ISV 'nin aboneliğine dağıtır](../how-to/onboard-customer.md) .

## <a name="next-steps"></a>Sonraki adımlar

- [Çapraz kiracı yönetim deneyimleri](cross-tenant-management-experience.md)hakkında bilgi edinin.
- [Azure tarafından atanan temsilcinin kaynak yönetimi](azure-delegated-resource-management.md) hakkında bilgi edinin.