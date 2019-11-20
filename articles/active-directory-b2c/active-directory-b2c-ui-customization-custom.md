---
title: Azure Active Directory B2C | içinde özel bir ilke kullanarak uygulamanızın kullanıcı arabirimini özelleştirme | Microsoft Docs
description: Azure Active Directory B2C özel bir ilke kullanarak Kullanıcı arabirimini özelleştirme hakkında bilgi edinin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1ef4ddc422041de623b96f3a0c85f067427cacd7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374221"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Azure Active Directory B2C özel bir ilke kullanarak uygulamanızın kullanıcı arabirimini özelleştirme

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makaleyi tamamladıktan sonra, marka ve görünümünüzdeki bir kaydolma ve oturum açma özel ilkesine sahip olacaksınız. Azure Active Directory B2C (Azure AD B2C) sayesinde, kullanıcılara sunulan HTML ve CSS içeriğinin neredeyse tam denetimini alırsınız. Özel bir ilke kullandığınızda, Azure portal denetimleri kullanmak yerine, XML 'de UI özelleştirmesini yapılandırırsınız.

## <a name="prerequisites"></a>Önkoşullar

[Özel ilkelerle çalışmaya başlama](active-directory-b2c-get-started-custom.md)bölümündeki adımları uygulayın. Kaydolma ve yerel hesaplarla oturum açma için çalışan bir özel ilkenize sahip olmanız gerekir.

## <a name="page-ui-customization"></a>Sayfa Kullanıcı arabirimi özelleştirmesi

Sayfa UI özelleştirmesi özelliğini kullanarak, herhangi bir özel ilkenin görünümünü özelleştirebilirsiniz. Ayrıca bu sayede uygulamanızla Azure AD B2C arasında marka ve görsel tutarlılığı sağlayabilirsiniz.

