---
title: Adanmış SQL Havuzu (eski adıyla SQL DW) nedir?
description: Azure SYNAPSE Analytics 'te adanmış SQL Havuzu (eski adıyla SQL DW), Azure SYNAPSE Analytics 'teki kurumsal veri ambarı işlevidir.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9077ce35065b1bf45646496cc4c43d6def82d958
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685234"
---
# <a name="what-is-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL Havuzu (eski adıyla SQL DW) nedir?

Azure Synapse Analytics, kurumsal veri ambarı özellikleriyle Büyük Veri analizini bir araya getiren bir analiz hizmetidir. Adanmış SQL Havuzu (eski adıyla SQL DW), Azure SYNAPSE Analytics 'te kullanılabilen kurumsal veri ambarı özelliklerine başvurur.



![Azure SYNAPSE ile bağlantılı olarak adanmış SQL Havuzu (eski adıyla SQL DW)](./media/sql-data-warehouse-overview-what-is/dedicated-sql-pool.png)



Adanmış SQL Havuzu (eski adıyla SQL DW), SYNAPSE SQL kullanılırken sağlanan analitik kaynakların koleksiyonunu temsil eder. Adanmış bir SQL havuzunun boyutu (eski adıyla SQL DW), veri ambarı birimleri (DWU) tarafından belirlenir.

Adanmış SQL havuzunuz oluşturulduktan sonra, basit [PolyBase](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL sorgularıyla büyük verileri içeri aktarabilir ve ardından yüksek performanslı analizler çalıştırmak için dağıtılmış sorgu altyapısının gücünden yararlanabilirsiniz. Verileri tümleştirdiğinizde ve analiz ettiğiniz için adanmış SQL Havuzu (eski adıyla SQL DW), işletmenizin daha hızlı ve daha sağlam Öngörüler için üzerine gelebilmesi için tek bir Truth sürümü olacaktır.

> [!NOTE]
>[Azure SYNAPSE Analytics belgelerini](../overview-what-is.md)inceleyin.
> 

## <a name="key-component-of-a-big-data-solution"></a>Büyük veri çözümünün anahtar bileşeni

Veri depolama, bulut tabanlı, uçtan uca büyük veri çözümünün temel bir bileşenidir.

![Veri ambarı çözümü](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

Bulut veri çözümlerinde, farklı kaynaklarda yer alan veriler büyük veri depolarına alınır. Büyük veri deposuna alınan veriler Hadoop, Spark ve makine öğrenmesi algoritmaları ile hazırlanır ve eğitilir. Veriler karmaşık Analize hazırlanmaya hazırsanız, adanmış SQL havuzu büyük veri depolarını sorgulamak için PolyBase 'i kullanır. PolyBase, verileri adanmış SQL Havuzu (eski adıyla SQL DW) tablolarına getirmek için Standart T-SQL sorgularını kullanır.

Adanmış SQL Havuzu (eski adıyla SQL DW), verileri sütunlu depolama ile ilişkisel tablolarda depolar. Bu biçim veri depolama maliyetlerini önemli ölçüde düşürürken sorgu performansını artırır. Veriler depolandıktan sonra geniş ölçekte analiz işlemleri gerçekleştirebilirsiniz. Analiz işlemleri geleneksel veritabanı sistemlerine kıyasla dakikalar yerine saniyeler veya günler yerine saatler içinde tamamlanır.

Analiz sonuçları dünya çapındaki raporlama veritabanlarına veya uygulamalarına iletilebilir. Ardından iş analistleri işlerle ilgili destekli kararlar almak üzere öngörü sahibi olabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SYNAPSE mimarisini](massively-parallel-processing-mpp-architecture.md) keşfet
- [Adanmış BIR SQL havuzu hızlı bir şekilde oluşturun](create-data-warehouse-portal.md)
- [Örnek verileri yükleyin](./load-data-from-azure-blob-storage-using-copy.md).
- [Videoları](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse) keşfet

Ya da diğer Azure SYNAPSE kaynaklarından bazılarına bakın.

- [Bloglarda](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/) ara
- [Özellik istekleri](https://feedback.azure.com/forums/307516-sql-data-warehouse) gönderme
- [Destek bileti oluşturma](sql-data-warehouse-get-started-create-support-ticket.md)
- [Soru sayfasında Microsoft Q&](/answers/topics/azure-synapse-analytics.html) arama
- [Stack Overflow Forumu](https://stackoverflow.com/questions/tagged/azure-sqldw) ara