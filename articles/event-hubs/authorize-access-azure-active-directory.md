---
title: Azure Active Directory'ye erişimi yetkilendirme
description: Bu makalede Azure Active Directory kullanarak Event Hubs kaynaklarına erişimi yetkilendirme hakkında bilgi sağlanır.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 0d8d1b37e7f5ebb6eef1c76e4324041c48ab8986
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995738"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Azure Active Directory kullanarak Event Hubs kaynaklarına erişim yetkisi verme
Azure Event Hubs, istekleri Event Hubs kaynaklara yetkilendirmek için Azure Active Directory (Azure AD) kullanılmasını destekler. Azure AD ile rol tabanlı erişim denetimi 'ni (RBAC), bir kullanıcı veya uygulama hizmeti sorumlusu olabilecek bir güvenlik sorumlusu için izin vermek üzere kullanabilirsiniz. Roller ve rol atamaları hakkında daha fazla bilgi edinmek için bkz. [farklı rolleri anlama](../role-based-access-control/overview.md).

## <a name="overview"></a>Genel Bakış
Bir güvenlik sorumlusu (bir kullanıcı veya uygulama) bir Event Hubs kaynağına erişmeyi denediğinde, isteğin yetkilendirilmiş olması gerekir. Azure AD ile bir kaynağa erişim iki adımlı bir işlemdir. 

 1. İlk olarak, güvenlik sorumlusunun kimliği doğrulanır ve bir OAuth 2,0 belirteci döndürülür. Belirteç istemek için kaynak adı `https://eventhubs.azure.net/`.
 1. Ardından, belirteç, belirtilen kaynağa erişim yetkisi vermek için Event Hubs hizmetine bir isteğin bir parçası olarak geçirilir.

Kimlik doğrulama adımı, bir uygulama isteğinin çalışma zamanında bir OAuth 2,0 erişim belirteci içermesi gerekir. Bir uygulama bir Azure VM 'si, bir sanal makine ölçek kümesi veya bir Azure Işlev uygulaması gibi bir Azure varlığı içinde çalışıyorsa, kaynaklara erişmek için yönetilen bir kimlik kullanabilir. Yönetilen bir kimlik tarafından Event Hubs hizmetine yapılan isteklerin nasıl doğrulanabilmesi hakkında bilgi edinmek için bkz. [Azure kaynakları için Azure Active Directory ve yönetilen kimlikler Ile azure Event Hubs kaynaklarına erişim kimlik doğrulaması](authenticate-managed-identity.md). 

Yetkilendirme adımı, güvenlik sorumlusuna bir veya daha fazla RBAC rolünün atanmasını gerektirir. Azure Event Hubs, Event Hubs kaynakları için izin kümelerini çevreleyen RBAC rolleri sağlar. Bir güvenlik sorumlusu 'na atanan roller, sorumlunun sahip olacağı izinleri belirleyebilir. RBAC rolleri hakkında daha fazla bilgi için bkz. [Azure Event Hubs Için YERLEŞIK RBAC rolleri](#built-in-rbac-roles-for-azure-event-hubs). 

Event Hubs istek yapan yerel uygulamalar ve Web uygulamaları Azure AD ile de yetki verebilir. Erişim belirteci isteme ve bu uygulamayı Event Hubs kaynaklara yönelik istekleri yetkilendirmek üzere kullanma hakkında bilgi edinmek için bkz. [bir uygulamadan Azure AD Ile azure Event Hubs erişim kimlik doğrulaması](authenticate-application.md). 

## <a name="assign-rbac-roles-for-access-rights"></a>Erişim hakları için RBAC rolleri atama
Azure Active Directory (Azure AD), [rol tabanlı erişim denetimi (RBAC)](../role-based-access-control/overview.md)aracılığıyla güvenli kaynaklara erişim haklarını yetkilendirir. Azure Event Hubs, Olay Hub 'ı verilerine erişmek için kullanılan ortak izin kümelerini çevreleyen yerleşik RBAC rollerinin bir kümesini tanımlar ve verilere erişmek için özel roller de tanımlayabilir.

