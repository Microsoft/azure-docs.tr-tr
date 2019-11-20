---
title: Azure Izleyici 'yi kullanarak günlük uyarıları oluşturma, görüntüleme ve yönetme | Microsoft Docs
description: Azure 'da günlük uyarı kurallarını yazmak, görüntülemek ve yönetmek için Azure Izleyicisini kullanın.
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: d540912d280302d2ae2166b75fb2d163458d7a61
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677836"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Azure Izleyici 'yi kullanarak günlük uyarıları oluşturma, görüntüleme ve yönetme

## <a name="overview"></a>Genel Bakış
Bu makalede, Azure portal içindeki uyarılar arabirimini kullanarak günlük uyarılarının nasıl ayarlanacağı gösterilmektedir. Uyarı kuralının tanımı üç bölümden oluşur:
- Hedef: izlenecek belirli Azure kaynağı
- Ölçüt: sinyalde görüldüğü belirli koşul veya mantık eylemi tetiklemelidir
- Eylem: bir bildirim alıcısına gönderilen belirli çağrı-e-posta, SMS, Web kancası vb.

**Günlük** , [Log Analytics çalışma alanında](../learn/tutorial-viewdata.md) veya [Application Insights](../app/analytics.md), sinyalin günlük sorgusu olduğu uyarıları betimleyen uyarıları anlatmaktadır. Günlük uyarılarından işlevsellik, terminoloji ve türler hakkında daha fazla bilgi edinin [-genel bakış](alerts-unified-log.md).

> [!NOTE]
> [Bir Log Analytics çalışma alanındaki](../../azure-monitor/learn/tutorial-viewdata.md) popüler günlük verileri artık Azure izleyici 'de ölçüm platformunda de mevcuttur. Ayrıntılar görünümü için, [Günlükler Için ölçüm uyarısı](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Azure portal günlük uyarılarını yönetme

Ayrıntılı ileri, Azure portal arabirimini kullanarak günlük uyarılarını kullanmaya yönelik adım adım kılavuzlardır.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Azure portal bir günlük uyarı kuralı oluşturun

