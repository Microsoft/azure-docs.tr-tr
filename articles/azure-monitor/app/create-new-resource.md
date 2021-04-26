---
title: Yeni bir Azure Application Insights kaynağı oluşturma | Microsoft Docs
description: Yeni bir canlı uygulama için Application Insights izlemeyi el ile ayarlayın.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 6158b5604046897e20053c67321f26d650c21b7f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566231"
---
# <a name="create-an-application-insights-resource"></a>Application Insights kaynağı oluşturma

Azure Application Insights, uygulamanız hakkındaki verileri bir Microsoft Azure *kaynağında* görüntüler. Bu nedenle yeni bir kaynak oluşturmak, [Yeni bir uygulamayı izlemek için Application Insights ayarlamanın][start]bir parçasıdır. Yeni kaynağınızı oluşturduktan sonra, izleme anahtarını alabilir ve Application Insights SDK 'sını yapılandırmak için kullanabilirsiniz. İzleme anahtarı, telemetrinizi kaynağa bağlar.

> [!IMPORTANT]
> [Klasik Application Insights kullanım dışı bırakıldı](https://azure.microsoft.com/updates/we-re-retiring-classic-application-insights-on-29-february-2024/). Lütfen [çalışma alanı tabanlı Application Insights yükseltme](convert-classic-resource.md)hakkında daha fazla bilgi için bu yönergeleri izleyin.

## <a name="sign-in-to-microsoft-azure"></a>Microsoft Azure oturum açın

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="create-an-application-insights-resource"></a>Application Insights kaynağı oluşturma

[Azure Portal](https://portal.azure.com)oturum açın ve bir Application Insights kaynağı oluşturun:

![Sol üst köşedeki ' + ' işaretine tıklayın. Geliştirici Araçları sonra Application Insights ' yi seçin](./media/create-new-resource/new-app-insights.png)

   | Ayarlar        |  Değer           | Açıklama  |
   | ------------- |:-------------|:-----|
   | **Ad**      | `Unique value` | İzlemekte olduğunuz uygulamayı tanımlayan ad. |
   | **Kaynak Grubu**     | `myResourceGroup`      | App Insights verilerini barındıracak yeni veya mevcut kaynak grubunun adı. |
   | **Bölge** | `East US` | Size yakın bir konum seçin veya uygulamanızın nerede barındırıldığını görürsünüz. |
   | **Kaynak modu** | `Classic` veya `Workspace-based` | Çalışma alanı tabanlı kaynaklar Application Insights telemetrinizi ortak bir Log Analytics çalışma alanına göndermenizi sağlar. Daha fazla bilgi için [çalışma alanı tabanlı kaynaklardaki makaleye](create-workspace-resource.md)bakın.

> [!NOTE]
> Aynı kaynak adını farklı kaynak grupları arasında kullanabilmeniz mümkün olsa da, genel olarak benzersiz bir ad kullanılması yararlı olabilir. Bu, gerekli sözdizimini basitleştirdiği için [çapraz kaynak sorguları gerçekleştirmeyi](../logs/cross-workspace-query.md#identifying-an-application) planlıyorsanız yararlı olabilir.

Gerekli alanlara uygun değerleri girin ve ardından **gözden geçir + oluştur**' u seçin.

> [!div class="mx-imgBorder"]
> ![Gerekli alanlara değerler girin ve ardından "gözden geçir + oluştur" u seçin.](./media/create-new-resource/review-create.png)

Uygulamanız oluşturulduğunda yeni bir bölme açılır. Bu bölme, izlenen uygulamanızla ilgili performans ve kullanım verilerini görebileceğiniz yerdir. 

## <a name="copy-the-instrumentation-key"></a>İzleme anahtarını kopyalama

İzleme anahtarı, telemetri verilerinizi ilişkilendirmek istediğiniz kaynağı tanımlar. İzleme anahtarını kopyalamanız ve uygulamanızın koduna eklemeniz gerekir.

> [!IMPORTANT]
> Yeni Azure bölgeleri, izleme anahtarları yerine bağlantı dizelerinin kullanılmasını **gerektirir** . [Bağlantı dizesi](./sdk-connection-string.md?tabs=net) , telemetri verilerinizi ilişkilendirmek istediğiniz kaynağı tanımlar. Ayrıca, kaynağınızın telemetri için hedef olarak kullanacağı uç noktaları değiştirmenize de olanak tanır. Bağlantı dizesini kopyalamanız ve uygulamanızın koduna veya bir ortam değişkenine eklemeniz gerekir.

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

Bu cmdlet 'in tam PowerShell belgeleri için ve izleme anahtarını alma hakkında bilgi edinmek için [Azure PowerShell belgelerine](/powershell/module/az.applicationinsights/new-azapplicationinsights)danışın.

### <a name="azure-cli-preview"></a>Azure CLı (Önizleme)

Azure CLı komutlarına önizleme Application Insights erişmek için öncelikle şunu çalıştırmanız gerekir:

```azurecli
 az extension add -n application-insights
```

Komutu çalıştırmazsanız `az extension add` , şu durumlarda bir hata iletisi görürsünüz: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

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

Bu komutla ilgili tam Azure CLı belgeleri için ve izleme anahtarını alma hakkında bilgi edinmek için bkz. [Azure CLI belgeleri](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create).

## <a name="next-steps"></a>Sonraki adımlar
* [Tanılama Araması](./diagnostic-search.md)
* [Ölçümleri keşfetme](../essentials/metrics-charts.md)
* [Analytics sorguları yazma](../logs/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../essentials/metrics-charts.md
[start]: ./app-insights-overview.md

