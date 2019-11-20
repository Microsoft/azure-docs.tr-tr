---
title: Tek bit hızlı canlı akış göndermek için NewTek karmaşık Aster Kodlayıcısı 'nı yapılandırın | Microsoft Docs
description: Bu konuda, karmaşık kodlama için etkinleştirilmiş AMS kanallarına tek bit hızlı bir akış göndermek için, karmaşık kodlayıcının nasıl yapılandırılacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 0e793a5aa7d619b0bb7a1d3efcdf665ea400c555
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2019
ms.locfileid: "69016730"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Tek bit hızlı canlı akış göndermek için NewTek karmaşık Aster Kodlayıcısı 'nı kullanın  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elete canlı](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Bu makalede, [NewTek](https://newtek.com/products/tricaster-40.html) bir bit hızı Live Encoder 'ın canlı kodlama için etkinleştirilmiş AMS kanallarına tek bir bit hızlı akış göndermek için nasıl yapılandırılacağı gösterilmektedir. Daha fazla bilgi için bkz. [Azure Media Services ile Gerçek Zamanlı Kodlama Gerçekleştirmek İçin Etkinleştirilmiş Kanallar ile Çalışma](media-services-manage-live-encoder-enabled-channels.md).

Bu öğreticide, Azure Media Services Gezgini (AMSE) aracı ile Azure Media Services (AMS) yönetilecek gösterilmektedir. Bu araç yalnızca Windows bilgisayarda çalışır. Mac veya Linux bilgisayarda ise oluşturmak için Azure portalını kullanma [kanalları](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) ve [programlar](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Gerçek zamanlı kodlama için etkinleştirilmiş AMS kanallarına bir katkı akışında göndermek üzere karmaşık bir şekilde kullanırken,, akışlar arasında hızlı kesme veya SLA 'lara geçiş yapma gibi karmaşık olaylarınızın belirli özelliklerini kullanırsanız canlı olayınızdaki video/ses bozuklukları olabilir. AMS ekibi bu sorunları gidermeye çalışarak, bu özelliklerin kullanılması önerilmez.
>
>

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

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Bir kanal adı belirtirseniz açıklama alanı isteğe bağlıdır. Kanal ayarları altında **standart** Live Encoding seçeneğini ayarlamak giriş protokolü için **RTMP**. Olduğu gibi tüm diğer ayarlar bırakabilirsiniz.

    Emin **yeni kanal Şimdi Başlat** seçilir.

3. Tıklayın **kanal oluşturma**.

   ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Kanalı başlatmak için 20 dakika sürebilir.
>
>

Kanal başlatılırken yapabilecekleriniz [kodlayıcıyı Yapılandır](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Faturalandırma, kanalın bir hazır durumuna geçtiğinde hemen sonra başlar. Daha fazla bilgi için [kanalın durumları](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigure_tricaster_rtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>NewTek kaçınıcı Encoder 'ı yapılandırma

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

1. Hangi video giriş kaynağının kullanılmakta olduğuna bağlı olarak yeni bir **NewTek karmaşık Aster** projesi oluşturun.
2. Bu proje içinde bir kez, **Stream** düğmesini bulun ve yanındaki dişli simgesine tıklayarak akış yapılandırma menüsüne erişin.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Menü açıldıktan sonra bağlantı başlığının altındaki **Yeni** ' ye tıklayın. Bağlantı türü sorulduğunda **Adobe Flash**' ı seçin.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. **Tamam**'ı tıklatın.
5. Bir FMLE profili artık **akış profili** altında aşağı açılan oka tıklayarak ve **gözatmaya**karşı gezinerek içeri aktarılabilir.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Yapılandırılan FMıLE profilinin kaydedildiği yere gidin.
7. Seçin ve **Tamam**' a basın.

    Profil karşıya yüklendikten sonra, sonraki adıma geçin.
8. Kanalın giriş URL 'sini, bunu karmaşık Aster **RTMP uç noktasına**atamak için alın.

    AMSE aracına gidin ve kanal tamamlanma durumunu denetleyin. Durum değiştiğinde **başlangıç** için **çalıştıran**, giriş URL'sini alabilirsiniz.

    Kanal çalışırken kanal adına sağ tıklayın, **giriş URL 'sini panoya kopyala** üzerine gelin ve ardından **birincil giriş URL 'sini**seçin.  

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Bu bilgileri, karmaşık Aster projesindeki **Flash Server** altındaki **konum** alanına yapıştırın. Ayrıca **AKıŞ kimliği** alanına bir akış adı atayın.

    Akış bilgileri FMLE profiline eklendiyse, **ayarları Içeri aktar**' a tıklayarak, kaydedilen FMLE profiline giderek ve **Tamam**' a tıklayarak bu bölüme de aktarılabilir. İlgili Flash Server alanları FMLE 'nin bilgileriyle doldurulmalıdır.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. İşiniz bittiğinde, ekranın altındaki **Tamam** ' a tıklayın. Daha karmaşık bir şekilde video ve ses girişleri hazırlandıktan sonra **akış** düğmesine tıklayarak AMS 'ye akışa başlayın.

     ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Tıklamadan önce **Stream**, size **gerekir** kanal hazır olduğundan emin olun.
> Ayrıca, kanal hazır durumda > 15 dakikadan fazla akış bir giriş katkı olmadan bırakmamaya emin olun.
>
>

## <a name="test-playback"></a>Testi kayıttan yürütme

AMSE aracına gidin ve test edilecek kanal sağ tıklayın. Menüden üzerine **kayıttan yürütme Önizleme** seçip **Azure Media Player ile**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Ardından akış Player'da görünüyorsa, kodlayıcı düzgün AMS'ye bağlanmak için yapılandırıldı.

Bir hata alınıyorsa, kanalın sıfırlanması ve kodlayıcı ayarlarının ayarlanması gerekir. Bkz: [sorun giderme](media-services-troubleshooting-live-streaming.md) makale Kılavuzu.  

## <a name="create-a-program"></a>Bir program oluşturma

1. Kanal kayıttan yürütme onaylandıktan sonra bir program oluşturun. Altında **canlı** sekmesinde AMSE aracı içinde program alanı sağ tıklatın ve seçin **yeni bir Program oluşturma**.  

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
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

## <a name="next-step"></a>Sonraki adım

Media Services öğrenme yollarını gözden geçirin.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
