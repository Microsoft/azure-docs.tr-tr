---
title: Azure Izleyici 'de Günlükler için ölçüm uyarıları oluşturma
description: Popüler Günlük Analizi verilerinde neredeyse gerçek zamanlı ölçüm uyarıları oluşturma hakkında öğretici.
author: yanivlavi
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 80ad38856686229c259730bb4e4a8fcd38d5df4f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677772"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Azure Izleyici 'de Günlükler için ölçüm uyarıları oluşturma

## <a name="overview"></a>Genel bakış

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Izleyici, [Klasik uyarılarla](../../azure-monitor/platform/alerts-classic-portal.md)avantajları olan [ölçüm uyarı türünü](../../azure-monitor/platform/alerts-metric-near-real-time.md) destekler. Ölçümler, [büyük Azure hizmetleri listesi](../../azure-monitor/platform/metrics-supported.md)için kullanılabilir. Bu makalede kaynak-`Microsoft.OperationalInsights/workspaces` için bir alt küme (yani) kullanımı açıklanmaktadır.

Azure 'daki veya Şirket içindeki kaynaklar dahil olmak üzere günlüklerden ölçüm kapsamında ayıklanan popüler Log Analytics günlüklerinde ölçüm uyarılarını kullanabilirsiniz. Desteklenen Log Analytics çözümleri aşağıda listelenmiştir:

- Windows & Linux makineleri için [performans sayaçları](../../azure-monitor/platform/data-sources-performance-counters.md)
- [Aracı Durumu için sinyal kayıtları](../../azure-monitor/insights/solution-agenthealth.md)
- [Yönetim kayıtlarını Güncelleştir](../../automation/automation-update-management.md)
- [Olay veri](../../azure-monitor/platform/data-sources-windows-events.md) günlükleri

Azure 'da sorgu tabanlı [günlük uyarıları](../../azure-monitor/platform/alerts-log.md) üzerinde **Günlükler için ölçüm uyarılarını** kullanmanın birçok avantajı vardır; Bunlardan bazıları aşağıda listelenmiştir:

- Ölçüm uyarıları, aynı olduğundan emin olmak için günlük kaynak çatalları verilerine yönelik neredeyse gerçek zamanlı izleme özelliği ve ölçüm uyarıları sunar.
- Ölçüm uyarıları durum bilgisi-yalnızca uyarı tetiklendiğinde bir kez ve uyarı çözüldüğünde bir kez bildiriliyor; Günlük uyarılarının aksine, durum bilgisi olmayan ve uyarı koşulu karşılanırsa her aralıkta tetikleme devam eden.
- Günlük için ölçüm uyarıları birden çok boyut sağlar ve bilgisayarlar, işletim sistemi türü vb. daha basit gibi belirli değerlere filtre sağlar. Analytics 'te penkenme sorgusuna gerek kalmadan.

> [!NOTE]
> Belirli bir ölçüm ve/veya boyut yalnızca seçili dönemde verileri varsa gösterilir. Bu ölçümler, Azure Log Analytics çalışma alanları olan müşteriler için kullanılabilir.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Günlükler için desteklenen ölçümler ve boyutlar

 Ölçüm uyarıları, boyutları kullanan ölçümler için uyarı vermeyi destekler. Ölçülerinizi doğru düzeye filtrelemek için boyutları kullanabilirsiniz. [Log Analytics çalışma alanlarındaki](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) Günlükler için desteklenen ölçümlerin tam listesi listelenir; , desteklenen çözümler genelinde.

> [!NOTE]
> [Azure izleyici-ölçümler](../../azure-monitor/platform/metrics-charts.md)aracılığıyla Log Analytics çalışma alanından ayıklanmak üzere desteklenen ölçümleri görüntülemek için diyor olan ölçüm için günlük ölçüm uyarısı oluşturulmalıdır. Günlükler için ölçüm uyarısında seçilen boyutlar-yalnızca Azure Izleyici ölçümleri aracılığıyla araştırma için görünür.

## <a name="creating-metric-alert-for-log-analytics"></a>Log Analytics için ölçüm uyarısı oluşturuluyor

Popüler günlüklere ait ölçüm verileri, Azure Izleyici-ölçümler ' de Log Analytics işlenmeden önce gönderilir. Bu, kullanıcıların ölçüm platformunun özelliklerinden ve ölçüm uyarısından yararlanmasını sağlar ve 1 dakikalık bir sıklık süresi ile uyarılar içeren uyarılar vardır.
Günlükler için bir ölçüm uyarısı taslağı oluşturma yolları aşağıda listelenmiştir.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Günlükler için ölçüm uyarısı önkoşulları

