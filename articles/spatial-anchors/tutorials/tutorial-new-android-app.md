---
title: Öğretici-Azure uzamsal bağlayıcılarını kullanarak yeni bir Android uygulaması oluşturmaya yönelik adım adım yönergeler | Microsoft Docs
description: Bu öğreticide, Azure uzamsal bağlayıcılarını kullanarak yeni bir Android uygulaması oluşturmayı öğreneceksiniz.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 6386ef775f897ca56d2660adf72a885672c8dfd2
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092054"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Öğretici: Azure uzamsal bağlayıcıları kullanarak yeni bir Android uygulaması oluşturmaya yönelik adım adım yönergeler

Bu öğreticide, Azure uzamsal bağlayıcılarla en yeni işlevleri tümleştiren yeni bir Android uygulamasının nasıl oluşturulacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için şunlar sahip olduğunuzdan emin olun:

- <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>Içeren bir Windows veya MacOS makinesi.
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">Geliştirici etkin</a> ve <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">korumalı</a> bir Android cihaz.

## <a name="getting-started"></a>Başlarken

Android Studio'yu başlatın. **Android Studio hoş geldiniz** penceresinde **Yeni bir Android Studio projesi Başlat ' a**tıklayın. Ya da zaten açık bir projeniz varsa **dosya**->**Yeni proje**' yi seçin.

**Yeni proje oluştur** penceresinde, **telefon ve tablet** bölümünde **boş etkinlik**' i seçin ve **İleri**' ye tıklayın. Ardından, **En düşük API düzeyi**altında `API 26: Android 8.0 (Oreo)`' yi seçin ve **dilin** `Java`olarak ayarlandığından emin olun. Proje adını & konumunu ve paket adını değiştirmek isteyebilirsiniz. Diğer seçenekleri oldukları gibi bırakın. **Son**'a tıklayın. **Bileşen yükleyicisi** çalışmaya çalışacaktır. İşiniz bittiğinde **son**' a tıklayın. Bazı işlemeden sonra, Android Studio IDE 'yi açar.

## <a name="trying-it-out"></a>Deneniyor

Yeni uygulamanızı test etmek için, geliştirici özellikli cihazınızı bir USB kablosuyla geliştirme makinenize bağlayın. **Çalıştır** ' a tıklayın-> **' App ' Çalıştır**. **Dağıtım hedefi seç** penceresinde cihazınızı seçin ve **Tamam**' ı tıklatın. Android Studio uygulamayı bağlı cihazınıza yükleyip başlatır. Şimdi "Merhaba Dünya!" görmeniz gerekir cihazınızda çalışan uygulamada gösterilir. **Çalıştır**-> **' uygulama ' ı Durdur**' a tıklayın.

## <a name="integrating-_arcore_"></a>_Arcore_ tümleştirme

<a href="https://developers.google.com/ar/discover/" target="_blank">_Arcore_</a> , genişletmiş gerçeklik deneyimlerini oluşturmaya yönelik Google 'ın platformu olduğundan, cihazın taşırken kendi konumunu izlemesini sağlar ve gerçek dünyanın kendisini öğrendiğini oluşturur.

`app\manifests\AndroidManifest.xml`, kök `<manifest>` düğümüne aşağıdaki girişleri eklemek için değiştirin. Bu kod parçacığı birkaç şeyi yapar:

- Bu, uygulamanızın cihaz kameranıza erişmesine izin verir.
- Ayrıca, uygulamanızın yalnızca ARCore destekleyen cihazlara Google Play Store göründüğünden emin olur.
- Zaten yüklü değilse, uygulamanız yüklendiğinde, daha önce yüklü değilse, Google Play Store.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

`Gradle Scripts\build.gradle (Module: app)` aşağıdaki girişi içerecek şekilde değiştirin. Bu kod, uygulamanızın ARCore sürüm 1,8 ' i hedeflediğinden emin olur. Bu değişiklikten sonra, Gradle adresinden eşitleme isteyip istemediğinizi soran bir bildirim alabilirsiniz: **Şimdi Eşitle**' ye tıklayın.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>_Manzara formunu_ tümleştirme

