---
title: Azure portalını kullanarak isteğe bağlı video göndermeye başlama | Microsoft Belgeleri
description: Bu öğretici, Azure portalı kullanarak Azure Media Services uygulaması ile temel bir isteğe bağlı video içerik teslim hizmeti uygulamanın adımlarını açıklar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 4a947c01d63e3842ead91481e480024a54380144
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "69015049"
---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>Azure Portal’ı kullanarak isteğe bağlı içerik göndermeye başlama

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>En son sürüm olan [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)’ü inceleyin. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-from-v2-to-v3.md)

Bu öğretici, Azure portalı kullanarak Azure Media Services uygulaması ile temel bir isteğe bağlı video içerik teslim hizmeti uygulamanın adımlarını açıklar.

## <a name="prerequisites"></a>Önkoşullar
Öğreticiyi tamamlamak için aşağıdakiler gereklidir:

* Bir Azure hesabı. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/). 
* Bir Media Services hesabı. Bir Media Services hesabı oluşturmak için bkz. [Media Services hesabı oluşturma](media-services-portal-create-account.md).

Bu öğretici aşağıdaki görevleri içerir:

1. Akış uç noktasını başlatın.
2. Bir video dosyası yükleyin.
3. Kaynak dosyayı uyarlamalı bit hızlı bir MP4 dosyaları grubuna kodlayın.
4. Varlığı yayımlayın, akış ve aşamalı indirme URL’lerini alın.  
5. İçeriğinizi oynatın.

## <a name="start-the-streaming-endpoint"></a>Akış uç noktasını başlatma

Azure Media Services ile çalışırken en sık karşılaşılan senaryolardan biri bit hızı uyarlamalı akış iletmektir. Media Services dinamik paketleme olanağı sağlar. Dinamik paketleme ile bit hızı uyarlamalı MP4 biçiminde kodlanmış içeriklerinizi Media Services tarafından desteklenen anlık akış biçimlerinde sunabilirsiniz. Örnekler arasında Apple HTTP Canlı Akışı (HLS), Microsoft Kesintisiz Akış ve Dynamic Adaptive Streaming over HTTP (DASH, MPEG-DASH olarak da bilinir) bulunur. Media Services bit hızı uyarlamalı akışı kullanarak, bu akış biçimlerinin önceden paketlenmiş sürümlerini depolamadan videolarınızı sunabilirsiniz.

> [!NOTE]
> Media Services hesabınızı oluşturduğunuzda hesabınıza **Durdurulmuş** durumda bir varsayılan akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumda olması gerekir. 

Akış uç noktasını başlatmak için:

