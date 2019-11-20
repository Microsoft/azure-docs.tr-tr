---
title: İş yükü önem düzeyini yapılandırma
description: İstek düzeyi önemini ayarlamayı öğrenin.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 59ba4b936f6098b0d0b3f5e571f107af088206e0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692698"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda iş yükü önem derecesini yapılandırma

SQL veri ambarı 'nda önem derecesi ayarlandığında, sorguların zamanlamasını etkileyebilirsiniz. Daha yüksek önem taşıyan sorgular, daha düşük öneme sahip sorgulardan önce çalıştırılmak üzere zamanlanır. Sorgulara önem atamak için bir iş yükü Sınıflandırıcısı oluşturmanız gerekir.

## <a name="create-a-workload-classifier-with-importance"></a>Önem derecesine sahip bir Iş yükü Sınıflandırıcısı oluşturun

Genellikle bir veri ambarı senaryosunda, sorgularının hızla çalışmasını gerektiren kullanıcılarınız vardır.  Kullanıcı, raporları çalıştırması gereken şirketin Yöneticiler olabilir veya Kullanıcı geçici sorgu çalıştıran bir analist olabilir. Bir sorguya önem düzeyi atamak için bir iş yükü Sınıflandırıcısı oluşturursunuz.  Aşağıdaki örneklerde, iki sınıflandırıcı oluşturmak için yeni [iş yükü sınıflandırıcı](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) sözdizimi kullanılır.  MemberName tek bir kullanıcı veya grup olabilir. Bireysel kullanıcı sınıflandırmaları, rol sınıflandırmalarına göre önceliklidir. Mevcut veri ambarı kullanıcılarını bulmak için şunu çalıştırın:

```sql
Select name from sys.sysusers
```

Daha yüksek önem taşıyan bir kullanıcı için iş yükü Sınıflandırıcısı oluşturmak için:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Daha düşük önem taşıyan bir kullanıcı için geçici sorgular çalıştıran bir iş yükü Sınıflandırıcısı oluşturmak için:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Sonraki Adımlar
- İş yükü yönetimi hakkında daha fazla bilgi için bkz. [Iş yükü sınıflandırması](sql-data-warehouse-workload-classification.md)
- Önem hakkında daha fazla bilgi için bkz. [Iş yükü önemi](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Manage ve Monitor Iş yükünü önem derecesi ' ne gidin](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
