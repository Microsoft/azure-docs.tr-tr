---
title: Xamarin iOS & Android ile aracıları kullanma | Mavisi
titleSuffix: Microsoft identity platform
description: .NET için Microsoft Authenticator ve Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.NET) kullanan Xamarin iOS uygulamalarını ayarlamayı öğrenin. Ayrıca, .NET için Azure AD kimlik doğrulama kitaplığı 'ndan (ADAL.NET) .NET için Microsoft kimlik doğrulama kitaplığı 'na (MSAL.NET) geçiş yapmayı öğrenin.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 226e94510709b37a7e6b1aae90a7e0ec5b4222b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199569"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Xamarin uygulamalarında Microsoft Authenticator veya Intune Şirket Portalı kullanma

Android ve iOS 'ta Microsoft Authenticator ve Android 'e özgü Microsoft Intune gibi aracılar Şirket Portalı etkinleştirir:

- **Çoklu oturum açma (SSO)**: kullanıcıların her uygulamada oturum açması gerekmez.
- **Cihaz kimliği**: aracı cihaz sertifikasına erişir. Bu sertifika, çalışma alanına katıldığında cihazda oluşturulur.
- **Uygulama tanımlama doğrulaması**: bir uygulama aracıyı çağırdığında, yeniden yönlendirme URL 'sini geçirir. Aracı URL 'YI doğrular.

Bu özelliklerden birini etkinleştirmek için, `WithBroker()` yöntemini çağırdığınızda parametresini kullanın `PublicClientApplicationBuilder.CreateApplication` . `.WithBroker()`Parametresi varsayılan olarak true olarak ayarlanır.

.NET için Microsoft kimlik doğrulama kitaplığı 'nda (MSAL.NET) aracılı kimlik doğrulamasının kurulumu platforma göre değişiklik gösterir:

