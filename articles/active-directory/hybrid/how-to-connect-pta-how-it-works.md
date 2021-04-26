---
title: 'Azure AD Connect: geçişli kimlik doğrulaması-nasıl kullanılır? | Microsoft Docs'
description: Bu makalede Azure Active Directory geçişli kimlik doğrulamanın nasıl çalıştığı açıklanmaktadır
services: active-directory
keywords: Azure AD Connect geçişli kimlik doğrulaması, Active Directory yüklemesi, Azure AD, SSO, çoklu oturum açma için gerekli bileşenler
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe92f761ac0b16da7c3cc3c69c1fa4b00f4e7579
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996602"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory geçişli kimlik doğrulaması: teknik derinlemesine bakış
Bu makale, Azure Active Directory (Azure AD) geçişli kimlik doğrulamanın nasıl çalıştığına ilişkin bir genel bakıştır. Ayrıntılı teknik ve güvenlik bilgileri için bkz. [güvenlik derinlemesine](how-to-connect-pta-security-deep-dive.md) bakış makalesi.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Azure Active Directory geçişli kimlik doğrulaması nasıl çalışır?

>[!NOTE]
>Geçiş kimlik doğrulamasının çalışması için önkoşul olarak, kullanıcıların Azure AD Connect kullanarak şirket içi Active Directory Azure AD 'ye sağlanması gerekir. Doğrudan kimlik doğrulaması, yalnızca bulutta bulunan kullanıcılar için geçerlidir.

Bir Kullanıcı Azure AD tarafından güvenli bir uygulamada oturum açmaya çalıştığında ve kiracı üzerinde doğrudan kimlik doğrulaması etkinse, aşağıdaki adımlar oluşur:

1. Kullanıcı bir uygulamaya (örneğin, [Outlook Web App](https://outlook.office365.com/owa/)) erişmeyi dener.
2. Kullanıcı önceden oturum açmamışsa, Kullanıcı Azure AD **Kullanıcı oturum açma** sayfasına yönlendirilir.
3. Kullanıcı Kullanıcı adını Azure AD oturum açma sayfasına girer ve sonra **İleri** düğmesini seçer.
4. Kullanıcı, parolasını Azure AD oturum açma sayfasına girer ve sonra **oturum aç** düğmesini seçer.
5. Azure AD, oturum açma isteğini alma sırasında, Kullanıcı adını ve parolayı (kimlik doğrulama aracılarının ortak anahtarı kullanılarak şifrelenir) bir sıraya koyar.
6. Şirket içi kimlik doğrulama Aracısı, Kullanıcı adını ve şifreli parolayı kuyruktan alır. Aracının kuyruktaki istekleri sıklıkla yoklamadığını, ancak önceden kurulan kalıcı bir bağlantı üzerinden istekleri alacağını unutmayın.
7. Aracı, özel anahtarını kullanarak parolanın şifresini çözer.
8. Aracı, Active Directory Federasyon Hizmetleri (AD FS) (AD FS) tarafından kullanılan benzer bir mekanizma olan standart Windows API 'Lerini kullanarak Kullanıcı adı ve parolayı Active Directory doğrular. Kullanıcı adı, şirket içi varsayılan Kullanıcı adı, genellikle `userPrincipalName` veya Azure AD Connect yapılandırılmış başka bir öznitelik olabilir (olarak bilinir `Alternate ID` ).
9. Şirket içi Active Directory etki alanı denetleyicisi (DC), isteği değerlendirir ve aracıya uygun yanıtı (başarı, hata, parola doldu veya Kullanıcı kilitli) döndürür.
10. Kimlik doğrulama Aracısı, sırasıyla bu yanıtı Azure AD 'ye geri döndürür.
11. Azure AD yanıtı değerlendirir ve kullanıcıya uygun şekilde yanıt verir. Örneğin, Azure AD kullanıcıyı hemen veya Azure AD Multi-Factor Authentication istekleri olarak imzalar.
12. Kullanıcı oturum açma işlemi başarılı olursa, Kullanıcı uygulamaya erişebilir.

Aşağıdaki diyagramda tüm bileşenler ve ilgili adımlar gösterilmektedir:

![Doğrudan Kimlik Doğrulama](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Sonraki adımlar
- [Geçerli sınırlamalar](how-to-connect-pta-current-limitations.md): hangi senaryoların desteklendiğini ve hangilerinin desteklenmediğini öğrenin.
- [Hızlı başlangıç](how-to-connect-pta-quick-start.md): Azure AD geçişli kimlik doğrulaması ile çalışmaya başlayın.
- [AD FS 'den geçişli kimlik doğrulamaya geçiş](https://aka.ms/adfstoPTADP) -AD FS (veya diğer Federasyon teknolojileri) üzerinden doğrudan kimlik doğrulamaya geçiş yapmak için ayrıntılı kılavuz.
- [Akıllı kilitleme](../authentication/howto-password-smart-lockout.md): Kullanıcı hesaplarını korumak için kiracınızda akıllı kilitleme özelliğini yapılandırın.
- [Sık sorulan sorular](how-to-connect-pta-faq.md): sık sorulan soruların yanıtlarını bulun.
- [Sorun giderme](tshoot-connect-pass-through-authentication.md): doğrudan kimlik doğrulama özelliğiyle yaygın sorunları çözmeyi öğrenin.
- [Güvenlik derinlemesine](how-to-connect-pta-security-deep-dive.md)bakış: doğrudan kimlik doğrulama özelliği hakkında ayrıntılı teknik bilgiler alın.
- [Azure AD sorunsuz SSO](how-to-connect-sso.md): Bu tamamlayıcı özellik hakkında daha fazla bilgi edinin.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): yeni özellik isteklerini dosya olarak yüklemek Için Azure Active Directory forumunu kullanın.

