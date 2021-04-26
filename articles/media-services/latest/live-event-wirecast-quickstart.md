---
title: Azure Media Services canlı akış oluşturma
description: Portalı ve Kablolayarak Azure Media Services canlı akış oluşturmayı öğrenin
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: de3b3f4b9e871ab95e60f4bb2393e86770069544
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123654"
---
# <a name="create-an-azure-media-services-live-stream"></a>Azure Media Services canlı akış oluşturma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu hızlı başlangıç, Azure portal ve Telestream Kablotonunu kullanarak Azure Media Services canlı bir akış oluşturmanıza yardımcı olur. Azure aboneliğiniz olduğunu ve bir Media Services hesabı oluşturduğunuzu varsayar.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Web tarayıcınızı açın ve [Microsoft Azure Portal](https://portal.azure.com/)gidin. Portalda oturum açmak için kimlik bilgilerinizi girin. Varsayılan görünüm hizmet panonuzu içerir.

Bu hızlı başlangıçta şunları ele alacağız:

- Ücretsiz Telestream Kablobir deneme sürümü ile şirket içi kodlayıcı ayarlama.
- Canlı akış ayarlama.
- Canlı akış çıktıları ayarlanıyor.
- Varsayılan akış uç noktası çalıştırılıyor.
- Canlı akışı ve isteğe bağlı çıktıyı görüntülemek için Azure Media Player kullanma.

Şeyleri basit tutmak için, kablolu atama, geçişli bulut kodlaması ve RTMP 'de Azure Media Services için bir kodlama ön ayarı kullanacağız.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Kablolu dönüştürme kullanarak bir şirket içi kodlayıcı ayarlama

