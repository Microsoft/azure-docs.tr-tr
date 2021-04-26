---
title: FTP/S kullanarak içerik dağıtma
description: Uygulamanızı FTP veya FTPS kullanarak Azure App Service dağıtma hakkında bilgi edinin. Şifrelenmemiş FTP 'yi devre dışı bırakarak Web sitesi güvenliğini geliştirebilirsiniz.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: fd856ee47c1100292f7558bb0fa2a1772951a3cb
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832384"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Uygulamanızı FTP/S kullanarak Azure App Service dağıtma

Bu makalede, [Azure App Service](./overview.md)için Web uygulamanızı, mobil uygulama arka ucunu veya API uygulamanızı DAĞıTMAK üzere FTP veya FTPS 'nin nasıl kullanılacağı gösterilmektedir.

Uygulamanızın FTP/S uç noktası zaten etkin. FTP/S dağıtımını etkinleştirmek için yapılandırma gerekmez.

> [!NOTE]
> Eski dağıtım deneyimi olan Azure portal **Geliştirme Merkezi (klasik)** sayfası mart, 2021 ' de kullanım dışı olacaktır. Bu değişiklik uygulamanızdaki mevcut dağıtım ayarlarını etkilemez ve **Dağıtım Merkezi** sayfasında uygulama dağıtımını yönetmeye devam edebilirsiniz.

## <a name="get-deployment-credentials"></a>Dağıtım kimlik bilgilerini al

1. Uygulama kapsamı kimlik bilgilerini kopyalamak veya kullanıcı kapsamı kimlik bilgilerini ayarlamak için [Azure App Service dağıtım kimlik bilgilerini yapılandırma](deploy-configure-credentials.md) bölümündeki yönergeleri izleyin. Kimlik bilgilerini kullanarak uygulamanızın FTP/S uç noktasına bağlanabilirsiniz.

1. Aşağıdaki biçimde, kimlik bilgisi kapsamı seçiminize bağlı olarak FTP Kullanıcı adını aşağıdaki biçimde oluşturur:

    | Uygulama kapsamı | Kullanıcı kapsamı |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    App Service, FTP/S uç noktası uygulamalar arasında paylaşılır. Kullanıcı kapsamı kimlik bilgileri belirli bir kaynakla bağlantılı olmadığından, yukarıda gösterildiği gibi, kullanıcı kapsamı kullanıcı adını uygulama adıyla sonuna eklemeniz gerekir.

## <a name="get-ftps-endpoint"></a>FTP/S uç noktası al
    
# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Uygulamanızın dağıtım kimlik bilgilerini (**Dağıtım Merkezi**  >  **FTP kimlik bilgileri**) kopyaladığınız Yönetim sayfasında, **FTPS uç noktası**' nı kopyalayın.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[Az WebApp Deployment List-Publishing-Profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) komutunu çalıştırın. Aşağıdaki örnek, çıktısından FTP/S uç noktalarını ayıklamak için bir [Jmes yolu](https://jmespath.org/) kullanır.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Her uygulamanın iki FTP/S uç noktası vardır, diğeri salt okunurdur ( `profileName` içerir `ReadOnly` ) ve veri kurtarma senaryolarında olur. FTP ile dosya dağıtmak için okuma-yazma uç noktasının URL 'sini kopyalayın.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) komutunu çalıştırın. Aşağıdaki örnek, XML çıktısından FTP/S uç noktasını ayıklar.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Azure 'a dosya dağıtma

