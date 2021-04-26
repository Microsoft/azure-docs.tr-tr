---
title: Azure Media Services | tarafından önerilen kodlayıcılar hakkında bilgi edinin Microsoft Docs
description: Bu makalede, Azure Media Services tarafından önerilen şirket içi kodlayıcılar listelenmektedir.
services: media-services
keywords: kodlama; kodlayıcılar; medya
author: IngridAtMicrosoft
manager: johndeu
ms.author: johndeu
ms.date: 03/10/2021
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 98d78a105bf06a2e49dee0b8c2be710b220a0023
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009445"
---
# <a name="recommended-on-premises-encoders"></a>Önerilen şirket içi kodlayıcılar

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services ile canlı akış yapıldığında, kanalınızın giriş akışını nasıl almasını istediğinizi belirtebilirsiniz. Canlı kodlama kanalı ile şirket içi bir kodlayıcı kullanmayı seçerseniz, kodlayıcının yüksek kaliteli tek bit hızında bir akışı çıkış olarak göndermelidir. Geçiş kanalı ile şirket içi kodlayıcı kullanmayı tercih ederseniz, kodlayıcının istenen tüm çıktı kalitelerini içeren bir çoklu bit hızı akışını çıkış olarak iletmesi gerekir. Daha fazla bilgi için bkz. [Şirket içi kodlayıcılarla canlı akış](media-services-live-streaming-with-onprem-encoders.md).

## <a name="encoder-requirements"></a>Kodlayıcı gereksinimleri

Kodlayıcılar, HTTPS veya RTMPS protokollerini kullanırken TLS 1,2 ' i desteklemelidir.

## <a name="live-encoders-that-output-rtmp"></a>RTMP çıkış yapan canlı kodlayıcılar 

Azure Media Services, çıkış olarak RTMP 'ye sahip aşağıdaki canlı kodlayıcılardan birini kullanmanızı önerir:

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream kablolu dönüştürme (TLS 1,2 gereksinimi nedeniyle sürüm 13.0.2 veya üzeri)

  RTMPS protokolleri kullanılırken kodlayıcılar TLS 1,2 ' i desteklemelidir.
- Teradek Slice 756
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Parçalanan MP4 veren canlı kodlayıcılar 

Azure Media Services, çıkış olarak çoklu bit hızlı parçalanmış-MP4 (Kesintisiz Akış) içeren aşağıdaki canlı kodlayıcılardan birini kullanmanızı önerir:

- Media Excel Hero Live ve Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Dinamik kullanım (TLS 1,2 gereksinimi nedeniyle sürüm 2.14.15 ve üzeri)

  Kodlayıcılar, HTTPS protokollerini kullanırken TLS 1,2 ' i desteklemelidir.
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenıo MCP3

> [!NOTE]
> Canlı kodlayıcı, bir geçiş kanalına tek bit hızlı bir akış gönderebilir, ancak bu yapılandırma, istemciye Uyarlamalı bit hızı akışa izin vermediğinden önerilmez.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Şirket içi kodlayıcı iş ortağı olma

Şirket içi kodlayıcı iş ortağı Azure Media Services olarak, Media Services kurumsal müşterilere kodlayıcınızı önererek ürününüzü yükseltir. Şirket içi kodlayıcı iş ortağı olmak için, Media Services ile şirket içi kodlayıcılarınızın uyumluluğunu doğrulamanız gerekir. Bunu yapmak için aşağıdaki doğrulamaları doldurun:

Geçiş kanalı doğrulaması
1. Azure Media Services hesabınızı oluşturun veya ziyaret edin
2. **Geçiş** kanalı oluşturma ve başlatma
3. Kodlayıcınızı çoklu bit hızlı canlı bir akış göndermek için yapılandırın.
4. Yayınlanan Canlı etkinlik oluşturma
5. Gerçek zamanlı kodlarınızı yaklaşık 10 dakika boyunca çalıştırın
6. Canlı etkinliği durdur
7. Bir akış uç noktası oluşturun,, kayıttan yürütmenin tüm kalite seviyelerine yönelik görünür olmadığından emin olmak için [Azure Media Player](https://aka.ms/azuremediaplayer) gibi bir oyuncu kullanın (veya alternatif olarak, adım 6 ' dan önce canlı oturum sırasında önizleme URL 'si aracılığıyla izleme ve doğrulama)
8. Varlık KIMLIĞINI, Canlı Arşiv için yayımlanan akış URL 'sini ve canlı kodlayıcıınızdan kullanılan ayarları ve sürümü kaydedin
9. Her örnek oluşturulduktan sonra kanal durumunu sıfırlayın
10. Kodlarınızın desteklediği tüm yapılandırmalarda 3 ile 9 arasındaki adımları yineleyin (reklam sinyali/resim yazıları/farklı kodlama hızları olmadan)

Canlı kodlama kanalı doğrulaması
1. Azure Media Services hesabınızı oluşturun veya ziyaret edin
2. **Canlı kodlama** kanalı oluşturma ve başlatma
3. Kodlayıcınızı tek bit hızlı canlı bir akış göndermek için yapılandırın.
4. Yayınlanan Canlı etkinlik oluşturma
5. Gerçek zamanlı kodlarınızı yaklaşık 10 dakika boyunca çalıştırın
6. Canlı etkinliği durdur
7. Bir akış uç noktası oluşturun,, kayıttan yürütmenin tüm kalite seviyelerine yönelik görünür olmadığından emin olmak için [Azure Media Player](https://aka.ms/azuremediaplayer) gibi bir oyuncu kullanın (veya alternatif olarak, adım 6 ' dan önce canlı oturum sırasında önizleme URL 'si aracılığıyla izleme ve doğrulama)
8. Varlık KIMLIĞINI, Canlı Arşiv için yayımlanan akış URL 'sini ve canlı kodlayıcıınızdan kullanılan ayarları ve sürümü kaydedin
9. Her örnek oluşturulduktan sonra kanal durumunu sıfırlayın
10. Kodlarınızın desteklediği tüm yapılandırmalarda 3 ' ten 9 ' a kadar olan adımları yineleyin (reklam sinyali/resim yazıları/çeşitli kodlama hızları olmadan)

Longeçekimi doğrulaması
1. Azure Media Services hesabınızı oluşturun veya ziyaret edin
2. **Geçiş** kanalı oluşturma ve başlatma
3. Kodlayıcınızı çoklu bit hızlı canlı bir akış göndermek için yapılandırın.
4. Yayınlanan Canlı etkinlik oluşturma
5. Live Encoder ' ınızı bir hafta veya daha uzun bir süre çalıştırın
6. Kayıttan yürütmenin görünür olmadığından emin olmak için zaman zaman (veya arşivlenmiş varlık) için canlı akışı izlemek üzere [Azure Media Player](https://aka.ms/azuremediaplayer) gibi bir oyuncu kullanın
7. Canlı etkinliği durdur
8. Varlık KIMLIĞINI, Canlı Arşiv için yayımlanan akış URL 'sini ve canlı kodlayıcıınızdan kullanılan ayarları ve sürümü kaydedin

Son olarak, e-postayla Media Services kayıtlı ayarlarınızı ve canlı arşiv parametrelerini gönderin amsstreaming@microsoft.com . Media Services alındıktan sonra, Live Encoder 'ınızdan örnekler üzerinde doğrulama testleri gerçekleştirir. Bu işlemle ilgili tüm sorularla Media Services başvurabilirsiniz.
