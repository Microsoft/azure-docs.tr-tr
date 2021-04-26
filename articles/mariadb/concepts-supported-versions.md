---
title: Desteklenen sürümler-MariaDB için Azure veritabanı
description: MariaDB için Azure veritabanı hizmetinde bulunan MariaDB sunucusu sürümlerinin hangi sürümlerini destekleyeceğinizi öğrenin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: dbf32626714200e6712c67b701ebc597c4a7ba7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662449"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>MariaDB sunucu sürümleri için desteklenen Azure veritabanı

MariaDB için Azure veritabanı, InnoDB altyapısı kullanılarak açık kaynaklı [MariaDB sunucusundan](https://downloads.mariadb.org/)geliştirilmiştir.

MariaDB, X. Y. Z adlandırma şemasını kullanır. X ana sürümdür, Y ise ikincil sürümdür ve Z, yama sürümüdür.

> [!NOTE]
> Hizmette, bağlantıları sunucu örneklerine yeniden yönlendirmek için bir ağ geçidi kullanılır. Bağlantı kurulduktan sonra MySQL istemcisi, MariaDB sunucu örneğiniz üzerinde çalışan gerçek sürümü değil, ağ geçidinde bulunan MariaDB sürümünü görüntüler. MariaDB sunucu örneğinizin sürümünü öğrenmek için `SELECT VERSION();` komutunu kullanın.

MariaDB için Azure veritabanı şu anda şu sürümü desteklemektedir:

## <a name="mariadb-version-102"></a>MariaDB sürüm 10,2

Düzeltme Eki Sürümü: 10.2.32

Bu sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [MariaDB belgelerine](https://mariadb.com/kb/en/mariadb-10232-release-notes/) bakın.

## <a name="mariadb-version-103"></a>MariaDB sürüm 10,3

Düzeltme Eki Sürümü: 10.3.23

Bu sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [MariaDB belgelerine](https://mariadb.com/kb/en/mariadb-10323-release-notes/) bakın.

## <a name="managing-updates-and-upgrades"></a>Güncelleştirmeleri ve yükseltmeleri yönetme
Hizmet, düzeltme eki güncelleştirmelerine yönelik yükseltmeleri otomatik olarak yönetir. Örneğin, 10.2.21 to 10.2.23.  

Şu anda alt ve ana sürüm yükseltmeleri desteklenmez. Örneğin MariaDB 10.2'den MariaDB 10.3'e yükseltme desteklenmez. 10,2 ' den 10,3 ' ye yükseltmek istiyorsanız, bir dökümünü alın ve yeni altyapı sürümüyle oluşturulmuş bir sunucuya [geri yükleyin](./howto-migrate-dump-restore.md) .

## <a name="next-steps"></a>Sonraki adımlar

- **Hizmet katmanınıza** dayalı belirli kaynak kotaları ve sınırlamalar hakkında bilgi için bkz. [hizmet katmanları](./concepts-pricing-tiers.md).
