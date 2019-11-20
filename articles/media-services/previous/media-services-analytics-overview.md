---
title: Media Services platformunda Media Analytics | Microsoft Docs
description: Media Analytics genel önizlemesine genel bakış, kurumsal ölçekte konuşma ve görüntü işleme hizmetleri koleksiyonu, uyumluluk, güvenlik ve küresel erişim
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.reviewer: milanga; johndeu
ms.openlocfilehash: 66edbc872fe5f1d4320107aa21ed2792bdd4fecc
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083881"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Services platformunda Media Analytics 

## <a name="retirement-plans"></a>Emeklilik planları

> [!IMPORTANT]
> Aşağıdaki eski medya işlemcileri 2020 ' de kullanımdan kaldırılacak, aşağıdaki tablodaki ayrıntılara bakın. 

|Medya işlemci adı|Emeklilik tarihi|Ek notlar|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1 Ocak 2020|Bu medya işlemcisi [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/)ile değiştirilmeyecektir. Daha fazla bilgi için, bkz. [Azure Media Indexer 2 ' den Azure Media Services video Indexer geçiş](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 Ekim 2020|Bu medya işlemcisi [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/)ile değiştirilmeyecektir. Daha fazla bilgi için bkz. [Azure Media Indexer 'den Azure Media Services 'ye geçirme video Indexer](migrate-indexer-v1-v2.md)
 
## <a name="overview"></a>Genel Bakış

Daha fazla kuruluş, çalışanları eğitme, müşterilerine katılım ve iş işlevlerini belgelemek için tercih edilen ortam olarak videoyu kullanıyor. Bulut bilgi işlem, bu büyük medya dosyalarını depolamak, akışa almak ve bunlara erişmek için bir yol sağlar. Ancak, bir şirketin video içeriği kitaplığı büyüdükçe içerikten içgörüler elde etmek için eşit derecede etkili bir yöntem gerekir. 

Bu büyüyen gereksinimi karşılamak için Azure Media Services Azure Media Analytics sunar. Medya Analizi, kuruluş ve işletmelerin video dosyalarından eyleme dönüştürülebilir öngörüler türetmesini kolaylaştıran bir grup konuşma ve görme bileşenidir. Çekirdek Media Services platform bileşenleri kullanılarak oluşturulan Media Analytics, medya işlemeyi günde bir ölçekte işleyebilir.

Media Analytics, geliştiriciler gelişmiş video işlevlerini hızla uygulamalara getirebilir. Büyük kuruluşlar için gereken tam ölçek, uyumluluk, güvenlik ve küresel erişim sunan kurumsal ortamlara sahiptir.

Aşağıdaki diyagramda Media Services platformunun Media Analytics ve diğer ana kısımları gösterilmektedir. 

![VoD iş akışı](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Medya Analizi medya işlemcileri MP4 veya JSON dosyaları üretir. Medya işlemcisi bir MP4 dosyası üretirse dosyayı aşamalı olarak indirebilirsiniz. Bir medya işlemcisi bir JSON dosyası üretirse, dosyayı Azure Blob depolamadan indirebilirsiniz. 

## <a name="media-analytics-services"></a>Media Analytics hizmetleri

### <a name="indexer"></a>Dizinleyici
Azure Media Indexer, içeriği aranabilir hale getirebilirsiniz ve kapalı açıklamalı altyazı parçaları oluşturabilirsiniz. Önceki sürümle karşılaştırıldığında, Azure Media Indexer 2 önizlemesinde daha hızlı Dizin oluşturma ve daha geniş dil desteği vardır. Desteklenen diller arasında Ingilizce, Ispanyolca, Fransızca, Almanca, Italyanca, Çince, Portekizce ve Arapça bulunur. Ayrıntılı bilgi ve örnekler için bkz. [Azure Media Indexer 2 Ile işlem videoları](media-services-process-content-with-indexer2.md).
### <a name="motion-detector"></a>Hareket Algılayıcısı
Hareket algılayıcısının, sabit arka planların bulunduğu bir videodaki hareketi algılamak için kullanabilirsiniz. Bu, gözetim kameraları tarafından algılanan hareket olaylarında hatalı pozitif sonuçlar olup olmadığını denetlemeyi mümkün kılar. Ayrıntılı bilgi ve örnekler için bkz. [Azure Media Analytics Için hareket algılama](media-services-motion-detection.md).
### <a name="face-detector"></a>Yüz Algılayıcısı
Yüz algılayıcısı 'nı kullanarak, insanlardan ve bu kişilerin yüzlerinin yanı sıra, mutluluklar ve sürpriz dahil olmak üzere kişilerin yüzlerini ve bunların özelliklerini algılayabilirsiniz Bu, daha sonra bir olaya katılan kişilerin yeniden eylemlerini toplama ve çözümleme dahil olmak üzere, daha sonra açıklanan birkaç faydalı sektör uygulamasına sahiptir. Ayrıntılı bilgi ve örnekler için bkz. [Azure Media Analytics Için yüz ve duygu algılama](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Video özetleme
Video özetleme, kaynak videodan ilgi çekici parçacıkları otomatik olarak seçerek uzun videoların özetlerini oluşturmanıza yardımcı olabilir. Bu özellik, uzun bir videoda beklendiklere ilişkin hızlı bir genel bakış sağlamak istediğinizde yararlı olur. Ayrıntılı bilgi ve örnekler için bkz. [video özeti oluşturmak için Azure Media video Thumbnails kullanma](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optik karakter tanıma
Azure Media OCR (optik karakter tanıma) ile video dosyalarındaki metin içeriğini düzenlenebilir ve aranabilir dijital metinlere dönüştürebilirsiniz. Daha sonra, medyanızın video sinyalinden anlamlı meta verilerin ayıklanmasını otomatik hale getirebilirsiniz.
### <a name="scalable-face-redaction"></a>Ölçeklenebilir yüz redaksiyon
Azure Media Redactor, bulutta ölçeklenebilir yüz redaksiyon sağlayan bir Media Analytics medya işlemcisidir. Yüz redaksiyon kullanarak, seçili kişilerin yüzlerini bulanıklaştırmak için videonuzu değiştirebilirsiniz. Haber ortamında yüz redaksiyon hizmetini veya genel güvenlik dahil edildiğinde kullanmak isteyebilirsiniz. Birden çok yüz içeren birkaç dakikalık bir çekimi, el ile redaksiyonda zaman alabilir, ancak bu hizmette, yüz redaksiyon yalnızca birkaç basit adım sürer. Daha fazla bilgi için, [Azure Media Analytics ile yüzeyleri redaksiyona](media-services-face-redaction.md) bakın.
### <a name="content-moderation"></a>İçerik Denetleme
Azure Content Moderator, videolarınız için makine destekli denetleme kullanmanıza olanak sağlar. Örneğin videolardaki yetişkinlere yönelik veya müstehcen içerikleri tespit edip belirlenen içeriklerin moderasyon ekibiniz tarafından gözden geçirilmesini isteyebilirsiniz. İstenmeyen içerik için videoları el ile moderalıme, zaman alan ve pahalı bir görevdir. Bu hizmet ve ilişkili gözden geçirme araçlarıyla, en iyi sonuçlar için verimli ve uygun maliyetli bir şekilde, makine destekli denetleme özelliğini her ne kadar verimli bir şekilde birleştirmelisiniz. Daha fazla bilgi edinmek için bkz. [Azure Content moderator videolarınızı işleme](media-services-content-moderation.md) makalesi.

## <a name="common-scenarios"></a>Genel senaryolar
Media Analytics, kuruluşların ve kuruluşların videodan yeni Öngörüler iletmelerine ve büyük hacimlerde video içeriğini daha verimli bir şekilde yönetmesine yardımcı olabilir. İşte birkaç senaryo:

* **Çağrı merkezleri**. Sosyal medya kasayına karşın müşteri çağrı merkezleri, müşteri hizmetleri işlemlerinin büyük bir yüzdesini de kolaylaştırmaya devam eder. Bu ses verilerinde kodlanan, daha yüksek müşteri memnuniyetini sağlamak üzere çözümlenebilecek büyük miktarda müşteri bilgisi olacaktır. Media Indexer kullanarak kuruluşlar metin ayıklayabilir ve arama dizinlerini ve panoları oluşturabilir. Daha sonra, yaygın şikayetler, şikayet kaynakları ve diğer ilgili veriler hakkında zeka bilgileri ayıklayabilir.
* **Kullanıcı tarafından oluşturulan içerik denetleme**. Haber medyasından, polie departmanlarına kadar birçok kuruluşun video ve görüntü gibi kullanıcı tarafından oluşturulan medyayı kabul eden, herkese açık portalları vardır. İçerik hacmi, beklenmeyen olaylar nedeniyle ani olabilir. Bu senaryolarda, uygun şekilde, uygun bir şekilde içeriğe yönelik olarak el ile gözden geçirmeler yapmak zordur. Müşteriler, uygun içeriğe odaklanmak için içerik denetleme hizmetine güvenebilirler.

## <a name="media-analytics-media-processors"></a>Media Analytics medya işlemcileri
Bu bölümde, Media Analytics medya işlemcileri listelenir ve medya işlemcisi (MP) nesnesini almak için .NET veya REST 'in nasıl kullanılacağı gösterilir.

### <a name="mp-names"></a>MP adları
* Azure Media Indexer 2 Preview
* Azure Media Indexer
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media Content Moderator

### <a name="net"></a>.NET
Aşağıdaki işlev, belirtilen MP adlarından birini alır ve bir MP nesnesi döndürür.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


### <a name="rest"></a>REST
İstek:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net

Yanıt:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>Demolar
Bkz. [Azure Media Analytics gösterileri](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>İlgili makaleler:
Bkz. [Media Services Analytics duyurusu](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Sonraki adımlar
Media Services öğrenme yollarını gözden geçirin.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
