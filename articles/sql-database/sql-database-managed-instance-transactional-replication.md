---
title: İşlem çoğaltması
description: Azure SQL veritabanı 'nda tek, havuza alınmış ve örnek veritabanlarıyla SQL Server işlemsel çoğaltma kullanma hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: a57d1c85384204c26e75f7138b9514f2b3297bef
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823303"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Azure SQL veritabanı 'nda tek, havuza alınmış ve örnek veritabanlarıyla işlem çoğaltması

İşlem çoğaltma, Azure SQL veritabanı 'nın bir özelliğidir ve Azure SQL veritabanı 'ndaki bir tablodan veya bir SQL Server uzak veritabanlarına yerleştirilmiş tablolara veri çoğaltmanıza olanak tanıyan SQL Server. Bu özellik, farklı veritabanlarındaki birden çok tabloyu eşitlemenize olanak tanır.

## <a name="when-to-use-transactional-replication"></a>Işlemsel çoğaltma ne zaman kullanılır?

İşlemsel çoğaltma, aşağıdaki senaryolarda faydalıdır:
- Veritabanındaki bir veya daha fazla tabloda yapılan değişiklikleri yayımlayın ve bunları, değişiklikler için abone olunan bir veya daha fazla SQL Server veya Azure SQL veritabanı 'na dağıtın.
- Birkaç dağıtılmış veritabanını eşitlenmiş durumda tutun.
- Değişiklikleri sürekli olarak yayımlayarak bir SQL Server veya yönetilen örnekten veritabanlarını başka bir veritabanına geçirin.

## <a name="overview"></a>Genel Bakış

İşlem çoğaltmadaki anahtar bileşenler aşağıdaki resimde gösterilmiştir:  

![SQL veritabanı ile çoğaltma](media/replication-to-sql-database/replication-to-sql-database.png)

**Yayımcı** , güncelleştirmeleri dağıtıcıya göndererek bazı tablolarda (makaleler) yapılan değişiklikleri yayımlayan bir örnek veya sunucusudur. Şirket içi SQL Server herhangi bir Azure SQL veritabanına yayımlama, aşağıdaki SQL Server sürümleri tarafından desteklenir:

