---
title: Azure AD uygulama proxy'si kullanarak yayımlanmış uygulamalar için özel bir ana sayfa ayarlama | Microsoft Docs
description: Azure AD uygulama ara sunucusu bağlayıcıları hakkında temel kavramları kapsar
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51596e4db8999de5089748e40f9b24bd46c84e56
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807843"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Azure AD uygulama proxy'si kullanarak yayımlanmış uygulamalar için özel bir ana sayfa ayarlayın

Bu makalede, bir kullanıcı özel giriş sayfasına yönlendirmek için bir uygulama yapılandırma açıklanmaktadır. Uygulama Ara sunucusu ile bir uygulama yayımladığınızda, bir iç URL ayarlandı, ancak bazen, bir kullanıcı ilk kez görmeniz gerekir sayfa değil. Özel bir ana sayfa uygulamaya eriştiklerinde, sayfanın sağ bir kullanıcı alır şekilde ayarlayın. Bir kullanıcı özel giriş sayfasını görürsünüz, mi uygulamayı Azure Active Directory erişim paneli veya Office 365 uygulama başlatıcısında eriştiklerinde bağımsız olarak ayarladığınız.

Kullanıcı uygulamayı başlattığında, varsayılan olarak yayımlanan uygulama için kök etki alanı URL'si yönlendirilirsiniz. Giriş sayfası, genellikle giriş sayfası URL'si ayarlanır. Azure AD PowerShell modülünün, uygulama kullanıcısı uygulama içinde belirli bir sayfada yerleşmesi istediğinizde bir özel giriş sayfası URL'si tanımlamak için kullanın.

Neden şirket özel bir ana sayfa ayarlamalı açıklayan bir senaryo aşağıda verilmiştir:

- Bir kullanıcı, şirket ağı içinde gider `https://ExpenseApp/login/login.aspx` oturum açmak ve uygulamanıza erişmek için.
- Uygulama proxy'si Klasör yapısındaki en üst düzeyinde erişmesi gereken diğer varlıklar (örneğin, resim) olduğundan, uygulamayı yayımladığınız `https://ExpenseApp` İç URL.
- Varsayılan dış URL `https://ExpenseApp-contoso.msappproxy.net`, hangi dış kullanıcı oturum açma sayfasına olması değil.
- Ayarlamak istediğiniz `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` giriş sayfası URL'si bunun yerine, bu nedenle bir dış kullanıcının görür oturum açma sayfası ilk.

