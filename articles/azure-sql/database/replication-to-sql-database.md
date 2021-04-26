---
title: Azure SQL veritabanı 'na Azure SQL Server çoğaltması
description: Azure SQL veritabanında bir veritabanını, tek yönlü bir işlem veya anlık görüntü çoğaltma topolojisinde anında iletme abonesi olarak yapılandırabilirsiniz.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 6ff1d485ab4c0662ae8a9d754ce67b1446b76fcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780963"
---
# <a name="replication-to-azure-sql-database"></a>Azure SQL veritabanı 'na çoğaltma
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bir Azure SQL veritabanını, tek yönlü bir işlem veya anlık görüntü çoğaltma topolojisinde anında iletme abonesi olarak yapılandırabilirsiniz.

> [!NOTE]
> Bu makalede, Azure SQL veritabanı 'nda [İşlemsel çoğaltmanın](/sql/relational-databases/replication/transactional/transactional-replication) kullanımı açıklanmaktadır. Tek tek veritabanlarının tam okunabilir çoğaltmalarını oluşturmanızı sağlayan bir Azure SQL veritabanı özelliği olan [etkin coğrafi çoğaltma](./active-geo-replication-overview.md)ile ilgili değildir.

## <a name="supported-configurations"></a>Desteklenen yapılandırmalar
  
- Azure SQL veritabanı yalnızca bir SQL Server yayımcısı ve dağıtıcısının gönderim abonesi olabilir.  
- Yayımcı ve/veya dağıtıcı gibi davranan SQL Server örnek, [Şirket içinde çalışan bir SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads)örneği olabilir, [Azure SQL yönetilen örneği](../managed-instance/instance-create-quickstart.md)veya [buluttaki bir azure sanal makinesinde çalışan bir SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)örneği olabilir. 
- Dağıtım veritabanı ve çoğaltma aracıları Azure SQL veritabanı 'ndaki bir veritabanına yerleştirilemez.  
- [Anlık görüntü](/sql/relational-databases/replication/snapshot-replication) ve [tek yönlü işlemsel](/sql/relational-databases/replication/transactional/transactional-replication) çoğaltma desteklenir. Eşler arası işlem çoğaltma ve birleştirme çoğaltması desteklenmez.

### <a name="versions"></a>Sürümler  

Azure SQL veritabanında bir veritabanına başarıyla çoğaltma yapmak için SQL Server yayımcılar ve dağıtımcılar aşağıdaki sürümlerden birini kullanıyor olmalıdır:

SQL Server veritabanından herhangi bir Azure SQL veritabanına yayımlama, aşağıdaki SQL Server sürümleri tarafından desteklenir:

- SQL Server 2016 ve üzeri
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) veya [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) veya [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Desteklenmeyen bir sürüm kullanılarak çoğaltmayı yapılandırma denemesi, hata numarası MSSQL_REPL20084 (işlem aboneye bağlanamaz.) ve MSSQL_REPL40532 ( \<name> oturum açma tarafından istenen sunucu açılamıyor. Oturum açılamadı.).  

Azure SQL veritabanı 'nın tüm özelliklerini kullanmak için, en son [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ve [SQL Server veri araçları](/sql/ssdt/download-sql-server-data-tools-ssdt)sürümlerini kullanıyor olmanız gerekir.  

### <a name="types-of-replication"></a>Çoğaltma türleri

Farklı [çoğaltma türleri](/sql/relational-databases/replication/types-of-replication)vardır:

| Çoğaltma | Azure SQL Veritabanı | Azure SQL Yönetilen Örnek |
| :----| :------------- | :--------------- |
| [**Standart Işlem**](/sql/relational-databases/replication/transactional/transactional-replication) | Evet (yalnızca abone olarak) | Yes | 
| [**Görüntüye**](/sql/relational-databases/replication/snapshot-replication) | Evet (yalnızca abone olarak) | Yes|
| [**Birleştirme çoğaltması**](/sql/relational-databases/replication/merge/merge-replication) | Hayır | Hayır|
| [**Eşler arası**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Hayır | Hayır|
| [**Çift yönlü**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Hayır | Yes|
| [**Güncelleştirilebilir abonelikler**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Hayır | Hayır|
| &nbsp; | &nbsp; | &nbsp; |

  
## <a name="remarks"></a>Açıklamalar

- Yalnızca Azure SQL veritabanı 'na yönelik gönderme abonelikleri desteklenir.  
- Çoğaltma, [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) kullanılarak veya yayımcıdaki Transact-SQL deyimleri yürütülerek yapılandırılabilir. Azure portal kullanarak çoğaltmayı yapılandıramazsınız.  
- Çoğaltma, Azure SQL veritabanı 'na bağlanmak için yalnızca SQL Server kimlik doğrulaması oturum açma bilgilerini kullanabilir.
- Çoğaltılan tablolarda birincil anahtar olmalıdır.  
- Mevcut bir Azure aboneliğinizin olması gerekir.  
- Azure SQL veritabanı abonesi herhangi bir bölgede olabilir.  
- SQL Server tek bir yayını hem Azure SQL veritabanını hem de SQL Server (Şirket içi ve SQL Server bir Azure sanal makinesinde) aboneleri destekleyebilir.  
- Çoğaltma yönetimi, izleme ve sorun giderme işlemleri Azure SQL veritabanı yerine SQL Server yapılmalıdır.  
- Yalnızca `@subscriber_type = 0` SQL veritabanı için **sp_addsubscription** desteklenir.  
- Azure SQL veritabanı, çift yönlü, anında, güncelleştirilebilir veya eşler arası çoğaltmayı desteklemez.

## <a name="replication-architecture"></a>Çoğaltma mimarisi  

![Diyagramda, farklı bölgelerde birkaç abone kümesi içeren Azure SQL veritabanı ile çoğaltma mimarisi ve uzak kümelere bağlanan bir yayımcı, LogRead yürütülebiliri ve dağıtıcı yürütülebilir dosyaları içeren şirket içi Azure sanal makineleri gösterilmektedir.](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Senaryolar  

### <a name="typical-replication-scenario"></a>Tipik çoğaltma senaryosu  

1. SQL Server veritabanında bir işlem çoğaltma yayını oluşturun.  
2. SQL Server Azure SQL veritabanı 'na bir abonelik gönderimi oluşturmak için **yeni abonelik Sihirbazı 'nı** veya Transact-SQL deyimlerini kullanın.  
3. Azure SQL veritabanı 'nda tek ve havuza alınmış veritabanları sayesinde, ilk veri kümesi, anlık görüntü Aracısı tarafından oluşturulan ve Dağıtım Aracısı tarafından dağıtılan ve uygulanan bir anlık görüntüdür. SQL yönetilen örnek yayımcısı ile Azure SQL veritabanı abonesini temel almak için bir veritabanı yedeklemesi de kullanabilirsiniz.

### <a name="data-migration-scenario"></a>Veri geçiş senaryosu  

1. SQL Server veritabanından Azure SQL veritabanı 'na veri çoğaltmak için işlem çoğaltmayı kullanın.  
2. Veritabanı kopyasını güncelleştirmek için istemciyi veya orta katman uygulamaları yeniden yönlendirin.  
3. Tablonun SQL Server sürümünün güncelleştirilmesini durdurun ve yayını kaldırın.  

## <a name="limitations"></a>Sınırlamalar

Azure SQL veritabanı abonelikleri için aşağıdaki seçenekler desteklenmez:

- Dosya grupları ilişkilendirmesini Kopyala  
- Tablo bölümleme düzenlerini Kopyala  
- Dizin bölümleme düzenlerini Kopyala  
- Kullanıcı tanımlı istatistikleri kopyalama  
- Varsayılan bağlamaları Kopyala  
- Kural bağlamalarını Kopyala  
- Tam metin dizinlerini Kopyala  
- XML XSD 'yi Kopyala  
- XML dizinlerini Kopyala  
- İzinleri Kopyala  
- Uzamsal dizinleri Kopyala  
- Filtrelenmiş dizinleri Kopyala  
- Veri sıkıştırma özniteliğini Kopyala  
- Seyrek sütun özniteliğini Kopyala  
- FILESTREAM 'i MAX veri türlerine Dönüştür  
- HierarchyId 'yi MAX veri türlerine Dönüştür  
- Uzamsal değeri en fazla veri türlerine Dönüştür  
- Genişletilmiş özellikleri Kopyala  

### <a name="limitations-to-be-determined"></a>Belirlenecek sınırlamalar

- Harmanlamayı Kopyala  
- SP 'nin serileştirilmiş bir işleminde yürütme  

## <a name="examples"></a>Örnekler

Yayın ve gönderme temelli bir abonelik oluşturun. Daha fazla bilgi için bkz.
  
- [Yayın oluşturma](/sql/relational-databases/replication/publish/create-a-publication)
- Abone olarak sunucu adını (örneğin, **Nazurestodbdns. Database. Windows. net '**) ve Azure SQL veritabanı adını hedef veritabanı (örneğin **AdventureWorks**) olarak kullanarak [bir anında iletme aboneliği oluşturun](/sql/relational-databases/replication/create-a-push-subscription/) .  

## <a name="see-also"></a>Ayrıca Bkz.  

- [İşlem çoğaltması](../managed-instance/replication-transactional-overview.md)
- [Yayın oluşturma](/sql/relational-databases/replication/publish/create-a-publication)
- [Itme aboneliği oluşturma](/sql/relational-databases/replication/create-a-push-subscription/)
- [Çoğaltma Türleri](/sql/relational-databases/replication/types-of-replication)
- [İzleme (çoğaltma)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Abonelik başlatma](/sql/relational-databases/replication/initialize-a-subscription)