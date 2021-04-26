---
title: SQL Kimlik Doğrulaması
description: Azure SYNAPSE Analytics 'te SQL kimlik doğrulaması hakkında bilgi edinin.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 505c0de5a508bd97b10091451116ec3670a20493
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677541"
---
# <a name="sql-authentication"></a>SQL Kimlik Doğrulaması

Azure SYNAPSE Analytics, kaynak tüketiminizi denetlemenizi sağlayan iki SQL form etmenlerine sahiptir. Bu makalede, iki form faktöründe Kullanıcı kimlik doğrulamasının nasıl kontrol olduğu açıklanmaktadır.

SYNAPSE SQL 'e yetki vermek için iki yetkilendirme türü kullanabilirsiniz:

- Azure Active Directory yetkilendirmesi
- SQL yetkilendirmesi

Azure Active Directory, Kullanıcı yönetimi için tek bir yer kullanmanıza olanak sağlar. SQL yetkilendirmesi eski uygulamaların SYNAPSE SQL 'i iyi tanıdık bir şekilde kullanmasına olanak sağlar.

## <a name="administrative-accounts"></a>Yönetim hesapları

Yönetici işlevlerine sahip iki yönetici hesabı (**Sunucu yöneticisi** ve **Active Directory yöneticisi**) vardır. SQL sunucunuz için bu yönetici hesaplarını belirlemek için, Azure portal açın ve SYNAPSE SQL 'nizin Özellikler sekmesine gidin.

![SQL Server Yöneticileri](./media/sql-authentication/sql-admins.png)

- **Sunucu yöneticisi**

  Bir Azure SYNAPSE Analizi oluşturduğunuzda, bir **Sunucu Yöneticisi oturum açma** adı vermelisiniz. SQL Server, bu hesabı ana veritabanında oturum açma bilgileri olarak oluşturur. Bu hesap SQL Server kimlik doğrulaması (kullanıcı adı ve parola) kullanarak bağlanır. Bu hesaplardan yalnızca biri mevcut olabilir.

- **Azure Active Directory yöneticisi**

  Ayrıca, Azure Active Directory’deki bir adet kişi veya güvenlik grubu hesabı da yönetici olarak yapılandırılabilir. Azure AD yöneticisi yapılandırmak isteğe bağlıdır, ancak Azure AD hesaplarını kullanmak istiyorsanız SYNAPSE SQL 'e bağlanmak için bir Azure **ad Yöneticisi yapılandırılmalıdır** .

**Sunucu Yöneticisi** ve **Azure AD yönetici** hesapları aşağıdaki özelliklere sahiptir:

- Yalnızca sunucudaki herhangi bir SQL veritabanına otomatik olarak bağlanabilecek hesaplardır. (Diğer hesapların, bir kullanıcı veritabanına bağlanabilmek için veritabanının sahibi olmaları veya kullanıcı veritabanında kullanıcı hesabına sahip olmaları gerekir.)
- Bu hesaplar kullanıcı veritabanlarına `dbo` kullanıcısı olarak girer ve kullanıcı veritabanlarında tüm izinlere sahip olur. (Kullanıcı veritabanının sahibi de veritabanına `dbo` kullanıcısı olarak girer.)
- `master`Veritabanını Kullanıcı olarak girmeyin `dbo` ve ana üzerinde sınırlı izinlere sahip olmanız gerekir.
-  `sysadmin` , SQL veritabanında kullanılamayan standart SQL Server sabit sunucu rolünün üyesi değildir.  
- Veritabanları, oturum açmalar, ana öğe ve sunucu düzeyinde IP güvenlik duvarı kuralları oluşturabilir, değiştirebilir ve bırakabilir.
- , Ve rollerine üye ekleyebilir veya kaldırabilir `dbmanager` `loginmanager` .
- , `sys.sql_logins` Sistem tablosunu görüntüleyebilir.

## <a name="serverless-sql-pool"></a>[Sunucusuz SQL havuzu](#tab/serverless)

