---
title: Hata ve özel durum işleme-Azure Logic Apps
description: Azure Logic Apps hata ve özel durum işleme desenleri hakkında bilgi edinin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.date: 01/31/2018
ms.topic: article
ms.openlocfilehash: 828bea50a66b90f35843901ae2d7c703ffa58f2d
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208180"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Azure Logic Apps hataları ve özel durumları işleme

Herhangi bir tümleştirme mimarisinin kapalı kalma süresini veya bağımlı sistemlerden kaynaklanan sorunları uygun şekilde işleme yöntemi bir zorluk oluşturabilirler. Sorunları ve hataları sorunsuz şekilde işleyen güçlü ve dayanıklı tümleştirmeler oluşturmanıza yardımcı olmak için Logic Apps hataları ve özel durumları işlemeye yönelik birinci sınıf bir deneyim sağlar. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>İlkeleri yeniden deneme

En temel özel durum ve hata işleme için, desteklenen yerlerde her türlü eylemde veya tetikleyici için bir *yeniden deneme ilkesi* kullanabilirsiniz. Yeniden deneme ilkesi, özgün istek zaman aşımına uğradığında veya başarısız olduğunda, bir isteğin bir isteği yeniden deneme şeklini, bir 408, 429 veya 5xx yanıtı ile sonuçlanan bir istek olduğunu belirtir. Başka bir yeniden deneme ilkesi kullanılmazsa, varsayılan ilke kullanılır. 

Yeniden deneme ilkesi türleri şunlardır: 

| Type | Açıklama | 
|------|-------------| 
| **Varsayılan** | Bu ilke, 7,5 saniye ölçeklendirilen ancak 5 ila 45 saniye arasında ölçeklenebilen, en fazla dört yeniden deneme aralığını üstel olarak *artırır* . | 
| **Üstel Aralık**  | Bu ilke, sonraki isteği göndermeden önce üstel olarak büyüyen bir aralıktan seçilen rastgele aralığı bekler. | 
| **Sabit Aralık**  | Bu ilke, sonraki isteği göndermeden önce belirtilen aralığı bekler. | 
| **Yok.**  | İsteği yeniden gönderin. | 
||| 

