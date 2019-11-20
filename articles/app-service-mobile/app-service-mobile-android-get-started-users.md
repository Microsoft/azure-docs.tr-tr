---
title: Android 'de Mobile Apps kimlik doğrulaması ekleme | Microsoft Docs
description: Google, Facebook, Twitter ve Microsoft gibi çeşitli kimlik sağlayıcıları aracılığıyla Android uygulamanızın kullanıcılarının kimliğini doğrulamak için Azure App Service Mobile Apps özelliğini nasıl kullanacağınızı öğrenin.
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
editor: ''
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: b520f6129e7d7fa1a4800143d5987d3eba625e98
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388943"
---
# <a name="add-authentication-to-your-android-app"></a>Android uygulamanıza kimlik doğrulaması ekleme
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="summary"></a>Özet
Bu öğreticide, desteklenen bir kimlik sağlayıcısı kullanarak Android 'de ToDoList hızlı başlangıç projesine kimlik doğrulaması eklersiniz. Bu öğretici, ilk olarak gerçekleştirmeniz gereken Mobile Apps öğreticiye [Mobile Apps kullanmaya başlayın] ' i temel alır.

## <a name="register"></a>Uygulamanızı kimlik doğrulaması için kaydetme ve Azure App Service yapılandırma
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Uygulamanızı Izin verilen dış yönlendirme URL 'Lerine ekleyin

Güvenli kimlik doğrulaması, uygulamanız için yeni bir URL şeması tanımlamanızı gerektirir. Bu, kimlik doğrulama işlemi tamamlandıktan sonra kimlik doğrulama sisteminin uygulamanıza yeniden yönlendirilmesini sağlar. Bu öğreticide, üzerinde URL şeması _uygulamamız_ kullanırız. Ancak, seçtiğiniz herhangi bir URL şemasını kullanabilirsiniz. Bu, mobil uygulamanız için benzersiz olmalıdır. Sunucu tarafında yeniden yönlendirmeyi etkinleştirmek için:

1. [Azure portalda]App Service seçin.

2. **Kimlik doğrulama/yetkilendirme** menü seçeneğine tıklayın.

3. **Izin verilen dış yeniden yönlendirme URL 'lerinde**`appname://easyauth.callback` girin.  Bu dizedeki _appname_ , MOBIL uygulamanızın URL şemadır.  Bir protokol için normal URL belirtimini izlemelidir (yalnızca harfler ve rakamlar kullanın ve bir harfle başlar).  Mobil uygulama kodunuzu birkaç yerde URL düzeniyle ayarlamanız gerekeceğinden, seçtiğiniz dizeyi bir yere iade etmeniz gerekir.

4. **Tamam**’a tıklayın.

5. **Kaydet** düğmesine tıklayın.

## <a name="permissions"></a>Kimliği doğrulanmış kullanıcılar için izinleri kısıtla
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Android Studio ' de, [Mobile Apps kullanmaya başlayın]öğreticisiyle tamamladığınız projeyi açın. **Çalıştır** menüsünde, **uygulamayı çalıştır**' a tıklayın ve durum kodu 401 (yetkisiz) olan işlenmemiş bir özel durumun uygulama başladıktan sonra yapıldığını doğrulayın.

     Bu özel durum, uygulamanın kimliği doğrulanmamış bir kullanıcı olarak arka uca erişmeye çalıştığı, ancak *TodoItem* tablosunun artık kimlik doğrulaması gerektirdiğinden meydana gelir.

Daha sonra, Mobile Apps arka uçta kaynakları isteyerek önce kullanıcıların kimliğini doğrulamak üzere uygulamayı güncelleşolursunuz.

## <a name="add-authentication-to-the-app"></a>Uygulamaya kimlik doğrulaması ekleme
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>İstemcide önbellek kimlik doğrulaması belirteçleri
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Sonraki adımlar
Bu temel kimlik doğrulama öğreticisini tamamladığınıza göre, aşağıdaki öğreticilerden birine devam etmeyi göz önünde bulundurun:

* [Android uygulamanıza anında iletme bildirimleri ekleyin](app-service-mobile-android-get-started-push.md).
  Mobile Apps arka uca, anında iletme bildirimleri göndermek için Azure Notification Hub 'larını kullanmak üzere nasıl yapılandıracağınızı öğrenin.
* [Android uygulamanız için çevrimdışı eşitlemeyi etkinleştirin](app-service-mobile-android-get-started-offline-data.md).
  Mobile Apps arka ucu kullanarak uygulamanıza çevrimdışı destek eklemeyi öğrenin. Çevrimdışı eşitleme sayesinde, kullanıcılar bir mobil uygulama olan @ no__t-0görüntüleme, ekleme veya değiştirme, ağ bağlantısı olmadığında bile @ no__t-1verileri ile etkileşime geçebilir.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Mobile Apps kullanmaya başlayın]: app-service-mobile-android-get-started.md
[Azure portalda]: https://portal.azure.com/
