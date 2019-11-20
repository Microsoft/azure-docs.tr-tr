---
title: 'Azure AD Connect: Geçişli kimlik doğrulaması | Microsoft Docs'
description: Bu makalede Azure Active Directory (Azure AD) geçişli kimlik doğrulaması ve kullanıcıların parolalarını şirket içi Active Directory karşı doğrulayarak Azure AD oturum açma işlemlerinin nasıl izin verdiği açıklanmaktadır.
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
ms.topic: conceptual
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 814c81b6092c4af3778617e165a0bdbce09d71d7
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779117"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Azure Active Directory geçişli kimlik doğrulamasıyla Kullanıcı oturumu açma

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Azure Active Directory geçişli kimlik doğrulaması nedir?

Azure Active Directory (Azure AD) geçişli kimlik doğrulaması, kullanıcılarınızın aynı parolaları kullanarak hem şirket içi hem de bulut tabanlı uygulamalarda oturum açmasına olanak tanır. Bu özellik hatırlamaları gereken parola sayısını azaltarak kullanıcılarınıza daha iyi bir deneyim sağlar ve oturum açma bilgileri muhtemelen daha az unutulacağından BT yardım masası maliyetlerinizi de düşürür. Kullanıcılar Azure AD kullanarak oturum açtığında, bu özellik kullanıcıların parolalarını şirket içi Active Directory doğrudan doğrular.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Bu özellik, kuruluşlarda bulut kimlik doğrulamasının aynı avantajını sağlayan [Azure AD Parola karması eşitleme](how-to-connect-password-hash-synchronization.md)'nin bir alternatifidir. Bununla birlikte, şirket içi Active Directory güvenlik ve parola ilkelerini zorlamak isteyen kuruluşlar, bunun yerine doğrudan kimlik doğrulamasını kullanmayı seçebilir. Çeşitli Azure AD oturum açma yöntemlerinin ve kuruluşunuzun doğru oturum açma yönteminin bir karşılaştırması için [Bu kılavuzu](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) gözden geçirin.

![Azure AD geçişli kimlik doğrulaması](./media/how-to-connect-pta/pta1.png)

Doğrudan kimlik doğrulamayı [sorunsuz çoklu oturum açma](how-to-connect-sso.md) özelliğiyle birleştirebilirsiniz. Bu şekilde, kullanıcılarınız Şirket ağınızdaki şirket makinelerinde bulunan uygulamalara erişirken, oturum açmak için parolalarını yazmaları gerekmez.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Azure AD geçişli kimlik doğrulaması kullanmanın temel avantajları

- *Harika kullanıcı deneyimi*
  - Kullanıcılar, hem şirket içi hem de bulut tabanlı uygulamalarda oturum açmak için aynı parolaları kullanır.
  - Kullanıcılar, parola ile ilgili sorunları çözmede BT yardım masasına daha az zaman harcamaktadır.
  - Kullanıcılar, buluttaki [self servis parola yönetimi](../authentication/active-directory-passwords-overview.md) görevlerini tamamlayabilir.
- *Dağıtımı kolay & Yönet*
  - Karmaşık şirket içi dağıtımlar veya ağ yapılandırmasına gerek yoktur.
  - Yalnızca bir basit aracının şirket içinde yüklü olmasını gerektirir.
  - Yönetim yükü yok. Aracı iyileştirmeleri ve hata düzeltmelerini otomatik olarak alır.
- *Güvenlik*
  - Şirket içi parolalar hiçbir biçimde bulutta depolanmaz.
  - Multi-Factor Authentication (MFA) dahil olmak üzere [Azure AD koşullu erişim ilkeleri](../active-directory-conditional-access-azure-portal.md)ile sorunsuz çalışarak kullanıcı hesaplarınızı korur, [eski kimlik doğrulamasını engellemeyi](../conditional-access/conditions.md) ve [deneme yanılma saldırılarına zorlar](../authentication/howto-password-smart-lockout.md).
  - Aracı yalnızca ağınızın içinden giden bağlantılar oluşturur. Bu nedenle, aracıyı DMZ olarak da bilinen bir çevre ağına yüklemek için gerekli değildir.
  - Bir aracı ve Azure AD arasındaki iletişimin sertifika tabanlı kimlik doğrulaması kullanılarak güvenliği sağlanır. Bu sertifikalar Azure AD tarafından her birkaç ayda bir otomatik olarak yenilenir.
