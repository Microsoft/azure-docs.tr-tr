---
title: Oracle 'dan PostgreSQL için Azure veritabanı 'na çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları hakkında makale-tek sunucu | Microsoft Docs
description: Oracle 'dan PostgreSQL için Azure veritabanı 'na çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları hakkında bilgi edinin.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: ecd2750926fd04c84d2d6b48f5fde06363dd15df
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606750"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Oracle 'dan PostgreSQL için Azure DB 'ye çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları-tek sunucu

Oracle 'dan PostgreSQL için Azure veritabanı 'na çevrimiçi geçişlerle ilişkili bilinen sorunlar ve sınırlamalar-tek sunucu aşağıdaki bölümlerde açıklanmıştır.

## <a name="oracle-versions-supported-as-a-source-database"></a>Kaynak veritabanı olarak desteklenen Oracle sürümleri

Azure veritabanı geçiş hizmeti, bağlantısını destekler:

- Oracle sürüm 10G, 11g ve 12c.
- Oracle Enterprise, Standard, Express ve Personal Edition.

Azure veritabanı geçiş hizmeti, çok kiracılı kapsayıcı veritabanlarına (CDBs) bağlanmayı desteklemiyor.

## <a name="postgresql-versions-supported-as-a-target-database"></a>Hedef veritabanı olarak desteklenen PostgreSQL sürümleri

Azure veritabanı geçiş hizmeti PostgreSQL için Azure veritabanı 'na geçişi destekler-tek sunuculu sürüm 9,5, 9,6, 10 ve 11. PostgreSQL için Azure veritabanı-tek sunucu 'da sürüm desteği hakkındaki güncel bilgiler için bkz. [PostgreSQL veritabanı sürümleri](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) .

## <a name="datatype-limitations"></a>Veri türü sınırlamaları

Aşağıdaki **veri türleri** geçirilmez:

- BDosya
- ROWıD
- REF
- UııD
- ANYDATA
- SDO_GEOMETRY
- İç içe tablolar
- Kullanıcı tanımlı veri türleri
- Notlar
- Sanal sütunlar
- ROWıD sütununu temel alan gerçekleştirilmiş görünümler

Ayrıca, boş BLOB/CLOB sütunları hedef üzerinde NULL ile eşleştirilir.

## <a name="lob-limitations"></a>LOB sınırlamaları

- Sınırlı boyutlu LOB modu etkin olduğunda, Oracle kaynağında boş lob 'Lar NULL değerler olarak çoğaltılır.
- Uzun nesne adları (30 bayttan fazla) desteklenmez.
- LONG ve LONG RAW sütunundaki veriler 64K 'yı aşamaz. 64K 'dan daha fazla veri kesilecek.
- Yalnızca Oracle 12 ' de, LOB sütunlarında yapılan değişiklikler desteklenmez (geçirilir).
- XMLTYPE ve LOB sütunlarındaki güncelleştirmeler desteklenmez (geçirilmiş).

## <a name="known-issues-and-limitations"></a>Bilinen sorunlar ve sınırlamalar

- Müşterilerin Oracle 'a bağlanmak için SYSDBA kullanması gerekir.
- Bölüm/alt bölüm işlemlerinden (ekleme, BıRAKMA, DEĞIŞIM ve kesme) kaynaklanan veri değişiklikleri geçirilmez ve şu hatalara neden olabilir:
  - EKLEME işlemleri için, eklenen verilerdeki güncelleştirmeler ve silmeler "0 satır etkilendi" uyarısı döndürebilir.
  - BıRAKMA ve kesme işlemleri için yeni ekler "yinelemeler" hatalarına neden olabilir.
  - EXCHANGE işlemleri için, "0 satır etkilendi" uyarısı ve "yinelemeler" hatası oluşabilir.
- Adları kesme işareti içeren tablolar çoğaltılamaz.
