---
title: Hesapları ve kimlik bilgilerini paylaşma-Azure Active Directory | Microsoft Docs
description: Azure Active Directory kuruluşların şirket içi uygulamalar ve tüketici bulut hizmetleri için hesapları güvenli bir şekilde paylaşmasına nasıl olanak sağladığını açıklar.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a01ebde09517cbe7f093dd56a8a4116e76e64e38
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736765"
---
# <a name="sharing-accounts-with-azure-ad"></a>Hesapları Azure AD ile paylaşma

## <a name="overview"></a>Genel Bakış

Bazen kuruluşların, genellikle iki durumda gerçekleşen birden çok kişi için tek bir Kullanıcı adı ve parola kullanması gerekir:

* Her bir kullanıcı için benzersiz bir oturum açma ve parola gerektiren uygulamalara erişirken, şirket içi uygulamalar veya tüketici bulut Hizmetleri (örneğin, kurumsal sosyal medya hesapları).
* Çoklu Kullanıcı ortamları oluştururken. Yükseltilmiş ayrıcalıklara sahip tek bir yerel hesabınız olabilir ve temel kurulum, yönetim ve kurtarma etkinliklerini yapmak için kullanılır. Örneğin, Office 365 için yerel "genel yönetici" hesabı veya Salesforce 'daki kök hesabı.

Geleneksel olarak, bu hesaplar kimlik bilgileri (Kullanıcı adı ve parola) doğru kişilere dağıtarak veya birden çok güvenilen aracının erişebileceği paylaşılan bir konumda depolanarak paylaşılır.

Geleneksel paylaşım modelinin çeşitli dezavantajları vardır:

* Yeni uygulamalara erişimi etkinleştirmek için kimlik bilgilerini erişmesi gereken herkese dağıtmanız gerekir.
* Paylaşılan her uygulama, kullanıcıların birden çok kimlik bilgisi kümesini anımsamasını gerektiren kendi benzersiz bir paylaşılan kimlik bilgileri kümesi gerektirebilir. Kullanıcıların birçok kimlik bilgilerini hatırlamaları gerektiğinde riskli uygulamalara yönelik risk artar. (örneğin, parola yazma).
* Uygulamaya kimlerin erişebileceğini söyleyebilirsiniz.
* Uygulamaya kimin *eriştiğini* söyleyemiyoruz.
* Bir uygulamaya erişimi kaldırmak istediğinizde, kimlik bilgilerini güncelleştirip bu uygulamaya erişmesi gereken herkese yeniden dağıtmanız gerekir.

## <a name="azure-active-directory-account-sharing"></a>Hesap paylaşımını Azure Active Directory

Azure AD, bu dezavantajları ortadan kaldıran paylaşılan hesapları kullanmaya yönelik yeni bir yaklaşım sağlar.

Azure AD yöneticisi, bir kullanıcının erişim panelini kullanarak hangi uygulamalara erişebileceğini ve bu uygulama için en uygun çoklu oturum açma türünü seçip seçmeyeceğini yapılandırır. Bu türlerden biri olan *parola tabanlı çoklu oturum açma*, Azure AD 'nin bu uygulama için oturum açma işlemi sırasında bir tür "aracı" olarak hareket etmesini sağlar.

Kullanıcılar, kurumsal hesabıyla bir kez oturum açabilirler. Bu hesap, masaüstü veya e-postasına erişmek için düzenli olarak kullandıkları bir hesaptır. Bunlar, yalnızca atandığı uygulamaları bulabilir ve bunlara erişebilir. Paylaşılan hesaplarla, bu uygulama listesi herhangi bir sayıda paylaşılan kimlik bilgisi içerebilir. Son kullanıcının, kullanmakta olabileceği çeşitli hesapları anımsaması veya yazması gerekmez.

Paylaşılan hesaplar yalnızca gözetim artışı ve kullanılabilirliği iyileştirmez ve ayrıca güvenlerinizi geliştirir. Kimlik bilgilerini kullanma izni olan kullanıcılar, paylaşılan parolayı göremez, ancak parolayı genişletilmiş bir kimlik doğrulama akışının parçası olarak kullanma izinleri alın. Ayrıca, bazı parola SSO uygulamaları, Azure AD 'yi kullanarak parolalarını düzenli olarak Rollover (güncelleştirme) seçeneğini sunar. Sistem, hesap güvenliğini artıran büyük, karmaşık parolalar kullanır. Yönetici, bir uygulamaya erişimi kolayca verebilir veya iptal edebilir, hesaba kimlerin erişebileceğini bilir ve geçmişte kimin eriştiği.

Azure AD, tüm parola çoklu oturum açma uygulamalarında tüm Enterprise Mobility Suite (EMS) veya Azure AD Premium lisans planına yönelik paylaşılan hesapları destekler. Uygulama galerisindeki binlerce önceden tümleştirilmiş uygulama için hesapları paylaşabilir ve [özel SSO uygulamalarıyla](../manage-apps/configure-single-sign-on-portal.md)kendi parola kimlik doğrulama uygulamanızı ekleyebilirsiniz.

Hesap paylaşımını etkinleştiren Azure AD özellikleri şunlardır:

* [Parola çoklu oturum açma](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Parola çoklu oturum açma Aracısı
* [Grup ataması](groups-self-service-management.md)
* Özel parola uygulamaları
* [Uygulama kullanımı panosu/raporları](../active-directory-passwords-get-insights.md)
* Son Kullanıcı erişim portalları
* [Uygulama proxy 'si](../manage-apps/application-proxy.md)
* [Active Directory marketi](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Hesap paylaşma

Bir hesabı paylaşmak üzere Azure AD 'yi kullanmak için şunları yapmanız gerekir:

* Uygulama uygulaması [Galerisi](https://azure.microsoft.com/marketplace/active-directory/) veya [özel uygulama](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/) ekleme
* Uygulamayı parola çoklu oturum açma (SSO) için yapılandırma
* [Grup tabanlı atamayı](groups-saasapps.md) kullanın ve paylaşılan bir kimlik bilgisi girme seçeneğini belirleyin

Ayrıca, Multi-Factor Authentication (MFA) ile paylaşılan hesabınızı daha güvenli hale getirebilirsiniz ( [Azure AD ile uygulamaların güvenliğini sağlama](../authentication/concept-mfa-whichversion.md)hakkında daha fazla bilgi edinin) ve [Azure AD self servis kullanarak uygulamaya kimlerin erişimi olduğunu yönetme yeteneği atayabilirsiniz. ](groups-self-service-management.md)Grup Yönetimi.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Active Directory’de Uygulama Yönetimi](../manage-apps/what-is-application-management.md)
* [Koşullu erişimle uygulamaları koruma](../active-directory-conditional-access-azure-portal.md)
* [Self Servis Grup Yönetimi/SSAA](groups-self-service-management.md)
