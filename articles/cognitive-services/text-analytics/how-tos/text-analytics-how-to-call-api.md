---
title: Metin Analizi API’sini çağırma
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure bilişsel hizmetler REST API ve Postman Metin Analizi nasıl çağrlayabileceğiniz açıklanır.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 5697ae4c8864e0b9c4cbfc9e1e1048e1c3d60f77
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837207"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Metin Analizi nasıl çağrılacağını REST API

**Metin Analizi API'si** çağrıları, herhangi bir dilde formülleştirmek IÇIN http post/Get çağrılardır. Bu makalede, önemli kavramları göstermek için REST ve [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) kullanırız.

Her isteğin erişim anahtarınızı ve bir HTTP uç noktasını içermesi gerekir. Uç nokta, kaydolma sırasında seçtiğiniz bölgeyi, hizmet URL 'sini ve istekte kullanılan bir kaynağı belirtir: `sentiment`, `keyphrases`, `languages`ve `entities`. 

Yönetilecek veri varlığı olmadığından Metin Analizi durum bilgisiz olduğunu hatırlayın. Metniniz karşıya yüklenir, teslim edildiğinde çözümlenir ve sonuçlar çağıran uygulamaya hemen döndürülür.

> [!Tip]
> API 'nin nasıl çalıştığını görmek için tek kapalı çağrılar için, herhangi bir [API belgesi sayfasında](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)bulunan yerleşik **API test konsolundan**post istekleri gönderebilirsiniz. Bir kurulum yoktur ve tek gereksinimler, isteğe bir erişim anahtarı ve JSON belgelerini buraya yapıştırmaktır. 

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>JSON şema tanımı

Giriş, ham yapılandırılmamış metinde JSON olmalıdır. XML desteklenmiyor. Şema basittir ve aşağıdaki listede açıklanan öğelerden oluşur. 

Şu anda tüm Metin Analizi işlemler için aynı belgeleri gönderebilirsiniz: yaklaşım, anahtar tümceciği, dil algılama ve varlık tanımlama. (Şema, gelecekte her analiz için farklılık gösterir.)

| Öğe | Geçerli değerler | Gerekli mi? | Kullanım |
|---------|--------------|-----------|-------|
|`id` |Veri türü dizedir, ancak uygulama belge kimlikleri ' nde tam sayı olarak eğilimlidir. | Gerekli | Sistem çıktıyı yapılandırmak için sağladığınız kimlikleri kullanır. İstekteki her bir KIMLIK için dil kodları, anahtar tümceleri ve yaklaşım puanları oluşturulur.|
|`text` | Yapılandırılmamış ham metin, en fazla 5.120 karakter. | Gerekli | Dil algılama için metin herhangi bir dilde ifade edilebilir. Yaklaşım analizi, anahtar ifade ayıklama ve varlık tanımlama için, metin [desteklenen bir dilde](../text-analytics-supported-languages.md)olmalıdır. |
|`language` | 2 karakterlik [ıso 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) [desteklenen bir dil](../text-analytics-supported-languages.md) için kod | Değişir | Yaklaşım analizi, anahtar ifade ayıklama ve varlık bağlama için gereklidir; dil algılama için isteğe bağlı. Bunu dışladığınızda bir hata yoktur, ancak analiz bu olmadan zayıflatılmalıdır. Dil kodu, sağladığınız `text` karşılık gelmelidir. |

Sınırlamalar hakkında daha fazla bilgi için bkz. [metin analizi genel bakış > veri sınırları](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Postman 'da istek ayarlama

Hizmet, boyutu 1 MB 'a kadar olan isteği kabul eder. Postman (veya başka bir Web API test aracı) kullanıyorsanız, uç noktasını kullanmak istediğiniz kaynağı içerecek şekilde ayarlayın ve erişim anahtarını bir istek üst bilgisine sağlayın. Her işlem için ilgili kaynağı uç noktaya eklemeniz gerekir. 

1. Postman 'da:

   + İstek türü olarak **gönderi** ' ı seçin.
   + Portal sayfasından kopyaladığınız uç noktaya yapıştırın.
   + Kaynak Ekle.

   Kaynak uç noktaları aşağıdaki gibidir (bölgeniz farklılık gösterebilir):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. Üç istek üst bilgilerini ayarlayın:

   + `Ocp-Apim-Subscription-Key`: Azure portal adresinden alınan erişim anahtarınız.
   + `Content-Type`: Application/JSON.
   + `Accept`: Application/JSON.

   İsteğiniz, **/keyPhrases** kaynağını varsayarak aşağıdaki ekran görüntüsüne benzer şekilde görünmelidir.

   ![Uç nokta ve üst bilgilerle ekran görüntüsü iste](../media/postman-request-keyphrase-1.png)

4. **Gövde** ' ye tıklayın ve biçim için **RAW** ' ı seçin.

   ![Gövde ayarları ile ekran görüntüsü iste](../media/postman-request-body-raw.png)

5. Bazı JSON belgelerini amaçlanan analiz için geçerli bir biçimde yapıştırın. Belirli bir analiz hakkında daha fazla bilgi için aşağıdaki konulara bakın:

  + [Dil algılama](text-analytics-how-to-language-detection.md)  
  + [Anahtar tümceciği ayıklama](text-analytics-how-to-keyword-extraction.md)  
  + [Yaklaşım Analizi](text-analytics-how-to-sentiment-analysis.md)  
  + [Varlık tanıma](text-analytics-how-to-entity-linking.md)  


6. İsteği göndermek için **Gönder** ' e tıklayın. Dakika ve saniye başına gönderebilmeniz için istek sayısı hakkında genel [bakış bölümüne bakın](../overview.md#data-limits) .

   Postman 'da, yanıt, istekte belirtilen her belge KIMLIĞI için bir öğe olan bir sonraki pencerede, tek bir JSON belgesi olarak görüntülenir.

## <a name="see-also"></a>Ayrıca bkz. 

 [Metin Analizine Genel Bakış](../overview.md)  
 [Sık sorulan sorular (SSS)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Dili algıla](text-analytics-how-to-language-detection.md)
