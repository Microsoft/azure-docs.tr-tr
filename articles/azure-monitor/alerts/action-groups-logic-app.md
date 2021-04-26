---
title: Azure Izleyici uyarıları ile karmaşık eylemleri tetikleme
description: Azure Izleyici uyarılarını işlemek için bir mantıksal uygulama eylemi oluşturmayı öğrenin.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: f1e81dca6926ae9f57e428eb1cef761c588a78b6
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029854"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Azure Izleyici uyarıları ile karmaşık eylemleri tetikleme

Bu makalede, bir mantıksal uygulamayı bir uyarı tetiklendiğinde Microsoft ekiplerinde konuşma oluşturmak üzere nasıl ayarlayabileceğiniz ve tetikleyebileceğiniz gösterilmektedir.

## <a name="overview"></a>Genel Bakış

Bir Azure Izleyici uyarısı tetiklendiğinde, bir [eylem grubu](./action-groups.md)çağırır. Eylem grupları bir veya daha fazla eylemi bir uyarı hakkında bilgilendirmek ve ayrıca düzeltmek için tetiklemeniz sağlar.

Genel işlem şu şekilde yapılır:

-   İlgili uyarı türü için mantıksal uygulamayı oluşturun.

-   Mantıksal uygulamaya ilgili uyarı türü için örnek bir yük içeri aktarın.

-   Mantıksal uygulama davranışını tanımlayın.

-   Mantıksal uygulamanın HTTP uç noktasını bir Azure eylem grubuna kopyalayın.

Mantıksal uygulamanın farklı bir eylem gerçekleştirmesini istiyorsanız işlem benzerdir.

## <a name="create-an-activity-log-alert-administrative"></a>Etkinlik günlüğü uyarısı oluştur: Yönetim

1. [Mantıksal uygulama oluşturun](~/articles/logic-apps/quickstart-create-first-logic-app-workflow.md).

1.  Tetikleyiciyi seçin: **BIR http isteği alındığında**.

1. **Http isteğinin alındığı zaman** iletişim kutusunda, **şema oluşturmak Için örnek yük kullan**' ı seçin.

    ![Bir g/ç isteği oluştur iletişim kutusu ve şema oluşturmak için örnek yük kullan seçeneğinin seçili olduğunu gösteren ekran görüntüsü. ](~/articles/app-service/media/tutorial-send-email/generate-schema-with-payload.png)

1.  Aşağıdaki örnek yükü kopyalayıp iletişim kutusuna yapıştırın:

    ```json
        {
            "schemaId&quot;: &quot;Microsoft.Insights/activityLogs&quot;,
            &quot;data&quot;: {
                &quot;status&quot;: &quot;Activated&quot;,
                &quot;context&quot;: {
                &quot;activityLog&quot;: {
                    &quot;authorization&quot;: {
                    &quot;action&quot;: &quot;microsoft.insights/activityLogAlerts/write&quot;,
                    &quot;scope&quot;: &quot;/subscriptions/…&quot;
                    },
                    &quot;channels&quot;: &quot;Operation&quot;,
                    &quot;claims&quot;: &quot;…&quot;,
                    &quot;caller&quot;: &quot;logicappdemo@contoso.com&quot;,
                    &quot;correlationId&quot;: &quot;91ad2bac-1afa-4932-a2ce-2f8efd6765a3&quot;,
                    &quot;description&quot;: &quot;&quot;,
                    &quot;eventSource&quot;: &quot;Administrative&quot;,
                    &quot;eventTimestamp&quot;: &quot;2018-04-03T22:33:11.762469+00:00&quot;,
                    &quot;eventDataId&quot;: &quot;ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0&quot;,
                    &quot;level&quot;: &quot;Informational&quot;,
                    &quot;operationName&quot;: &quot;microsoft.insights/activityLogAlerts/write&quot;,
                    &quot;operationId&quot;: &quot;61f59fc8-1442-4c74-9f5f-937392a9723c&quot;,
                    &quot;resourceId&quot;: &quot;/subscriptions/…&quot;,
                    &quot;resourceGroupName&quot;: &quot;LOGICAPP-DEMO&quot;,
                    &quot;resourceProviderName&quot;: &quot;microsoft.insights&quot;,
                    &quot;status&quot;: &quot;Succeeded&quot;,
                    &quot;subStatus&quot;: &quot;&quot;,
                    &quot;subscriptionId&quot;: &quot;…&quot;,
                    &quot;submissionTimestamp&quot;: &quot;2018-04-03T22:33:36.1068742+00:00&quot;,
                    &quot;resourceType&quot;: &quot;microsoft.insights/activityLogAlerts&quot;
                }
                },
                &quot;properties&quot;: {}
            }
        }
    ```

