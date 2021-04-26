---
title: Bir kimlik sağlayıcısı olarak Facebook ekleme-Azure AD
description: Dış kullanıcıların (konuklar) kendi Facebook hesaplarıyla Azure AD uygulamalarınızda oturum açmasını sağlamak için Facebook ile federasyona Federin.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcacfc8cede0ab91241fd431aace10cbbfbea916
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709693"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Facebook 'ı dış kimlikler için kimlik sağlayıcısı olarak ekleyin

Self Servis kaydolma Kullanıcı akışlarınıza Facebook ekleyebilirsiniz, böylece kullanıcılar kendi Facebook hesaplarını kullanarak uygulamalarınızda oturum açabilirler. Kullanıcıların Facebook kullanarak oturum açmalarına izin vermek için öncelikle kiracınız için [self servis kaydolma 'yı etkinleştirmeniz](self-service-sign-up-user-flow.md) gerekir. Bir kimlik sağlayıcısı olarak Facebook ekledikten sonra, uygulama için bir Kullanıcı akışı ayarlayın ve oturum açma seçeneklerinden biri olarak Facebook ' u seçin.

Uygulamanızın oturum açma seçeneklerinden biri olarak Facebook eklendikten sonra, **oturum açma** sayfasında, Kullanıcı yalnızca Facebook 'ta oturum açmak için kullandıkları e-postayı girebilir veya **oturum açma seçeneklerini** seçip **Facebook 'ta oturum aç**' ı seçebilir. Her iki durumda da, kimlik doğrulaması için Facebook oturum açma sayfasına yönlendirilir.

![Facebook kullanıcıları için oturum açma seçenekleri](media/facebook-federation/sign-in-with-facebook-overview.png)

> [!NOTE]
> Kullanıcılar, self servis kaydolma ve Kullanıcı akışlarını kullanarak uygulamalara kaydolmak için yalnızca Facebook hesaplarını kullanabilir. Kullanıcılar, Facebook hesabı kullanılarak davetlerinden davet edilemez ve bunları kullanamaz.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Facebook geliştiricileri konsolunda uygulama oluşturma

Bir Facebook hesabını [kimlik sağlayıcısı](identity-providers.md)olarak kullanmak Için, Facebook geliştiricileri konsolunda bir uygulama oluşturmanız gerekir. Henüz bir Facebook hesabınız yoksa kaydolabilirsiniz [https://www.facebook.com/](https://www.facebook.com) .

> [!NOTE]  
> Aşağıdaki 3 ve 16. adımlarda aşağıdaki URL 'Leri kullanın.
> - **Site URL 'si** için uygulamanızın adresini girin, örneğin `https://contoso.com` .
> - **Geçerli OAuth yeniden yönlendirme URI 'leri** için girin `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp` . `<tenant-ID>`Azure Active Directory genel bakış dikey penceresinde bulabilirsiniz.


1. Facebook hesabı kimlik bilgilerinizle [geliştiriciler Için Facebook](https://developers.facebook.com/) 'ta oturum açın.
2. Daha önce yapmadıysanız, Facebook geliştiricisi olarak kaydolmanız gerekir. Bunu yapmak için sayfanın sağ üst **köşesinden başlayın ' ı seçin,** Facebook ilkelerini kabul edin ve kayıt adımlarını uygulayın.
3. **Uygulamalarım** ' ı ve ardından **uygulama oluştur**' u seçin.
4. Bir **görünen ad** ve geçerli bir **Iletişim e-postası** girin.
5. **Uygulama kimliği oluştur**' u seçin. Bu, Facebook platformu ilkelerini kabul etmenizi ve çevrimiçi bir güvenlik denetimi tamamlamanızı gerektirebilir.
6. **Ayarlar**  >  **temel** öğesini seçin.
7. Örneğin Iş ve sayfalar gibi bir **Kategori** seçin. Bu değer Facebook için gereklidir, ancak Azure AD için kullanılmaz.
8. Sayfanın alt kısmındaki **Platform Ekle**' yi ve ardından **Web sitesi**' ni seçin.
9. **Site URL 'si**' nde, uygun URL 'yi girin (yukarıda belirtilen).
10. **Gizlilik ilkesi URL 'si**' nde, uygulamanız için gizlilik bilgilerini tuttuğunuz sayfanın URL 'sini girin (örneğin,) `http://www.contoso.com` .
11. **Değişiklikleri Kaydet**' i seçin.
12. Sayfanın üst kısmında, **uygulama kimliği** değerini kopyalayın.
13. **Göster** ' i seçin ve **uygulama gizli anahtarı** değerini kopyalayın. Facebook 'ı kiracınızda bir kimlik sağlayıcısı olarak yapılandırmak için her ikisini de kullanabilirsiniz. **Uygulama gizli anahtarı** önemli bir güvenlik kimlik bilgileridir.
14. **Ürünlerin** yanındaki artı işaretini ve ardından **Facebook oturumu** altında **Ayarla** ' yı seçin.
15. **Facebook oturum açma** bölümünde **Ayarlar**' ı seçin.
16. **Geçerli OAuth yeniden yönlendirme URI 'lerinde**, uygun URL 'yi girin (yukarıda belirtilen).
17. Sayfanın alt kısmındaki **Değişiklikleri Kaydet** ' i seçin.
18. Facebook uygulamanızı Azure AD **'de** kullanılabilir hale getirmek için sayfanın sağ üst kısmındaki durum seçicisini seçin ve uygulamayı ortak hale getirmek için açın, sonra **geçiş modu**' nu seçin. Bu noktada, durumun **geliştirme aşamasında** **canlı** olarak değiştirilmesi gerekir.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Bir Facebook hesabını kimlik sağlayıcısı olarak yapılandırma
Artık Facebook istemci KIMLIĞINI ve istemci gizli anahtarını Azure AD portalına girerek ya da PowerShell kullanarak ayarlayacaksınız. Self Servis kaydolma için etkinleştirilen bir uygulamadaki Kullanıcı akışı aracılığıyla kayıt yaparak Facebook yapılandırmanızı test edebilirsiniz.

### <a name="to-configure-facebook-federation-in-the-azure-ad-portal"></a>Azure AD portalında Facebook Federasyonu 'ni yapılandırmak için
1. [Azure Portal](https://portal.azure.com) Azure AD kiracınızın genel yöneticisi olarak oturum açın.
2. **Azure hizmetleri** altında **Azure Active Directory**' yi seçin.
3. Sol taraftaki menüden **dış kimlikler**' i seçin.
4. **Tüm kimlik sağlayıcıları**' nı seçin ve **Facebook**' u seçin.
5. **ISTEMCI kimliği** için, daha önce oluşturduğunuz Facebook UYGULAMASıNıN uygulama **kimliğini** girin.
6. **İstemci parolası** için, kaydettiğiniz **uygulama gizli** anahtarını girin.

   ![Sosyal kimlik sağlayıcısı Ekle sayfasını gösteren ekran görüntüsü](media/facebook-federation/add-social-identity-provider-page.png)

7. **Kaydet**’i seçin.
### <a name="to-configure-facebook-federation-by-using-powershell"></a>PowerShell kullanarak Facebook Federasyonu yapılandırmak için
1. Graph modülü için Azure AD PowerShell 'in en son sürümünü ([Azureadpreview](https://www.powershellgallery.com/packages/AzureADPreview)) yükler.
2. Şu komutu çalıştırın: `Connect-AzureAD` .
3. Oturum açma isteminde, yönetilen genel yönetici hesabıyla oturum açın.  
4. Şu komutu çalıştırın: 
   
   `New-AzureADMSIdentityProvider -Type Facebook -Name Facebook -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Facebook Geliştirici konsolunda, yukarıda oluşturduğunuz uygulamadan istemci KIMLIĞINI ve istemci gizli anahtarını kullanın. Daha fazla bilgi için bkz. [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true) makalesi. 

## <a name="how-do-i-remove-facebook-federation"></a>Facebook Federasyonu Nasıl yaparım? kaldırılsın mı?
Facebook Federasyonu kurulumunu silebilirsiniz. Bunu yaparsanız, Facebook hesaplarıyla Kullanıcı akışları üzerinden kaydolan kullanıcılar artık oturum açamaz. 

### <a name="to-delete-facebook-federation-in-the-azure-ad-portal"></a>Azure AD portalında Facebook Federasyonu 'ni silmek için: 
1. [Azure Portal](https://portal.azure.com) gidin. Sol bölmede **Azure Active Directory**’yi seçin. 
2. **Dış kimlikler**' i seçin.
3. **Tüm kimlik sağlayıcılarını** seçin.
4. **Facebook** satırında bağlam menüsünü (**...**) seçin ve **Sil**' i seçin. 
5. Silmeyi onaylamak için **Evet** ' i seçin.

### <a name="to-delete-facebook-federation-by-using-powershell"></a>PowerShell kullanarak Facebook Federasyonu silmek için: 
1. Graph modülü için Azure AD PowerShell 'in en son sürümünü ([Azureadpreview](https://www.powershellgallery.com/packages/AzureADPreview)) yükler.
2. `Connect-AzureAD` öğesini çalıştırın.  
4. Oturum açma isteminde, yönetilen genel yönetici hesabıyla oturum açın.  
5. Aşağıdaki komutu girin:

    `Remove-AzureADMSIdentityProvider -Id Facebook-OAUTH`

   > [!NOTE]
   > Daha fazla bilgi için bkz. [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true). 

## <a name="next-steps"></a>Sonraki adımlar

- [Bir uygulamaya self servis kaydolma ekleme](self-service-sign-up-user-flow.md)