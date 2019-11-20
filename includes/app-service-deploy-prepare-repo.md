---
title: include dosyası
description: include dosyası
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836743"
---
## <a name="prepare-your-repository"></a>Deponuzu hazırlama

Azure App Service Kudu derleme sunucusundan otomatik derlemeler almak için depo kökünüzde projenize doğru dosya olduğundan emin olun.

| Çalışma Zamanı | Kök dizin dosyaları |
|-|-|
| ASP.NET (yalnızca Windows) | _*.sln_, _*.csproj_, veya _default.aspx_ |
| ASP.NET Core | _*.sln_ veya _*.csproj_ |
| PHP | _index.php_ |
| Ruby (yalnızca Linux) | _Gemfile_ |
| Node.js | _Server.js_, _app.js_, veya _package.json_ bir başlangıç betiği ile |
| Python | _\*.PY_, _requirements.txt_, veya _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_, veya  _iisstart.htm_ |
| WebJobs | _\<job_name > / çalıştırın. \<uzantısı >_ altında _uygulama\_veri/iş/continuous_ sürekli WebJobs için veya _uygulama\_veri/iş/triggered_ tetiklenen için Web işleri. Daha fazla bilgi için [Kudu Web işleri belgeleri](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| İşlevler | Bkz: [Azure işlevleri için sürekli dağıtım](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Dağıtımınızı özelleştirmek için dahil bir *.deployment* depo köküne dosya. Daha fazla bilgi için [dağıtımlarını özelleştirme](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) ve [özel dağıtım betiği](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Visual Studio'da oluşturursanız, izin [Visual Studio sizin için bir depo oluşturma](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Projeyi hemen Git kullanarak dağıtılmaya hazırdır.
>

