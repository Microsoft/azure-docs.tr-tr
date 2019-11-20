---
title: Wingtips uygulamasına hoş geldiniz
description: Bulut ortamındaki Azure SQL veritabanı için veritabanı Kiracı modelleri ve örnek Wingtips SaaS uygulaması hakkında bilgi edinin.
keywords: sql veritabanı öğreticisi
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 4e0b3afe51ac7c7a6b9213fcee79af57cbbd8197
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818323"
---
# <a name="the-wingtip-tickets-saas-application"></a>Wingtip bilet SaaS uygulaması

Aynı *Wingtip bilet* SaaS uygulaması üç örnek için de uygulanır. Uygulama, küçük havalandırma noktaları, sinek, vb. hedefleyen basit bir olay listelemesi ve anahtar kullanımı SaaS uygulamasıdır. Her bir mekan uygulamanın bir kiracısıdır ve kendi verileri vardır: mekan ayrıntıları, olay listeleri, müşteriler, Bilet siparişleri vb.  Uygulama, yönetim betikleri ve öğreticilerle birlikte, uçtan uca bir SaaS senaryosu gösterir. Bu, kiracılar sağlama, performansı izleme ve yönetme, şema yönetimi ve çapraz kiracı raporlama ile analizlerini içerir.

## <a name="three-saas-application-and-tenancy-patterns"></a>Üç SaaS uygulaması ve kiracı desenleri

Uygulamanın üç sürümü kullanılabilir; her biri, Azure SQL veritabanı 'nda farklı bir veritabanı Kiracı modelini araştırır.  İlki, kiracı başına kendi veritabanıyla tek başına bir uygulama kullanır. İkincisi, kiracı başına bir veritabanı ile çok kiracılı bir uygulama kullanır. Üçüncü örnek, parçalı çok kiracılı veritabanları içeren çok kiracılı bir uygulama kullanır.

![Üç kiracı deseni][image-three-tenancy-patterns]

 Her örnek, uygulama kodunu ve bir dizi tasarım ve yönetim desenini keşfettiğinde yönetim komut dosyalarını ve öğreticilerini içerir.  Her örnek, beş dakikadan kısa bir sürede dağıtılır.  Tasarımın ve yönetimin farklılığı karşılaştırabilmeniz için üçü de yan yana dağıtılabilir.

## <a name="standalone-application-per-tenant-pattern"></a>Her kiracı için tek başına uygulama

Her kiracı için tek başına uygulama, her kiracı için bir veritabanıyla tek bir kiracı uygulaması kullanır. Her kiracının veritabanı da dahil olmak üzere her bir uygulaması ayrı bir Azure Kaynak grubuna dağıtılır. Kaynak grubu, hizmet sağlayıcının aboneliğine veya kiracının aboneliğine dağıtılabilir ve kiracının adına sağlayıcı tarafından yönetilebilir. Her kiracı için tek başına uygulama, kiracı yalıtımı sağlar, ancak birden çok kiracı arasında kaynak paylaşmak için hiçbir fırsat olmadığından genellikle en pahalıdır.  Bu model daha karmaşık olabilecek ve daha az sayıda kiracıya dağıtılan uygulamalar için uygundur.  Tek başına dağıtımlarla, uygulama her kiracı için diğer desenlerden daha kolay özelleştirilebilir.  

GitHub [. ../Microsoft/wingtipbilet ssaas-standaloneapp][github-code-for-wingtip-sa]'teki [öğreticiler][docs-tutorials-for-wingtip-sa] ve koda göz atın.

## <a name="database-per-tenant-pattern"></a>Kiracı başına veritabanı Kalıbı

Her kiracı için veritabanı, kiracı yalıtımıyla ilgili olan hizmet sağlayıcıları için etkilidir ve paylaşılan kaynakların düşük maliyetli kullanımına izin veren merkezi bir hizmet çalıştırmak ister. Her bir mekan veya kiracı için bir veritabanı oluşturulur ve tüm veritabanları merkezi olarak yönetilir. Veritabanları, kiracının öngörülemeyen iş yükü desenlerini kullanan uygun maliyetli ve kolay performans yönetimi sağlamak için esnek havuzlarda barındırılabilir. Katalog veritabanı, kiracılar ve veritabanları arasındaki eşlemeyi barındırır. Bu eşleme, uygulamaya verimli bağlantı yönetimi sağlayan [elastik veritabanı Istemci kitaplığının](sql-database-elastic-database-client-library.md)parça eşleme yönetimi özellikleri kullanılarak yönetilir.

GitHub [. ../Microsoft/wingtipbilet ssaas-dbpertenant][github-code-for-wingtip-dpt]'daki [öğreticiler][docs-tutorials-for-wingtip-dpt] ve koda göz atın.

## <a name="sharded-multi-tenant-database-pattern"></a>Parçalı çok kiracılı veritabanı Kalıbı

Çok kiracılı veritabanları, kiracı başına daha düşük maliyetli ve azaltılmış kiracı yalıtımıyla ilgili olan hizmet sağlayıcıları için geçerlidir. Bu model, kiracı başına maliyeti aşağı doğru sunarak çok sayıda kiracının tek bir veritabanına paketlenmesi sağlar. Kiracıların birden çok veritabanı arasında parçalı olarak neredeyse sınırsız ölçeklendirilmesi mümkündür. Katalog veritabanı kiracılar ile veritabanlarına eşlenir.  

Bu model Ayrıca, bir veritabanında birden fazla kiracı ile maliyet için iyileştirebileceğinizi veya kendi veritabanında tek bir kiracıyla yalıtımda en iyileştirmek için bir *karma* modele izin verir. Bu seçenek, kiracı sağlandığında veya daha sonra uygulama üzerinde hiçbir etkisi olmadan kiracı tarafından kiracı temelinde yapılabilir.  Kiracıların grupları farklı şekilde değerlendirilme gerektiğinde bu model etkin bir şekilde kullanılabilir. Örneğin, düşük maliyetli kiracılar paylaşılan veritabanlarına atanabilir, ancak Premium kiracılar kendi veritabanlarına atanabilir. 

GitHub [. ../Microsoft/wingtipbilet ssaas-multitenantdb][github-code-for-wingtip-mt]' de [öğreticiler][docs-tutorials-for-wingtip-mt] ve koda göz atın.

## <a name="next-steps"></a>Sonraki adımlar

#### <a name="conceptual-descriptions"></a>Kavramsal açıklamalar

- Uygulama kiracının desenlerinin daha ayrıntılı bir açıklaması [çok kiracılı SaaS veritabanı Kiracı desenlerinde][saas-tenancy-app-design-patterns-md] mevcuttur

#### <a name="tutorials-and-code"></a>Öğreticiler ve kod

- Kiracı başına tek başına uygulama:
    - [Tek başına uygulama öğreticileri][docs-tutorials-for-wingtip-sa].
    - [GitHub 'da tek başına uygulama Için kod][github-code-for-wingtip-sa].

- Kiracı başına veritabanı:
    - [Kiracı başına veritabanı öğreticileri][docs-tutorials-for-wingtip-dpt].
    - [GitHub 'da kiracı başına veritabanı kodu][github-code-for-wingtip-dpt].

- Parçalı çoklu kiracı:
    - Parçalara ayrılmış [çok kiracılı öğreticiler][docs-tutorials-for-wingtip-mt].
    - [GitHub 'da, parçalara ayrılmış çok kiracılı kod][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Üç kiracı deseni."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

