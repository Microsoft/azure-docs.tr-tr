---
title: İki aşamalı doğrulama Azure AD MFA ve ADFS-Azure Active Directory
description: Bu, Azure AD MFA ve AD FS kullanmaya başlamanızı açıklayan Azure AD Multi-Factor Authentication sayfasıdır.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0023d40fdc26fa1c42a67ce78a9259643098abb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741414"
---
# <a name="getting-started-with-azure-ad-multi-factor-authentication-and-active-directory-federation-services"></a>Azure AD Multi-Factor Authentication ve Active Directory Federasyon Hizmetleri (AD FS) kullanmaya başlama

<center>

![Azure AD MFA ve ADFS başlangıç](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Kuruluşunuz şirket içi Active Directory AD FS kullanarak Azure Active Directory federe içeriyorsa, Azure AD Multi-Factor Authentication kullanmanın iki seçeneği vardır.

* Azure AD Multi-Factor Authentication veya Active Directory Federasyon Hizmetleri (AD FS) kullanarak bulut kaynaklarını güvenli hale getirme
* Azure Multi-Factor Authentication Sunucusu kullanarak bulut ve şirket içi kaynakları güvenli hale getirme

Aşağıdaki tabloda, Azure AD Multi-Factor Authentication ve AD FS ile kaynakları güvenli hale getirme arasındaki doğrulama deneyimi özetlenmektedir.

| Doğrulama Deneyimi - Tarayıcı Tabanlı Uygulamalar | Doğrulama Deneyimi - Tarayıcı Tabanlı Olmayan Uygulamalar |
|:--- |:--- |
| Azure AD Multi-Factor Authentication kullanarak Azure AD kaynaklarını güvenli hale getirme |<li>İlk doğrulama adımı, AD FS kullanılarak şirket içinde gerçekleştirilir.</li> <li>İkinci adım, bulut kimlik doğrulaması kullanılarak yapılan telefon tabanlı yöntemdir.</li> |
| Active Directory Federasyon Hizmetleri’ni kullanarak Azure AD kaynaklarını güvenli hale getirme |<li>İlk doğrulama adımı, AD FS kullanılarak şirket içinde gerçekleştirilir.</li><li>İkinci adım, talebin onaylanmasıyla şirket içinde gerçekleştirilir.</li> |

Federasyon kullanıcıları için uygulama parolaları ile ilgili uyarılar:

* Uygulama parolaları, bulut kimlik doğrulaması kullanılarak doğrulanır ve bu nedenle federasyonu atlar. Federasyon, yalnızca uygulama parolası ayarlanırken etkin şekilde kullanılır.
* Şirket içi İstemci Erişim Denetimi ayarları, uygulama parolaları tarafından onaylanmaz.
* Uygulama parolaları için şirket içi kimlik doğrulaması günlüğe kaydetme özelliğini kaybedersiniz.
* Hesabı devre dışı bırakma/silme işlemi, bulut kimliğinde dizin eşitlemesi ve uygulama parolalarının devre dışı bırakılması/silinmesi nedeniyle üç saate kadar sürebilir.

Azure AD Multi-Factor Authentication veya Azure Multi-Factor Authentication Sunucusu AD FS ile ayarlama hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure AD Multi-Factor Authentication ve AD FS kullanarak bulut kaynaklarını güvenli hale getirme](howto-mfa-adfs.md)
* [Windows Server 2012 R2 AD FS ile Azure Multi-Factor Authentication Sunucusu kullanarak bulut ve şirket içi kaynakları güvenli hale getirme](howto-mfaserver-adfs-2012.md)
* [AD FS 2.0 ile Azure Multi-Factor Authentication Sunucusu kullanarak bulut ve şirket içi kaynakları güvenli hale getirin.](howto-mfaserver-adfs-2.md)
