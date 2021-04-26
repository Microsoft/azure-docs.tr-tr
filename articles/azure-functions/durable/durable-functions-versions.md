---
title: Dayanıklı İşlevler sürümlere genel bakış-Azure Işlevleri
description: Dayanıklı İşlevler sürümleri hakkında bilgi edinin.
author: cgillum
ms.topic: conceptual
ms.date: 12/23/2020
ms.author: azfuncdf
ms.openlocfilehash: c4d10bab06428295bbc8c5319bd47787d7b1fb34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763379"
---
# <a name="durable-functions-versions-overview"></a>Dayanıklı İşlevler sürümlere genel bakış

*Dayanıklı işlevler* , [Azure Işlevleri](../functions-overview.md) ve [Azure Web işleri](../../app-service/webjobs-create.md) 'nin bir uzantısıdır ve bu da sunucusuz bir ortamda durum bilgisi olan işlevler yazmanızı sağlar. Uzantı sizin için durumu, denetim noktalarını ve yeniden başlatmaları yönetir. Daha önce Dayanıklı İşlevler hakkında bilginiz yoksa [genel bakış belgelerine](durable-functions-overview.md)bakın.

## <a name="new-features-in-2x"></a>2. x içindeki yeni özellikler

Bu bölümde, sürüm 2. x ' de eklenen Dayanıklı İşlevler özellikleri açıklanmaktadır.

### <a name="durable-entities"></a>Dayanıklı varlıklar

Dayanıklı İşlevler 2. x ' de yeni bir [varlık işlevleri](durable-functions-entities.md) kavramı sunuyoruz.

Varlık işlevleri, *dayanıklı varlıklar* olarak bilinen küçük durum parçalarını okumak ve güncelleştirmek için işlemleri tanımlar. Orchestrator işlevleri gibi, varlık işlevleri de özel tetikleyici türü, *varlık tetikleyicisi* olan işlevlerdir. Orchestrator işlevlerinin aksine, varlık işlevlerinin belirli kod kısıtlamaları yoktur. Varlık işlevleri, durumu denetim akışı aracılığıyla örtük olarak temsil etmek yerine, durumu açıkça da yönetir.

Daha fazla bilgi için bkz. [dayanıklı varlıklar](durable-functions-entities.md) makalesi.

### <a name="durable-http"></a>Kalıcı HTTP

Dayanıklı İşlevler 2. x ' de şunları yapmanıza olanak sağlayan yeni bir [DAYANıKLı http](durable-functions-http-features.md#consuming-http-apis) özelliği sunuyoruz:

* HTTP API 'Lerini doğrudan düzenleme işlevlerinden çağırın (bazı belgelenen sınırlamalar ile).
* Otomatik istemci tarafı HTTP 202 durum yoklamasını uygulayın.
* [Azure Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md)için yerleşik destek.

