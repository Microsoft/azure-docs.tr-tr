---
title: Özel ilkelerle Kullanıcı yolculuğunun Kullanıcı ARABIRIMINI özelleştirme | Microsoft Docs
description: Azure Active Directory B2C özel ilkeler hakkında bilgi edinin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7e4714de9868dbd540e2e662b22a22da6df6514b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147541"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Özel ilkelerle Kullanıcı yolculuğunun Kullanıcı ARABIRIMINI özelleştirme

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Bu makale, Kullanıcı arabirimi özelleştirmesinin nasıl çalıştığını ve kimlik deneyimi çerçevesini kullanarak Azure AD B2C özel ilkelerle nasıl etkinleştireceğinizi gösteren gelişmiş bir açıklamadır.


Sorunsuz bir kullanıcı deneyimi, her işletmeden müşteriye çözüm için bir anahtardır. Sorunsuz bir kullanıcı deneyimi, cihaz veya tarayıcıda, kullanıcının hizmet üzerinden yolculuğun kullandıkları müşteri hizmetinden ayırt edilemeyen bir şekilde olmadığı bir deneyimdir.

## <a name="understand-the-cors-way-for-ui-customization"></a>UI özelleştirmesi için CORS yöntemini anlayın

Azure AD B2C, özel ilkelerinizi kullanarak Azure AD B2C tarafından sunulan ve görüntülenen çeşitli sayfalarda kullanıcı deneyiminin (UX) görünümünü özelleştirmenizi sağlar.

