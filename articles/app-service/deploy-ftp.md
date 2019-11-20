---
title: FTP/S-Azure App Service kullanarak içerik dağıtma | Microsoft Docs
description: Uygulamanızı FTP veya FTPS kullanarak Azure App Service dağıtma hakkında bilgi edinin.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/18/2019
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 6e8a6820b3cf3031f11ab04d9baf4a7888491c81
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098040"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Uygulamanızı FTP/S kullanarak Azure App Service dağıtma

Bu makalede, [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)için Web uygulamanızı, mobil uygulama arka ucunu veya API uygulamanızı DAĞıTMAK üzere FTP veya FTPS 'nin nasıl kullanılacağı gösterilmektedir.

Uygulamanızın FTP/S uç noktası zaten etkin. FTP/S dağıtımını etkinleştirmek için yapılandırma gerekmez.

## <a name="open-ftp-dashboard"></a>FTP panosunu aç

[Azure Portal](https://portal.azure.com), uygulamanızın [kaynak sayfasını](../azure-resource-manager/manage-resources-portal.md#manage-resources)açın.

FTP panosunu açmak için **Deployment Center** > **FTP** > **panosu**' na tıklayın.

![FTP panosunu aç](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>FTP bağlantı bilgilerini al

FTP panosunda, FTPS uç noktası ve uygulama kimlik bilgilerini kopyalamak için **Kopyala** ' ya tıklayın.

![FTP bilgilerini Kopyala](./media/app-service-deploy-ftp/ftp-dashboard.png)

Uygulama **kimlik bilgilerini** , her bir uygulama için benzersiz olduğundan uygulamanıza dağıtmak için kullanmanız önerilir. Ancak, **Kullanıcı kimlik bilgileri**' ne tıklarsanız, aboneliğinizdeki tüm App SERVICE uygulamalarda FTP/S oturumu için kullanabileceğiniz Kullanıcı düzeyi kimlik bilgilerini ayarlayabilirsiniz.

> [!NOTE]
> Kullanıcı düzeyindeki kimlik bilgilerini kullanarak bir FTP/FTPS uç noktası için kimlik doğrulaması, aşağıdaki biçimde bir Kullanıcı adı talep ediyor: 
>
>`<app-name>\<user-name>`
>
> Kullanıcı düzeyi kimlik bilgileri, belirli bir kaynak değil kullanıcıya bağlı olduğundan, oturum açma işlemini doğru uygulama uç noktasına yönlendirmek için Kullanıcı adının bu biçimde olması gerekir.
>

## <a name="deploy-files-to-azure"></a>Azure 'a dosya dağıtma

1. FTP istemcinizden (örneğin, [Visual Studio](https://www.visualstudio.com/vs/community/), [siduck](https://cyberduck.io/)veya [WinSCP](https://winscp.net/index.php)), uygulamanıza bağlanmak için topladığınız bağlantı bilgilerini kullanın.
2. Dosyalarınızı ve ilgili dizin yapılarını Azure 'daki [ **/site/Wwwroot** dizinine](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (veya WebJobs için **/site/Wwwroot/App_Data/Jobs/** Directory) kopyalayın.
3. Uygulamanın düzgün çalıştığını doğrulamak için uygulamanızın URL 'sine gidin. 

> [!NOTE] 
> [Git tabanlı dağıtımlardan](deploy-local-git.md)farklı olarak, FTP dağıtımı aşağıdaki dağıtım önerilerini desteklemez: 
>
> - bağımlılık geri yüklemeleri (NuGet, NPM, PI ve besteci tahminleri gibi)
> - .NET ikili dosyalarının derlenmesi
> - Web. config dosyası oluşturma (bir [Node. js örneği](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Bu gerekli dosyaları yerel makinenizde el ile oluşturun ve ardından uygulamanızla birlikte dağıtın.
>

## <a name="enforce-ftps"></a>FTPS 'yi zorla

Gelişmiş güvenlik için yalnızca SSL üzerinden FTP 'ye izin vermeniz gerekir. FTP dağıtımını kullanmıyorsanız hem FTP hem de FTPS 'yi devre dışı bırakabilirsiniz.

Uygulamanızın kaynak sayfasında, [Azure Portal](https://portal.azure.com)' de, sol gezinmede **yapılandırma** > **genel ayarları** ' nı seçin.

Şifrelenmemiş FTP 'yi devre dışı bırakmak için, **FTPS** ' yi yalnızca **FTP durumunda**seçin. Hem FTP hem de FTPS 'yi tamamen devre dışı bırakmak için **devre dışı**seçeneğini belirleyin İşlemi tamamladıktan sonra **Kaydet**’e tıklayın. **Yalnızca FTPS**kullanıyorsanız, Web uygulamanızın **TLS/SSL ayarları** dikey penceresine giderek TLS 1,2 veya üstünü zorlayabilmeniz gerekir. TLS 1,0 ve 1,1 **yalnızca FTPS**ile desteklenmez.

![FTP/S 'yi devre dışı bırak](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Betiklerle otomatikleştirme

[Azure CLI](/cli/azure)kullanarak FTP dağıtımı için bkz. [Web UYGULAMASı oluşturma ve FTP ile dosya DAĞıTMA (Azure CLI)](./scripts/cli-deploy-ftp.md).

[Azure PowerShell](/cli/azure)kullanarak FTP dağıtımı için bkz. [FTP (PowerShell) kullanarak bir Web uygulamasına dosya yükleme](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP dağıtımı sorunlarını giderme

- [FTP dağıtımı ile ilgili sorunları nasıl giderebilirim?](#how-can-i-troubleshoot-ftp-deployment)
- [FTP kullanamıyorum ve kodumu yayımlayamıyorum. Sorunu nasıl çözebilirim?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Pasif mod aracılığıyla Azure App Service FTP 'ye nasıl bağlanabilirim?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>FTP dağıtımı ile ilgili sorunları nasıl giderebilirim?

FTP dağıtımında sorun gidermeye yönelik ilk adım, bir çalışma zamanı uygulama sorunundan bir dağıtım sorununu yalıtmaya yöneliktir.

Dağıtım sorunu genellikle uygulamanıza hiçbir dosya veya yanlış dosya dağıtılmasına neden olur. FTP dağıtımınızı inceleyerek veya alternatif bir dağıtım yolu (örneğin, kaynak denetimi) seçerek sorun giderebilirsiniz.

Çalışma zamanı uygulama sorunu genellikle uygulamanıza dağıtılan doğru dosya kümesine, ancak yanlış uygulama davranışına neden olur. Çalışma zamanında kod davranışına odaklanarak ve belirli hata yollarını inceleyerek sorun gidermeye devam edebilirsiniz.

Bir dağıtımı veya çalışma zamanı sorununu anlamak için bkz. [dağıtım ve çalışma zamanı sorunları](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Kodumu FTP ve yayınlayamıyorum. Sorunu nasıl çözebilirim?
Doğru ana bilgisayar adını ve [kimlik bilgilerini](#open-ftp-dashboard)girdiğinizden emin olun. Ayrıca, makinenizde bulunan aşağıdaki FTP bağlantı noktalarının bir güvenlik duvarı tarafından engellenmediğinden emin olun:

- FTP denetim bağlantı noktası: 21
- FTP veri bağlantısı bağlantı noktası: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Pasif mod aracılığıyla Azure App Service FTP 'ye nasıl bağlanabilirim?
Azure App Service hem etkin hem de Pasif mod aracılığıyla bağlanmayı destekler. Dağıtım makineleriniz genellikle bir güvenlik duvarının arkasında (işletim sisteminde veya bir ev veya iş ağının parçası olarak) olduğundan Pasif mod tercih edilir. [WinSCP belgelerinden bir örnek](https://winscp.net/docs/ui_login_connection)görüntüleyin. 

## <a name="next-steps"></a>Sonraki adımlar

Daha gelişmiş dağıtım senaryoları için [Git Ile Azure 'a](deploy-local-git.md)dağıtım yapmayı deneyin. Azure 'a git tabanlı dağıtım, sürüm denetimi, paket geri yükleme, MSBuild ve daha fazlasını sağlar.

## <a name="more-resources"></a>Daha fazla kaynak

* [Azure App Service dağıtım kimlik bilgileri](deploy-configure-credentials.md)
