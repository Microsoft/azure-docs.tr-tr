---
title: Azure Active Directory B2C Kullanıcı arabirimini (UI) özel ilkeleri kullanarak dinamik olarak özelleştirme | Microsoft Docs
description: Çalışma zamanında dinamik olarak değişen HTML5/CSS içeriğiyle birden çok marka deneyimini destekler.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 43c0da3ca8fa4b2f74d48b0e202cc56bc8b9406c
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227216"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: Özel ilkeler kullanarak, Dinamik içerikle Kullanıcı arabirimini yapılandırma

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) özel ilkeleri kullanarak bir sorgu dizesinde bir parametre gönderebilirsiniz. Parametreyi HTML uç noktanıza ileterek sayfa içeriğini dinamik olarak değiştirebilirsiniz. Örneğin web veya mobil uygulamanızdan ilettiğiniz bir parametreye göre Azure AD B2C kaydolma veya oturum açma sayfanızdaki arka plan görüntüsünü değiştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu makalede, özel ilkeler kullanılarak Azure AD B2C Kullanıcı arabiriminin *Dinamik içerikle* nasıl özelleştirileceği konusuna odaklanılmaktadır. Başlamak için bkz. [özel Ilkede UI özelleştirmesi](active-directory-b2c-ui-customization-custom.md).

>[!NOTE]
>Azure AD B2C makalesi, [Kullanıcı arabirimi özelleştirmesini özel bir Ilkede yapılandırma](active-directory-b2c-ui-customization-custom.md), aşağıdaki temelleri anlatmaktadır:
> * Sayfa Kullanıcı arabirimi (UI) özelleştirme özelliği.
> * Örnek içeriğimizi kullanarak sayfa UI özelleştirmesi özelliğini test etmeye yönelik temel araçlar.
> * Her sayfa türünün temel kullanıcı arabirimi öğeleri.
> * Bu özelliği uygulamak için en iyi uygulamalar.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Kullanıcı yolculuğuna HTML5/CSS şablonlarına bir bağlantı ekleyin

Özel bir ilkede, içerik tanımı, belirtilen kullanıcı arabirimi adımı için kullanılan HTML5 sayfa URI 'sini tanımlar (örneğin, oturum açma veya kaydolma sayfaları). Temel ilke, HTML5 dosyalarının URI 'sine işaret ederek varsayılan görünümü tanımlar (CSS). Uzantı ilkesinde, HTML5 dosyası için LoadUri 'yi geçersiz kılarak görünümü değiştirebilirsiniz. İçerik tanımları, uygun şekilde HTML5/CSS dosyaları tarafından tanımlanan dış içeriğe yönelik URL 'Leri içerir.

`ContentDefinitions` Bölüm bir`ContentDefinition` dizi xml öğesi içerir. `ContentDefinition` Öğesinin ID özniteliği, içerik tanımıyla ilgili sayfa türünü belirtir. Diğer bir deyişle, öğesi özel bir HTML5/CSS şablonunun uygulanacağı bağlamı tanımlar. Aşağıdaki tabloda, ıEF altyapısı tarafından tanınan içerik tanımı kimlikleri ve bunlarla ilgili sayfa türleri açıklanmaktadır.

