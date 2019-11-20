---
title: 'Azure AD Connect: sorunsuz çoklu oturum açma | Microsoft Docs'
description: Bu konuda, Azure Active Directory (Azure AD) sorunsuz çoklu oturum açma ve Şirket ağınızdaki kurumsal masaüstü kullanıcıları için isteğe bağlı çoklu oturum açma sağlamanıza nasıl izin verdiği açıklanmaktadır.
services: active-directory
keywords: Azure AD Connect nedir, yükler Active Directory, Azure AD, SSO, çoklu oturum açma için gerekli bileşenler
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7791e7b50a963d2f92a2cbc460e36f9e83bb1b52
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025708"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Kesintisiz çoklu oturum açma Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Sorunsuz çoklu oturum açma Azure Active Directory nedir?

Azure Active Directory Sorunsuz Çoklu Oturum Açma (Azure AD Sorunsuz SSO) özelliği, kurumsal ağınıza bağlı kuruluş cihazlarını kullanan kullanıcıların otomatik olarak oturum açmasını sağlar. Etkinleştirildiğinde, kullanıcıların Azure AD 'de oturum açması için parolalarını girmesi gerekmez ve genellikle kullanıcı adlarını bile yazmalarına gerek kalmaz. Bu özellik kullanıcılarınızın şirket içi ek bileşenlere ihtiyaç duymadan bulut tabanlı uygulamalarınıza kolayca erişmesini sağlar.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Kesintisiz SSO, [Parola karması eşitlemesi](how-to-connect-password-hash-synchronization.md) veya [geçişli kimlik doğrulaması](how-to-connect-pta.md) oturum açma yöntemleriyle birleştirilebilir. Sorunsuz SSO Active Directory Federasyon Hizmetleri (AD FS) (ADFS) için geçerli _değildir_ .

