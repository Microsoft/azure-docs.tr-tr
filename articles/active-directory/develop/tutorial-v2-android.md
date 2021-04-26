---
title: 'Öğretici: kimlik doğrulaması için Microsoft Identity platformunu kullanan bir Android uygulaması oluşturma | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu öğreticide, kullanıcıların oturum açması için Microsoft Identity platformunu kullanan bir Android uygulaması oluşturacaksınız ve Microsoft Graph API 'sini adına çağırmak için bir erişim belirteci alacaksınız.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 7d297d96ba764c812a3d4db6d9383122c73cfe31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103150"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-application"></a>Öğretici: kullanıcılarda oturum açın ve Android uygulamasından Microsoft Graph API 'sini çağırın

Bu öğreticide, kullanıcıları oturum açmak ve Microsoft Graph API 'sini çağırmak için bir erişim belirteci almak amacıyla Microsoft Identity platformu ile tümleştirilen bir Android uygulaması oluşturacaksınız.

Bu öğreticiyi tamamladığınızda, uygulamanız kişisel Microsoft hesaplarının (outlook.com, live.com ve diğerleri dahil) oturum açma işlemlerinin yanı sıra Azure Active Directory kullanan herhangi bir şirketten veya kuruluştan iş veya okul hesabı kabul eder.

Bu öğreticide: 

> [!div class="checklist"]
> * *Android Studio* bir Android uygulaması projesi oluşturma
> * Uygulamayı Azure portal kaydetme
> * Kullanıcı oturum açma ve oturum kapatma desteği için kod ekleme
> * Microsoft Graph API 'sini çağırmak için kod ekleme
> * Uygulamayı test etme

## <a name="prerequisites"></a>Önkoşullar

* Android Studio 3.5 +

## <a name="how-this-tutorial-works"></a>Bu öğreticinin nasıl çalıştığı

