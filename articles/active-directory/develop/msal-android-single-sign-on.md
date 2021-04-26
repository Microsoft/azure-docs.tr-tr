---
title: MSAL kullanarak Android 'de uygulamalar arası SSO 'yu etkinleştirme | Mavisi
titleSuffix: Microsoft identity platform
description: Uygulamalarınız genelinde çoklu oturum açmayı etkinleştirmek için Android için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanma.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: f3d4ec8db89e9bebfdcd594e842a6c19d3d66d54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104102"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>Nasıl yapılır: MSAL kullanarak Android üzerinde uygulamalar arası SSO etkinleştirme

Çoklu oturum açma (SSO), kullanıcıların kimlik bilgilerini yalnızca bir kez girmelerini ve bu kimlik bilgilerinin uygulamalar arasında otomatik olarak çalışmasını sağlar.

[Microsoft Identity platformu](./index.yml) ve Microsoft kimlik doğrulama KITAPLıĞı (msal) kendi uygulama paketinizde SSO 'yu etkinleştirmenize yardımcı olur. Aracı yeteneği ve Authenticator uygulamalarıyla, SSO 'yu tüm cihaz genelinde genişletebilirsiniz.

Bu nasıl yapılır, uygulamanız tarafından müşterilerinize SSO sağlamak üzere kullanılan SDK 'Ları nasıl yapılandıracağınızı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu şekilde nasıl yapılacağını bildiğiniz varsayılır:

