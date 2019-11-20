---
title: Hizmet modülünü kullanma-Azure Maps | Microsoft Docs
description: Azure haritalar Hizmetleri modülünü nasıl kullanacağınızı öğrenin.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 3911d4e780e993fdd1c2945b34cd683d47fb884a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827285"
---
# <a name="use-the-azure-maps-services-module"></a>Azure haritalar Hizmetleri modülünü kullanma

Azure Haritalar Web SDK 'Sı bir *hizmet modülü*sağlar. Bu modül, JavaScript veya TypeScript kullanarak Web veya Node. js uygulamalarında Azure Maps REST hizmetlerini kullanmayı kolaylaştıran yardımcı bir kitaplıktır.

## <a name="use-the-services-module-in-a-webpage"></a>Bir Web sayfasında hizmetler modülünü kullanma

1. Yeni bir HTML dosyası oluşturun.
1. Azure haritalar Hizmetleri modülünü yükleyin. Bunu iki şekilde yükleyebilirsiniz:
    - Azure haritalar Hizmetleri modülünün küresel olarak barındırılan, Azure Content Delivery Network sürümünü kullanın. Dosyanın `<head>` öğesine bir betik başvurusu ekleyin:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - Alternatif olarak, Azure Maps web SDK kaynak kodunu [Azure-Maps-Rest](https://www.npmjs.com/package/azure-maps-rest) NPM paketini kullanarak yerel olarak yükleyin ve ardından uygulamanızla birlikte barındırın. Bu paket TypeScript tanımlarını da içerir. Şu komutu kullanın:
    
        > **NPM Install Azure-Maps-Rest**
    
        Ardından, dosyanın `<head>` öğesine bir betik başvurusu ekleyin:

         ```html
        <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
         ```

1. Kimlik doğrulama işlem hattı oluşturun. Hizmet URL 'SI istemci uç noktasını başlatmak için işlem hattını oluşturmanız gerekir. Azure haritalar arama hizmeti istemcisinin kimliğini doğrulamak için kendi Azure haritalar hesap anahtarınızı veya Azure Active Directory (Azure AD) kimlik bilgilerinizi kullanın. Bu örnekte, arama hizmeti URL 'SI istemcisi oluşturulacaktır. 

    Kimlik doğrulaması için bir abonelik anahtarı kullanıyorsanız:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Kimlik doğrulaması için Azure AD kullanıyorsanız:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Daha fazla bilgi için bkz. [Azure Maps Ile kimlik doğrulama](azure-maps-authentication.md).

1. Aşağıdaki kod, "1 Microsoft Way, Redmond, WA" adresine coğrafi olarak yeni oluşturulan Azure Maps arama hizmeti URL 'SI istemcisini kullanır. Kod `searchAddress` işlevini kullanır ve sonuçları sayfanın gövdesinde bir tablo olarak görüntüler.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    Aşağıda, çalışan kod örneğinin tamamı verilmiştir:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Hizmetler modülünü kullanma" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'Da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>Hizmetler modülünü kullanarak</a> kaleme bakın.
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [MapsURL 'Si](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL 'Si](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

Hizmetler modülünü kullanan daha fazla kod örneği için şu makalelere bakın:

> [!div class="nextstepaction"]
> [Haritada arama sonuçlarını göster](./map-search-location.md)

> [!div class="nextstepaction"]
> [Bir koordinatdan bilgi al](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [A 'dan B 'ye yönlendirmeler göster](./map-route.md)