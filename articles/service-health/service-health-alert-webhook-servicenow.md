---
title: ServiceNow ile Azure hizmet durumu uyarılarını gönderme
description: ServiceNow örneğiniz için hizmet sistem durumu olayları hakkında kişiselleştirilmiş bildirimler alın.
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: devx-track-js
ms.openlocfilehash: df6596dd9853a792b5bfdb333361a2b9cd02a347
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588190"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Web kancalarını kullanarak ServiceNow ile Azure hizmet durumu uyarıları gönderme

Bu makalede, bir Web kancası kullanarak Azure hizmet durumu uyarılarını ServiceNow ile tümleştirme gösterilmektedir. ServiceNow örneğinizle Web kancası tümleştirmesini ayarladıktan sonra, Azure hizmet sorunları sizi etkiliyorsa mevcut bildirim altyapınızı kullanarak uyarılar alırsınız. Azure hizmet durumu uyarısı her tetiklendiğinde ServiceNow 'ın komut dosyalı REST API aracılığıyla bir Web kancası çağırır.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>ServiceNow 'da komut dosyalı REST API oluşturma

1.  Kaydolduğunuzdan ve [ServiceNow](https://www.servicenow.com/) hesabınızda oturum açtığınızdan emin olun.

1.  ServiceNow içindeki **sistem Web Hizmetleri** bölümüne gidin ve **Betikleştirilmiş REST API 'leri** seçin.

    ![ServiceNow 'daki "komut dosyalı Web hizmeti" bölümü](./media/webhook-alerts/servicenow-sws-section.png)

1.  Yeni bir komut dosyalı REST hizmeti oluşturmak için **Yeni** ' yi seçin.
 
    ![ServiceNow 'daki "yeni komut dosyalı REST API" düğmesi](./media/webhook-alerts/servicenow-new-button.png)

1.  REST API bir **ad** ekleyin ve **API kimliğini** olarak ayarlayın `azureservicehealth` .

1.  **Gönder**’i seçin.

    ![ServiceNow içindeki "REST API ayarları"](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Oluşturduğunuz REST API seçin ve **kaynaklar** sekmesinde **Yeni**' yi seçin.

    ![ServiceNow içindeki "kaynak sekmesi"](./media/webhook-alerts/servicenow-resources-tab.png)

1.  Yeni kaynağınızı **adlandırın** `event` ve **http yöntemini** olarak değiştirin `POST` .

1.  **Betik** bölümünde aşağıdaki JavaScript kodunu ekleyin:

    >[!NOTE]
    >`<secret>` `<group>` Aşağıdaki betikte, ve değerini güncelleştirmeniz gerekir `<email>` .
    >* `<secret>` GUID gibi bir rastgele dize olmalıdır
    >* `<group>` olayı atamak istediğiniz ServiceNow grubu olmalıdır
    >* `<email>` olayı atamak istediğiniz belirli kişi olmalıdır (isteğe bağlı)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health&quot;;
                if (event.data.context.activityLog.properties.incidentType == &quot;Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  Güvenlik sekmesinde **, onay işaretini** kaldırın ve **Gönder**' i seçin. `<secret>`Ayarladığınız ayarlar, bunun yerine bu API 'yi korur.

    ![ServiceNow 'da "kimlik doğrulaması gerektirir" onay kutusu](./media/webhook-alerts/servicenow-resource-settings.png)

1.  Komut dosyalı REST API 'Leri bölümüne geri döndüğünüzde, yeni REST API için **temel API yolunu** bulmanız gerekir:

     ![ServiceNow 'da "temel API yolu"](./media/webhook-alerts/servicenow-base-api-path.png)

1.  Tam tümleştirme URL 'niz şöyle görünür:

    ```http
    https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>
    ```

## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Azure portal ServiceNow kullanarak uyarı oluşturma
### <a name="for-a-new-action-group"></a>Yeni bir eylem grubu için:
1. Yeni bir eylem grubuyla bir uyarı oluşturmak için [Bu makaledeki](./alerts-activity-log-service-notifications-portal.md) 1 ' den 8 ' e kadar olan adımları izleyin.

1. **Eylem** listesinde tanımlayın:

    a. **Eylem türü:** *Web kancası*

    b. **Ayrıntılar:** Daha önce kaydettiğiniz ServiceNow **tümleştirme URL 'si** .

    c. **Ad:** Web kancası adı, diğer ad veya tanımlayıcı.

1. Uyarıyı oluşturmak için işiniz bittiğinde **Kaydet** ' i seçin.

### <a name="for-an-existing-action-group"></a>Mevcut bir eylem grubu için:
1. [Azure Portal](https://portal.azure.com/), **İzle**' yi seçin.

1. **Ayarlar** bölümünde **eylem grupları**' nı seçin.

1. Düzenlemek istediğiniz eylem grubunu bulun ve seçin.

1. **Eylem** listesine ekle:

    a. **Eylem türü:** *Web kancası*

    b. **Ayrıntılar:** Daha önce kaydettiğiniz ServiceNow **tümleştirme URL 'si** .

    c. **Ad:** Web kancası adı, diğer ad veya tanımlayıcı.

1. Eylem grubunu güncelleştirmek için işiniz bittiğinde **Kaydet** ' i seçin.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Web kancası tümleştirmenizi bir HTTP POST isteği aracılığıyla test etme
1. Göndermek istediğiniz hizmet durumu yükünü oluşturun. [Azure etkinlik günlüğü uyarıları Için Web kancalarında](../azure-monitor/alerts/activity-log-alerts-webhook.md)örnek bir hizmet durumu Web kancası yükü bulabilirsiniz.

1. Aşağıdaki gibi bir HTTP POST isteği oluşturun:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. `200 OK`"Olay oluşturuldu" iletisiyle bir yanıt almanız gerekir.

1. Tümleştirmenin başarıyla ayarlandığını onaylamak için [ServiceNow](https://www.servicenow.com/) 'a gidin.

## <a name="next-steps"></a>Sonraki adımlar
- [Mevcut sorun yönetimi sistemleri için Web kancası bildirimlerinin nasıl yapılandırılacağını](service-health-alert-webhook-guide.md)öğrenin.
- [Etkinlik günlüğü uyarısı Web kancası şemasını](../azure-monitor/alerts/activity-log-alerts-webhook.md)gözden geçirin. 
- [Hizmet durumu bildirimleri](./service-notifications.md)hakkında bilgi edinin.
- [Eylem grupları](../azure-monitor/alerts/action-groups.md)hakkında daha fazla bilgi edinin.
