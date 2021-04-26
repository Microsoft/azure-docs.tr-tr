---
title: Azure Işlevlerinde .NET ve .NET Core uygulamaları için Snapshot Debugger etkinleştirme | Microsoft Docs
description: Azure Işlevlerinde .NET ve .NET Core uygulamaları için Snapshot Debugger etkinleştirme
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: 48eb3cf81384446a07fea69572ac16e0b80cee38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025637"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>Azure Işlevlerinde .NET ve .NET Core uygulamaları için Snapshot Debugger etkinleştirme

Snapshot Debugger Şu anda Windows hizmet planlarındaki Azure Işlevleri üzerinde çalışan ASP.NET ve ASP.NET Core uygulamaları için çalışır.

Snapshot Debugger kullanırken uygulamanızı temel hizmet katmanında veya daha yüksek bir sürüme çalıştırmanızı öneririz.

Çoğu uygulama için, ücretsiz ve paylaşılan hizmet katmanlarında anlık görüntüleri kaydetmek için yeterli bellek veya disk alanı yoktur.

## <a name="prerequisites"></a>Önkoşullar

* [İşlev Uygulaması Application Insights izlemeyi etkinleştirme](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>Snapshot Debugger etkinleştir

Farklı bir Azure hizmeti türü çalıştırıyorsanız, desteklenen diğer platformlarda Snapshot Debugger etkinleştirme talimatları aşağıda verilmiştir:
* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric Hizmetleri](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure sanal makineleri ve sanal makine ölçek kümeleri](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Şirket içi sanal veya fiziksel makineler](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Işlev uygulamanızda Snapshot Debugger etkinleştirmek için, `host.json` özelliği `snapshotConfiguration` aşağıda tanımlanan şekilde ekleyerek ve işlevinizi yeniden dağıtarak dosyanızı güncelleştirmeniz gerekir.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

Snapshot Debugger, varsayılan olarak devre dışı bırakılmış olan Azure Işlevleri çalışma zamanının bir parçası olarak önceden yüklenmiştir.

Snapshot Debugger Azure Işlevleri çalışma zamanına eklendiğinden, ek NuGet paketleri ve uygulama ayarları eklemek gerekmez.

Aynı şekilde, basit bir Işlev uygulaması (.NET Core) için, aşağıdaki `.csproj` `{Your}Function.cs` Snapshot Debugger,, ve üzerinde etkinleştirildikten sonra nasıl görünecektir `host.json` .

Proje csproj

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Function Sınıfı

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

Ana bilgisayar dosyası

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Diğer bulutlar için Snapshot Debugger etkinleştir

Şu anda yalnızca uç nokta değişiklikleri gerektiren bölgeler [Azure Kamu](../../azure-government/compare-azure-government-global-azure.md#application-insights) ve [Azure Çin](/azure/china/resources-developer-guide)' dir.

Aşağıda `host.json` ABD kamu bulut Aracısı uç noktası ile güncelleştirilmiş bir örneği verilmiştir:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

Snapshot Debugger Aracı uç noktasının desteklenen geçersiz kılmaları aşağıda verilmiştir:

|Özellik    | ABD kamu bulutu | Çin bulutu |   
|---------------|---------------------|-------------|
|Tendtendpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

## <a name="disable-snapshot-debugger"></a>Snapshot Debugger devre dışı bırak

Işlev uygulamanızda Snapshot Debugger devre dışı bırakmak için, yalnızca `host.json` özelliğine ayarlayarak dosyanızı güncelleştirmeniz gerekir `false` `snapshotConfiguration.isEnabled` .

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

Uygulama özel durumlarının tanılamayı kolaylaştırmak için tüm uygulamalarınızda Snapshot Debugger etkinleştirilmiş olması önerilir.

## <a name="next-steps"></a>Sonraki adımlar

- Uygulamanıza bir özel durum tetikleyebilmesi için trafik oluşturun. Ardından, anlık görüntülerin Application Insights örneğine gönderilmesi için 10 ila 15 dakika bekleyin.
- Azure portal [anlık görüntüleri görüntüleyin](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) .
- Işlev uygulamanızda kullanım örneğine göre Snapshot Debugger yapılandırmayı özelleştirin. Daha fazla bilgi için bkz. [host.jsüzerinde anlık görüntü yapılandırması](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration).
- Sorunları giderme Snapshot Debugger konusunda yardım için bkz. [Snapshot Debugger sorun giderme](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).