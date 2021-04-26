---
title: Hizmet sorumlusu kullanarak Windows sanal masaüstü (klasik) için bir Yönetim Aracı dağıtma-Azure
description: PowerShell kullanarak Windows sanal masaüstü (klasik) için yönetim aracı 'nı dağıtma.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 60eec4580e222123795db9554f56e74cb01c5257
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444454"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-powershell"></a>PowerShell ile Windows sanal masaüstü (klasik) Yönetim Aracı dağıtma

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir.

Bu makalede, PowerShell kullanarak yönetim aracının nasıl dağıtılacağı gösterilir.

## <a name="important-considerations"></a>Önemli noktalar

Her Azure Active Directory (Azure AD) kiracının aboneliği, yönetim aracının ayrı bir dağıtımına ihtiyaç duyuyor. Bu araç Azure AD Işletmeden Işletmeye (B2B) senaryolarını desteklemez.

Bu yönetim aracı bir örnektir. Microsoft, önemli güvenlik ve kalite güncelleştirmeleri sağlayacaktır. [Kaynak kodu GitHub ' da kullanılabilir](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Bir müşteri veya iş ortağı olun, bu aracı İşletmenizin ihtiyaçlarını karşılayacak şekilde özelleştirmenizi öneririz.

Aşağıdaki tarayıcılar yönetim aracıyla uyumludur:

- Google Chrome 68 veya üzeri
- Microsoft Edge 40,15063 veya üzeri
- Mozilla Firefox 52,0 veya üzeri
- Safari 10 veya üzeri (yalnızca macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Yönetim aracını dağıtmak için gerekenler

Yönetim aracını dağıtmadan önce, bir uygulama kaydı oluşturmak ve yönetim kullanıcı arabirimini dağıtmak için bir Azure Active Directory (Azure AD) kullanıcısına sahip olmanız gerekir. Bu Kullanıcı şunları sağlamalıdır:

- Azure aboneliğinizde kaynak oluşturma izniniz var
- Azure AD uygulaması oluşturma izniniz vardır. [Gerekli izinlere](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)sahip olan yönergeleri izleyerek, kullanıcılarınızın gerekli izinlere sahip olup olmadığını denetlemek için bu adımları izleyin.

Yönetim aracını dağıttıktan ve yapılandırdıktan sonra, her şeyin çalıştığından emin olmak için kullanıcıdan yönetim kullanıcı arabirimini başlatması gerektiğini öneririz. Yönetim Kullanıcı arabirimini başlatan kullanıcının Windows sanal masaüstü kiracı 'sını görüntülemesine veya düzenlemesine izin veren bir rol ataması olmalıdır.

## <a name="set-up-powershell"></a>PowerShell 'i ayarlama

Yalnızca az ve Azure AD PowerShell modüllerine oturum açarak başlayın. Oturum açmak için şu adımları uygulayın:

1. PowerShell 'i yönetici olarak açın ve PowerShell betiklerini kaydettiğiniz dizine gidin.
2. Aşağıdaki cmdlet 'i çalıştırarak yönetim aracını oluşturmak için kullanmayı planladığınız Azure aboneliğinde sahip veya katkıda bulunan izinlerine sahip bir hesapla Azure 'da oturum açın:

    ```powershell
    Login-AzAccount
    ```

3. Az PowerShell modülü için kullandığınız hesapla Azure AD 'de oturum açmak üzere aşağıdaki cmdlet 'i çalıştırın:

    ```powershell
    Connect-AzureAD
    ```

4. Bundan sonra, RDS-Templates GitHub deposundan iki PowerShell betiğini kaydettiğiniz klasöre gidin.

Oturum açarken ek PowerShell cmdlet 'leri çalıştırmak için açık oturum açmak için kullandığınız PowerShell penceresini tutun.

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory uygulama kaydı oluşturma

Yönetim aracını başarıyla dağıtıp yapılandırmak için, önce [RDS-Templates GitHub deposundan](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) aşağıdaki PowerShell betiklerini indirmeniz gerekir

```powershell
Set-Location -Path "c:\temp"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/createWvdMgmtUxAppRegistration.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\createWvdMgmtUxAppRegistration.ps1"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/updateWvdMgmtUxApiUrl.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\updateWvdMgmtUxApiUrl.ps1"
```

Gerekli API izinleriyle uygulama kaydını oluşturmak için aşağıdaki komutları çalıştırın:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$azureSubscription = Get-AzSubscription | Out-GridView -PassThru
$subscriptionId = $azureSubscription.Id
Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Azure AD uygulama kaydını tamamladığınıza göre, yönetim aracını dağıtabilirsiniz.

## <a name="deploy-the-management-tool"></a>Yönetim aracını dağıtma

Yönetim aracını dağıtmak ve yeni oluşturduğunuz hizmet sorumlusu ile ilişkilendirmek için aşağıdaki PowerShell komutlarını çalıştırın:

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

Web uygulamasını oluşturduktan sonra, kullanıcılara başarıyla oturum açmak için Azure AD uygulamasına bir yeniden yönlendirme URI 'SI eklemeniz gerekir.

## <a name="set-the-redirect-uri"></a>Yeniden yönlendirme URI 'sini ayarla

Web uygulaması URL 'sini almak için aşağıdaki PowerShell komutlarını çalıştırın ve kimlik doğrulaması yeniden yönlendirme URI 'SI olarak ayarlayın (yanıt URL 'SI olarak da bilinir):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri
```

Yeniden yönlendirme URI 'SI eklemişseniz, yönetim aracının API arka uç hizmetiyle etkileşime geçmesini sağlamak için bir sonraki API URL 'sini güncelleştirmeniz gerekir.

## <a name="update-the-api-url-for-the-web-application"></a>Web uygulaması için API URL 'sini güncelleştirme

Web uygulaması ön ucundaki API URL yapılandırmasını güncelleştirmek için aşağıdaki betiği çalıştırın:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Artık Yönetim Aracı Web uygulamasını tam olarak yapılandırdığınıza göre, Azure AD uygulamasını doğrulamaya ve izin sağlamanıza yönelik bir zaman vardır.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Azure AD uygulamasını doğrulama ve onay sağlama

Azure AD uygulama yapılandırmasını doğrulamak ve izin sağlamak için:

1. Internet tarayıcınızı açın ve [Azure Portal](https://portal.azure.com/) Yönetici hesabınızla oturum açın.
2. Azure portal en üstündeki arama çubuğundan **uygulama kayıtları** arayın ve **Hizmetler** altında öğeyi seçin.
3. **Tüm uygulamalar** ' ı seçin ve [Azure Active Directory uygulama kaydı oluşturma](#create-an-azure-active-directory-app-registration)bölümünde PowerShell betiği için verdiğiniz benzersiz uygulama adını arayın.
4. Tarayıcının sol tarafındaki panelde **kimlik doğrulaması** ' nı seçin ve aşağıdaki görüntüde gösterildiği gibi yeniden yönlendirme URI 'sinin yönetim aracının Web uygulaması URL 'siyle aynı olduğundan emin olun.

   [![Girilen yeniden yönlendirme URI 'si ](../media/management-ui-redirect-uri-inline.png) ile kimlik doğrulama sayfası](../media/management-ui-redirect-uri-expanded.png#lightbox)

5. Sol bölmede, izinlerin eklendiğini doğrulamak için **API izinleri** ' ni seçin. Genel yöneticileriniz varsa, **yönetici için `tenantname` izin ver** düğmesini seçin ve kuruluşunuz için yönetici onayı sağlamak üzere iletişim istemini izleyin.

    [![API izinleri sayfası ](../media/management-ui-permissions-inline.png)](../media/management-ui-permissions-expanded.png#lightbox)

Artık yönetim aracını kullanmaya başlayabilirsiniz.

## <a name="use-the-management-tool"></a>Yönetim aracını kullanma

Yönetim aracını dilediğiniz zaman ayarladığınıza göre, her yerde dilediğiniz zaman başlatabilirsiniz. Aracın nasıl başlatılacağı aşağıda verilmiştir:

1. Web uygulamasının URL 'sini bir Web tarayıcısında açın. URL 'YI anımsamıyorsanız Azure 'da oturum açabilir, yönetim aracı için dağıttığınız App Service 'i bulabilir ve ardından URL 'YI seçebilirsiniz.
2. Windows sanal masaüstü kimlik bilgilerinizi kullanarak oturum açın.

   > [!NOTE]
   > Yönetim aracını yapılandırırken yönetici izni sağlamazsanız, oturum açan her kullanıcının aracı kullanabilmesi için kendi Kullanıcı onayını sağlaması gerekir.

3. Bir kiracı grubu seçmeniz istendiğinde, açılan listeden **varsayılan kiracı grubu** ' nu seçin.
4. **Varsayılan kiracı grubu**' nu seçtiğinizde, pencerenizin sol tarafında bir menü görünür. Bu menüde, kiracı grubunuzun adını bulun ve seçin.

   > [!NOTE]
   > Özel bir kiracı grubunuz varsa, açılan listeden seçim yapmak yerine adı el ile girin.

## <a name="report-issues"></a>Sorun bildirme

Yönetim Aracı veya diğer Windows Sanal Masaüstü araçlarıyla ilgili herhangi bir sorun yaşıyorsanız, GitHub 'da raporlamak için [Uzak Masaüstü Hizmetleri Azure Resource Manager şablonlarındaki](https://github.com/Azure/RDS-Templates/blob/master/README.md) yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Yönetim aracına nasıl dağıtılacağını ve bağlandığınızı öğrendiğinize göre, hizmet sorunlarını ve sistem durumu belgelerini izlemek için Azure hizmet durumunu nasıl kullanacağınızı öğrenebilirsiniz. Daha fazla bilgi edinmek için bkz. [hizmet uyarılarını ayarlama öğreticisi](set-up-service-alerts-2019.md).
