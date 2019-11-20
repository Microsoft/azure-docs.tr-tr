---
title: Xamarin.Forms ile Mobile Apps’i kullanmaya başlama
description: Xamarin.Forms geliştirme için Azure Mobile Apps kullanmaya başlamak için bu öğreticiyi izleyin.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: e891bc34840e8c8bb61be1a9da607c43a93f2d17
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388459"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Azure ile Xamarin.Forms uygulaması oluşturma

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış
Bu öğreticide, bir Xamarin.Forms mobil uygulamasına bulut tabanlı bir arka uç hizmetini Azure Uygulama Hizmeti’nin Mobile Apps özelliğini kullanarak nasıl ekleyeceğiniz gösterilir. Yeni bir Mobil Uygulama arka ucu ve uygulama verilerini Azure’da depolayan basit bir yapılacaklar listesi Xamarin.Forms uygulaması oluşturacaksınız.

Bu öğreticiyi tamamlamak Xamarin.Forms uygulamalarına ilişkin tüm Mobile Apps öğreticileri için ön koşuldur.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Etkin bir Azure hesabı. Bir hesabınız yoksa, Azure deneme sürümünü kaydolabilir ve deneme süresi bittikten sonra dahi kullanmaya devam edebileceğiniz 10 ücretsiz mobil uygulama edinebilirsiniz. Daha fazla bilgi için bkz. [Azure Ücretsiz Denemesi](https://azure.microsoft.com/pricing/free-trial/).

* Xamarin için Visual Studio Araçları, Visual Studio 2017 veya üzeri ya da Mac için Visual Studio. Yönergeler için [Xamarin yükleme sayfasına][Install Xamarin] bakın.

* (isteğe bağlı) iOS uygulaması oluşturmak için, Xcode 9.0 veya üzerine sahip bir Mac gereklidir. Mac için Visual Studio iOS uygulamaları geliştirmek için kullanılabilir veya Visual Studio 2017 veya üzeri kullanılabilir (Bu nedenle Mac ağda kullanılabilir olduğu sürece).

## <a name="create-a-new-mobile-apps-back-end"></a>Yeni bir Mobile Apps arka ucu oluşturma
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Bir veritabanı bağlantısı oluşturma ve istemciyi ve sunucu projesini yapılandırma
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>Xamarin. Forms çözümünü çalıştırma

Xamarin Visual Studio Araçları çözümü açmak için gereklidir, [Xamarin yükleme yönergelerine][Install Xamarin]bakın. Araçlar zaten yüklendiyse, çözümü indirip açmak için aşağıdaki adımları izleyin:

### <a name="visual-studio-windows-and-mac"></a>Visual Studio (Windows ve Mac)

1. [Azure Portal](https://portal.azure.com/) gidin ve oluşturduğunuz mobil uygulamaya gidin. @No__t-0 dikey penceresinde, mobil uygulamanız için genel uç nokta olan URL 'YI arayın. Örnek-uygulamamın adı "test123" olan SiteName değeri https://test123.azurewebsites.net olacaktır.

2. Bu klasörde-0 @no__t dosyasını açın-Xamarin. Forms/ZUMOAPPNAME. Uygulama adı `ZUMOAPPNAME` ' dır.

3. @No__t-0 sınıfında, `ZUMOAPPURL` değişkenini Yukarıdaki genel uç nokta ile değiştirin.

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    geldiğinde

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. Android veya Windows projelerini ve ağa bağlı bir Mac bilgisayar varsa iOS projesini çalıştırmak için aşağıdaki yönergeleri izleyin.

## <a name="optional-run-the-android-project"></a>(İsteğe bağlı) Android projesi çalıştırma

Bu bölümde Xamarin.Android projesini çalıştırırsınız. Android cihazlarıyla çalışmıyorsanız, bu bölümü atlayabilirsiniz.

### <a name="visual-studio"></a>Visual Studio

1. Android (Droid) projesine sağ tıklayın ve ardından **Başlangıç Projesi Olarak Ayarla**’yı seçin.

2. **Yapı** menüsünde, **Yapılandırma Yöneticisi**’ni seçin.

3. **Yapılandırma Yöneticisi** iletişim kutusunda, Android projesinin yanındaki **Yapı** ve **Dağıt** onay kutularını seçin ve paylaşılan kod projesinde **Yapı** kutusunun işaretli olduğundan emin olun.

4. Projeyi oluşturmak ve uygulamayı Android öykünücüsünde başlatmak için **F5** tuşuna basın veya **Başlat** düğmesine tıklayın.

### <a name="visual-studio-for-mac"></a>Mac için Visual Studio

1. Android projesine sağ tıklayın ve ardından **Başlangıç Projesi Olarak Ayarla**’yı seçin.

2. Projeyi oluşturmak ve uygulamayı Android öykünücüsünde başlatmak için **Çalıştır** menüsünü ve ardından **Hata Ayıklamayı Başlat**’ı seçin.

Uygulamada, *Xamarin öğren* gibi anlamlı bir metin yazın ve ardından artı simgesini ( **+** ) seçin.

![Android yapılacaklar uygulaması][11]

Bu işlem, Azure üzerinde barındırılan yeni Mobile Apps arka ucuna bir post isteği gönderir. İstekten alınan veriler TodoItem tablosuna eklenir. Tabloda depolanan öğeler Mobile Apps arka ucu tarafından döndürülür ve veriler listede görüntülenir.

> [!NOTE]
> Mobile Apps arka ucuna erişen kod, çözümün paylaşılan kod projesinin **TodoItemManager.cs** C# dosyasındadır.
>

## <a name="optional-run-the-ios-project"></a>(İsteğe bağlı) iOS projesi çalıştırma

Bu bölümde iOS cihazlarda Xamarin iOS projesi çalıştırırsınız. iOS cihazlarıyla çalışmıyorsanız, bu bölümü atlayabilirsiniz.

### <a name="visual-studio"></a>Visual Studio

1. iOS projesine sağ tıklayın ve ardından **Başlangıç Projesi Olarak Ayarla**’yı seçin.

2. **Yapı** menüsünde, **Yapılandırma Yöneticisi**’ni seçin.

3. **Yapılandırma Yöneticisi** iletişim kutusunda, iOS projesinin yanındaki **Yapı** ve **Dağıt** onay kutularını seçin ve paylaşılan kod projesinde **Yapı** kutusunun işaretli olduğundan emin olun.

4. Projeyi oluşturmak ve uygulamayı iPhone öykünücüsünde başlatmak için **F5** tuşuna basın.

### <a name="visual-studio-for-mac"></a>Mac için Visual Studio

1. iOS projesine sağ tıklayın ve ardından **Başlangıç Projesi Olarak Ayarla**’yı seçin.

2. **Çalıştır** menüsünde, **Hata Ayıklamayı Başlat**’a tıklayarak projeyi oluşturun ve iPhone öykünücüsünde uygulamayı başlatın.

Uygulamada, *Xamarin öğren* gibi anlamlı bir metin yazın ve ardından artı simgesini ( **+** ) seçin.

![iOS yapılacaklar uygulaması][10]

Bu işlem, Azure üzerinde barındırılan yeni Mobile Apps arka ucuna bir post isteği gönderir. İstekten alınan veriler TodoItem tablosuna eklenir. Tabloda depolanan öğeler Mobile Apps arka ucu tarafından döndürülür ve veriler listede görüntülenir.

> [!NOTE]
> Çözümün paylaşılan kod projesinin **TodoItemManager.cs** C# dosyasında Mobile Apps arka ucuna erişen kodu bulacaksınız.
>

## <a name="optional-run-the-windows-project"></a>(İsteğe bağlı) Windows projesi çalıştırma

Bu bölümde, Windows cihazlar için Xamarin.Forms Evrensel Windows Platformu (UWP) projelerini çalıştırırsınız. Windows cihazlarıyla çalışmıyorsanız, bu bölümü atlayabilirsiniz.

### <a name="visual-studio"></a>Visual Studio

1. Herhangi bir UWP projesine sağ tıklayın ve ardından **Başlangıç Projesi Olarak Ayarla**’yı seçin.

2. **Yapı** menüsünde, **Yapılandırma Yöneticisi**’ni seçin.

3. **Yapılandırma Yöneticisi** iletişim kutusunda, seçtiğiniz Windows projesinin yanındaki **Yapı** ve **Dağıt** onay kutularını seçin ve paylaşılan kod projesinde **Yapı** kutusunun işaretli olduğundan emin olun.

4. Projeyi oluşturmak ve uygulamayı Windows öykünücüsünde başlatmak için **F5** tuşuna basın veya **Başlat** düğmesine (**Yerel Makine** yazmalıdır) tıklayın.

> [!NOTE]
> Windows projesi macOS üzerinde çalıştırılamaz.

Uygulamada, *Xamarin öğren* gibi anlamlı bir metin yazın ve ardından artı simgesini ( **+** ) seçin.

Bu işlem, Azure üzerinde barındırılan yeni Mobile Apps arka ucuna bir post isteği gönderir. İstekten alınan veriler TodoItem tablosuna eklenir. Tabloda depolanan öğeler Mobile Apps arka ucu tarafından döndürülür ve veriler listede görüntülenir.

![UWP yapılacaklar uygulaması][12]

> [!NOTE]
> Çözümünüzün taşınabilir sınık kitaplık projesinin **TodoItemManager.cs** C# dosyasında Mobile Apps arka ucuna erişen kodu bulacaksınız.
>

## <a name="troubleshooting"></a>Sorun giderme

Çözümü oluşturma konusunda sorun yaşarsanız, NuGet paket yöneticisini çalıştırıp `Xamarin.Forms`’in en son sürümüne güncelleştirin ve Android projesinde `Xamarin.Android` destek paketlerini güncelleştirin. Hızlı Başlangıç projeleri her zaman son sürümleri içermeyebilir.

Android projenizde başvurulan tüm destek paketlerinin aynı sürüme sahip olması gerektiğini unutmayın. [Azure Mobile Apps NuGet paketi](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/), Android platformu için `Xamarin.Android.Support.CustomTabs` bağımlılığına sahiptir, yani projeniz daha yeni destek paketleri kullanıyorsa, çakışmaları önlemek için doğrudan gerekli sürüme sahip bu paketi yüklemeniz gerekir.

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
