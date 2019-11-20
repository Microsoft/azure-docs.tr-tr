---
title: Özel bir görünüm ara-Bing Özel Arama
titleSuffix: Azure Cognitive Services
description: Özel arama deneyiminizi yapılandırdıktan sonra, Bing Özel Arama portalı içinden test edebilirsiniz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: maheshb
ms.openlocfilehash: f04bfc306a89c7bb6caf3f928f5fdfec66358649
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072736"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Portaldan Bing Özel Arama örneğinizi çağırın

Özel arama deneyiminizi yapılandırdıktan sonra, Bing Özel Arama [portalı](https://customsearch.ai)içinden test edebilirsiniz. 

![Bing özel arama portalının ekran görüntüsü](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Arama sorgusu oluşturma 

Bing Özel Arama [portalında](https://customsearch.ai)oturum açtıktan sonra, arama örneğinizi seçin ve **Üretim** sekmesine tıklayın. **Uç noktalar**altında bir API uç noktası (örneğin, Web API) seçin. Aboneliğiniz hangi uç noktaların gösterileceğini belirler.

Bir arama sorgusu oluşturmak için, uç noktanızın parametre değerlerini girin. Portalda görüntülenen parametrelerin seçtiğiniz uç noktaya göre değişebileceğini unutmayın. Daha fazla bilgi için [özel arama API'si başvurusuna](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) bakın. Arama örneğinizin kullandığı aboneliği değiştirmek için uygun abonelik anahtarını ekleyin ve uygun pazar ve/veya dil parametrelerini güncelleştirin.

Bazı önemli parametreler aşağıdadır:


|Parametre  |Açıklama  |
|---------|---------|
|Sorgu     | Aranacak arama terimi. Yalnızca Web, resim, video ve otomatik öneri uç noktaları için kullanılabilir |
|Özel yapılandırma KIMLIĞI | Seçilen özel arama örneğinin yapılandırma KIMLIĞI. Bu alan salt okunurdur. |
|Market     | Sonuçların kaynağı olarak verilecek Pazar. Yalnızca Web, görüntü, video ve barındırılan Kullanıcı arabirimi uç noktaları için kullanılabilir.        |
|Abonelik anahtarı | Sınanacak abonelik anahtarı. Açılan listeden bir anahtar seçebilir veya bir el ile girebilirsiniz.          |

**Ek parametrelere** tıkladığınızda aşağıdaki parametreler görünür:  

|Parametre  |Açıklama  |
|---------|---------|
|Safe Search     | Yetişkinlere yönelik içerik için Web sayfalarını filtrelemek üzere kullanılan filtre. Yalnızca Web, görüntü, video ve barındırılan Kullanıcı arabirimi uç noktaları için kullanılabilir.        |
|Kullanıcı arabirimi dili    | Kullanıcı arabirimi dizeleri için kullanılan dil. Örneğin, barındırılan Kullanıcı arabiriminde görüntüleri ve videoları etkinleştirirseniz, **görüntü** ve **video** sekmeleri belirtilen dili kullanır.        |
|Sayı     | Yanıtta döndürülecek arama sonuçlarının sayısı. Yalnızca Web, görüntü ve video uç noktaları için kullanılabilir.         |
|Uzaklık    | Sonuçları döndürmeden önce atlanacak arama sonuçlarının sayısı. Yalnızca Web, görüntü ve video uç noktaları için kullanılabilir.        |
    
Tüm gerekli seçenekleri belirledikten sonra, sağ bölmedeki JSON yanıtını görüntülemek için **çağır** ' a tıklayın. Barındırılan UI uç noktasını seçerseniz, arama deneyimini alt bölmede test edebilirsiniz.

## <a name="change-your-bing-custom-search-subscription"></a>Bing Özel Arama aboneliğinizi değiştirme

Bing Özel Arama örneğiniz ile ilişkili aboneliği yeni bir örnek oluşturmadan değiştirebilirsiniz. API çağrılarının gönderilmesini ve yeni bir aboneliğe ücretlendirildiği için, Azure portal yeni bir Bing Özel Arama kaynağı oluşturun. API isteklerinizin yeni abonelik anahtarını, örneğinizin özel yapılandırma KIMLIĞIYLE birlikte kullanın.

## <a name="next-steps"></a>Sonraki adımlar

- [Özel görünümünüzü şu şekilde çağırınC#](./call-endpoint-csharp.md)
- [Java ile özel görünümünüzü çağırma](./call-endpoint-java.md)
- [NodeJs ile özel görünümünüzü çağırma](./call-endpoint-nodejs.md)
- [Python ile özel görünümünüzü çağırma](./call-endpoint-python.md)

- [C# SDK ile özel görünümünüzü çağırma](./sdk-csharp-quick-start.md)
