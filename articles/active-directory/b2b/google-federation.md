---
title: B2B-Azure Active Directory için bir kimlik sağlayıcısı olarak Google ekleme | Microsoft Docs
description: Konuk kullanıcıların Azure AD uygulamalarınızda kendi Gmail hesabıyla oturum açmasını sağlamak için Google ile Federasyonun
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/1/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68acf32660fe36ddd4c2982b818ce21adde7ddab
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603585"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>B2B Konuk kullanıcıları için bir kimlik sağlayıcısı olarak Google ekleme

Google ile Federasyon ayarlayarak, davet edilen kullanıcıların Microsoft hesapları (MSAs) oluşturmaya gerek kalmadan kendi Gmail hesaplarıyla paylaşılan uygulamalarınızda ve kaynaklarda oturum açmalarına izin verebilirsiniz. 

> [!NOTE]
> Google Federasyonu, Gmail kullanıcıları için özel olarak tasarlanmıştır. G Suite etki alanlarıyla federasyona eklemek için, [doğrudan Federasyon özelliğini](direct-federation.md)kullanın.

## <a name="what-is-the-experience-for-the-google-user"></a>Google kullanıcısına yönelik deneyim nedir?
Bir Google Gmail kullanıcısına davetiye gönderdiğinizde, Konuk kullanıcının kiracı bağlamını içeren bir bağlantıyı kullanarak paylaşılan uygulamalarınıza veya kaynaklarınıza erişmesi gerekir. Bu deneyimler, Google 'da zaten oturum açmış olmasına bağlı olarak farklılık gösterir:
  - Konuk Kullanıcı Google 'da oturum açmamışsa, Google 'da oturum açması istenir.
  - Konuk Kullanıcı zaten Google 'da oturum açmışsa, kullanmak istedikleri hesabı seçmeleri istenir. Bunları davet etmek için kullandığınız hesabı seçmesi gerekir.

Konuk Kullanıcı bir "üst bilgi çok uzun" hatası görürse, kendi tanımlama bilgilerini temizlemeyi deneyebilir veya özel ya da bilişsiz pencereyi açıp yeniden oturum açmayı deneyebilir.

