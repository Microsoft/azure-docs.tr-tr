---
title: JavaScript tek sayfalı uygulama senaryosuna genel bakış-Microsoft Identity platform
description: Microsoft Identity platformunu tümleştiren tek sayfalı uygulama (senaryoya genel bakış) oluşturmayı öğrenin.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c4e671473ff6c6fecdc13fe61dbde1d3fb3809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852509"
---
# <a name="scenario-single-page-application"></a>Senaryo: Tek sayfalı uygulama

Tek sayfalı uygulama (SPA) oluşturmak için ihtiyacınız olan tüm uygulamaları öğrenin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Başlarken

JavaScript SPA hızlı başlangıcı ' nı izleyerek ilk uygulamanızı oluşturabilirsiniz:

> [!div class="nextstepaction"]
> [Hızlı Başlangıç: Tek sayfalı uygulama](./quickstart-v2-javascript.md)

## <a name="overview"></a>Genel Bakış

Birçok modern web uygulaması, JavaScript veya angular, Vue. js ve tepki verme. js gibi bir SPA çerçevesi kullanılarak yazılmış istemci tarafı tek sayfalı uygulamalar olarak oluşturulmuştur. Bu uygulamalar bir Web tarayıcısında çalışır ve geleneksel sunucu tarafı Web uygulamalarından farklı kimlik doğrulama özelliklerine sahiptir. Microsoft Identity platformu, tek sayfalı uygulamaların kullanıcılara oturum açmasını ve [OAuth 2,0 örtük akışını](./v2-oauth2-implicit-grant-flow.md)kullanarak arka uç hizmetlerine veya Web API 'lerine erişim belirteçleri almasını sağlar. Örtük akış, uygulamanın kimliği doğrulanmış kullanıcıyı temsil etmesi için kimlik belirteçleri almasına izin verir ve ayrıca korumalı API 'Leri çağırmak için gereken belirteçleri de erişin.

![Tek sayfalı uygulamalar](./media/scenarios/spa-app.svg)

Bu kimlik doğrulama akışı, elektron, yanıt verme, yerel vb. platformlar arası JavaScript çerçeveleri kullanılarak uygulama senaryoları içermez. Yerel platformlarla etkileşim için daha fazla yetenek gerektirdiğinden.

## <a name="specifics"></a>Özelliklerini

Uygulamanız için bu senaryoyu etkinleştirmek üzere aşağıdaki yönleri gereklidir:

* Azure AD ile uygulama kaydı, örtük akışı etkinleştirmeyi ve belirteçlerin döndürüldüğü yeniden yönlendirme URI 'sini ayarlamayı içerir.
* Uygulama KIMLIĞI gibi kayıtlı uygulama özellikleriyle uygulama yapılandırması.
* Kimlik doğrulama akışını yapmak için MSAL kitaplığını kullanarak oturum açın ve belirteçleri alın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulama kaydı](scenario-spa-app-registration.md)
