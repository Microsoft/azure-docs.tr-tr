---
title: Konuşma Cihazları SDK’sını edinme
titleSuffix: Azure Cognitive Services
description: Konuşma hizmeti çok çeşitli cihazlar ve ses kaynaklarıyla birlikte çalışmaktadır. Artık, konuşma uygulamalarınızı eşleşen donanım ve yazılımla bir sonraki düzeye getirebilirsiniz. Bu makalede, konuşma cihazları SDK 'sına nasıl erişebileceğinizi ve geliştirmeye nasıl başlayacağınızı öğreneceksiniz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 69898e64934c363a18a3ce2fa5e678116624bd24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026378"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Bilişsel hizmetler konuşma cihazları SDK 'sını alın

Konuşma cihazları SDK 'Sı, amaç oluşturulmuş geliştirme takımları ve farklı mikrofon dizisi yapılandırmalarının birlikte çalışmak üzere tasarlanan önceden ayarlanmış bir kitaplıktır.

## <a name="choose-a-development-kit"></a>Geliştirme seti seçin

|Cihazlar|Belirtim|Description|Senaryolar|
|--|--|--|--|
|[Urdaha Iyi geliştirme seti](http://www.urbetter.com/products_56/278.html) ![ Urdaha Iyi DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 Mic Array, ARM SOC, WIFI, Ethernet, HDMı, USB kamera. <br>Linux|Microsoft mik Array 'e uyum sağlayan ve HDMı/Ethernet ve daha fazla USB çevre birimi gibi genişletilmiş g/ç 'yi destekleyen bir sektör düzeyi konuşma cihazları SDK 'Sı <br> [Daha iyi iletişim kurun](http://www.urbetter.com/products_56/278.html)|Konuşma dökümü, eğitim, Hospte, robots, OTT kutusu, Ses Aracısı, sürücüye geçme|
|[Roobo akıllı ses geliştirme seti](http://ddk.roobo.com)<br>[Kurulum](speech-devices-sdk-roobo-v1.md)  /  [Hızlı başlangıç](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) ![ Roobo akıllı ses geliştirme seti](media/speech-devices-sdk/device-roobo-v1.jpg)|7 MIC dizisi, ARM SOC, WIFI, ses çıkışı, GÇ. <br>[Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)|Yüksek kaliteli savunma ve konuşma senaryoları geliştirmek için Microsoft MIC dizisi ve ön işleme SDK 'sını uyarlamak için ilk konuşma cihazları SDK 'Sı|Konuşma dökümü, akıllı konuşmacı, Ses Aracısı, takılabilir|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Kurulum](../../kinect-dk/set-up-azure-kinect-dk.md)  /  [Hızlı başlangıç](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows) ![ Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 MIC dizi RGB ve derinlik kameraları. <br>[Windows](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows) / [Linux](./speech-devices-sdk-quickstart.md?pivots=platform-linux%253fpivots%253dplatform-linux)|Gelişmiş yapay zeka (AI) algılayıcılarının yanı sıra karmaşık görüntü işleme ve konuşma modelleri oluşturmaya yönelik bir geliştirici seti. Bir video kamera ve yönlendirme algılayıcısını içeren bir en iyi sınıf uzamsal mikrofon dizisi ve derinlik kamerayı, bir dizi işlem türüne uyum sağlayacak şekilde birden çok modu, seçeneği ve SDK 'Sı olan bir küçük cihazda birleştirir.|Konuşma dökümü, Robotics, akıllı bina|
|Roobo akıllı ses geliştirme seti 2<br>[Kurulum](speech-devices-sdk-roobo-v2.md)<br>![Roobo akıllı ses geliştirme seti 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 Mic Array, ARM SOC, WIFI, Bluetooth, GÇ. <br>Linux|Maliyet açısından geçerli bir başvuru tasarımında Alternatif işletim sistemi ve daha fazla özellik sağlayan 2. nesil konuşma cihazları SDK.|Konuşma dökümü, akıllı konuşmacı, Ses Aracısı, takılabilir|


## <a name="download-the-speech-devices-sdk"></a>Konuşma cihazları SDK 'sını indirin

[Konuşma cihazları SDK 'sını](./speech-devices-sdk.md)indirin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Konuşma cihazları SDK 'sını kullanmaya başlama](./speech-devices-sdk-quickstart.md?pivots=platform-android)