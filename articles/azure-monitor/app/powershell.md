---
title: Azure Application Insights PowerShell ile otomatikleştirin | Microsoft Docs
description: Azure Resource Manager şablonu kullanarak PowerShell 'de kaynakları, uyarıları ve kullanılabilirlik testlerini oluşturmayı ve yönetmeyi otomatikleştirin.
ms.topic: conceptual
ms.date: 05/02/2020
ms.openlocfilehash: c2e3d33be487b6a92cb7038d814e17fcd5a10064
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589803"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>PowerShell kullanarak Application Insights kaynaklarını yönetme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bu makalede, Azure Kaynak Yönetimi kullanılarak [Application Insights](./app-insights-overview.md) kaynaklarının otomatik olarak oluşturulmasını ve güncelleştirilmesini nasıl otomatikleştirebileceğiniz gösterilmektedir. Örneğin, bir yapı sürecinin bir parçası olarak bunu yapabilirsiniz. Temel Application Insights kaynağıyla birlikte, [kullanılabilirlik Web testleri](./monitor-web-app-availability.md)oluşturabilir, [uyarıları](../alerts/alerts-log.md)ayarlayabilir, [fiyatlandırma şemasını](pricing.md)ayarlayabilir ve diğer Azure kaynaklarını oluşturabilirsiniz.

Bu kaynakları oluşturmaya yönelik anahtar, [Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md)için JSON şablonlarıdır. Temel yordam: mevcut kaynakların JSON tanımlarını indirin; adlar gibi bazı değerleri parametreleştirin; ardından, yeni bir kaynak oluşturmak istediğiniz her seferinde şablonu çalıştırın. Çeşitli kaynakları tek bir şekilde paketleyebilir, örneğin, sürekli dışa aktarma için kullanılabilirlik testleri, uyarılar ve depolama ile bir uygulama izleyicisine sahip olabilirsiniz. Burada açıklayacağımız bazı parametreler için bazı alt değişkenler vardır.

## <a name="one-time-setup"></a>Tek seferlik kurulum
Daha önce Azure aboneliğinizle PowerShell kullanmadıysanız:

Azure PowerShell modülünü, betikleri çalıştırmak istediğiniz makineye yükleyebilirsiniz:

