---
title: İş akışlarında hataları ve özel durumları işleme
description: Azure Logic Apps kullanılarak oluşturulan otomatikleştirilmiş görevler ve iş akışlarında oluşan hataları ve özel durumları nasıl işleyeceğinizi öğrenin
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: estfan, logicappspm, azla
ms.date: 02/18/2021
ms.topic: article
ms.openlocfilehash: fbe797937021763bb97ca09e1da792d9a7010f9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702513"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Azure Logic Apps'teki hataları ve özel durumları işleme

Herhangi bir tümleştirme mimarisinin kapalı kalma süresini veya bağımlı sistemlerden kaynaklanan sorunları uygun şekilde işleme yöntemi bir zorluk oluşturabilirler. Sorunları ve hataları sorunsuz şekilde işleyen güçlü ve dayanıklı tümleştirmeler oluşturmanıza yardımcı olmak için Logic Apps hataları ve özel durumları işlemeye yönelik birinci sınıf bir deneyim sağlar.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Yeniden deneme ilkeleri

En temel özel durum ve hata işleme için, desteklenen yerlerde, her türlü eylemde veya tetikleyici için bir *yeniden deneme ilkesi* kullanabilirsiniz, örneğin, bkz. [http eylemi](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). Yeniden deneme ilkesi, özgün istek zaman aşımına uğradığında veya başarısız olduğunda, bir isteğin bir isteği yeniden deneme şeklini, bir 408, 429 veya 5xx yanıtı ile sonuçlanan bir istek olduğunu belirtir. Başka bir yeniden deneme ilkesi kullanılmazsa, varsayılan ilke kullanılır.

Yeniden deneme ilkesi türleri şunlardır:

| Tür | Description |
|------|-------------|
| **Varsayılanını** | Bu ilke, 7,5 saniye ölçeklendirilen ancak 5 ila 45 saniye arasında ölçeklenebilen, en fazla dört yeniden deneme aralığını üstel olarak *artırır* . |
| **Üstel Aralık**  | Bu ilke, sonraki isteği göndermeden önce üstel olarak büyüyen bir aralıktan seçilen rastgele aralığı bekler. |
| **Sabit aralık**  | Bu ilke, sonraki isteği göndermeden önce belirtilen aralığı bekler. |
| **Hiçbiri**  | İsteği yeniden gönderin. |
|||

