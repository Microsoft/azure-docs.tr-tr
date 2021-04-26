---
title: Bulut çözümü sağlayıcıları için Azure AD Domain Services | Microsoft Docs
description: Azure bulut çözümü sağlayıcıları için Azure Active Directory Domain Services yönetilen etki alanlarını etkinleştirme ve yönetme hakkında bilgi edinin
services: active-directory-ds
author: justinha
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: d8edafff9b6534e5f1ce1c4581595ee187dfd432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619908"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Azure bulut çözümü sağlayıcıları için Azure Active Directory Domain Services dağıtımı ve yönetimi

Azure bulut çözümü sağlayıcıları (CSP), Microsoft Iş ortakları için bir programdır ve çeşitli Microsoft bulut hizmetleri için bir lisans kanalı sağlar. Azure CSP, iş ortaklarının satışları yönetmesine, fatura ilişkisine sahip olmasını, teknik ve faturalandırma desteği sağlamasını ve müşterinin tek iletişim noktası olmasını sağlar. Ayrıca, Azure CSP, bir self servis portalı ve eşlik eden API 'Ler dahil olmak üzere tam bir araç kümesi sağlar. Bu araçlar, CSP iş ortaklarının Azure kaynaklarını kolayca sağlamanıza ve yönetmesine ve müşterilere ve aboneliklerine yönelik faturalandırma sağlamasına imkan tanır.

[Iş Ortağı Merkezi portalı](/partner-center/azure-plan-lp) , tüm Azure CSP iş ortaklarının giriş noktasıdır ve zengin müşteri yönetim özellikleri, otomatikleştirilmiş işleme ve daha fazlasını sağlar. Azure CSP iş ortakları, Web tabanlı bir kullanıcı arabirimi veya PowerShell ve çeşitli API çağrıları kullanarak Iş Ortağı Merkezi özelliklerini kullanabilir.

Aşağıdaki diyagramda, CSP modelinin yüksek düzeyde nasıl çalıştığı gösterilmektedir. Contoso 'da bir Azure Active Directory (Azure AD) kiracısı vardır. Azure CSP aboneliklerinde kaynakları dağıtan ve yöneten CSP ile bir iş ortaklığı vardır. Contoso ayrıca doğrudan contoso 'da faturalandırılan normal (doğrudan) Azure aboneliklerine sahip olabilir.

![CSP modeline genel bakış](./media/csp/csp_model_overview.png)

CSP iş ortağının kiracısında üç özel aracı grubu vardır- *yönetici* aracıları, *Yardım Masası* aracıları ve *Satış* aracıları.

*Yönetici* aracıları grubu, contoso Azure AD kiracısında kiracı yöneticisi rolüne atanır. Sonuç olarak, CSP iş ortağının yönetici aracıları grubuna ait olan bir kullanıcının, contoso Azure AD kiracısında kiracı yöneticisi ayrıcalıkları vardır.

CSP iş ortağı Contoso için bir Azure CSP aboneliği sağlarken, yönetici aracıları grubu bu abonelik için sahip rolüne atanır. Sonuç olarak, CSP ortağının yönetim aracıları, contoso adına sanal makineler, sanal ağlar ve Azure AD Domain Services gibi Azure kaynaklarını sağlamak için gerekli ayrıcalıklara sahiptir.

Daha fazla bilgi için bkz. [Azure CSP genel bakış](/partner-center/azure-plan-lp)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Azure AD DS Azure CSP aboneliğinde kullanmanın avantajları

Azure Active Directory Domain Services (Azure AD DS), Windows Server Active Directory Domain Services ile tamamen uyumlu etki alanına katılması, Grup ilkesi, LDAP, Kerberos/NTLM kimlik doğrulaması gibi yönetilen etki alanı Hizmetleri sağlar. Çok sayıda uygulama, bu özellikleri kullanarak AD 'ye karşı çalışmak üzere oluşturulmuştur. Birçok bağımsız yazılım satıcısı (ISV), müşterilerinin şirket içi uygulamaları oluşturup dağıtmakta olan uygulamalara sahiptir. Uygulamaların dağıtıldığı farklı ortamlara erişmeniz gerektiğinden, bu uygulamaların desteklenmesi zordur. Azure CSP abonelikleri sayesinde, Azure 'un ölçek ve esnekliğine sahip daha basit bir alternatiftir.

