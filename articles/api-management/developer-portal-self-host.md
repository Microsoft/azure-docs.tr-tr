---
title: Geliştirici portalını kendi kendine barındırma
titleSuffix: Azure API Management
description: API Management geliştirici portalının kendisini nasıl barındıralabileceğinizi öğrenin.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e63a329a10699102802af6d544ab55aa19513f17
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741891"
---
# <a name="self-host-the-api-management-developer-portal"></a>API Management geliştirici portalını kendi kendine barındırma

Bu öğreticide, [API Management geliştirici portalının](api-management-howto-developer-portal.md)kendisini nasıl barındıracak açıklanmaktadır. Kendi kendine barındırma, geliştirici portalını çalışma zamanında sayfaları dinamik olarak özelleştiren özel mantık ve pencere öğeleri ile genişletme esnekliği sağlar. Farklı özelliklerle API Management örneğiniz için birden çok portalı kendi kendine barındırabilirsiniz. Bir portalı kendinden barındırdığınızda, bakımcı olur ve yükseltmelerinden sorumlu olursunuz. 

Aşağıdaki adımlarda yerel geliştirme ortamınızı ayarlama, geliştirici portalındaki değişiklikleri gerçekleştirme ve bunları bir Azure depolama hesabına yayımlama ve dağıtma işlemlerinin nasıl yapılacağı gösterilmektedir.

