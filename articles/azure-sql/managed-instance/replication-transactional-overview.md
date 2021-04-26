---
title: İşlem çoğaltması
titleSuffix: Azure SQL Managed Instance
description: Azure SQL yönetilen örneği (Önizleme) ile SQL Server işlemsel çoğaltma kullanma hakkında bilgi edinin.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 04/20/2020
ms.openlocfilehash: e08fe67dece02b936aa3a22e9cac58d809f19f46
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285692"
---
# <a name="transactional-replication-with-azure-sql-managed-instance-preview"></a>Azure SQL yönetilen örneği (Önizleme) ile işlemsel çoğaltma
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

İşlem çoğaltma, Azure SQL yönetilen örneği 'nin bir özelliğidir ve Azure SQL yönetilen örneğindeki bir tablodan veya bir SQL Server örneğindeki verileri uzak veritabanlarına yerleştirilmiş tablolara çoğaltmanıza olanak tanıyan SQL Server. Bu özellik, farklı veritabanlarındaki birden çok tabloyu eşitlemenize olanak tanır. 

İşlemsel çoğaltma şu anda SQL yönetilen örneği için genel önizlemededir. 

## <a name="overview"></a>Genel Bakış

Azure SQL yönetilen örneğinde yapılan değişiklikleri şu şekilde göndermek için işlem çoğaltmayı kullanabilirsiniz:

- Şirket içinde veya Azure VM 'de SQL Server veritabanı
- Azure SQL veritabanı 'nda bir veritabanı
- Azure SQL yönetilen örneği 'nde örnek veritabanı

  > [!NOTE]
  > Azure SQL yönetilen örneği 'nin tüm özelliklerini kullanmak için [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) ve [SQL Server veri araçları (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)en son sürümlerini kullanmanız gerekir.

### <a name="components"></a>Bileşenler

Aşağıdaki resimde gösterildiği gibi, işlemsel çoğaltma 'daki anahtar bileşenleri **Yayımcı**, **dağıtıcı** ve **abone**' dir:  

![SQL veritabanı ile çoğaltma](./media/replication-transactional-overview/replication-to-sql-database.png)

| Rol | Azure SQL Veritabanı | Azure SQL Yönetilen Örnek |
| :----| :------------- | :--------------- |
| **Publisher** | Hayır | Yes |
| **Dağıtım** | Hayır | Yes|
| **Çekme abonesi** | Hayır | Yes|
| **İtme abonesi**| Yes | Yes|
| &nbsp; | &nbsp; | &nbsp; |

**Yayımcı** , güncelleştirmeleri dağıtıcıya göndererek bazı tablolarda (makaleler) yapılan değişiklikleri yayımlar. Yayımcı bir Azure SQL yönetilen örneği veya bir SQL Server örneği olabilir.

**Dağıtıcı** , bir yayımcının makalelerindeki değişiklikleri toplar ve bunları abonelere dağıtır. Dağıtıcı bir Azure SQL yönetilen örneği ya da bir SQL Server örneği (yayımcı sürümüne eşit veya daha yüksek bir sürüm) olabilir.

**Abone** , yayımcı üzerinde yapılan değişiklikleri alır. Bir SQL Server örneği ve Azure SQL yönetilen örneği hem itme hem de çekme aboneleri olabilir, ancak dağıtıcı bir Azure SQL yönetilen örneği olduğunda ve abone olmadığında bir çekme aboneliği desteklenmez. Azure SQL veritabanındaki bir veritabanı yalnızca bir itme abonesi olabilir.

Azure SQL yönetilen örneği, aşağıdaki SQL Server sürümlerinden abone olmak için destek alabilir:

