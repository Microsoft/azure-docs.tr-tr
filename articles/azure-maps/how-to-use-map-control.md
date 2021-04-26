---
title: Microsoft Azure Maps web harita denetimi ile çalışmaya başlama
description: Azure haritalar 'daki Harita Denetimi istemci tarafı JavaScript kitaplığını kullanarak Web ve mobil uygulamalara nasıl haritalar ekleneceğini öğrenin. Bkz. eşlemeleri yerelleştirin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: b283cacf94ff3567428787e50a3d7db9ba91fd9c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864049"
---
# <a name="use-the-azure-maps-map-control"></a>Azure Haritalar harita denetimini kullanma

Harita Denetimi istemci tarafı JavaScript kitaplığı, haritalar ve katıştırılmış Azure haritaları işlevlerini Web veya mobil uygulamanıza işletirmesini sağlar.

Bu belge, Azure Maps web SDK 'sını kullanır, ancak Azure haritalar Hizmetleri herhangi bir harita denetimiyle birlikte kullanılabilir. Azure haritalar ekibinin eklenti oluşturduğu bazı popüler açık kaynaklı harita denetimleri [aşağıda](open-source-projects.md#third-part-map-control-plugins) verilmiştir.

## <a name="prerequisites"></a>Önkoşullar

Harita Denetimi bir Web sayfasında kullanmak için aşağıdaki önkoşulların birine sahip olmanız gerekir:

* [Azure haritalar hesabı oluşturun](quick-demo-map-app.md#create-an-azure-maps-account) ve birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account).

* [Kimlik doğrulama seçenekleriyle](/javascript/api/azure-maps-control/atlas.authenticationoptions)Azure ACTIVE DIRECTORY (AAD) kimlik bilgilerinizi alın.

## <a name="create-a-new-map-in-a-web-page"></a>Web sayfasında yeni eşleme oluşturma

Harita Denetimi istemci tarafı JavaScript kitaplığını kullanarak bir Web sayfasına harita ekleyebilirsiniz.

1. Yeni bir HTML dosyası oluşturun.

2. Azure Haritalar Web SDK 'sını yükleyin. İki seçenekten birini belirleyebilirsiniz:

    * HTML dosyasının öğesinde JavaScript ve stil sayfasına başvurular ekleyerek Azure Maps web SDK 'sının küresel olarak barındırılan CDN sürümünü kullanın `<head>` :

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * Azure Haritalar Web SDK kaynak kodunu [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) NPM paketini kullanarak yerel olarak yükleyin ve uygulamanızla birlikte barındırın. Bu paket TypeScript tanımlarını da içerir.

      > **NPM Install Azure-Maps-Control**

    Ardından, Azure Maps stil sayfasına başvuruları `<head>` dosyanın öğesine ekleyin:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > Aşağıdaki kod eklenerek TypeScript tanımları uygulamanıza aktarılabilir:
    >
    > ```javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Haritayı sayfanın tam gövdesini dolduracak şekilde işlemek için aşağıdaki `<style>` öğesini `<head>` öğesine ekleyin.

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. Sayfanın gövdesinde bir `<div>` öğe ekleyin ve bunu `id` **myMap**' den verin.

   ```HTML
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. Şimdi harita denetimini başlatacağız. Denetimin kimliğini doğrulamak için bir Azure Maps abonelik anahtarınız olması veya [kimlik doğrulama seçenekleriyle](/javascript/api/azure-maps-control/atlas.authenticationoptions)Azure ACTIVE DIRECTORY (AAD) kimlik bilgileri kullanmanız gerekir.

    Kimlik doğrulaması için bir abonelik anahtarı kullanıyorsanız, aşağıdaki betik öğesini kopyalayın ve `<head>` öğesinin içine ve ilk öğenin altına yapıştırın `<script>` . `<Your Azure Maps Key>`Azure haritalar birincil abonelik anahtarınızla değiştirin.

     ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
       }
    </script>
    ```

    Kimlik doğrulaması için Azure Active Directory (AAD) kullanıyorsanız, aşağıdaki betik öğesini kopyalayın ve `<head>` öğesinin içine ve ilk öğenin altına yapıştırın `<script>` .

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'aad',
                    clientId: '<Your AAD Client Id>',
                    aadAppId: '<Your AAD App Id>',
                    aadTenant: '<Your AAD Tenant Id>'
                }
            });
        }
    </script>
   ```

    Azure haritalar ile kimlik doğrulama hakkında daha fazla bilgi için bkz. [Azure Maps Ile kimlik doğrulama](azure-maps-authentication.md) belgesi. Ayrıca, Azure Active Directory (AAD) Azure eşlemeleriyle nasıl tümleştirileceğini gösteren örneklerin listesi [burada](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)bulunabilir.

    >[!TIP]
    >Bu örnekte, eşlemenin ' de geçirdik `id` `<div>` . Bunu yapmanın bir diğer yolu, `HTMLElement` ilk parametre olarak geçirerek nesneyi geçirmektir `document.getElementById('myMap')` .

