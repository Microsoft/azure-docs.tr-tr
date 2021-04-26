---
title: "Konuşma SDK 'sını kullanarak konuşma amaçlarını tanıma #"
titleSuffix: Azure Cognitive Services
description: Bu kılavuzda, C# için konuşma SDK 'sını kullanarak konuşmadan amaçları tanımayı öğrenirsiniz.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 93a3adf00203e317be912e3e72de7a3f7ca666c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001123"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>C için konuşma SDK 'sını kullanarak konuşma amaçlarını tanıma #

Bilişsel Hizmetler [konuşma SDK 'sı](speech-sdk.md) , **Amaç tanıma** sağlamak IÇIN [Language Understanding hizmeti (Luo)](https://www.luis.ai/home) ile tümleşir. Amaç, kullanıcının yapmak istediği herhangi bir şeydir: uçak rezervasyonu, hava durumuna bakma veya telefon etme. Kullanıcı kendisine hangi terim doğal geliyorsa onu kullanabilir. Machine Learning kullanarak, LUSıS Kullanıcı isteklerini tanımladığınız amaçlar ile eşleştirir.

> [!NOTE]
> LUIS uygulaması tanımak istediğiniz amaçları ve varlıkları tanımlar. Konuşma hizmetini kullanan C# uygulamasından ayrıdır. Bu makalede "app" LUIS uygulaması anlamına gelirken "uygulama" da C# kodu anlamına gelmektedir.

Bu kılavuzda, cihazınızın mikrofonuna göre Kullanıcı aralarından amaçları türeten bir C# konsol uygulaması geliştirmek için konuşma SDK 'sını kullanırsınız. Şunları öğrenirsiniz:

> [!div class="checklist"]
>
> - Konuşma SDK'sı NuGet paketine başvuran bir Visual Studio projesi oluşturma
> - Bir konuşma yapılandırması oluşturma ve amaç tanıyıcısı edinme
> - LUIS app’iniz için modeli alma ve size gereken amaçları ekleme
> - Konuşma tanıma için dil belirtme
> - Dosyadan konuşma tanıma
> - Zaman uyumsuz, olay odaklı sürekli tanıma kullanma

## <a name="prerequisites"></a>Önkoşullar

Bu kılavuza başlamadan önce aşağıdaki öğelere sahip olduğunuzdan emin olun:

- LUIS hesabı. [LUIS portalından](https://www.luis.ai/home) ücretsiz bir hesap alabilirsiniz.
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (herhangi bir sürüm).

## <a name="luis-and-speech"></a>LUIS ve konuşma

LUIS, konuşmadaki amaçları tanımak için Konuşma hizmetiyle tümleştirilir. Konuşma hizmeti aboneliğine ihtiyacınız yoktur; LUIS yeterlidir.

LUSıS üç tür anahtar kullanır:

| Anahtar türü  | Amaç                                               |
| --------- | ----------------------------------------------------- |
| Yazma | LUSıS uygulamalarını programlı bir şekilde oluşturmanızı ve değiştirmenizi sağlar |
| Başlangıç   | LUSıS uygulamanızı yalnızca metin kullanarak test etmenizi sağlar   |
| Uç Nokta  | Belirli bir Lua uygulamasına erişim yetkisi verir            |

Bu kılavuz için uç nokta anahtar türüne ihtiyacınız vardır. Bu kılavuzda, [önceden oluşturulmuş giriş Otomasyonu uygulama](../luis/luis-get-started-create-app.md) hızlı başlangıcı ' nı Izleyerek oluşturabileceğiniz GIRIŞ Otomasyonu Luo uygulaması örneği kullanılmaktadır. Kendi bir LUSıS uygulaması oluşturduysanız bunun yerine kullanabilirsiniz.

Bir LUSıS uygulaması oluşturduğunuzda, bu uygulamayı metin sorgularını kullanarak test edebilmeniz için, LUSıS otomatik olarak bir başlangıç anahtarı oluşturur. Bu anahtar, konuşma hizmeti tümleştirmesini etkinleştirmez ve bu kılavuzla çalışmaz. Azure panosunda bir LUSıS kaynağı oluşturun ve bunu LUO uygulamasına atayın. Bu kılavuz için ücretsiz abonelik katmanını kullanabilirsiniz.

Azure panosu 'nda LUO kaynağını oluşturduktan sonra, [Halu portalında](https://www.luis.ai/home)oturum açın, **uygulamalarım** sayfasında uygulamanızı seçin, sonra uygulamanın **Yönet** sayfasına geçin. Son olarak, kenar çubuğunda **anahtarlar ve uç noktalar** ' ı seçin.

![LUIS portalı anahtarları ve uç nokta ayarları](media/sdk/luis-keys-endpoints-page.png)

**Anahtarlar ve uç nokta ayarları** sayfasında:

1. Aşağı kaydırarak **kaynaklar ve anahtarlar** bölümüne gidin ve **kaynak ata**' yı seçin.
1. **Uygulamanıza anahtar ata** iletişim kutusunda aşağıdaki değişiklikleri yapın:

   - **Kiracı** altında **Microsoft**' u seçin.
   - **Abonelik adı** bölümünde, kullanmak istediğiniz Luo kaynağını içeren Azure aboneliğini seçin.
   - **Anahtar** altında uygulamayla birlikte kullanmak istediğiniz Luo kaynağını seçin.

   Kısa süre içinde yeni abonelik sayfanın altındaki tabloda görüntülenir.

1. Panoya kopyalamak için bir anahtarın yanındaki simgeyi seçin. (İstediğiniz anahtarı kullanabilirsiniz.)

![LUIS app abonelik anahtarları](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Visual Studio’da konuşma projesi oluşturma

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Kod ekleme

Ardından, projeye kod eklersiniz.

1. **Çözüm Gezgini**, dosya **programı. cs** dosyasını açın.

1. `using`Dosyanın başındaki deyimlerin bloğunu aşağıdaki bildirimlerle değiştirin:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. `Main()`Aşağıdaki zaman uyumsuz eşdeğerleriyle, belirtilen yöntemi değiştirin:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Burada gösterildiği gibi boş bir zaman uyumsuz yöntem oluşturun `RecognizeIntentAsync()` :

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. Bu yeni yöntemin gövdesinde şu kodu ekleyin:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. Bu yöntemdeki yer tutucuları aşağıda gösterildiği gibi LUIS abonelik anahtarınız, bölgeniz ve uygulama kimliğinizle değiştirin.

   | Yer tutucu | Şununla değiştir |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | LUIS uç nokta anahtarınız. Yine de, bu öğeyi bir "başlangıç anahtarı" değil, Azure panonuzdan almanız gerekir. Bu dosyayı, [BASIS portalındaki](https://www.luis.ai/home)uygulamanızın **anahtarlar ve uç noktalar** sayfasında ( **Yönet** bölümünde) bulabilirsiniz. |
   | `YourLanguageUnderstandingServiceRegion` | LUIS aboneliğinizin içinde bulunduğu bölgenin kısa tanımlayıcısı (örneğin, Batı ABD için `westus`). Bkz. [Bölgeler](regions.md). |
   | `YourLanguageUnderstandingAppId` | LUIS app kimliği. Bu dosyayı, [lusıs portalındaki](https://www.luis.ai/home)uygulamanızın **Ayarlar** sayfasında bulabilirsiniz. |

Bu değişiklikler yapıldıktan sonra, (**Control + SHIFT + B**) oluşturabilir ve uygulamayı çalıştırabilirsiniz (**F5**). İstendiğinde, BILGISAYARıNıZıN mikrofonuna "Işıkları kapatmayı" söyleyerek deneyin. Uygulama, sonucu konsol penceresinde görüntüler.

Aşağıdaki bölümlerde kod açıklaması yer alır.

## <a name="create-an-intent-recognizer"></a>Amaç tanıyıcı oluşturma

İlk olarak, LUSıS uç nokta anahtarınızdan ve bölgenizde bir konuşma yapılandırması oluşturmanız gerekir. Konuşma SDK 'sının çeşitli özellikleri için Tanıyıcılar oluşturmak üzere konuşma yapılandırmasını kullanabilirsiniz. Konuşma yapılandırmasında, kullanmak istediğiniz aboneliği belirtmek için birden çok yol vardır; burada, `FromSubscription` abonelik anahtarını ve bölgesini alan kullanırız.

> [!NOTE]
> Bir konuşma hizmeti aboneliği değil, LUSıS aboneliğiniz için anahtar ve bölge kullanın.

Daha sonra, `new IntentRecognizer(config)` kullanarak bir amaç tanıyıcı oluşturun. Yapılandırma, hangi aboneliğin kullanılacağını zaten biliyor olduğundan, tanıyıcı oluştururken abonelik anahtarını ve uç noktayı belirtmeniz gerekmez.

## <a name="import-a-luis-model-and-add-intents"></a>LUIS modelini içeri aktarma ve amaç ekleme

Şimdi de, `LanguageUnderstandingModel.FromAppId()` kullanarak LUIS app’ten modeli içeri aktarın ve tanıyıcının `AddIntent()` yöntemi aracılığıyla tanınmasını istediğiniz LUIS amaçlarını ekleyin. Bu iki adım, kullanıcıların isteklerinde kullanabileceği sözcükleri belirterek konuşma tanımanın doğruluğunu güçlendirir. Uygulamanızda tümünü tanımak zorunda olmadığınız takdirde tüm uygulamanın amaçlarını eklemeniz gerekmez.

Amaçları eklemek için üç bağımsız değişken sağlamalısınız: LUSıS modeli (oluşturulan ve adlandırılan `model` ), amaç adı ve bir amaç kimliği. Kimlik ve ad arasındaki fark aşağıda gösterilmiştir.

| `AddIntent()`&nbsp;değişkendir | Amaç |
| --------------------------- | ------- |
| `intentName` | LUIS app’te tanımlandığı şekliyle amacın adı. Bu değer, LUO amaç adıyla tam olarak eşleşmelidir. |
| `intentID` | Konuşma SDK’sı tarafından tanınan amaca atanan kimlik. Bu değer, istediğiniz her şey olabilir; LUSıS uygulamasında tanımlanan amaç adına karşılık gelmesi gerekmez. Örneğin, aynı kodla birden çok amaç işleniyorsa, bunlar için aynı kimliği kullanabilirsiniz. |

Home Automation LUIN uygulamasının iki amacı vardır: bir cihazı açmak için bir, diğeri de bir cihazı kapatmak için. Aşağıdaki satırlar bu amaçları tanıyıcıya ekler; `RecognizeIntentAsync()` yöntemindeki üç `AddIntent` satırını bu kodla değiştirin.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Ayrı amaçlar eklemek yerine, `AddAllIntents` bir modeldeki tüm amaçları tanıyıcıya eklemek için yöntemini de kullanabilirsiniz.

## <a name="start-recognition"></a>Tanımayı başlatma

Oluşturulan tanıyıcıyla ve eklenen amaçlarla tanıma başlayabilir. Konuşma SDK’sı hem tek seferlik hem de sürekli tanımayı destekler.

| Tanıma modu | Çağrılacak yöntemler | Sonuç |
| ---------------- | --------------- | ------ |
| Tek seferlik | `RecognizeOnceAsync()` | Tek konuşmadan sonra, tanınan amacı (varsa) döndürür. |
| Sürekli | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Birden çok kuralı tanır; sonuçlar kullanılabilir olduğunda olayları (örneğin, `IntermediateResultReceived` ) yayar. |

Uygulama tek bir görüntü modunu kullanır ve bu nedenle `RecognizeOnceAsync()` tanıma başlamak için çağırır. Sonuç, tanınan amaç hakkındaki bilgileri içeren `IntentRecognitionResult` nesnesidir. Aşağıdaki ifadeyi kullanarak LUSıS JSON yanıtını ayıklayın:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Uygulama JSON sonucunu ayrıştırmıyor. Yalnızca konsol penceresinde JSON metnini görüntüler.

![Tek LUSıS tanıma sonuçları](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Tanıma dilini belirtme

LUIS varsayılan olarak ABD İngilizcesindeki (`en-us`) amaçları tanır. Konuşma yapılandırmasının `SpeechRecognitionLanguage` özelliğine yerel ayar kodu atayarak başka dillerde de amaçları tanıyabilirsiniz. Örneğin, Almanca 'nın `config.SpeechRecognitionLanguage = "de-de";` amaçlarını tanımak üzere tanıyıcı oluşturmadan önce uygulamamıza ekleyin. Daha fazla bilgi için bkz. [lusıs dil desteği](../LUIS/luis-language-support.md#languages-supported).

## <a name="continuous-recognition-from-a-file"></a>Dosyadan sürekli tanıma

Aşağıdaki kod, Konuşma SDK’sını kullanarak amaç tanımanın iki ek özelliğini gösterir. Önceden belirtildiği gibi, ilk özellik sonuçlar sağlandığında tanıyıcının olayları gösterdiği sürekli tanımadır. Ardından bu olaylar sağladığınız olay işleyicileri tarafından işlenebilir. Sürekli tanıma sayesinde, `StartContinuousRecognitionAsync()` tanımayı başlatmak için tanıyıcı metodunu çağırın `RecognizeOnceAsync()` .

Diğer özellik, işlenecek konuşmayı içeren sesin WAV dosyasından okunmasıdır. Uygulama, amaç tanıyıcı oluştururken kullanılabilecek bir ses yapılandırması oluşturmayı içerir. Bu dosya 16 kHz örnekleme hızıyla tek kanallı (mono) olmalıdır.

Bu özellikleri denemek için yöntemin gövdesini silin veya Not `RecognizeIntentAsync()` edin ve aşağıdaki kodu yerine ekleyin.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Daha önce olduğu gibi LUIS uç nokta anahtarınızı, bölgenizi ve app kimliğinizi dahil etmek, ayrıca Ev Otomasyonu amaçlarını eklemek için kodu düzeltin. `whatstheweatherlike.wav`Kayıtlı ses dosyanızın adına geçin. Sonra derleyin, ses dosyasını yapı dizinine kopyalayın ve uygulamayı çalıştırın.

Örneğin, "ışıkları kapat" deyin, duraklatıp ve ardından kayıtlı ses dosyanızdaki "ışıkları aç" deyin, aşağıdakilere benzer konsol çıktısı görünebilir:

![Ses dosyası LUSıS tanıma sonuçları](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
**Samples/CSharp/sharedcontent/Console** klasöründe bu makaledeki kodu arayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Hızlı başlangıç: bir mikrofondan konuşmayı tanıma](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)