Yönetilen portaldaki medya dosyalarını zaten karşıya yüklediyseniz veya değiştirdiyseniz, bu makalenin ilerleyen kısımlarında, bkz. [yönetilen 'ten şirket Içine geçme](#move-from-managed-to-self-hosted-developer-portal).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Önkoşullar

Yerel bir geliştirme ortamı ayarlamak için şunları yapmanız gerekir:

- Bir API Management hizmet örneği. Bir tane yoksa, bkz. [hızlı başlangıç-Azure API Management örneği oluşturma](get-started-create-service-instance.md).
- [Statik Web siteleri özelliği](../storage/blobs/storage-blob-static-website.md) etkin olan bir Azure depolama hesabı. Bkz. [depolama hesabı oluşturma](../storage/common/storage-account-create.md).
- Makinenizde git. [Bu git öğreticisini izleyerek bu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)uygulamayı yüklemelisiniz.
- Makinenizde Node.js (LTS sürümü `v10.15.0` veya üzeri) ve NPM. Bkz. [Node.js ve NPM 'Yi indirme ve yükleme](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).
- Azure CLI. [Azure CLI yükleme adımlarını](/cli/azure/install-azure-cli-windows)izleyin.

## <a name="step-1-set-up-local-environment"></a>1. Adım: yerel ortamı ayarlama

Yerel ortamınızı ayarlamak için depoyu kopyalamanız, geliştirici portalının en son sürümüne geçmeniz ve NPM paketlerini kurmanız gerekir.

1. GitHub 'dan [api-Management-Developer-Portal](https://github.com/Azure/api-management-developer-portal.git) deposunu kopyalayın:

    ```console
    git clone https://github.com/Azure/api-management-developer-portal.git
    ```
1. Deponun yerel kopyasına gidin:

    ```console
    cd api-management-developer-portal
    ```

1. Portalın en son sürümüne göz atın.

    Aşağıdaki kodu çalıştırmadan önce [deponun yayınlar bölümünde](https://github.com/Azure/api-management-developer-portal/releases) geçerli yayın etiketini kontrol edin ve `<current-release-tag>` değeri en son yayın etiketiyle değiştirin.
    
    ```console
    git checkout <current-release-tag>
    ```

1. Kullanılabilir NPM paketlerini yükler:

    ```console
    npm install
    ```

> [!TIP]
> Her zaman [en son Portal sürümünü](https://github.com/Azure/api-management-developer-portal/releases) kullanın ve tüm portalınızı güncel tutun. Yazılım mühendisleri, `master` günlük geliştirme amaçları için bu deponun dalını kullanır. Yazılımın kararsız sürümlerini içerir.

## <a name="step-2-configure-json-files-static-website-and-cors-settings"></a>2. Adım: JSON dosyalarını, statik Web sitesini ve CORS ayarlarını yapılandırma

Geliştirici portalı, içeriği yönetmek için API Management REST API gerektirir.

### <a name="configdesignjson-file"></a>Dosya üzerinde config.design.js

`src`Klasöre gidin ve `config.design.json` dosyayı açın.

```json
{
  "environment": "development",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature ...",
  "backendUrl": "https://<service-name>.developer.azure-api.net",
  "useHipCaptcha": false
}
```

Dosyayı yapılandırın:

1. `managementApiUrl`Değerde, `<service-name>` API Management örneğinizin adıyla değiştirin. [Özel bir etki alanı](configure-custom-domain.md)yapılandırdıysanız bunun yerine kullanın (örneğin, `https://management.contoso.com` ).

    ```json
    {
    ...
    "managementApiUrl": "https://contoso-api.management.azure-api.net"
    ...
    ``` 

1. API Management örneğinize doğrudan REST API erişimi etkinleştirmek için [BIR SAS belirteci el ile oluşturun](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication#ManuallyCreateToken) .

1. Oluşturulan belirteci kopyalayın ve değer olarak yapıştırın `managementApiAccessToken` .

1. `backendUrl`Değerde, `<service-name>` API Management örneğinizin adıyla değiştirin. [Özel bir etki alanı](configure-custom-domain.md)yapılandırdıysanız bunun yerine kullanın (örneğin, `https://portal.contoso.com` ).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

1. Geliştirici portalınızda CAPTCHA 'yı etkinleştirmek istiyorsanız, bkz. [CAPTCHA 'Yı etkinleştirme](#enable-captcha).

### <a name="configpublishjson-file"></a>Dosya üzerinde config.publish.js

`src`Klasöre gidin ve `config.publish.json` dosyayı açın.

```json
{
  "environment": "publishing",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature...",
  "useHipCaptcha": false
}
```

Dosyayı yapılandırın:

1. `managementApiUrl` `managementApiAccessToken` Önceki yapılandırma dosyasından ve değerlerini kopyalayıp yapıştırın.

1. Geliştirici portalınızda CAPTCHA 'yı etkinleştirmek istiyorsanız, bkz. [CAPTCHA 'Yı etkinleştirme](#enable-captcha).

### <a name="configruntimejson-file"></a>Dosya üzerinde config.runtime.js

`src`Klasöre gidin ve `config.runtime.json` dosyayı açın.

```json
{
  "environment": "runtime",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "backendUrl": "https://<service-name>.developer.azure-api.net"
}
```

Dosyayı yapılandırın:

1. `managementApiUrl`Önceki yapılandırma dosyasından değeri kopyalayıp yapıştırın.

1. `backendUrl`Değerde, `<service-name>` API Management örneğinizin adıyla değiştirin. [Özel bir etki alanı](configure-custom-domain.md)yapılandırdıysanız bunun yerine kullanın (örneğin,. `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

### <a name="configure-the-static-website"></a>Statik Web sitesini yapılandırma

Dizin ve hata sayfalarına yollar sağlayarak depolama hesabınızda **statik Web sitesi** özelliğini yapılandırın:

1. Azure portal depolama hesabınıza gidin ve soldaki menüden **statik Web sitesi** ' ni seçin.

1. **Statik Web sitesi** sayfasında **etkin**' i seçin.

1. **Dizin belgesi adı** alanına *index.html* yazın.

1. **Hata belgesi yolu** alanına *404/index.html* girin.

1. **Kaydet**’i seçin.

### <a name="configure-the-cors-settings"></a>CORS ayarlarını yapılandırma

Çıkış noktaları arası kaynak paylaşımı (CORS) ayarlarını yapılandırın:

1. Azure portal depolama hesabınıza gidin ve soldaki menüden **CORS** ' yi seçin.

1. **BLOB hizmeti** sekmesinde aşağıdaki kuralları yapılandırın:

    | Kural | Değer |
    | ---- | ----- |
    | İzin verilen çıkış noktaları | * |
    | İzin verilen Yöntemler | Tüm HTTP fiillerini seçin. |
    | İzin verilen üst bilgiler | * |
    | Kullanıma sunulan üst bilgiler | * |
    | Maksimum yaş | 0 |

1. **Kaydet**’i seçin.

## <a name="step-3-run-the-portal"></a>3. Adım: portalı çalıştırma

Artık geliştirme modunda yerel bir portal örneği oluşturup çalıştırabilirsiniz. Geliştirme modunda, tüm iyileştirmeler kapalıdır ve kaynak eşlemeleri açıktır.

Şu komutu çalıştırın:

```console
npm start
```

Kısa bir süre sonra, varsayılan tarayıcı otomatik olarak yerel geliştirici portalı örneğiniz ile açılır. Varsayılan Adres `http://localhost:8080` , ancak zaten mevcutsa bağlantı noktası değiştirilebilir `8080` . Projenin kod tabanında yapılan değişiklikler bir yeniden derleme tetikleyip tarayıcı pencerenizi yenilemeyecektir.

## <a name="step-4-edit-through-the-visual-editor"></a>4. Adım: görsel düzenleyiciyi düzenleme

Bu görevleri gerçekleştirmek için görsel düzenleyiciyi kullanın:

- Portalınızı özelleştirme
- Yazar içeriği
- Web sitesinin yapısını düzenleme
- Görünümünü stilize et

Bkz. [öğretici: Geliştirici portalına erişim ve özelleştirme](api-management-howto-developer-portal-customize.md). Yönetici Kullanıcı arabiriminin temellerini ele alır ve varsayılan içerikte önerilen değişiklikleri listeler. Tüm değişiklikleri yerel ortamda kaydedin ve kapatmak için **CTRL + C** tuşlarına basın.

## <a name="step-5-publish-locally"></a>5. Adım: yerel olarak yayımlama

Portal verileri, kesin türü belirtilmiş nesneler biçiminde gelir. Aşağıdaki komut bunları statik dosyalara çevirir ve çıktıyı `./dist/website` dizine koyar:

```console
npm run publish
```

## <a name="step-6-upload-static-files-to-a-blob"></a>6. Adım: statik dosyaları bir bloba yükleme

Yerel olarak oluşturulan statik dosyaları bir bloba yüklemek için Azure CLı 'yi kullanın ve ziyaretçilerin bunlara getirebileceği şekilde emin olun:

1. Windows komut Istemi, PowerShell veya diğer komut kabuğu 'nu açın.

1. Aşağıdaki Azure CLı komutunu çalıştırın.
   
    Değerini `<account-connection-string>` depolama hesabınızın bağlantı dizesiyle değiştirin. Depolama hesabınızın **erişim anahtarları** bölümünden edinebilirsiniz.

    ```azurecli
    az storage blob upload-batch --source dist/website \
        --destination '$web' \
        --connection-string <account-connection-string>
    ```


## <a name="step-7-go-to-your-website"></a>7. Adım: Web sitenize gidin

Web siteniz artık Azure depolama özelliklerinde belirtilen ana bilgisayar adı altında ( **statik Web sitelerindeki****birincil uç nokta** ) etkin durumdadır.

## <a name="step-8-change-api-management-notification-templates"></a>8. Adım: API Management bildirim şablonlarını değiştirme

API Management bildirim şablonlarındaki geliştirici portalı URL 'sini kendi kendine barındırılan portalınızı işaret etmek üzere değiştirin. Bkz. [Azure API Management bildirimleri ve e-posta şablonlarını yapılandırma](api-management-howto-configure-notifications.md).

Özellikle, varsayılan şablonlarda aşağıdaki değişiklikleri gerçekleştirin:

> [!NOTE]
> Aşağıdaki **güncelleştirilmiş** bölümlerdeki değerler, portalı **https: \/ /Portal.contoso.com/** konumunda barındırdığınızı varsayar. 

### <a name="email-change-confirmation"></a>E-posta değişikliği onayı

**E-posta değişikliği onay** bildirimi şablonunda geliştirici portalı URL 'sini güncelleştirin:

**Özgün içerik**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Güncellendi**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="new-developer-account-confirmation"></a>Yeni geliştirici hesabı onayı

**Yeni geliştirici hesabı onay** bildirimi şablonunda geliştirici portalı URL 'sini güncelleştirin:

**Özgün içerik**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Güncellendi**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="invite-user"></a>Kullanıcıyı davet et

**Kullanıcı** bildirim şablonunda geliştirici portalı URL 'sini güncelleştirin:

**Özgün içerik**

```html
<a href="$ConfirmUrl">$ConfirmUrl</a>
```

**Güncellendi**

```html
<a href="https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery">https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery</a>
```

### <a name="new-subscription-activated"></a>Yeni abonelik etkinleştirildi

**Yeni Abonelik etkin** bildirim şablonunda geliştirici portalı URL 'sini güncelleştirin:

**Özgün içerik**

```html
Thank you for subscribing to the <a href="http://$DevPortalUrl/products/$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Güncellendi**

```html
Thank you for subscribing to the <a href="https://portal.contoso.com/product#product=$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Özgün içerik**

```html
Visit the developer <a href="http://$DevPortalUrl/developer">profile area</a> to manage your subscription and subscription keys
```

**Güncellendi**

```html
Visit the developer <a href="https://portal.contoso.com/profile">profile area</a> to manage your subscription and subscription keys
```

**Özgün içerik**

```html
<a href="http://$DevPortalUrl/docs/services?product=$ProdId">Learn about the API</a>
```

**Güncellendi**

```html
<a href="https://portal.contoso.com/product#product=$ProdId">Learn about the API</a>
```

**Özgün içerik**

```html
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/applications">Feature your app in the app gallery</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">You can publish your application on our gallery for increased visibility to potential new users.</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/issues">Stay in touch</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
      If you have an issue, a question, a suggestion, a request, or if you just want to tell us something, go to the <a href="http://$DevPortalUrl/issues">Issues</a> page on the developer portal and create a new topic.
</p>
```

**Güncellendi**

```html
<!--Remove the entire block of HTML code above.-->
```

### <a name="password-change-confirmation"></a>Parola değiştirme onayı

**Parola değiştirme onayı** bildirim şablonunda geliştirici portalı URL 'sini güncelleştirin:

**Özgün içerik**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Güncellendi**

```html
<a href="https://portal.contoso.com/confirm-password?$ConfirmQuery">https://portal.contoso.com/confirm-password?$ConfirmQuery</a>
```

### <a name="all-templates"></a>Tüm şablonlar

Altbilgide bağlantısı olan herhangi bir şablonda geliştirici portalı URL 'sini güncelleştirin:

**Özgün içerik**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Güncellendi**

```html
<a href="https://portal.contoso.com/">https://portal.contoso.com/</a>
```

## <a name="move-from-managed-to-self-hosted-developer-portal"></a>Yönetilen 'ten şirket içinde barındırılan geliştirici portalına taşıma

Zaman içinde, iş gereksinimleriniz değişebilir. API Management geliştirici portalının yönetilen sürümünün gereksinimlerinizi artık karşılamazsa bir durum ortaya çıkabilir. Örneğin, yeni bir gereksinim, bir üçüncü taraf veri sağlayıcısıyla tümleşen özel bir pencere öğesi oluşturmaya zorlayabilir. Yönetim sürümünden farklı olarak, portalın şirket içinde barındırılan sürümü size tam esneklik ve genişletilebilirlik sağlar.

### <a name="transition-process"></a>Geçiş işlemi

Yönetilen sürümden aynı API Management hizmet örneği içinde şirket içinde barındırılan bir sürüme geçiş yapabilirsiniz. İşlem, portalın yönetilen sürümünde yaptığınız değişiklikleri korur. Portalın içeriğini önceden yedeklediğinizden emin olun. Yedekleme betiğini `scripts` API Management geliştirici portalı [GitHub deposunun](https://github.com/Azure/api-management-developer-portal)klasöründe bulabilirsiniz.

Dönüştürme işlemi, bu makaledeki önceki adımlarda gösterildiği gibi, genel olarak barındırılan bir portalı ayarlamaya neredeyse aynıdır. Yapılandırma adımında bir özel durum vardır. Dosyadaki depolama hesabının, `config.design.json` portalın yönetilen sürümünün depolama hesabıyla aynı olması gerekir. Bkz. Öğretici: SAS URL 'sini alma yönergeleri için [SAS kimlik bilgileri aracılığıyla Azure depolama 'ya erişmek Için LINUX VM sistem tarafından atanan bir kimlik kullanma](../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-storage-sas.md#get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls) .

> [!TIP]
> Dosyada ayrı bir depolama hesabı kullanmanızı öneririz `config.publish.json` . Bu yaklaşım size daha fazla denetim sağlar ve portalınızın barındırma hizmeti yönetimini basitleştirir.

## <a name="enable-captcha"></a>CAPTCHA 'yı etkinleştir

Şirket içinde barındırılan portalı ayarlarken, bu ayar aracılığıyla CAPTCHA ' yı devre dışı bırakmış olabilirsiniz `useHipCaptcha` . CAPTCHA ile iletişim, yalnızca yönetilen geliştirici portalı ana bilgisayar adı için çıkış noktaları arası kaynak paylaşımı (CORS) oluşmasını sağlayan bir uç nokta aracılığıyla gerçekleştirilir. Geliştirici portalınız şirket içinde barındırılıyorsa, farklı bir konak adı kullanır ve CAPTCHA, iletişime izin vermez.

### <a name="update-the-json-config-files"></a>JSON yapılandırma dosyalarını güncelleştirme

Kendi kendine barındırılan portalınızdaki CAPTCHA 'yı etkinleştirmek için:

1. `api.contoso.com`API Management hizmetinizin **Geliştirici Portalı** uç noktasına özel bir etki alanı (örneğin,) atayın.

    Bu etki alanı, portalınızın yönetilen sürümü ve CAPTCHA uç noktası için geçerlidir. Adımlar için bkz. [Azure API Management örneğiniz için özel etki alanı adı yapılandırma](configure-custom-domain.md).

1. `src`Kendi kendine barındırılan portalınız için [Yerel ortamdaki](#step-1-set-up-local-environment) klasöre gidin.

1. Yapılandırma dosyalarını güncelleştirin `json` :

    | Dosya | Yeni değer | Not |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | `<custom-domain>`İlk adımda ayarladığınız özel etki alanı ile değiştirin. |
    |  | `"useHipCaptcha": true` | Değeri şu şekilde değiştirin `true` |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | `<custom-domain>`İlk adımda ayarladığınız özel etki alanı ile değiştirin. |
    |  | `"useHipCaptcha": true` | Değeri şu şekilde değiştirin `true` |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | `<custom-domain>`İlk adımda ayarladığınız özel etki alanı ile değiştirin. |

1. Portalı [yayımlayın](#step-5-publish-locally) .

1. Yeni yayınlanan portalı [karşıya yükleyin](#step-6-upload-static-files-to-a-blob) ve barındırın.

1. Şirket içinde barındırılan portalı özel bir etki alanı aracılığıyla kullanıma sunun.

Portalın etki alanının ilk ve ikinci seviyelerinin ilk adımda ayarlanan etki alanı ile eşleşmesi gerekir. Örneğin, `portal.contoso.com`. Tam adımlar, tercih ettiğiniz barındırma platformunuza bağlıdır. Bir Azure depolama hesabı kullandıysanız yönergeler için [özel bir etki alanını Azure Blob depolama uç noktası Ile eşleme](../storage/blobs/storage-custom-domain-name.md) konusuna başvurabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Kendi kendine barındırma Için alternatif yaklaşımlar](developer-portal-alternative-processes-self-host.md) hakkında bilgi edinin