<a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Manzara_</a> , bir OpenGL öğrenmeden, genişletilmiş gerçeklik uygulamalarında gerçekçi 3B sahneler oluşturmayı basit hale getirir.

`Gradle Scripts\build.gradle (Module: app)` aşağıdaki girişleri içerecek şekilde değiştirin. Bu kod, uygulamanızın `Sceneform` gereken Java 8 ' den dil yapılarını kullanmasına olanak sağlar. Ayrıca, uygulamanızın kullandığı ARCore sürümüyle eşleşmesi gerektiğinden, uygulamanızın 1,8 sürümünü `Sceneform` hedeflediğini de güvence altına alırsınız. Bu değişiklikten sonra, Gradle adresinden eşitleme isteyip istemediğinizi soran bir bildirim alabilirsiniz: **Şimdi Eşitle**' ye tıklayın.

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.11.0'
    ...
}
```

`app\res\layout\activity_main.xml`açın ve var olan Hello Wolrd `<TextView>` öğesini aşağıdaki ArFragment ile değiştirin. Bu kod, kamera beslemenin ekranda görüntülenmesine neden olur, çünkü taşırken cihaz konumunuzu izlemek için ARCore 'u etkinleştirir.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

Uygulamanızı daha sonra doğrulamak için cihazınıza yeniden [dağıtın](#trying-it-out) . Bu kez, kamera izinlerine sorulur. Onaylandığında, kameranızın akış işlemesini ekranda görmeniz gerekir.

## <a name="place-an-object-in-the-real-world"></a>Gerçek dünyaya bir nesne yerleştirme

Uygulamanızı kullanarak bir nesne oluşturalım &. İlk olarak, aşağıdaki içeri aktarmaları `app\java\<PackageName>\MainActivity`ekleyin:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Ardından, aşağıdaki üye değişkenlerini `MainActivity` sınıfa ekleyin:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Sonra, `app\java\<PackageName>\MainActivity` `onCreate()` yöntemine aşağıdaki kodu ekleyin. Bu kod, Kullanıcı cihazınızda ekrana dokunduğunda tespit edilecek `handleTap()`adlı bir dinleyiciyi yedekedecektir. Tap 'ın izleme tarafından zaten tanınmış olan gerçek bir dünya yüzeyinde olması durumunda dinleyici çalışır.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Son olarak, her şeyi bir araya bağlayan aşağıdaki `handleTap()` yöntemi ekleyin. Bir Sphere oluşturacak ve bunu, dokunduğunuz konuma yerleştirmeyecektir. `this.recommendedSessionProgress` şimdi sıfır olarak ayarlandığından Sphere başlangıçta siyah olur. Bu değer, daha sonra ayarlanacak.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

Uygulamanızı daha sonra doğrulamak için cihazınıza yeniden [dağıtın](#trying-it-out) . Bu kez, ortamınızı tanımayı başlatmaya başlayabilmeniz için cihazınızın etrafında gezinebilirsiniz. Daha sonra ekrana dokunarak siyah küetinizi istediğiniz yüzey üzerine yerleştirin & oluşturun.

## <a name="attach-a-local-azure-spatial-anchor"></a>Yerel bir Azure uzamsal Bağlayıcısı ekleyin

`Gradle Scripts\build.gradle (Module: app)` aşağıdaki girişi içerecek şekilde değiştirin. Bu kod, uygulamanızın Azure uzamsal bağlantıları 1.3.0 sürümünü hedeflediğinden emin olur. Bu şekilde, Azure uzamsal Çıpaların son sürümüne başvurmak için çalışmanız gerekir.

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[1.3.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[1.3.0]"
    ...
}
```

