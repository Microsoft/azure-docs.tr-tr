---
title: Veritabanı kopyalama
description: Aynı sunucuda veya farklı bir sunucuda var olan bir Azure SQL veritabanının işlemsel olarak tutarlı bir kopyasını oluşturun.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 11/14/2019
ms.openlocfilehash: 0b8bfff03414dd02360cab1957ea2205e392235d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082471"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Azure SQL veritabanı 'nın işlem temelli tutarlı bir kopyasını kopyalama

Azure SQL veritabanı, aynı sunucuda veya farklı bir sunucuda var olan bir Azure SQL veritabanının ([tek veritabanı](sql-database-single-database.md)) işlemsel olarak tutarlı bir kopyasını oluşturmak için çeşitli yöntemler sağlar. Azure portal, PowerShell veya T-SQL ' i kullanarak bir SQL veritabanını kopyalayabilirsiniz. 

## <a name="overview"></a>Genel Bakış

Veritabanı kopyası, kopyalama isteği sırasında kaynak veritabanının anlık görüntüsüdür. Aynı sunucuyu veya farklı bir sunucuyu seçebilirsiniz. Ayrıca, hizmet katmanını ve işlem boyutunu tutmayı seçebilir veya aynı hizmet katmanı (Edition) içinde farklı bir işlem boyutu kullanabilirsiniz. Kopyalama işlemi tamamlandıktan sonra, tamamen işlevsel, bağımsız bir veritabanı haline gelir. Bu noktada, dilediğiniz sürüme yükseltebilir veya onu indirgeyede olursunuz. Oturum açmalar, kullanıcılar ve izinler bağımsız olarak yönetilebilir. Kopya, coğrafi çoğaltma teknolojisi kullanılarak oluşturulur ve dengeli dağıtım tamamlandığında, coğrafi çoğaltma bağlantısı otomatik olarak sonlandırılır. Coğrafi çoğaltmanın kullanılmasıyla ilgili tüm gereksinimler veritabanı kopyalama işlemi için geçerlidir. Ayrıntılar için bkz. [etkin coğrafi Çoğaltmaya genel bakış](sql-database-active-geo-replication.md) .

> [!NOTE]
> [Otomatik veritabanı yedeklemeleri](sql-database-automated-backups.md) , bir veritabanı kopyası oluştururken kullanılır.

## <a name="logins-in-the-database-copy"></a>Veritabanı kopyasında oturum açma işlemleri

