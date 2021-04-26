---
title: T-SQL kullanarak SQL Server Windows kullanıcılarını ve gruplarını SQL yönetilen örneği 'ne geçirme
description: Windows kullanıcılarını ve gruplarını bir SQL Server örneğindeki Azure SQL yönetilen örneği 'ne geçirme hakkında bilgi edinin
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: f2dd34ab7c6ee5be26836e4abb86960605ee44ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84708695"
---
# <a name="tutorial-migrate-windows-users-and-groups-in-a-sql-server-instance-to-azure-sql-managed-instance-using-t-sql-ddl-syntax"></a>Öğretici: T-SQL DDL sözdizimini kullanarak bir SQL Server örneğindeki Windows kullanıcılarını ve gruplarını Azure SQL yönetilen örneği 'ne geçirme
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

> [!NOTE]
> Bu makaledeki kullanıcıları ve grupları SQL yönetilen örneği 'ne geçirmek için kullanılan söz dizimi **genel önizlemededir**.

Bu makale, T-SQL söz dizimini kullanarak SQL Server şirket içi Windows kullanıcılarınızı ve gruplarınızı Azure SQL yönetilen örneği 'ne geçirme sürecinde size kılavuzluk ediyor.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> - SQL Server için oturum açma bilgileri oluşturma
> - Geçiş için bir test veritabanı oluşturma
> - Oturum açma bilgileri, kullanıcılar ve roller oluşturma
> - Veritabanınızı SQL yönetilen örneği 'ne yedekleme ve geri yükleme (mı)
> - Kullanıcıları ALTER USER söz dizimini kullanarak el ile MI?
> - Yeni eşlenen kullanıcılarla kimlik doğrulamasını test etme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki önkoşullar geçerlidir:

- Windows etki alanı Azure Active Directory (Azure AD) ile federe olur.
- Kullanıcı/Grup oluşturmak için Active Directory erişim.
- Şirket içi ortamınızda mevcut bir SQL Server.
- Mevcut bir SQL yönetilen örneği. Bkz. [hızlı başlangıç: SQL yönetilen örneği oluşturma](instance-create-quickstart.md).
  - `sysadmin`SQL yönetilen örneğindeki bir, Azure AD oturum açmaları oluşturmak için kullanılmalıdır.
