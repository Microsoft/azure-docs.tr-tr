---
title: Konuşma Cihazları SDK’sını edinme
titleSuffix: Azure Cognitive Services
description: Konuşma hizmeti, çok çeşitli cihazları ve ses kaynakları ile çalışır. Şimdi, konuşma uygulamalarınızın eşleşen donanım ve yazılım ile bir sonraki düzeye alabilir. Bu makalede, konuşma cihazları SDK 'sına nasıl erişebileceğinizi ve geliştirmeye nasıl başlayacağınızı öğreneceksiniz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b60c8735833c3012523c5cc0d7eea6a50e9d10b6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559694"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Bilişsel hizmetler konuşma cihaz SDK'sı Al

Konuşma cihazları SDK 'Sı, amaç oluşturulmuş geliştirme takımları ve farklı mikrofon dizisi yapılandırmalarının birlikte çalışmak üzere tasarlanan önceden ayarlanmış bir kitaplıktır.

## <a name="choose-a-development-kit"></a>Geliştirme seti seçin

|Cihazlar|Min|Açıklama|Senaryolar|
|--|--|--|--|
|[Roobo akıllı ses geliştirme seti](https://ddk.roobo.com)</br>[Kurulum](speech-devices-sdk-roobo-v1.md) / [hızlı](speech-devices-sdk-android-quickstart.md)başlangıçkökü![Bo akıllı ses geliştirme seti](media/speech-devices-sdk/device-roobo-v1.jpg)|7 MIC dizisi, ARM SOC, WIFI, ses çıkışı, GÇ. </br>[Android](speech-devices-sdk-android-quickstart.md)|Yüksek kaliteli savunma ve konuşma senaryoları geliştirmek için Microsoft MIC dizisi ve ön işleme SDK 'sını uyarlamak için ilk konuşma cihazları SDK 'Sı|Konuşma dökümü, akıllı konuşmacı, Ses Aracısı, takılabilir|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)</br>[Kurulum](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [hızlı](speech-devices-sdk-windows-quickstart.md)başlangıçAzure![Kinect dk](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 MIC dizi RGB ve derinlik kameraları. </br>[Windows](speech-devices-sdk-windows-quickstart.md)Linux/[](speech-devices-sdk-linux-quickstart.md)|Gelişmiş yapay zeka (AI) algılayıcılarının yanı sıra karmaşık görüntü işleme ve konuşma modelleri oluşturmaya yönelik bir geliştirici seti. Bir video kamera ve yönlendirme algılayıcısını içeren bir en iyi sınıf uzamsal mikrofon dizisi ve derinlik kamerayı, bir dizi işlem türüne uyum sağlayacak şekilde birden çok modu, seçeneği ve SDK 'Sı olan bir küçük cihazda birleştirir.|Konuşma dökümü, Robotics, akıllı bina|
|Roobo akıllı ses geliştirme seti 2![Roobo akıllı ses geliştirme seti 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 Mic Array, ARM SOC, WIFI, Bluetooth, GÇ. </br>Linux|Maliyet açısından geçerli bir başvuru tasarımında Alternatif işletim sistemi ve daha fazla özellik sağlayan 2. nesil konuşma cihazları SDK.|Konuşma dökümü, akıllı konuşmacı, Ses Aracısı, takılabilir|
|Uriyt11 standardını geliştirme panosu![Urdaha Iyi DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 Mic Array, ARM SOC, WIFI, Ethernet, HDMı, USB kamera. </br>Linux|Microsoft mik Array 'e uyum sağlayan ve HDMı/Ethernet ve daha fazla USB çevre birimi gibi genişletilmiş g/ç 'yi destekleyen bir sektör düzeyi konuşma cihazları SDK 'Sı|Konuşma dökümü, eğitim, Hospte, robots, OTT kutusu, Ses Aracısı, sürücüye geçme|

## <a name="download-the-speech-devices-sdk"></a>Konuşma cihaz SDK'sını indirin

[Konuşma cihazları SDK 'sını](https://aka.ms/sdsdk-download)indirin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Konuşma cihaz SDK'sı ile çalışmaya başlama](https://aka.ms/sdsdk-quickstart)
