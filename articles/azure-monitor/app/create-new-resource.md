---
title: Yeni bir Azure Application Insights kaynağı oluşturma | Microsoft Docs
description: Yeni bir canlı uygulama için Application Insights izlemeyi el ile ayarlayın.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/16/2019
ms.openlocfilehash: be68c70a105737f232741f02994da87c0c628bd7
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677496"
---
# <a name="create-an-application-insights-resource"></a>Application Insights kaynağı oluşturma

Azure Application Insights, uygulamanız hakkındaki verileri bir Microsoft Azure *kaynağında*görüntüler. Bu nedenle yeni bir kaynak oluşturmak, [Yeni bir uygulamayı izlemek için Application Insights ayarlamanın][start]bir parçasıdır. Yeni kaynağınızı oluşturduktan sonra, izleme anahtarını alabilir ve Application Insights SDK 'sını yapılandırmak için kullanabilirsiniz. İzleme anahtarı, telemetrinizi kaynağa bağlar.

## <a name="sign-in-to-microsoft-azure"></a>Microsoft Azure oturum açın

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="create-an-application-insights-resource"></a>Application Insights kaynağı oluşturma

[Azure Portal](https://portal.azure.com)oturum açın ve bir Application Insights kaynağı oluşturun:

![Sol üst köşedeki ' + ' işaretine tıklayın. Geliştirici Araçları sonra Application Insights ' yi seçin](./media/create-new-resource/new-app-insights.png)

   | Ayarlar        |  Değer           | Açıklama  |
   | ------------- |:-------------|:-----|
   | **Adı**      | Genel Olarak Benzersiz Değer | İzlemekte olduğunuz uygulamayı tanımlayan ad. |
   | **Kaynak Grubu**     | myResourceGroup      | App Insights verilerini barındıracak yeni veya mevcut kaynak grubunun adı. |
   | **Konum** | Doğu ABD | Size yakın bir konum seçin veya uygulamanızın nerede barındırıldığını görürsünüz. |

Gerekli alanlara uygun değerleri girin ve ardından **gözden geçir + oluştur**' u seçin.

![Gerekli alanlara değerler girin ve ardından "gözden geçir + oluştur" u seçin.](./media/create-new-resource/review-create.png)

Uygulamanız oluşturulduğunda yeni bir bölme açılır. Bu bölme, izlenen uygulamanızla ilgili performans ve kullanım verilerini görebileceğiniz yerdir. 

## <a name="copy-the-instrumentation-key"></a>İzleme anahtarını kopyalama

İzleme anahtarı, telemetri verilerinizi ilişkilendirmek istediğiniz kaynağı tanımlar. İzleme anahtarını uygulamanızın koduna eklemek için kopyaya ihtiyacınız olacak.

![İzleme anahtarını tıklayın ve kopyalayın](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Uygulamanıza SDK 'Yı yükler

Application Insights SDK 'sını uygulamanıza yükler. Bu adım büyük ölçüde uygulamanızın türüne bağlıdır.

[Uygulamanıza yüklediğiniz SDK 'yı][start]yapılandırmak için izleme anahtarını kullanın.

SDK, ek kod yazmak zorunda kalmadan telemetri gönderen Standart modüller içerir. Kullanıcı eylemlerini izlemek veya sorunları daha ayrıntılı olarak tanılamak için [API 'yi kullanarak][api] kendi telemetrinizi gönderin.

## <a name="creating-a-resource-automatically"></a>Otomatik olarak kaynak oluşturma

### <a name="powershell"></a>PowerShell

Yeni bir Application Insights kaynağı oluşturma

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Örnek

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Sonuçlar

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

Bu cmdlet 'in tam PowerShell belgeleri için ve izleme anahtarını alma hakkında bilgi edinmek için [Azure PowerShell belgelerine](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0)danışın.

### <a name="azure-cli-preview"></a>Azure CLı (Önizleme)

Azure CLı komutlarına önizleme Application Insights erişmek için öncelikle şunu çalıştırmanız gerekir:

```azurecli
 az extension add -n application-insights
```

@No__t_0 komutunu çalıştırmazsanız, şu durumu belirten bir hata iletisi görürsünüz: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Artık Application Insights kaynağınız oluşturmak için aşağıdakileri çalıştırabilirsiniz:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Örnek

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Sonuçlar

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

Bu komutla ilgili tam Azure CLı belgeleri için ve izleme anahtarını alma hakkında bilgi edinmek için bkz. [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

## <a name="next-steps"></a>Sonraki adımlar
* [Tanılama Araması](../../azure-monitor/app/diagnostic-search.md)
* [Ölçümleri keşfetme](../../azure-monitor/app/metrics-explorer.md)
* [Analytics sorguları yazma](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md