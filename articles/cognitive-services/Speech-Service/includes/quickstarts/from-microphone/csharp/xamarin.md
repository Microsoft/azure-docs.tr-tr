---
title: 'Hızlı başlangıç: bir mikrofondan konuşmayı tanıma C# , (Xamarin)-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Bu makalede bilişsel hizmetler konuşma SDK 'sını kullanarak C# evrensel WINDOWS platformu (UWP), Android ve iOS için platformlar arası bir Xamarin uygulaması oluşturacaksınız. Cihazınızın veya benzeticisinin mikrofonunuzdan konuşmayı gerçek zamanlı olarak metne dönüştürme. Uygulama, konuşma SDK 'Sı NuGet paketiyle oluşturulmuştur ve 2019 Microsoft Visual Studio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: d0d1cfee535a04203d9ff995b94fcad5d432ed3e
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961188"
---
## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yaptığınızdan emin olun:

> [!div class="checklist"]
> * [Azure konuşma kaynağı oluşturma](../../../../get-started.md)
> * [Geliştirme ortamınızı kurma](../../../../quickstarts/setup-platform.md?tabs=xamarin)
> * [Boş bir örnek proje oluşturun](../../../../quickstarts/create-project.md?tabs=xamarin)

Bunu zaten yaptıysanız harika. Şimdi devam edelim.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Ortak HelloWorld projesi için örnek kod ekleme

Ortak HelloWorld projesi, platformlar arası uygulamanız için platformdan bağımsız uygulamalar içerir. Şimdi uygulamanın kullanıcı arabirimini tanımlayan XAML kodunu ekleyin ve uygulamanın arkasındaki C# kodu ekleyin.

1. **Çözüm Gezgini**, ortak HelloWorld projesi altında `MainPage.xaml`açın.

1. Tasarımcı XAML görünümünde, aşağıdaki XAML kod parçacığını `<StackLayout>` ve `</StackLayout>`arasında **kılavuz** etiketine ekleyin:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. **Çözüm Gezgini**, arka plan kod kaynak dosyası `MainPage.xaml.cs`açın. `MainPage.xaml`altında gruplandırılır.

1. İçindeki tüm kodu aşağıdaki kod parçacığıyla değiştirin:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Kaynak dosyanın `OnRecognitionButtonClicked` işleyicisinde, `YourSubscriptionKey`dizesini bulun ve abonelik anahtarınızla değiştirin.


1. `OnRecognitionButtonClicked` işleyicisinde, `YourServiceRegion`dizesini bulun ve aboneliğinizle ilişkili [bölge](~/articles/cognitive-services/Speech-Service/regions.md) ile değiştirin. (Örneğin, ücretsiz deneme aboneliği için `westus` kullanın.)

1. Daha sonra, UWP, Android ve iOS gibi farklı platform projelerinden gelen mikrofon izinlerini sorgulamak için kullanılan bir [Xamarin hizmeti](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)oluşturmanız gerekir. Bunu yapmak için HelloWorld projesi altında *Hizmetler* adlı yeni bir klasör ekleyin ve bunun altında yeni C# bir kaynak dosya oluşturun. *Hizmetler* klasörüne sağ tıklayıp > **Yeni öğe** > **kod dosyası** **Ekle** ' yi seçebilirsiniz. `IMicrophoneService.cs`dosyayı yeniden adlandırın ve aşağıdaki kod parçacığındaki tüm kodu bu dosyaya yerleştirin:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="androidtabx-android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>`helloworld.Android` projesi için örnek kod ekleme

Şimdi uygulamanın Android C# 'e özgü bölümünü tanımlayan kodu ekleyin.

1. **Çözüm Gezgini**, HelloWorld altında. Android projesi, `MainActivity.cs`açın.

1. İçindeki tüm kodu aşağıdaki kod parçacığıyla değiştirin:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Ardından, HelloWorld altında yeni klasör *hizmetlerini* oluşturarak `MicrophoneService` için Android 'e özgü uygulama ekleyin. Android projesi. Bundan sonra, altında yeni C# bir kaynak dosya oluşturun. `MicrophoneService.cs`dosyayı yeniden adlandırın. Aşağıdaki kod parçacığını kopyalayıp bu dosyaya yapıştırın:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Bundan sonra, *Özellikler* klasörü altında `AndroidManifest.xml` açın. `<manifest>` ve `</manifest>`arasında mikrofon için aşağıdaki kullanımlar-izin ayarını ekleyin:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="iostabios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>`helloworld.iOS` projesi için örnek kod ekleme

Şimdi uygulamanın iOS C# 'a özgü bölümünü tanımlayan kodu ekleyin. Ayrıca HelloWorld. iOS projesinde Apple cihaza özgü yapılandırma oluşturma.

