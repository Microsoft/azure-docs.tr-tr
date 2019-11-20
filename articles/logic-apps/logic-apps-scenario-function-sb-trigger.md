---
title: Azure Işlevleri ile mantıksal uygulamaları çağırma-Azure Logic Apps
description: Azure Service Bus dinleyerek Logic Apps 'i çağıran veya tetikleyen Azure işlevleri oluşturun
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: c65a0464bbad6dbaca51dbc5bbc0d84adbd605d7
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904637"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Azure Işlevleri 'ni ve Azure Service Bus kullanarak mantıksal uygulamaları çağırın veya tetikleyin

Uzun süre çalışan bir dinleyici veya görev dağıtmanız gerektiğinde, bir mantıksal uygulamayı tetiklemek için [Azure işlevleri](../azure-functions/functions-overview.md) 'ni kullanabilirsiniz. Örneğin, bir [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) kuyruğu üzerinde dinleme yapan bir Azure işlevi oluşturabilir ve bir mantıksal uygulamayı anında iletme tetikleyicisi olarak tetikleyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Bir Azure Service Bus ad alanı. Bir ad alanınız yoksa, [önce ad alanınızı oluşturun](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Azure işlevleri için kapsayıcı olan bir Azure işlev uygulaması. Bir işlev uygulamanız yoksa, [önce işlev uygulamanızı oluşturun](../azure-functions/functions-create-first-azure-function.md)ve çalışma zamanı yığını olarak .net ' i seçtiğinizden emin olun.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

## <a name="create-logic-app"></a>Mantıksal uygulama oluşturma

Bu senaryo için, tetiklemek istediğiniz her mantıksal uygulamayı çalıştıran bir işleviniz vardır. İlk olarak, bir HTTP istek tetikleyicisi ile başlayan bir mantıksal uygulama oluşturun. İşlevi, her bir kuyruk iletisi alındığında bu uç noktayı çağırır.

1. [Azure Portal](https://portal.azure.com)oturum açın ve boş mantıksal uygulama oluşturun.

   Logic Apps 'e yeni başladıysanız [hızlı başlangıç: ilk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)konusunu inceleyin.

1. Arama kutusuna `http request` yazın. Tetikleyiciler listesinden **BIR http isteği alındığında** tetikleyiciyi seçin.

   ![Tetikleyiciyi seçin](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Istek tetikleyicisi ile isteğe bağlı olarak, kuyruk iletisiyle birlikte kullanılacak bir JSON şeması girebilirsiniz. JSON şemaları, Logic App Designer 'ın giriş verilerinin yapısını anlamasına yardımcı olur ve iş akışınızda, çıktıları sizin için daha kolay hale getirir.

1. Bir şema belirtmek için, şemayı **Istek GÖVDESI JSON şemasına** girin, örneğin:

   ![JSON şeması belirt](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Bir şemanız yoksa ancak JSON biçiminde bir örnek yüküyle karşılaşırsanız, bu yükün bir şemasını oluşturabilirsiniz.

   1. Istek tetikleyicisinde, **şema oluşturmak için örnek yük kullan**' ı seçin.

   1. **Örnek BIR JSON yükü girin veya yapıştırın**bölümüne örnek yükünüzü girip **bitti**' yi seçin.

      ![Örnek yük girin](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Bu örnek yük, tetikleyicide görüntülenen bu şemayı oluşturur:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Sıra iletisini aldıktan sonra çalıştırmak istediğiniz diğer eylemleri ekleyin.

   Örneğin, Office 365 Outlook Bağlayıcısı ile bir e-posta gönderebilirsiniz.

1. Bu mantıksal uygulamadaki tetikleyici için geri çağırma URL 'sini üreten mantıksal uygulamanızı kaydedin. Daha sonra, Azure Service Bus kuyruğu tetikleyicisinin kodunda bu geri çağırma URL 'sini kullanırsınız.

   Geri çağırma URL 'si **http post URL 'si** özelliğinde görüntülenir.

   ![Tetikleyici için geri çağırma URL 'SI oluşturuldu](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Azure işlevi oluşturma

Sonra tetikleyici olarak davranan ve kuyruğu dinleyen işlevi oluşturun.

1. Azure portal, zaten açık değilse, işlev uygulamanızı açın ve genişletin. 

1. İşlev uygulamanızın adı altında **işlevler**' i genişletin. **İşlevler** bölmesinde **yeni işlev**' ı seçin.

   !["Işlevler" i genişletin ve "yeni işlev" ı seçin](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Çalışma zamanı yığını olarak .NET seçtiğiniz yeni bir işlev uygulaması oluşturup oluşturamayacağını veya var olan bir işlev uygulamasını kullandığınızı temel alarak bu şablonu seçin.

   * Yeni işlev uygulamaları için şu şablonu seçin: **Service Bus kuyruğu tetikleyicisi**

     ![Yeni işlev uygulaması için şablon seçin](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Mevcut bir işlev uygulaması için şu şablonu seçin: **Service Bus kuyruğu tetikleyicisi- C#**

     ![Mevcut işlev uygulaması için şablon seçin](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. **Azure Service Bus kuyruğu tetikleyicisi** bölmesinde, Tetikleyiciniz için bir ad girin ve Azure Service Bus SDK `OnMessageReceive()` dinleyicisini kullanan ve sıra için **Service Bus bağlantısını** ayarlayın ve **Oluştur**' u seçin.

1. Kuyruk iletisini tetikleyici olarak kullanarak, önceden oluşturulmuş mantıksal uygulama uç noktasını çağırmak için temel bir işlev yazın. İşlevinizi yazmadan önce şu hususları gözden geçirin:

   * Bu örnek `application/json` ileti içerik türünü kullanır, ancak bu türü gerektiği gibi değiştirebilirsiniz.
   
   * Eşzamanlı çalışan işlevlerin, yüksek birimlerin veya ağır yükün olması nedeniyle, `using` ifadesiyle [HttpClient sınıfını](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) örneklemeyi ve Istek başına HttpClient örnekleri oluşturmayı önleyin. Daha fazla bilgi için bkz. [Esnek http isteklerini uygulamak Için HttpClientFactory kullanma](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core).
   
   * Mümkünse, HTTP istemcilerinin örneğini yeniden kullanın. Daha fazla bilgi için bkz. [Azure işlevlerinde bağlantıları yönetme](../azure-functions/manage-connections.md).

   Bu örnekte [`Task.Run` yöntemi](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task.run) [zaman uyumsuz](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async) modda kullanılmaktadır. Daha fazla bilgi için bkz. [Async ve await Ile zaman uyumsuz programlama](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/).

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. İşlevi test etmek için, [Service Bus Gezgini](https://github.com/paolosalvatori/ServiceBusExplorer)gibi bir araç kullanarak bir kuyruk iletisi ekleyin.

   Mantıksal uygulama, işlev iletiyi aldıktan hemen sonra tetiklenir.

## <a name="next-steps"></a>Sonraki adımlar

* [HTTP uç noktalarını kullanarak iş akışlarını çağırma, tetikleme veya iç içe geçme](../logic-apps/logic-apps-http-endpoint.md)
