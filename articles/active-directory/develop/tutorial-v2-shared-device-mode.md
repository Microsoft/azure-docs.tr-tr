---
title: 'Öğretici: Android için Microsoft kimlik doğrulama kitaplığı (MSAL) ile paylaşılan cihaz modunu kullanma | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu öğreticide, bir Android cihazını paylaşılan modda çalıştırmak ve ilk satırlık bir çalışan uygulamasını çalıştırmak için nasıl hazırlanacağınızı öğreneceksiniz.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6a173ed4dae9237d8aae991c943817ed70246eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649064"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Öğretici: Android uygulamanızda paylaşılan cihaz modunu kullanma

Bu öğreticide, Android geliştiricileri ve Azure Active Directory (Azure AD) kiracı yöneticileri, Android uygulamasında paylaşılan cihaz modunu etkinleştirmek için gereken kod, kimlik doğrulayıcı uygulaması ve kiracı ayarları hakkında bilgi sağlar.

Bu öğreticide:

> [!div class="checklist"]
> * Bir kod örneği indirin
> * Paylaşılan cihaz modunu etkinleştirme ve algılama
> * Tek veya birden çok hesap modunu Algıla
> * Bir Kullanıcı anahtarını tespit edin ve küresel oturum açma ve oturum kapatma özelliğini etkinleştirin
> * Kiracıyı ayarlama ve uygulamayı Azure portal kaydetme
> * Paylaşılan cihaz modunda bir Android cihazı ayarlama
> * Örnek uygulamayı çalıştırma

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="developer-guide"></a>Geliştirici kılavuzu

Öğreticinin bu bölümü, Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanarak bir Android uygulamasında paylaşılan cihaz modunu uygulamaya yönelik Geliştirici Kılavuzu sağlar. MSAL Android uygulamanızla tümleştirme, bir Kullanıcı oturum açma, Microsoft Graph 'i çağırma ve bir kullanıcının oturumunu kapatma hakkında bilgi edinmek için [msal Android öğreticisine](./tutorial-v2-android.md) bakın.

### <a name="download-the-sample"></a>Örneği indirme

