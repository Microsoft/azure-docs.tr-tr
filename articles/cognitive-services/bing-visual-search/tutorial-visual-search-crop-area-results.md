---
title: 'Öğretici: Bing Görsel Arama SDK ile görüntü kırpma'
description: Bir görüntüdeki belirli uygulamalardan Öngörüler almak için Bing Görsel Arama SDK 'sını kullanın.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: rosh
ms.openlocfilehash: a6b625325e2adfe441d8abd2012f100780b659b4
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910023"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Öğretici: İçin Bing Görsel Arama SDK ile görüntü kırpmaC#

Bing Görsel Arama SDK, benzer çevrimiçi görüntüleri bulmadan önce bir görüntüyü kırpmanızı sağlar. Bu uygulama, birden fazla kişi içeren bir görüntüden tek bir kişiyi kırpar ve sonra çevrimiçi bulunan benzer görüntüleri içeren arama sonuçları döndürür.

Bu uygulamanın tam kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs)'da ek hata işleme ve ek açıklama ile kullanılabilir.

Bu öğreticide nasıl yapılacağı gösterilmektedir:

> [!div class="checklist"]
> * Bing Görsel Arama SDK kullanarak istek gönderme
> * Bing Görsel Arama ile aramak için bir görüntünün alanını kırpın
> * Yanıtı al ve işle
> * Yanıtta eylem öğelerinin URL 'Lerini bulun

## <a name="prerequisites"></a>Önkoşullar

* Herhangi bir [Visual Studio 2019](https://www.visualstudio.com/downloads/)sürümü.
* Linux/MacOS kullanıyorsanız bu uygulama, [Mono](https://www.mono-project.com/) kullanılarak çalıştırılabilir.
* [NuGet Özel Arama](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) paketinin yüklenmiş olması.
    - Visual Studio 'daki Çözüm Gezgini projenize sağ tıklayın ve menüden **NuGet Paketlerini Yönet** ' i seçin. `Microsoft.Azure.CognitiveServices.Search.CustomSearch` paketini yükleyin. NuGet Özel Arama paketini yüklediğinizde aşağıdaki derlemeler de yüklenir:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Görüntü kırpma alanını belirtin

Bu uygulama, Microsoft üst düzey liderliği ekibinin bu görüntüsünün bir alanını kırpar. Bu kırpma alanı, tüm görüntünün yüzdesi olarak temsil edilen sol üst ve sağ alt koordinatları kullanılarak tanımlanır:  

![Microsoft Üst Düzey Liderlik Ekibi](./media/MS_SrLeaders.jpg)

Bu görüntü, kırpma alanından bir `ImageInfo` nesne oluşturularak ve `ImageInfo` nesnesi bir `VisualSearchRequest`öğesine yüklenirken kırpılır. `ImageInfo` Nesne Ayrıca görüntünün URL 'sini de içerir:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Kırpma alanına benzer görüntüleri ara

Değişkeni `VisualSearchRequest` , görüntünün kırpma alanı ve URL 'si hakkındaki bilgileri içerir. `VisualSearchMethodAsync()` Yöntemi sonuçları alır:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>URL verilerini al`ImageModuleAction`

Bing Görsel Arama sonuçları `ImageTag` nesneler. Her etiket bir `ImageAction` nesneleri listesi içerir. Her `ImageAction` biri, `Data` eylem türüne bağlı değerlerin listesi olan bir alan içerir.

Çeşitli türleri aşağıdaki kodla yazdırabilirsiniz:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

Tam uygulama şunları döndürür:

|EylemTürü  |URL  | |
|---------|---------|---------|
|Websearchurl dahil Pages     |         |
|Mosearchurl 'Yi yeniden boyutlandırır     |         |  
|VisualSearch WebSearchURL 'Si    |         |
|Imagebyıd WebSearchURL     |         |  
|Relatedaramaweb Searchurl     |         |
|Entity-> WebSearchUrl     | https\://www.Bing.com/CR?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&RD=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3A%2F%2Fwww.Bing.com%2fsearch%3Fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults-> WebSearchUrl    |  https\://www.Bing.com/CR?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&RD=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3A%2F%2Fwww.Bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|Imageresults-> WebSearchUrl    |  https\://www.Bing.com/CR?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&RD=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3A%2F%2Fwww.Bing.com%2fimages%2fsearch%3Fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Yukarıda gösterildiği gibi, `Entity` ActionType tanınabilir bir kişi, yer veya bir şey hakkında bilgi döndüren bir Bing arama sorgusu içerir. `TopicResults` ve `ImageResults` türleri, ilgili görüntülerin sorgularını içerir. Listedeki URL’ler, Bing arama sonuçlarına yönlendirir.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Görüntüler için `PagesIncluding` `ActionType` URL 'ler al

Gerçek görüntü URL’lerini almak için, bir `ActionType` türünü `ImageModuleAction` olarak okuyan bir tür dönüştürme gerekir. Bu tür ise değerler listesine sahip bir `Data` öğesi içerir. Her değer, bir görüntünün URL’sidir. Aşağıdaki `PagesIncluding` eylem türünü öğesine `ImageModuleAction` yayınlar ve değerleri okur:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Görsel Arama tek sayfalı Web uygulaması oluşturma](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Ayrıca bkz.
> [Bing Görsel Arama API'si nedir?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
