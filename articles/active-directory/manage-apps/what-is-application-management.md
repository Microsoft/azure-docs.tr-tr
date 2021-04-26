---
title: Azure Active Directory 'de uygulama yönetimi nedir?
description: Bulut ve şirket içi uygulamalarınız için kimlik ve erişim yönetimi (ıAM) sistemi olarak Azure Active Directory (AD) kullanılmasına genel bakış.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: iangithinji
ms.reviewer: ''
ms.openlocfilehash: 8bdb8eb9cf176f8e190769e38c81d02ad2985196
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376401"
---
# <a name="what-is-application-management"></a>Uygulama yönetimi nedir?

Azure AD bir Kimlik ve Erişim Yönetimi (IAM) sistemidir. Dijital kimliklerle ilgili depolama bilgilerinin tek bir yerde depolanmasını sağlar. Yazılım uygulamalarınızı, kullanıcı bilgilerini Azure AD’de depolayacak biçimde yapılandırabilirsiniz. 

Azure AD bir uygulamayla tümleştirilecek şekilde yapılandırılmalıdır. Diğer bir deyişle, kimlik için hangi uygulamaların kullandığını bilmeleri gerekir. Azure AD 'nin bu uygulamalardan haberdar olması ve bunların nasıl işlenmesi gerektiği, uygulama yönetimi olarak bilinir.

Uygulamaları, Azure Active Directory portalının Yönet bölümünde yer alan **Kurumsal uygulamalar** sayfasında yönetirsiniz.

