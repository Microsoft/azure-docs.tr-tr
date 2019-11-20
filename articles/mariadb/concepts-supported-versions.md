---
title: Desteklenen sürümler-MariaDB için Azure veritabanı
description: MariaDB için Azure veritabanı hizmetinde bulunan MariaDB sunucusu sürümlerinin hangi sürümlerini destekleyeceğinizi öğrenin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 950f8600dac6fccfa72f5dea6e900a210572ee38
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973531"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>MariaDB sunucu sürümleri için desteklenen Azure veritabanı

MariaDB için Azure veritabanı, InnoDB altyapısı kullanılarak açık kaynaklı [MariaDB sunucusundan](https://downloads.mariadb.org/)geliştirilmiştir.

MariaDB, X. Y. Z adlandırma şemasını kullanır. X ana sürümdür, Y ise ikincil sürümdür ve Z, yama sürümüdür.

> [!NOTE]
> Hizmette, bağlantıları sunucu örneklerine yeniden yönlendirmek için bir ağ geçidi kullanılır. Bağlantı kurulduktan sonra MySQL istemcisi, MariaDB sunucu örneğiniz üzerinde çalışan gerçek sürümü değil, ağ geçidinde bulunan MariaDB sürümünü görüntüler. MariaDB sunucu örneğinizin sürümünü öğrenmek için `SELECT VERSION();` komutunu kullanın.

MariaDB için Azure veritabanı şu anda şu sürümü desteklemektedir:

## <a name="mariadb-version-102"></a>MariaDB sürüm 10,2

Düzeltme Eki Sürümü: 10.2.25

Bu sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [MariaDB belgelerine](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) bakın.

## <a name="mariadb-version-103"></a>MariaDB sürüm 10,3

Düzeltme Eki Sürümü: 10.3.16

Bu sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [MariaDB belgelerine](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) bakın.

## <a name="managing-updates-and-upgrades"></a>Güncelleştirmeleri ve yükseltmeleri yönetme
Hizmet, düzeltme eki güncelleştirmelerine yönelik yükseltmeleri otomatik olarak yönetir. Örneğin, 10.2.21 to 10.2.23.  

Şu anda alt ve ana sürüm yükseltmeleri desteklenmez. Örneğin MariaDB 10.2'den MariaDB 10.3'e yükseltme desteklenmez. 10,2 ' den 10,3 ' ye yükseltmek istiyorsanız, bir dökümünü alın ve yeni altyapı sürümüyle oluşturulmuş bir sunucuya [geri yükleyin](./howto-migrate-dump-restore.md) .

## <a name="next-steps"></a>Sonraki adımlar

- **Hizmet katmanınıza**dayalı belirli kaynak kotaları ve sınırlamalar hakkında bilgi için bkz. [hizmet katmanları](./concepts-pricing-tiers.md).