Log Analytics verilerinde toplanan Günlükler için ölçüm öncesinde, aşağıdakiler ayarlanmalıdır ve kullanılabilir olmalıdır:

1. **Etkin Log Analytics çalışma alanı**: geçerli ve etkin bir Log Analytics çalışma alanı bulunmalıdır. Daha fazla bilgi için bkz. [Azure portal Log Analytics çalışma alanı oluşturma](../../azure-monitor/learn/quick-create-workspace.md).
2. **Aracı Log Analytics çalışma alanı için yapılandırıldı**: aracının, önceki adımda kullanılan Log Analytics çalışma alanına veri göndermek üzere Azure VM 'leri (ve/veya) Şirket Içi VM 'ler için yapılandırılması gerekir. Daha fazla bilgi için bkz. [Log Analytics aracısına genel bakış](../../azure-monitor/platform/agents-overview.md).
3. **Desteklenen Log Analytics çözümleri yüklendi**: Log Analytics çözümün Log Analytics çalışma alanına veri göndermesi ve bu verilere gönderilmesi gerekir-desteklenen çözümler, [Windows & Linux için performans sayaçları](../../azure-monitor/platform/data-sources-performance-counters.md), [için sinyal kayıtları Aracı Durumu](../../azure-monitor/insights/solution-agenthealth.md), [güncelleştirme yönetimi](../../automation/automation-update-management.md)ve [Olay verileri](../../azure-monitor/platform/data-sources-windows-events.md).
4. **Günlükleri göndermek için yapılandırılan Log Analytics çözümleri**: Log Analytics çözüm, [Log Analytics çalışma alanları için desteklenen ölçümlere](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) karşılık gelen gerekli günlüklere/verilere sahip olmalıdır. Örneğin, *% kullanılabilir bellek* sayacı için Ilk olarak [performans sayaçları](../../azure-monitor/platform/data-sources-performance-counters.md) çözümünde yapılandırılması gerekir.

## <a name="configuring-metric-alert-for-logs"></a>Günlükler için ölçüm uyarısını yapılandırma

 Ölçüm uyarıları Azure portal, Kaynak Yöneticisi şablonları, REST API, PowerShell ve Azure CLı kullanılarak oluşturulabilir ve yönetilebilir. Günlükler için ölçüm uyarıları, bir ölçüm uyarısı olduğundan, Önkoşullar yapıldıktan sonra, belirtilen Log Analytics çalışma alanı için Günlükler için ölçüm uyarısı oluşturulabilir. [Ölçüm uyarılarının](../../azure-monitor/platform/alerts-metric-near-real-time.md) tüm özellikleri ve işlevleri, Günlükler için ölçüm uyarıları için de geçerli olacaktır; Yük şeması, uygulanabilir kota limitleri ve Faturalanan Fiyat dahil.

