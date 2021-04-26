---
title: Azure Işlevlerinde akış yürütme günlükleri
description: boşluk dahil 115-145 karakter. Bu özet, arama sonucunda görüntülenir.
ms.date: 9/1/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4afb1068acda69c9dd65a423d887abea80c695cd
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830907"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>Azure Işlevlerinde akış yürütme günlüklerini etkinleştirme

Bir uygulama geliştirirken genellikle Azure 'da çalışırken neredeyse gerçek zamanlı olarak günlüklere ne yazıldığını görmek istersiniz.

İşlev yürütmeleri tarafından oluşturulan günlük dosyalarının akışını görüntülemenin iki yolu vardır.

* **Yerleşik günlük akışı**: App Service platformu, uygulama günlüğü dosyalarınızın akışını görüntülemenize olanak sağlar. Bu, [yerel geliştirme](functions-develop-local.md) sırasında işlevlerinizi hata ayıkladığınızda ve portalda **Test** sekmesini kullandığınızda görülen çıkış ile eşdeğerdir. Günlük tabanlı tüm bilgiler görüntülenir. Daha fazla bilgi için bkz. [akış günlükleri](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Bu akış yöntemi yalnızca tek bir örneği destekler ve bir tüketim planında Linux üzerinde çalışan bir uygulamayla birlikte kullanılamaz.

* **Canlı ölçüm akışı**: işlev uygulamanız [Application Insights bağlandığı](configure-monitoring.md#enable-application-insights-integration)zaman, Azure Portal [canlı ölçüm akışı](../azure-monitor/app/live-stream.md)kullanarak günlük verilerini ve diğer ölçümleri neredeyse gerçek zamanlı olarak görebilirsiniz. Bir tüketim planında birden çok örnek veya Linux üzerinde çalışan işlevleri izlerken bu yöntemi kullanın. Bu yöntem [örneklenmiş verileri](configure-monitoring.md#configure-sampling)kullanır.

Günlük akışları hem portalda hem de birçok yerel geliştirme ortamında görüntülenebilir. 

## <a name="portal"></a>Portal

Portalda her iki tür günlük akışı görüntüleyebilirsiniz.

### <a name="built-in-log-streaming"></a>Yerleşik günlük akışı

Portalda akış günlüklerini görüntülemek için, işlev uygulamanızda **platform özellikleri** sekmesini seçin. Ardından, **izleme** altında **günlük akışı**' nı seçin.

![Portalda akış günlüklerini etkinleştirme](./media/functions-monitoring/enable-streaming-logs-portal.png)

Bu, uygulamanızı günlük akış hizmetine bağlar ve uygulama günlükleri pencerede görüntülenir. **Uygulama günlükleri** ve **Web sunucusu günlükleri** arasında geçiş yapabilirsiniz.  

![Portalda akış günlüklerini görüntüleme](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>Canlı Ölçüm Akışı

Uygulamanızın Canlı Ölçüm Akışı görüntülemek için, işlev uygulamanızın **genel bakış** sekmesini seçin. Application Insights etkinleştirdiğinizde, **yapılandırılmış özellikler** altında bir **Application Insights** bağlantısı görürsünüz. Bu bağlantı sizi uygulamanızın Application Insights sayfasına götürür.

Application Insights ' de **canlı ölçüm akışı**' ı seçin. [Örneklenir günlük girişleri](configure-monitoring.md#configure-sampling) **örnek telemetri** altında görüntülenir.

![Portalda Canlı Ölçüm Akışı görüntüleme](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Temel araçlar

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/install-azure-cli)kullanarak akış günlüklerini etkinleştirebilirsiniz. Oturum açmak için aşağıdaki komutları kullanın, aboneliğinizi seçin ve günlük dosyalarını akışla açın:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

Akış günlüklerini, [Azure PowerShell](/powershell/azure/)kullanarak etkinleştirebilirsiniz. PowerShell için, aşağıdaki kod parçacığında gösterildiği gibi, işlev uygulamasında günlüğe kaydetmeyi etkinleştirmek için [set-AzWebApp](/powershell/module/az.websites/set-azwebapp) komutunu kullanın: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Daha fazla bilgi için, [tüm kod örneğine](../app-service/scripts/powershell-monitor.md#sample-script)bakın. 

## <a name="next-steps"></a>Sonraki adımlar

+ [Azure İşlevlerini İzleme](functions-monitoring.md)
+ [Application Insights 'de Azure Işlevleri telemetrisini çözümleyin](analyze-telemetry-data.md)