1. [Azure Portal](https://portal.azure.com/) oturum açın.
2. **Ayarlar** > **Akış uç noktaları**’nı seçin. 
3. Varsayılan akış uç noktasına tıklayın. **VARSAYILAN AKIŞ UÇ NOKTASI AYRINTILAR** penceresi görüntülenir.
4. **Başlat** simgesini seçin.
5. **Kaydet** düğmesini seçin.

## <a name="upload-files"></a>Dosyaları karşıya yükleme
Media Services kullanarak video akışı sağlamak için kaynak videoları karşıya yükler, bunları çoklu bit hızlarında kodlar ve sonucu yayımlarsınız. İlk adım, bu bölümde ele alınmıştır. 

1. [Azure portalında](https://portal.azure.com/) Azure Media Services hesabınızı seçin.
2. **Ayarlar** > **Varlıklar**’ı seçin. Ardından **Karşıya Yükle** düğmesini seçin.
   
    ![Dosyaları karşıya yükleme](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    **Varlığı karşıya yükle** penceresi görüntülenir.
   
   > [!NOTE]
   > Media Services, karşıya yüklenecek videoların dosya boyutunu kısıtlamaz.
   > 
   > 
3. Bilgisayarınızda, karşıya yüklemek istediğiniz videoya gidin. Videoyu seçin ve ardından **Tamam**’ı seçin.  
   
    Karşıya yükleme başlar. Dosya adı altında ilerleme durumunu görebilirsiniz.  

Karşıya yükleme tamamlandığında, yeni varlık **Varlıklar** bölmesinde listelenir. 

## <a name="encode-assets"></a>Varlıkları kodlama
Dinamik paketlemeden yararlanmak için kaynak dosyanızı çoklu bit hızına sahip bir dizi MP4 dosyası olarak kodlamanız gerekir. Kodlama adımları bu bölümde gösterilmiştir.

### <a name="encode-assets-in-the-portal"></a>Portalda varlıkları kodlama
İçeriğinizi Azure portalında Media Encoder Standard kullanarak kodlamak için:

1. [Azure portalında](https://portal.azure.com/) Azure Media Services hesabınızı seçin.
2. **Ayarlar** > **Varlıklar**’ı seçin. Kodlamak istediğiniz varlığı seçin.
3. **Kodla** düğmesini seçin.
4. **Bir varlık kodla** bölmesinde, **Media Encoder Standard** işlemcisini ve bir ön ayarı seçin. Hazır ayarlar hakkında daha fazla bilgi için bkz. [Bit hızı merdivenini otomatik oluşturma](media-services-autogen-bitrate-ladder-with-mes.md) ve [Media Encoder Standard için görev ön ayarları](media-services-mes-presets-overview.md). Girdi videonuz için en iyi sonucu verecek hazır ayarı seçmeniz önemlidir. Örneğin, girdi videonuzun 1920 &#215; 1080 piksel çözünürlüğü olduğunu biliyorsanız, **H264 Çoklu Bit hızı 1080p** ön ayarını kullanabilirsiniz. Düşük çözünürlüklü bir videonuz varsa (640 &#215; 360), **H264 Çoklu Bit Hızı 1080p** ön ayarını kullanmamalısınız.
   
   Kaynaklarınızın yönetmenize yardımcı olmak için çıktı varlığının ve işin adını düzenleyebilirsiniz.
   
   ![Varlıkları kodlama](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. **Oluştur**’u seçin.

### <a name="monitor-encoding-job-progress"></a>Kodlama işi ilerleme durumunu izleme
Kodlama işinin ilerleme durumunu izlemek için, sayfanın üst kısmından **Ayarlar**’ı ve ardından **İşler**’i seçin.

![İşler](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>İçerik yayımlama
Kullanıcınıza içeriğinizin akışını sağlamak veya indirmek için kullanılabilecek bir URL sağlamak üzere, önce bulucu oluşturarak varlığınızı “yayımlamanız” gerekir. Bulucular varlıkta bulunan dosyalara erişim imkanı sağlar. Azure Media Services iki tür bulucuyu destekler: 

* **Akış (OnDemandOrigin) bulucuları**. Akış bulucuları, uyarlamalı akış için kullanılır. Uyarlamalı akış örnekleri HLS, Kesintisiz Akış ve MPEG-DASH’i içerir. Akış bulucusu oluşturmak için varlığınız bir .ism dosyası içermelidir. 
* **Aşamalı (paylaşılan erişim imzası) bulucuları**. Aşamalı bulucular, videoları aşamalı indirme aracılığıyla sunmak için kullanılır.

HLS akış URL’si oluşturmak için, URL’ye *(format=m3u8-aapl)* ekleyin.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Kesintisiz Akış varlıklarını oynatacak akış URL’sini oluşturmak için aşağıdaki URL biçimini kullanın:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest

MPEG DASH akış URL’si oluşturmak için, URL’ye *(format=mpd-time-csf)* ekleyin.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Paylaşılan erişim imzası URL'si aşağıdaki biçime sahiptir:

    {blob container name}/{asset name}/{file name}/{shared access signature}

> [!NOTE]
> Azure portalında Mart 2015 öncesinde oluşturulmuş olan bulucuların iki yıllık sona erme tarihi vardır.  
> 
> 

Bulucunun sona erme tarihini güncelleştirmek için bir [REST API](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) veya [.NET API](https://go.microsoft.com/fwlink/?LinkID=533259) kullanabilirsiniz. 

> [!NOTE]
> Paylaşılan erişim imzası bulucunun sona erme tarihini güncelleştirdiğinizde URL değişir.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Bir varlık yayımlamak için portal kullanmak üzere
1. [Azure portalında](https://portal.azure.com/) Azure Media Services hesabınızı seçin.
2. **Ayarlar** > **Varlıklar**’ı seçin. Yayımlamak istediğiniz varlığı seçin.
3. **Yayımla** düğmesini seçin.
4. Bulucu türünü seçin.
5. **Add (Ekle)** seçeneğini belirleyin.
   
    ![Videoyu yayımlama](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL **Yayımlanan URL’ler** listesine eklenir.

## <a name="play-content-from-the-portal"></a>Portaldan içerik oynatma
Videonuzu Azure portalında bir içerik oynatıcıda test edebilirsiniz.

Videoyu seçin ve ardından **Oynat** düğmesini seçin.

![Videoyu Azure portalında oynatma](./media/media-services-portal-vod-get-started/media-services-play.png)

Bazı dikkate alınması gereken noktalar vardır:

* Akışı başlatmak için varsayılan akış uç noktasını çalıştırmaya başlayın
* Videonun yayımlandığından emin olun.
* Azure portal medya oynatıcı varsayılan akış uç noktasından oynatılır. Varsayılan olmayan bir akış uç noktasından oynatmak istiyorsanız, URL'yi seçip kopyalayın ve başka bir oynatıcıya yapıştırın. Örneğin [Azure Media Player](https://aka.ms/azuremediaplayer) üzerinde videonuzu test edebilirsiniz.

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
