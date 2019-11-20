---
title: Otomatik ayarlamayı etkinleştirme
description: Azure SQL veritabanınızda otomatik ayarlamayı kolayca etkinleştirebilirsiniz.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 0abf4bb015be52a10178423a566433b87127a167
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821904"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Sorguları izlemek ve iş yükü performansını geliştirmek için otomatik ayarlamayı etkinleştirin

Azure SQL veritabanı, sorgularınızı sürekli olarak izleyen ve iş yükünüzün performansını artırmak için gerçekleştirebileceğiniz eylemi tanımlayan otomatik olarak yönetilen bir veri hizmetidir. Önerileri gözden geçirebilir ve bunları el ile uygulayabilir ya da Azure SQL veritabanı 'nın düzeltici eylemleri otomatik olarak uygulamasını sağlayabilirsiniz. Bu, **otomatik ayarlama modu**olarak bilinir.

Otomatik ayarlama, [Azure Portal](sql-database-automatic-tuning-enable.md#azure-portal), [REST API](sql-database-automatic-tuning-enable.md#rest-api) ÇAĞRıLARı ve [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) komutları aracılığıyla sunucuda veya veritabanı düzeyinde etkinleştirilebilir.

> [!NOTE]
> Yönetilen örnek için desteklenen seçenek FORCE_LAST_GOOD_PLAN yalnızca [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) üzerinden yapılandırılabilir. Bu makalede açıklanan portal tabanlı yapılandırma ve Otomatik Dizin ayarlama seçenekleri yönetilen örnek için uygulanmıyor.

> [!NOTE]
> ARM (Azure Resource Manager) şablonu aracılığıyla otomatik ayarlama seçeneklerinin yapılandırılması Şu anda desteklenmiyor.

## <a name="enable-automatic-tuning-on-server"></a>Sunucuda otomatik ayarlamayı etkinleştir

Sunucu düzeyinde, otomatik ayarlama yapılandırmasını "Azure Varsayılanları" ndan devralmayı veya yapılandırmayı devralma seçeneğini belirleyebilirsiniz. Azure Varsayılanları FORCE_LAST_GOOD_PLAN etkindir, CREATE_INDEX etkinleştirilmiştir ve DROP_INDEX devre dışıdır.

### <a name="azure-portal"></a>Azure portal

Azure SQL veritabanı mantıksal **sunucusunda**otomatik ayarlamayı etkinleştirmek için Azure Portal ' deki sunucuya gidin ve menüden **otomatik ayarlama** ' yı seçin.

![Sunucu](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Lütfen şu anda **DROP_INDEX** seçeneğinin bölüm değiştirme ve Dizin ipuçları kullanan uygulamalarla uyumlu olmadığına ve bu durumlarda etkinleştirilmemelidir. Kullanılmayan dizinleri bırakma, Premium ve İş Açısından Kritik hizmet katmanlarında desteklenmez.
>

Etkinleştirmek istediğiniz otomatik ayarlama seçeneklerini belirleyin ve **Uygula**' yı seçin.

Bir sunucudaki otomatik ayarlama seçenekleri, bu sunucudaki tüm veritabanlarına uygulanır. Varsayılan olarak, tüm veritabanları yapılandırma üst sunucusundan devralınır, ancak bu, her veritabanı için ayrı ayrı geçersiz kılınabilir ve belirtilebilir.

### <a name="rest-api"></a>REST API

Bir sunucuda otomatik ayarlamayı etkinleştirmek için REST API kullanma hakkında daha fazla bilgi edinin [SQL Server otomatik ayarlama güncelleştirme ve http YÖNTEMLERINI alma](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)' ya bakın.

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Tek bir veritabanında otomatik ayarlamayı etkinleştir

Azure SQL veritabanı, her bir veritabanı için otomatik ayarlama yapılandırmasını tek tek belirtmenizi sağlar. Veritabanı düzeyinde otomatik ayarlama yapılandırmasını "Azure Varsayılanları" ana sunucusundan devralmayı veya yapılandırmayı devralma seçeneğini belirleyebilirsiniz. Azure Varsayılanları FORCE_LAST_GOOD_PLAN etkinleştirilmiş, CREATE_INDEX etkin ve DROP_INDEX devre dışı olarak ayarlanır.

> [!TIP]
> Genel öneri, otomatik ayarlama yapılandırmasını **sunucu düzeyinde** yönetmelidir, böylece aynı yapılandırma ayarları her veritabanına otomatik olarak uygulanabilir. Yalnızca veritabanının aynı sunucudan gelen ayarları devrallarından farklı ayarlara sahip olması gerekiyorsa, tek bir veritabanında otomatik ayarlamayı yapılandırın.
>

### <a name="azure-portal"></a>Azure portal

**Tek bir veritabanında**otomatik ayarlamayı etkinleştirmek için Azure Portal veritabanında veritabanına gidin ve **otomatik ayarlama**' yı seçin.

Bireysel otomatik ayarlama ayarları her veritabanı için ayrı olarak yapılandırılabilir. Tek bir otomatik ayarlama seçeneğini el ile yapılandırabilir veya bir seçeneğin ayarlarını sunucudan devralmasını belirtebilirsiniz.

![Database](./media/sql-database-automatic-tuning-enable/database.png)

Lütfen şu anda DROP_INDEX seçeneğinin bölüm değiştirme ve Dizin ipuçları kullanan uygulamalarla uyumlu olmadığına ve bu durumlarda etkinleştirilmemelidir.

İstediğiniz yapılandırmayı seçtikten sonra **Uygula**' ya tıklayın.

### <a name="rest-api"></a>REST API 'SI

Tek bir veritabanında otomatik ayarlamayı etkinleştirmek üzere REST API kullanma hakkında daha fazla bilgi edinmek için bkz. [SQL veritabanı otomatik ayarlama güncelleştirme ve http YÖNTEMLERINI alma](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

T-SQL aracılığıyla tek bir veritabanında otomatik ayarlamayı etkinleştirmek için veritabanına bağlanın ve aşağıdaki sorguyu yürütün:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Otomatik ayarlamayı OTOMATIK olarak ayarlamak, Azure varsayılanlarını uygular. DEVRALMA olarak ayarlandığında, otomatik ayarlama yapılandırması üst sunucudan devralınacaktır. Özel ' i seçtiğinizde otomatik ayarlamayı el ile yapılandırmanız gerekecektir.

Bireysel otomatik ayarlama seçeneklerini T-SQL aracılığıyla yapılandırmak için veritabanına bağlanın ve şunun gibi sorguyu yürütün:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Ayrı ayarlama seçeneğinin açık olarak ayarlanması, veritabanının Devralındığı tüm ayarları geçersiz kılar ve ayarlama seçeneğini etkinleştirir. Devre dışı olarak ayarlandığında, veritabanının Devralındığı tüm ayarlar da geçersiz kılınır ve ayarlama seçeneğini devre dışı bırakılır. Otomatik ayarlama seçeneği, varsayılan olarak belirtildiğinde yapılandırma, veritabanı düzeyi otomatik ayarlama ayarından devralınır.  

> [!IMPORTANT]
> [Etkin coğrafi çoğaltma](sql-database-auto-failover-group.md)söz konusu olduğunda, otomatik ayarlamaya yalnızca birincil veritabanında yapılandırılması gerekir. Örneğin, dizin oluşturma veya silme gibi otomatik olarak uygulanan ayarlama eylemleri, salt okunurdur. Salt okunabilir ikincil üzerinde T-SQL aracılığıyla otomatik ayarlamayı etkinleştirmeye çalışmak, salt okunurdur ikincil üzerinde farklı bir ayarlama yapılandırması desteklenmediğinden hata oluşmasına neden olur.
>

Daha fazla doğrulamaya ilişkin T-SQL seçeneklerini bulma otomatik ayarlamayı yapılandırmak için bkz. [SQL veritabanı sunucusu için alter database set Options (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Sistem tarafından devre dışı

Otomatik ayarlama, veritabanında aldığı tüm işlemleri izliyor ve bazı durumlarda otomatik ayarlamanın veritabanında düzgün şekilde çalışamadığı tespit edebilir. Bu durumda ayarlama seçeneği sistem tarafından devre dışı bırakılacaktır. Çoğu durumda bunun nedeni, sorgu deposunun etkin olmaması veya belirli bir veritabanında salt okuma durumunda olması olabilir.

## <a name="permissions"></a>İzinler

Otomatik ayarlama Azure özelliği olduğundan, bunu kullanmak için Azure 'ın yerleşik RBAC rollerini kullanmanız gerekir. Yalnızca SQL kimlik doğrulaması kullanmak Azure portal özelliği kullanmak için yeterli olmaz.

Otomatik ayarlamayı kullanmak için, kullanıcıya verilmesi gereken en düşük izin Azure 'un yerleşik [SQL DB katılımcısı](../role-based-access-control/built-in-roles.md#sql-db-contributor) rolüdür. Ayrıca, katkıda bulunan, katkıda bulunan ve sahip SQL Server daha yüksek ayrıcalık rollerini kullanmayı da düşünebilirsiniz.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Otomatik ayarlama e-posta bildirimlerini yapılandırma

Bkz. [otomatik ayarlama e-posta bildirimleri](sql-database-automatic-tuning-email-notifications.md) Kılavuzu.

## <a name="next-steps"></a>Sonraki adımlar

* Otomatik ayarlama hakkında daha fazla bilgi edinmek ve performansı artırmanıza nasıl yardımcı olabileceğini öğrenmek için [otomatik ayarlama makalesini](sql-database-automatic-tuning.md) okuyun.
* Azure SQL veritabanı performans önerilerine genel bakış için bkz. [performans önerileri](sql-database-advisor.md) .
* En iyi sorguların performans etkisini görüntüleme hakkında bilgi edinmek için bkz. [sorgu Performans öngörüleri](sql-database-query-performance.md) .
