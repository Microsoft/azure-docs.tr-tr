---
title: Azure Media Services bir küçük resim Sprite oluştur | Microsoft Docs
description: Bu konu, Azure Media Services ile bir küçük resim Sprite oluşturmayı gösterir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ce66b6f605b10f65ec8a98d14c682928c7b21321
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012250"
---
# <a name="generate-a-thumbnail-sprite"></a>Küçük resim görüntüsü oluşturma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Bir VTT dosyasıyla birlikte tek bir (büyük) görüntüde birlikte bulunan birden çok küçük çözünürlüklü küçük resim içeren bir JPEG dosyası olan küçük resim sprite oluşturmak için Media Encoder Standard kullanabilirsiniz. Bu VTT dosyası, her bir küçük resmin, büyük JPEG dosyasındaki bu küçük resmin boyutuyla ve koordinatlarıyla birlikte gösterdiği, giriş videosunun zaman aralığını belirtir. Video oyuncuları, bir ' görsel ' SeekBar göstermek için VTT dosyasını ve Sprite görüntüsünü kullanır ve video zaman çizelgesinde geri ve ileri ilerledikçe görsel geri bildirimde bulunan bir Görüntüleyici sağlar.

Küçük resim sprite oluşturmak için Media Encoder Standard kullanmak için önceden ayarlanmış:

1. JPG küçük resim biçimi kullanılmalıdır
2. Başlangıç/adım/Aralık değerleri, zaman damgası olarak veya% values (çerçeve sayıları değil) olarak belirtilmelidir 
    
    1. Zaman damgaları ve% değerleri karıştırmak normaldir

3. SpriteColumn değeri, negatif olmayan bir sayı olarak 1 ' den büyük veya buna eşit olmalıdır

    1. SpriteColumn, e >= 1 olarak ayarlandıysa, çıkış resmi, e-sütunları olan bir dikdörtgendir. #2 aracılığıyla oluşturulan küçük resim sayısı, son satırın tam katı olmaması halinde, son satır eksik olur ve siyah piksellerle kalır.  

Aşağıda bir örnek verilmiştir:

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
    }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
   ]
}
```

## <a name="known-issues"></a>Bilinen Sorunlar

1.  Tek bir görüntü satırı içeren bir sprite görüntüsü oluşturmak mümkün değildir (SpriteColumn = 1, tek sütunlu bir görüntüyle sonuçlanır).
2.  Hareketli görüntüleri orta ölçekli JPEG görüntülerine parçalama henüz desteklenmiyor. Bu nedenle, sonuçta elde edilen küçük resim Sprite 'ın yaklaşık 8Gb piksel veya daha az olması için küçük resimlerin sayısını ve boyutunu sınırlamak üzere dikkatli olunmalıdır.
3.  Azure Media Player, Microsoft Edge, Chrome ve Firefox tarayıcılarındaki spritları destekler. VTT ayrıştırma ıE11 içinde desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

[İçerik kodlama](media-services-encode-asset.md)