1. [Microsoft Web Platformu Yükleyicisi (V5 veya üzeri)](https://www.microsoft.com/web/downloads/platform.aspx)yükler.
2. Microsoft Azure PowerShell yüklemek için kullanın.

Kaynak Yöneticisi şablonlarını kullanmanın yanı sıra, program aracılığıyla Application Insights kaynaklarını yapılandırmayı kolaylaştıran zengin bir [Application Insights PowerShell cmdlet 'leri](/powershell/module/az.applicationinsights)kümesi vardır. Cmdlet 'ler tarafından etkinleştirilen yetenekler şunlardır:

* Application Insights kaynaklarını oluşturma ve silme
* Application Insights kaynak listelerini ve bunların özelliklerini al
* Sürekli dışarı aktarma oluşturma ve yönetme
* Uygulama anahtarları oluşturma ve yönetme
* Günlük ucunu ayarla
* Fiyatlandırma planını ayarlama

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>PowerShell cmdlet 'i kullanarak Application Insights kaynakları oluşturma

[New-Azapplicationınsights](/powershell/module/az.applicationinsights/new-azapplicationinsights) cmdlet 'Ini kullanarak Azure Doğu ABD veri merkezinde yeni bir Application Insights kaynağı oluşturma işlemi aşağıda verilmiştir:

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak Application Insights kaynakları oluşturma

Bir Kaynak Yöneticisi şablonu kullanarak yeni bir Application Insights kaynağı oluşturma hakkında daha fazla bilgiyi burada bulabilirsiniz.

### <a name="create-the-azure-resource-manager-template"></a>Azure Resource Manager şablonu oluşturma

Yeni bir. JSON dosyası oluşturun-Bu örnekte bunu çağıralım `template1.json` . Bu içeriği buraya kopyalayın:

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
                "defaultValue": 0,
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
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
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

1. PowerShell 'de, kullanarak Azure 'da oturum açın `$Connect-AzAccount`
2. Bağlamını bir abonelik olarak ayarlayın `Set-AzContext "<subscription ID>"`
2. Yeni bir Application Insights kaynak oluşturmak için yeni bir dağıtım çalıştırın:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` , yeni kaynakları oluşturmak istediğiniz gruptur.
   * `-TemplateFile` Özel parametrelerden önce gerçekleşmelidir.
   * `-appName` Oluşturulacak kaynağın adı.

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

Bu cmdlet 'lerin parametreleri için [ayrıntılı belgelere](/powershell/module/az.applicationinsights) başvurun.  

## <a name="set-the-data-retention"></a>Veri bekletmesini ayarlama

Aşağıda, bir Application Insights kaynağında veri bekletmesini programlı bir şekilde ayarlamak için üç yöntem verilmiştir.

### <a name="setting-data-retention-using-a-powershell-commands"></a>PowerShell komutları kullanarak veri saklama ayarlama

Application Insights kaynağınız için veri bekletmesini ayarlamak üzere basit bir PowerShell komutları kümesi aşağıda verilmiştir:

```PS
$Resource = Get-AzResource -ResourceType Microsoft.Insights/components -ResourceGroupName MyResourceGroupName -ResourceName MyResourceName
$Resource.Properties.RetentionInDays = 365
$Resource | Set-AzResource -Force
```

### <a name="setting-data-retention-using-rest"></a>REST kullanarak veri saklama ayarlama

Application Insights kaynağınız için geçerli veri bekletmesini almak için, OSS aracı [Armclient](https://github.com/projectkudu/ARMClient)' ı kullanabilirsiniz.  ( [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) ve [Daniel bowbevet](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)makalelerini kullanarak armclient hakkında daha fazla bilgi edinin.)  Aşağıda `ARMClient` , geçerli bekletmenin elde etmek için kullanılan bir örnek verilmiştir:

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

### <a name="setting-data-retention-using-a-powershell-script"></a>PowerShell betiği kullanarak veri saklama ayarlama

Aşağıdaki betik, bekletme değiştirmek için de kullanılabilir. Bu betiği farklı kaydet 'e kopyalayın `Set-ApplicationInsightsRetention.ps1` .

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

Günlük üst sınır özelliklerini almak için [set-Azapplicationınsiıspricingplan](/powershell/module/az.applicationinsights/set-azapplicationinsightspricingplan) cmdlet 'ini kullanın: 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Günlük üst sınır özelliklerini ayarlamak için aynı cmdlet 'i kullanın. Örneğin, üst sınırı 300 GB/gün olarak ayarlamak için

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

Ayrıca, [Armclient](https://github.com/projectkudu/ARMClient) kullanarak günlük uç parametrelerini alabilir ve ayarlayabilirsiniz.  Geçerli değerleri almak için şunu kullanın:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>Günlük üst sınır sıfırlama süresini ayarlama

Günlük üst sınır sıfırlama süresini ayarlamak için [Armclient](https://github.com/projectkudu/ARMClient)kullanabilirsiniz. Aşağıda `ARMClient` , sıfırlama süresini yeni bir saate ayarlamak için kullanılan bir örnek (Bu örnekte 12:00 UTC) verilmiştir:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Fiyatlandırma planını ayarlama 

Geçerli fiyatlandırma planını almak için [set-Azapplicationınsiıspricingplan](/powershell/module/az.applicationinsights/set-azapplicationinsightspricingplan) cmdlet 'ini kullanın:

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Fiyatlandırma planını ayarlamak için, aynı cmdlet 'i belirtilen şekilde kullanın `-PricingPlan` :  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Ayrıca, yukarıdaki Kaynak Yöneticisi şablonunu kullanarak mevcut bir Application Insights kaynağı üzerinde fiyatlandırma planı ayarlayabilirsiniz. Bu, "Microsoft. Insights/bileşenler" kaynağı ve faturalandırma kaynağından düğüm hariç olabilir `dependsOn` . Örneğin, bunu GB başına plana (eski adıyla temel plan olarak adlandırılır) ayarlamak için şunu çalıştırın:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

`priceCode`Şöyle tanımlanır:

|priceCode|plan|
|---|---|
|1|GB başına (eski adıyla temel plan olarak adlandırılır)|
|2|Düğüm başına (eski adıyla kurumsal planı adlandırın)|

Son olarak, [Armclient](https://github.com/projectkudu/ARMClient) kullanarak fiyatlandırma planları ve günlük uç parametreleri alabilir ve ayarlayabilirsiniz.  Geçerli değerleri almak için şunu kullanın:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

Bu parametreleri kullanarak şu parametreleri de ayarlayabilirsiniz:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Bu, günlük üst sınırı 200 GB/gün olarak ayarlar, günlük uç sıfırlama süresini 12:00 UTC olarak yapılandırır, her ikisi de Cap 'e ulaşıldığında ve uyarı düzeyi karşılandığında e-posta gönderir ve uyarı eşiğini ucun %90 olarak ayarlar.  

## <a name="add-a-metric-alert"></a>Ölçüm uyarısı Ekle

Ölçüm uyarılarının oluşturulmasını otomatik hale getirmek için [ölçüm uyarıları şablonu makalesine](../alerts/alerts-metric-create-templates.md#template-for-a-simple-static-threshold-metric-alert) başvurun


## <a name="add-an-availability-test"></a>Bir kullanılabilirlik testi ekleyin

Kullanılabilirlik testlerini otomatikleştirmek için [ölçüm uyarıları şablonu makalesine](../alerts/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert)başvurun.

## <a name="add-more-resources"></a>Daha fazla kaynak ekleyin

Herhangi bir türdeki başka bir kaynağı oluşturmayı otomatikleştirin, bir örneği el ile oluşturun ve ardından [Azure Resource Manager](https://resources.azure.com/)kodunu kopyalayın ve parametreleştirin. 

1. [Azure Resource Manager](https://resources.azure.com/)açın. `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`Uygulama kaynağına kadar ilerleyin. 
   
    ![Azure Kaynak Gezgini gezinti](./media/powershell/01.png)
   
    *Bileşenler* , uygulamaları görüntülemek için temel Application Insights kaynaklarıdır. İlişkili uyarı kuralları ve kullanılabilirlik Web testleri için ayrı kaynaklar vardır.
2. Bileşenin JSON 'sini içinde uygun yere kopyalayın `template1.json` .
3. Bu özellikleri Sil:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. `webtests`Ve bölümlerini açın `alertrules` ve tek tek öğeler için JSON öğesini şablonunuza kopyalayın. ( `webtests` Veya `alertrules` düğümlerinden kopyalamayın: altındaki öğelere gidin.)
   
    Her Web testinin ilişkili bir uyarı kuralı vardır, bu nedenle her ikisini de kopyalamanız gerekir.
   
5. Her kaynağa bu satırı ekleyin:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Şablonu Parametreleştirme
Artık belirli adları parametrelerle değiştirmeniz gerekir. [Bir şablonu parametreleştirmek](../../azure-resource-manager/templates/template-syntax.md)için, bir [dizi yardımcı işlevi](../../azure-resource-manager/templates/template-functions.md)kullanarak ifadeleri yazarsınız. 

Bir dizenin yalnızca bir kısmını parametreleştirebilirsiniz, bu nedenle `concat()` dizeleri derlemek için kullanın.

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

* Şablon kullanmadan hızlı Yöntem [Application Insights oluşturun](./create-new-resource.md#creating-a-resource-automatically) .
* [Web testleri oluşturma](../alerts/resource-manager-alerts-metric.md#availability-test-with-metric-alert)
* [Azure Tanılama verilerini Application Insights’a gönderme](powershell-azure-diagnostics.md)
* [Sürüm ek açıklamaları oluştur](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)