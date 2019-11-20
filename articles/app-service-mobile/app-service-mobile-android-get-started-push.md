---
title: Mobile Apps | Android uygulamanıza anında iletme bildirimleri ekleyin | Microsoft Docs
description: Android uygulamanıza anında iletme bildirimleri göndermek için Mobile Apps kullanmayı öğrenin.
services: app-service\mobile
documentationcenter: android
manager: crdun
editor: ''
author: elamalani
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 539859ba73c8a26d6f7e8f25b9e7453d987a52bd
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389020"
---
# <a name="add-push-notifications-to-your-android-app"></a>Android uygulamanıza anında iletme bildirimleri ekleme

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış

Bu öğreticide, bir kayıt her eklendiğinde cihaza anında iletme bildirimi gönderilmesi için [Android hızlı başlangıç] projesine anında iletme bildirimleri eklersiniz.

İndirilen hızlı başlangıç sunucusu projesini kullanmıyorsanız, anında iletme bildirimi uzantı paketine ihtiyacınız vardır. Daha fazla bilgi için bkz. [Azure için .net arka uç sunucu SDK 'sı Mobile Apps çalışma](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Önkoşullar

Şunlar gerekir:

* Projenizin arka ucuna bağlı olarak bir IDE:

  * Bu uygulamanın bir Node. js arka ucu varsa [Android Studio](https://developer.android.com/sdk/index.html) .
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) veya sonraki bir sürümü, bu uygulamanın bir Microsoft .net arka ucu varsa.
* Android 2,3 veya üzeri, Google Repository düzeltmesi 27 veya üzeri ve Firebase Cloud Messaging için Google Play Hizmetleri 9.0.2 veya üzeri.
* [Android hızlı başlangıç]doldurun.

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Firebase Cloud Messaging'i destekleyen bir proje oluşturma

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Bildirim hub’ını yapılandırma

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Azure 'ı anında iletme bildirimleri gönderecek şekilde yapılandırma

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Sunucu projesi için anında iletme bildirimlerini etkinleştir

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Uygulamanıza anında iletme bildirimleri ekleyin

Bu bölümde, anında iletme bildirimlerini işleyecek istemci Android uygulamanızı güncelleştirmeniz gerekir.

### <a name="verify-android-sdk-version"></a>Android SDK sürümünü doğrula

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Sonraki adımınız Google Play hizmetleri yüklemektir. Firebase Cloud Messaging, geliştirme ve test için en düşük API düzeyi gereksinimlerine sahiptir ve bu, bildirimdeki **Minsdkversion** özelliği ile uyumlu olmalıdır.

Daha eski bir cihazla test ediyorsanız, bu değeri nasıl ayarlayameyeceğinizi ve uygun şekilde ayarlamayı öğrenmek için [Android projenize Firebase ekleyin] ' e başvurun.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Projeye Firebase bulut Iletileri ekleyin

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Kod Ekle

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Uygulamayı yayımlanan mobil hizmete karşı test etme

Uygulamayı, USB kablosuyla doğrudan bir Android telefonu ekleyerek veya öykünücüsünde bir sanal cihaz kullanarak test edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticiyi tamamladığınıza göre, aşağıdaki öğreticilerden birine devam etmeyi göz önünde bulundurun:

* [Android uygulamanıza kimlik doğrulaması ekleyin](app-service-mobile-android-get-started-users.md).
  Desteklenen bir kimlik sağlayıcısı kullanarak Android 'de ToDoList hızlı başlangıç projesine nasıl kimlik doğrulaması ekleneceğini öğrenin.
* [Android uygulamanız için çevrimdışı eşitlemeyi etkinleştirin](app-service-mobile-android-get-started-offline-data.md).
  Mobile Apps arka ucu kullanarak uygulamanıza çevrimdışı destek eklemeyi öğrenin. Çevrimdışı eşitleme sayesinde, kullanıcılar bir mobil uygulama olan @ no__t-0görüntüleme, ekleme veya değiştirme, ağ bağlantısı olmadığında bile @ no__t-1verileri ile etkileşime geçebilir.

<!-- URLs -->
[Android hızlı başlangıç]: app-service-mobile-android-get-started.md
[Android projenize Firebase ekleyin]: https://firebase.google.com/docs/android/setup