1. FTP istemcinizden (örneğin, [Visual Studio](https://www.visualstudio.com/vs/community/), [siduck](https://cyberduck.io/)veya [WinSCP](https://winscp.net/index.php)), uygulamanıza bağlanmak için topladığınız bağlantı bilgilerini kullanın.
2. Dosyalarınızı ve ilgili dizin yapılarını Azure 'daki [ **/site/Wwwroot** dizinine](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (veya WebJobs için **/site/Wwwroot/App_Data/Jobs/** dizininden) kopyalayın.
3. Uygulamanın düzgün çalıştığını doğrulamak için uygulamanızın URL 'sine gidin. 

> [!NOTE] 
> [Git tabanlı dağıtımların](deploy-local-git.md) ve [ZIP dağıtımının](deploy-zip.md)aksine, FTP dağıtımı derleme Otomasyonu 'nu desteklemez, örneğin: 
>
> - bağımlılık geri yüklemeleri (NuGet, NPM, PI ve besteci tahminleri gibi)
> - .NET ikili dosyalarının derlenmesi
> - web.config oluşturma ( [Node.js bir örnek](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Bu gerekli dosyaları yerel makinenizde el ile oluşturun ve ardından uygulamanızla birlikte dağıtın.
>

## <a name="enforce-ftps"></a>FTPS 'yi zorla

Gelişmiş güvenlik için yalnızca TLS/SSL üzerinden FTP 'ye izin vermeniz gerekir. FTP dağıtımını kullanmıyorsanız hem FTP hem de FTPS 'yi devre dışı bırakabilirsiniz.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

1. Uygulamanızın kaynak sayfasında, [Azure Portal](https://portal.azure.com)' de,   >  sol gezinmede yapılandırma **genel ayarları** ' nı seçin.

2. Şifrelenmemiş FTP 'yi devre dışı bırakmak için, **FTPS** ' yi yalnızca **FTP durumunda** seçin. Hem FTP hem de FTPS 'yi tamamen devre dışı bırakmak için **devre dışı** seçeneğini belirleyin İşlemi tamamladıktan sonra **Kaydet**’e tıklayın. **Yalnızca FTPS** kullanıyorsanız, Web uygulamanızın **TLS/SSL ayarları** dikey penceresine giderek TLS 1,2 veya üstünü zorlayabilmeniz gerekir. TLS 1,0 ve 1,1 **yalnızca FTPS** ile desteklenmez.

    ![FTP/S 'yi devre dışı bırak](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Bu bağımsız değişkenle [az WebApp config Set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) komutunu çalıştırın `--ftps-state` .

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

İçin olası değerler `--ftps-state` `AllAllowed` (FTP ve FTPS etkin), `Disabled` (FTP ve FTPS devre dışı) ve `FtpsOnly` (yalnızca FTPS).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Parametresiyle [set-AzWebApp](/powershell/module/az.websites/set-azwebapp) komutunu çalıştırın `-FtpsState` .

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

İçin olası değerler `--ftps-state` `AllAllowed` (FTP ve FTPS etkin), `Disabled` (FTP ve FTPS devre dışı) ve `FtpsOnly` (yalnızca FTPS).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP dağıtımı sorunlarını giderme

- [FTP dağıtımı ile ilgili sorunları nasıl giderebilirim?](#how-can-i-troubleshoot-ftp-deployment)
- [Kodumu FTP ve yayınlayamıyorum. Sorunu nasıl çözebilirim?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Pasif mod aracılığıyla Azure App Service FTP 'ye nasıl bağlanabilirim?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>FTP dağıtımı ile ilgili sorunları nasıl giderebilirim?

FTP dağıtımında sorun gidermeye yönelik ilk adım, bir çalışma zamanı uygulama sorunundan bir dağıtım sorununu yalıtmaya yöneliktir.

Dağıtım sorunu genellikle uygulamanıza hiçbir dosya veya yanlış dosya dağıtılmasına neden olur. FTP dağıtımınızı inceleyerek veya alternatif bir dağıtım yolu (örneğin, kaynak denetimi) seçerek sorun giderebilirsiniz.

Çalışma zamanı uygulama sorunu genellikle uygulamanıza dağıtılan doğru dosya kümesine, ancak yanlış uygulama davranışına neden olur. Çalışma zamanında kod davranışına odaklanarak ve belirli hata yollarını inceleyerek sorun gidermeye devam edebilirsiniz.

Bir dağıtımı veya çalışma zamanı sorununu anlamak için bkz. [dağıtım ve çalışma zamanı sorunları](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>FTP kullanamıyorum ve kodumu yayımlayamıyorum. Sorunu nasıl çözebilirim?
Doğru [ana bilgisayar adını](#get-ftps-endpoint) ve [kimlik bilgilerini](#get-deployment-credentials)girdiğinizden emin olun. Ayrıca, makinenizde bulunan aşağıdaki FTP bağlantı noktalarının bir güvenlik duvarı tarafından engellenmediğinden emin olun:

- FTP denetim bağlantı noktası: 21, 990
- FTP veri bağlantısı bağlantı noktası: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Pasif mod aracılığıyla Azure App Service FTP 'ye nasıl bağlanabilirim?
Azure App Service hem etkin hem de Pasif mod aracılığıyla bağlanmayı destekler. Dağıtım makineleriniz genellikle bir güvenlik duvarının arkasında (işletim sisteminde veya bir ev veya iş ağının parçası olarak) olduğundan Pasif mod tercih edilir. [WinSCP belgelerinden bir örnek](https://winscp.net/docs/ui_login_connection)görüntüleyin. 

## <a name="more-resources"></a>Diğer kaynaklar

* [Azure App Service için yerel git dağıtımı](deploy-local-git.md)
* [Azure App Service dağıtım kimlik bilgileri](deploy-configure-credentials.md)
* [Örnek: bir Web uygulaması oluşturun ve FTP (Azure CLI) ile dosyaları dağıtın](./scripts/cli-deploy-ftp.md).
* [Örnek: FTP (PowerShell) kullanarak bir Web uygulamasına dosya yükleme](./scripts/powershell-deploy-ftp.md).