Adım adım ayrıntılar ve örnekler için bkz. [ölçüm uyarıları oluşturma ve yönetme](https://aka.ms/createmetricalert). Özellikle, Günlükler için ölçüm uyarıları için, ölçüm uyarılarını yönetmek için yönergeleri izleyin ve aşağıdakileri doğrulayın:

- Ölçüm uyarısı hedefi geçerli bir *Log Analytics çalışma alanıdır*
- Seçili *Log Analytics çalışma alanı* için ölçüm uyarısı için seçilen sinyal **ölçüm** türünde
- Boyut filtrelerini kullanarak belirli koşullar veya kaynaklar için filtre uygulayın; Günlükler için ölçümler çok boyutlu
- *Sinyal mantığını*yapılandırırken, boyutun birden çok değerini (bilgisayar gibi) yaymak için tek bir uyarı oluşturulabilir
- Seçili *Log Analytics çalışma alanı*için ölçüm uyarısı oluşturmak üzere **Azure Portal kullanmıyorsanız;** daha sonra kullanıcının [Azure izleyici tarafından zamanlanan sorgu kurallarını](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)kullanarak günlük verilerini bir ölçüme dönüştürmek için el ile bir açık kural oluşturması gerekir.

> [!NOTE]
> Log Analytics çalışma alanı için ölçüm uyarısı oluştururken, [Azure izleyici](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) ile günlük verilerini ölçüsüne dönüştürmek için Azure Portal ilgili kural aracılığıyla, *herhangi bir kullanıcının ihtiyacı olmadan arka planda otomatik olarak oluşturulur müdahale veya eylem*. Azure portal dışındaki yollarla günlük oluşturma için ölçüm uyarısı için, bkz. bir ScheduledQueryRule oluşturma, ölçüm uyarı oluşturma işleminden önce ölçüm dönüştürme kuralına [yönelik kaynak şablonu](#resource-template-for-metric-alerts-for-logs) -diğer oluşturulan günlüklerde ölçüm uyarısı için hiçbir veri olmayacaktır.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Günlükler için ölçüm uyarıları kaynak şablonu

Daha önce belirtildiği gibi, günlüklerden ölçüm uyarıları oluşturma işlemi iki ayaklı:

1. ScheduledQueryRule API kullanarak desteklenen günlüklerden ölçümleri ayıklama kuralı oluşturma
2. Günlükte ayıklanan (adım ' de) ve çalışma alanındaki hedef kaynak olarak Log Analytics bir ölçüm uyarısı oluşturun

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Statik eşikli Günlükler için ölçüm uyarıları

Aynı şekilde elde etmek için, bir statik eşik ölçüm uyarısının oluşturulması, aşağıdaki örnek Azure Resource Manager şablonunu kullanarak, bir statik eşik ölçümü oluşturma işleminin, scheduledQueryRule aracılığıyla günlüklerden ölçümleri ayıklama kuralının başarıyla oluşturulmasına bağlıdır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Yukarıdaki JSON 'un Metricfromlogsalcertstatic. JSON olarak kaydedildiğini varsayalım; kaynak şablonu tabanlı oluşturma için bir parametre JSON dosyası ile bağlanabilir. Örnek parametre JSON dosyası aşağıda listelenmiştir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Yukarıdaki parametre dosyasının Metricfromlogsalcertstatic. Parameters. JSON olarak kaydedildiği varsayıldığında daha sonra, [Azure Portal oluşturma Için kaynak şablonu](../../azure-resource-manager/resource-group-template-deploy-portal.md)kullanılarak Günlükler için ölçüm uyarısı oluşturabilir.

Alternatif olarak, bir diğeri aşağıdaki Azure PowerShell komutunu da kullanabilir:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Veya Azure CLı kullanarak kaynak şablonu dağıtma ' yı kullanın:

```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Dinamik eşiklere sahip Günlükler için ölçüm uyarıları

Aynı sağlamak için, bunlardan biri aşağıdaki örnek Azure Resource Manager şablonunu kullanabilir; burada dinamik bir eşik ölçüm uyarısı oluşturulması, scheduledQueryRule aracılığıyla günlüklerden ölçümleri ayıklama kuralının başarılı bir şekilde oluşturulmasına bağlıdır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Yukarıdaki JSON 'un, Metricfromlogsalcertdynamic. JSON olarak kaydedildiğini varsayalım; kaynak şablonu tabanlı oluşturma için bir parametre JSON dosyası ile bağlanabilir. Örnek parametre JSON dosyası aşağıda listelenmiştir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterOrLessThan"
          },
          "alertSensitivity": {
              "value": "Medium"
          },
          "numberOfEvaluationPeriods": {
              "value": "4"
          },
          "minFailingPeriodsToAlert": {
              "value": "3"
          },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Yukarıdaki parametre dosyasının Metricfromlogsalcertdynamic. Parameters. JSON olarak kaydedildiği varsayıldığında daha sonra, [Azure Portal oluşturma Için kaynak şablonu](../../azure-resource-manager/resource-group-template-deploy-portal.md)kullanılarak Günlükler için ölçüm uyarısı oluşturabilir.

Alternatif olarak, bir diğeri aşağıdaki Azure PowerShell komutunu da kullanabilir:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Veya Azure CLı kullanarak kaynak şablonu dağıtma ' yı kullanın:

```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Sonraki adımlar

- [Ölçüm uyarıları](alerts-metric.md)hakkında daha fazla bilgi edinin.
- [Azure 'da günlük uyarıları](../../azure-monitor/platform/alerts-unified-log.md)hakkında bilgi edinin.
- [Azure 'daki uyarılar](alerts-overview.md)hakkında bilgi edinin.
