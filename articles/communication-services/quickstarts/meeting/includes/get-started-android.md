---
title: Hızlı başlangıç-Azure Iletişim hizmetlerini kullanarak bir Android uygulamasına takımlar toplantısı ekleme
description: Bu hızlı başlangıçta, Android için Azure Communication Services ekipleri ekleme kitaplığı 'nı nasıl kullanacağınızı öğreneceksiniz.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5ac4c53550468d33e9ed533303749d29e772d766
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108493"
---
Bu hızlı başlangıçta, Android için Azure Communication Services ekipleri ekleme kitaplığı 'nı kullanarak bir takımlar toplantısına nasıl katılacağınızı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Android uygulamanızı oluşturmak için [Android Studio](https://developer.android.com/studio).
- Dağıtılan bir Iletişim Hizmetleri kaynağı. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- Azure Iletişim hizmetiniz için bir [Kullanıcı erişim belirteci](../../access-tokens.md) .

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-an-android-app-with-an-empty-activity"></a>Boş bir etkinliğe sahip bir Android uygulaması oluşturma

Android Studio yeni bir Android Studio projesi Başlat ' ı seçin.

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Android Studio ' de seçili ' yeni Android Studio projesi Başlat ' düğmesini gösteren ekran görüntüsü.":::

"Telefon ve Tablet" altında "boş etkinlik" proje şablonunu seçin.

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="Proje şablonu ekranında ' boş etkinlik ' seçeneğinin seçili olduğunu gösteren ekran görüntüsü.":::

Projeyi adlandırın `TeamsEmbedAndroidGettingStarted` , dili Java olarak ayarlayın ve en düşük SDK "API 21: Android 5,0 (Lollipop)" veya daha fazlasını seçin.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Proje şablonu ekranı 2 ' de seçili ' boş etkinlik ' seçeneğinin gösterildiği ekran görüntüsü.":::


### <a name="install-the-azure-package"></a>Azure paketini yükler

Uygulama düzeyi derlemeniz. Gradle, bağımlılıklar ve Android bölümlerine aşağıdaki satırları ekleyin

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
}
```

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
    ...
}
```

### <a name="install-the-teams-embed-package"></a>Takımlar ekleme paketini yükler

Paketi indirin `MicrosoftTeamsSDK` .

Ardından, MicrosoftTeamsSDK klasörünü Projeler uygulama klasörünüze ayıklayın. Örn. `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### <a name="add-teams-embed-package-to-your-buildgradle"></a>Derlemenize takımlar ekleme paketi ekleyin. Gradle

Uygulama düzeyinizdeki `build.gradle` dosyanın sonuna aşağıdaki satırı ekleyin:

```groovy
apply from: 'MicrosoftTeamsSDK/MicrosoftTeamsSDK.gradle'
```

Projeyi Gradle dosyalarıyla Eşitle.

### <a name="create-application-class"></a>Uygulama sınıfı oluştur

Adlı yeni Java sınıfı dosyasını oluşturun `TeamsEmbedAndroidGettingStarted` . Bu, genişletilmesi gereken uygulama sınıfı olacaktır `TeamsSDKApplication` . [Android belgeleri](https://developer.android.com/reference/android/app/Application)

:::image type="content" source="../media/android/application-class-location.png" alt-text="Android Studio içinde uygulama sınıfının nerede oluşturulacağını gösteren ekran görüntüsü":::

```java
package com.microsoft.teamsembedandroidgettingstarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### <a name="update-themes"></a>Temaları Güncelleştir

Stil adını hem hem de `AppTheme` dosyalarınızda olarak ayarlayın `theme.xml` `theme.xml (night)` .

:::image type="content" source="../media/android/theme-settings.png" alt-text="Android Studio theme.xml dosyaları gösteren ekran görüntüsü":::

```xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
```

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
```

### <a name="add-permissions-to-application-manifest"></a>Uygulama bildirimine izinler ekleme

`RECORD_AUDIO`Uygulama bildiriminiz () iznini ekleyin `app/src/main/AndroidManifest.xml` :  


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
```

### <a name="add-app-name-and-theme-to-application-manifest"></a>Uygulama bildirimine uygulama adı ve tema ekleme

Bildirime ' xmlns: Tools = " http://schemas.android.com/tools " ekleyin.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
```

' E ekleyin, öğesine ekleyin `.TeamsEmbedAndroidGettingStarted` `android:name` `android:name` `tools:replace` ve `android:theme` öğesine değiştirin `@style/AppTheme`

```xml
<application
    android:name=".TeamsEmbedAndroidGettingStarted"
    tools:replace="android:name"
    android:theme="@style/AppTheme"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true">
