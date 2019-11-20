---
title: Kaynak sınıfını bir iş yükü grubuna Dönüştür
description: Azure SQL veri ambarı 'nda kaynak sınıfına benzer bir iş yükü grubu oluşturmayı öğrenin.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5708dccce5f7cdcc33fe6bfca980126cd8b5ee7f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685712"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Kaynak sınıflarını Iş yükü gruplarına Dönüştür
İş yükü grupları, sistem kaynaklarını yalıtmak ve içermesi için bir mekanizma sağlar.  Ayrıca, iş yükü grupları, üzerinde çalışan istekler için yürütme kuralları ayarlamanıza olanak sağlar.  Sorgu zaman aşımı yürütme kuralı, Kullanıcı müdahalesi olmadan ard arda yapılan sorguların iptal edilmesine izin verir.  Bu makalede, var olan bir kaynak sınıfı alma ve benzer bir yapılandırmaya sahip bir iş yükü grubu oluşturma açıklanır.  Ayrıca, isteğe bağlı bir sorgu zaman aşımı kuralı eklenir.

## <a name="understanding-the-existing-resource-class-configuration"></a>Mevcut kaynak sınıfı yapılandırmasını anlama
İş yükü grupları, istek başına ayrılan toplam sistem kaynakları yüzdesini belirten `REQUEST_MIN_RESOURCE_GRANT_PERCENT` adlı bir parametre gerektirir.  Kaynak ayırma, eşzamanlılık yuvaları ayırarak [kaynak sınıfları](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes) için yapılır.  `REQUEST_MIN_RESOURCE_GRANT_PERCENT`için belirtme değerini belirlemek için sys. DM _workload_management_workload_groups_stats <link tbd> DMV kullanın.  Örneğin, aşağıdaki sorgu sorgusu, staticrc40 öğesine benzer bir iş yükü grubu oluşturmak için `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parametresi için kullanılabilecek bir değer döndürür.   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> İş yükü grupları, genel sistem kaynaklarının yüzdesine göre çalışır.  

İş yükü grupları, ölçeği yukarı ve aşağı doğru olan genel sistem kaynaklarının yüzdesine göre çalıştığından, genel sistem kaynaklarına göre statik kaynak sınıflarına ayrılan kaynakların yüzdesi değişir.  Örneğin, staticrc40 at DW1000c genel sistem kaynaklarının% 9,6 ' i ayırır.  DW2000c adresinde% 19,2 ayrıldı.  İstek başına daha fazla kaynak ayırarak eşzamanlılık için ölçeği ölçeklendirmek istiyorsanız bu model benzerdir.   

## <a name="create-workload-group"></a>Iş yükü grubu oluştur
Bilinen `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, iş yükü grubunu oluşturmak için Iş yükü grubu oluşturma <link> sözdizimini kullanabilirsiniz.  İsteğe bağlı olarak, iş yükü grubu için kaynakları yalıtmak üzere sıfırdan büyük bir `MIN_PERCENTAGE_RESOURCE` belirtebilirsiniz.  Ayrıca, isteğe bağlı olarak, iş yükü grubunun tüketebileceği kaynak miktarını sınırlamak için 100 ' den az `CAP_PERCENTAGE_RESOURCE` belirtebilirsiniz.  

Aşağıdaki örnek, `MIN_PERCENTAGE_RESOURCE` sistem kaynaklarının% 9,6 ' sini `wgDataLoads` olacak şekilde ayarlar ve bir sorgunun her zaman çalışmasına izin verir.  Ayrıca, `CAP_PERCENTAGE_RESOURCE`% 38,4 olarak ayarlanır ve bu iş yükü grubunu dört eşzamanlı istek ile sınırlandırır.  `QUERY_EXECUTION_TIMEOUT_SEC` parametresini 3600 olarak ayarlayarak, 1 saatten uzun süre çalışan herhangi bir sorgu otomatik olarak iptal edilir.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Sınıflandırıcı oluşturma
Daha önce, sorguları kaynak sınıflarıyla eşleme [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class)ile yapılır.  Aynı işlevselliği elde etmek ve istekleri iş yükü gruplarıyla eşlemek için [Iş yükü SıNıFLANDıRıCıSı oluştur](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql) sözdizimini kullanın.  Sp_addrolemember kullanarak yalnızca bir oturum açma temelli kaynakları bir istek ile eşlemenizi sağlayabilirsiniz.  Bir sınıflandırıcı, oturum açma hakkında ek seçenekler sağlar, örneğin:
    - etiket
    - oturum
    - Aşağıdaki örnek, `AdfLogin` oturum açma, yukarıda oluşturulan `wgDataLoads` iş yükü grubuna `factloads` olarak ayarlanmış [seçenek etiketi](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label) de olan sorguları atar.

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

İş yükü yalıtımı-bağlantı TBD nasıl yapılır-bir Iş yükü grubu oluşturma-bağlantı TBD