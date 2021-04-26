---
title: Desteklenen sürümler-PostgreSQL için Azure veritabanı-esnek sunucu
description: PostgreSQL için Azure veritabanı 'nda desteklenen PostgreSQL büyük ve küçük sürümlerini açıklar-esnek sunucu.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 5fda7ebb5a72dd9bbfab0ba72511540cf141563f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608859"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı 'nda desteklenen PostgreSQL ana sürümleri-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

PostgreSQL için Azure veritabanı-esnek sunucu şu anda aşağıdaki ana sürümleri desteklemektedir:

## <a name="postgresql-version-12"></a>PostgreSQL sürüm 12

Geçerli küçük yayın 12,5 ' dir. Bu küçük sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [PostgreSQL belgelerine](https://www.postgresql.org/docs/12/static/release-12-4.html) bakın.

## <a name="postgresql-version-11"></a>PostgreSQL sürüm 11

Geçerli küçük yayın 11,10 ' dir. Bu küçük sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [PostgreSQL belgelerine](https://www.postgresql.org/docs/11/static/release-11-9.html) bakın.

## <a name="postgresql-version-10-and-older"></a>PostgreSQL sürüm 10 ve üzeri

PostgreSQL için Azure veritabanı-esnek sunucu için PostgreSQL sürüm 10 ve daha eskisini desteklemiyoruz. Eski sürümlere ihtiyacınız varsa lütfen [tek sunuculu](../concepts-supported-versions.md) dağıtım seçeneğini kullanın.

## <a name="managing-upgrades"></a>Yükseltmeleri yönetme

PostgreSQL projesi, bildirilen hataları çözmek için düzenli olarak küçük yayınlar yayınlar. PostgreSQL için Azure Veritabanı, hizmetin aylık dağıtımları sırasında ikincil sürümlerle sunuculara otomatik olarak yama uygular.

Ana sürüm yükseltmesi için Otomasyon henüz desteklenmiyor. Örneğin, şu anda PostgreSQL 11 ' den PostgreSQL 12 ' ye otomatik yükseltme yoktur.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->