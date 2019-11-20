---
title: Service Fabric yönetilen kimliğe genel bakış | Microsoft Docs
description: Bu makale, yönetilen kimliğe bir genel bakış.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: e2f05ba28109a9b3c88d80eb218bf94014f0f082
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73243033"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Service Fabric uygulaması için yönetilen kimlik (Önizleme)

Bulut uygulamaları oluştururken yaygın olarak karşılaşılan bir zorluk, bulut hizmetlerinde kimlik doğrulaması yapmak için kodunuzda bulunan kimlik bilgilerinin yönetimidir. Kimlik bilgilerinin güvenli tutulması, geliştirici iş istasyonlarında hiç görünmeyeceğinden ve kaynak denetimine iade etmeyeceğinden önemli bir görevdir. Azure Active Directory (Azure AD) içindeki Azure kaynakları için yönetilen kimlik özelliği bu sorunu çözer. Bu özellik, Azure hizmetlerine Azure AD üzerinde otomatik olarak yönetilen bir kimlik sağlar. Bu kimliği kullanarak, Key Vault da dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen tüm hizmetlerde kodunuzda kimlik bilgileri olmadan kimlik doğrulaması yapabilirsiniz.

Azure kaynakları için yönetilen kimlik özelliği Azure abonelikleri için Azure AD ile ücretsizdir. Ek bir maliyet yoktur.

> [!NOTE]
> Azure kaynakları için yönetilen kimlik, daha önce Yönetilen Hizmet Kimliği (MSI) olarak bilinen hizmetin yeni adıdır.

## <a name="terminology"></a>Terminoloji

Aşağıdaki terimler Azure kaynakları için yönetilen kimlik belge kümesi boyunca kullanılır:

- **ISTEMCI kimliği** -Azure AD tarafından oluşturulan ve ilk sağlama sırasında bir uygulamaya ve hizmet sorumlusuna bağlı olan benzersiz bir tanımlayıcı (Ayrıca bkz. [uygulama kimliği](/azure/active-directory/develop/developer-glossary#application-id-client-id).)

- **Asıl kimlik** -Azure kaynağına rol tabanlı erişim sağlamak Için kullanılan yönetilen Kimliğiniz için hizmet sorumlusu NESNESININ nesne kimliği.

- **Hizmet sorumlusu** -belirli bir KIRACıDA bir AAD uygulamasının projeksiyonunu temsil eden bir Azure Active Directory nesnesi (Ayrıca bkz. [hizmet sorumlusu](../active-directory/develop/developer-glossary.md#service-principal-object).)


## <a name="about-managed-identities-in-azure"></a>Azure 'da Yönetilen kimlikler hakkında

- [Azure 'da yönetilen kimlik türleri (mı)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [Azure 'da sistem tarafından atanan yönetilen kimlik nasıl çalışır?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [Kullanıcı tanımlı yönetilen kimlik (mı) Azure 'da nasıl çalışır?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric uygulamalar için desteklenen senaryolar

Service Fabric için Yönetilen kimlikler yalnızca Azure tarafından dağıtılan Service Fabric kümelerinde ve yalnızca Azure kaynakları olarak dağıtılan uygulamalarda desteklenir; Azure kaynağı olarak dağıtılmayan bir uygulamaya bir kimlik atanamaz. Kavramsal konuşma, Azure Service Fabric kümesindeki Yönetilen kimlikler için destek iki aşamadan oluşur:

1. Uygulama kaynağına bir veya daha fazla yönetilen kimlik atayın; bir uygulamaya, sırasıyla, tek bir sistem tarafından atanan kimlik ve/veya en fazla 32 Kullanıcı tarafından atanan kimlik atanabilir.

2. Uygulamanın tanımı içinde, uygulamaya atanan kimliklerden birini uygulamayı kapsayan her bir hizmete eşleyin.

Uygulamanın sistem tarafından atanan kimliği, bu uygulamaya özeldir; Kullanıcı tarafından atanan bir kimlik birden çok uygulamaya atanabilecek tek başına kaynaktır. Bir uygulama içinde, tek bir kimlik (sistem tarafından atanan veya Kullanıcı atanmamış) uygulamanın birden çok hizmetine atanabilir, ancak her bir hizmete yalnızca bir kimlik atanabilir. Son olarak, bu özelliğe erişmek için bir hizmete açıkça bir kimlik atanmalıdır. Aslında, bir uygulamanın kimliklerinin bileşen hizmetlerine eşlenmesi, uygulama içi yalıtımına izin verir. bir hizmet yalnızca kendisiyle eşleştirilmiş kimliği kullanabilir (ve açıkça bir atanmamışsa yoksa hiç hiçbirini etkilemez.)  

Önizleme sürümü için desteklenen senaryoların listesi aşağıdaki gibidir:

   - Bir veya daha fazla hizmet ile yeni bir uygulama dağıtın ve bir veya daha fazla atanan kimlik

   - Azure kaynaklarına erişmek için mevcut bir uygulamaya bir veya daha fazla yönetilen kimlik atayın; uygulamanın bir Azure kaynağı olarak dağıtılmış olması gerekir


Aşağıdaki senaryolar desteklenmez veya önerilmez; Not Bu eylemler engellenmeyebilir, ancak uygulamalarınızda kesintilere yol açabilir:

   - Bir uygulamaya atanan kimlikleri kaldırma veya değiştirme; değişiklik yapmanız gerekiyorsa, ilk olarak yeni bir kimlik ataması eklemek ve ardından daha önce atanmış bir tane kaldırmak için ayrı dağıtımlar göndermeniz gerekir. Mevcut bir uygulamadan bir kimliğin kaldırılması, uygulamanızı yükseltilemeyen bir durumda bırakmak dahil istenmeyen etkileri olabilir. Bir kimliğin kaldırılması gerekliyse uygulamayı tamamen silmek güvenlidir; Bu işlem, uygulamayla ilişkili sistem tarafından atanan kimliği (tanımlanmışsa) silecek ve uygulamaya atanan kullanıcı tarafından atanan kimliklerle tüm ilişkilendirmeleri kaldıracak.

   - Yönetilen kimlikler için SF desteği şu anda [AzureServiceTokenProvider](../key-vault/service-to-service-authentication.md)ile tümleştirilmiştir; Tümleştirme, yönetilen kimlik özelliği için Önizleme döneminin sonuna kadar elde edilir.

>
> [!NOTE]
>
> Bu özellik önizlemededir; Bu nedenle, sık sık değişikliklere tabi olabilir ve üretim dağıtımları için uygun olmayabilir.

## <a name="next-steps"></a>Sonraki adımlar
* [Yönetilen kimlik desteği ile yeni bir Azure Service Fabric kümesi dağıtma](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [Mevcut bir Azure Service Fabric kümesinde yönetilen kimlik desteğini etkinleştir](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [Sistem tarafından atanan yönetilen kimlik ile Azure Service Fabric uygulaması dağıtma](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Kullanıcı tarafından atanan yönetilen kimlik ile bir Azure Service Fabric uygulaması dağıtma](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Hizmet kodundan Service Fabric uygulamasının yönetilen kimliğinden yararlanın](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric uygulamasına diğer Azure kaynaklarına erişim izni verme](./how-to-grant-access-other-resources.md)
* [Uygulama gizli dizilerini KeyVaultReferences olarak bildirme ve kullanma](./service-fabric-keyvault-references.md) 