![Bu öğretici tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Bu öğreticideki uygulama, kullanıcıların oturum açmasını ve adına veri almasını sağlayacaktır. Bu verilere, yetkilendirme gerektiren ve Microsoft Identity platform tarafından korunan korumalı bir API (Microsoft Graph API) üzerinden erişilir.

Daha ayrıntılı belirtmek gerekirse:

* Uygulamanız kullanıcı tarafından bir tarayıcı veya Microsoft Authenticator ile Intune Şirket Portalı oturum açacaktır.
* Son Kullanıcı, uygulamanızın istediği izinleri kabul eder.
* Uygulamanıza Microsoft Graph API 'SI için bir erişim belirteci verilmeyecektir.
* Erişim belirteci, Web API 'sine HTTP isteğine dahil edilir.
* Microsoft Graph yanıtını işleyin.

Bu örnek, kimlik doğrulamasını uygulamak için Android için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanır: [com. Microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

MSAL, belirteçleri otomatik olarak yenileyecek, cihazdaki diğer uygulamalar arasında çoklu oturum açma (SSO) sunacaktır ve hesapları yönetir.

Bu öğreticide, Android için MSAL ile çalışmaya yönelik Basitleştirilmiş örnekler gösterilmektedir. Kolaylık olması için yalnızca tek hesap modunu kullanır. Daha karmaşık senaryoları araştırmak için GitHub 'da tamamlanan [çalışma kodu örneğine](https://github.com/Azure-Samples/ms-identity-android-java/) bakın.

## <a name="create-a-project"></a>Proje oluşturma
Henüz bir Android uygulamanız yoksa, yeni bir proje ayarlamak için aşağıdaki adımları izleyin.

1. Android Studio açın ve **Yeni bir Android Studio projesi Başlat**' ı seçin.
2. **Temel etkinlik** ' i seçin ve **İleri ' yi** seçin.
3. Uygulamanızı adlandırın.
4. Paket adını kaydedin. Daha sonra Azure portal buraya girersiniz.
5. **Kotlin** dilini **Java** ile değiştirin.
6. **En düşük API düzeyini** **api 19** veya üzeri olarak ayarlayın ve **son**' a tıklayın.
7. Proje görünümünde, açılan listeden **Proje** ' yi seçerek kaynak ve kaynak olmayan proje dosyalarını görüntüleyin, **App/Build. Gradle** dosyasını açın ve `targetSdkVersion` olarak ayarlayın `28` .

## <a name="integrate-with-the-microsoft-authentication-library"></a>Microsoft kimlik doğrulama kitaplığıyla tümleştirin

### <a name="register-your-application"></a>Uygulamanızı kaydetme

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
1. Uygulamanız için bir **ad** girin. Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
1. **Kaydet**’i seçin.
1. **Yönet** altında **kimlik doğrulama**  >  **Platform**  >  **Android** ekleme ' yi seçin.
1. Projenizin paket adını girin. Kodu indirdiyseniz, bu değer olur `com.azuresamples.msalandroidapp` .
1. **Android uygulamanızı yapılandırma** sayfanızın **imza karması** bölümünde, **bir geliştirme imza karması oluşturma** ' yı seçin. ve platformunuz için kullanmak üzere KeyTool komutunu kopyalayın.


     KeyTool.exe, Java Development Kit 'in (JDK) bir parçası olarak yüklenir. Ayrıca, KeyTool komutunu yürütmek için OpenSSL aracını da yüklemelisiniz. Daha fazla bilgi için [bir anahtar oluşturma hakkındaki Android belgelerine](https://developer.android.com/studio/publish/app-signing#generate-key) bakın.

1. KeyTool tarafından oluşturulan **imza karmasını** girin.
1. Uygulamanızı daha sonra yapılandırırken girebilmeniz için **Android yapılandırma** sayfasında görünen **msal yapılandırmasını** **Yapılandır** ve Kaydet ' i seçin.  
1. **Bitti** seçeneğini belirleyin.

### <a name="configure-your-application"></a>Uygulamanızı yapılandırma

1. Android Studio projesi bölmesinde **app\src\mainres dizinine** gidin.
1. **Kay** ' a sağ tıklayın ve **Yeni**  >  **Dizin**' i seçin. `raw`Yeni dizin adı olarak girin ve **Tamam**' a tıklayın.
1. **App**  >  **src**  >  **ana**  >  **res**  >  **RAW** bölümünde, adlı yeni bir JSON dosyası oluşturun `auth_config_single_account.json` ve daha önce kaydettiğiniz msal yapılandırmasını yapıştırın.

    Yeniden yönlendirme URI 'sinin altında şunu yapıştırın:
    ```json
      "account_mode" : "SINGLE",
    ```
    Yapılandırma dosyanız Şu örneğe benzemelidir:
    ```json
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "broker_redirect_uri_registered" : true,
      "account_mode" : "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
   ```

     Bu öğretici yalnızca bir uygulamanın tek hesap modunda nasıl yapılandırılacağını gösterir. [Tek ve birden çok hesap modu](./single-multi-account.md) ve [uygulamanızı yapılandırma](./msal-configuration.md) hakkında daha fazla bilgi için belgeleri görüntüleyin

4. **App**  >  **src**  >  **ana**  >  **AndroidManifest.xml**, `BrowserTabActivity` Aşağıdaki etkinliği uygulama gövdesine ekleyin. Bu giriş, Microsoft 'un, kimlik doğrulamasını tamamladıktan sonra uygulamanıza geri çağırmasını sağlar:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
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

    Değer için Azure portal kaydettiğiniz paket adını değiştirin `android:host=` .
    Değer için Azure portal kaydettiğiniz anahtar karmasını değiştirin `android:path=` . Imza karması URL kodlamalı **olmamalıdır.** Imza Karmalarınızın başlangıcında önde bir değer bulunduğundan emin olun `/` .
    
    Değerin yerini alacak olan "paket adı" `android:host` şuna benzemelidir: "com. azuresamples. msalandroıdapp".
    Değerini ile değiştirecek "Imza karması" şuna benzemelidir `android:path` : "/1wIqXSqBj7w + h11ZifsnqwgyKrY =".
    
    Ayrıca, bu değerleri uygulama kaydlarınızın kimlik doğrulama dikey penceresinde bulabilirsiniz. Yeniden yönlendirme URI 'nizin şuna benzer olacağını unutmayın: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Imza karması bu değerin sonunda URL 'YI kodlarken, Imza karması, değer içinde URL **kodlamalı olmamalıdır** `android:path` .

## <a name="use-msal"></a>MSAL kullanma

### <a name="add-msal-to-your-project"></a>Projenize MSAL ekleyin

1. Android Studio projesi penceresinde, **App**  >  **src**  >  **Build. Gradle** öğesine gidin ve aşağıdakileri ekleyin:

    ```gradle
    repositories{
        jcenter()
    }
    dependencies{
        implementation 'com.microsoft.identity.client:msal:2.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version")
    }
    ```
    [Microsoft Graph SDK üzerinde daha fazla bilgi](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Gerekli İçeri Aktarmalar

Aşağıdakini **App**  >  **src** Main Java com ' un en üstüne ekleyin  >   >    >  **. örnek (yourapp)**  >  **MainActivity. Java**

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>PublicClientApplication örneği oluştur
#### <a name="initialize-variables"></a>Değişkenleri Başlat
```java
private final static String[] SCOPES = {"Files.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
Sınıfının içinde, `MainActivity` kullanarak msal örneğini oluşturmak için aşağıdaki onCreate () yöntemine bakın `SingleAccountPublicClientApplication` .

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount

```java
//When app comes to the foreground, load existing account to determine if user is signed in
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
}
```

### <a name="initializeui"></a>InitializeUI
Düğmeleri dinleyin ve yöntemleri arayın veya hataları buna göre günlüğe kaydedin.
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);

        //Sign in user
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });

        //Interactive
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> MSAL ile oturum açmak, bir kullanıcıyla ilgili tüm bilinen bilgileri uygulamadan kaldırır, ancak kullanıcının cihazında etkin bir oturumu olmaya devam edecektir. Kullanıcı yeniden oturum açmayı denerse, oturum açma kullanıcı arabirimini görebilir, ancak cihaz oturumu hala etkin olduğundan kimlik bilgilerini yeniden girmeniz gerekebilir.

### <a name="getauthinteractivecallback"></a>Getauthınteractivecallback
Etkileşimli istekler için kullanılan geri çağırma.

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Sessiz istekler için kullanılan geri çağırma
```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Microsoft Graph API'si çağırma

Aşağıdaki kod, Graph SDK kullanarak GraphAPI 'nin nasıl çağrılacağını gösterir.

### <a name="callgraphapi"></a>callGraphAPI

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>UI Ekle
### <a name="activity"></a>Etkinlik
Bu öğreticide Kullanıcı arabiriminizi modellemek istiyorsanız aşağıdaki yöntemler, metin güncelleştirme ve düğmelere dinleme için bir kılavuz sağlar.

#### <a name="updateui"></a>UpdateUI
Oturum açma durumuna göre düğmeleri etkinleştirin/devre dışı bırakın ve metin ayarlayın.
```java
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Oturumu kapatma yansıtmak için Kullanıcı arabiriminde metin güncelleştirme yöntemi.

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Layout

`activity_main.xml`Düğmeleri ve metin kutularını görüntüleyen örnek dosya.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Uygulamanızı test etme

### <a name="run-locally"></a>Yerel olarak çalıştırma

Uygulamayı derleyin ve bir test cihazında veya öykünücüsünde dağıtın. Azure AD veya kişisel Microsoft hesapları için oturum açabiliyor ve belirteçleri alabilmesi gerekir.

Oturum açtıktan sonra, uygulama Microsoft Graph uç noktasından döndürülen verileri görüntüler `/me` .
PR 4
### <a name="consent"></a>Onay

Herhangi bir Kullanıcı uygulamanızda ilk kez oturum açtığında, bu kullanıcılara istenen izinleri onaylaması için Microsoft kimliği sorulur. Bazı Azure AD kiracılar, yöneticilerin tüm kullanıcılar adına onay vermesini gerektiren Kullanıcı onayını devre dışı bıraktı. Bu senaryoyu desteklemek için kendi kiracınızı oluşturmanız ya da yönetici onayı almanız gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Uygulamanızı kaydetme](#register-your-application) adımındaki oluşturduğunuz uygulama nesnesini silin.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Çok bölgeli senaryo serimizde korumalı Web API 'Lerini çağıran mobil uygulamalar oluşturma hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Senaryo: Web API 'Lerini çağıran mobil uygulama](scenario-mobile-overview.md)
