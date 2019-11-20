---
title: 'Öğretici: Language Understanding bot C# v4'
titleSuffix: Azure Cognitive Services
description: C# kullanarak, dil anlama (LUIS) ile tümleşik bir sohbet robotu oluşturun. Bot, bot Framework sürüm 4 ve Azure Web App bot hizmeti ile oluşturulmuştur.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 213449a78baf8fc2b7cb6c74709efeaf2a50d5b2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495462"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Öğretici: ' de Language Understanding ile etkin bir Web uygulaması bot kullanınC#

Dil C# anlama (Luu) ile tümleştirilmiş bir sohbet bot oluşturmak için kullanın. Bot, Azure [Web App bot](https://docs.microsoft.com/azure/bot-service/) kaynağı ve [bot Framework sürüm](https://github.com/Microsoft/botbuilder-dotnet) v4 ile oluşturulmuştur.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]


**Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:**

> [!div class="checklist"]
> * Web uygulaması robotu oluşturma. Bu işlem sizin için yeni bir LUIS uygulaması oluşturur.
> * Web bot hizmeti tarafından oluşturulan bot projesini indirin
> * Robotu ve öykünücüyü bilgisayarınızda yerel olarak başlatma
> * Robotta konuşma sonuçlarını görüntüleme

## <a name="prerequisites"></a>Önkoşullar

