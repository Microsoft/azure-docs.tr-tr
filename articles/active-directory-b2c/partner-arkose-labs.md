---
title: Arkoo Labs ile Azure Active Directory B2C yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Riskli ve sahte kullanıcıları belirlemek için Arkoo Labs ile Azure Active Directory B2C yapılandırma öğreticisi
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/18/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c10a39b050fa66192f762ba642b4c8ac2e080250
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258153"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>Öğretici: Azure Active Directory B2C ile Arkoo Labs yapılandırma

Bu örnek öğreticide, [Arkoo Labs](https://www.arkoselabs.com/)ile Azure ACTIVE DIRECTORY (ad) B2C kimlik doğrulamasını tümleştirmeyi öğrenin. Arkoo Labs, kuruluşlara bot saldırılarına, hesap hileli saldırıları ve sahte hesap açılışlarına karşı yardımcı olur.  

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

- Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

- Azure aboneliğinize bağlı [bir Azure AD B2C kiracısı](tutorial-create-tenant.md) .

- Bir [Arkoi Labs](https://www.arkoselabs.com/book-a-demo/) hesabı.

## <a name="scenario-description"></a>Senaryo açıklaması

Arkoo Labs tümleştirmesi aşağıdaki bileşenleri içerir:

- **Arkoo Labs** -botlar ve diğer otomatik kötüye kullanım açısından korumaya yönelik bir sahtekarlık ve uygunsuz kullanım hizmetidir.

- **Kaydolma Kullanıcı akışı Azure AD B2C** -Arkoo Labs hizmetini kullanacak kaydolma deneyimi. , Arkoo Labs hizmeti ile tümleştirilecek özel HTML ve JavaScript ve API bağlayıcılarını kullanır.

- **Azure işlevleri** -API bağlayıcıları özelliği ile birlikte çalışarak sizin tarafınızdan barındırılan API uç noktası. Bu API, Arkoken Labs oturum belirtecinin sunucu tarafında doğrulanmasını sağlamaktan sorumludur.

Aşağıdaki diyagramda, Arkoo Labs Azure AD B2C ile nasıl tümleşildiği açıklanmaktadır.

![Görüntü Arkoo Labs mimari diyagramını gösterir](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| Adım  | Açıklama |
|---|---|
|1     | Kullanıcı kaydolur ve bir hesap oluşturur. Kullanıcı Gönder ' i seçtiğinde bir Arkoo Labs zorlama sınaması görüntülenir.         |
|2     |  Kullanıcı sınamayı tamamladıktan sonra, Azure AD B2C bir belirteç oluşturmak için durumu Arkoo Labs 'e gönderir. |
|3     |  Arkoo Labs bir belirteç oluşturur ve Azure AD B2C geri gönderir.   |
|4     |  Azure AD B2C Kaydolma formunu geçirmek için bir ara Web API 'SI çağırır.      |
|5     |  Ara Web API 'SI, kayıt formunu belirteç doğrulaması için Arkoo Laboratuvarı 'na gönderir.    |
|6     | Arkoi Laboratuvarı, doğrulama sonuçlarını ara Web API 'sine geri işler ve gönderir.|
|7     | Ara Web API 'SI, başarı veya başarısızlık sonucunu zorluk Azure AD B2C için gönderir. |
|8     | Sınama başarıyla tamamlanırsa, Azure AD B2C bir kaydolma formu gönderilir ve Azure AD B2C kimlik doğrulamasını tamamlar.|

## <a name="onboard-with-arkose-labs"></a>Arkoo Labs ile ekleme

1. [Arkotıcı](https://www.arkoselabs.com/book-a-demo/) ile iletişim kurun ve bir hesap oluşturun.

2. Hesap oluşturulduktan sonra şuraya gidin https://dashboard.arkoselabs.com/login  

3. Pano içinde, ortak anahtarınızı ve özel anahtarınızı bulmak için site ayarları ' na gidin. Azure AD B2C yapılandırmak için bu bilgiler daha sonra gerekecektir. Ortak ve özel anahtarların değerleri, `ARKOSE_PUBLIC_KEY` `ARKOSE_PRIVATE_KEY` [örnek kodda](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose)ve olarak adlandırılır.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C ile tümleştirme

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>1. kısım – bir ArkoseSessionToken özel özniteliği oluşturma

Özel bir öznitelik oluşturmak için aşağıdaki adımları izleyin:  

1. **Azure Portal**  >  **Azure AD B2C** git

2. **Kullanıcı özniteliklerini** seçin

3. **Ekle**’yi seçin

4. Öznitelik adı olarak **Arkosesessiontoken** girin

5. **Oluştur**’u seçin

[Özel öznitelikler](./user-flow-custom-attributes.md?pivots=b2c-user-flow)hakkında daha fazla bilgi edinin.

### <a name="part-2---create-a-user-flow"></a>Bölüm 2-Kullanıcı akışı oluşturma

Kullanıcı akışı, **kaydolma** ve **oturum açma** ya da yalnızca **kaydolma** için olabilir. Arkode Labs Kullanıcı akışı yalnızca kayıt sırasında gösterilir.

1. Kullanıcı akışı oluşturma [yönergelerine](./tutorial-create-user-flows.md) bakın. Mevcut bir Kullanıcı akışı kullanılıyorsa, **Önerilen (yeni nesil Önizleme)** sürüm türünde olması gerekir.

2. Kullanıcı akış ayarları ' nda, **Kullanıcı öznitelikleri** ' ne gidin ve **Arkosesessiontoken** talebini seçin.

![Görüntü özel özniteliklerin nasıl ekleneceğini gösterir](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>3. kısım-özel HTML, JavaScript ve sayfa düzenlerini yapılandırma

Belirtilen [HTML betiğine](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html)gidin. Dosya, JavaScript etiketlerine sahip bir HTML şablonu içerir ve `<script>` Bu üç şeyi yapar:

1. Arkoo Labs pencere öğesini işleyen ve istemci tarafı arkode Labs doğrulaması olan arkoo Labs betiğini yükleyin.

2. `extension_ArkoseSessionToken`Özel özniteliğe karşılık gelen giriş öğesi ve etiketini, `ArkoseSessionToken` kullanıcıya GÖSTERILEN kullanıcı arabiriminden gizleyin.

3. Kullanıcı arkoo Labs sınamasını tamamladığında, Arkoo Labs kullanıcının yanıtını doğrular ve bir belirteç oluşturur. `arkoseCallback`Özel JavaScript 'teki geri çağırma değeri, tarafından `extension_ArkoseSessionToken` oluşturulan belirteç değerine ayarlanır. Bu değer, sonraki bölümde açıklandığı gibi API uç noktasına gönderilir.

    Arkoo Labs istemci tarafı doğrulaması hakkında bilgi edinmek için [Bu makaleye](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup) bakın.

Kullanıcı akışınız için özel HTML ve JavaScript 'ı kullanmak için bahsedilen adımları izleyin.

1. [selfAsserted.html](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) dosyasını `<ARKOSE_PUBLIC_KEY>` , istemci tarafı doğrulaması için oluşturduğunuz değerle eşleşecek şekilde değiştirin ve hesabınız Için arkose Labs betiğini yüklemek üzere kullanılır.

2. HTML sayfasını, bir çıkış noktaları arası kaynak paylaşımı (CORS) etkin Web uç noktası üzerinde barındırın. [Azure Blob depolama hesabı oluşturun](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ve [CORS 'yi yapılandırın](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

  >[!NOTE]
  >Kendi özel HTML 'niz varsa, `<script>` öğeleri KOPYALAYıP HTML sayfanıza yapıştırın.

3. Sayfa düzenlerini yapılandırmak için bu adımları izleyin

   a. Azure portal **Azure AD B2C** gidin.

   b. **Kullanıcı akışları** ' na gidin ve Kullanıcı akışınızı seçin

   c. **Sayfa düzenlerini** seçin

   d. **Yerel hesap kayıt sayfası yerleşimini** seçin

   e. **Özel sayfa Içeriğini kullan** öğesini **Evet** olarak aç

   f. Özel HTML 'nizin bulunduğu URI 'yi **özel sayfa Içeriğini kullanarak** yapıştırın

   örneğin: Sosyal kimlik sağlayıcıları kullanıyorsanız, **sosyal hesap kayıt sayfası** düzeni için e ve **f** **adımını** tekrarlayın.

   ![sayfa düzenlerini gösteren resim](media/partner-arkose-labs/page-layouts.png)

4. Kullanıcı akışınızdan **Özellikler** ' e gidin ve sayfa düzeni (Önizleme) uygulayan **JavaScript 'i etkinleştir** ' i seçin. Daha fazla bilgi için bu [makaleye](./javascript-and-page-layout.md?pivots=b2c-user-flow) bakın.

### <a name="part-4---create-and-deploy-your-api"></a>4. Bölüm-API 'nizi oluşturma ve dağıtma

Visual Studio Code için [Azure işlevleri uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) yükler.

>[!Note]
>Bu bölümde bahsedilen adımlar, Azure Işlevini dağıtmak için Visual Studio Code kullandığınızı varsayar. Ayrıca, dağıtmak için Azure portal, Terminal veya komut istemi ya da başka bir kod Düzenleyicisi de kullanabilirsiniz.

#### <a name="run-the-api-locally"></a>API 'YI yerel olarak çalıştırma

1. Sol gezinti çubuğunda Visual Studio Code 'da Azure uzantısına gidin. Yerel Azure işlevinizi temsil eden **Yerel proje** klasörünü seçin.

2. Hata    >  ayıklayıcıyı başlatmak ve Azure işlevleri ana bilgisayarına eklemek için F5 tuşuna basın veya hata ayıklama **başlatma hata ayıklama** menüsünü kullanın. Bu komut, Azure Işlevinin oluşturduğu tek hata ayıklama yapılandırmasını otomatik olarak kullanır.

3. Azure Işlevi uzantısı yerel geliştirme için otomatik olarak birkaç dosya oluşturur, bağımlılıkları yükler ve henüz yoksa Işlev çekirdek araçlarını yükler. Bu araçlar, hata ayıklama deneyimiyle ilgili yardım sağlar.

4. Işlev çekirdeği aracının çıktısı Visual Studio Code **Terminal** panelinde görüntülenir. Konak başlatıldıktan sonra, **alt +** çıkışta gösterilen yerel URL 'ye tıklayarak tarayıcıyı açın ve işlevi çalıştırın. Azure Işlevleri Gezgininde, yerel olarak barındırılan işlevin URL 'sini görmek için işlevine sağ tıklayın.

Test sırasında yerel örneği yeniden dağıtmak için 1 ile 4 arasındaki adımları tekrarlayın.

#### <a name="add-environment-variables"></a>Ortam değişkenleri ekleme

Bu örnek, [http temel kimlik doğrulaması](https://tools.ietf.org/html/rfc7617)kullanarak Web API uç noktasını korur.

Kullanıcı adı ve parola, deponun bir parçası olarak değil, ortam değişkenleri olarak depolanır. Daha fazla bilgi için bkz. [local.settings.js](../azure-functions/functions-run-local.md?tabs=macos%2ccsharp%2cbash#local-settings-file) .

1. Kök klasörünüzdeki dosyada local.settings.jsoluşturma

2. Aşağıdaki kodu kopyalayıp dosyanın üzerine yapıştırın:

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
**BASIC_AUTH_USERNAME** ve **BASIC_AUTH_PASSWORD** değerleri, Azure işlevinizdeki API çağrısının kimliğini doğrulamak için kullanılan kimlik bilgileridir. İstediğiniz değerleri seçin.

, `<ARKOSE_PRIVATE_KEY>` Arkoo Labs hizmetinde oluşturduğunuz sunucu tarafı gizli dizedir. Bu, ön uç tarafından oluşturulan değerini doğrulamak için [Arkoo Labs sunucu tarafı doğrulama API](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) 'sini çağırmak için kullanılır `ArkoseSessionToken` .

, `<B2C_EXTENSIONS_APP_ID>` Dizininde özel öznitelikleri depolamak için Azure AD B2C tarafından kullanılan uygulamanın uygulama kimliğidir. Bu uygulama KIMLIĞINI Uygulama kayıtları giderek, B2C-Extensions-App ' i arayarak ve uygulama (istemci) KIMLIĞINI **genel bakış** bölmesinden kopyalayarak bulabilirsiniz. Karakterleri kaldırın `-` .

![Görüntü, uygulama kimliğine göre arama gösterir](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>Uygulamayı Web 'e dağıtma

1. Azure işlevinizi buluta dağıtmak için [Bu](/azure/javascript/tutorial-vscode-serverless-node-04) kılavuzda bahsedilen adımları izleyin. Azure işlevinizin Endpoint Web URL 'sini kopyalayın.

2. Dağıtıldıktan sonra **ayarları karşıya yükle** seçeneğini belirleyin. Ortam değişkenlerinizi App Service 'in [uygulama ayarlarına](../azure-functions/functions-develop-vs-code.md?tabs=csharp#application-settings-in-azure) yükler. Bu uygulama ayarları Azure portal aracılığıyla da yapılandırılabilir veya [yönetilebilir.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)

Azure Işlevleri için Visual Studio Code geliştirme hakkında daha fazla bilgi edinmek için [Bu makaleye](../azure-functions/functions-develop-vs-code.md?tabs=csharp#republish-project-files) bakın.

#### <a name="configure-and-enable-the-api-connector"></a>API bağlayıcısını yapılandırma ve etkinleştirme

[BIR API Bağlayıcısı oluşturun](./add-api-connector.md) ve Kullanıcı akışınız için etkinleştirin. API Bağlayıcısı yapılandırmanız şöyle görünmelidir:

![Görüntü, API bağlayıcısının nasıl yapılandırılacağını gösterir](media/partner-arkose-labs/configure-api-connector.png)

- **Uç nokta URL 'si** -Azure işlevi dağıtılırken daha önce kopyaladığınız işlev URL 'sidir.

- **Kullanıcı adı ve parola** -daha önce ortam değişkenleri olarak tanımladığınız Kullanıcı adı ve paroladır.

API bağlayıcısını etkinleştirmek için Kullanıcı akışınız için **API Bağlayıcısı** ayarları ' nda, **Kullanıcı adımını oluşturmadan önce** üzerinde çağrılacak API bağlayıcısını seçin. Bu, Kullanıcı kaydolma akışında **Oluştur** seçildiğinde API 'yi çağırır. API, `ArkoseSessionToken` arkoo pencere öğesinin geri çağırması tarafından ayarlanan değer için sunucu tarafı doğrulaması yapılır `arkoseCallback` .

![Görüntü API bağlayıcısını etkinleştirmeyi gösterir](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Azure AD B2C kiracıyı açın ve Ilkeler altında **Kullanıcı akışları ' nı** seçin.

2. Daha önce oluşturduğunuz Kullanıcı akışınızı seçin.

3. **Kullanıcı akışını Çalıştır** ' ı seçin ve ayarları seçin:

   a. Uygulama: kayıtlı uygulamayı seçin (örnek JWT)

   b. Yanıt URL 'SI: yeniden yönlendirme URL 'sini seçin

   c. **Kullanıcı akışını Çalıştır**' ı seçin.

4. Kaydolma akışına gidin ve hesap oluşturun

5. Oturumu kapat

6. Oturum açma akışından geçin  

7. **Devam**' ı seçtikten sonra bir arkoo Labs bulmaca görüntülenir.

## <a name="additional-resources"></a>Ek kaynaklar

- Azure AD B2C kaydolma Kullanıcı akışı için [örnek kodlar](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose)

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)