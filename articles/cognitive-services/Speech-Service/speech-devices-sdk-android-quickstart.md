---
title: "Hızlı başlangıç: Android 'de konuşma cihazları SDK 'sını çalıştırma"
titleSuffix: Azure Cognitive Services
description: Android konuşma cihazları SDK 'Sı ile çalışmaya başlama önkoşulları ve yönergeleri.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: c3eb6ec28879a7c53feb270e33857cd67dc06b0b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111698"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Hızlı başlangıç: Android 'de konuşma cihazları SDK örnek uygulamasını çalıştırma

Bu hızlı başlangıçta, konuşma özellikli bir ürün oluşturmak veya bir konuşma [dökümü](conversation-transcription-service.md) cihazı olarak kullanmak için Android Için konuşma cihazları SDK 'sını nasıl kullanacağınızı öğreneceksiniz.

Bu kılavuzda, bir konuşma Hizmetleri kaynağına sahip bir Azure bilişsel [Hizmetler](get-started.md) hesabı gerekir. Bir hesabınız yoksa, abonelik anahtarı almak için [ücretsiz deneme sürümünü](https://azure.microsoft.com/try/cognitive-services/) kullanabilirsiniz.

Örnek uygulama için kaynak kodu konuşma cihaz SDK'sı ile dahil edilir. Ayrıca [github'da](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Önkoşullar

Konuşma cihazları SDK 'sını kullanmaya başlamadan önce şunları yapmanız gerekir:

- Cihaza güç sağlamak için [geliştirme setiniz](get-speech-devices-sdk.md) ile birlikte sunulan yönergeleri izleyin.

- [Konuşma cihazları SDK 'sının](https://aka.ms/sdsdk-download)en son sürümünü indirin ve. zip dosyasını çalışma dizininize ayıklayın.

  > [!NOTE]
  > Android-Sample-Release. zip dosyası Android örnek uygulamasını içerir ve bu hızlı başlangıç, uygulamanın C:\SDSDK\Android-Sample-Release 'ye ayıklandığını varsayar

- [Konuşma Hizmetleri için bir Azure abonelik anahtarı](get-started.md) almak için

- Konuşma dökümünü kullanmayı planlıyorsanız, [döngüsel bir mikrofon cihazı](get-speech-devices-sdk.md) kullanmanız gerekir ve bu özellik şu anda yalnızca "en-US" ve "zh-CN" bölgelerinde, "merkezde ABD" ve "eastasıya" olarak kullanılabilir. Konuşma dökümünü kullanmak için bu bölgelerden birinde bir konuşma anahtarınız olmalıdır.

- Kullanıcı dıklarından amaçları (veya eylemleri) belirlemek için konuşma hizmetlerini kullanmayı planlıyorsanız, bir [Language Understanding hizmeti (Luu)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) aboneliğine sahip olmanız gerekir. Lua ve amaç tanıma hakkında daha fazla bilgi edinmek için bkz. [lusıs C#Ile konuşma amaçlarını tanıma ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

  Yapabilecekleriniz [basit LUIS modeline oluşturma](https://docs.microsoft.com/azure/cognitive-services/luis/) veya LUIS modeline LUIS example.json örneği kullanın. LUIS modeline kullanılabilir örnek [konuşma cihazları SDK indirme sitesi](https://aka.ms/sdsdk-luis). Modelinizin JSON dosyasını karşıya yüklemek için [LUIS portalı](https://www.luis.ai/home)seçin **alma yeni uygulama**ve ardından JSON dosyasını seçin.

- Yükleme [Android Studio](https://developer.android.com/studio/) ve [Vysor](https://vysor.io/download/) PC'nizde.

## <a name="set-up-the-device"></a>Cihazı ayarlama

1. Bilgisayarınızda Vysor başlatın.

   ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. Cihazınızı altında listelenmelidir **bir cihaz seçin**. Seçin **görünümü** cihazın yanındaki düğmesi.

1. Klasör simgesini seçerek kablosuz ağınıza bağlayın ve ardından **ayarları** > **WLAN**.

   ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Şirketiniz kendi Wi-Fi sisteme cihazlar bağlama hakkında ilkeler varsa, MAC adresini alın ve şirketinizin Wi-Fi'a bağlayın konusunda BT departmanınıza başvurun gerekir.
   >
   > Dev Seti MAC adresini bulmak için dev Seti masaüstünde dosya klasör simgesini seçin.
   >
   > ![Vysor dosya klasörü](media/speech-devices-sdk/qsg-10.png)
   >
   > Seçin **ayarları**. "İçin mac adresi" için arama yapın ve ardından **Mac adresi** > **Gelişmiş WLAN**. İletişim kutusunun altına görünen MAC adresi yazın.
   >
   > ![Vysor MAC adresi](media/speech-devices-sdk/qsg-11.png)
   >
   > Bazı şirketler, cihazın ne kadar kalabileceği üzerinde bir zaman sınırı olabilir, Wi-Fi sisteme bağlı. Wi-Fi sisteminizle dev Seti'nın kayıt belirli bir gün sayısından sonra genişletmek gerekebilir.

## <a name="run-the-sample-application"></a>Örnek uygulamayı çalıştırın

Geliştirme Seti kurulumunuzu doğrulamak için örnek uygulamayı derleyin ve kurun:

1. Android Studio'yu başlatın.

1. **Var olan Android Studio projesini aç**'ı seçin.

   ![Android Studio - mevcut bir projeyi Aç](media/speech-devices-sdk/qsg-5.png)

1. İçin C:\SDSDK\Android-Sample-Release\example gidin. Seçin **Tamam** örnek projesini açın.

1. Konuşma abonelik anahtarınızı kaynak koda ekleyin. Amaç tanıma denemek istiyorsanız, ayrıca ekleyin, [Language Understanding hizmeti](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) abonelik anahtarı ve uygulama kimliği

   Konuşma ve LUSıS için, bilgileriniz MainActivity. Java içine gider:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Konuşma dökümünü kullanıyorsanız konuşma anahtarınız ve bölge bilgileriniz de konuşmada de gereklidir. Java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Varsayılan anahtar sözcüğü "bilgisayar" dır. "Makine" veya "yardımcı" gibi, belirtilen diğer anahtar sözcüklerden birini de deneyebilirsiniz. Bu alternatif anahtar sözcüklerin kaynak dosyaları, anahtar sözcük klasöründe konuşma cihazları SDK 'snda bulunur. Örneğin, C:\SDSDK\Android-Sample-Release\keyword\Computer "bilgisayar" anahtar sözcüğü için kullanılan dosyaları içerir.

   > [!TIP]
   > Ayrıca, [özel bir anahtar sözcük oluşturabilirsiniz](speech-devices-sdk-create-kws.md).

   Yeni bir anahtar sözcük kullanmak için `MainActivity.java`aşağıdaki iki satırı güncelleştirin ve anahtar sözcük paketini uygulamanıza kopyalayın. Örneğin, KWS-Machine. zip anahtar sözcük paketinden ' Machine ' anahtar sözcüğünü kullanmak için:

   - Anahtar sözcük paketini "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\" klasörüne kopyalayın.
   - `MainActivity.java` anahtar sözcüğüyle ve paket adıyla güncelleştirin:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Mikrofon dizi geometri ayarları içeren aşağıdaki satırları güncelleştirin:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Bu tabloda desteklenen değerler listelenmiştir:

   | Değişken | Anlamı | Kullanılabilir değerler |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Fiziksel MIC yapılandırma | Döngüsel Geliştirme Seti için: `Circular6+1` |
   |          |         | Doğrusal Geliştirme Seti için: `Linear4` |
   | `SelectedGeometry` | Yazılım MIC yapılandırması | Tüm mikrofonlardan kullanan bir döngüsel Geliştirme Seti için: `Circular6+1` |
   |          |         | Dört mikrofonlardan kullanan bir döngüsel Geliştirme Seti için: `Circular3+1` |
   |          |         | Tüm mikrofonlardan kullanan bir doğrusal Geliştirme Seti için: `Linear4` |
   |          |         | İki mikrofonlardan kullanan bir doğrusal Geliştirme Seti için: `Linear2` |

1. Uygulamayı derlemek için **çalıştırma** menüsünde **'uygulamayı' Çalıştır**. **Dağıtım hedefini seçin** iletişim kutusu görüntülenir.

1. Cihazınızı seçin ve ardından **Tamam** cihaza uygulamayı dağıtmak için.

   ![Dağıtım hedefi iletişim kutusunu seçin](media/speech-devices-sdk/qsg-7.png)

1. Konuşma cihaz SDK'sı örnek bir uygulama başlar ve şu seçeneklerini gösterir:

   ![Örnek konuşma cihaz SDK'sı örnek uygulama ve seçenekleri](media/speech-devices-sdk/qsg-8.png)

1. Yeni konuşma dökümü tanıtımı ' nı deneyin. ' Start Session ' ile bir kopyasını başlatın. Varsayılan olarak, herkes bir konudır. Ancak, katılımcının ses imzaları varsa, cihazdaki bir dosyaya `/video/participants.properties` yerleştirebilirsiniz. Ses imzasını oluşturmak için, [konuşmalar (SDK)](how-to-use-conversation-transcription-service.md)konusuna bakın.

   ![Demo konuşma dökümü uygulaması](media/speech-devices-sdk/qsg-15.png)

1. Deneyin!

## <a name="troubleshooting"></a>Sorun giderme

Konuşma cihazına bağlanamıyorsanız. Komut Istemi penceresinde aşağıdaki komutu yazın. Cihazların bir listesini döndürür:

```powershell
 adb devices
```

> [!NOTE]
> Bu komut Android Studio yüklemesinin parçası olan `adb.exe`Android Debug Bridge kullanır. Bu araç C:\Users bulunur\[kullanıcı adı] \AppData\Local\Android\Sdk\platform araçları. Bu dizin çağırmak daha kullanışlı hale getirmek için yola ekleyebilirsiniz `adb`. Aksi takdirde, yüklemenizin adb.exe çağıran her komut için tam yolunu belirtmeniz gerekir `adb`.
>
> `no devices/emulators found` bir hata görürseniz, USB kablonuz bağlı olduğundan ve yüksek kaliteli bir kablonun kullanıldığından emin olun.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Sürüm notlarını gözden geçirin](devices-sdk-release-notes.md)
