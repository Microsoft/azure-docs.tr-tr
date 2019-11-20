---
title: Bulut çözümü sağlayıcıları için Azure AD Domain Services | Microsoft Docs
description: Azure bulut çözümü sağlayıcıları için Azure Active Directory Domain Services.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: iainfou
ms.openlocfilehash: 1134c078ee36a146cb1e1cbf8ca46f6cd9f8d775
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754435"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure bulut çözüm sağlayıcıları (CSP) için Azure Active Directory (AD) etki alanı Hizmetleri
Bu makalede, Azure CSP aboneliğinde Azure AD Domain Services nasıl kullanabileceğiniz açıklanmaktadır.

## <a name="overview-of-azure-csp"></a>Azure CSP 'ye Genel Bakış
Azure CSP, Microsoft Iş ortakları için bir programdır ve çeşitli Microsoft bulut hizmetleri için bir lisans kanalı sağlar. Azure CSP, iş ortaklarının satışları yönetmesine, fatura ilişkisine sahip olmasını, teknik ve faturalandırma desteği sağlamasını ve müşterinin tek iletişim noktası olmasını sağlar. Ayrıca, Azure CSP, bir self servis portalı ve eşlik eden API 'Ler dahil olmak üzere tam bir araç kümesi sağlar. Bu araçlar, CSP iş ortaklarının Azure kaynaklarını kolayca sağlamanıza ve yönetmesine ve müşterilere ve aboneliklerine yönelik faturalandırma sağlamasına imkan tanır.

[Iş Ortağı Merkezi portalı](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) , tüm Azure CSP iş ortakları için bir giriş noktası işlevi görür. Zengin müşteri yönetim özellikleri, otomatikleştirilmiş işleme ve daha fazlasını sağlar. Azure CSP iş ortakları, Web tabanlı bir kullanıcı arabirimi veya PowerShell ve çeşitli API çağrıları kullanarak Iş Ortağı Merkezi özelliklerini kullanabilir.

Aşağıdaki diyagramda, CSP modelinin yüksek düzeyde nasıl çalıştığı gösterilmektedir. Contoso 'da bir Azure AD Active Directory vardır. Azure CSP aboneliklerinde kaynakları dağıtan ve yöneten CSP ile bir iş ortaklığı vardır. Contoso ayrıca doğrudan contoso 'da faturalandırılan normal (doğrudan) Azure aboneliklerine sahip olabilir.

![CSP modeline genel bakış](./media/csp/csp_model_overview.png)

CSP iş ortağının kiracısında üç özel aracı grubu vardır-yönetici aracıları, yardım masası aracıları ve satış aracıları. Yönetici aracıları grubu, contoso 'nun Azure AD dizinindeki kiracı yöneticisi rolüne atanır. Sonuç olarak, CSP iş ortağının yönetici aracıları grubuna ait olan bir kullanıcının, contoso Azure AD dizininde kiracı yöneticisi ayrıcalıkları vardır. CSP iş ortağı Contoso için bir Azure CSP aboneliği sağlarken, yönetici aracıları grubu bu abonelik için sahip rolüne atanır. Sonuç olarak, CSP ortağının yönetim aracıları, contoso adına sanal makineler, sanal ağlar ve Azure AD Domain Services gibi Azure kaynaklarını sağlamak için gerekli ayrıcalıklara sahiptir.

