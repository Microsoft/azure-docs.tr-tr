---
title: Karma kimlik bağlantı noktaları ve protokoller-Azure | Microsoft Docs
description: Bu sayfa, Azure AD Connect için açık olması gereken bağlantı noktaları için teknik başvuru sayfasıdır
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/02/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9277e35fceb382fbccd009e5bbfe63ce57b8361
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305168"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Karma Kimlik için Gereken Bağlantı Noktaları ve Protokoller
Aşağıdaki belge, karma kimlik çözümü uygulamaya yönelik gereken bağlantı noktaları ve protokollerle ilgili teknik bir başvurudur. Aşağıdaki çizimi kullanın ve ilgili tabloya başvurun.

![Azure AD Connect nedir?](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tablo 1-Azure AD Connect ve şirket içi AD
Bu tabloda, Azure AD Connect sunucusu ile şirket içi AD arasındaki iletişim için gereken bağlantı noktaları ve protokoller açıklanmaktadır.

| Protocol | Bağlantı Noktaları | Açıklama |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Hedef ormanda DNS aramaları. |
| Kerberos |88 (TCP/UDP) |AD ormanında Kerberos kimlik doğrulaması. |
| MS-RPC |135 (TCP/UDP) |Azure AD Connect sihirbazının ilk yapılandırması sırasında, AD ormanına bağlandığında ve parola eşitleme sırasında kullanılır. |
| LDAP |389 (TCP/UDP) |AD 'den veri içeri aktarma için kullanılır. Veriler Kerberos oturum & mühürle şifrelenir. |
| SMB | 445 (TCP/UDP) |Sorunsuz SSO tarafından, AD ormanında bir bilgisayar hesabı oluşturmak için kullanılır. |
| LDAP/SSL |636 (TCP/UDP) |AD 'den veri içeri aktarma için kullanılır. Veri aktarımı imzalanır ve şifrelenir. Yalnızca SSL kullanıyorsanız kullanılır. |
| PRC |49152-65535 (rastgele yüksek RPC bağlantı noktası) (TCP/UDP) |Azure AD Connect ilk yapılandırması sırasında, AD ormanlarına bağlandığında ve parola eşitleme sırasında kullanılır. Daha fazla bilgi için bkz. [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)ve [KB224196](https://support.microsoft.com/kb/224196) . |
|Winrs  | 5985 (TCP/UDP) |Yalnızca Azure AD Connect sihirbazıyla gMSA ile AD FS yüklüyorsanız kullanılır|
|AD DS Web Hizmetleri | 9389 (TCP/UDP) |Yalnızca Azure AD Connect sihirbazıyla gMSA ile AD FS yüklüyorsanız kullanılır |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tablo 2-Azure AD Connect ve Azure AD
Bu tabloda Azure AD Connect sunucusu ile Azure AD arasında iletişim için gereken bağlantı noktaları ve protokoller açıklanmaktadır.

| Protocol | Bağlantı Noktaları | Açıklama |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |SSL sertifikalarını doğrulamak için CRL 'Leri (sertifika Iptal listeleri) indirmek için kullanılır. |
| HTTPS |443(TCP/UDP) |Azure AD ile eşitlenmek için kullanılır. |

Güvenlik duvarınızda açmanız gereken URL 'Lerin ve IP adreslerinin bir listesi için bkz. [Office 365 URL 'leri ve IP adresi aralıkları](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tablo 3-Azure AD Connect ve AD FS Federasyon sunucuları/WAP
Bu tabloda, Azure AD Connect sunucusu ile AD FS Federasyon/WAP sunucuları arasındaki iletişim için gereken bağlantı noktaları ve protokoller açıklanmaktadır.  

| Protocol | Bağlantı Noktaları | Açıklama |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |SSL sertifikalarını doğrulamak için CRL 'Leri (sertifika Iptal listeleri) indirmek için kullanılır. |
| HTTPS |443(TCP/UDP) |Azure AD ile eşitlenmek için kullanılır. |
| Winrs |5985 |WinRM dinleyicisi |

## <a name="table-4---wap-and-federation-servers"></a>Tablo 4-WAP ve Federasyon sunucuları
Bu tabloda, Federasyon sunucuları ve WAP sunucuları arasındaki iletişim için gereken bağlantı noktaları ve protokoller açıklanmaktadır.

| Protocol | Bağlantı Noktaları | Açıklama |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Kimlik doğrulaması için kullanılır. |

## <a name="table-5---wap-and-users"></a>Tablo 5-WAP ve kullanıcılar
Bu tabloda, kullanıcılar ve WAP sunucuları arasındaki iletişim için gereken bağlantı noktaları ve protokoller açıklanmaktadır.

| Protocol | Bağlantı Noktaları | Açıklama |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Cihaz kimlik doğrulaması için kullanılır. |
| TCP |49443 (TCP) |Sertifika kimlik doğrulaması için kullanılır. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Table 6a & 6B-çoklu oturum açma (SSO) ve parola karması eşitlemesi ile çoklu oturum açma (SSO) ile doğrudan geçiş kimlik doğrulaması
Aşağıdaki tablolarda, Azure AD Connect ile Azure AD arasında iletişim için gereken bağlantı noktaları ve protokoller açıklanmaktadır.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Table 6a-SSO ile doğrudan kimlik doğrulama
|Protocol|Bağlantı Noktası Numarası|Açıklama
| --- | --- | ---
|HTTP|80|SSL gibi güvenlik doğrulaması için giden HTTP trafiğini etkinleştirin. Ayrıca bağlayıcı Otomatik Güncelleştirme özelliğinin düzgün çalışması için de gereklidir.
|HTTPS|443| Özelliği etkinleştirme ve devre dışı bırakma, bağlayıcıları kaydetme, bağlayıcı güncelleştirmelerini indirme ve tüm Kullanıcı oturum açma isteklerini işleme gibi işlemler için giden HTTPS trafiğini etkinleştirin.

Bunlara ek olarak, Azure AD Connect [Azure veri MERKEZI IP aralıklarına](https://www.microsoft.com/download/details.aspx?id=41653)doğrudan IP bağlantısı yapabilmeleri gerekir.

### <a name="table-6b---password-hash-sync-with-sso"></a>Tablo 6b-SSO ile parola karması eşitleme

|Protocol|Bağlantı Noktası Numarası|Açıklama
| --- | --- | ---
|HTTPS|443| SSO kaydını etkinleştirin (yalnızca SSO kayıt işlemi için gereklidir).

Bunlara ek olarak, Azure AD Connect [Azure veri MERKEZI IP aralıklarına](https://www.microsoft.com/download/details.aspx?id=41653)doğrudan IP bağlantısı yapabilmeleri gerekir. Bu, yalnızca SSO kayıt işlemi için gereklidir.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tablo 7A & 7B-(AD FS/eşitleme) ve Azure AD için Azure AD Connect Health Aracısı
Aşağıdaki tablolarda Azure AD Connect Health aracıları ile Azure AD arasındaki iletişim için gereken uç noktalar, bağlantı noktaları ve protokoller açıklanır.

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tablo 7A-(AD FS/Sync) ve Azure AD için Azure AD Connect Health aracısına yönelik bağlantı noktaları ve protokoller
Bu tabloda, Azure AD Connect Health aracıları ile Azure AD arasında iletişim için gereken aşağıdaki giden bağlantı noktaları ve protokoller açıklanmaktadır.  

| Protocol | Bağlantı Noktaları | Açıklama |
| --- | --- | --- |
| HTTPS |443 (TCP) |Giden |
| Azure Service Bus |5671 (TCP) |Giden |

Azure Service Bus bağlantı noktası 5671, aracının en son sürümü için artık gerekli değildir. En son Azure AD Connect Health Aracısı sürümü yalnızca 443 numaralı bağlantı noktasını gerektirir.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7B-Azure AD Connect Health Aracısı için uç noktalar (AD FS/Sync) ve Azure AD
Uç noktaların listesi için [Azure AD Connect Health aracı Için gereksinimler bölümüne](how-to-connect-health-agent-install.md#requirements)bakın.

