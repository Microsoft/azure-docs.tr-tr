---
title: HTML şablonlarıyla Kullanıcı arabirimini özelleştirme
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C kullanan uygulamalarınız için Kullanıcı arabirimini HTML şablonlarıyla özelleştirmeyi öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8f9f6dc1abd08c5e53f3d44a8f6ec1b3e20786ed
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717450"
---
# <a name="customize-the-user-interface-with-html-templates-in-azure-active-directory-b2c"></a>Kullanıcı arabirimini Azure Active Directory B2C HTML şablonlarıyla özelleştirme

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) müşterileriniz tarafından görüntülenen kullanıcı arabirimini markalamayı ve özelleştirmeyi, uygulamanızda sorunsuz bir kullanıcı deneyimi sağlamaya yardımcı olur. Bu deneyimlere kaydolma, oturum açma, profil düzenlemesi ve parola sıfırlama dahildir. Bu makale, Kullanıcı arabirimi (UI) özelleştirmesi yöntemlerini tanıtır. 

> [!TIP]
> Kullanıcı akış sayfalarınızın yalnızca başlık logosunu, arka plan resmini ve arka plan rengini değiştirmek istiyorsanız, [Şirket markası](customize-ui.md) özelliğini deneyebilirsiniz.

## <a name="custom-html-and-css-overview"></a>Özel HTML ve CSS 'ye Genel Bakış

