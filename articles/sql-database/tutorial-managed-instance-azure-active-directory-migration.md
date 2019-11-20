---
title: T-SQL kullanarak SQL Server Windows kullanıcılarını ve gruplarını yönetilen örneğe geçirme
description: Şirket içi Windows kullanıcılarını ve gruplarını yönetilen örneğe SQL Server geçirme hakkında bilgi edinin
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: 3ed4e4b1d37a9705378281ca74b53a6b60713d97
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73807169"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>Öğretici: T-SQL DDL sözdizimini kullanarak şirket içi Windows kullanıcılarını ve gruplarını Azure SQL veritabanı yönetilen örneği 'ne SQL Server geçirme

> [!NOTE]
> Bu makaledeki kullanıcıları ve grupları yönetilen örneğe geçirmek için kullanılan söz dizimi **genel önizlemededir**.

Bu makale, SQL Server şirket içi Windows kullanıcılarınızı ve gruplarınızı T-SQL söz dizimini kullanarak mevcut bir Azure SQL veritabanı yönetilen örneğine geçirme sürecinde size kılavuzluk ediyor.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> - SQL Server için oturum açma bilgileri oluşturma
> - Geçiş için bir test veritabanı oluşturma
> - Oturum açma bilgileri, kullanıcılar ve roller oluşturma
> - Veritabanınızı yönetilen örneğe yedekleme ve geri yükleme (mı)
> - Kullanıcıları ALTER USER söz dizimini kullanarak el ile MI?
> - Yeni eşlenen kullanıcılarla kimlik doğrulamasını test etme

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki önkoşullar geçerlidir:

- Windows etki alanı Azure Active Directory (Azure AD) ile federe olur.
- Kullanıcı/Grup oluşturmak için Active Directory erişim.
- Şirket içi ortamınızda mevcut bir SQL Server.
- Mevcut bir yönetilen örnek. Bkz. [hızlı başlangıç: Azure SQL veritabanı yönetilen örneği oluşturma](sql-database-managed-instance-get-started.md).
  - Yönetilen örnekteki bir `sysadmin` Azure AD oturum açmaları oluşturmak için kullanılmalıdır.
- [Yönetilen örnek için bir Azure AD yöneticisi oluşturun](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).
- Ağınız içindeki yönetilen örneğinize bağlanabilirsiniz. Daha fazla bilgi için aşağıdaki makalelere bakın: 
    - [Uygulamanızı Azure SQL veritabanı yönetilen örneğine bağlama](sql-database-managed-instance-connect-app.md)
    - [Hızlı başlangıç: şirket içi Azure SQL veritabanı yönetilen örneği ile noktadan siteye bağlantı yapılandırma](sql-database-managed-instance-configure-p2s.md)
    - [Azure SQL Veritabanı yönetilen örneğinde genel uç noktayı yapılandırma](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>T-SQL DDL sözdizimi

Aşağıda, Azure AD kimlik doğrulamasıyla yönetilen örneğe geçiş SQL Server şirket içi Windows kullanıcılarını ve gruplarını desteklemek için kullanılan T-SQL DDL sözdizimi aşağıda verilmiştir.

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>Bağımsız Değişkenler

_Etki_</br>
Kullanıcının etki alanı adını belirtir.

_Nitelen_</br>
Veritabanı içinde tanımlanan kullanıcının adını belirtir.

_= loginName\@domainName.com_</br>
Bir kullanıcıyı Azure AD oturum açmayla yeniden eşler

_adýdýr_</br>
Veritabanı içinde tanımlanan grubun adını belirtir.

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>1\. kısım: şirket içi kullanıcılar ve gruplar SQL Server için oturum açma oluşturma

> [!IMPORTANT]
> Aşağıdaki sözdizimi SQL Server bir Kullanıcı ve grup oturumu oluşturur. Aşağıdaki sözdizimini yürütmeden önce, Kullanıcı ve grubun Active Directory (AD) içinde mevcut olduğundan emin olmanız gerekir. </br> </br>
> Kullanıcılar: testUser1, testGroupUser </br>
> Grup: geçiş-testGroupUser 'ın AD içindeki geçiş grubuna ait olması gerekir

Aşağıdaki örnek, _aadsqlmı_etki alanı altında _testUser1_ adlı bir hesap için SQL Server bir oturum açma oluşturur. 

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases
 
use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;   
go; 

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser]. 
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;    
go; 


