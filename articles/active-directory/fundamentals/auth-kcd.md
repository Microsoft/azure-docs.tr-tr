---
title: Azure Active Directory ile Kerberos kısıtlanmış temsili
description: Azure Active Directory ile Kerberos kısıtlanmış temsilciyi elde etmeye yönelik mimari rehberlik.
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
ms.openlocfilehash: a1c7aa4d2300a6dee44da067b122fc7af97f7aa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172865"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Windows kimlik doğrulaması-Azure Active Directory ile Kerberos kısıtlı temsili

Kerberos kısıtlanmış temsili (KCD), kaynaklar arasında kısıtlı temsili sağlar ve hizmet asıl adlarını temel alır. Etki alanı yöneticilerinin temsilcileri oluşturmasını ve tek bir etki alanıyla sınırlı olmasını gerektirir. Kaynak tabanlı KCD genellikle, bir Active Directory ormanında birden çok etki alanında kullanıcıları olan bir Web uygulaması için Kerberos kimlik doğrulaması sağlamanın bir yolu olarak kullanılır.

Azure Active Directory Uygulama Ara Sunucusu, erişim ve Kerberos kısıtlı temsilcisi (KCD) için Kerberos bileti gerektiren KCD tabanlı uygulamalara çoklu oturum açma (SSO) ve uzaktan erişim sağlayabilir.

SSO 'yu, Active Directory kullanıcıların kimliğine bürünmek için uygulama ara sunucusu bağlayıcılarına izin vererek tümleşik Windows kimlik doğrulaması (ıWA) kullanan şirket içi KCD uygulamalarınız için etkinleştirir. Uygulama proxy Bağlayıcısı, kullanıcıların adına belirteç göndermek ve almak için bu izni kullanır.

## <a name="use-when"></a>Şu durumlarda kullanın

Uzaktan erişim sağlamak, ön kimlik doğrulamasıyla korumak ve şirket içi ıWA uygulamalarına SSO sağlamak için bir gereksinim vardır.

![Mimari diyagramı](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>Sistem bileşenleri

* **Kullanıcı**: uygulama proxy 'si tarafından sunulan eski uygulamaya erişir.

* **Web tarayıcısı**: kullanıcının uygulamanın dış URL 'sine erişmek için etkileşimde bulunduğu bileşen.

* **Azure AD**: kullanıcının kimliğini doğrular. 

* **Uygulama proxy hizmeti**: kullanıcıdan şirket içi uygulamaya istek göndermek için ters proxy işlevi görür. Azure AD 'de bulunur. Uygulama proxy 'Si, koşullu erişim ilkelerini de uygulayabilir.

* **Uygulama proxy Bağlayıcısı**: uygulamaya bağlantı sağlamak için Windows Server 'da şirket içinde yüklü. Azure AD 'nin yanıtını döndürür. Active Directory ile KCD anlaşması gerçekleştirerek uygulamaya Kerberos belirteci almak için kullanıcıyı taklit edin.

* **Active Directory**: uygulamanın Kerberos belirtecini uygulama proxy Bağlayıcısı 'na gönderir.

* **Eski uygulamalar**: uygulama proxy 'sinden Kullanıcı istekleri alan uygulamalar. Eski uygulamalar, uygulama proxy Bağlayıcısı 'na yanıtı döndürür.

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>Azure AD ile Windows kimlik doğrulaması (KCD) uygulama

* [Uygulama Ara Sunucusu ile uygulamalarınızda çoklu oturum açmak için Kerberos Kısıtlanmış Temsil](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md) 

* [Azure Active Directory içindeki uygulama proxy 'Si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](../manage-apps/application-proxy-add-on-premises-application.md)

