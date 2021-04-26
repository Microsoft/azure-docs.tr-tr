---
title: Azure Işlevleri ile Azure statik Web Apps bir API ekleme
description: Azure Işlevleri 'ni kullanarak statik Web uygulamanıza sunucusuz bir API ekleyerek Azure statik Web Apps kullanmaya başlayın.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: 33adaf33ba15b49b0a7e4d3e8f1b2a8c435a7966
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449044"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Azure Işlevleri ile Azure statik Web Apps önizlemesine API ekleme

Azure Işlevleri ile tümleştirme yoluyla, Azure statik Web Apps sunucusuz API 'Ler ekleyebilirsiniz. Bu makalede bir Azure statik Web Apps sitesine API ekleme ve dağıtma işlemlerinin nasıl yapılacağı gösterilir.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan Azure hesabı.
  - Hesabınız yoksa, [ücretsiz olarak bir tane oluşturabilirsiniz](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code için [Azure işlevleri uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Canlı sunucu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) uzantısı.
- API uygulamasını yerel olarak çalıştırmak için [Node.js](https://nodejs.org/download/)

## <a name="create-a-git-repository"></a>Git deposu oluşturma

Aşağıdaki adımlarda, yeni bir depoyu oluşturma ve dosyaları bilgisayarınıza kopyalama işlemleri gösterilmektedir.

1. GitHub ' da oturum açtığınızdan emin olun ve [https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate) Yeni bir depo oluşturmak için ' a gidin.
1. _Depo adı_ kutusuna **My-Vanilla-API** yazın.
1. **Şablondan depo oluştur**' a tıklayın.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Vanilla 'dan yeni bir depo oluşturma-temel":::

Projeniz oluşturulduktan sonra, yeni depo için tarayıcınızda URL 'YI kopyalayın. Git deposunu kopyalamak için bu URL 'YI Visual Studio Code kullanabilirsiniz.

1. Komut paletinde komutunu açmak için **F1** tuşuna basın.
1. URL 'YI _Git: kopyalama_ istemine yapıştırın ve **ENTER** tuşuna basın.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Visual Studio Code kullanarak bir GitHub projesini kopyalama":::

    Projeyi kopyalamak için bir depo konumu seçmek üzere istemleri izleyin.

## <a name="create-the-api"></a>API oluşturma

Daha sonra, uygulamanın API 'SI olarak bir Azure Işlevleri projesi oluşturursunuz. 

1. _My-Vanilla-API_ projesinde, **API** adlı bir alt klasör oluşturun.
1. Komut paletini açmak için **F1** tuşuna basın
1. **Azure işlevleri yazın: yeni proje oluştur...**
1. **Enter** tuşuna basın
1. **Gözatmayı** seçin
1. Proje çalışma alanınızın dizini olarak **API** klasörünü seçin
1. **Seç ' i** seçin

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Ekran görüntüsünde bir P I klasörü ve Seç düğmesi gösterilir.":::

1. İstemlerde aşağıdaki bilgileri sağlayın:

    - _Dil seçin_: **JavaScript** seçin
    - _Projenizin ilk işlevi için bir şablon seçin_: **http tetikleyicisi** seçin
    - _Bir işlev adı_ girin: **GetMessage** girin
    - _Yetkilendirme düzeyi_: herkesin işlev uç noktanızı çağırmasını sağlayan **anonim**' i seçin.
        - Yetkilendirme düzeyleri hakkında bilgi edinmek için bkz. [Yetkilendirme anahtarları](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

Visual Studio Code, HTTP ile tetiklenen bir işlev içeren bir Azure Işlevleri projesi oluşturur.

Uygulamanız artık aşağıdaki örneğe benzer bir proje yapısına sahiptir.

```files
├── api
│   ├── GetMessage
│   │   ├── function.json
│   │   ├── index.js
│   │   └── sample.dat
│   ├── host.json
│   ├── local.settings.json
│   ├── package.json
│   └── proxies.json
├── index.html
├── readme.md
└── styles.css
```

Ardından, `GetMessage` işlevi ön uca bir ileti döndürecek şekilde değiştirirsiniz.

1. `GetMessage` _API/getmessage/index.js_ altındaki işlevi aşağıdaki kodla güncelleştirin.

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. `GetMessage`Aşağıdaki ayarlarla yapılandırmayı güncelleştirin `api/GetMessage/function.json` .

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```

Yukarıdaki ayarlarla API uç noktası şu şekilde olur:

- İşleve bir HTTP isteği yapıldığında tetiklenir
- Kimlik doğrulama durumundan bağımsız olarak tüm istekler için kullanılabilir
- _/Api/Message_ yolu aracılığıyla sunulur

## <a name="run-the-api-locally"></a>API 'YI yerel olarak çalıştırma

Visual Studio Code, Azure 'da yayımlamadan önce bu projeyi yerel geliştirme bilgisayarınızda çalıştırmanıza olanak sağlamak için [Azure Functions Core Tools](../azure-functions/functions-run-local.md) tümleştirilir.

> [!TIP]
> Devam etmeden önce [Önkoşul](#prerequisites) bölümünde listelenen tüm kaynakların yüklü olduğundan emin olun.

1. Işlevler uygulamasını başlatmak için **F5** tuşuna basarak işlevi çalıştırın.

1. Azure Functions Core Tools önceden yüklenmemişse, istemde **yükleme** ' yi seçin.

    Temel araçlar, _Terminal_ panelinde çalışan uygulamanın çıktısını gösterir. Çıktının bir parçası olarak, HTTP ile tetiklenen işlevinizin URL uç noktasını yerel olarak çalıştıran bir şekilde görebilirsiniz.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Ekran görüntüsü, U R L 'yi görebileceğiniz TERMINAL sekmesini gösterir.":::

1. Temel araçlar çalışırken, API 'nin doğru şekilde çalıştığını doğrulamak için aşağıdaki URL 'ye gidin: `http://localhost:7071/api/message` .

   Tarayıcıdaki yanıt aşağıdaki örneğe benzer şekilde görünmelidir:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Alt Textekran görüntüsü tarayıcıda bir metin yanıtı gösterir.":::

1. Hata ayıklama oturumunu durdurmak için **SHIFT + F5** tuşlarına basın.

### <a name="call-the-api-from-the-application"></a>Uygulamadan API 'YI çağırma

Azure 'a dağıtıldığında, API 'ye gönderilen istekler, rotaya gönderilen istekler için Işlevler uygulamasına otomatik olarak yönlendirilir `api` . Yerel olarak çalışarak, uygulama ayarlarını yerel API 'ye proxy istekleri olarak yapılandırmanız gerekir.

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>API 'ye erişmek için HTML dosyalarını güncelleştirme

1. Daha sonra, _index.html_ dosyasının IÇERIĞINI, API işlevindeki metni getirmek ve ekranda göstermek için aşağıdaki kodla güncelleştirin:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading content from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

1. API projesini başlatmak için **F5** tuşuna basın.

1. **F1** tuşuna basın ve **canlı sunucu: Live Server ile aç**' ı seçin.

    Şimdi Web sayfasında API iletisini görmeniz gerekir.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Ekran görüntüsü bir tarayıcıda P ı iletisi gösterir.":::

   > [!NOTE]
   > Dosyayı çalıştırmak için diğer HTTP sunucularını veya proxy 'leri kullanabilirsiniz `index.html` . Kaynağından erişim `index.html` `file:///` çalışmayacak.

1. API projesini durdurmak için **SHIFT + F5** tuşlarına basın.

### <a name="commit-and-push-your-changes-to-github"></a>Değişikliklerinizi yürütün ve GitHub 'a gönderin

Visual Studio Code kullanarak değişikliklerinizi uzak git deposuna yürütün ve gönderin.

1. Komut paletini açmak için **F1** tuşuna basın
1. **Git yazın: Tümünü Kaydet**
1. Bir COMMIT iletisi ekleyin ve **ENTER** 'a basın
1. **F1** tuşuna basın
1. **Git: Push** yazın ve **ENTER** tuşuna basın

## <a name="create-a-static-web-app"></a>Statik web uygulaması oluşturma

1. [Azure portalına](https://portal.azure.com) gidin
1. **Kaynak oluştur** seçeneğine tıklayın
1. **Statik Web uygulaması** ara
1. **Statik Web uygulaması (Önizleme)** seçeneğine tıklayın
1. **Oluştur** seçeneğine tıklayın

Sonra uygulamaya özgü ayarları ekleyin.

1. _Azure aboneliğinizi_ seçin
1. Yeni bir _kaynak grubu_ seçin veya oluşturun
1. Uygulamayı **My-Vanilla-API** olarak adlandırın.
1. Size en yakın _bölgeyi_ seçin
1. **Ücretsiz** _SKU 'yu_ seçin
1. **GitHub ile Oturum Aç** düğmesine tıklayın ve GitHub ile kimliğinizi doğrulayın
1. Tercih ettiğiniz _kuruluşu_ seçin
1. _Depo_ açılır listesinden **My-Vanilla-API** ' yi seçin
1. _Dal_ açılan listesinden **Main** ' i seçin
1. Yapı _ön ayarları_ açılır listesinden yapı yapılandırması için tercih ettiğiniz çerçeveyi seçin

 > Bu alanlar, uygulama türünün varsayılan proje yapısını yansıtır. Değerleri uygulamanıza uyacak şekilde değiştirin.

Sonra, aşağıdaki derleme ayrıntılarını ekleyin.

1. **/** _Uygulama konumu_ için girin.
1. API _konumu_ kutusuna **API** girin.
1. Varsayılan değeri, _uygulama yapıtı konumundan_ kaldırın ve kutuyu boş bırakın.
1. **Gözden geçir ve oluştur**’a tıklayın.
1. **Oluştur** düğmesine tıklayın

    _Oluştur_ düğmesine tıkladığınızda Azure iki şey yapar. İlk olarak, temel bulut hizmetleri uygulamayı desteklemek için oluşturulur. Ardından, bir arka plan işlemi, uygulamayı derleyip dağıtmaya başlar.

1. Web uygulamasının _genel bakış_ sayfasına gitmek Için **Kaynağa Git** düğmesine tıklayın.

    Uygulama arka planda derlenmekte olduğundan, yapı durumunu görüntülemek için bir bağlantı içeren başlığa tıklayabilirsiniz.

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="GitHub Iş akışı":::

1. Dağıtım tamamlandıktan sonra, _genel bakış_ sayfasında gösterilen _URL_ bağlantısına tıklayarak web uygulamasına gidebilirsiniz.

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Azure portal statik uygulama URL 'sine erişme":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı daha fazla kullanım için tutmak istemiyorsanız, Azure statik Web uygulamasını ve ilgili kaynaklarını silmek için aşağıdaki adımları kullanabilirsiniz.

1. [Azure portalına](https://portal.azure.com) gidin
1. En üstteki arama çubuğunda **kaynak grupları** yazın
1. **Kaynak grupları** ' na tıklayın
1. **Myresourcegroup** seçin
1. _Myresourcegroup_ sayfasında, listelenen kaynakların silmek istedikleriniz olduğundan emin olun.
1. **Sil**’i seçin
1. Metin kutusuna **Myresourcegroup** yazın
1. **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulama ayarlarını yapılandırma](./application-settings.md)
