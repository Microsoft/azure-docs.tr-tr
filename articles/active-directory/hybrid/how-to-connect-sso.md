---
title: 'Azure AD Connect: kesintisiz tek Sign-On | Microsoft Docs'
description: Bu konuda, Azure Active Directory (Azure AD) sorunsuz tek Sign-On ve şirket ağınız içindeki kurumsal masaüstü kullanıcıları için nasıl doğru bir çoklu oturum açma sağlayabileceğinizi açıklanmaktadır.
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
ms.topic: how-to
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeede88bfbe024a66e40270240d32488e581dd77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517710"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory Sorunsuz Çoklu Oturum Açma

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Sorunsuz çoklu oturum açma Azure Active Directory nedir?

Azure Active Directory Sorunsuz Çoklu Oturum Açma (Azure AD Sorunsuz SSO) özelliği, kurumsal ağınıza bağlı kuruluş cihazlarını kullanan kullanıcıların otomatik olarak oturum açmasını sağlar. Etkinleştirildiğinde, kullanıcıların Azure AD 'de oturum açması için parolalarını girmesi gerekmez ve genellikle kullanıcı adlarını bile yazmalarına gerek kalmaz. Bu özellik kullanıcılarınızın şirket içi ek bileşenlere ihtiyaç duymadan bulut tabanlı uygulamalarınıza kolayca erişmesini sağlar.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Kesintisiz SSO, [Parola karması eşitlemesi](how-to-connect-password-hash-synchronization.md) veya [geçişli kimlik doğrulaması](how-to-connect-pta.md) oturum açma yöntemleriyle birleştirilebilir. Sorunsuz SSO Active Directory Federasyon Hizmetleri (AD FS) (ADFS) için geçerli _değildir_ .

![Kesintisiz tek Sign-On](./media/how-to-connect-sso/sso1.png)

## <a name="sso-via-primary-refresh-token-vs-seamless-sso"></a>Birincil yenileme belirteci ve sorunsuz SSO ile SSO

Windows 10, Windows Server 2016 ve sonraki sürümlerinde, SSO 'yu birincil yenileme belirteci (PRT) aracılığıyla kullanmanız önerilir. Windows 7 ve 8,1 için sorunsuz SSO kullanmanız önerilir.
Sorunsuz SSO Kullanıcı cihazının etki alanına katılmış olmasını gerektirir, ancak Windows 10 [Azure AD 'ye katılmış cihazlarda](../devices/concept-azure-ad-join.md) veya [karma Azure AD 'ye katılmış cihazlarda](../devices/concept-azure-ad-join-hybrid.md)kullanılmaz. Azure AD 'ye katılmış, hibrit Azure AD 'ye katılmış ve Azure AD 'ye kayıtlı cihazlarda SSO, [birincil yenileme belirtecine (PRT)](../devices/concept-primary-refresh-token.md) göre çalışmaktadır

PRT aracılığıyla SSO, karma Azure AD 'ye katılmış, Azure AD 'ye katılmış veya kişisel kayıtlı cihazlarda Iş veya okul hesabı ekle yoluyla cihazlar Azure AD 'ye kaydedildikten sonra çalışır. Windows 10 ile PRT kullanarak SSO 'nun nasıl çalıştığı hakkında daha fazla bilgi için bkz. [birincil yenileme belirteci (prt) ve Azure AD](../devices/concept-primary-refresh-token.md)


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

