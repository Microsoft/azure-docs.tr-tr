---
title: Durumu denetleme, günlüğü ayarlama ve uyarıları alma-Azure Logic Apps
description: Durumu izleyin, tanılama verilerini günlüğe kaydedin ve Azure Logic Apps uyarılarını ayarlayın
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 07/21/2017
ms.openlocfilehash: 336e2ac93a954c35b7afc8dbb98dd1fca1838985
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680291"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Durumu izleyin, tanılama günlüğünü ayarlayın ve Azure Logic Apps uyarılarını açın

[Mantıksal uygulama oluşturup çalıştırdıktan](../logic-apps/quickstart-create-first-logic-app-workflow.md) sonra çalıştırma geçmişini, tetikleyici geçmişini, durumunu ve performansını denetleyebilirsiniz. Gerçek zamanlı olay izleme ve daha zengin hata ayıklama için mantıksal uygulamanız için [tanılama günlüğü](#azure-diagnostics) ayarlayın. Bu şekilde tetikleyici olayları, çalıştırma olayları ve eylem olayarı gibi [olayları bulabilir ve görüntüleyebilirsiniz](#find-events). Ayrıca bu [tanılama verilerini diğer hizmetlerle](#extend-diagnostic-data), örneği Azure Depolama ve Azure Event Hubs ile de kullanabilirsiniz. 

Hataları veya olası diğer sorunlar hakkında bildirim almak için [uyarıları](#add-azure-alerts)ayarlayın. Örneğin, "Beş ' dan fazla çalıştırma başarısız olduğunda" bir uyarı oluşturabilirsiniz. Ayrıca, [Azure Tanılama olay ayarlarını ve özelliklerini](#diagnostic-event-properties)kullanarak, izleme, izleme ve günlüğü programlı bir şekilde ayarlayabilirsiniz.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Mantıksal uygulamanız için çalıştırma ve tetikleme geçmişini görüntüleme

1. [Azure Portal](https://portal.azure.com)mantıksal uygulamanızı bulmak için, ana Azure menüsünde **tüm hizmetler**' i seçin. Arama kutusuna "Logic Apps" yazın ve **Logic Apps**' i seçin.

   ![Mantıksal uygulamanızı bulun](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Azure portal, Azure aboneliğinizle ilişkili tüm mantıksal uygulamaları gösterir. 

2. Mantıksal uygulamanızı seçip **genel bakış**' ı seçin.

   Azure portal, mantıksal uygulamanız için çalıştırma geçmişi ve tetikleme geçmişini gösterir. Örnek:

   ![Mantıksal uygulama geçmişi ve tetikleme geçmişini çalıştırır](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Çalıştırma geçmişi** , mantıksal uygulamanız için tüm çalıştırmaları gösterir. 
   * **Tetikleyici geçmişi** , mantıksal uygulamanız için tüm tetikleyici etkinliklerini gösterir.

   Durum açıklamaları için bkz. [mantıksal uygulamanızda sorun giderme](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Beklenen verileri bulamazsanız, araç çubuğunda **Yenile**' yi seçin.

3. Belirli bir çalışmadan ilgili adımları görüntülemek için, çalışma **geçmişi**altında o çalıştırmayı seçin. 

   İzleyici görünümü, çalıştırılan her adımı gösterir. Örnek:

   ![Belirli bir çalıştırma için Eylemler](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Çalıştırma hakkında daha fazla bilgi edinmek için, **çalıştırma ayrıntıları**' nı seçin. Bu bilgiler, çalıştırma için adımları, durumu, girişleri ve çıkışları özetler. 

   !["Ayrıntıları Çalıştır" ı seçin](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Örneğin, [Logic Apps için REST API](https://docs.microsoft.com/rest/api/logic)kullandığınızda ihtiyacınız olabilecek ÇALıŞTıRMANıN **bağıntı kimliğini**alabilirsiniz.

5. Belirli bir adımla ilgili ayrıntıları almak için bu adımı seçin. Artık girişler, çıktılar ve bu adımla ilgili tüm hatalar gibi ayrıntıları gözden geçirebilirsiniz. Örnek:

   ![Adım ayrıntıları](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Tüm çalışma zamanı ayrıntıları ve olayları Logic Apps hizmeti içinde şifrelenir. Yalnızca bir Kullanıcı bu verileri görüntülemek istediğinde şifresi çözülür. [Azure rol tabanlı Access Control (RBAC)](../role-based-access-control/overview.md)ile bu olaylara erişimi de denetleyebilirsiniz.

6. Belirli bir tetikleyici olayı hakkındaki ayrıntıları almak için **genel bakış** bölmesine dönün. **Tetikleyici geçmişi**altında tetikleyici olayını seçin. Artık giriş ve çıkış gibi ayrıntıları gözden geçirebilirsiniz, örneğin:

   ![Olay çıkış ayrıntılarını tetikleme](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Mantıksal uygulamanız için tanılama günlüğünü açma

Çalışma zamanı ayrıntıları ve olayları ile daha zengin hata ayıklama için, [Azure izleyici günlükleri](../log-analytics/log-analytics-overview.md)ile tanılama günlüğü ayarlayabilirsiniz. Azure Izleyici, Azure 'da bulut ve şirket içi ortamlarınızı izleyen ve kullanılabilirlik ve performanslarını korumanıza yardımcı olan bir hizmettir. 

Başlamadan önce bir Log Analytics çalışma alanına sahip olmanız gerekir. [Log Analytics çalışma alanı oluşturmayı](../azure-monitor/learn/quick-create-workspace.md)öğrenin.

1. [Azure Portal](https://portal.azure.com)mantıksal uygulamanızı bulun ve seçin. 

2. Mantıksal uygulama dikey penceresi menüsünde, **izleme**altında **Tanılama >** **Tanılama ayarları**' nı seçin.

   ![Izleme, tanılama ve tanılama ayarlarına git](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. **Tanılama ayarları**altında **Açık**' ı seçin.

   ![tanılama günlüklerini aç](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Şimdi Log Analytics çalışma alanını ve günlüğe kaydetme için olay kategorisini gösterildiği gibi seçin:

   1. **Log Analytics gönder**' i seçin. 
   2. **Log Analytics**altında **Yapılandır**' ı seçin. 
   3. **OMS çalışma alanları**altında, günlüğe kaydetme için kullanılacak çalışma alanını seçin.
      > [!NOTE]
      > OMS çalışma alanları artık Log Analytics çalışma alanları olarak adlandırılır.
   4. **Günlük**altında **WorkflowRuntime** kategorisini seçin.
   5. Ölçüm aralığını seçin.
   6. İşiniz bittiğinde **Kaydet**’i seçin.

   ![Log Analytics çalışma alanı ve verileri günlüğe kaydetme için seçin](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Şimdi, tetikleyici olayları, çalıştırma olayları ve eylem olayları için olayları ve diğer verileri bulabilirsiniz.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Mantıksal uygulamanıza yönelik olayları ve verileri bulun

Mantıksal uygulamanızdaki olayları, tetikleyici olayları, çalıştırma olayları ve eylem olayları gibi bulmak ve görüntülemek için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com), **tüm hizmetler**' i seçin. "Log Analytics" araması yapın ve ardından **Log Analytics** aşağıda gösterildiği gibi seçin:

   !["Log Analytics" öğesini seçin](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. **Log Analytics**altında Log Analytics çalışma alanınızı bulun ve seçin. 

   ![Log Analytics çalışma alanınızı seçin](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. **Yönetim**altında **günlük araması**' nı seçin.

   !["Günlük aramasını" seçin](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. Arama kutusunda, bulmak istediğiniz bir alan belirtin ve **ENTER**tuşuna basın. Yazmaya başladığınızda, kullanabileceğiniz olası eşleşmeleri ve işlemleri görürsünüz. 

   Örneğin, gerçekleşen ilk 10 olayı bulmak için bu arama sorgusunu girin ve seçin: **arama kategorisi = = "WorkflowRuntime" | sınır 10**

   ![Arama dizesini girin](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   [Azure izleyici günlüklerinde veri bulma](../log-analytics/log-analytics-log-searches.md)hakkında daha fazla bilgi edinin.

5. Sonuçlar sayfasında, sol çubukta, görüntülemek istediğiniz zaman dilimini seçin.
Filtre ekleyerek sorgunuzu iyileştirmek için **+ Ekle**' yi seçin.

   ![Sorgu sonuçları için zaman dilimini seçin](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. Filtre **Ekle**' nin altında, istediğiniz filtreyi bulmak için filtre adını girin. Filtreyi seçin ve **+ Ekle**' yi seçin.

   Bu örnek, **AzureDiagnostics**altındaki başarısız olayları bulmak için "Status" sözcüğünü kullanır.
   Burada **status_s** filtresi zaten seçilidir.

   ![Filtre Seç](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. Sol çubukta, kullanmak istediğiniz filtre değerini seçin ve **Uygula**' yı seçin.

   ![Filtre değeri seçin, "Uygula" yı seçin](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Şimdi oluşturmakta olduğunuz sorguya geri dönün. Sorgunuz, seçtiğiniz filtre ve değer ile güncelleştirilir. Önceki sonuçlarınız artık çok filtrelenmiştir.

   ![Filtrelenmiş sonuçlarla sorgunuza geri dönme](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. Sorgunuzu daha sonra kullanmak üzere kaydetmek için **Kaydet**' i seçin. [Sorgunuzu kaydetmeyi](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query)öğrenin.

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Tanılama verilerini diğer hizmetlerle nasıl ve nerede kullanacağınızı genişletin

Azure Izleyici günlükleriyle birlikte, mantıksal uygulamanızın tanılama verilerini diğer Azure hizmetleriyle nasıl kullanacağınızı genişletebilirsiniz, örneğin: 

* [Azure depolama 'da Azure Tanılama günlüklerini arşivleme](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Azure Event Hubs akış Azure Tanılama günlükleri](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

Daha sonra [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) ve [Power BI](../azure-monitor/platform/powerbi.md)gibi diğer hizmetlerden telemetri ve analiz kullanarak gerçek zamanlı izleme sağlayabilirsiniz. Örnek:

* [Event Hubs veri akışı Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Stream Analytics ile akış verilerini çözümleme ve Power BI bir gerçek zamanlı analiz panosu oluşturma](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Ayarlamak istediğiniz seçeneklere bağlı olarak, önce [bir Azure depolama hesabı oluşturun](../storage/common/storage-create-storage-account.md) veya [bir Azure Olay Hub 'ı oluşturun](../event-hubs/event-hubs-create.md). Ardından tanılama verilerini göndermek istediğiniz seçenekleri belirleyin:

![Verileri Azure depolama hesabına veya Olay Hub 'ına gönder](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Saklama süreleri yalnızca bir depolama hesabı kullanmayı seçtiğinizde geçerlidir.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Mantıksal uygulamanız için uyarıları ayarlama

Mantıksal uygulamanıza yönelik belirli ölçümleri izlemek veya eşikleri aşmış olması için, [Azure 'da uyarıları](../azure-monitor/platform/alerts-overview.md)ayarlayın. [Azure 'da ölçümler](../monitoring-and-diagnostics/monitoring-overview-metrics.md)hakkında bilgi edinin. 

Uyarıları [Azure izleyici günlükleri](../log-analytics/log-analytics-overview.md)olmadan ayarlamak için aşağıdaki adımları izleyin. Daha gelişmiş uyarılar ölçütü ve eylemleri için [Azure izleyici günlüklerini de ayarlayın](#azure-diagnostics) .

1. Mantıksal uygulama dikey penceresi menüsünde, **izleme**altında **tanılama**  > **Uyarı  >  kuralları** ' nı seçin ve burada gösterildiği gibi**uyarı ekleyin** :

   ![Mantıksal uygulamanız için bir uyarı ekleyin](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. **Uyarı kuralı ekle** dikey penceresinde, uyarıyı gösterildiği gibi oluşturun:

   1. **Kaynak**altında, henüz seçili değilse mantıksal uygulamanızı seçin. 
   2. Uyarınız için bir ad ve açıklama sağlayın.
   3. İzlemek istediğiniz bir **ölçüm** veya olay seçin.
   4. Bir **koşul**seçin, ölçüm Için bir **eşik** belirtin ve bu ölçümü izlemek için **dönemi** seçin.
   5. Uyarı için posta gönderilmesini isteyip istemediğinizi seçin. 
   6. Uyarının gönderilmesi için herhangi bir e-posta adresi belirtin. 
   Ayrıca, uyarıyı göndermek istediğiniz yere bir Web kancası URL 'SI de belirtebilirsiniz.

   Örneğin, bu kural bir saatte beş veya daha fazla çalıştırma başarısız olduğunda bir uyarı gönderir:

   ![Ölçüm uyarı kuralı oluştur](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Bir uyarıdan mantıksal uygulama çalıştırmak için, bu örnekler gibi görevler gerçekleştirmenize olanak sağlayan [istek tetikleyicisini](../connectors/connectors-native-reqres.md) iş akışınıza dahil edebilirsiniz:
> 
> * [Bolluk 'e gönder](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Metin gönder](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Kuyruğa ileti ekleme](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Olay ayarları ve Ayrıntılar Azure Tanılama

Her tanılama olayının, mantıksal uygulamanız ve bu olay hakkında, örneğin, durum, başlangıç saati, bitiş saati vb. ayrıntıları vardır. Program aracılığıyla izlemeyi, izlemeyi ve günlüğe kaydetmeyi ayarlamak için bu ayrıntıları [Azure Logic Apps REST API](https://docs.microsoft.com/rest/api/logic) ve [Azure tanılama REST API](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows)için kullanabilirsiniz.

Örneğin `ActionCompleted` olayı, izleme ve izleme için kullanabileceğiniz `clientTrackingId` ve `trackedProperties` özelliklerine sahiptir:

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
    "category": "WorkflowRuntime",
    "level": "Information",
    "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
    "properties": {
        "$schema": "2016-06-01",
        "startTime": "2016-07-09T17:09:53.4336305Z",
        "endTime": "2016-07-09T17:09:53.5430281Z",
        "status": "Succeeded",
        "code": "OK",
        "resource": {
            "subscriptionId": "<subscription-ID>",
            "resourceGroupName": "MyResourceGroup",
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
            "workflowName": "MyLogicApp",
            "runId": "08587361146922712057",
            "location": "westus",
            "actionName": "Http"
        },
        "correlation": {
            "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
            "clientTrackingId": "<my-custom-tracking-ID>"
        },
        "trackedProperties": {
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`: sağlanmazsa, Azure bu KIMLIĞI otomatik olarak oluşturur ve mantıksal uygulama tarafından çağrılan iç içe geçmiş iş akışları dahil olmak üzere bir mantıksal uygulama çalıştırması genelinde olayları otomatik olarak oluşturur. Bu KIMLIĞI, tetikleyici isteğindeki özel KIMLIK değeri ile bir `x-ms-client-tracking-id` üst bilgisi geçirerek bir tetikleyiciden el ile belirtebilirsiniz. İstek tetikleyicisi, HTTP tetikleyicisi veya Web kancası tetikleyicisi kullanabilirsiniz.

* `trackedProperties`: Tanılama verilerinde girişleri veya çıkışları izlemek Için mantıksal uygulamanızın JSON tanımında izlenen özellikleri eylemlere ekleyebilirsiniz. İzlenen özellikler yalnızca tek bir eylemin girişlerini ve çıkışlarını izleyebilir, ancak bir çalıştırmada eylemler arasında ilişki kurmak için olayların `correlation` özelliklerini de kullanabilirsiniz.

  Bir veya daha fazla özelliği izlemek için `trackedProperties` bölümünü ve eylem tanımına istediğiniz özellikleri ekleyin. Örneğin, telemetrinizde "Order ID" gibi verileri izlemek istediğinizi varsayalım:

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```
  **Değişken Initialize** eylemini kullanan başka bir örnek aşağıda verilmiştir. Örnek, eylemin girişinin bir kayıt değil, bir dizi olması durumunda, izlenen özellikleri eyleme ekler.  

  ``` json
  "actions": { 
   "Initialize_variable": { 
      "inputs": { 
         "variables": [{ 
            "name": "ConnectorName", 
            "type": "String", 
            "value": "SFTP-SSH" 
         }]
      },
      "runAfter": {},
      "trackedProperties": { 
         "Track1": "@action().inputs.variables[0].value"
      },
      "type": "InitializeVariable"
   } 
  }
  ```

## <a name="next-steps"></a>Sonraki adımlar

* [Mantıksal uygulama dağıtımını otomatikleştirme](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Enterprise Integration Pack ile B2B senaryoları](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [B2B iletilerini izleme](../logic-apps/logic-apps-monitor-b2b-message.md)
