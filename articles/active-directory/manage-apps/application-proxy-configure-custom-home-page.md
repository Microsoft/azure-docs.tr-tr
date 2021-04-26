---
title: Yayımlanan uygulamalar için özel ana sayfa-Azure AD Uygulama Ara Sunucusu
description: Azure AD Uygulama Ara Sunucusu bağlayıcıları hakkında temel bilgileri içerir
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebef93455414c114fb60df9af8ebc629769a33e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254437"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu kullanarak yayımlanan uygulamalar için özel bir giriş sayfası ayarlama

Bu makalede, bir uygulamayı özel bir giriş sayfasına yönlendirmek için bir uygulamanın nasıl yapılandırılacağı açıklanır. Uygulama proxy 'si ile bir uygulama yayımladığınızda, dahili bir URL ayarlarsınız, ancak bazen kullanıcının ilk göreceği sayfa değildir. Bir kullanıcının uygulamaya erişirken doğru sayfayı alması için özel bir giriş sayfası ayarlayın. Bir Kullanıcı, uygulamaya Azure Active Directory uygulamalarımın veya Microsoft 365 uygulama başlatıcılarından erişip erişmediğine bakılmaksızın, ayarladığınız özel giriş sayfasını görür.

Bir Kullanıcı uygulamayı başlattığında, varsayılan olarak yayımlanan uygulamanın kök etki alanı URL 'sine yönlendirilir. Giriş sayfası genellikle giriş sayfası URL 'SI olarak ayarlanır. Uygulama kullanıcısının uygulama içindeki belirli bir sayfaya giriş yapmak istediğinizde, bir özel giriş sayfası URL 'SI tanımlamak için Azure AD PowerShell modülünü kullanın.

Şirketinizin neden özel bir giriş sayfası ayarlayacağını anlatan bir senaryo aşağıda verilmiştir:

- Şirket ağınızın içinde, bir Kullanıcı `https://ExpenseApp/login/login.aspx` oturum açıp uygulamanıza erişmek için ' a gider.
- Uygulama proxy 'sinin klasör yapısının en üst düzeyinde erişmesi gereken diğer varlıklara (örneğin, görüntüler) sahip olduğunuzdan, uygulamayı `https://ExpenseApp` Iç URL 'si olarak yayımlayabilirsiniz.
- Varsayılan dış URL, `https://ExpenseApp-contoso.msappproxy.net` oturum açma sayfasına bir dış Kullanıcı almaz.
- `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx`Bunun yerine giriş sayfası URL 'si olarak ayarlamak istiyorsunuz, bu nedenle bir dış Kullanıcı önce oturum açma sayfasını görür.

> [!NOTE]
> Kullanıcılara yayımlanan uygulamalara erişim izni verdiğinizde uygulamalar [uygulamalarım](../user-help/my-apps-portal-end-user-access.md) ve [Office 365 uygulama başlatıcısı](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)'nda görüntülenir.

## <a name="before-you-start"></a>Başlamadan önce

Giriş sayfası URL 'sini ayarlamadan önce aşağıdaki gereksinimleri göz önünde bulundurun:

- Belirttiğiniz yolun kök etki alanı URL 'sinin bir alt etki alanı yolu olması gerekir.

  Örneğin, kök etki alanı URL 'SI ise `https://apps.contoso.com/app1/` , yapılandırdığınız giriş sayfası URL 'si ile başlamalıdır `https://apps.contoso.com/app1/` .

- Yayımlanan uygulamada bir değişiklik yaparsanız, değişiklik giriş sayfası URL 'sinin değerini sıfırlayabilir. Uygulamayı gelecekte güncelleştirdiğinizde, gerekirse yeniden denetleyip giriş sayfası URL 'sini güncelleştirmeniz gerekir.

Giriş sayfası URL 'sini Azure portal aracılığıyla ya da PowerShell kullanarak ayarlayabilirsiniz.

## <a name="change-the-home-page-in-the-azure-portal"></a>Azure portal giriş sayfasını değiştirme

