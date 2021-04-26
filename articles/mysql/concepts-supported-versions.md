---
title: Desteklenen sürümler-MySQL için Azure veritabanı
description: MySQL Server 'ın hangi sürümlerini MySQL için Azure veritabanı hizmetinde destekleyeceğinizi öğrenin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 1be15c16a1897797326ea869c34c3590ffb07691
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363878"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Desteklenen MySQL için Azure Veritabanı sunucu sürümleri

MySQL için Azure veritabanı, InnoDB depolama altyapısı kullanılarak [MySQL Community Edition](https://www.mysql.com/products/community/)'dan geliştirilmiştir. Hizmet, topluluk tarafından desteklenen tüm geçerli ana sürümü destekler, burada MySQL 5,6, 5,7 ve 8,0. MySQL, X 'in ana sürüm olan X. Y. Z adlandırma şemasını kullanır, Y ise ikincil sürümdür ve Z hata çözme sürümüdür. Düzen hakkında daha fazla bilgi için [MySQL belgelerine](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)bakın.



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Belirli bir MySQL sürümünü çalıştıran bir ağ geçidi düğümüne bağlanma

Tek sunuculu dağıtım seçeneğinde, sunucu örneklerine bağlantıları yeniden yönlendirmek için bir ağ geçidi kullanılır. Bağlantı kurulduktan sonra, MySQL istemcisi MySQL sunucu örneğinizde çalışan gerçek sürümü değil ağ geçidinde belirlenen MySQL sürümünü görüntüler. MySQL sunucu örneğinizin sürümünü öğrenmek için MySQL komut isteminde `SELECT VERSION();` komutunu kullanın. MySQL için Azure veritabanı hizmet mimarisinde ağ geçitleri hakkında daha fazla bilgi edinmek için [bağlantı mimarisini](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture) gözden geçirin.

MySQL için Azure veritabanı, ana sürüm v 5.6, v 5.7 ve v 8.0 'ı desteklediğinden, MySQL için Azure veritabanı 'na bağlanmak üzere varsayılan bağlantı noktası 3306, tüm 3 desteklenen ana sürümlerin sunucularıyla bağlantıları desteklemek için MySQL Client sürüm 5,6 ' yi (en az ortak paydası) çalıştırır. Ancak, uygulamanızın belirli ana sürüme bağlanmak için v 5.7 veya v 8.0 gereksinimi varsa, sunucu bağlantı dizinizdeki bağlantı noktasını değiştirerek bunu yapabilirsiniz.

MySQL için Azure veritabanı hizmetinde, ağ geçidi düğümleri v 5.7 istemcileri için 3308 bağlantı noktasını ve v 8.0 istemcileri için bağlantı noktası 3309 ' i dinler. Diğer bir deyişle, v 5.7 Gateway istemcisine bağlanmak istiyorsanız, istemci uygulamasından sunucunuza bağlanmak için tam sunucu adını ve bağlantı noktası 3308 ' i kullanmanız gerekir. Benzer şekilde, v 8.0 Gateway istemcisine bağlanmak istiyorsanız, sunucunuza bağlanmak için tam sunucu adı ve bağlantı noktası 3309 ' yı kullanabilirsiniz. Daha fazla açıklık için aşağıdaki örneğe bakın.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Farklı ağ geçidi MySQL sürümleri aracılığıyla bağlanan örnek":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>MySQL için Azure veritabanı şu anda MySQL 'in aşağıdaki büyük ve küçük sürümlerini desteklemektedir:


| Sürüm | [Tek sunucu](overview.md) <br/> Geçerli ikincil sürüm |[Esnek Sunucu (Önizleme)](/azure/mysql/flexible-server/overview) <br/> Geçerli ikincil sürüm  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL sürüm 5,6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (kullanımdan kaldırıldı) | Desteklenmez|
|MySQL sürüm 5,7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL sürüm 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

[Sürüm desteği ilkesi belgelerindeki](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql) kullanımdan kaldırılan sürümler için sürüm desteği ilkesini okuyun.

## <a name="managing-updates-and-upgrades"></a>Güncelleştirmeleri ve yükseltmeleri yönetme
Hizmet, hata düzeltme sürümü güncelleştirmeleri için düzeltme eki uygulamayı otomatik olarak yönetir. Örneğin, 5.7.20 yükle to 5.7.21.  

Ana sürüm yükseltmesi şu anda, MySQL v 5.6 'dan v 5.7 'e yükseltmeler için hizmet tarafından desteklenmektedir. Daha ayrıntılı bilgi için bkz. [ana sürüm yükseltmeleri gerçekleştirme](how-to-major-version-upgrade.md). 5,7 ' den 8,0 ' ye yükseltmek istiyorsanız, yeni altyapı sürümüyle oluşturulmuş bir sunucuya [döküm ve geri yükleme](./concepts-migrate-dump-restore.md) işlemleri yapmanızı öneririz.

## <a name="next-steps"></a>Sonraki adımlar

- MySQL için Azure veritabanı sürüm oluşturma ilkesi hakkında daha fazla bilgi için [Bu belgeye](concepts-version-policy.md)bakın.
- **Hizmet katmanınıza** dayalı belirli kaynak kotaları ve sınırlamalar hakkında bilgi için bkz. [hizmet katmanları](./concepts-pricing-tiers.md)
