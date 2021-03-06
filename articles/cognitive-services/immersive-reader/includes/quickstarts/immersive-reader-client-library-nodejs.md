---
title: Modern okuyucu Node.js istemci kitaplığı hızlı başlangıç
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, sıfırdan bir Web uygulaması oluşturup tam ekran okuyucusu API işlevini eklersiniz.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: ffef03457f9d3faf385bd0a924474dfa6efc4121
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102619277"
---
[Modern okuyucu](https://www.onenote.com/learningtools) , yeni okuyucular, dil öğrenimi ve öğrenimi gibi öğrenme farklılığı olan kişiler için okuma kavramasını geliştirmek üzere kanıtlanmış teknikler uygulayan, benzer şekilde tasarlanmış bir araçtır. Uygulamalarınızı odaklamak üzere metin yalıtmak, yaygın olarak kullanılan sözcüklerin resimlerini göstermek, konuşma parçalarını vurgulamak, seçili metni sesli okumak, sözcükleri ve tümceleri gerçek zamanlı olarak çevirmek ve daha fazlasını yapmak için uygulamalarınızda modern okuyucu kullanabilirsiniz.

Bu hızlı başlangıçta, sıfırdan bir Web uygulaması oluşturacaksınız ve tam ekran okuyucu istemci kitaplığını kullanarak modern okuyucuyu tümleştirmeniz gerekir. Bu hızlı başlangıçta tam çalışma örneği [GitHub ' da](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs)kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* Azure Active Directory kimlik doğrulaması için yapılandırılmış bir tam ekran okuyucu kaynağı. Kurulumunu yapmak için [Bu yönergeleri](../../how-to-create-immersive-reader.md) izleyin. Ortam özellikleri yapılandırılırken burada oluşturulan bazı değerler gerekir. Daha sonra başvurmak üzere oturumunuzun çıkışını bir metin dosyasına kaydedin.
* [Node.js](https://nodejs.org/) ve [Yarn](https://yarnpkg.com)
* [Visual Studio Code](https://code.visualstudio.com/) gıbı bir IDE

## <a name="create-a-nodejs-web-app-with-express"></a>Express ile Node.js Web uygulaması oluşturma

Araçla bir Node.js Web uygulaması oluşturun `express-generator` .

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Yarn bağımlılıklarını yükleyip `request` `dotenv` daha sonra hızlı başlangıçta kullanılacak bağımlılıkları ekleyin.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>Kimlik doğrulamasını ayarlama

### <a name="configure-authentication-values"></a>Kimlik doğrulama değerlerini yapılandırma

Projenizin kökünde _. env_ adlı yeni bir dosya oluşturun. Aşağıdaki kodu, tam ekran okuyucuyu oluştururken verilen değerleri sağlayarak buna yapıştırın.
Tırnak işaretlerini veya "{" ve "}" karakterlerini eklemeyin.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ortak olmaması gereken gizli dizileri içerdiğinden, bu dosyayı kaynak denetimine yürütmemeyi unutmayın.

Sonra, _app.js_ açın ve aşağıdaki dosyayı dosyanın en üstüne ekleyin. Bu,. env dosyasında tanımlanan özellikleri, düğümüne ortam değişkenleri olarak yükler.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>Belirteci almak için yönlendiriciyi güncelleştirme
_routes\index.js_ dosyasını açın ve otomatik olarak oluşturulan kodu aşağıdaki kodla değiştirin.

Bu kod, hizmet sorumlusu parolanızı kullanarak bir Azure AD kimlik doğrulama belirteci alan bir API uç noktası oluşturur. Alt etki alanı da alır. Sonra belirteci ve alt etki alanını içeren bir nesne döndürür.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
            headers: {
                'content-type': 'application/x-www-form-urlencoded'
            },
            url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: process.env.CLIENT_ID,
                client_secret: process.env.CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

**Gettokenandalt etki alanı** API uç noktası, yetkisiz kullanıcıların, bir dizi kimlik doğrulaması (örneğin, [OAuth](https://oauth.net/2/)) arkasında güvenli hale gelmelidir Bu iş, bu hızlı başlangıç kapsamının ötesinde.

## <a name="add-sample-content"></a>Örnek içerik Ekle

Şimdi bu Web uygulamasına örnek içerik ekleyeceğiz. _Views\ındex.Pug_ dosyasını açın ve otomatik olarak oluşturulan kodu şu örnekle değiştirin:

```pug
doctype html
html
   head
      title Immersive Reader Quickstart Node.js

      link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

      // A polyfill for Promise is needed for IE11 support.
      script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

      script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
      script(src='https://code.jquery.com/jquery-3.3.1.min.js')

      style(type="text/css").
        .immersive-reader-button {
          background-color: white;
          margin-top: 5px;
          border: 1px solid black;
          float: right;
        }
   body
      div(class="container")
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

        h1(id="ir-title") About Immersive Reader
        div(id="ir-content" lang="en-us")
          p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

            ul
                li Shows content in a minimal reading view
                li Displays pictures of commonly used words
                li Highlights nouns, verbs, adjectives, and adverbs
                li Reads your content out loud to you
                li Translates your content into another language
                li Breaks down words into syllables

          h3 The Immersive Reader is available in many languages.

          p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
          p(lang="zh-cn") 沉浸式阅读器支持许多语言
          p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
          p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

script(type="text/javascript").
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
                type: "GET",
                success: function (data) {
                    if (data.error) {
                        reject(data.error);
                    } else {
                        resolve(data);
                    }
                },
                error: function (err) {
                    reject(err);
                }
            });
        });
    }

    $(".immersive-reader-button").click(function () {
        handleLaunchImmersiveReader();
    });

    function handleLaunchImmersiveReader() {
        getTokenAndSubdomainAsync()
            .then(function (response) {
                const token = response["token"];
                const subdomain = response["subdomain"];
                // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                const data = {
                    title: $("#ir-title").text(),
                    chunks: [{
                        content: $("#ir-content").html(),
                        mimeType: "text/html"
                    }]
                };
                // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                const options = {
                    "onExit": exitCallback,
                    "uiZIndex": 2000
                };
                ImmersiveReader.launchAsync(token, subdomain, data, options)
                    .catch(function (error) {
                        alert("Error in launching the Immersive Reader. Check the console.");
                        console.log(error);
                    });
            })
            .catch(function (error) {
                alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                console.log(error);
            });
    }

    function exitCallback() {
        console.log("This is the callback function. It is executed when the Immersive Reader closes.");
    }
```


Metnin tümünde, metnin dillerini açıklayan bir **lang** özniteliği olduğuna dikkat edin. Bu öznitelik, modern okuyucunun ilgili dil ve dil bilgisi özelliklerini sağlamasına yardımcı olur.

## <a name="build-and-run-the-app"></a>Uygulamayı derleyin ve çalıştırın

Web Uygulamam artık hazır. Uygulamayı çalıştırarak başlatın:

```bash
npm start
```

Tarayıcınızı açın ve adresine gidin _http://localhost:3000_ . Şunları görmeniz gerekir:

![Örnek uygulama-Node.js](../../media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Tam ekran okuyucuyu başlatın

"Modern okuyucu" düğmesine tıkladığınızda, sayfadaki içerikle birlikte modern okuyucu başlatılır.

![Modern okuyucu-Node.js](../../media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kaynak oluşturma ve AAD yapılandırma](../../how-to-create-immersive-reader.md)