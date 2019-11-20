---
title: Dayanıklı İşlevler sürümlere genel bakış-Azure Işlevleri
description: Dayanıklı İşlevler sürümleri hakkında bilgi edinin.
author: cgillum
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 5d6c8bcf610bfc8900e0f2a5237228208cd633ca
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614544"
---
# <a name="durable-functions-versions-overview"></a>Dayanıklı İşlevler sürümlere genel bakış

*Dayanıklı işlevler* , [Azure Işlevleri](../functions-overview.md) ve [Azure Web işleri](../../app-service/web-sites-create-web-jobs.md) 'nin bir uzantısıdır ve bu da sunucusuz bir ortamda durum bilgisi olan işlevler yazmanızı sağlar. Uzantı sizin için durumu, denetim noktalarını ve yeniden başlatmaları yönetir. Daha önce Dayanıklı İşlevler hakkında bilginiz yoksa [genel bakış belgelerine](durable-functions-overview.md)bakın.

## <a name="new-features-in-2x"></a>2\. x içindeki yeni özellikler

Bu bölümde, sürüm 2. x ' de eklenen Dayanıklı İşlevler özellikleri açıklanmaktadır.

### <a name="durable-entities"></a>Dayanıklı varlıklar

Dayanıklı İşlevler 2. x ' de yeni bir [varlık işlevleri](durable-functions-entities.md) kavramı sunuyoruz.

Varlık işlevleri, *dayanıklı varlıklar*olarak bilinen küçük durum parçalarını okumak ve güncelleştirmek için işlemleri tanımlar. Orchestrator işlevleri gibi, varlık işlevleri de özel tetikleyici türü, *varlık tetikleyicisi*olan işlevlerdir. Orchestrator işlevlerinin aksine, varlık işlevlerinin belirli kod kısıtlamaları yoktur. Varlık işlevleri, durumu denetim akışı aracılığıyla örtük olarak temsil etmek yerine, durumu açıkça da yönetir.

Daha fazla bilgi için bkz. [dayanıklı varlıklar](durable-functions-entities.md) makalesi.

### <a name="durable-http"></a>Kalıcı HTTP

Dayanıklı İşlevler 2. x ' de şunları yapmanıza olanak sağlayan yeni bir [DAYANıKLı http](durable-functions-http-features.md#consuming-http-apis) özelliği sunuyoruz:

* HTTP API 'Lerini doğrudan düzenleme işlevlerinden çağırın (bazı belgelenen sınırlamalar ile).
* Otomatik istemci tarafı HTTP 202 durum yoklamasını uygulayın.
* [Azure Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md)için yerleşik destek.

Daha fazla bilgi için bkz. [http özellikleri](durable-functions-http-features.md#consuming-http-apis) makalesi.

## <a name="migrate-from-1x-to-2x"></a>1\. x 'ten 2. x 'e geçiş

Bu bölümde, yeni özelliklerden yararlanmak için mevcut sürüm 1. x Dayanıklı İşlevler sürüm 2. x ' e nasıl geçirileceği açıklanmaktadır.

### <a name="upgrade-the-extension"></a>Uzantıyı yükseltin

[Dayanıklı işlevler bağlamaları uzantısının](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) sürüm 2. x ' i projenize yükler. Daha fazla bilgi için bkz. [Azure işlevleri bağlama uzantılarını kaydetme](../functions-bindings-register.md) .

### <a name="update-your-code"></a>Kodunuzu güncelleştirme

Dayanıklı İşlevler 2. x birkaç önemli değişiklik sunar. Dayanıklı İşlevler 1. x uygulamaları, kod değişikliği olmadan Dayanıklı İşlevler 2. x ile uyumlu değildir. Bu bölümde, sürüm 1. x işlevinizi 2. x olarak yükseltirken yapmanız gereken bazı değişiklikler listelenmiştir.

#### <a name="hostjson-schema"></a>Host. JSON şeması

Dayanıklı İşlevler 2. x yeni bir Host. JSON şeması kullanır. 1\. x üzerindeki ana değişiklikler şunları içerir:

* depolamaya özgü yapılandırma için `"storageProvider"` (ve `"azureStorage"` alt bölümü).
* izleme ve günlüğe kaydetme yapılandırması için `"tracking"`.
* Event Grid bildirim yapılandırması için `"notifications"` (ve `"eventGrid"` alt bölümü).

Ayrıntılar için [dayanıklı işlevler Host. JSON başvurusu belgelerine](durable-functions-bindings.md#durable-functions-2-0-host-json) bakın.

#### <a name="public-interface-changes-net-only"></a>Ortak arabirim değişiklikleri (yalnızca .NET)

1\. x sürümünde, Dayanıklı İşlevler tarafından desteklenen çeşitli _bağlam_ nesneleri, birim testinde kullanılmak üzere tasarlanan soyut temel sınıflara sahiptir. Dayanıklı İşlevler 2. x ' in bir parçası olarak, bu soyut temel sınıflar, arabirimler ile değiştirilmiştir.

Aşağıdaki tablo, ana değişiklikleri temsil eder:

| 'in | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` veya `IDurableClient` |
| `DurableOrchestrationContext` veya `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` veya `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

Soyut bir temel sınıfın sanal yöntemler içerdiği durumlarda, bu sanal yöntemler `DurableContextExtensions`tanımlı uzantı yöntemleriyle değiştirilmiştir.

#### <a name="functionjson-changes-javascript-and-c-script"></a>function. JSON değişiklikleri (JavaScript ve C# betik)

Dayanıklı İşlevler 1. x içinde Orchestration istemci bağlaması `orchestrationClient``type` kullanır. Sürüm 2. x bunun yerine `durableClient` kullanır.
