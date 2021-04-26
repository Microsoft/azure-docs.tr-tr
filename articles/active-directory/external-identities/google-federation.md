---
title: B2B için kimlik sağlayıcısı olarak Google ekleme-Azure AD
description: Konuk kullanıcıların Azure AD uygulamalarınızda kendi Gmail hesaplarıyla oturum açmasını sağlamak için Google ile Federasyonun.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cb3b3fd2010bf4ad1288b767d62d3d76f7b37e3
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550974"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>B2B Konuk kullanıcıları için bir kimlik sağlayıcısı olarak Google ekleme

Google ile Federasyonu ayarlayarak, davet edilen kullanıcıların Microsoft hesapları oluşturmaya gerek kalmadan kendi Gmail hesaplarıyla paylaşılan uygulamalarınızda ve kaynaklarda oturum açmalarına izin verebilirsiniz.

Uygulamanızı uygulamanızın oturum açma seçeneklerinden biri olarak ekledikten sonra, **oturum açma** sayfasında, Kullanıcı yalnızca Google 'da oturum açmak için kullandıkları e-postayı girebilir veya **oturum açma seçeneklerini** seçip **Google ile oturum aç** seçeneğini belirleyebilir. Her iki durumda da, kimlik doğrulaması için Google oturum açma sayfasına yönlendirilir.