-- Check logins were created
select * from sys.server_principals; 
go; 
```

Bu test için bir veritabanı oluşturun.

```sql
-- Create a database called [migration]
create database migration 
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>2\. Bölüm: Windows kullanıcıları ve grupları oluşturun, ardından Roller ve izinler ekleyin

Test kullanıcısını oluşturmak için aşağıdaki sözdizimini kullanın.

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

Kullanıcı izinlerini denetleyin:

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

Bir rol oluşturun ve test kullanıcısını bu role atayın:

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

Belirli bir role atanan kullanıcı adlarını göstermek için aşağıdaki sorguyu kullanın:

```sql
-- Display user name assigned to a specific role 
SELECT DP1.name AS DatabaseRoleName,    
   isnull (DP2.name, 'No members') AS DatabaseUserName    
 FROM sys.database_role_members AS DRM   
 RIGHT OUTER JOIN sys.database_principals AS DP1   
   ON DRM.role_principal_id = DP1.principal_id   
 LEFT OUTER JOIN sys.database_principals AS DP2   
   ON DRM.member_principal_id = DP2.principal_id   
WHERE DP1.type = 'R' 
ORDER BY DP1.name; 
```

Bir grup oluşturmak için aşağıdaki sözdizimini kullanın. Ardından grubu `db_owner`role ekleyin.

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration]; 
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration'; 
go 

--Check the db_owner role for 'aadsqlmi\migration' group 
select is_rolemember('db_owner', 'aadsqlmi\migration')   
go 
-- Output  ( 1 means YES) 
```

Aşağıdaki sözdizimini kullanarak bir test tablosu oluşturun ve bazı verileri ekleyin:

```sql
-- Create a table and add data 
create table test ( a int, b int); 
go 

insert into test values (1,10) 
go 

-- Check the table values
select * from test; 
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>3\. kısım: bireysel kullanıcı veritabanını yönetilen örneğe yedekleme ve geri yükleme

[Yedekleme ve geri yükleme Ile veritabanlarını kopyalama](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)makalesini kullanarak geçiş veritabanının bir yedeğini oluşturun veya aşağıdaki sözdizimini kullanın:

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

[Hızlı başlangıç: veritabanını yönetilen bir örneğe geri yükleme](sql-database-managed-instance-get-started-restore.md).

## <a name="part-4-migrate-users-to-managed-instance"></a>4\. Bölüm: kullanıcıları yönetilen örneğe geçirme

> [!NOTE]
> Oluşturulduktan sonra yönetilen örnek işlevselliği için Azure AD yöneticisi değişti. Daha fazla bilgi için bkz. [mı Için yeni Azure AD yönetici işlevselliği](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi).

Yönetilen örnekteki geçiş işlemini gerçekleştirmek için ALTER USER komutunu yürütün.

