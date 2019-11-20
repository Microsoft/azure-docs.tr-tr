---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 4f3236c0a167a2b6f7586c6cb5fea8e30f55a86c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954069"
---
## <a name="rest"></a>ZIP dosyasını REST API 'lerle dağıtma 

. Zip dosyasını Azure 'daki uygulamanıza dağıtmak için [dağıtım HIZMETI REST API 'lerini](https://github.com/projectkudu/kudu/wiki/REST-API) kullanabilirsiniz. Dağıtım yapmak için https://< app_name >. scm. azurewebsites. NET/API/zipdeploy 'a bir POST isteği gönderin. POST isteğinin ileti gövdesinde. zip dosyası içermesi gerekir. Uygulamanızın dağıtım kimlik bilgileri, HTTP BASIC kimlik doğrulaması kullanılarak istekte belirtilir. Daha fazla bilgi için bkz [. ZIP Push dağıtım başvurusu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

HTTP temel kimlik doğrulaması için App Service dağıtım kimlik bilgilerinizin olması gerekir. Dağıtım kimlik bilgilerinizi ayarlama hakkında bilgi için bkz. [Kullanıcı düzeyi kimlik bilgilerini ayarlama ve sıfırlama](../articles/app-service/deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Kıvrımlı ile

Aşağıdaki örnek, bir. zip dosyasını dağıtmak için kıvrımlı aracı kullanır. `<username>`Yer tutucuları `<password>` değiştirin,`<app_name>`, ve. `<zip_file_path>` Kıvrımlı tarafından istendiğinde, parolayı yazın.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Bu istek, karşıya yüklenen. zip dosyasından gönderim dağıtımını tetikler. Aşağıdaki kıvrımlı örnekte gösterildiği gibi `https://<app_name>.scm.azurewebsites.net/api/deployments` uç noktasını kullanarak geçerli ve geçmiş dağıtımları inceleyebilirsiniz. Yeniden, uygulamanızın `<app_name>` adıyla ve `<deployment_user>` dağıtım kimlik bilgilerinizin Kullanıcı adıyla değiştirin.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>PowerShell ile

Aşağıdaki örnek [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) ' i. zip dosyasını karşıya yükle kullanır. Yer tutucuları `<group-name>` `<app-name>`, ve ile `<zip-file-path>`değiştirin.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

Bu istek, karşıya yüklenen. zip dosyasından gönderim dağıtımını tetikler. 

Geçerli ve geçmiş dağıtımları gözden geçirmek için aşağıdaki komutları çalıştırın. Yeniden, ve `<deployment-user>` `<deployment-password>` yer`<app-name>` tutucularını değiştirin.

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
