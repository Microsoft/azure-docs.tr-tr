---
title: Web API 'Leri çağıran masaüstü uygulaması (genel bakış)-Microsoft Identity platform
description: Web API 'Leri çağıran bir masaüstü uygulaması oluşturmayı öğrenin (genel bakış)
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: d56a07b46c4cd54577bcb3d81f2c45c03433fc31
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268343"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Senaryo: Web API'lerini çağıran masaüstü uygulaması

Web API 'Lerini çağıran bir masaüstü uygulaması oluşturmak için ihtiyacınız olan tüm hakkında bilgi edinin

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Başlarken

Henüz yapmadıysanız, .NET Masaüstü hızlı başlangıcı, UWP hızlı başlangıcı veya macOS yerel uygulaması hızlı başlangıcı ' nı izleyerek ilk uygulamanızı oluşturun:

> [!div class="nextstepaction"]
> [Hızlı Başlangıç: Bir Windows masaüstü uygulamasından belirteç alma ve Microsoft Graph API çağrısı](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Hızlı Başlangıç: UWP uygulamasından bir belirteç edinin ve Microsoft Graph API çağrısı yapın](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Hızlı Başlangıç: MacOS yerel uygulamasından belirteç alma ve Microsoft Graph API çağrısı](./quickstart-v2-ios.md)

## <a name="overview"></a>Genel Bakış

Bir masaüstü uygulaması yazar ve kullanıcılara oturum açmak ve Microsoft Graph, diğer Microsoft API 'Leri ya da kendi Web API 'niz gibi Web API 'Lerini çağırmak istiyorsunuz. Çeşitli olanaklar vardır:

- Etkileşimli belirteç alımı kullanabilirsiniz:

  - Masaüstü uygulamanız grafik denetimlerini destekliyorsa (örneğin, bir Windows. form uygulaması, bir WPF uygulaması veya macOS yerel uygulaması).
  - Bu bir .NET Core uygulamasıdır ve Azure AD ile kimlik doğrulama etkileşiminin sistem tarayıcısında gerçekleşme durumunu kabul etmiş olursunuz.

- Windows barındırılan uygulamalar için, Windows etki alanına katılmış bilgisayarlarda çalışan uygulamaların veya AAD 'nin tümleşik Windows kimlik doğrulaması kullanarak sessizce bir belirteç almasına katılmış olması de mümkündür.
- Son olarak, ancak önerilmese de, genel istemci uygulamalarında Kullanıcı adı/parola ' yı kullanabilirsiniz. Bazı senaryolarda (DevOps gibi) hala gereklidir, ancak bunu kullanmanın uygulamanıza kısıtlamalar getirdiğinden emin olun. Örneğin, çok faktörlü kimlik doğrulaması (koşullu erişim) gerçekleştirmesi gereken kullanıcı oturum açamaz. Ayrıca uygulamanız çoklu oturum açma (SSO) özelliğinden yarar olmayacaktır.

  Bu, modern kimlik doğrulaması ilkelerine de karşı geçerlidir ve yalnızca eski nedenlerle sağlanır.

  ![Masaüstü uygulaması](media/scenarios/desktop-app.svg)

- Taşınabilir bir komut satırı aracı yazıyorsanız (büyük olasılıkla Linux veya Mac üzerinde çalışan bir .NET Core uygulaması) ve kimlik doğrulamasının sistem tarayıcısına verilmiş olmasını kabul ediyorsanız etkileşimli kimlik doğrulaması kullanabilirsiniz. (.NET Core henüz bir [Web tarayıcısı](https://aka.ms/msal-net-uses-web-browser) sağlamıyor ve bu nedenle sistem tarayıcısında kimlik doğrulaması gerçekleşmez), aksi takdirde, bu durumda en iyi seçenek cihaz kodu akışını kullanmaktır. Bu akış, IoT uygulamaları gibi tarayıcı olmayan uygulamalar için de kullanılır

  ![Tarayıcısız uygulama](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Özelliklerini

Masaüstü uygulamalarında, genellikle uygulamanızın etkileşimli kimlik doğrulaması kullanıp kullanmadığına bağlı olarak bir dizi karmaşıklık vardır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Masaüstü uygulaması-uygulama kaydı](scenario-desktop-app-registration.md)
