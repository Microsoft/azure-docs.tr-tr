---
title: İş yükü sınıflandırma
description: Azure SQL veri ambarı 'nda sorgular için eşzamanlılık, önem ve işlem kaynaklarını yönetmek üzere sınıflandırmayı kullanma kılavuzu.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 15ca4b9fe3c40b7bf49d86464858747642e3cb5a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685391"
---
# <a name="azure-sql-data-warehouse-workload-classification"></a>Azure SQL veri ambarı iş yükü sınıflandırması

Bu makalede, bir kaynak sınıfı atama ve gelen isteklere önem vermenin SQL veri ambarı iş yükü sınıflandırma süreci açıklanmaktadır.

## <a name="classification"></a>Sınıflandırma

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

İş yükü yönetimi sınıflandırması, [kaynak sınıfları](resource-classes-for-workload-management.md#what-are-resource-classes) ve [önemi](sql-data-warehouse-workload-importance.md)atanarak iş yükü ilkelerinin isteklere uygulanmasını sağlar.

Veri ambarı iş yüklerini sınıflandırmanın birçok yolu olmakla kalmaz, en basit ve en yaygın sınıflandırma Load ve Query 'dir. INSERT, Update ve DELETE deyimleriyle veri yüklersiniz.  Seçim ' i kullanarak verileri sorgulayın. Veri ambarı çözümü, genellikle daha fazla kaynakla daha yüksek bir kaynak sınıfı atama gibi yükleme etkinliğine yönelik bir iş yükü ilkesine sahip olur. Yük etkinlikleriyle karşılaştırıldığında daha düşük önem düzeyi gibi sorgulara farklı bir iş yükü ilkesi uygulanabilir.

Ayrıca, yük ve sorgu iş yüklerinizi de alt sınıflandırın. Alt sınıflandırma, iş yüklerinizde daha fazla denetim sağlar. Örneğin, sorgu iş yükleri küp yenilemelerinden, pano sorgularından veya geçici sorgulardan oluşabilir. Bu sorgu iş yüklerinden her birini, farklı kaynak sınıflarıyla veya önem ayarlarına göre sınıflandırabilirsiniz. Yük, alt sınıflandırmadan da yararlanabilir. Büyük dönüşümler, daha büyük kaynak sınıflarına atanabilir. Önemli satış verilerinin, hava durumu verilerinden veya sosyal veri akışından önce yükleyici olduğundan emin olmak için daha yüksek önem düzeyi kullanılabilir.

Tüm deyimler, kaynak gerektirmediğinden veya yürütmeyi etkilemek için önem duymadığından sınıflandırılmazlar.  DBCC komutları, BEGIN, COMMıT ve ROLLBACK TRANSACTION deyimleri sınıflandırılmıyor.

## <a name="classification-process"></a>Sınıflandırma işlemi

SQL veri ambarı 'nda sınıflandırma, [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)kullanılarak kendisine atanmış karşılık gelen bir kaynak sınıfına sahip bir role Kullanıcı atanarak bugün elde edilir. Bir kaynak sınıfına bir oturum açma ötesinde istekleri niteleyebilme özelliği bu özellikle sınırlıdır. Artık sınıflandırma için daha zengin bir yöntem [oluşturma Iş yükü sınıflandırıcı](/sql/t-sql/statements/create-workload-classifier-transact-sql) sözdizimi ile kullanılabilir.  Bu söz dizimi ile SQL veri ambarı kullanıcıları, isteklere önem ve kaynak sınıfı atayabilir.  

> [!NOTE]
> Sınıflandırma, istek başına temelinde değerlendirilir. Tek bir oturumdaki birden çok istek farklı şekilde sınıflandırılabilir.

## <a name="classification-precedence"></a>Sınıflandırma önceliği

Sınıflandırma sürecinin bir parçası olarak, hangi kaynak sınıfının atandığını belirleyen öncelik yerinde olur. Veritabanı kullanıcısını temel alan sınıflandırma, rol üyeliğinden önceliklidir. KullanıcıA veritabanı kullanıcısını bir düz RC kaynak sınıfıyla eşleyen bir sınıflandırıcı oluşturursanız. Ardından, RoleA veritabanı rolünü (UserA 'nın üyesi olduğu) largerc kaynak sınıfına eşleyin. Veritabanı kullanıcısını, düz RC kaynak sınıfıyla eşleyen sınıflandırıcının, RoleA veritabanı rolünü largerc kaynak sınıfıyla eşleştiren sınıflandırıcıdan öncelikli olması gerekir.

Bir Kullanıcı birden çok sınıflandırıcıda atanmış veya eşleştirilmiş farklı kaynak sınıflarına sahip birden çok rolün üyesiyse, kullanıcıya en yüksek kaynak sınıfı ataması verilir.  Bu davranış, varolan kaynak sınıfı atama davranışı ile tutarlıdır.

## <a name="system-classifiers"></a>Sistem sınıflandırıcıları

İş yükü sınıflandırmasında sistem iş yükü sınıflandırıcıları vardır. Sistem sınıflandırıcıları, var olan kaynak sınıfı rolü üyeliklerini, normal öneme sahip kaynak sınıfı kaynak tahsisatlarına eşler. Sistem sınıflandırıcıları bırakılamaz. Sistem sınıflandırıcıları görüntülemek için aşağıdaki sorguyu çalıştırabilirsiniz:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Sınıflandırıcılarla kaynak sınıfı atamalarını karıştırma

Sizin adınıza oluşturulan sistem sınıflandırıcıları, iş yükü sınıflandırmasına geçirmek için kolay bir yol sağlar. Sınıflandırma önceliğiyle kaynak sınıfı rol eşlemelerini kullanmak, önem derecesine sahip yeni sınıflandırıcılar oluşturmaya başladığınızda yanlış sınıflandırmaya neden olabilir.

Aşağıdaki senaryoyu göz önünde bulundurun:

- Mevcut bir veri ambarında, largerc kaynak sınıfı rolüne atanan bir veritabanı kullanıcısı DBAUser vardır. Kaynak sınıfı ataması sp_addrolemember ile gerçekleştirildi.
- Veri ambarı artık iş yükü yönetimi ile güncelleştirildi.
- Yeni sınıflandırma söz dizimini test etmek için, DBARole veritabanı rolü (DBAUser 'ın üyesi olduğu), bunları ana RC ve yüksek önem derecesine eşleyerek oluşturulmuş bir sınıflandırıcıdır.
- DBAUser oturum açar ve bir sorgu çalıştırdığında sorgu largerc 'ye atanır. Bir Kullanıcı bir rol üyeliğinden öncelikli olduğundan.

Hatalı sınıflandırmayla ilgili sorun gidermeyi kolaylaştırmak için, iş yükü sınıflandırıcıları oluştururken kaynak sınıfı rol eşlemelerini kaldırmanızı öneririz.  Aşağıdaki kod, var olan kaynak sınıfı rolü üyeliklerini döndürür.  Karşılık gelen kaynak sınıfından döndürülen her üye adı için [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) çalıştırın.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>Sonraki adımlar

- Sınıflandırıcı oluşturma hakkında daha fazla bilgi için bkz. [Iş yükü SıNıFLANDıRıCıSı oluşturma (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- İş yükü Sınıflandırıcısı oluşturma [iş yükü Sınıflandırıcısı oluşturma](quickstart-create-a-workload-classifier-tsql.md)hakkında hızlı başlangıç bölümüne bakın.
- [Iş yükü önemini yapılandırmak](sql-data-warehouse-how-to-configure-workload-importance.md) ve [iş yükü yönetimini yönetmek ve izlemek](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)için nasıl yapılır makalelerine bakın.
- Sorguları ve atanan önemi görüntülemek için bkz. [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) .