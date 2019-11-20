---
title: B2B işbirliği ve B2C 'yi karşılaştırın-Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B işbirliği ile Azure AD B2C arasındaki fark nedir?
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 07/22/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e7f5aa324db869e30a8b2fe214416129baca8d9
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380739"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Azure Active Directory’de B2B işbirliğini ve B2C’yi karşılaştırma

Hem Azure Active Directory (Azure AD) B2B işbirliği hem de Azure AD B2C, Azure AD’de dış kullanıcılarla çalışmanıza olanak sağlar. O halde bunların benzerlikleri ve farkları nelerdir?

**Azure AD B2B**, işbirliği yapabilmeleri için harici kullanıcılarla dosya ve kaynakları güvenli şekilde paylaşmak isteyen işletmeler içindir. Azure yöneticisi, Azure portalda B2B’yi ayarlar ve Azure AD, işletmeniz ile şirket dışındaki iş ortağınız arasındaki federasyon işleminden yararlanır. Kullanıcılar, iş veya okul hesabı ya da herhangi bir e-posta hesabı ile basit bir davet ve kullanım işlemini kullanarak paylaşılan kaynaklarda oturum açar.
 
**Azure AD B2C**, öncelikle müşteriye yönelik uygulamalar oluşturan işletmeler ve geliştiriciler içindir. Azure AD B2C ile geliştiriciler, bir yandan müşterilerin önceden belirlediği bir kimlikle (Facebook veya Gmail gibi) oturum açmasını sağlarken diğer yandan uygulamaları için tam özellikli kimlik sistemi olarak Azure AD’yi de kullanabilir.

Aşağıdaki tabloda ayrıntılı bir karşılaştırma sağlanmıştır.


B2B işbirliği özellikleri |     Azure AD B2C bağımsız teklifi
-------- | --------
İçin tasarlanan: Kimlik sağlayıcısından bağımsız olarak, bir iş ortağı kuruluştan kullanıcıların kimliğini doğrulayabilmek isteyen kuruluşlar. | İçin tasarlanan: Kişilerin, kurum veya kurumsal müşterilerin Azure AD 'nize, mobil ve Web uygulamalarınızın müşterilerini davet etme.
Desteklenen kimlikler: İş veya okul hesapları, iş veya okul hesaplarıyla iş ortakları veya herhangi bir e-posta adresi olan çalışanlar. Yakında doğrudan federasyon desteklenecektir.  | Desteklenen kimlikler: Yerel uygulama hesapları (herhangi bir e-posta adresi veya Kullanıcı adı) veya doğrudan Federasyon ile desteklenen herhangi bir sosyal kimlik olan tüketici kullanıcıları.
Dış kullanıcılar, çalışanlarla aynı dizinde yönetilir, ancak özellikle açıklama eklenir. Bunlar, çalışanlarla aynı şekilde yönetilebilir, aynı gruplara eklenebilir ve bu şekilde devam edebilir  | Dış kullanıcılar, uygulama dizininde yönetilir. Bunlar, kuruluşun çalışanı ve iş ortağı dizininden (varsa) ayrı olarak yönetilirler.
Tüm Azure AD ile bağlantılı uygulamalarda çoklu oturum açma (SSO) desteklenir. Örneğin, Office 365 veya şirket içi uygulamalara ve Salesforce ya da Workday gibi diğer SaaS uygulamalarına erişim sağlayabilirsiniz.  |  Azure AD B2C kiracılarında müşterilere ait uygulamalarda SSO desteklenir. Office 365 veya diğer Microsoft SaaS uygulamalarına yönelik SSO desteklenmez.
İş ortağı yaşam döngüsü: Konak/kuruluş tarafından yönetiliyor.  | Müşteri yaşam döngüsü: Uygulama tarafından Self servis veya yönetilen.
Güvenlik ilkesi ve Uyumluluk: Konak/kuruluş tarafından yönetilir (örneğin, [koşullu erişim ilkeleriyle](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Güvenlik ilkesi ve Uyumluluk: Uygulama tarafından yönetilir.
Olanağı Kuruluşun markasını barındırmak/davet etmek kullanılır.  |    Olanağı Uygulama tarafından yönetiliyor. Genellikle kuruluşun arka planda soluk olduğu şekilde ürün markalı olma eğilimindedir.
Daha fazla bilgi: [Blog gönderisi](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Belgeler](what-is-b2b.md)  | Daha fazla bilgi: [Ürün sayfası](https://azure.microsoft.com/services/active-directory-b2c/), [Belgeler](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
- [B2B işbirliği kullanıcı özellikleri](user-properties.md)