1. **Çözüm Gezgini**, HelloWorld. iOS projesi altında `AppDelegate.cs`açın.

1. İçindeki tüm kodu aşağıdaki kod parçacığıyla değiştirin:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Sonra, helloworld.iO projesi altında yeni klasör *hizmetlerini* oluşturarak `MicrophoneService` için iOS 'a özgü uygulama ekleyin. Bundan sonra, altında yeni C# bir kaynak dosya oluşturun. `MicrophoneService.cs`dosyayı yeniden adlandırın. Aşağıdaki kod parçacığını kopyalayıp bu dosyaya yapıştırın:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Metin düzenleyicisinde HelloWorld. iOS projesi altında `Info.plist` açın. Aşağıdaki anahtar değer çiftini dict bölümüne ekleyin:

   <key>Nsmikro Phoneusagedescription</key>
   <string>Bu örnek uygulama için mikrofon erişimi gerekiyor</string>

   > [!NOTE]
   > İPhone cihazı için oluşturuyorsanız `Bundle Identifier` cihazınızın sağlama profili uygulama KIMLIĞIYLE eşleştiğinden emin olun. Aksi takdirde, yapı başarısız olur. İPhoneSimulator ile bunu olduğu gibi bırakabilirsiniz.

1. Bir Windows BILGISAYAR üzerinde derleme yapıyorsanız, Mac > **iOS** > **Mac ile eşleştirme** **araçları** aracılığıyla oluşturmaya yönelik Mac cihazına bir bağlantı kurun. Mac cihazına bağlantıyı etkinleştirmek için Visual Studio tarafından sunulan yönerge Sihirbazı ' nı izleyin.

#### <a name="uwptabhelloworlduwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>`helloworld.UWP` projesi için örnek kod ekleme

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>HelloWorld için örnek kod ekleyin. UWP projesi

Şimdi uygulamanın UWP C# 'e özgü bölümünü tanımlayan kodu ekleyin.

1. **Çözüm Gezgini**, HelloWorld altında. UWP projesi, `MainPage.xaml.cs`açın.

1. İçindeki tüm kodu aşağıdaki kod parçacığıyla değiştirin:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Ardından, HelloWorld altında yeni klasör *hizmetlerini* oluşturarak `MicrophoneService` için UWP 'e özgü bir uygulama ekleyin. UWP projesi. Bundan sonra, altında yeni C# bir kaynak dosya oluşturun. `MicrophoneService.cs`dosyayı yeniden adlandırın. Aşağıdaki kod parçacığını kopyalayıp bu dosyaya yapıştırın:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Ardından, HelloWorld altındaki `Package.appxmanifest` dosyasına çift tıklayın. Visual Studio içinde UWP projesi. **Yetenekler**altında, **mikrofonun** seçili olduğundan emin olun ve dosyayı kaydedin.

1. Daha sonra, Visual Studio içindeki `helloworld.UWP` **projesi altında `Package.appxmanifest`** dosya ' ya ve > **mikrofon** ' u kontrol edin ve dosyayı kaydedin.
   > Not: bir uyarı görmeniz durumunda: sertifika dosyası yok: HelloWorld. Daha fazla bilgi için lütfen [konuşmayı metin](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) örneğine göz atın. UWP_TemporaryKey

1. Değişikliklerinizi kaydetmek için menü çubuğundan **dosya** > **Tümünü Kaydet** ' i seçin.

## <a name="build-and-run-the-uwp-application"></a>UWP uygulamasını derleme ve çalıştırma

1. HelloWorld ayarlayın. Başlangıç projesi olarak UWP. HelloWorld öğesine sağ tıklayın. UWP projesi ve uygulamayı derlemek için **Oluştur** ' u seçin.

1. Uygulamayı başlatmak **için hata ayıklama > ** **başlatın** (veya **F5**' i seçin). **HelloWorld** penceresi görüntülenir.

   ![Hızlı başlangıçta örnek UWP konuşma tanıma C# uygulaması](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. **Mikrofonu etkinleştir**' i seçin. Erişim izni isteği göründüğünde **Evet**' i seçin.

   ![Mikrofon erişimi izin isteği](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. **Konuşma tanımayı Başlat**' ı seçin ve cihazınızın mikrofonuna bir İngilizce tümcecik veya cümle konuşun. Konuşma, konuşma hizmetlerine iletilir ve pencerede görüntülenen metne gönderilir.

   ![Konuşma tanıma kullanıcı arabirimi](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Android ve iOS uygulamalarını derleyin ve çalıştırın

Android ve iOS uygulamalarını cihazda veya simülatörde oluşturmak ve çalıştırmak UWP 'ye benzer bir şekilde gerçekleşir. Tüm SDK 'ların Bu makalenin "Önkoşullar" bölümünde gerektiği şekilde yüklendiğinden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]
