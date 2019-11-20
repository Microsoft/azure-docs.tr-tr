---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 4395e0a14819021bd1e4ae32c89ffb0cf8e07d00
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906456"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Bir proje oluşturun ve gerekli modülleri içeri aktarın

En sevdiğiniz IDE veya düzenleyiciyi kullanarak yeni bir proje oluşturun veya masaüstünüzde adlı `translate-text.js` bir dosya içeren yeni bir klasör oluşturun. Sonra bu kod parçacığını projenize/dosyanıza kopyalayın:

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Bu modülleri kullanmadıysanız, programınızı çalıştırmadan önce bunları yüklemeniz gerekir. Bu paketleri yüklemek için şunu çalıştırın: `npm install request uuidv4`.

Bu modüller HTTP isteği ve `'X-ClientTraceId'` üst bilgisi için benzersiz tanıtıcı oluşturmak için gereklidir.

## <a name="set-the-subscription-key-and-endpoint"></a>Abonelik anahtarını ve uç noktayı ayarlama

Bu örnek, bu ortam değişkenlerinden Translator metin çevirisi abonelik anahtarınızı ve uç noktasını okumaya çalışacaktır: `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` ve. `TRANSLATOR_TEXT_ENDPOINT` Ortam değişkenlerine alışkın değilseniz, dizeler ayarlayabilir ve koşullu deyimleri açıklama `subscriptionKey` `endpoint` olarak ayarlayabilirsiniz.

Bu kodu projenize kopyalayın:

```javascript
var key_var = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('Please set/export the following environment variable: ' + key_var);
}
var subscriptionKey = process.env[key_var];
var endpoint_var = 'TRANSLATOR_TEXT_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('Please set/export the following environment variable: ' + endpoint_var);
}
var endpoint = process.env[endpoint_var];
```

## <a name="configure-the-request"></a>İsteği yapılandırma

İstek modülü aracılığıyla kullanıma sunulan `request()` yöntemi HTTP yöntemi, URL, istek parametreleri, üst bilgileri ve JSON gövdesi bileşenlerini `options` nesnesi olarak geçirmemizi sağlar. Bu kod parçacığında isteği yapılandıracağız:

>[!NOTE]
> Uç noktalar, rotalar ve istek parametreleri hakkında daha fazla bilgi için bkz [. Translator metin çevirisi API'si 3,0: Alfabede olacak şekilde.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate)

```javascript
let options = {
    method: 'POST',
    baseUrl: endpoint,
    url: 'transliterate',
    qs: {
      'api-version': '3.0',
      'language': 'ja',
      'fromScript': 'jpan',
      'toScript': 'latn'
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'こんにちは'
    }],
    json: true,
};
```

Bir isteğin kimliğini doğrulamanın en kolay yolu, abonelik anahtarınızda bir `Ocp-Apim-Subscription-Key` üst bilgisi olarak geçirmektir. Bu örnekte bu yöntem kullanılır. Alternatif olarak, abonelik anahtarınızı bir erişim belirteciyle değiştirebilir ve isteğinizi doğrulamak için erişim belirtecini bir `Authorization` üst bilgisi olarak geçirebilirsiniz.

Bilişsel hizmetler çoklu hizmet aboneliği kullanıyorsanız, istek üst bilgilerine de dahil `Ocp-Apim-Subscription-Region` etmeniz gerekir.

Daha fazla bilgi için bkz. [Kimlik doğrulaması](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="make-the-request-and-print-the-response"></a>İstekte bulunma ve yanıtı yazdırma

Ardından, `request()` yöntemini kullanarak isteği oluşturacağız. Bu istek ilk bağımsız değişken olarak bir önceki bölümde oluşturduğumuz `options` nesnesini alır ve düzeltilmiş JSON yanıtını yazdırır.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> Bu örnekte HTTP isteğini `options` nesnesinde tanımlıyoruz. Ancak istek modülü `.post` ve `.get` gibi kullanışlı yöntemleri de destekler. Daha fazla bilgi için bkz. [kullanışlı yöntemler](https://github.com/request/request#convenience-methods).

## <a name="put-it-all-together"></a>Hepsini bir araya getirin

İşte bu kadar! Translator Metin Çevirisi API'sini çağıran ve bir JSON yanıtı döndüren basit bir program oluşturdunuz. Artık programınızı çalıştırmak zamanı geldi:

```console
node transliterate-text.js
```

Kodunuzu bizimkiyle karşılaştırmak isterseniz, tam örnek kodu [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)’da bulabilirsiniz.

## <a name="sample-response"></a>Örnek yanıt

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Abonelik anahtarınızı programınıza sabit kodladıysanız, bu hızlı başlangıcı tamamladıktan sonra abonelik anahtarını kaldırdığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Translator Metin Çevirisi API'si ile yapabileceğiniz her şeyi anlamak için API başvurusuna göz atın.

> [!div class="nextstepaction"]
> [API başvurusu](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