Azure AD güvenlik sorumlusuna bir RBAC rolü atandığında Azure, bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, abonelik düzeyi, kaynak grubu, Event Hubs ad alanı veya bunun altındaki herhangi bir kaynak kapsamına eklenebilir. Azure AD güvenlik sorumlusu, bir kullanıcı veya bir uygulama hizmeti sorumlusu ya da [Azure kaynakları için yönetilen bir kimlik](../active-directory/managed-identities-azure-resources/overview.md)olabilir.

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Azure Event Hubs için yerleşik RBAC rolleri
Azure, Azure AD ve OAuth kullanarak Event Hubs verilerine erişim yetkilendirmek için aşağıdaki yerleşik RBAC rollerini sağlar:

- [Azure Event Hubs veri sahibi](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Event Hubs kaynaklarına yönelik tüm erişimi sağlamak için bu rolü kullanın.
- [Azure Event Hubs veri gönderici](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Event Hubs kaynaklarına gönderme erişimi vermek için bu rolü kullanın.
- [Azure Event Hubs veri alıcısı](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Event Hubs kaynaklarına erişim/alan erişimi sağlamak için bu rolü kullanın.

## <a name="resource-scope"></a>Kaynak kapsamı 
Bir güvenlik sorumlusuna RBAC rolü atamadan önce, güvenlik sorumlusunun sahip olması gereken erişimin kapsamını saptayın. En iyi uygulamalar, yalnızca en dar olası kapsamı sağlamak için her zaman en iyi seçenektir.

Aşağıdaki listede, en dar kapsamdan başlayarak Event Hubs kaynaklarına erişimi kapsamındaki düzeyler açıklanmaktadır:

- **Tüketici grubu**: Bu kapsamda, rol ataması yalnızca bu varlık için geçerlidir. Şu anda Azure portal, bu düzeyde güvenlik sorumlusuna RBAC rolü atamayı desteklemez. 
- **Olay Hub 'ı**: Rol ataması, Olay Hub 'ı varlığı ve altındaki Tüketici grubu için geçerlidir.
- **Ad alanı**: Rol ataması, ad alanı altındaki tüm Event Hubs topolojisine ve onunla ilişkili tüketici grubuna yayılır.
- **Kaynak grubu**: Rol ataması, kaynak grubu altındaki tüm Event Hubs kaynaklarına uygulanır.
- **Abonelik**: Rol ataması, abonelikteki tüm kaynak gruplarındaki tüm Event Hubs kaynaklara uygulanır.

> [!NOTE]
> RBAC rol atamalarının yaymanın beş dakika sürebileceğini aklınızda bulundurun. 

Yerleşik rollerin nasıl tanımlandığı hakkında daha fazla bilgi için bkz. [rol tanımlarını anlama](../role-based-access-control/role-definitions.md#management-and-data-operations). Özel RBAC rolleri oluşturma hakkında daha fazla bilgi için bkz. [Azure rol tabanlı Access Control için özel roller oluşturma](../role-based-access-control/custom-roles.md).

## <a name="next-steps"></a>Sonraki adımlar
- Bir güvenlik sorumlusuna yerleşik bir RBAC rolü atamayı öğrenin, bkz. [Azure Active Directory kullanarak Event Hubs kaynaklarına erişim kimlik doğrulaması](authenticate-application.md).
- [RBAC ile özel roller oluşturmayı](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)öğrenin.
- [Azure ACTIVE DIRECTORY Eh ile kullanmayı](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK) öğrenin

Aşağıdaki ilgili makalelere bakın:

- [Azure Active Directory kullanarak bir uygulamadan Azure Event Hubs istek kimliklerini doğrulama](authenticate-application.md)
- [Event Hubs kaynaklara erişmek için Azure Active Directory ile yönetilen bir kimliğin kimliğini doğrulama](authenticate-managed-identity.md)
- [Paylaşılan erişim Imzalarını kullanarak Azure Event Hubs istek kimliklerini doğrulama](authenticate-shared-access-signature.md)
- [Paylaşılan erişim Imzalarını kullanarak Event Hubs kaynaklarına erişim yetkisi verme](authorize-access-shared-access-signature.md)