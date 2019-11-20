---
title: Sık sorulan sorular (SSS)-Bing Resim Arama API'si
titleSuffix: Azure Cognitive Services
description: Bing Resim Arama API'si ilgili kavramlar, kod ve senaryolar hakkında sık sorulan soruların yanıtlarını bulun.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6841e573446103466e2719797da9e4161b70b5a6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881693"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Bing Resim Arama API'si hakkında sık sorulan sorular (SSS)

Azure 'da Microsoft bilişsel hizmetler için Bing Resim Arama API'si ilgili kavramlar, kod ve senaryolar hakkında sık sorulan soruların yanıtlarını bulun.

## <a name="response-headers-in-javascript"></a>JavaScript 'teki yanıt üstbilgileri

Bing Resim Arama API'si yanıtlarında aşağıdaki üstbilgiler meydana gelebilir.

| `Attribute`         | `Description` |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |Bing 'in kullanıcıya atadığı benzersiz KIMLIK |
| `BingAPIs-Market`   |İsteği yerine getirmek için kullanılan Pazar |
| `BingAPIs-TraceId`  |Bu istek için Bing API sunucusundaki günlük girdisi (destek için) |

İstemci KIMLIĞINI kalıcı hale getirmek ve sonraki isteklerle döndürmek özellikle önemlidir. Bunu yaptığınızda arama, arama sonuçlarında geçmiş bağlam kullanır ve ayrıca tutarlı bir kullanıcı deneyimi sağlar.

Ancak, JavaScript 'ten Bing Resim Arama API'si çağırdığınızda, tarayıcınızın yerleşik güvenlik özellikleri (CORS) bu üstbilgilerin değerlerine erişmenizi engelleyebilir.

Üstbilgilere erişim kazanmak için bir CORS proxy 'si aracılığıyla Bing Resim Arama API'si isteği yapabilirsiniz. Böyle bir ara sunucudan gelen yanıtta, yanıt üst bilgilerini beyaz listeye alan ve JavaScript’in kullanımına sunan `Access-Control-Expose-Headers` üst bilgisi bulunur.

[Öğretici uygulamamız](tutorial-bing-image-search-single-page-app.md) , isteğe bağlı istemci üst bilgilerine erişmesine izin vermek IÇIN bir CORS proxy yüklemek kolaydır. İlk olarak, henüz yüklemediyseniz [Node.js'yi yükleyin](https://nodejs.org/en/download/). Sonra bir komut isteminde aşağıdaki komutu girin.

    npm install -g cors-proxy-server

Sonra, HTML dosyasındaki Bing Resim Arama API'si uç noktasını şu şekilde değiştirin:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Son olarak, aşağıdaki komutla CORS ara sunucusunu başlatın:

    cors-proxy-server

Öğretici uygulamasını kullanırken komut penceresini açık bırakın; pencere kapatılırsa ara sunucu durdurulur. Arama sonuçlarının altındaki genişletilebilir HTTP Üst Bilgileri bölümünde artık `X-MSEdge-ClientID` üst bilgisini (diğerleriyle birlikte) görebilir ve bunun her istekte aynı olduğunu doğrulayabilirsiniz.

## <a name="response-headers-in-production"></a>Üretimdeki yanıt üst bilgileri

Önceki cevap bölümünde açıklanan CORS proxy yaklaşımı geliştirme, test ve öğrenme için uygundur.

Ancak, bir üretim ortamında, Bing Web Araması API'si kullanan Web sayfasıyla aynı etki alanında bir sunucu tarafı betiği barındırmalısınız. Bu betik aslında web sayfası JavaScript 'ten istek üzerine API çağrıları yapması ve üst bilgiler dahil olmak üzere tüm sonuçları istemciye geri iletmektir. İki kaynak (sayfa ve komut dosyası) bir kaynağı paylaştığından, CORS oynatılmaz ve özel üst bilgilere Web sayfasındaki JavaScript 'in erişimine açık hale gelir.

Bu yaklaşım ayrıca API anahtarınızı herkese açık hale karşı korur, çünkü yalnızca sunucu tarafı betiği için gereklidir. Komut dosyası, isteğin yetkilendirildiğinden emin olmak için başka bir Yöntem (HTTP başvuran gibi) kullanabilir.

## <a name="next-steps"></a>Sonraki adımlar

Sorunuz, eksik bir özellik veya işlevlerle ilgili mi? [Kullanıcı sesli web sitemiz](https://cognitive.uservoice.com/forums/555907-bing-search)üzerinde bunu ister veya oylayacağınızı düşünün.

## <a name="see-also"></a>Ayrıca bkz.

 [Stack Overflow: Bilişsel hizmetler](https://stackoverflow.com/questions/tagged/bing-api)
