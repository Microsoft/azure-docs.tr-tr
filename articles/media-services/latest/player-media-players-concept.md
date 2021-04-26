---
title: Media Services için medya oyuncuları genel bakış
description: Azure Media Services ile hangi medya yürütücüleri kullanabilirim? Azure Media Player, Shaka ve Video.js şu ana kadar.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 43d0a8ee82a84a57be7c8ded9e7f5afc172bd9d6
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282414"
---
# <a name="media-players-for-media-services"></a>Media Services için medya yürütücüleri

Media Services ile birkaç medya oynatıcı kullanabilirsiniz.

## <a name="azure-media-player"></a>Azure Media Player

Azure Media Player, çok çeşitli tarayıcılar ve cihazlar için bir video oynatıcı olur. Azure Media Player, zenginleştirilmiş bir uyarlamalı akış deneyimi sağlamak üzere HTML5, medya kaynağı uzantıları (MSE) ve şifreli Medya Uzantıları (EME) gibi sektör standartlarını kullanır. Bu standartlar bir cihazda veya bir tarayıcıda kullanılabilir olmadığında, Azure Media Player Flash ve Silverlight 'ı geri dönüş teknolojisi olarak kullanır. Kullanılan kayıttan yürütme teknolojisinden her ne kadar, geliştiricilerin API 'Lere erişmek için birleştirilmiş bir JavaScript arabirimi vardır. Azure Media Services tarafından sunulan içerikler, ek bir çaba olmadan çok çeşitli cihazlarda ve tarayıcılarda çalınabilir.

[Azure Media Player belgelerine](../azure-media-player/azure-media-player-overview.md)bakın.

## <a name="shaka"></a>Shaka

Shaka oynatıcı, uyarlamalı medya için açık kaynaklı bir JavaScript kitaplığıdır. Eklentiler veya Flash kullanmadan bir tarayıcıda Uyarlamalı medya biçimlerini (DASH ve HLS gibi) çalar. Bunun yerine, Shaka Player açık Web standartları medya kaynağı uzantılarını ve şifreli medya uzantılarını kullanır.

[Azure Media Services Shaka oynatıcıyı nasıl kullanacağınızı](player-shaka-player-how-to.md)öğrenin.

## <a name="videojs"></a>Video.js

Video.js, bir tarayıcıda Uyarlamalı medya biçimlerini (DASH ve HLS gibi) oynatan bir video oynatıcı olur. Video.js, Open Web standartları MediaSource uzantılarını ve şifreli medya uzantılarını kullanır. Masaüstleri ve mobil cihazlarda video oynatmayı destekler. Resmi belgelerini adresinde bulabilirsiniz https://docs.videojs.com/ .

[Video.js oynatıcıyı Azure Media Services Ile nasıl kullanacağınızı](player-how-to-video-js-player.md)öğrenin.


## <a name="next-steps"></a>Sonraki adımlar ##

- [Hızlı başlangıç Azure Media Player](../azure-media-player/azure-media-player-quickstart.md)