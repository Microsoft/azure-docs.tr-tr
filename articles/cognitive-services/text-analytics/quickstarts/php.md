---
title: "Hızlı Başlangıç: Metin Analizi API'si çağırmak için PHP kullanma"
titleSuffix: Azure Cognitive Services
description: Azure bilişsel hizmetler 'de Metin Analizi API'si kullanmaya hızlı bir şekilde başlamanıza yardımcı olması için bilgi ve kod örnekleri alın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: a1e3484c544478fa6e9414eea1e139b81652ace3
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135002"
---
# <a name="quickstart-using-php-to-call-the-text-analytics-cognitive-service"></a>Hızlı Başlangıç: Metin Analizi bilişsel hizmeti çağırmak için PHP kullanma
<a name="HOLTop"></a>

Bu makalede, [dil algılama](#Detect), yaklaşım [Çözümleme](#SentimentAnalysis), [anahtar tümceleri ayıklama](#KeyPhraseExtraction)ve PHP ile [metin analizi API 'leri](//go.microsoft.com/fwlink/?LinkID=759711) kullanarak [bağlantılı varlıkları belirleme](#Entities) işlemlerinin nasıl yapılacağı gösterilir.

API'lerle ilgili teknik bilgiler için [API tanımları](//go.microsoft.com/fwlink/?LinkID=759346) sayfasını inceleyin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>Dili algıla

Dil Algılama API'si, [Dili Algıla metodunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) kullanarak bir metin belgesinin dilini algılar.

1. Ortam değişkenlerini `TEXT_ANALYTICS_SUBSCRIPTION_KEY` ve `TEXT_ANALYTICS_ENDPOINT` kaynağınızın Azure uç noktası ve abonelik anahtarı için oluşturun. Uygulamayı düzenleme başladıktan sonra bu ortam değişkenlerini oluşturduysanız, değişkenlere erişmek için kullandığınız düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.
1. Sık kullandığınız IDE ile yeni bir PHP projesi oluşturun.
1. Aşağıda sağlanan kodu ekleyin.
1. Programı çalıştırın.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
if (!getenv($key_var)) {
    throw new Exception ("Please set/export the following environment variable: $key_var");
} else {
    $subscription_key = getenv($key_var);
}
$endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
if (!getenv($endpoint_var)) {
    throw new Exception ("Please set/export the following environment variable: $endpoint_var");
} else {
    $endpoint = getenv($endpoint_var);
}

$path = '/text/analytics/v2.1/languages';

function DetectLanguage ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'text' => 'This is a document written in English.' ),
        array ( 'id' => '2', 'text' => 'Este es un document escrito en Español.' ),
        array ( 'id' => '3', 'text' => '这是一个用中文写的文件')
    )
);

print "Please wait a moment for the results to appear.";

$result = DetectLanguage ($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Dil algılama yanıtı**

Başarılı yanıt, aşağıdaki örnekte gösterildiği gibi JSON biçiminde döndürülür: 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}