> [!NOTE]
> Kullanıcıların yayımlanan uygulamalara erişmesini sağlamak, uygulamalar görüntülenecek [Azure AD erişim paneli](../user-help/my-apps-portal-end-user-access.md) ve [Office 365 uygulama başlatıcısında](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Başlamadan önce

Giriş sayfası URL'si ayarlamadan önce aşağıdaki gereksinimleri göz önünde bulundurun:

- Belirttiğiniz yol kök etki alanı URL bir alt etki alanı yolu olmalıdır.

  Örneğin, kök etki alanı URL'si `https://apps.contoso.com/app1/`, yapılandırdığınız giriş sayfası URL'si ile başlamalıdır `https://apps.contoso.com/app1/`.

- Yayımlanan uygulama için bir değişiklik yaparsanız, değişiklik giriş sayfası URL'si değerini sıfırlayabilir. Giriş sayfası URL'si güncelleştirdiğinizde, bağlantı uygulama gelecekte yeniden denetlemek ve gerekirse güncelleştirin.

Giriş sayfası URL'si Azure portal veya PowerShell kullanarak ayarlayabilirsiniz.

## <a name="change-the-home-page-in-the-azure-portal"></a>Azure portalında giriş sayfasını değiştirme

Uygulamanızı Azure AD Portalı aracılığıyla giriş sayfası URL'sini değiştirmek için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com/)’da yönetici olarak oturum açın.
1. Seçin **Azure Active Directory**, ardından **uygulama kayıtları**. Kayıtlı uygulamalar listesinde görünür.
1. Uygulamanızı listeden seçin. Kayıtlı uygulama ayrıntılarını gösteren bir sayfa görüntülenir.
1. Altında **Yönet**seçin **markalama**.
1. Güncelleştirme **giriş sayfası URL'si** yeni yolunuzla.

   ![Giriş sayfası URL alanını gösteren kayıtlı bir uygulama sayfası markalama](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. **Kaydet**’i seçin.

## <a name="change-the-home-page-with-powershell"></a>PowerShell ile giriş sayfasını değiştirme

PowerShell kullanarak uygulama ana sayfası yapılandırmak için yapmanız:

1. Azure AD PowerShell modülünü yükleyin.
1. Uygulama objectID değerini bulur.
1. Uygulamanın giriş sayfası URL'si PowerShell komutlarını kullanarak güncelleştirin.

### <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell modülünü yükleme

PowerShell kullanarak bir özel giriş sayfası URL'si tanımlamadan önce Azure AD PowerShell modülünü yükleyin. Paketten indirebileceğiniz [PowerShell Galerisi](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), Graph API uç noktası kullanır.

Paketi yüklemek için aşağıdaki adımları izleyin:

1. Standart bir PowerShell penceresi açın ve ardından aşağıdaki komutu çalıştırın:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Komutu yönetici olmayan çalıştırıyorsanız, kullanın `-scope currentuser` seçeneği.

1. Yükleme sırasında seçin **Y** iki paketlerini Nuget.org adresinden yükleyin. Her iki paketi de gereklidir.

### <a name="find-the-objectid-of-the-app"></a>ObjectID uygulamanın Bul

ObjectID uygulamanın, uygulama için arama yaparak, görünen adını veya giriş sayfası alın.

1. Aynı PowerShell penceresinde, Azure AD modülünü içeri aktarın.

   ```powershell
   Import-Module AzureAD
   ```

1. Azure AD modülünü için Kiracı Yöneticisi olarak oturum açın.

   ```powershell
   Connect-AzureAD
   ```

1. Uygulamayı bulun. ObjectID uygulamanın görünen adı ile arama yaparak bulmak için bu örnek PowerShell'i kullanmaktadır `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Aşağıda gösterildiği gibi bir sonuç almanız gerekir. Sonraki bölümde kullanmak için objectID GUID kopyalayın.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Alternatif olarak, yalnızca tüm uygulamaların listesine çekme, uygulamanın giriş sayfası veya özel bir görüntü adı ile listesinde arama yapın ve uygulamanın objectID uygulamanın bulunduğunda kopyalayın.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Giriş sayfası URL'sini güncelleştirme

Giriş sayfası URL'si oluşturun ve bu değeri ile uygulamanızı güncelleştirin. Aynı PowerShell penceresi kullanmaya devam edin veya yeni bir PowerShell penceresi kullanıyorsanız, Azure AD modülünü kullanarak tekrar oturum `Connect-AzureAD`. Ardından aşağıdaki adımları izleyin:

1. Önceki bölümde kopyaladığınız objectID değerini tutacak bir değişken oluşturun. (Bu, uygulamanızın objectID değeri ile SharePoint örnekte kullanılan objectID değerini değiştirin.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Aşağıdaki komutu çalıştırarak uygulamanın doğru olduğunu doğrulayın. Çıktı önceki bölümde anlatıldığı çıkış için aynı olması gerekir ([objectID uygulamanın Bul](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Yapmak istediğiniz değişiklikleri tutmak için bir boş uygulama nesnesi oluşturun.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Giriş sayfası URL'si istediğiniz değere ayarlayın. Değer yayımlanan bir uygulamanın bir alt etki alanı yolu olmalıdır. Örneğin, giriş sayfası URL değiştirirseniz `https://sharepoint-iddemo.msappproxy.net/` için `https://sharepoint-iddemo.msappproxy.net/hybrid/`, uygulama kullanıcılarının doğrudan özel giriş sayfasına gidin.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Giriş sayfasının güncelleştirme olun.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Değişiklik başarılı olduğunu doğrulamak için adım 2 ' aşağıdaki komutu yeniden çalıştırın.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Bizim örneğimizde, çıkış şimdi aşağıdaki gibi görünmelidir:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Beklendiği gibi giriş sayfası ilk ekran görüntülendiğini doğrulamak için uygulamayı yeniden başlatın.

> [!NOTE]
> Giriş sayfası URL'si uygulamaya yaptığınız tüm değişiklikler sıfırlayabilir. Giriş sayfası URL'nizi sıfırlarsa, geri ayarlamak için bu bölümdeki adımları yineleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD uygulama ara sunucusu ile SharePoint uzaktan erişimi etkinleştirme](application-proxy-integrate-with-sharepoint-server.md)
- [Öğretici: Azure Active Directory Uygulama proxy'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md)