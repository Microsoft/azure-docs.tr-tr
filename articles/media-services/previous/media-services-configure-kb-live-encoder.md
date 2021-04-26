---
title: Haivision KB Kodlayıcısı 'nı Azure 'a tek bit hızlı canlı bir akış gönderecek şekilde yapılandırma | Microsoft Docs
description: Bu konuda, Haivision KB Live Encoder 'ın canlı kodlama için etkinleştirilmiş AMS kanallarına tek bir bit hızı akışı göndermek için nasıl yapılandırılacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 20c75672bc7198fb028278dc5432cc6a77e3766e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016874"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Tek bit hızlı canlı akış göndermek için Haivision KB Live Encoder kullanın

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Bu konuda, [Havision KB Live Encoder](https://www.haivision.com/products/kb-series/) Encoder 'ın canlı kodlama için etkinleştirilmiş AMS kanallarına tek bir bit hızı akışı göndermek için nasıl yapılandırılacağı gösterilmektedir. Daha fazla bilgi için bkz. [Azure Media Services ile Gerçek Zamanlı Kodlama Gerçekleştirmek İçin Etkinleştirilmiş Kanallar ile Çalışma](media-services-manage-live-encoder-enabled-channels.md).

Bu öğreticide, Azure Media Services (AMS) Azure Media Services Explorer (AMSE) aracıyla nasıl yönetileceği gösterilmektedir. Bu araç yalnızca Windows bilgisayarında çalışır. Mac veya Linux kullanıyorsanız, [kanalları](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) ve [programları](media-services-portal-creating-live-encoder-enabled-channel.md)oluşturmak için Azure Portal kullanın.

## <a name="prerequisites"></a>Önkoşullar
*   SW v 5.01 veya üzerini çalıştıran bir Haivision KB Kodlayıcısı 'na erişin.
* [Azure Media Services hesabı oluşturma](media-services-portal-create-account.md)
* Çalışan bir akış uç noktası olduğundan emin olun. Daha fazla bilgi için bkz. [Media Services hesapta akış uç noktalarını yönetme](media-services-portal-manage-streaming-endpoints.md)
* [Ami](https://github.com/Azure/Azure-Media-Services-Explorer) aracının en son sürümünü yükler.
* Aracı başlatın ve AMS hesabınıza bağlanın.

## <a name="tips"></a>İpuçları
* Mümkünse kablolu internet bağlantısı kullanın.
* Bant genişliği gereksinimlerini belirleme aşamasındaki temel kurallardan biri, akış bit hızlarını ikiyle çarpmaktır. Bu zorunlu bir gereksinim olmasa da, ağ tıkanıklığı etkisini azaltmaya yardımcı olur.
* Yazılım tabanlı kodlayıcılar kullanırken gereksiz programları kapatın.

## <a name="create-a-channel"></a>Kanal oluşturma
1. AMI aracında, **canlı** sekmesine gidin ve kanal alanının içine sağ tıklayın. Kanal oluştur ' u seçin **...** menüsünde.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Bir kanal adı belirtin, açıklama alanı isteğe bağlıdır. Kanal ayarları altında, Live Encoding seçeneği için **Standart** ' ı seçerek giriş Protokolü **RTMP** olarak ayarlanır. Diğer tüm ayarları olduğu gibi bırakabilirsiniz. **Yeni kanalı Şimdi Başlat** ' ın seçildiğinden emin olun.
3. **Kanal oluştur**' a tıklayın.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Kanalın başlaması 20 dakika kadar sürebilir.

## <a name="configure-the-haivision-kb-encoder"></a>Haivision KB Kodlayıcısı 'nı yapılandırma
Bu öğreticide, aşağıdaki çıkış ayarları kullanılır. Bu bölümün geri kalanında yapılandırma adımları daha ayrıntılı olarak açıklanmaktadır.

Video:
-   Codec: H.264
-   Profil: Yüksek (Seviye 4.0)
-   Bit hızı: 5000 kbps
-   Ana kare: 2 saniye (60 kare)
-   Kare hızı: 30

Müzik
-   Codec: AAC (LC)
-   Bit hızı: 192 kb/sn
-   Örnek hızı: 44,1 kHz

## <a name="configuration-steps"></a>Yapılandırma adımları
1.  Haivision KB Kullanıcı arabiriminde oturum açın.
2.  Kanal denetim merkezindeki **Menü düğmesine** tıklayın ve **Kanal ekle** ' yi seçin.  
    ![9.15.09 saatinde ekran görüntüsü 2017-08-14](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Ad alanına **kanal adını** yazın ve ileri ' ye tıklayın.  
    ![9.19.07 saatinde ekran görüntüsü 2017-08-14](./media/media-services-configure-kb-live-encoder/step3.png)
4.  **Giriş kaynağı** açılır listesinden **kanal giriş kaynağını** seçin ve ileri ' ye tıklayın.
    ![9.20.44 saatinde ekran görüntüsü 2017-08-14](./media/media-services-configure-kb-live-encoder/step4.png)
5.  **Kodlayıcı şablonu** açılır listesinden **H264-720-AAC-192** ' ı seçin ve ileri ' ye tıklayın.
    ![9.23.15 saatinde ekran görüntüsü 2017-08-14](./media/media-services-configure-kb-live-encoder/step5.png)
6.  **Yeni çıkış Seç** açılır listesinden **RTMP** ' yi seçin ve ileri ' ye tıklayın.  
    ![9.27.51 saatinde ekran görüntüsü 2017-08-14](./media/media-services-configure-kb-live-encoder/step6.png)
7.  **Kanal çıkış** penceresinde Azure akış bilgilerini doldurun. **RTMP** bağlantısını **sunucu** alanındaki ilk kanal kurulumundan yapıştırın. **Çıkış adı** alanına kanalın adını yazın. Akış adı şablon alanında, akışı adlandırmak için% video_bitrate% RTMPStreamName_ şablonu kullanın.
    ![9.33.17 saatinde ekran görüntüsü 2017-08-14](./media/media-services-configure-kb-live-encoder/step7.png)
8.  İleri ' ye ve ardından bitti ' ye tıklayın.
9.  Kodlayıcı kanalını başlatmak için **Oynat düğmesine** tıklayın.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Kayıttan yürütmeyi test etme
AML aracına gidin ve test edilecek kanala sağ tıklayın. Menüden önizlemeyi kayıttan yürütme üzerine gelin ve Azure Media Player seçin.

Akış Player 'da görünürse, kodlayıcı AMS 'ye bağlanacak şekilde doğru şekilde yapılandırılmıştır.

Bir hata alırsanız, kanalın sıfırlanması ve kodlayıcı ayarlarının ayarlanması gerekir. Rehberlik için sorun giderme makalesine bakın.

## <a name="create-a-program"></a>Program oluşturma
1.  Kanal kayıttan yürütme onaylandıktan sonra bir program oluşturun. AMO aracının canlı sekmesi altında, program alanının içine sağ tıklayıp yeni program oluştur ' u seçin.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Programı adlandırın ve gerekirse arşiv penceresi uzunluğunu (varsayılan olarak dört saat) ayarlayın. Ayrıca bir depolama konumu belirtebilir veya varsayılan olarak bırakabilirsiniz.
2.  Şimdi programa başla kutusunu işaretleyin.
3.  Program oluştur ' a tıklayın.
4.  Program çalışmaya başladıktan sonra programa sağ tıklayıp, programları yeniden yürütmeye ve sonra Azure Media Player ' yi seçerek kayıttan yürütmeyi onaylayın.
5.  Onaylandıktan sonra programa yeniden sağ tıklayın ve çıkış URL 'sini panoya kopyala ' yı seçin (veya bu bilgileri menüdeki program bilgileri ve ayarlar seçeneğinden alın).

Akış artık bir yürütücüyü katıştırmaya hazırdır veya canlı görüntüleme için bir hedef kitleye dağıtılır.

> [!NOTE]
> Program oluşturma, kanal oluşturulduktan daha az zaman alır.
