---
title: Kaynak sınıfını bir iş yükü grubuna Dönüştür
description: Adanmış bir SQL havuzundaki kaynak sınıfına benzer bir iş yükü grubu oluşturmayı öğrenin.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1207f4856882d8aa0e6d1e41712071536bfecf29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728565"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Kaynak sınıflarını Iş yükü gruplarına Dönüştür

İş yükü grupları, sistem kaynaklarını yalıtmak ve içermesi için bir mekanizma sağlar.  Ayrıca, iş yükü grupları, üzerinde çalışan istekler için yürütme kuralları ayarlamanıza olanak sağlar.  Sorgu zaman aşımı yürütme kuralı, Kullanıcı müdahalesi olmadan ard arda yapılan sorguların iptal edilmesine izin verir.  Bu makalede, var olan bir kaynak sınıfı alma ve benzer bir yapılandırmaya sahip bir iş yükü grubu oluşturma açıklanır.  Ayrıca, isteğe bağlı bir sorgu zaman aşımı kuralı eklenir.

> [!NOTE]
> İş yükü gruplarını ve kaynak sınıflarını aynı anda kullanma hakkında rehberlik için [Iş yükü sınıflandırması](sql-data-warehouse-workload-classification.md) kavram belgesinde [sınıflandırıcılarla kaynak sınıfı atamalarını karıştırma](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) bölümüne bakın.

## <a name="understanding-the-existing-resource-class-configuration"></a>Mevcut kaynak sınıfı yapılandırmasını anlama

İş yükü grupları `REQUEST_MIN_RESOURCE_GRANT_PERCENT` , istek başına ayrılan toplam sistem kaynakları yüzdesini belirten adlı bir parametre gerektirir.  Kaynak ayırma, eşzamanlılık yuvaları ayırarak [kaynak sınıfları](resource-classes-for-workload-management.md#what-are-resource-classes) için yapılır.  İçin belirtme değerini belirlemek için `REQUEST_MIN_RESOURCE_GRANT_PERCENT` , <link tbd> dmv sys.dm_workload_management_workload_groups_stats kullanın.  Örneğin, aşağıdaki sorgu sorgusu, `REQUEST_MIN_RESOURCE_GRANT_PERCENT` staticrc40 şuna benzer bir iş yükü grubu oluşturmak için parametresi için kullanılabilecek bir değer döndürür.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> İş yükü grupları, genel sistem kaynaklarının yüzdesine göre çalışır.  

İş yükü grupları, ölçeği yukarı ve aşağı doğru olan genel sistem kaynaklarının yüzdesine göre çalıştığından, genel sistem kaynaklarına göre statik kaynak sınıflarına ayrılan kaynakların yüzdesi değişir.  Örneğin, staticrc40 at DW1000c genel sistem kaynaklarının% 19,2 ' i ayırır.  DW2000c adresinde% 9,6 ayrıldı.  İstek başına daha fazla kaynak ayırarak eşzamanlılık için ölçeği ölçeklendirmek istiyorsanız bu model benzerdir.

## <a name="create-workload-group"></a>Iş yükü grubu oluştur

Bilinen `REQUEST_MIN_RESOURCE_GRANT_PERCENT` , <link> iş yükü grubunu oluşturmak IÇIN Iş yükü grubu oluştur sözdizimini kullanabilirsiniz.  İsteğe bağlı olarak, `MIN_PERCENTAGE_RESOURCE` iş yükü grubu için kaynakları yalıtmak üzere sıfırdan büyük bir değer belirtebilirsiniz.  Ayrıca, isteğe bağlı olarak, `CAP_PERCENTAGE_RESOURCE` iş yükü grubunun kullanabileceği kaynak miktarını sınırlamak için 100 'den daha az bir süre belirtebilirsiniz.  

Düz RC 'yi bir örnek olarak kullanarak, aşağıdaki kod, `MIN_PERCENTAGE_RESOURCE` sistem kaynaklarının %10 ' sini ayırmak için ' i ayarlar `wgDataLoads` ve tek bir sorgunun her zaman çalışmasına izin verir.  Ayrıca, `CAP_PERCENTAGE_RESOURCE` %40 olarak ayarlanır ve bu iş yükü grubunu dört eşzamanlı istek ile sınırlandırır.  `QUERY_EXECUTION_TIMEOUT_SEC`Parametreyi 3600 olarak ayarlayarak 1 saatten uzun süre çalışan herhangi bir sorgu otomatik olarak iptal edilir.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 10
 ,MIN_PERCENTAGE_RESOURCE = 10
 ,CAP_PERCENTAGE_RESOURCE = 40
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Sınıflandırıcı oluşturma

Daha önce, sorguların kaynak sınıflarıyla eşlenmesi [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class)ile yapılır.  Aynı işlevselliği elde etmek ve istekleri iş yükü gruplarıyla eşlemek için [Iş yükü SıNıFLANDıRıCıSı oluştur](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) sözdizimini kullanın.  Sp_addrolemember kullanmak yalnızca bir oturum açma temelli kaynakları bir istek ile eşlemenizi sağlar.  Bir sınıflandırıcı, oturum açma hakkında ek seçenekler sağlar, örneğin:
    - etiket
    - oturum
    - Aşağıdaki örnek, `AdfLogin` oturum açma verilerinden, yukarıda oluşturulan iş yükü grubuna ayarlanmış [seçenek etiketini](sql-data-warehouse-develop-label.md)  de içeren sorguları atar `factloads` `wgDataLoads` .

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Örnek sorgu ile test etme

Aşağıda, iş yükü grubunun ve sınıflandırıcının doğru şekilde yapılandırıldığından emin olmak için örnek bir sorgu ve DMV sorgusu verilmiştir.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Sonraki adımlar

- [İş yükü yalıtımı](sql-data-warehouse-workload-isolation.md)
- [Nasıl yapılır-Iş yükü grubu oluşturma](quickstart-configure-workload-isolation-tsql.md)
- [Iş yükü SıNıFLANDıRıCıSı oluşturma (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql??view=azure-sqldw-latest&preserve-view=true)
- [Iş yükü grubu oluşturma (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest&preserve-view=true)
