---
title: Azure Izleyici uyarıları ile karmaşık eylemleri tetikleme
description: Azure Izleyici uyarılarını işlemek için bir mantıksal uygulama eylemi oluşturmayı öğrenin.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 0abc7c4ae370a894c46eda38df41d64a6ef2cd91
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027708"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Azure Izleyici uyarıları ile karmaşık eylemleri tetikleme

Bu makalede, bir mantıksal uygulamayı bir uyarı tetiklendiğinde Microsoft ekiplerinde konuşma oluşturmak üzere nasıl ayarlayabileceğiniz ve tetikleyebileceğiniz gösterilmektedir.

## <a name="overview"></a>Genel Bakış
Bir Azure Izleyici uyarısı tetiklendiğinde, bir [eylem grubu](../../azure-monitor/platform/action-groups.md)çağırır. Eylem grupları bir veya daha fazla eylemi bir uyarı hakkında bilgilendirmek ve ayrıca düzeltmek için tetiklemeniz sağlar.

Genel işlem şu şekilde yapılır:

-   İlgili uyarı türü için mantıksal uygulamayı oluşturun.

-   Mantıksal uygulamaya ilgili uyarı türü için örnek bir yük içeri aktarın.

-   Mantıksal uygulama davranışını tanımlayın.

-   Mantıksal uygulamanın HTTP uç noktasını bir Azure eylem grubuna kopyalayın.

Mantıksal uygulamanın farklı bir eylem gerçekleştirmesini istiyorsanız işlem benzerdir.

## <a name="create-an-activity-log-alert-administrative"></a>Etkinlik günlüğü uyarısı oluştur: Yönetim

1.  Azure portal, sol üst köşedeki **kaynak oluştur** ' u seçin.

2.  **Mantıksal uygulama**araması yapın ve seçin ve ardından **Oluştur**' u seçin.

3.  Mantıksal uygulamanıza bir **ad**verin, bir **kaynak grubu**seçin ve bu şekilde devam edin.

    ![Mantıksal]uygulama oluşturma(media/action-groups-logic-app/create-logic-app-dialog.png "mantıksal uygulama oluşturma")

4.  Mantıksal uygulamayı oluşturmak için **Oluştur** ' u seçin. Açılan ileti, mantıksal uygulamanın oluşturulduğunu gösterir. **Logic Apps tasarımcısını**açmak Için **kaynağı Başlat** ' ı seçin.

5.  Tetikleyiciyi seçin: **BIR http isteği alındığında**.

    ![Mantıksal uygulama],(media/action-groups-logic-app/logic-app-triggers.png "mantıksal uygulama tetikleyicilerini") tetikler

6.  HTTP istek tetikleyicisini değiştirmek için **Düzenle** ' yi seçin.

    ![Http isteği Tetikleyicileri](media/action-groups-logic-app/http-request-trigger-shape.png "http istek Tetikleyicileri")

7.  **Şema oluşturmak için örnek yük kullanma** öğesini seçin.

    Örnek ![Yük kullanma]örnek(media/action-groups-logic-app/use-sample-payload-button.png "Yük kullanma")

8.  Aşağıdaki örnek yükü kopyalayıp iletişim kutusuna yapıştırın:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. **Logic App Designer** , mantıksal uygulamaya gönderilen isteğin **Content-Type** üst bilgisini **Application/JSON**olarak ayarlaması gerektiğini hatırlatmak için bir açılır pencere görüntüler. Açılır pencereyi kapatın. Azure Izleyici uyarısı üstbilgiyi ayarlar.

    Content-Type üst bilgisini ayarlama Content-(media/action-groups-logic-app/content-type-header.png "Type") üst ![bilgisini ayarlama]

10. **@No__t-1** **yeni adım** ' ı seçin ve ardından **Eylem Ekle**' yi seçin.

    ![Eylem ekleme](media/action-groups-logic-app/add-action.png "eylem ekleme")

11. Microsoft ekipleri bağlayıcısını arayın ve seçin. **Microsoft ekipleri – Ileti gönder** eylemini seçin.

    ![Microsoft ekipleri eylemler](media/action-groups-logic-app/microsoft-teams-actions.png "Microsoft ekipleri eylemleri")