Şu şekilde çalışır: Azure AD B2C kodu müşterinizin tarayıcısında çalıştırır ve [çıkış noktaları arası kaynak paylaşımı (CORS)](https://www.w3.org/TR/cors/)adlı modern bir yaklaşım kullanır. İlk olarak, özel ilkede özelleştirilmiş HTML içeriğiyle bir URL belirtirsiniz. Azure AD B2C, kullanıcı arabirimi öğelerini URL'nizden yüklenen HTML içeriğiyle birleştirdikten sonra sayfayı müşteriye gösterir.

## <a name="create-your-html5-content"></a>HTML5 içeriğinizi oluşturma

Başlığında ürününüzün marka adıyla HTML içeriği oluşturun.

1. Aşağıdaki HTML kod parçacığını kopyalayın. *@No__t-1div ID = "api" \> @ no__t-3/div @ no__t-4* adlı *\<body @ no__t-7* etiketleri içinde bulunan boş bir öğe ile iyi biçimlendirilmiş HTML5. Bu öğe Azure AD B2C içeriğin nereye ekleneceğini gösterir.

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

1. Kopyalanmış parçacığı bir metin düzenleyicisine yapıştırın, sonra dosyayı *Customize-ui. html*olarak kaydedin.

> [!NOTE]
> Login.microsoftonline.com kullanırsanız, güvenlik kısıtlamaları nedeniyle HTML form öğeleri kaldırılır. Özel HTML içerikinizdeki HTML form öğelerini kullanmak istiyorsanız lütfen b2clogin.com kullanın. Diğer avantajlar için bkz. [B2clogin.com kullanma](b2clogin.md) .

## <a name="create-an-azure-blob-storage-account"></a>Azure Blob depolama hesabı oluşturma

>[!NOTE]
> Bu makalede, içeriğimizi barındırmak için Azure Blob depolamayı kullanırız. İçeriğinizi bir Web sunucusunda barındırabilirsiniz, ancak [Web SUNUCUNUZDA CORS 'yi etkinleştirmeniz](https://enable-cors.org/server.html)gerekir.

Bu HTML içeriğini blob depolamada barındırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
1. **Hub** menüsünde **Yeni** > **depolama** > **depolama hesabı**' nı seçin.
1. Depolama hesabınız için bir **abonelik** seçin.
1. Bir **kaynak grubu** oluşturun veya var olan bir grubu seçin.
1. Depolama hesabınız için benzersiz bir **ad** girin.
1. Depolama hesabınız için **coğrafi konumu** seçin.
1. **Dağıtım modeli** **Kaynak Yöneticisi**kalabilirler.
1. **Performans** **Standart**kalabilirler.
1. **Hesap türünü** **BLOB depolama alanı**olarak değiştirin.
1. **Çoğaltma** , **RA-GRS**kalabilir.
1. **Erişim katmanı** , **sık**kalabilir.
1. Depolama hesabı oluşturmak için **gözden geçir + oluştur** ' a tıklayın.
    Dağıtım tamamlandıktan sonra, **depolama hesabı** sayfası otomatik olarak açılır.

## <a name="create-a-container"></a>Bir kapsayıcı oluşturma

Blob depolamada ortak bir kapsayıcı oluşturmak için aşağıdaki adımları uygulayın:

1. Sol taraftaki menüdeki **BLOB hizmeti** altında **Bloblar**' ı seçin.
1. **+ Kapsayıcı**' ya tıklayın.
1. **Ad**için *kök*girin. Bu, seçtiğimiz bir ad olabilir, örneğin *wingtiptoys*, ancak basitlik için bu örnekteki *kökü* kullanırız.
1. **Genel erişim düzeyi**için **BLOB**' u ve ardından **Tamam**' ı seçin.
1. Yeni kapsayıcıyı açmak için **kök** öğesine tıklayın.
1. **Karşıya Yükle**'ye tıklayın.
1. **Dosya Seç**' in yanındaki klasör simgesine tıklayın.
1. Sayfasına gidin ve sayfa UI özelleştirmesi bölümünde daha önce oluşturduğunuz **Customize-ui. html** dosyasını seçin.
1. Bir alt klasöre yüklemek istiyorsanız, **Gelişmiş** ' i genişletin ve klasöre **Yükle ' ye**bir klasör adı girin.
1. **Karşıya Yükle**’yi seçin.
1. Karşıya yüklediğiniz **Customize-ui. html** blobu seçin.
1. **URL metin kutusunun** SAĞıNDA, URL 'yi panonuza kopyalamak Için **Panoya Kopyala** simgesini seçin.
1. Web tarayıcısında, karşıya yüklediğiniz Blobun erişilebilir olduğunu doğrulamak için kopyaladığınız URL 'ye gidin. Erişilemezse, örneğin, `ResourceNotFound` hatasıyla karşılaşırsanız, kapsayıcı erişim türünün **BLOB**olarak ayarlandığından emin olun.

## <a name="configure-cors"></a>CORS Yapılandırma

Aşağıdaki adımları gerçekleştirerek, çıkış noktaları arası kaynak paylaşımı için blob depolamayı yapılandırın:

1. Menüsünde **CORS**' yi seçin.
1. **Izin verilen çıkış noktaları**için `https://your-tenant-name.b2clogin.com` girin. @No__t-0 ' yı Azure AD B2C kiracınızın adıyla değiştirin. Örneğin, `https://fabrikam.b2clogin.com`. Kiracı adınızı girerken tüm küçük harfleri kullanmanız gerekir.
1. **Izin verilen Yöntemler**için `GET` ve `OPTIONS` ' yi seçin.
1. **Izin verilen üst bilgiler**için bir yıldız işareti (*) girin.
1. **Gösterilen üstbilgiler**için bir yıldız işareti (*) girin.
1. **Maksimum yaş**için 200 girin.
1. **Kaydet** düğmesine tıklayın.

## <a name="test-cors"></a>Test CORS

Aşağıdaki adımları gerçekleştirerek hazırlandığınızı doğrulayın:

1. [Www.test-CORS.org](https://www.test-cors.org/) Web sitesine gidin ve ardından **uzak URL** kutusuna URL 'yi yapıştırın.
1. **Istek gönder**' e tıklayın.
    Bir hata alırsanız, [CORS ayarlarınızın](#configure-cors) doğru olduğundan emin olun. Ayrıca CTRL + SHIFT + P tuşlarına basarak tarayıcı önbelleğinizi temizlemeniz veya özel bir gözatma oturumu açmanız gerekebilir.

## <a name="modify-the-extensions-file"></a>Uzantılar dosyasını değiştirme

UI özelleştirmesini yapılandırmak için, **ContentDefinition** ve onun alt öğelerini temel dosyadan uzantılar dosyasına kopyalayın.

1. İlkenizin temel dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/` **`TrustFrameworkBase.xml`** </em> . Bu, önkoşullu olarak elde etmeniz gereken özel ilke başlangıç paketine eklenen ilke dosyalarından biridir, [özel ilkeleri](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom)kullanmaya başlayın.
1. **ContentDefinitions** öğesinin tüm içeriğini arayın ve kopyalayın.
1. Uzantı dosyasını açın. Örneğin, *TrustFrameworkExtensions. xml*. **Buildingblocks** öğesi için arama yapın. Öğe yoksa, ekleyin.
1. **Buildingblocks** öğesinin bir alt öğesi olarak kopyaladığınız **ContentDefinitions** öğesinin tüm içeriğini yapıştırın.
1. Kopyaladığınız XML 'de `Id="api.signuporsignin"` içeren **ContentDefinition** öğesini arayın.
1. **Loaduri** değerini, depolama ALANıNA yüklediğiniz HTML dosyasının URL 'si ile değiştirin. Örneğin, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Özel ilkeniz aşağıdaki gibi görünmelidir:

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

## <a name="upload-your-updated-custom-policy"></a>Güncelleştirilmiş özel ilkenizi karşıya yükleyin

1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Kimlik deneyimi çerçevesini**seçin.
1. **Tüm ilkeler**' e tıklayın.
1. **Ilkeyi karşıya yükle**' ye tıklayın.
1. Daha önce değiştirdiğiniz uzantıları dosyasını karşıya yükleyin.

## <a name="test-the-custom-policy-by-using-run-now"></a>**Şimdi Çalıştır** 'ı kullanarak özel ilkeyi test etme

1. **Azure AD B2C** sayfasında, **Tüm ilkeler**' e gidin.
1. Karşıya yüklediğiniz özel ilkeyi seçin ve **Şimdi Çalıştır** düğmesine tıklayın.
1. Bir e-posta adresi kullanarak kaydolabilirsiniz.

## <a name="reference"></a>Başvuru

### <a name="sample-templates"></a>Örnek şablonlar
UI özelleştirmesi için örnek şablonları buradan bulabilirsiniz:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Sample_templates/Wingtip klasörü aşağıdaki HTML dosyalarını içerir:

| HTML5 şablonu | Açıklama |
|----------------|-------------|
| *PhoneFactor. html* | Bu dosyayı Multi-Factor Authentication sayfası için şablon olarak kullanın. |
| *ResetPassword. html* | Parolayı unuttum sayfası için bu dosyayı şablon olarak kullanın. |
| *selfassırted. html* | Bu dosyayı bir sosyal hesap kaydolma sayfası, yerel hesap kaydolma sayfası veya yerel hesap oturum açma sayfası için şablon olarak kullanın. |
| *Birleşik. html* | Bu dosyayı Birleşik kaydolma veya oturum açma sayfası için şablon olarak kullanın. |
| *updateprofile. html* | Bu dosyayı profil güncelleştirme sayfası için şablon olarak kullanın. |

Aşağıda, örneği kullanma adımları verilmiştir:

1. Yerel makinenizde depoyu kopyalayın. Sample_templates altında bir şablon klasörü seçin. @No__t-0 veya `contoso` ' i kullanabilirsiniz.
1. @No__t-0, `fonts` ve `images` klasörlerinin altındaki tüm dosyaları önceki bölümlerde açıklandığı gibi BLOB depolamaya yükleyin.
1. Ardından, her bir @no__t -0. html dosyasını `wingtip` veya `contoso` ' nin kökünde açın (ilk adımda seçtiğinizden) ve "http://localhost" öğesinin tüm örneklerini adım 2 ' de karşıya yüklediğiniz CSS, resim ve yazı tipi dosyalarının URL 'Leriyle değiştirin.
1. @No__t -0. html dosyalarını kaydedin ve BLOB depolamaya yükleyin.
1. Şimdi uzantılar dosyasını [değiştirme](#modify-the-extensions-file)bölümünde belirtildiği gibi uzantılar dosyasını değiştirin.
1. Eksik yazı tiplerini, görüntüleri veya CSS 'yi görürseniz, uzantılar ilkesi ve @no__t -0. html dosyaları içindeki başvurularınızı kontrol edin.

### <a name="content-definition-ids"></a>İçerik tanımı kimlikleri

Kaydolma veya oturum açma özel ilkesini değiştirme bölümünde, `api.idpselections` için içerik tanımını yapılandırdınız. Azure AD B2C Identity Experience Framework ve açıklamaları tarafından tanınan içerik tanımı kimliklerinin tam kümesi aşağıdaki tabloda verilmiştir:

| İçerik tanımı KIMLIĞI | Açıklama |
|-----------------------|-------------|
| *api. Error* | **Hata sayfası**. Bu sayfa bir özel durum veya hata ile karşılaşıldığında görüntülenir. |
| *api. ıdpseçimlerin* | **Kimlik sağlayıcısı seçim sayfası**. Bu sayfa, kullanıcının oturum açma sırasında aralarından seçim yapabileceğiniz kimlik sağlayıcılarının bir listesini içerir. Bu seçenekler, kurumsal kimlik sağlayıcılarıdır, Facebook ve Google + gibi sosyal kimlik sağlayıcıları ya da yerel hesaplar. |
| *api. ıdpseçimlerin. Signup* | **Kaydolma Için kimlik sağlayıcısı seçimi**. Bu sayfa, kullanıcının kaydolma sırasında aralarından seçim yapabileceğiniz kimlik sağlayıcılarının bir listesini içerir. Bu seçenekler, kurumsal kimlik sağlayıcılarıdır, Facebook ve Google + gibi sosyal kimlik sağlayıcıları ya da yerel hesaplar. |
| *api. localaccountpasswordreset* | **Parolayı unuttum sayfası**. Bu sayfa, kullanıcının parola sıfırlama işlemini başlatmak için tamamlaması gereken bir form içerir.  |
| *api. localaccountsignın* | **Yerel hesap oturum açma sayfası**. Bu sayfa, bir e-posta adresine veya Kullanıcı adına dayalı bir yerel hesapla oturum açmak için bir oturum açma formu içerir. Form bir metin girişi kutusu ve parola giriş kutusu içerebilir. |
| *api. localaccountsignup* | **Yerel hesap kaydolma sayfası**. Bu sayfa, bir e-posta adresini veya Kullanıcı adını temel alan bir yerel hesaba kaydolma için bir kayıt formu içerir. Form, metin girişi kutusu, parola girişi kutusu, radyo düğmesi, tek seçim açılan kutuları ve çoklu seçim onay kutuları gibi çeşitli giriş denetimleri içerebilir. |
| *api. phonefactor* | **Multi-Factor Authentication sayfası**. Bu sayfada, kullanıcılar telefon numaralarını (metin veya ses kullanarak) kaydolma veya oturum açma sırasında doğrulayabilirler. |
| *api. selfasted* | **Sosyal hesap kaydolma sayfası**. Bu sayfa, kullanıcıların Facebook veya Google + gibi bir sosyal kimlik sağlayıcısından mevcut bir hesabı kullanarak kaydolduklarında tamamlaması gereken bir kaydolma formu içerir. Bu sayfa, parola girişi alanları hariç, önceki sosyal hesap kaydolma sayfasına benzer. |
| *api. selfasserted. profileUpdate* | **Profil güncelleştirme sayfası**. Bu sayfa, kullanıcıların profilini güncelleştirmek için kullanabileceği bir form içerir. Bu sayfa, parola girişi alanları hariç sosyal hesap kaydolma sayfasına benzerdir. |
| *api. signuporsignın* | **Birleşik kaydolma veya oturum açma sayfası**. Bu sayfa, kullanıcıların kaydolma ve oturum açma öğelerinin yanı sıra kurumsal kimlik sağlayıcıları, Facebook veya Google + gibi sosyal kimlik sağlayıcıları veya yerel hesaplar tarafından kullanılabilir.  |

## <a name="next-steps"></a>Sonraki adımlar

Özelleştirilebilecek Kullanıcı arabirimi öğeleri hakkında daha fazla bilgi için bkz. [Yerleşik ilkeler IÇIN UI özelleştirmesi için başvuru kılavuzu](active-directory-b2c-reference-ui-customization.md).
