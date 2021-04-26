---
title: Desteklenen sürümler-PostgreSQL için Azure veritabanı-tek sunucu
description: PostgreSQL için Azure veritabanı 'nda desteklenen Postgres birincil ve ikincil sürümlerini açıklar-tek sunucu.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: b372c9d8be31ab6f51d737cd00c692ac9242e4db
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605281"
---
# <a name="supported-postgresql-major-versions"></a>Desteklenen PostgreSQL ana sürümleri

Destek ilkesi ayrıntıları için lütfen [PostgreSQL Için Azure veritabanı sürüm oluşturma ilkesine](concepts-version-policy.md) bakın.

PostgreSQL için Azure veritabanı şu anda aşağıdaki ana sürümleri desteklemektedir:

## <a name="postgresql-version-11"></a>PostgreSQL sürüm 11
Geçerli küçük yayın 11,6 ' dir. Bu küçük sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [PostgreSQL belgelerine](https://www.postgresql.org/docs/11/static/release-11-6.html) bakın.

## <a name="postgresql-version-10"></a>PostgreSQL sürüm 10
Geçerli küçük yayın 10,11 ' dir. Bu küçük sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [PostgreSQL belgelerine](https://www.postgresql.org/docs/10/static/release-10-11.html) bakın.

## <a name="postgresql-version-96"></a>PostgreSQL sürüm 9,6
Geçerli ikincil sürüm 9.6.16. Bu küçük sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [PostgreSQL belgelerine](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) bakın.

## <a name="postgresql-version-95-retired"></a>PostgreSQL sürüm 9,5 (kullanımdan kaldırıldı)
Postgres topluluğunun [sürüm oluşturma ilkesiyle](https://www.postgresql.org/support/versioning/)hizalama, PostgreSQL Için Azure veritabanı, 11 Şubat 2021 Itibariyle Postgres sürüm 9,5 ' ü kullanımdan kaldırılmıştır. Daha fazla ayrıntı ve kısıtlama için lütfen [PostgreSQL Için Azure veritabanı sürüm oluşturma ilkesine](concepts-version-policy.md) bakın. Bu ana sürümü çalıştırıyorsanız, lütfen en kısa zamanda, tercihen daha yüksek bir sürüme yükselterek tercihen PostgreSQL 11 ' e yükseltin.

## <a name="managing-upgrades"></a>Yükseltmeleri yönetme
PostgreSQL projesi, bildirilen hataları çözmek için düzenli olarak küçük yayınlar yayınlar. PostgreSQL için Azure Veritabanı, hizmetin aylık dağıtımları sırasında ikincil sürümlerle sunuculara otomatik olarak yama uygular. 

Ana sürümler için otomatik yerinde yükseltmeler desteklenmez. Daha yüksek bir ana sürüme yükseltmek için şunları yapabilirsiniz 
   * [Döküm ve geri yükleme kullanarak ana sürüm yükseltmeleri](./how-to-upgrade-using-dump-and-restore.md)bölümünde belgelenen yöntemlerden birini kullanın.
   * Bir veritabanını yeni altyapı sürümüyle oluşturulan bir sunucuya taşımak için [pg_dump ve pg_restore](./howto-migrate-using-dump-and-restore.md) kullanın.
   * Çevrimiçi yükseltmeler gerçekleştirmek için [Azure veritabanı geçiş hizmeti](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) 'ni kullanın.

### <a name="version-syntax"></a>Sürüm sözdizimi
PostgreSQL sürüm 10 ' dan önce, [PostgreSQL sürüm oluşturma ilkesi](https://www.postgresql.org/support/versioning/) , _ana sürüm_ yükseltmesini birinci _veya_ ikinci sayıda artış olacak şekilde kabul ediyor. Örneğin, 9,5 9,6, _ana_ sürüm yükseltmesi olarak kabul edildi. Sürüm 10 itibariyle, yalnızca ilk sayıdaki değişiklik ana sürüm yükseltmesi olarak kabul edilir. Örneğin, 10,0 ile 10,1 arasında _küçük_ bir yayın yükseltmesi vardır. Sürüm 10 ' dan 11 ' e _büyük_ bir sürüm yükseltirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Desteklenen PostgreSQL uzantıları hakkında daha fazla bilgi için bkz. [Uzantılar belgesi](concepts-extensions.md).