* [iOS uygulamaları](#brokered-authentication-for-ios)
* [Android Uygulamaları](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>İOS için aracılı kimlik doğrulaması

Xamarin. iOS uygulamanızın [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) uygulamayla iletişim kurmasını sağlamak için aşağıdaki adımları kullanın. İOS 13 ' i hedefliyorsanız, [Apple 'ın son API değişikliğini](./msal-net-xamarin-ios-considerations.md)okumayı göz önünde bulundurun.

### <a name="step-1-enable-broker-support"></a>1. Adım: aracı desteğini etkinleştirme

Ayrı örnekleri için aracı desteğini etkinleştirmeniz gerekir `PublicClientApplication` . Destek varsayılan olarak devre dışıdır. `PublicClientApplication`Aracılığıyla oluşturduğunuzda `PublicClientApplicationBuilder` , `WithBroker()` Aşağıdaki örnekte gösterildiği gibi parametresini kullanın. `WithBroker()`Parametresi varsayılan olarak true olarak ayarlanır.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>2. Adım: Anahtarlık erişimini etkinleştirme

Anahtarlık erişimini etkinleştirmek için, uygulamanız için bir Anahtarlık erişim grubunuz olmalıdır. `WithIosKeychainSecurityGroup()`Uygulamanızı oluştururken Anahtarlık erişim grubunuzu ayarlamak için API 'yi kullanabilirsiniz:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Daha fazla bilgi için bkz. [Anahtarlık erişimini etkinleştirme](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3. Adım: geri aramayı işlemek için AppDelegate 'i güncelleştirme

MSAL.NET aracı çağırdığında, aracı, sınıfının yöntemi aracılığıyla uygulamanıza geri çağrı yapılır `OpenUrl` `AppDelegate` . MSAL, aracının yanıtını beklediği için uygulamanızın MSAL.NET geri çağırmak için birlikte çalışması gerekir. Bu ortak işlemi etkinleştirmek için *Appdelegate. cs* dosyasını aşağıdaki yöntemi geçersiz kılmak üzere güncelleştirin.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
    if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
    {
      AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
      return true;
    }

    else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
    {
         return false;
    }

    return true;
}
```

Bu yöntem, uygulama her başlatıldığında çağrılır. Aracıdan gelen yanıtı işlemek ve MSAL.NET 'in başladığı kimlik doğrulama işlemini tamamlamaya yönelik bir fırsat olarak kullanılır.

### <a name="step-4-set-uiviewcontroller"></a>4. Adım: UIViewController () ayarlama

Hala *Appdelegate. cs* dosyasında bir nesne penceresi ayarlayın. Genellikle Xamarin iOS için nesne penceresini ayarlamanız gerekmez, ancak aracıdan yanıt göndermek ve almak için bir nesne penceresi gerekir.

Nesne penceresini ayarlamak için:

1. *Appdelegate. cs* dosyasında, `App.RootViewController` yeni olarak ayarlayın `UIViewController()` . Bu atama, aracı çağrısının dahil edilmesini sağlar `UIViewController` . Bu ayar yanlış atanmışsa, şu hatayı alabilirsiniz:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. `AcquireTokenInteractive`Çağrıda, `.WithParentActivityOrWindow(App.RootViewController)` öğesini kullanın ve ardından kullanacağınız nesne penceresi başvurusunu geçirin.

    *App. cs*'de:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    *Appdelegate. cs* dosyasında:

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    `AcquireToken`Çağrıda:

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>5. Adım: bir URL düzenini kaydetme

MSAL.NET, aracıyı çağırmak için URL 'Ler kullanır ve sonra, aracı yanıtını uygulamanıza döndürür. Gidiş dönüş işleminin tamamlanabilmesi için, *Info. plist* dosyasına uygulamanız IÇIN bir URL düzeni kaydedin.

`CFBundleURLSchemes`Ad, `msauth.` ön ek olarak içermeli. Önekini ile izleyin `CFBundleURLName` .

URL şemasında, `BundleId` uygulamayı benzersiz olarak tanımlar: `$"msauth.(BundleId)"` . Öyleyse `BundleId` `com.yourcompany.xforms` , URL şeması olur `msauth.com.yourcompany.xforms` .

> [!NOTE]
> Bu URL şeması, Aracıdan yanıtı aldığında uygulamanızı benzersiz bir şekilde tanımlayan yeniden yönlendirme URI 'sinin bir parçası haline gelir.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>6. Adım: aracı tanımlayıcısını Lsapplicationqueriesdüzenlerinin bölümüne ekleme

MSAL, `–canOpenURL:` aracının cihazda yüklü olup olmadığını denetlemek için kullanır. İOS 9 ' da, Apple bir uygulamanın sorgulayabilme düzenlerini kilitlemiş.

`msauthv2` `LSApplicationQueriesSchemes` Aşağıdaki örnekte gösterildiği gibi, *Info. plist* dosyasının bölümüne ekleyin:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>7. Adım: uygulama kaydınız için bir yeniden yönlendirme URI 'SI ekleme

Aracıyı kullandığınızda, yeniden yönlendirme URI 'nizin ek bir gereksinimi vardır. Yeniden yönlendirme URI 'SI aşağıdaki biçime sahip _olmalıdır_ :

```csharp
$"msauth.{BundleId}://auth"
```

Aşağıda bir örnek verilmiştir:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

Yeniden yönlendirme URI 'SI, `CFBundleURLSchemes` *Info. plist* dosyasına dahil ettiğiniz adla eşleştiğine dikkat edin.

Yeniden yönlendirme URI 'sini [Azure Portal](https://portal.azure.com)uygulamanın kaydına ekleyin. Düzgün biçimli bir yeniden yönlendirme URI 'SI oluşturmak için, paket KIMLIĞINDEN aracılı yeniden yönlendirme URI 'sini oluşturmak için Azure portal **uygulama kayıtları** kullanın.

**Yeniden yönlendirme URI 'SI oluşturmak için:**

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1.   >  Kayıtlı uygulamanızı > Azure Active Directory **uygulama kayıtları** seçin
1. **Kimlik doğrulama**  >  **Platform Ekle**  >  **iOS/MacOS** seçin
1. Paket KIMLIĞINIZI girin ve ardından **Yapılandır**' ı seçin.

    Kodunuzu eklemek için **yeniden YÖNLENDIRME URI** 'si metin kutusunda görünen üretilen YENIDEN yönlendirme URI 'sini kopyalayın:

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="Azure portal içinde üretilen yeniden yönlendirme URI 'SI olan iOS platformu ayarları":::
1. Yeniden yönlendirme URI 'sini oluşturmayı tamamladıktan sonra **bitti** ' yi seçin.

## <a name="brokered-authentication-for-android"></a>Android için aracılı kimlik doğrulaması

### <a name="step-1-enable-broker-support"></a>1. Adım: aracı desteğini etkinleştirme

Aracı desteği, `PublicClientApplication` temelinde etkinleştirilir. Varsayılan olarak devre dışıdır. `WithBroker()`Aracılığıyla oluştururken parametresini kullanın (varsayılan olarak true olarak ayarlanır) `IPublicClientApplication` `PublicClientApplicationBuilder` .

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2. Adım: geri aramayı işlemek için AppDelegate 'i güncelleştirme

MSAL.NET aracı çağırdığında, aracı yöntemi ile uygulamanıza geri çağrı yapılır `OnActivityResult()` . MSAL, aracıdan gelen yanıtı bekleyecek için, uygulamanızın sonucu MSAL.NET 'e yönlendirmesi gerekir.

`SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)`Yöntemi, burada gösterildiği gibi geçersiz kılarak yöntemine yönlendirin `OnActivityResult()` :

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Bu yöntem, aracı uygulaması her başlatıldığında çağrılır ve aracıdan gelen yanıtı işleme ve MSAL.NET tarafından başlatılan kimlik doğrulama işlemini tamamlamaya yönelik bir fırsat olarak kullanılır.

### <a name="step-3-set-an-activity"></a>3. Adım: etkinlik ayarlama

Aracılı kimlik doğrulamasını etkinleştirmek için, MSAL 'in aracıya yanıt gönderebilmesi ve aracısına alabilmesi için bir etkinlik ayarlayın. Bunu yapmak için, etkinliği (genellikle `MainActivity` ) `WithParentActivityOrWindow(object parent)` üst nesneye sağlayın.

Örneğin, çağrısına `AcquireTokenInteractive()` :

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>4. Adım: uygulama kaydınız için bir yeniden yönlendirme URI 'SI ekleyin

MSAL, aracıyı çağırmak ve sonra uygulamanıza geri dönmek için URL 'Ler kullanır. Bu gidiş dönüş işleminin tamamlanabilmesi için [Azure Portal](https://portal.azure.com)kullanarak uygulamanız Için bir **yeniden yönlendirme URI 'si** kaydedin.

Uygulamanızın yeniden yönlendirme URI 'sinin biçimi APK 'yi imzalamak için kullanılan sertifikaya bağlıdır. Örnek:

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

URI 'nin son bölümü, `hgbUYHVBYUTvuvT&Y6tr554365466=` APK 'nin imzalandığı imza Için Base64 kodlamalı bir sürümdür. Visual Studio 'da uygulamanızı geliştirirken, belirli bir sertifikayla APK 'yi imzalamadan kodunuzda hata ayıklaması yapıyorsanız, Visual Studio hata ayıklama amacıyla APK 'yi imzalar. Visual Studio, APK 'yi bu şekilde imzaladığında, üzerine oluşturulduğu makine için benzersiz bir imza verir. Bu nedenle, uygulamanızı farklı bir makinede derlediğinizde, MSAL ile kimlik doğrulamak için uygulamanın kodundaki yeniden yönlendirme URI 'sini ve uygulamanın Azure portal kaydını güncelleştirmeniz gerekir.

Hata ayıklama sırasında, belirtilen yeniden yönlendirme URI 'sinin yanlış olduğunu belirten bir MSAL özel durumu (veya günlük iletisi) ile karşılaşabilirsiniz. **Özel durum veya günlük iletisi ayrıca** , üzerinde hata ayıklaması yaptığınız geçerli makineyle birlikte kullanmanız gereken YENIDEN yönlendirme URI 'sini de belirtir. Kod içinde yeniden yönlendirme URI 'sini güncelleştirmiş ve belirtilen yeniden yönlendirme URI 'sini Azure portal uygulamanın kaydına ekleyerek uygulamanızı geliştirmeye devam etmek için, belirtilen yeniden yönlendirme URI 'sini kullanabilirsiniz.

Kodunuzu sonuçlamaya hazırladıktan sonra, kod içindeki yeniden yönlendirme URI 'sini ve uygulamanın Azure portal kaydını kullanarak APK 'Yı ile imzalayabileceğinizi sertifikanın imzasını kullanın.

Uygulamada, bu, Geliştirme ekibinizin her bir üyesi için bir yeniden yönlendirme URI 'SI eklemeyi ve APK 'nin üretim imzalı sürümü *için bir yeniden* yönlendirme URI 'sini eklemeyi düşünmeniz gerektiği anlamına gelir.

İmzayı kendi MSAL nasıl yaptığı hakkında benzer şekilde kendiniz de hesaplamanız gerekir:

```csharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Aşağıdaki komutlarla **Keytool** kullanarak paketinizin imzasını alma seçeneğiniz de vardır:

