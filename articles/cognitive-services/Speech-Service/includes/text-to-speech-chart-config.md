---
title: Konuşma kapsayıcılarını yükler
titleSuffix: Azure Cognitive Services
description: Metinden konuşmaya helb grafik yapılandırma seçeneklerinin ayrıntılarını sağlar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002314"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Metinden konuşmaya (alt grafik: grafikler/textToSpeech)

"Şemsiye" grafiğini geçersiz kılmak için, öneki `textToSpeech.` daha özel hale getirmek için herhangi bir parametreye ekleyin. Örneğin, karşılık gelen parametreyi geçersiz kılacak şekilde geçersiz kılar `textToSpeech.numberOfConcurrentRequest` `numberOfConcurrentRequest` .

|Parametre|Açıklama|Varsayılan|
| -- | -- | -- |
| `enabled` | **Metin okuma** hizmetinin etkin olup olmadığı. | `false` |
| `numberOfConcurrentRequest` | **Metinden konuşmaya** hizmeti için eşzamanlı istek sayısı. Bu grafik, CPU ve bellek kaynaklarını bu değere göre otomatik olarak hesaplar. | `2` |
| `optimizeForTurboMode`| Hizmetin metin dosyaları aracılığıyla metin girişi için iyileştirilmesi gerekip gerekmediği. `true`Bu grafik, hizmete daha fazla CPU kaynağı ayırır. | `false` |
| `image.registry`| **Metinden konuşmaya** Docker görüntü kayıt defteri. | `containerpreview.azurecr.io` |
| `image.repository` | **Metinden konuşmaya** Docker görüntü deposu. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | **Metinden konuşmaya** Docker görüntü etiketi. | `latest` |
| `image.pullSecrets` | **Metinden konuşmaya** Docker görüntüsünü çekmek için görüntü gizli dizileri. | |
| `image.pullByHash`| Docker görüntüsünün karma tarafından çekilip çekilmediğini belirtir. Varsa `true` , `image.hash` gereklidir. | `false` |
| `image.hash`| **Metinden konuşmaya** Docker görüntü karması. Yalnızca ne zaman kullanılır `image.pullByHash: true` .  | |
| `image.args.eula` istenir | Lisansı kabul ettiğinizi gösterir. Geçerli tek değer `accept` | |
| `image.args.billing` istenir | Faturalandırma uç noktası URI değeri Azure portal konuşmaya Genel Bakış sayfasında kullanılabilir. | |
| `image.args.apikey` istenir | Faturalandırma bilgilerini izlemek için kullanılır. ||
| `service.type` | **Metin okuma** hizmeti 'Nin Kubernetes hizmet türü. Daha fazla ayrıntı için bkz. [Kubernetes hizmet türleri yönergeleri](https://kubernetes.io/docs/concepts/services-networking/service/) ve bulut sağlayıcısı desteğini doğrulayın. | `LoadBalancer` |
| `service.port`|  **Metin okuma** hizmetinin bağlantı noktası. | `80` |
| `service.annotations` | Hizmet meta verileri için **metinden konuşmaya** ek açıklamaları. Ek açıklamalar anahtar değer çiftleridir. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | [Yatay Pod otomatik Scaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) 'ın etkinleştirilip etkinleştirilmeyeceğini belirtir. İse, `true` `text-to-speech-autoscaler` Kubernetes kümesinde dağıtılır. | `true` |
| `service.podDisruption.enabled` | [Pod kesintisi bütçesinin](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) etkin olup olmadığı. İse, `true` `text-to-speech-poddisruptionbudget` Kubernetes kümesinde dağıtılır. | `true` |