![Google kullanıcıları için oturum açma seçenekleri](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> Google Federasyonu, Gmail kullanıcıları için özel olarak tasarlanmıştır. G Suite etki alanlarıyla federasyona eklemek için [doğrudan Federasyon](direct-federation.md)' ı kullanın.

> [!IMPORTANT]
> **4 ocak 2021 tarihinden itibaren** Google, [WebView oturum açma desteğini kullanımdan](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)kaldırır. Gmail ile Google Federasyonu veya self servis kaydolma kullanıyorsanız, [iş kolu yerel uygulamalarınızı uyumluluk için test](google-federation.md#deprecation-of-webview-sign-in-support)etmeniz gerekir.

## <a name="what-is-the-experience-for-the-google-user"></a>Google kullanıcısına yönelik deneyim nedir?

Bir Google kullanıcısı davetinizi bir daha hazırlarsa, bu deneyim, Google 'da zaten oturum açmış olmasına bağlı olarak farklılık gösterir:

- Google 'da oturum açmamış Konuk kullanıcılardan bunu yapması istenir.
- Google 'da zaten oturum açmış Konuk kullanıcılardan kullanmak istedikleri hesabı seçmeleri istenir. Bunları davet etmek için kullandığınız hesabı seçmesi gerekir.

"Üst bilgi çok uzun" hatası görebilen Konuk kullanıcılar kendi tanımlama bilgilerini temizleyebilir veya özel veya bilito penceresini açabilir ve yeniden oturum açmayı deneyebilir.

![Google oturum açma sayfasını gösteren ekran görüntüsü.](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>Oturum açma uç noktaları

Google Guest kullanıcıları artık [ortak bir uç nokta](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (diğer bir deyişle, kiracı bağlamınızı içermeyen genel bir uygulama URL 'si) kullanarak çok kiracılı veya Microsoft ilk taraf uygulamalarınızda oturum açabilir. Oturum açma işlemi sırasında, Konuk Kullanıcı **oturum açma seçeneklerini** seçer ve ardından **bir kuruluşta oturum açmayı** seçer. Ardından Kullanıcı kuruluşunuzun adını yazıp Google kimlik bilgilerini kullanarak oturum açmaya devam eder.

Google Guest kullanıcıları, kiracı bilgilerinizi içeren uygulama uç noktalarını da kullanabilir, örneğin:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Ayrıca, Google Konuk kullanıcılarına kiracı bilgilerinizi ekleyerek bir uygulama veya kaynağa doğrudan bağlantı verebilirsiniz `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` .

## <a name="deprecation-of-webview-sign-in-support"></a>WebView oturum açma desteğinin kullanımdan kaldırılması

Google, 4 Ocak 2021 tarihinden itibaren [gömülü WebView oturum açma desteğini kullanımdan](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)kaldırır. Gmail ile Google Federasyonu veya [self servis kaydolma](identity-providers.md)kullanıyorsanız, iş kolu yerel uygulamalarınızı uyumluluk için test etmeniz gerekir. Uygulamalarınız kimlik doğrulaması gerektiren WebView içeriği içeriyorsa, Google Gmail kullanıcıları kimlik doğrulaması yapamaz. Gmail kullanıcılarını etkileyecek bilinen senaryolar aşağıda verilmiştir:

- Windows 'un eski sürümlerinde ekli WebView veya WebAccountManager (WAM) kullanan Windows uygulamaları.
- Geliştirmiş olduğunuz diğer yerel uygulamalar, kimlik doğrulaması için gömülü bir tarayıcı çerçevesi kullanır.

Bu değişiklik etkilenmez:

- Windows 'un en son sürümlerinde ekli WebView veya WebAccountManager (WAM) kullanan Windows uygulamaları
- Microsoft iOS uygulamaları
- G Suite kimlikleri, örneğin, G Suite ile SAML tabanlı [Direct Federation](direct-federation.md) kullandığınızda

Çeşitli platformları ve senaryoları sınamaya devam ediyoruz ve bu makaleyi buna göre güncelleştirecek.
### <a name="to-test-your-apps-for-compatibility"></a>Uygulamalarınızı uyumluluk için test etmek üzere

1. Uygulamalarınızın etkilenip etkilenmediğini öğrenmek için [Google 'ın kılavuzunu](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) izleyin.
2. Fiddler veya başka bir test aracı kullanarak, oturum açma sırasında bir üst bilgi ekler ve oturum açma sınamasını denemek için bir Google dış kimliği kullanın:

   1. İstekler accounts.google.com 'e gönderildiğinde HTTP istek başlıklarınıza Google-Accounts-Check-OAuth-Login: true değerini ekleyin.
   1. Accounts.google.com oturum açma sayfasına bir Gmail adresi girerek uygulamada oturum açmayı deneyin.
   1. Oturum açma başarısız olursa ve "bu tarayıcı veya uygulama güvenli olmayabilir" gibi bir hata görürseniz, Google dış kimliklerinizin oturum açması engellenir.

3. Aşağıdakilerden birini yaparak sorunu çözün:

   - Windows uygulamanız Windows 'un eski bir sürümünde ekli WebView veya WebAccountManager (WAM) kullanıyorsa, Windows 'un en son sürümüne güncelleştirin.
   - Uygulamalarınızı, oturum açma için sistem tarayıcısını kullanacak şekilde değiştirin. Ayrıntılar için MSAL.NET belgelerindeki [ekli vs System Web Kullanıcı arabirimi](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) ' ne bakın.  


## <a name="step-1-configure-a-google-developer-project"></a>1. Adım: Google Geliştirici projesini yapılandırma
İlk olarak, daha sonra Azure Active Directory (Azure AD) ekleyebileceğiniz istemci KIMLIĞINI ve bir istemci gizli anahtarını almak için Google geliştiricileri konsolunda yeni bir proje oluşturun. 
1. Adresindeki Google API 'Lerine gidin https://console.developers.google.com ve Google hesabınızla oturum açın. Paylaşılan bir takım Google hesabı kullanmanızı öneririz.
2. İstenirse hizmet koşullarını kabul edin.
3. Yeni bir proje oluşturun: sayfanın sol üst köşesinde proje listesini seçin ve ardından **proje seçin** sayfasında **Yeni proje**' yi seçin.
4. **Yeni proje** sayfasında, projeye bir ad verin (örneğin, **Azure AD B2B**) ve ardından **Oluştur**' u seçin: 
   
   ![Yeni bir proje sayfasını gösteren ekran görüntüsü.](media/google-federation/google-new-project.png)

4. **Apı & Hizmetleri** sayfasında yeni projenizin altında **Görünüm** ' ü seçin.

5. API 'Ler kartında **API 'lere genel bakış ' ı** seçin. **OAuth onay ekranı**' nı seçin.

6. **Dış**' i seçin ve ardından **Oluştur**' u seçin. 

7. **OAuth onay ekranında**, bir **uygulama adı** girin:

   ![Google OAuth onay ekranını gösteren ekran görüntüsü.](media/google-federation/google-oauth-consent-screen.png)

8. **Yetkili etki alanları** bölümüne gidin ve **microsoftonline.com** girin:

   ![Yetkili etki alanları bölümünü gösteren ekran görüntüsü.](media/google-federation/google-oauth-authorized-domains.PNG)

9. **Kaydet**’i seçin.

10. **Kimlik Bilgileri**’ni seçin. **Kimlik bilgileri oluştur** menüsünde, **OAuth istemci kimliği**' ni seçin.

    ![Google API 'Leri oluşturma kimlik bilgileri menüsünü gösteren ekran görüntüsü.](media/google-federation/google-api-credentials.png)

11. **Uygulama türü** altında **Web uygulaması**' nı seçin. Uygulamaya **Azure AD B2B** gibi uygun bir ad verin. **Yetkili yeniden yönlendirme URI 'leri** altında aşağıdaki URI 'leri girin:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>( `<tenant ID>` KIRACı kimliğiniz nerede)
   
    > [!NOTE]
    > Kiracı KIMLIĞINIZI bulmak için [Azure Portal](https://portal.azure.com)gidin. **Azure Active Directory** altında **Özellikler** ' i seçin ve **Kiracı kimliğini** kopyalayın.

    ![Yetkili yeniden yönlendirme URI 'Leri bölümünü gösteren ekran görüntüsü.](media/google-federation/google-create-oauth-client-id.png)

12. **Oluştur**’u seçin. İstemci KIMLIĞINI ve istemci parolasını kopyalayın. Azure portal kimlik sağlayıcısını eklediğinizde kullanırsınız.

    ![OAuth İstemci KIMLIĞINI ve istemci gizli anahtarını gösteren ekran görüntüsü.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2. Adım: Google Federation 'ı Azure AD 'de yapılandırma 
Şimdi Google istemci KIMLIĞINI ve istemci gizli anahtarını ayarlayacaksınız. Azure portal veya PowerShell 'i kullanarak bunu yapabilirsiniz. Google Federation yapılandırmanızı kendinize davet ederek test ettiğinizden emin olun. Bir Gmail adresi kullanın ve daveti davet edilen Google hesabınızla kullanmaya çalışın. 

**Azure portal Google Federation 'ı yapılandırmak için** 
1. [Azure Portal](https://portal.azure.com) gidin. Sol bölmede **Azure Active Directory**' yi seçin. 
2. **Dış kimlikler**' i seçin.
3. **Tüm kimlik sağlayıcıları**' nı seçin ve ardından **Google** düğmesini seçin.
4. Daha önce edindiğiniz istemci KIMLIĞINI ve istemci gizli anahtarını girin. **Kaydet**' i seçin: 

   ![Google Identity Provider Ekle sayfasını gösteren ekran görüntüsü.](media/google-federation/google-identity-provider.png)

**PowerShell kullanarak Google Federasyonu yapılandırmak için**
1. Graph modülü için Azure AD PowerShell 'in en son sürümünü ([Azureadpreview](https://www.powershellgallery.com/packages/AzureADPreview)) yükler.
2. Şu komutu çalıştırın: `Connect-AzureAD`
3. Oturum açma isteminde, yönetilen genel yönetici hesabıyla oturum açın.  
4. Şu komutu çalıştırın: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > "Adım 1: Google Geliştirici projesi yapılandırma" bölümünde oluşturduğunuz uygulamadan istemci KIMLIĞINI ve istemci gizli anahtarını kullanın. Daha fazla bilgi için, bkz. [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true). 
 
## <a name="how-do-i-remove-google-federation"></a>Google Federasyonu Nasıl yaparım? kaldırılsın mı?
Google Federation kurulumunuzu silebilirsiniz. Bunu yaparsanız, davetini zaten kullanan Google Konuk kullanıcılar oturum açamaz. Ancak, kullanım [durumlarını sıfırlayarak](reset-redemption-status.md)bu kullanıcılara kaynaklarınızın erişimini sağlayabilirsiniz.
 
**Azure AD portalında Google Federasyonu 'ni silmek için**
1. [Azure Portal](https://portal.azure.com) gidin. Sol bölmede **Azure Active Directory**' yi seçin. 
2. **Dış kimlikler**' i seçin.
3. **Tüm kimlik sağlayıcılarını** seçin.
4. **Google** satırında üç nokta düğmesini (**...**) seçin ve **Sil**' i seçin. 
   
   ![Sosyal kimlik sağlayıcısı için Sil düğmesini gösteren ekran görüntüsü.](media/google-federation/google-social-identity-providers.png)

1. Silmeyi onaylamak için **Evet** ' i seçin. 

**PowerShell kullanarak Google Federasyonu silmek için** 
1. Graph modülü için Azure AD PowerShell 'in en son sürümünü ([Azureadpreview](https://www.powershellgallery.com/packages/AzureADPreview)) yükler.
2. `Connect-AzureAD` öğesini çalıştırın.  
4. Oturum açma isteminde, yönetilen genel yönetici hesabıyla oturum açın.  
5. Aşağıdaki komutu girin:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Daha fazla bilgi için bkz. [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true).
