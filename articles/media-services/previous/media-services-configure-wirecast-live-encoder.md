---
title: Telestream kablolu dönüştürme kodlayıcısını tek bir bit hızı canlı akış gönderecek şekilde yapılandırma | Microsoft Docs
description: "Bu konuda, kablolu yayın Live Encoder 'ın canlı kodlama için etkinleştirilmiş AMS kanallarına tek bir bit hızı akışı göndermek üzere nasıl yapılandırılacağı gösterilmektedir. "
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: a736270919f36cb7d4bbd4e686a98ddc57f402fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016840"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Tek bit hızlı canlı akış göndermek için kablolu dönüştürme Kodlayıcısı 'nı kullanın

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

Bu makalede, [Telestream kablolu yayın](https://www.telestream.net/wirecast/overview.htm) Live Encoder 'ın, canlı kodlama için etkinleştirilmiş AMS kanallarına tek bir bit hızı akışı göndermek üzere nasıl yapılandırılacağı gösterilmektedir. Daha fazla bilgi için bkz. [Azure Media Services ile Gerçek Zamanlı Kodlama Gerçekleştirmek İçin Etkinleştirilmiş Kanallar ile Çalışma](media-services-manage-live-encoder-enabled-channels.md).

Bu öğreticide, Azure Media Services (AMS) Azure Media Services Explorer (AMSE) aracıyla nasıl yönetileceği gösterilmektedir. Bu araç yalnızca Windows bilgisayarında çalışır. Mac veya Linux kullanıyorsanız, [kanalları](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) ve [programları](media-services-portal-creating-live-encoder-enabled-channel.md)oluşturmak için Azure Portal kullanın.

> [!NOTE]
> RTMPS protokolleri kullanılırken kodlayıcılar TLS 1,2 ' i desteklemelidir. TLS 1,2 gereksinimi nedeniyle kablolu yayın sürümü 13.0.2 veya üstünü kullanın.

## <a name="prerequisites"></a>Önkoşullar
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

    ![Ekran görüntüsü, bir menüden seçili kanal oluştur ' un gösterir.](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Bir kanal adı belirtin, açıklama alanı isteğe bağlıdır. Kanal ayarları altında, Live Encoding seçeneği için **Standart** ' ı seçerek giriş Protokolü **RTMP** olarak ayarlanır. Diğer tüm ayarları olduğu gibi bırakabilirsiniz.

    **Yeni kanalı Şimdi Başlat** ' ın seçildiğinden emin olun.

3. **Kanal oluştur**' a tıklayın.

   ![Ekran görüntüsü canlı kanal oluştur iletişim kutusunu gösterir.](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Kanalın başlaması 20 dakika kadar sürebilir.
>
>

Kanal Başlarken, [Encoder 'ı yapılandırabilirsiniz](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Faturalandırma, kanal, bir hazırlık durumuna geçtiğinde başlar. Daha fazla bilgi için bkz. [kanalın durumları](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Telestream kablolu dönüştürme Kodlayıcısı 'nı yapılandırma
Bu öğreticide, aşağıdaki çıkış ayarları kullanılır. Bu bölümün geri kalanında yapılandırma adımları daha ayrıntılı olarak açıklanmaktadır.

**Video**:

* Codec: H.264
* Profil: Yüksek (Seviye 4.0)
* Bit hızı: 5000 kbps
* Ana kare: 2 saniye (60 saniye)
* Kare hızı: 30

**Ses**:

* Codec: AAC (LC)
* Bit hızı: 192 kb/sn
* Örnek hızı: 44,1 kHz

### <a name="configuration-steps"></a>Yapılandırma adımları
1. Kullanılan makinede Telestream kablolu yayın uygulamasını açın ve RTMP akışı için ayarlayın.
2. **Çıkış sekmesine gidip** çıkış ayarları ' nı seçerek çıktıyı yapılandırın **...**

    **Çıktı hedefinin** **RTMP sunucusu** olarak ayarlandığından emin olun.
3. **Tamam**'a tıklayın.
4. Ayarlar sayfasında, **hedef** alanı **Azure Media Services** olarak ayarlayın.

    Kodlama profili **Azure H. ıbu 16:9 (1280x720)** için önceden seçilmiştir. Bu ayarları özelleştirmek için, açılan kutudan sağ tarafındaki dişli simgesini seçin ve ardından **Yeni önayar**' ı seçin.

    ![Ekran görüntüsü, BlobTrigger seçiliyken şablon seç iletişim kutusunu gösterir.](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Kodlayıcı ön ayarlarını yapılandırın.

    Önayarı adlandırın ve aşağıdaki önerilen ayarları kontrol edin:

    **Video**

   * Kodlayıcı: Mainkavram H.,
   * Kare/saniye: 30
   * Ortalama bit hızı: 5000 kbits/sn (ağ kısıtlamalarına göre ayarlanabilir)
   * Profil: Ana
   * Anahtar çerçeve sıklığı: 60 çerçeveler

     **Ses**

   * Hedef bit hızı: 192 kbits/sn
   * Örnek hızı: 44,100 kHz

     ![Ekran görüntüsü, AzureTest1 için Kodlayıcı ön ayarını gösterir.](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. **Kaydet**’e basın.

    Kodlama alanında artık yeni oluşturulan profil seçilebilir.

    Yeni profilin seçildiğinden emin olun.
7. Kablolu atama **RTMP uç noktasına** atamak için kanalın giriş URL 'sini alın.

    AML aracına geri dönün ve kanal tamamlanma durumunu kontrol edin. Durum, **çalışmaya** **BAŞLADıKTAN sonra** giriş URL 'sini alabilirsiniz.

    Kanal çalışırken kanal adına sağ tıklayın, **giriş URL 'sini panoya kopyala** üzerine gelin ve ardından **birincil giriş URL 'sini** seçin.  

    ![Ekran görüntüsü, birincil giriş U R L için giriş U R L 'yi panoya kopyala seçeneğinin gösterildiği gösterir.](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Kablolu yayın **Çıkış ayarları** penceresinde, bu bilgileri çıkış bölümünün **Adres** alanına yapıştırın ve bir akış adı atayın.

    ![Ekran görüntüsü çıkış ayarlarını gösterir.](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. **Tamam**’ı seçin.
2. Ana **kablolu yayın** ekranında, video ve ses için giriş kaynaklarını onaylayın ve sonra sol üst köşedeki **akış** ' ı ziyaret edin.

    ![Ekran görüntüsü kablolu yayın akışı düğmesini gösterir.](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> **Stream**' e tıklamadan önce kanalın Ready olduğundan emin **olmanız gerekir** .
> Ayrıca, > 15 dakikadan daha uzun bir süre boyunca girdi katkısı akışı olmadan kanalı, bir giriş katkı akışı olmadan ayrılmadığınızdan emin olun.
>
>

## <a name="test-playback"></a>Kayıttan yürütmeyi test etme

AML aracına gidin ve test edilecek kanala sağ tıklayın. Menüden **önizlemeyi kayıttan yürütme** üzerine gelin ve **Azure Media Player** seçin.  

![Ekran görüntüsünde önizlemeyi Azure Media Player seçeneğinin seçili olduğunu gösterir.](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Akış Player 'da görünürse, kodlayıcı AMS 'ye bağlanacak şekilde doğru şekilde yapılandırılmıştır.

Bir hata alırsanız, kanalın sıfırlanması ve kodlayıcı ayarlarının ayarlanması gerekir. Rehberlik için [sorun giderme](media-services-troubleshooting-live-streaming.md) makalesine bakın.  

## <a name="create-a-program"></a>Program oluşturma
1. Kanal kayıttan yürütme onaylandıktan sonra bir program oluşturun. AMO aracının **canlı** sekmesi altında, program alanının içine sağ tıklayıp **yeni program oluştur**' u seçin.  

    ![Ekran görüntüsü program oluştur seçeneğinin seçili olduğunu gösterir.](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Programı adlandırın ve gerekirse **Arşiv penceresi uzunluğunu** (varsayılan olarak dört saat) ayarlayın. Ayrıca bir depolama konumu belirtebilir veya varsayılan olarak bırakabilirsiniz.  
3. **Şimdi programa başla** kutusunu işaretleyin.
4. **Program oluştur**' a tıklayın.  

   >[!NOTE]
   >Program oluşturma, kanal oluşturulduktan daha az zaman alır.
       
5. Program çalışmaya başladıktan sonra programa sağ tıklayıp **programları kayıttan yürütmeyi** ve sonra **Azure Media Player**' yi seçerek kayıttan yürütmeyi onaylayın.  
6. Onaylandıktan sonra programa yeniden sağ tıklayın ve **Çıkış URL 'Sini panoya kopyala** ' yı seçin (veya bu bilgileri menüdeki **Program bilgileri ve ayarlar** seçeneğinden alın).

Akış artık bir yürütücüyü katıştırmaya hazırdır veya canlı görüntüleme için bir hedef kitleye dağıtılır.  

## <a name="troubleshooting"></a>Sorun giderme
Rehberlik için [sorun giderme](media-services-troubleshooting-live-streaming.md) makalesine bakın.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
