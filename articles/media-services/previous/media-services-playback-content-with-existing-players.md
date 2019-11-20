---
title: İçeriğinizi kayıttan yürütmek için mevcut oyuncuları kullanın-Azure | Microsoft Docs
description: Bu konu, içeriğinizi kayıttan yürütmek için kullanabileceğiniz mevcut oyuncuları listeler.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0da8ce396ce07b7c9859fcfccb5cb524c0ce21c1
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "69015285"
---
# <a name="playing-your-content-with-existing-players"></a>Mevcut oyuncularla içeriğinizi oynama
Azure Media Services, Kesintisiz Akış, HTTP Canlı Akışı ve MPEG-Dash gibi birçok popüler akış biçimini destekler. Bu konu, akışlarınızı test etmek için kullanabileceğiniz mevcut oyunculara işaret eder.

### <a name="the-azure-portal-media-services-content-player"></a>Azure portal Media Services içerik yürütücüsü
**Azure** Portal, videonuzu test etmek için kullanabileceğiniz bir içerik oynatıcı sağlar.

İstediğiniz videoya tıklayın ( [yayımlanmış](media-services-portal-publish.md)olduğundan emin olun) ve portalın altındaki **oynat** düğmesine tıklayın.

Bazı dikkate alınması gereken noktalar vardır:

* **MEDYA HİZMETLERİ İÇERİK OYNATICISI** varsayılan akış uç noktasından oynatır. Varsayılan dışı bir akış uç noktasından oynatmak istiyorsanız başka bir oynatıcı kullanın. Örneğin, [Azure Media Player](https://aka.ms/azuremediaplayer).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player

İçeriğinizi (şifresiz veya korumalı) aşağıdaki biçimlerden birinde kayıttan yürütmek için [Azure Media Player](https://aka.ms/azuremediaplayer) kullanın:

* Kesintisiz Akış
* MPEG DASH
* HLS
* İlerleyen MP4

### <a name="flash-player"></a>Flash oynatıcı

#### <a name="aes-encrypted-with-token"></a>AES-belirteç ile şifrelendi

[https://aestoken.azurewebsites.net](https://aestoken.azurewebsites.net)

#### <a name="playready-with-token"></a>Belirteç ile PlayReady

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH çalarlar

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Diğer
HLS URL 'Leri test etmek için aşağıdakileri de kullanabilirsiniz:

* İOS cihazında **Safari** veya
* Windows üzerinde **3ivx HLS oynatıcı** .

## <a name="developing-video-players"></a>Video oyuncuları geliştirme
Kendi oyuncularınızı geliştirme hakkında daha fazla bilgi için bkz. [video yürütücüleri geliştirme](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