Yeniden deneme ilkesi limitleri hakkında bilgi için bkz. [Logic Apps sınırları ve yapılandırması](../logic-apps/logic-apps-limits-and-config.md#request-limits). 

### <a name="change-retry-policy"></a>Değişiklik yeniden deneme ilkesi

Farklı bir yeniden deneme ilkesi seçmek için şu adımları izleyin: 

1. Mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın. 

2. Bir eylem veya tetikleyici için **ayarları** açın.

3. Eylem veya tetikleyici yeniden deneme ilkelerini destekliyorsa, **yeniden deneme ilkesi**altında istediğiniz türü seçin. 

Ya da yeniden deneme ilkelerini destekleyen bir eylem veya tetikleyici için `inputs` bölümünde yeniden deneme ilkesini el ile belirtebilirsiniz. Bir yeniden deneme ilkesi belirtmezseniz, eylem varsayılan ilkeyi kullanır.

```json
"<action-name>": {
   "type": "<action-type>", 
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximun-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Gerekli*

| Value | Type | Açıklama |
|-------|------|-------------|
| <*retry-policy-type*> | String | Kullanmak istediğiniz yeniden deneme ilkesi türü: `default`, `none`, `fixed`veya`exponential` | 
| <*retry-interval*> | String | Değerin [ıso 8601 biçimini](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)kullanması gereken yeniden deneme aralığı. Varsayılan en düşük Aralık `PT5S` ve en yüksek `PT1D`Aralık. Üstel Aralık ilkesini kullandığınızda, farklı en düşük ve en yüksek değerleri belirtebilirsiniz. | 
| <*retry-attempts*> | Integer | 1 ile 90 arasında olması gereken yeniden deneme girişimi sayısı | 
||||

*Seçim*

| Value | Type | Açıklama |
|-------|------|-------------|
| <*minimum-interval*> | Dize | Üstel Aralık ilkesi için [ıso 8601 biçiminde](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) rastgele seçilen Aralık için en küçük Aralık | 
| <*maximum-interval*> | Dize | Üstel Aralık ilkesi için [ıso 8601 biçiminde](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) rastgele seçilen Aralık için en büyük Aralık | 
|||| 

Farklı ilke türleri hakkında daha fazla bilgi aşağıda verilmiştir.

<a name="default-retry"></a>

### <a name="default"></a>Varsayılan

Bir yeniden deneme ilkesi belirtmezseniz, eylem, 7,5 saniye ölçeklendirilen aralıkları artırarak, en fazla dört yeniden deneme Gönderen, aslında [üstel bir Aralık ilkesi](#exponential-interval) olan varsayılan ilkeyi kullanır. Aralık 5 ile 45 saniye arasında yapılır. 

Eylemde veya tetikleyicide açıkça tanımlanmamışsa, örnek bir HTTP eyleminde varsayılan ilkenin nasıl davrandığı aşağıda verilmiştir:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Yok.

Eylem veya tetikleyicinin başarısız istekleri yeniden denemediğini belirtmek için <*retry-Policy-type*> olarak `none`ayarlayın.

### <a name="fixed-interval"></a>Sabit Aralık

Bir sonraki isteği göndermeden önce eylemin veya tetikleyicinin belirtilen aralığı bekleyeceğini belirtmek için, <*retry-Policy-type*> olarak `fixed`ayarlayın.

*Örnek*

Bu yeniden deneme ilkesi, her bir girişim arasında 30 saniyelik bir gecikmeyle en son başarısız istekten sonraki iki kez en son haberleri almaya çalışır:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Üstel Aralık

Bir sonraki isteği göndermeden önce eylemin veya tetikleyicinin rastgele bir Aralık bekleyeceğini belirtmek için, <*retry-Policy-type*> olarak `exponential`ayarlayın. Rastgele Aralık, üstel olarak büyüyen bir aralıktan seçilir. İsteğe bağlı olarak, varsayılan en düşük ve en büyük aralıklarını, en az ve en fazla aralıklarını belirterek de geçersiz kılabilirsiniz.

**Rastgele değişken aralıkları**

Bu tabloda, Logic Apps her bir yeniden deneme için belirtilen aralıkta bulunan ve yeniden deneme sayısı dahil olmak üzere, nasıl tek bir rastgele değişken oluşturduğu gösterilmektedir:

| Yeniden deneme numarası | Minimum Aralık | Maksimum Aralık |
|--------------|------------------|------------------|
| 1\. | en fazla (0, <*Minimum aralık*>) | Min (Interval, <*maksimum aralık*>) |
| 2 | maksimum (Aralık, <*Minimum aralık*>) | Min (2 * Aralık, <*maksimum aralık*>) |
| 3 | en fazla (2 * Aralık, <*Minimum aralık*>) | Min (4 * Aralık, <*maksimum aralık*>) |
| 4 | Max (4 * Aralık, <*Minimum aralık*>) | Min (8 * Aralık, <*maksimum aralık*>) |
| .... | .... | .... | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>RunAfter özelliği ile sorunları yakala ve işle

Her mantıksal uygulama eylemi, bu eylem başlamadan önce tamamlaması gereken eylemleri bildirir ve iş akışınızdaki adımların sırasını nasıl belirttiğine benzer. Bir eylem tanımında, **Runafter** özelliği bu sıralamayı tanımlar ve eylemi hangi eylemlerin ve eylem durumlarının yürütebileceğini açıklayan bir nesnedir.

Varsayılan olarak, mantıksal uygulama tasarımcısında eklediğiniz tüm eylemler, önceki adımın sonucu **başarılı**olduğunda önceki adımdan sonra çalıştırılacak şekilde ayarlanır. Ancak, **Runafter** değerini özelleştirmek için, önceki eylemler neden **başarısız**, **atlandı**veya bu değerlerin bir birleşimi olduğunda eylemlerin tetiklenmesi gerekir. Örneğin, belirli bir **Insert_Row** eylemi başarısız olduktan sonra belirli bir Service Bus konusuna bir öğe eklemek için, bu örnek şu şekilde çalışma açıklamasını kullanabilirsiniz:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

**Insert_Row** eylem durumu **başarısız**olduğunda **runafter** özelliği çalıştırılacak şekilde ayarlanır. Eylem durumu **başarılı**, **başarısız**veya atlandıysa eylemi çalıştırmak için şu sözdizimini kullanın:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Önceki bir eylem başarısız olduktan sonra başarıyla çalışan ve son görüntülenen eylemler **başarılı**olarak işaretlenir. Bu davranış, bir iş akışındaki tüm sorunları başarıyla yakalayabiliyorsanız, çalıştırmanın kendisi **başarılı**olarak işaretlenir anlamına gelir.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Kapsamları ve bunların sonuçlarını kullanarak eylemleri değerlendirin

**Runafter** özelliğinden bağımsız eylemlerden sonra çalışan adımlara benzer şekilde, eylemleri bir [kapsam](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)içinde gruplandırabilirsiniz. İşlemleri mantıksal olarak gruplamak, kapsamın toplam durumunu değerlendirmek ve bu duruma göre eylemler gerçekleştirmek istediğinizde kapsamları kullanabilirsiniz. Bir kapsamdaki tüm eylemler çalışmayı bitirdikten sonra, kapsamın kendisi kendi durumunu alır. 

Bir kapsamın durumunu denetlemek için, bir mantıksal uygulamanın çalıştırma durumunu denetlemek için kullanılan ölçütü ( **başarılı**, **başarısız**vb.) kullanabilirsiniz. 

Varsayılan olarak, tüm kapsamın eylemleri başarılı olduğunda, kapsamın durumu **başarılı**olarak işaretlenir. Bir kapsamdaki son eylem **başarısız** veya **durdurulmuş**olarak sonuçlanırsa, kapsamın durumu **başarısız**olarak işaretlenir. 

**Başarısız** bir kapsamda özel durumları yakalamak ve bu hataları işleyen eylemleri çalıştırmak Için, **başarısız** olan kapsam için **runafter** özelliğini kullanabilirsiniz. Bu şekilde, kapsamdaki *herhangi bir* eylem başarısız olursa ve bu kapsam Için **runafter** özelliğini kullanırsanız, hata yakalamak için tek bir eylem oluşturabilirsiniz.

Kapsamlar için sınırlar [ve yapılandırma](../logic-apps/logic-apps-limits-and-config.md)konusuna bakın.

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Bağlam ve hatalara ilişkin sonuçları al

Bir kapsamdan hata yakalama yararlı olsa da, bağlamı tam olarak hangi eylemlerin başarısız olduğunu ve döndürülen hataları veya durum kodlarını anlamanıza yardımcı olmak isteyebilirsiniz.

[`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) İşlevi, bir kapsamdaki tüm eylemlerin sonuçları hakkında bağlam sağlar. `result()` İşlevi, kapsamın adı olan tek bir parametreyi kabul eder ve bu kapsam içindeki tüm eylem sonuçlarını içeren bir dizi döndürür. Bu eylem nesneleri, `@actions()` nesnenin başlangıç zamanı, bitiş zamanı, durum, girişler, bağıntı kimlikleri ve çıktılar gibi nesneyle aynı öznitelikleri içerir. Kapsam içinde başarısız olan herhangi bir eylemin bağlamını göndermek için, `@result()` `runAfter` özelliği ile kolayca bir ifadeyi eşleştirin.

