---
title: Haivision KB Kodlayıcısı 'nı Azure 'a tek bit hızlı canlı bir akış gönderecek şekilde yapılandırma | Microsoft Docs
description: Bu konuda, Haivision KB Live Encoder 'ın canlı kodlama için etkinleştirilmiş AMS kanallarına tek bir bit hızı akışı göndermek için nasıl yapılandırılacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: dbgeorge
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 7bb3db4861842e145689682035adc3c691538adf
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297790"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Tek bit hızlı canlı akış göndermek için Haivision KB Live Encoder kullanın  
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Bu konuda, [Havision KB Live Encoder](https://www.haivision.com/products/kb-series/) Encoder 'ın canlı kodlama için etkinleştirilmiş AMS kanallarına tek bir bit hızı akışı göndermek için nasıl yapılandırılacağı gösterilmektedir. Daha fazla bilgi için bkz. [Azure Media Services ile Gerçek Zamanlı Kodlama Gerçekleştirmek İçin Etkinleştirilmiş Kanallar ile Çalışma](media-services-manage-live-encoder-enabled-channels.md).

Bu öğreticide, Azure Media Services Gezgini (AMSE) aracı ile Azure Media Services (AMS) yönetilecek gösterilmektedir. Bu araç yalnızca Windows bilgisayarda çalışır. Mac veya Linux bilgisayarda ise oluşturmak için Azure portalını kullanma [kanalları](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) ve [programlar](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Önkoşullar
*   SW v 5.01 veya üzerini çalıştıran bir Haivision KB Kodlayıcısı 'na erişin.
* [Azure Media Services hesabı oluşturma](media-services-portal-create-account.md)
* Çalışan bir akış uç bulunduğundan emin olun. Daha fazla bilgi için [akış uç noktalarını yönetme Media Services hesabı](media-services-portal-manage-streaming-endpoints.md)
* En son sürümünü yükleyin [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) aracı.
* Aracı'nı başlatın ve AMS hesabınızı bağlayın.

## <a name="tips"></a>İpuçları
* Mümkün olduğunda, bir sabit internet bağlantısı kullanın.
* Bir iyi bant genişliği gereksinimlerini belirlerken için udur akış bit hızlarına dönüştürme çift. Bu zorunlu bir gereksinim olmamasına karşın, Ağ Tıkanıklığı etkisini azaltmaya yardımcı olur.
* Yazılım tabanlı kodlayıcılar kullanırken, gereksiz tüm programları kapatın.

## <a name="create-a-channel"></a>Kanal oluşturma
1. AMSE Aracı'nda gidin **canlı** sekmesini tıklatıp içinde kanal alana sağ tıklayın. Seçin **kanal oluştur...** belirleyin.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Bir kanal adı belirtirseniz açıklama alanı isteğe bağlıdır. Kanal ayarları altında **standart** Live Encoding seçeneğini ayarlamak giriş protokolü için **RTMP**. Olduğu gibi tüm diğer ayarlar bırakabilirsiniz. Emin **yeni kanal Şimdi Başlat** seçilir.
3. Tıklayın **kanal oluşturma**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Kanalı başlatmak için 20 dakika sürebilir.

## <a name="configure-the-haivision-kb-encoder"></a>Haivision KB Kodlayıcısı 'nı yapılandırma
Bu öğreticide, aşağıdaki çıkış ayarları kullanılır. Bu bölümün geri kalanında daha ayrıntılı yapılandırma adımlarını açıklar.

Video:
-   Bileşeni H.
-   Profilinizi Yüksek (düzey 4,0)
-   Bit hızı 5000 kbps
-   Denetçisinde 2 saniye (60 kare)
-   Kare hızı: 30

Müzik
-   Bileşeni AAC (LC)
-   Bit hızı 192 kbps
-   Örnek hız: 44,1 kHz

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
7.  **Kanal çıkış** penceresinde Azure akış bilgilerini doldurun. **RTMP** bağlantısını **sunucu** alanındaki ilk kanal kurulumundan yapıştırın. **Çıkış adı** alanına kanalın adını yazın. Akış adı şablon alanında, akışı adlandırmak için RTMPStreamName_% video_bitrate% şablonunu kullanın.
    ![9.33.17 saatinde ekran görüntüsü 2017-08-14](./media/media-services-configure-kb-live-encoder/step7.png)
8.  İleri ' ye ve ardından bitti ' ye tıklayın.
9.  Kodlayıcı kanalını başlatmak için **Oynat düğmesine** tıklayın.  
    ![Haivision KB. png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Testi kayıttan yürütme
AMSE aracına gidin ve test edilecek kanal sağ tıklayın. Menüden önizlemeyi kayıttan yürütme üzerine gelin ve Azure Media Player seçin.

Ardından akış Player'da görünüyorsa, kodlayıcı düzgün AMS'ye bağlanmak için yapılandırıldı.

Bir hata aldıysanız, kanal sıfırlanması gerekir ve Kodlayıcı ayarları ayarlanır. Rehberlik için sorun giderme makalesine bakın.

## <a name="create-a-program"></a>Bir program oluşturma
1.  Kanal kayıttan yürütme onaylandıktan sonra bir program oluşturun. AMO aracının canlı sekmesi altında, program alanının içine sağ tıklayıp yeni program oluştur ' u seçin.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Programı adlandırın ve gerekirse arşiv penceresi uzunluğunu (varsayılan olarak dört saat) ayarlayın. Ayrıca, bir depolama konumu belirtin veya varsayılan olarak bırakın.
2.  Şimdi programa başla kutusunu işaretleyin.
3.  Program oluştur ' a tıklayın.
4.  Program çalışmaya başladıktan sonra programa sağ tıklayıp, programları yeniden yürütmeye ve sonra Azure Media Player ' yi seçerek kayıttan yürütmeyi onaylayın.
5.  Onaylandıktan sonra programa yeniden sağ tıklayın ve çıkış URL 'sini panoya kopyala ' yı seçin (veya bu bilgileri menüdeki program bilgileri ve ayarlar seçeneğinden alın).

Akış bir oynatıcı içinde gömülü veya canlı görüntülemek için bir hedef kitle için Dağıtılmış artık hazırdır.

> [!NOTE]
> Program oluşturma kanal oluşturmayı daha az zaman alır.
