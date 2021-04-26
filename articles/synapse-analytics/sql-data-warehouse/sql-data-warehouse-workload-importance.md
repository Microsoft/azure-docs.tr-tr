---
title: İş yükü önem düzeyi
description: Azure SYNAPSE Analytics 'te adanmış SQL havuzu sorgularının önemini ayarlamaya yönelik kılavuz.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 06d1957d182f2cabc336afcfc47a790442a3cb9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678415"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Azure SYNAPSE Analytics iş yükü önemi

Bu makalede, iş yükü önemlerinin Azure SYNAPSE 'de adanmış SQL havuzu istekleri için yürütme sırasını nasıl etkileyebileceğini açıklamaktadır.

## <a name="importance"></a>Önem

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

İş ihtiyaçları, veri ambarı iş yüklerinin diğerlerinden daha önemli olmasını gerektirebilir.  Mali dönemin kapanması için görev açısından kritik satış verilerinin yüklendiği bir senaryoyu göz önünde bulundurun.  Hava durumu verileri gibi diğer kaynakların veri yükleri katı SLA 'Lara sahip değildir. Hava durumu verilerini yükleme isteği için yüksek önem derecesi ayarlama bir istek için büyük önem derecesi ayarlamak, satış veri yükünün kaynaklara ilk kez erişmesini ve daha hızlı tamamlanmasını sağlar.

## <a name="importance-levels"></a>Önem düzeyleri

Beş önem düzeyi vardır: düşük, below_normal, normal, above_normal ve yüksek.  Önem ayarı olmayan isteklere varsayılan normal düzeyi atanır. Aynı önem düzeyine sahip istekler, bugün mevcut zamanlama davranışlarına sahiptir.

## <a name="importance-scenarios"></a>Önemli senaryolar

Yukarıda açıklanan temel önemli senaryonun ötesinde Sales ve hava durumu verileriyle birlikte, iş yükü önem derecesi veri işleme ve sorgulama ihtiyaçlarını karşılamak için yardımcı olan başka senaryolar da vardır.

### <a name="locking"></a>Kilitleme

Okuma ve yazma etkinliği için kilitlerin erişimi, doğal çekişmenin bir alanıdır. [Bölüm değiştirme](sql-data-warehouse-tables-partition.md) veya [nesne yeniden adlandırma](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) gibi etkinlikler yükseltilmiş kilitler gerektirir.  İş yükü önemi olmadan, Azure 'da adanmış SQL havuzu üretilen iş için optimize SYNAPSE. Üretilen iş için iyileştirmek, çalışırken ve sıraya alınan isteklerin aynı kilitleme ihtiyaçlarına ve kaynakların kullanılabilir olduğu durumlarda, sıraya alınan isteklerin istek kuyruğuna daha önce ulaşan istekleri atlayabilir. Daha yüksek kilitleme ihtiyaçlarına sahip isteklere iş yükü önemi uygulandıktan sonra. Daha yüksek öneme sahip istek, daha düşük öneme sahip istekten önce çalıştırılır.

Aşağıdaki örneği inceleyin:

- S1 etkin bir şekilde çalışır ve Salesolgu verilerini seçmektir.
- S2, Q1 'nin tamamlanmasını beklerken kuyruğa alındı.  Bu, 00 ' da gönderilmiştir ve yeni verileri satış olgusuna kaydetmeye çalışıyor.
- Q3 9:01:00'da gönderilir ve Salessıdan veri seçmek istiyor.

S2 ve Q3 aynı önem derecesine sahip ve Q1 hala yürütülerek S3 yürütülmeye başlayacaktır. S2, Salesolgusu üzerinde dışlamalı bir kilit beklemek için devam edecektir.  S2, S3 'den daha yüksek bir öneme sahipse, S3 yürütmeye başlamadan önce S2 bitene kadar bekler.

### <a name="non-uniform-requests"></a>Tekdüzen olmayan istekler

Farklı kaynak sınıflarına sahip isteklerin gönderilmesi durumunda önem derecesine yardımcı olabilecek önemli bir senaryo.  Daha önce belirtildiği gibi, Azure 'daki adanmış SQL havuzu, daha önce belirtildiği gibi, üretilen iş için optimize SYNAPSE. Karma boyut istekleri (smallrc veya Orta RC gibi) sıraya alınmışsa, adanmış SQL havuzu kullanılabilir kaynaklara uyan en eski gelen isteği seçer. İş yükü önemi uygulanmışsa, en yüksek önemli istek daha sonra zamanlanır.
  
DW500c üzerinde aşağıdaki örneği göz önünde bulundurun:

- S1, S2, Q3 ve S4, smallrc sorgularını çalıştırıyor.
- Q5,-00 ' da ortarc kaynak sınıfıyla gönderilir.
- Q6, smallrc kaynak sınıfıyla 9:01:00'da gönderilir.

Q5, düz RC olduğundan iki eşzamanlılık yuvası gerektirir. Q5 'in çalışan iki sorgunun tamamlanmasını beklemesi gerekir.  Ancak, çalışan sorgulardan biri (Q1-S4) tamamlandığında, kaynakları sorguyu yürütmek için mevcut olduğundan, Q6 hemen zamanlanır.  Q5, Q6 'den daha yüksek bir öneme sahipse, Q6 yürütülmeye başlamadan önce Q5 çalışmaya başlamadan önce bekler.

## <a name="next-steps"></a>Sonraki adımlar

- Sınıflandırıcı oluşturma hakkında daha fazla bilgi için bkz. [Iş yükü SıNıFLANDıRıCıSı oluşturma (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  
- İş yükü sınıflandırması hakkında daha fazla bilgi için bkz. [Iş yükü sınıflandırması](sql-data-warehouse-workload-classification.md).  
- İş yükü Sınıflandırıcısı oluşturma hakkında bilgi için hızlı başlangıç [oluşturma sınıflandırıcıya](quickstart-create-a-workload-classifier-tsql.md) bakın.
- [Iş yükü önemini yapılandırmak](sql-data-warehouse-how-to-configure-workload-importance.md) ve [Iş yükü yönetimini yönetmek ve izlemek](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)için nasıl yapılır makalelerine bakın.
- Sorguları ve atanan önemi görüntülemek için [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) bakın.