- Oturum açma Kullanıcı adı, şirket içi varsayılan Kullanıcı adı ( `userPrincipalName` ) veya Azure AD Connect () içinde yapılandırılmış başka bir öznitelik olabilir `Alternate ID` . Her iki kullanım çalışması da çalışır çünkü sorunsuz SSO, `securityIdentifier` Azure AD 'de karşılık gelen Kullanıcı nesnesini aramak Için Kerberos anahtarındaki talebi kullanır.
- Sorunsuz SSO, fırsatçı bir özelliktir. Herhangi bir nedenle başarısız olursa, Kullanıcı oturum açma deneyimi normal davranışına geri döner. Yani, kullanıcının oturum açma sayfasında parolasını girmesi gerekir.
- Bir uygulama (örneğin,  `https://myapps.microsoft.com/contoso.com` ) bir `domain_hint` (OpenID Connect) veya `whr` (SAML) parametresini ilettiğinde, kiracınızı tanımlar veya `login_hint` Kullanıcı parametresini parametre olarak tanımlar, Azure AD oturum açma isteğinde kullanıcılar Kullanıcı adları veya parolalar girmeden otomatik olarak oturum açar.
- Ayrıca, bir uygulama (örneğin, `https://contoso.sharepoint.com` ) Azure AD uç noktalarına kiracı olarak ayarlanan `https://login.microsoftonline.com/contoso.com/<..>` veya `https://login.microsoftonline.com/<tenant_ID>/<..>` Azure AD 'nin ortak uç noktası yerine, veya-olan bir olan oturum açma isteklerini gönderen bir sessiz oturum açma deneyimi alırlar `https://login.microsoftonline.com/common/<...>` .
- Oturumu kapatma destekleniyor. Bu, kullanıcıların sorunsuz SSO 'yu otomatik olarak kullanarak oturum açmak yerine farklı bir Azure AD hesabı seçmesine olanak sağlar.
- 16.0.8730. xxxx ve üzeri sürümleriyle birlikte Microsoft 365 Win32 istemcileri (Outlook, Word, Excel ve diğerleri) etkileşimli olmayan bir akış kullanılarak desteklenir. OneDrive için, [OneDrive sessiz yapılandırma özelliğini](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) bir sessiz oturum açma deneyimi için etkinleştirmeniz gerekir.
- Bu, Azure AD Connect aracılığıyla etkinleştirilebilir.
- Bu, ücretsiz bir özelliktir ve Azure AD 'nin ücretli sürümlerinin kullanılmasını gerektirmez.
- Kerberos kimlik doğrulaması yeteneğine sahip platformlar ve tarayıcılarda [modern kimlik doğrulamayı](/office365/enterprise/modern-auth-for-office-2013-and-2016) destekleyen Web tarayıcı tabanlı Istemcilerde ve Office istemcilerinde desteklenir:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Evet\*|Yes|Yes|Evet\*\*\*|Yok
|Windows 8.1|Yes\*|Yes\*\*\*|Yes|Evet\*\*\*|Yok
|Windows 8|Yes\*|YOK|Evet|Evet\*\*\*|Yok
|Windows 7|Evet\*|YOK|Evet|Evet\*\*\*|Yok
|Windows Server 2012 R2 veya üzeri|Yes\*\*|YOK|Evet|Evet\*\*\*|Yok
|Mac OS X|Yok|Yok|Evet\*\*\*|Evet\*\*\*|Evet\*\*\*


\*Internet Explorer sürüm 10 veya üstünü gerektirir.

\*\*Internet Explorer sürüm 10 veya üstünü gerektirir. Gelişmiş korumalı modu devre dışı bırakın.

\*\*\*[Ek yapılandırma](how-to-connect-sso-quick-start.md#browser-considerations)gerektirir.

\*\*\*\*Microsoft Edge sürüm 77 veya üstünü gerektirir.

## <a name="next-steps"></a>Sonraki adımlar

- [**Hızlı başlangıç**](how-to-connect-sso-quick-start.md) -Azure AD sorunsuz SSO 'yu alın ve çalıştırın.
- [**Dağıtım planı**](../manage-apps/plan-sso-deployment.md) -adım adım dağıtım planı.
- [**Teknik kapsamlı**](how-to-connect-sso-how-it-works.md) bakış-bu özelliğin nasıl çalıştığını anlayın.
- [**Sık sorulan sorular**](how-to-connect-sso-faq.md) -sık sorulan soruların yanıtları.
- [**Sorun giderme**](tshoot-connect-sso.md) -özellikle ilgili yaygın sorunları çözmeyi öğrenin.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -yeni özellik isteklerini dosyalama.
