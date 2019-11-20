---
title: 'Öğretici: Language Understanding bot Node. js v4'
titleSuffix: Azure Cognitive Services
description: Node.js'yi kullanarak, dil anlama (LUIS) ile tümleşik bir sohbet robotu oluşturun. Bu sohbet robotu, bir robot çözümünü kısa sürede gerçekleştirmek için İnsan Kaynakları uygulamasını kullanır. Robot, Bot Framework sürümü 4 ve Azure Web uygulaması robotu ile geliştirilmiştir.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 9a38f43b24e5db6a60ff38cd0f1d9b59b9875bba
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492695"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Öğretici: node. js ' de Language Understanding etkin bir Web uygulaması bot kullanın 

Dil anlama (LUU) ile tümleştirilmiş bir sohbet bot oluşturmak için Node. js ' yi kullanın. Bot, Azure [Web App bot](https://docs.microsoft.com/azure/bot-service/) kaynağı ve [bot Framework sürüm](https://github.com/Microsoft/botbuilder-dotnet) v4 ile oluşturulmuştur.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

**Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:**

> [!div class="checklist"]
> * Web uygulaması robotu oluşturma. Bu işlem sizin için yeni bir LUIS uygulaması oluşturur.
> * Web bot hizmeti tarafından oluşturulan bot projesini indirin
> * Robotu ve öykünücüyü bilgisayarınızda yerel olarak başlatma
> * Robotta konuşma sonuçlarını görüntüleme

## <a name="prerequisites"></a>Önkoşullar

* [Robot öykünücüsü](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Web uygulaması bot kaynağı oluşturma

1. [Azure portalda](https://portal.azure.com) **Yeni kaynak oluştur**'u seçin.

1. Arama kutusunda **Web Uygulaması Robotu**'nu arayın ve bunu seçin. **Oluştur**'u seçin.

1. **Robot Hizmeti**'nde gerekli bilgileri sağlayın:

    |Ayar|Amaç|Önerilen ayar|
    |--|--|--|
    |Robot adı|Kaynak adı|`luis-nodejs-bot-` + `<your-name>`, örneğin `luis-nodejs-bot-johnsmith`|
    |Abonelik|Robotun oluşturulacağı abonelik.|Birincil aboneliğiniz.
    |Kaynak grubu|Azure kaynaklarının mantıksal grubu|Bu robotla kullanılan tüm kaynakları depolamak için yeni bir grup oluşturun ve grubu `luis-nodejs-bot-resource-group` olarak adlandırın.|
    |Konum|Azure bölgesi - LUIS yazma veya yayımlama bölgesi ile aynı olması gerekmez.|`westus`|
    |Fiyatlandırma katmanı|Hizmet isteği sınırları ve faturalandırma için kullanılır.|`F0` ücretsiz katmanı gösterir.
    |Uygulama adı|Ad, robotunuz buluta dağıtıldığında alt etki alanı olarak kullanılır (örneğin humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, örneğin `luis-nodejs-bot-johnsmith`|
    |Robot şablonu|Bot Framework ayarları - sonraki tabloya bakın|
    |LUIS Uygulaması konumu|LUIS kaynak bölgesi ile aynı olmalıdır|`westus`|
    |Uygulama hizmeti planı/konumu|Belirtilen varsayılan değerden değiştirmeyin.|
    |Application Insights|Belirtilen varsayılan değerden değiştirmeyin.|
    |Microsoft uygulama KIMLIĞI ve parolası|Belirtilen varsayılan değerden değiştirmeyin.|

1. **Bot şablonunda**, aşağıdakileri seçin ve ardından bu ayarlar altındaki **Seç** düğmesini seçin:

    |Ayar|Amaç|Seçim|
    |--|--|--|
    |SDK sürümü|Robot Framework sürümü|**SDK v4**|
    |SDK dili|Robotun programlama dili|**Node.js**|
    |Hecesi|Robot türü|**Temel robot**|
    
1. **Oluştur**'u seçin. Robot hizmetini oluşturur ve Azure'a dağıtır. Bu işlemin bir parçası olarak `luis-nodejs-bot-XXXX` adlı bir LUIS uygulaması oluşturulur. Bu ad/Azure bot hizmeti uygulama adını temel alır.

    [Web uygulaması bot ![oluştur](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

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

1. Açılan iletişim kutusu **indirilen ZIP dosyasında uygulama ayarlarını dahil**Istediğinde, **Evet**' i seçin. Bu, LUSıS ayarlarını sağlar. 

1. Kaynak kodu .zip dosyasına sıkıştırılmışsa, bir iletide kodu indirme bağlantısı sağlanır. Bağlantıyı seçin. 

1. Bu .zip dosyasını yerel bilgisayarınıza kaydedin ve dosyaları ayıklayın. Projeyi Visual Studio ile açın. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Lua 'ya ve yanıtı almaya yönelik kodu gözden geçirin

1. Kullanıcı iletişim kutusunu LUSıS tahmin uç noktasına göndermek için, **iletişim > Flightbookingtanıyıcı. js** dosyasını açın. Robota girilen kullanıcı konuşmasının LUIS'e gönderildiği yer burasıdır. LU, **executeLuisQuery** yönteminden yanıt döndürülür.  

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. **İletişim kutuları-> MainDialog** , söylenişi 'i yakalar ve actstep yönteminde executeLuisQuery 'e gönderir.


    ````javascript
    class MainDialog extends ComponentDialog {

        constructor(luisRecognizer, bookingDialog) {
            ...
            this.luisRecognizer = luisRecognizer;
            ...
        }


        ...

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {

            ...

            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);

            switch (LuisRecognizer.topIntent(luisResult)) {
                    case 'BookFlight':
                        // Extract the values for the composite entities from the LUIS result.
                        const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                        const toEntities = this.luisRecognizer.getToEntities(luisResult);
            
                        // Show a warning for Origin and Destination if we can't resolve them.
                        await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);
            
                        // Initialize BookingDetails with any entities we may have found in the response.
                        bookingDetails.destination = toEntities.airport;
                        bookingDetails.origin = fromEntities.airport;
                        bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                        console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));
            
                        // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                        return await stepContext.beginDialog('bookingDialog', bookingDetails);
            
                    case 'GetWeather':
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        const getWeatherMessageText = 'TODO: get weather flow here';
                        await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        break;
            
                    default:
                        // Catch all for unhandled intents
                        const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                        await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    }
            
                    return await stepContext.next();

        }

        ...

    }
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Bot öykünücüsünü test etmek için kullanın

Kitap uçuş amacı için bir soru sorun.

1. Bot öykünücüsünü başlatın ve **bot 'ı aç**' ı seçin.
1. **Bir bot** açılır penceresi açın iletişim kutusunda, `http://localhost:3978/api/messages`gıbı bot URL 'nizi girin. `/api/messages` yolu, bot 'un Web adresidir.
1. İndirdiğiniz bot kodunun kökündeki **. env** dosyasında bulunan **Microsoft uygulama kimliği** ve **Microsoft uygulama parolasını**girin.

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