**Başarısız** olan bir kapsamda her eylem için bir eylem çalıştırmak ve sonuçların dizisini başarısız eylemlere göre filtrelemek için, bir `@result()` ifadeyi bir [**filtre dizisi**](../connectors/connectors-native-query.md) eylemiyle ve [**her döngü için**](../logic-apps/logic-apps-control-flow-loops.md) bir ile eşleştirin. Her bir döngü **için** kullanarak filtrelenmiş sonuç dizisini alabilir ve her hata için bir eylem gerçekleştirebilirsiniz.

Aşağıda, "My_Scope" kapsamında başarısız olan herhangi bir eylem için yanıt gövdesi ile bir HTTP POST isteği gönderen ayrıntılı bir açıklama verilmiştir.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Bu örnekte neler olduğunu açıklayan ayrıntılı bir anlatım aşağıda verilmiştir:

1. "My_Scope" içindeki tüm eylemlerin sonucunu almak için, **diziyi filtrele** eylemi bu filtre ifadesini kullanır:`@result('My_Scope')`

2. **Filtre dizisi** koşulu, durumu **başarısız**olan `@result()` herhangi bir öğedir. Bu koşul, "My_Scope" olan tüm eylem sonuçlarına sahip diziyi yalnızca başarısız eylem sonuçlarıyla bir diziye filtreler.

