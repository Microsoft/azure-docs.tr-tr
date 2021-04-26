---
title: 'Sorun giderme: sunucusuz SQL havuzu kullanarak CSV veya PARQUET dosyalarından UTF-8 metnini okuma'
description: Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu kullanarak CSV veya PARQUET dosyalarından UTF-8 metnini okuma
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 12/03/2020
ms.openlocfilehash: 70ce3c82790db0296d5359b5db2e6a323306c309
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576426"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu kullanarak CSV veya Parquet dosyalarından UTF-8 metnini okuma sorunlarını giderme

Bu makalede, Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu kullanarak CSV veya Parquet dosyalarından UTF-8 metnini okumak için sorun giderme adımları sağlanmaktadır.

UTF-8 metni sunucusuz SQL havuzu kullanarak bir CSV veya PARQUET dosyasından Okunmuşsa, sorgu UTF8 olmayan harmanlamalara sahip VARCHAR sütunları döndürürse, ü ve ö gibi bazı özel karakterler yanlış dönüştürülür. Bu, SQL Server ve Azure SQL 'de bilinen bir sorundur. UTF8 olmayan SYNAPSE harmanlaması, müşteri sorgularının etkileneceğini sağlamak için SQL 'de varsayılandır. Standart Ingilizce karakterler ve genişletilmiş Latin karakterlerinden oluşan bir alt küme kullanan müşteriler dönüştürme hatalarını fark etmez. Yanlış dönüştürme, [sunucusuz SQL havuzunda UTF-8 metnini okumak Için her zaman UTF-8 harmanlamalarını kullanma](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633) bölümünde daha ayrıntılı olarak açıklanmıştır

## <a name="workaround"></a>Geçici çözüm

Bu sorunun geçici çözümü, CSV veya PARQUET dosyalarından UTF-8 metnini okurken her zaman UTF-8 harmanlaması kullanmaktır.

- Çoğu durumda, veritabanı üzerinde UTF8 harmanlaması ayarlamanız yeterlidir (meta veri işlemi).

   ```sql
   alter database MyDB
         COLLATE Latin1_General_100_BIN2_UTF8;
   ```

- OPENROWSET veya dış tablodaki VARCHAR sütununda açıkça harmanlama tanımlayabilirsiniz:

   ```sql
   select geo_id, cases = sum(cases)
   from openrowset(
           bulk 'latest/ecdc_cases.parquet', data_source = 'covid', format = 'parquet'
       ) with ( cases int,
                geo_id VARCHAR(6) COLLATE Latin1_General_100_BIN2_UTF8 ) as rows
   group by geo_id
   ```
 
- UTF8 verilerini okuyan dış tablolarda UTF8 harmanlaması belirtmediyseniz, etkilenen dış tabloları yeniden oluşturmanız ve VARCHAR sütunlarında UTF8 harmanlaması ayarlamanız gerekir (meta veri işlemi).


## <a name="next-steps"></a>Sonraki adımlar

* [SYNAPSE SQL ile Parquet dosyalarını sorgulama](../sql/query-parquet-files.md)
* [CSV dosyalarını SYNAPSE SQL ile sorgulama](../sql/query-single-csv-file.md)
* [SYNAPSE SQL ile CETAS](../sql/develop-tables-cetas.md)
* [Hızlı başlangıç: sunucusuz SQL havuzu kullanma](../quickstart-sql-on-demand.md)
