---
title: LUSıS ve QnAMaker-bot tümleştirmesi
titleSuffix: Azure Cognitive Services
description: Soru-Cevap Oluşturma bilgi tabanınız büyüdükçe büyük bir tek tek tek parçalı bir küme olarak korunması zor olur ve bilgi bankasını daha küçük mantıksal parçalara bölmek için bir gereksinim vardır.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e1ea234bde96ce84259841bbc592bf6373bc639
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802803"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Bilgi tabanınızı dağıtmak için Soru-Cevap Oluşturma ve LUO ile bot kullanın
Soru-Cevap Oluşturma bilgi tabanınız büyüdükçe büyük bir tek tek tek parçalı bir küme olarak korunması zor olur ve bilgi bankasını daha küçük mantıksal parçalara bölmek için bir gereksinim vardır.

Soru-Cevap Oluşturma ' de birden fazla bilgi tabanı oluşturmak basit olsa da, gelen soruyu ilgili Bilgi Bankası 'na yönlendirmek için bir mantığın olması gerekir. Bunu LUSıS kullanarak yapabilirsiniz.

Bu makalede, bot Framework v3 SDK kullanılmaktadır. Bu bilgilerin bot Framework v4 SDK sürümü ile ilgileniyorsanız lütfen bu [bot Framework makalesine](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)bakın.

## <a name="architecture"></a>Mimari

![Language Understanding mimarisiyle Soru-Cevap Oluşturma](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Yukarıdaki senaryoda, Soru-Cevap Oluşturma ilk olarak bir LUO modelinden gelen sorunun amacını alır ve bunu, doğru Soru-Cevap Oluşturma bilgi tabanına yönlendirmek için kullanır.

## <a name="create-a-luis-app"></a>LUSıS uygulaması oluşturma

1. [Lui](https://www.luis.ai/) portalında oturum açın.
1. [Uygulama oluşturun](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. Her Soru-Cevap Oluşturma Bilgi Bankası için [bir amaç ekleyin](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) . Örnek, Soru-Cevap Oluşturma bilgi bankasındaki sorulara karşılık gelmelidir.
1. Lusıs uygulamasını [eğitme](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) ve LUSıS uygulamasını halsıs uygulamanızın [yayımlaması](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) .
1. **Yönet** bölümünde, lusıs uygulama KIMLIĞINIZI, lusıs bitiş noktası anahtarınızı ve [özel etki alanı adını](../../cognitive-services-custom-subdomains.md)unutmayın. Bu değerlere daha sonra ihtiyacınız olacak. 

## <a name="create-qna-maker-knowledge-bases"></a>Soru-Cevap Oluşturma bilgi tabanları oluşturun

1. [Soru-cevap oluşturma](https://qnamaker.ai)oturum açın.
1. LUSıS uygulamasındaki her amaç için bir Bilgi Bankası temeli [oluşturun](https://www.qnamaker.ai/Create) .
1. Bilgi temellerini test edin ve yayımlayın. Her bir KB 'yi yayımladığınızda, KB KIMLIĞI, kaynak adı ( _. azurewebsites.net/qnamaker_öncesi özel alt etki alanı) ve yetkilendirme uç noktası anahtarı hakkında bilgi alın. Bu değerlere daha sonra ihtiyacınız olacak. 

    Bu makalede, KBs 'lerin aynı Azure Soru-Cevap Oluşturma aboneliğinde oluşturulduğu varsayılır.

    ![HTTP isteğini Soru-Cevap Oluşturma](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web uygulaması bot

1. Bir LUO uygulamasını otomatik olarak içeren [bir "temel" Web uygulaması bot oluşturun](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0) . Programlama C# dilini seçin.

1. Web uygulaması bot oluşturulduktan sonra, Azure portal web uygulaması bot ' ı seçin.
1. Web uygulaması bot hizmeti gezinmede **uygulama ayarları** ' nı seçin, sonra da kullanılabilir ayarlar ' ın **uygulama ayarları** bölümüne gidin.
1. **Luisappıd** 'yi, önceki bölümde oluşturulan Luo uygulamasının değerine değiştirin ve ardından **Kaydet**' i seçin.


## <a name="change-code-in-basicluisdialogcs"></a>BasicLuisDialog.cs 'de kodu değiştirme
1. Azure portal web uygulaması bot 'ın **bot Management** bölümünden **Oluştur**' u seçin.
2. **Çevrimiçi Kod Düzenleyiciyi Aç**' ı seçin. Çevrimiçi düzen ortamı ile yeni bir tarayıcı sekmesi açılır. 
3. **Wwwroot** bölümünde, **iletişim kutuları** dizinini seçip **BasicLuisDialog.cs**açın.
4. **BasicLuisDialog.cs** dosyasının en üstüne bağımlılıklar ekleyin:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. Soru-Cevap Oluşturma yanıtının serisini kaldırmak için aşağıdaki sınıfları ekleyin:

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. Soru-Cevap Oluşturma hizmetine bir HTTP isteği oluşturmak için aşağıdaki sınıfı ekleyin. **Yetkilendirme** üstbilgisinin değeri, sözcükten sonraki bir boşluk ile `EndpointKey` sözcüklerini içerdiğine dikkat edin. JSON sonucunun önceki sınıflarda serisi kaldırıldı ve ilk yanıt döndürülür.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. BasicLuisDialog sınıfını değiştirin. Her bir LUIN amacı, **Luisamaç**ile donatılmış bir yönteme sahip olmalıdır. Dekorasyonu parametresi gerçek LUO amaç adıdır. Donatılmış Yöntem adı, okunabilirlik ve bakım için LUıN amaç _adı olmalıdır,_ ancak tasarım veya çalışma zamanında aynı olması gerekmez.  

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all KBs are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_resourceName = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>Bot 'ı oluşturma
1. Kod Düzenleyicisi 'nde, `build.cmd` ' a sağ tıklayıp **konsolundan Çalıştır**' ı seçin.

    ![konsoldan Çalıştır](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Kod görünümü, oluşturma işleminin ilerleme durumunu ve sonuçlarını gösteren bir Terminal penceresiyle değiştirilmiştir.

    ![Konsol derlemesi](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Bot 'ı test etme
Azure portal, bot 'ı test etmek için **Web sohbetinde test '** i seçin. Karşılık gelen bilgi bankasından yanıt almak için farklı amaçlardan iletiler yazın.

![Web sohbeti testi](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Soru-Cevap Oluşturma için iş sürekliliği planı oluşturma](../How-To/business-continuity-plan.md)
