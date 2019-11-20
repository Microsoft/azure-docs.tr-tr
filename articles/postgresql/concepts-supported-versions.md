---
title: PostgreSQL için Azure veritabanı 'nda desteklenen sürümler-tek sunucu
description: PostgreSQL için Azure veritabanı 'nda desteklenen sürümleri (tek sunucu) açıklar.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2a2b8b71e07e5dac74d73d3a81c150ac5d980ea2
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935346"
---
# <a name="supported-postgresql-database-versions"></a>Desteklenen PostgreSQL veritabanı sürümleri
Microsoft amaçlar, PostgreSQL için Azure veritabanı 'nda PostgreSQL altyapısının n-2 sürümlerini desteklemeye yöneliktir-tek sunucu. Sürümler, Azure 'daki geçerli ana sürümdür (n) ve önceki iki ana sürüm (-2) olacaktır.

PostgreSQL için Azure veritabanı şu anda aşağıdaki ana sürümleri desteklemektedir:

## <a name="postgresql-version-11"></a>PostgreSQL sürüm 11
Geçerli ikincil sürüm 11,5 ' dir. Bu ikincil sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [PostgreSQL belgelerine](https://www.postgresql.org/docs/11/static/release-11-5.html) bakın.

## <a name="postgresql-version-10"></a>PostgreSQL sürüm 10
Geçerli ikincil sürüm 10,10 ' dir. Bu ikincil sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [PostgreSQL belgelerine](https://www.postgresql.org/docs/10/static/release-10-10.html) bakın.

## <a name="postgresql-version-96"></a>PostgreSQL sürüm 9,6
Geçerli ikincil sürüm 9.6.15. Bu ikincil sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [PostgreSQL belgelerine](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) bakın.

## <a name="postgresql-version-95"></a>PostgreSQL sürüm 9,5
Geçerli ikincil sürüm 9.5.19. Bu ikincil sürümdeki geliştirmeler ve düzeltmeler hakkında bilgi edinmek için [PostgreSQL belgelerine](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) başvurun.

## <a name="managing-upgrades"></a>Yükseltmeleri yönetme
PostgreSQL için Azure veritabanı, ikincil sürüm yükseltmelerini otomatik olarak yönetir. 

Otomatik ana sürüm yükseltmesi desteklenmez. Örneğin PostgreSQL 9.5’ten PostgreSQL 9.6’ya otomatik yükseltme yoktur. Sonraki ana sürüme yükseltmek için, yeni altyapı sürümünde oluşturulmuş bir sunucuya [veritabanı dökümü ve yedeklemesi](./howto-migrate-using-dump-and-restore.md) oluşturun.

### <a name="version-syntax"></a>Sürüm sözdizimi
PostgreSQL sürüm 10 ' dan önce, [PostgreSQL sürüm oluşturma ilkesi](https://www.postgresql.org/support/versioning/) , _ana sürüm_ yükseltmesini birinci _veya_ ikinci sayıda artış olacak şekilde kabul ediyor. Örneğin, 9,5 9,6, _ana_ sürüm yükseltmesi olarak kabul edildi. Sürüm 10 itibariyle, yalnızca ilk sayıdaki değişiklik ana sürüm yükseltmesi olarak kabul edilir. Örneğin, 10,0 ile 10,1 arasında _küçük_ bir sürüm yükseltmesi vardır. Sürüm 10 ' dan 11 ' e _büyük_ bir sürüm yükseltirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Desteklenen PostgreSQL uzantıları hakkında daha fazla bilgi için bkz. [Uzantılar belgesi](concepts-extensions.md).