| İçerik tanımı KIMLIĞI | Varsayılan HTML5 şablonu| Açıklama |
|-----------------------|--------|-------------|
| *api.error* | [Exception. cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hata sayfası**. Bu sayfa bir özel durum veya hata ile karşılaşıldığında görüntülenir. |
| *api. ıdpseçimlerin* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Kimlik sağlayıcısı seçim sayfası**. Bu sayfada, kullanıcıların oturum açma sırasında aralarından seçim yapabileceğiniz kimlik sağlayıcıları listelenmektedir. Seçenekler genellikle kurumsal kimlik sağlayıcılardır, Facebook ve Google + gibi sosyal kimlik sağlayıcılarıdır veya yerel hesaplardır. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Kaydolma Için kimlik sağlayıcısı seçimi**. Bu sayfada, kullanıcıların kaydolma sırasında aralarından seçim yapabileceğiniz kimlik sağlayıcıları listelenir. Seçenekler, kurumsal kimlik sağlayıcıları, Facebook ve Google + gibi sosyal kimlik sağlayıcıları ya da yerel hesaplar. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Parolayı unuttum sayfası**. Bu sayfa, kullanıcıların parola sıfırlama işlemini başlatmak için tamamlaması gereken bir form içerir.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Yerel hesap oturum açma sayfası**. Bu sayfa, bir e-posta adresini veya Kullanıcı adını temel alan bir yerel hesapla oturum açmak için bir form içerir. Form bir metin girişi kutusu ve parola giriş kutusu içerebilir. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Yerel hesap kaydolma sayfası**. Bu sayfa, bir e-posta adresini veya Kullanıcı adını temel alan bir yerel hesaba kaydolma formu içerir. Form, metin girişi kutusu, parola girişi kutusu, radyo düğmesi, tek seçim açılan kutuları ve çoklu seçim onay kutuları gibi çeşitli giriş denetimleri içerebilir. |
| *api. phonefactor* | [çok faktörlü-1.0.0. cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication sayfası**. Bu sayfada, kullanıcılar telefon numaralarını (metin veya ses kullanarak) kaydolma veya oturum açma sırasında doğrulayabilirler. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Sosyal hesap kaydolma sayfası**. Bu sayfa, kullanıcıların bir sosyal kimlik sağlayıcısından mevcut bir hesabı kullanarak kaydolduklarında tamamlaması gereken bir form içerir. Bu sayfa, parola girişi alanları hariç, önceki sosyal hesap kaydolma sayfasına benzer. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profil güncelleştirme sayfası**. Bu sayfa, kullanıcıların profilini güncelleştirmek için erişebileceği bir form içerir. Bu sayfa, parola girişi alanları hariç sosyal hesap kaydolma sayfasına benzerdir. |
| *api.signuporsignin* | [Birleşik. html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Birleşik kaydolma veya oturum açma sayfası**. Bu sayfa Kullanıcı kayıt ve oturum açma sürecini işler. Kullanıcılar, kurumsal kimlik sağlayıcılarını, Facebook veya Google + gibi sosyal kimlik sağlayıcılarını veya yerel hesapları kullanabilir.  |

## <a name="serving-dynamic-content"></a>Dinamik içerik sunma
[Özel bir ilke için Kullanıcı arabirimi özelleştirmesini yapılandırma](active-directory-b2c-ui-customization-custom.md) makalesinde HTML5 dosyalarını Azure Blob depolamaya yüklersiniz. Bu HTML5 dosyaları statiktir ve her istek için aynı HTML içeriğini işler.

Bu makalede sorgu dizesi parametrelerini kabul edebilir ve buna uygun şekilde yanıt verebilecek bir ASP.NET Web uygulaması kullanırsınız.

Bu izlenecek yolda şunları yapabilirsiniz:
* HTML5 şablonlarınızı barındıran bir ASP.NET Core Web uygulaması oluşturun.
* Özel bir HTML5 şablonu ekleyin, _birleştirilmiş. cshtml_.
* Web uygulamanızı Azure App Service yayımlayın.
* Web uygulamanız için çıkış noktaları arası kaynak paylaşımı (CORS) ayarlayın.
* HTML5 dosyanıza işaret etmek için öğelerigeçersizkılın.`LoadUri`

## <a name="step-1-create-an-aspnet-web-app"></a>1\. adım: ASP.NET web uygulaması oluşturma

1. Visual Studio 'da **Dosya** > **Yeni** > **Proje**' yi seçerek bir proje oluşturun.

2. **Yeni proje** penceresinde,  >  **Visual C#**  **Web** > **ASP.NET Core Web uygulaması (.NET Core)** seçeneğini belirleyin.

3. Uygulamayı adlandırın (örneğin, *contoso. AADB2C. UI*) ve ardından **Tamam**' ı seçin.

    ![Yeni Visual Studio projesi oluştur](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. **Web uygulaması** şablonunu seçin.

5. Kimlik doğrulamasını **kimlik doğrulaması olmadan**ayarlayın.

    ![Web uygulaması şablonu seçin](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Seçin **Tamam** projeyi oluşturmak için.

## <a name="step-2-create-mvc-view"></a>2\. adım: MVC görünümü oluşturma
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Adım 2,1: B2C yerleşik HTML5 şablonunu indirin
Özel HTML5 şablonunuz Azure AD B2C yerleşik HTML5 şablonunu temel alır. [Birleşik. html dosyasını](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) indirebilir veya şablonu [Starter Pack](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip)'ten indirebilirsiniz. Bu HTML5 dosyasını, Birleşik bir kaydolma veya oturum açma sayfası oluşturmak için kullanırsınız.

### <a name="step-22-add-the-mvc-view"></a>Adım 2,2: MVC görünümünü ekleme
1. Görünümler/giriş klasörüne sağ tıklayın ve ardından**Yeni öğe** **ekleyin** > .

    ![Visual Studio 'da yeni öğe Ekle menü öğesi](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. **Yeni öğe Ekle-contoso. AADB2C. UI** penceresinde **Web > ASP.net**' yi seçin.

3. **MVC görünüm sayfası**' nı seçin.

4. **Ad** kutusunda, adı **Unified. cshtml**olarak değiştirin.

5. **Add (Ekle)** seçeneğini belirleyin.

    ![MVC görünümü sayfası vurgulanmış şekilde Visual Studio 'da yeni öğe Ekle iletişim kutusu](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. *Birleşik. cshtml* dosyası henüz açık değilse, açmak için dosyaya çift tıklayın ve ardından dosya içeriğini temizleyin.

7. Bu izlenecek yol için, Düzen sayfası başvurusunu kaldırdık. Aşağıdaki kod parçacığını _Unified. cshtml_öğesine ekleyin:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Azure AD B2C yerleşik HTML5 şablonundan indirdiğiniz _birleştirilmiş. cshtml_ dosyasını açın.

9. Tek at işaretleri (@) işaretini çift (@ @) ile değiştirin.

10. Dosyanın içeriğini kopyalayın ve düzen tanımının altına yapıştırın. Kodunuz şöyle görünmelidir:

    ![HTML5 eklendikten sonra birleştirilmiş. cshtml dosyası](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Adım 2,3: Arka plan görüntüsünü değiştirme

`<img>`  **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** Background_background_image değerini içeren öğeyi bulun ve ardından değeriyadakullanmakistediğinizbaşkabirarkaplangörüntüsünüdeğiştirin.`src` `ID`

![Özel background_background_image src değeri olan img öğesi](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Adım 2,4: Görünümünüzü MVC denetleyicisine ekleyin

1. **Controllers\homecontroller.cs**dosyasını açın ve aşağıdaki yöntemi ekleyin:

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Bu kod, yöntemin tarayıcıya yanıt işlemek için bir *Görünüm* şablonu dosyası kullanması gerektiğini belirtir. *Görünüm* şablonu dosyasının adını açıkça belirttiğimiz IÇIN, MVC varsayılan olarak */views/Home* klasöründeki _Unified. cshtml_ görünüm dosyasını kullanmaktır.

    _Birleşik_ yöntemi ekledikten sonra kodunuzun şöyle görünmesi gerekir:

    ![Denetleyiciyi, görünümü işleyecek şekilde değiştirin](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Web uygulamanızda hata ayıklayın ve _birleştirilmiş_ sayfanın erişilebilir olduğundan emin olun (örneğin, `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Adım 2,5: Azure'a Yayımlama
1. **Çözüm Gezgini**, **contoso. AADB2C. UI** projesine sağ tıklayın ve ardından **Yayımla**' yı seçin.

    ![Microsoft Azure App Service yayımlayın](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. **Microsoft Azure App Service** kutucuğunu seçip **Yayımla**' yı seçin.

    ![Yeni Microsoft Azure App Service oluştur](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    **App Service oluştur** penceresi açılır. Bu durumda, ASP.NET Web uygulamasını Azure 'da çalıştırmak için gerekli tüm Azure kaynaklarını oluşturmaya başlayabilirsiniz.

    > [!NOTE]
    > Yayımlama hakkında daha fazla bilgi için bkz. [Azure 'da ASP.NET Web uygulaması oluşturma](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. **Web uygulaması adı** kutusuna benzersiz bir uygulama adı yazın (geçerli karakterler a-z, a-z, 0-9 ve kısa çizgi (-) yazın. Web uygulamasının URL'si `http://<app_name>.azurewebsites.NET` şeklindedir; burada `<app_name>`, web uygulamanızın adıdır. Otomatik oluşturulmuş benzersiz adı kabul edebilirsiniz.

4. Azure kaynaklarını oluşturmaya başlamak için **Oluştur**’u seçin.

    ![App Service özellikleri sağlama](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Oluşturma işlemi tamamlandıktan sonra, sihirbaz ASP.NET Web uygulamasını Azure 'da yayımlar ve ardından uygulamayı varsayılan tarayıcıda başlatır.

5. _Birleşik_ sayfanın URL 'sini kopyalayın (örneğin, _https://< app_name >. azurewebsites. net/Home/Unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>3\. adım: Azure App Service içinde CORS 'yi yapılandırma
1. [Azure Portal](https://portal.azure.com/), **uygulama hizmetleri**' nı seçin ve ardından API uygulamanızın adını seçin.

    ![Azure portal API uygulamasını seçin](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. **Ayarlar** bölümünde, **API** bölümü altında **CORS**' yi seçin.

    ![CORS menü öğesi Azure portal App Service menüsünde vurgulanır](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. **CORS** penceresinde, **izin verilen** kaynaklar kutusunda aşağıdakilerden birini yapın:

    * JavaScript çağrılarının gelmesini sağlamak istediğiniz URL veya URL 'Leri girin. Girdiğiniz URL 'lerde tüm küçük harfleri kullanmanız gerekir.
    * Tüm kaynak etki alanlarının kabul edildiğini belirtmek için bir yıldız işareti (*) girin.

4. **Kaydet**’i seçin.

    ![Izin verilen çıkış noktaları ile birlikte yıldız vurgulanmış CORS ayarları sayfası](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    **Kaydet**' i seçtikten sonra, API uygulaması belirtilen URL 'Lerden gelen JavaScript çağrılarını kabul eder.

## <a name="step-4-html5-template-validation"></a>4\. Adım: HTML5 şablon doğrulaması
HTML5 şablonunuz kullanıma hazırlanıyor. Ancak, `ContentDefinition` kodda kullanılamaz. Özel ilkenize ekleyebilmeniz `ContentDefinition` için önce aşağıdakileri doğrulayın:
* İçeriğiniz HTML5 uyumludur ve erişilebilir.
* İçerik sunucunuz CORS için etkinleştirildi.

    >[!NOTE]
    >İçeriğinizi barındırmakta olduğunuz sitenin CORS 'yi etkinleştirdiğini ve CORS isteklerini sınaygeçirebildiğini doğrulamak için [Test-CORS.org](https://test-cors.org/) Web sitesine gidin.

* Hizmet verilen içeriğiniz **https**üzerinden güvenlidir.
* Tüm bağlantılar, CSS içeriği ve görüntüler için `https://yourdomain/content`gibi *mutlak URL 'ler*kullanıyorsunuz.

## <a name="step-5-configure-your-content-definition"></a>5\. Adım: İçerik tanımınızı yapılandırın
Yapılandırmak `ContentDefinition`için aşağıdakileri yapın:
1. İlkenizin temel dosyasını açın (örneğin, *TrustFrameworkBase. xml*).

2. Öğesini arayın ve sonra `<ContentDefinitions>` düğümün tüm içeriğini kopyalayın. `<ContentDefinitions>`

3. Uzantı dosyasını açın (örneğin, *TrustFrameworkExtensions. xml*) ve ardından `<BuildingBlocks>` öğesi için arama yapın. Öğe yoksa, ekleyin.

4. `<BuildingBlocks>` Öğesinin bir alt öğesi olarak kopyaladığınız `<ContentDefinitions>` düğümün tüm içeriğini yapıştırın.

5. Kopyaladığınız XML içinde içeren `Id="api.signuporsignin"` düğümüarayın.`<ContentDefinition>`

6. Değerini `LoadUri` _~/Tenant/default/Unified_ ile _https://< app_name >. azurewebsites. net/Home/Unified_olarak değiştirin.
    Özel ilkeniz aşağıdaki gibi görünmelidir:

    ![LoadUri öğesi vurgulanmış örnek XML kod parçacığı](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>6\. Adım: İlkeyi kiracınıza yükleyin
1. [Azure Portal](https://portal.azure.com), [Azure AD B2C kiracınızın bağlamına](active-directory-b2c-navigate-to-b2c-context.md)geçin ve ardından **Azure AD B2C**' yı seçin.

2. **Kimlik deneyimi çerçevesini**seçin.

3. **Tüm ilkeler**' i seçin.

4. **Ilkeyi karşıya yükle**' yi seçin.

5. Varsa **Ilkenin üzerine yaz** onay kutusunu seçin.

6. *TrustFrameworkExtensions. xml* dosyasını karşıya yükleyin ve doğrulamayı geçirdiğinizden emin olun.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>7\. Adım: Şimdi Çalıştır 'ı kullanarak özel ilkeyi test etme
1. **Azure AD B2C ayarları**' nı ve ardından **kimlik deneyimi çerçevesi**' ni seçin.

    >[!NOTE]
    >Şimdi Çalıştır, kiracıya en az bir uygulamanın önceden bağlı olmasını gerektirir. Uygulamaları nasıl kaydedeceğinizi öğrenmek için Azure AD B2C [Başlarken](active-directory-b2c-get-started.md) makalesine veya [uygulama kaydı](active-directory-b2c-app-registration.md) makalesine bakın.

2. Karşıya yüklediğiniz **B2C_1A_signup_signin**, bağlı olan taraf (RP) özel ilkesini açın ve **Şimdi Çalıştır**' ı seçin.
    Daha önce oluşturduğunuz arka planda özel HTML5 'i görebilmeniz gerekir.

    ![Kaydolma veya oturum açma ilkeniz](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>8\. Adım: Dinamik içerik Ekle
_Kampanyakimliği_adlı sorgu dizesi parametresine göre arka planı değiştirin. RP uygulamanız (Web ve mobil uygulamalar) Azure AD B2C parametresi gönderir. İlkeniz parametresini okur ve değerini HTML5 şablonize gönderir.

### <a name="step-81-add-a-content-definition-parameter"></a>Adım 8,1: İçerik tanımı parametresi Ekle

Aşağıdakileri yaparak `ContentDefinitionParameters` öğesini ekleyin:
1. İlkenizin *signuporsignın* dosyasını açın (örneğin, *signuporsignın. xml*).

2. Düğüm altına `UserJourneyBehaviors` düğümü ekleyin: `<DefaultUserJourney>`

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ContentDefinitionParameters>
          <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Adım 8,2: Kodunuzu bir sorgu dizesi parametresini kabul edecek şekilde değiştirin ve arka plan görüntüsünü değiştirin
HomeController `unified` metodunu, kampanyakimliği parametresini kabul edecek şekilde değiştirin. Yöntemi daha sonra parametrenin değerini denetler ve `ViewData["background"]` değişkeni uygun şekilde ayarlar.

1. *Controllers\homecontroller.cs* dosyasını açın ve ardından aşağıdaki kod parçacığını ekleyerek `unified` yöntemi değiştirin:

    ```csharp
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. `src` `@ViewData["background"]`Kimliğine `<img>` sahipöğeyibulun`background_background_image`ve değerini ile değiştirin.

    ![src değeri vurgulanmış img öğesi ](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8,3: Değişiklikleri karşıya yükleyin ve ilkenizi yayımlayın
1. Azure App Service için Visual Studio projenizi yayımlayın.

2. Azure AD B2C için *Signuporsignın. xml* ilkesini karşıya yükleyin.

3. Karşıya yüklediğiniz RP özel ilkesini **B2C_1A_signup_signin**açın ve **Şimdi Çalıştır**' ı seçin.
    Daha önce görüntülenen arka plan görüntüsünü görmeniz gerekir.

4. Tarayıcının adres çubuğundan URL 'YI kopyalayın.

5. URI 'ye _Kampanya NID_ sorgu dizesi parametresini ekleyin. Örneğin, aşağıdaki görüntüde `&campaignId=hawaii`gösterildiği gibi ekleyin:

    ![Kampanyakimliği sorgu dizesi parametresi vurgulanmış URI](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Hawaii arka plan resmini göstermek için **ENTER** ' u seçin.

    ![Hawaii resmi özel arka planı ile oturum açma sayfası](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Değeri *Tokyo*olarak değiştirin ve **ENTER**' u seçin.
    Tarayıcı, Tokyo arka planını görüntüler.

    ![Tokyo görüntüsü özel arka planı ile kaydolma oturum açma sayfası](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>9\. Adım: Kullanıcı yolculuğunun geri kalanını değiştirme
Oturum açma sayfasında **Şimdi kaydolun** bağlantısını seçerseniz, tarayıcı tanımladığınız görüntüyü değil, varsayılan arka plan görüntüsünü görüntüler. Yalnızca kaydolma veya oturum açma sayfasını değiştirdiğiniz için bu davranış ortaya çıkar. Kendi kendini onaylama içerik tanımlarının geri kalanını değiştirmek için:
1. "2. adım" bölümüne geri dönün ve şunları yapın:

    a. *Selfasted* dosyasını indirin.

    b. Dosya içeriğini kopyalayın.

    c. Yeni bir görünüm oluşturun, *selfasted*.

    d. **Ana** denetleyiciye *selfasted* ekleyin.

2. "4. adım" bölümüne geri dönün ve şunları yapın:

    a. Uzantı ilkenizde, `<ContentDefinition>` , ve `Id="api.selfasserted"` `Id="api.localaccountsignup"` `Id="api.localaccountpasswordreset"`içeren düğümü bulun.

    b. Özniteliği, `LoadUri` *selfasserted* URI 'niz olarak ayarlayın.

3. "Adım 8,2" bölümüne dönün ve kodunuzu sorgu dizesi parametrelerini kabul edecek şekilde değiştirin, ancak bu kez *selfasserted* işlevine.

4. *TrustFrameworkExtensions. xml* ilkesini karşıya yükleyin ve doğrulamayı geçirdiğinizden emin olun.

5. İlke testini çalıştırın ve ardından sonucu görmek için **Şimdi kaydolun** ' ı seçin.

## <a name="optional-download-the-complete-policy-files-and-code"></a>Seçim Tüm ilke dosyalarını ve kodunu indirin
* [Özel ilkeleri kullanmaya başlama talimatlarını](active-directory-b2c-get-started-custom.md) tamamladıktan sonra, kendi özel ilke dosyalarınızı kullanarak senaryonuzu oluşturmanızı öneririz. Başvurunuz için [örnek ilke dosyaları](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)sağladık.
* [Başvuru Için örnek Visual Studio çözümünden](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)tüm kodu indirebilirsiniz.




