---
title: Kaynakları başka bir bölgeye taşıma
description: Azure SQL veritabanınızı, Azure SQL elastik havuzunu veya Azure SQL yönetilen örneğinizi başka bir bölgeye taşımayı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821425"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Azure SQL kaynaklarını başka bir bölgeye taşıma

Bu makalede, Azure SQL veritabanı tek veritabanı, esnek havuz ve yönetilen örneğinizi yeni bir bölgeye taşıma hakkında genel bir iş akışı öğretilir. 

## <a name="overview"></a>Genel Bakış

Mevcut Azure SQL kaynaklarınızı bir bölgeden diğerine taşımak istediğiniz çeşitli senaryolar vardır. Örneğin, işinizi yeni bir bölgeye genişlettikten sonra yeni müşteri tabanı için en iyileştirmek istiyorsunuz. Ya da uyumluluk nedenleriyle işlemleri farklı bir bölgeye taşımanız gerekir. Veya Azure, daha iyi bir yakınlık sağlayan ve müşteri deneyimini geliştiren yepyeni bir bölge yayımlamıştır.  

Bu makale, kaynakları farklı bir bölgeye taşımak için genel bir iş akışı sağlar. İş akışı aşağıdaki adımlardan oluşur: 

- Taşıma için önkoşulları doğrulama 
- Kapsamdaki kaynakları taşımaya hazırlanma
- Hazırlama işlemini izleme
- Taşıma işlemini test etme
- Gerçek taşımayı başlatın 
- Kaynakları kaynak bölgesinden kaldır 


> [!NOTE]
> Bu makale, Azure genel bulutundaki veya aynı sogeign bulutu içindeki geçişler için geçerlidir. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Tek veritabanını taşı

### <a name="verify-prerequisites"></a>Önkoşulları doğrulama 

1. Her kaynak sunucu için bir hedef mantıksal sunucu oluşturun. 
1. [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)kullanarak doğru özel durumlarla güvenlik duvarını yapılandırın.  
1. Mantıksal sunucuları doğru oturum açma bilgileriyle yapılandırın. Abonelik Yöneticisi veya SQL Server yöneticisi değilseniz, ihtiyaç duyduğunuz izinleri atamak için yöneticiyle birlikte çalışın. Daha fazla bilgi için bkz. [olağanüstü durum kurtarma sonrasında Azure SQL veritabanı güvenliğini yönetme](sql-database-geo-replication-security-config.md). 
1. Veritabanlarınız TDE ile şifrelenirse ve Azure Anahtar Kasası 'nda kendi şifreleme anahtarınızı kullanıyorsa, hedef bölgelerde doğru şifreleme malzemesinin sağlandığından emin olun. Daha fazla bilgi için, bkz. [Azure SQL Saydam Veri Şifrelemesi Azure Key Vault müşteri tarafından yönetilen anahtarlar](transparent-data-encryption-byok-azure-sql.md)
1. Veritabanı düzeyinde Denetim etkinse, devre dışı bırakın ve bunun yerine sunucu düzeyinde denetimi etkinleştirin. Yük devretmeden sonra, veritabanı düzeyinde denetim, taşıma sonrasında istenen veya mümkün olmayan bölgeler arası trafiğe gerek duyar. 
1. Sunucu düzeyinde denetimler için şunları doğrulayın:
   - Depolama kapsayıcısı, Log Analytics veya var olan denetim günlükleriyle Olay Hub 'ı hedef bölgeye taşınır. 
   - Denetim, hedef sunucuda yapılandırılır. Daha fazla bilgi için bkz. [SQL veritabanı denetimini kullanmaya başlama](sql-database-auditing.md). 
