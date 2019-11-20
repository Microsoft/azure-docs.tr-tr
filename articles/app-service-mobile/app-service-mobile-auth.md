---
title: Mobil uygulamalar için Azure App Service kimlik doğrulaması ve yetkilendirme | Microsoft Docs
description: Özellikle mobil uygulamalar için Azure App Service kimlik doğrulama/yetkilendirme özelliğine kavramsal başvuru ve genel bakış
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 6017d9bc8b9a791e46fd1562660c9049ca0f72e5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388903"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Mobil uygulamalar için Azure App Service kimlik doğrulaması ve yetkilendirme

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

Bu makalede, App Service arka ucu ile yerel mobil uygulamalar geliştirirken kimlik doğrulamanın ve yetkilendirmenin nasıl çalıştığı açıklanır. App Service, tümleşik kimlik doğrulaması ve yetkilendirme sağlar. böylece mobil uygulamalarınız, App Service hiçbir kodu değiştirmeden kullanıcıları oturum açabilirler. Uygulamanızı korumanın ve Kullanıcı başına verilerle çalışacak kolay bir yol sağlar. 

Bu makale, mobil uygulama geliştirmeye odaklanmaktadır. Mobil uygulamanız için App Service kimlik doğrulaması ve yetkilendirmeyle hızlıca çalışmaya başlamak için, aşağıdaki öğreticilerden birine [kimlik doğrulaması ekleyin iOS uygulamanıza][iOS] (veya [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]veya [Cordova]). 

Kimlik doğrulama ve yetkilendirme App Service ' de nasıl çalıştığı hakkında bilgi için, bkz. [Azure App Service kimlik doğrulama ve yetkilendirme](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Sağlayıcı SDK ile kimlik doğrulaması

Her şey App Service yapılandırıldıktan sonra, mobil istemcileri App Service oturum açmak için değiştirebilirsiniz. Burada iki yaklaşım vardır:

* Verilen bir kimlik sağlayıcısının kimlik oluşturmak için yayımladığı bir SDK kullanın ve ardından App Service erişim elde edin.
* Mobile Apps istemci SDK 'sının kullanıcıların oturum açmasını sağlamak için tek satırlık bir kod kullanın.

> [!TIP]
> Çoğu uygulama, kullanıcılar oturum açtığında, belirteç yenileme desteğini kullanmak ve sağlayıcının belirttiği diğer avantajları sağlamak için bir sağlayıcı SDK 'Sı kullanmalıdır.
> 
> 

Bir sağlayıcı SDK kullandığınızda, kullanıcılar uygulamanın üzerinde çalıştığı işletim sistemiyle daha sıkı bir şekilde tümleşmiş bir deneyimde oturum açabilirler. Bu yöntem ayrıca, istemci üzerinde, grafik API 'Lerini kullanmayı ve Kullanıcı deneyimini özelleştirmeyi çok daha kolay hale getiren bir sağlayıcı belirteci ve bazı Kullanıcı bilgileri de sağlar. Bazen blogların ve forumlarda, istemcideki kod kullanıcıların oturumunu açtığı ve istemci kodunun bir sağlayıcı belirtecine erişimi olduğu için "istemci akışı" veya "istemci ile yönlendirilen akış" olarak anılacaktır.

Bir sağlayıcı belirteci alındıktan sonra, doğrulama için App Service gönderilmesi gerekir. App Service belirteci doğruladıktan sonra, App Service istemciye döndürülen yeni bir App Service belirteci oluşturur. Mobile Apps istemci SDK 'Sı, bu alışverişi yönetmek için yardımcı yöntemlere sahiptir ve belirteci otomatik olarak tüm isteklere uygulama arka ucuna ekler. Geliştiriciler ayrıca sağlayıcı belirtecine bir başvuru tutabilir.

Kimlik doğrulama akışı hakkında daha fazla bilgi için bkz. [App Service kimlik doğrulaması akışı](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Sağlayıcı SDK 'Sı olmadan kimlik doğrulama

Bir sağlayıcı SDK 'yı ayarlamak istemiyorsanız, Azure App Service Mobile Apps özelliği sizin için oturum açmaya izin verebilir. Mobile Apps istemci SDK 'Sı, kullanıcıya seçtiğiniz ve oturum açan sağlayıcının bir web görünümünü açar. Bazen blogların ve forumlarda, sunucu kullanıcıları imzalayan işlemi yönettiğinden ve istemci SDK 'Sı sağlayıcı belirtecini hiçbir zaman almadığından, "sunucu akışı" veya "sunucu ile yönlendirilen akış" olarak adlandırılır.

Bu akışı başlatmak için kod, her platformun kimlik doğrulama öğreticisine dahildir. Akışın sonunda, istemci SDK bir App Service belirtecine sahiptir ve belirteç, uygulama arka ucuna tüm isteklere otomatik olarak eklenir.

Kimlik doğrulama akışı hakkında daha fazla bilgi için bkz. [App Service kimlik doğrulaması akışı](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Daha fazla kaynak

Aşağıdaki öğreticiler, [sunucu ile yönlendirilen akışı](../app-service/overview-authentication-authorization.md#authentication-flow)kullanarak mobil istemcilerinize kimlik doğrulamanın nasıl ekleneceğini göstermektedir:

* [İOS uygulamanıza kimlik doğrulaması ekleme][iOS]
* [Android uygulamanıza kimlik doğrulaması ekleme][Android]
* [Windows uygulamanıza kimlik doğrulaması ekleme][Windows]
* [Xamarin. iOS uygulamanıza kimlik doğrulaması ekleme][Xamarin.iOS]
* [Xamarin. Android uygulamanıza kimlik doğrulaması ekleme][Xamarin.Android]
* [Xamarin. Forms uygulamanıza kimlik doğrulaması ekleme][Xamarin.Forms]
* [Cordova uygulamanıza kimlik doğrulaması ekleme][Cordova]

Azure Active Directory için [istemci yönlendirmeli akışı](../app-service/overview-authentication-authorization.md#authentication-flow) kullanmak istiyorsanız aşağıdaki kaynakları kullanın:

* [İOS için Active Directory Authentication Library kullanma][ADAL-iOS]
* [Android için Active Directory Authentication Library kullanma][ADAL-Android]
* [Windows ve Xamarin için Active Directory Authentication Library kullanma][ADAL-dotnet]

Facebook için [istemci yönlendirmeli akışı](../app-service/overview-authentication-authorization.md#authentication-flow) kullanmak istiyorsanız aşağıdaki kaynakları kullanın:

* [İOS için Facebook SDK 'sını kullanma](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Twitter için [istemci yönlendirmeli akışı](../app-service/overview-authentication-authorization.md#authentication-flow) kullanmak istiyorsanız aşağıdaki kaynakları kullanın:

* [İOS için Twitter dokusunu kullanma](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Google için [istemci yönlendirmeli akışı](../app-service/overview-authentication-authorization.md#authentication-flow) kullanmak istiyorsanız aşağıdaki kaynakları kullanın:

* [İOS için Google oturum açma SDK 'sını kullanma](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
