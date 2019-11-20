---
title: Özel görüntü, çok Kapsayıcılı veya yerleşik görüntü dağıtma-Azure App Service | Microsoft Docs
description: Linux üzerinde App Service için özel Docker kapsayıcı dağıtımı, çok Kapsayıcılı ve yerleşik uygulama çerçevesi arasında karar verme
keywords: Azure App Service, Web uygulaması, Linux, OSS
services: app-service
documentationCenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: ae28b185aa44ca22d59204826036435a10c64e91
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066781"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Özel görüntü, çok Kapsayıcılı veya yerleşik platform görüntüsü?

[Linux üzerinde App Service](app-service-linux-intro.md) uygulamanızın Web 'de yayımlanmasını sağlamak için üç farklı yol sunar:

- **Özel görüntü dağıtımı**: Uygulamanızı, hazır bir pakette bulunan tüm dosyalarınızı ve bağımlılıklarınızı içeren bir Docker görüntüsüne "dockerize" ekleyin.
- **Çok Kapsayıcılı dağıtım**: Bir Docker Compose yapılandırma dosyası kullanarak uygulamanızı birden çok kapsayıcı genelinde "dockerize".
- **Yerleşik platform görüntüsüyle uygulama dağıtımı**: Yerleşik platform görüntülerimiz, Node ve PHP gibi yaygın web uygulaması çalışma zamanları ve bağımlılıklarını içerir. Uygulamanızı Web uygulamanızın depolamasına dağıtmak için [Azure App Service dağıtım yöntemlerinden](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) birini kullanın ve ardından çalıştırmak için yerleşik bir platform görüntüsü kullanın.

## <a name="which-method-is-right-for-your-app"></a>Uygulamanız için hangi yöntem uygun? 

Göz önünde bulundurulması gereken birincil etmenler şunlardır:

- **Geliştirme iş akışınızda Docker kullanılabilirliği**: Özel görüntü geliştirme, Docker geliştirme iş akışı için temel bilgileri gerektirir. Özel bir görüntünün bir Web uygulamasına dağıtılması için, özel görüntünüzün Docker Hub gibi bir depo konağına yayımlanması gerekir. Docker hakkında bilgi sahibiyseniz ve derleme iş akışınıza Docker görevleri ekleyebiliyorsanız veya uygulamanızı zaten bir Docker görüntüsü olarak yayımlıyorsanız, özel bir görüntü neredeyse kesinlikle en iyi seçenektir.
- **Çoklu katmanlı mimari**: Çoklu kapsayıcı kullanarak yetenekleri ayırmak için Web uygulaması katmanı ve API katmanı gibi birden çok kapsayıcıyı dağıtın. 
- **Uygulama performansı**: Redin gibi bir önbellek katmanını kullanarak çok Kapsayıcılı uygulamanızın performansını artırın. Bunu elde etmek için birden çok kapsayıcı seçin.
- **Benzersiz çalışma zamanı gereksinimleri**: Yerleşik platform görüntüleri, çoğu Web uygulamasının ihtiyaçlarını karşılayacak şekilde tasarlanmıştır, ancak özelleştirme ile sınırlıdır. Uygulamanız, yerleşik görüntülerin sahip olduğu özellikleri aşan benzersiz bağımlılıklara veya diğer çalışma zamanı gereksinimlerine sahip olabilir.
- **Derleme gereksinimleri**: [Sürekli dağıtım](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)sayesinde uygulamanızı Azure 'da doğrudan kaynak kodundan çalışır duruma getirebilirsiniz. Dış derleme veya yayın işlemi gerekli değildir. Ancak, [kudu](https://github.com/projectkudu/kudu/wiki) dağıtım altyapısında derleme araçlarının özelleştirme ve kullanılabilirliği için bir sınır vardır. Uygulamanız, bağımlılıklarında veya özel derleme mantığı için gereksinimlerde büyüdükçe kudu 'nin yeteneklerini daha fazla büyüyebilir.
- **Disk okuma/yazma gereksinimleri**: Web içeriği için tüm Web uygulamalarına bir depolama birimi ayrılır. Azure depolama tarafından desteklenen bu birim, uygulamanın dosya sistemine bağlanır `/home` . Kapsayıcı dosya sisteminde bulunan dosyaların aksine, içerik birimindeki dosyalara bir uygulamanın tüm ölçek örneklerinde erişilebilir ve değişiklikler uygulama yeniden başlatmalarının üzerinde kalır. Ancak, içerik biriminin disk gecikmesi daha yüksektir ve yerel kapsayıcı dosya sistemi gecikmesinden daha yüksektir ve erişim platform yükseltmeleri, planlanmamış kapalı kalma süresi ve ağ bağlantısı sorunlarından etkilenebilir. İçerik dosyalarına ağır salt okuma erişimi gerektiren uygulamalar, dosyaları içerik birimi yerine görüntü dosya sistemine yerleştiren özel görüntü dağıtımından faydalanabilir.
- **Derleme kaynak kullanımı**: Bir uygulama kaynaktan dağıtıldığında, kudu tarafından çalıştırılan dağıtım betikleri, çalışan uygulamayla aynı App Service planı işlem ve depolama kaynaklarını kullanır. Büyük uygulama dağıtımları, istenenden daha fazla kaynak veya zaman tüketebilir. Özellikle, birçok dağıtım iş akışı, bu tür bir etkinlik için en iyi duruma getirilmeyen uygulama içerik biriminde ağır disk etkinliği oluşturur. Özel görüntü, tüm uygulamanızın dosyalarını ve bağımlılıklarını, ek dosya aktarımları veya dağıtım eylemleri yapmanıza gerek olmadan tek bir pakette Azure 'a sunar.
- **Hızlı yineleme gereksinimi**: Bir uygulamayı dockerme, ek derleme adımları gerektirir. Değişikliklerin etkili olabilmesi için, yeni görüntünüzü her güncelleştirme ile bir depoya göndermeniz gerekir. Bu güncelleştirmeler daha sonra Azure ortamına çekilir. Yerleşik kapsayıcılardan biri uygulamanızın ihtiyaçlarını karşılıyorsa, kaynaktan dağıtım daha hızlı bir geliştirme iş akışı sunabilir.

## <a name="next-steps"></a>Sonraki adımlar

Özel kapsayıcı:
* [Özel kapsayıcıyı Çalıştır](quickstart-docker-go.md)

Çok Kapsayıcılı:
* [Çok Kapsayıcılı uygulama oluşturma](quickstart-multi-container.md)

Aşağıdaki makalelerde, yerleşik bir platform görüntüsü ile Linux üzerinde App Service kullanmaya başlamanızı sağlayabilirsiniz:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)