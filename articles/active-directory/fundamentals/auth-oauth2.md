---
title: Azure Active Directory ile OAUTH 2,0 kimlik doğrulaması
description: Azure Active Directory ile OAUTH 2,0 kimlik doğrulamasını elde etmeye yönelik mimari rehberlik.
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
ms.openlocfilehash: c1604d79ce5eb9949028cd677b340bf3d4b09f6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172848"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>Azure Active Directory ile OAuth 2,0 kimlik doğrulaması

OAuth 2,0, yetkilendirme için endüstri protokolüdür. Kullanıcının korunan kaynaklarına sınırlı erişim vermesini sağlar. Özellikle Köprü Metni Aktarım Protokolü (HTTP) ile çalışacak şekilde tasarlanan OAuth, istemcinin rolünü kaynak sahibinden ayırır. İstemci, kaynak sahibi tarafından denetlenen ve kaynak sunucu tarafından barındırılan kaynaklara erişim ister. Kaynak sunucu, kaynak sahibine onay vererek erişim belirteçleri verir. İstemci, kaynak sunucusu tarafından barındırılan korunan kaynaklara erişmek için erişim belirteçlerini kullanır. 

OAuth 2,0, OpenID Connect (OıDC) ile doğrudan ilgilidir. OıDC, OAuth 2,0 üzerinde oluşturulmuş bir kimlik doğrulaması ve yetkilendirme katmanı olduğundan, OAuth 1,0 ile geriye dönük olarak uyumlu değildir. Azure Active Directory (Azure AD) tüm OAuth 2,0 akışlarını destekler. 

## <a name="use-when"></a>Şu durumlarda kullanın:

Zengin istemci & modern uygulama senaryoları ve yeniden Web API erişimi için.

![Mimari diyagramı](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>Sistem bileşenleri

* **Kullanıcı**: Web uygulamasından (uygulama) bir hizmet ister. Kullanıcı genellikle verilerin sahibi olan kaynak sahibidir ve istemcilerin verilere veya kaynağa erişmesine izin veren bir güce sahiptir. 

* **Web tarayıcısı**: kullanıcının etkileşime girdiği Web tarayıcısı OAuth istemcisiyle aynıdır. 

* **Web uygulaması**: Web uygulaması veya kaynak sunucu, kaynak veya verilerin bulunduğu yerdir. OAuth istemcisini güvenli bir şekilde doğrulamak ve yetkilendirmek için yetkilendirme sunucusuna güvenir. 

* **Azure AD**: Azure AD, kimlik sağlayıcısı (IDP) olarak da bilinen yetkilendirme sunucusudur. Kullanıcının bilgileri, erişimleri ve güven ilişkisinin yaptığı her şeyi güvenli bir şekilde işler. Kaynaklara erişim izni veren veya erişimi iptal eden belirteçleri yayınmaktan sorumludur.

## <a name="implement-oauth-20-with-azure-ad"></a>Azure AD ile OAuth 2,0 uygulama

* [Uygulamaları Azure AD ile tümleştirme](../saas-apps/tutorial-list.md) 

* [Microsoft Identity platformunda OAuth 2,0 ve OpenID Connect protokolleri](../develop/active-directory-v2-protocols.md) 

* [Uygulama türleri ve OAuth2](../develop/v2-app-types.md) 

