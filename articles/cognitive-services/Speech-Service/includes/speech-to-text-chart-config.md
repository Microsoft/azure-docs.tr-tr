---
title: Konuşma kapsayıcılarını yükler
titleSuffix: Azure Cognitive Services
description: Konuşmadan metne hele grafik yapılandırma seçeneklerinin ayrıntılarını sağlar.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: 3f390affe7badb401277aa86d1867c763aa0ae3b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971347"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Konuşmayı metne dönüştürme (alt grafik: grafikler/speechToText)

"Şemsiye" grafiğini geçersiz kılmak için, öneki `speechToText.` daha özel hale getirmek için herhangi bir parametreye ekleyin. Örneğin, karşılık gelen parametreyi `speechToText.numberOfConcurrentRequest` `numberOfConcurrentRequest`geçersiz kılacak şekilde geçersiz kılar.

|Parametre|Açıklama|Varsayılan|
| -- | -- | -- |
| `enabled` | **Konuşmaya metin** hizmetinin etkinleştirilip etkinleştirilmeyeceğini belirtir. | `false` |
| `numberOfConcurrentRequest` | **Konuşmaya metin** hizmetine yönelik eşzamanlı istek sayısı. Bu grafik, CPU ve bellek kaynaklarını bu değere göre otomatik olarak hesaplar. | `2` |
| `optimizeForAudioFile`| Hizmetin ses dosyaları aracılığıyla ses girişi için iyileştirilmesi gerekip gerekmediği. Bu `true`grafik, hizmete daha fazla CPU kaynağı ayırır. | `false` |
| `image.registry`| **Konuşmadan metne** Docker görüntü kayıt defteri. | `containerpreview.azurecr.io` |
| `image.repository` | **Konuşmadan metne** Docker görüntü deposu. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **Konuşmayı metne** Docker resim etiketi. | `latest` |
| `image.pullSecrets` | **Konuşmayı metne** dönüştürme Docker görüntüsünü çekmek için görüntü gizli dizileri. | |
| `image.pullByHash`| Docker görüntüsünün karma tarafından çekilip çekilmediğini belirtir. Varsa `true` ,`image.hash` gereklidir. | `false` |
| `image.hash`| **Konuşmadan metne** Docker görüntü karması. Yalnızca ne zaman `image.pullByHash: true`kullanılır.  | |
| `image.args.eula`istenir | Lisansı kabul ettiğinizi gösterir. Geçerli tek değer`accept` | |
| `image.args.billing`istenir | Faturalandırma uç noktası URI değeri Azure portal konuşmaya Genel Bakış sayfasında kullanılabilir. | |
| `image.args.apikey`istenir | Fatura bilgileri izlemek için kullanılır. ||
| `service.type` | **Konuşma-metin** hizmetinin Kubernetes hizmet türü. Daha fazla ayrıntı için bkz. [Kubernetes hizmet türleri yönergeleri](https://kubernetes.io/docs/concepts/services-networking/service/) ve bulut sağlayıcısı desteğini doğrulayın. | `LoadBalancer` |
| `service.port`|  **Konuşmadan metne** hizmetinin bağlantı noktası. | `80` |
| `service.annotations` | Hizmet meta verileri için **konuşmaya metin** ek açıklamaları. Ek açıklamalar anahtar değer çiftleridir. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | [Yatay Pod otomatik Scaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) 'ın etkinleştirilip etkinleştirilmeyeceğini belirtir. İse `true`,Kuberneteskümesindedağıtılır. `speech-to-text-autoscaler` | `true` |
| `service.podDisruption.enabled` | [Pod kesintisi bütçesinin](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) etkin olup olmadığı. İse `true`,Kuberneteskümesindedağıtılır. `speech-to-text-poddisruptionbudget` | `true` |