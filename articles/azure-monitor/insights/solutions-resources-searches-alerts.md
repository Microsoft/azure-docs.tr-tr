---
title: Yönetim çözümlerinde kaydedilen aramalar | Microsoft Docs
description: Yönetim çözümleri genellikle, çözüm tarafından toplanan verileri çözümlemek için Log Analytics ' de kaydedilmiş aramaları içerir. Ayrıca, kullanıcıya bildirimde bulunan uyarıları tanımlayabilir veya kritik bir soruna yanıt olarak otomatik olarak işlem yapması gerekebilir. Bu makalede, yönetim çözümlerine dahil edilmesini sağlamak üzere Kaynak Yöneticisi şablonda Log Analytics kayıtlı aramaların nasıl tanımlanacağı açıklanmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce4f3dcbc28668f786c706e7029061e541a76ce9
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553915"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Yönetim çözümüne Log Analytics kaydedilmiş aramalar ve uyarılar ekleme (Önizleme)

> [!IMPORTANT]
> [Daha önce duyurulduğu](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)gibi, *1 Haziran 2019* tarihinden sonra oluşturulan Log Analytics çalışma alanı (ler), **yalnızca** Azure Scheduledqueryrules [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/), [Azure Resource Manager şablonu](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) ve PowerShell kullanarak uyarı kurallarını yönetebilecektir [ cmdlet 'i](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Müşteriler, Azure Izleyici scheduledQueryRules 'ı varsayılan olarak kullanabilmek ve yerel PowerShell cmdlet 'leri kullanma özelliği gibi birçok [yeni avantaj](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) kazanmak için, daha eski çalışma alanları için [kendi tercih edilen uyarı kuralı yönetimini](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) kolayca değiştirebilir. kuralların ayrı kaynak grubunda veya abonelikte ve çok daha fazlasına göre kuralları oluşturma sırasında geri dönüş süresi.

> [!NOTE]
> Bu, şu anda önizleme aşamasında olan yönetim çözümlerini oluşturmaya yönelik bir belgedir. Aşağıda açıklanan tüm şemalarla değişiklik yapılır.

[Yönetim çözümleri](solutions.md) , çözüm tarafından toplanan verileri çözümlemek için genellikle Log Analytics ' a [kaydedilmiş aramaları](../../azure-monitor/log-query/log-query-overview.md) içerir. Ayrıca, kullanıcıya bildirimde bulunan [uyarıları](../../azure-monitor/platform/alerts-overview.md) tanımlayabilir veya kritik bir soruna yanıt olarak otomatik olarak işlem yapması gerekebilir. Bu makalede, [yönetim çözümlerine](solutions-creating.md)eklenmek üzere bir [kaynak yönetimi şablonunda](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) Log Analytics kayıtlı aramaların ve uyarıların nasıl tanımlanacağı açıklanmaktadır.

> [!NOTE]
> Bu makaledeki örneklerde, yönetim çözümlerinde gerekli veya ortak olan ve [Azure 'da bir yönetim çözümü tasarlama ve derleme](solutions-creating.md) konularında açıklanan parametreleri ve değişkenleri kullanır

## <a name="prerequisites"></a>Önkoşullar
Bu makalede [bir yönetim çözümünün nasıl oluşturulacağı](solutions-creating.md) ve bir [Kaynak Yöneticisi şablonu](../../azure-resource-manager/resource-group-authoring-templates.md) ve çözüm dosyasının yapısı hakkında zaten bilgi sahibi olduğunuz varsayılır.


## <a name="log-analytics-workspace"></a>Log Analytics Çalışma Alanı
Log Analytics içindeki tüm kaynaklar bir [çalışma alanında](../../azure-monitor/platform/manage-access.md)yer alır. [Log Analytics çalışma alanı ve Otomasyon hesabı](solutions.md#log-analytics-workspace-and-automation-account)' nda açıklandığı gibi, çalışma alanı yönetim çözümüne dahil değildir ancak çözüm yüklenmeden önce var olmalıdır. Kullanılabilir değilse çözüm yüklemesi başarısız olur.

Çalışma alanının adı her Log Analytics kaynağının adıdır. Bu, bir SavedSearch kaynağının aşağıdaki örneğinde olduğu gibi **çalışma alanı** parametresiyle çözümde yapılır.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API sürümü
Bir Kaynak Yöneticisi şablonunda tanımlanan tüm Log Analytics kaynakların, kaynağın kullanması gereken API sürümünü tanımlayan bir **Apiversion** özelliği vardır.

Aşağıdaki tabloda, bu örnekte kullanılan kaynak için API sürümü listelenmektedir.

| Kaynak türü | API sürümü | Sorgu |
|:---|:---|:---|
| Savedaramalarındaki aramalar | 2017-03-15-Önizleme | EventLevelName = = "Error" adlı olay &#124;  |


## <a name="saved-searches"></a>Kayıtlı Aramalar
Kullanıcıların çözümünüz tarafından toplanan verileri sorgulamasına izin vermek için [kaydedilmiş aramaları](../../azure-monitor/log-query/log-query-overview.md) bir çözüme ekleyin. Kayıtlı aramalar Azure portal **kayıtlı aramalar** altında görüntülenir. Her uyarı için de kayıtlı bir arama gerekir.

[Log Analytics kayıtlı arama](../../azure-monitor/log-query/log-query-overview.md) kaynakları bir `Microsoft.OperationalInsights/workspaces/savedSearches` türüne sahiptir ve aşağıdaki yapıya sahiptir. Bu kod parçacığını kopyalayıp çözüm dosyanıza yapıştırarak parametre adlarını değiştirebilmeniz için ortak değişkenleri ve parametreleri içerir.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Kayıtlı bir aramanın her özelliği aşağıdaki tabloda açıklanmıştır.

| Özellik | Açıklama |
|:--- |:--- |
| category | Kaydedilen arama kategorisi.  Aynı çözümde bulunan tüm kayıtlı aramalar, genellikle konsolda birlikte gruplandırılmaları için tek bir kategoriyi paylaşır. |
| DisplayName | Portalda kayıtlı arama için görüntülenecek ad. |
| sorgu | Çalıştırılacak sorgu. |

> [!NOTE]
> JSON olarak yorumlanabilecek karakterler içeriyorsa, sorguda kaçış karakterleri kullanmanız gerekebilir. Örneğin, sorgunuz **AzureActivity ise | OperationName: "Microsoft. COMPUTE/virtualMachines/Write"** , çözüm dosyasında AzureActivity olarak yazılması gerekir **| OperationName:/\"Microsoft. COMPUTE/virtualMachines/Write \"** .

## <a name="alerts"></a>Uyarılar
[Azure günlük uyarıları](../../azure-monitor/platform/alerts-unified-log.md) , belirli günlük sorgularını düzenli aralıklarla çalıştıran Azure uyarı kuralları tarafından oluşturulur. Sorgunun sonuçları belirtilen ölçütlerle eşleşiyorsa, bir uyarı kaydı oluşturulur ve [eylem grupları](../../azure-monitor/platform/action-groups.md)kullanılarak bir veya daha fazla eylem çalıştırılır.

Uyarıları Azure 'a genişleten kullanıcılar için, eylemler artık Azure eylem gruplarında kontrol edilir. Bir çalışma alanı ve uyarıları Azure 'a genişletildiğinde, [eylem grubu Azure Resource Manager şablonunu](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)kullanarak eylemleri alabilir veya ekleyebilirsiniz.
Eski yönetim çözümünde uyarı kuralları aşağıdaki üç farklı kaynaktan oluşur.

- **Arama kaydedildi.** Çalıştırılan günlük aramasını tanımlar. Birden çok uyarı kuralı, tek bir kayıtlı aramayı paylaşabilir.
- **Çizelgesini.** Günlük aramasının ne sıklıkla çalıştırılacağını tanımlar. Her uyarı kuralının bir ve yalnızca bir zamanlaması vardır.
- **Uyarı eylemi.** Her uyarı kuralında bir uyarı kaydı oluşturma ölçütü ve uyarının önem derecesi gibi uyarının ayrıntılarını tanımlayan bir **Uyarı** türü olan bir eylem grubu kaynağı veya eylem kaynağı (eski) vardır. [Eylem grubu](../../azure-monitor/platform/action-groups.md) kaynağı, uyarı başlatıldığında yapılacak, sesli çağrı, SMS, e-posta, Web KANCASı, ITSM Aracı, Otomasyon Runbook 'u, mantıksal uygulama vb. gibi yapılandırılmış eylemlerin listesine sahip olabilir.

Kayıtlı arama kaynakları yukarıda açıklanmıştır. Diğer kaynaklar aşağıda açıklanmıştır.

### <a name="schedule-resource"></a>Kaynağı zamanla

Kayıtlı aramada, her zamanlamaya göre ayrı bir uyarı kuralını temsil eden bir veya daha fazla zamanlama bulunabilir. Zamanlama, aramanın ne sıklıkla çalıştırılacağını ve verilerin alınma zaman aralığını tanımlar. Zamanlanan kaynakların `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` bir türü vardır ve aşağıdaki yapıya sahiptir. Bu kod parçacığını kopyalayıp çözüm dosyanıza yapıştırarak parametre adlarını değiştirebilmeniz için ortak değişkenleri ve parametreleri içerir.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
Zamanlama kaynaklarının özellikleri aşağıdaki tabloda açıklanmıştır.

| Öğe adı | Gereklidir | Açıklama |
|:--|:--|:--|
| etkinletir       | Yes | Uyarının oluşturulduğu sırada etkinleştirilip etkinleştirilmeyeceğini belirtir. |
| interval      | Yes | Sorgunun dakikalar içinde çalışma sıklığı. |
| queryTimeSpan | Yes | Sonuçların değerlendirileceği sürenin dakika cinsinden uzunluğu. |

Zamanlama kaynağı, zamanlamadan önce oluşturulabilmesi için, kaydedilen aramaya bağlı olmalıdır.
> [!NOTE]
> Zamanlama adı, belirli bir çalışma alanında benzersiz olmalıdır; farklı kaydedilmiş aramalarla ilişkilendirilseler de iki zamanlama aynı KIMLIĞE sahip olamaz. Ayrıca, Log Analytics API ile oluşturulan tüm kayıtlı aramaların, zamanlamaların ve eylemlerin küçük harfle yazılmalıdır.

### <a name="actions"></a>Eylemler
Bir zamanlamada birden çok eylem olabilir. Bir eylem, e-posta gönderme veya Runbook başlatma gibi bir veya daha fazla işlemi gerçekleştirebilir veya bir aramanın sonuçlarının bazı ölçütlerle eşleştiğini belirleyen bir eşik tanımlayabilir. Eşik karşılandığında işlemlerin gerçekleştirilmesi için bazı eylemler her ikisini de tanımlayacaktır.
Eylemler, [eylem grubu] kaynağı veya eylem kaynağı kullanılarak tanımlanabilir.

**Tür** özelliği tarafından belirtilen iki tür eylem kaynağı vardır. Bir zamanlama, uyarı kuralının ayrıntılarını ve bir uyarı oluşturulduğunda hangi eylemlerin alındığını tanımlayan bir **Uyarı** eylemi gerektirir. Eylem kaynakları bir tür `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions` sahiptir.

Uyarı eylemleri aşağıdaki yapıya sahiptir. Bu kod parçacığını kopyalayıp çözüm dosyanıza yapıştırarak parametre adlarını değiştirebilmeniz için ortak değişkenleri ve parametreleri içerir.

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

Uyarı eylemi kaynaklarının özellikleri aşağıdaki tablolarda açıklanmıştır.

| Öğe adı | Gereklidir | Açıklama |
|:--|:--|:--|
| `type` | Yes | Eylemin türü.  Bu uyarı eylemleri için **uyarıdır** . |
| `name` | Yes | Uyarı için görünen ad.  Bu, uyarı kuralı için konsolunda görüntülenen addır. |
| `description` | Hayır | Uyarının isteğe bağlı açıklaması. |
| `severity` | Yes | Uyarı kaydının aşağıdaki değerlerden önem derecesi:<br><br> **başlatma**<br>**Warning**<br>**amaçlı**

#### <a name="threshold"></a>Eşiği
Bu bölüm gereklidir. Uyarı eşiğinin özelliklerini tanımlar.

| Öğe adı | Gereklidir | Açıklama |
|:--|:--|:--|
| `Operator` | Yes | Aşağıdaki değerlerden karşılaştırma için işleç:<br><br>**gt = büyüktür <br>lt = küçüktür** |
| `Value` | Yes | Sonuçları karşılaştırmak için değer. |

##### <a name="metricstrigger"></a>Metricçabagger
Bu bölüm isteğe bağlıdır. Ölçüm ölçümü uyarısı için bunu ekleyin.

| Öğe adı | Gereklidir | Açıklama |
|:--|:--|:--|
| `TriggerCondition` | Yes | Eşiğin, şu değerlerden alınan toplam ihlal sayısı veya ardışık ihlal olup olmadığını belirtir:<br><br>**Toplam <br>Consecutive** |
| `Operator` | Yes | Aşağıdaki değerlerden karşılaştırma için işleç:<br><br>**gt = büyüktür <br>lt = küçüktür** |
| `Value` | Yes | Uyarının tetiklenmesi için ölçütlerin karşılanması gereken zaman sayısı. |


#### <a name="throttling"></a>Azaltma
Bu bölüm isteğe bağlıdır. Bir uyarı oluşturulduktan sonra, belirli bir süre için aynı kuraldaki uyarıları bastırmak istiyorsanız bu bölümü ekleyin.

| Öğe adı | Gereklidir | Açıklama |
|:--|:--|:--|
| DurationInMinutes | Daraltma öğesi dahil ise Evet | Aynı uyarı kuralından biri oluşturulduktan sonra uyarıların nasıl gizlenme sayısı. |

#### <a name="azure-action-group"></a>Azure eylem grubu
Tüm uyarılar Azure 'da, eylemleri işlemek için varsayılan mekanizma olarak eylem grubunu kullanın. Eylem grubuyla, eylemlerinizi bir kez belirtebilir ve ardından Eylem grubunu Azure 'da birden çok uyarı ile ilişkilendirebilirsiniz. Gerek olmadan, aynı eylemleri tekrar tekrar tekrar bildirmek için. Eylem grupları e-posta, SMS, sesli çağrı, ıTSM bağlantısı, Otomasyon Runbook 'u, Web kancası URI ve daha fazlasını içeren birden çok eylemi destekler

Uyarıları Azure 'a genişletmiş olan kullanıcı için, bir zamanlamaya göre bir uyarı oluşturabilmek için bir zamanlamanın Işlem grubu ayrıntılarının eşikle birlikte geçirilmesi gerekir. E-posta ayrıntıları, Web kancası URL 'Leri, runbook Otomasyon ayrıntıları ve diğer eylemler, bir uyarı oluşturmadan önce bir eylem grubu içinde tanımlanmalıdır. Portal 'da [Azure izleyici 'den bir eylem grubu](../../azure-monitor/platform/action-groups.md) oluşturabilir veya [eylem grubu-kaynak şablonu](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)' nu kullanabilirsiniz.

| Öğe adı | Gereklidir | Açıklama |
|:--|:--|:--|
| AzNsNotification | Yes | Uyarı ölçütleri karşılandığında gerekli eylemleri gerçekleştirmek üzere uyarı ile ilişkilendirilecek Azure eylem grubunun kaynak KIMLIĞI. |
| CustomEmailSubject | Hayır | İlişkili eylem grubunda belirtilen tüm adreslere gönderilen postanın özel konu satırı. |
| Customwebkancayükü | Hayır | İlişkili eylem grubunda tanımlanan tüm Web kancası uç noktalarına gönderilecek özelleştirilmiş yük. Biçim, Web kancasının beklediği işe bağlıdır ve geçerli bir seri hale getirilmiş JSON olmalıdır. |

## <a name="sample"></a>Örnek

Aşağıdaki kaynakları içeren bir çözümün örneği aşağıda verilmiştir:

- Kayıtlı arama
- Zamanlama
- Eylem grubu

Örnek, kaynak tanımlarındaki değerlerin kodlanması aksine yaygın olarak bir çözümde kullanılan [standart çözüm parametreleri]( solutions-solution-file.md#parameters) değişkenlerini kullanır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

Aşağıdaki parametre dosyası bu çözüm için örnek değerleri sağlar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
* Yönetim çözümünüze [Görünümler ekleyin](solutions-resources-views.md) .
* Yönetim çözümünüze [Otomasyon Runbook 'larını ve diğer kaynakları ekleyin](solutions-resources-automation.md) .
