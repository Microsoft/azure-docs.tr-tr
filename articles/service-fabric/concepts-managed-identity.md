---
title: Azure için Yönetilen kimlikler
description: Service Fabric ile Azure için Yönetilen kimlikler kullanma hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 71fa9ef4da8081a167b68553e2e3eac8477e8aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881729"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Service Fabric ile Azure için Yönetilen kimlikler kullanma

Bulut uygulamaları derlerken ortak bir zorluk, bir geliştirici iş istasyonuna veya kaynak denetiminde yerel olarak kaydedilmeksizin çeşitli hizmetlere kimlik doğrulaması yapmak için kodunuzda kimlik bilgilerini güvenli bir şekilde yönetme. *Azure Için Yönetilen kimlikler* , Azure AD 'de otomatik olarak yönetilen kimlikler sunarak Azure Active Directory (Azure AD) içindeki tüm kaynaklarınız için bu sorunu çözün. Kodunuzda depolanan kimlik bilgileri olmadan Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için bir hizmetin kimliğini kullanabilirsiniz.

Azure *kaynakları Için Yönetilen kimlikler* Azure abonelikleri IÇIN Azure AD ile ücretsizdir. Ek ücret alınmaz.

> [!NOTE]
> *Azure Için Yönetilen kimlikler* , daha önce YÖNETILEN HIZMET KIMLIĞI (MSI) olarak bilinen hizmetin yeni adıdır.

## <a name="concepts"></a>Kavramlar

Azure için Yönetilen kimlikler çeşitli önemli kavramlara dayalıdır:

- **ISTEMCI kimliği** -Azure AD tarafından oluşturulan ve ilk sağlama sırasında bir uygulamaya ve hizmet sorumlusuna bağlı olan benzersiz bir tanımlayıcı (Ayrıca bkz. [uygulama kimliği](../active-directory/develop/developer-glossary.md#application-id-client-id).)

- **Asıl kimlik** -Azure kaynağına rol tabanlı erişim sağlamak Için kullanılan yönetilen Kimliğiniz için hizmet sorumlusu NESNESININ nesne kimliği.

- **Hizmet sorumlusu** -belirli bir KIRACıDA bir AAD uygulamasının projeksiyonunu temsil eden bir Azure Active Directory nesnesi (Ayrıca bkz. [hizmet sorumlusu](../active-directory/develop/developer-glossary.md#service-principal-object).)

İki tür yönetilen kimlik vardır:

- **Sistem tarafından atanan yönetilen kimlik** doğrudan bir Azure hizmeti örneği üzerinde etkinleştirilir.  Sistem tarafından atanan kimliğin yaşam döngüsü, etkinleştirilmiş olduğu Azure hizmet örneği için benzersizdir.
- **Kullanıcı tarafından atanan yönetilen kimlik**, tek başına bir Azure kaynağı olarak oluşturulur. Kimlik bir veya daha fazla Azure hizmet örneğine atanabilir ve bu örneklerin yaşam döngülerinden ayrı olarak yönetilir.

Yönetilen kimlik türleri arasındaki farkı daha fazla anlamak için bkz. [Azure kaynakları için Yönetilen kimlikler nasıl çalışır?](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric uygulamalar için desteklenen senaryolar

Service Fabric Yönetilen kimlikler yalnızca Azure tarafından dağıtılan Service Fabric kümelerinde ve yalnızca Azure kaynakları olarak dağıtılan uygulamalar için desteklenir; Azure kaynağı olarak dağıtılmayan bir uygulamaya bir kimlik atanamaz. Kavramsal konuşma, bir Azure Service Fabric kümesindeki Yönetilen kimlikler için destek iki aşamadan oluşur:

1. Uygulama kaynağına bir veya daha fazla yönetilen kimlik atayın; bir uygulamaya, sırasıyla, tek bir sistem tarafından atanan kimlik ve/veya en fazla 32 Kullanıcı tarafından atanan kimlik atanabilir.

2. Uygulamanın tanımı içinde, uygulamaya atanan kimliklerden birini uygulamayı kapsayan her bir hizmete eşleyin.

Uygulamanın sistem tarafından atanan kimliği, bu uygulamaya özeldir; Kullanıcı tarafından atanan bir kimlik birden çok uygulamaya atanabilecek tek başına kaynaktır. Bir uygulama içinde, tek bir kimlik (sistem tarafından atanan veya Kullanıcı atanmamış) uygulamanın birden çok hizmetine atanabilir, ancak her bir hizmete yalnızca bir kimlik atanabilir. Son olarak, bu özelliğe erişmek için bir hizmete açıkça bir kimlik atanmalıdır. Aslında, bir uygulamanın kimliklerinin bileşen hizmetlerine eşlenmesi, uygulama içi yalıtımına izin veriyor. bir hizmet yalnızca kendisiyle eşleştirilmiş kimliği kullanabilir.  

Şu anda bu özellik için aşağıdaki senaryolar desteklenir:

- Bir veya daha fazla hizmet ile bir veya daha fazla atanan kimlik ile yeni bir uygulama dağıtın

- Azure kaynaklarına erişebilmek için bir veya daha fazla yönetilen kimliği mevcut (Azure tarafından dağıtılan) bir uygulamaya atama

Aşağıdaki senaryolar desteklenmez veya önerilmez; Not Bu eylemler engellenmeyebilir, ancak uygulamalarınızda kesintilere yol açabilir:

- Bir uygulamaya atanan kimlikleri kaldırma veya değiştirme; değişiklik yapmanız gerekiyorsa, ilk olarak yeni bir kimlik ataması eklemek ve ardından daha önce atanmış bir tane kaldırmak için ayrı dağıtımlar göndermeniz gerekir. Mevcut bir uygulamadan bir kimliğin kaldırılması, uygulamanızı yükseltilemeyen bir durumda bırakmak dahil istenmeyen etkileri olabilir. Bir kimliğin kaldırılması gerekliyse uygulamayı tamamen silmek güvenlidir; Bu işlem, uygulamayla ilişkili sistem tarafından atanan kimliği (tanımlanmışsa) silecek ve uygulamaya atanan kullanıcı tarafından atanan kimliklerle tüm ilişkilendirmeleri kaldıracak.

- Yönetilen kimlikler için Service Fabric desteği şu anda [AzureServiceTokenProvider](/dotnet/api/overview/azure/service-to-service-authentication)'e tümleştirilmiştir.

## <a name="next-steps"></a>Sonraki adımlar

- [Yönetilen kimlik desteği ile yeni bir Azure Service Fabric kümesi dağıtma](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Mevcut bir Azure Service Fabric kümesinde yönetilen kimlik desteğini etkinleştir](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Sistem tarafından atanan yönetilen kimlik ile Azure Service Fabric uygulaması dağıtma](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Kullanıcı tarafından atanan yönetilen kimlik ile bir Azure Service Fabric uygulaması dağıtma](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Hizmet kodundan Service Fabric uygulamasının yönetilen kimliğinden yararlanın](./how-to-managed-identity-service-fabric-app-code.md)
- [Azure Service Fabric uygulamasına diğer Azure kaynaklarına erişim izni verme](./how-to-grant-access-other-resources.md)
- [Uygulama gizli dizilerini KeyVaultReferences olarak bildirme ve kullanma](./service-fabric-keyvault-references.md)