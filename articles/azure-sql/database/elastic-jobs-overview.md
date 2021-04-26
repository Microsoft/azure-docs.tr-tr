---
title: Elastik veritabanı Işleri (Önizleme)
description: Azure SQL veritabanı 'nda bir veya daha fazla veritabanı kümesi üzerinde Transact-SQL (T-SQL) betikleri çalıştırmak için elastik veritabanı Işlerini (Önizleme) yapılandırma
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: f9a026ed47d662b80ef01e505bfbcf8f32d20b04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792183"
---
# <a name="create-configure-and-manage-elastic-jobs-preview"></a>Elastik işler oluşturma, yapılandırma ve yönetme (Önizleme)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu makalede, elastik işler oluşturmayı, yapılandırmayı ve yönetmeyi öğreneceksiniz.

Elastik işler kullandıysanız, [Azure SQL veritabanı 'nda iş Otomasyonu kavramları hakkında daha fazla bilgi edinin](job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Aracıyı oluşturma ve yapılandırma

1. Boş bir S0 veya daha yüksek veritabanı oluşturun veya oluşturun. Bu veritabanı, elastik Iş Aracısı oluşturma sırasında *iş veritabanı* olarak kullanılacaktır.
2. [Portalda](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) veya [PowerShell](elastic-jobs-powershell-create.md#create-the-elastic-job-agent)Ile elastik bir iş Aracısı oluşturun.

   ![Elastik Iş Aracısı oluşturma](./media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>İşleri oluşturma, çalıştırma ve yönetme

1. [PowerShell](elastic-jobs-powershell-create.md) veya [T-SQL](elastic-jobs-tsql-create-manage.md#create-a-credential-for-job-execution)kullanarak iş *veritabanında* iş yürütmeye yönelik bir kimlik bilgisi oluşturun.
2. [PowerShell](elastic-jobs-powershell-create.md) veya [T-SQL](elastic-jobs-tsql-create-manage.md#create-a-target-group-servers)kullanarak hedef grubu (işi çalıştırmak istediğiniz veritabanları) tanımlayın.
3. İşin çalışacağı her veritabanında bir iş aracısı kimlik bilgisi oluşturun [(kullanıcıyı (veya rolü) gruptaki her bir veritabanına ekleyin)](logins-create-manage.md). Örnek için bkz. [PowerShell öğreticisi](elastic-jobs-powershell-create.md).
4. [PowerShell](elastic-jobs-powershell-create.md) veya [T-SQL](elastic-jobs-tsql-create-manage.md#deploy-new-schema-to-many-databases)kullanarak bir iş oluşturun.
5. [PowerShell](elastic-jobs-powershell-create.md) veya [T-SQL](elastic-jobs-tsql-create-manage.md#deploy-new-schema-to-many-databases) kullanarak iş adımlarını ekleyin.
6. [PowerShell](elastic-jobs-powershell-create.md#run-the-job) veya [T-SQL](elastic-jobs-tsql-create-manage.md#begin-unplanned-execution-of-a-job)kullanarak bir işi çalıştırın.
7. Portal, [PowerShell](elastic-jobs-powershell-create.md#monitor-status-of-job-executions) veya [T-SQL](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status)kullanarak iş yürütme durumunu izleyin.

   ![Portal](./media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>İşleri çalıştırmak için kullanılan kimlik bilgileri

İşler, yürütme sırasında hedef grup tarafından belirtilen veritabanlarına bağlanmak için [veritabanı kapsamlı kimlik bilgilerini](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) kullanır. Hedef grupta sunucular veya havuzlar varsa bu veritabanı kapsamlı kimlik bilgileri, kullanılabilir durumdaki veritabanlarını numaralandırmak amacıyla asıl veritabanına bağlanmak için kullanılır.

Bir işi çalıştırmak için uygun kimlik bilgilerinin ayarlanması kafa karışıklığına neden olabileceğinden aşağıdaki noktaları göz önünde bulundurun:

- Veritabanı kapsamlı kimlik bilgilerinin *iş veritabanında* oluşturulması gerekir.
- **Tüm hedef veritabanlarının, işin başarıyla tamamlanabilmesi için [yeterli izinlere](/sql/relational-databases/security/permissions-database-engine) sahip bir oturum açma işlemi olması gerekir** ( `jobuser` Aşağıdaki diyagramda).
- Kimlik bilgileri işler arasında yeniden kullanılabilir ve kimlik bilgisi parolaları, iş nesnelerine salt okuma erişimi olan kullanıcılardan şifrelenir ve bunların güvenliğini sağlar.

Aşağıdaki resim, uygun iş kimlik bilgilerinin anlaşılması ve ayarlanması konusunda yardımcı olmak üzere tasarlanmıştır. **Kullanıcının, işin çalıştırılacağı her veritabanında (tüm *hedef kullanıcı veritabanlarında*) oluşturulması gerektiğini unutmayın**.

![Elastik İşler kimlik bilgileri](./media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>En iyi güvenlik uygulamaları

Elastik İşlerle çalışırken dikkat etmeniz gereken en iyi deneyimlerin bazıları:

- API’lerin kullanımını güvenilir kişilerle sınırlayın.
- Kimlik bilgileri iş adımını gerçekleştirmek için gerekli olan en düşük ayrıcalıklara sahip olmalıdır. Daha fazla bilgi için bkz. [Yetkilendirme ve izinler](/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- Sunucu ve/veya havuz hedef grubu üyesini kullanırken, iş yürütmeden önce sunucu (ler) ve/veya havuzların veritabanı listelerini genişletmek için kullanılan veritabanlarını görüntülemek/listelemek için ana veritabanında haklara sahip ayrı bir kimlik bilgisi oluşturmanız önerilir.

## <a name="agent-performance-capacity-and-limitations"></a>Aracı performansı, kapasitesi ve sınırlamaları

Elastik İşler, uzun süren işlerin tamamlanması sırasında en az düzeyde işlem kaynağı kullanır.

Hedef veritabanı grubunun boyutuna ve bir işin istenen yürütme süresine (eşzamanlı çalışan sayısı) bağlı olarak aracı için gerekli olan işlem süresi ve *İş veritabanı* performansı değişiklik gösterir (hedef ve iş sayısı ne kadar yüksek olursa gereken işlem zamanı o kadar fazla olur).

Önizleme şu an için 100 eşzamanlı işle sınırlıdır.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>İşlerin hedef veritabanının performansını düşürmesini engelleme

Bir SQL elastik havuzundaki veritabanları üzerinde iş çalıştırılması sırasında kaynakların aşırı yüklenmesini önlemek için işler aynı anda üzerinde çalışılabilecek veritabanı sayısını sınırlayacak şekilde yapılandırılabilir.

Bir işin üzerinde çalıştığı eşzamanlı veritabanı sayısını `sp_add_jobstep` `@max_parallelism` , T-SQL Içinde veya PowerShell 'de saklı yordamın parametresini ayarlayarak ayarlayın `Add-AzSqlElasticJobStep -MaxParallelism` .

## <a name="best-practices-for-creating-jobs"></a>İş oluşturmak için en iyi deneyimler

### <a name="idempotent-scripts"></a>Bir kez etkili betikler
Bir işin T-SQL betiklerinin [bir kez etkili](https://en.wikipedia.org/wiki/Idempotence) olması gerekir. **Bir kez etkili**, betiğin başarılı olması ve tekrar çalıştırılması durumunda aynı sonucun ortaya çıkması anlamına gelir. Bir betik, geçici ağ sorunları nedeniyle başarısız olabilir. Bu durumda iş, betiği atlamadan önce otomatik olarak önceden belirtilen sayıda yeniden deneme gerçekleştirir. Bir kez etkili betik, iki kez (veya daha fazla) çalıştırılsa dahi aynı sonucu verir.

Basit bir yöntem, bir nesneyi oluşturmadan önce mevcut olup olmadığını test etmektir.


```sql
IF NOT EXISTS (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Benzer şekilde bir betiğin mantıksal olarak test ederek ve bulduğu sonuçlara göre kendini ayarlayarak başarılı şekilde yürütülebilmesi gerekir.



## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell kullanarak elastik Işler oluşturma ve yönetme](elastic-jobs-powershell-create.md)
- [Transact-SQL (T-SQL) kullanarak Elastik İşler oluşturma ve yönetme](elastic-jobs-tsql-create-manage.md)