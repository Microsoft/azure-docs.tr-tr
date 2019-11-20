---
title: Linux 'ta App Service 'ye giriş-Azure | Microsoft Docs
description: Linux’ta Azure App Service hakkında bilgi edinin.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: msangapu-msft
manager: gwallace
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 1bbcd5e4f8c6a429def84ad77d7dd93fa11b7324
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819692"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Linux’ta Azure App Service’e Giriş

[Azure App Service](../overview.md) , Web sitelerini ve Web uygulamalarını barındırmak için optimize edilmiş, tam olarak yönetilen bir işlem platformudur. Müşteriler Linux’ta App Service’i kullanarak desteklenen uygulama yığınları için Linux’ta yerel olarak web uygulamaları barındırabilir.

## <a name="languages"></a>Diller

Linux’ta App Service, geliştirici üretkenliğini artırmaya yönelik çeşitli Yerleşik görüntüleri destekler. Diller şunlardır: node. js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core ve Ruby. En son dilleri ve desteklenen sürümleri görüntülemek için [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) çalıştırın. Yerleşik görüntülerde uygulamanızın gerektirdiği çalışma zamanı desteklenmiyorsa, [kendi Docker görüntünüzü](tutorial-custom-docker-image.md) oluşturarak Kapsayıcılar için Web App’e dağıtmaya yönelik yönergeler sunulmaktadır.

## <a name="deployments"></a>Dağıtımlar

* FTP
* Yerel Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Hazırlama ortamları
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) ve DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Konsol, Yayımlama ve Hata Ayıklama

* Ortamlar
* Dağıtımlar
* Temel konsol
* SSH

## <a name="scaling"></a>Ölçeklendirme

* Müşteriler [App Service planı](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json) katmanlarını değiştirerek web uygulamalarının ölçeğini büyütüp küçültebilirler

## <a name="locations"></a>Konumlar

[Azure Durum Panosu](https://azure.microsoft.com/status)’nu inceleyin.

## <a name="limitations"></a>Sınırlamalar

Azure portalı, yalnızca şu anda Kapsayıcılar için Web App ile kullanılabilen özellikleri gösterir. Yeni özellikler etkinleştirildikçe portalda görünmeye başlayacaktır.

Linux üzerinde App Service yalnızca [ücretsiz, temel, standart ve Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) App Service planlarında desteklenir ve [paylaşılan](https://azure.microsoft.com/pricing/details/app-service/plans/) bir katmana sahip değildir. Linux olmayan Web Apps barındırmakta olan bir App Service planında Linux Web uygulaması oluşturamazsınız.  

Aynı kaynak grubu için geçerli bir sınırlamaya bağlı olarak, aynı bölgedeki Windows ve Linux uygulamalarını karıştıramazsınız.

## <a name="troubleshooting"></a>Sorun giderme

Uygulamanız başlatılamazsa veya uygulamanızdan alınan günlük kayıtlarına bakmak isterseniz LogFiles dizinindeki Docker günlüklerini inceleyin. Bu dizine SCM siteniz üzerinden veya FTP aracılığıyla erişebilirsiniz. `stdout` ve `stderr` kapsayıcısından günlüğe kaydetmek için **App Service günlükleri**altında **Docker kapsayıcı günlüğünü** etkinleştirmeniz gerekir. Ayar hemen yürürlüğe girer. App Service değişikliği algılar ve kapsayıcıyı otomatik olarak yeniden başlatır.

SCM sitesine **Geliştirme Araçları** menüsündeki **Gelişmiş Araçlar**’dan erişebilirsiniz.

![Kudu kullanarak Docker günlüklerini görüntüleme][1]

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makaleler farklı dillerde yazılmış web uygulamalarıyla Linux'ta App Service hizmetini kullanmaya başlamanızı sağlar:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Birden çok kapsayıcılı uygulamalar](quickstart-multi-container.md)

Linux üzerinde App Service hakkında daha fazla bilgi için bkz.:

* [Linux’ta App Service hakkında SSS](app-service-linux-faq.md)
* [Linux'ta App Service için SSH desteği](app-service-linux-ssh-support.md)
* [App Service’te hazırlık ortamları ayarlama](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub sürekli dağıtımı](app-service-linux-ci-cd.md)

Sorularınızı ve çekincelerinizi [forumumuzda](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview) paylaşabilirsiniz.

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
