---
title: API konsolu ile orta boyutlu görüntüler-Content Moderator
titleSuffix: Azure Cognitive Services
description: Tarama ve görüntü içeriği için denetim iş akışlarını gözden geçirme işlemini başlatmak için Azure Content Moderator görüntü denetleme API 'sini kullanın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: ce8ea76cfc1f3f30418b66cd21de43d244da6764
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043971"
---
# <a name="moderate-images-from-the-api-console"></a>API konsolundan orta görüntüler

Tarama ve görüntü içeriği için denetim iş akışlarını gözden geçirme işlemini başlatmak için Azure Content Moderator [görüntü denetleme API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) 'sini kullanın. Denetleme işi içeriğinizi küfür için tarar ve özel ve paylaşılan kara listelerle karşılaştırır.

## <a name="use-the-api-console"></a>API konsolunu kullanma
Çevrimiçi konsolda API 'YI test etmeden önce, abonelik anahtarınız olmalıdır. Bu, **Ayarlar** sekmesinde, **OCP-apim-Subscription-Key** kutusunda bulunur. Daha fazla bilgi için bkz. [Genel Bakış](overview.md).

1. [Görüntü denetleme API 'si başvurusuna](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)gidin.

   Görüntü denetimi görüntü denetlemeyi **değerlendir** sayfası açılır.

2. **Open API test konsolu**için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin. 

   ![Görüntü dene-sayfa bölgesi seçimini değerlendir](images/test-drive-region.png)
  
   **Görüntü değerlendirmesi** API konsolu açılır.

3. **OCP-apim-Subscription-Key** kutusuna abonelik anahtarınızı girin.

   ![Görüntüyü dene-konsol abonelik anahtarını değerlendir](images/try-image-api-1.PNG)

4. **İstek gövdesi** kutusunda varsayılan örnek görüntüyü kullanın veya taranacak bir görüntü belirtin. Görüntünün kendisini ikili bit veri olarak gönderebilir veya bir görüntü için genel olarak erişilebilen bir URL belirtebilirsiniz. 

   Bu örnekte, **İstek gövdesi** kutusunda belirtilen yolu kullanın ve ardından **Gönder**' i seçin. 

   ![Görüntüyü dene-konsol Isteği gövdesini değerlendir](images/try-image-api-2.PNG)

   Bu, URL 'deki görüntüdür:

   ![Görüntüyü dene-konsol örnek görüntüsünü değerlendir](images/sample-image.jpg) 

5. **Gönder**’i seçin.

6. API, her sınıflandırma için bir olasılık puanı döndürür. Ayrıca, resmin koşulları karşılayıp karşılamadığını (**true** veya **false**) de döndürür. 

   ![Görüntüyü deneyin-konsol olasılık Puanını ve koşul belirlemeyi değerlendirin](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Yüz algılama

Görüntüdeki yüzeyleri bulmak için görüntü denetleme API 'sini kullanabilirsiniz. Bu seçenek, gizlilik sorunları varsa ve belirli bir yüzün platformunuza gönderilmesini engellemek istediğinizde yararlı olabilir. 

1. [Görüntü denetleme API 'si başvurusunda](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), sol taraftaki menüdeki **görüntü**altında bulunan **yüzeyleri bul**' u seçin. 

   **Görüntü-yüzeyleri bul** sayfası açılır.

2. **Open API test konsolu**için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin. 

   ![Görüntüyü dene-yüzeyleri bul sayfa bölge seçimi](images/test-drive-region.png)

   **Görüntü-yüzeyleri bulma** API 'si konsolu açılır.

3. Taranacak bir görüntü belirtin. Görüntünün kendisini ikili bit veri olarak gönderebilir veya bir görüntüye genel olarak erişilebilen bir URL belirtebilirsiniz. Bu örnek, bir CNN öyküünde kullanılan bir görüntüye bağlantı sağlar.

   ![Görüntü dene-yüz örnek görüntüsü bulma](images/try-image-api-face-image.jpg)

   ![Görüntü dene-yüz örnek isteği bulma](images/try-image-api-face-request.png)

4. **Gönder**’i seçin. Bu örnekte, API iki yüz bulur ve görüntüdeki koordinatlarını döndürür.

   ![Görüntü deneme-yüzeyleri bulma örnek yanıt içeriği kutusu](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>OCR özelliği aracılığıyla metin algılama

Görüntülerdeki metni algılamak için Content Moderator OCR özelliğini kullanabilirsiniz.

1. [Görüntü denetleme API 'si başvurusunda](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), sol taraftaki menüdeki **görüntü**altında **OCR**' yi seçin. 

   **Görüntü-OCR** sayfası açılır.

2. **Open API test konsolu**için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin. 

   ![Görüntü-OCR sayfa bölgesi seçimi](images/test-drive-region.png)

   **Görüntü-OCR** API konsolu açılır.

3. **OCP-apim-Subscription-Key** kutusuna abonelik anahtarınızı girin.

4. **İstek gövdesi** kutusunda varsayılan örnek görüntüyü kullanın. Bu, önceki bölümde kullanılan görüntüdür.

5. **Gönder**’i seçin. Ayıklanan metin JSON içinde görüntülenir:

   ![Image-OCR örnek yanıtı içerik kutusu](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Sonraki adımlar

Kodunuzda REST API kullanın veya uygulamanıza görüntü denetlemesi eklemek için [.NET SDK hızlı](dotnet-sdk-quickstart.md) başlangıcı ' nı izleyin.