6. İsteğe bağlı olarak, aşağıdaki `meta` öğeleri sayfanın öğesine eklemeyi de yararlı bulabilirsiniz `head` :

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Hepsini birlikte koymak, HTML dosyanız aşağıdaki işarettekine benzer bir şekilde görünmelidir:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

8. Web tarayıcınızda dosyasını açın ve işlenmiş Haritayı görüntüleyin. Aşağıdaki görüntü gibi görünmelidir:

   ![İşlenen sonucu gösteren harita resmi](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map&quot;></a>Haritayı yerelleştirme

Azure haritalar, işlenen harita için dili ve bölgesel görünümü ayarlamanın iki farklı yolunu sağlar. İlk seçenek, bu bilgileri genel `atlas` ad alanına eklemektir, bu da uygulamanızdaki tüm harita denetim örneklerinin bu ayarları varsayılan olarak alacak şekilde sonuçlanır. Aşağıdaki dil, dili Fransızca (&quot;fr-FR") ve bölgesel görünüm olarak "Auto" olarak ayarlar:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

İkinci seçenek, eşleme şu şekilde yüklenirken bu bilgileri eşleme seçeneklerine iletmektir:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!NOTE]
> Aynı sayfada farklı dil ve bölge ayarlarıyla birden çok eşleme örneği yüklemek mümkündür. Buna ek olarak, bu ayarlar haritanın işlevi kullanılarak yüklendikten sonra da güncelleştirilemeyebilir `setStyle` .

Aşağıda, dil "fr-FR" olarak ayarlanan ve bölgesel görünüm "Auto" olarak ayarlanmış bir Azure Maps örneği verilmiştir.

![Fransızca 'da etiketleri gösteren harita resmi](./media/how-to-use-map-control/websdk-localization.png)

Desteklenen dillerin ve bölgesel görünümlerin tamamen listesi [burada](supported-languages.md)belgelenmiştir.

## <a name="azure-government-cloud-support"></a>Azure Kamu bulut desteği

Azure Haritalar Web SDK 'Sı, Azure Kamu Bulutu 'nı destekler. Azure Maps web SDK 'sına erişmek için kullanılan tüm JavaScript ve CSS URL 'Leri aynı kalır. Azure haritalar platformunun Azure Kamu bulut sürümüne bağlanmak için aşağıdaki görevlerin yapılması gerekir.

Etkileşimli harita denetimini kullanırken, sınıfının bir örneğini oluşturmadan önce aşağıdaki kod satırını ekleyin `Map` .

```javascript
atlas.setDomain('atlas.azure.us');
```

Harita ve hizmetlerin kimliğini doğrularken Azure 'un kimlik doğrulama ayrıntılarını Azure Kamu bulut platformunda kullandığınızdan emin olun.

Hizmetler modülünü kullanırken, bir API URL uç noktası örneği oluşturulurken hizmetler için etki alanının ayarlanması gerekir. Örneğin, aşağıdaki kod, sınıfının bir örneğini oluşturur `SearchURL` ve etki alanını Azure Kamu bulutuna yönlendirir.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Azure haritalar REST hizmetlerine doğrudan erişiyorsanız, URL etki alanını olarak değiştirin `atlas.azure.us` . Örneğin, Search API hizmetini kullanıyorsanız, URL etki alanını `https://atlas.microsoft.com/search/` olarak değiştirin `https://atlas.azure.us/search/` .

## <a name="javascript-frameworks"></a>JavaScript çerçeveleri

Bir JavaScript çerçevesi kullanılarak geliştirilirken, aşağıdaki açık kaynaklı projelerden biri yararlı olabilir:

* [ng-Azure-Maps](https://github.com/arnaudleclerc/ng-azure-maps) -Azure haritaları etrafında angular 10 sarmalayıcı.
* [AzureMapsControl. Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) -bir Azure Maps Blazor bileşeni.
* [Azure Maps 'e tepki verme bileşeni](https://github.com/WiredSolutions/react-azure-maps) -Azure Maps denetimi için bir tepki düzeyi.
* [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) -Vue uygulaması Için bir Azure Maps bileşeni.

## <a name="next-steps"></a>Sonraki adımlar

Bir harita oluşturmayı ve bununla nasıl etkileşime gireceğini öğrenin:

> [!div class="nextstepaction"]
> [Harita oluşturma](map-create.md)

Bir haritanın stilini nasıl ayarlayacağınızı öğrenin:

> [!div class="nextstepaction"]
> [Harita stili seçme](choose-map-style.md)

En iyi uygulamaları öğrenin ve örnekleri inceleyin:

> [!div class="nextstepaction"]
> [En iyi uygulamalar](web-sdk-best-practices.md)

> [!div class="nextstepaction"]
> [Kod örnekleri](/samples/browse/?products=azure-maps)

Azure Active Directory (AAD) Azure Maps ile nasıl tümleştirileceğini gösteren örneklerin listesi için bkz.:

> [!div class="nextstepaction"]
> [Azure AD kimlik doğrulaması örnekleri](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
