---
title: Azure Active Directory B2C Kullanıcı arabirimini özelleştirme
description: Azure Active Directory B2C kullanan uygulamalarınız için Kullanıcı arabirimini özelleştirmeyi öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6ebaeedf88bc02aa16e8be07fcb734e44ffa5bb6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258161"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Azure Active Directory B2C Kullanıcı arabirimini özelleştirme

Azure Active Directory B2C (Azure AD B2C) müşterileriniz tarafından görüntülenen kullanıcı arabirimini markalamayı ve özelleştirmeyi, uygulamanızda sorunsuz bir kullanıcı deneyimi sağlamaya yardımcı olur. Bu deneyimlere kaydolma, oturum açma, profil düzenlemesi ve parola sıfırlama dahildir. Bu makalede hem Kullanıcı akışları hem de özel ilkeler için Kullanıcı arabirimi (UI) özelleştirmesi yöntemleri tanıtılmaktadır.

## <a name="ui-customization-in-different-scenarios"></a>Farklı senaryolarda UI özelleştirmesi

Uygulamanın kullanıcı ARABIRIMINI özelleştirmek için kullanabileceğiniz çeşitli yollar vardır. her biri farklı senaryolar için uygundur.

### <a name="user-flows"></a>Kullanıcı akışları

[Kullanıcı akışları](active-directory-b2c-reference-policies.md)kullanıyorsanız, yerleşik *sayfa düzeni şablonlarını*kullanarak veya kendi HTML ve CSS 'nizi kullanarak Kullanıcı akış sayfalarınızın görünümünü değiştirebilirsiniz. Her iki yöntem de bu makalenin ilerleyen kısımlarında ele alınmıştır.

Kullanıcı akışları için UI özelleştirmesini yapılandırmak üzere [Azure Portal](tutorial-customize-ui.md) kullanırsınız.

### <a name="custom-policies"></a>Özel ilkeler

Uygulamanızda kaydolma veya oturum açma, parola sıfırlama veya profil düzenlemesi sağlamak için [özel ilkeler](active-directory-b2c-overview-custom.md) kullanıyorsanız, [Kullanıcı arabirimini özelleştirmek için ilke dosyalarını](active-directory-b2c-ui-customization-custom.md)kullanın.

Bir müşterinin kararına göre dinamik içerik sağlamanız gerekiyorsa, bir sorgu dizesinde gönderilen parametreye bağlı olarak [sayfa içeriğini dinamik olarak değiştirecek](active-directory-b2c-ui-customization-custom-dynamic.md) özel ilkeler kullanın. Örneğin, Azure AD B2C kaydolma veya oturum açma sayfasındaki arka plan görüntüsünü, Web veya mobil uygulamanızdan geçirdiğiniz bir parametreye göre değiştirebilirsiniz.

### <a name="javascript"></a>JavaScript

İstemci tarafı JavaScript kodunu, hem [Kullanıcı akışlarında](user-flow-javascript-overview.md) hem de [özel ilkelerde](page-layout.md)etkinleştirebilirsiniz.

### <a name="sign-in-only-ui-customization"></a>Yalnızca oturum açma kullanıcı arabirimi özelleştirmesi

Kendisine yönelik parola sıfırlama sayfası ve doğrulama e-postalarıyla birlikte yalnızca oturum açma sağlıyorsanız, bir [Azure AD oturum açma sayfasında](../active-directory/fundamentals/customize-branding.md)kullanılan özelleştirme adımlarını kullanın.

Müşteriler, oturum açmadan önce profilini düzenlemeye çalışırlarsa, Azure AD oturum açma sayfasını özelleştirmek için kullanılan adımların aynısını kullanarak özelleştirdiğiniz bir sayfaya yönlendirilir.

## <a name="page-layout-templates"></a>Sayfa düzeni şablonları

Kullanıcı akışları, Kullanıcı deneyimi sayfalarınıza profesyonel bir görünüm kazandırmak için içinden seçebileceğiniz çeşitli yerleşik şablonlar sağlar. Bu düzen şablonları Ayrıca kendi özelleştirmesi için başlangıç noktası olarak da kullanılabilir.

Sol menüdeki **Özelleştir** altında **sayfa düzenleri** ' ni seçin ve ardından **şablon**' u seçin.

![Azure portal Kullanıcı akışı sayfasında şablon seçme açılan listesi](media/customize-ui-overview/template-selection.png)

