---
title: Avrupa müşterileri için kimlik veri depolaması-Azure AD
description: Azure Active Directory, Avrupa müşterilerine yönelik kimlikle ilgili verileri nerede depoladığını öğrenin.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a8b013723707c4a3a087a90674227c3d41c5108
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836946"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory Avrupa müşterileri için kimlik veri depolaması
Kimlik verileri, Azure AD tarafından, Microsoft 365 ve Azure gibi bir Microsoft Online hizmetine abone olurken kuruluşunuzun verdiği adrese bağlı olarak coğrafi bir konumda depolanır. Kimlik verilerinizin nerede depolandığı hakkında bilgi için, Microsoft Güven Merkezi ' nin [veri konumunu nerede bulabilirsiniz?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) bölümünde bulabilirsiniz.

Avrupa 'da adres sağlayan müşteriler için Azure AD, Avrupa veri merkezlerindeki kimlik verilerinin çoğunu tutar. Bu belge, Azure AD Hizmetleri tarafından Avrupa dışında depolanan veriler hakkında bilgi sağlar.

## <a name="microsoft-azure-ad-multi-factor-authentication"></a>Microsoft Azure AD Multi-Factor Authentication

Bulut tabanlı Azure AD Multi-Factor Authentication için, kimlik doğrulaması kullanıcıya en yakın veri merkezinde tamamlanmıştır. Azure AD Multi-Factor Authentication için veri merkezleri Kuzey Amerika, Avrupa ve Asya Pasifik mevcuttur.

* Telefon çağrılarını kullanan Multi-Factor Authentication ABD veri merkezlerinden kaynaklanarak küresel sağlayıcılar tarafından yönlendirilir.
* SMS kullanarak çok faktörlü kimlik doğrulaması genel sağlayıcılar tarafından yönlendirilir.
* AB veri merkezlerinden kaynaklanan Microsoft Authenticator uygulama anında iletme bildirimleri kullanan Multi-Factor Authentication istekleri, AB veri merkezlerinde işlenir.
    * Apple Anında Iletme bildirimleri gibi cihaz satıcısına özgü hizmetler Avrupa dışında olabilir.
* AB veri merkezlerinden kaynaklanan OATH kodlarını kullanan Multi-Factor Authentication istekleri, AB 'de onaylanır.

Azure Multi-Factor Authentication Sunucusu (MFA sunucusu) ve bulut tabanlı Azure AD MFA tarafından hangi kullanıcı bilgilerinin toplandığı hakkında daha fazla bilgi için bkz. [azure Multi-Factor Authentication Kullanıcı verileri koleksiyonu](../authentication/howto-mfa-reporting-datacollection.md).

## <a name="password-based-single-sign-on-for-enterprise-applications"></a>Kurumsal uygulamalar için parola tabanlı tek Sign-On
 
Bir müşteri yeni bir kurumsal uygulama (Azure AD Galerisi veya Galeri olmayan) oluşturuyorsa ve parola tabanlı SSO 'yu etkinleştirse, uygulama oturum açma URL 'SI ve özel yakalama oturum açma alanları Birleşik Devletler depolanır. Bu özellik hakkında daha fazla bilgi için lütfen [parola tabanlı çoklu oturum açmayı yapılandırma](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md) bölümüne bakın

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C ilke yapılandırma verileri ve anahtar kapsayıcıları ABD veri merkezlerinde depolanır. Bunlar Kullanıcı kişisel verilerini içermez. İlke yapılandırması hakkında daha fazla bilgi için [Azure Active Directory B2C: Yerleşik ilkeler](../../active-directory-b2c/user-flow-overview.md) makalesine bakın.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B, ve ABD veri merkezlerinde yeniden yönlendirme URL 'SI bilgileriyle davetleri depolar. Bunlara ek olarak, B2B davetleri almayı iptal eden kullanıcıların e-posta adresi de ABD veri merkezlerinde depolanır.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS, kullanıcı verilerini müşterinin seçtiği Azure Sanal Ağı ile aynı konumda depolar. Ağınız Avrupa dışındaysa veriler Avrupa dışında çoğaltılır ve depolanır.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Microsoft Exchange Server 2013 ' de Federasyon
    
- Uygulama tanımlayıcısı (AppID)-Exchange kuruluşlarını belirlemek için Azure Active Directory kimlik doğrulama sistemi tarafından oluşturulan benzersiz bir sayıdır.
- Uygulama için onaylanan Federasyon etki alanları listesi
- Uygulamanın belirteç imzalama ortak anahtarı 

Microsoft Exchange Server 'da Federasyon hakkında daha fazla bilgi için bkz. [Federasyon: Exchange 2013 yardım](/exchange/federation-exchange-2013-help) makalesi.


## <a name="other-considerations"></a>Diğer önemli noktalar

Azure AD ile tümleştirilen hizmet ve uygulamaların kimlik verilerine erişimi vardır. Kimlik verilerinin belirli bir hizmet ve uygulama tarafından nasıl işlendiğini ve şirketinizin veri depolama gereksinimlerini karşılayıp karşılamadığını öğrenmek için kullandığınız her hizmeti ve uygulamayı değerlendirin.

Microsoft hizmetlerinin verileri depoladığı konumlar hakkında daha fazla bilgi için Microsoft Güven Merkezi'nin [Verileriniz nerede bulunur?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) bölümünü inceleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Yukarıda açıklanan özellik ve işlevlerden herhangi biri hakkında daha fazla bilgi için şu makalelere bakın:
- [Multi-Factor Authentication nedir?](../authentication/concept-mfa-howitworks.md)

- [Azure AD self servis parola sıfırlama](../authentication/concept-sspr-howitworks.md)

- [Azure Active Directory B2C nedir?](../../active-directory-b2c/overview.md)

- [Azure AD B2B işbirliği nedir?](../external-identities/what-is-b2b.md)

- [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md)