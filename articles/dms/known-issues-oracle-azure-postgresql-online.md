---
title: "Bilinen sorunlar: Oracle 'dan PostgreSQL için Azure veritabanı 'na geçiş"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni kullanarak Oracle 'dan Azure veritabanı 'na PostgreSQL-Single Server için çevrimiçi geçişlerle ilgili bilinen sorunlar ve geçiş sınırlamaları hakkında bilgi edinin.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 1b331f8e0af452937028c63fba123cb92f57a6b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94962425"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>PostgreSQL-Single sunucusu için Oracle 'dan Azure DB 'ye çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları

Oracle 'dan PostgreSQL-Single Server için Azure veritabanı 'na çevrimiçi geçişlerle ilişkili bilinen sorunlar ve sınırlamalar aşağıdaki bölümlerde açıklanmıştır.

## <a name="oracle-versions-supported-as-a-source-database"></a>Kaynak veritabanı olarak desteklenen Oracle sürümleri

Azure veritabanı geçiş hizmeti, bağlantısını destekler:

- Oracle sürüm 10G, 11g ve 12c.
- Oracle Enterprise, Standard, Express ve Personal Edition.

Azure veritabanı geçiş hizmeti, çok kiracılı kapsayıcı veritabanlarına (CDBs) bağlanmayı desteklemiyor.

## <a name="postgresql-versions-supported-as-a-target-database"></a>Hedef veritabanı olarak desteklenen PostgreSQL sürümleri

Azure veritabanı geçiş hizmeti, PostgreSQL-Single Server sürüm 9,5, 9,6, 10 ve 11 için Azure veritabanı 'na geçişi destekler. PostgreSQL-Single sunucusu için Azure veritabanı 'nda sürüm desteği hakkında güncel bilgi için bkz. [PostgreSQL veritabanı sürümleri](../postgresql/concepts-supported-versions.md) .

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

- Kullanıcı, Oracle sunucusunda DBA ayrıcalığına sahip olmalıdır.
- Bölüm/alt bölüm işlemlerinden (ekleme, BıRAKMA, DEĞIŞIM ve kesme) kaynaklanan veri değişiklikleri geçirilmez ve şu hatalara neden olabilir:
  - EKLEME işlemleri için, eklenen verilerdeki güncelleştirmeler ve silmeler "0 satır etkilendi" uyarısı döndürebilir.
  - BıRAKMA ve kesme işlemleri için yeni ekler "yinelemeler" hatalarına neden olabilir.
  - EXCHANGE işlemleri için, "0 satır etkilendi" uyarısı ve "yinelemeler" hatası oluşabilir.
- Adları kesme işareti içeren tablolar çoğaltılamaz.