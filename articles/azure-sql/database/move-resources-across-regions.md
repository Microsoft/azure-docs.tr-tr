---
title: Kaynakları yeni bölgeye taşı
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Veritabanınızı veya yönetilen örneğinizi başka bir bölgeye taşımayı öğrenin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: ae6c87c9eabea837ba9c43676d4ca712caa385cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94594173"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>Kaynakları yeni bölgeye taşı-Azure SQL veritabanı & Azure SQL yönetilen örneği
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Bu makalede, veritabanınızı veya yönetilen örneğinizi yeni bir bölgeye taşıma hakkında genel bir iş akışı öğretilir.

## <a name="overview"></a>Genel Bakış

Mevcut veritabanınızı veya yönetilen örneğinizi bir bölgeden diğerine taşımak istediğiniz çeşitli senaryolar vardır. Örneğin, işinizi yeni bir bölgeye genişletmekte ve yeni müşteri tabanında iyileştirmek isteyeceksiniz. Ya da uyumluluk nedenleriyle işlemleri farklı bir bölgeye taşımanız gerekir. Veya Azure, daha iyi bir yakınlık sağlayan ve müşteri deneyimini geliştiren yeni bir bölge yayımlamıştır.  

Bu makale, kaynakları farklı bir bölgeye taşımak için genel bir iş akışı sağlar. İş akışı aşağıdaki adımlardan oluşur:

1. Taşıma için önkoşulları doğrulayın.
1. Kapsamdaki kaynakları taşımaya hazırlanın.
1. Hazırlama işlemini izleyin.
1. Taşıma işlemini test edin.
1. Gerçek taşımayı başlatın.
1. Kaynak bölgeden kaynakları kaldırın.

> [!NOTE]
> Bu makale, Azure genel bulutundaki veya aynı bağımsız bulutu içindeki geçişler için geçerlidir.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>Veritabanını taşıma

### <a name="verify-prerequisites"></a>Önkoşulları doğrulama

1. Her kaynak sunucu için bir hedef sunucu oluşturun.
1. [PowerShell](scripts/create-and-configure-database-powershell.md)'i kullanarak, güvenlik duvarını doğru özel durumlarla yapılandırın.  
1. Sunucuları doğru oturum açmaları ile yapılandırın. Abonelik Yöneticisi veya SQL Server yöneticisi değilseniz, ihtiyaç duyduğunuz izinleri atamak için yöneticiyle birlikte çalışın. Daha fazla bilgi için bkz. [olağanüstü durum kurtarma sonrasında Azure SQL veritabanı güvenliğini yönetme](active-geo-replication-security-configure.md).
1. Veritabanları saydam veri şifrelemesi ile şifrelenirse ve Azure Key Vault ' de kendi şifreleme anahtarınızı kullanıyorsa, hedef bölgelerde doğru şifreleme malzemesinin sağlandığından emin olun. Daha fazla bilgi için, [Azure Key Vault içindeki müşteri tarafından yönetilen anahtarlarla Azure SQL saydam veri şifrelemesi](transparent-data-encryption-byok-overview.md)bölümüne bakın.
1. Veritabanı düzeyinde Denetim etkinse, devre dışı bırakın ve bunun yerine sunucu düzeyinde denetimi etkinleştirin. Yük devretmeden sonra, veritabanı düzeyinde denetim, taşıma sonrasında istenen veya mümkün olmayan bölgeler arası trafiğe gerek duyar.
1. Sunucu düzeyinde denetimler için şunları doğrulayın:
   - Depolama kapsayıcısı, Log Analytics veya var olan denetim günlükleriyle Olay Hub 'ı hedef bölgeye taşınır.
   - Denetim, hedef sunucuda yapılandırılır. Daha fazla bilgi için bkz. [SQL veritabanı denetimini kullanmaya başlama](../../azure-sql/database/auditing-overview.md).
