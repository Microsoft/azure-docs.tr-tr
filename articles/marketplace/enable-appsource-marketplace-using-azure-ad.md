---
title: Azure Active Directory kullanarak Microsoft AppSource ve Azure Marketi listesini etkinleştirme | Mavisi
description: Azure Marketi 'nde Azure Active Directory ve uygulama ve hizmet yayımcıları için AppSource kullanarak liste türünü etkinleştirin.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/12/2018
ms.author: pabutler
ms.openlocfilehash: 9423263270313659017b16a5e34511e7ef385af5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825883"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Azure Active Directory kullanarak AppSource ve Market listesini etkinleştirme

 Azure Active Directory (Azure AD), bir Microsoft hesabı kimlik doğrulamaya izin veren bir bulut kimlik hizmetidir. Azure AD, sektör standardı çerçeveler kullanır. [Azure Active Directory hakkında daha fazla bilgi edinin](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Azure AD avantajları

Microsoft AppSource ve Azure Market müşterileri, liste kataloglarında arama yapmak için ürün içi deneyimler kullanır. Bu eylemler, müşterilerin üründe oturum açmasını gerektirir. Azure AD tümleştirmesi aşağıdaki avantajları sağlar:

- Daha hızlı katılım ve iyileştirilmiş müşteri deneyimi
- Milyonlarca kurumsal Kullanıcı için çoklu oturum açma (SSO)
- Farklı iş ortakları tarafından yayımlanan uygulamalar arasında tutarlı, oturum açma deneyimi
- Mobil ve bulut uygulamaları için ölçeklenebilir, platformlar arası kimlik doğrulaması

## <a name="offers-that-require-azure-ad"></a>Azure AD gerektiren teklifler

AppSource ve Azure Marketi için çeşitli [Listeleme seçenekleri ve teklif türleri](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) , Azure AD uygulamasına yönelik farklı gereksinimlere sahiptir. Ayrıntılar için aşağıdaki tabloya bakın:

| **Teklif türü**    | **Azure AD SSO gerekli mi?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Benimle iletişim kurun | Deneme | Test Sürüşü | İşlem |
| Sanal Makine | Yok | Hayır | Hayır | Hayır |
| Azure uygulamaları (çözüm şablonu)  | Yok | Yok | Yok | Yok |
| Yönetilen uygulamalar  | Yok | Yok | Yok | Hayır |
| SaaS  | Hayır | Evet | Evet | Evet |
| Kapsayıcılar  | Yok | Yok | Yok | Hayır |
| Danışmanlık Hizmetleri  | Hayır | Yok | Yok | Yok |

SaaS teknik gereksinimleri hakkında daha fazla bilgi için bkz. [SaaS uygulamaları Için Yayımlama Kılavuzu](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Azure AD tümleştirmesi

- Azure AD 'yi listeleyerek tümleştirerek çoklu oturum açmayı etkinleştirme hakkında bilgi için, bkz. [Azure Active Directory geliştiriciler]( https://aka.ms/aaddev).
- Azure AD çoklu oturum açma hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Deneme listesini etkinleştirme

Otomatik müşteri kurulumu dönüştürme olasılığını artırabilir. Müşteriniz deneme listesini seçtiğinde ve deneme ortamınıza yönlendirildiğinde, ek oturum açma adımları gerekmeden müşteriyi doğrudan ayarlayabilirsiniz.

Kimlik doğrulama sırasında Azure AD, uygulamanıza veya teklifinizi bir belirteç gönderir. Belirteç tarafından belirtilen kullanıcı bilgileri, uygulamanızda veya teklifinizdeki bir kullanıcı hesabının oluşturulmasına izin verilir. Daha fazla bilgi için bkz. [örnek belirteçleri](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Uygulamanızda tek tıklamayla kimlik doğrulamayı etkinleştirmek için Azure AD kullandığınızda şunları yapabilirsiniz:

- Market 'ten deneme listeinize müşteri deneyimini kolaylaştırın.
- Kullanıcı Market 'ten etki alanı veya deneme ortamınıza yeniden yönlendirildiğinde bile, ürün içi deneyim hisini koruyun.
- Ek oturum açma adımları olmadığından, kullanıcılar yeniden yönlendirildiğinde terk olma olasılığını azaltın.
- Azure AD kullanıcılarının büyük popülasyonu için dağıtım engelleri azaltma.

## <a name="verify-azure-ad-integration"></a>Azure AD tümleştirmesini doğrulama

### <a name="multitenant-solutions"></a>Çok kiracılı çözümler

Aşağıdaki eylemleri desteklemek için Azure AD 'yi kullanın:

- Uygulamanızı Market stokörkinden birine kaydedin. Daha fazla bilgi için [uygulama kaydını](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) veya [appsource sertifikasını](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) görüntüleyin.
- Tek tıklamayla deneme deneyimi almak için Azure AD 'de çok kiracılı destek özelliğini etkinleştirin.

Azure AD Federasyon çoklu oturum açma 'yı kullanmaya yeni başladıysanız şu adımları uygulayın:

1. Uygulamanızı Market 'e kaydedin.
1. [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) veya [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)kullanarak Azure AD ile SSO geliştirin.
1. Tek tıklamayla deneme deneyimi sağlamak için Azure AD 'de çok kiracılı destek özelliğini etkinleştirin.

### <a name="single-tenant-solutions"></a>Tek kiracılı çözümler

Aşağıdaki eylemlerden birini desteklemek için Azure AD 'yi kullanın:

- [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)kullanarak dizininize Konuk kullanıcı ekleyin.
- **İletişim** yayımlama yayınlaması seçeneğini kullanarak müşteriler için denemeleri el ile ayarlayın.
- Müşteri başına test sürücüsü geliştirin.
- SSO kullanan çok kiracılı örnek Tanıtım uygulaması oluşturun.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Market ' te kayıtlı](https://azuremarketplace.microsoft.com/sell)olduğunuzdan emin olun.
- Teklifinizi oluşturma veya tamamlamaya yönelik daha fazla bilgi için bkz. [Iş Ortağı Merkezi hesabı oluşturma](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) .
