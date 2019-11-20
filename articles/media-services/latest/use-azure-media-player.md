---
title: Azure Media Player-Azure ile kayıttan yürütme | Microsoft Docs
description: Bu konu, Azure Media Player bir genel bakış sunar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.openlocfilehash: 6157f59d96770298d947a1cf6ca28aa343e1a0d4
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311815"
---
# <a name="azure-media-player-overview"></a>Azure Media Player genel bakış

Azure Media Player, medya içeriğini çok çeşitli tarayıcılarda ve cihazlarda Microsoft Azure Media Services çalmak için oluşturulmuş bir web video oyuncusu. Azure Media Player HTML5, medya kaynağı uzantıları (MSE) ve şifreli Medya Uzantıları (EME) gibi sektör standartlarını, zenginleştirilmiş bir uyarlamalı akış deneyimi sağlamak için kullanır. Bu standartlar bir cihazda veya bir tarayıcıda kullanılabilir olmadığında, Azure Media Player Flash ve Silverlight 'ı geri dönüş teknolojisi olarak kullanır. Kullanılan kayıttan yürütme teknolojisinden bağımsız olarak, geliştiricilerin API 'Lere erişmek için birleştirilmiş bir JavaScript arabirimi olacaktır. Bu, Azure Media Services tarafından sunulan içeriğin ek bir çaba olmadan çok sayıda cihaz ve tarayıcıda yürütülebilmesini sağlar.

Microsoft Azure Media Services, içeriğin kayıttan yürütülmesi için HLS, DASH, Kesintisiz Akış akış biçimleriyle birlikte sunulmasını sağlar. Azure Media Player, bu çeşitli biçimleri dikkate alır ve platform/tarayıcı özelliklerine göre en iyi bağlantıyı otomatik olarak yürütür. Media Services Ayrıca, PlayReady şifrelemesi veya AES-128 bit zarfı Şifrelemeli varlıkların dinamik şifrelemesine olanak tanır. Azure Media Player, uygun şekilde yapılandırıldığında PlayReady ve AES-128 bit şifreli içeriğin şifresini çözmeye izin verir. 

> [!NOTE]
> Wdevine şifrelenmiş içerik için HTTPS kayıttan yürütme gereklidir.

[Ücretsiz deneme sürümünüzü başlatın](https://azure.microsoft.com/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Azure Media Player demo sayfası kullanma

### <a name="start-using"></a>Kullanmaya başlayın

[Azure Media Player Demo sayfasını](https://aka.ms/azuremediaplayer) kullanarak Azure Media Services örnekleri veya kendi akışınızı oynatabilirsiniz.  

Yeni bir video oynatmak için, farklı bir URL yapıştırın ve **Güncelleştir**' e basın.

Çeşitli kayıttan yürütme seçeneklerini yapılandırmak için (örneğin, teknik, dil veya şifreleme), **Gelişmiş Seçenekler**' e basın.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Video akışının tanılamalarını izleme

Video akışının tanılamalarını izlemek için [Azure Media Player Demo sayfasını](https://aka.ms/azuremediaplayer) kullanabilirsiniz. 

![Azure Media Player tanılama](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>HTML 'inizdeki Azure Media Player ayarlama

Azure Media Player kolayca ayarlanabilir. Media Services hesabınızdan medya içeriğinin temel oynatımını almak yalnızca birkaç dakika sürer. Azure Media Player ayarlama ve yapılandırma hakkında ayrıntılı bilgi için bkz. [Azure Media Player belgeleri](https://aka.ms/ampdocs) . 

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Media Player belgeleri](https://aka.ms/ampdocs)
- [Azure Media Player örnekleri](https://aka.ms/ampsamples)
