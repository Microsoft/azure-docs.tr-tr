---
title: Azure Izleyici günlüklerini kullanarak mantıksal uygulamaları izleme
description: Azure Izleyici günlüklerini ayarlayıp Azure Logic Apps için tanılama verileri toplayarak Logic Apps sorunlarını giderin
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 09/24/2020
ms.openlocfilehash: d1c095be1d0525cc843c04a3b83e14bb4211b175
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721268"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Azure Izleyici günlüklerini ayarlama ve Azure Logic Apps için tanılama verilerini toplama

Çalışma zamanı sırasında mantıksal uygulamalarınız hakkında daha zengin hata ayıklama bilgileri almak için [Azure izleyici günlüklerini](../azure-monitor/logs/data-platform-logs.md) ayarlayıp kullanarak çalışma zamanı verileri ve olayları hakkında, olayları tetikleme, çalıştırma olayları ve [Log Analytics çalışma alanında](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)eylem olayları gibi bilgileri kaydedebilir ve kaydedebilirsiniz. [Azure izleyici](../azure-monitor/overview.md) , kullanılabilirliğini ve performansını daha kolay koruyabilmeniz için bulutunuzu ve şirket içi ortamlarınızı izlemenize yardımcı olur. Azure Izleyici günlüklerini kullanarak, bu bilgileri toplayıp gözden geçirmenize yardımcı olan [günlük sorguları](../azure-monitor/logs/log-query-overview.md) oluşturabilirsiniz. Bu tanılama verilerini Azure depolama ve Azure Event Hubs gibi [diğer Azure hizmetleriyle de kullanabilirsiniz](#extend-data).

Mantıksal uygulamanız için günlük kaydı ayarlamak için, [mantıksal uygulamanızı oluştururken Log Analytics etkinleştirebilir](#logging-for-new-logic-apps)veya mevcut Logic Apps için Log Analytics çalışma alanınıza [Logic Apps yönetimi çözümünü yükleyebilirsiniz](#install-management-solution) . Bu çözüm, mantıksal uygulama çalışmalarınız için toplu bilgiler sağlar ve durum, yürütme süresi, yeniden gönderme görevi durumu ve bağıntı kimlikleri gibi belirli ayrıntıları içerir. Daha sonra, bu bilgiler için günlüğe kaydetmeyi etkinleştirmek ve sorgular oluşturmak için [Azure izleyici günlüklerini ayarlayın](#set-up-resource-logs).

Bu makalede, yeni Logic Apps ve mevcut Logic Apps üzerinde Log Analytics nasıl etkinleştirileceği, Logic Apps yönetim çözümünün nasıl yükleneceği ve ayarlanacağı ve Azure Izleyici günlükleri için sorguları ayarlama ve oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce bir [Log Analytics çalışma alanına](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)ihtiyacınız vardır. Çalışma alanınız yoksa [Log Analytics çalışma alanı oluşturmayı](../azure-monitor/logs/quick-create-workspace.md)öğrenin.

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Yeni mantıksal uygulamalar için Log Analytics etkinleştir

Mantıksal uygulamanızı oluştururken Log Analytics açabilirsiniz.

1. [Azure Portal](https://portal.azure.com), mantıksal uygulamanızı oluşturmak için bilgileri sağladığınız bölmede aşağıdaki adımları izleyin:

   1. **Log Analytics** altında **Açık**' ı seçin.

   1. **Log Analytics çalışma alanı** listesinden, mantıksal uygulama çalıştırmalarından verileri göndermek istediğiniz çalışma alanını seçin.

      ![Mantıksal uygulama bilgilerini sağlama](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Bu adımı tamamladıktan sonra Azure, artık Log Analytics çalışma alanınız ile ilişkilendirilen mantıksal uygulamanızı oluşturur. Ayrıca, bu adım Logic Apps yönetim çözümünü çalışma alanınıza otomatik olarak yüklenir.

1. İşiniz bittiğinde **Oluştur**'u seçin.

1. Mantıksal uygulamanızı çalıştırdıktan sonra, mantıksal uygulama çalıştırmalarını görüntülemek için [Bu adımlarla devam](#view-logic-app-runs)edin.

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Logic Apps yönetimi çözümünü yükler

Mantıksal uygulamanızı oluştururken Log Analytics açtıysanız, bu adımı atlayın. Log Analytics çalışma alanınıza zaten Logic Apps Management çözümü yüklediniz.

1. [Azure Portal](https://portal.azure.com)arama kutusuna girin `log analytics workspaces` ve sonra **Log Analytics çalışma alanları**' nı seçin.

   !["Log Analytics çalışma alanları" i seçin](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. **Log Analytics çalışma alanları** altında, çalışma alanınızı seçin.

   ![Log Analytics çalışma alanınızı seçin](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. **Genel bakış** bölmesinde, izleme çözümlerini **yapılandırma Log Analytics kullanmaya başlama** altında  >  , **çözümleri görüntüle**' yi seçin.

   ![Genel Bakış bölmesinde "çözümleri görüntüle" seçeneğini belirleyin.](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. **Genel bakış**' ın altında **Ekle**' yi seçin.

   ![Genel Bakış bölmesinde yeni çözüm Ekle](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. **Market** açıldıktan sonra arama kutusuna girin `logic apps management` ve **Logic Apps yönetim**' i seçin.

   ![Market 'ten "Logic Apps yönetimi" ni seçin.](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. Çözüm açıklaması bölmesinde **Oluştur**' u seçin.

   !["Logic Apps yönetimi" çözümü eklemek için "Oluştur" u seçin](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Çözümü yüklemek istediğiniz Log Analytics çalışma alanını gözden geçirip onaylayın ve yeniden **Oluştur** ' u seçin.

   !["Logic Apps yönetimi" için "Oluştur" u seçin](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Azure, çözümü Log Analytics çalışma alanınızı içeren Azure Kaynak grubuna dağıtduktan sonra, çözüm çalışma alanınızın Özet bölmesinde görünür.

   ![Çalışma alanı Özet bölmesi](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Azure Izleyici günlüklerini ayarlama

[Azure izleyici günlüklerinde](../azure-monitor/logs/data-platform-logs.md)çalışma zamanı olayları ve verileri hakkında bilgi depolayadığınızda, bu bilgileri bulmanıza ve gözden geçirmenize yardımcı olan [günlük sorguları](../azure-monitor/logs/log-query-overview.md) oluşturabilirsiniz.

> [!NOTE]
> Tanılama ayarlarını etkinleştirdikten sonra, tanılama verileri, Log Analytics, Olay Hub 'ı veya depolama hesabı gibi belirtilen hedefteki günlüklere 30 dakika kadar akmayabilir. Bu gecikme, bu zaman dilimi içindeki tanılama verilerinin gözden geçirmeniz için mevcut olmadığı anlamına gelir. Tamamlanan olaylar ve [izlenen Özellikler](#extend-data) , 10-15 dakika boyunca Log Analytics çalışma alanınızda görünmeyebilir.

1. [Azure Portal](https://portal.azure.com)mantıksal uygulamanızı bulun ve seçin.

1. Mantıksal uygulama menünüzde **izleme** altında **Tanılama ayarları**  >  **Tanılama ayarı Ekle**' yi seçin.

   !["Izleme" altında "Tanılama ayarları" > "Tanılama ayarı Ekle" seçeneğini belirleyin.](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Ayarı oluşturmak için aşağıdaki adımları izleyin:

   1. Ayar için bir ad girin.

   1. **Log Analytics gönder**' i seçin.

   1. **Abonelik** için, Log Analytics çalışma alanınız Ile ilişkili Azure aboneliğini seçin.

   1. **Log Analytics çalışma alanı** için, kullanmak istediğiniz çalışma alanını seçin.

   1. **Günlük** altında, kaydetmek istediğiniz olay kategorisini belirten **WorkflowRuntime** kategorisini seçin.

   1. Tüm ölçümleri seçmek için **ölçüm** bölümünde **allölçümler**' i seçin.

   1. İşiniz bittiğinde **Kaydet**'i seçin.

   Örnek:

   ![Log Analytics çalışma alanı ve verileri günlüğe kaydetme için seçin](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Mantıksal uygulama çalışmalarının durumunu görüntüle

Mantıksal uygulamanız çalıştıktan sonra, Log Analytics çalışma alanınızda bu çalıştıranlarla ilgili verileri görüntüleyebilirsiniz.

1. [Azure Portal](https://portal.azure.com), Log Analytics çalışma alanınızı bulun ve açın.

1. Çalışma alanınızın menüsünde, **çalışma alanı Özeti**  >  **Logic Apps yönetim**' i seçin.

   ![Mantıksal uygulama çalıştırma durumu ve sayısı](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Logic Apps yönetim kutucuğu bir çalıştırmadan sonra sonuçları hemen göstermezse, yeniden denemeden önce **Yenile** ' yi seçmeyi veya kısa bir süre beklemeyi deneyin.

   Burada, mantıksal uygulamanız adına veya yürütme durumuna göre gruplandırılır. Bu sayfada Ayrıca, mantıksal uygulama çalıştırmaları için eylemlerdeki veya tetikleyicilere yönelik hataların ayrıntıları gösterilir.

   ![Mantıksal uygulama çalışmalarınız için durum özeti](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Belirli bir mantıksal uygulamanın veya durumun tüm çalıştırmalarını görüntülemek için, bu mantıksal uygulamanın veya durumun satırını seçin.

   Belirli bir mantıksal uygulama için tüm çalıştırmaları gösteren bir örnek aşağıda verilmiştir:

   ![Mantıksal uygulama çalıştırmalarını ve durumunu görüntüleme](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   [İzlenen özellikleri ayarladığınız](#extend-data)eylemler Için, **Izlenen Özellikler** sütununda **görüntüle** ' yi seçerek de bu özellikleri görüntüleyebilirsiniz. İzlenen özelliklerde arama yapmak için sütun filtresini kullanın.

   ![Mantıksal uygulama için izlenen özellikleri görüntüleme](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

1. Sonuçlarınızı filtrelemek için hem istemci tarafı hem de sunucu tarafı filtrelemeyi gerçekleştirebilirsiniz.

   * **İstemci tarafı filtresi**: her bir sütun için istediğiniz filtreleri seçin, örneğin:

     ![Örnek sütun filtreleri](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Sunucu tarafı filtresi**: belirli bir zaman penceresi seçmek veya görüntülenen çalıştırmaların sayısını sınırlamak için sayfanın üst kısmındaki kapsam denetimini kullanın. Varsayılan olarak, tek seferde yalnızca 1.000 kayıt görünür.

     ![Zaman penceresini değiştirme](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Belirli bir çalıştırmaya ilişkin tüm eylemleri ve bunların ayrıntılarını görüntülemek için, mantıksal uygulama çalıştırmasının satırını seçin.

   Aşağıda, belirli bir mantıksal uygulama çalıştırması için tüm eylemleri ve Tetikleyicileri gösteren bir örnek verilmiştir:

   ![Mantıksal uygulama çalıştırması için eylemleri görüntüleme](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Azure depolama 'ya ve Azure Event Hubs Tanılama verileri gönderme

Azure Izleyici günlükleriyle birlikte, mantıksal uygulamanızın tanılama verilerini diğer Azure hizmetleriyle nasıl kullanacağınızı genişletebilirsiniz, örneğin:

* [Azure Kaynak günlüklerini depolama hesabına arşivleme](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
* [Azure platformu günlüklerini Azure Event Hubs akış](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

Daha sonra [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) ve [Power BI](../azure-monitor/visualize/powerbi.md)gibi diğer hizmetlerden telemetri ve analiz kullanarak gerçek zamanlı izleme sağlayabilirsiniz. Örnek:

* [Event Hubs veri akışı Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Stream Analytics ile akış verilerini çözümleme ve Power BI bir gerçek zamanlı analiz panosu oluşturma](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Tanılama verilerini göndermek istediğiniz konumlara bağlı olarak, önce [bir Azure depolama hesabı oluşturun](../storage/common/storage-account-create.md) veya [bir Azure Olay Hub 'ı oluşturun](../event-hubs/event-hubs-create.md). Daha sonra, bu verileri göndermek istediğiniz hedefleri seçebilirsiniz. Bekletme dönemleri yalnızca bir depolama hesabı kullandığınızda geçerlidir.

![Verileri Azure depolama hesabına veya Olay Hub 'ına gönder](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Azure Izleyici tanılama olayları

Her tanılama olayının, mantıksal uygulamanız ve bu olay hakkında, örneğin, durum, başlangıç saati, bitiş saati vb. ayrıntıları vardır. Program aracılığıyla izlemeyi, izlemeyi ve günlüğe kaydetmeyi ayarlamak için, bu bilgileri Azure Logic Apps ve [Azure izleyici REST API](../azure-monitor/essentials/metrics-supported.md#microsoftlogicworkflows) [için REST API](/rest/api/logic) kullanabilirsiniz. Ayrıca, `clientTrackingId` `trackedProperties` içinde görüntülenen ve özelliklerini de kullanabilirsiniz. 

* `clientTrackingId`: Sağlanmazsa, Azure bu KIMLIĞI otomatik olarak oluşturur ve mantıksal uygulamadan çağrılan iç içe geçmiş iş akışları dahil olmak üzere bir mantıksal uygulama çalıştırması genelinde olayları otomatik olarak oluşturur. `x-ms-client-tracking-id`Tetikleyici isteğindeki özel kimlik değeri ile bir üstbilgi geçirerek bir tetikleyicide bu kimliği el ile belirtebilirsiniz. İstek tetikleyicisi, HTTP tetikleyicisi veya Web kancası tetikleyicisi kullanabilirsiniz.

* `trackedProperties`: Tanılama verilerinde girişleri veya çıkışları izlemek için, `trackedProperties` mantıksal uygulama Tasarımcısı 'nı kullanarak veya doğrudan mantıksal UYGULAMANıZıN JSON tanımında bir eyleme bölüm ekleyebilirsiniz. İzlenen özellikler yalnızca tek bir eylemin girişlerini ve çıkışlarını izleyebilir, ancak `correlation` bir çalıştırmada eylemler arasında ilişki kurmak için olayların özelliklerini kullanabilirsiniz. Birden fazla özelliği, bir veya daha fazla özelliği izlemek için, `trackedProperties` eylem tanımına istediğiniz bölümü ve özellikleri ekleyin.

  İşte, **değişkenin** bir kayıt değil, girişin bir dizi olduğu eylemin girişinden izlenen özellikleri nasıl içerdiğini gösteren bir örnek aşağıda verilmiştir.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  Bu örnek, birden çok izlenen özelliği gösterir:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

Bu örnek, `ActionCompleted` olayının ve özniteliklerinin nasıl dahil olduğunu gösterir `clientTrackingId` `trackedProperties` :

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
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
         "workflowId": "<logic-app-workflow-ID>",
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
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [İzleyici ve izleme sorguları oluşturma](../logic-apps/create-monitoring-tracking-queries.md)
* [Azure İzleyici günlükleri ile B2B iletilerini izleme](../logic-apps/monitor-b2b-messages-log-analytics.md)
