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
ms.openlocfilehash: 5a738152296aacbb5914e859a65976bd0f6dbf0a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532421"
---
# <a name="scenes-shots-and-keyframes"></a>Sahneler, çekimler, ana kareler

Video Indexer, Videoları yapısal ve anlam özelliklerine göre zamana bağlı birimlere parçalara ayırır. Bu özellik, müşterilerin, değişen granlular temelinde video içeriğine kolayca gözatmasını, yönetmesini ve düzenlemesini sağlar. Örneğin, bu konuda açıklanan sahneleri, görüntüleri ve ana kareleri temel alan.   

![Sahneler, çekimler, ana kareler](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Sahne algılama  
 
Video Indexer görsel ipuçlarıyla videoda bir sahnenin ne zaman değişiklik olduğunu belirler. Bir sahne, tek bir olay gösterir ve bu, anlam ile ilgili olan bir dizi ardışık anlık görüntüsünü meydana gelir. Sahne küçük resmi, temel alınan görüntüsündeki ilk ana karedir. Video Indexer, bir videoyu ardışık görüntülerdeki renk tutarlılılığını temel alarak sahneleri parçalara ayırır ve her sahnenin başlangıç ve bitiş saatini alır. Sahne algılama, videoların anlamlı yönlerini içerdiğinden zorlayıcı bir görev olarak değerlendirilir.

> [!NOTE]
> En az 3 sahne içeren videolar için geçerlidir.

## <a name="shot-detection"></a>Görüntü algılama

Video Indexer, ardışık çerçevelerin renk düzeninde hem sert hem de dereceli geçişleri izleyerek görsel ipuçlarıyla videoda bir çekneye ne zaman değişiklik olduğunu belirler. Alıcının meta verileri bir başlangıç ve bitiş zamanı ve bu çekte yer alan ana kare listesini içerir. Bu görüntüleri aynı kameradaki aynı kameradan alınan ardışık çerçevelerdir.

## <a name="keyframe-detection"></a>Ana kare algılama

Video Indexer her bir görüntüsünü en iyi temsil eden çerçeveleri seçer. Ana kareler, Aesthetic Characteristics özelliklerine (örneğin, kontrast ve stablete) göre videonun tamamının seçtiği temsili çerçevelerdir. Video Indexer, ana kare kimliklerinin bir listesini, ana kareyi yüksek çözünürlüklü bir görüntü olarak hangi müşterilerin çıkarabileceği temel alınarak, yansımanın meta verilerinin bir parçası olarak alır.  

### <a name="extracting-keyframes"></a>Ana kareleri ayıklama

Videonuzla ilgili yüksek çözünürlüklü ana kareleri ayıklamak için öncelikle videoyu karşıya yükleyip dizinlemenizin olması gerekir.

![Gezen](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Video Indexer Web sitesiyle

Video Indexer Web sitesini kullanarak ana kareleri ayıklamak için videonuzu karşıya yükleyin ve dizine yükleyin. Dizin oluşturma işi tamamlandıktan sonra **İndir** düğmesine tıklayın ve **yapılar (ZIP)** öğesini seçin. Bu, yapıt klasörünü bilgisayarınıza indirir. 

!["Yapılar" seçiliyken "Indir" açılan görüntüsünü gösteren ekran görüntüsü.](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Açın ve klasörü açın. *_KeyframeThumbnail* klasöründe, videodan ayıklanan tüm ana kareleri bulacaksınız. 

#### <a name="with-the-video-indexer-api"></a>Video Indexer API 'SI ile

Video Indexer API 'sini kullanarak ana kareleri almak için [karşıya yükleme video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) çağrısını kullanarak videonuzu karşıya yükleyin ve dizine alın. Dizin oluşturma işi tamamlandıktan sonra, [video dizinini al](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index)' ı çağırın. Bu, Video Indexer JSON dosyasındaki İçeriğinizden ayıkladığı tüm öngörüleri sağlar.  

Her bir shot meta verisinin parçası olarak ana kare kimliklerinin bir listesini alırsınız. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Şimdi bu ana kare kimliklerinin her birini [küçük resim al](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) çağrısında çalıştırmanız gerekir. Bu işlem, ana kare görüntülerinin her birini bilgisayarınıza indirir. 

## <a name="editorial-shot-type-detection"></a>Düzenleme görüntüsü türü algılama

Ana kareler, JSON çıkışı içindeki görüntüleriyle ilişkilendirilir. 

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