`app\java\<PackageName>`->**yeni**->**Java sınıfı**' na sağ tıklayın. **Adı** _MyFirstApp_ve **üst sınıftan** _Android. app. Application_olarak ayarlayın. Diğer seçenekleri oldukları gibi bırakın. **OK (Tamam)** düğmesine tıklayın. `MyFirstApp.java` adlı bir dosya oluşturulacaktır. Aşağıdaki içeri aktarmayı ekleyin:

```java
import com.microsoft.CloudServices;
```

Ardından, yeni `MyFirstApp` sınıfına aşağıdaki kodu ekleyin. Bu, Azure uzamsal bağlayıcıların uygulamanızın bağlamıyla başlatıldığından emin olur.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Şimdi `app\manifests\AndroidManifest.xml`, kök `<application>` düğümüne aşağıdaki girişi içerecek şekilde değiştirin. Bu kod, uygulamanızda oluşturduğunuz uygulama sınıfını yedekler.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

`app\java\<PackageName>\MainActivity`geri dönüp içine aşağıdaki içeri aktarmaları ekleyin:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Ardından, aşağıdaki üye değişkenlerini `MainActivity` sınıfa ekleyin:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Sonra, aşağıdaki `initializeSession()` yöntemini `mainActivity` sınıfınız içine ekleyelim. Bir kez çağrıldıktan sonra, uygulamanızın başlatılması sırasında bir Azure uzamsal bağlayıcı oturumunun oluşturulduğundan ve düzgün başlatılmış olduğundan emin olur.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Şimdi `initializeSession()` yönteminizi `onCreate()` yöntemine geçidelim. Ayrıca, kamera akışınızdan gelen çerçevelerin işlenmek üzere Azure uzamsal bağlayıcı SDK 'sına gönderilmesini de sağlamaktır.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Son olarak, `handleTap()` yöntemine aşağıdaki kodu ekleyin. Gerçek dünyaya yerleştirdiğimiz siyah sphere öğesine yerel bir Azure uzamsal Bağlayıcısı ekler.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

