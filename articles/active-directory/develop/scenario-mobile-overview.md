---
title: Web API 'Lerini çağıran mobil uygulama-genel bakış
titleSuffix: Microsoft identity platform
description: Web API 'Leri çağıran bir mobil uygulama oluşturmayı öğrenin (genel bakış)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98f05470a07bad82a1e51517a787fb98c78f92ce
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803751"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Senaryo: Web API 'Lerini çağıran mobil uygulama

Web API 'Lerini çağıran bir mobil uygulama oluşturmak için bilmeniz gereken her şey hakkında bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Başlangıç

İlk mobil uygulamanızı oluşturun ve hızlı başlangıç yapın!

> [!div class="nextstepaction"]
> [Hızlı başlangıç: bir Android uygulamasından belirteç alma ve Microsoft Graph API çağırma](./quickstart-v2-android.md)
>
> [Hızlı başlangıç: bir iOS uygulamasından belirteç alma ve Microsoft Graph API çağırma](./quickstart-v2-ios.md)
>
> [Hızlı başlangıç: bir Xamarin iOS & Android uygulamasından belirteç alma ve Microsoft Graph API çağırma](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Genel Bakış

Kişiselleştirilmiş, sorunsuz bir kullanıcı deneyimi, mobil uygulamalar için gereklidir.  Microsoft Identity platform, mobil geliştiricilerin iOS ve Android kullanıcıları için bu deneyimi oluşturmalarına olanak sağlar. Uygulamanız Azure Active Directory (Azure AD) kullanıcıları, kişisel Microsoft hesabı kullanıcıları ve Azure AD B2C kullanıcıları açabilir ve kendi adına bir Web API 'SI çağırmak için belirteçler alabilir. Bu akışları uygulamak için, sektör standardı [OAuth 2.0 yetkilendirme kodu akışını](v2-oauth2-auth-code-flow.md)uygulayan Microsoft kimlik doğrulama kitaplığı 'Nı (msal) kullanacağız.

![Daemon uygulamaları](./media/scenarios/mobile-app.svg)

Mobil uygulamalar için dikkat edilmesi gerekenler:

- **Kullanıcı deneyimi anahtardır**: kullanıcıların oturum açmayı istemeden önce uygulamanızın değerini görmesine izin verin ve yalnızca gerekli izinleri isteyin.
- **Tüm Kullanıcı yapılandırmalarının desteklenmesi**: birçok mobil Iş kullanıcısı koşullu erişim ve cihaz uyumluluk ilkeleri altındadır. Bu anahtar senaryolarını desteklediğinizden emin olun.
- **Çoklu oturum açma (SSO) uygulama**: msal ve Microsoft Identity platform, cihazın tarayıcısı veya Microsoft Authenticator (ve Android üzerinde Intune şirket portalı) aracılığıyla basit çoklu oturum açmayı etkinleştirmeyi kolaylaştırır.

## <a name="specifics"></a>Özelliklerini

Microsoft Identity platformunda bir mobil uygulama oluşturduğunuzda bu hususları göz önünde bulundurun:

- Platforma bağlı olarak, kullanıcılar ilk kez oturum açtığında bazı Kullanıcı etkileşimi gerekebilir. Örneğin iOS, Microsoft Authenticator (ve Android üzerinde Intune Şirket Portalı) aracılığıyla SSO kullanırken, uygulamaların kullanıcı etkileşimini göstermesini gerektirir.
- İOS ve Android 'de MSAL, kullanıcıların oturum açması için bir dış tarayıcı (uygulamanızın üst kısmında görünebilir) kullanabilir. Bunun yerine uygulama içi Web görünümlerini kullanmak için yapılandırmayı özelleştirebilirsiniz.
- Bir mobil uygulamada hiç gizli anahtar kullanmayın. Bu, tüm kullanıcılar tarafından erişilebilecektir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulama kaydı](scenario-mobile-app-registration.md)