```

### <a name="set-up-the-layout-for-the-app"></a>Uygulamanın yerleşimini ayarlama

KIMLIĞI olan bir düğme oluşturun `join_meeting` . () Öğesine gidin `app/src/main/res/layout/activity_main.xml` ve dosyanın içeriğini aşağıdakiler ile değiştirin:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Ana Etkinlik yapı iskelesi ve bağlamaları oluşturma

Düzen oluşturulduktan sonra, etkinliğin gerekli bağlamalarla birlikte temel yapı iskelesi eklenebilir. Etkinlik, çalışma zamanı izinleri istemeyi, Toplantı istemcisini oluşturmayı ve düğmeye basıldığında bir toplantıya katılmayı işleymeyecektir. Her biri kendi bölümünde ele alınacaktır. 

`onCreate`Yöntemi çağırmak için geçersiz kılınır `getAllPermissions` ve `createAgent` düğme için bağlamaları ekler `Join Meeting` . Bu, etkinlik oluşturulduğunda yalnızca bir kez gerçekleşir. Hakkında daha fazla bilgi için `onCreate` , [etkinlik yaşam döngüsünü anlama](https://developer.android.com/guide/components/activities/activity-lifecycle)kılavuzuna bakın.

**MainActivity. Java** ' ya gidin ve içeriği şu kodla değiştirin:

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;

import java.util.ArrayList;
import java.util.concurrent.Callable;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    private MeetingUIClient meetingUIClient;
    private MeetingJoinOptions meetingJoinOptions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        meetingJoinOptions = new MeetingJoinOptions(displayName);
        
        getAllPermissions();
        createMeetingClient();

        Button joinMeeting = findViewById(R.id.join_meeting);
        joinMeeting.setOnClickListener(l -> joinMeeting());
    }

    private void createMeetingClient() {
        // See section on creating meeting client
    }

    private void joinMeeting() {
        // See section on joining a meeting
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### <a name="request-permissions-at-runtime"></a>Çalışma zamanında izin iste

Android 6,0 ve üzeri (API düzeyi 23) ve `targetSdkVersion` 23 veya üzeri için, uygulama yüklendiği zaman yerine çalışma zamanında izin verilir. Bunu desteklemek için, `getAllPermissions` `ActivityCompat.checkSelfPermission` `ActivityCompat.requestPermissions` gerekli her izin için ve çağırmak üzere uygulanabilir.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> Uygulamanızı tasarlarken, bu izinlerin ne zaman istendiğine göz önünde bulundurun. İzinler, gerekli oldukları sırada değil, istenerek istenir. Daha fazla bilgi için bkz [. Android Izinleri Kılavuzu.](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, Azure Iletişim Hizmetleri ekiplerinin ekleme kitaplığının bazı önemli özelliklerinden bazılarını işler:

| Ad                                  | Açıklama                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Meeting ınguiclient| Meeting ınguiclient, takımlar ekleme kitaplığına ana giriş noktasıdır. |
| Meeting ınjoinoptions | Meeting ınjoinoptions, görünen ad gibi yapılandırılabilir seçenekler için kullanılır. |
| Çağrı durumu | Çağrı durumu değişikliklerini raporlamak için CallState kullanılır. Seçenekler şunlardır: `connecting` , `waitingInLobby` , `connected` ve `ended` . |

## <a name="create-a-meetingclient-from-the-user-access-token"></a>Kullanıcı erişim belirtecinden bir Meeting Inclient oluşturma

Kimliği doğrulanmış bir toplantı istemcisi, bir Kullanıcı erişim belirteci ile oluşturulabilir. Bu belirteç genellikle uygulamaya özgü kimlik doğrulaması olan bir hizmet tarafından oluşturulur. Kullanıcı erişim belirteçleri hakkında daha fazla bilgi edinmek için [Kullanıcı erişim belirteçleri](../../access-tokens.md) Kılavuzu ' na bakın. Hızlı başlangıç için, `<USER_ACCESS_TOKEN>` Azure Iletişim hizmeti kaynağınız için oluşturulmuş bir Kullanıcı erişim belirteci ile değiştirin.

```java
private void createMeetingClient() {
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        meetingUIClient = new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="setup-token-refreshing"></a>Kurulum belirtecini yenileme

Çağrılabilir bir `tokenRefresher` Yöntem oluşturun. Ardından `fetchToken` kullanıcı belirtecini almak için bir yöntem oluşturun. [Burada nasıl yapılacağı hakkında yönergeler bulabilirsiniz](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java)

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return USER_ACCESS_TOKEN;
}
```

## <a name="get-the-teams-meeting-link"></a>Takımlar toplantı bağlantısını alın

Takımlar toplantı bağlantısı, Graph API 'Leri kullanılarak alınabilir. Bu, [Graph belgelerinde](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)ayrıntılıdır.
SDK 'Yı çağıran Iletişim Hizmetleri, bir tam takımlar toplantısı bağlantısını kabul eder. Bu bağlantı, kaynağın bir parçası olarak döndürülür `onlineMeeting` , [ `joinWebUrl` özelliğin](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) altında erişilebilir, toplantı davetini davet eden ekipteki **Toplantı** URL 'sinden gerekli toplantı bilgilerini de alabilirsiniz.

## <a name="start-a-meeting-using-the-meeting-client"></a>Toplantı istemcisini kullanarak bir toplantı başlatma

Bir toplantıya katılma, ile yapılabilir `MeetingClient` ve yalnızca bir `meetingURL` ve gerektirir `JoinOptions` . `<MEETING_URL>`Bir ekiple toplantı URL 'si ile değiştirin.

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    try {
        meetingUIClient.join("<MEETING_URL>", meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="launch-the-app-and-join-a-meeting"></a>Uygulamayı başlatın ve bir toplantıya katılırsanız

Uygulama artık araç çubuğunda "uygulama Çalıştır" düğmesi kullanılarak başlatılabilir (SHIFT + F10). 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="Tamamlanmış uygulamayı gösteren ekran görüntüsü.":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="Toplantı birleştirildikten sonra tamamlanmış uygulamayı gösteren ekran görüntüsü.":::

## <a name="sample-code"></a>Örnek Kod

Örnek uygulamayı [GitHub](https://github.com/Azure-Samples/teams-embed-android-getting-started) 'dan indirebilirsiniz
