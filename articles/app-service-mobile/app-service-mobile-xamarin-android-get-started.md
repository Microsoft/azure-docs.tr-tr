---
title: Xamarin.Android uygulamaları için Azure Mobile Apps kullanmaya başlama
description: Xamarin Android geliştirme için Azure Mobile Apps’i kullanmaya başlamak için bu öğreticiden yararlanın
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 038ca0bd7690ac90470d9a739a47d0f634f1297b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388467"
---
# <a name="create-a-xamarinandroid-app"></a>Xamarin.Android Uygulaması oluşturma
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış
Bu öğreticide, bir Xamarin.Android uygulamasına bulut tabanlı arka uç hizmetini nasıl ekleyeceğiniz gösterilmiştir. Daha fazla bilgi için bkz. [Mobile Apps nedir?](app-service-mobile-value-prop.md).

Aşağıda tamamlanmış uygulamadan bir ekran görüntüsünü görebilirsiniz:

![][0]

Bu öğreticiyi tamamlamak Xamarin Android uygulamalarına ilişkin tüm Mobile Apps öğreticileri için ön koşuldur.

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticiyi tamamlamak için aşağıdaki önkoşulları karşılamanız gerekir:

* Etkin bir Azure hesabı. Hesabınız yoksa Azure deneme sürümü için kaydolun ve 10 ücretsiz Mobil Uygulama edinin. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/).
* Xamarin ile Visual Studio. Yönergeler için bkz. [Visual Studio ve Xamarin için Kurulum ve Yükleme](/visualstudio/cross-platform/setup-and-install).

## <a name="create-an-azure-mobile-app-backend"></a>Azure Mobil Uygulama arka ucu oluşturma
Mobil Uygulama arka ucu oluşturmak için bu adımları izleyin.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Artık, mobil istemci uygulamalarınız tarafından kullanılabilecek bir Azure Mobil Uygulama arka ucu sağladınız. Ardından, basit bir "Yapılacaklar listesi" arka ucu için sunucu projesi indirin ve Azure’a yayımlayın.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Bir veritabanı bağlantısı oluşturma ve istemciyi ve sunucu projesini yapılandırma
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Xamarin. Android uygulamasını çalıştırma
1. Xamarin. Android projesini açın.

2. [Azure Portal](https://portal.azure.com/) gidin ve oluşturduğunuz mobil uygulamaya gidin. @No__t-0 dikey penceresinde, mobil uygulamanız için genel uç nokta olan URL 'YI arayın. Örnek-uygulamamın adı "test123" olan SiteName değeri https://test123.azurewebsites.net olacaktır.

3. Bu klasördeki `ToDoActivity.cs` dosyasını açın-Xamarin. Android/ZUMOAPPNAME/ToDoActivity. cs. Uygulama adı `ZUMOAPPNAME` ' dır.

4. @No__t-0 sınıfında, `ZUMOAPPURL` değişkenini Yukarıdaki genel uç nokta ile değiştirin.

    `const string applicationURL = @"ZUMOAPPURL";`

    geldiğinde
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Uygulamayı dağıtmak ve çalıştırmak için F5 tuşuna basın.

6. Uygulamada, *Öğreticiyi tamamla* gibi anlamlı bir metin yazın ve ardından **Ekle** düğmesine basın.

    ![][10]

    İstekten alınan veriler TodoItem tablosuna eklenir. Tabloda depolanan öğeler mobil uygulama arka ucu tarafından döndürülür ve veriler listede görüntülenir.

   > [!NOTE]
   > Sorgulamak ve ToDoActivity.cs C# dosyasında bulunan verileri eklemek için, mobil uygulamanızın arka ucuna erişen kodu gözden geçirebilirsiniz.
   
## <a name="troubleshooting"></a>Sorun giderme
Çözümü oluşturma konusunda sorun yaşarsanız, NuGet paket yöneticisini çalıştırıp `Xamarin.Android` destek paketlerini güncelleştirin. Hızlı Başlangıç projeleri her zaman son sürümleri içermeyebilir.

Projenizde başvurulan tüm destek paketlerinin aynı sürüme sahip olması gerektiğini unutmayın. [Azure Mobile Apps NuGet paketi](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/), Android platformu için `Xamarin.Android.Support.CustomTabs` bağımlılığına sahiptir, yani projeniz daha yeni destek paketleri kullanıyorsa, çakışmaları önlemek için doğrudan gerekli sürüme sahip bu paketi yüklemeniz gerekir.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
