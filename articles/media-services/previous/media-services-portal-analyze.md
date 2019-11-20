---
title: Azure portal kullanarak medyanızı çözümleyin | Microsoft Docs
description: Bu konuda, Azure portal kullanarak medyanızın Media Analytics medya işlemcileri (MPs) ile nasıl işlenmesi ele alınmaktadır.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 26a951ffaf0253371ffe69c6df798120f0464082
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881908"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Azure portalını kullanarak medyanızı analiz etme 

> [!IMPORTANT]
> Bazı medya işlemcilerinin [kullanımdan kaldırma planlarını](media-services-analytics-overview.md#retirement-plans) gözden geçirin.

## <a name="overview"></a>Genel Bakış
Azure Media Services Analytics, kuruluşların ve kuruluşların video dosyalarından eyleme dönüştürülebilir Öngörüler türetmesini kolaylaştıran bir konuşma ve görme bileşenleri koleksiyonudur (kurumsal ölçekte, uyumluluk, güvenlik ve küresel erişim). Azure Media Services Analytics hakkında daha ayrıntılı genel bakış için [Bu](media-services-analytics-overview.md) konuya bakın. 

Bu konuda, Azure portal kullanarak medyanızın Media Analytics medya işlemcileri (MPs) ile nasıl işlenmesi ele alınmaktadır. Media Analytics MPs, MP4 dosyaları veya JSON dosyaları oluşturur. Bir medya işlemcisi bir MP4 dosyası üretdiyse, dosyayı aşamalı olarak indirebilirsiniz. Bir medya işlemcisi bir JSON dosyası üretdiyse, dosyayı Azure Blob depolamadan indirirler. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Çözümlemek istediğiniz bir varlık seçin
1. [Azure portalında](https://portal.azure.com/) Azure Media Services hesabınızı seçin.
2. **Ayarlar** penceresinde **Varlıklar**’ı seçin.  
   
    ![Videoları analiz etme](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Çözümlemek istediğiniz varlığı seçin ve **Çözümle** düğmesine basın.
   
    ![Videoları analiz etme](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. **İşlem medyası varlığını Media Analytics** penceresinde, işlemciyi seçin. 
   
    Makalenin geri kalanında her işlemcinin neden ve nasıl kullanılacağı açıklanmaktadır. 
5. İşe başlamak için **Oluştur** ' a basın.

## <a name="azure-media-indexer"></a>Azure Media Indexer
**Azure Media Indexer** medya işlemcisi, medya dosyalarını ve içeriği aranabilir yapmanızı ve kapalı açıklamalı altyazı parçaları oluşturmanızı sağlar. Bu bölümde, bu MP için belirttiğiniz seçeneklerle ilgili bazı ayrıntılar verilmektedir.

![Videoları analiz etme](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Dil
Çoklu ortam dosyasında tanınmak için doğal dil. Örneğin, Ingilizce veya Ispanyolca. 

### <a name="captions"></a>Açıklamalı alt yazılar
İçeriğinizden oluşturulacak bir resim yazısı biçimi seçebilirsiniz. Bir dizin oluşturma işi, aşağıdaki biçimlerde kapalı açıklamalı altyazı dosyaları oluşturabilir:  

* **LAPONCA**
* **TTML**
* **WebVTT**

Bu biçimlerdeki kapalı açıklamalı altyazı (CC) dosyaları, ses ve video dosyalarını işitme engelli kişiler için erişilebilir hale getirmek için kullanılabilir.

### <a name="aib-file"></a>AıB dosyası
Özel SQL Server IFilter ile kullanmak üzere ses dizini blobu dosyası oluşturmak istiyorsanız bu seçeneği belirleyin. Daha fazla bilgi için [Bu](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) bloga bakın.

### <a name="keywords"></a>Anahtar sözcükler
Anahtar kelimeler XML dosyası oluşturmak istiyorsanız bu seçeneği belirleyin. Bu dosya, sıklık ve konum bilgileri ile konuşma içeriğinden ayıklanan anahtar sözcükler içerir.

### <a name="job-name"></a>İş adı
İşi tanımlamanızı sağlayan kolay bir ad. [Bu](media-services-portal-check-job-progress.md) makalede bir işin ilerlemesini nasıl izleyebileceğinizi açıklar. 

### <a name="output-file"></a>Çıktı dosyası
Çıktı içeriğini tanımlamanızı sağlayan kolay bir ad. 

### <a name="speed"></a>Hız
Giriş videosunu hızlandırmanın hızını belirtin. Çıktı, giriş videosunun bir sabitlenmiş ve zaman atlamalı şekilde çevrilmesinden oluşur.

### <a name="job-name"></a>İş adı
İşi tanımlamanızı sağlayan kolay bir ad. [Bu](media-services-portal-check-job-progress.md) makalede bir işin ilerlemesini nasıl izleyebileceğinizi açıklar. 

### <a name="output-file"></a>Çıktı dosyası
Çıktı içeriğini tanımlamanızı sağlayan kolay bir ad. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
**Azure Media Face Detector** medya IŞLEMCISI (MP), yüz ifadeleriyle hareket etmenizi, hareketleri izlemenizi ve hatta ölçek katılımı ve yeniden eylemini izlemenize olanak sağlar. Bu hizmet iki özellik içerir: 

* **Yüz algılama**
  
    Yüz algılama, video içindeki insan yüzlerini bulur ve izler. Birden çok yüz algılanabilir ve sonra bir JSON dosyasında döndürülen zaman ve konum meta verileri ile birlikte hareket ettikleri sırada izlenir. İzleme sırasında, Kullanıcı ekranda dolaştıklarında veya kısa bir süre sonra bile olsa da, aynı yüz için tutarlı bir KIMLIK vermemeye çalışır.
  
  > [!NOTE]
  > Bu hizmetler yüz tanıma gerçekleştirmez. Çerçeveyi atan bir kişiye veya çok uzun süre boyunca geri döntiklerinde yeni bir KIMLIK verilmeyecektir.
  > 
  > 
* **Duygu algılama**
  
    Duygu algılama, anormal, sadyetler, korku, Anger ve daha fazlası dahil olmak üzere, algılanan yüzlerden çok sayıda öznitelik Analizi döndüren Yüz Algılama medya Işlemcisinin isteğe bağlı bir bileşenidir. 

![Videoları analiz etme](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Algılama modu
Aşağıdaki modlardan biri işlemci tarafından kullanılabilir:

* Yüz algılama
* yüz başına duygu algılama
* toplu duygu tanıma algılaması

### <a name="job-name"></a>İş adı
İşi tanımlamanızı sağlayan kolay bir ad. [Bu](media-services-portal-check-job-progress.md) makalede bir işin ilerlemesini nasıl izleyebileceğinizi açıklar. 

### <a name="output-file"></a>Çıktı dosyası
Çıktı içeriğini tanımlamanızı sağlayan kolay bir ad. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
**Azure Media Motion Detector** medya IŞLEMCISI (MP), diğer bir deyişle, diğer bir deyişle, diğer bir deyişle, diğer bir deyişle, diğer bir deyişle, önemli olmayan video Hareket algılama, videonun, hareketin gerçekleştiği yerleri belirlemek için statik kamera çekimleri üzerinde kullanılabilir. Zaman damgalarına ve olayın gerçekleştiği sınırlayıcı bölgeye sahip bir meta veri içeren bir JSON dosyası oluşturur.

Güvenlik video akışlarına hedeflenmiş bu teknoloji, ilgili olaylara yönelik hareketi ve gölge ve aydınlatma değişiklikleri gibi hatalı pozitif sonuçları kategorilere ayırmanıza olanak sağlar. Bu, çok uzun bir gözetim videolarından ilgi çekici bir şekilde istenmeyen olaylar elde etmeksizin kamera akışından güvenlik uyarıları oluşturmanıza olanak sağlar.

![Videoları analiz etme](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Bu işlemci, kaynak videodan ilgi çekici parçacıkları otomatik olarak seçerek uzun videoların özetlerini oluşturmanıza yardımcı olabilir. Bu, uzun bir videoda beklendiklere ilişkin hızlı bir genel bakış sağlamak istediğinizde yararlıdır. Ayrıntılı bilgi ve örnekler için bkz. [bir video özeti oluşturmak için Azure Media video Thumbnails kullanma](media-services-video-summarization.md)

![Videoları analiz etme](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>İş adı
İşi tanımlamanızı sağlayan kolay bir ad. [Bu](media-services-portal-check-job-progress.md) makalede bir işin ilerlemesini nasıl izleyebileceğinizi açıklar. 

### <a name="output-file"></a>Çıktı dosyası
Çıktı içeriğini tanımlamanızı sağlayan kolay bir ad. 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
Bu işlemci, videolardaki olası yetişkinlere ve en kötü içerikleri saptamanıza yardımcı olur. İşlemci, videodaki görüntüleri ve ana kareleri otomatik olarak algılar. Olası yetişkin veya ANCY içeriği için ana kareleri puan eder ve varsayılan eşiklere göre incelemeler önerir. Ayrıntılı bilgi ve örnekler için bkz. [Azure Media Content moderator 'yi orta videoları Ile kullanma](media-services-content-moderation.md)

![Orta videolar](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Sürüm 
"2,0" kullanın.

### <a name="mode"></a>Mod
2,0 sürümü `Mode` ayarını yoksayar.

## <a name="next-steps"></a>Sonraki adımlar
Media Services öğrenme yollarını görüntüleyin.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirim sağlayın
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