1. Örneğinizde uzun süreli bir bekletme ilkesi varsa (LTR), mevcut LTR yedeklemeleri geçerli sunucu ile ilişkili olarak kalır. Hedef sunucu farklı olduğundan, kaynak sunucu ' da sunucu silinmiş olsa bile eski LTR yedeklemelere kaynak sunucuyu kullanarak erişebileceksiniz. 

  > [!NOTE]
  > Bu, bağımsız bulutu ve bir ortak bölge arasında geçiş yapmak için yeterli olacaktır. Bu tür bir geçiş, LTR yedeklemelerin Şu anda desteklenmeyen hedef sunucuya taşınmasını gerektirir. 

### <a name="prepare-resources"></a>Kaynakları hazırlama

1. Kaynağın mantıksal sunucusu arasında hedefin mantıksal sunucusu arasında bir [Yük devretme grubu](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) oluşturun.  
1. Yük devretme grubuna taşımak istediğiniz veritabanlarını ekleyin. 
    - Tüm eklenen veritabanlarının çoğaltılması otomatik olarak başlatılacak. Daha fazla bilgi için bkz. [tek veritabanlarıyla yük devretme grupları kullanmak Için en iyi uygulamalar](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 
 
### <a name="monitor-the-preparation-process"></a>Hazırlama işlemini izleme

Veritabanlarınızı kaynaktan hedefe çoğaltmayı izlemek için, [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) öğesini düzenli aralıklarla çağırabilirsiniz. `Get-AzSqlDatabaseFailoverGroup` çıkış nesnesi **replicationstate**için bir özellik içerir: 
   - **Replicationstate = 2** (CATCH_UP), veritabanının eşitlendiğini ve güvenle yük devretmekte olduğunu gösterir. 
   - **Replicationstate = 0** (dengeli dağıtım), veritabanının henüz çalıştırılmadığını ve yük devretme denemesinin başarısız olacağını belirtir. 

### <a name="test-synchronization"></a>Test eşitlemesi

**Replicationstate** `2`olduktan sonra, ikincil uç nokta `<fog-name>.secondary.database.windows.net` kullanarak her bir veritabanına veya veritabanlarının alt kümesine bağlanın ve bağlantı, uygun güvenlik yapılandırması ve veri çoğaltmayı sağlamak için veritabanlarına yönelik sorgu gerçekleştirin. 

### <a name="initiate-the-move"></a>Taşımayı Başlat

1. İkincil uç nokta `<fog-name>.secondary.database.windows.net`kullanarak hedef sunucuya bağlanın.
1. İkincil yönetilen örneği tam eşitlemeyle birincil olacak şekilde değiştirmek için [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) komutunu kullanın. Bu işlem başarılı olur ya da geri alınacaktır. 
1. DNS CNAME girişinin hedef bölgenin IP adresine işaret ettiğini belirlemek için `nslook up <fog-name>.secondary.database.windows.net` kullanarak komutun başarıyla tamamlandığını doğrulayın. Switch komutu başarısız olursa CNAME güncellenmez. 

### <a name="remove-the-source-databases"></a>Kaynak veritabanlarını kaldırma

Taşıma tamamlandıktan sonra gereksiz ücretlerden kaçınmak için kaynak bölgedeki kaynakları kaldırın. 

1. [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)kullanarak yük devretme grubunu silin. 
1. Kaynak sunucudaki veritabanlarının her biri için [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) kullanarak her bir kaynak veritabanını silin. Bu, coğrafi çoğaltma bağlantılarını otomatik olarak sonlandırır. 
1. [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)kullanarak kaynak sunucuyu silin. 
1. Anahtar kasasını, denetim depolama kapsayıcıları, Olay Hub 'ı, AAD örneği ve diğer bağımlı kaynakları, bunlar için faturalandırılmaya izin verecek şekilde kaldırın. 

## <a name="move-elastic-pools"></a>Elastik havuzları taşı

### <a name="verify-prerequisites"></a>Önkoşulları doğrulama 

1. Her kaynak sunucu için bir hedef mantıksal sunucu oluşturun. 
1. [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)kullanarak doğru özel durumlarla güvenlik duvarını yapılandırın. 
1. Mantıksal sunucuları doğru oturum açma bilgileriyle yapılandırın. Abonelik Yöneticisi veya SQL Server yöneticisi değilseniz, ihtiyaç duyduğunuz izinleri atamak için yöneticiyle birlikte çalışın. Daha fazla bilgi için bkz. [olağanüstü durum kurtarma sonrasında Azure SQL veritabanı güvenliğini yönetme](sql-database-geo-replication-security-config.md). 
1. Veritabanlarınız TDE ile şifrelenirse ve Azure Anahtar Kasası 'nda kendi şifreleme anahtarınızı kullanıyorsa, hedef bölgede doğru şifreleme malzemesinin sağlandığından emin olun.
1. Her bir kaynak elastik havuz için bir hedef elastik havuz oluşturun, bu havuzun aynı ada ve aynı boyuta sahip aynı hizmet katmanında oluşturulduğundan emin olun. 
1. Veritabanı düzeyinde bir Denetim etkinse, devre dışı bırakın ve bunun yerine sunucu düzeyinde denetimi etkinleştirin. Yük devretmeden sonra, veritabanı düzeyinde denetim, istenen veya taşımadan sonra mümkün olmayan bölgeler arası trafiğe gerek duyar. 
1. Sunucu düzeyinde denetimler için şunları doğrulayın:
    - Depolama kapsayıcısı, Log Analytics veya var olan denetim günlükleriyle Olay Hub 'ı hedef bölgeye taşınır.
    - Denetim yapılandırması hedef sunucuda yapılandırıldı. Daha fazla bilgi için bkz. [SQL veritabanı denetimi](sql-database-auditing.md).
1. Örneğinizde uzun süreli bir bekletme ilkesi varsa (LTR), mevcut LTR yedeklemeleri geçerli sunucu ile ilişkili olarak kalır. Hedef sunucu farklı olduğundan, kaynak sunucu ' da sunucu silinmiş olsa bile eski LTR yedeklemelere kaynak sunucuyu kullanarak erişebileceksiniz. 

  > [!NOTE]
  > Bu, bağımsız bulutu ve bir ortak bölge arasında geçiş yapmak için yeterli olacaktır. Bu tür bir geçiş, LTR yedeklemelerin Şu anda desteklenmeyen hedef sunucuya taşınmasını gerektirir. 

### <a name="prepare-to-move"></a>Taşımaya hazırlanma
 
1.  Kaynak mantıksal sunucusundaki her elastik havuz ve hedef sunucudaki karşılık gelen elastik havuzu arasında ayrı bir [Yük devretme grubu](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) oluşturun. 
1.  Havuzdaki tüm veritabanlarını yük devretme grubuna ekleyin. 
    - Eklenen veritabanlarının çoğaltılması otomatik olarak başlatılacak. Daha fazla bilgi için bkz. [elastik havuzlarla yük devretme grupları için en iyi uygulamalar](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > Birden çok elastik havuz içeren bir yük devretme grubu oluşturmak mümkün olsa da, her havuz için ayrı bir yük devretme grubu oluşturmanızı kesinlikle öneririz. Taşımanız gereken birden çok elastik havuzda çok sayıda veritabanınız varsa, hazırlık adımlarını paralel olarak çalıştırabilir ve ardından taşıma adımını paralel olarak başlatabilirsiniz. Bu işlem daha iyi ölçeklenecektir ve aynı yük devretme grubunda birden çok elastik havuza sahip olmaya kıyasla daha az zaman alır. 

### <a name="monitor-the-preparation-process"></a>Hazırlama işlemini izleme

Veritabanlarınızı kaynaktan hedefe çoğaltmayı izlemek için, [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) öğesini düzenli aralıklarla çağırabilirsiniz. `Get-AzSqlDatabaseFailoverGroup` çıkış nesnesi **replicationstate**için bir özellik içerir: 
   - **Replicationstate = 2** (CATCH_UP), veritabanının eşitlendiğini ve güvenle yük devretmekte olduğunu gösterir. 
   - **Replicationstate = 0** (dengeli dağıtım), veritabanının henüz çalıştırılmadığını ve yük devretme denemesinin başarısız olacağını belirtir. 

### <a name="test-synchronization"></a>Test eşitlemesi
 
**Replicationstate** `2`olduktan sonra, ikincil uç nokta `<fog-name>.secondary.database.windows.net` kullanarak her bir veritabanına veya veritabanlarının alt kümesine bağlanın ve bağlantı, uygun güvenlik yapılandırması ve veri çoğaltmayı sağlamak için veritabanlarına yönelik sorgu gerçekleştirin. 

### <a name="initiate-the-move"></a>Taşımayı Başlat
 
1. İkincil uç nokta `<fog-name>.secondary.database.windows.net`kullanarak hedef sunucuya bağlanın.
1. İkincil yönetilen örneği tam eşitlemeyle birincil olacak şekilde değiştirmek için [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) komutunu kullanın. Bu işlem başarılı olur ya da geri alınacaktır. 
1. DNS CNAME girişinin hedef bölgenin IP adresine işaret ettiğini belirlemek için `nslook up <fog-name>.secondary.database.windows.net` kullanarak komutun başarıyla tamamlandığını doğrulayın. Switch komutu başarısız olursa CNAME güncellenmez. 

### <a name="remove-the-source-elastic-pools"></a>Kaynak elastik havuzları kaldırma
 
Taşıma tamamlandıktan sonra gereksiz ücretlerden kaçınmak için kaynak bölgedeki kaynakları kaldırın. 

1. [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)kullanarak yük devretme grubunu silin.
1. [Remove-Azsqtalaçıkartma havuzunu](/powershell/module/az.sql/remove-azsqlelasticpool)kullanarak kaynak sunucudaki her bir kaynak elastik havuzu silin. 
1. [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)kullanarak kaynak sunucuyu silin. 
1. Anahtar kasasını, denetim depolama kapsayıcıları, Olay Hub 'ı, AAD örneği ve diğer bağımlı kaynakları, bunlar için faturalandırılmaya izin verecek şekilde kaldırın. 

## <a name="move-managed-instance"></a>Yönetilen örneği taşı

### <a name="verify-prerequisites"></a>Önkoşulları doğrulama
 
1. Her kaynak yönetilen örnek için, hedef bölgede aynı boyutta bir hedef yönetilen örnek oluşturun.  
1. Ağı yönetilen bir örnek için yapılandırın. Daha fazla bilgi için bkz. [ağ yapılandırması](sql-database-howto-managed-instance.md#network-configuration).
1. Hedef ana veritabanını doğru oturum açma bilgileriyle yapılandırın. Abonelik Yöneticisi veya SQL Server yöneticisi değilseniz, ihtiyaç duyduğunuz izinleri atamak için yöneticiyle birlikte çalışın. 
1. Veritabanlarınız TDE ile şifrelenirse ve Azure Anahtar Kasası 'nda kendi şifreleme anahtarınızı kullanıyorsa, aynı şifreleme anahtarlarına sahip AKV 'nin hem kaynak hem de hedef bölgelerde bulunduğundan emin olun. Daha fazla bilgi için bkz. [Azure Key Vault içindeki müşteri tarafından yönetilen anahtarlarla TDE](transparent-data-encryption-byok-azure-sql.md).
1. Örnek için denetim etkinleştirildiyse şunları doğrulayın:
    - Depolama kapsayıcısı veya mevcut günlüklere sahip olay hub 'ı hedef bölgeye taşınır. 
    - Denetim, hedef örnekte yapılandırıldı. Daha fazla bilgi için bkz. [yönetilen örnekle denetleme](sql-database-managed-instance-auditing.md).
1. Örneğinizde uzun süreli bir bekletme ilkesi varsa (LTR), mevcut LTR yedeklemeleri geçerli sunucu ile ilişkili olarak kalır. Hedef sunucu farklı olduğundan, kaynak sunucu ' da sunucu silinmiş olsa bile eski LTR yedeklemelere kaynak sunucuyu kullanarak erişebileceksiniz. 

  > [!NOTE]
  > Bu, bağımsız bulutu ve bir ortak bölge arasında geçiş yapmak için yeterli olacaktır. Bu tür bir geçiş, LTR yedeklemelerin Şu anda desteklenmeyen hedef sunucuya taşınmasını gerektirir. 

### <a name="prepare-resources"></a>Kaynakları hazırlama

Her kaynak örneği ve ilgili hedef örneği arasında bir yük devretme grubu oluşturun.
    - Her örnekteki tüm veritabanlarının çoğaltılması otomatik olarak başlatılacak. Daha fazla bilgi için bkz. [otomatik yük devretme grupları](sql-database-auto-failover-group.md) .

 
### <a name="monitor-the-preparation-process"></a>Hazırlama işlemini izleme

Veritabanlarınızı kaynaktan hedefe çoğaltmayı izlemek için, [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) öğesini düzenli aralıklarla çağırabilirsiniz. `Get-AzSqlDatabaseFailoverGroup` çıkış nesnesi **replicationstate**için bir özellik içerir: 
   - **Replicationstate = 2** (CATCH_UP), veritabanının eşitlendiğini ve güvenle yük devretmekte olduğunu gösterir. 
   - **Replicationstate = 0** (dengeli dağıtım), veritabanının henüz çalıştırılmadığını ve yük devretme denemesinin başarısız olacağını belirtir. 

### <a name="test-synchronization"></a>Test eşitlemesi

**Replicationstate** `2`olduktan sonra, ikincil uç nokta `<fog-name>.secondary.database.windows.net` kullanarak her bir veritabanına veya veritabanlarının alt kümesine bağlanın ve bağlantı, uygun güvenlik yapılandırması ve veri çoğaltmayı sağlamak için veritabanlarına yönelik sorgu gerçekleştirin. 

### <a name="initiate-the-move"></a>Taşımayı Başlat 

1. İkincil uç nokta `<fog-name>.secondary.database.windows.net`kullanarak hedef sunucuya bağlanın.
1. İkincil yönetilen örneği tam eşitlemeyle birincil olacak şekilde değiştirmek için [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) komutunu kullanın. Bu işlem başarılı olur ya da geri alınacaktır. 
1. DNS CNAME girişinin hedef bölgenin IP adresine işaret ettiğini belirlemek için `nslook up <fog-name>.secondary.database.windows.net` kullanarak komutun başarıyla tamamlandığını doğrulayın. Switch komutu başarısız olursa CNAME güncellenmez. 

### <a name="remove-the-source-managed-instances"></a>Kaynak yönetilen örnekleri kaldır
Taşıma tamamlandıktan sonra gereksiz ücretlerden kaçınmak için kaynak bölgedeki kaynakları kaldırın. 

1. [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)kullanarak yük devretme grubunu silin. Bu, yük devretme grubu yapılandırmasını bırakacak ve iki örnek arasında coğrafi çoğaltma bağlantılarını sonlandıracaktır. 
1. [Remove-Azsqlınstance](/powershell/module/az.sql/remove-azsqlinstance)kullanarak kaynak yönetilen örneği silin. 
1. Kaynak grubundaki sanal küme, sanal ağ ve güvenlik grubu gibi ek kaynakları kaldırın. 

## <a name="next-steps"></a>Sonraki adımlar 

Geçiş yapıldıktan sonra Azure SQL veritabanınızı [yönetin](sql-database-manage-after-migration.md) . 