Azure AD portalı aracılığıyla uygulamanızın giriş sayfası URL 'sini değiştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/)’da yönetici olarak oturum açın.
1. **Azure Active Directory**' yi seçin ve ardından **uygulama kayıtları**. Kayıtlı uygulamaların listesi görüntülenir.
1. Listeden uygulamanızı seçin. Kayıtlı uygulamanın ayrıntılarını gösteren bir sayfa görüntülenir.
1. **Yönet** altında **marka**' i seçin.
1. **Giriş sayfası URL 'sini** yeni yolunuza göre güncelleştirin.

   ![Giriş sayfası URL 'SI alanını gösteren kayıtlı bir uygulama için marka sayfası](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. **Kaydet**’i seçin.

## <a name="change-the-home-page-with-powershell"></a>Giriş sayfasını PowerShell ile değiştirme

PowerShell kullanarak bir uygulamanın giriş sayfasını yapılandırmak için şunları yapmanız gerekir:

1. Azure AD PowerShell modülünü yükler.
1. Uygulamanın ObjectID değerini bulur.
1. PowerShell komutlarını kullanarak uygulamanın giriş sayfası URL 'sini güncelleştirin.

### <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell modülünü yükler

PowerShell kullanarak özel bir giriş sayfası URL 'SI yapılandırmadan önce Azure AD PowerShell modülünü yükleyebilirsiniz. Paketi, Graph API uç noktasını kullanan [PowerShell Galerisi](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)indirebilirsiniz.

Paketi yüklemek için şu adımları izleyin:

1. Standart bir PowerShell penceresi açın ve ardından aşağıdaki komutu çalıştırın:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Komutu yönetici olmayan olarak çalıştırıyorsanız, `-scope currentuser` seçeneğini kullanın.

1. Yükleme sırasında, Nuget.org adresinden iki paket yüklemek için **Y** ' yi seçin. Her iki paket de gereklidir.

### <a name="find-the-objectid-of-the-app"></a>Uygulamanın ObjectID 'sini bulma

Uygulamayı görünen adına veya giriş sayfasına göre arayarak uygulamanın ObjectID 'sini alırsınız.

1. Aynı PowerShell penceresinde Azure AD modülünü içeri aktarın.

   ```powershell
   Import-Module AzureAD
   ```

1. Azure AD modülünde kiracı yöneticisi olarak oturum açın.

   ```powershell
   Connect-AzureAD
   ```

1. Uygulamayı bulun. Bu örnek, bir görünen adıyla uygulamayı arayarak ObjectID 'yi bulmak için PowerShell kullanır `SharePoint` .

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Burada gösterilenle benzer bir sonuç almanız gerekir. Sonraki bölümde kullanmak için ObjectID GUID 'INI kopyalayın.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Alternatif olarak, yalnızca tüm uygulamaların listesini çekebilir, belirli bir görünen ad veya giriş sayfası ile uygulamayı listede arayabilir ve uygulama bulunduğunda uygulamanın ObjectID 'yi kopyalayabilirsiniz.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Giriş sayfası URL 'sini güncelleştirme

Giriş sayfası URL 'sini oluşturun ve uygulamanızı bu değerle güncelleştirin. Aynı PowerShell penceresini kullanmaya devam edin veya yeni bir PowerShell penceresi kullanıyorsanız, kullanarak Azure AD modülünde tekrar oturum açın `Connect-AzureAD` . Ardından aşağıdaki adımları izleyin:

1. Önceki bölümde kopyaladığınız ObjectID değerini tutacak bir değişken oluşturun. (Bu SharePoint örneğinde için kullanılan ObjectID değerini uygulamanızın ObjectID değeriyle değiştirin.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Aşağıdaki komutu çalıştırarak doğru uygulamaya sahip olmadığınızı doğrulayın. Çıktı, önceki bölümde gördüğünüz çıkışla aynı olmalıdır ([uygulamanın ObjectID 'Sini bulun](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Yapmak istediğiniz değişiklikleri tutmak için boş bir uygulama nesnesi oluşturun.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Giriş sayfası URL 'sini istediğiniz değere ayarlayın. Değer yayımlanmış uygulamanın bir alt etki alanı yolu olmalıdır. Örneğin, giriş sayfası URL 'sini `https://sharepoint-iddemo.msappproxy.net/` olarak değiştirirseniz `https://sharepoint-iddemo.msappproxy.net/hybrid/` , uygulama kullanıcıları doğrudan özel giriş sayfasına gider.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Giriş sayfası güncelleştirmesini yapın.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Değişikliğin başarılı olduğunu onaylamak için adım 2 ' de aşağıdaki komutu yeniden çalıştırın.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Bizim örneğimizde, çıktı şu şekilde görünmelidir:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Giriş sayfasının beklendiği gibi ilk ekran olarak göründüğünü onaylamak için uygulamayı yeniden başlatın.

> [!NOTE]
> Uygulamada yaptığınız tüm değişiklikler giriş sayfası URL 'sini sıfırlayabilirler. Giriş sayfası URL 'SI sıfırlarsa, geri ayarlamak için bu bölümdeki adımları tekrarlayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD Uygulama Ara Sunucusu ile SharePoint’e uzaktan erişimi etkinleştirme](application-proxy-integrate-with-sharepoint-server.md)
- [Öğretici: Azure Active Directory içindeki uygulama proxy 'Si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md)
