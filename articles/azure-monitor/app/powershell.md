---
title: Azure Application Insights PowerShell ile otomatikleştirin | Microsoft Docs
description: Azure Resource Manager şablonu kullanarak PowerShell 'de kaynakları, uyarıları ve kullanılabilirlik testlerini oluşturmayı ve yönetmeyi otomatikleştirin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/17/2019
ms.openlocfilehash: 2f59b66bacbd0105e5a543b019a5c6646e010c48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928528"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>PowerShell kullanarak Application Insights kaynaklarını yönetme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bu makalede, Azure Kaynak Yönetimi kullanılarak [Application Insights](../../azure-monitor/app/app-insights-overview.md) kaynaklarının otomatik olarak oluşturulmasını ve güncelleştirilmesini nasıl otomatikleştirebileceğiniz gösterilmektedir. Örneğin, bir yapı sürecinin bir parçası olarak bunu yapabilirsiniz. Temel Application Insights kaynağıyla birlikte, [kullanılabilirlik Web testleri](../../azure-monitor/app/monitor-web-app-availability.md)oluşturabilir, [uyarıları](../../azure-monitor/app/alerts.md)ayarlayabilir, [fiyatlandırma şemasını](pricing.md)ayarlayabilir ve diğer Azure kaynaklarını oluşturabilirsiniz.

Bu kaynakları oluşturmaya yönelik anahtar, [Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md)için JSON şablonlarıdır. Temel yordam: mevcut kaynakların JSON tanımlarını indirin; adlar gibi bazı değerleri parametreleştirin; ardından, yeni bir kaynak oluşturmak istediğiniz her seferinde şablonu çalıştırın. Çeşitli kaynakları tek bir şekilde paketleyebilir, örneğin, sürekli dışa aktarma için kullanılabilirlik testleri, uyarılar ve depolama ile bir uygulama izleyicisine sahip olabilirsiniz. Burada açıklayacağımız bazı parametreler için bazı alt değişkenler vardır.

## <a name="one-time-setup"></a>Tek seferlik kurulum
Daha önce Azure aboneliğinizle PowerShell kullanmadıysanız:

Azure PowerShell modülünü, betikleri çalıştırmak istediğiniz makineye yükleyebilirsiniz:

