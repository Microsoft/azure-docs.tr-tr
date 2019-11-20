---
title: Bing Yerel İşletme Arama API'si nedir?
titleSuffix: Azure Cognitive Services
description: Bing Yerel İşletme Arama API'si, uygulamalarınızın arama sorguları temelinde yerel konumlar ve işletmeler hakkında bilgi bulmasına olanak tanıyan bir RESTful hizmetidir.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: bdada613ef46881bbf051144efded541ac3d0974
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996698"
---
# <a name="what-is-bing-local-business-search"></a>Bing yerel Iş arama nedir?
Bing yerel Iş Arama API 'SI, uygulamalarınızın arama sorguları temelinde yerel işletmeler hakkında bilgi bulmasına olanak tanıyan bir yeniden hizmet hizmetidir. Örneğin, `q=<business-name> in Redmond, Washington`veya `q=Italian restaurants near me`. 

## <a name="features"></a>Özellikler
| Özellik | Açıklama |  
| -- | -- | 
| [Yerel işletmeleri ve konumları bulma](quickstarts/local-quickstart.md) | Bing yerel Iş Arama API 'SI, bir sorgudan yerelleştirilmiş sonuçlar alır. Sonuç olarak, iş Web sitesinin bir URL 'SI ve görüntüleme metni, telefon numarası ve coğrafi konum gibi aşağıdakiler de dahildir: GPS koordinatları, şehir, sokak adresi |  
| [Yerel sonuçları coğrafi sınırlara göre filtrele](specify-geographic-search.md) | Sonuçları, dairesel bir alan veya kare sınırlayıcı kutusuyla belirtilen belirli bir coğrafi alanla sınırlamak için arama parametresi olarak koordinatları ekleyin. | 
| [Yerel iş sonuçlarını kategoriye göre filtrele](local-categories.md) | Kategoriye göre yerel iş sonuçlarını arayın. Bu seçenek, çeşitli iş kategorilerindeki yerelleştirilmiş sonuçları döndürmek için çağıranın ters IP konumunu veya GPS koordinatlarını kullanır.|

## <a name="workflow"></a>İş akışı
HTTP istekleri yapıp JSON yanıtlarını ayrıştırabilen herhangi bir programlama dilinden Bing yerel Iş Arama API 'sini çağırın. Bu hizmete REST API kullanılarak erişilebilir.
 
1. Bing Arama API'leri erişimi olan bilişsel [HIZMETLER API hesabı](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) oluşturun. Azure aboneliğiniz yoksa [ücretsiz hesap](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) oluşturabilirsiniz.   
2. URL, `q=""` sorgu parametresi için arama terimlerinizi kodlayın. Örneğin, `q=nearby+restaurant` veya `q=nearby%20restaurant`. Gerekirse, sayfalandırma da ayarlayın. 
3. [Bing yerel Iş Arama API 'sine istek](quickstarts/local-quickstart.md) gönderme 
4. JSON yanıtını Ayrıştır 

> [!NOTE]
> Şu anda yerel iş arama yalnızca `en-US` pazarı destekler. 
> [!NOTE]
> Şu anda yerel Iş araması otomatik önermeyi desteklemez. 

## <a name="next-steps"></a>Sonraki adımlar
- [Sorgu ve yanıt](local-search-query-response.md)
- [Yerel Iş araması hızlı başlangıç](quickstarts/local-quickstart.md)
- [Yerel Iş Arama API 'SI başvurusu](local-search-reference.md)
- [Kullanım ve görüntüleme gereksinimleri](use-display-requirements.md)