1. Yönetilen örnek için Azure AD yönetici hesabını kullanarak yönetilen Örneğinizde oturum açın. Ardından, aşağıdaki sözdizimini kullanarak yönetilen örnekte Azure AD oturum açma bilgilerinizi oluşturun. Daha fazla bilgi için bkz. [öğretici: Azure ad sunucu sorumlularını (oturumlar) kullanarak Azure SQL veritabanı 'Nda yönetilen örnek güvenliği](sql-database-managed-instance-aad-security-tutorial.md).

    ```sql
    use master 
    go 
    
    -- Create login for AAD user [testUser1@aadsqlmi.net] 
    create login [testUser1@aadsqlmi.net] from external provider 
    go
    
    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net] 
    create login [migration] from external provider 
    go 
     
    --Check the two new logins 
    select * from sys.server_principals 
    go
    ```

1. Doğru veritabanı, tablo ve sorumlular için geçişinizi denetleyin.

    ```sql
    -- Switch to the database migration that is already restored for MI 
    use migration;  
    go 
     
    --Check if the restored table test exist and contain a row 
    select * from test; 
    go 
     
    -- Check that the SQL on-premise Windows user/group exists  
    select * from sys.database_principals; 
    go 
    -- the old user aadsqlmi\testUser1 should be there 
    -- the old group aadsqlmi\migration should be there
    ```

1. Şirket içi kullanıcıyı Azure AD oturum açma ile eşlemek için ALTER USER sözdizimini kullanın.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net]; 
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
    -- New user testUser1@aadsqlmi.net should be there instead 
     
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go

    -- Check a specific role   
    -- Display Db user name assigned to a specific role 
    SELECT DP1.name AS DatabaseRoleName,    
    isnull (DP2.name, 'No members') AS DatabaseUserName    
    FROM sys.database_role_members AS DRM   
    RIGHT OUTER JOIN sys.database_principals AS DP1   
    ON DRM.role_principal_id = DP1.principal_id   
    LEFT OUTER JOIN sys.database_principals AS DP2   
    ON DRM.member_principal_id = DP2.principal_id   
    WHERE DP1.type = 'R' 
    ORDER BY DP1.name;
    ```

1. Şirket içi grubu Azure AD oturum açma ile eşlemek için ALTER USER sözdizimini kullanın.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration]; 
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
     
    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go 
     
    --Check the db_owner role for 'aadsqlmi\migration' user 
    select is_rolemember('db_owner', 'migration')   
    go 
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>5\. Bölüm: Azure AD Kullanıcı veya grup kimlik doğrulamasını test etme

Daha önce ALTER USER söz dizimini kullanarak Azure AD oturum açma bilgilerine eşlenmiş kullanıcıyı kullanarak yönetilen örneğe kimlik doğrulamayı test edin.
 
1. Mı aboneliğinizi `aadsqlmi\testUser1` olarak kullanarak Federasyon VM 'de oturum açın
1. SQL Server Management Studio (SSMS) kullanarak yönetilen Örneğinizde oturum açarak, **Active Directory tümleşik** kimlik doğrulaması, veritabanına bağlanma `migration`kullanın.
    1. Ayrıca, SSMS seçeneğiyle testUser1@aadsqlmi.net kimlik bilgilerini kullanarak da oturum açabilirsiniz **: MFA desteğiyle Universal Active Directory**. Ancak, bu durumda çoklu oturum açma mekanizmasını kullanamazsınız ve bir parola yazmanız gerekir. Yönetilen örneğiniz üzerinde oturum açmak için bir Federasyon VM 'si kullanmanız gerekmez.
1. Rol üyesi **Seç**bölümünde, `test` tablosundan seçim yapabilirsiniz

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


Windows grubunun bir üyesini kullanarak yönetilen bir örneğe kimlik doğrulaması testi `migration`. Kullanıcı `aadsqlmi\testGroupUser` geçişten önce gruba `migration` eklenmiş olmalıdır.

1. Mı aboneliğinizi `aadsqlmi\testGroupUser` olarak kullanarak Federasyon VM 'de oturum açın 
1. SSMS 'yi **Active Directory tümleşik** kimlik doğrulamasıyla kullanma, mı sunucusuna ve veritabanına bağlanın `migration`
    1. Ayrıca, SSMS seçeneğiyle testGroupUser@aadsqlmi.net kimlik bilgilerini kullanarak da oturum açabilirsiniz **: MFA desteğiyle Universal Active Directory**. Ancak, bu durumda çoklu oturum açma mekanizmasını kullanamazsınız ve bir parola yazmanız gerekir. Yönetilen Örneğinizde oturum açmak için bir Federasyon VM 'si kullanmanız gerekmez. 
1. `db_owner` rolünün bir parçası olarak yeni bir tablo oluşturabilirsiniz.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> Azure SQL DB 'nin bilinen bir tasarım sorunu nedeniyle, bir grubun üyesi olarak yürütülen tablo oluşturma deyimlerinin aşağıdaki hatayla başarısız olması gerekir: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> Geçerli geçici çözüm, yukarıdaki < dbo. New > var olan bir şemaya sahip bir tablo oluşturmaktır.

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: DMS kullanarak SQL Server Azure SQL veritabanı yönetilen örneğine çevrimdışı geçirme](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)