```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Yaklaşımı analiz etme

Yaklaşım Analizi API'si, [Yaklaşım metodunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) kullanarak bir metin kaydı kümesinin yaklaşımını algılar. Aşağıdaki örnek, biri İngilizce diğeri İspanyolca olan iki belge puanlar.

1. Ortam değişkenlerini `TEXT_ANALYTICS_SUBSCRIPTION_KEY` ve `TEXT_ANALYTICS_ENDPOINT` kaynağınızın Azure uç noktası ve abonelik anahtarı için oluşturun. Uygulamayı düzenleme başladıktan sonra bu ortam değişkenlerini oluşturduysanız, değişkenlere erişmek için kullandığınız düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.
1. Sık kullandığınız IDE ile yeni bir PHP projesi oluşturun.
1. Aşağıda sağlanan kodu ekleyin.
1. Programı çalıştırın.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
if (!getenv($key_var)) {
    throw new Exception ("Please set/export the following environment variable: $key_var");
} else {
    $subscription_key = getenv($key_var);
}
$endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
if (!getenv($endpoint_var)) {
    throw new Exception ("Please set/export the following environment variable: $endpoint_var");
} else {
    $endpoint = getenv($endpoint_var);
}

$path = '/text/analytics/v2.1/sentiment';

function GetSentiment ($host, $path, $key, $data) {
    // Make sure all text is UTF-8 encoded.
    foreach ($data as &$item) {
        foreach ($item as $ignore => &$value) {
            $value['text'] = utf8_encode($value['text']);
        }
    }

    $data = json_encode ($data);

    $headers = "Content-type: text/json\r\n" .
        "Content-Length: " . strlen($data) . "\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetSentiment($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Yaklaşım analizi yanıtı**

Başarılı yanıt, aşağıdaki örnekte gösterildiği gibi JSON biçiminde döndürülür: 

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Başlıca sözcük gruplarını ayıkla

Anahtar İfade Ayıklama API'si [Anahtar İfadeler metodunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) kullanarak bir metin belgesindeki anahtar ifadeleri ayıklar. Aşağıdaki örnekte hem İngilizce hem de İspanyolca belgelerin anahtarı ifadeleri ayıklanır.

1. Ortam değişkenlerini `TEXT_ANALYTICS_SUBSCRIPTION_KEY` ve `TEXT_ANALYTICS_ENDPOINT` kaynağınızın Azure uç noktası ve abonelik anahtarı için oluşturun. Uygulamayı düzenleme başladıktan sonra bu ortam değişkenlerini oluşturduysanız, değişkenlere erişmek için kullandığınız düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.
1. Sık kullandığınız IDE ile yeni bir PHP projesi oluşturun.
1. Aşağıda sağlanan kodu ekleyin.
1. Programı çalıştırın.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
if (!getenv($key_var)) {
    throw new Exception ("Please set/export the following environment variable: $key_var");
} else {
    $subscription_key = getenv($key_var);
}
$endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
if (!getenv($endpoint_var)) {
    throw new Exception ("Please set/export the following environment variable: $endpoint_var");
} else {
    $endpoint = getenv($endpoint_var);
}

$path = '/text/analytics/v2.1/keyPhrases';

function GetKeyPhrases ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' ),
        array ( 'id' => '3', 'language' => 'en', 'text' => 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetKeyPhrases($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Anahtar ifade ayıklama yanıtı**

Başarılı yanıt, aşağıdaki örnekte gösterildiği gibi JSON biçiminde döndürülür: 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Varlıkları tanımlama 

Varlıklar API'si, [Varlıklar metodunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) kullanarak bir metin belgesindeki iyi bilinen varlıkları tanımlar. Aşağıdaki örnekte İngilizce belgelerin varlıkları tanımlanır.

1. Ortam değişkenlerini `TEXT_ANALYTICS_SUBSCRIPTION_KEY` ve `TEXT_ANALYTICS_ENDPOINT` kaynağınızın Azure uç noktası ve abonelik anahtarı için oluşturun. Uygulamayı düzenleme başladıktan sonra bu ortam değişkenlerini oluşturduysanız, değişkenlere erişmek için kullandığınız düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.
1. Sık kullandığınız IDE ile yeni bir PHP projesi oluşturun.
1. Aşağıda sağlanan kodu ekleyin.
1. Programı çalıştırın.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
if (!getenv($key_var)) {
    throw new Exception ("Please set/export the following environment variable: $key_var");
} else {
    $subscription_key = getenv($key_var);
}
$endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
if (!getenv($endpoint_var)) {
    throw new Exception ("Please set/export the following environment variable: $endpoint_var");
} else {
    $endpoint = getenv($endpoint_var);
}

$path = '/text/analytics/v2.1/entities';

function GetEntities ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Content-Length: " . Length($data) . "\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";
    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'Microsoft is and It company.' ),
    )
);

print "Please wait a moment for the results to appear.";

$result = GetEntities($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Varlık ayıklama yanıtı**

Başarılı yanıt, aşağıdaki örnekte gösterildiği gibi JSON biçiminde döndürülür: 

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Power BI ile Metin Analizi](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Ayrıca bkz. 

 [Metin Analizine genel bakış](../overview.md)  
 [Sık sorulan sorular (SSS)](../text-analytics-resource-faq.md)