Azure AD B2C, [çıkış noktaları arası kaynak paylaşımı (CORS)](https://www.w3.org/TR/cors/)kullanarak müşterinizin tarayıcısında kodu çalıştırır. Çalışma zamanında, içerik, Kullanıcı akışınız veya özel ilkenizde belirttiğiniz bir URL 'den yüklenir. Kullanıcı deneyimindeki her sayfa, içeriğini o sayfa için belirttiğiniz URL 'den yükler. URL 'nizden içerik yüklendikten sonra, Azure AD B2C tarafından yerleştirilen bir HTML parçası ile birleştirilir ve sayfa müşterinize görüntülenir.

![Özel sayfa içeriği kenar boşluğu](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Özel HTML sayfası içeriği

Özel sayfa içeriğinizi karşılamak için kendi markaınızla bir HTML sayfası oluşturun. Bu sayfa bir statik `*.html` sayfa veya .net, Node.js veya PHP gibi dinamik bir sayfa olabilir.

Özel sayfa içeriğiniz CSS ve JavaScript dahil olmak üzere herhangi bir HTML öğesi içerebilir, ancak iframe 'ler gibi güvenli olmayan öğeleri içeremez. Tek gerekli öğe, `id` `api` HTML sayfanız içinde olduğu gibi, olarak ayarlanmış bir div öğesidir `<div id="api"></div>` .

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>Varsayılan Azure AD B2C sayfalarını özelleştirme

Özel sayfa içeriğinizi sıfırdan oluşturmak yerine, Azure AD B2C's varsayılan sayfa içeriğini özelleştirebilirsiniz.

Aşağıdaki tabloda, Azure AD B2C tarafından sunulan varsayılan sayfa içeriği listelenmektedir. Dosyaları indirin ve kendi özel sayfalarınızı oluşturmak için bir başlangıç noktası olarak kullanın.

| Varsayılan sayfa | Description | İçerik tanımı KIMLIĞI<br/>(yalnızca özel ilke) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hata sayfası**. Bu sayfa bir özel durum veya hata ile karşılaşıldığında görüntülenir. | *api. Error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Otomatik olarak onaylanan sayfa**. Bu dosyayı bir sosyal hesap kaydolma sayfası, yerel hesap kaydolma sayfası, yerel hesap oturum açma sayfası, parola sıfırlama ve daha fazlası için özel bir sayfa içeriği olarak kullanın. Form, metin girişi kutusu, parola girişi kutusu, radyo düğmesi, tek seçim açılan kutuları ve çoklu seçim onay kutuları gibi çeşitli giriş denetimleri içerebilir. | *api. localaccountsignın*, *api. localaccountsignup*, *API. localaccountpasswordreset*, *API. selfasted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication sayfası**. Bu sayfada, kullanıcılar telefon numaralarını (metin veya ses kullanarak) kaydolma veya oturum açma sırasında doğrulayabilirler. | *api. phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profil güncelleştirme sayfası**. Bu sayfa, kullanıcıların profilini güncelleştirmek için erişebileceği bir form içerir. Bu sayfa, parola girişi alanları hariç sosyal hesap kaydolma sayfasına benzerdir. | *api. selfasserted. profileUpdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Birleşik kaydolma veya oturum açma sayfası**. Bu sayfa Kullanıcı kayıt ve oturum açma sürecini işler. Kullanıcılar, kurumsal kimlik sağlayıcılarını, Facebook veya Google + gibi sosyal kimlik sağlayıcılarını veya yerel hesapları kullanabilir. | *api. signuporsignın* |

## <a name="hosting-the-page-content"></a>Sayfa içeriğini barındırma

Kullanıcı arabirimini özelleştirmek için kendi HTML ve CSS dosyalarınızı kullanırken, Kullanıcı arabirimi içeriğinizi CORS 'yi destekleyen genel kullanıma açık bir HTTPS uç noktası üzerinde barındırın. Örneğin, [Azure Blob depolama](../storage/blobs/storage-blobs-introduction.md), [Azure App Services](../app-service/index.yml), Web sunucuları, CDNS, AWS S3 veya dosya paylaşım sistemleri.

## <a name="guidelines-for-using-custom-page-content"></a>Özel sayfa içeriğini kullanma yönergeleri

- HTML dosyanıza medya, CSS ve JavaScript dosyaları gibi dış kaynakları dahil ettiğinizde mutlak bir URL kullanın.
- [Sayfa düzeni](page-layout.md) 1.2.0 ve üstünü kullanarak, `data-preload="true"` CSS ve JavaScript 'in yükleme sırasını denetlemek için özniteliğini HTML etiketinize ekleyebilirsiniz. İle `data-preload="true"` , sayfa kullanıcıya gösterilmeden önce oluşturulur. Bu öznitelik, Kullanıcı tarafından gösterilen stilsiz HTML olmadan CSS dosyasını önceden yükleyerek sayfanın "titreşme" olmasını önlemeye yardımcı olur. Aşağıdaki HTML kod parçacığı etiketinin kullanımını gösterir `data-preload` .
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Varsayılan sayfa içeriğiyle başlayıp üzerine inşa etmenizi öneririz.
- [JavaScript 'i](javascript-and-page-layout.md) özel içeriğinize dahil edebilirsiniz.
- Desteklenen tarayıcı sürümleri şunlardır:
  - Internet Explorer 11, 10 ve Microsoft Edge
  - Internet Explorer 9 ve 8 için sınırlı destek
  - Google Chrome 42,0 ve üzeri
  - Mozilla Firefox 38,0 ve üzeri
  - İOS ve macOS için Safari, sürüm 12 ve üzeri
- Güvenlik kısıtlamaları nedeniyle, Azure AD B2C `frame` , `iframe` veya `form` HTML öğelerini desteklemez.

## <a name="localize-content"></a>İçeriği yerelleştirin

Azure AD B2C kiracınızda [dil özelleştirmesini](language-customization.md) etkinleştirerek HTML içeriğinizi yerelleştirebilirsiniz. Bu özelliği etkinleştirmek Azure AD B2C, OpenID Connect parametresini uç noktanıza iletmesine izin verir `ui_locales` . İçerik sunucunuz, dile özgü HTML sayfaları sağlamak için bu parametreyi kullanabilir.

İçerik, kullanılan yerel ayara bağlı olarak farklı yerlerden çekeklenebilir. CORS etkin uç noktanıza, belirli diller için içerik barındırmak üzere bir klasör yapısı ayarlarsınız. Joker karakter değerini kullanırsanız, doğru olanı çağıracaksınız `{Culture:RFC5646}` .

Örneğin, özel sayfa URI 'niz şöyle görünebilir:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Buradan içerik çekerek sayfayı Fransızca olarak yükleyebilirsiniz:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Özel sayfa içeriği Kılavuzu

İşleme genel bir bakış aşağıda verilmiştir:

1. Özel sayfa içeriğinizi barındırmak için bir konum hazırlayın (genel olarak erişilebilen, CORS özellikli bir HTTPS uç noktası).
1. Örneğin, varsayılan bir sayfa içerik dosyasını indirip özelleştirin `unified.html` .
1. Özel sayfa içeriğinizi herkese açık olan HTTPS uç noktanızla yayımlayın.
1. Web uygulamanız için çıkış noktaları arası kaynak paylaşımı (CORS) ayarlayın.
1. İlkenize özel ilke içerik URI 'sine işaret edin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. HTML içeriğinizi oluşturun

Başlığında ürününüzün marka adıyla özel bir sayfa içeriği oluşturun.

1. Aşağıdaki HTML kod parçacığını kopyalayın. *\<div id="api"\>\</div\>* Etiketler içinde yer alan boş bir öğe ile iyi BIÇIMLENDIRILMIŞ HTML5 *\<body\>* . Bu öğe Azure AD B2C içeriğin nereye ekleneceğini gösterir.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Kopyalanmış parçacığı bir metin düzenleyicisine yapıştırın
1. Azure AD B2C, sayfanıza eklediği Kullanıcı Arabirimi öğelerine stil eklemek için CSS kullanın. Aşağıdaki örnek, kayıt eklenmiş HTML öğelerinin ayarlarını da içeren basit bir CSS dosyasını göstermektedir:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  Dosyayı *customize-ui.html* olarak kaydedin.

> [!NOTE]
> Login.microsoftonline.com kullanırsanız, güvenlik kısıtlamaları nedeniyle HTML form öğeleri kaldırılır. Özel HTML içerikinizdeki HTML form öğelerini kullanmak istiyorsanız, [b2clogin.com kullanın](b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. bir Azure Blob depolama hesabı oluşturun

Bu makalede, içeriğimizi barındırmak için Azure Blob depolamayı kullanırız. İçeriğinizi bir Web sunucusunda barındırabilirsiniz, ancak [Web SUNUCUNUZDA CORS 'yi etkinleştirmeniz](https://enable-cors.org/server.html)gerekir.

Blob depolamada HTML içeriğinizi barındırmak için aşağıdaki adımları gerçekleştirin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Hub** menüsünde, **Yeni**  >  **depolama**  >  **depolama hesabı**' nı seçin.
1. Depolama hesabınız için bir **abonelik** seçin.
1. Bir **kaynak grubu** oluşturun veya var olan bir grubu seçin.
1. Depolama hesabınız için benzersiz bir **ad** girin.
1. Depolama hesabınız için **coğrafi konumu** seçin.
1. **Dağıtım modeli** **Kaynak Yöneticisi** kalabilirler.
1. **Performans** **Standart** kalabilirler.
1. **Hesap türünü** **BLOB depolama alanı** olarak değiştirin.
1. **Çoğaltma** , **RA-GRS** kalabilir.
1. **Erişim katmanı** , **sık** kalabilir.
1. Depolama hesabı oluşturmak için **gözden geçir + oluştur** ' u seçin.
    Dağıtım tamamlandıktan sonra, **depolama hesabı** sayfası otomatik olarak açılır.

#### <a name="21-create-a-container"></a>2,1 kapsayıcı oluşturma

Blob depolamada ortak bir kapsayıcı oluşturmak için aşağıdaki adımları uygulayın:

1. Sol taraftaki menüdeki **BLOB hizmeti** altında **Bloblar**' ı seçin.
1. **+ Kapsayıcı** seçin.
1. **Ad** için *kök* girin. Ad, seçtiğiniz bir ad olabilir, örneğin *contoso*, ancak basitlik için bu örnekteki *kökü* kullanırız.
1. **Genel erişim düzeyi** için **BLOB**' u ve ardından **Tamam**' ı seçin.
1. Yeni kapsayıcıyı açmak için **kök** ' ı seçin.

#### <a name="22-upload-your-custom-page-content-files"></a>2,2 özel sayfa içerik dosyalarınızı karşıya yükleyin

1. **Karşıya Yükle**’yi seçin.
1. **Dosya Seç**' in yanındaki klasör simgesini seçin.
1. Sayfasına gidin ve sayfa UI özelleştirmesi bölümünde daha önce oluşturduğunuz **customize-ui.html**' ı seçin.
1. Bir alt klasöre yüklemek istiyorsanız, **Gelişmiş** ' i genişletin ve klasöre **Yükle ' ye** bir klasör adı girin.
1. **Karşıya Yükle**’yi seçin.
1. Karşıya yüklediğiniz **customize-ui.html** blobu seçin.
1. **URL metin kutusunun** SAĞıNDA, URL 'yi panonuza kopyalamak Için **Panoya Kopyala** simgesini seçin.
1. Web tarayıcısında, karşıya yüklediğiniz Blobun erişilebilir olduğunu doğrulamak için kopyaladığınız URL 'ye gidin. Erişilemezse, örneğin bir `ResourceNotFound` hatayla karşılaşırsanız, kapsayıcı erişim türünün **BLOB** olarak ayarlandığından emin olun.

### <a name="3-configure-cors"></a>3. CORS 'yi yapılandırma

Aşağıdaki adımları gerçekleştirerek, çıkış noktaları arası kaynak paylaşımı için blob depolamayı yapılandırın:

1. Menüsünde **CORS**' yi seçin.
1. **Izin verilen çıkış noktaları** için girin `https://your-tenant-name.b2clogin.com` . `your-tenant-name`Azure AD B2C kiracınızın adıyla değiştirin. Örneğin, `https://fabrikam.b2clogin.com`. Kiracı adınızı girerken tüm küçük harfleri kullanın.
1. **Izin verilen Yöntemler** için hem hem de öğesini seçin `GET` `OPTIONS` .
1. **Izin verilen üst bilgiler** için bir yıldız işareti (*) girin.
1. **Gösterilen üstbilgiler** için bir yıldız işareti (*) girin.
1. **Maksimum yaş** için 200 girin.
1. **Kaydet**’i seçin.

#### <a name="31-test-cors"></a>3,1 test CORS

Aşağıdaki adımları gerçekleştirerek hazırlandığınızı doğrulayın:

1. CORS yapılandırma adımını tekrarlayın. **Izin verilen çıkış noktaları** için şunu girin`https://www.test-cors.org`
1. [Www.test-CORS.org](https://www.test-cors.org/) adresine gidin 
1. **Uzak URL** kutusu IÇIN, HTML dosyanızın URL 'sini yapıştırın. Örneğin, `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. **Istek gönder**' i seçin.
    Sonuç olmalıdır `XHR status: 200` . 
    Bir hata alırsanız, CORS ayarlarınızın doğru olduğundan emin olun. Ayrıca CTRL + SHIFT + P tuşlarına basarak tarayıcı önbelleğinizi temizlemeniz veya özel bir gözatma oturumu açmanız gerekebilir.

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. Kullanıcı akışını güncelleştirme

1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Kullanıcı akışları**' nı seçin ve ardından *B2C_1_signupsignin1* Kullanıcı akışı ' nı seçin.
1. **Sayfa düzenlerini** seçin ve ardından **Birleşik kaydolma veya oturum açma sayfası** altında, **özel sayfa içeriğini kullanmak** için **Evet** ' i tıklatın.
1. **Özel sayfa URI 'si** içinde, daha önce kaydettiğiniz *custom-ui.html* dosyasının URI 'sini girin.
1. Sayfanın en üstünde **Kaydet**' i seçin.

### <a name="5-test-the-user-flow"></a>5. Kullanıcı akışını test etme

1. Azure AD B2C kiracınızda **Kullanıcı akışları** ' nı seçin ve *B2C_1_signupsignin1* Kullanıcı akışı ' nı seçin.
1. Sayfanın üst kısmında, **Kullanıcı akışını Çalıştır**' a tıklayın.
1. **Kullanıcı akışını Çalıştır** düğmesine tıklayın.

Aşağıdaki örneğe benzer bir sayfa, oluşturduğunuz CSS dosyasına göre ortalanan öğeleri görmeniz gerekir:

![Özel UI öğeleriyle kaydolma veya oturum açma sayfasını gösteren Web tarayıcısı](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. uzantılar dosyasını değiştirme

UI özelleştirmesini yapılandırmak için, **Içerik tanımını** ve alt öğelerini temel dosyadan uzantılar dosyasına kopyalayın.

1. İlkenizin temel dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Bu temel dosya, önkoşul içinde elde etmeniz gereken özel ilke başlangıç paketine dahil olan ilke dosyalarından biridir, [özel ilkeleri](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)kullanmaya başlayın.
1. **ContentDefinitions** öğesinin tüm içeriğini arayın ve kopyalayın.
1. Uzantı dosyasını açın. Örneğin, *TrustFrameworkExtensions.xml*. **Buildingblocks** öğesi için arama yapın. Öğe yoksa, ekleyin.
1. **Buildingblocks** öğesinin bir alt öğesi olarak kopyaladığınız **ContentDefinitions** öğesinin tüm içeriğini yapıştırın.
1. Kopyaladığınız XML içinde içeren **ContentDefinition** öğesini arayın `Id="api.signuporsignin"` .
1. **Loaduri** değerini, depolama ALANıNA yüklediğiniz HTML dosyasının URL 'si ile değiştirin. Örneğin, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Özel ilkeniz aşağıdaki kod parçacığı gibi görünmelidir:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Uzantı dosyasını kaydedin.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. güncelleştirilmiş özel ilkenizi karşıya yükleyin ve test edin

#### <a name="51-upload-the-custom-policy"></a>5,1 özel ilkeyi karşıya yükleme

1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Arama yapın ve **Azure AD B2C** seçin.
1. **İlkeler** altında **kimlik deneyimi çerçevesi**' ni seçin.
1. **Özel Ilkeyi karşıya yükle**' yi seçin.
1. Daha önce değiştirdiğiniz uzantıları dosyasını karşıya yükleyin.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2 **Şimdi Çalıştır** 'ı kullanarak özel ilkeyi test etme

1. Karşıya yüklediğiniz ilkeyi seçin ve **Şimdi Çalıştır**' ı seçin.
1. Bir e-posta adresi kullanarak kaydolabilirsiniz.

## <a name="configure-dynamic-custom-page-content-uri"></a>Dinamik özel sayfa içerik URI 'sini Yapılandır

Azure AD B2C özel ilke kullanarak, URL yolunda veya bir sorgu dizesinde bir parametre gönderebilirsiniz. Parametreyi HTML uç noktanıza ileterek sayfa içeriğini dinamik olarak değiştirebilirsiniz. Örneğin web veya mobil uygulamanızdan ilettiğiniz bir parametreye göre Azure AD B2C kaydolma veya oturum açma sayfanızdaki arka plan görüntüsünü değiştirebilirsiniz. Parametresi, uygulama KIMLIĞI, dil KIMLIĞI veya gibi özel sorgu dizesi parametresi gibi herhangi bir [talep Çözümleyicisi](claim-resolver-overview.md)olabilir `campaignId` .

### <a name="sending-query-string-parameters"></a>Sorgu dizesi parametreleri gönderiliyor

Sorgu dizesi parametreleri göndermek için [bağlı olan taraf ilkesinde](relyingparty.md) `ContentDefinitionParameters` aşağıda gösterildiği gibi bir öğe ekleyin.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

İçerik tanımınızda değerini `LoadUri` olarak değiştirin `https://<app_name>.azurewebsites.net/home/unified` . Özel ilkeniz `ContentDefinition` Aşağıdaki kod parçacığı gibi görünmelidir:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C sayfayı yüklediğinde, Web sunucusu uç noktanıza bir çağrı yapar:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Dinamik sayfa içerik URI 'SI

İçerik, kullanılan parametrelere göre farklı konumlardan çekeklenebilir. CORS etkin uç noktanıza, ana içerik için bir klasör yapısı ayarlayın. Örneğin, içeriği aşağıdaki yapıda düzenleyebilirsiniz. *Dil/HTML dosyalarınıza göre kök klasör/klasör*. Örneğin, özel sayfa URI 'niz şöyle görünebilir:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C, Fransızca için dil için iki harfli ISO kodu gönderir `fr` :

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>Örnek şablonlar

UI özelleştirmesi için örnek şablonları buradan bulabilirsiniz:

```bash
git clone https://github.com/azure-ad-b2c/html-templates
```

Bu proje aşağıdaki şablonları içerir:
- [Okyanus Mavisi](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/AzureBlue)
- [Kurşun Grisi](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/MSA)
- [Klasik](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/classic)
- [Şablon kaynakları](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/src)

Örneği kullanmak için:

1. Yerel makinenizde depoyu kopyalayın. Bir şablon klasörü `/AzureBlue` , `/MSA` veya seçin `/classic` .
1. Şablon klasörü ve klasörü altındaki tüm dosyaları `/src` , önceki bölümlerde açıklandığı gibi BLOB depolama alanına yükleyin.
1. Ardından, her bir `\*.html` dosyayı şablon klasöründen açın. Ardından, URL 'lerin tüm örneklerini `https://login.microsoftonline.com` Adım 2 ' de karşıya YÜKLEDIĞINIZ URL ile değiştirin. Örnek:
    
    Kimden:
    ```html
    https://login.microsoftonline.com/templates/src/fonts/segoeui.WOFF
    ```

    Hedef:
    ```html
    https://your-storage-account.blob.core.windows.net/your-container/templates/src/fonts/segoeui.WOFF
    ```
    
1. Dosyaları kaydedin `\*.html` ve BLOB depolamaya yükleyin.
1. Şimdi, daha önce belirtildiği gibi, HTML dosyanıza işaret eden ilkeyi değiştirin.
1. Eksik yazı tiplerini, görüntüleri veya CSS 'yi görürseniz, uzantılar ilkesi ve dosyalar 'daki başvurularınızı kontrol edin `\*.html` .

## <a name="use-company-branding-assets-in-custom-html"></a>Özel HTML 'de Şirket marka varlıklarını kullanma

[Şirket marka](customize-ui.md#configure-company-branding) varlıklarını özel bir HTML içinde kullanmak için, etiketin dışına aşağıdaki etiketleri ekleyin `<div id="api">` . Görüntü kaynağı arka plan resminin ve başlık logosunun yerine konur.

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

## <a name="next-steps"></a>Sonraki adımlar

[İstemci tarafı JavaScript kodunu](javascript-and-page-layout.md)etkinleştirmeyi öğrenin.