Azure AD DS, Azure CSP aboneliklerini destekler. Uygulamanızı, müşterinizin Azure AD kiracısına bağlı bir Azure CSP aboneliğine dağıtabilirsiniz. Sonuç olarak, çalışanlarınız (destek personeli) uygulamanızın kurumsal kimlik bilgileri kullanılarak dağıtıldığı VM 'Leri yönetebilir, yönetebilir ve hizmet verebilir.

Ayrıca, Azure AD DS yönetilen bir etki alanını müşterinizin Azure AD kiracısında dağıtabilirsiniz. Uygulamanız daha sonra müşterinizin yönetilen etki alanına bağlanır. Uygulamanızda Kerberos/NTLM, LDAP veya [System. DirectoryServices API 'sini](/dotnet/api/system.directoryservices) kullanan yetenekler, müşterinizin etki alanında sorunsuz bir şekilde çalışır. Son müşteriler, uygulamanın dağıtıldığı altyapıyı sürdürme konusunda endişelenmenize gerek kalmadan, uygulamanızı bir hizmet olarak tüketerek faydalanır.

Azure AD DS dahil olmak üzere bu abonelikte kullandığınız Azure kaynakları için tüm faturalandırma, size geri ücretlendirilir. Müşteri ile ilişki üzerinde, satış, faturalandırma, teknik destek vb. geldiğinde tam denetim sahibi olursunuz. Azure CSP platformunun esnekliği sayesinde, destek aracılarının küçük bir ekibi, uygulamanızın örnekleri dağıtılan birçok müşteriye hizmet verebilir.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Azure AD DS için CSP dağıtım modelleri

Azure AD DS Azure CSP aboneliği ile kullanmanın iki yolu vardır. Müşterilerinizin sahip olduğu güvenliğe ve basit noktalara göre doğru olanı seçin.

### <a name="direct-deployment-model"></a>Doğrudan dağıtım modeli

Bu dağıtım modelinde Azure AD DS, Azure CSP aboneliğine ait bir sanal ağ içinde etkinleştirilir. CSP iş ortağının yönetici aracıları aşağıdaki ayrıcalıklara sahiptir:

* Müşterinin Azure AD kiracısında *genel yönetici* ayrıcalıkları.
* Azure CSP aboneliğinde *abonelik sahibi* ayrıcalıkları.

![Doğrudan dağıtım modeli](./media/csp/csp_direct_deployment_model.png)

Bu dağıtım modelinde, CSP sağlayıcısının yönetim aracıları müşterinin kimliklerini yönetebilir. Bu yönetici aracıları, Yeni Kullanıcı veya grup sağlama veya müşterinin Azure AD kiracısına uygulama ekleme gibi görevleri gerçekleştirebilir.

Bu dağıtım modeli, adanmış bir kimlik yöneticisi olmayan daha küçük kuruluşlar için uygun olabilir veya CSP iş ortağının adına kimlikleri yönetmesini tercih edebilir.

### <a name="peered-deployment-model"></a>Eşlenen dağıtım modeli

Bu dağıtım modelinde Azure AD DS, müşteriye ait bir sanal ağ (müşteri tarafından ödenen doğrudan Azure aboneliği) içinde etkinleştirilir. CSP iş ortağı, müşterilerin CSP aboneliğine ait olan bir sanal ağ içindeki uygulamaları dağıtabilir. Sanal ağlar daha sonra Azure sanal ağ eşlemesi kullanılarak bağlanabilir.

Bu dağıtımla, Azure CSP aboneliğinde CSP iş ortağı tarafından dağıtılan iş yükleri veya uygulamalar müşterinin doğrudan Azure aboneliğinde sağlanan yönetilen etki alanına bağlanabilir.

![Eşlenen dağıtım modeli](./media/csp/csp_peered_deployment_model.png)

Bu dağıtım modeli ayrıcalıkların ayrılmasını sağlar ve CSP iş yardım masası aracılarının Azure aboneliğini yönetmesine ve içindeki kaynakları dağıtmasına ve yönetmesine olanak tanır. Ancak, CSP ortağının yardım masası aracılarının müşterinin Azure AD dizininde genel yönetici ayrıcalıklarına sahip olması gerekmez. Müşterinin kimlik yöneticileri, kuruluşlarının kimliklerini yönetmeye devam edebilir.

Bu dağıtım modeli, bir ISV 'nin şirket içi uygulamalarının barındırılan bir sürümünü sağladığı, ayrıca müşterinin Azure AD 'ye bağlanması gereken senaryolara uygun olabilir.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>CSP aboneliklerinde Azure AD DS yönetme

Azure CSP aboneliğinde yönetilen bir etki alanını yönetirken aşağıdaki önemli konular geçerlidir:

* **CSP yönetici aracıları, kimlik bilgilerini kullanarak yönetilen bir etki alanı sağlayabilir:** Azure AD DS, Azure CSP aboneliklerini destekler. CSP iş ortağının yönetici aracıları grubuna ait olan kullanıcılar yeni bir yönetilen etki alanı sağlayabilir.

* **CSP 'ler, PowerShell kullanarak müşterileri için yeni yönetilen etki alanlarının komut dosyası oluşturulmasına sahiptir:** Ayrıntılar için bkz. [PowerShell kullanarak Azure AD DS etkinleştirme](powershell-create-instance.md) .

* **CSP yönetim aracıları, kimlik bilgilerini kullanarak yönetilen etki alanında devam eden yönetim görevleri gerçekleştiremez:** CSP yönetici kullanıcıları, kimlik bilgilerini kullanarak yönetilen etki alanı içinde rutin yönetim görevleri gerçekleştiremez. Bu kullanıcılar müşterinin Azure AD kiracısının dışında bulunur ve kimlik bilgileri müşterinin Azure AD kiracısı içinde kullanılamaz. Azure AD DS, bu kullanıcılar için Kerberos ve NTLM parola karmalarını erişimine sahip olmadığından, kullanıcıların yönetilen etki alanlarında kimlik doğrulaması yapılamıyor.

  > [!WARNING]
  > Yönetilen etki alanında devam eden yönetim görevlerini gerçekleştirmek için müşterinin dizininde bir kullanıcı hesabı oluşturmanız gerekir.
  >
  > CSP yönetici kullanıcısının kimlik bilgilerini kullanarak yönetilen etki alanında oturum açamazsınız. Bunu yapmak için müşterinin Azure AD kiracısına ait olan bir kullanıcı hesabının kimlik bilgilerini kullanın. VM 'Leri yönetilen etki alanına katma, DNS 'i yönetme veya grup ilkesi yönetme gibi görevler için bu kimlik bilgilerine ihtiyacınız vardır.

* **Devam eden Yönetim için oluşturulan kullanıcı hesabı *AAD DC Administrators* grubuna eklenmelidir:** *AAD DC yöneticileri* grubu, yönetilen etki alanında bazı Temsilcili yönetim görevlerini gerçekleştirme ayrıcalıklarına sahiptir. Bu görevler, DNS yapılandırması, kuruluş birimleri oluşturma ve Grup İlkesi yönetme işlemlerini içerir.
    
    Bir CSP iş ortağı tarafından yönetilen bir etki alanında bu görevleri gerçekleştirmek için, müşterinin Azure AD kiracısında bir kullanıcı hesabının oluşturulması gerekir. Bu hesabın kimlik bilgileri CSP iş ortağının yönetici aracılarıyla paylaşılmalıdır. Ayrıca, yönetilen etki alanındaki yapılandırma görevlerinin bu kullanıcı hesabı kullanılarak gerçekleştirilmesi için bu kullanıcı hesabının *AAD DC Yöneticiler* grubuna eklenmesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için [Azure CSP programına kaydolun](/partner-center/enrolling-in-the-csp-program). Daha sonra [Azure Portal](tutorial-create-instance.md) veya [Azure PowerShell](powershell-create-instance.md)kullanarak Azure AD Domain Services etkinleştirebilirsiniz.