1. [Portalda](https://portal.azure.com/) **izleyici** ' yi seçin ve izleyici bölümünde **Uyarılar**' ı seçin.

    ![İzleme](media/alerts-log/AlertsPreviewMenu.png)

1. Azure 'da yeni bir uyarı oluşturmak için **Yeni uyarı kuralı** düğmesini seçin.

    ![Uyarı Ekle](media/alerts-log/AlertsPreviewOption.png)

1. Uyarı oluştur bölümü, şu üç bölümden oluşur: *Uyarı koşulunu tanımlayın*, *uyarı ayrıntılarını tanımlayın*ve *Eylem grubunu tanımlayın*.

    ![Kural Oluştur](media/alerts-log/AlertsPreviewAdd.png)

1. **Kaynak Seç** bağlantısını kullanarak ve bir kaynak seçerek hedefi belirterek uyarı koşulunu tanımlayın. _Abonelik_, _kaynak türü_ve gerekli _kaynağı_seçerek filtre uygulayın.

   > [!NOTE]
   > Günlük uyarısı oluşturmak için-devam etmeden önce seçili kaynak için **günlük** sinyalinin kullanılabilir olduğunu doğrulayın.
   >  ![Select Resource @ no__t-1

1. *Günlük uyarıları*: **kaynak türünün** *Log Analytics* veya *Application Insights* ve sinyal türü olarak **günlük**olarak bir analiz kaynağı olduğundan emin olun, ardından uygun **kaynak** seçildiğinde, *bitti*' ye tıklayın. Ardından, kaynak için kullanılabilen sinyal seçeneklerinin listesini görüntülemek için **Ölçüt Ekle** düğmesini kullanın ve *Log Analytics* veya *Application Insights*gibi seçili günlük izleme hizmeti için sinyal listesi **özel günlük araması** seçeneğini kullanın.

   ![Kaynak seçin-özel günlük araması](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Uyarı listeleri, yukarıdaki çizimde görüldüğü gibi Analytics sorgusunu, sinyal türü- **günlük (kaydedilmiş sorgu)** olarak içeri aktarabilir. Böylece kullanıcılar, sorgulamanızı sorgulayabilir ve daha sonra uyarılarda kullanmak üzere kaydedebilir. [Azure izleyici 'de günlük sorgusu](../log-query/log-query-overview.md) veya [Application Insights Analytics 'te paylaşılan sorgu](../app/app-insights-overview.md)kullanarak, sorguyu kaydetme hakkında daha ayrıntılı bilgi edinin.

1. *Günlük uyarıları*: seçildiğinde, uyarı sorgusu **arama sorgusu** alanında belirtilebilir; sorgu söz dizimi yanlışsa, alan hatayı kırmızı olarak görüntüler. Sorgu söz dizimi doğruysa, belirtilen sorgunun başvuru tarihi geçmiş verileri Için, son altı saatten geçen haftaya kadar olan zaman penceresini ince ayar seçeneği içeren bir grafik olarak gösterilir.

    ![Uyarı kuralını Yapılandır](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > Geçmiş veri görselleştirmesi yalnızca sorgu sonuçlarının zaman ayrıntıları varsa gösterilir. Sorgunuz özetlenmiş veriler veya belirli sütun değerleri ile sonuçlanırsa-aynı tekil bir çizim olarak gösterilir.
   > Application Insights kullanarak günlük uyarılarının ölçüm ölçümü türü veya [yenı API 'ye geçiş](alerts-log-api-switch.md)Için, **toplama** seçeneğini kullanarak verileri hangi belirli değişkenin gruplandırmaya yönelik olduğunu belirtebilirsiniz; aşağıda gösterildiği gibi:
   > 
   > ![toplama açık seçeneği](media/alerts-log/aggregate-on.png)

1. *Günlük uyarıları*: görselleştirme ile birlikte, **Uyarı mantığı** koşul, toplama ve son eşik seçenekleri arasından seçilebilir. Son olarak, **Dönem** seçeneğini kullanarak belirtilen koşul için değerlendirme süresini mantığa belirtin. **Sıklık**' i seçerek uyarının ne sıklıkta çalışacağını öğrenin. **Günlük uyarıları** şunları temel alabilir:
    - [Kayıt sayısı](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): sorgu tarafından döndürülen kayıt sayısı, belirtilen değerden büyükse veya bundan küçükse bir uyarı oluşturulur.
    - [Ölçüm ölçümü](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): sonuçlardaki her bir *toplama değeri* , belirtilen eşik değerini aşarsa ve seçilen değere *göre gruplandırılmışsa* bir uyarı oluşturulur. Bir uyarı için ihlal sayısı, eşiğin seçilen dönemde kaç kez aşıldığını sayısıdır. Sonuçlar kümesi veya ardışık ihlallerin her türlü birleşimi için, her türlü ihlal kombinasyonu için, binlerin ardışık örneklerde oluşması gerekir.


1. İkinci adım olarak, uyarı **kuralı adı** alanında uyarı için bir ad tanımlayın ve belirtilen seçeneklerden uyarı ve **önem derecesi** değerinin ayrıntılarını açıklayan bir **Açıklama** belirtin. Bu ayrıntılar, Azure Izleyici tarafından yapılan tüm uyarı e-postalarında, bildirimlerde veya göndermede yeniden kullanılır. Ayrıca, Kullanıcı oluşturma seçeneği için **kuralı etkinleştir** seçeneğini uygun şekilde değiştirerek, oluşturma sırasında uyarı kuralını hemen etkinleştirmeyi seçebilir.

    Yalnızca **günlük uyarıları** Için, uyarı ayrıntılarında bazı ek işlevler kullanılabilir:

    - **Uyarıları bastır**: uyarı kuralı için gizleme özelliğini açtığınızda, yeni bir uyarı oluşturulduktan sonra kural için Eylemler tanımlı bir süre için devre dışı bırakılır. Kural hala çalışıyor ve ölçüt karşılandığında uyarı kayıtları oluşturuyor. Yinelenen eylemleri çalıştırmadan sorunu düzeltmeniz için zaman sağlar.

        ![Günlük uyarıları uyarılarını gösterme](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Bildirimlerin çakışma olmadan durdurulduğundan emin olmak için uyarı sıklığından daha büyük bir gizleme uyarı değeri belirtin

1. Üçüncü ve son adımla, uyarı koşulu karşılandığında uyarı kuralı için herhangi bir **eylem grubunun** tetiklenmesi gerekip gerekmediğini belirtin. Uyarı ile var olan herhangi bir eylem grubunu seçebilir veya yeni bir eylem grubu oluşturabilirsiniz. Seçili eylem grubuna göre, uyarı, Azure tarafından tetiklenir: e-posta gönderin, SMS gönderin, Web kancaları çağırın, Azure runbook 'Ları kullanarak düzeltin, ıTSM aracınızı gönderin, vb. [Eylem grupları](action-groups.md)hakkında daha fazla bilgi edinin.

    > [!NOTE]
    > Azure eylem grupları aracılığıyla günlük uyarıları için tetiklenen runbook yüklerinin sınırları için [Azure abonelik hizmeti sınırlarına](../../azure-subscription-service-limits.md) bakın

    **Günlük uyarıları** için, varsayılan eylemleri geçersiz kılmak üzere bazı ek işlevler kullanılabilir:

    - **E-posta bildirimi**: eylem grubu ile gönderilen e-posta *konusunu* geçersiz kılar; söylemi eylem grubunda bir veya daha fazla e-posta eylemi mevcutsa. Postanın gövdesini değiştiremezsiniz ve bu alan e-posta adresi için **değil** .
    - **Özel JSON yükünü dahil et**: eylem grupları tarafından kullanılan Web kancası JSON değerini geçersiz kılar; söylemi eylem grubunda bir veya daha fazla Web kancası eylemi varsa. Kullanıcı, ilişkili eylem grubunda yapılandırılmış tüm Web kancaları için kullanılacak JSON biçimini belirtebilir; Web kancası biçimleri hakkında daha fazla bilgi için bkz. [günlük uyarıları için Web kancası eylemi](../../azure-monitor/platform/alerts-log-webhook.md). Örnek JSON verilerini kullanarak Biçimlendirmeyi denetlemek için Web kancasını görüntüle seçeneği sağlanır.

        ![Günlük uyarıları için eylem geçersiz kılmaları](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Tüm alanlar geçerliyse ve yeşil onay işareti varsa **Uyarı kuralı oluştur** düğmesine tıklanmış ve Azure Izleyici-Uyarılar ' ta bir uyarı oluşturulur. Tüm uyarılar uyarılar panosundan görüntülenebilir.

     ![Kural oluşturma](media/alerts-log/AlertsPreviewCreate.png)

     Birkaç dakika içinde, uyarı etkin ve daha önce açıklandığı gibi tetikler.

Ayrıca kullanıcılar, [Log Analytics](../log-query/portals.md) 'te analiz sorgusunu da sonlandırılabilir ve ardından ' uyarı ayarla ' düğmesine bir uyarı oluşturmak için bunu gönderebilir ve yukarıdaki öğreticide adım 6 ' dan sonraki yönergeleri izleyin.

 ![Log Analytics-uyarı ayarla](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Azure portal 'da günlük uyarılarını görüntüleme & yönetme

1. [Portalda](https://portal.azure.com/) **izleyici** ' yi seçin ve izleyici bölümünde **Uyarılar**' ı seçin.

1. **Uyarılar panosu** görüntülenir; tüm Azure uyarıları (günlük uyarıları dahil) tekil bir panoda görüntülenir; günlük uyarı kuralınız tetiklendiğinde her örneğini dahil etme. Daha fazla bilgi için bkz. [uyarı yönetimi](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Günlük uyarı kuralları, kullanıcılar tarafından belirtilen özel sorgu tabanlı mantığın ve bu nedenle çözümlenmiş bir durum olmadan oluşur. Günlük uyarı kuralında belirtilen koşulların her karşılanması nedeniyle, bu durum tetiklenir.

1. Kural yönetimi bölümüne gitmek için üst taraftaki çubukta **Kuralları Yönet** düğmesini seçin-tüm uyarı kurallarının listelendiği yer. devre dışı bırakılmış uyarılar dahil.
    ![ uyarı kurallarını yönet @ no__t-1

## <a name="managing-log-alerts-using-azure-resource-template"></a>Azure kaynak şablonu kullanarak günlük uyarılarını yönetme

Azure Izleyici 'deki günlük uyarıları `Microsoft.Insights/scheduledQueryRules/` kaynak türüyle ilişkilendirilir. Bu kaynak türü hakkında daha fazla bilgi için bkz. [Azure izleyici-zamanlanmış sorgu KURALLARı API başvurusu](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Application Insights veya Log Analytics için günlük uyarıları, [Zamanlanmış sorgu kuralları API 'si](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)kullanılarak oluşturulabilir.

> [!NOTE]
> Log Analytics için günlük uyarıları, eski [Log Analytics uyarı API 'si](api-alerts.md) ve [Log Analytics kayıtlı aramaların ve uyarıların](../insights/solutions-resources-searches-alerts.md) eski şablonları kullanılarak da yönetilebilir. Burada ayrıntılı olarak açıklanan yeni ScheduledQueryRules API 'sini kullanma hakkında daha fazla bilgi için, bkz. [Log Analytics uyarılar için yenı API 'ye geçme](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Azure kaynak şablonu kullanarak örnek günlük uyarısı oluşturma

Aşağıda, örnek verileri değişken olarak ayarlanmış şekilde, [sonuç türü günlük uyarısı](alerts-unified-log.md#number-of-results-alert-rules)için standart günlük arama sorgusu kullanılarak [zamanlanan sorgu kuralları oluşturma](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) tabanlı kaynak şablonunun yapısı verilmiştir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"requests",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4"
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescription')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "aznsAction":{
                    "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]"
                }
            }
        }
    } ]
}

```

Yukarıdaki örnek JSON (deyin) sampleScheduledQueryRule. JSON olarak kaydedilebilir ve bu izlenecek yol için [Azure portal Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)kullanılarak dağıtılabilir.


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Azure kaynak şablonu kullanarak çapraz kaynak sorgusuyla günlük uyarısı

Aşağıda, değişken olarak ayarlanan örnek verilerle birlikte, [Ölçüm Ölçüm türü günlük uyarısı](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)' nın [çapraz kaynak günlük arama sorgusu](../../azure-monitor/log-query/cross-workspace-query.md) kullanılarak [zamanlanan sorgu kuralları oluşturma](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) tabanlı kaynak şablonunun yapısı verilmiştir.

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> Günlük uyarısında çapraz kaynak sorgusu kullanılırken, [Authorizedresources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) kullanımı zorunludur ve kullanıcının belirtilen kaynak listesine erişimi olması gerekir

Yukarıdaki örnek JSON (deyin) sampleScheduledQueryRule. JSON olarak kaydedilebilir ve bu izlenecek yol için [Azure portal Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)kullanılarak dağıtılabilir.

## <a name="managing-log-alerts-using-powershell"></a>PowerShell kullanarak günlük uyarılarını yönetme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Izleyici- [Zamanlanmış sorgu kuralları API 'si](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) bir REST API ve Azure Resource Manager REST API ile tamamen uyumludur. Ve aşağıda listelenen PowerShell cmdlet 'leri, [Zamanlanmış sorgu KURALLARı API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)'sini kullanabilir.

1. New [-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : PowerShell cmdlet 'i yeni bir günlük uyarı kuralı oluşturmak için.
1. [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : PowerShell cmdlet 'i var olan bir günlük uyarısı kuralını güncelleştirmek için.
1. [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : bir günlük uyarısı için kaynak parametrelerini belirten nesne oluşturmak veya güncelleştirmek için PowerShell cmdlet 'i. [New-azscheduledqueryrule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) ve [set-azscheduledqueryrule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet 'i tarafından giriş olarak kullanılır.
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): bir günlük uyarısı için zamanlama parametrelerini belirten nesne oluşturmak veya güncelleştirmek için PowerShell cmdlet 'i. [New-azscheduledqueryrule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) ve [set-azscheduledqueryrule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet 'i tarafından giriş olarak kullanılır.
1. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) : bir günlük uyarısı için eylem parametreleri belirten nesne oluşturmak veya güncelleştirmek için PowerShell cmdlet 'i. [New-azscheduledqueryrule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) ve [set-azscheduledqueryrule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet 'i tarafından giriş olarak kullanılır.
1. [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : bir günlük uyarısı için eylem grupları parametrelerini belirten nesne oluşturmak veya güncelleştirmek için PowerShell cmdlet 'i. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) cmdlet 'i tarafından giriş olarak kullanılır.
1. [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : günlük uyarısı için tetikleyici koşulu parametrelerini belirten nesne oluşturmak veya güncelleştirmek için PowerShell cmdlet 'i. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) cmdlet 'i tarafından giriş olarak kullanılır.
1. [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : ölçüm [Ölçüm türü günlük uyarısı](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)için ölçüm tetikleyicisi koşul parametrelerini belirten nesne oluşturmak veya güncelleştirmek için PowerShell cmdlet 'i. [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) cmdlet 'i tarafından giriş olarak kullanılır.
1. [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : var olan günlük uyarısı kurallarını veya belirli bir günlük uyarısı kuralını listelemek için PowerShell cmdlet 'i
1. [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : PowerShell cmdlet 'i, günlük uyarı kuralını etkinleştirmek veya devre dışı bırakmak için
1. [Remove-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): PowerShell cmdlet 'i var olan bir günlük uyarı kuralını siler

> [!NOTE]
> ScheduledQueryRules PowerShell cmdlet 'leri yalnızca oluşturulan cmdlet 'lerin kendisini yönetebilir veya Azure Izleyici ile [Zamanlanmış sorgu kuralları API 'sini](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)kullanabilir. Eski [Log Analytics uyarı API 'si](api-alerts.md) kullanılarak oluşturulan günlük uyarı kuralları ve [Log Analytics kayıtlı aramaların ve uyarıların](../insights/solutions-resources-searches-alerts.md) eski şablonları, yalnızca Kullanıcı [oturum için API tercihine geçtiğinde, scheduledqueryrules PowerShell cmdlet 'leri kullanılarak yönetilebilir Analiz uyarıları](alerts-log-api-switch.md).

Sonraki adımda, scheduledQueryRules PowerShell cmdlet 'lerini kullanarak örnek günlük uyarısı kuralı oluşturma adımları gösterilmektedir.
```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ \"alert\":\"#alertrulename\", \"IncludeSearchResults\":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>CLı veya API kullanarak günlük uyarılarını yönetme

Azure Izleyici- [Zamanlanmış sorgu kuralları API 'si](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) bir REST API ve Azure Resource Manager REST API ile tamamen uyumludur. Bu nedenle, Azure CLı için Kaynak Yöneticisi komutları kullanılarak PowerShell aracılığıyla kullanılabilir.


> [!NOTE]
> Log Analytics için günlük uyarıları, eski [Log Analytics uyarı API 'si](api-alerts.md) ve [Log Analytics kayıtlı aramaların ve uyarıların](../insights/solutions-resources-searches-alerts.md) eski şablonları kullanılarak da yönetilebilir. Burada ayrıntılı olarak açıklanan yeni ScheduledQueryRules API 'sini kullanma hakkında daha fazla bilgi için, bkz. [Log Analytics uyarılar için yenı API 'ye geçme](alerts-log-api-switch.md).

Günlük uyarıları Şu anda adanmış CLı komutlarına sahip değil; aşağıda gösterildiği gibi, kaynak şablonu bölümünde daha önce gösterilen örnek kaynak şablonu için Azure Resource Manager CLı komutu aracılığıyla (sampleScheduledQueryRule. JSON) kullanılabilir:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Başarılı bir işlem olduğunda, 201 yeni uyarı kuralı oluşturma durumuna döndürülecek veya mevcut bir uyarı kuralı değiştirilirse 200 döndürülür.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure uyarılarında günlük uyarıları](../../azure-monitor/platform/alerts-unified-log.md) hakkında bilgi edinin
* [Günlük uyarıları Için Web kancası eylemlerini](../../azure-monitor/platform/alerts-log-webhook.md) anlama
* [Application Insights](../../azure-monitor/app/analytics.md) hakkında daha fazla bilgi edinin
* [Günlük sorguları](../log-query/log-query-overview.md)hakkında daha fazla bilgi edinin.