3. *Filtrelenen dizi* çıktılarında her döngü eylemi **için** bir gerçekleştirin. Bu adım, daha önce filtrelenen her başarısız eylem sonucu için bir eylem gerçekleştirir.

   Kapsamda tek bir eylem başarısız olduysa, **her** bir döngüdeki eylemler yalnızca bir kez çalışır. 
   Birden çok başarısız eylem hata başına bir eyleme neden olur.

4. `@item()['outputs']['body']` İfadesi olan her öğe yanıt gövdesi **için** bir http gönderisi gönderin. 

   Öğe şekli `@actions()` şekille aynıdır ve aynı şekilde ayrıştırılabilir. `@result()`

5. Başarısız eylem adı (`@item()['name']`) ve başarısız çalıştırma istemci izleme kimliği (`@item()['clientTrackingId']`) ile iki özel üst bilgi ekleyin.

Başvuru için, önceki örnekte ayrıştırılmış **ad**, **gövde**ve `@result()` **clienttrackingıd** özelliklerini gösteren tek bir öğe örneği aşağıda verilmiştir. **Her** bir eylemin dışında, `@result()` bu nesnelerin bir dizisini döndürür.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

Farklı özel durum işleme desenleri gerçekleştirmek için, bu makalede daha önce açıklanan ifadeleri kullanabilirsiniz. Tüm filtrelenmiş hata dizisini kabul eden kapsam dışında tek bir özel durum işleme eylemini çalıştırmayı ve **her eylem için** öğesini kaldırmayı tercih edebilirsiniz. Daha önce açıklandığı gibi  **\@Result ()** yanıtından diğer yararlı özellikleri de ekleyebilirsiniz.

## <a name="azure-diagnostics-and-metrics"></a>Azure Tanılama ve ölçümler

Önceki desenler, bir çalıştırma içindeki hataları ve özel durumları işlemek için harika bir yoldur, ancak aynı zamanda çalıştırmanın kendisinden bağımsız olarak hataları tanımlayabilir ve bunlara yanıt verebilirsiniz. 
[Azure tanılama](../logic-apps/logic-apps-monitor-your-logic-apps.md) , tüm çalıştırma ve eylem durumları dahil olmak üzere tüm iş akışı olaylarını bir Azure depolama hesabına veya Azure Event Hubs oluşturulmuş bir olay hub 'ına göndermek için basit bir yol sağlar. 

Çalıştırma durumlarını değerlendirmek için günlükleri ve ölçümleri izleyebilir ya da istediğiniz izleme aracında yayımlayabilirsiniz. Olası bir seçenek, tüm olayların [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)Event Hubs aracılığıyla akışını sağlar. Stream Analytics, tanılama günlüklerinden tüm bozukluklar, ortalamalar veya hatalara göre canlı sorgular yazabilirsiniz. Stream Analytics, kuyruklar, konular, SQL, Azure Cosmos DB veya Power BI gibi diğer veri kaynaklarına bilgi göndermek için kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Bir müşterinin Azure Logic Apps ile hata işlemeyi nasıl Derlemeleriyle ilgili bilgi](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Daha fazla Logic Apps örnek ve senaryo bulun](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
