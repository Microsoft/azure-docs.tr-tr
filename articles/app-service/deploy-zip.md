---
title: Kodu ZIP veya WAR dosyası ile dağıtma
description: Uygulamanızı bir ZIP dosyası (veya Java geliştiricileri için bir WAR dosyası) ile Azure App Service nasıl dağıtacağınızı öğrenin.
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9f59576ea66b72a492e1e6c665a51258861842dd
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833031"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Bir ZIP veya WAR dosyası ile Azure App Service uygulamanızı dağıtma

Bu makalede, [Azure App Service](overview.md)Web uygulamanızı dağıtmak IÇIN bir ZIP dosyası veya War dosyası nasıl kullanacağınız gösterilmektedir. 

Bu ZIP dosya dağıtımı, sürekli tümleştirme tabanlı dağıtımları destekleyen kudu hizmetini kullanır. Kudu, ZIP dosya dağıtımı için aşağıdaki işlevleri destekler: 

- Önceki bir dağıtımdan kalan dosyaların silinmesi.
- Paket geri yüklemeyi içeren varsayılan derleme işlemini açma seçeneği.
- Dağıtım komut dosyalarını çalıştırma dahil dağıtım özelleştirmesi.  
- Dağıtım günlükleri. 
- 2048 MB 'lık bir dosya boyutu sınırı.

Daha fazla bilgi için bkz. [kudu belgeleri](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

WAR dosya dağıtımı, Java Web uygulamanızı çalıştırmak için [War](https://wikipedia.org/wiki/WAR_(file_format)) dosyanızı App Service olarak dağıtır. Bkz. [WAR dosyasını dağıtma](#deploy-war-file).

> [!NOTE]
> Kullanırken `ZipDeploy` , dosyalar yalnızca, zaman damgaları zaten dağıtıldıklarıyla eşleşmezse kopyalanır. Çıkışları önbelleğe alan bir yapı işlemi kullanarak bir ZIP oluşturmak daha hızlı dağıtımlar oluşmasına neden olabilir. Daha fazla bilgi için bkz. [bir zip dosyasından veya URL 'Den dağıtma](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlayabilmeniz için [bir App Service uygulaması oluşturun](./index.yml)veya başka bir öğretici için oluşturduğunuz uygulamayı kullanın.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Yukarıdaki uç nokta Linux uygulama hizmetleri için şu anda çalışmıyor. Bunun yerine FTP veya [ZIP dağıtım API 'sini](faq-app-service-linux.md#continuous-integration-and-deployment) kullanmayı düşünün.

## <a name="deploy-zip-file-with-azure-cli"></a>Azure CLı ile ZIP dosyası dağıtma

[Az WebApp Deployment Source config-ZIP](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_zip) komutunu kullanarak KARŞıYA yüklenen ZIP dosyasını Web uygulamanıza dağıtın.  

Aşağıdaki örnek, karşıya yüklediğiniz ZIP dosyasını dağıtır. Yerel bir Azure CLı yüklemesi kullanırken, için yerel ZIP dosyanızın yolunu belirtin `--src` .

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Bu komut ZIP içindeki dosyaları ve dizinleri App Service uygulama klasörünüze (`\home\site\wwwroot`) dağıtır ve uygulamayı yeniden başlatır.

Varsayılan olarak, dağıtım altyapısı bir ZIP dosyasının olduğu gibi çalıştırılmaya hazır olduğunu varsayar ve herhangi bir derleme Otomasyonu çalıştırmaz. Bir [Git dağıtımında](deploy-local-git.md)olduğu gibi aynı derleme Otomasyonu 'nu etkinleştirmek için `SCM_DO_BUILD_DURING_DEPLOYMENT` [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırarak uygulama ayarını ayarlayın:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Daha fazla bilgi için bkz. [kudu belgeleri](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR dosyası dağıtma

App Service bir WAR dosyasını dağıtmak için, ' a bir POST isteği gönderin `https://<app-name>.scm.azurewebsites.net/api/wardeploy` . POST isteğinin ileti gövdesinde .war dosyası bulunmalıdır. Uygulamanızın dağıtım kimlik bilgileri, HTTP BASIC kimlik doğrulaması kullanılarak istekte belirtilir.

`/api/wardeploy`WAR dosyaları dağıtıldığında her zaman kullanın. Bu API, WAR dosyanızı genişlettikten sonra paylaşılan dosya sürücüsüne yerleştirmeyecektir. diğer dağıtım API 'Lerinin kullanılması tutarsız davranışa neden olabilir. 

HTTP temel kimlik doğrulaması için App Service dağıtım kimlik bilgilerinizin olması gerekir. Dağıtım kimlik bilgilerinizi ayarlama hakkında bilgi için bkz. [Kullanıcı düzeyi kimlik bilgilerini ayarlama ve sıfırlama](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Kıvrımlı ile

Aşağıdaki örnek, bir. war dosyasını dağıtmak için kıvrımlı aracı kullanır. Yer tutucuları, `<username>` `<war-file-path>` ve ile değiştirin `<app-name>` . Kıvrımlı tarafından istendiğinde, parolayı yazın.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>PowerShell ile

Aşağıdaki örnek [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) ,. war dosyasını karşıya yükle kullanır. Yer tutucuları, `<group-name>` `<app-name>` ve ile değiştirin `<war-file-path>` .

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Sonraki adımlar

Daha gelişmiş dağıtım senaryoları için [Git Ile Azure 'a](deploy-local-git.md)dağıtım yapmayı deneyin. Azure 'a git tabanlı dağıtım, sürüm denetimi, paket geri yükleme, MSBuild ve daha fazlasını sağlar.

## <a name="more-resources"></a>Diğer kaynaklar

* [Kudu: zip dosyasından dağıtma](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service dağıtım kimlik bilgileri](deploy-ftp.md)
