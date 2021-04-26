---
title: Varlık dönüştürme REST API
description: REST API aracılığıyla bir varlığın nasıl dönüştürüleceğini açıklar
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e3db4b9c9b4a05142f1327f681b067748cb1a2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951649"
---
# <a name="use-the-model-conversion-rest-api"></a>Model dönüştürme REST API’yi kullanma

[Model dönüştürme](model-conversion.md) hizmeti bir [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer)ile denetlenir. Bu API, dönüştürmeler oluşturmak, dönüştürme özelliklerini almak ve var olan dönüştürmeleri listelemek için kullanılabilir.

## <a name="rest-api-reference"></a>REST API başvurusu

Uzaktan Işleme REST API başvuru belgeleri [burada](/rest/api/mixedreality/2021-01-01/remoterendering)ve Swagger tanımlarını [burada](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)bulabilirsiniz.

*Conversion.ps1* adlı *betikler* klasöründeki [ARR örnekleri deposunda](https://github.com/Azure/azure-remote-rendering) , hizmetimizin kullanımını gösteren bir PowerShell betiği sağlıyoruz. Betik ve yapılandırması burada açıklanmıştır: [örnek PowerShell betikleri](../../samples/powershell-example-scripts.md). Ayrıca [.net](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) ve [Java]( https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md)için SDK 'lar sunuyoruz.

## <a name="next-steps"></a>Sonraki adımlar

- [Model dönüştürmesi için Azure Blob depolamayı kullanma](blob-storage.md)
- [Model dönüştürme](model-conversion.md)