1. **Logic Apps tasarımcısında** , mantıksal uygulamaya gönderilen isteğin **Content-Type** üst bilgisini **Application/JSON** olarak ayarlaması gerektiğini hatırlatmak için bir açılır pencere görüntülenir. Açılır pencereyi kapatın. Azure Izleyici uyarısı üstbilgiyi ayarlar.

    ![Content-Type üst bilgisini ayarlama](media/action-groups-logic-app/content-type-header.png &quot;Content-Type üst bilgisini ayarlama")

1. **+** **Yeni adım** ' ı ve ardından **Eylem Ekle**' yi seçin.

    ![Eylem ekleme](media/action-groups-logic-app/add-action.png "Eylem ekleme")

1. Microsoft ekipleri bağlayıcısını arayın ve seçin. **Microsoft ekipleri-Iletiyi gönder** eylemini seçin.

    ![Microsoft ekipleri eylemleri](media/action-groups-logic-app/microsoft-teams-actions.png "Microsoft ekipleri eylemleri")

1. Microsoft ekipleri eylemini yapılandırın. **Logic Apps tasarımcı** , iş veya okul hesabınızda kimlik doğrulaması belirtmenizi ister. İletinin gönderileceği **EKIP kimliğini** ve **Kanal kimliğini** seçin.

13. Statik metin birleşimini ve dinamik içerikte öğesine başvuruları kullanarak iletiyi yapılandırın \<fields\> . Aşağıdaki metni kopyalayıp **ileti** alanına yapıştırın:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Ardından, öğesini arayın ve \<fields\> aynı ada sahip dinamik içerik etiketleri ile değiştirin.

    > [!NOTE]
    > **Durum** olarak adlandırılan iki dinamik alan vardır. Bu alanların her ikisini de iletiye ekleyin. **ActivityLog** Özellik paketinde bulunan alanını kullanın ve diğer alanı silin. Aşağıdaki ekran görüntüsünde gösterildiği gibi, tam alan başvurusunu görmek için imlecinizi **durum** alanının üzerine getirin:

    ![Microsoft ekipleri eylemi: ileti gönderin](media/action-groups-logic-app/teams-action-post-message.png "Microsoft ekipleri eylemi: ileti gönderin")

1. **Logic Apps tasarımcısının** en üstünde, mantıksal uygulamanızı kaydetmek için **Kaydet** ' i seçin.

1. Mevcut eylem grubunuzu açın ve mantıksal uygulamaya başvurmak için bir eylem ekleyin. Mevcut bir eylem grubunuz yoksa, bir tane oluşturmak için [Azure Portal eylem grupları oluşturma ve yönetme](./action-groups.md) bölümüne bakın. Değişikliklerinizi kaydetmeyi unutmayın.

    ![Eylem grubunu güncelleştirme](media/action-groups-logic-app/update-action-group.png "Eylem grubunu güncelleştirme")

Bir uyarı bir sonraki sefer eylem grubunuzu çağırdığında mantıksal uygulamanız çağırılır.

## <a name="create-a-service-health-alert"></a>Hizmet durumu uyarısı oluşturma

Azure hizmet durumu girdileri, etkinlik günlüğü 'nün bir parçasıdır. Uyarı oluşturma işlemi, [bir etkinlik günlüğü uyarısı oluşturmaya](#create-an-activity-log-alert-administrative)benzer ancak birkaç değişiklik ile benzerdir:

- 1 ile 3 arasındaki adımlar aynıdır.
- 4. adım için, HTTP istek tetikleyicisi için aşağıdaki örnek yükü kullanın:

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
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{\"ImpactedRegions\"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  5 ve 6. adımlar aynıdır.
-  7 ila 11 arasındaki adımlar için aşağıdaki işlemi kullanın:

   1. **+** **Yeni adım** ' ı ve ardından **Koşul Ekle**' yi seçin. Aşağıdaki koşulları, mantıksal uygulamanın yalnızca giriş verileri aşağıdaki değerlerle eşleştiğinde yürütülür şekilde ayarlayın.  Metin kutusuna sürüm değeri girerken, bir sayısal tür değil, bir dize olarak değerlendirildiğinden emin olmak için tırnak içine alın ("0.1.1").  Sayfaya geri dönerseniz, sistem tırnak işaretlerini göstermez, ancak temeldeki kod yine de dize türünü korur.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Hizmet durumu yük koşulu"](media/action-groups-logic-app/service-health-payload-condition.png "Hizmet durumu yükü koşulu")

   1. **Eğer doğruysa** , Microsoft ekipleri eylemini eklemek için [bir etkinlik günlüğü oluşturma uyarısında](#create-an-activity-log-alert-administrative) adım 11 ' den 13 ' e kadar olan yönergeleri izleyin.

   1. Bir HTML ve dinamik içerik birleşimini kullanarak iletiyi tanımlayın. Aşağıdaki içeriği kopyalayıp **ileti** alanına yapıştırın. ,, `[incidentType]` `[trackingID]` `[title]` Ve `[communication]` alanlarını aynı ada sahip dinamik içerik etiketleriyle değiştirin:

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

       !["Hizmet durumu doğru koşulu gönderme eylemi"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Hizmet durumu doğru koşul sonrası eylemi")

   1. **Eğer yanlış** koşulu için yararlı bir ileti sağlayın:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Hizmet durumu yanlış koşulu gönderme eylemi"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Hizmet durumu yanlış koşulu durum gönderme eylemi")

- Aynı adım 15 ' tir. Mantıksal uygulamanızı kaydetmek ve eylem grubunuzu güncelleştirmek için yönergeleri izleyin.

## <a name="create-a-metric-alert"></a>Ölçüm uyarısı oluşturma

Ölçüm uyarısı oluşturma işlemi, [etkinlik günlüğü uyarısı oluşturmaya](#create-an-activity-log-alert-administrative)benzer ancak birkaç değişiklik ile benzerdir:

- 1 ile 3 arasındaki adımlar aynıdır.
- 4. adım için, HTTP istek tetikleyicisi için aşağıdaki örnek yükü kullanın:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
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
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- 5 ve 6. adımlar aynıdır.
- 7 ila 11 arasındaki adımlar için aşağıdaki işlemi kullanın:

  1. **+** **Yeni adım** ' ı ve ardından **Koşul Ekle**' yi seçin. Aşağıdaki koşulları, mantıksal uygulamanın yalnızca giriş verileri aşağıdaki değerlerle eşleştiğinde yürütülür şekilde ayarlayın. Metin kutusuna sürüm değeri girerken, tırnak içine alın ("2,0") ve sayısal bir tür değil, bir dize olarak değerlendirildiğinden emin olun.  Sayfaya geri dönerseniz, sistem tırnak işaretlerini göstermez, ancak temeldeki kod yine de dize türünü korur. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Ölçüm uyarısı yük koşulu"](media/action-groups-logic-app/metric-alert-payload-condition.png "Ölçüm uyarısı yük koşulu")

  1. **Eğer true ise** , **her döngü Için** bir ve Microsoft ekipleri eylemi ekleyin. Bir HTML ve dinamik içerik birleşimini kullanarak iletiyi tanımlayın.

      !["Ölçüm uyarısı doğru koşulu gönderme eylemi"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Ölçüm uyarısı doğru koşulu gönderme eylemi")

  1. **Eğer false** koşulunda, ölçüm uyarısının mantıksal uygulamanın beklentileriyle eşleşmediğinden iletişim kurmak Için bir Microsoft ekipleri eylemi tanımlayın. JSON yükünü dahil edin. İfadedeki dinamik içeriğe nasıl başvurulacağını fark edin `triggerBody` `json()` .

      !["Ölçüm uyarısı yanlış koşulu gönderme eylemi"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Ölçüm uyarısı yanlış koşulu gönderme eylemi")

- Aynı adım 15 ' tir. Mantıksal uygulamanızı kaydetmek ve eylem grubunuzu güncelleştirmek için yönergeleri izleyin.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Microsoft ekiplerinin yanı sıra diğer uygulamaları çağırma
Logic Apps, çok sayıda uygulama ve veritabanında eylemleri tetikleyesağlayan farklı bağlayıcılara sahiptir. Bolluk, SQL Server, Oracle, Salesforce yalnızca bazı örneklerdir. Bağlayıcılar hakkında daha fazla bilgi için bkz. [Logic App bağlayıcıları](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Sonraki adımlar
* [Azure etkinlik günlüğü uyarılarına genel bir bakış](./alerts-overview.md) elde edin ve uyarı alma hakkında bilgi edinin.  
* [Bir Azure hizmet durumu bildirimi gönderildiğinde uyarıların nasıl yapılandırılacağını](../../service-health/alerts-activity-log-service-notifications-portal.md)öğrenin.
* [Eylem grupları](./action-groups.md)hakkında daha fazla bilgi edinin.
