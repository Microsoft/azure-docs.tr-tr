---
title: Kodu ZIP veya WAR dosyası ile dağıtma-Azure App Service | Microsoft Docs
description: Uygulamanızı bir ZIP dosyası (veya Java geliştiricileri için bir WAR dosyası) ile Azure App Service nasıl dağıtacağınızı öğrenin.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: cephalin
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 83951f6408094b8d1e04d19650a5f2ef596be988
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801155"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Bir ZIP veya WAR dosyası ile Azure App Service uygulamanızı dağıtma

Bu makalede, [Azure App Service](overview.md)Web uygulamanızı dağıtmak IÇIN bir ZIP dosyası veya War dosyası nasıl kullanacağınız gösterilmektedir. 

Bu ZIP dosya dağıtımı, sürekli tümleştirme tabanlı dağıtımları destekleyen kudu hizmetini kullanır. Kudu, ZIP dosya dağıtımı için aşağıdaki işlevleri destekler: 

- Önceki bir dağıtımdan kalan dosyaların silinmesi.
- Paket geri yüklemeyi içeren varsayılan derleme işlemini açma seçeneği.
- Dağıtım komut dosyalarını çalıştırma dahil [dağıtım özelleştirmesi](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings).  
- Dağıtım günlükleri. 
- 2048 MB 'lık bir dosya boyutu sınırı.

Daha fazla bilgi için bkz. [kudu belgeleri](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

WAR dosya dağıtımı, Java Web uygulamanızı çalıştırmak için [War](https://wikipedia.org/wiki/WAR_(file_format)) dosyanızı App Service olarak dağıtır. Bkz. [WAR dosyasını dağıtma](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları gerçekleştirmek için:

* [Bir App Service uygulaması oluşturun](/azure/app-service/) veya başka bir öğretici için oluşturduğunuz bir uygulamayı kullanın.

## <a name="create-a-project-zip-file"></a>Proje ZIP dosyası oluşturma

>[!NOTE]
> Dosyaları bir ZIP dosyasına indirdiyseniz, önce dosyaları ayıklayın. Örneğin, GitHub 'dan bir ZIP dosyası indirdiyseniz, bu dosyayı olduğu gibi dağıtamazsınız. GitHub, App Service çalışmayan ek iç içe dizinler ekler. 
>

Yerel bir Terminal penceresinde, uygulama projenizin kök dizinine gidin. 

Bu dizin, Web uygulamanıza, _Dizin. html_, _index. php_ve _app. js_gibi giriş dosyasını içermelidir. Ayrıca, _Project. JSON_, _besteci. JSON_, _Package. JSON_, _Bower. JSON_ve _requirements. txt_gibi paket yönetim dosyalarını da içerebilir.

Projenizdeki tüm öğeleri içeren bir ZIP arşivi oluşturun. Aşağıdaki komut terminalinizdeki varsayılan aracı kullanmaktadır:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Yukarıdaki uç nokta Linux uygulama hizmetleri için şu anda çalışmıyor. Bunun yerine FTP veya [ZIP dağıtım API 'sini](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) kullanmayı düşünün.

## <a name="deploy-zip-file-with-azure-cli"></a>Azure CLı ile ZIP dosyası dağıtma

Azure CLı sürümünüzün 2.0.21 veya üzeri olduğundan emin olun. Sahip olduğunuz sürümü görmek için, Terminal pencerenizde `az --version` komutunu çalıştırın.

[Az WebApp Deployment Source config-ZIP](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) komutunu kullanarak KARŞıYA yüklenen ZIP dosyasını Web uygulamanıza dağıtın.  

Aşağıdaki örnek, karşıya yüklediğiniz ZIP dosyasını dağıtır. Yerel bir Azure CLı yüklemesi kullanırken, için `--src`yerel ZIP dosyanızın yolunu belirtin.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Bu komut ZIP içindeki dosyaları ve dizinleri App Service uygulama klasörünüze (`\home\site\wwwroot`) dağıtır ve uygulamayı yeniden başlatır.

Varsayılan olarak, dağıtım altyapısı bir ZIP dosyasının olduğu gibi çalıştırılmaya hazır olduğunu varsayar ve herhangi bir derleme Otomasyonu çalıştırmaz. Bir [Git dağıtımında](deploy-local-git.md)olduğu gibi aynı derleme Otomasyonu 'nu etkinleştirmek için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu `SCM_DO_BUILD_DURING_DEPLOYMENT` çalıştırarak uygulama ayarını ayarlayın:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```



Daha fazla bilgi için bkz. [kudu belgeleri](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR dosyası dağıtma

App Service bir WAR dosyasını dağıtmak için, ' a bir POST isteği `https://<app_name>.scm.azurewebsites.net/api/wardeploy`gönderin. POST isteğinin ileti gövdesinde .war dosyası bulunmalıdır. Uygulamanızın dağıtım kimlik bilgileri, HTTP BASIC kimlik doğrulaması kullanılarak istekte belirtilir.

HTTP temel kimlik doğrulaması için App Service dağıtım kimlik bilgilerinizin olması gerekir. Dağıtım kimlik bilgilerinizi ayarlama hakkında bilgi için bkz. [Kullanıcı düzeyi kimlik bilgilerini ayarlama ve sıfırlama](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Kıvrımlı ile

Aşağıdaki örnek, bir. war dosyasını dağıtmak için kıvrımlı aracı kullanır. Yer tutucuları `<username>` `<war-file-path>`, ve ile `<app-name>`değiştirin. Kıvrımlı tarafından istendiğinde, parolayı yazın.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>PowerShell ile

Aşağıdaki örnek [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) ,. war dosyasını karşıya yükle kullanır. Yer tutucuları `<group-name>` `<app-name>`, ve ile `<war-file-path>`değiştirin.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Sonraki adımlar

Daha gelişmiş dağıtım senaryoları için [Git Ile Azure 'a](deploy-local-git.md)dağıtım yapmayı deneyin. Azure 'a git tabanlı dağıtım, sürüm denetimi, paket geri yükleme, MSBuild ve daha fazlasını sağlar.

## <a name="more-resources"></a>Daha fazla kaynak

* [Kudu Zip dosyasından dağıtma](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service dağıtım kimlik bilgileri](deploy-ftp.md)
