---
title: Web API 'Leri çağıran masaüstü uygulaması (uygulama kaydı)-Microsoft Identity platform
description: Web API 'Leri (uygulama kaydı) çağıran bir masaüstü uygulaması oluşturmayı öğrenin
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 648652ed32a5dea30de665b7fa49190171a7f10a
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268400"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Web API 'Lerini çağıran masaüstü uygulaması-uygulama kaydı

Bu makale, bir masaüstü uygulaması için uygulama kaydı karmaşıklığını içerir.

## <a name="supported-accounts-types"></a>Desteklenen hesap türleri

Masaüstü uygulamasında desteklenen hesap türleri, açmak istediğiniz deneyime bağlı olarak değişir. Bu ilişki nedeniyle, desteklenen hesap türleri kullanmak istediğiniz akışlara bağlıdır.

### <a name="audience-for-interactive-token-acquisition"></a>Etkileşimli belirteç alımı için hedef kitle

Masaüstü uygulamanız etkileşimli kimlik doğrulaması kullanıyorsa, kullanıcıların herhangi bir [Hesap türünden](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)oturum açmasını sağlayabilirsiniz.

### <a name="audience-for-desktop-app-silent-flows"></a>Masaüstü uygulaması için hedef kitle sessiz akışlar

- Tümleşik Windows kimlik doğrulaması veya Kullanıcı adı/parola kullanmak için uygulamanızın kendi kiracınızda (LOB Geliştirici) veya Azure Active Directory kuruluşları 'nda (ISV senaryosu) Kullanıcı oturum açması gerekir. Bu kimlik doğrulama akışları, Microsoft kişisel hesapları için desteklenmez.
- Cihaz kod akışını kullanmak istiyorsanız, kullanıcıları Microsoft kişisel hesaplarıyla henüz oturum açamazsınız.
- Sosyal kimlik ve ilke geçirimli kullanıcılar oturum açarsanız, yalnızca etkileşimli ve Kullanıcı adı-parola kimlik doğrulamasını kullanabilirsiniz.

## <a name="redirect-uris"></a>Yeniden Yönlendirme URI'leri

Masaüstü uygulamasında kullanılacak yeniden yönlendirme URI 'Leri, kullanmak istediğiniz akışa bağlı olacaktır.

- **Etkileşimli kimlik doğrulama** veya **cihaz kod akışı**kullanıyorsanız, kullanmak `https://login.microsoftonline.com/common/oauth2/nativeclient`isteyeceksiniz. Uygulamanız için **kimlik doğrulama** bölümünde karşılık gelen URL 'ye tıklayarak bu yapılandırmayı elde edeceksiniz.
  
  > [!IMPORTANT]
  > Bugün MSAL.NET, Windows (`urn:ietf:wg:oauth:2.0:oob`) üzerinde çalışan masaüstü uygulamalarında varsayılan olarak başka bir yeniden yönlendirme URI 'si kullanır. Gelecekte bu Varsayılanı değiştirmek istiyoruz ve bu nedenle şunu kullanmanızı öneririz`https://login.microsoftonline.com/common/oauth2/nativeclient`

- MacOS için yerel bir amaç-C veya Swift uygulaması oluşturuyorsanız, redirectUri 'yi uygulamanızın paket tanımlayıcısına göre aşağıdaki biçimde kaydetmek isteyeceksiniz: **msauth <. app. demeti. ıd >:/auth** (replace < Uygulamanızın paket tanımlayıcısına sahip. app. demeti. ID >
- Uygulamanız yalnızca tümleşik Windows kimlik doğrulaması veya Kullanıcı adı/parola kullanıyorsa, uygulamanız için bir yeniden yönlendirme URI 'SI kaydetmeniz gerekmez. Bu akışlar Microsoft Identity platform v 2.0 uç noktasına gidiş dönüş yapılır ve uygulamanız belirli bir URI üzerinde geri çağrılmayacaktır.
- Cihaz kod akışını, tümleşik Windows kimlik doğrulamasını ve Kullanıcı adını/parolayı yeniden yönlendirme URI 'Leri olmayan bir gizli istemci uygulama akışından ayırt etmek için (daemon uygulamalarında kullanılan istemci kimlik bilgisi akışı), uygulama, ortak bir istemci uygulamasıdır. Bu yapılandırmayı başarmak için uygulamanızın **kimlik doğrulama** bölümüne gidin. Ardından, **Gelişmiş ayarlar** alt bölümünde, **varsayılan istemci türü** paragrafında, **uygulamayı ortak istemci olarak işle**sorusuna **Evet** ' i seçin.

  ![Ortak istemciye izin ver](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API izinleri

Masaüstü uygulamaları, oturum açmış kullanıcı için API 'Ler çağırır. Temsilci izinleri istemeleri gerekir. Ancak, yalnızca [Daemon uygulamalarında](scenario-daemon-overview.md)işlenen uygulama izinleri isteyemeyebilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Masaüstü uygulaması-uygulama yapılandırması](scenario-desktop-app-configuration.md)
