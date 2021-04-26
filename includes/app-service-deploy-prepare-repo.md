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
ms.openlocfilehash: 93bfb515c46413400ecd47105de378c7b677cecd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564976"
---
## <a name="prepare-your-repository"></a>Deponuzu hazırlayın

Azure App Service yapı sunucusundan otomatik derlemeler almak için, depo köklerinizin projenizde doğru dosyalara sahip olduğundan emin olun.

| Çalışma Zamanı | Kök dizin dosyaları |
|-|-|
| ASP.NET (yalnızca Windows) | _*. sln_, _*. csproj_ veya _default. aspx_ |
| ASP.NET Core | _*. sln_ veya _*. csproj_ |
| PHP | _index. php_ |
| Ruby (yalnızca Linux) | _Gemfile_ |
| Node.js | Bir başlangıç betiğine _server.js_, _app.js_ veya _package.js_ |
| Python | _\* . kopyala_, _requirements.txt_ veya _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default. asp_, _index.htm_, _index.html_ veya _iisstart.htm_ |
| Web İşleri | _\<job_name>/Run.\<extension>_ _uygulama \_ verileri/işleri/_ sürekli Web işleri için sürekli veya tetiklenen Web işleri için _uygulama \_ verileri/işleri_ /işleri. Daha fazla bilgi için bkz. [kudu WebJobs belgeleri](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| İşlevler | Bkz. [Azure işlevleri Için sürekli dağıtım](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Dağıtımınızı özelleştirmek için depo köküne bir *. Deployment* dosyası ekleyin. Daha fazla bilgi için bkz. dağıtımları ve [özel dağıtım betiğini](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) [Özelleştirme](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) .

> [!NOTE]
> Visual Studio 'da geliştirirseniz, [Visual Studio 'nun sizin için bir depo oluşturmasına](/azure/devops/repos/git/creatingrepo?tabs=visual-studio)izin verin. Proje git kullanılarak dağıtılmaya hemen hazırlanın.
>

