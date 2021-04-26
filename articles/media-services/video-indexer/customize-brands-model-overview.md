---
title: Video Indexer bir marka modelini özelleştirme-Azure
titleSuffix: Azure Media Services
description: Bu makale, Video Indexer bir marka modeli nedir ve özelleştirmeyi özelleştirmek için bir genel bakış sunar.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: juliako
ms.openlocfilehash: 81d7dda854c6afcc9397289ff23ba45b02ed9fc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586083"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Video Indexer bir markalar modeli özelleştirme

Video Indexer, video ve ses içeriğinin dizin oluşturma ve yeniden dizin oluşturma sırasında konuşma ve görsel metinden marka algılamayı destekler. Marka algılama özelliği, Bing 'ün markalar veritabanı tarafından önerilen ürünlerin, hizmetlerin ve şirketlerin bahsetmelerini belirler. Örneğin, Microsoft bir video veya ses içeriğinde bahsedildiğinde veya videoda görsel metin gösteriyorsa, Video Indexer içeriği bir marka olarak algılar. Markalar, bağlamı kullanılarak diğer terimlerden ayırt edilir.

Marka algılama, içerik Arşivi ve bulma, bağlamsal reklam, Sosyal Medya Analizi, perakende rekabet analizi ve çok daha fazlası gibi çok çeşitli iş senaryolarında faydalıdır. Video Indexer marka algılama, Bing 'in markalar veritabanının yanı sıra her bir Video Indexer hesabı için özel bir markalar modeli oluşturarak özelleştirerek marka bahsetmelerini ve görsel metinleri dizmenizi sağlar. Özel markalar modeli özelliği, Bing markalar veritabanından markalar belirleyip algılamadığına, bazı markaların algılanma (aslında onaylanmamış markalar listesini oluşturma) hariç tutmasını ve kuruluşunuzun markalar veritabanında (aslında onaylanan markaların listesini oluşturma), modelinizin bir parçası olması gereken markaların dahil edilip Video Indexer edilmeyeceğini seçmenize olanak sağlar. Oluşturduğunuz özel markalar modeli yalnızca modeli oluşturduğunuz hesapta kullanılabilir olacaktır.

## <a name="out-of-the-box-detection-example"></a>Kullanıma hazır algılama örneği

"Microsoft Build 2017 gün 2" sunusunda, "Microsoft Windows" markası birden çok kez görünür. Bazen, bazen görsel metin olarak ve hiçbir zaman tam olarak yazılı olarak yazı. Video Indexer, bir terimin bağlam temelinde gerçekten marka halinde olduğunu ve en fazla 90K markalarını ve sürekli olarak güncelleştirilmesini algılar. 02:25 ' de Video Indexer, konuşma ve Windows logosunun bir parçası olan görsel metinden daha sonra yeniden 02:40 ' de bir marka algılar.

![Markalara genel bakış](./media/content-model-customization/brands-overview.png)

Oluşturma bağlamında Windows hakkında konuşmak, "Windows" sözcüğünü marka olarak algılamaz ve Box, Apple, Fox, vb. için aynı şekilde, bağlamdan nasıl ayırt leyeceğinizi bilen gelişmiş Machine Learning algoritmalarına dayanır. Marka algılama, tüm desteklenen dillerimiz için geçerlidir.  

## <a name="next-steps"></a>Sonraki adımlar

Kendi markalarınızı getirmek için şu konulara göz atın:

[API 'Leri kullanarak markalar modelini özelleştirme](customize-brands-model-with-api.md)

[Web sitesini kullanarak markalar modelini özelleştirme](customize-brands-model-with-website.md)