- SQL Server 2019 (Önizleme)
- SQL Server 2016 SQL 2017
- SQL Server 2014 SP1 CU3 veya üzeri (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 veya üzeri (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Azure 'da nesnelere yayımlamayı desteklemeyen diğer SQL Server sürümleri için, verileri SQL Server yeni sürümlerine taşımak üzere yeniden [Yayımlama verileri](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) yöntemini kullanmak mümkündür. 

**Dağıtıcı** , bir yayımcının makalelerindeki değişiklikleri toplayan ve bunları abonelere dağıtan bir örnek veya sunucusudur. Dağıtıcı, Azure SQL veritabanı yönetilen örneği veya SQL Server (yayımcı sürümüne eşit veya daha yüksek bir sürüm) olabilir. 

**Abone** , yayımcı üzerinde yapılan değişiklikleri alan bir örnek veya sunucusudur. Aboneler Azure SQL veritabanı veya SQL Server veritabanlarında tek, havuza alınmış ve örnek veritabanları olabilir. Tek veya havuza alınmış bir veritabanındaki bir abone, push abonesi olarak yapılandırılmalıdır. 

| Rol | Tek ve havuza alınmış veritabanları | Örnek veritabanları |
| :----| :------------- | :--------------- |
| **Yayımcı** | Hayır | Evet | 
| **Dağıtım** | Hayır | Evet|
| **Çekme abonesi** | Hayır | Evet|
| **İtme abonesi**| Evet | Evet|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Dağıtıcı bir örnek veritabanı olduğunda ve abone olmadığında bir çekme aboneliği desteklenmez. 

Farklı [çoğaltma türleri](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)vardır:


| Çoğaltma | Tek ve havuza alınmış veritabanları | Örnek veritabanları|
| :----| :------------- | :--------------- |
| [**Standart Işlem**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Evet (yalnızca abone olarak) | Evet | 
| [**Görüntüye**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Evet (yalnızca abone olarak) | Evet|
| [**Birleştirme çoğaltması**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Hayır | Hayır|
| [**Eşler arası**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Hayır | Hayır|
| [**Çift yönlü**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Hayır | Evet|
| [**Güncelleştirilebilir abonelikler**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Hayır | Hayır|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Daha eski bir sürümü kullanarak çoğaltmayı yapılandırmaya çalışmak, hata numarası MSSQL_REPL20084 (işlem aboneye bağlanamaz.) ve MSSQ_REPL40532 (oturum açma tarafından istenen > sunucu \<adı açılamıyor. Oturum açılamadı.)
  > - Azure SQL veritabanı 'nın tüm özelliklerini kullanmak için, en son [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ve [SQL Server veri araçları (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)sürümlerini kullanmanız gerekir.
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Örnek veritabanları ve şirket içi sistemler için Supportability matrisi
  Örnek veritabanları için çoğaltma desteklenebilirlik matrisi, şirket içi SQL Server için olan ile aynıdır. 
  
  | **Yayımcı**   | **Dağıtım** | **Abonenin** |
| :------------   | :-------------- | :------------- |
| SQL Server 2017 | SQL Server 2017 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Gereksinimler

- Bağlantı, çoğaltma katılımcıları arasında SQL Kimlik Doğrulaması kullanır. 
- Çoğaltma tarafından kullanılan çalışma dizini için bir Azure depolama hesabı payı. 
- Azure dosya paylaşımının erişebilmesi için, yönetilen örnek alt ağının güvenlik kurallarında bağlantı noktası 445 (TCP Giden) açık olması gerekir. 
- Yayımcı/dağıtıcı yönetilen bir örnekte ise ve abone şirket içi ise, bağlantı noktası 1433 (TCP Giden) açılması gerekir.


>[!NOTE]
> - Dağıtıcı bir örnek veritabanı olduğunda ve abone şirket içinde olduğunda giden ağ güvenlik grubu (NSG) bağlantı noktası 445 ' i engellenirse, bir Azure depolama dosyasına bağlanırken 53 hatasıyla karşılaşabilirsiniz. Bu sorunu çözmek için [vNet NSG 'Yi güncelleştirin](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) . 
> - Yönetilen bir örnekteki yayımcı ve dağıtıcı veritabanları [otomatik yük devretme grupları](sql-database-auto-failover-group.md)kullanıyorsa, yönetilen örnek yöneticisinin [eski birincil üzerindeki tüm yayınları silmesi ve yük devretme gerçekleştikten sonra yeni birincil üzerinde yeniden yapılandırması](sql-database-managed-instance-transact-sql-information.md#replication)gerekir.

### <a name="compare-data-sync-with-transactional-replication"></a>Veri eşitlemesini Işlemsel çoğaltma ile karşılaştırın

| | Data Sync | İşlem Çoğaltması |
|---|---|---|
| Üstünlü | -Etkin-etkin destek<br/>-Şirket içi ve Azure SQL veritabanı arasında çift yönlü | -Düşük gecikme süresi<br/>-İşlemsel tutarlılık<br/>-Geçişten sonra var olan topolojiyi yeniden kullan |
| Olumsuz | -5 dk veya daha fazla gecikme<br/>-İşlem tutarlılığı yok<br/>-Daha yüksek performans etkisi | -Azure SQL veritabanı tek veritabanı veya havuza alınmış veritabanından yayımlanamıyor<br/>-Yüksek bakım maliyeti |
| | | |

## <a name="common-configurations"></a>Ortak yapılandırma

Genellikle, yayımcı ve dağıtıcı bulutta ya da şirket içinde olmalıdır. Aşağıdaki konfigürasyonlar desteklenir: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Yönetilen bir örnek üzerinde yerel dağıtıcıya sahip Yayımcı

![Yayımcı ve dağıtıcı olarak tek örnek](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Yayımcı ve dağıtıcı tek bir yönetilen örnek içinde yapılandırılır ve değişiklikleri diğer yönetilen örneğe, tek veritabanına, havuza alınmış veritabanına veya şirket içi SQL Server dağıtmaya dağıtılır. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Yönetilen bir örnek üzerinde uzak dağıtıcıya sahip Yayımcı

Bu yapılandırmada, bir yönetilen örnek, çok sayıda kaynak yönetilen örneğe yönelik başka bir yönetilen örneğe yerleştirilmiş ve değişiklikleri yönetilen örnek, tek bir veritabanı, havuza alınmış veritabanı veya SQL Server.

![Yayımcı ve dağıtıcı için ayrı örnekler](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Yayımcı ve dağıtıcı iki yönetilen örnek üzerinde yapılandırılır. Bu yapılandırmayla bazı kısıtlamalar vardır: 

- Her iki yönetilen örnek de aynı vNet üzerinde.
- Her iki yönetilen örnek de aynı konumdadır.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Tek, havuza alınmış ve örnek veritabanında bir abone ile şirket içi yayımcı ve dağıtıcı 

![Abone olarak Azure SQL DB](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
Bu yapılandırmada, bir Azure SQL veritabanı (tek, havuza alınmış ve örnek veritabanı) bir abone olur. Bu yapılandırma Şirket içinden Azure 'a geçişi destekler. Abone tek veya havuza alınmış bir veritabanı üzerinde ise, gönderim modunda olmalıdır.  


## <a name="next-steps"></a>Sonraki adımlar

1. [İki yönetilen örnek arasında çoğaltmayı yapılandırın](replication-with-sql-database-managed-instance.md). 
1. [Bir yayın oluşturun](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. Abone olarak Azure SQL veritabanı sunucu adını (örneğin, `N'azuresqldbdns.database.windows.net` ve hedef veritabanı olarak Azure SQL veritabanı adını (örneğin **AdventureWorks**) kullanarak [bir anında iletme aboneliği oluşturun](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) . )
1. [Yönetilen bir örnek Için işlemsel çoğaltmanın sınırlamaları](sql-database-managed-instance-transact-sql-information.md#replication) hakkında bilgi edinin



## <a name="see-also"></a>Ayrıca Bkz.  

- [Mı ve bir yük devretme grubuyla çoğaltma](sql-database-managed-instance-transact-sql-information.md#replication)
- [SQL Veritabanına Çoğaltma](replication-to-sql-database.md)
- [Yönetilen örneğe çoğaltma](replication-with-sql-database-managed-instance.md)
- [Yayın oluşturma](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Itme aboneliği oluşturma](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Çoğaltma Türleri](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [İzleme (çoğaltma)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Abonelik başlatma](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