- Azure portal kullanarak uygulamanızı sağlayın. Bu konu hakkında daha fazla bilgi için [Android öğreticisinde](./tutorial-v2-android.md#create-a-project) uygulama oluşturma yönergelerine bakın.
- Uygulamanızı [Android Için Microsoft kimlik doğrulama kitaplığı](https://github.com/AzureAD/microsoft-authentication-library-for-android)ile tümleştirin.

## <a name="methods-for-single-sign-on"></a>Çoklu oturum açma yöntemleri

Android için MSAL kullanan uygulamaların SSO 'ya ulaşmasını sağlamak için iki yol vardır:

* [Aracı uygulaması](#sso-through-brokered-authentication) aracılığıyla
* [Sistem tarayıcısı](#sso-through-system-browser) aracılığıyla


   Cihaz genelinde SSO, hesap yönetimi ve koşullu erişim gibi avantajlar için bir aracı uygulaması kullanmanız önerilir. Ancak, kullanıcılarınızın ek uygulamalar indirmesini gerektirir.

## <a name="sso-through-brokered-authentication"></a>Aracılı kimlik doğrulaması aracılığıyla SSO

Cihaz genelinde çoklu oturum açma 'ya (SSO) katılmak ve kuruluş koşullu erişim ilkelerini karşılamak için Microsoft 'un kimlik doğrulama aracılarından birini kullanmanızı öneririz. Bir aracı ile tümleştirme aşağıdaki avantajları sağlar:

- Cihaz çoklu oturum açma
- İçin koşullu erişim:
  - Intune Uygulama Koruması
  - Cihaz kaydı (Workplace Join)
  - Mobil Cihaz Yönetimi
- Cihaz genelinde hesap yönetimi
  -  Android AccountManager & hesap ayarlarını kullanarak
  - "İş hesabı"-özel hesap türü

Android 'de, Microsoft kimlik doğrulama Aracısı, [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) ve [Intune şirket portalı](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) uygulamalarına dahil olan bir bileşendir.

Aşağıdaki diyagramda, uygulamanız, Microsoft kimlik doğrulama kitaplığı (MSAL) ve Microsoft 'un kimlik doğrulama aracıları arasındaki ilişki gösterilmektedir.

![Bir uygulamanın MSAL, Broker Apps ve Android Account Manager ile ilişkisini gösteren diyagram.](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>Aracı barındıran uygulamalar yükleme

Aracı barındırma uygulamaları, cihaz sahibi tarafından, uygulama mağazalarından (genellikle Google Play Store) herhangi bir zamanda yüklenebilir. Ancak bazı API 'Ler (kaynaklar), cihazların şunları gerektiren koşullu erişim Ilkeleriyle korunmaktadır:

- Kayıtlı (çalışma alanına katılmış) ve/veya
- Cihaz yönetimine kaydolmuş veya
- Intune Uygulama Koruması kayıtlı

Bir cihazda zaten yüklü bir aracı uygulaması yoksa, MSAL, uygulamanın bir belirteci etkileşimli olarak almaya çalıştığı anda kullanıcıya bir tane yüklemesini ister. Daha sonra uygulamanın, cihazı gerekli ilkeyle uyumlu hale getirmek için kullanıcıyı bu adımları izleyerek yapması gerekir.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>Aracıyı yükleme ve kaldırma etkileri

#### <a name="when-a-broker-is-installed"></a>Bir aracı yüklendiğinde

Bir aracı bir cihaza yüklendiğinde, sonraki tüm etkileşimli Belirteç istekleri (öğesine çağrılar `acquireToken()` ), msal tarafından yerel olarak değil, aracı tarafından işlenir. Daha önce MSAL için kullanılabilir olan tüm SSO durumları, aracıda kullanılamaz. Sonuç olarak, kullanıcının yeniden kimlik doğrulaması yapması veya cihaz tarafından bilinen mevcut hesapların listesinden bir hesap seçmeniz gerekir.

Bir aracı yüklemek için kullanıcının yeniden oturum açması gerekmez. Yalnızca kullanıcının bir sorunu çözmesi gerektiğinde, bir `MsalUiRequiredException` sonraki istek aracıya gider. `MsalUiRequiredException` çeşitli nedenlerle oluşturulabilir ve etkileşimli olarak çözülmesi gerekir. Örnek:

- Kullanıcı, hesabıyla ilişkili parolayı değiştirdi.
- Kullanıcının hesabı artık bir koşullu erişim ilkesini karşılamamaktadır.
- Kullanıcı, uygulamanın hesabıyla ilişkilendirilmesi için onayını iptal etti.

**Birden çok** aracı-bir cihaza birden çok aracı yüklenirse, önce yüklenen aracı her zaman etkin aracıdır. Bir cihazda yalnızca tek bir aracı etkin olabilir.

#### <a name="when-a-broker-is-uninstalled"></a>Bir aracı kaldırıldığında

Yalnızca bir aracı barındırma uygulaması yüklüyse ve kaldırılırsa, kullanıcının yeniden oturum açması gerekecektir. Etkin Aracıyı kaldırmak, hesabı ve ilişkili belirteçleri cihazdan kaldırır.

Intune Şirket Portalı yüklenip etkin aracı olarak çalışıyorsa ve Microsoft Authenticator da yüklüyse, Intune Şirket Portalı (etkin aracı) kaldırılırsa, kullanıcının yeniden oturum açması gerekir. Yeniden oturum açtıklarında, Microsoft Authenticator uygulama etkin aracı olur.

### <a name="integrating-with-a-broker"></a>Bir aracı ile tümleştirme

#### <a name="generate-a-redirect-uri-for-a-broker"></a>Aracı için yeniden yönlendirme URI 'SI oluşturma

Aracıda uyumlu bir yeniden yönlendirme URI 'SI kaydetmeniz gerekir. Aracının yeniden yönlendirme URI 'SI, uygulamanızın paket adını ve uygulamanızın imzasının Base64 kodlamalı gösterimini içermelidir.

Yeniden yönlendirme URI 'sinin biçimi: `msauth://<yourpackagename>/<base64urlencodedsignature>`

[Anahtar aracını](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) , uygulamanızın imzalama anahtarlarını kullanarak base64 kodlu bir imza karması oluşturmak için kullanabilir ve ardından Azure Portal kullanarak YENIDEN yönlendirme URI 'nizi bu karmayı kullanarak oluşturabilirsiniz.

Linux ve macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

*Keytool* ile imza karması oluşturduktan sonra, YENIDEN yönlendirme URI 'sini oluşturmak için Azure Portal kullanın:

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> oturum açın ve **uygulama kayıtları** Android uygulamanızı seçin.
1. **Kimlik doğrulama**  >  **platformu Ekle**  >  **Android**' i seçin.
1. Açılan **Android uygulamanızı yapılandırın** bölmesinde, daha önce oluşturduğunuz **imza karmasını** ve bir **paket adını** girin.
1. **Yapılandır** düğmesini seçin.

Azure portal, yeniden yönlendirme URI 'sini oluşturur ve **Android yapılandırma** bölmesinin **yeniden yönlendirme URI 'si** alanında görüntüler.

Uygulamanızı imzalama hakkında daha fazla bilgi için, Android Studio kullanıcı kılavuzunda [uygulamanızı](https://developer.android.com/studio/publish/app-signing) imzalama bölümüne bakın.

> [!IMPORTANT]
> Uygulamanızın üretim sürümü için üretim imzalama anahtarınızı kullanın.

#### <a name="configure-msal-to-use-a-broker"></a>Aracıyı kullanmak için MSAL yapılandırma

Uygulamanızda bir aracı kullanmak için, aracı yeniden yönlendirmeyi yapılandırdığınız için test etmeniz gerekir. Örneğin, hem aracının etkin yeniden yönlendirme URI 'sini, hem de MSAL yapılandırma dosyanıza aşağıdaki ayarları ekleyerek kaydettiğiniz olduğunu belirtin:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Aracıda ilgili özel durumlar

MSAL, aracı ile iki şekilde iletişim kurar:

- Aracı ile bağlantılı hizmet
- Android AccountManager

MSAL önce bu hizmeti çağırmak hiçbir Android izni gerektirmediğinden, ilk olarak aracı ile ilişkili hizmeti kullanır. Bağlama hizmetine bağlama başarısız olursa, MSAL Android AccountManager API 'sini kullanır. MSAL yalnızca, uygulamanıza zaten izin verildiyse bunu yapar `"READ_CONTACTS"` .

`MsalClientException`Hata kodu ile karşılaşırsanız `"BROKER_BIND_FAILURE"` , iki seçenek vardır:

- Kullanıcıdan Microsoft Authenticator uygulaması ve Intune Şirket Portalı için güç iyileştirmesini devre dışı vermesini isteyin.
- Kullanıcıdan izin vermesini isteyin `"READ_CONTACTS"`

### <a name="verify-broker-integration"></a>Aracı tümleştirmesini doğrulama

Aracı tümleştirmesi 'nin çalıştığını hemen temizlemeyebilir, ancak aşağıdakileri denetlemek için aşağıdaki adımları kullanabilirsiniz:

1. Android cihazınızda, aracıyı kullanarak bir istek doldurun.
1. Android cihazınızdaki Ayarlar ' da, kimlik doğrulamasından geçen hesaba karşılık gelen yeni oluşturulan bir hesabı arayın. Hesap *iş hesabı* türünde olmalıdır.

Testi yinelemek istiyorsanız, hesabı ayarlardan kaldırabilirsiniz.

## <a name="sso-through-system-browser"></a>Sistem tarayıcısı aracılığıyla SSO

Android uygulamalarında, kimlik doğrulama kullanıcı deneyimi için WebView, Sistem tarayıcısı veya Chrome özel sekmelerini kullanma seçeneği vardır. Uygulama aracılı kimlik doğrulaması kullanmıyor ise, SSO sağlamak için yerel Web görünümü yerine sistem tarayıcısını kullanması gerekir.

### <a name="authorization-agents"></a>Yetkilendirme aracıları

Yetkilendirme aracıları için belirli bir strateji seçilmesi isteğe bağlıdır ve uygulamaların özelleştirebileceği ek işlevleri temsil eder. Çoğu uygulama, MSAL varsayılanlarını kullanır (çeşitli Varsayılanları görmek için bkz. [ANDROID msal yapılandırma dosyasını anlayın](msal-configuration.md) ).

MSAL `WebView` , veya sistem tarayıcısını kullanarak yetkilendirmeyi destekler.  Aşağıdaki görüntüde, veya customtab `WebView` ile veya customtab ile sistem tarayıcısını kullanarak nasıl göründüğü gösterilmektedir:

![MSAL oturum açma örnekleri](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Çoklu oturum açma etkileri

Varsayılan olarak, MSAL ile tümleştirilmiş uygulamalar, yetkilendirme için sistem tarayıcısının özel sekmelerini kullanır. WebViews 'ın aksine özel sekmeler, Web veya özel sekmelerle tümleştirilmiş diğer yerel uygulamalarla daha az oturum açma olanağı sağlayan bir tanımlama bilgisi jar 'yi varsayılan sistem tarayıcısıyla paylaşır.

Uygulama `WebView` Microsoft Authenticator veya şirket portalı desteğini uygulamayla tümleştirmeden kullanıyorsa, kullanıcılar cihazda veya yerel uygulamalar ile Web uygulamaları arasında çoklu oturum açma deneyimine sahip olmaz.

Uygulama, Microsoft Authenticator veya Intune Şirket Portalı gibi bir aracıya sahip MSAL kullanıyorsa, uygulamalardan biriyle etkin bir oturum açma işlemi varsa, kullanıcılar uygulamalar arasında SSO deneyimine sahip olabilir.

### <a name="webview"></a>WebView

Uygulama içi WebView 'u kullanmak için, MSAL 'e geçirilen uygulama yapılandırması JSON öğesine aşağıdaki satırı koyun:

```json
"authorization_user_agent" : "WEBVIEW"
```

Uygulama içi kullanıldığında `WebView` , Kullanıcı doğrudan uygulamada oturum açar. Belirteçler, uygulamanın korumalı alanı içinde tutulur ve uygulamanın tanımlama bilgisi jar dışında kullanılamaz. Sonuç olarak, uygulamalar Authenticator veya Şirket Portalı tümleştirmediği sürece kullanıcının uygulamalar arasında SSO deneyimi yoktur.

Ancak, `WebView` oturum açma kullanıcı arabirimi için görünüm ve kullanım özelliklerini özelleştirme yeteneği sağlar. Bu özelleştirmeyi nasıl yapılacağı hakkında daha fazla bilgi için bkz. [Android WebViews](https://developer.android.com/reference/android/webkit/WebView) .

### <a name="default-browser-plus-custom-tabs"></a>Varsayılan tarayıcı Plus özel sekmeler

Varsayılan olarak, MSAL tarayıcıyı ve [özel bir sekmeler](https://developer.chrome.com/multidevice/android/customtabs) stratejisini kullanır. Bu stratejiyi, `DEFAULT` AŞAĞıDAKI JSON yapılandırmasını özel yapılandırma dosyasında kullanarak, gelecekteki sürümlerde yapılan değişikliklerin önlenmesi için açıkça belirtebilirsiniz:

```json
"authorization_user_agent" : "BROWSER"
```

Cihazın tarayıcısı aracılığıyla bir SSO deneyimi sağlamak için bu yaklaşımı kullanın. MSAL, diğer yerel uygulamaların veya Web uygulamalarının, MSAL tarafından ayarlanan kalıcı oturum tanımlama bilgisini kullanarak cihazda SSO sağlamasına izin veren paylaşılan bir tanımlama bilgisi jar 'i kullanır.

### <a name="browser-selection-heuristic"></a>Tarayıcı seçimi buluşsal yöntemi

MSAL 'in çok sayıda Android telefonunda kullanılacak tarayıcı paketinin tam olarak belirtilmediği için, MSAL, en iyi cihazlar arası SSO sağlamaya çalışan bir tarayıcı seçimi buluşsal yöntemi uygular.

MSAL, birincil olarak paket yöneticisinden varsayılan tarayıcıyı alır ve test edilmiş bir güvenli tarayıcı listesinde olup olmadığını denetler. Değilse, MSAL, güvenli listeden varsayılan olmayan bir tarayıcı başlatmak yerine WebView 'u kullanmaya geri döner. Varsayılan tarayıcı, özel sekmeleri destekleyip desteklememesinden bağımsız olarak seçilir. Tarayıcı özel sekmeleri destekliyorsa, MSAL özel sekmesini başlatacaktır. özel sekmeler, uygulamaya yönelik bir görünüm ve `WebView` temel kullanıcı arabirimi özelleştirmesine izin verir. Daha fazla bilgi edinmek için bkz. [Android 'de özel sekmeler](https://developer.chrome.com/multidevice/android/customtabs) .

Cihazda hiç tarayıcı paketi yoksa, MSAL uygulamayı kullanır `WebView` . Cihaz varsayılan ayarı değiştirilmemişse, SSO deneyimi sağlamak için her oturum açma için aynı tarayıcı başlatılmalıdır.

#### <a name="tested-browsers"></a>Sınanan tarayıcılar

Aşağıdaki tarayıcılar yapılandırma dosyasında belirtilen öğesine doğru şekilde yönlendirildiklerini görmek üzere test edilmiştir `"redirect_uri"` :

| Cihaz | Yerleşik tarayıcı | Chrome | Opera  | Microsoft Edge | UC tarayıcısı | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | aktar | aktar |uygulanamaz |uygulanamaz |uygulanamaz |uygulanamaz |
| Samsung S7 (API 25) | <sup>1</sup> . geçiş | aktar | aktar | aktar | Neden |aktar |
| Huawei (API 26) |<sup>2</sup> . geçiş | aktar | Neden | aktar | aktar |aktar |
| Vivo (API 26) |aktar|aktar|aktar|aktar|aktar|Neden|
| Piksel 2 (API 26) |aktar | aktar | aktar | aktar | Neden |aktar |
| Oppo | aktar | geçerli değil<sup>3</sup>|uygulanamaz  |uygulanamaz |uygulanamaz | uygulanamaz|
| OnePlus (API 25) |aktar | aktar | aktar | aktar | Neden |aktar |
| Nexus (API 28) |aktar | aktar | aktar | aktar | Neden |aktar |
|MI | aktar | aktar | aktar | aktar | Neden |aktar |

<sup>1</sup> Samsung 'in yerleşik tarayıcısı Samsung Internet 'dir.<br/>
<sup>2</sup> Huawei 'ın yerleşik tarayıcısı Huawei tarayıcısıdır.<br/>
<sup>3</sup> Varsayılan tarayıcı, Oppo cihaz ayarı içinde değiştirilemez.

## <a name="next-steps"></a>Sonraki adımlar

[Android cihazları Için paylaşılan cihaz modu](msal-android-shared-devices.md) , bir Android cihazını birden çok çalışan tarafından kolayca paylaşılabilen şekilde yapılandırmanıza olanak tanır.