- [SQL yönetilen örneği için bir Azure AD yöneticisi oluşturun](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).
- Ağınız içindeki SQL yönetilen örneğinize bağlanabilirsiniz. Daha fazla bilgi için aşağıdaki makalelere bakın:
  - [Uygulamanızı Azure SQL Yönetilen Örneği'ne bağlama](connect-application-instance.md)
  - [Hızlı başlangıç: Şirket içinden bir Azure SQL yönetilen örneğine Noktadan siteye bağlantı yapılandırma](point-to-site-p2s-configure.md)
  - [Azure SQL yönetilen örneği 'nde ortak uç noktayı yapılandırma](public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>T-SQL DDL sözdizimi

Aşağıda, Azure AD kimlik doğrulamasıyla bir SQL Server örneğinden SQL yönetilen örneğine Windows kullanıcıları ve gruplarının geçişini desteklemek için kullanılan T-SQL DDL sözdizimi aşağıda verilmiştir.

```sql
-- For individual Windows users with logins
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com];

--For individual groups with logins
ALTER USER [domainName\groupName] WITH LOGIN=[groupName]
```

## <a name="arguments"></a>Bağımsız değişkenler

_Etki_</br>
Kullanıcının etki alanı adını belirtir.

_Nitelen_</br>
Veritabanı içinde tanımlanan kullanıcının adını belirtir.

_= loginName \@ domainName.com_</br>
Bir kullanıcıyı Azure AD oturum açmayla yeniden eşler

_adýdýr_</br>
Veritabanı içinde tanımlanan grubun adını belirtir.

## <a name="part-1-create-logins-in-sql-server-for-windows-users-and-groups"></a>1. kısım: Windows kullanıcıları ve grupları için SQL Server oturum açma bilgileri oluşturma

> [!IMPORTANT]
> Aşağıdaki sözdizimi SQL Server bir Kullanıcı ve grup oturumu oluşturur. Aşağıdaki sözdizimini yürütmeden önce, Kullanıcı ve grubun Active Directory (AD) içinde mevcut olduğundan emin olmanız gerekir. </br> </br>
> Kullanıcılar: testUser1, testGroupUser </br>
> Grup: geçiş-testGroupUser 'ın AD içindeki geçiş grubuna ait olması gerekir

Aşağıdaki örnek, _aadsqlmı_ etki alanı altında _testUser1_ adlı bir hesap için SQL Server bir oturum açma oluşturur.

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

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>2. Bölüm: Windows kullanıcıları ve grupları oluşturun, ardından Roller ve izinler ekleyin

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

Bir grup oluşturmak için aşağıdaki sözdizimini kullanın. Ardından grubu role ekleyin `db_owner` .

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

## <a name="part-3-backup-and-restore-the-individual-user-database-to-sql-managed-instance"></a>3. kısım: bireysel kullanıcı veritabanını SQL yönetilen örneğine yedekleme ve geri yükleme

[Yedekleme ve geri yükleme Ile veritabanlarını kopyalama](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)makalesini kullanarak geçiş veritabanının bir yedeğini oluşturun veya aşağıdaki sözdizimini kullanın:

```sql
use master;
go
backup database migration to disk = 'C:\Migration\migration.bak';
go
```

[Hızlı başlangıç: bir veritabanını BIR SQL yönetilen örneğine geri yükleyin](restore-sample-database-quickstart.md).

## <a name="part-4-migrate-users-to-sql-managed-instance"></a>4. Bölüm: kullanıcıları SQL yönetilen örneği 'ne geçirme

SQL yönetilen örneği üzerinde geçiş işlemini gerçekleştirmek için ALTER USER komutunu yürütün.

1. SQL yönetilen örneği için Azure AD yönetici hesabını kullanarak SQL yönetilen Örneğinizde oturum açın. Ardından, aşağıdaki sözdizimini kullanarak SQL yönetilen örneğinde Azure AD oturum açma bilgilerinizi oluşturun. Daha fazla bilgi için bkz. [öğretici: Azure AD Server sorumlularını (oturumlar) kullanarak Azure SQL veritabanı 'NDA SQL yönetilen örnek güvenliği](aad-security-configure-tutorial.md).

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

    -- Check that the SQL on-premises Windows user/group exists  
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

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>5. Bölüm: Azure AD Kullanıcı veya grup kimlik doğrulamasını test etme

Daha önce ALTER USER söz dizimini kullanarak Azure AD oturum açma bilgilerine eşlenmiş kullanıcıyı kullanarak SQL yönetilen örneği için kimlik doğrulamasını test edin.

1. Azure SQL yönetilen örnek aboneliğinizi şu şekilde kullanarak Federasyon VM 'de oturum açın `aadsqlmi\testUser1`
1. SQL Server Management Studio (SSMS) kullanarak, veritabanına bağlanarak **Active Directory tümleşik** kimlik doğrulaması kullanarak SQL yönetilen Örneğinizde oturum açın `migration` .
    1. Ayrıca, testUser1@aadsqlmi.net SSMS seçeneği **Active Directory – UNIVERSAL with MFA desteğiyle** kimlik bilgilerini kullanarak da oturum açabilirsiniz. Ancak, bu durumda çoklu oturum açma mekanizmasını kullanamazsınız ve bir parola yazmanız gerekir. SQL yönetilen örneğiniz üzerinde oturum açmak için bir Federasyon VM 'si kullanmanız gerekmez.
1. Rol üyesi **Seç** bölümünde, tablodan seçim yapabilirsiniz `test`

    ```sql
    Select * from test  --  and see one row (1,10)
    ```

Bir Windows grubunun üyesini kullanarak bir SQL yönetilen örneği için kimlik doğrulaması testi `migration` . Kullanıcının `aadsqlmi\testGroupUser` geçişten önce gruba eklenmiş olması gerekir `migration` .

1. Azure SQL yönetilen örnek aboneliğinizi şu şekilde kullanarak Federasyon VM 'de oturum açın `aadsqlmi\testGroupUser`
1. SSMS 'yi **Active Directory tümleşik** kimlik doğrulamasıyla kullanma, Azure SQL yönetilen örnek sunucusuna ve veritabanına bağlanma `migration`
    1. Ayrıca, testGroupUser@aadsqlmi.net SSMS seçeneği **Active Directory – UNIVERSAL with MFA desteğiyle** kimlik bilgilerini kullanarak da oturum açabilirsiniz. Ancak, bu durumda çoklu oturum açma mekanizmasını kullanamazsınız ve bir parola yazmanız gerekir. SQL yönetilen Örneğinizde oturum açmak için bir Federasyon VM 'si kullanmanız gerekmez.
1. Rolün bir parçası olarak `db_owner` Yeni bir tablo oluşturabilirsiniz.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```

> [!NOTE]
> Azure SQL veritabanı 'nın bilinen bir tasarım sorunu nedeniyle, bir grubun üyesi olarak yürütülen tablo oluşturma deyimlerinin aşağıdaki hatayla başarısız olması gerekir: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> Geçerli geçici çözüm, yukarıdaki <dbo. New> var olan bir şemaya sahip bir tablo oluşturmaktır.

## <a name="next-steps"></a>Sonraki adımlar

[Öğretici: DMS kullanarak Azure SQL yönetilen örneği 'ne çevrimdışı SQL Server geçirme](../../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)