Daha fazla bilgi için bkz. [Azure CSP genel bakış](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Azure CSP aboneliğinde Azure AD Domain Services kullanmanın avantajları
Azure AD Domain Services, Azure 'da LDAP, Kerberos/NTLM kimlik doğrulaması, etki alanına ekleme, Grup İlkesi ve DNS gibi Windows Server AD uyumlu hizmetler sağlar. Çok sayıda uygulama, bu özellikleri kullanarak AD 'ye karşı çalışmak üzere oluşturulmuştur. Birçok bağımsız yazılım satıcısı (ISV), müşterilerinin şirket içi uygulamaları oluşturup dağıtmakta olan uygulamalara sahiptir. Bu uygulamalar, genellikle bu uygulamaların dağıtıldığı farklı ortamlara erişim gerektirdiğinden, destek sağlamak için gereklidir. Azure CSP abonelikleri sayesinde, Azure 'un ölçek ve esnekliğine sahip daha basit bir alternatiftir.

Azure AD Domain Services artık Azure CSP aboneliklerini desteklemektedir. Artık uygulamanızı, müşterinizin Azure AD dizinine bağlı bir Azure CSP aboneliğine dağıtabilirsiniz. Sonuç olarak, çalışanlarınız (destek personeli), uygulamanızın kurumsal kimlik bilgileri kullanılarak dağıtıldığı sanal makineleri yönetebilir, yönetebilir ve hizmet verebilir. Ayrıca, müşterinizin Azure AD dizini için Azure AD Domain Services yönetilen bir etki alanı sağlayabilirsiniz. Uygulamanız, müşterinizin yönetilen etki alanına bağlanır. Bu nedenle, uygulamanızda Kerberos/NTLM, LDAP veya [System. DirectoryServices API 'sini](/dotnet/api/system.directoryservices) kullanan yetenekler, müşterinizin dizinine karşı sorunsuz çalışır. Son müşterileriniz, uygulamanın dağıtıldığı altyapıyı sürdürme konusunda endişelenmenize gerek kalmadan uygulamanızı hizmet olarak kullanmanın önemli ölçüde avantajına sahiptir.

Azure AD Domain Services de dahil olmak üzere bu abonelikte kullandığınız tüm Azure kaynakları için faturalandırma size geri ücretlendirilir. Müşteri ile ilişki üzerinde, satış, faturalandırma, teknik destek vb. geldiğinde tam denetim sahibi olursunuz. Azure CSP platformunun esnekliği sayesinde, destek aracılarının küçük bir ekibi, uygulamanızın örnekleri dağıtılan birçok müşteriye hizmet verebilir.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Azure AD etki alanı Hizmetleri için CSP dağıtım modelleri
Azure CSP aboneliğiyle Azure AD Domain Services kullanmanın iki yolu vardır. Müşterilerinizin sahip olduğu güvenliğe ve basit noktalara göre doğru olanı seçin.

### <a name="direct-deployment-model"></a>Doğrudan dağıtım modeli
Bu dağıtım modelinde Azure AD Domain Services, Azure CSP aboneliğine ait bir sanal ağ içinde etkinleştirilir. CSP iş ortağının yönetici aracıları aşağıdaki ayrıcalıklara sahiptir:
* Müşterinin Azure AD dizininde genel yönetici ayrıcalıkları.
* Azure CSP aboneliğinde abonelik sahibi ayrıcalıkları.

![Doğrudan dağıtım modeli](./media/csp/csp_direct_deployment_model.png)

Bu dağıtım modelinde, CSP sağlayıcısının yönetim aracıları müşterinin kimliklerini yönetebilir. Bu yönetici aracılarında yeni kullanıcılar, gruplar sağlama, müşterinin Azure AD dizini içinde uygulama ekleme özelliği vardır. Bu dağıtım modeli, adanmış bir kimlik yöneticisine sahip olmayan daha küçük kuruluşlar için uygun olabilir veya CSP iş ortağının adına kimlikleri yönetmesini tercih edebilir.


### <a name="peered-deployment-model"></a>Eşlenen dağıtım modeli
Bu dağıtım modelinde Azure AD Domain Services, müşteriye ait bir sanal ağ içinde etkinleştirilir. Bu, müşteri tarafından ödenen doğrudan bir Azure aboneliğine sahiptir. CSP iş ortağı daha sonra, müşterinin CSP aboneliğine ait olan bir sanal ağ içindeki uygulamaları dağıtabilir. Sanal ağlar daha sonra Azure sanal ağ eşlemesi kullanılarak bağlanabilir. Sonuç olarak, Azure CSP aboneliğinde CSP iş ortağı tarafından dağıtılan iş yükleri/uygulamalar müşterinin doğrudan Azure aboneliğinde sağlanan yönetilen etki alanına bağlanabilir.

![Eşlenen dağıtım modeli](./media/csp/csp_peered_deployment_model.png)

Bu dağıtım modeli ayrıcalıkların ayrılmasını sağlar ve CSP iş yardım masası aracılarının Azure aboneliğini yönetmesine ve içindeki kaynakları dağıtmasına ve yönetmesine olanak tanır. Ancak, CSP ortağının yardım masası aracılarının müşterinin Azure AD dizininde genel yönetici ayrıcalıklarına sahip olması gerekmez. Müşterinin kimlik yöneticileri, kuruluşlarının kimliklerini yönetmeye devam edebilir.

Bu dağıtım modeli, bir ISV (bağımsız yazılım satıcısı) Şirket içi uygulamalarının barındırılan bir sürümünü sağladığından müşterinin adına bağlanması gereken senaryolara uygun olabilir.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>CSP aboneliklerinde Azure AD Domain Services yönetilen etki alanlarını yönetme
Azure CSP aboneliğinde yönetilen bir etki alanını yönetirken aşağıdaki önemli konular geçerlidir:

* **CSP yönetici aracıları, kimlik bilgilerini kullanarak yönetilen bir etki alanı sağlayabilir:** Azure AD Domain Services Azure CSP aboneliklerini destekler. Bu nedenle, bir CSP iş ortağının yönetici aracıları grubuna ait olan kullanıcılar yeni bir Azure AD Domain Services yönetilen etki alanı sağlayabilir.

* **CSP 'ler, PowerShell kullanarak müşterileri için yeni yönetilen etki alanlarının komut dosyası oluşturulmasına sahiptir:** Ayrıntılar için bkz. [PowerShell kullanarak Azure AD Domain Services etkinleştirme](powershell-create-instance.md) .

* **CSP yönetim aracıları, kimlik bilgilerini kullanarak yönetilen etki alanında devam eden yönetim görevleri gerçekleştiremez:** CSP yönetici kullanıcıları, kimlik bilgilerini kullanarak yönetilen etki alanı içinde rutin yönetim görevleri gerçekleştiremez. Bu kullanıcılar müşterinin Azure AD dizininin dışında ve kimlik bilgileri müşterinin Azure AD dizininde kullanılamaz. Bu nedenle, Azure AD Domain Services bu kullanıcılar için Kerberos ve NTLM parola karmalarını erişimine sahip değildir. Sonuç olarak, bu tür kullanıcılar Azure AD Domain Services yönetilen etki alanlarında kimlik doğrulaması yapılamaz.

  > [!WARNING]
  > **Yönetilen etki alanında devam eden yönetim görevlerini gerçekleştirmek için müşterinin dizininde bir kullanıcı hesabı oluşturmanız gerekir.**
  > CSP yönetici kullanıcısının kimlik bilgilerini kullanarak yönetilen etki alanında oturum açılamıyor. Bunu yapmak için müşterinin Azure AD dizinine ait olan bir kullanıcı hesabının kimlik bilgilerini kullanın. Sanal makineleri yönetilen etki alanına katma, DNS 'yi yönetme, grup ilkesi yönetme gibi görevler için bu kimlik bilgilerine ihtiyacınız vardır.
  >

* **Devam eden Yönetim için oluşturulan kullanıcı hesabının ' AAD DC Administrators ' grubuna eklenmesi gerekir:** ' AAD DC yöneticileri ' grubunun, yönetilen etki alanında bazı Temsilcili yönetim görevlerini gerçekleştirme ayrıcalıkları vardır. Bu görevler, DNS yapılandırmayı, kuruluş birimleri oluşturmayı, Grup ilkesini yönetmeyi içerir. Bir CSP iş ortağının yönetilen bir etki alanında bu gibi görevleri gerçekleştirmesi için müşterinin Azure AD dizininde bir kullanıcı hesabının oluşturulması gerekir. Bu hesabın kimlik bilgileri CSP iş ortağının yönetici aracılarıyla paylaşılmalıdır. Ayrıca, yönetilen etki alanında bu kullanıcı hesabı kullanılarak gerçekleştirilecek yapılandırma görevlerinin etkinleştirilmesi için bu kullanıcı hesabının ' AAD DC Administrators ' grubuna eklenmesi gerekir.


## <a name="next-steps"></a>Sonraki adımlar
* [Azure CSP programına kaydolun](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) ve Azure CSP aracılığıyla iş oluşturmaya başlayın.
* [Azure CSP 'de bulunan Azure hizmetleri](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)listesini gözden geçirin.
* [PowerShell kullanarak Azure AD Domain Services'ı etkinleştirme](powershell-create-instance.md)
* [Azure AD Domain Services kullanmaya başlayın](tutorial-create-instance.md)