Sonra, listeden bir şablon seçin. Her şablon için oturum açma sayfalarının örnekleri aşağıda verilmiştir:

| Okyanus Mavisi | Kurşun Grisi | Klasik |
|:-:|:-:|:-:|
|![Kaydolma oturum açma sayfasında işlenen okyanus mavi şablonu örneği](media/customize-ui-overview/template-ocean-blue.png)|![Kaydolma oturum açma sayfasında oluşturulan Kurşun Grisi şablonu örneği](media/customize-ui-overview/template-slate-gray.png)|![Kaydolma oturum açma sayfasında işlenen klasik şablon örneği](media/customize-ui-overview/template-classic.png)|

Bir şablon seçtiğinizde, seçilen düzen Kullanıcı akışındaki tüm sayfalara uygulanır ve her sayfanın URI 'SI **özel sayfa URI 'si** alanında görünür.

## <a name="custom-html-and-css"></a>Özel HTML ve CSS

Azure AD B2C, [çıkış noktaları arası kaynak paylaşımı (CORS)](https://www.w3.org/TR/cors/)adlı bir yaklaşım kullanarak müşterinizin tarayıcısında kodu çalıştırır.

Çalışma zamanında, içerik, Kullanıcı akışınız veya özel ilkenizde belirttiğiniz bir URL 'den yüklenir. Kullanıcı deneyimindeki her sayfa, içeriğini o sayfa için belirttiğiniz URL 'den yükler. URL 'nizden içerik yüklendikten sonra, Azure AD B2C tarafından yerleştirilen bir HTML parçası ile birleştirilir ve sayfa müşterinize görüntülenir.

Kullanıcı arabirimini özelleştirmek için kendi HTML ve CSS dosyalarınızı kullanmadan önce aşağıdaki kılavuzu gözden geçirin:

- Azure AD B2C HTML içeriğini sayfalarınıza **birleştirir** . Azure AD B2C sağladığı varsayılan içeriği kopyalamayın ve değiştirmeyi denemeyin. HTML içeriğinizi sıfırdan oluşturmak ve varsayılan içeriği başvuru olarak kullanmak en iyisidir.
- **JavaScript** , hem [Kullanıcı akışları](user-flow-javascript-overview.md) hem de [özel ilkeler](javascript-samples.md)için özel içeriğinize eklenebilir.
- Desteklenen **tarayıcı sürümleri** şunlardır:
  - Internet Explorer 11, 10 ve Microsoft Edge
  - Internet Explorer 9 ve 8 için sınırlı destek
  - Google Chrome 42,0 ve üzeri
  - Mozilla Firefox 38,0 ve üzeri
- HTML 'nize **form etiketleri** eklemeyin. Form etiketleri, Azure AD B2C tarafından eklenen HTML tarafından oluşturulan POST işlemlerini kesintiye uğrayor.

### <a name="where-do-i-store-ui-content"></a>UI içeriğini nerede depolayabilirim?

Kullanıcı arabirimini özelleştirmek için kendi HTML ve CSS dosyalarınızı kullanırken, Kullanıcı arabirimi içeriğinizi CORS 'yi destekleyen genel kullanıma açık bir HTTPS uç noktası üzerinden barındırabilirsiniz. Örneğin, [Azure Blob depolama](../storage/blobs/storage-blobs-introduction.md), Web sunucuları, CDNS, AWS S3 veya dosya paylaşım sistemleri.

Önemli nokta, içeriği CORS 'yi etkin olan genel kullanıma açık bir HTTPS uç noktası üzerinde barındırmanıza bağlıdır. İçeriğinizi belirttiğinizde, mutlak bir URL kullanmanız gerekir.

## <a name="get-started-with-custom-html-and-css"></a>Özel HTML ve CSS ile çalışmaya başlama

Bu yönergeleri izleyerek, Kullanıcı deneyimi sayfalarınızda kendi HTML ve CSS 'nizi kullanmaya başlayın.

- İçinde herhangi bir yerde bulunan boş `<div id="api"></div>` bir öğeyle düzgün biçimlendirilmiş HTML içeriği oluşturun. `<body>` Bu öğe Azure AD B2C içeriğin nereye ekleneceğini işaretler. Aşağıdaki örnek, en az bir sayfa göstermektedir:

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Azure AD B2C, sayfanıza eklediği Kullanıcı Arabirimi öğelerine stil eklemek için CSS kullanın. Aşağıdaki örnek, kayıt eklenmiş HTML öğelerinin ayarlarını da içeren basit bir CSS dosyasını göstermektedir:

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

- İçeriğinizi bir HTTPS uç noktasında (CORS ile izin verilir) barındırın. CORS yapılandırılırken hem GET hem de OPTIONS istek yöntemlerinin etkinleştirilmesi gerekir.
- Oluşturduğunuz içeriği kullanmak için bir Kullanıcı akışı veya özel ilke oluşturun veya düzenleyin.

### <a name="html-fragments-from-azure-ad-b2c"></a>Azure AD B2C HTML parçaları

Aşağıdaki tabloda, Azure AD B2C içeriklerde bulunan `<div id="api"></div>` öğe ile birleştirmekte olan HTML parçaları listelenmektedir.

| Sayfa ekleniyor | HTML açıklaması |
| ------------- | ------------------- |
| Kimlik sağlayıcı seçimi | Müşterinin kaydolma veya oturum açma sırasında aralarından seçim yapabileceğiniz kimlik sağlayıcılarının düğmelerinin bir listesini içerir. Bu düğmeler Facebook, Google veya yerel hesaplar gibi sosyal kimlik sağlayıcılarını (e-posta adresine veya Kullanıcı adına göre) içerir. |
| Yerel hesap kaydı | Bir e-posta adresine veya Kullanıcı adına göre yerel hesap kaydolma için bir form içerir. Form, metin girişi kutusu, parola giriş kutusu, radyo düğmesi, tek seçim açılan kutuları ve çoklu seçim onay kutuları gibi farklı giriş denetimleri içerebilir. |
| Sosyal hesap kaydolma | Facebook veya Google gibi bir sosyal kimlik sağlayıcısından mevcut bir hesap kullanılarak kaydolurken görünebilir. Bir kaydolma formu kullanılarak müşteriden ek bilgiler toplandığında kullanılır. |
| Birleşik kaydolma veya oturum açma | Facebook, Google veya yerel hesaplar gibi sosyal kimlik sağlayıcılarını kullanabilecek müşterilerin hem kaydolma hem de oturum açma sürümlerini işler. |
| Multi-factor authentication | Müşteriler, kaydolma veya oturum açma sırasında telefon numaralarını (metin veya ses kullanarak) doğrulayabilirler. |
| Hata | Müşteriye hata bilgilerini sağlar. |

## <a name="localize-content"></a>İçeriği yerelleştirin

Azure AD B2C kiracınızda [dil özelleştirmesini](active-directory-b2c-reference-language-customization.md) etkinleştirerek HTML içeriğinizi yerelleştirebilirsiniz. Bu özelliği etkinleştirmek Azure AD B2C, OpenID Connect parametresini `ui-locales` uç noktanıza iletmesine izin verir. İçerik sunucunuz, dile özgü HTML sayfaları sağlamak için bu parametreyi kullanabilir.

İçerik, kullanılan yerel ayara bağlı olarak farklı yerlerden çekeklenebilir. CORS etkin uç noktanıza, belirli diller için içerik barındırmak üzere bir klasör yapısı ayarlarsınız. Joker karakter değerini `{Culture:RFC5646}`kullanırsanız, doğru olanı çağıracaksınız.

Örneğin, özel sayfa URI 'niz şöyle görünebilir:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Buradan içerik çekerek sayfayı Fransızca olarak yükleyebilirsiniz:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Örnekler

GitHub 'da [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) deposunda birkaç örnek şablon dosyası bulabilirsiniz.

Şablonlarda örnek HTML ve CSS dosyaları [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) dizininde bulunur.

## <a name="next-steps"></a>Sonraki adımlar

- **Kullanıcı akışları**kullanıyorsanız, Kullanıcı arabiriminizi öğreticiyle özelleştirmeye başlayabilirsiniz:

    [Azure Active Directory B2C ' de uygulamalarınızın Kullanıcı arabirimini özelleştirin](tutorial-customize-ui.md).
- **Özel ilkeler**kullanıyorsanız, şu makaleyle Kullanıcı arabirimini özelleştirmeye başlayabilirsiniz:

    [Azure Active Directory B2C özel bir ilke kullanarak uygulamanızın kullanıcı arabirimini özelleştirin](active-directory-b2c-ui-customization-custom.md).
