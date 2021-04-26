---
title: Azure Active Directory ile SAML kimlik doğrulaması
description: Azure Active Directory ile SAML kimlik doğrulaması elde etmeye yönelik mimari kılavuz
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
ms.openlocfilehash: 7cd2aa5e9ff8cbaeead69f11d2e3de7f760b53ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168653"
---
# <a name="saml-authentication-with-azure-active-directory"></a>Azure Active Directory ile SAML kimlik doğrulaması

Security Assertion Markup Language (SAML) kimlik sağlayıcısı ve hizmet sağlayıcısı arasında kimlik doğrulaması ve yetkilendirme verilerini değiş tokuş etmek için açık bir standarttır. SAML, hizmet sağlayıcılarının erişim denetimi kararları vermek için kullandığı deyimler olan güvenlik onayları için XML tabanlı bir biçimlendirme dilidir. 

SAML belirtimi üç rol tanımlar:

* Sorumlu, genellikle bir Kullanıcı
* Kimlik sağlayıcısı (IDP)
* Hizmet sağlayıcı (SP)


## <a name="use-when"></a>Şu durumlarda kullanın

Kurumsal SAML uygulaması için çoklu oturum açma (SSO) deneyimi sağlamanız gerekir.

SAML adreslerinin en önemli kullanım çalışmalarından biri, özellikle de güvenlik etki alanları genelinde SSO 'yu genişletirken başka kullanım durumları (profiller olarak adlandırılır) de vardır. 

![SAML için mimari diyagramı](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>Sistem bileşenleri

* **Kullanıcı**: uygulamadan bir hizmet ister.

* **Web tarayıcısı**: kullanıcının etkileşime girdiği bileşen.

* **Web uygulaması**: SAML 'yi destekleyen ve Azure AD 'Yi IDP olarak kullanan kurumsal uygulama.

* **Belirteç**: ilke (Kullanıcı) hakkında IDP tarafından yapılan talep kümelerini taşıyan bir SAML onaylama (SAML belirteçleri olarak da bilinir). Kimlik doğrulama bilgilerini, öznitelikleri ve yetkilendirme kararı deyimlerini içerir.

* **Azure AD**: SAML UYGULAMALARı için SSO ve Multi-Factor Authentication sağlayan kurumsal bulut IDP. Bağlı uygulamalara kimlik doğrulama hizmetleri sağlarken kullanıcılar için kimlik bilgilerini eşitler, korur ve yönetir. 

## <a name="implement-saml-authentication-with-azure-ad"></a>Azure AD ile SAML kimlik doğrulaması uygulama

* [Azure Active Directory kullanarak SaaS uygulamalarını tümleştirmek için öğreticiler](../saas-apps/tutorial-list.md) 

* [Galeri olmayan uygulamalar için SAML tabanlı çoklu oturum açmayı yapılandırma](../manage-apps/add-application-portal.md) 

* [Azure AD, SAML protokolünü nasıl kullanır?](../develop/active-directory-saml-protocol-reference.md)