![Kesintisiz çoklu oturum açma](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Sorunsuz SSO, kullanıcının cihazının **etki alanına katılmış**olmasını gerektirir, ancak CIHAZıN [Azure AD 'ye katılmış](../active-directory-azureadjoin-overview.md)olması gerekmez.

## <a name="key-benefits"></a>Önemli avantajlar

- *Harika kullanıcı deneyimi*
  - Kullanıcılar, hem şirket içi hem de bulut tabanlı uygulamalarda otomatik olarak imzalanır.
  - Kullanıcıların parolalarını tekrar tekrar girmesi gerekmez.
- *Dağıtımı kolay & Yönet*
  - Şirket içinde bu işi yapmak için ek bileşen gerekmez.
  - Herhangi bir bulut kimlik doğrulama yöntemi ile, [Parola karması eşitlemesi](how-to-connect-password-hash-synchronization.md) veya [geçişli kimlik doğrulaması](how-to-connect-pta.md)ile birlikte çalışarak.
  - Grup ilkesi kullanarak kullanıcılarınızın bazılarına veya tümüne dağıtılabilir.
  - Windows 10 olmayan cihazları, herhangi bir AD FS altyapısına gerek duymadan Azure AD ile kaydedin. Bu yetenek, [çalışma alanına katılma istemcisinin](https://www.microsoft.com/download/details.aspx?id=53554)2,1 veya sonraki bir sürümünü kullanmanızı gerektirir.

## <a name="feature-highlights"></a>Özellik vurguları

- Oturum açma Kullanıcı adı, şirket içi varsayılan Kullanıcı adı (`userPrincipalName`) ya da Azure AD Connect (`Alternate ID`) yapılandırılmış başka bir öznitelik olabilir. Her iki kullanım çalışması da çalışır çünkü sorunsuz SSO, Azure AD 'de karşılık gelen Kullanıcı nesnesini aramak için Kerberos anahtarındaki `securityIdentifier` talebini kullanır.
- Sorunsuz SSO, fırsatçı bir özelliktir. Herhangi bir nedenle başarısız olursa, Kullanıcı oturum açma deneyimi normal davranışına geri döner. Yani, kullanıcının oturum açma sayfasında parolasını girmesi gerekir.
- Bir uygulama (örneğin, `https://myapps.microsoft.com/contoso.com`), kiracınızı tanımlayan bir `domain_hint` (OpenID Connect) veya `whr` (SAML) parametresini ya da `login_hint` parametresini (kullanıcıyı tanımlama), Azure AD oturum açma isteğinde otomatik olarak oturum açanlar olmadan oturum açabilirler Kullanıcı adları veya parolalar girme.
- Ayrıca, bir uygulama (örneğin, `https://contoso.sharepoint.com`) Azure AD uç noktalarına kiracı olarak ayarlanan oturum açma isteklerini, `https://login.microsoftonline.com/contoso.com/<..>` veya `https://login.microsoftonline.com/<tenant_ID>/<..>`-diğer bir deyişle, `https://login.microsoftonline.com/common/<...>`.
- Oturumu kapatma destekleniyor. Bu, kullanıcıların sorunsuz SSO 'yu otomatik olarak kullanarak oturum açmak yerine farklı bir Azure AD hesabı seçmesine olanak sağlar.
- 16.0.8730. xxxx ve üzeri sürümleriyle Office 365 Win32 istemcileri (Outlook, Word, Excel ve diğerleri) etkileşimli olmayan bir akış kullanılarak desteklenir. OneDrive için, [OneDrive sessiz yapılandırma özelliğini](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) bir sessiz oturum açma deneyimi için etkinleştirmeniz gerekir.
- Bu, Azure AD Connect aracılığıyla etkinleştirilebilir.
- Bu, ücretsiz bir özelliktir ve Azure AD 'nin ücretli sürümlerinin kullanılmasını gerektirmez.
- Kerberos kimlik doğrulaması yeteneğine sahip platformlar ve tarayıcılarda [modern kimlik doğrulamayı](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) destekleyen Web tarayıcı tabanlı Istemcilerde ve Office istemcilerinde desteklenir:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Evet @ no__t-0|Yes|Yes|Evet @ no__t-0 @ no__t-1 @ no__t-2|Yok
|Windows 8.1|Evet @ no__t-0|Yok|Yes|Evet @ no__t-0 @ no__t-1 @ no__t-2|Yok
|Windows 8|Evet @ no__t-0|Yok|Yes|Evet @ no__t-0 @ no__t-1 @ no__t-2|Yok
|Windows 7|Evet @ no__t-0|Yok|Yes|Evet @ no__t-0 @ no__t-1 @ no__t-2|Yok
|Windows Server 2012 R2 veya üzeri|Evet @ no__t-0 @ no__t-1|Yok|Yes|Evet @ no__t-0 @ no__t-1 @ no__t-2|Yok
|Mac OS X|Yok|Yok|Evet @ no__t-0 @ no__t-1 @ no__t-2|Evet @ no__t-0 @ no__t-1 @ no__t-2|Evet @ no__t-0 @ no__t-1 @ no__t-2


\*Internet Explorer sürümleri 10 veya üzerini gerektirir

\* @ no__t-1ınternet Explorer sürümleri 10 veya üstünü gerektirir. Gelişmiş korumalı modu devre dışı bırak

\* @ no__t-1 @ no__t- [2ek yapılandırma](how-to-connect-sso-quick-start.md#browser-considerations) gerektirir

>[!NOTE]
>Windows 10 ' da, Azure AD 'ye yönelik en iyi çoklu oturum açma deneyimi için [Azure AD JOIN](../active-directory-azureadjoin-overview.md) 'in kullanılması önerilir.

## <a name="next-steps"></a>Sonraki adımlar

- [**Hızlı başlangıç**](how-to-connect-sso-quick-start.md) -Azure AD sorunsuz SSO 'yu alın ve çalıştırın.
- [**Dağıtım planı**](https://aka.ms/deploymentplans/sso) -adım adım dağıtım planı.
- [**Teknik kapsamlı**](how-to-connect-sso-how-it-works.md) bakış-bu özelliğin nasıl çalıştığını anlayın.
- [**Sık sorulan sorular**](how-to-connect-sso-faq.md) -sık sorulan soruların yanıtları.
- [**Sorun giderme**](tshoot-connect-sso.md) -özellikle ilgili yaygın sorunları çözmeyi öğrenin.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -yeni özellik isteklerini dosyalama.

