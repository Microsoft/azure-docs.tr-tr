---
title: 'Hızlı Başlangıç: İlk Bing Özel Arama örneği oluştur | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Tanımladığınız etki alanlarını ve Web sayfalarını araybarındırabileceğiniz özel bir Bing örneği oluşturmak için bu makaleyi kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 2806ca6f7079ffac3d2222363cd5b3839ef8f97d
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405025"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Hızlı Başlangıç: İlk Bing Özel Arama örneğinizi oluşturma

Bing Özel Arama hizmetini kullanmak için görünümünüzü veya web dilimini tanımlayan özel bir arama örneği oluşturmanız gerekir. Bu örnek, arama yapmak istediğiniz genel etki alanlarını, Web sitelerini ve Web sayfalarını, istediğiniz sıralama ayarlamalarıyla birlikte içerir. 

Örneği oluşturmak için [Bing özel arama portalını](https://customsearch.ai)kullanın. 

![Bing Özel Arama portalının resmi](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Özel arama örneği oluşturma

Bing Özel Arama örneği oluşturmak için:

1. [Bing özel arama Portal](https://customsearch.ai) Web sayfasında **kullanmaya başlayın** ' a tıklayın ve Microsoft hesabı oturum açın.

2. **Yeni örnek**' e tıklayın ve açıklayıcı bir ad girin. Örneğinizin adını dilediğiniz zaman değiştirebilirsiniz.
 
3. **Arama Deneyimi** bölümündeki **Etkin** sekmesine aramak istediğiniz bir veya daha fazla web sitesinin URL'sini girin. 

    > [!NOTE]
    > Bing Özel Arama örnekleri yalnızca etki alanları ve genel olan ve Bing tarafından dizinlenen Web sayfaları için sonuçlar döndürür.

4. Bing Özel Arama portalının sağ tarafını bir sorgu girmek ve arama örneğiniz tarafından döndürülen arama sonuçlarını incelemek için kullanabilirsiniz. Sonuç döndürülmezse, farklı bir URL girmeyi deneyin.  

5. Değişiklikleri üretim ortamında yayımlamak ve örneğin uç noktalarını güncelleştirmek için **Yayımla** ' ya tıklayın.

6.  **Uç noktalar**altındaki **Üretim** sekmesine tıklayın ve **özel yapılandırma kimliğinizi**kopyalayın. Bu kimliğe, çağrılarınızın `customconfig=` sorgu parametresine ekleyerek özel arama API'si çağrısı yapmanız gerekir.


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Hızlı Başlangıç: Bing Özel Arama uç noktanızı çağırın](./call-endpoint-csharp.md)