12. Microsoft ekipleri eylemini yapılandırın. **Logic Apps tasarımcı** , Office 365 hesabınızda kimlik doğrulaması belirtmenizi ister. İletinin gönderileceği **EKIP kimliğini** ve **Kanal kimliğini** seçin.

13. Statik metin birleşimini ve dinamik içerikte @ no__t-1 \<fields başvurularını kullanarak iletiyi yapılandırın. Aşağıdaki metni kopyalayıp **ileti** alanına yapıştırın:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Ardından, \<fields @ no__t-1 ' i arayın ve aynı ada sahip dinamik içerik etiketleri ile değiştirin.

    > [!NOTE]
    > **Durum**olarak adlandırılan iki dinamik alan vardır. Bu alanların her ikisini de iletiye ekleyin. **ActivityLog** Özellik paketinde bulunan alanını kullanın ve diğer alanı silin. Aşağıdaki ekran görüntüsünde gösterildiği gibi, tam alan başvurusunu görmek için imlecinizi **durum** alanının üzerine getirin:

    ![Microsoft ekipleri eylemi: Ileti gönderme](media/action-groups-logic-app/teams-action-post-message.png "Microsoft ekipleri eylemi: ileti gönderin")

14. **Logic Apps tasarımcısının**en üstünde, mantıksal uygulamanızı kaydetmek için **Kaydet** ' i seçin.

15. Mevcut eylem grubunuzu açın ve mantıksal uygulamaya başvurmak için bir eylem ekleyin. Mevcut bir eylem grubunuz yoksa, bir tane oluşturmak için [Azure Portal eylem grupları oluşturma ve yönetme](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) bölümüne bakın. Değişikliklerinizi kaydetmeyi unutmayın.

    Eylem grubunu ![güncelleştirme](media/action-groups-logic-app/update-action-group.png "Eylem grubunu güncelleştirme")

Bir uyarı bir sonraki sefer eylem grubunuzu çağırdığında mantıksal uygulamanız çağırılır.

## <a name="create-a-service-health-alert"></a>Hizmet durumu uyarısı oluşturma

Azure hizmet durumu girdileri, etkinlik günlüğü 'nün bir parçasıdır. Uyarı oluşturma işlemi, [bir etkinlik günlüğü uyarısı oluşturmaya](#create-an-activity-log-alert-administrative)benzer ancak birkaç değişiklik ile benzerdir:

- 1 ile 7 arasındaki adımlar aynıdır.
- 8\. adım için, HTTP istek tetikleyicisi için aşağıdaki örnek yükü kullanın:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  9 ve 10 adımları aynıdır.
-  11 ila 14 arasındaki adımlar için aşağıdaki işlemi kullanın:

   1. **@No__t-1** **yeni adım** ' ı seçin ve ardından **Koşul Ekle**' yi seçin. Aşağıdaki koşulları, mantıksal uygulamanın yalnızca giriş verileri aşağıdaki değerlerle eşleştiğinde yürütülür şekilde ayarlayın.  Metin kutusuna sürüm değeri girerken, bir sayısal tür değil, bir dize olarak değerlendirildiğinden emin olmak için tırnak içine alın ("0.1.1").  Sayfaya geri dönerseniz, sistem tırnak işaretlerini göstermez, ancak temeldeki kod yine de dize türünü korur.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Hizmet durumu yükü koşulu"](media/action-groups-logic-app/service-health-payload-condition.png "hizmet durumu yükü koşulu")

   1. **Eğer doğruysa** , Microsoft ekipleri eylemini eklemek için [bir etkinlik günlüğü oluşturma uyarısında](#create-an-activity-log-alert-administrative) adım 11 ' den 13 ' e kadar olan yönergeleri izleyin.

   1. Bir HTML ve dinamik içerik birleşimini kullanarak iletiyi tanımlayın. Aşağıdaki içeriği kopyalayıp **ileti** alanına yapıştırın. @No__t-0, `[trackingID]`, `[title]` ve `[communication]` alanlarını aynı ada sahip dinamik içerik etiketleriyle değiştirin:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Hizmet durumu doğru koşulu gönderme eylemi"](media/action-groups-logic-app/service-health-true-condition-post-action.png "hizmet durumu doğru koşulu gönderme eylemi")

   1. **Eğer yanlış** koşulu için yararlı bir ileti sağlayın:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Hizmet durumu yanlış koşulu gönderme eylemi"](media/action-groups-logic-app/service-health-false-condition-post-action.png "hizmet durumu yanlış koşulu durum gönderme eylemi")

- Aynı adım 15 ' tir. Mantıksal uygulamanızı kaydetmek ve eylem grubunuzu güncelleştirmek için yönergeleri izleyin.

## <a name="create-a-metric-alert"></a>Ölçüm uyarısı oluşturma

Ölçüm uyarısı oluşturma işlemi, [etkinlik günlüğü uyarısı oluşturmaya](#create-an-activity-log-alert-administrative)benzer ancak birkaç değişiklik ile benzerdir:

- 1 ile 7 arasındaki adımlar aynıdır.
- 8\. adım için, HTTP istek tetikleyicisi için aşağıdaki örnek yükü kullanın:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

- 9 ve 10 adımları aynıdır.
- 11 ila 14 arasındaki adımlar için aşağıdaki işlemi kullanın:

  1. **@No__t-1** **yeni adım** ' ı seçin ve ardından **Koşul Ekle**' yi seçin. Aşağıdaki koşulları, mantıksal uygulamanın yalnızca giriş verileri aşağıdaki değerlerle eşleştiğinde yürütülür şekilde ayarlayın. Metin kutusuna sürüm değeri girerken, tırnak içine alın ("2,0") ve sayısal bir tür değil, bir dize olarak değerlendirildiğinden emin olun.  Sayfaya geri dönerseniz, sistem tırnak işaretlerini göstermez, ancak temeldeki kod yine de dize türünü korur. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Ölçüm uyarısı yük koşulu"](media/action-groups-logic-app/metric-alert-payload-condition.png "ölçüm uyarısı yük koşulu")

  1. **Eğer true ise** , **her döngü Için** bir ve Microsoft ekipleri eylemi ekleyin. Bir HTML ve dinamik içerik birleşimini kullanarak iletiyi tanımlayın.

      !["Ölçüm uyarısı true Condition Post ACTION"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "ölçüm uyarısı true Condition Post ACTION")

  1. **Eğer false** koşulunda, ölçüm uyarısının mantıksal uygulamanın beklentileriyle eşleşmediğinden iletişim kurmak Için bir Microsoft ekipleri eylemi tanımlayın. JSON yükünü dahil edin. @No__t-1 ifadesinde `triggerBody` dinamik içeriğine nasıl başvurulduğuna dikkat edin.

      !["Ölçüm uyarısı yanlış koşulu gönderme eylemi"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "ölçüm uyarısı yanlış koşulu gönderme eylemi")

- Aynı adım 15 ' tir. Mantıksal uygulamanızı kaydetmek ve eylem grubunuzu güncelleştirmek için yönergeleri izleyin.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Microsoft ekiplerinin yanı sıra diğer uygulamaları çağırma
Logic Apps, çok sayıda uygulama ve veritabanında eylemleri tetikleyesağlayan farklı bağlayıcılara sahiptir. Bolluk, SQL Server, Oracle, Salesforce yalnızca bazı örneklerdir. Bağlayıcılar hakkında daha fazla bilgi için bkz. [Logic App bağlayıcıları](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Sonraki adımlar
* [Azure etkinlik günlüğü uyarılarına genel bir bakış](../../azure-monitor/platform/alerts-overview.md) elde edin ve uyarı alma hakkında bilgi edinin.  
* [Bir Azure hizmet durumu bildirimi gönderildiğinde uyarıların nasıl yapılandırılacağını](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)öğrenin.
* [Eylem grupları](../../azure-monitor/platform/action-groups.md)hakkında daha fazla bilgi edinin.

