---
title: Azure Active Directory ile OpenID Connect kimlik doğrulaması
description: Azure Active Directory ile OpenID Connect kimlik doğrulamasını elde etmeye yönelik mimari rehberlik.
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
ms.openlocfilehash: 0e5bf7e51de38d42e64f6737e687c5946a464160
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168670"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>Azure Active Directory ile OpenID Connect kimlik doğrulaması

OpenID Connect (OıDC), OAuth2 protokolüne dayalı bir kimlik doğrulama protokolüdür (yetkilendirme için kullanılır). OıDC, kimlik hizmetleri sağlamak için OAuth2 adresinden standartlaştırılmış ileti akışlarını kullanır. 

OıDC 'in tasarım hedefi "basit şeyleri basit ve karmaşık şeyleri mümkün hale getirme" sağlamaktır. OıDC, geliştiricilerin parola dosyalarına sahip olmak ve bunları yönetmek zorunda kalmadan Web siteleri ve uygulamalar arasında kimliklerini doğrulamalarını sağlar. Bu, uygulama Oluşturucu 'yu, şu anda uygulamaya bağlı olan kişinin kimliğini doğrulamak için güvenli bir yol sağlar.

Kullanıcının kimlik doğrulaması, kullanıcının oturumunun veya kimlik bilgilerinin denetlendiği bir kimlik sağlayıcısında gerçekleşmelidir. Bunu yapmak için güvenilir bir aracıya ihtiyacınız vardır. Yerel uygulamalar genellikle bu amaçla sistem tarayıcısını başlatır. Uygulamanın kullanıcı parolası üzerinde gözetmesini önleyen bir şey olmadığından, gömülü görünümler güvenilir değil olarak kabul edilir. 

Kimlik doğrulamasına ek olarak, kullanıcıdan onay istenebilir. İzin, kullanıcının korumalı kaynaklara erişmesine izin vermek için açık olan izindir. Onay, bir kaynak için yalnızca bir kez sağlanması gerektiğinden, kimlik doğrulamasından farklıdır. Kullanıcı veya yönetici izni el ile iptal edene kadar onay geçerli kalır. 

## <a name="use-when"></a>Şu durumlarda kullanın

Kullanıcı onayı ve Web oturumu açma için bir gereksinim vardır.

![mimari diyagram](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>Sistem bileşenleri

* **Kullanıcı**: uygulamadan bir hizmet ister.

* **Güvenilen aracı**: kullanıcının etkileşime girdiği bileşen. Bu güvenilen aracı genellikle bir web tarayıcısıdır.

* **Uygulama**: uygulama veya kaynak sunucu, kaynak veya verilerin bulunduğu yerdir. Güvenli aracıyı güvenli bir şekilde doğrulamak ve yetkilendirmek için kimlik sağlayıcısına güvenir. 

* **Azure AD**: kimlik sağlayıcısı olarak da bilinen OIDC sağlayıcısı, kullanıcının bilgileri, erişimleri ve bir akıştaki taraflar arasındaki güven ilişkilerini güvenli bir şekilde yönetir. Kullanıcı kimliğinin kimliğini doğrular, kaynaklara erişim izni verir ve erişimi iptal eder ve sorun belirteçleri. 

## <a name="implement-oidc-with-azure-ad"></a>Azure AD ile OıDC uygulama

* [Uygulamaları Azure AD ile tümleştirme](../saas-apps/tutorial-list.md) 

* [Microsoft Identity platformunda OAuth 2,0 ve OpenID Connect protokolleri](../develop/active-directory-v2-protocols.md) 

* [Microsoft Identity platform ve OpenID Connect Protokolü](../develop/v2-protocols-oidc.md) 

* [Azure Active Directory B2C 'de OpenID Connect ile Web oturumu açma](../../active-directory-b2c/openid-connect.md) 

* [OpenID Connect ve Azure AD kullanarak uygulamanızın güvenliğini sağlama](/learn/modules/secure-app-with-oidc-and-azure-ad/) 