1. [Telestream Web sitesinde](https://www.telestream.net)işletim sisteminiz Için kablolu dönüştürme 'yi indirip yükleyin.
1. Uygulamayı başlatın ve ürünü kaydetmek için sık kullandığınız e-posta adresinizi kullanın. Uygulamayı açık tutun.
1. Aldığınız e-postada, e-posta adresinizi doğrulayın. Ardından, uygulama ücretsiz denemeyi başlatır.
1. Önerilir: uygulama açma ekranında video öğreticisini Izleyin.

## <a name="set-up-an-azure-media-services-live-stream"></a>Azure Media Services canlı akış ayarlama

1. Portalda Azure Media Services hesabına gidin ve **Media Services** listesinden **canlı akış** ' ı seçin.

   ![Canlı akış bağlantısı](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Yeni bir canlı akış olayı oluşturmak için **canlı etkinlik Ekle** ' yi seçin.

   ![Canlı olay simgesi ekle](media/live-events-wirecast-quickstart/add-live-event.png)
1. Yeni olaylarınız için, **canlı olay adı** kutusuna *testliveevent* gibi bir ad girin.

   ![Canlı olay adı kutusu](media/live-events-wirecast-quickstart/live-event-name.png)
1. **Açıklama** kutusuna olay için isteğe bağlı bir açıklama girin.
1. **Doğrudan geçiş – bulut kodlaması yok** seçeneğini belirleyin.

   ![Bulut kodlama seçeneği](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. **RTMP** seçeneğini belirleyin.
1. Canlı etkinlik için ücretsiz olarak faturalandırılmaya engel olmak için, **canlı etkinlik Başlat** için **Hayır** seçeneğinin seçildiğinden emin olun. (Faturalandırma, canlı etkinlik başlatıldığında başlayacaktır.)

   ![Canlı etkinlik seçeneğini Başlat](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. Ayarları gözden geçirmek için **gözden geçir + oluştur** düğmesini seçin.
1. Canlı etkinliği oluşturmak için **Oluştur** düğmesini seçin. Daha sonra canlı etkinlik listesine döndürüyorsunuz.
1. Yeni oluşturduğunuz canlı etkinliğin bağlantısını seçin. Olaylarınızın durdurulduğundan emin olun.
1. Bu sayfayı tarayıcınızda açık tutun. Daha sonra bu yüklemeye geri döneceğiz.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Kablolu yayın Studio 'Yu kullanarak canlı akış ayarlama

1. Kablolu yayın uygulamasında, ana menüden **boş belge oluştur** ' u seçin ve ardından **devam**' ı seçin.

   ![Kablolu yayın başlangıç ekranı](media/live-events-wirecast-quickstart/open-empty-document.png)
1. **Kablolu atama katmanları** alanındaki ilk katmanın üzerine gelin.  Görüntülenen **Ekle** simgesini seçin ve akışa almak istediğiniz video girişini seçin.

   ![Kablolu dönüştürme Ekle simgesi](media/live-events-wirecast-quickstart/add-icon.png)

   **Ana katman 1** iletişim kutusu açılır.
1. Menüden **video yakalama** ' yı seçin ve ardından kullanmak istediğiniz kamerayı seçin.

   ![Video yakalama için önizleme alanı](media/live-events-wirecast-quickstart/video-shot-selection.png)

   Kameradan Görünüm önizleme alanında görüntülenir.
1. **Kablolu atama katmanları** alanındaki ikinci katmanın üzerine gelin. Görüntülenen **Ekle** simgesini seçin ve akışa almak istediğiniz ses girişini seçin. **Ana katman 2** iletişim kutusu açılır.
1. Menüden **ses yakalama** ' yı seçin ve ardından kullanmak istediğiniz ses girişini seçin.

   ![Ses yakalama için girişler](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. Ana menüden **Çıkış ayarları**' nı seçin. **Çıkış hedefi seç** iletişim kutusu görüntülenir.
1. **Hedef** açılan listesinden **Azure Media Services** seçin. Azure Media Services çıktı ayarı, çıkış ayarlarının *çoğunu* otomatik olarak doldurur.

   ![Kablolu yayın çıkış ayarları ekranı](media/live-events-wirecast-quickstart/azure-media-services.png)


Sonraki yordamda, çıkış ayarlarına girmek üzere giriş URL 'sini kopyalamak için tarayıcınızda Azure Media Services geri dönebilirsiniz:

1. Portalın Azure Media Services sayfasında, canlı akış olayını başlatmak için **Başlat** ' ı seçin. (Faturalandırma şimdi başlar.)

   ![Başlangıç simgesi](media/live-events-wirecast-quickstart/start.png)
2. Güvenli **/güvenli değil** durumunu **güvenli değil** olarak ayarlayın. Bu adım, protokolü RTMPS yerine RTMP 'ye ayarlar.
3. **Giriş URL 'si** kutusuna URL 'yi panonuza kopyalayın.

   ![Giriş URL 'SI](media/live-events-wirecast-quickstart/input-url.png)
4. Kablolu atama uygulamasına geçin ve **GIRIŞ URL** 'sini çıkış ayarlarındaki **Adres** kutusuna yapıştırın.

   ![Kablolu atama URL 'SI](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. **Tamam**’ı seçin.

## <a name="set-up-outputs"></a>Çıkışları ayarlama

Bu bölüm, çıktılarınızı ayarlar ve canlı akışınızın bir kaydını kaydetmenizi sağlar.  

> [!NOTE]
> Bu çıktıyı akıtmak için akış uç noktasının çalışıyor olması gerekir. Daha sonra [varsayılan akış uç noktasını çalıştırma](#run-the-default-streaming-endpoint) bölümüne bakın.

1. **Çıktılar** video görüntüleyicisinin altındaki **çıktıları oluştur** bağlantısını seçin.
1. İsterseniz, daha sonra kolayca bulabilmek için **ad** kutusunda çıktının adını daha kolay bir şekilde düzenleyin.
   
   ![Çıkış adı kutusu](media/live-events-wirecast-quickstart/output-name.png)
1. Tüm kutularının geri kalanını şimdilik bırakın.
1. Bir akış Bulucu eklemek için **İleri ' yi** seçin.
1. Bulucunun adını, isterseniz daha kolay bir şekilde değiştirin.
   
   ![Konumlandırıcı ad kutusu](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Bu ekranda bulunan her şeyi şimdilik yalnız bırakın.
1. **Oluştur**’u seçin.

## <a name="start-the-broadcast"></a>Yayını Başlat

1. Kabloda bulunan **Çıkış**  >  **Başlat/yayını durdur**  >  **Başlat Azure Media Services:** ana menüden Azure Media Services.

   ![Yayın menü öğelerini Başlat](media/live-events-wirecast-quickstart/start-broadcast.png)

   Akış canlı olaya gönderildiğinde, Azure Media Services ' deki canlı etkinlik sayfasında video oynatıcı ' da, kablodaki **canlı** pencere görüntülenir.

1. Kablolu atama katmanları için seçtiğiniz videoyu ve sesi yayınlamasını başlatmak için Önizleme penceresinin altındaki **Git** düğmesini seçin.

   ![Kablolu yayın Git düğmesi](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Bir hata oluşursa Player 'ın üzerindeki **oynatıcıyı yeniden yükle** bağlantısını seçerek oynatıcıyı yeniden yüklemeyi deneyin.

## <a name="run-the-default-streaming-endpoint"></a>Varsayılan akış uç noktasını Çalıştır

1. Media Services listesinde **akış uç noktalarını** seçin.

   ![Akış uç noktaları menü öğesi](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Varsayılan akış uç noktası durumu durdurulmuşsa, bunu seçin. Bu adım sizi bu uç noktanın sayfasına götürür.
1. **Başlat**'ı seçin.
   
   ![Akış uç noktası için Başlat düğmesi](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Azure Media Player kullanarak çıkış yayınını yürütün

1. **Çıkış** video oyuncusunun altındakı akış URL 'sini kopyalayın.
1. Bir Web tarayıcısında [Azure Media Player tanıtımı](https://ampdemo.azureedge.net/azuremediaplayer.html)' nı açın.
1. Akış URL 'sini Azure Media Player **URL** kutusuna yapıştırın.
1. **Oynatıcı Güncelleştir** düğmesini seçin.
1. Canlı akışınızı görmek için videoda **oynat** simgesini seçin.

## <a name="stop-the-broadcast"></a>Yayını durdur

Yeterli içerik akışını düşünüyorsanız yayını durdurun.

1. Kabloda bulunan **yayın** düğmesini seçin. Bu adım, kablodan yayını durduruyor.
1. Portalda **Durdur**' u seçin. Ardından canlı akışın durulacağı bir uyarı iletisi alırsınız, ancak çıktı artık isteğe bağlı bir varlık olur.
1. Uyarı iletisindeki **Durdur** ' u seçin. Canlı akış artık kullanılamadığından Azure Media Player şimdi bir hata gösterir.

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
