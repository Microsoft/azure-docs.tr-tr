---
title: SQL 'den Azure 'a Izleme günlük sorgusu sayfası | Microsoft Docs
description: Azure Izleyici 'de günlük sorguları yazarken SQL 'e alışkın olan kullanıcılar için yardım.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 4acf3c2f8cee3ca9e679915eec677b6dd92792bf
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932922"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>SQL 'den Azure 'a Izleme günlük sorgusu sayfası 

Aşağıdaki tabloda, SQL 'e alışkın olan kullanıcıların Azure Izleyici 'de günlük sorgularını yazmak için kusto sorgu dilini öğrenme konusunda yardımcı olur. Genel senaryoları ve Azure Izleyici günlük sorgusunda eşdeğerini çözmek için T-SQL komutuna göz atın.

## <a name="sql-to-azure-monitor"></a>SQL 'den Azure 'a Izleyici

Açıklama                             |SQL sorgusu                                                                                          |Azure Izleyici günlük sorgusu
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Tablodaki tüm verileri seçin            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Tablodaki belirli sütunları seçme    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
Tablodaki 100 kaydı seçme         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
Null değerlendirme                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Dize karşılaştırması: eşitlik             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Dize karşılaştırması: alt dize            |`SELECT * FROM dependencies WHERE name like "%bcd%"`                                                   |<code>dependencies <br>&#124; where name contains "bcd"</code>
Dize karşılaştırması: joker karakter             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Tarih karşılaştırması: Son 1 gün             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Tarih karşılaştırması: tarih aralığı             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Boole karşılaştırması                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False" </code>
Sırala                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc </code>
Ayrı                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type </code>
Gruplandırma, toplama                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Sütun diğer adları, Genişlet                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Ölçüye göre ilk n kayıt                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Birleşim                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
Birleşim: koşullara sahip                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5`                |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Birleştir                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Sonraki adımlar

- [Azure izleyici 'de günlük sorguları yazma](get-started-queries.md)hakkındaki derslere gidin.
