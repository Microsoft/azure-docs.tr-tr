---
title: Windows sanal masaüstü kimlik doğrulaması-Azure
description: Windows sanal masaüstü için kimlik doğrulama yöntemleri.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 32e113bf02a2cbd21b70805396c212a1f9467382
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448245"
---
# <a name="supported-authentication-methods"></a>Desteklenen kimlik doğrulama yöntemleri

Bu makalede, Windows sanal masaüstü 'nde kullanabileceğiniz hangi kimlik doğrulama türleri hakkında kısa bir genel bakış sunuyoruz.

## <a name="session-host-authentication"></a>Oturum Ana bilgisayar kimlik doğrulaması

Windows sanal masaüstü, oturum ana bilgisayar kimlik doğrulaması için hem NT LAN Manager (NTLM) hem de Kerberos destekler. Ancak, Kerberos kullanmak için istemcinin, etki alanı denetleyicisinde çalışan bir Anahtar Dağıtım Merkezi (KDC) hizmetinden Kerberos güvenlik biletleri alması gerekir. Bilet almak için, istemcinin etki alanı denetleyicisine doğrudan bir görüş satırı olması gerekir. Şirket ağınızı kullanarak doğrudan bir görüş satırı edinebilirsiniz. Ayrıca, kurumsal ağınıza bir VPN bağlantısı kullanabilir veya bir [KDC ara sunucusu](key-distribution-center-proxy.md)kurabilirsiniz.

Bunlar şu anda desteklenen oturum açma yöntemleridir:

- Windows Masaüstü istemcisi
    - Kullanıcı adı ve parola
    - Kart
    - Iş için Windows Hello (yalnızca sertifika güveni)
- Windows Mağazası istemcisi
    - Kullanıcı adı ve parola
- Web istemcisi
    - Kullanıcı adı ve parola
- Android
    - Kullanıcı adı ve parola
- iOS
    - Kullanıcı adı ve parola
- Mac OS
    - Kullanıcı adı ve parola

>[!NOTE]
>Akıllı kart ve Iş için Windows Hello yalnızca Kerberos kullanarak oturum açabilir. Kerberos ile oturum açmak için etki alanı denetleyicisine veya bir [KDC ara sunucusuna](key-distribution-center-proxy.md)yönelik bir görüş satırı gerekir.

## <a name="hybrid-identity"></a>Karma kimlik

Windows sanal masaüstü, Active Directory Federasyon Hizmetleri (AD FS) (ADFS) kullanılarak Federasyon dahil olmak üzere Azure Active Directory (AD) aracılığıyla [karma kimlikleri](../active-directory/hybrid/whatis-hybrid-identity.md) destekler. Kullanıcıların Azure AD aracılığıyla bulunabilir olması gerektiğinden, Windows sanal masaüstü ADFS ile tek başına Active Directory dağıtımlarını desteklemez.

## <a name="single-sign-on-sso"></a>Çoklu oturum açma (SSO)

Windows sanal masaüstü Şu anda SSO için Active Directory Federasyon Hizmetleri (AD FS) (ADFS) desteklememektedir.

Oturum Ana bilgisayarı için kimlik bilgileriniz istenmeden kaçınmanın tek yolu, bunları istemciye kaydetmaktır. Diğer kullanıcıların kaynaklarınıza erişmesini engellemek için bunu yalnızca güvenli cihazlarla yapmanızı öneririz.

## <a name="next-steps"></a>Sonraki adımlar

Dağıtımınızı güvenli tutmanın diğer yollarını merak ediyor musunuz? [En Iyi güvenlik uygulamalarına](security-guide.md)göz atın.
