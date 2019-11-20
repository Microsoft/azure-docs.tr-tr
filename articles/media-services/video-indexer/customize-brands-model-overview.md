---
title: Video Indexer bir marka modelini özelleştirme-Azure
titleSuffix: Azure Media Services
description: Bu makale, Video Indexer bir marka modeli nedir ve özelleştirmeyi özelleştirmek için bir genel bakış sunar.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: ca3d825fb2f4184448cc279d9408f47ad4ad004a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838367"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Video Indexer bir markalar modeli özelleştirme

Video Indexer, video ve ses içeriğinin dizin oluşturma ve yeniden dizin oluşturma sırasında konuşma ve görsel metinden marka algılamayı destekler. Marka algılama özelliği, Bing 'ün markalar veritabanı tarafından önerilen ürünlerin, hizmetlerin ve şirketlerin bahsetmelerini belirler. Örneğin, Microsoft bir video veya ses içeriğinde bahsedildiğinde veya videoda görsel metin gösteriyorsa, Video Indexer içeriği bir marka olarak algılar. Markalar, bağlamı kullanılarak diğer terimlerden ayırt edilir.

Marka algılama, içerik Arşivi ve bulma, bağlamsal reklam, Sosyal Medya Analizi, perakende rekabet analizi ve çok daha fazlası gibi çok çeşitli iş senaryolarında faydalıdır. Video Indexer marka algılama, Bing 'in markalar veritabanının yanı sıra her bir Video Indexer hesabı için özel bir markalar modeli oluşturarak özelleştirerek marka bahsetmelerini ve görsel metinleri dizmenizi sağlar. Özel markalar modeli özelliği, Bing markalar veritabanından markalar belirleyip algılamadığına, belirli markaların algılanarak dışlanVideo Indexer mayacağını (temel olarak siyah bir marka listesi oluşturarak) ve bir parçası olması gereken markalar dahil etmenize olanak tanır Bing 'ün markalar veritabanında bulunmayabilir (aslında bir markaların beyaz listesini oluşturma). Oluşturduğunuz özel markalar modeli yalnızca modeli oluşturduğunuz hesapta kullanılabilir olacaktır.

## <a name="out-of-the-box-detection-example"></a>Kullanıma hazır algılama örneği

[Microsoft Build 2017 gün 2](https://www.videoindexer.ai/media/ed6ede78ad/) sunusunda, "Microsoft Windows" markası birden çok kez görünür. Bazen, bazen görsel metin olarak ve hiçbir zaman tam olarak yazılı olarak yazı. Video Indexer, bir terimin bağlam temelinde gerçekten marka halinde olduğunu ve en fazla 90K markalarını ve sürekli olarak güncelleştirilmesini algılar. 02:25 ' de Video Indexer, konuşma ve Windows logosunun bir parçası olan görsel metinden daha sonra yeniden 02:40 ' de bir marka algılar.

![Markalara genel bakış](./media/content-model-customization/brands-overview.png)

Oluşturma bağlamında Windows hakkında konuşmak, "Windows" sözcüğünü marka olarak algılamaz ve Box, Apple, Fox, vb. için aynı şekilde, bağlamdan nasıl ayırt leyeceğinizi bilen gelişmiş Machine Learning algoritmalarına dayanır. Marka algılama, tüm desteklenen dillerimiz için geçerlidir. [Tam Microsoft Build 2017 gün 2 açılış video ve dizini](https://www.videoindexer.ai/media/ed6ede78ad/)için buraya tıklayın.

Kendi markalarınızı getirmek için sonraki adımlara göz atın.

## <a name="next-steps"></a>Sonraki adımlar

[API 'Leri kullanarak markalar modelini özelleştirme](customize-brands-model-with-api.md)

[Web sitesini kullanarak markalar modelini özelleştirme](customize-brands-model-with-website.md)
