---
title: API kimlik doğrulamasını anlama-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS kullanarak API 'lerle bağlantı kurmak ve bunları doğrulamak hakkında bilgi edinin.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 6af6a4501ad58fc8e371b895da601d177d872f41
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013950"
---
# <a name="connect-to-and-authenticate-with-apis"></a>API 'lerle bağlantı kurmak ve kimlik doğrulamak

Azure dijital TWINS, kullanıcıların kimliğini doğrulamak ve uygulamaları korumak için Azure Active Directory (Azure AD) kullanır. Azure AD, çeşitli modern mimarilerin kimlik doğrulamasını destekler. Bunların hepsi, OAuth 2,0 veya OpenID Connect sektör standardı protokollerine dayanır. Ayrıca, geliştiriciler tek kiracılı ve iş kolu (LOB) uygulamaları oluşturmak için Azure AD 'yi kullanabilir. Geliştiriciler, çok kiracılı uygulamalar geliştirmek için Azure AD 'yi de kullanabilir.

Azure AD 'ye genel bakış için, adım adım kılavuzlar, kavramlar ve hızlı başlangıçların [temelleri sayfasını](https://docs.microsoft.com/azure/active-directory/fundamentals/) ziyaret edin.

> [!TIP]
> Azure dijital TWINS örnek uygulamasını ayarlamak ve çalıştırmak için [öğreticiyi](tutorial-facilities-setup.md) izleyin.

Bir uygulama veya hizmeti Azure AD ile tümleştirmek için geliştiricilerin önce uygulamayı Azure AD'ye kaydetmesi gerekir. Ayrıntılı yönergeler ve ekran görüntüleri için bkz. [Bu hızlı başlangıç](../active-directory/develop/quickstart-register-app.md).

Azure AD tarafından [beş birincil uygulama senaryosu](../active-directory/develop/v2-app-types.md) desteklenir:

* Tek sayfalı uygulama (SPA): kullanıcının Azure AD tarafından güvenliği sağlanmış tek sayfalı bir uygulamada oturum açması gerekir.
* Web uygulamasına Web tarayıcısı: bir kullanıcının Azure AD tarafından güvenliği sağlanmış bir Web uygulamasında oturum açması gerekir.
* Yerel uygulama Web API 'sine: bir telefonda, tablette veya BILGISAYAR üzerinde çalışan yerel bir uygulamanın, Azure AD tarafından güvenliği sağlanmış bir Web API 'sinden kaynak alması için bir kullanıcının kimliğini doğrulaması gerekir.
* Web uygulaması Web API 'SI: bir Web uygulamasının Azure AD ile güvenliği sağlanmış bir Web API 'sinden kaynak alması gerekir.
* Web API 'sine yönelik Daemon veya sunucu uygulaması: Web Kullanıcı arabirimi olmayan bir arka plan uygulaması veya bir sunucu uygulaması, Azure AD tarafından güvenliği sağlanmış bir Web API 'sinden kaynak almaya ihtiyaç duyuyor.

> [!IMPORTANT]
> Azure dijital TWINS aşağıdaki kimlik doğrulama kitaplıklarının her ikisini de destekler:
> * Daha yeni [Microsoft kimlik doğrulama kitaplığı (msal)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Azure Active Directory kimlik doğrulama kitaplığı (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Orta katman Web API 'sinden dijital TWINS çağırma

Geliştiriciler dijital TWINS çözümlerini mimarilerlerse, genellikle bir orta katman uygulaması veya API 'SI oluşturur. Uygulama veya API daha sonra dijital TWINS API 'sini aşağı akış olarak çağırır. Bu standart Web çözüm mimarisini desteklemek için, kullanıcıların önce şunları yaptığınızdan emin olun:

1. Orta katman uygulamasıyla kimlik doğrulama

1. Kimlik doğrulaması sırasında bir OAuth 2,0 adına belirteç alındı

1. Elde edilen belirteç daha sonra kimlik doğrulaması için veya şirket içi akış kullanarak daha fazla aşağı akış olan API 'Leri çağırmak için kullanılır

Şirket adına akışı düzenleme hakkında yönergeler için bkz. [OAuth 2,0 on-the Flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Ayrıca, [bir aşağı akış Web API 'Sini çağırarak](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)kod örneklerini görüntüleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

OAuth 2,0 örtük verme akışını kullanarak Azure dijital TWINS yapılandırmak ve test etmek için [Postman yapılandırma](./how-to-configure-postman.md)makalesini okuyun.

Azure dijital TWINS güvenliği hakkında daha fazla bilgi edinmek için [rol atamaları oluşturma ve yönetme](./security-create-manage-role-assignments.md)konusunu okuyun.