1. Örneğinizde uzun süreli bir bekletme ilkesi varsa (LTR), mevcut LTR yedeklemeleri geçerli sunucu ile ilişkili olarak kalır. Hedef sunucu farklı olduğu için, sunucu silinmiş olsa bile kaynak sunucu kullanarak eski LTR yedeklemelerine erişebilirsiniz.

      > [!NOTE]
      > Bu, bağımsız bulutu ve bir ortak bölge arasında geçiş yapmak için yeterli olacaktır. Bu tür bir geçiş, LTR yedeklemelerin Şu anda desteklenmeyen hedef sunucuya taşınmasını gerektirir.

### <a name="prepare-resources"></a>Kaynakları hazırlama

1. Kaynak sunucu ve hedefin sunucusu arasında bir [Yük devretme grubu](failover-group-add-single-database-tutorial.md#2---create-the-failover-group) oluşturun.  
1. Yük devretme grubuna taşımak istediğiniz veritabanlarını ekleyin.
  
    Tüm eklenen veritabanlarının çoğaltılması otomatik olarak başlatılacak. Daha fazla bilgi için bkz. [tek veritabanlarıyla yük devretme grupları kullanmak Için en iyi uygulamalar](auto-failover-group-overview.md#best-practices-for-sql-database).

### <a name="monitor-the-preparation-process"></a>Hazırlama işlemini izleme

Veritabanlarınızı kaynaktan hedefe çoğaltmayı izlemek için, [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) öğesini düzenli aralıklarla çağırabilirsiniz. Çıkış nesnesi `Get-AzSqlDatabaseFailoverGroup` **replicationstate** için bir özellik içerir:

- **Replicationstate = 2** (CATCH_UP), veritabanının eşitlendiğini ve güvenle yük devretmekte olduğunu gösterir.
- **Replicationstate = 0** (dengeli dağıtım), veritabanının henüz çalıştırılmadığını ve Yük Devretme girişiminin başarısız olacağını belirtir.

### <a name="test-synchronization"></a>Test eşitlemesi

**Replicationstate** olduktan sonra `2` , `<fog-name>.secondary.database.windows.net` bağlantı, uygun güvenlik yapılandırması ve veri çoğaltmayı sağlamak için ikincil uç noktayı kullanarak her bir veritabanına veya veritabanı alt kümesine bağlanın ve veritabanlarına yönelik sorgu gerçekleştirin.

### <a name="initiate-the-move"></a>Taşımayı Başlat

1. İkincil uç noktayı kullanarak hedef sunucuya bağlanın `<fog-name>.secondary.database.windows.net` .
1. İkincil yönetilen örneği tam eşitlemeyle birincil olacak şekilde değiştirmek için [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) komutunu kullanın. Bu işlem başarılı olur veya geri alınacaktır.
1. `nslook up <fog-name>.secondary.database.windows.net`DNS CNAME girişinin hedef BÖLGENIN IP adresine işaret ettiğini belirlemek için komutunu kullanarak komutun başarıyla tamamlandığını doğrulayın. Switch komutu başarısız olursa, CNAME güncellenmez.

### <a name="remove-the-source-databases"></a>Kaynak veritabanlarını kaldırma

Taşıma tamamlandıktan sonra gereksiz ücretlerden kaçınmak için kaynak bölgedeki kaynakları kaldırın.

1. [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)kullanarak yük devretme grubunu silin.
1. Kaynak sunucudaki veritabanlarının her biri için [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) kullanarak her bir kaynak veritabanını silin. Bu, coğrafi çoğaltma bağlantılarını otomatik olarak sonlandırır.
1. [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)kullanarak kaynak sunucuyu silin.
1. Anahtar kasasını, denetim depolama kapsayıcılarını, Olay Hub 'ı, Azure Active Directory (Azure AD) örneğini ve diğer bağımlı kaynakları, bunlar için faturalandırılmaya izin verecek şekilde kaldırın.

## <a name="move-elastic-pools"></a>Elastik havuzları taşı

### <a name="verify-prerequisites"></a>Önkoşulları doğrulama

1. Her kaynak sunucu için bir hedef sunucu oluşturun.
1. [PowerShell](scripts/create-and-configure-database-powershell.md)kullanarak doğru özel durumlarla güvenlik duvarını yapılandırın.
1. Sunucuları doğru oturum açmaları ile yapılandırın. Abonelik Yöneticisi veya sunucu yöneticisi değilseniz, ihtiyaç duyduğunuz izinleri atamak için yöneticiyle birlikte çalışın. Daha fazla bilgi için bkz. [olağanüstü durum kurtarma sonrasında Azure SQL veritabanı güvenliğini yönetme](active-geo-replication-security-configure.md).
1. Veritabanları, saydam veri şifrelemesi ile şifrelendiyse ve Azure Key Vault ' de kendi şifreleme anahtarınızı kullanıyorsa, hedef bölgede doğru şifreleme malzemesinin sağlandığından emin olun.
1. Her bir kaynak elastik havuz için bir hedef elastik havuz oluşturun, bu havuzun aynı ada ve aynı boyuta sahip aynı hizmet katmanında oluşturulduğundan emin olun.
1. Veritabanı düzeyinde bir Denetim etkinse, devre dışı bırakın ve bunun yerine sunucu düzeyinde denetimi etkinleştirin. Yük devretmeden sonra, veritabanı düzeyinde denetim, istenen veya taşımadan sonra mümkün olmayan bölgeler arası trafiğe gerek duyar.
1. Sunucu düzeyinde denetimler için şunları doğrulayın:
    - Depolama kapsayıcısı, Log Analytics veya var olan denetim günlükleriyle Olay Hub 'ı hedef bölgeye taşınır.
    - Denetim yapılandırması hedef sunucuda yapılandırıldı. Daha fazla bilgi için bkz. [SQL veritabanı denetimi](../../azure-sql/database/auditing-overview.md).
1. Örneğinizde uzun süreli bir bekletme ilkesi varsa (LTR), mevcut LTR yedeklemeleri geçerli sunucu ile ilişkili olarak kalır. Hedef sunucu farklı olduğundan, sunucu silinmiş olsa da kaynak sunucu kullanarak eski LTR yedeklemelerine erişebilirsiniz.

      > [!NOTE]
      > Bu, bağımsız bulutu ve bir ortak bölge arasında geçiş yapmak için yeterli olacaktır. Bu tür bir geçiş, LTR yedeklemelerin Şu anda desteklenmeyen hedef sunucuya taşınmasını gerektirir.

### <a name="prepare-to-move"></a>Taşımaya hazırlanma

1. Kaynak sunucudaki her elastik havuz ve hedef sunucuda buna karşılık gelen elastik havuzu arasında ayrı bir [Yük devretme grubu](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group) oluşturun.
1. Havuzdaki tüm veritabanlarını yük devretme grubuna ekleyin.

    Eklenen veritabanlarının çoğaltılması otomatik olarak başlatılacak. Daha fazla bilgi için bkz. [elastik havuzlarla yük devretme grupları Için en iyi uygulamalar](auto-failover-group-overview.md#best-practices-for-sql-database).

      > [!NOTE]
      > Birden çok elastik havuz içeren bir yük devretme grubu oluşturmak mümkün olsa da, her havuz için ayrı bir yük devretme grubu oluşturmanızı kesinlikle öneririz. Taşımanız gereken birden çok elastik havuzda çok sayıda veritabanınız varsa, hazırlık adımlarını paralel olarak çalıştırabilir ve ardından taşıma adımını paralel olarak başlatabilirsiniz. Bu işlem daha iyi ölçeklenecektir ve aynı yük devretme grubunda birden çok elastik havuza sahip olmaya kıyasla daha az zaman alır.

### <a name="monitor-the-preparation-process"></a>Hazırlama işlemini izleme

Veritabanlarınızı kaynaktan hedefe çoğaltmayı izlemek için, [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) öğesini düzenli aralıklarla çağırabilirsiniz. Çıkış nesnesi `Get-AzSqlDatabaseFailoverGroup` **replicationstate** için bir özellik içerir:

- **Replicationstate = 2** (CATCH_UP), veritabanının eşitlendiğini ve güvenle yük devretmekte olduğunu gösterir.
- **Replicationstate = 0** (dengeli dağıtım), veritabanının henüz çalıştırılmadığını ve Yük Devretme girişiminin başarısız olacağını belirtir.

### <a name="test-synchronization"></a>Test eşitlemesi

**Replicationstate** olduktan sonra `2` `<fog-name>.secondary.database.windows.net` bağlantı, uygun güvenlik yapılandırması ve veri çoğaltma sağlamak için, ikincil uç noktayı kullanarak her bir veritabanına veya veritabanı alt kümesine bağlanın ve veritabanlarına yönelik sorgu gerçekleştirin.

### <a name="initiate-the-move"></a>Taşımayı Başlat

1. İkincil uç noktayı kullanarak hedef sunucuya bağlanın `<fog-name>.secondary.database.windows.net` .
1. İkincil yönetilen örneği tam eşitlemeyle birincil olacak şekilde değiştirmek için [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) komutunu kullanın. Bu işlem başarılı olur ya da geri alınacaktır.
1. `nslook up <fog-name>.secondary.database.windows.net`DNS CNAME girişinin hedef BÖLGENIN IP adresine işaret ettiğini belirlemek için komutunu kullanarak komutun başarıyla tamamlandığını doğrulayın. Switch komutu başarısız olursa, CNAME güncellenmez.

### <a name="remove-the-source-elastic-pools"></a>Kaynak elastik havuzları kaldırma

Taşıma tamamlandıktan sonra gereksiz ücretlerden kaçınmak için kaynak bölgedeki kaynakları kaldırın.

1. [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)kullanarak yük devretme grubunu silin.
1. [Remove-Azsqtalaçıkartma havuzunu](/powershell/module/az.sql/remove-azsqlelasticpool)kullanarak kaynak sunucudaki her bir kaynak elastik havuzu silin.
1. [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)kullanarak kaynak sunucuyu silin.
1. Anahtar kasasını, denetim depolama kapsayıcılarını, Olay Hub 'ı, Azure AD örneğini ve diğer bağımlı kaynakları, bunlar için faturalandırılmaya izin verecek şekilde kaldırın.

## <a name="move-a-managed-instance"></a>Yönetilen bir örneği taşıma

### <a name="verify-prerequisites"></a>Önkoşulları doğrulama

1. Her kaynak yönetilen örnek için, hedef bölgede aynı boyutta bir SQL yönetilen örneği hedef örneği oluşturun.  
1. Ağı yönetilen bir örnek için yapılandırın. Daha fazla bilgi için bkz. [ağ yapılandırması](../managed-instance/how-to-content-reference-guide.md#network-configuration).
1. Hedef ana veritabanını doğru oturum açma bilgileriyle yapılandırın. Aboneliğiniz veya SQL yönetilen örnek yöneticisi değilseniz, ihtiyaç duyduğunuz izinleri atamak için yöneticiyle birlikte çalışın.
1. Veritabanları, saydam veri şifrelemesi ile şifrelenirse ve Azure Key Vault kendi şifreleme anahtarınızı kullanıyorsa, aynı şifreleme anahtarlarına sahip Azure Key Vault hem kaynak hem de hedef bölgelerde bulunduğundan emin olun. Daha fazla bilgi için, [Azure Key Vault içindeki müşteri tarafından yönetilen anahtarlarla saydam veri şifrelemesi](transparent-data-encryption-byok-overview.md)bölümüne bakın.
1. Yönetilen örnek için denetim etkinleştirildiyse şunları doğrulayın:
    - Depolama kapsayıcısı veya mevcut günlüklere sahip olay hub 'ı hedef bölgeye taşınır.
    - Denetim, hedef örnekte yapılandırıldı. Daha fazla bilgi için bkz. [SQL yönetilen örneği Ile denetleme](../managed-instance/auditing-configure.md).
1. Örneğinizde uzun süreli bir bekletme ilkesi varsa (LTR), mevcut LTR yedeklemeleri geçerli örnekle ilişkili olarak kalır. Hedef örnek farklı olduğundan, örnek silinse de kaynak örneğini kullanarak kaynak bölgedeki eski LTR yedeklemelere erişebilirsiniz.

  > [!NOTE]
  > Bu, bağımsız bulutu ve bir ortak bölge arasında geçiş yapmak için yeterli olacaktır. Bu tür bir geçiş, LTR yedeklemelerin Şu anda desteklenmeyen hedef örneğe taşınmasını gerektirir.

### <a name="prepare-resources"></a>Kaynakları hazırlama

Her kaynak yönetilen örnek ve SQL yönetilen örneği 'nin karşılık gelen hedef örneği arasında bir yük devretme grubu oluşturun.

Her örnekteki tüm veritabanlarının çoğaltılması otomatik olarak başlatılacak. Daha fazla bilgi için bkz. [otomatik yük devretme grupları](auto-failover-group-overview.md).

### <a name="monitor-the-preparation-process"></a>Hazırlama işlemini izleme

Veritabanlarınızı kaynaktan hedefe çoğaltmayı izlemek için, [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) öğesini düzenli aralıklarla çağırabilirsiniz. Çıkış nesnesi `Get-AzSqlDatabaseFailoverGroup` **replicationstate** için bir özellik içerir:

- **Replicationstate = 2** (CATCH_UP), veritabanının eşitlendiğini ve güvenle yük devretmekte olduğunu gösterir.
- **Replicationstate = 0** (dengeli dağıtım), veritabanının henüz çalıştırılmadığını ve Yük Devretme girişiminin başarısız olacağını belirtir.

### <a name="test-synchronization"></a>Test eşitlemesi

**Replicationstate** olduktan sonra `2` , `<fog-name>.secondary.database.windows.net` bağlantı, uygun güvenlik yapılandırması ve veri çoğaltma sağlamak için her bir veritabanına veya ikincil uç noktayı kullanarak veritabanlarının alt kümesine bağlanın ve veritabanlarına yönelik sorgu gerçekleştirin.

### <a name="initiate-the-move"></a>Taşımayı Başlat

1. İkincil uç noktayı kullanarak hedef yönetilen örneğe bağlanın `<fog-name>.secondary.database.windows.net` .
1. İkincil yönetilen örneği tam eşitlemeyle birincil olacak şekilde değiştirmek için [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) komutunu kullanın. Bu işlem başarılı olur veya geri alınacaktır.
1. `nslook up <fog-name>.secondary.database.windows.net`DNS CNAME girişinin hedef BÖLGENIN IP adresine işaret ettiğini belirlemek için komutunu kullanarak komutun başarıyla tamamlandığını doğrulayın. Switch komutu başarısız olursa, CNAME güncellenmez.

### <a name="remove-the-source-managed-instances"></a>Kaynak yönetilen örnekleri kaldır

Taşıma tamamlandıktan sonra gereksiz ücretlerden kaçınmak için kaynak bölgedeki kaynakları kaldırın.

1. [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)kullanarak yük devretme grubunu silin. Bu, yük devretme grubu yapılandırmasını bırakacak ve iki örnek arasında coğrafi çoğaltma bağlantılarını sonlandıracaktır.
1. [Remove-Azsqlınstance](/powershell/module/az.sql/remove-azsqlinstance)kullanarak kaynak yönetilen örneği silin.
1. Kaynak grubundaki sanal küme, sanal ağ ve güvenlik grubu gibi ek kaynakları kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

Geçiş yapıldıktan sonra veritabanınızı [yönetin](manage-data-after-migrating-to-database.md) .