[Örnek uygulamayı](https://github.com/Azure-Samples/ms-identity-android-java/) GitHub ' dan kopyalayın. Örnek, [tek veya çoklu hesap modunda](./single-multi-account.md)çalışma özelliğine sahiptir.

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>MSAL SDK 'sını yerel Maven deponuza ekleyin

Örnek uygulamayı kullanmıyorsanız, MSAL kitaplığını Build. Gradle dosyanıza bağımlılık olarak ekleyin, örneğin:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Uygulamanızı paylaşılan cihaz modunu kullanacak şekilde yapılandırma

Yapılandırma dosyanızı ayarlama hakkında daha fazla bilgi için [yapılandırma belgelerine](./msal-configuration.md) bakın.

`"shared_device_mode_supported"` `true` Msal yapılandırma dosyanızda olarak ayarlayın.

Birden çok hesap modunu desteklemeyi planlamayabilir. Bu, paylaşılan bir cihaz kullanmıyorsanız ve Kullanıcı uygulamada aynı anda birden fazla hesapla oturum açabilir. Öyleyse, olarak ayarlayın `"account_mode"` `"SINGLE"` . Bu, uygulamanızı her zaman alır `ISingleAccountPublicClientApplication` ve msal tümleştirmenizi önemli ölçüde basitleştirir. Varsayılan değeri `"account_mode"` olduğundan `"MULTIPLE"` , mod kullanıyorsanız bu değerin yapılandırma dosyasında değiştirilmesi önemlidir `"single account"` .

Örnek uygulamanın **uygulama** > **ana** > **res** > **Ham** dizinine eklenen auth_config.jsdosyasına bir örnek aşağıda verilmiştir:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Paylaşılan cihaz modunu Algıla

Paylaşılan cihaz modu, Android cihazları birden çok çalışan tarafından paylaşılmak üzere yapılandırmanıza olanak sağlarken, Microsoft kimliği tarafından desteklenen cihaz yönetimini de sağlar. Çalışanlar cihazlarında oturum açabilir ve müşteri bilgilerine hızlıca erişebilir. Bunlar, vardiyası veya göreviyle bitdiklerinde, tek tıklamayla paylaşılan cihazdaki tüm uygulamaların oturumunu açabiliyor ve cihaz bir sonraki çalışanın kullanımına hemen hazır olacaktır.

`isSharedDevice()`Paylaşılan cihaz modundaki bir cihazda bir uygulamanın çalışıp çalışmadığını anlamak için kullanın. Uygulamanız, UX 'e göre değişiklik gerekip gerekmediğini öğrenmek için bu bayrağı kullanabilir.

İşte nasıl kullanabileceğinizi gösteren bir kod parçacığı `isSharedDevice()` .  `SingleAccountModeFragment`Örnek uygulamadaki sınıfından olur:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ? "Shared" : "Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>PublicClientApplication nesnesini başlatma

`"account_mode":"SINGLE"`Msal yapılandırma dosyasında ayarlarsanız, döndürülen uygulama nesnesini güvenli bir şekilde olarak çevirebilirsiniz `ISingleAccountPublicCLientApplication` .

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>Tek ve birden çok hesap modunu Algıla

Paylaşılan bir cihazdaki yalnızca ilk satır çalışanları için kullanılacak bir uygulama yazıyorsanız, uygulamanızı yalnızca tek hesap modunu destekleyecek şekilde yazmanızı öneririz. Bu, tıp kayıtları uygulamaları, fatura uygulamaları ve çoğu iş kolu uygulaması gibi görev odaklı birçok uygulamayı içerir. Bu, SDK 'nın birçok özelliğinin konamesi gerekmeyeceği için geliştirmeyi basitleştirir.

Uygulamanız birden çok hesabı ve paylaşılan cihaz modunu destekliyorsa, bir tür denetimi gerçekleştirmeniz ve aşağıda gösterildiği gibi uygun arabirime dönüştürmeniz gerekir.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Oturum açan kullanıcıyı al ve cihazda bir kullanıcının değişip değişmediğini belirleme

`loadAccount`Yöntemi, oturum açmış kullanıcının hesabını alır. `onAccountChanged`Yöntemi, oturum açmış kullanıcının değişip değişmediğini belirler ve gerekiyorsa temizler:

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>Bir kullanıcı için genel olarak oturum açma

Aşağıdaki, bir kullanıcının, kimlik doğrulayıcı uygulamasıyla MSAL kullanan diğer uygulamalara, cihaz genelinde oturum açtığı:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Kullanıcı oturumunu küresel olarak kapatma

Aşağıdakiler, oturum açmış olan hesabı kaldırır ve önbelleğe alınmış belirteçleri yalnızca uygulamadan değil, aynı zamanda paylaşılan cihaz modundaki cihazdan temizler:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Yönetici kılavuzu

Aşağıdaki adımlarda Azure portal uygulamanızı ayarlama ve cihazınızı paylaşılan-cihaz moduna geçirme açıklanır.

### <a name="register-your-application-in-azure-active-directory"></a>Uygulamanızı Azure Active Directory kaydetme

İlk olarak, uygulamanızı kuruluş kiracınıza kaydedin. Daha sonra uygulamanızın doğru çalışması için auth_config.jsaşağıdaki değerleri girin.

Bunun nasıl yapılacağı hakkında daha fazla bilgi için, bkz. [Uygulamanızı kaydetme](./tutorial-v2-android.md#register-your-application).

> [!NOTE]
> Uygulamanızı kaydettiğinizde lütfen sol taraftaki hızlı başlangıç kılavuzunu kullanın ve ardından **Android**' i seçin. Bu, size uygulamanız için **paket adı** ve **imza karması** sağlamak istediğiniz bir sayfaya yol açacaktır. Bunlar, uygulama yapılandırmanızın çalışmasını sağlamak için çok önemlidir. Daha sonra, uygulamanız için kullanabileceğiniz bir yapılandırma nesnesi alacaksınız ve dosyadaki auth_config.jsüzerine yapıştırabilirsiniz.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-app.png" alt-text="Azure portal hızlı başlangıç bölümünde Android uygulaması sayfanızı yapılandırma":::

**Bu değişikliği bana yap** ' ı seçmeniz ve ardından hızlı başlangıç için Azure Portal için gereken değerleri sağlamanız gerekir. Bu işlem tamamlandığında, ihtiyacınız olan tüm yapılandırma dosyalarını oluşturacağız.

:::image type="content" source="media/tutorial-v2-shared-device-mode/config-info.png" alt-text="Azure portal hızlı başlangıçta proje sayfanızı yapılandırma":::

## <a name="set-up-a-tenant"></a>Kiracı ayarlama

Sınama amacıyla kiracınızda şunları ayarlayın: en az iki çalışan, bir bulut cihaz yöneticisi ve bir genel yönetici. Azure portal, kuruluş rollerini değiştirerek bulut cihazı yöneticisini ayarlayın. Azure Portal, **Azure Active Directory**  >  **Roller ve yöneticiler**  >  **bulut Cihaz Yöneticisi**' ni seçerek kurumsal rollerinizi erişin. Paylaşılan moda bir cihaz koyabileceğiniz kullanıcıları ekleyin.

## <a name="set-up-an-android-device-in-shared-mode"></a>Paylaşılan modda bir Android cihazı ayarlama

### <a name="download-the-authenticator-app"></a>Authenticator uygulamasını indirin

Google Play mağazasından Microsoft Authenticator uygulamasını indirin. Uygulamayı zaten yüklediyseniz, en son sürüm olduğundan emin olun.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Cihaza kimlik doğrulayıcı uygulama ayarları & cihazı buluta kaydetme

Authenticator uygulamasını başlatın ve ana hesap sayfasına gidin. **Hesap Ekle** sayfasını gördüğünüzde, cihazı paylaşılan duruma getirmek için hazırsınız demektir.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-add-account.png" alt-text="Kimlik doğrulayıcı hesap ekleme ekranı":::

Sağ menü çubuğunu kullanarak **Ayarlar** bölmesine gidin. **İş & okul hesapları** altında **cihaz kaydı** ' nı seçin.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-settings.png" alt-text="Doğrulayıcı ayarları ekranı":::

Bu düğmeye tıkladığınızda, cihaz kişilerine erişim yetkisi vermeniz istenir. Bunun nedeni, Android 'in cihazdaki hesap Tümleştirmesidir. **İzin ver**' i seçin.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png" alt-text="Doğrulayıcı erişim onayı ekranına izin ver":::

Bulut Cihaz Yöneticisi, kurumsal e-postalarını, **paylaşılan bir cihaz olarak girin veya kaydeder**. Ardından **paylaşılan cihaz olarak kaydet** düğmesine tıklayın ve kimlik bilgilerini girin.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-device.png" alt-text="Uygulamadaki cihaz kayıt ekranı":::

:::image type="content" source="media/tutorial-v2-shared-device-mode/sign-in.png" alt-text="Microsoft oturum açma sayfasını gösteren uygulama ekran görüntüsü":::

Cihaz artık paylaşılan modda.

:::image type="content" source="media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png" alt-text="Paylaşılan cihaz modunu etkin gösteren uygulama ekranı":::

 Cihazdaki tüm oturum açma işlemleri ve oturum açma işlemleri genel olacaktır, yani MSAL ve cihazdaki Microsoft Authenticator tümleştirilmiş tüm uygulamalar için geçerlidir. Artık, paylaşılan cihaz modu özelliklerini kullanan cihaza uygulamalar dağıtabilirsiniz.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Azure portal paylaşılan cihazı görüntüleme

Paylaşılan moda bir cihaz yerleştirdikten sonra, kuruluşunuz tarafından bilinir ve kuruluş kiracınızda izlenir. Paylaşılan cihazlarınızı, Azure portal Azure Active Directory dikey penceresinde bulunan **JOIN türüne** bakarak görüntüleyebilirsiniz.

:::image type="content" source="media/tutorial-v2-shared-device-mode/registered-device-screen.png" alt-text="Azure portal gösterilen tüm cihazlar bölmesi":::

## <a name="running-the-sample-app"></a>Örnek uygulamayı çalıştırma

Örnek uygulama, kuruluşunuzun Graph API çağıramayacak basit bir uygulamadır. İlk çalıştırmada, uygulama çalışan hesabınıza yeni olduğu için onay istenir.

:::image type="content" source="media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png" alt-text="Uygulama yapılandırma bilgisi ekranı":::

## <a name="next-steps"></a>Sonraki adımlar

Android cihazlarda Microsoft kimlik doğrulama kitaplığı ve paylaşılan cihaz moduyla çalışma hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Android cihazlar için paylaşılan cihaz modu](msal-android-shared-devices.md)