Daha fazla bilgi için bkz. [http özellikleri](durable-functions-http-features.md#consuming-http-apis) makalesi.

## <a name="migrate-from-1x-to-2x"></a>1. x 'ten 2. x 'e geçiş

Bu bölümde, yeni özelliklerden yararlanmak için mevcut sürüm 1. x Dayanıklı İşlevler sürüm 2. x ' e nasıl geçirileceği açıklanmaktadır.

### <a name="upgrade-the-extension"></a>Uzantıyı yükseltin

Dayanıklı İşlevler bağlamaları uzantısının en son 2. x sürümünü projenize yükler.

#### <a name="javascript-python-and-powershell"></a>JavaScript, Python ve PowerShell

Dayanıklı İşlevler 2. x [Azure işlevleri Uzantı paketi](../functions-bindings-register.md#extension-bundles)'nin 2. x sürümünde kullanılabilir.

Dayanıklı İşlevler 'de Python desteği için Dayanıklı İşlevler 2. x gerekir.

Projenizdeki Uzantı paketi sürümünü güncelleştirmek için üzerinde host.jsaçın ve `extensionBundle` bölümü 2. x () kullanacak şekilde güncelleştirin `[2.*, 3.0.0)` .

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

> [!NOTE]
> Uzantı paketi sürümünü değiştirdikten sonra doğru şablonlar görüntülenmiyorsa Visual Studio Code, *Geliştirici: pencereyi yeniden yükle* komutunu çalıştırarak pencereyi yeniden yükleyin (Windows ve Linux 'ta <kbd>CTRL + r</kbd> , MacOS 'ta <kbd>Command + r</kbd> ).

#### <a name="net"></a>.NET

.NET projenizi [dayanıklı işlevler bağlamaları uzantısının](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)en son sürümünü kullanacak şekilde güncelleştirin.

Daha fazla bilgi için bkz. [Azure işlevleri bağlama uzantılarını kaydetme](../functions-bindings-register.md#local-csharp) .

### <a name="update-your-code"></a>Kodunuzu güncelleştirme

Dayanıklı İşlevler 2. x birkaç önemli değişiklik sunar. Dayanıklı İşlevler 1. x uygulamaları, kod değişikliği olmadan Dayanıklı İşlevler 2. x ile uyumlu değildir. Bu bölümde, sürüm 1. x işlevinizi 2. x olarak yükseltirken yapmanız gereken bazı değişiklikler listelenmiştir.

#### <a name="hostjson-schema"></a>Şemada Host.js

Dayanıklı İşlevler 2. x, şema üzerinde yeni bir host.jskullanır. 1. x üzerindeki ana değişiklikler şunları içerir:

* `"storageProvider"``"azureStorage"`depolama birimine özgü yapılandırma için (ve alt bölümü).
* `"tracing"` izleme ve günlük yapılandırma için.
* `"notifications"``"eventGrid"`olay Kılavuzu bildirim yapılandırması için (ve alt bölümü).

Ayrıntılar için [başvuru belgelerindeki Dayanıklı İşlevler host.js](durable-functions-bindings.md#durable-functions-2-0-host-json) bakın.

#### <a name="default-taskhub-name-changes"></a>Varsayılan taskhub adı değişiklikleri

Sürüm 1. x ' te, üzerinde host.jsbir görev hub 'ı adı belirtilmemişse, varsayılan olarak "DurableFunctionsHub" olarak kabul edildi. Sürüm 2. x ' de, varsayılan görev merkezi adı artık işlev uygulamasının adından türetilir. Bu nedenle, 2. x sürümüne yükseltirken bir görev hub 'ı adı belirtmiyorsanız, kodunuz yeni görev hub 'ı ile çalışır ve tüm uçuş düzenlemeleri artık bunları işleyen bir uygulamaya sahip olmayacaktır. Bu sorunu geçici olarak çözmek için, görev hub 'ınızın adını "DurableFunctionsHub" nin varsayılan değeri olan v1. x varsayılan olarak ayarlayabilir veya kesintiye neden olan değişiklikleri nasıl işleyebileceğine ilişkin ayrıntılar için [sıfır kesinti dağıtım kılavuzumuzu](durable-functions-zero-downtime-deployment.md) takip edebilirsiniz.

#### <a name="public-interface-changes-net-only"></a>Ortak arabirim değişiklikleri (yalnızca .NET)

1. x sürümünde, Dayanıklı İşlevler tarafından desteklenen çeşitli _bağlam_ nesneleri, birim testinde kullanılmak üzere tasarlanan soyut temel sınıflara sahiptir. Dayanıklı İşlevler 2. x ' in bir parçası olarak, bu soyut temel sınıflar, arabirimler ile değiştirilmiştir.

Aşağıdaki tablo, ana değişiklikleri temsil eder:

| 'in | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` veya `IDurableClient` |
| `DurableOrchestrationContext` veya `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` veya `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

Soyut bir temel sınıfın sanal yöntemler içerdiği durumlarda, bu sanal yöntemler ' de tanımlanan genişletme yöntemleriyle değiştirilmiştir `DurableContextExtensions` .

#### <a name="functionjson-changes-javascript-and-c-script"></a>Değişiklikler üzerinde function.js(JavaScript ve C# betiği)

Dayanıklı İşlevler 1. x içinde Orchestration istemci bağlaması ' a kullanır `type` `orchestrationClient` . Bunun yerine sürüm 2. x kullanır `durableClient` .

#### <a name="raise-event-changes"></a>Olay değişikliklerini yükselt

Dayanıklı İşlevler 1. x içinde, oluşturma [olayı](durable-functions-external-events.md#send-events) API 'sini çağırmak ve var olmayan bir örneği belirtmek sessiz bir hata ile sonuçlandı. 2. x ' den başlayarak, bir olayı var olmayan bir düzenlemeye yükseltmek bir özel durumla sonuçlanır.