![Google oturum açma sayfasını gösteren ekran görüntüsü](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Sınırlamalar

Takımlar tüm cihazlarda Google Konuk kullanıcılarını tamamen destekler. Google kullanıcıları, `https://teams.microsoft.com`gibi ortak bir uç noktadan ekiplerde oturum açabilirler.

Diğer uygulamaların ortak uç noktaları Google kullanıcılarını desteklemeyebilir. Google Konuk kullanıcılarının kiracı bilgilerinizi içeren bir bağlantı kullanarak oturum açması gerekir. Aşağıda örnekler verilmiştir:
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Google Guest kullanıcıları `https://myapps.microsoft.com` veya `https://portal.azure.com`gibi bir bağlantı kullanmayı denlerse bir hata alırlar.

Ayrıca, bu bağlantı kiracı bilgilerinizi içerdiği sürece (örneğin `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`), Google Konuk kullanıcılarına bir uygulama veya kaynağa doğrudan bağlantı verebilirsiniz. 

## <a name="step-1-configure-a-google-developer-project"></a>1\. Adım: Google Geliştirici projesini yapılandırma
İlk olarak, bir istemci KIMLIĞI ve daha sonra Azure AD 'ye ekleyebileceğiniz bir istemci gizli anahtarı almak için Google geliştiricileri konsolunda yeni bir proje oluşturun. 
1. https://console.developers.google.comadresindeki Google API 'Lerine gidin ve Google hesabınızla oturum açın. Paylaşılan bir takım Google hesabı kullanmanızı öneririz.
2. Yeni bir proje oluşturun: panoda **proje oluştur**' u seçin ve ardından **Oluştur**' u seçin. Yeni proje sayfasında, bir **Proje adı**girin ve ardından **Oluştur**' u seçin.
   
   ![Google için yeni bir proje sayfası gösteren ekran görüntüsü](media/google-federation/google-new-project.png)

3. Proje menüsünde Yeni projenizin seçildiğinden emin olun. Ardından, sol üstteki menüyü açın ve **API 'ler & hizmetleri** > **kimlik bilgileri**' ni seçin.

   ![Google API kimlik bilgileri seçeneğini gösteren ekran görüntüsü](media/google-federation/google-api.png)
 
4. **OAuth onay ekranı** sekmesini seçin ve bir **uygulama adı**girin. (Diğer ayarları bırakın.)

   ![Google OAuth onay ekranı seçeneğini gösteren ekran görüntüsü](media/google-federation/google-oauth-consent-screen.png)

5. **Yetkili etki alanları** bölümüne gidin ve microsoftonline.com girin.

   ![Yetkili etki alanları bölümünü gösteren ekran görüntüsü](media/google-federation/google-oauth-authorized-domains.png)

6. **Kaydet**’i seçin.

7. **Kimlik bilgileri** sekmesini seçin. **Kimlik bilgileri oluştur** menüsünde **OAuth istemci kimliği**' ni seçin.

   ![Google API 'Leri kimlik bilgileri oluşturma seçeneğini gösteren ekran görüntüsü](media/google-federation/google-api-credentials.png)

8. **Uygulama türü**' nün altında **Web uygulaması**' nı seçin ve ardından **yetkili yeniden yönlendirme URI 'leri**altında aşağıdaki URI 'leri girin:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(`<directory id>` Dizin KIMLIĞINIZ)
   
     > [!NOTE]
     > Dizin KIMLIĞINIZI bulmak için https://portal.azure.comadresine gidin ve **Azure Active Directory**altında **Özellikler** ' i seçin ve **dizin kimliğini**kopyalayın.

   ![Yetkili yeniden yönlendirme URI 'Leri bölümünü gösteren ekran görüntüsü](media/google-federation/google-create-oauth-client-id.png)

9. **Oluştur**'u seçin. Kimlik sağlayıcısını Azure AD portalına eklerken kullanacağınız istemci KIMLIĞINI ve istemci parolasını kopyalayın.

   ![OAuth İstemci KIMLIĞINI ve istemci gizli anahtarını gösteren ekran görüntüsü](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2\. Adım: Google Federation 'ı Azure AD 'de yapılandırma 
Artık Google istemci KIMLIĞINI ve istemci gizli anahtarını Azure AD portalına girerek ya da PowerShell kullanarak ayarlayacaksınız. Kendinize bir Gmail adresi kullanarak davet ederek Google Federation yapılandırmanızı test ettiğinizden emin olun ve daveti davet edilen Google hesabınızla kullanmaya çalışıyorsunuz. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Google Federasyonu 'ni Azure AD portalında yapılandırmak için 
1. [Azure Portal](https://portal.azure.com) gidin. Sol bölmede **Azure Active Directory**’yi seçin. 
2. **Kuruluş ilişkilerini**seçin.
3. **Kimlik sağlayıcıları**' nı seçin ve ardından **Google** düğmesine tıklayın.
4. Bir ad girin. Daha önce edindiğiniz istemci KIMLIĞINI ve istemci gizli anahtarını girin. **Kaydet**’i seçin. 

   ![Google Identity Provider Ekle sayfasını gösteren ekran görüntüsü](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>PowerShell kullanarak Google Federasyonu yapılandırmak için
1. Graph modülü için Azure AD PowerShell 'in en son sürümünü ([Azureadpreview](https://www.powershellgallery.com/packages/AzureADPreview)) yükler.
2. Şu komutu çalıştırın: `Connect-AzureAD`.
3. Oturum açma isteminde, yönetilen genel yönetici hesabıyla oturum açın.  
4. Şu komutu çalıştırın: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > "Adım 1: Google Geliştirici projesi yapılandırma" bölümünde oluşturduğunuz uygulamadan istemci kimliğini ve istemci gizli anahtarını kullanın. Daha fazla bilgi için bkz. [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) makalesi. 
 
## <a name="how-do-i-remove-google-federation"></a>Google Federasyonu Nasıl yaparım? kaldırılsın mı?
Google Federation kurulumunuzu silebilirsiniz. Bunu yaparsanız, davetini zaten kullanan Google Konuk kullanıcılar oturum açamaz, ancak bunları dizinden silerek ve yeniden davet ederek kaynaklarınıza yeniden erişim izni verebilirsiniz. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Azure AD portalında Google Federasyonu 'ni silmek için: 
1. [Azure Portal](https://portal.azure.com) gidin. Sol bölmede **Azure Active Directory**’yi seçin. 
2. **Kuruluş ilişkilerini**seçin.
3. **Kimlik sağlayıcıları**' nı seçin.
4. **Google** satırında bağlam menüsünü ( **...** ) seçin ve **Sil**' i seçin. 
   
   ![Sosyal kimlik sağlayıcısı için silme seçeneğini gösteren ekran görüntüsü](media/google-federation/google-social-identity-providers.png)

1. Silmeyi onaylamak için **Evet** ' i seçin. 

### <a name="to-delete-google-federation-by-using-powershell"></a>PowerShell kullanarak Google Federasyonu silmek için: 
1. Graph modülü için Azure AD PowerShell 'in en son sürümünü ([Azureadpreview](https://www.powershellgallery.com/packages/AzureADPreview)) yükler.
2. `Connect-AzureAD` öğesini çalıştırın.  
4. Oturum açma isteminde, yönetilen genel yönetici hesabıyla oturum açın.  
5. Aşağıdaki komutu kullanın:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Daha fazla bilgi için bkz. [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