Sunucusuz SQL havuzuna erişimi olan kullanıcıları yönetmek için aşağıdaki yönergeleri kullanabilirsiniz.

Sunucusuz SQL havuzu 'nda oturum açmak için aşağıdaki sözdizimini kullanın:

```sql
CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
-- or
CREATE LOGIN Mary@domainname.net FROM EXTERNAL PROVIDER;
```
Oturum açma mevcut olduğunda, tek tek veritabanlarında sunucusuz SQL havuzu uç noktasındaki kullanıcıları oluşturabilir ve bu kullanıcılara gerekli izinleri verebilirsiniz. Bir kullanım oluşturmak için aşağıdaki sözdizimini kullanabilirsiniz:
```sql
CREATE USER Mary FROM LOGIN Mary;
-- or
CREATE USER Mary FROM LOGIN Mary@domainname.net;
-- or
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Oturum açma ve Kullanıcı oluşturulduktan sonra, haklar vermek için normal SQL Server sözdizimini kullanabilirsiniz.

## <a name="sql-pool"></a>[SQL havuzu](#tab/provisioned)

### <a name="administrator-access-path"></a>Yönetici erişim yolu

Sunucu düzeyi güvenlik duvarı doğru şekilde yapılandırıldığında **SQL sunucu yöneticisi** ve **Azure Active Directory yöneticisi**, SQL Server Management Studio veya SQL Server Veri Araçları gibi istemci araçlarını kullanarak bağlantı kurabilir. Tüm özellikler ve yetenekler yalnızca en güncel araçlar tarafından sunulur. 

Aşağıdaki diyagramda, iki yönetici hesabı için tipik bir yapılandırma gösterilmektedir:
 
![iki yönetim hesabının yapılandırılması](./media/sql-authentication/1sql-db-administrator-access.png)

Yöneticiler, sunucu düzeyi güvenlik duvarındaki açık bağlantı noktalarından birini kullanarak tüm SQL Veritabanlarına bağlanabilir.

### <a name="database-creators"></a>Veritabanı oluşturucuları

Bu yönetim rollerinden biri **DBManager** rolüdür. Bu rolün üyeleri yeni veritabanları oluşturabilir. Bu rolü kullanmak için `master` veritabanında bir kullanıcı oluşturmanız ve bu kullanıcıyı **dbmanager** veritabanı rolüne eklemeniz gerekir. 

Bir veritabanı oluşturmak için Kullanıcı, veritabanında bir SQL Server oturum açma `master` veya Azure Active Directory bir kullanıcıya dayalı veritabanı kullanıcısı olan bir kullanıcı olmalıdır.

1. Yönetici hesabı kullanarak `master` veritabanına bağlanın.
2. [Create LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true) ifadesini kullanarak SQL Server bir kimlik doğrulaması oturum açma oluşturun. Örnek deyim:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Oturum açma bilgileri veya bağımsız veritabanı kullanıcısı oluştururken güçlü bir parola kullanın. Daha fazla bilgi için bkz. [Güçlü Parolalar](/sql/relational-databases/security/strong-passwords?view=azure-sqldw-latest&preserve-view=true).

   Performansı artırmak için oturum açma bilgileri (sunucu düzeyi asıl hesaplar) veritabanı düzeyinde geçici olarak önbelleğe alınır. Kimlik doğrulaması önbelleğini yenilemek için bkz. [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql?view=azure-sqldw-latest&preserve-view=true).

3. [Create User](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest&preserve-view=true) ifadesini kullanarak bir veritabanı kullanıcısı oluşturun. Kullanıcı Azure Active Directory bir kimlik doğrulaması, veritabanı kullanıcısı (ortamınızı Azure AD kimlik doğrulaması için yapılandırdıysanız) veya bir SQL Server kimlik doğrulaması içeren veritabanı kullanıcısı veya bir SQL Server kimlik doğrulaması oturum açma SQL Server kimlik doğrulaması kullanıcısı (önceki adımda oluşturulan) olabilir. Örnek deyimler:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Yeni kullanıcıyı, sp_addrolemember yordamını kullanarak içindeki **DBManager** veritabanı rolüne ekleyin `master` (SQL [](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=azure-sqldw-latest&preserve-view=true) sağlanan SQL 'de [alter role](/sql/t-sql/statements/alter-role-transact-sql?view=azure-sqldw-latest&preserve-view=true) ifadesinin desteklenmediğini unutmayın). Örnek deyimler:

   ```sql
   EXEC sp_addrolemember 'dbmanager', 'Mary'; 
   EXEC sp_addrolemember 'dbmanager', 'mike@contoso.com]'; 
   ```

   > [!NOTE]
   > dbmanager, ana veritabanındaki bir veritabanı rolü olduğu için bu role yalnızca veritabanı kullanıcısı ekleyebilirsiniz. Veritabanı düzeyindeki role sunucu düzeyinde oturum açma bilgisi ekleyemezsiniz.

5. Gerekirse, yeni kullanıcının bağlantı kurabilmesi için bir güvenlik duvarı kuralı yapılandırın. (Yeni kullanıcı, mevcut bir güvenlik duvarı kuralı kapsamında olabilir.)

Böylece Kullanıcı `master` veritabanına bağlanabilir ve yeni veritabanları oluşturabilir. Veritabanını oluşturan hesap, veritabanının sahibi olur.

### <a name="login-managers"></a>Oturum açma yöneticileri

Diğer yönetim rolü ise oturum açma yöneticisi rolüdür. Bu rolün üyeleri ana veritabanında yeni oturum açma bilgileri oluşturabilir. İsterseniz, aynı adımları uygulayarak (oturum açma bilgileri oluşturmak, kullanıcı oluşturmak ve **loginmanager** rolüne kullanıcı eklemek) bir kullanıcının ana veritabanında yeni oturum açma bilgileri oluşturmasını sağlayabilirsiniz. Genellikle, Microsoft 'un, oturum açma bilgilerini temel alan kullanıcıları kullanmak yerine veritabanı düzeyinde kimlik doğrulayan kapsanan veritabanı kullanıcılarını kullanmasını önerdiği için oturum açmaları gerekmez. Daha fazla bilgi için bkz. [Bağımsız Veritabanı Kullanıcıları - Veritabanınızı Taşınabilir Hale Getirme](/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=azure-sqldw-latest&preserve-view=true).

---

## <a name="non-administrator-users"></a>Yönetici olmayan kullanıcılar

Genellikle, yönetici olmayan hesapların ana veritabanına erişmesi gerekmez. [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) deyimini kullanarak veritabanı düzeyinde bağımsız veritabanı kullanıcıları oluşturun. 

Kullanıcı Azure Active Directory kimlik doğrulaması bağımsız veritabanı kullanıcısı (ortamınızı Azure AD kimlik doğrulaması için yapılandırdıysanız) veya SQL Server kimlik doğrulaması bağımsız veritabanı kullanıcısı ya da SQL Server kimlik doğrulaması oturum açma bilgilerini kullanan SQL Server kimlik doğrulaması kullanıcısı (önceki adımda oluşturulan) olabilir.  

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

Azure SQL veritabanı veya SYNAPSE sunucusuz 'de, ifadesini kullanın `ALTER ROLE` .

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

Adanmış SQL havuzunda [EXEC sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=azure-sqldw-latest&preserve-view=true)kullanın.

```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```

> [!NOTE]
> Sunucu oturumuna dayalı bir veritabanı kullanıcısı oluşturmanın yaygın nedenlerinden biri, birden çok veritabanına erişmesi gereken kullanıcılar içindir. Kapsanan veritabanı kullanıcıları bağımsız varlıklar olduğundan, her veritabanı kendi kullanıcısını ve kendi parolasını tutar. Bu, kullanıcının her bir veritabanı için her parolayı hatırlamaları gerektiği için ek yüke neden olabilir ve birçok veritabanı için birden çok parolayı değiştirmek zorunda kalmazsa, bu, eklenebilir hale gelebilir. Ancak, SQL Server oturumlarını ve yüksek kullanılabilirliği (etkin coğrafi çoğaltma ve yük devretme grupları) kullanırken, SQL Server oturum açma işlemleri her bir sunucuda el ile ayarlanmalıdır. Aksi halde, bir yük devretme gerçekleştikten sonra veritabanı kullanıcısı artık sunucu oturum açma bilgileriyle eşlenmeyecektir ve veritabanı yük devretmesine erişemeyecektir. 

Coğrafi çoğaltma için oturum açma yapılandırma hakkında daha fazla bilgi için bkz.  [coğrafi geri yükleme veya yük devretme Için Azure SQL veritabanı güvenliğini yapılandırma ve yönetme](../../azure-sql/database/active-geo-replication-security-configure.md).

### <a name="configuring-the-database-level-firewall"></a>Veritabanı düzeyinde güvenlik duvarını yapılandırma

En iyi uygulamalardan biri, yönetici olmayan kullanıcıların kullandıkları veritabanlarına yalnızca güvenlik duvarı aracılığıyla erişim sağlamasıdır. Bu kullanıcıların IP adreslerini sunucu düzeyinde güvenlik duvarından yetkilendirip tüm veritabanlarına erişim vermek yerine [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database?view=azure-sqldw-latest&preserve-view=true) deyimini kullanarak veritabanı düzeyinde güvenlik duvarını yapılandırabilirsiniz. Veritabanı düzeyinde güvenlik duvarı portal kullanılarak yapılandırılamaz.

### <a name="non-administrator-access-path"></a>Yönetici olmayan kullanıcılar için erişim yolu

Veritabanı düzeyinde güvenlik duvarı doğru şekilde yapılandırıldığında veritabanı kullanıcıları SQL Server Management Studio veya SQL Server Veri Araçları gibi istemci araçlarını kullanarak bağlantı kurabilir. Tüm özellikler ve yetenekler yalnızca en güncel araçlar tarafından sunulur. Aşağıdaki şema, yönetici olmayan kullanıcılar için tipik erişim yolunu göstermektedir.

![Yönetici olmayan kullanıcılar için erişim yolu](./media/sql-authentication/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Gruplar ve roller

Verimli erişim yönetimi için bireysel kullanıcılar yerine gruplara ve rollere atanan izinler kullanılır.

- Azure Active Directory kimlik doğrulaması kullandığınızda Azure Active Directory kullanıcılarını bir Azure Active Directory grubuna ekleyin. Grup için bir bağımsız veritabanı kullanıcısı oluşturun. Bir veya daha fazla veritabanı kullanıcılarını bir [veritabanı rolüne](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true) ekleyin ve [izinleri](/sql/relational-databases/security/permissions-database-engine?view=azure-sqldw-latest&preserve-view=true) veritabanı rolüne atayın.

- SQL Server kimlik doğrulamasını kullanırken veritabanında bağımsız veritabanı kullanıcılarını oluşturun. Bir veya daha fazla veritabanı kullanıcılarını bir [veritabanı rolüne](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true) ekleyin ve [izinleri](/sql/relational-databases/security/permissions-database-engine?view=azure-sqldw-latest&preserve-view=true) veritabanı rolüne atayın.

Veritabanı rolleri **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** ve **db_denydatareader** gibi yerleşik roller olabilir. Birkaç kullanıcıya tam izin vermek için genelde **db_owner** kullanılır. Diğer sabit veritabanı rolleri, geliştirme aşamasında basit bir veritabanını hızlı bir şekilde kullanıma almak için kullanışlıdır ancak çoğu üretim veritabanı için önerilmez. 

Örneğin **db_datareader** sabit veritabanı rolü, veritabanındaki tüm tablolara okuma izni verir ve bu durum genelde ihtiyaç duyulandan fazlasıdır. 

Kullanıcı tanımlı veritabanı rollerinizi oluşturmak ve her role iş ihtiyacı için gereken en az izni vermek için [Create role](/sql/t-sql/statements/create-role-transact-sql) ifadesini kullanmak çok daha iyidir. Birden fazla rolün üyesi olan kullanıcılar, tüm rollerin izinlerine sahip olur.

## <a name="permissions"></a>İzinler

SQL Veritabanında ayrı ayrı verilebilen veya reddedilebilen 100'den fazla izin vardır. Bu izinlerin çoğu iç içe geçmiş haldedir. Örneğin, bir şemada için verilen `UPDATE` izni, o şema içindeki tüm tablolar için `UPDATE` iznini de içerir. Çoğu izin sisteminde olduğu gibi bir iznin reddedilmesi, aynı iznin verilme durumunu geçersiz kılar. 

İç içe geçmiş yapısı ve izin sayısı nedeniyle, veritabanınızı doğru şekilde korumak için uygun bir izin sistemi tasarlamak uzun ve dikkatli bir çalışma gerektirebilir. 

[İzinler (Veritabanı Altyapısı)](/sql/relational-databases/security/permissions-database-engine) ile başlayın ve izinlerin [poster boyutundaki tablosunu](/sql/relational-databases/security/media/database-engine-permissions.png) inceleyin.

### <a name="considerations-and-restrictions"></a>Dikkat edilmesi gerekenler ve kısıtlamalar

SQL veritabanında oturum açma işlemlerini ve kullanıcıları yönetirken aşağıdaki noktaları göz önünde bulundurun:

- Deyimlerini yürütürken **ana** veritabanına bağlı olmanız gerekir `CREATE/ALTER/DROP DATABASE` .
- **Sunucu Yöneticisi** oturum açma bilgilerine karşılık gelen veritabanı kullanıcısı değiştirilemez veya bırakılamaz.
- **Sunucu yöneticisi** oturum açma bilgilerinin varsayılan dili ABD-İngilizce olarak belirlenmiştir.
- Yalnızca yöneticiler (**Sunucu yöneticisi** oturum açma bilgileri veya Azure AD yöneticisi) ve **ana** veritabanındaki **dbmanager** veritabanı rolünün üyeleri `CREATE DATABASE` ve `DROP DATABASE` deyimlerini yürütme iznine sahiptir.
- `CREATE/ALTER/DROP LOGIN` deyimlerini yürütürken ana veritabanına bağlanmış olmanız gerekir. Ancak oturum açma bilgilerinin kullanılması önerilmez. Bunun yerine bağımsız veritabanı kullanıcılarını kullanmanız önerilir.
- Bir kullanıcı veritabanına bağlanmak için bağlantı dizesinde veritabanının adını belirtmeniz gerekir.
- Yalnızca sunucu düzeyi asıl oturum açma bilgisi ve **ana** veritabanındaki **loginmanager** veritabanı rolünün üyeleri `CREATE LOGIN`,`ALTER LOGIN` ve `DROP LOGIN` deyimlerini yürütme iznine sahiptir.
- `CREATE/ALTER/DROP LOGIN` `CREATE/ALTER/DROP DATABASE` Bir ADO.NET uygulamasında ve deyimlerini yürütürken parametreli komutların kullanılmasına izin verilmez. Daha fazla bilgi için bkz. [Komutlar ve Parametreler](/dotnet/framework/data/adonet/commands-and-parameters).
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
- Bir veritabanı rolünün sahibi başka bir kullanıcıyı ilgili veritabanı rolüne eklemeye veya bu rolden kaldırmaya çalıştığında şu hata ortaya çıkabilir: **Kullanıcı veya rol "Ad" bu veritabanında mevcut değil.** Bu hata, Kullanıcı Sahibe görünür olmadığı için oluşur. Bu sorunu çözmek için rol sahibine kullanıcı için `VIEW DEFINITION` iznini verin. 

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Bağımsız Veritabanı Kullanıcıları - Veritabanınızı Taşınabilir Hale Getirme](/sql/relational-databases/security/contained-database-users-making-your-database-portable).
