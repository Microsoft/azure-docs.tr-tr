---
title: Adanmış SQL havuzunda iş yükü önemini yönetme ve izleme
description: Azure SYNAPSE Analytics için istek düzeyi önem derecesi adanmış SQL havuzunu yönetme ve izleme hakkında bilgi edinin.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 12b75ad3746cd0f54e27e474e0fd13bb0bba0e05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685251"
---
# <a name="manage-and-monitor-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>Azure SYNAPSE Analytics için adanmış SQL havuzunda iş yükü önemini yönetme ve izleme

DMVs ve katalog görünümlerini kullanarak Azure SYNAPSE 'de adanmış SQL havuzu istek düzeyi önemini yönetin ve izleyin.

## <a name="monitor-importance"></a>Önemli izleme

[Sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) dinamik yönetim görünümündeki yeni önem sütununu kullanarak önem derecesini izleyin.
Aşağıdaki izleme sorgusunda sorgular için gönderme zamanı ve başlangıç saati gösterilmektedir. Zamanlamanın planlanmasının nasıl yapıldığını görmek için önem derecesine sahip gönderme süresini ve başlangıç saatini gözden geçirin.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Sorguların zamanlamaya göre daha ayrıntılı bir şekilde bakmak için katalog görünümlerini kullanın.

## <a name="manage-importance-with-catalog-views"></a>Katalog görünümleriyle önemi yönetme

Sys.workload_management_workload_classifiers Katalog görünümü sınıflandırıcılerle ilgili bilgiler içerir. Kaynak sınıflarıyla eşlenen sistem tarafından tanımlanan sınıflandırıcıları dışlamak için aşağıdaki kodu yürütün:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

[Sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)Katalog görünümü, sınıflandırıcının oluşturulmasında kullanılan parametrelerle ilgili bilgiler içerir.  Aşağıdaki sorgu, ExecReportsClassifier 'in ```membername``` Executıvereports değerleri için parametresinde oluşturulduğunu gösterir:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![sorgu sonuçları](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Hatalı sınıflandırmayla ilgili sorun gidermeyi kolaylaştırmak için, iş yükü sınıflandırıcıları oluştururken kaynak sınıfı rol eşlemelerini kaldırmanızı öneririz. Aşağıdaki kod, var olan kaynak sınıfı rolü üyeliklerini döndürür. ```membername```Karşılık gelen kaynak sınıfından döndürülen her bir sp_droprolemember çalıştırın.
Aşağıda, bir iş yükü sınıflandırıcısını bırakmadan önce varolup olmadığını denetleme örneği verilmiştir:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Sonraki adımlar

- Sınıflandırma hakkında daha fazla bilgi için bkz. [Iş yükü sınıflandırması](sql-data-warehouse-workload-classification.md).
- Önem hakkında daha fazla bilgi için bkz. [Iş yükü önemi](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Iş yükü önemini yapılandırma bölümüne gidin](sql-data-warehouse-how-to-configure-workload-importance.md)
