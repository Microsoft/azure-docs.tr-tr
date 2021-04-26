---
title: Sürüm oluşturma ilkesi-PostgreSQL için Azure veritabanı-tek sunucu ve esnek sunucu (Önizleme)
description: PostgreSQL için Azure veritabanı-tek sunucu içindeki Postgres birincil ve ikincil sürümleri etrafında ilkeyi açıklar.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 62fe1b3391eb4cb2d409a92b936fd3f1ae56d992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100518428"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>PostgreSQL için Azure Veritabanı sürüm oluşturma ilkesi

Bu sayfa, PostgreSQL için Azure veritabanı sürüm oluşturma ilkesini açıklar ve PostgreSQL için Azure veritabanı-tek sunucu ve PostgreSQL için Azure veritabanı-esnek sunucu (Önizleme) Dağıtım modları için geçerlidir.

## <a name="supported--postgresql-versions"></a>Desteklenen PostgreSQL sürümleri

PostgreSQL için Azure veritabanı aşağıdaki veritabanı sürümlerini destekler.

| Sürüm | Tek sunucu | Esnek Sunucu (Önizleme) |
| ----- | :------: | :----: |
| PostgreSQL 12 |  | X  | 
| PostgreSQL 11 | X | X |
| PostgreSQL 10 | X |  |
| PostgreSQL 9,6 | X |  |
| *PostgreSQL 9,5 (kullanımdan kaldırıldı)* | X |  |

## <a name="major-version-support"></a>Ana sürüm desteği
PostgreSQL için Azure veritabanı, PostgreSQL Community [sürüm oluşturma ilkesinde](https://www.postgresql.org/support/versioning/)belirtildiği gibi, PostgreSQL topluluğu tarafından devre dışı bırakılana kadar Azure 'un sürümü desteklemeye başladığı tarihten Itibaren PostgreSQL Için Azure veritabanı tarafından desteklenecektir.

## <a name="minor-version-support"></a>İkincil sürüm desteği
PostgreSQL için Azure veritabanı, düzenli bakımın parçası olarak Azure tercih edilen PostgreSQL sürümüne küçük sürüm yükseltmeleri otomatik olarak gerçekleştirir. 

## <a name="major-version-retirement-policy"></a>Ana sürüm kullanımdan kaldırma ilkesi
Aşağıdaki tabloda PostgreSQL ana sürümleri için kullanımdan kaldırma ayrıntıları verilmiştir. Tarihler [PostgreSQL Community sürüm oluşturma ilkesini](https://www.postgresql.org/support/versioning/)izler.

| Sürüm | Yenilikler | Azure desteği Başlangıç tarihi | Emeklilik tarihi|
| ----- | ----- | ------ | ----- |
| [PostgreSQL 9,5 (kullanımdan kaldırıldı)](https://www.postgresql.org/about/news/postgresql-132-126-1111-1016-9621-and-9525-released-2165/)| [Özellikler](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 18 Nisan 2018   | 11 Şubat 2021
| [PostgreSQL 9,6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [Özellikler](https://wiki.postgresql.org/wiki/NewIn96) | 18 Nisan 2018  | 11 Kasım 2021
| [PostgreSQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [Özellikler](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 4 Haziran 2018  | 10 Kasım 2022
| [PostgreSQL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [Özellikler](https://www.postgresql.org/docs/11/release-11.html) | 24 Temmuz 2019  | 9 Kasım 2023
| [PostgreSQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [Özellikler](https://www.postgresql.org/docs/12/release-12.html) | 22 Eylül 2020  | 14 Kasım 2024

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>PostgreSQL için Azure veritabanı 'nda, kullanımdan kaldırılan PostgreSQL altyapısı sürümleri desteklenmiyor

PostgreSQL için Azure veritabanı 'nda kullanımdan kaldırılan sürümü çalıştırmaya devam edebilirsiniz. Ancak, her bir PostgreSQL veritabanı sürümü için kullanımdan kaldırma tarihinden sonra aşağıdaki kısıtlamalara göz önünde olun:
- Topluluk başka hata düzeltmeleri veya güvenlik düzeltmelerinin serbest bırakılmamasına neden olarak, PostgreSQL için Azure veritabanı, herhangi bir hata veya güvenlik sorunu için kullanımdan kaldırılan veritabanı altyapısına düzeltme eki uygulanmaz veya kullanımdan kaldırılan veritabanı altyapısı ile ilgili güvenlik önlemleri almaz. Sonuç olarak güvenlik açıklarına veya diğer sorunlarla karşılaşabilirsiniz. Ancak Azure, ana bilgisayar, işletim sistemi, kapsayıcılar ve hizmetle ilgili diğer tüm bileşenler için düzenli bakım ve düzeltme eki gerçekleştirmeye devam edecektir.
- Karşılaşabileceğiniz herhangi bir destek sorunu PostgreSQL veritabanıyla ilişkili olursa size destek sunamayacak olabilir. Bu gibi durumlarda, size herhangi bir destek sağlayabilmesi için veritabanınızı yükseltmeniz gerekir.
- Kullanımdan kaldırılan sürüm için yeni veritabanı sunucuları oluşturabileceksiniz. Bununla birlikte, mevcut sunucularınız için zaman içinde kurtarmalar gerçekleştirebilir ve okuma çoğaltmaları oluşturabilirsiniz.
- PostgreSQL için Azure veritabanı tarafından geliştirilen yeni hizmet özellikleri yalnızca desteklenen veritabanı sunucusu sürümleri için kullanılabilir olabilir.
- Çalışma süresi SLA 'Ları yalnızca PostgreSQL için Azure veritabanı ile ilgili sorunlar için geçerlidir ve veritabanı altyapısından kaynaklanan hatalara neden olur.  
- Devre dışı bırakılmış veritabanı sürümünde tanımlanan PostgreSQL veritabanı altyapısı güvenlik açığından kaynaklanan, hizmette ciddi bir tehdit olması durumunda, Azure hizmeti güvenli hale getirmek için veritabanı sunucunuzu durdurmayı tercih edebilir. Böyle bir durumda, sunucuyu çevrimiçi duruma getirmeden önce sunucuyu yükseltmeniz istenir.

## <a name="postgresql-version-syntax"></a>PostgreSQL sürümü sözdizimi
PostgreSQL sürüm 10 ' dan önce, [PostgreSQL sürüm oluşturma ilkesi](https://www.postgresql.org/support/versioning/) , _ana sürüm_ yükseltmesini birinci _veya_ ikinci sayıda artış olacak şekilde kabul ediyor. Örneğin, 9,5 9,6, _ana_ sürüm yükseltmesi olarak kabul edildi. Sürüm 10 itibariyle, yalnızca ilk sayıdaki değişiklik ana sürüm yükseltmesi olarak kabul edilir. Örneğin, 10,0 ile 10,1 arasında _küçük_ bir yayın yükseltmesi vardır. Sürüm 10 ' dan 11 ' e _büyük_ bir sürüm yükseltirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
- Bkz. PostgreSQL için Azure veritabanı-tek sunuculu [Desteklenen sürümler](./concepts-supported-versions.md)
- Bkz. PostgreSQL için Azure veritabanı-esnek sunucu (Önizleme) [Desteklenen sürümler](flexible-server/concepts-supported-versions.md)
- Ana sürüm yükseltmeleri gerçekleştirme hakkında daha fazla bilgi için bkz. [ana sürüm yükseltmeleri](how-to-upgrade-using-dump-and-restore.md) belgeleri.
- Desteklenen PostgreSQL uzantıları hakkında daha fazla bilgi için bkz. [Uzantılar belgesi](concepts-extensions.md).