- SQL Server 2016 ve üzeri
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) veya [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) veya [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - Azure 'da nesnelere yayımlamayı desteklemeyen diğer SQL Server sürümleri için, verileri SQL Server yeni sürümlerine taşımak üzere yeniden [Yayımlama verileri](/sql/relational-databases/replication/republish-data) yöntemini kullanmak mümkündür.
   > - Daha eski bir sürümü kullanarak çoğaltmayı yapılandırma denemesi, hata numarası MSSQL_REPL20084 (işlem aboneye bağlanamaz.) ve MSSQ_REPL40532 ( \<name> oturum açma tarafından istenen sunucu açılamıyor. Oturum açılamadı.)

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

### <a name="supportability-matrix"></a>Desteklenebilirlik matrisi

  Azure SQL yönetilen örneği için işlemsel çoğaltma desteklenebilirliği, SQL Server ile aynıdır.
  
| **Publisher**   | **Dağıtım** | **Abone** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>Kullanılması gereken durumlar

İşlemsel çoğaltma, aşağıdaki senaryolarda faydalıdır:

- Veritabanındaki bir veya daha fazla tabloda yapılan değişiklikleri yayımlayın ve bunları, değişiklikler için abone olunan bir SQL Server örneğindeki veya Azure SQL veritabanındaki bir veya daha fazla veritabanına dağıtın.
- Birkaç dağıtılmış veritabanını eşitlenmiş durumda tutun.
- Değişiklikleri sürekli olarak yayımlayarak bir SQL Server örneğinden veya Azure SQL yönetilen örneğinden veritabanlarını başka bir veritabanına geçirin.

### <a name="compare-data-sync-with-transactional-replication"></a>Veri eşitlemesini Işlemsel çoğaltma ile karşılaştırın

| Kategori | Data Sync | İşlem Çoğaltması |
|---|---|---|
| Avantajlar | -Etkin-etkin destek<br/>-Şirket içi ve Azure SQL veritabanı arasında çift yönlü | -Düşük gecikme süresi<br/>-İşlemsel tutarlılık<br/>-Geçişten sonra var olan topolojiyi yeniden kullan |
| Dezavantajlar | -İşlem tutarlılığı yok<br/>-Daha yüksek performans etkisi | -Azure SQL veritabanından yayımlanamıyor <br/>-Yüksek bakım maliyeti |

## <a name="common-configurations"></a>Ortak yapılandırma

Genellikle, yayımcı ve dağıtıcı bulutta ya da şirket içinde olmalıdır. Aşağıdaki yapılandırmalar desteklenir:

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>SQL yönetilen örneği üzerinde yerel dağıtıcıya sahip Yayımcı

![Yayımcı ve dağıtıcı olarak tek örnek](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

Yayımcı ve dağıtıcı tek bir SQL yönetilen örneği içinde yapılandırılır ve değişiklikleri başka bir SQL yönetilen örneği, SQL veritabanı veya SQL Server örneğine dağıtmaktadır.

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>SQL yönetilen örneği üzerinde uzak dağıtıcıya sahip Yayımcı

Bu yapılandırmada, bir yönetilen örnek, çok sayıda kaynak SQL yönetilen örneğe yönelik olan ve değişiklikleri Azure SQL veritabanı, Azure SQL yönetilen örneği veya SQL Server bir veya daha fazla hedefe dağıtan başka bir SQL yönetilen örneğine yerleştirilmiş bir dağıtıcıda yayınlar.

![Yayımcı ve dağıtıcı için ayrı örnekler](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

Yayımcı ve dağıtıcı iki yönetilen örnek üzerinde yapılandırılır. Bu yapılandırmayla bazı kısıtlamalar vardır:

- Her iki yönetilen örnek de aynı vNet üzerinde.
- Her iki yönetilen örnek de aynı konumdadır.

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>Uzak abone ile şirket içi Yayımcı/dağıtıcı

![Abone olarak Azure SQL veritabanı](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

Bu yapılandırmada, Azure SQL veritabanı veya Azure SQL yönetilen örneği için bir veritabanı abone olur. Bu yapılandırma Şirket içinden Azure 'a geçişi destekler. Bir abone Azure SQL veritabanı 'nda bir veritabanı ise, gönderim modunda olmalıdır.  

## <a name="requirements"></a>Gereksinimler

- Çoğaltma katılımcıları arasında bağlantı için SQL kimlik doğrulamasını kullanın.
- Çoğaltma tarafından kullanılan çalışma dizini için bir Azure depolama hesabı paylaşma kullanın.
- Azure dosya paylaşımında erişmek için alt ağ güvenlik kurallarında TCP giden bağlantı noktası 445 ' i açın.
- SQL yönetilen örneği Yayımcı/dağıtıcı olduğunda ve abone olmadığında TCP giden bağlantı noktası 1433 ' u açın. Ayrıca, `allow_linkedserver_outbound` bağlantı noktası 1433 **hedef hizmet etıketı** Için SQL yönetilen örnek NSG giden güvenlik kuralını olarak değiştirmeniz gerekebilir `virtualnetwork` `internet` .
- Hem yayımcıyı hem de dağıtıcıyı buluta veya her ikisinde de şirket içinde yerleştirin.
- Sanal ağlar farklıysa, çoğaltma katılımcılarının sanal ağları arasındaki VPN eşlemesini yapılandırın.

> [!NOTE]
> Dağıtıcı bir Azure SQL yönetilen örnek veritabanı olduğunda ve abone şirket içinde olduğunda, giden ağ güvenlik grubu (NSG) bağlantı noktası 445 engellenirse, bir Azure depolama dosyasına bağlanırken 53 hatasıyla karşılaşabilirsiniz. Bu sorunu çözmek için [vNet NSG 'Yi güncelleştirin](../../storage/files/storage-troubleshoot-windows-file-connection-problems.md) .

## <a name="with-failover-groups"></a>Yük devretme gruplarıyla

[Etkin coğrafi çoğaltma](../database/active-geo-replication-overview.md) , işlemsel çoğaltma kullanılarak SQL yönetilen örneği ile desteklenmez. Etkin coğrafi çoğaltma yerine, [otomatik yük devretme grupları](../database/auto-failover-group-overview.md)kullanın, ancak yayının, birincil yönetilen örnekten [el ile silinmesini](transact-sql-tsql-differences-sql-server.md#replication) ve yük DEVRETMEDEN sonra ikincil SQL yönetilen örneği üzerinde yeniden oluşturulmasını unutmayın.

Bir **Yayımcı** veya **dağıtıcı** SQL yönetilen örneği bir [Yük devretme grubunda](../database/auto-failover-group-overview.md)ise, SQL yönetilen örnek yöneticisinin eski birincil üzerindeki tüm yayınları temizlemesi ve yük devretme gerçekleştikten sonra yeni birincil üzerinde yeniden yapılandırması gerekir. Bu senaryoda aşağıdaki etkinlikler gereklidir:

1. Varsa, veritabanında çalışan tüm çoğaltma işlerini durdurun.
1. Yayımcı veritabanında aşağıdaki betiği çalıştırarak, yayımcıdan abonelik meta verilerini bırakın:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. Abonelik meta verilerini aboneden bırakın. Abone SQL yönetilen örneğindeki abonelik veritabanında aşağıdaki betiği çalıştırın:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. Yayımlanan veritabanında aşağıdaki betiği çalıştırarak tüm çoğaltma nesnelerini yayımcıya zorla bırakın:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Eski dağıtıcısı 'yı özgün birincil SQL yönetilen örneğinden zorla bırakma (dağıtıcıya sahip olmak için kullanılan eski bir birincili geri yük devreder). Aşağıdaki betiği, eski dağıtıcı SQL yönetilen örneğindeki ana veritabanında çalıştırın:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Bir yük devretme grubundaki bir **abone** örneğinde coğrafi çoğaltma etkinleştirilmişse, yayın, abone yönetilen örneği için yük devretme grubu dinleyicisi uç noktasına bağlanacak şekilde yapılandırılmalıdır. Yük devretme durumunda, yönetilen örnek yöneticisinin sonraki eylemi, oluşan yük devretme türüne bağlıdır:

- Veri kaybı olmayan bir yük devretme için, çoğaltma yük devretme sonrasında çalışmaya devam edecektir.
- Veri kaybı olan bir yük devretme için çoğaltma da çalışacaktır. Kayıp değişiklikleri yeniden çoğaltacaktır.
- Veri kaybı olan bir yük devretme için, ancak veri kaybı dağıtım veritabanı saklama süresinin dışında, SQL yönetilen örnek yöneticisinin abonelik veritabanını yeniden başlatmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

İşlem çoğaltmasını yapılandırma hakkında daha fazla bilgi için aşağıdaki öğreticilere bakın:

- [SQL yönetilen örnek yayımcısı ve abonesi arasında çoğaltmayı yapılandırma](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [SQL yönetilen örnek yayımcısı, SQL yönetilen örnek dağıtıcısı ve SQL Server abonesi arasında çoğaltmayı yapılandırma](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [Bir yayın oluşturun](/sql/relational-databases/replication/publish/create-a-publication).
- [](/sql/relational-databases/replication/create-a-push-subscription) Abone olarak sunucu adını (örneğin `N'azuresqldbdns.database.windows.net` , hedef VERITABANı olarak Azure SQL veritabanı adı (örneğin **AdventureWorks**) kullanarak bir anında iletme aboneliği oluşturun. )

## <a name="see-also"></a>Ayrıca bkz.  

- [SQL yönetilen örneği ve bir yük devretme grubu ile çoğaltma](transact-sql-tsql-differences-sql-server.md#replication)
- [SQL Veritabanına Çoğaltma](../database/replication-to-sql-database.md)
- [Yönetilen örneğe çoğaltma](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Yayın oluşturma](/sql/relational-databases/replication/publish/create-a-publication)
- [Itme aboneliği oluşturma](/sql/relational-databases/replication/create-a-push-subscription/)
- [Çoğaltma Türleri](/sql/relational-databases/replication/types-of-replication)
- [İzleme (çoğaltma)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Abonelik başlatma](/sql/relational-databases/replication/initialize-a-subscription)
