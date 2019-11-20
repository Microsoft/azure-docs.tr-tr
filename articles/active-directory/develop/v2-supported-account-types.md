---
title: Uygulamalarda desteklenen hesaplar (seyirci)-Microsoft Identity platform
description: Uygulamalarda izleyiciler ve desteklenen hesap türleri hakkında kavramsal belgeler
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 929479f2816ef979edb26ba5aa00cf449c49b70a
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73942164"
---
# <a name="supported-account-types"></a>Desteklenen hesap türleri

Bu makalede, uygulamalarda desteklenen hesap türleri (bazen hedef kitleleri olarak adlandırılır) açıklanmaktadır

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Microsoft Identity platform uygulamalarında desteklenen hesap türleri

Microsoft Azure genel bulutta, çoğu uygulama türü kullanıcılara herhangi bir kitle ile oturum açabilir:

- Iş kolu (LOB) uygulaması yazıyorsanız, kullanıcılar kendi kuruluşunuzda oturum açabilir. Böyle bir uygulama bazen **tek kiracı**olarak adlandırılır.
- ISV iseniz, kullanıcıları oturum açan bir uygulama yazabilirsiniz:

  - Herhangi bir kuruluşta. Böyle bir uygulama, **çok kiracılı** bir Web uygulaması olarak adlandırılır. Bazen kullanıcıların iş veya okul hesaplarıyla oturum açdığına ilişkin bilgi edineceksiniz.
  - İş veya okul veya kişisel Microsoft hesabı.
  - Yalnızca kişisel Microsoft hesabı.
    > [!NOTE]
    > Şu anda Microsoft Identity platformu kişisel Microsoft hesaplarını yalnızca **iş veya okul veya Microsoft kişisel hesapları**için bir uygulama kaydederek destekler ve sonra, uygulamayı oluştururken `https://login.microsoftonline.com/consumers`gibi BIR Azure AD yetkilisi belirterek uygulamanın kodunda oturum açmasını kısıtlar.

- Bir işletmeyi tüketiciler uygulamasına yazıyorsanız, kullanıcılar Azure AD B2C kullanarak sosyal kimlikleri ile de oturum açabilirsiniz.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Bazı kimlik doğrulama akışları tüm hesap türlerini desteklemez

Bazı hesap türleri belirli kimlik doğrulama akışlarıyla kullanılamaz. Örneğin, masaüstünde, UWP uygulamalarında veya Daemon uygulamalarında:

- Daemon uygulamaları yalnızca Azure Active Directory kuruluşlarıyla birlikte kullanılabilir. Microsoft kişisel hesaplarını işlemek için Daemon uygulamalarını kullanmaya çalışmak mantıklı değildir (yönetici onayı hiçbir şekilde verilmeyecektir).  
- Tümleşik Windows kimlik doğrulaması akışını yalnızca iş veya okul hesaplarıyla (kuruluşunuzda veya herhangi bir kuruluşta) kullanabilirsiniz. Aslında, tümleşik Windows kimlik doğrulaması etki alanı hesaplarıyla birlikte çalışarak makinelerin etki alanına katılmış veya Azure AD 'ye katılmış olmasını gerektirir. Bu akış kişisel Microsoft hesapları için anlamlı değildir.
- [Kaynak sahibi parolası verme](./v2-oauth-ropc.md) (Kullanıcı adı/parola) kişisel Microsoft hesaplarıyla kullanılamaz. Gerçekten, kişisel Microsoft hesapları, kullanıcının her oturum açma oturumunda kişisel kaynaklara erişmesini gerektirir. Bu nedenle, bu davranış etkileşimli olmayan akışlarla uyumlu değildir.
- Cihaz kod akışı henüz kişisel Microsoft hesaplarıyla çalışmıyor.

## <a name="supported-account-types-in-national-clouds"></a>Ulusal bulutlarda desteklenen hesap türleri

 Uygulamalar, kullanıcıların [Ulusal bulutlarda](authentication-national-cloud.md)da oturum açabilir. Ancak, Microsoft kişisel hesapları bu bulutlarda (Bu bulutların tanımına göre) desteklenmez. Bu nedenle, desteklenen hesap türlerinin bu bulutlara, kuruluşunuzda (tek bir kiracıya) veya herhangi bir kuruluşa (çok kiracılı uygulamalar) karşı azaltıldı.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory 'de kiracı](./single-and-multi-tenant-apps.md) hakkında daha fazla bilgi edinin
- [Ulusal bulutlar](./authentication-national-cloud.md) hakkında daha fazla bilgi edinin
