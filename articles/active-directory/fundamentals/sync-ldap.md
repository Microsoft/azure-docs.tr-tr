---
title: Azure Active Directory ile LDAP eşitlemesi
description: Azure Active Directory ile LDAP eşitlemesini elde etmeye yönelik mimari rehberlik.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f34e734b315c7c05ce77f5e168a452fc1c1c547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168687"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>Azure Active Directory ile LDAP eşitlemesi

Hafif Dizin Erişim Protokolü (LDAP), TCP/IP yığınında çalışan bir dizin hizmeti protokolüdür. İnternet dizinlerinde bağlantı kurmak, bunları aramak ve değiştirmek için kullanılan bir mekanizma sağlar. LDAP Dizin hizmeti bir istemci-sunucu modeline dayalıdır ve işlevi, var olan bir dizine erişim sağlamak için kullanılır. Birçok şirket, kullanıcıların ve grupların kritik iş uygulamalarına yönelik olarak depolanması için şirket içi LDAP sunucularına bağımlıdır. 

Azure Active Directory (Azure AD), LDAP eşitlemesini Azure AD Connect ile değiştirebilir. Azure AD Connect eşitleme hizmeti, şirket içi ortamlarınız ile Azure AD arasında kimlik verilerini eşitlemeye ilişkin tüm işlemleri gerçekleştirir. 

## <a name="use-when"></a>Şu durumlarda kullanın

Şirket içi LDAP v3 dizinleriniz ile Azure AD arasında kimlik verilerini eşitlemeniz gerekir. 

![mimari diyagram](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>Sistem bileşenleri

* **Kullanıcı**: kullanıcıları ve parolaları SıRALAMAK için LDAP v3 dizinini kullanan bir uygulamaya erişir.

* **Web tarayıcısı**: kullanıcının uygulamanın dış URL 'sine erişmesi için etkileşimde bulunduğu bileşen

* **Web uygulaması**: LDAP v3 dizinlerinde bağımlılıklara sahip uygulama.

* **Azure AD**: Azure ad, Azure AD Connect aracılığıyla kuruluşun ŞIRKET içi LDAP dizinlerindeki kimlik bilgilerini (kullanıcılar, gruplar, parolalar) eşitler. 

* **Azure AD Connect**: şirket kimliği altyapılarına Microsoft Azure AD bağlanmak için bir araçtır. Sihirbaz ve kılavuzlu deneyimler, bağlantı için gereken önkoşulları ve bileşenleri dağıtmaya ve yapılandırmaya yardımcı olur. 

* **Özel bağlayıcı**: genel bir LDAP bağlayıcısı, Azure AD Connect eşitleme HIZMETINI bir LDAP v3 sunucusuyla tümleştirmenize olanak sağlar. Azure AD Connect üzerinde bulunur.

* **Active Directory**: Active Directory, çoğu Windows Server işletim sisteminde yer alan bir dizin hizmetidir. Active Directory Dizin Hizmetleri çalıştıran sunuculara etki alanı denetleyicileri denir ve Windows etki alanındaki tüm Kullanıcı ve bilgisayarların kimliklerini doğrular ve yetkilendirirler.

* **LDAP v3 sunucusu**: Dizin Hizmetleri kimlik doğrulaması için kullanılan, şirket kullanıcılarını ve parolaları depolayan LDAP protokolü ile uyumlu dizin.

## <a name="implement-ldap-synchronization-with-azure-ad"></a>Azure AD ile LDAP eşitlemesini uygulama

* [Karma kimlik dizini tümleştirme araçları](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Azure AD Connect yükleme yol haritası](../hybrid/how-to-connect-install-roadmap.md) 

* [Genel bakış ve LDAP Bağlayıcısı oluşturma](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > LDAP bağlayıcıları, Forefront Identity Manager ve/veya Microsoft Identity Manager için bazı benzerlik gerektiren gelişmiş bir yapılandırmadır. Üretimde kullanılıyorsa, bu yapılandırmayla ilgili soruların [Premier destek](https://support.microsoft.com/premier) veya Microsoft iş ortağı ağı ilerlemelidir.

