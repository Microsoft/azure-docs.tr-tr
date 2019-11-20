---
title: Azure Active Directory kimlik doğrulama protokolleri | Microsoft Docs
description: Azure Active Directory tarafından desteklenen kimlik doğrulama protokollerine genel bakış (AD)
documentationcenter: dev-center-name
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: f292d4804adf7e1a58e5c2097f689aac182ff783
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835487"
---
# <a name="azure-active-directory-authentication-protocols"></a>Azure Active Directory kimlik doğrulama protokolleri
Azure Active Directory (Azure AD) en yaygın olarak kullanılan kimlik doğrulama ve yetkilendirme protokollerini destekler. Bu bölümdeki konularda desteklenen protokoller ve bunların Azure AD 'de uygulamaları açıklanır. Konular, desteklenen talep türlerini gözden geçirme, Federasyon meta verilerinin kullanımına giriş, ayrıntılı OAuth 2,0. ve SAML 2,0 protokol başvurusu belgeleri ve sorun giderme bölümü.

## <a name="authentication-protocols-articles-and-reference"></a>Kimlik doğrulama protokolleri makaleleri ve başvurusu
* [Azure AD 'de anahtar geçişi Imzalama hakkında önemli bilgiler](active-directory-signing-key-rollover.md) – Azure AD 'nin imzalama anahtar geçişi temposunda hakkında bilgi edinin, anahtarı otomatik olarak güncelleştirmek için yapabileceğiniz değişiklikler ve en sık kullanılan uygulama senaryolarını güncelleştirme tartışması.
* [Desteklenen belirteç ve talep türleri](v1-id-and-access-tokens.md) -Azure AD sorunlarının bulunduğu belirteçlerdeki talepler hakkında bilgi edinin.
* [Federasyon meta verileri](azure-ad-federation-metadata.md) -Azure AD 'nin oluşturduğu meta veri belgelerini bulmayı ve yorumlanmasını öğrenin.
* [Azure AD 'de oauth 2,0](v1-protocols-oauth-code.md) -Azure AD 'de OAuth 2,0 uygulamasını öğrenin.
* [OpenID Connect 1,0](v1-protocols-openid-connect-code.md) -kimlik doğrulaması için OAuth 2,0, yetkilendirme protokolünü nasıl kullanacağınızı öğrenin.
* [Istemci kimlik bilgileriyle](v1-oauth2-client-creds-grant-flow.md) hizmete hizmet çağrıları-Service to hizmet çağrıları için OAuth 2,0 istemci kimlik bilgileri verme akışını nasıl kullanacağınızı öğrenin.
* Service [to-on Flow ile](v1-oauth2-on-behalf-of-flow.md) hizmetten hizmete çağrılar-hizmet için hizmet çağrıları için OAuth 2,0 ' in nasıl kullanılacağını öğrenin.
* [Saml protokol başvurusu](active-directory-saml-protocol-reference.md) -Azure AD 'Nin çoklu oturum açma ve çoklu oturum açma SAML profilleri hakkında bilgi edinin.

## <a name="see-also"></a>Ayrıca Bkz.
[Geliştirici Kılavuzu Azure Active Directory](v1-overview.md)

[Active Directory kodu örnekleri](sample-v1-code.md)