- *Yüksek oranda kullanılabilir*
  - Birden fazla şirket içi sunucuya, oturum açma isteklerinin yüksek düzeyde kullanılabilirliğini sağlamak için ek aracılar yüklenebilir.

## <a name="feature-highlights"></a>Özellik vurguları

- , Tüm Web tarayıcısı tabanlı uygulamalarda ve [modern kimlik doğrulaması](https://aka.ms/modernauthga)kullanan Microsoft Office istemci uygulamalarında Kullanıcı oturum açmayı destekler.
- Oturum açma kullanıcı adları, şirket içi varsayılan Kullanıcı adı (`userPrincipalName`) veya Azure AD Connect yapılandırılmış başka bir öznitelik (olarak `Alternate ID`bilinir) olabilir.
- Özelliği, kullanıcılarınızın güvenliğini sağlamaya yardımcı olmak için Multi-Factor Authentication (MFA) gibi [koşullu erişim](../active-directory-conditional-access-azure-portal.md) özellikleriyle sorunsuz bir şekilde çalışır.
- Yaygın olarak kullanılan parolalara bakarak şirket içi Active Directory ve parola korumasına yönelik parola geri yazma özelliği de dahil olmak üzere bulut tabanlı [self servis parola yönetimiyle](../authentication/active-directory-passwords-overview.md)tümleşiktir.
- AD ormanlarınız arasında orman güvenleri varsa ve ad soneki yönlendirmesi doğru yapılandırılmışsa, çok ormanlı ortamlar desteklenir.
- Bu, ücretsiz bir özelliktir ve Azure AD 'nin ücretli sürümlerinin kullanılmasını gerektirmez.
- Bu, [Azure AD Connect](whatis-hybrid-identity.md)aracılığıyla etkinleştirilebilir.
- Parola doğrulama isteklerini dinleyen ve yanıt veren hafif bir şirket içi aracı kullanır.
- Birden çok aracı yüklemek, oturum açma isteklerinin yüksek kullanılabilirliğini sağlar.
- Şirket içi hesaplarınızı buluttaki deneme yanılma saldırılarına karşı [korur](../authentication/howto-password-smart-lockout.md) .

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç](how-to-connect-pta-quick-start.md) -Azure AD geçişli kimlik doğrulaması ile çalışmaya başlayın.
- [AD FS 'den geçişli kimlik doğrulamaya geçiş](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) -AD FS (veya diğer Federasyon teknolojileri) üzerinden doğrudan kimlik doğrulamaya geçiş yapmak için ayrıntılı kılavuz.
- [Akıllı kilitleme](../authentication/howto-password-smart-lockout.md) -Kullanıcı hesaplarını korumak için kiracınızda akıllı kilitleme özelliğini yapılandırın.
- [Geçerli sınırlamalar](how-to-connect-pta-current-limitations.md) -hangi senaryoların desteklendiğini ve hangilerinin hangilerinin desteklenmediğini öğrenin.
- [Teknik kapsamlı](how-to-connect-pta-how-it-works.md) bakış-bu özelliğin nasıl çalıştığını anlayın.
- [Sık sorulan sorular](how-to-connect-pta-faq.md) -sık sorulan soruların yanıtları.
- [Sorun giderme](tshoot-connect-pass-through-authentication.md) -özellikle ilgili yaygın sorunları çözmeyi öğrenin.
- [Güvenlik derinlemesine](how-to-connect-pta-security-deep-dive.md) bakış-özellikle ilgili ayrıntılı teknik bilgiler.
- [Azure AD sorunsuz SSO](how-to-connect-sso.md) -bu tamamlayıcı özellik hakkında daha fazla bilgi edinin.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -yeni özellik isteklerini dosyalama.
