---
title: B2B işbirliği Lisanslama Kılavuzu-Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B işbirliğinin ücretli Azure AD lisansları gerekmez, ancak B2B Konuk kullanıcıları için ücretli özellikler de edinebilirsiniz
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5968536d0bd0fbe2496fd9bf5fca86c7a1c4053d
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162258"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B işbirliği lisanslama kılavuzu

Azure Active Directory (Azure AD) işletmeden işletmeye (B2B) işbirliğiyle, dış kullanıcıları (veya "Konuk kullanıcıları") ücretli Azure AD hizmetlerinizi kullanacak şekilde davet edebilirsiniz. Bazı özellikler ücretsizdir, ancak ücretli Azure AD özellikleri için, kiracınızda çalışan veya Konuk olmayan bir kullanıcı için sahip olduğunuz her bir Azure AD sürümü lisansı için beş adede kadar Konuk Kullanıcı davet edebilirsiniz.

> [!NOTE]
> Azure AD fiyatlandırması ve B2B işbirliği özellikleri hakkında ayrıntılı bilgi için [Azure Active Directory fiyatlandırmasına](https://azure.microsoft.com/pricing/details/active-directory/) bakın.

B2B Konuk Kullanıcı lisanslama, 1:5 oranına göre otomatik olarak hesaplanır ve raporlanır. Şu anda, doğrudan Konuk kullanıcılara B2B Konuk kullanıcı lisansları atamak mümkün değildir.

Ayrıca, Konuk kullanıcılar ücretsiz Azure AD özelliklerini ek lisans gereksinimleri olmadan kullanabilir. Ücretli Azure AD lisanslarınız olmasa bile, konuk kullanıcıların ücretsiz Azure AD özelliklerine erişimi vardır. 

## <a name="examples-calculating-guest-user-licenses"></a>Örnekler: Konuk kullanıcı lisanslarını hesaplama
Kaç tane Konuk kullanıcının ücretli Azure AD hizmetlerine erişmesi gerektiğini belirledikten sonra, Konuk kullanıcıları gereken 1:5 oranında kapsayacak Azure AD ücretli lisanslarınızın bulunduğundan emin olun. Bazı örnekler şunlardır:

- Azure AD uygulamalarınıza veya hizmetlerine 100 Konuk Kullanıcı davet etmek ve erişim yönetimi ve sağlama sağlamak istiyorsunuz. Bu konuk kullanıcıların 50 ' de, MFA ve koşullu erişim gerektirmek gerekir, bu nedenle bu özellikler için 10 Azure AD Premium P1 lisansı gerekir. Konuk kullanıcılarınızla kimlik koruması özelliklerini kullanmayı planlıyorsanız, Konuk kullanıcıları kapsayan aynı 1:5 oranında Azure AD Premium P2 lisanslarına sahip olmanız gerekir.
- Tüm MFA gerektiren 60 Konuk kullanıcıları davet etmek istiyorsunuz, bu nedenle en az 12 Azure AD Premium P1 lisanslarına sahip olmanız gerekir. Azure AD Premium P1 lisanslarına sahip 10 çalışanlarınız var ve bu, 1:5 lisanslama oranının altında 50 adede kadar Konuk kullanıcıya izin verir. 10 ek Konuk kullanıcıyı kapsayacak iki ek Premium P1 lisansı satın almanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD B2B işbirliği üzerinde aşağıdaki kaynaklara bakın:

* [Azure Active Directory fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
* [Azure Active Directory B2B işbirliği hakkında sık sorulan sorular (SSS)](faq.md)