Bu amaçla Azure AD B2C, tüketicinizin tarayıcısında kod çalıştırır ve HTML5/CSS 'nizi işaret etmek üzere özel bir ilkede belirttiğiniz belirli bir URL 'den özel içerik yüklemek için modern ve standart yaklaşım [çıkış noktaları arası kaynak paylaşımı (CORS)](https://www.w3.org/TR/cors/) kullanır şablondan. CORS, bir Web sayfasındaki yazı tipi gibi kısıtlanmış kaynakların, kaynağın kaynaklandığı etki alanı dışındaki başka bir etki alanından istenmek için izin veren bir mekanizmadır.

Şablon sayfalarının, sınırlı metin ve görüntüleri verdiğiniz çözüme ait olduğu, ancak sorunsuz bir deneyim elde etmek için en fazla zorluklara sahip olduğu ve bu sayede, CORS 'nin HTML5 ve CSS 'yi destekler ve şunları yapmanıza olanak sağlar:

- İçeriği barındırın ve çözüm, istemci tarafı betiği kullanarak denetimlerini çıkartır.
- Düzen ve kullanım için her piksel üzerinde tam denetime sahip olmak.

Uygun şekilde HTML5/CSS dosyaları oluşturarak istediğiniz kadar çok içerik sayfası sağlayabilirsiniz.

> [!NOTE]
> Güvenlik nedenleriyle JavaScript 'in kullanımı Şu anda özelleştirme için engelleniyor. 

HTML5/CSS şablonlarınızın her birinde, HTML veya içerik sayfasındaki gerekli `<div id="api">` öğesine karşılık gelen bir *tutturucu* öğesi sağlarsınız. Azure AD B2C, tüm içerik sayfalarında bu özel DIV 'e sahip olmasını gerektirir.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

Sayfa için Azure AD B2C ilişkili içerik bu div içine eklenir, ancak sayfanın geri kalanı denetim altına alırken. Azure AD B2C JavaScript kodu içeriğinizi çeker ve HTML 'yi bu özel div öğesine ekler. Azure AD B2C aşağıdaki denetimleri uygun şekilde çıkarır: Account Chooser Control, denetimleri oturum açma, Multi-Factor (Şu anda telefon tabanlı) denetimleri ve öznitelik koleksiyonu denetimleri. Azure AD B2C, tüm denetimlerin HTML5 uyumlu ve erişilebilir olmasını, tüm denetimlerin tamamen stillenmesini ve bir denetim sürümünün giriş yapılmasını sağlar.

Birleştirilen içerik, sonunda tüketicinizin dinamik belgesi olarak görüntülenir.

Her şeyin beklendiği gibi çalıştığından emin olmak için şunları yapmanız gerekir:

- İçeriğinizin HTML5 uyumlu ve erişilebilir olduğundan emin olun
- İçerik sunucunuzun CORS için etkinleştirildiğinden emin olun.
- HTTPS üzerinden içerik sunar.
- Tüm bağlantılar ve CSS içeriği için `https://yourdomain/content` gibi mutlak URL 'LERI kullanın.

> [!TIP]
> İçeriğinizi barındırmakta olduğunuz sitenin CORS 'yi etkinleştirdiğini ve CORS isteklerini test ettiğini doğrulamak için, site https://test-cors.org/ kullanabilirsiniz. Bu site sayesinde, CORS isteğini uzak sunucuya gönderebilir (CORS 'nin desteklenip desteklenmediğini test etmek için) veya CORS isteğini bir test sunucusuna gönderebilirsiniz (CORS 'nin belirli özelliklerini incelemek için).

> [!TIP]
> Site https://enable-cors.org/ CORS 'de faydalı kaynaklardan daha fazlasını da oluşturur.

Bu CORS tabanlı yaklaşım sayesinde, son kullanıcıların uygulamanız ve Azure AD B2C tarafından sunulan sayfalar arasında tutarlı deneyimleri vardır.

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Bir önkoşul olarak, bir depolama hesabı oluşturmanız gerekir. Azure Blob depolama hesabı oluşturmak için bir Azure aboneliğine ihtiyacınız vardır. [Azure Web sitesinde](https://azure.microsoft.com/pricing/free-trial/)ücretsiz deneme sürümü kaydolabilirsiniz.

1. Bir gözatma oturumu açın ve [Azure Portal](https://portal.azure.com)gidin.
2. Yönetici kimlik bilgilerinizle oturum açın.
3. **Depolama > ** **depolama hesabı** > **kaynak oluştur ' a** tıklayın.  **Depolama hesabı oluştur** bölmesi açılır.
4. **Ad**alanına depolama hesabı için bir ad girin, örneğin, *contoso369b2c*. Bu değer daha sonra *storageAccountName*olarak adlandırılır.
5. Fiyatlandırma Katmanı, kaynak grubu ve abonelik için uygun seçimleri seçin. **Startboard Için sabitle** seçeneğinin işaretli olduğundan emin olun. **Oluştur**’a tıklayın.
6. Başlangıç panosuna dönün ve oluşturduğunuz depolama hesabına tıklayın.
7. **Hizmetler** bölümünde **Bloblar**' a tıklayın. Bir **BLOB hizmeti bölmesi** açılır.
8. **+ Kapsayıcı**' ya tıklayın.
9. **Ad**alanına kapsayıcı için bir ad sağlayın, örneğin *B2C*. Bu değer daha sonra *ContainerName*olarak adlandırılır.
9. **Erişim türü**olarak **BLOB** ' u seçin. **Oluştur**’a tıklayın.
10. Oluşturduğunuz kapsayıcı, **BLOB hizmeti bölmesindeki**listede görüntülenir.
11. **Bloblar** bölmesini kapatın.
12. **Depolama hesabı bölmesinde**, **anahtar** simgesine tıklayın. Bir **erişim tuşları bölmesi** açılır.  
13. **KEY1**değerini yazın. Bu değer daha sonra *KEY1*olarak adlandırılır.

## <a name="downloading-the-helper-tool"></a>Yardımcı aracı indiriliyor

1.  Yardım aracını [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip)'dan indirin.
2.  *B2C-AzureBlobStorage-Client-Master. zip* dosyasını yerel makinenize kaydedin.
3.  Yerel diskinizdeki B2C-AzureBlobStorage-Client-master. zip dosyasının içeriğini (örneğin, altında *B2C-AzureBlobStorage-Client-Master* klasörü oluşturan **UI-Customization-Pack** klasörü) ayıklayın.
4.  Bu klasörü açın ve içindeki *B2CAzureStorageClient. zip* arşiv dosyasının içeriğini ayıklayın.

## <a name="upload-the-ui-customization-pack-sample-files"></a>UI-özelleşme paketi örnek dosyalarını karşıya yükleme

1.  Windows Explorer 'ı kullanarak, önceki bölümde oluşturulan *UI-Özelleştirme-paketi* klasörü altında bulunan *B2C-AzureBlobStorage-Client-Master* klasörüne gidin.
2.  *B2CAzureStorageClient. exe* dosyasını çalıştırın. Bu program, belirttiğiniz dizindeki tüm dosyaları depolama hesabınıza yükler ve bu dosyalar için CORS erişimini etkinleştirir.
3.  İstendiğinde, şunu belirtin: a.  Depolama hesabınızın adı, *storageAccountName*, örneğin, *contoso369b2c*.
    b.  Azure Blob depolama alanınızı birincil erişim anahtarı, *KEY1*, örneğin *contoso369b2c*.
    c.  Depolama Blobu depolama kapsayıcının adı, *ContainerName*, örneğin *B2C*.
    d.  Örneğin, *Starter-Pack* örnek dosyalarının yolu *... \B2CTemplates\wingtiptoys*.

Yukarıdaki adımları izlediyseniz, kurgusal şirket için *UI-Özelleştirme-PAKETININ* HTML5 ve CSS **dosyaları artık depolama** hesabınıza işaret ediyor.  Azure portal ilişkili kapsayıcı bölmesini açarak içeriğin doğru şekilde karşıya yüklendiğini doğrulayabilirsiniz. Alternatif olarak, bir tarayıcıdan sayfaya erişerek içeriğin doğru bir şekilde karşıya yüklendiğini doğrulayabilirsiniz. Daha fazla bilgi için bkz. [Azure Active Directory B2C: sayfa Kullanıcı arabirimi (UI) özelleştirme özelliğini göstermek için kullanılan bir yardımcı araç](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Depolama hesabının CORS özelliğinin etkin olduğundan emin olun

İçeriğinizi Azure AD B2C için, CORS (çıkış noktaları arası kaynak paylaşımı) uç noktanıza etkin olmalıdır. Bunun nedeni, içeriğinizin etki alanından farklı bir etki alanında barındırılması Azure AD B2C.

İçeriğinizi barındırmakta olduğunuz depolamanın CORS 'nin etkin olduğunu doğrulamak için aşağıdaki adımlara ilerleyin:

1. Bir gözatma oturumu açın ve Depolama hesabınızdaki konumunun tam URL 'sini kullanarak *Unified. html* sayfasına gidin `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Örneğin, https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. https://test-cors.org gidin. Bu site, kullanmakta olduğunuz sayfanın CORS özellikli olduğunu doğrulamanıza izin verir.  
   <!--
   ![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
   -->

3. **Uzak URL**'de, Unified. html içeriğiniz için tam URL 'yi girin ve **isteği gönder**' e tıklayın.
4. **Sonuçlar** bölümündeki çıkışın *XHR durumu: 200*içerdiğini doğrulayın. Bu, CORS 'nin etkinleştirildiğini gösterir.
   <!--
   ![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
   -->
   Depolama hesabı artık, *Başlangıç paketinden*aşağıdaki wingtiptoys şablonlarını içeren çizimde *B2C* adlı bir blob kapsayıcısı içermelidir.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

Aşağıdaki tablo, önceki HTML5 sayfalarının amacını açıklamaktadır.

| HTML5 şablonu | Açıklama |
|----------------|-------------|
| *PhoneFactor. html* | Bu sayfa, Multi-Factor Authentication sayfası için şablon olarak kullanılabilir. |
| *ResetPassword. html* | Bu sayfa, Unutulan parola sayfası için şablon olarak kullanılabilir. |
| *selfassırted. html* | Bu sayfa, bir sosyal hesap kaydolma sayfası, yerel hesap kaydolma sayfası veya yerel hesap oturum açma sayfası için şablon olarak kullanılabilir. |
| *Birleşik. html* | Bu sayfa, Birleşik kaydolma veya oturum açma sayfası için şablon olarak kullanılabilir. |
| *updateprofile. html* | Bu sayfa, bir profil güncelleştirme sayfası için şablon olarak kullanılabilir. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Kullanıcı yolculuğuna HTML5/CSS şablonlarınıza bir bağlantı ekleyin

Özel bir ilkeyi doğrudan düzenleyerek HTML5/CSS şablonlarınıza Kullanıcı yolculuğuna bir bağlantı ekleyebilirsiniz.

Kullanıcı yolculuğunda kullanabileceğiniz özel HTML5/CSS şablonlarının, bu kullanıcı yolculuğunda kullanılabilecek bir içerik tanımları listesinde belirtilmesi gerekir. Bu amaçla, bir isteğe bağlı *\<ContentDefinitions >* XML öğesi özel ilke XML dosyanızın *\<buildingblocks >* bölümünde bildirilmelidir.

Aşağıdaki tabloda, Azure AD B2C Identity Experience Engine tarafından tanınan içerik tanımı kimlikleri ve bunlarla ilgili sayfaların türü açıklanmaktadır.

| İçerik tanımı KIMLIĞI | Açıklama |
|-----------------------|-------------|
| *api. Error* | **Hata sayfası**. Bu sayfa bir özel durum veya hata ile karşılaşıldığında görüntülenir. |
| *api. ıdpseçimlerin* | **Kimlik sağlayıcısı seçim sayfası**. Bu sayfa, kullanıcının oturum açma sırasında aralarından seçim yapabileceğiniz kimlik sağlayıcılarının bir listesini içerir. Bu sağlayıcılar, kurumsal kimlik sağlayıcılarıdır, Facebook ve Google + gibi sosyal kimlik sağlayıcıları ya da yerel hesaplar (e-posta adresine veya Kullanıcı adına göre). |
| *api. ıdpseçimlerin. Signup* | **Kaydolma Için kimlik sağlayıcısı seçimi**. Bu sayfa, kullanıcının kaydolma sırasında aralarından seçim yapabileceğiniz kimlik sağlayıcılarının bir listesini içerir. Bu sağlayıcılar, kurumsal kimlik sağlayıcılarıdır, Facebook ve Google + gibi sosyal kimlik sağlayıcıları ya da yerel hesaplar (e-posta adresine veya Kullanıcı adına göre). |
| *api. localaccountpasswordreset* | **Parolayı unuttum sayfası**. Bu sayfa, kullanıcının parola sıfırlamalarını başlatmak için doldurması gereken bir form içerir.  |
| *api. localaccountsignın* | **Yerel hesap oturum açma sayfası**. Bu sayfa, kullanıcının e-posta adresini veya Kullanıcı adını temel alan bir yerel hesapla oturum açarken doldurmasının gereken bir oturum açma formunu içerir. Form bir metin girişi kutusu ve parola giriş kutusu içerebilir. |
| *api. localaccountsignup* | **Yerel hesap kaydolma sayfası**. Bu sayfa, kullanıcının e-posta adresini veya Kullanıcı adını temel alan bir yerel hesaba kaydolurken doldurmasının gereken bir kaydolma formu içerir. Form, metin girişi kutusu, parola giriş kutusu, radyo düğmesi, tek seçim açılan kutuları ve çoklu seçim onay kutuları gibi farklı giriş denetimleri içerebilir. |
| *api. phonefactor* | **Multi-Factor Authentication sayfası**. Bu sayfada, kullanıcılar kaydolma veya oturum açma sırasında telefon numaralarını (metin veya ses kullanarak) doğrulayabilirler. |
| *api. selfasted* | **Sosyal hesap kaydolma sayfası**. Bu sayfa, kullanıcının Facebook veya Google + gibi bir sosyal kimlik sağlayıcısından mevcut bir hesabı kullanarak kaydolurken doldurmasının gerektiği bir kaydolma formu içerir. Bu sayfa, parola girişi alanları hariç olmak üzere önceki sosyal hesap kaydolma sayfasına benzer. |
| *api. selfasserted. profileUpdate* | **Profil güncelleştirme sayfası**. Bu sayfa, kullanıcının profilini güncelleştirmek için kullanabileceği bir form içerir. Bu sayfa, parola girişi alanları hariç olmak üzere önceki sosyal hesap kaydolma sayfasına benzer. |
| *api. signuporsignın* | **Birleşik kaydolma veya oturum açma sayfası**.  Bu sayfa, kullanıcıların oturum açma & oturum açma, kurumsal kimlik sağlayıcıları, Facebook veya Google + gibi sosyal kimlik sağlayıcıları ya da yerel hesaplar tarafından da kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar
[Başvuru: özel ilkelerin B2C 'de kimlik deneyimi çerçevesiyle nasıl çalıştığını anlayın](active-directory-b2c-reference-custom-policies-understanding-contents.md)
