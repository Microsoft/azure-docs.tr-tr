---
title: Azure Uygulama Yapılandırması nedir?
description: Azure uygulama yapılandırma hizmeti 'ne genel bakış konusunu okuyun. Uygulama yapılandırmasını neden kullanmak istediğinizi anlayın ve bunu nasıl kullanabileceğinizi öğrenin.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 9786ff20c1d3f467e217d5fe34518fcc9494863e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930118"
---
# <a name="what-is-azure-app-configuration"></a>Azure Uygulama Yapılandırması nedir?

Azure Uygulama yapılandırması, uygulama ayarlarını ve özellik bayraklarını merkezi olarak yönetmek için bir hizmet sağlar. Modern programlar, özellikle bir bulutta çalışan programlar, genellikle doğası halinde dağıtılan birçok bileşene sahiptir. Bu bileşenler genelinde yapılandırma ayarlarının yayılması, uygulama dağıtımı sırasında sorun gidermeye yönelik hatalara neden olabilir. Uygulamanızın tüm ayarlarını depolamak ve erişimlerini tek bir yerde güvenli hale getirmek için uygulama yapılandırması 'nı kullanın.

## <a name="why-use-app-configuration"></a>Uygulama yapılandırması neden kullanılmalıdır?

Bulut tabanlı uygulamalar genellikle birden çok bölgedeki birden çok sanal makine veya kapsayıcı üzerinde çalışır ve birden çok dış hizmet kullanır. Dağıtılmış bir ortamda güçlü ve ölçeklenebilir bir uygulama oluşturmak önemli bir zorluk gösterir.

Çeşitli programlama yöntemleri, geliştiricilerin uygulama oluşturmanın artmasının karmaşıklığıyla uğraşmasını sağlamaya yardımcı olur. Örneğin, [on Iki öğeli uygulama](https://12factor.net/) , bulut uygulamalarıyla kullanılmak üzere çok iyi sınanmış mimari desenleri ve en iyi uygulamaları açıklar. Bu kılavuzun önemli bir önerisi, koddan yapılandırmanın ayrı olması olur. Bir uygulamanın yapılandırma ayarları, çalıştırılabilirinin dışarıdan tutulması ve çalışma zamanı ortamından veya bir dış kaynaktan okunmalıdır.

Herhangi bir uygulama uygulama yapılandırmasından yararlanabileceği sürece, aşağıdaki örnekler, kullanmanın avantajlarından yararlanan uygulama türleridir:

* Azure Kubernetes hizmeti, Azure Service Fabric veya bir veya daha fazla coğrafi bilgisayarlarda dağıtılan diğer Kapsayıcılı uygulamalara dayalı mikro hizmetler
* Azure Işlevleri veya diğer olay odaklı durum bilgisi olmayan işlem uygulamaları içeren sunucusuz uygulamalar
* Sürekli dağıtım işlem hattı

Uygulama yapılandırması aşağıdaki avantajları sunar:

* Dakikalar içinde ayarlanabilir, tam olarak yönetilen bir hizmet
* Esnek anahtar gösterimleri ve eşlemeler
* Etiketlerle etiketleme
* Ayarları zaman içinde yeniden yürütme
* Özellik bayrağı yönetimi için adanmış Kullanıcı arabirimi
* Özel tanımlanmış boyutlarda iki yapılandırma kümesini karşılaştırma
* Azure tarafından yönetilen kimlikler aracılığıyla geliştirilmiş güvenlik
* REST ve iletim sırasında hassas bilgilerin şifrelenmesi
* Popüler çerçeveler ile yerel tümleştirme

Uygulama yapılandırması, uygulama gizli dizileri depolamak için kullanılan [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)tamamlar. Uygulama yapılandırması aşağıdaki senaryoları uygulamayı kolaylaştırır:

* Farklı ortamlar ve coğrafi veriler için hiyerarşik yapılandırma verilerinin yönetimini ve dağıtımını merkezileştirin
* Uygulamayı yeniden dağıtmaya veya yeniden başlatmaya gerek kalmadan uygulama ayarlarını dinamik olarak değiştirme
* Gerçek zamanlı denetim özelliği kullanılabilirliği

## <a name="use-app-configuration"></a>Uygulama yapılandırmasını kullan

Uygulamanıza uygulama yapılandırma deposu eklemenin en kolay yolu Microsoft tarafından sunulan bir istemci kitaplığı kullanmaktır. Seçtiğiniz dile ve çerçeveye bağlı olarak, uygulamanızla bağlantı kurmak için aşağıdaki yöntemler kullanılabilir

| Programlama dili ve Framework | Bağlanma |
|---|---|
| .NET Core ve ASP.NET Core | .NET Core için uygulama yapılandırma sağlayıcısı |
| .NET Framework ve ASP.NET | .NET için uygulama yapılandırma Oluşturucusu |
| Java Spring | Yay bulutu için uygulama yapılandırma istemcisi |
| Diğer | Uygulama yapılandırma REST API |

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç ASP.NET Core](./quickstart-aspnet-core-app.md)
* [.NET Core hızlı başlangıç](./quickstart-dotnet-core-app.md)
* [Hızlı başlangıç .NET Framework](./quickstart-dotnet-app.md)
* [Azure Işlevleri hızlı başlangıç](./quickstart-azure-functions-csharp.md)
* [Java Spring hızlı başlangıç](./quickstart-java-spring-app.md)
* [ASP.NET Core Özellik bayrağını hızlı başlangıç](./quickstart-feature-flag-aspnet-core.md)
* [Yay önyükleme özelliği bayrak hızlı başlangıç](./quickstart-feature-flag-spring-boot.md)