* Windows:
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* MacOS
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>5. adım (isteğe bağlı): sistem tarayıcısına geri dönme

MSAL aracıyı kullanacak şekilde yapılandırıldıysa ancak aracı yüklü değilse, MSAL bir Web görünümü (tarayıcı) kullanmaya geri döner. MSAL, cihazdaki varsayılan sistem tarayıcısını kullanarak kimlik doğrulaması yapmayı dener, bu başarısız olur ve yeniden yönlendirme URI 'SI aracı için yapılandırılmıştır ve sistem tarayıcısı, MSAL 'ye geri dönmek için nasıl kullanacağınızı bilmez. Hatanın oluşmaması için, adım 4 ' te kullandığınız aracı yeniden yönlendirme URI 'SI ile bir *Amaç filtresi* yapılandırabilirsiniz.

Amaç filtresi eklemek için uygulamanızın bildirimini değiştirin:

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

Örneğin, bir yeniden yönlendirme URI 'niz varsa `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` , bildiriminiz AŞAĞıDAKI XML kod parçacığı gibi görünmelidir.

`/`Değer içindeki imzanın önünde bulunan eğik çizgi () `android:path` **gereklidir**.

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

Uygulamanızı Sistem tarayıcısı ve Android 11 desteği için yapılandırma hakkında daha fazla bilgi için bkz. [Sistem tarayıcı desteği Için Android bildirimini güncelleştirme](msal-net-xamarin-android-considerations.md#update-the-android-manifest-for-system-webview-support).

Alternatif olarak, MSAL 'yi, yeniden yönlendirme URI 'sine bağlı olmayan gömülü tarayıcıya geri dönecek şekilde yapılandırabilirsiniz:

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Android aracılı kimlik doğrulaması için sorun giderme ipuçları

Android 'de aracılı kimlik doğrulaması uyguladığınızda sorunları önlemenin bazı ipuçları aşağıda verilmiştir:

- **Yeniden yönlendirme URI 'si** - [Azure Portal](https://portal.azure.com/)uygulama kaydınız için bir yeniden yönlendirme URI 'si ekleyin. Eksik veya hatalı yeniden yönlendirme URI 'SI geliştiricilerin karşılaştığı yaygın bir sorundur.
- **Aracı sürümü** -aracı uygulamalarının gerekli en düşük sürümünü yükler. Bu iki uygulamalardan herhangi biri, Android 'de aracılı kimlik doğrulaması için kullanılabilir.
  - [Intune şirket portalı](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (sürüm 5.0.4689.0 veya üzeri)
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (sürüm 6.2001.0140 veya üzeri).
- **Broker önceliği** -msal, birden çok aracı yüklendiğinde cihazda *yüklü olan ilk aracıda* iletişim kurar.

    Örnek: Microsoft Authenticator önce yükleyip Intune Şirket Portalı yüklerseniz aracılı kimlik doğrulaması *yalnızca* Microsoft Authenticator olur.
- **Günlükler** -aracılı kimlik doğrulamasıyla ilgili bir sorunla karşılaşırsanız, aracının günlüklerini görüntülemek nedeni tanılamanıza yardımcı olabilir.
  - Microsoft Authenticator günlüklerini al:

    1. Uygulamanın sağ üst köşesindeki menü düğmesini seçin.
    1. Sorun **yaşayan geri bildirim gönder** seçeneğini belirleyin  >  .
    1. **Ne yapmak istiyorsunuz?** altında bir seçenek belirleyin ve bir açıklama ekleyin.
    1. Günlükleri göndermek için uygulamanın sağ üst köşesindeki oku seçin.

    Günlükleri gönderdikten sonra, olay KIMLIĞINI görüntüleyen bir iletişim kutusu görüntülenir. Olay KIMLIĞINI kaydedin ve yardım istediğinizde ekleyin.

  - Intune Şirket Portalı günlüklerini al:

    1. Uygulamanın sol üst köşesindeki menü düğmesini seçin.
    1. **Yardım**  >  **e-posta desteği**' ni seçin.
    1. Günlükleri göndermek için **yalnızca günlükleri karşıya yükle**' yi seçin.

    Günlükleri gönderdikten sonra, olay KIMLIĞINI görüntüleyen bir iletişim kutusu görüntülenir. Olay KIMLIĞINI kaydedin ve yardım istediğinizde ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

[Msal.NET ile Evrensel Windows platformu kullanma hakkında konular](msal-net-uwp-considerations.md)hakkında bilgi edinin.