1. [Microsoft Web Platformu Yükleyicisi (V5 veya üzeri)](https://www.microsoft.com/web/downloads/platform.aspx)yükler.
2. PowerShell 'i Microsoft Azure yüklemek için kullanın.

Kaynak Yöneticisi şablonlarını kullanmanın yanı sıra, program aracılığıyla Application Insights kaynaklarını yapılandırmayı kolaylaştıran zengin bir [Application Insights PowerShell cmdlet 'leri](https://docs.microsoft.com/powershell/module/az.applicationinsights)kümesi vardır. Cmdlet 'ler tarafından etkinleştirilen yetenekler şunlardır:

* Application Insights kaynaklarını oluşturma ve silme
* Application Insights kaynak listelerini ve bunların özelliklerini al
* Sürekli dışarı aktarma oluşturma ve yönetme
* Uygulama anahtarları oluşturma ve yönetme
* Günlük ucunu ayarla
* Fiyatlandırma planını ayarlama

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>PowerShell cmdlet 'i kullanarak Application Insights kaynakları oluşturma

[New-Azapplicationınsights](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights) cmdlet 'Ini kullanarak Azure Doğu ABD veri merkezinde yeni bir Application Insights kaynağı oluşturma işlemi aşağıda verilmiştir:

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak Application Insights kaynakları oluşturma

Bir Kaynak Yöneticisi şablonu kullanarak yeni bir Application Insights kaynağı oluşturma hakkında daha fazla bilgiyi burada bulabilirsiniz.

### <a name="create-the-azure-resource-manager-template"></a>Azure Resource Manager şablonu oluşturma

Yeni bir. JSON dosyası oluşturun-Bu örnekte `template1.json` çağıralım. Bu içeriği buraya kopyalayın:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "retentionInDays": "[parameters('retentionInDays')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Yeni bir Application Insights kaynağı oluşturmak için Kaynak Yöneticisi şablonunu kullanın

1. PowerShell 'de `$Connect-AzAccount` kullanarak Azure 'da oturum açın
2. `Set-AzContext "<subscription ID>"` ile bağlamını bir aboneliğe ayarlayın
2. Yeni bir Application Insights kaynak oluşturmak için yeni bir dağıtım çalıştırın:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * yeni kaynakları oluşturmak istediğiniz grup `-ResourceGroupName`.
   * `-TemplateFile` özel parametrelerden önce oluşmalıdır.
   * oluşturulacak kaynağın adını `-appName`.

Başka parametreler ekleyebilirsiniz. bu kişilerin açıklamalarını, şablonun parametreler bölümünde bulacaksınız.

## <a name="get-the-instrumentation-key"></a>İzleme anahtarını al

Uygulama kaynağı oluşturduktan sonra, izleme anahtarını isteyeceksiniz: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Application Insights kaynağınızın pek çok diğer özelliklerinin listesini görmek için şunu kullanın:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Cmdlet 'ler aracılığıyla ek özellikler mevcuttur:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Bu cmdlet 'lerin parametreleri için [ayrıntılı belgelere](https://docs.microsoft.com/powershell/module/az.applicationinsights) başvurun.  

## <a name="set-the-data-retention"></a>Veri bekletmesini ayarlama 

Application Insights kaynağınız için geçerli veri bekletmesini almak için, OSS aracı [Armclient](https://github.com/projectkudu/ARMClient)' ı kullanabilirsiniz.  ( [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) ve [Daniel bowbevet](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)makalelerini kullanarak armclient hakkında daha fazla bilgi edinin.)  Geçerli saklama süresini almak için `ARMClient`kullanarak bir örnek aşağıda verilmiştir:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Bekletme ayarlamak için, komut benzer bir PUT olur:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Yukarıdaki şablonu kullanarak veri bekletmesini 365 güne ayarlamak için şunu çalıştırın:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

Aşağıdaki betik, bekletme değiştirmek için de kullanılabilir. `Set-ApplicationInsightsRetention.ps1`olarak kaydetmek için bu betiği kopyalayın.

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Bu betik daha sonra şu şekilde kullanılabilir:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Günlük ucunu ayarla

Günlük üst sınır özelliklerini almak için [set-Azapplicationınsiıspricingplan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) cmdlet 'ini kullanın: 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Günlük üst sınır özelliklerini ayarlamak için aynı cmdlet 'i kullanın. Örneğin, üst sınırı 300 GB/gün olarak ayarlamak için 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Fiyatlandırma planını ayarlama 

Geçerli fiyatlandırma planını almak için [set-Azapplicationınsiıspricingplan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) cmdlet 'ini kullanın: 

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Fiyatlandırma planını ayarlamak için, belirtilen `-PricingPlan` aynı cmdlet 'i kullanın:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Ayrıca, faturalandırma kaynağından "Microsoft. Insights/Components" kaynağı ve `dependsOn` düğümünü atlayarak yukarıdaki Kaynak Yöneticisi şablonunu kullanarak mevcut bir Application Insights kaynağı için fiyatlandırma planı ayarlayabilirsiniz. Örneğin, bunu GB başına plana (eski adıyla temel plan olarak adlandırılır) ayarlamak için şunu çalıştırın:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

|priceCode|plan|
|---|---|
|1|GB başına (eski adıyla temel plan olarak adlandırılır)|
|2|Düğüm başına (eski adıyla kurumsal planı adlandırın)|

## <a name="add-a-metric-alert"></a>Ölçüm uyarısı Ekle

Bir ölçüm uyarısını uygulama kaynağınız ile aynı anda ayarlamak için aşağıdaki kodu şablon dosyası ile birleştirin:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Şablonu çağırdığınızda, isteğe bağlı olarak şu parametreyi ekleyebilirsiniz:

    `-responseTime 2`

Tabii ki diğer alanları parametreleştirebilirsiniz. 

Diğer uyarı kurallarının tür adlarını ve yapılandırma ayrıntılarını bulmak için, el ile bir kural oluşturun ve sonra [Azure Resource Manager](https://resources.azure.com/)inceleyin. 


## <a name="add-an-availability-test"></a>Bir kullanılabilirlik testi ekleyin

Bu örnek, ping testi için (tek bir sayfayı test etmek için).  

Bir kullanılabilirlik testinde **iki bölüm vardır** : test kendisi ve hatalara bildirim veren uyarı.

Aşağıdaki kodu, uygulamayı oluşturan şablon dosyasında birleştirin.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Diğer test konumlarına ait kodları keşfetme veya daha karmaşık Web testi oluşturmayı otomatikleştirin, el ile bir örnek oluşturun ve sonra [Azure Resource Manager](https://resources.azure.com/)kodu parametreleştirin.

## <a name="add-more-resources"></a>Daha fazla kaynak ekleyin

Herhangi bir türdeki başka bir kaynağı oluşturmayı otomatikleştirin, bir örneği el ile oluşturun ve ardından [Azure Resource Manager](https://resources.azure.com/)kodunu kopyalayın ve parametreleştirin. 

1. [Azure Resource Manager](https://resources.azure.com/)açın. `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, uygulama kaynağınızın altına gidin. 
   
    ![Azure Kaynak Gezgini gezinti](./media/powershell/01.png)
   
    *Bileşenler* , uygulamaları görüntülemek için temel Application Insights kaynaklarıdır. İlişkili uyarı kuralları ve kullanılabilirlik Web testleri için ayrı kaynaklar vardır.
2. Bileşenin JSON 'sini `template1.json`uygun yere kopyalayın.
3. Bu özellikleri Sil:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. `webtests` ve `alertrules` bölümlerini açın ve tek tek öğeler için JSON öğesini şablonunuza kopyalayın. (`webtests` veya `alertrules` düğümlerinden kopyalamayın: altındaki öğelere gidin.)
   
    Her Web testinin ilişkili bir uyarı kuralı vardır, bu nedenle her ikisini de kopyalamanız gerekir.
   
    Ölçümlere uyarıları da dahil edebilirsiniz. [Ölçüm adları](powershell-alerts.md#metric-names).
5. Her kaynağa bu satırı ekleyin:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Şablonu Parametreleştirme
Artık belirli adları parametrelerle değiştirmeniz gerekir. [Bir şablonu parametreleştirmek](../../azure-resource-manager/resource-group-authoring-templates.md)için, bir [dizi yardımcı işlevi](../../azure-resource-manager/resource-group-template-functions.md)kullanarak ifadeleri yazarsınız. 

Bir dizenin yalnızca bir kısmını parametreleştirebilirsiniz, bu nedenle dizeleri derlemek için `concat()` kullanın.

Yapmak istediğiniz değişimlerin örnekleri aşağıda verilmiştir. Her değiştirmenin çeşitli oluşumları vardır. Şablonunuzda başkalarının olması gerekebilir. Bu örnekler, şablonun üst kısmında tanımladığımız parametreleri ve değişkenleri kullanır.

| find | Değiştir |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (küçük harf) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Kaynaklar arasında bağımlılıkları ayarlama
Azure, kaynakları katı sırayla ayarlamalıdır. Bir kurulumun bir sonraki başlamadan önce tamamlandığından emin olmak için, bağımlılık satırlarını ekleyin:

* Kullanılabilirlik testi kaynağında:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* Bir kullanılabilirlik testinin uyarı kaynağında:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Sonraki adımlar
Diğer otomasyon makaleleri:

* Şablon kullanmadan hızlı Yöntem [Application Insights oluşturun](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) .
* [Uyarıları ayarlama](powershell-alerts.md)
* [Web testleri oluştur](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Azure Tanılama verilerini Application Insights’a gönderme](powershell-azure-diagnostics.md)
* [GitHub 'dan Azure 'a dağıtma](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Sürüm ek açıklamaları oluştur](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)
