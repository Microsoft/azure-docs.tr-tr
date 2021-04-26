---
title: Birden çok modern okuyucu kaynağını tümleştirme
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, birden çok derinlikli okuyucu kaynağı kullanarak tam ekran okuyucu Başlatan bir Node.js uygulaması oluşturacaksınız.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 01/14/2020
ms.author: skamal
ms.custom: devx-track-js
ms.openlocfilehash: dc6c7bd9e38aa21ab10f55fca2500b0c12194d43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620174"
---
# <a name="integrate-multiple-immersive-reader-resources"></a>Birden çok modern okuyucu kaynağını tümleştirme

[Genel bakışta](./overview.md), derinlikli okuyucu ne olduğunu ve dil öğrenimi, gelişmekte olan okuyucular ve öğrenme farklılığı olan öğrenciler için okuma kavraışını geliştirmek üzere kendini kanıtlamış tekniklerin nasıl uyguladığını öğrendiniz. [Hızlı](./quickstarts/client-libraries.md)başlangıçta, tek bir kaynakla modern okuyucu kullanmayı öğrendiniz. Bu öğreticide, aynı uygulamadaki birden çok derinlikli okuyucu kaynağını tümleştirme ele alınmaktadır. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Var olan bir kaynak grubu altında birden çok modern okuyucu kaynağı oluşturma
> * Birden çok kaynak kullanarak tam ekran okuyucu başlatma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/cognitive-services/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* NodeJS ile modern okuyucuyu Başlatan bir Web uygulaması oluşturmak için [hızlı](./quickstarts/client-libraries.md?pivots=programming-language-nodejs) başlangıcı izleyin. Bu hızlı başlangıçta, tek bir tam ekran okuyucu kaynağı yapılandırırsınız. Bu öğreticide bunun üzerine oluşturacağız.

## <a name="create-the-immersive-reader-resources"></a>Tam ekran okuyucu kaynakları oluşturma

Her bir derinlikli okuyucu kaynağını oluşturmak için [Bu yönergeleri](./how-to-create-immersive-reader.md) izleyin. **Create-ImmersiveReaderResource** betiği,, `ResourceName` `ResourceSubdomain` ve `ResourceLocation` olarak parametreler içerir. Bunlar, oluşturulmakta olan her kaynak için benzersiz olmalıdır. Kalan parametreler, ilk derinlikli okuyucu kaynağınızı ayarlarken kullandığınız ile aynı olmalıdır. Bu şekilde, her bir kaynak aynı Azure Kaynak grubu ve Azure AD uygulaması ile bağlantılı olabilir.

Aşağıdaki örnekte, biri WestUS ve diğeri EastUS içinde olmak üzere iki kaynağın nasıl oluşturulacağı gösterilmektedir. , Ve için benzersiz değerlere dikkat edin `ResourceName` `ResourceSubdomain` `ResourceLocation` .

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>Ortam yapılandırmasına kaynak ekleme

Hızlı başlangıçta,,, ve parametrelerini içeren bir ortam yapılandırma dosyası oluşturdunuz `TenantId` `ClientId` `ClientSecret` `Subdomain` . Tüm kaynaklarınız aynı Azure AD uygulamasını kullandığından, ve için aynı değerleri kullanabiliriz `TenantId` `ClientId` `ClientSecret` . Yapılması gereken tek değişiklik her bir kaynak için her bir alt etki alanını listeme amaçlıdır.

Yeni __. env__ dosyanız şimdi aşağıdakine benzer görünmelidir:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Ortak olmaması gereken gizli dizileri içerdiğinden, bu dosyayı kaynak denetimine yürütmemeyi unutmayın.

Daha sonra, oluşturduğum  _routes\index.js_ dosyayı, birden çok kaynaklarımızı destekleyecek şekilde değiştirmektir. İçeriğini aşağıdaki kodla değiştirin.

Daha önce olduğu gibi, bu kod hizmet sorumlusu parolanızı kullanarak bir Azure AD kimlik doğrulama belirteci alan bir API uç noktası oluşturur. Bu kez, kullanıcının bir kaynak konumu belirtmesini ve bunu bir sorgu parametresi olarak iletmesini sağlar. Ardından, belirteci ve ilgili alt etki alanını içeren bir nesne döndürür.

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

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

**Getimmersivereaderlaunchparams** API uç noktası, yetkisiz kullanıcıların, bir dizi kimlik doğrulaması (örneğin, [OAuth](https://oauth.net/2/)) arkasında güvenli hale gelmelidir Bu iş, Bu öğreticinin kapsamı dışındadır.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Örnek içerikle modern okuyucu başlatma

1. _Views\ındex.Pug_ dosyasını açın ve içeriğini aşağıdaki kodla değiştirin. Bu kod, sayfayı bazı örnek içerikle doldurur ve tam ekran okuyucuyu Başlatan iki düğme ekler. Bir tane, EastUS kaynağı için derinlikli okuyucu başlatma ve WestUS kaynağı için bir diğeri.

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
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

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
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
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

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
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

3. Web Uygulamam artık hazır. Uygulamayı çalıştırarak başlatın:

    ```bash
    npm start
    ```

4. Tarayıcınızı açın ve adresine gidin `http://localhost:3000` . Sayfada Yukarıdaki içeriği görmeniz gerekir. Bu ilgili kaynakları kullanarak tam ekran okuyucuyu başlatmak için **EastUS derinlikli okuyucu** düğmesine veya **WestUS derinlikli okuyucu** düğmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

* [Modern Okuyucu SDK 'sını](https://github.com/microsoft/immersive-reader-sdk) ve [tam ekran okuyucu SDK başvurusunu](./reference.md) keşfet
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp) 'daki kod örneklerini görüntüle