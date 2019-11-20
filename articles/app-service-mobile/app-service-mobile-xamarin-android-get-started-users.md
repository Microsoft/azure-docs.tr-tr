---
title: Xamarin Android 'de Mobile Apps kimlik doğrulaması ile çalışmaya başlama
description: AAD, Google, Facebook, Twitter ve Microsoft gibi çeşitli kimlik sağlayıcıları aracılığıyla Xamarin Android uygulamanızın kullanıcılarının kimliğini doğrulamak için Mobile Apps nasıl kullanacağınızı öğrenin.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: e0baf64c3490dd5b7edd8c3f7d209c135c546ed5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388438"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Xamarin. Android uygulamanıza kimlik doğrulaması ekleme
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış
Bu konu, istemci uygulamanızdan bir mobil uygulama kullanıcılarının kimliğini nasıl doğrulayacağınızı gösterir. Bu öğreticide, Azure Mobile Apps tarafından desteklenen bir kimlik sağlayıcısı kullanarak hızlı başlangıç projesine kimlik doğrulaması eklersiniz. Mobil uygulamada başarıyla kimlik doğrulamasından ve yetkilendirdikten sonra, Kullanıcı KIMLIĞI değeri görüntülenir.

Bu öğretici, mobil uygulama hızlı başlangıcını temel alır. Ayrıca öncelikle [Xamarin. Android uygulaması oluşturma]öğreticisini tamamlamalısınız. İndirilen hızlı başlangıç sunucusu projesini kullanmazsanız, kimlik doğrulama uzantısı paketini projenize eklemeniz gerekir. Sunucu Uzantısı paketleri hakkında daha fazla bilgi için bkz. [Azure için .net arka uç sunucu SDK 'sı Mobile Apps çalışma](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Uygulamanızı kimlik doğrulaması için kaydetme ve uygulama hizmetlerini yapılandırma
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Uygulamanızı Izin verilen dış yönlendirme URL 'Lerine ekleyin

Güvenli kimlik doğrulaması, uygulamanız için yeni bir URL şeması tanımlamanızı gerektirir. Bu, kimlik doğrulama işlemi tamamlandıktan sonra kimlik doğrulama sisteminin uygulamanıza yeniden yönlendirilmesini sağlar. Bu öğreticide, üzerinde URL şeması _uygulamamız_ kullanırız. Ancak, seçtiğiniz herhangi bir URL şemasını kullanabilirsiniz. Bu, mobil uygulamanız için benzersiz olmalıdır. Sunucu tarafında yeniden yönlendirmeyi etkinleştirmek için:

1. [Azure portal] içinde App Service seçin.

2. **Kimlik doğrulama/yetkilendirme** menü seçeneğine tıklayın.

3. **Izin verilen dış yeniden yönlendirme URL 'lerinde**`url_scheme_of_your_app://easyauth.callback` girin.  Bu dizedeki **url_scheme_of_your_app** , MOBIL uygulamanızın URL şemadır.  Bir protokol için normal URL belirtimini izlemelidir (yalnızca harfler ve rakamlar kullanın ve bir harfle başlar).  Mobil uygulama kodunuzu birkaç yerde URL düzeniyle ayarlamanız gerekeceğinden, seçtiğiniz dizeyi bir yere iade etmeniz gerekir.

4. **Tamam**’a tıklayın.

5. **Kaydet** düğmesine tıklayın.

## <a name="permissions"></a>Kimliği doğrulanmış kullanıcılar için izinleri kısıtla
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Visual Studio veya Xamarin Studio, istemci projesini bir cihazda veya Öykünücüde çalıştırın. Uygulama başladıktan sonra 401 (yetkisiz) durum koduna sahip işlenmemiş bir özel durumun yapıldığını doğrulayın. Bu durum, uygulamanın mobil uygulama arka ucunuza kimliği doğrulanmamış bir kullanıcı olarak erişmeye çalıştığı için oluşur. *TodoItem* tablosu artık kimlik doğrulaması gerektiriyor.

Daha sonra, istemci uygulamasını, kimliği doğrulanmış bir kullanıcıyla mobil uygulama arka ucuna kaynak isteyecek şekilde güncelleşolursunuz.

## <a name="add-authentication"></a>Uygulamaya kimlik doğrulaması ekleme
Uygulama, kullanıcıların **oturum açma** düğmesine dokunmasına ve veri görüntülenmeden önce kimlik doğrulamasından geçmesini gerektirecek şekilde güncelleştirilir.

1. Aşağıdaki kodu **TodoActivity** sınıfına ekleyin:
   
        // Define an authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Bu, yeni bir **oturum açma** düğmesi için bir kullanıcının ve Yöntem işleyicisinin kimlik doğrulaması için yeni bir yöntem oluşturur. Yukarıdaki örnek kodda bulunan kullanıcının kimliği, Facebook oturum açma kullanılarak doğrulanır. Kimlik doğrulamasından sonra kullanıcı KIMLIĞINI göstermek için bir iletişim kutusu kullanılır.
   
   > [!NOTE]
   > Facebook dışında bir kimlik sağlayıcısı kullanıyorsanız, yukarıda **Loginasync** değerine geçirilen değeri şunlardan birine değiştirin: *MicrosoftAccount*, *Twitter*, *Google*veya *windowsazureactivedirectory*.
   > 
   > 
2. **OnCreate** yönteminde aşağıdaki kod satırını silin veya not edin:
   
        OnRefreshItemsSelected ();
3. Activity_To_Do. axml dosyasında *, var olan* ek düğmeden önce şu *LoginUser* düğme tanımını ekleyin:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Aşağıdaki öğeyi dizeler. xml kaynak dosyasına ekleyin:
   
        <string name="login_button_text">Sign in</string>
5. AndroidManifest. xml dosyasını açın, `<application>` XML öğesinin içine aşağıdaki kodu ekleyin:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. Visual Studio veya Xamarin Studio 'de, istemci projesini bir cihaz veya öykünücü üzerinde çalıştırın ve seçtiğiniz kimlik sağlayıcısıyla oturum açın. Başarıyla oturum açtığınızda, uygulama oturum açma KIMLIĞINIZI ve Yapılacaklar öğelerinin listesini görüntüler ve verilerde güncelleştirmeler yapabilirsiniz.

## <a name="troubleshooting"></a>Sorun giderme

**Uygulama `Java.Lang.NoSuchMethodError: No static method startActivity` ile kilitlendi**

Bazı durumlarda, destek paketlerindeki çakışmalar yalnızca Visual Studio 'da bir uyarı olarak gösterilir, ancak uygulama çalışma zamanında bu özel durumla kilitleniyor. Bu durumda, projenizde başvurulan tüm destek paketlerinin aynı sürüme sahip olduğundan emin olmanız gerekir. [Azure Mobile Apps NuGet paketi](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/), Android platformu için `Xamarin.Android.Support.CustomTabs` bağımlılığına sahiptir, yani projeniz daha yeni destek paketleri kullanıyorsa, çakışmaları önlemek için doğrudan gerekli sürüme sahip bu paketi yüklemeniz gerekir.

<!-- URLs. -->
[Xamarin. Android uygulaması oluşturma]: app-service-mobile-xamarin-android-get-started.md
