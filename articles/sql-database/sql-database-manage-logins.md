---
title: Oturum açma bilgileri ve kullanıcılar
description: SQL veritabanı ve SQL veri ambarı güvenlik yönetimi hakkında, özellikle de sunucu düzeyi sorumlu hesabı aracılığıyla veritabanı erişimini ve oturum açma güvenliğini yönetme hakkında bilgi edinin.
keywords: sql veritabanı güvenliği,veritabanı güvenliği yönetimi,oturum açma güvenliği,veritabanı güvenliği,veritabanı erişimi
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/26/2019
ms.openlocfilehash: e9934f868fb62f9b1a19ef408dab69ab8a2c0e29
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159154"
---
# <a name="controlling-and-granting-database-access-to-sql-database-and-sql-data-warehouse"></a>SQL veritabanı ve SQL veri ambarı 'na veritabanı erişimini denetleme ve verme

Güvenlik duvarı kuralları yapılandırmasından sonra, Azure [SQL veritabanı](sql-database-technical-overview.md) ve [SQL veri ambarı](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 'na, veritabanı sahibi olarak veya veritabanındaki bir veritabanı kullanıcısı olarak yönetici hesaplarından biri olarak bağlanabilirsiniz.  

> [!NOTE]  
> Bu konu, Azure SQL Server ve Azure SQL Server 'da oluşturulan SQL veritabanı ve SQL veri ambarı veritabanları için geçerlidir. Kolaylık açısından, hem SQL Veritabanı hem de SQL Veri Ambarı için SQL Veritabanı terimi kullanılmaktadır. 
> [!TIP]
> Öğretici için bkz. [Azure SQL veritabanınızı güvenli hale getirme](sql-database-security-tutorial.md). Bu öğretici, **Azure SQL veritabanı yönetilen örneği**için geçerlidir.

## <a name="unrestricted-administrative-accounts"></a>Kısıtlanmamış yönetici hesapları

Yönetici işlevlerine sahip iki yönetici hesabı (**Sunucu yöneticisi** ve **Active Directory yöneticisi**) vardır. SQL sunucunuz için bu yönetici hesaplarını belirlemek için, Azure portal açın ve SQL Server veya SQL veritabanınızın Özellikler sekmesine gidin.

![SQL Server Yöneticileri](media/sql-database-manage-logins/sql-admins.png)

- **Sunucu Yöneticisi**

  Bir Azure SQL sunucusu oluşturduğunuzda **Sunucu yöneticisi oturum açma bilgileri** belirlemeniz gerekir. SQL Server, bu hesabı ana veritabanında oturum açma bilgileri olarak oluşturur. Bu hesap SQL Server kimlik doğrulaması (kullanıcı adı ve parola) kullanarak bağlanır. Bu hesaplardan yalnızca biri mevcut olabilir.

  > [!NOTE]
  > Sunucu yöneticisinin parolasını sıfırlamak için [Azure Portal](https://portal.azure.com)gidin, **SQL sunucuları**' na tıklayın, listeden sunucuyu seçin ve ardından **Parolayı Sıfırla**' ya tıklayın.

- **Yönetici Azure Active Directory**

  Ayrıca, Azure Active Directory’deki bir adet kişi veya güvenlik grubu hesabı da yönetici olarak yapılandırılabilir. Bir Azure AD yöneticisi yapılandırmak isteğe bağlıdır, ancak SQL veritabanı 'na bağlanmak için Azure AD hesapları kullanmak istiyorsanız bir Azure **ad Yöneticisi yapılandırılmalıdır** . Azure Active Directory erişimini yapılandırma hakkında daha fazla bilgi için bkz. [Azure Active Directory Kimlik Doğrulamasını Kullanarak SQL Veritabanı’na veya SQL Veri Ambarı’na Bağlanma](sql-database-aad-authentication.md) ve [SQL Veritabanı ve SQL Veri Ambarı ile Azure AD MFA kullanımı için SSMS desteği](sql-database-ssms-mfa-authentication.md).

**Sunucu Yöneticisi** ve **Azure AD yönetici** hesapları aşağıdaki özelliklere sahiptir:

- Yalnızca sunucudaki herhangi bir SQL veritabanına otomatik olarak bağlanabilecek hesaplardır. (Diğer hesapların, bir kullanıcı veritabanına bağlanabilmek için veritabanının sahibi olmaları veya kullanıcı veritabanında kullanıcı hesabına sahip olmaları gerekir.)
- Bu hesaplar kullanıcı veritabanlarına `dbo` kullanıcısı olarak girer ve kullanıcı veritabanlarında tüm izinlere sahip olur. (Kullanıcı veritabanının sahibi de veritabanına `dbo` kullanıcısı olarak girer.) 
- `master` veritabanını `dbo` Kullanıcı olarak girmeyin ve ana üzerinde sınırlı izinlere sahip olmanız gerekir. 
- , SQL veritabanında kullanılamayan standart SQL Server `sysadmin` sabit sunucu rolünün üyesi **değil** .  
- Veritabanları, oturum açmalar, ana öğe ve sunucu düzeyinde IP güvenlik duvarı kuralları oluşturabilir, değiştirebilir ve bırakabilir.
- `dbmanager` ve `loginmanager` rollerine üye ekleyebilir ve kaldırabilir.
- `sys.sql_logins` sistem tablosunu görüntüleyebilir.
- Yeniden adlandırılamaz.
- Azure AD yönetici hesabını değiştirmek için portalını veya Azure CLı 'yi kullanın.
- Sunucu Yöneticisi hesabı daha sonra değiştirilemez.

### <a name="configuring-the-firewall"></a>Güvenlik duvarını yapılandırma

Sunucu düzeyinde güvenlik duvarı tek bir IP adresi veya aralığı için yapılandırıldığında **SQL sunucu yöneticisi** ve **Azure Active Directory yöneticisi**, ana veritabanına ve tüm kullanıcı veritabanlarına bağlanabilir. İlk sunucu düzeyi güvenlik duvarı [Azure portalı](sql-database-single-database-get-started.md) üzerinden [PowerShell](sql-database-powershell-samples.md) veya [REST API](https://msdn.microsoft.com/library/azure/dn505712.aspx) kullanılarak yapılandırılabilir. Bağlantı kurulduktan sonra [Transact-SQL](sql-database-configure-firewall-settings.md)kullanılarak ek sunucu düzeyi IP güvenlik duvarı kuralları da yapılandırılabilir.

### <a name="administrator-access-path"></a>Yönetici erişim yolu

Sunucu düzeyi güvenlik duvarı doğru şekilde yapılandırıldığında **SQL sunucu yöneticisi** ve **Azure Active Directory yöneticisi**, SQL Server Management Studio veya SQL Server Veri Araçları gibi istemci araçlarını kullanarak bağlantı kurabilir. Tüm özellikler ve yetenekler yalnızca en güncel araçlar tarafından sunulur. Aşağıdaki şemada iki yönetici hesabı için tipik yapılandırma gösterilmektedir.

![iki yönetim hesabının yapılandırılması](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Yöneticiler, sunucu düzeyi güvenlik duvarındaki açık bağlantı noktalarından birini kullanarak tüm SQL Veritabanlarına bağlanabilir.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>SQL Server Management Studio kullanarak bir veritabanına bağlanma

Sunucu, veritabanı, sunucu düzeyinde IP güvenlik duvarı kuralları oluşturma ve bir veritabanını sorgulamak için SQL Server Management Studio kullanma hakkında bilgi için, bkz [. Azure Portal ve SQL kullanarak Azure SQL veritabanı sunucularını, veritabanlarını ve güvenlik duvarı kurallarını kullanmaya başlama Sunucu Management Studio](sql-database-single-database-get-started.md).

> [!IMPORTANT]
> Microsoft Azure ve SQL Veritabanı güncelleştirmeleriyle aynı sürümde olmak için her zaman en güncel Management Studio sürümünü kullanmanız önerilir. [SQL Server Management Studio’yu güncelleyin](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="additional-server-level-administrative-roles"></a>Ek sunucu düzeyinde yönetim rolleri

>[!IMPORTANT]
>Bu roller **Azure**SQL veritabanı 'na özel olduğundan, bu bölüm **Azure SQL veritabanı yönetilen örneği** için geçerlidir.

Daha önce anlatılan sunucu düzeyi yönetim rollerine ek olarak SQL Veritabanı, ana veritabanında iki adet kısıtlı yönetim rolü sunar. Bu rollere, veritabanı oluşturma veya oturum açma bilgilerini yönetme izinleri veren kullanıcı hesapları eklenebilir.

### <a name="database-creators"></a>Veritabanı oluşturucuları

Bu yönetici rollerinden biri, **dbmanager** rolüdür. Bu rolün üyeleri yeni veritabanları oluşturabilir. Bu rolü kullanmak için `master` veritabanında bir kullanıcı oluşturmanız ve bu kullanıcıyı **dbmanager** veritabanı rolüne eklemeniz gerekir. Bir veritabanı oluşturmak için, Kullanıcı `master` veritabanında SQL Server oturum açma veya Azure Active Directory Kullanıcı tabanlı veritabanı kullanıcısı olan bir kullanıcı olmalıdır.

1. Yönetici hesabı kullanarak `master` veritabanına bağlanın.
2. [Create LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) ifadesini kullanarak SQL Server bir kimlik doğrulaması oturum açma oluşturun. Örnek deyim:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Oturum açma bilgileri veya bağımsız veritabanı kullanıcısı oluştururken güçlü bir parola kullanın. Daha fazla bilgi için bkz. [Güçlü Parolalar](https://msdn.microsoft.com/library/ms161962.aspx).

   Performansı artırmak için oturum açma bilgileri (sunucu düzeyi asıl hesaplar) veritabanı düzeyinde geçici olarak önbelleğe alınır. Kimlik doğrulaması önbelleğini yenilemek için bkz. [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

3. `master` veritabanında kullanıcı [Oluştur](https://msdn.microsoft.com/library/ms173463.aspx) ifadesini kullanarak bir kullanıcı oluşturun. Kullanıcı Azure Active Directory bir kimlik doğrulaması, veritabanı kullanıcısı (ortamınızı Azure AD kimlik doğrulaması için yapılandırdıysanız) veya bir SQL Server kimlik doğrulaması içeren veritabanı kullanıcısı veya bir SQL Server tabanlı SQL Server kimlik doğrulaması kullanıcısı olabilir kimlik doğrulaması oturum açma (önceki adımda oluşturulur.) Örnek deyimler:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. [Alter role](https://msdn.microsoft.com/library/ms189775.aspx) deyimini kullanarak `master` yeni kullanıcıyı **DBManager** veritabanı rolüne ekleyin. Örnek deyimler:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```

   > [!NOTE]
   > dbmanager, ana veritabanındaki bir veritabanı rolü olduğu için bu role yalnızca veritabanı kullanıcısı ekleyebilirsiniz. Veritabanı düzeyindeki role sunucu düzeyinde oturum açma bilgisi ekleyemezsiniz.

5. Gerekirse, yeni kullanıcının bağlantı kurabilmesi için bir güvenlik duvarı kuralı yapılandırın. (Yeni kullanıcı, mevcut bir güvenlik duvarı kuralı kapsamında olabilir.)

Artık Kullanıcı `master` veritabanına bağlanabilir ve yeni veritabanları oluşturabilir. Veritabanını oluşturan hesap, veritabanının sahibi olur.

### <a name="login-managers"></a>Oturum açma yöneticileri

Diğer yönetim rolü ise oturum açma yöneticisi rolüdür. Bu rolün üyeleri ana veritabanında yeni oturum açma bilgileri oluşturabilir. İsterseniz, aynı adımları uygulayarak (oturum açma bilgileri oluşturmak, kullanıcı oluşturmak ve **loginmanager** rolüne kullanıcı eklemek) bir kullanıcının ana veritabanında yeni oturum açma bilgileri oluşturmasını sağlayabilirsiniz. Microsoft, oturum açma bilgisi tabanlı kullanıcılar yerine, veritabanı düzeyinde kimlik doğrulaması yapan bağımsız veritabanı kullanıcılarından yararlanılmasını önerir. Dolayısıyla, oturum açma bilgileri genellikle gerekli değildir. Daha fazla bilgi için bkz. [Bağımsız Veritabanı Kullanıcıları - Veritabanınızı Taşınabilir Hale Getirme](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="non-administrator-users"></a>Yönetici olmayan kullanıcılar

Çoğu durumda, yönetici olmayan kullanıcıların ana veritabanına erişmesi gerekmez. [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx) deyimini kullanarak veritabanı düzeyinde bağımsız veritabanı kullanıcıları oluşturun. Kullanıcı Azure Active Directory bir kimlik doğrulaması, veritabanı kullanıcısı (ortamınızı Azure AD kimlik doğrulaması için yapılandırdıysanız) veya bir SQL Server kimlik doğrulaması içeren veritabanı kullanıcısı veya bir SQL Server tabanlı SQL Server kimlik doğrulaması kullanıcısı olabilir kimlik doğrulaması oturum açma (önceki adımda oluşturulur.) Daha fazla bilgi için bkz. [Kapsanan Veritabanı kullanıcıları-veritabanınızı taşınabilir hale getirme](https://msdn.microsoft.com/library/ff929188.aspx). 

Kullanıcı oluşturmak için veritabanına bağlanın ve aşağıdaki örneklere benzer deyimleri çalıştırın:

```sql
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Başlangıçta veritabanı yöneticilerinden yalnızca biri veya veritabanının sahibi kullanıcı oluşturabilir. Daha fazla kullanıcıya yeni kullanıcı oluşturma yetkisi vermek için şunun gibi bir deyim kullanarak `ALTER ANY USER` izni verin:

```sql
GRANT ALTER ANY USER TO Mary;
```

Başka kullanıcılara veritabanına tam denetim sağlamak için, **db_owner** sabit veritabanı rolünün bir üyesi yapın.

Azure SQL veritabanı 'nda `ALTER ROLE` ifadesini kullanın.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

Azure SQL veri ambarı 'nda [EXEC sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)kullanın.
```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```


> [!NOTE]
> SQL veritabanı sunucusu oturum açma bilgilerini temel alan bir veritabanı kullanıcısı oluşturmanın yaygın nedenlerinden biri, birden çok veritabanına erişmesi gereken kullanıcılar içindir. Kapsanan veritabanı kullanıcıları bağımsız varlıklar olduğundan, her veritabanı kendi kullanıcısını ve kendi parolasını tutar. Bu, kullanıcının her bir veritabanı için her parolayı hatırlamaları gerektiği için ek yüke neden olabilir ve birçok veritabanı için birden çok parolayı değiştirmek zorunda kalmazsa, bu, eklenebilir hale gelebilir. Ancak, SQL Server oturumlarını ve yüksek kullanılabilirliği (etkin coğrafi çoğaltma ve yük devretme grupları) kullanırken, SQL Server oturum açma işlemleri her bir sunucuda el ile ayarlanmalıdır. Aksi halde, bir yük devretme gerçekleştikten sonra veritabanı kullanıcısı artık sunucu oturum açma bilgileriyle eşlenmeyecektir ve veritabanı yük devretmesine erişemeyecektir. Coğrafi çoğaltma için oturum açma yapılandırma hakkında daha fazla bilgi için lütfen bkz. [coğrafi geri yükleme veya yük devretme Için Azure SQL veritabanı güvenliğini yapılandırma ve yönetme](sql-database-geo-replication-security-config.md).

### <a name="configuring-the-database-level-firewall"></a>Veritabanı düzeyinde güvenlik duvarını yapılandırma

En iyi uygulamalardan biri, yönetici olmayan kullanıcıların kullandıkları veritabanlarına yalnızca güvenlik duvarı aracılığıyla erişim sağlamasıdır. Bu kullanıcıların IP adreslerini sunucu düzeyinde güvenlik duvarından yetkilendirip tüm veritabanlarına erişim vermek yerine [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) deyimini kullanarak veritabanı düzeyinde güvenlik duvarını yapılandırabilirsiniz. Veritabanı düzeyinde güvenlik duvarı portal kullanılarak yapılandırılamaz.

### <a name="non-administrator-access-path"></a>Yönetici olmayan kullanıcılar için erişim yolu

Veritabanı düzeyinde güvenlik duvarı doğru şekilde yapılandırıldığında veritabanı kullanıcıları SQL Server Management Studio veya SQL Server Veri Araçları gibi istemci araçlarını kullanarak bağlantı kurabilir. Tüm özellikler ve yetenekler yalnızca en güncel araçlar tarafından sunulur. Aşağıdaki şema, yönetici olmayan kullanıcılar için tipik erişim yolunu göstermektedir.

![Yönetici olmayan kullanıcılar için erişim yolu](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Gruplar ve roller

Verimli erişim yönetimi için bireysel kullanıcılar yerine gruplara ve rollere atanan izinler kullanılır. 

- Azure Active Directory kimlik doğrulaması kullandığınızda Azure Active Directory kullanıcılarını bir Azure Active Directory grubuna ekleyin. Grup için bir bağımsız veritabanı kullanıcısı oluşturun. Bir veya daha fazla veritabanı kullanıcılarını bir [veritabanı rolüne](https://msdn.microsoft.com/library/ms189121) ekleyin ve [izinleri](https://msdn.microsoft.com/library/ms191291.aspx) veritabanı rolüne atayın.

- SQL Server kimlik doğrulamasını kullanırken veritabanında bağımsız veritabanı kullanıcılarını oluşturun. Bir veya daha fazla veritabanı kullanıcılarını bir [veritabanı rolüne](https://msdn.microsoft.com/library/ms189121) ekleyin ve [izinleri](https://msdn.microsoft.com/library/ms191291.aspx) veritabanı rolüne atayın.

Veritabanı rolleri **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** ve **db_denydatareader** gibi yerleşik roller olabilir. Birkaç kullanıcıya tam izin vermek için genelde **db_owner** kullanılır. Diğer sabit veritabanı rolleri, geliştirme aşamasında basit bir veritabanını hızlı bir şekilde kullanıma almak için kullanışlıdır ancak çoğu üretim veritabanı için önerilmez. Örneğin **db_datareader** sabit veritabanı rolü, veritabanındaki tüm tablolara okuma izni verir ve bu durum genelde ihtiyaç duyulandan fazlasıdır. [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) deyimini kullanarak kendi kullanıcı tanımlı veritabanı rollerinizi oluşturmak ve dikkatli bir şekilde her role ihtiyaç duyulan en alt düzeyde izinleri vermek çok daha iyidir. Birden fazla rolün üyesi olan kullanıcılar, tüm rollerin izinlerine sahip olur.

## <a name="permissions"></a>İzinler

SQL Veritabanında ayrı ayrı verilebilen veya reddedilebilen 100'den fazla izin vardır. Bu izinlerin çoğu iç içe geçmiş haldedir. Örneğin, bir şemada için verilen `UPDATE` izni, o şema içindeki tüm tablolar için `UPDATE` iznini de içerir. Çoğu izin sisteminde olduğu gibi bir iznin reddedilmesi, aynı iznin verilme durumunu geçersiz kılar. İç içe geçmiş yapısı ve izin sayısı nedeniyle, veritabanınızı doğru şekilde korumak için uygun bir izin sistemi tasarlamak uzun ve dikkatli bir çalışma gerektirebilir. [İzinler (Veritabanı Altyapısı)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) ile başlayın ve izinlerin [poster boyutundaki tablosunu](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) inceleyin.


### <a name="considerations-and-restrictions"></a>Dikkat edilmesi gerekenler ve kısıtlamalar

SQL Veritabanında oturum açma bilgilerini ve kullanıcıları yönetirken aşağıdaki noktalara dikkat edin:

- **deyimlerini yürütürken**ana`CREATE/ALTER/DROP DATABASE` veritabanına bağlanmış olmanız gerekir.   
- **Sunucu yöneticisi** oturum açma bilgilerine karşılık gelen veritabanı kullanıcısı değiştirilemez veya kaldırılamaz. 
- **Sunucu yöneticisi** oturum açma bilgilerinin varsayılan dili ABD-İngilizce olarak belirlenmiştir.
- Yalnızca yöneticiler (**Sunucu yöneticisi** oturum açma bilgileri veya Azure AD yöneticisi) ve **ana** veritabanındaki **dbmanager** veritabanı rolünün üyeleri `CREATE DATABASE` ve `DROP DATABASE` deyimlerini yürütme iznine sahiptir.
- `CREATE/ALTER/DROP LOGIN` deyimlerini yürütürken ana veritabanına bağlanmış olmanız gerekir. Ancak oturum açma bilgilerinin kullanılması önerilmez. Bunun yerine bağımsız veritabanı kullanıcılarını kullanmanız önerilir.
- Bir kullanıcı veritabanına bağlanmak için bağlantı dizesinde veritabanının adını belirtmeniz gerekir.
- Yalnızca sunucu düzeyi asıl oturum açma bilgisi ve **ana** veritabanındaki **loginmanager** veritabanı rolünün üyeleri `CREATE LOGIN`,`ALTER LOGIN` ve `DROP LOGIN` deyimlerini yürütme iznine sahiptir.
- Bir ADO.NET uygulamasında `CREATE/ALTER/DROP LOGIN` ve `CREATE/ALTER/DROP DATABASE` deyimlerini yürütürken parametreli komutların kullanılmasına izin verilmez. Daha fazla bilgi için bkz. [Komutlar ve Parametreler](https://msdn.microsoft.com/library/ms254953.aspx).
- `CREATE/ALTER/DROP DATABASE` ve `CREATE/ALTER/DROP LOGIN` deyimlerini yürütürken bu deyimlerin her birinin bir Transact-SQL toplu işindeki tek deyim olması gerekir. Aksi takdirde bir hata oluşur. Örneğin, aşağıdaki Transact-SQL veritabanının mevcut olup olmadığını kontrol eder. Mevcutsa, veritabanını kaldırmak için bir `DROP DATABASE` deyimi çağrılır. `DROP DATABASE` deyimi toplu işteki tek deyim olmadığından aşağıdaki Transact-SQL deyiminin yürütülmesi hatayla sonuçlanacaktır.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```
  
  Bunun yerine, aşağıdaki Transact-SQL ifadesini kullanın:
  
  ```sql
  DROP DATABASE IF EXISTS [database_name]
  ```

- `CREATE USER` deyimini `FOR/FROM LOGIN` seçeneğiyle yürütürken bunun bir Transact-SQL toplu işindeki tek deyim olması gerekir.
- `ALTER USER` deyimini `WITH LOGIN` seçeneğiyle yürütürken bunun bir Transact-SQL toplu işindeki tek deyim olması gerekir.
- Bir kullanıcıda `CREATE/ALTER/DROP` işlemini gerçekleştirmek için veritabanında `ALTER ANY USER` izni gerekir.
- Bir veritabanı rolünün sahibi başka bir kullanıcıyı ilgili veritabanı rolüne eklemeye veya bu rolden kaldırmaya çalıştığında şu hata ortaya çıkabilir: **Kullanıcı veya rol "Ad" bu veritabanında mevcut değil.** Bu hatanın nedeni, kullanıcının rol sahibine görünür olmamasıdır. Bu sorunu çözmek için rol sahibine kullanıcı için `VIEW DEFINITION` iznini verin. 


## <a name="next-steps"></a>Sonraki adımlar

- Güvenlik duvarı kuralları hakkında daha fazla bilgi için bkz. [Azure SQL Veritabanı Güvenlik Duvarı](sql-database-firewall-configure.md).
- SQL Veritabanı güvenlik özelliklerinin tümüne genel bakış için bkz. [SQL Veritabanı güvenliğine genel bakış](sql-database-security-overview.md).
- Öğretici için bkz. [Azure SQL veritabanınızı güvenli hale getirme](sql-database-security-tutorial.md).
- Görünümler ve saklı yordamlar hakkında bilgi için bkz. [Görünüm ve saklı yordam oluşturma](https://msdn.microsoft.com/library/ms365311.aspx)
- Bir veritabanı nesnesine erişim verme hakkında bilgi için bkz. [Bir veritabanı nesnesine erişim verme](https://msdn.microsoft.com/library/ms365327.aspx)