Yeniden deneme ilkesi limitleri hakkında bilgi için bkz. [Logic Apps sınırları ve yapılandırması](../logic-apps/logic-apps-limits-and-config.md#http-limits).

### <a name="change-retry-policy"></a>Değişiklik yeniden deneme ilkesi

Farklı bir yeniden deneme ilkesi seçmek için şu adımları izleyin:

1. Mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. Bir eylem veya tetikleyici için **ayarları** açın.

1. Eylem veya tetikleyici yeniden deneme ilkelerini destekliyorsa, **yeniden deneme ilkesi** altında istediğiniz türü seçin.

Ya da yeniden deneme `inputs` ilkelerini destekleyen bir eylem veya tetikleyici için bölümünde yeniden deneme ilkesini el ile belirtebilirsiniz. Bir yeniden deneme ilkesi belirtmezseniz, eylem varsayılan ilkeyi kullanır.

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
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Gerekli*

| Değer | Tür | Description |
|-------|------|-------------|
| <*retry-ilke-tür*> | Dize | Kullanmak istediğiniz yeniden deneme ilkesi türü: `default` , `none` , `fixed` veya `exponential` |
| <*yeniden deneme aralığı*> | Dize | Değerin [ıso 8601 biçimini](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)kullanması gereken yeniden deneme aralığı. Varsayılan en düşük Aralık `PT5S` ve en yüksek Aralık `PT1D` . Üstel Aralık ilkesini kullandığınızda, farklı en düşük ve en yüksek değerleri belirtebilirsiniz. |
| <*yeniden deneme-denemeler*> | Tamsayı | 1 ile 90 arasında olması gereken yeniden deneme girişimi sayısı |
||||

*İsteğe bağlı*

| Değer | Tür | Description |
|-------|------|-------------|
| <*Minimum Aralık*> | Dize | Üstel Aralık ilkesi için [ıso 8601 biçiminde](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) rastgele seçilen Aralık için en küçük Aralık |
| <*Maksimum Aralık*> | Dize | Üstel Aralık ilkesi için [ıso 8601 biçiminde](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) rastgele seçilen Aralık için en büyük Aralık |
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

### <a name="none"></a>Yok

Eylem veya tetikleyicinin başarısız istekleri yeniden denemediğini belirtmek için <*retry-Policy-type*> olarak ayarlayın `none` .

### <a name="fixed-interval"></a>Sabit aralık

Bir sonraki isteği göndermeden önce eylemin veya tetikleyicinin belirtilen aralığı bekleyeceğini belirtmek için, <*retry-Policy-type*> olarak ayarlayın `fixed` .

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

Bir sonraki isteği göndermeden önce eylemin veya tetikleyicinin rastgele bir Aralık bekleyeceğini belirtmek için, <*retry-Policy-type*> olarak ayarlayın `exponential` . Rastgele Aralık, üstel olarak büyüyen bir aralıktan seçilir. İsteğe bağlı olarak, varsayılan en düşük ve en büyük aralıklarını, en az ve en fazla aralıklarını belirterek de geçersiz kılabilirsiniz.

**Rastgele değişken aralıkları**

Bu tabloda, Logic Apps her bir yeniden deneme için belirtilen aralıkta bulunan ve yeniden deneme sayısı dahil olmak üzere, nasıl tek bir rastgele değişken oluşturduğu gösterilmektedir:

| Yeniden deneme numarası | Minimum Aralık | Maksimum Aralık |
|--------------|------------------|------------------|
| 1 | en fazla (0, <*Minimum aralık*>) | Min (Interval, <*maksimum aralık*>) |
| 2 | maksimum (Aralık, <*Minimum aralık*>) | Min (2 * Aralık, <*maksimum aralık*>) |
| 3 | en fazla (2 * Aralık, <*Minimum aralık*>) | Min (4 * Aralık, <*maksimum aralık*>) |
| 4 | Max (4 * Aralık, <*Minimum aralık*>) | Min (8 * Aralık, <*maksimum aralık*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>"Sonra Çalıştır" davranışı değiştirilerek sorunları yakalayın ve işleyin

Logic App Designer 'a eylemler eklediğinizde, bu eylemleri çalıştırmak için kullanım sırasını örtük olarak bildirirsiniz. Bir eylem çalışmayı tamamladıktan sonra, bu eylem,, veya gibi bir durumla işaretlenir `Succeeded` `Failed` `Skipped` `TimedOut` . Her bir eylem tanımında özelliği, `runAfter` ilk tamamlaması gereken öncül eylemi ve ardıl eylem çalıştırılmadan önce bu öncül için izin verilen durumları belirtir. Varsayılan olarak, tasarımcıya eklediğiniz bir eylem yalnızca öncül durum ile tamamlandıktan sonra çalışır `Succeeded` .

Bir eylem işlenmeyen bir hata veya özel durum oluşturduğunda, eylem işaretlenir `Failed` ve herhangi bir ardıl eylem işaretlenir `Skipped` . Bu davranış, paralel dallara sahip bir eylem için olursa Logic Apps altyapısı, tamamlanma durumlarını belirlemede diğer dalları izler. Örneğin, bir dal bir eylemle sonlanıyorsa `Skipped` , bu dalın tamamlanma durumu bu atlanan eylemin öncül durumuna bağlıdır. Mantıksal uygulama çalıştırması tamamlandıktan sonra, altyapı tüm dal durumlarını değerlendirerek çalıştırmanın tüm durumunu belirler. Herhangi bir dal hata ile biterse tüm mantıksal uygulama çalıştırması işaretlenir `Failed` .

![Çalıştırma durumlarının nasıl değerlendirildiğini gösteren örnekler](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Bir eylemin öncüünün durumuna rağmen çalışmaya devam ettiğinden emin olmak için, [bir eylemin "sonra Çalıştır" davranışından](#customize-run-after) , öncül 'un başarısız durumlarını idare edin.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>"Sonra Çalıştır" davranışını özelleştirme

Bir eylemin "sonra Çalıştır" davranışını, öncül öğesinin durumu,,, `Succeeded` `Failed` `Skipped` `TimedOut` veya bu durumlardan herhangi biri olduğunda çalışacak şekilde özelleştirebilirsiniz. Örneğin, Excel Online öncül eyleminden sonra bir e-posta göndermek için, yerine `Add_a_row_into_a_table` `Failed` `Succeeded` "sonra Çalıştır" davranışını değiştirin, aşağıdakilerden birini izleyin:

* Tasarım görünümünde üç nokta (**...**) düğmesini seçin ve sonra **Çalıştır 'ı sonra Yapılandır**' ı seçin.

  ![Bir eylem için "sonra Çalıştır" davranışını yapılandırma](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  Eylem şekli, bu örnekteki **tabloya bir satır eklemek** için, öncül eylemi için gereken varsayılan durumu gösterir:

  ![Bir eylem için varsayılan "sonra Çalıştır" davranışı](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  "Sonra Çalıştır" davranışını istediğiniz durum olarak değiştirin, bu örnekte **başarısız oldu** :

  !["Sonra Çalıştır" davranışını "başarısız oldu" olarak değiştir](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Eylemin, öncül eyleminin ya da olarak işaretlenip işaretlenmediğini belirtmek için `Failed` `Skipped` `TimedOut` diğer durumları seçin:

  !["Sonra Çalıştır" davranışını başka bir duruma sahip olacak şekilde değiştir](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* Kod görünümünde, eylemin JSON tanımında, `runAfter` Bu söz dizimini izleyen özelliği düzenleyin:

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  Bu örnek için, `runAfter` özelliğini `Succeeded` olarak değiştirin `Failed` :

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  Eylemin, öncül eylemin veya olarak işaretli olup olmadığını belirtmek için `Failed` `Skipped` `TimedOut` diğer durumları ekleyin:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Kapsamları ve bunların sonuçlarını kullanarak eylemleri değerlendirin

Özelliği ile ayrı eylemlerden sonra çalışan adımlara benzer şekilde `runAfter` , eylemleri bir [kapsam](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)içinde birlikte gruplandırabilirsiniz. İşlemleri mantıksal olarak gruplamak, kapsamın toplam durumunu değerlendirmek ve bu duruma göre eylemler gerçekleştirmek istediğinizde kapsamları kullanabilirsiniz. Bir kapsamdaki tüm eylemler çalışmayı bitirdikten sonra, kapsamın kendisi kendi durumunu alır.

Bir kapsamın durumunu denetlemek için,, ve gibi bir mantıksal uygulamanın çalıştırma durumunu denetlemek için kullandığınız ölçütü kullanabilirsiniz `Succeeded` `Failed` .

Varsayılan olarak, tüm kapsamın eylemleri başarılı olduğunda, kapsamın durumu işaretlenir `Succeeded` . Bir kapsamdaki son eylem veya olarak sonuçlanırsa `Failed` `Aborted` , kapsamın durumu işaretlenir `Failed` .

Bir kapsamdaki özel durumları yakalamak `Failed` ve bu hataları işleyen eylemleri çalıştırmak için, `runAfter` Bu kapsam için özelliğini kullanabilirsiniz `Failed` . Bu şekilde, kapsamdaki *herhangi bir* eylem başarısız olursa ve `runAfter` Bu kapsamın özelliğini kullanırsanız, hata yakalamak için tek bir eylem oluşturabilirsiniz.

Kapsamlar için sınırlar [ve yapılandırma](../logic-apps/logic-apps-limits-and-config.md)konusuna bakın.

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Bağlam ve hatalara ilişkin sonuçları al

Bir kapsamdan hata yakalama yararlı olsa da, bağlamı tam olarak hangi eylemlerin başarısız olduğunu ve döndürülen hataları veya durum kodlarını anlamanıza yardımcı olmak isteyebilirsiniz. [ `result()` İşlevi](../logic-apps/workflow-definition-language-functions-reference.md#result) , kapsamdaki en üst düzey eylemlerden sonuçları, kapsamın adı olan tek bir parametre kabul ederek ve bu ilk düzey eylemlerden sonuçları içeren bir dizi döndürerek döndürür. Bu eylem nesneleri, `actions()` işlevin başlangıç zamanı, bitiş zamanı, durum, girişler, bağıntı kimlikleri ve çıktılar gibi işlev tarafından döndürülenlerle aynı öznitelikleri içerir. 

> [!NOTE]
> `result()`İşlevi, anahtar veya koşul eylemleri gibi daha derin iç içe geçmiş eylemlerden değil, *yalnızca* ilk düzey eylemlerden sonuçları döndürür.

Kapsamda başarısız olan eylemler hakkında bağlam almak için, `@result()` ifadeyi kapsamın adı ve özelliği ile birlikte kullanabilirsiniz `runAfter` . Döndürülen diziyi durumu olan eylemlere göre filtrelemek için `Failed` , [ **diziyi filtrele** eylemini](logic-apps-perform-data-operations.md#filter-array-action)ekleyebilirsiniz. Döndürülen başarısız bir eylem için bir eylem çalıştırmak için, döndürülen filtrelenmiş diziyi alıp [ **her döngü için**](../logic-apps/logic-apps-control-flow-loops.md)bir kullanın.

Aşağıda, "My_Scope" adlı kapsam eyleminde başarısız olan herhangi bir eylem için yanıt gövdesi ile bir HTTP POST isteği gönderen ayrıntılı bir açıklama verilmiştir:

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

1. "My_Scope" içindeki tüm eylemlerin sonucunu almak için, **diziyi filtrele** eylemi bu filtre ifadesini kullanır: `@result('My_Scope')`

1. **Filtre dizisi** koşulu, `@result()` değerine eşit olan herhangi bir öğedir `Failed` . Bu koşul, "My_Scope" olan tüm eylem sonuçlarına sahip diziyi yalnızca başarısız eylem sonuçlarıyla bir diziye filtreler.

1. `For_each` *Filtrelenmiş dizi* çıkışları üzerinde bir döngü eylemi gerçekleştirin. Bu adım, daha önce filtrelenen her başarısız eylem sonucu için bir eylem gerçekleştirir.

   Kapsamdaki tek bir eylem başarısız olursa, `For_each` döngüdeki eylemler yalnızca bir kez çalıştırılır. Birden çok başarısız eylem hata başına bir eyleme neden olur.

1. İfade olan öğe yanıtı gövdesinde bir HTTP GÖNDERISI gönderin `For_each` `@item()['outputs']['body']` .

   `@result()`Öğe şekli `@actions()` şekille aynıdır ve aynı şekilde ayrıştırılabilir.

1. Başarısız eylem adı ( `@item()['name']` ) ve başarısız çalıştırma istemci Izleme kimliği () ile iki özel üst bilgi ekleyin `@item()['clientTrackingId']` .

Başvuru için, bir `@result()` `name` `body` `clientTrackingId` Önceki örnekte ayrıştırılabilen, ve özelliklerini gösteren tek bir öğe örneği aşağıda verilmiştir. Bir `For_each` eylemin dışında, `@result()` Bu nesnelerin bir dizisini döndürür.

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

Farklı özel durum işleme desenleri gerçekleştirmek için, bu makalede daha önce açıklanan ifadeleri kullanabilirsiniz. Tüm filtrelenmiş hata dizisini kabul eden kapsam dışında tek bir özel durum işleme eylemini çalıştırmayı ve eylemi kaldırmayı tercih edebilirsiniz `For_each` . `\@result()`Daha önce açıklandığı gibi yanıttan diğer yararlı özellikleri de ekleyebilirsiniz.

## <a name="set-up-azure-monitor-logs"></a>Azure Izleyici günlüklerini ayarlama

Önceki desenler, bir çalıştırma içindeki hataları ve özel durumları işlemek için harika bir yoldur, ancak aynı zamanda çalıştırmanın kendisinden bağımsız olarak hataları tanımlayabilir ve bunlara yanıt verebilirsiniz. [Azure izleyici](../azure-monitor/overview.md) , tüm çalıştırma ve eylem durumları dahil olmak üzere tüm iş akışı olaylarını bir [Log Analytics çalışma alanına](../azure-monitor/logs/data-platform-logs.md), [azure depolama hesabına](../storage/blobs/storage-blobs-overview.md)veya [Azure Event Hubs](../event-hubs/event-hubs-about.md)göndermek için basit bir yol sağlar.

Çalıştırma durumlarını değerlendirmek için günlükleri ve ölçümleri izleyebilir ya da istediğiniz izleme aracında yayımlayabilirsiniz. Olası bir seçenek, tüm olayların [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)Event Hubs aracılığıyla akışını sağlar. Stream Analytics, tanılama günlüklerinden tüm bozukluklar, ortalamalar veya hatalara göre canlı sorgular yazabilirsiniz. Stream Analytics, kuyruklar, konular, SQL, Azure Cosmos DB veya Power BI gibi diğer veri kaynaklarına bilgi göndermek için kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Bir müşterinin Azure Logic Apps ile hata işlemeyi nasıl Derlemeleriyle ilgili bilgi](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Daha fazla Logic Apps örnek ve senaryo bulun](../logic-apps/logic-apps-examples-and-scenarios.md)
