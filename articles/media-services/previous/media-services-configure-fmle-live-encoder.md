---
title: FMLE kodlayıcısını tek bir bit hızı canlı akış gönderecek şekilde yapılandırma | Microsoft Docs
description: Bu konu, canlı kodlama için etkinleştirilmiş AMS kanallarına tek bit hızlı bir akış göndermek için Flash Media Live Encoder (FMLE) Kodlayıcısı 'nın nasıl yapılandırılacağını gösterir.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 09d9bdffefe9204e9f58b8f07af5b21228269f6c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016748"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Tek bit hızlı canlı akış göndermek için FMLE Kodlayıcısı 'nı kullanın 
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Bu makalede, canlı kodlama için etkinleştirilmiş AMS kanallarına tek bir bit hızı akışı göndermek için [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) (FMLE) Kodlayıcısı 'nın nasıl yapılandırılacağı gösterilmektedir. Daha fazla bilgi için bkz. [Azure Media Services ile Gerçek Zamanlı Kodlama Gerçekleştirmek İçin Etkinleştirilmiş Kanallar ile Çalışma](media-services-manage-live-encoder-enabled-channels.md).

Bu öğreticide, Azure Media Services Gezgini (AMSE) aracı ile Azure Media Services (AMS) yönetilecek gösterilmektedir. Bu araç yalnızca Windows bilgisayarda çalışır. Mac veya Linux bilgisayarda ise oluşturmak için Azure portalını kullanma [kanalları](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) ve [programlar](media-services-portal-creating-live-encoder-enabled-channel.md).

Bu öğreticide, AAC 'nin kullanılması açıklanmaktadır. Ancak, FMLE varsayılan olarak AAC 'yi desteklemez. Mainkavram gibi AAC kodlaması için bir eklenti satın almanız gerekir: [AAC eklentisi](https://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

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

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Bir kanal adı belirtirseniz açıklama alanı isteğe bağlıdır. Kanal ayarları altında **standart** Live Encoding seçeneğini ayarlamak giriş protokolü için **RTMP**. Olduğu gibi tüm diğer ayarlar bırakabilirsiniz.

    Emin **yeni kanal Şimdi Başlat** seçilir.

3. Tıklayın **kanal oluşturma**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> Kanalı başlatmak için 20 dakika sürebilir.
>
>

Kanal başlatılırken yapabilecekleriniz [kodlayıcıyı Yapılandır](media-services-configure-fmle-live-encoder.md).

> [!IMPORTANT]
> Bu durumda, kanal bir hazırlık durumuna geçtiğinde faturalandırma başlar. Daha fazla bilgi için [kanalın durumları](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>FMLE Encoder 'ı yapılandırma
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
1. Kullanılmakta olan makinedeki Flash Media Live Encoder 'ın (FMLE) arabirimine gidin.

    Arabirim, ayarların bir ana sayfasıdır. FMLE kullanarak akışa başlamak için aşağıdaki önerilen ayarları göz önünde yararlanın.

   * Biçim: H., bir kare hızı: 30,00
   * Giriş boyutu: 1280 x 720
   * Bit hızı: 5000 kbps (ağ kısıtlamalarına göre ayarlanabilir)  

     ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Aralıklı kaynakları kullanırken, lütfen "titreşimsiz" seçeneğini onay işareti yapın
2. Biçim ' in yanındaki İngiliz anahtarı simgesini seçin, bu ek ayarların olması gerekir:

   * Profilinizi Ana
   * Düzey: 4.0
   * Ana kare sıklığı: 2 saniye

     ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Aşağıdaki önemli ses ayarını ayarlayın:

   * Biçim: AAC
   * Örnek hız: 44100 Hz
   * Bit hızı 192 Kbps

     ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. FMLE 'nın **RTMP uç noktasına**atamak için kanalın giriş URL 'sini alın.

    AMSE aracına gidin ve kanal tamamlanma durumunu denetleyin. Durum değiştiğinde **başlangıç** için **çalıştıran**, giriş URL'sini alabilirsiniz.

    Kanal çalıştırırken, kanal adına sağ tıklayın, üzerine gelindiğinde aşağı gidin **Panoya kopyalama giriş URL** seçip **birincil giriş URL**.  

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Bu bilgileri çıktı bölümünün **FMS URL 'si** alanına yapıştırın ve bir akış adı atayın.

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Daha fazla artıklık için bu adımları Ikincil giriş URL 'siyle tekrarlayın.
6. **Bağlan**’ı seçin.

> [!IMPORTANT]
> **Bağlan**' a tıklamadan önce kanalın Ready olduğundan emin **olmanız gerekir** .
> Ayrıca, kanal hazır durumda > 15 dakikadan fazla akış bir giriş katkı olmadan bırakmamaya emin olun.
>
>

## <a name="test-playback"></a>Testi kayıttan yürütme

AMSE aracına gidin ve test edilecek kanal sağ tıklayın. Menüden üzerine **kayıttan yürütme Önizleme** seçip **Azure Media Player ile**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Ardından akış Player'da görünüyorsa, kodlayıcı düzgün AMS'ye bağlanmak için yapılandırıldı.

Bir hata aldıysanız, kanal sıfırlanması gerekir ve Kodlayıcı ayarları ayarlanır. Bkz: [sorun giderme](media-services-troubleshooting-live-streaming.md) makale Kılavuzu.  

## <a name="create-a-program"></a>Bir program oluşturma
1. Kanal kayıttan yürütme onaylandıktan sonra bir program oluşturun. Altında **canlı** sekmesinde AMSE aracı içinde program alanı sağ tıklatın ve seçin **yeni bir Program oluşturma**.  

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Programı adlandırın ve gerekirse **Arşiv penceresi uzunluğunu** (varsayılan olarak 4 saat) ayarlayın. Ayrıca, bir depolama konumu belirtin veya varsayılan olarak bırakın.  
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
