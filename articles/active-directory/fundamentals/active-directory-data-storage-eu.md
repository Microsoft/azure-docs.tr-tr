---
title: Avrupalı müşteriler - Azure Active Directory için kimlik verileri depolama | Microsoft Docs
description: Azure Active Directory kimlik doğrulamayla ilgili veriler, Avrupalı müşteriler için depoladığı hakkında bilgi edinin.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 178f81cf42e5c57be4a0b69ada6560d46951a3a3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68942855"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Kimlik Azure Active Directory'de Avrupalı müşteriler için veri depolama
Kimlik verileri, Azure AD tarafından, Office 365 ve Azure gibi bir Microsoft Online hizmetine abone olurken kuruluşunuzun verdiği adrese bağlı olarak coğrafi bir konumda depolanır. Kimlik verilerinizin nerede depolandığı hakkında bilgi için, Microsoft Güven Merkezi ' nin [veri konumunu nerede bulabilirsiniz?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) bölümünde bulabilirsiniz.

Avrupa 'da adres sağlayan müşteriler için Azure AD, Avrupa veri merkezlerindeki kimlik verilerinin çoğunu tutar. Bu belge, Azure AD Hizmetleri tarafından Avrupa dışında depolanan veriler hakkında bilgi sağlar.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure Multi-Factor Authentication (MFA)
    
- Telefon görüşmeleri veya SMS kullanan tüm iki öğeli kimlik doğrulama ABD veri merkezlerinden kaynaklanıyorsa ve ayrıca küresel sağlayıcılar tarafından da yönlendirilir.
- Microsoft Authenticator uygulamasını kullanarak anında iletme bildirimleri ABD veri merkezlerinden kaynaklarından geliyor. Buna ek olarak, cihaz satıcısına özgü hizmetler de oynatma halinde gelebilir ve bu hizmetler Avrupa dışında olabilir.
- OATH kodları her zaman ABD'de doğrulanır. 

Azure Multi-Factor Authentication sunucusu (MFA sunucusu) ve bulut tabanlı Azure MFA tarafından hangi kullanıcı bilgilerinin toplandığı hakkında daha fazla bilgi için bkz. [Azure Multi-Factor Authentication Kullanıcı verileri toplama](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C ilke yapılandırma verileri ve anahtar kapsayıcıları ABD veri merkezlerinde depolanır. Bunlar Kullanıcı kişisel verilerini içermez. İlke konfigürasyonları hakkında daha fazla bilgi için [Azure Active Directory B2C bakın: Yerleşik ilkeler](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) makalesi.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B, ve ABD veri merkezlerinde yeniden yönlendirme URL 'SI bilgileriyle davetleri depolar. Bunlara ek olarak, B2B davetleri almayı iptal eden kullanıcıların e-posta adresi de ABD veri merkezlerinde depolanır.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory etki alanı Hizmetleri (Azure AD DS)

Azure AD DS, kullanıcı verilerini müşterinin seçtiği Azure Sanal Ağı ile aynı konumda depolar. Ağınız Avrupa dışındaysa veriler Avrupa dışında çoğaltılır ve depolanır.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Microsoft Exchange Server 2013 ' de Federasyon
    
- Uygulama tanımlayıcısı (AppID)-Exchange kuruluşlarını belirlemek için Azure Active Directory kimlik doğrulama sistemi tarafından oluşturulan benzersiz bir sayıdır.
- Uygulama için onaylanan Federasyon etki alanları listesi
- Uygulamanın belirteç imzalama ortak anahtarı 

Microsoft Exchange Server 'da Federasyon hakkında daha fazla bilgi için bkz [. Federasyon: Exchange 2013 yardım](https://docs.microsoft.com/exchange/federation-exchange-2013-help) makalesi.


## <a name="other-considerations"></a>Dikkat edilecek diğer noktalar

Azure AD ile tümleştirilen hizmet ve uygulamaların kimlik verilerine erişimi vardır. Kimlik verilerinin belirli bir hizmet ve uygulama tarafından nasıl işlendiğini ve şirketinizin veri depolama gereksinimlerini karşılayıp karşılamadığını öğrenmek için kullandığınız her hizmeti ve uygulamayı değerlendirin.

Microsoft hizmetlerinin verileri depoladığı konumlar hakkında daha fazla bilgi için Microsoft Güven Merkezi'nin [Verileriniz nerede bulunur?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) bölümünü inceleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Yukarıda açıklanan özellik ve işlevlerden herhangi biri hakkında daha fazla bilgi için şu makalelere bakın:
- [Multi-Factor Authentication nedir?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD self servis parola sıfırlama](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Azure Active Directory B2C nedir?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Azure AD B2B işbirliği nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
