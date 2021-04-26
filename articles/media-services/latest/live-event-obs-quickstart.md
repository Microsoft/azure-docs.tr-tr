---
title: OBS Studio ile canlı akış oluşturma
description: Portal ve OBS Studio kullanarak Azure Media Services canlı akış oluşturmayı öğrenin
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/20/2021
ms.openlocfilehash: 0e425cddea1adaec8bfb8f0055b55bb0c45fb168
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123644"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>OBS ile Azure Media Services canlı akış oluşturma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu hızlı başlangıç, Azure portal kullanarak Media Services canlı bir olay oluşturmanıza yardımcı olur. Azure aboneliğiniz olduğunu ve bir Media Services hesabı oluşturduğunuzu varsayar.

Bu hızlı başlangıçta şunları ele alacağız:

- OBS ile şirket içi kodlayıcı ayarlama.
- Canlı akış ayarlama.
- Canlı akış çıktıları ayarlanıyor.
- Varsayılan akış uç noktası çalıştırılıyor.
- Canlı akışı ve isteğe bağlı çıktıyı görüntülemek için Azure Media Player kullanma.

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Web tarayıcınızı açın ve [Microsoft Azure Portal](https://portal.azure.com/)gidin. Portalda oturum açmak için kimlik bilgilerinizi girin. Varsayılan görünüm hizmet panonuzu içerir.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>OBS kullanarak bir şirket içi kodlayıcı ayarlama

1. [Açık yayıncı yazılım web sitesinde](https://obsproject.com/)işletim sisteminiz için OBS 'yi indirin ve yükleyin.
1. Uygulamayı başlatın ve açık tutun.

## <a name="run-the-default-streaming-endpoint"></a>Varsayılan akış uç noktasını Çalıştır

1. Media Services listesinde **akış uç noktalarını** seçin.

   ![Akış uç noktaları menü öğesi.](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Varsayılan akış uç noktası durumu durdurulmuşsa, bunu seçin. Bu adım sizi bu uç noktanın sayfasına götürür.
1. **Başlat**'ı seçin.

   ![Akış uç noktası için Başlat düğmesi.](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Azure Media Services canlı akış ayarlama

1. Portalda Azure Media Services hesabına gidin ve **Media Services** listesinden **canlı akış** ' ı seçin.

   ![Canlı akış bağlantısı.](media/live-events-obs-quickstart/select-live-streaming.png)
1. Yeni bir canlı akış olayı oluşturmak için **canlı etkinlik Ekle** ' yi seçin.

   ![Canlı olay simgesi ekle.](media/live-events-obs-quickstart/add-live-event.png)
1. Yeni olaylarınız için, **canlı olay adı** kutusuna *testliveevent* gibi bir ad girin.

   ![Canlı olay adı kutusu.](media/live-events-obs-quickstart/live-event-name.png)
1. **Açıklama** kutusuna olay için isteğe bağlı bir açıklama girin.
1. **Doğrudan geçiş – bulut kodlaması yok** seçeneğini belirleyin.

   ![Bulut kodlama seçeneği.](media/live-events-obs-quickstart/cloud-encoding.png)
1. **RTMP** seçeneğini belirleyin.
1. Canlı etkinlik için ücretsiz olarak faturalandırılmaya engel olmak için, **canlı etkinlik Başlat** için **Hayır** seçeneğinin seçildiğinden emin olun. (Faturalandırma, canlı etkinlik başlatıldığında başlayacaktır.)

   ![Canlı etkinlik seçeneğini başlatın.](media/live-events-obs-quickstart/start-live-event-no.png)
1. Ayarları gözden geçirmek için **gözden geçir + oluştur** düğmesini seçin.
1. Canlı etkinliği oluşturmak için **Oluştur** düğmesini seçin. Daha sonra canlı etkinlik listesine döndürüyorsunuz.
1. Oluşturduğunuz canlı etkinliğin bağlantısını seçin. Olaylarınızın durdurulduğundan emin olun.
1. Bu sayfayı tarayıcınızda açık tutun. Daha sonra bu yüklemeye geri döneceğiz.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>OBS Studio 'Yu kullanarak canlı akış ayarlama

OBS varsayılan sahneye başlar, ancak hiçbir giriş seçilmemiş.

   ![OBS varsayılan ekran](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Video kaynağı ekleme

1. **Kaynaklar** panelinden yeni bir kaynak cihaz seçmek için **Ekle** simgesini seçin. **Kaynaklar** menüsü açılır.

1. Kaynak cihaz menüsünden **video yakalama cihazı** ' nı seçin. **Kaynak oluştur/Seç** menüsü açılır.

   ![Video cihazının seçili olduğu OBS kaynakları menüsü.](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. **Var olan radyo ekle** düğmesini seçin ve ardından **Tamam**' ı seçin. **Video aygıtı menüsü özellikleri** açılır.

   ![Var olanı Ekle seçiliyken yeni video kaynağı menüsü.](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. **Cihaz** açılan listesinden, yayınınız için kullanmak istediğiniz video girişini seçin. Geri kalan ayarları hemen bırakın ve **Tamam**' ı seçin. Giriş kaynağı **kaynaklar** paneline eklenir ve video giriş görünümü **Önizleme** alanında görüntülenir.

   ![OBS kamera ayarları](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Ses kaynağı ekleme

1. **Kaynaklar** panelinden yeni bir kaynak cihaz seçmek için **Ekle** simgesini seçin. Kaynak cihaz menüsü açılır.

1. Kaynak cihaz menüsünden **ses girişi yakalama** ' yı seçin. **Kaynak oluştur/Seç** menüsü açılır.

   ![Ses cihazı seçiliyken OBS kaynakları menüsü.](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. **Var olan radyo ekle** düğmesini seçin ve ardından **Tamam**' ı seçin. **Ses girişi yakalama menüsü özellikleri** açılır.

   ![Var olan ekle seçili olan OBS ses kaynağı.](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. **Cihaz** açılan listesinden, yayınınız için kullanmak istediğiniz ses yakalama cihazını seçin. Geri kalan ayarları hemen bırakın ve Tamam ' ı seçin. Ses yakalama aygıtı ses karıştırıcı paneline eklenecektir.

   ![OBS ses cihazı seçim açılan listesi](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-and-advanced-encoding-settings-in-obs"></a>OBS 'de akış ve gelişmiş kodlama ayarları ayarlama

Sonraki yordamda, çıkış ayarlarına girmek üzere giriş URL 'sini kopyalamak için tarayıcınızda Azure Media Services geri dönebilirsiniz:

1. Portalın Azure Media Services sayfasında, canlı akış olayını başlatmak için **Başlat** ' ı seçin. (Faturalandırma şimdi başlar.)

   ![Başlangıç simgesi.](media/live-events-obs-quickstart/start.png)
1. **RTMP** 'Yi **RTMPS** olarak ayarlayın.
1. **Giriş URL 'si** kutusuna URL 'yi panonuza kopyalayın.

   ![Giriş URL 'SI.](media/live-events-obs-quickstart/input-url.png)

1. OBS uygulamasına geçiş yapın.

1. **Denetimler** panelinde **Ayarlar** düğmesini seçin. Ayarlar seçenekleri açılır.

   ![Ayarlar seçiliyken OBS denetimleri paneli.](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. **Ayarlar** menüsünden **akış** ' ı seçin.

1. **Hizmet** açılan listesinden tümünü göster ' i seçin, sonra **özel...** seçeneğini belirleyin.

1. **Sunucu** alanında, panonuza kopyaladığınız RTMPS URL 'sini yapıştırın.

1. **Akış anahtarı** alanına bir öğe girin.  Bu, ne olduğu konusunda gerçekten bağımsız değildir, ancak bir değere sahip olması gerekir.

    ![OBS akış ayarları.](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. **Ayarlar** menüsünden **Çıkış ' ı** seçin.

1. Sayfanın üst kısmındaki **çıkış modu** açılan listesini seçin ve kullanılabilir tüm kodlayıcı ayarlarına erişmek için **Gelişmiş** ' i seçin.

1. Kodlayıcıyı ayarlamak için **akış** sekmesini seçin.

1. Sisteminiz için doğru kodlayıcıyı seçin.  Donanımınız GPU hızlandırmasını destekliyorsa, NVıDıA **Nvenc** H., veya Intel **QuickSync** H. ation ' dan birini seçin. Sisteminizde desteklenen bir GPU yoksa, **x264** Software Encoder seçeneğini belirleyin.

#### <a name="x264-encoder-settings"></a>X264 kodlayıcı ayarları

1. **X264** Encoding seçeneğini belirlediyseniz, **Rescale çıkış** kutusunu seçin. Standart (720P) canlı bir olay kullanıyorsanız, Media Services veya 1280x720 ' de Premium canlı etkinlik kullanıyorsanız 1920x1080 ' yi seçin.  Bir geçişli canlı etkinlik kullanıyorsanız, kullanılabilir herhangi bir çözümü seçebilirsiniz.

1. **Bit hızını** 1500 kbps ve 4000 kbps arasında bir yere ayarlayın. 720P 'de standart kodlama canlı olayı kullanıyorsanız 2500 kbps önerilir. 1080P Premium canlı etkinliği kullanıyorsanız, 4000 kbps önerilir. İstenen kalite ayarını elde etmek için ağınızdaki kullanılabilir CPU özelliklerine ve bant genişliğine göre bit hızını ayarlamak isteyebilirsiniz.

1. **Ana kare aralığı** alanına *2* yazın. Değer, anahtar kare aralığını 2 saniyeye ayarlar ve bu, Media Services, HLS veya DASH üzerinden teslim edilen parçaların son boyutunu denetler. Anahtar çerçeve aralığını 4 saniyeden daha yükseğe hiçbir zaman ayarlayın.  Yayın sırasında yüksek gecikme süresi görüyorsanız, uygulama kullanıcılarınızı her zaman bu değeri 2 saniyeye ayarlamaya yönelik her zaman iki kez göz atın veya bildirmeniz gerekir. Düşük gecikmeli canlı teslim almaya çalışırken, bu değeri 1 saniye düşük olarak ayarlamayı tercih edebilirsiniz.

1. Isteğe bağlı: CPU kullanımı ön ayarını **veryfast** olarak ayarlayın ve bir veya daha fazla ek yük ile, yerel CPU 'nun bit hızı ve önayar birleşimini işleyebileceğini görmek için bir denemeleri çalıştırın. Canlı akış sırasında herhangi bir sorunu önlemek için %80 ' den yüksek bir CPU ile sonuçlanacak ayarları kullanmaktan kaçının. Kaliteyi artırmak için, CPU sınırlamalarına ulaşana kadar **daha hızlı** ve **hızlı** önceden ayarlanmış ayarlarla test edebilirsiniz.

   ![OBS x264 kodlayıcı ayarları](media/live-events-obs-quickstart/live-event-obs-x264-settings.png)

1. Geri kalan ayarları değiştirmeden bırakın ve **Tamam**' ı seçin.

#### <a name="nvidia-nvenc-encoder-settings"></a>NVIDIA NVENC kodlayıcı ayarları

1. **Nvenc** GPU kodlama seçeneğini belirlediyseniz, **Recale çıkış** kutusunu Işaretleyin ve Media Services veya standart (720p) canlı bir olay kullanıyorsanız, 1920x1080 veya 1280x720 ' de bir Premium canlı etkinlik kullanıyorsanız, bu seçeneği belirleyin. Bir geçişli canlı etkinlik kullanıyorsanız, kullanılabilir herhangi bir çözümü seçebilirsiniz.

1. Sabit bit hızı hız denetimi için **hız DENETIMINI** CBR olarak ayarlayın.

1. **Bit hızını** 1500 kbps ve 4000 kbps arasında bir yere ayarlayın. 720P 'de standart kodlama canlı olayı kullanıyorsanız 2500 kbps önerilir. 1080P Premium canlı etkinliği kullanıyorsanız, 4000 kbps önerilir. Bu ayarı, istenen kalite ayarını elde etmek için ağınızdaki kullanılabilir CPU özelliklerine ve bant genişliğine göre ayarlamayı tercih edebilirsiniz.

1. X264 seçeneklerinin altında belirtilen **ana kare aralığını** 2 saniyeye ayarlayın. Bu, canlı yayınınızın gecikmesini önemli ölçüde etkileyebileceğinden 4 saniyeyi aşmayın.

1. Yerel makinenizde CPU hızına bağlı olarak, **önceden ayarlanan ayarı** düşük gecikme süresi, Low-Latency performans veya Low-Latency kalitesi olarak ayarlayın. Kendi donanımınızın kalite ve CPU kullanımı arasındaki en iyi dengeyi elde etmek için bu ayarlarla denemeler yapın.

1. Daha güçlü bir donanım yapılandırması kullanıyorsanız, **profili** "Main" veya "High" olarak ayarlayın.

1. **İleri** işaretini işaretsiz bırakın. Çok güçlü bir makineniz varsa bunu kontrol edebilirsiniz.

1. **Psycho görsel ayarlamasını** işaretsiz bırakın. Çok güçlü bir makineniz varsa bunu kontrol edebilirsiniz.

1. Hangi GPU 'Ların ayıracağına otomatik olarak karar vermek için **GPU 'yu** 0 olarak ayarlayın. İsterseniz GPU kullanımını kısıtlayabilirsiniz.

1. **En fazla B karesini** 2 olarak ayarlayın

   ![OBS NVIDIA NVIDIA NVENC GPU Kodlayıcısı ayarları.](media/live-events-obs-quickstart/live-event-obs-nvidia-settings.png)

#### <a name="intel-quicksync-encoder-settings"></a>Intel QuickSync Kodlayıcısı ayarları

1. Intel **QuickSync** GPU kodlama seçeneğini belirlediyseniz, **Recale çıkış** kutusunu Işaretleyin ve Media Services veya standart (720p) canlı bir olay kullanıyorsanız, 1920x1080 veya 1280X720 ' de bir Premium canlı etkinlik kullanıyorsanız, bu seçeneği belirleyin. Bir geçişli canlı etkinlik kullanıyorsanız, kullanılabilir herhangi bir çözümü seçebilirsiniz.

1. **Hedef kullanımını** "dengeli" olarak AYARLAYıN veya CPU ve GPU Birleşik yüküne göre gereken şekilde ayarlayın. Donanımınızın üretme yeteneğine sahip olduğu kaliteyle ortalama olarak %80 maksimum CPU kullanımı elde etmek için gereken şekilde ayarlayın ve deneyin. Daha kısıtlamalı bir donanımınız varsa, performans sorunlarınız varsa "hızlı" veya "çok hızlı" ile test edin.

1. Daha güçlü bir donanım yapılandırması kullanıyorsanız, **profili** "Main" veya "High" olarak ayarlayın.

1. X264 seçeneklerinin altında belirtilen **ana kare aralığını** 2 saniyeye ayarlayın. Bu, canlı yayınınızın gecikmesini önemli ölçüde etkileyebileceğinden 4 saniyeyi aşmayın.

1. Sabit bit hızı hız denetimi için **hız DENETIMINI** CBR olarak ayarlayın.

1. **Bit hızı** 1500 Ile 4000 kbps arasında bir yere ayarlayın.  720P 'de standart kodlama canlı olayı kullanıyorsanız 2500 kbps önerilir. 1080P Premium canlı etkinliği kullanıyorsanız, 4000 kbps önerilir. Bu ayarı, istenen kalite ayarını elde etmek için ağınızdaki kullanılabilir CPU özelliklerine ve bant genişliğine göre ayarlamayı tercih edebilirsiniz.

1. **Gecikmeyi** "düşük" olarak ayarlayın.

1. **B çerçevelerini** 2 olarak ayarlayın.

1. **Öznel video geliştirmelerini** işaretsiz bırakın.

   ![OBS Intel QuickSync GPU Kodlayıcısı ayarları.](media/live-events-obs-quickstart/live-event-obs-intel-settings.png)

### <a name="set-audio-settings"></a>Ses ayarlarını ayarla

Sonraki yordamda, ses kodlama ayarlarını ayarlayacaksınız.

1. Ayarlar ' da çıkış->ses sekmesini seçin.

1. 1 **Ses bit hızını** 128 kbps olarak ayarlayın.

   ![OBS ses bit hızı ayarları.](media/live-events-obs-quickstart/live-event-obs-audio-output-panel.png)

1. Ayarlar ' da ses sekmesini seçin.

1. **Örnek hızını** 44,1 kHz olarak ayarlayın.

   ![OBS ses örneği hız ayarları.](media/live-events-obs-quickstart/live-event-obs-audio-sample-rate-settings.png)

### <a name="start-streaming"></a>Akışı Başlat

1. **Denetimler** panelinde **akışı Başlat**' a tıklayın.

    ![OBS akışı Başlat düğmesi.](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Tarayıcınızda Azure Media Services canlı etkinlik ekranına geçin ve **Player 'ı yeniden yükle** bağlantısına tıklayın. Artık Stream 'i önizleme yürütücüsünde görmeniz gerekir.

## <a name="set-up-outputs"></a>Çıkışları ayarlama

Bu bölüm, çıktılarınızı ayarlar ve canlı akışınızın bir kaydını kaydetmenizi sağlar.  

> [!NOTE]
> Bu çıktıyı akıtmak için akış uç noktasının çalışıyor olması gerekir. Daha sonra [varsayılan akış uç noktasını çalıştırma](#run-the-default-streaming-endpoint) bölümüne bakın.

1. **Çıktılar** video görüntüleyicisinin altındaki **çıktıları oluştur** bağlantısını seçin.
1. İsterseniz, daha sonra kolayca bulabilmek için **ad** kutusunda çıktının adını daha kolay bir şekilde düzenleyin.

   ![Çıkış adı kutusu.](media/live-events-wirecast-quickstart/output-name.png)
1. Tüm kutularının geri kalanını şimdilik bırakın.
1. Bir akış Bulucu eklemek için **İleri ' yi** seçin.
1. Bulucunun adını, isterseniz daha kolay bir şekilde değiştirin.

   ![Konumlandırıcı ad kutusu.](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Bu ekranda bulunan her şeyi şimdilik yalnız bırakın.
1. **Oluştur**’u seçin.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Azure Media Player kullanarak çıkış yayınını yürütün

1. **Çıkış** video oyuncusunun altındakı akış URL 'sini kopyalayın.
1. Bir Web tarayıcısında [Azure Media Player tanıtımı](https://ampdemo.azureedge.net/azuremediaplayer.html)' nı açın.
1. Akış URL 'sini Azure Media Player **URL** kutusuna yapıştırın.
1. **Oynatıcı Güncelleştir** düğmesini seçin.
1. Canlı akışınızı görmek için videoda **oynat** simgesini seçin.

## <a name="stop-the-broadcast"></a>Yayını durdur

Yeterli içerik akışını düşünüyorsanız yayını durdurun.

1. Portalda **Durdur**' u seçin.

1. OBS 'de, **denetimler** panelinde **akışı durdur** düğmesini seçin. Bu adım OBS 'den yayını sonlandırır.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Azure Media Player kullanarak isteğe bağlı çıktıyı yürütün

Oluşturduğunuz çıktı, artık akış uç noktası çalıştığı sürece isteğe bağlı akış için kullanılabilir.

1. Media Services listesine gidin ve **varlıklar**' ı seçin.
1. Daha önce oluşturduğunuz olay çıktısını bulun ve varlık bağlantısını seçin. Varlık çıkış sayfası açılır.
1. Varlık için video oynatıcı altındaki akış URL 'sini kopyalayın.
1. Tarayıcıda Azure Media Player dön ve akış URL 'sini URL kutusuna yapıştırın.
1. **Oynatıcıyı Güncelleştir**' i seçin.
1. İsteğe bağlı varlığı görüntülemek için videoda **oynat** simgesini seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

> [!IMPORTANT]
> Hizmetleri durdurun! Bu hızlı başlangıçta bulunan adımları tamamladıktan sonra, canlı etkinliği ve akış uç noktasını durdurduğunuzdan emin olun veya çalışmaya devam ettikleri süre için faturalandırılırsınız. Canlı etkinliği durdurmak için [yayını durdurma](#stop-the-broadcast) yordamını, adım 2 ve 3 ' e bakın.

Akış uç noktasını durdurmak için:

1. Media Services listesinden **akış uç noktaları**' nı seçin.
2. Daha önce başlattığınız varsayılan akış uç noktasını seçin. Bu adım uç noktanın sayfasını açar.
3. **Durdur**' u seçin.

> [!TIP]
> Varlıkları bu olaydan korumak istemiyorsanız, depolama için faturalandırılmaması için bunları sildiğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Canlı etkinlikler ve canlı çıktılar Media Services](./live-event-outputs-concept.md)
