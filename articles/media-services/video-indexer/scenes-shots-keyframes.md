---
title: Sahneleri, görüntüleri ve ana kareleri Video Indexer
titleSuffix: Azure Media Services
description: Bu konu, Video Indexer sahneleri, görüntüleri ve ana kareleri için bir genel bakış sunar.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b24778434596f583be44572612c856fa4e0cecde
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860235"
---
# <a name="scenes-shots-and-keyframes"></a>Sahneler, çekimler, ana kareler

Video Indexer, Videoları yapısal ve anlam özelliklerine göre zamana bağlı birimlere parçalara ayırır. Bu özellik, müşterilerin, değişen granlular temelinde video içeriğine kolayca gözatmasını, yönetmesini ve düzenlemesini sağlar. Örneğin, bu konuda açıklanan sahneleri, görüntüleri ve ana kareleri temel alan.   

![Sahneler, çekimler, ana kareler](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Sahne algılama  
 
Video Indexer görsel ipuçlarıyla videoda bir sahnenin ne zaman değişiklik olduğunu belirler. Bir sahne, tek bir olay gösterir ve bu, anlam ile ilgili olan bir dizi ardışık anlık görüntüsünü meydana gelir. Sahne küçük resmi, temel alınan görüntüsündeki ilk ana karedir. Video Indexer, bir videoyu ardışık görüntülerdeki renk tutarlılılığını temel alarak sahneleri parçalara ayırır ve her sahnenin başlangıç ve bitiş saatini alır. Sahne algılama, videoların anlamlı yönlerini içerdiğinden zorlayıcı bir görev olarak değerlendirilir.

> [!NOTE]
> En az 3 sahne içeren videolar için geçerlidir.

## <a name="shot-detection"></a>Çekim algılama

Video Indexer, ardışık çerçevelerin renk düzeninde hem sert hem de dereceli geçişleri izleyerek görsel ipuçlarıyla videoda bir çekneye ne zaman değişiklik olduğunu belirler. Alıcının meta verileri bir başlangıç ve bitiş zamanı ve bu çekte yer alan ana kare listesini içerir. Bu görüntüleri aynı kameradaki aynı kameradan alınan ardışık çerçevelerdir.

## <a name="keyframe-detection"></a>Ana kare algılama

Resmi en iyi temsil eden çerçeveleri seçer. Ana kareler, Aesthetic Characteristics özelliklerine (örneğin, kontrast ve stablete) göre videonun tamamının seçtiği temsili çerçevelerdir. Video Indexer, ana kare kimliklerinin bir listesini, hangi müşterilerin ana kare küçük resmini ayıklayabileceği temel alınarak, alıcının meta verilerinin bir parçası olarak alır. 

Ana kareler, JSON çıkışı içindeki görüntüleriyle ilişkilendirilir. 

## <a name="editorial-shot-type-detection"></a>Düzenleme görüntüsü türü algılama

Öngörüler JSON 'daki tek bir çekle ilişkili olan görüntü türü, düzenleme türünü temsil eder. Videoları kliplere, tanıtıcıya veya sanatsal amaçlar için belirli bir ana kare stilini ararken yararlı olan bu görüntü türü özelliklerini bulabilirsiniz. Farklı türler her bir görüntüsündeki ilk ana karenin analizine göre belirlenir. Anlık görüntüleri, ilk ana karede görünen yüzlerin ölçeği, boyutu ve konumu tarafından tanımlanır. 

Görüntü boyutu ve ölçek, kamera ve çerçevede görünen yüzlerin arasındaki uzaklığa göre belirlenir. Bu özellikleri kullanarak Video Indexer aşağıdaki görüntü türlerini algılar:

* Geniş: bir kişinin gövdesinin tamamını gösterir.
* Orta: bir kişinin üst gövdesini ve yüzünü gösterir.
* Kapat: genellikle bir kişinin yüzünü gösterir.
* Aşırı yakın: bir kişinin ekranı dolduran yüzü gösterir. 

Görüntü türleri, çerçeve merkezine göre konu karakterlerinin konumuyla de belirlenebilir. Bu özellik Video Indexer aşağıdaki görüntü türlerini tanımlar:

* Sol yüz: çerçevenin sol tarafında bir kişi görünür.
* Orta yüz: karenin merkezi bölgesinde bir kişi görünür.
* Sağ yüz: çerçevenin sağ tarafında bir kişi görünür.
* Açık Hava: bir kişi, bir dış mekayarda görüntülenir.
* Inkapısı: bir Kullanıcı, bir ınkapılı ayarda görüntülenir.

Ek özellikler:

* İki anlık görüntüsü: iki kişinin orta büyüklükte bir yüzlerini gösterir.
* Birden çok yüz: ikiden fazla kişi.

## <a name="next-steps"></a>Sonraki adımlar

[API tarafından üretilen Video Indexer çıkışını inceleyin](video-indexer-output-json-v2.md#scenes)
