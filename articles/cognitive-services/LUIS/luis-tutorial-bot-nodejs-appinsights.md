---
title: 'Öğretici: Application Insights, Node. js-LUSıS'
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, Application Insights telemetri veri depolama alanına bot ve Language Understanding bilgileri eklenmektedir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: b0bc4a93df7db7145accf2b485c45256f53c324d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498945"
---
# <a name="tutorial-add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Öğretici: node. js içindeki bir bot 'tan Application Insights LUSıS sonuçları ekleme
Bu öğreticide, [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetri veri depolama alanına bot ve Language Understanding bilgileri eklenmektedir. Bu verilere sahip olduktan sonra, kusto Language veya Power BI ile sorgulayabilirsiniz, toplayabilirsiniz ve amaçlarını ve gerçek zamanlı olarak yapılan varlıkları analiz etmek, toplamak ve raporlamak için kullanabilirsiniz. Bu analiz, LUSıS uygulamanızın amaçlarını ve varlıklarını ekleme veya düzenleme gerekip gerekmediğini belirlemenize yardımcı olur.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Application Insights içinde verileri anlamak için bot ve dil yakalayın
> * Language Understanding verileri için sorgu Application Insights

## <a name="prerequisites"></a>Önkoşullar

* Application Insights etkinken oluşturulmuş bir Azure bot hizmet bot.
* Önceki bot **[öğreticiden](luis-nodejs-tutorial-bf-v4.md)** bot kodu indirildi. 
* [Robot öykünücüsü](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Bu öğreticideki tüm kod, [Azure-Samples Language Understanding GitHub deposunda](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry)bulunur. 

## <a name="add-application-insights-to-web-app-bot-project"></a>Web uygulaması bot projesine Application Insights ekleyin
Şu anda, bu Web uygulaması bot 'ta kullanılan Application Insights hizmeti, bot için genel durum telemetrisi toplar. LUSıS bilgileri toplanmaz. 

LUSıS bilgilerini yakalamak için Web uygulaması bot 'ın **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM paketinin yüklü ve yapılandırılmış olması gerekir.  

1. VSCode Integrated terminalinde, bot projesi kökünde, gösterilen komutu kullanarak aşağıdaki NPM paketlerini ekleyin: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    **Alt çizgi** paketi, Application Insights ' de kolayca görmeniz ve kullanılması daha kolay olması için LUSıS JSON yapısını düzleştirmek üzere kullanılır.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Application Insights için LUSıS sorgu sonuçlarını yakala ve Gönder

1. VSCode 'da, **Appınsightslog. js** adlı yeni bir dosya oluşturun ve aşağıdaki kodu ekleyin:

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    Bu dosya, her iki nesneyi düzleştirir ve Application Insights 'ta bir **izleme** olayına ekler. Olayın adı **lusıs**'dir. 

1. **İletişim kutusu** klasörünü ve ardından **luishelper. js** dosyasını açın. Yeni **Appınsightslog. js** dosyasını gerekli bir dosya olarak ekleyin ve bot BAĞLAMıNı ve lusıs yanıtını yakalayın. Bu dosyanın tüm kodu: 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

## <a name="add-application-insights-instrumentation-key"></a>Application Insights izleme anahtarı Ekle 

Application Insights 'a veri eklemek için, izleme anahtarına ihtiyacınız vardır.

1. Bir tarayıcıda [Azure Portal](https://portal.azure.com), bot 'ın **Application Insights** kaynağını bulun. Adında, bot adının büyük bir kısmında, adın sonunda `luis-nodejs-bot-johnsmithxqowom`gibi rastgele karakterler olacaktır. 
1. Application Insights kaynağında **genel bakış** sayfasında, **izleme anahtarını**kopyalayın.
1. VSCode 'da, bot projesinin kökünde **. env** dosyasını açın. Bu dosya, tüm ortam değişkenlerinizi içerir.  
1. İzleme anahtarınızın değeri ile `MicrosoftApplicationInsightsInstrumentationKey` yeni bir değişken ekleyin. Değeri tırnak içine alın. 

## <a name="start-the-bot"></a>Robotu başlatma

1. VSCode Integrated terminalinde, bot 'ı başlatın:
    
    ```console
    npm start
    ```

1. Bot öykünücüsünü başlatın ve bot 'ı açın. Bu [adım](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) , önceki öğreticide sunulmaktadır.

1. Bot 'a soru sorun. Bu [adım](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) , önceki öğreticide sunulmaktadır.

## <a name="view-luis-entries-in-application-insights"></a>Application Insights Lua girdilerini görüntüle

LUIN girdilerini görmek için Application Insights açın. Verilerin Application Insights görünmesi birkaç dakika sürebilir.

1. [Azure Portal](https://portal.azure.com), bot 'ın Application Insights kaynağını açın. 
1. Kaynak açıldığında **Ara** ' yı seçin ve son **30 dakika** içinde, **izleme**olay türü ile tüm verileri ara ' yı seçin. **Lusıs**adlı izlemeyi seçin. 
1. Bot ve LUSıS bilgileri **özel özellikler**altında bulunabilir. 

    ![Application Insights ' de depolanan LUSıS özel özelliklerini gözden geçirin](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Amaç, skor ve söylenişi için Application Insights sorgulama
Application Insights, verileri [kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) diliyle sorgulama yanı sıra [Power BI](https://powerbi.microsoft.com)dışa aktarma gücü sağlar. 

1. **Günlük (Analiz)** öğesini seçin. En üstteki bir sorgu penceresi ve bunun altında bir veri tablosu penceresi ile yeni bir pencere açılır. Daha önce veritabanlarını kullandıysanız, bu düzenleme tanıdık gelecektir. Sorgu, önceki Filtrelenen verileri temsil eder. **Customdimensions** sütununda bot ve lusıs bilgileri bulunur.
1. En üst amacı, puanı ve utterance 'i almak için, sorgu penceresinde son satırın (`|top...` satırı) hemen üstüne aşağıdakini ekleyin:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Sorguyu çalıştırın. Yeni topamaç, skor ve söylenişi sütunları mevcuttur. Sıralamak için Topamaç sütununu seçin.

[Kusto sorgu dili](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) hakkında daha fazla bilgi edinin veya [verileri Power BI dışarı aktarın](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Sonraki adımlar

Application Insights verilerine eklemek isteyebileceğiniz diğer bilgiler uygulama KIMLIĞI, sürüm KIMLIĞI, son model değiştirme tarihi, son tren tarihi, son yayımlama tarihi içerir. Bu değerler, uç nokta URL 'sinden (uygulama KIMLIĞI ve sürüm KIMLIĞI) alınabilir veya bir yazma API çağrısından, daha sonra Web uygulaması bot ayarlarında ayarlanır ve buradan çekilir.  

Birden fazla LUO uygulaması için aynı uç nokta aboneliğini kullanıyorsanız, abonelik KIMLIĞINI ve paylaşılan anahtar olduğunu belirten bir özelliği de eklemeniz gerekir. 

> [!div class="nextstepaction"]
> [Örnek araslar hakkında daha fazla bilgi edinin](luis-how-to-add-example-utterances.md)