* [Robot öykünücüsü](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Web uygulaması bot kaynağı oluşturma

1. [Azure portalda](https://portal.azure.com) **Yeni kaynak oluştur**'u seçin.

1. Arama kutusunda **Web Uygulaması Robotu**'nu arayın ve bunu seçin. **Oluştur**'u seçin.

1. **Robot Hizmeti**'nde gerekli bilgileri sağlayın:

    |Ayar|Amaç|Önerilen ayar|
    |--|--|--|
    |Robot adı|Kaynak adı|`luis-csharp-bot-` + `<your-name>`, örneğin `luis-csharp-bot-johnsmith`|
    |Abonelik|Robotun oluşturulacağı abonelik.|Birincil aboneliğiniz.
    |Kaynak grubu|Azure kaynaklarının mantıksal grubu|Bu robotla kullanılan tüm kaynakları depolamak için yeni bir grup oluşturun ve grubu `luis-csharp-bot-resource-group` olarak adlandırın.|
    |Konum|Azure bölgesi - LUIS yazma veya yayımlama bölgesi ile aynı olması gerekmez.|`westus`|
    |Fiyatlandırma katmanı|Hizmet isteği sınırları ve faturalandırma için kullanılır.|`F0` ücretsiz katmanı gösterir.
    |Uygulama adı|Ad, robotunuz buluta dağıtıldığında alt etki alanı olarak kullanılır (örneğin humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, örneğin `luis-csharp-bot-johnsmith`|
    |Robot şablonu|Bot Framework ayarları - sonraki tabloya bakın|
    |LUIS Uygulaması konumu|LUIS kaynak bölgesi ile aynı olmalıdır|`westus`|
    |Uygulama hizmeti planı/konumu|Belirtilen varsayılan değerden değiştirmeyin.|
    |Application Insights|Belirtilen varsayılan değerden değiştirmeyin.|
    |Microsoft uygulama KIMLIĞI ve parolası|Belirtilen varsayılan değerden değiştirmeyin.|

1. **Bot şablonunda**, aşağıdakileri seçin ve ardından bu ayarlar altındaki **Seç** düğmesini seçin:

    |Ayar|Amaç|Seçim|
    |--|--|--|
    |SDK sürümü|Robot Framework sürümü|**SDK v4**|
    |SDK dili|Robotun programlama dili|**C#**|
    |Hecesi|Robot türü|**Temel robot**|
    
1. **Oluştur**'u seçin. Robot hizmetini oluşturur ve Azure'a dağıtır. Bu işlemin bir parçası olarak `luis-csharp-bot-XXXX` adlı bir LUIS uygulaması oluşturulur. Bu ad/Azure bot hizmeti uygulama adını temel alır.

    [Web uygulaması bot ![oluştur](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Devam etmeden önce bot hizmeti oluşturuluncaya kadar bekleyin.

## <a name="the-bot-has-a-language-understanding-model"></a>Bot 'ın bir Language Understanding modeli vardır

Bot hizmeti oluşturma işlemi, amaçlar ve örnek dıklarla yeni bir LUO uygulaması da oluşturur. Robot, yeni LUIS uygulamasına şu amaçlar için amaç eşlemesi sağlar: 

|Temel robot LUIS amaçları|örnek konuşma|
|--|--|
|Kitap kolu|`Travel to Paris`|
|İptal|`bye`|
|Gethava durumu|`what's the weather like?`|
|None|Uygulamanın etki alanı dışındaki her şey.|

## <a name="test-the-bot-in-web-chat"></a>Web sohbetinde bot 'ı test etme

1. Yeni bot için Azure portal devam ederken, **Web sohbetinde test**' i seçin. 
1. **Iletinizi yazın** metin kutusuna `Book a flight from Seattle to Berlin tomorrow`yazın. Bot, bir uçuş sağlamak istediğiniz doğrulama ile yanıt verir. 

    ![Azure portal ekran görüntüsü, ' Merhaba ' metnini girin.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Botunuzu hızlı bir şekilde test etmek için test işlevselliğini kullanabilirsiniz. Hata ayıklama da dahil olmak üzere daha kapsamlı bir test için, bot kodunu indirin ve Visual Studio 'Yu kullanın. 

## <a name="download-the-web-app-bot-source-code"></a>Web uygulaması bot kaynak kodunu indirin
Web uygulaması robot kodunu geliştirmek için, yerel bilgisayarınızda kodu indirin ve kullanın. 

1. Azure portalda, **Robot yönetimi** bölümünden **Derle**'yi seçin. 

1. **Robot kaynak kodunu indir**'i seçin. 

    [temel bot için Web uygulaması bot kaynak kodunu Indirin ![](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Açılan iletişim kutusu **indirilen ZIP dosyasında uygulama ayarlarını dahil**Istediğinde, **Evet**' i seçin.

1. Kaynak kodu .zip dosyasına sıkıştırılmışsa, bir iletide kodu indirme bağlantısı sağlanır. Bağlantıyı seçin. 

1. Bu .zip dosyasını yerel bilgisayarınıza kaydedin ve dosyaları ayıklayın. Projeyi Visual Studio ile açın. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Lua 'ya ve yanıtı almaya yönelik kodu gözden geçirin

1. Kullanıcı utterliğini LUSıS tahmin uç noktasına göndermek için **FlightBookingRecognizer.cs** dosyasını açın. Robota girilen kullanıcı konuşmasının LUIS'e gönderildiği yer burasıdır. LUSıS yanıtı, **RecognizeAsync** yönteminden döndürülür.  

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    
    namespace Microsoft.BotBuilderSamples
    {
        public class FlightBookingRecognizer : IRecognizer
        {
            private readonly LuisRecognizer _recognizer;
    
            public FlightBookingRecognizer(IConfiguration configuration)
            {
                var luisIsConfigured = !string.IsNullOrEmpty(configuration["LuisAppId"]) && !string.IsNullOrEmpty(configuration["LuisAPIKey"]) && !string.IsNullOrEmpty(configuration["LuisAPIHostName"]);
                if (luisIsConfigured)
                {
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]);
    
                    _recognizer = new LuisRecognizer(luisApplication);
                }
            }
    
            // Returns true if luis is configured in the appsettings.json and initialized.
            public virtual bool IsConfigured => _recognizer != null;
    
            public virtual async Task<RecognizerResult> RecognizeAsync(ITurnContext turnContext, CancellationToken cancellationToken)
                => await _recognizer.RecognizeAsync(turnContext, cancellationToken);
    
            public virtual async Task<T> RecognizeAsync<T>(ITurnContext turnContext, CancellationToken cancellationToken)
                where T : IRecognizerConvert, new()
                => await _recognizer.RecognizeAsync<T>(turnContext, cancellationToken);
        }
    }
    ````

1. **İletişim kutularını aç-> MainDialog.cs** , söylenişi yakalar ve bunu actstep yönteminde executeLuisQuery 'e gönderir. 

    ```csharp
    public class MainDialog : ComponentDialog
    {
        private readonly FlightBookingRecognizer _luisRecognizer;

        ...

        public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                    : base(nameof(MainDialog))
        {
            _luisRecognizer = luisRecognizer;
            ...
        }

        private async Task<DialogTurnResult> ActStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
        {
            if (!_luisRecognizer.IsConfigured)
            {
                // LUIS is not configured, we just run the BookingDialog path with an empty BookingDetailsInstance.
                return await stepContext.BeginDialogAsync(nameof(BookingDialog), new BookingDetails(), cancellationToken);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt.)
            var luisResult = await _luisRecognizer.RecognizeAsync<FlightBooking>(stepContext.Context, cancellationToken);
            switch (luisResult.TopIntent().intent)
            {
                case FlightBooking.Intent.BookFlight:
                    await ShowWarningForUnsupportedCities(stepContext.Context, luisResult, cancellationToken);

                    // Initialize BookingDetails with any entities we may have found in the response.
                    var bookingDetails = new BookingDetails()
                    {
                        // Get destination and origin from the composite entities arrays.
                        Destination = luisResult.ToEntities.Airport,
                        Origin = luisResult.FromEntities.Airport,
                        TravelDate = luisResult.TravelDate,
                    };

                    // Run the BookingDialog giving it whatever details we have from the LUIS call, it will fill out the remainder.
                    return await stepContext.BeginDialogAsync(nameof(BookingDialog), bookingDetails, cancellationToken);

                case FlightBooking.Intent.GetWeather:
                    // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                    var getWeatherMessageText = "TODO: get weather flow here";
                    var getWeatherMessage = MessageFactory.Text(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(getWeatherMessage, cancellationToken);
                    break;

                default:
                    // Catch all for unhandled intents
                    var didntUnderstandMessageText = $"Sorry, I didn't get that. Please try asking in a different way (intent was {luisResult.TopIntent().intent})";
                    var didntUnderstandMessage = MessageFactory.Text(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(didntUnderstandMessage, cancellationToken);
                    break;
            }

            return await stepContext.NextAsync(null, cancellationToken);
        }
        
        ...

    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>Visual Studio 'da bot kodunu başlatma

Visual Studio 2019 ' de, bot 'ı başlatın. Web uygulaması robotunun `http://localhost:3978/` adresindeki sitesi ile bir tarayıcı penceresi açılır. Bir giriş sayfası, bot 'unuzla ilgili bilgileri görüntüler.

![Bir giriş sayfası, bot 'unuzla ilgili bilgileri görüntüler.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Bot öykünücüsünü test etmek için kullanın

1. Bot öykünücüsünü başlatın ve **bot 'ı aç**' ı seçin.
1. **Bir bot** açılır penceresi açın iletişim kutusunda, `http://localhost:3978/api/messages`gıbı bot URL 'nizi girin. `/api/messages` yolu, bot 'un Web adresidir.
1. İndirdiğiniz bot kodunun kökündeki **appSettings. JSON** dosyasında bulunan **Microsoft uygulama kimliği** ve **Microsoft uygulama parolasını**girin.


1. Bot öykünücüsünde `Book a flight from Seattle to Berlin tomorrow` girin ve **Web sohbetinde testte**aldığınız gibi temel bot için aynı yanıtı alın.

    [Öykünücüde temel bot yanıtını ![](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. **Evet**' i seçin. Bot, eylemlerinin bir özeti ile yanıt verir. 
1. Bot öykünücüsünün günlüğünden `Luis Trace`içeren çizgiyi seçin. Bu, deterance 'in amacı ve varlıkları için LUSıS 'den gelen JSON yanıtını görüntüler.

    [Öykünücüde temel bot yanıtını ![](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]


## <a name="next-steps"></a>Sonraki adımlar

Konuşma botları ile daha fazla [örnek](https://github.com/microsoft/botframework-solutions) görüntüleyin. 

> [!div class="nextstepaction"]
> [Özel konu etki alanı ile Language Understanding uygulaması oluşturma](luis-quickstart-intents-only.md)