![Azure AD portalının Yönet bölümünün altındaki kurumsal uygulamalar seçeneği.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Kimlik ve erişim yönetimi (ıAM) sistemi nedir?
Uygulama, bir amaç için kullanılan bir yazılım parçasıdır. Çoğu uygulama, kullanıcıların oturum açmasını gerektirir.

Merkezi bir kimlik sistemi, daha sonra tüm uygulamalar tarafından kullanılabilecek Kullanıcı bilgilerini depolamak için tek bir yer sağlar. Bu sistemler, kimlik ve erişim yönetimi (ıAM) sistemleri olarak bilinmelidir. Azure Active Directory, Microsoft bulutu için ıAM sistemidir.

>[!TIP]
>Bir ıAM sistemi, Kullanıcı kimliklerini izlemek için tek bir yer sağlar. Azure AD, Microsoft bulutu için ıAM sistemidir.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Neden bir bulut çözümüyle uygulamalar yönetilmelidir?

Kuruluşların çoğu zaman kullanıcıların işlerini yapmak için bağımlı oldukları yüzlerce uygulaması vardır. Kullanıcılar birçok cihaz ve konumdan bu uygulamalara erişir. Yeni uygulamalar her zaman eklenir, geliştirilmiştir ve gün batımı. Bu kadar çok uygulama ve erişim noktası sayesinde, bunlarla birlikte çalışacak bir kimlik çözümü kullanılması önemlidir.

>[!TIP]
>Azure AD Uygulama Galerisi, Azure AD ile bir kimlik sağlayıcısı olarak çalışmak üzere önceden yapılandırılmış çok sayıda popüler uygulama içerir.

## <a name="how-does-azure-ad-work-with-apps"></a>Azure AD, uygulamalarla nasıl çalışır?

Azure AD ortasında bulunur ve bulut ve şirket içi uygulamalar için kimlik yönetimi sağlar. 

![Azure AD aracılığıyla federe olan uygulamaları gösteren diyagram](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>Kullanıcıların şirket HR sistemine eklediğinizde otomatik olarak Azure AD 'ye eklenmesi için [Kullanıcı sağlamayı otomatikleştirerek](../app-provisioning/user-provisioning.md) yönetim maliyetlerini azaltın. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Azure AD ile tümleştirilebilen uygulama türleri nelerdir?

Azure AD 'yi, her türlü uygulama için kimlik sisteminiz olarak kullanabilirsiniz. Birçok uygulama önceden yapılandırılmıştır ve en düşük çabayla ayarlanabilir. Önceden yapılandırılmış bu uygulamalar [Azure AD uygulaması galerisinde](/azure/active-directory/saas-apps/)yayımlanır. 

Zaten galeride olmayan birçok uygulamayı çoklu oturum açma için el ile yapılandırabilirsiniz. Azure AD birkaç SSO seçeneği sağlar. En popüler, SAML tabanlı SSO ve OıDC tabanlı SSO. SSO 'yu etkinleştirmek üzere uygulamaları tümleştirme hakkında daha fazla bilgi için bkz. [Çoklu oturum açma seçenekleri](sso-options.md). 

Kuruluşunuz şirket içi uygulamalar kullanıyor mu? Bunları, uygulama proxy 'Si kullanarak tümleştirebilirsiniz. Daha fazla bilgi edinmek için bkz. [Azure AD 'Nin uygulama proxy 'si aracılığıyla şirket içi uygulamalara uzaktan erişim sağlama](application-proxy.md).

>[!TIP]
>Kendi iş kolu uygulamalarınızı oluştururken, çoklu oturum açmayı desteklemek için bunları Azure AD ile tümleştirebilirsiniz. Azure AD için uygulama geliştirme hakkında daha fazla bilgi için bkz. [Microsoft Identity platform](..//develop/v2-overview.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Koşullu erişim ilkeleriyle riskleri yönetme

[Koşullu erişim](../conditional-access/concept-conditional-access-cloud-apps.md) Ile Azure AD çoklu oturum açma (SSO), uygulamalara erişmek için yüksek düzeyde güvenlik sağlar. Koşullu erişim ilkeleri, ayarladığınız koşullara göre uygulamalara ayrıntılı denetim sağlar. 

## <a name="improve-productivity-with-single-sign-on"></a>Çoklu oturum açma ile üretkenliği artırma

Çoklu oturum açma (SSO), Microsoft 365 ile kullandığınız diğer tüm uygulamalar arasında birleştirilmiş bir kullanıcı deneyimi sağlar. Kullanıcı adınızı ve parolanızı sürekli girerek Hoşçakalın!

Çoklu oturum açma hakkında daha fazla bilgi edinmek için bkz. [Çoklu oturum açma](what-is-single-sign-on.md)nedir.

## <a name="address-governance-and-compliance"></a>İdare ve uyumu ele alma

Güvenlik olayı ve olay Izleme (SıEM) araçları kullanan raporlar aracılığıyla uygulamaları izleyin. Portaldan veya API’lerden raporlara erişebilirsiniz. Uygulamalarınıza kimlerin erişiminin olduğunu programlama yoluyla denetleyin ve erişim gözden geçirmeleri yoluyla etkin olmayan kullanıcılara erişimi kaldırın.

## <a name="manage-costs"></a>Maliyetleri yönetme

Azure AD’ye geçirerek maliyet tasarrufu yapabilir ve şirket içi altyapınızı yönetme zahmetini ortadan kaldırabilirsiniz. Azure AD, uygulamalara self servis erişimi de sağlar; böylece hem yöneticiler hem de kullanıcılar için zaman tasarrufu sağlanır. Çoklu oturum açma, uygulamaya özgü parolaları ortadan kaldırır. Bir kez oturum açma, uygulamalar için parola sıfırlama ve parolalar alınırken oluşan üretkenlik kaybı ile ilgili maliyetlerden tasarruf sağlar.

Insan kaynakları odaklı uygulamalarda veya büyük bir Kullanıcı kümesiyle diğer uygulamalarda, yaşamınızı kolaylaştırmak için uygulama sağlamayı kullanabilirsiniz. Uygulama sağlama, Kullanıcı ekleme ve kaldırma sürecini otomatikleştirir. Daha fazla bilgi edinmek için bkz. [uygulama sağlama nedir?](../app-provisioning/user-provisioning.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama yönetiminde hızlı başlangıç serisi](view-applications-portal.md)
- [Uygulama tümleştirmesini kullanmaya başlayın](plan-an-application-integration.md)
- [Sağlamayı otomatikleştirmeyi öğrenin](../app-provisioning/user-provisioning.md)