Uygulamanızı bir kez daha yeniden [dağıtın](#trying-it-out) . Cihazınızın etrafında ilerleyin, ekrana dokunun ve bir siyah küre yerleştirin. Bu süre, ancak kodunuz, kürenin yerel bir Azure uzamsal bağlayıcısını oluşturur ve iliştirir.

Devam etmeden önce, henüz yoksa bir Azure uzamsal bağlayıcı hesap tanımlayıcısı ve anahtarı oluşturmanız gerekir. Elde etmek için aşağıdaki bölümü izleyin.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Yerel çıpası buluta yükleyin

Azure uzamsal bağlayıcılarınızın hesap tanımlayıcısı ve anahtarınız olduktan sonra, `app\java\<PackageName>\MainActivity`geri dönüp aşağıdaki içeri aktarmaları buna ekleyebilirsiniz:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Ardından, aşağıdaki üye değişkenlerini `MainActivity` sınıfa ekleyin:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Şimdi, `initializeSession()` yöntemine aşağıdaki kodu ekleyin. İlk olarak, bu kod uygulamanızın, kamera akışınızdan çerçeveler topladığı için Azure uzamsal bağlayıcı SDK 'sının yaptığı ilerlemeyi izlemesine izin verir. Bu şekilde, Sphere 'nizin rengi orijinal siyahtan gri olarak değiştirmeye başlayacaktır. Daha sonra, bağlayıcıyı buluta göndermek için yeterli sayıda çerçeve toplandıktan sonra bu işlem beyaz olur. İkincisi, bu kod, bulut arka ucu ile iletişim kurmak için gereken kimlik bilgilerini sağlar. Burada, uygulamanızı hesap tanımlayıcıyı ve anahtarınızı kullanacak şekilde yapılandıracaksınız. [Uzamsal Tutturucuların kaynağını ayarlarken](#create-a-spatial-anchors-resource)bunları bir metin düzenleyicisine kopyaladınız.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Sonra, aşağıdaki `uploadCloudAnchorAsync()` yöntemini `mainActivity` sınıfınızın içine ekleyin. Bu yöntem çağrıldıktan sonra zaman uyumsuz olarak cihazınızdan yeterli sayıda çerçeve toplanana kadar bekler. Yani, kürenin rengini sarıya dönüştürür ve ardından yerel Azure uzamsal bağlantı yayınınızı buluta yüklemeye başlar. Karşıya yükleme tamamlandıktan sonra kod bir bağlantı tanımlayıcısı döndürür.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Son olarak her şeyi bir araya alalım. `handleTap()` yönteminde aşağıdaki kodu ekleyin. Sphere oluşturulduğu anda `uploadCloudAnchorAsync()` yönteminizi çağırır. Yöntem döndüğünde, aşağıdaki kod Sphere ' de bir son güncelleştirme gerçekleştirir ve rengini mavi olarak değiştirir.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

Uygulamanızı bir kez daha yeniden [dağıtın](#trying-it-out) . Cihazınızın etrafında ilerleyin, ekrana dokunun ve Sphere 'nizi yerleştirin. Ancak, bu kez, fotoğraf makineniz, kamera kareleri toplandığı sırada rengi siyahtan beyaz olarak değiştirecek. Yeterli kare olduktan sonra Sphere, sarı olarak açılır ve bulut yüklemesi başlar. Karşıya yükleme tamamlandıktan sonra Sphere 'niz mavi olarak açılır. İsteğe bağlı olarak, uygulamanızın gönderdiği günlük iletilerini izlemek için Android Studio içindeki `Logcat` penceresini de kullanabilirsiniz. Örneğin, çerçeve yakalamaları sırasında oturum ilerlemesi ve karşıya yükleme tamamlandıktan sonra bulutun döndürdüğü bağlayıcı tanımlayıcısı.

## <a name="locate-your-cloud-spatial-anchor"></a>Bulut uzamsal çıpası bulun

Bir bağlantı, buluta yüklendi, yeniden bulmaya başlamaya hazırız. İlk olarak, aşağıdaki içeri aktarmaları kodunuza ekleyelim.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Sonra, `handleTap()` yöntemine aşağıdaki kodu ekleyelim. Bu kod şu şekilde olur:

- Mevcut mavi kümizi ekrandan kaldırın.
- Azure uzamsal Bağlayıcılarımızı oturumunuzu yeniden başlatın. Bu eylem, bulduğumuz bağlayıcının oluşturduğumuz yerel bağlantı yerine buluttan geldiğinden emin olur.
- Buluta karşıya yüklemediğimiz tutturucu için bir sorgu verin.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Şimdi, sorgulamakta olduğumuz bağlayıcı bulunduğunda çağrılacak kodu açalım. `initializeSession()` yönteminizin içinde aşağıdaki kodu ekleyin. Bu kod parçacığı, bulut uzamsal bağlayıcının bulunduğu bir yeşil Sphere oluşturacak & oluşturacaktır. Ayrıca, ekranı dokunarak bir kez daha yeniden etkinleştirerek tüm senaryoyu daha sonra tekrarlayabilirsiniz: başka bir yerel bağlayıcı oluşturun, karşıya yükleyin ve tekrar bulun.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

İşte bu kadar! Tüm senaryoyu sona erdirmek için uygulamanızı en son bir kez yeniden [dağıtın](#trying-it-out) . Cihazınızın etrafında ilerleyin ve siyah kürenin yer aldığı yere yerleştirin. Ardından, Sphere sarıya dönüşene kadar cihazınızı kamera çerçevelerini yakalamaya devam edin. Yerel çıpası karşıya yüklenecek ve Sphere 'niz mavi kullanacaktır. Son olarak, daha sonra ekranınızı bir kez daha dokunduktan sonra yerel çıpası kaldırıldıktan sonra bulut karşılığına yönelik sorgu yapacağız. Bulut uzamsal bağlantı noktası bulunana kadar cihazınızı etrafında taşımaya devam edin. Yeşil Sphere 'ın doğru konumda görünmesi gerekir ve tüm senaryoyu yeniden tekrarlayabilirsiniz &.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
