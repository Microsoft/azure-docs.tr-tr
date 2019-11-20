---
title: Tek bit hızlı canlı akış göndermek için Telestream Wirecast Kodlayıcı yapılandırma | Microsoft Docs
description: 'Bu konuda, tek bit hızlı akış gerçek zamanlı kodlama için etkinleştirilmiş kanallar AMS göndermek için Wirecast Canlı Kodlayıcı yapılandırma gösterilmektedir. '
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: be3c75680599c07a3cebe3dcf0436884958e1706
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016676"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Wirecast Kodlayıcı tek bit hızlı canlı akış göndermektir. 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Bu makalede nasıl yapılacağı gösterilmektedir [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) AMS tekli bit hızı akışına kanallar göndermek için gerçek zamanlı Kodlayıcı, gerçek zamanlı kodlama için etkinleştirilir.  Daha fazla bilgi için bkz. [Azure Media Services ile Gerçek Zamanlı Kodlama Gerçekleştirmek İçin Etkinleştirilmiş Kanallar ile Çalışma](media-services-manage-live-encoder-enabled-channels.md).

Bu öğreticide, Azure Media Services Gezgini (AMSE) aracı ile Azure Media Services (AMS) yönetilecek gösterilmektedir. Bu araç yalnızca Windows bilgisayarda çalışır. Mac veya Linux bilgisayarda ise oluşturmak için Azure portalını kullanma [kanalları](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) ve [programlar](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Önkoşullar
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

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Bir kanal adı belirtirseniz açıklama alanı isteğe bağlıdır. Kanal ayarları altında **standart** Live Encoding seçeneğini ayarlamak giriş protokolü için **RTMP**. Olduğu gibi tüm diğer ayarlar bırakabilirsiniz.

    Emin **yeni kanal Şimdi Başlat** seçilir.

3. Tıklayın **kanal oluşturma**.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Kanalı başlatmak için 20 dakika sürebilir.
>
>

Kanal başlatılırken yapabilecekleriniz [kodlayıcıyı Yapılandır](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Faturalandırma, kanalın bir hazır durumuna geçtiğinde hemen sonra başlar. Daha fazla bilgi için [kanalın durumları](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigure_wirecast_rtmp-configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Yapılandırma Telestream Wirecast kodlayıcı
Bu öğreticide, aşağıdaki çıkış ayarları kullanılır. Bu bölümün geri kalanında daha ayrıntılı yapılandırma adımlarını açıklar.

**Video**:

* Bileşeni H.
* Profilinizi Yüksek (düzey 4,0)
* Bit hızı 5000 kbps
* Denetçisinde 2 saniye (60 saniye)
* Kare hızı: 30

**Ses**:

* Bileşeni AAC (LC)
* Bit hızı 192 kbps
* Örnek hız: 44,1 kHz

### <a name="configuration-steps"></a>Yapılandırma adımları
1. Kullanılan ve RTMP akış için ayarlanmış olan makine üzerinde Telestream Wirecast uygulamayı açın.
2. Çıkış giderek yapılandırma **çıkış** sekmesine **çıkış ayarları...** .

    Emin **Çıkış hedefini** ayarlanır **RTMP sunucusu**.
3. **Tamam** düğmesine tıklayın.
4. Ayarlar sayfasında, ayarlama **hedef** olmasını alan **Azure Media Services**.

    Kodlama profili için önceden seçilmiş **Azure H.264 720 p 16:9 (1280 x 720)** . Bu ayarları özelleştirmek için açılan sağındaki dişli simgesini seçin ve ardından **yeni önceden**.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Encoder hazır ayarlarını yapılandırın.

    Ön ayarın adını ve önerilen ayarları için şunları denetleyin:

    **Video**

   * Kodlayıcısı Mainkavram H.,
   * Kare/saniye: 30
   * Ortalama bit hızı: 5000 kbits/sn (ağ kısıtlamalarına göre ayarlanabilir)
   * Profilinizi Ana
   * Anahtar çerçeve sıklığı: 60 çerçeve

     **Ses**

   * Hedef bit hızı: 192 kbits/sn
   * Örnek hız: 44,100 kHz

     ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. **Kaydet**’e basın.

    Yeni oluşturulan profil seçilebilir Encoding alanı artık var.

    Yeni profili seçili olduğundan emin olun.
7. Kanal alma giriş URL'si için Wirecast atamak için **RTMP uç nokta**.

    AMSE aracına gidin ve kanal tamamlanma durumunu denetleyin. Durum değiştiğinde **başlangıç** için **çalıştıran**, giriş URL'sini alabilirsiniz.

    Kanal çalıştırırken, kanal adına sağ tıklayın, üzerine gelindiğinde aşağı gidin **Panoya kopyalama giriş URL** seçip **birincil giriş URL**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Wirecast içinde **çıkış ayarları** penceresinde, bu bilgileri yapıştırın **adresi** çıkış bölümüne ve ata Akış adı alanı.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. **Tamam**’ı seçin.
2. Ana **Wirecast** hazır olduğunuzda, video ve ses giriş kaynağı onaylayın ve ardından isabet **Stream** sol üst köşedeki.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Tıklamadan önce **Stream**, size **gerekir** kanal hazır olduğundan emin olun.
> Ayrıca, kanal hazır durumda > 15 dakikadan fazla akış bir giriş katkı olmadan bırakmamaya emin olun.
>
>

## <a name="test-playback"></a>Testi kayıttan yürütme

AMSE aracına gidin ve test edilecek kanal sağ tıklayın. Menüden üzerine **kayıttan yürütme Önizleme** seçip **Azure Media Player ile**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Ardından akış Player'da görünüyorsa, kodlayıcı düzgün AMS'ye bağlanmak için yapılandırıldı.

Bir hata aldıysanız, kanal sıfırlanması gerekir ve Kodlayıcı ayarları ayarlanır. Bkz: [sorun giderme](media-services-troubleshooting-live-streaming.md) makale Kılavuzu.  

## <a name="create-a-program"></a>Bir program oluşturma
1. Kanal kayıttan yürütme onaylandıktan sonra bir program oluşturun. Altında **canlı** sekmesinde AMSE aracı içinde program alanı sağ tıklatın ve seçin **yeni bir Program oluşturma**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Program adı ve gerekirse ayarlamak **arşiv penceresi uzunluğu** (bunun varsayılan dört saate kadar). Ayrıca, bir depolama konumu belirtin veya varsayılan olarak bırakın.  
3. Denetleme **programı'nı şimdi başlatmak** kutusu.
4. Tıklayın **Program oluşturma**.  

   >[!NOTE]
   >Program oluşturma kanal oluşturmayı daha az zaman alır.
       
5. Program çalışmaya başladığında, kayıttan yürütme programa sağ tıklayıp giderek onaylayın **kayıttan yürütme programlarının** seçip **Azure Media Player ile**.  
6. Onaylandıktan sonra programı tekrar sağ tıklayıp **çıkış URL'sini Panoya Kopyala** (veya bu bilginin alındığı **Program bilgileri ve ayarları** menü seçeneğinden).

Akış bir oynatıcı içinde gömülü veya canlı görüntülemek için bir hedef kitle için Dağıtılmış artık hazırdır.  

## <a name="troubleshooting"></a>Sorun giderme
Bkz: [sorun giderme](media-services-troubleshooting-live-streaming.md) makale Kılavuzu.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
