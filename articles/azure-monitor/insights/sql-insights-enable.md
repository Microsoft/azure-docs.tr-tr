---
title: SQL içgörülerini etkinleştirme
description: Azure Izleyici 'de SQL Insights 'ı etkinleştirme
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: 012aa364fe9e379455b6b63f7c9e541d2d5b97ed
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726907"
---
# <a name="enable-sql-insights-preview"></a>SQL Insights 'ı etkinleştir (Önizleme)
Bu makalede, SQL dağıtımlarınızı izlemek için [SQL Insights](sql-insights-overview.md) 'ın nasıl etkinleştirileceği açıklanır. İzleme, SQL dağıtımlarınıza bir bağlantı yapan ve izleme verilerini toplamak için dinamik yönetim görünümleri (DMVs) kullanan bir Azure sanal makineden gerçekleştirilir. Hangi veri kümelerinin toplanacağını ve bir izleme profili kullanan koleksiyon sıklığını denetleyebilirsiniz.

> [!NOTE]
> Bir Resource Manager şablonu kullanarak izleme profili ve sanal makine oluşturarak SQL öngörülerini etkinleştirmek için, bkz. [SQL Insights için Kaynak Yöneticisi şablonu örnekleri](resource-manager-sql-insights.md).

## <a name="create-log-analytics-workspace"></a>Log Analytics çalışma alanı oluşturma
SQL Insights, verilerini bir veya daha fazla [Log Analytics çalışma](../logs/data-platform-logs.md#log-analytics-workspaces)alanında depolar.  SQL öngörülerini etkinleştirebilmeniz için önce [bir çalışma alanı oluşturmanız](../logs/quick-create-workspace.md) ya da var olan bir çalışma alanı seçmeniz gerekir. Tek bir çalışma alanı birden çok izleme profili ile kullanılabilir, ancak çalışma alanı ve profillerin aynı Azure bölgesinde bulunması gerekir. SQL Insights 'daki özellikleri etkinleştirmek ve erişmek için, çalışma alanında [Log Analytics katkıda bulunan rolüne](../logs/manage-access.md) sahip olmanız gerekir. 

## <a name="create-monitoring-user"></a>İzleme kullanıcısı oluştur 
İzlemek istediğiniz SQL dağıtımlarında bir kullanıcıya ihtiyacınız vardır. Farklı türlerde SQL dağıtımları için aşağıdaki yordamları izleyin.

Aşağıdaki yönergelerde, izleyebilmeniz gereken SQL türü başına işlem ele alınmaktadır.  Bunu tek seferde birkaç SQL kaynak üzerinde bir komut dosyasıyla gerçekleştirmek için lütfen aşağıdaki [Benioku dosyasına](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation-README.txt) ve [örnek betiğe](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation.ps1)bakın.


### <a name="azure-sql-database"></a>Azure SQL veritabanı
[SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) veya [sorgu Düzenleyicisi (Önizleme)](../../azure-sql/database/connect-query-portal.md) ile Azure SQL veritabanı 'nı Azure Portal açın.

Gerekli izinlere sahip bir kullanıcı oluşturmak için aşağıdaki betiği çalıştırın. *Kullanıcıyı* bir Kullanıcı adı ve *mystrongpassword* ile parolayla değiştirin.

```sql
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Telegraf Kullanıcı betiği oluşturun." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Kullanıcının oluşturulduğunu doğrulayın.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Telegraf Kullanıcı betiğini doğrulayın." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type,
       authentication_type_desc as authentication_type
from sys.database_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

### <a name="azure-sql-managed-instance"></a>Azure SQL Yönetilen Örnek
Azure SQL yönetilen Örneğinizde oturum açın ve [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) veya benzer bir aracı kullanarak izleme kullanıcısını gereken izinlerle oluşturmak için aşağıdaki betiği çalıştırın. *Kullanıcıyı* bir Kullanıcı adı ve *mystrongpassword* ile parolayla değiştirin.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
SQL Server çalıştıran Azure sanal makinenizde oturum açın ve [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) veya benzer aracı kullanarak izleme kullanıcısını gereken izinlerle oluşturmak için aşağıdaki betiği çalıştırın. *Kullanıcıyı* bir Kullanıcı adı ve *mystrongpassword* ile parolayla değiştirin.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

Kullanıcının oluşturulduğunu doğrulayın.

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type
from sys.server_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

## <a name="create-azure-virtual-machine"></a>Azure sanal makinesi oluştur 
SQL 'i izlemek için veri toplamak üzere kullanılacak bir veya daha fazla Azure sanal makinesi oluşturmanız gerekecektir.  

> [!NOTE]
>  [İzleme profilleri](#create-sql-monitoring-profile) , izlemek istediğiniz farklı türde SQL içinden hangi verileri toplayacağınızı belirler. Her izleme sanal makinesinde ilişkili yalnızca bir izleme profili bulunabilir. Birden çok izleme profiline ihtiyacınız varsa, her biri için bir sanal makine oluşturmanız gerekir.

### <a name="azure-virtual-machine-requirements"></a>Azure sanal makine gereksinimleri
Azure sanal makineleri aşağıdaki gereksinimlere sahiptir.

- İşletim sistemi: Ubuntu 18,04 
- Önerilen Azure sanal makine boyutları: Standard_B2s (2 CPU, 4 GiB belleği) 
- Desteklenen bölgeler: [Azure izleyici Aracısı tarafından desteklenen](../agents/azure-monitor-agent-overview.md#supported-regions) herhangi bir bölge

> [!NOTE]
> Standard_B2s (2 CPU, 4 GiB belleği) sanal makine boyutu en fazla 100 bağlantı dizesini destekleyecektir. Tek bir sanal makineye 100 'den fazla bağlantı ayıramamanız gerekir.

SQL kaynaklarınızın ağ ayarlarına bağlı olarak, sanal makinelerin izleme verilerini toplamak üzere ağ bağlantıları yapabilmesi için SQL kaynaklarınızla aynı sanal ağa yerleştirilmesi gerekebilir.  

## <a name="configure-network-settings"></a>Ağ ayarlarını yapılandırma
Her SQL türü, izleme sanal makinenizin SQL 'e güvenli bir şekilde erişmesi için yöntemler sunar.  Aşağıdaki bölümler, SQL türüne göre seçenekleri kapsar.

### <a name="azure-sql-databases"></a>Azure SQL Veritabanları  

SQL Insights, Azure SQL veritabanınıza hem genel uç nokta hem de sanal ağı aracılığıyla erişmeyi destekler.

Genel uç nokta üzerinden erişim için, **güvenlik duvarı ayarları** sayfası ve [IP güvenlik duvarı ayarları](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-firewall-rules) bölümü altına bir kural eklersiniz.  Bir sanal ağdan erişim belirtmek için, [sanal ağ güvenlik duvarı kurallarını](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#virtual-network-firewall-rules) ayarlayabilir ve [Azure izleyici aracısının gerektirdiği hizmet etiketlerini](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking)ayarlayabilirsiniz.  [Bu makalede](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-vs-virtual-network-firewall-rules) , bu iki güvenlik duvarı kuralı türü arasındaki farklar açıklanmaktadır.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Sunucu güvenlik duvarı ayarla" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Güvenlik Duvarı ayarları." lightbox="media/sql-insights-enable/firewall-settings.png":::


### <a name="azure-sql-managed-instances"></a>Azure SQL Yönetilen Örnekleri 

İzleme sanal makineniz SQL MI kaynaklarınızla aynı VNet 'te yer alıyorsa, bkz. [aynı VNET 'in Içinde Bağlan](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet). İzleme sanal makineniz SQL MI kaynaklarınızdan farklı bir VNet 'te yer alıyorsa, bkz. [farklı bir VNET Içinde Bağlan](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet).


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Azure sanal makinesi ve Azure SQL sanal makinesi  
İzleme sanal makineniz SQL sanal makine kaynaklarınızla aynı VNet içindeyse, bkz. [sanal ağ içinde SQL Server bağlanma](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network). İzleme sanal makineniz SQL sanal makine kaynaklarınızdan farklı bir VNet 'te yer alıyorsa, bkz.  [İnternet üzerinden SQL Server bağlanma](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet).

## <a name="store-monitoring-password-in-key-vault"></a>İzleme parolasını Key Vault içinde depola
SQL Kullanıcı bağlantı parolalarınızı doğrudan izleme profili bağlantı dizelerinizle girmek yerine bir Key Vault depolamanız gerekir.

Profilinizi SQL izleme için ayarlıyorsanız, kullanmak istediğiniz Key Vault kaynak üzerinde aşağıdaki izinlerden birine ihtiyacınız olacaktır:

- Microsoft.Authorization/roleAssignments/write 
- Microsoft. Authorization/Roleatamalar/Kullanıcı erişimi Yöneticisi veya sahibi gibi silme izinleri 

Belirttiğiniz Key Vault kullanan SQL Monitoring profilinizi oluşturmanın bir parçası olarak yeni bir erişim ilkesi otomatik olarak oluşturulur. Key Vault ağ ayarları için *tüm ağlardan erişime Izin ver* ' i kullanın.


## <a name="create-sql-monitoring-profile"></a>SQL izleme profili oluştur
Azure portal **Azure izleyici** menüsünün **Öngörüler** bölümünde **SQL (Önizleme)** öğesini seçerek SQL Insights 'ı açın. **Yeni Profil oluştur**' a tıklayın. 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Yeni profil oluşturun." lightbox="media/sql-insights-enable/create-new-profile.png":::

Profil, SQL sistemlerinizden toplamak istediğiniz bilgileri depolayacaktır.  İçin belirli ayarlara sahiptir: 

- Azure SQL Veritabanı 
- Azure SQL Yönetilen Örnekleri 
- SQL Server sanal makinelerde çalışıyor  

Örneğin, *SQL üretimi* adlı bir profil ve veri toplama sıklığı, toplanacak veriler ve verilerin gönderileceği çalışma alanı için farklı ayarlara sahip başka bir *SQL hazırlama* adlı bir profil oluşturabilirsiniz. 

Profil, seçtiğiniz abonelik ve kaynak grubunda bir [veri toplama kuralı](../agents/data-collection-rule-overview.md) kaynağı olarak depolanır. Her profil için şunlar gerekir:

- Ada. Oluşturulduktan sonra düzenlenemez.
- Konum. Bu bir Azure bölgesidir.
- İzleme verilerini depolamak için çalışma alanı Log Analytics.
- Toplanacak SQL izleme verilerinin sıklığı ve türü için koleksiyon ayarları.

> [!NOTE]
> Profil konumunun, izleme verilerini göndermek için planladığınız Log Analytics çalışma alanıyla aynı konumda olması gerekir.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Profil ayrıntıları." lightbox="media/sql-insights-enable/profile-details.png":::

İzleme profilinizin ayrıntılarını girdikten sonra **izleme profili oluştur** ' a tıklayın. Profilin dağıtılması bir dakika kadar sürebilir.  **İzleme profili** Birleşik giriş kutusunda listelenen yeni profili görmüyorsanız, Yenile düğmesine tıklayın ve dağıtım tamamlandıktan sonra görünmelidir.  Yeni profili seçtikten sonra, profille ilişkilendirilecek bir izleme makinesi eklemek için **profili Yönet** sekmesini seçin.

### <a name="add-monitoring-machine"></a>İzleme makinesi Ekle
SQL örneklerinizi izlemek ve bağlantı dizelerini sağlamak üzere yüklenecek Sanal makineyi seçmek için bir bağlam paneli açmak üzere **izleme makinesi Ekle** ' yi seçin.

İzleme sanal makinenizin aboneliğini ve adını seçin. İzleme kullanıcısı için parolanızı depolamak üzere Key Vault kullanıyorsanız, bu gizli dizileri içeren Key Vault kaynakları seçin ve bağlantı dizelerinde kullanılacak URL ve parola adını girin. Farklı SQL dağıtımları için bağlantı dizesini tanımlama hakkında ayrıntılı bilgi için sonraki bölüme bakın.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="İzleme makinesi ekleyin." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Bağlantı dizeleri Ekle 
Bağlantı dizesi, SQL Insights 'ın dinamik yönetim görünümlerini çalıştırmak için SQL 'de oturum açarken kullanması gereken kullanıcı adını belirtir. İzleme kullanıcılarınız için parolayı depolamak üzere bir Key Vault kullanıyorsanız kullanılacak gizli dizinin URL 'sini ve adını belirtin. 

Bağlantı dizesi her bir SQL kaynağı türü için farklılık gösterecektir:

#### <a name="azure-sql-databases"></a>Azure SQL Veritabanları 
Bağlantı dizesini şu biçimde girin:

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

Veritabanına yönelik **bağlantı dizeleri** menü öğesinden ayrıntıları alın.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="SQL veritabanı bağlantı dizesi" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Okunabilir bir ikinciyi izlemek için bağlantı dizesine anahtar değerini ekleyin `ApplicationIntent=ReadOnly` . SQL Insights, tek bir ikincil izlemeyi destekler. Toplanan veriler birincil veya ikincil yansıtacak şekilde etiketlenecek. 


#### <a name="azure-virtual-machines-running-sql-server"></a>SQL Server çalıştıran Azure sanal makineleri 
Bağlantı dizesini şu biçimde girin:

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

İzleme sanal makineniz aynı VNET 'se, sunucunun özel IP adresini kullanın.  Aksi takdirde, genel IP adresini kullanın. Azure SQL sanal makinesini kullanıyorsanız, kaynağın **güvenlik** sayfasında burada hangi bağlantı noktasını kullanacağınızı görebilirsiniz.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="SQL sanal makine güvenliği" lightbox="media/sql-insights-enable/sql-vm-security.png":::


### <a name="azure-sql-managed-instances"></a>Azure SQL Yönetilen Örnekleri 
Bağlantı dizesini şu biçimde girin:

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
Yönetilen örnek için **bağlantı dizeleri** menü öğesinden ayrıntıları alın.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="SQL yönetilen örnek bağlantı dizesi" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Okunabilir bir ikinciyi izlemek için bağlantı dizesine anahtar değerini ekleyin `ApplicationIntent=ReadOnly` . SQL Insights, tek bir ikincili izlemeyi destekler ve toplanan veriler birincil veya Ikincil yansıtmak üzere etiketlenecek. 


## <a name="monitoring-profile-created"></a>İzleme profili oluşturuldu 

Sanal makineyi SQL kaynaklarınızdan veri toplayacak şekilde yapılandırmak için **izleme sanal makinesi Ekle** ' yi seçin. **Genel bakış** sekmesine dönmeyin.  Birkaç dakika içinde, durum sütunu "toplamayı" okumak için değişmelidir, izlemeyi seçtiğiniz SQL kaynakları için verileri görmeniz gerekir.

Veri görmüyorsanız, bkz. sorunu belirlemek için [SQL Insights sorunlarını giderme](sql-insights-troubleshoot.md) . 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Profil oluşturuldu" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> İzleme profilinizi veya izleme sanal makinelerinizdeki bağlantı dizelerini güncelleştirmeniz gerekiyorsa, bunu SQL Insights **Yönetim profili** sekmesi aracılığıyla yapabilirsiniz.  Güncelleştirmeleriniz kaydedildikten sonra değişiklikler yaklaşık 5 dakika içinde uygulanır.

## <a name="next-steps"></a>Sonraki adımlar

- SQL Insights etkinleştirildikten sonra düzgün çalışmıyorsa bkz. [SQL Insights sorunlarını giderme](sql-insights-troubleshoot.md) .