Aynı SQL veritabanı sunucusuna bir veritabanını kopyaladığınızda, her iki veritabanında da aynı oturum açma işlemleri kullanılabilir. Veritabanını kopyalamak için kullandığınız güvenlik sorumlusu yeni veritabanında veritabanı sahibi olur. Tüm veritabanı kullanıcıları, izinleri ve güvenlik tanımlayıcıları (SID 'Ler) veritabanı kopyasına kopyalanır.  

Bir veritabanını farklı bir SQL veritabanı sunucusuna kopyaladığınızda, yeni sunucudaki güvenlik sorumlusu yeni veritabanında veritabanı sahibi olur. Veri erişimi için [Kapsanan Veritabanı kullanıcılarını](sql-database-manage-logins.md) kullanıyorsanız, hem birincil hem de ikincil veritabanlarının her zaman aynı kullanıcı kimlik bilgilerine sahip olduğundan emin olun, böylece kopyalama tamamlandıktan sonra aynı kimlik bilgileriyle hemen erişebilirsiniz. 

[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)kullanıyorsanız, kopyada kimlik bilgilerini yönetme gereksinimini tamamen ortadan kaldırabilirsiniz. Ancak, veritabanını yeni bir sunucuya kopyaladığınızda oturum açma tabanlı erişim çalışmayabilir, çünkü oturum açmalar yeni sunucuda mevcut değildir. Farklı bir SQL veritabanı sunucusuna bir veritabanını kopyaladığınızda, oturum açma işlemlerini yönetme hakkında bilgi edinmek için bkz. [olağanüstü durum kurtarma sonrasında Azure SQL veritabanı güvenliğini yönetme](sql-database-geo-replication-security-config.md). 

Kopyalama başarılı olduktan ve diğer kullanıcılar yeniden eşlendikten sonra, yalnızca kopyalamayı Başlatan oturum açma, veritabanı sahibi yeni veritabanında oturum açabilir. Kopyalama işlemi tamamlandıktan sonra oturum açma işlemlerini çözümlemek için bkz. [oturum açma bilgilerini çözümleme](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Azure portal kullanarak bir veritabanını kopyalama

Azure portal kullanarak bir veritabanını kopyalamak için veritabanınızın sayfasını açın ve ardından **Kopyala**' ya tıklayın. 

   ![Veritabanı kopyalama](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>PowerShell kullanarak bir veritabanını kopyalama

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell kullanarak bir veritabanını kopyalamak için [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet 'ini kullanın. 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Tüm örnek betik için bkz. [veritabanını yeni bir sunucuya kopyalama](scripts/sql-database-copy-database-to-new-server-powershell.md).

Veritabanı kopyası zaman uyumsuz bir işlemdir ancak hedef veritabanı, istek kabul edildikten hemen sonra oluşturulur. Hala devam ederken kopyalama işlemini iptal etmeniz gerekiyorsa, [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet 'ini kullanarak hedef veritabanını bırakın.  

## <a name="rbac-roles-to-manage-database-copy"></a>Veritabanı kopyasını yönetmek için RBAC rolleri

Bir veritabanı kopyası oluşturmak için aşağıdaki rollerde olması gerekir

- Abonelik sahibi veya
- SQL Server katkıda bulunan rolü veya
- Kaynak ve hedef veritabanlarında aşağıdaki izinle özel rol:

   Microsoft. SQL/Servers/veritabanları/okuma   
   Microsoft. SQL/Servers/veritabanları/Write   

Bir veritabanı kopyasını iptal etmek için, aşağıdaki rollerde olması gerekir

- Abonelik sahibi veya
- SQL Server katkıda bulunan rolü veya
- Kaynak ve hedef veritabanlarında aşağıdaki izinle özel rol:

   Microsoft. SQL/Servers/veritabanları/okuma   
   Microsoft. SQL/Servers/veritabanları/Write   
   
Veritabanı kopyasını Azure portal kullanarak yönetmek için aşağıdaki izinlere de ihtiyacınız olacaktır:

&nbsp; &nbsp; &nbsp; Microsoft. resources/abonelikleri/kaynakları/okuma   
Microsoft. resources/abonelikleri/kaynakları/Write &nbsp; &nbsp; &nbsp;   
Microsoft. resources/dağıtımlar/Read &nbsp; &nbsp; &nbsp;   
Microsoft. resources/dağıtımlar/Write &nbsp; &nbsp; &nbsp;   
&nbsp; &nbsp; Microsoft. resources/dağıtımlar/operationdurumlarının/Read &nbsp;    

Portalda kaynak grubunda bulunan ve SQL işlemleri de dahil olmak üzere birden çok kaynak sağlayıcılarındaki işlemler altında gerçekleştirilen işlemleri görmek isterseniz, bu ek RBAC rollerinin olması gerekir: 

&nbsp; &nbsp; &nbsp; Microsoft. resources/abonelikler/ResourceGroups/dağıtımlar/işlemler/okuma   
&nbsp; &nbsp; &nbsp; Microsoft. resources/abonelikler/ResourceGroups/dağıtımlar/operationdurumlarının/Read



## <a name="copy-a-database-by-using-transact-sql"></a>Transact-SQL kullanarak bir veritabanını kopyalama

Ana veritabanında sunucu düzeyi sorumlu oturum açma veya kopyalamak istediğiniz veritabanını oluşturan oturum açma bilgileriyle oturum açın. Veritabanı kopyalama işleminin başarılı olması için, sunucu düzeyi sorumlu olmayan oturum açma işlemleri dbmanager rolünün üyesi olmalıdır. Oturumlar ve sunucuya bağlanma hakkında daha fazla bilgi için bkz. [oturum açma bilgilerini yönetme](sql-database-manage-logins.md).

[Veritabanı oluştur](https://msdn.microsoft.com/library/ms176061.aspx) ifadesiyle kaynak veritabanını kopyalamaya başlayın. Bu bildirimin yürütülmesi, veritabanı kopyalama işlemini başlatır. Bir veritabanının kopyalanması zaman uyumsuz bir işlem olduğundan, CREATE DATABASE deyimleri, veritabanı kopyalama işlemi tamamlanmadan önce döndürülür.

### <a name="copy-a-sql-database-to-the-same-server"></a>Bir SQL veritabanını aynı sunucuya kopyalama

Ana veritabanında sunucu düzeyi sorumlu oturum açma veya kopyalamak istediğiniz veritabanını oluşturan oturum açma bilgileriyle oturum açın. Veritabanı kopyalama işleminin başarılı olması için, sunucu düzeyi sorumlu olmayan oturum açma işlemleri dbmanager rolünün üyesi olmalıdır.

Bu komut, Veritabanı1 adlı yeni bir veritabanına aynı sunucuda Veritabanı2 kopyalar. Veritabanınızın boyutuna bağlı olarak kopyalama işleminin tamamlanması biraz zaman alabilir.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>SQL veritabanını farklı bir sunucuya kopyalama

Yeni veritabanının oluşturulacağı SQL veritabanı sunucusu hedef sunucunun ana veritabanında oturum açın. Kaynak SQL veritabanı sunucusundaki kaynak veritabanının veritabanı sahibiyle aynı ada ve parolaya sahip bir oturum açma kullanın. Hedef sunucudaki oturum açma, dbmanager rolünün bir üyesi olmalıdır veya sunucu düzeyinde asıl oturum açma olmalıdır.

Bu komut Sunucu1 üzerindeki Veritabanı1 öğesini Sunucu2 üzerinde Veritabanı2 adlı yeni bir veritabanına kopyalar. Veritabanınızın boyutuna bağlı olarak kopyalama işleminin tamamlanması biraz zaman alabilir.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> Her iki sunucu da güvenlik duvarı, T-SQL COPY komutunu veren istemcinin IP 'sinden gelen bağlantıya izin verecek şekilde yapılandırılmalıdır.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>SQL veritabanını farklı bir aboneliğe kopyalama

Veritabanınızı farklı bir abonelikte SQL veritabanı sunucusuna kopyalamak için önceki bölümde açıklanan adımları kullanabilirsiniz. Kaynak veritabanının veritabanı sahibiyle aynı ada ve parolaya sahip bir oturum açma ve dbmanager rolünün bir üyesi veya sunucu düzeyinde asıl oturum açma olduğundan emin olun. 

> [!NOTE]
> [Azure Portal](https://portal.azure.com) , Portal ARM API 'sini çağırdığından ve coğrafi çoğaltma işleminde yer alan her iki sunucuya erişmek için abonelik sertifikalarını kullandığından, farklı bir aboneliğe kopyalama desteklenmez.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>Kopyalama işleminin ilerlemesini izleme

Sys. databases ve sys. dm_database_copies görünümlerini sorgulayarak kopyalama işlemini izleyin. Kopyalama işlemi devam ederken, yeni veritabanı için sys. databases görünümünün **state_desc** sütunu **kopyalama**olarak ayarlanır.

* Kopyalama başarısız olursa, yeni veritabanı için sys. databases görünümünün **state_desc** sütunu **şüpheli**olarak ayarlanır. DROP ifadesini yeni veritabanında yürütün ve daha sonra yeniden deneyin.
* Kopyalama başarılı olursa, yeni veritabanı için sys. databases görünümünün **state_desc** sütunu **çevrimiçi**olarak ayarlanır. Kopyalama tamamlanmıştır ve yeni veritabanı, kaynak veritabanından bağımsız olarak değiştirilebilen normal bir veritabanıdır.

> [!NOTE]
> Kopyalama işlemi devam ederken iptal etmeyi seçerseniz, yeni veritabanında [drop database](https://msdn.microsoft.com/library/ms178613.aspx) ifadesini yürütün. Alternatif olarak, kaynak veritabanında DROP DATABASE ifadesinin yürütülmesi kopyalama işlemini de iptal eder.

> [!IMPORTANT]
> Kaynağından önemli ölçüde daha küçük bir SLO kopyası oluşturmanız gerekiyorsa, hedef veritabanı dengeli işlem işlemini tamamlayacak yeterli kaynağa sahip olmayabilir ve kopyalama işlemi başarısız olmasına neden olabilir. Bu senaryoda, farklı bir sunucuda ve/veya farklı bir bölgede bir kopya oluşturmak için bir coğrafi geri yükleme isteği kullanın. Daha fazla bilgi için bkz. [veritabanı yedeklerini kullanarak bir Azure SQL veritabanını kurtarma](sql-database-recovery-using-backups.md#geo-restore) .


## <a name="resolve-logins"></a>Oturum açma işlemlerini çözümle

Yeni veritabanı hedef sunucuda çevrimiçi olduktan sonra, kullanıcıları yeni veritabanından hedef sunucudaki oturum açmayla yeniden eşlemek için [alter User](https://msdn.microsoft.com/library/ms176060.aspx) deyimini kullanın. Yalnız bırakılmış kullanıcıları çözümlemek için bkz. [yalnız bırakılmış kullanıcılarda sorun giderme](https://msdn.microsoft.com/library/ms175475.aspx). Ayrıca bkz. [olağanüstü durum kurtarma sonrasında Azure SQL veritabanı güvenliğini yönetme](sql-database-geo-replication-security-config.md).

Yeni veritabanındaki tüm kullanıcılar, kaynak veritabanında sahip oldukları izinleri korurlar. Veritabanı kopyasını Başlatan Kullanıcı, yeni veritabanının veritabanı sahibi olur ve yeni bir güvenlik tanımlayıcısı (SID) atanır. Kopyalama başarılı olduktan ve diğer kullanıcılar yeniden eşlendikten sonra, yalnızca kopyalamayı Başlatan oturum açma, veritabanı sahibi yeni veritabanında oturum açabilir.

Bir veritabanını farklı bir SQL veritabanı sunucusuna kopyaladığınızda kullanıcıları ve oturum açma işlemlerini yönetme hakkında bilgi edinmek için bkz. [Azure SQL veritabanı güvenliğini olağanüstü durum kurtarma sonrasında yönetme](sql-database-geo-replication-security-config.md).

## <a name="database-copy-errors"></a>Veritabanı kopyalama hataları

Azure SQL veritabanı 'nda bir veritabanı kopyalanırken aşağıdaki hatalarla karşılaşılabilir. Daha fazla bilgi için bkz. [Azure SQL Veritabanını kopyalama](sql-database-copy.md).

| Hata kodu | Severity | Açıklama |
| ---:| ---:|:--- |
| 40635 |16 |İstemci IP adresi '%.&#x2a;ls' geçici olarak devre dışıdır. |
| 40637 |16 |Veritabanı kopyası oluşturma şu anda devre dışı. |
| 40561 |16 |Veritabanı kopyalama başarısız oldu. Kaynak ya da hedef veritabanı yok. |
| 40562 |16 |Veritabanı kopyalama başarısız oldu. Kaynak veritabanı bırakılmış. |
| 40563 |16 |Veritabanı kopyalama başarısız oldu. Hedef veritabanı bırakılmış. |
| 40564 |16 |Veritabanı kopyalama bir iç hata nedeniyle başarısız oldu. Lütfen hedef veritabanını bırakın ve yeniden deneyin. |
| 40565 |16 |Veritabanı kopyalama başarısız oldu. Aynı kaynaktan 1 ' den fazla eşzamanlı veritabanı kopyası yapılmasına izin verilmez. Lütfen hedef veritabanını bırakın ve daha sonra yeniden deneyin. |
| 40566 |16 |Veritabanı kopyalama bir iç hata nedeniyle başarısız oldu. Lütfen hedef veritabanını bırakın ve yeniden deneyin. |
| 40567 |16 |Veritabanı kopyalama bir iç hata nedeniyle başarısız oldu. Lütfen hedef veritabanını bırakın ve yeniden deneyin. |
| 40568 |16 |Veritabanı kopyalama başarısız oldu. Kaynak veritabanı kullanılamaz duruma geldi. Lütfen hedef veritabanını bırakın ve yeniden deneyin. |
| 40569 |16 |Veritabanı kopyalama başarısız oldu. Hedef veritabanı kullanılamaz duruma geldi. Lütfen hedef veritabanını bırakın ve yeniden deneyin. |
| 40570 |16 |Veritabanı kopyalama bir iç hata nedeniyle başarısız oldu. Lütfen hedef veritabanını bırakın ve daha sonra yeniden deneyin. |
| 40571 |16 |Veritabanı kopyalama bir iç hata nedeniyle başarısız oldu. Lütfen hedef veritabanını bırakın ve daha sonra yeniden deneyin. |

## <a name="next-steps"></a>Sonraki adımlar

* Oturumlar hakkında daha fazla bilgi için bkz. [oturum açma bilgilerini yönetme](sql-database-manage-logins.md) ve [olağanüstü durum kurtarma sonrasında Azure SQL veritabanı güvenliğini yönetme](sql-database-geo-replication-security-config.md).
* Bir veritabanını dışarı aktarmak için bkz. [veritabanını BACPAC 'e aktarma](sql-database-export.md).
