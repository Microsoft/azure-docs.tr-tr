---
title: Xamarin Android kod yapılandırması ve sorun giderme (MSAL.NET) | Mavisi
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) ile Xamarin Android kullanımıyla ilgili önemli noktalar hakkında bilgi edinin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 11642480ac817b50d102e396b8ab5e200948a615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199557"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>MSAL.NET ile Xamarin Android için yapılandırma gereksinimleri ve sorun giderme ipuçları

.NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) ile Xamarin Android kullanırken kodunuzda yapmanız gereken birkaç yapılandırma değişikliği vardır. Aşağıdaki bölümlerde, en yaygın sorunlardan bazılarını önlemenize yardımcı olmak üzere, gerekli değişiklikler ve bir [sorun giderme](#troubleshooting) bölümü gelmelidir.

## <a name="set-the-parent-activity"></a>Üst etkinliği ayarla

Xamarin Android 'de, belirtecin etkileşimden sonra döndürdüğü ana etkinliği ayarlayın:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

MSAL.NET 4,2 ve üzeri sürümlerde, bu işlevselliği [Publicclientapplication][PublicClientApplication]düzeyinde de ayarlayabilirsiniz. Bunu yapmak için bir geri çağırma kullanın:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

[CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)kullanıyorsanız, [`PublicClientApplication`][PublicClientApplication] Oluşturucu kodunuz şu kod parçacığına benzer şekilde görünmelidir:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Denetimin MSAL 'e döndürdüğünden emin olun

Kimlik doğrulama akışının etkileşimli kısmı sona erdiğinde, denetimi MSAL öğesine geri döndürün [`Activity`][Activity] .[`OnActivityResult()`][OnActivityResult] yöntemidir.

Geçersiz kılmada MSAL çağırın. NET `AuthenticationContinuationHelper` .`SetAuthenticationContinuationEventArgs()` kimlik doğrulama akışının etkileşimli bölümünün sonundaki denetimi MSAL 'e döndürme yöntemi.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest-for-system-webview-support"></a>System WebView desteği için Android bildirimini güncelleştirme 

System WebView 'u desteklemek için *AndroidManifest.xml* dosyası aşağıdaki değerleri içermelidir:

```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="msal{Client Id}" android:host="auth" />
  </intent-filter>
</activity>
```

`android:scheme`Değer, uygulama portalında yapılandırılan yeniden YÖNLENDIRME URI 'sinden oluşturulur. Örneğin, yeniden yönlendirme URI 'SI ise `msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842://auth` , `android:scheme` bildirimdeki giriş şu örneğe benzer şekilde görünür:

```xml
<data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
```

Alternatif olarak, *AndroidManifest.xml* el ile düzenlemeniz yerine [etkinlik kodda oluşturun](/xamarin/android/platform/android-manifest#the-basics) . Etkinliği kodda oluşturmak için önce özniteliğini ve özniteliğini içeren bir sınıf oluşturun `Activity` `IntentFilter` .

XML dosyasının değerlerini temsil eden bir sınıf örneği aşağıda verilmiştir:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="use-system-webview-in-brokered-authentication"></a>Aracılı kimlik doğrulamasında Sistem WebView 'u kullanma

Uygulamanızı aracılı kimlik doğrulaması kullanacak şekilde yapılandırdığınızda ve cihazda yüklü bir aracı yoksa, sistem Web Görünümü ' ni etkileşimli kimlik doğrulaması için geri dönüş olarak kullanmak için, MSAL ' yi aracının yeniden yönlendirme URI 'sini kullanarak kimlik doğrulama yanıtını yakalamaya etkinleştirin. MSAL Aracı, aracının kullanılamadığını algıladığında cihazdaki varsayılan System WebView 'u kullanarak kimlik doğrulamaya çalışacaktır. Bu varsayılan kullanıldığında, yeniden yönlendirme URI 'SI bir aracı kullanacak şekilde yapılandırıldığı ve sistem Web görünümü MSAL 'e geri dönmek için nasıl kullanacağınızı bilmez. Bu sorunu çözmek için, daha önce yapılandırdığınız aracı yeniden yönlendirme URI 'sini kullanarak bir _Amaç filtresi_ oluşturun. Uygulamanızın bildirimini şu örnekte olduğu gibi değiştirerek amaç filtresini ekleyin:

```xml
<!--Intent filter to capture System WebView or Authenticator calling back to our app after sign-in-->
<activity
      android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash" />
    </intent-filter>
</activity>
```

Değer için Azure portal kaydettiğiniz paket adını değiştirin `android:host=` . Değer için Azure portal kaydettiğiniz anahtar karmasını değiştirin `android:path=` . İmza karması URL kodlamalı *olmamalıdır.* İmza karmalarınızın başlangıcında önde gelen eğik çizgi ( `/` ) göründüğünden emin olun.

### <a name="xamarinforms-43x-manifest"></a>Xamarin. Forms 4.3. x bildirimi

Xamarin. Forms 4.3. x,AndroidManifest.xmlözniteliği olarak ayarlayan kodu oluşturur `package` `com.companyname.{appName}` . ** `DataScheme`Olarak kullanıyorsanız `msal{client_id}` , değeri ad alanı değeriyle eşleşecek şekilde değiştirmek isteyebilirsiniz `MainActivity.cs` .

## <a name="android-11-support"></a>Android 11 desteği

Android 11 ' de sistem tarayıcısını ve aracılı kimlik doğrulamasını kullanmak için, önce bu paketleri bildirmeniz gerekir, bu nedenle uygulama tarafından görülebilir. Android 10 (API 29) ve önceki sürümleri hedefleyen uygulamalar, belirli bir zamanda cihazda kullanılabilir olan paketlerin bir listesi için işletim sistemini sorgulayabilir. Android 11, gizlilik ve güvenliği desteklemek için paket görünürlüğünü, işletim sistemi paketlerinin varsayılan bir listesiyle ve uygulamanın *AndroidManifest.xml* dosyasında belirtilen paketlere düşürür. 

Uygulamanın hem sistem tarayıcısını hem de Aracısı 'nı kullanarak kimlik doğrulaması yapmasını etkinleştirmek için aşağıdaki bölümü *AndroidManifest.xml* ekleyin:

```xml
<!-- Required for API Level 30 to make sure the app can detect browsers and other apps where communication is needed.-->
<!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
<queries>
  <package android:name="com.azure.authenticator" />
  <package android:name="{Package Name}" />
  <package android:name="com.microsoft.windowsintune.companyportal" />
  <!-- Required for API Level 30 to make sure the app detect browsers
      (that don't support custom tabs) -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" />
  </intent>
  <!-- Required for API Level 30 to make sure the app can detect browsers that support custom tabs -->
  <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
  <intent>
    <action android:name="android.support.customtabs.action.CustomTabsService" />
  </intent>
</queries>
``` 

`{Package Name}`Uygulama paketi adıyla değiştirin. 

Artık Sistem tarayıcısı ve aracılı kimlik doğrulaması desteği içeren güncelleştirilmiş bildiriminiz Şu örneğe benzer görünmelidir:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.XamarinDev">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <application android:theme="@android:style/Theme.NoTitleBar">
        <activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msauth" android:host="com.companyname.XamarinDev" android:path="/Fc4l/5I4mMvLnF+l+XopDuQ2gEM=" />
            </intent-filter>
        </activity>
    </application>
    <!-- Required for API Level 30 to make sure we can detect browsers and other apps we want to
     be able to talk to.-->
    <!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
    <queries>
        <package android:name="com.azure.authenticator" />
        <package android:name="com.companyname.xamarindev" />
        <package android:name="com.microsoft.windowsintune.companyportal" />
        <!-- Required for API Level 30 to make sure we can detect browsers
        (that don't support custom tabs) -->
        <intent>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="https" />
        </intent>
        <!-- Required for API Level 30 to make sure we can detect browsers that support custom tabs -->
        <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
        <intent>
            <action android:name="android.support.customtabs.action.CustomTabsService" />
        </intent>
    </queries>
</manifest>
```

## <a name="use-the-embedded-web-view-optional"></a>Katıştırılmış Web görünümünü kullanın (isteğe bağlı)

Varsayılan olarak, MSAL.NET, sistem Web tarayıcısını kullanır. Bu tarayıcı, Web uygulamalarını ve diğer uygulamaları kullanarak çoklu oturum açmayı (SSO) almanızı sağlar. Nadir bazı durumlarda sisteminizin gömülü bir Web görünümü kullanmasını isteyebilirsiniz.

Bu kod örneği, gömülü bir Web görünümünün nasıl ayarlanacağını gösterir:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Daha fazla bilgi için bkz. MSAL.NET ve [Xamarin Android sistem tarayıcı konuları](msal-net-system-browser-android-considerations.md) [Için Web tarayıcıları kullanma](msal-net-web-browsers.md) .

## <a name="troubleshooting"></a>Sorun giderme

### <a name="general-tips"></a>Genel ipuçları

- Mevcut MSAL.NET NuGet paketini [msal.net 'in en son sürümüne](https://www.nuget.org/packages/Microsoft.Identity.Client/)güncelleştirin.
- Xamarin. Forms 'un en son sürümde olduğunu doğrulayın.
- Xamarin. Android. support. v4 'nin en son sürümde olduğunu doğrulayın.
- Tüm Xamarin. Android. support paketlerinin en son sürümü hedef aldığından emin olun.
- Uygulamayı temizleyin veya yeniden derleyin.
- Visual Studio 'da, en fazla paralel proje derlemesi sayısını **1** olarak ayarlamayı deneyin. Bunu yapmak için, **Seçenekler**  >  **Projeler ve çözümler**  >  **derleme ve**  >  **en fazla paralel proje derlemesi sayısını** Çalıştır ' ı seçin.
- Komut satırından derleme yapıyorsanız ve komutunuz kullanıyorsa `/m` , bu öğeyi komuttan kaldırmayı deneyin.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Hata: AuthenticationContinuationHelper adı geçerli bağlamda yok

Bir hata `AuthenticationContinuationHelper` geçerli bağlamda mevcut olmadığını gösteriyorsa, Visual Studio *\* Android. csproj* dosyasını yanlış bir şekilde güncelleştirmiş olabilir. Bazen öğedeki dosya yolu, `<HintPath>` yerine yanlış bir şekilde içerir `netstandard13` `monoandroid90` .

Bu örnek, doğru bir dosya yolu içerir:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için [Microsoft Identity platform kullanan bir Xamarin mobil uygulaması](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)örneğine bakın. Aşağıdaki tabloda README dosyasındaki ilgili bilgiler özetlenmektedir.

| Örnek | Platform | Description |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, UWP | Azure AD 2,0 uç noktası aracılığıyla Microsoft kişisel hesaplarının ve Azure AD kimlik doğrulaması için MSAL kullanmayı gösteren basit bir Xamarin. Forms uygulaması. Uygulama ayrıca Microsoft Graph nasıl erişebileceğini ve elde edilen belirtecin nasıl gösterileceğini gösterir. <br>![Kimlik doğrulama akışı diyagramı](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
