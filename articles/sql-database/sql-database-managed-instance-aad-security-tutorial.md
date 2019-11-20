---
title: Azure AD Server sorumluları (oturum açmalar) ile yönetilen örnek güvenliği
description: Azure SQL veritabanı 'nda yönetilen bir örneği güvenli hale getirmeye yönelik teknikler ve özellikler hakkında bilgi edinin ve Azure AD Server sorumlularını (oturum açma) kullanın
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: bd65a21c2aa21643c76966410931949db7d17ad6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822799"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-server-principals-logins"></a>Öğretici: Azure AD Server sorumlularını (oturum açma) kullanarak Azure SQL veritabanı 'nda yönetilen örnek güvenliği

Yönetilen örnek, en son SQL Server şirket içi (Enterprise Edition) veritabanı altyapısının sahip olduğu neredeyse tüm güvenlik özelliklerini sağlar:

- Yalıtılmış bir ortamda erişimi sınırlama
- Kimlik gerektiren kimlik doğrulama mekanizmalarını kullanın (Azure AD, SQL kimlik doğrulaması)
- Rol tabanlı Üyelikler ve izinlerle yetkilendirmeyi kullanma
- Güvenlik özelliklerini etkinleştir

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> - Yönetilen örnek için Azure Active Directory (AD) sunucu sorumlusu (oturum açma) oluşturma
> - Yönetilen bir örnekteki Azure AD Server sorumlularına (oturum açma) izin verme
> - Azure AD Server sorumlularını (oturum açma) Azure AD kullanıcıları oluşturma
> - Azure AD kullanıcılarına izin atama ve veritabanı güvenliğini yönetme
> - Azure AD kullanıcıları ile kimliğe bürünme kullanma
> - Azure AD kullanıcılarıyla veritabanları arası sorgular kullanma
> - Tehdit koruması, denetim, veri maskeleme ve şifreleme gibi güvenlik özellikleri hakkında bilgi edinin

Daha fazla bilgi edinmek için bkz. [Azure SQL veritabanı yönetilen örneği genel bakış](sql-database-managed-instance-index.yml) ve [yetenekler](sql-database-managed-instance.md) makaleleri.

## <a name="prerequisites"></a>Ön koşullar

Öğreticiyi tamamlayabilmeniz için aşağıdaki önkoşullara sahip olduğunuzdan emin olun:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Azure SQL veritabanı yönetilen örneği
  - Şu makaleyi izleyin: [hızlı başlangıç: Azure SQL veritabanı yönetilen örneği oluşturma](sql-database-managed-instance-get-started.md)
- Yönetilen örneğe erişebilir ve [yönetilen örnek için bir Azure AD yöneticisi temin](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)edebilir. Daha fazla bilgi için bkz:
    - [Uygulamanızı yönetilen bir örneğe bağlama](sql-database-managed-instance-connect-app.md) 
    - [Yönetilen örnek bağlantı mimarisi](sql-database-managed-instance-connectivity-architecture.md)
    - [SQL ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>Yönetilen örneğinize erişimi sınırlandırma

Yönetilen örneklere, özel bir IP adresi üzerinden erişilebilir. Bir bağlantının yapılabilmesi için, uygulamaların veya kullanıcıların yönetilen örnek ağı 'na (VNet) erişmesi gerekir. SQL Server Daha fazla bilgi için aşağıdaki makaleye bakın, [Uygulamanızı yönetilen bir örneğe bağlayın](sql-database-managed-instance-connect-app.md).

Yönetilen örnekte, Azure SQL veritabanı ile aynı şekilde genel bağlantılara izin veren bir hizmet uç noktası yapılandırmak da mümkündür. Daha fazla bilgi için, [Azure SQL veritabanı yönetilen örneği 'nde ortak uç noktayı yapılandırma](sql-database-managed-instance-public-endpoint-configure.md)başlıklı makaleye bakın.

> [!NOTE] 
> Hizmet uç noktaları etkin olsa bile [SQL veritabanı güvenlik duvarı kuralları](sql-database-firewall-configure.md) uygulanmaz. Yönetilen Örneğin, bağlantıyı yönetmek için kendi [yerleşik güvenlik duvarı](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) vardır.

## <a name="create-an-azure-ad-server-principal-login-for-a-managed-instance-using-ssms"></a>SSMS kullanarak yönetilen örnek için bir Azure AD sunucu sorumlusu (oturum açma) oluşturma

İlk Azure AD sunucu sorumlusu (oturum açma), bir `sysadmin`veya sağlama işlemi sırasında oluşturulan yönetilen örnek için Azure AD yöneticisi olan standart SQL Server hesabı (Azure dışı AD) tarafından oluşturulabilir. Daha fazla bilgi için bkz. [yönetilen örneğiniz için Azure Active Directory Yöneticisi sağlama](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Bu işlev, [Azure AD Server sorumluları 'Nın GA](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi)değerinden beri değişmiştir.

Yönetilen örneğinize bağlanma örnekleri için aşağıdaki makalelere bakın:

- [Hızlı başlangıç: yönetilen örneğe bağlanmak için Azure VM 'yi yapılandırma](sql-database-managed-instance-configure-vm.md)
- [Hızlı başlangıç: şirket içi yönetilen bir örneğe Noktadan siteye bağlantı yapılandırma](sql-database-managed-instance-configure-p2s.md)

1. [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance)kullanarak bir `sysadmin` veya BIR Azure AD yöneticisi olan bir standart SQL Server hesabı (Azure dışı ad) kullanarak yönetilen örnekte oturum açın.

2. **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.

3. Sorgu penceresinde, yerel bir Azure AD hesabı için oturum açma oluşturmak üzere aşağıdaki sözdizimini kullanın:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Bu örnek hesap nativeuser@aadsqlmi.onmicrosoft.comiçin bir oturum açma oluşturur.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Araç çubuğunda, oturum açmayı oluşturmak için **Yürüt** ' ü seçin.

5. Aşağıdaki T-SQL komutunu yürüterek yeni eklenen oturum açma bilgilerini denetleyin:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![Native-Login. png](media/sql-database-managed-instance-security-tutorial/native-login.png)

Daha fazla bilgi için bkz. [oturum oluşturma](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Yönetilen örnek oturum açmaları oluşturulmasına izin vermek için izinler verme

Diğer Azure AD sunucu sorumlularını (oturum açma) oluşturmak için, SQL Server rolün veya izinlerin sorumluya verilmesi gerekir (SQL veya Azure AD).

### <a name="sql-authentication"></a>SQL kimlik doğrulaması

- Oturum açma bir SQL sorumlusu ise, yalnızca `sysadmin` rolünün parçası olan oturumlar, bir Azure AD hesabı için oturum açma bilgileri oluşturmak üzere Oluştur komutunu kullanabilir.

### <a name="azure-ad-authentication"></a>Azure AD kimlik doğrulaması

- Yeni oluşturulan Azure AD sunucu sorumlusuna (oturum açma) diğer Azure AD kullanıcıları, grupları veya uygulamaları için başka oturumlar oluşturma olanağı sağlamak üzere, oturum açma `sysadmin` veya `securityadmin` sunucu rolünü verin. 
- Diğer Azure AD Server sorumlularını (oturum açma) oluşturmak için, en azından Azure AD sunucu sorumlusuna (oturum açma) **herhangi BIR oturum açma** izninin verilmesi gerekir. 
- Varsayılan olarak, ana sunucuda yeni oluşturulan Azure AD Server sorumlularına (oturum açma) verilen standart izin: **SQL 'e bağlanın** ve **herhangi bir veritabanını görüntüleyin**.
- `sysadmin` sunucusu rolü, yönetilen bir örnek içinde çok sayıda Azure AD Server sorumlularına (oturum açma) verilebilir.

`sysadmin` sunucu rolüne oturum açma eklemek için:

1. Yönetilen örnek içinde yeniden oturum açın veya mevcut bağlantıyı Azure AD yöneticisi veya `sysadmin`olan SQL sorumlusu ile kullanın.

1. **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.

1. Aşağıdaki T-SQL söz dizimini kullanarak `sysadmin` sunucu rolüne Azure AD sunucusu sorumlusu (oturum açma) verin:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    Aşağıdaki örnek, `sysadmin` sunucu rolünü oturum açmaya verir nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>SSMS kullanarak ek Azure AD sunucu sorumluları (oturum açma) oluşturma

Azure AD Server sorumlusu (oturum açma) oluşturulduktan ve `sysadmin` ayrıcalıklarla sağlandıktan sonra, bu oturum açma, **oturum oluştur**Ile **From dış sağlayıcı** yan tümcesini kullanarak ek oturumlar oluşturabilir.

1. SQL Server Management Studio kullanarak, Azure AD sunucusu sorumlusu (oturum açma) ile yönetilen örneğe bağlanın. Yönetilen örnek ana bilgisayar adınızı girin. SSMS 'de kimlik doğrulaması için bir Azure AD hesabıyla oturum açarken seçebileceğiniz üç seçenek vardır:

   - Active Directory-Universal with MFA desteği
   - Active Directory-parola
   - Active Directory ile tümleşik </br>

     ![SSMS-Login-Prompt. png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

     Daha fazla bilgi için şu makaleye bakın: [SQL veritabanı ve SQL veri ambarı Ile evrensel kimlik doğrulaması (MFA IÇIN SSMS desteği)](sql-database-ssms-mfa-authentication.md)

1. **MFA desteğiyle Active Directory-Universal**' i seçin. Bu, bir Multi-Factor Authentication (MFA) oturum açma penceresi getirir. Azure AD parolanızla oturum açın.

    ![MFA-Login-Prompt. png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. SSMS **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.
1. Sorgu penceresinde, başka bir Azure AD hesabı için oturum açma oluşturmak üzere aşağıdaki sözdizimini kullanın:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Bu örnek, etki alanı aadsqlmi.net Azure AD aadsqlmi.onmicrosoft.com ile federe olan Azure AD Kullanıcı bob@aadsqlmi.netiçin bir oturum oluşturur.

    Aşağıdaki T-SQL komutunu yürütün. Federasyon Azure AD hesapları, şirket içi Windows oturum açmaları ve kullanıcıları için yönetilen örnek değiştirmeler.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. [Veritabanı oluştur](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) sözdizimini kullanarak yönetilen örnekte bir veritabanı oluşturun. Bu veritabanı, sonraki bölümde kullanıcı oturumlarını test etmek için kullanılacaktır.
    1. **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.
    1. Sorgu penceresinde, **Mymitestdb**adlı bir veritabanı oluşturmak için aşağıdaki sözdizimini kullanın.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Azure AD 'de bir grup için yönetilen örnek oturum açma oluşturun. Oturum açma bilgilerini yönetilen örneğe ekleyebilmeniz için önce bu grubun Azure AD 'de mevcut olması gerekir. Bkz. [temel Grup oluşturma ve Azure Active Directory kullanarak üye ekleme](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). _Mygroup_ grubunu oluşturun ve bu gruba üye ekleyin.

1. SQL Server Management Studio yeni bir sorgu penceresi açın.

    Bu örnekte, Azure AD 'de _mygroup_ adlı bir grubun var olduğu varsayılır. Aşağıdaki komutu yürütün:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Test olarak, yönetilen örnekte yeni oluşturulan oturum açma veya grupla oturum açın. Yönetilen örneğe yeni bir bağlantı açın ve kimlik doğrulaması sırasında yeni oturum açma bilgilerini kullanın.
1. **Nesne Gezgini**, sunucuya sağ tıklayın ve yeni bağlantı Için **Yeni sorgu** ' yı seçin.
1. Aşağıdaki komutu yürüterek yeni oluşturulan Azure AD sunucusu sorumlusu (oturum açma) için sunucu izinlerini denetleyin:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Azure AD Konuk kullanıcıları, yalnızca bir Azure AD grubunun parçası olarak eklendiğinde yönetilen örnek oturum açma işlemleri için desteklenir. Azure AD Konuk kullanıcısı, başka bir Azure AD 'den yönetilen Örneğin ait olduğu Azure AD 'ye davet edilen bir hesaptır. Örneğin, joe@contoso.com (Azure AD hesabı) veya steve@outlook.com (MSA hesabı) Azure AD aadsqlmı içindeki bir gruba eklenebilir. Kullanıcılar bir gruba eklendikten sonra, **create LOGIN** sözdizimi kullanılarak grup için yönetilen örnek **ana** veritabanında bir oturum açma oluşturulabilir. Bu grubun üyesi olan Konuk kullanıcılar, geçerli oturumlarını (örneğin, joe@contoso.com veya steve@outlook.com) kullanarak yönetilen örneğe bağlanabilir.

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login-and-give-permissions"></a>Azure AD Server sorumlusu 'ndan (oturum açma) bir Azure AD kullanıcısı oluşturun ve izin verin

Bireysel veritabanlarının yetkilendirmesi, şirket içi SQL Server ile aynı şekilde yönetilen örnekte aynı şekilde çalışmaktadır. Bir Kullanıcı, veritabanında var olan bir oturum açma işleminden oluşturulabilir ve bu veritabanı üzerindeki izinlerle veya bir veritabanı rolüne eklenebilir.

Artık **Mymitestdb**adlı bir veritabanı oluşturduğumuz ve yalnızca varsayılan izinlere sahip olan bir oturum açma işlemi, sonraki adım bu oturum açma işleminden bir Kullanıcı oluşturmaktır. Şu anda, oturum açma yönetilen örneğe bağlanabilir ve tüm veritabanlarını görebilir, ancak veritabanlarıyla etkileşim kurabilir. Varsayılan izinleri olan Azure AD hesabıyla oturum açarsanız ve yeni oluşturulan veritabanını genişletmeyi denerseniz, aşağıdaki hatayı görürsünüz:

![SSMS-DB-Not-Accessible. png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

Veritabanı izinleri verme hakkında daha fazla bilgi için bkz. [veritabanı altyapısı Ile çalışmaya başlama izinleri](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Azure AD kullanıcısı oluşturma ve örnek tablo oluşturma

1. SQL Server Management Studio kullanarak yönetilen Örneğinizde `sysadmin` hesabı kullanarak oturum açın.
1. **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.
1. Sorgu penceresinde, bir Azure AD sunucusu sorumlusundan (oturum açma) bir Azure AD kullanıcısı oluşturmak için aşağıdaki sözdizimini kullanın:

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    Aşağıdaki örnek, oturum açma bob@aadsqlmi.net bir Kullanıcı bob@aadsqlmi.net: oluşturur.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Ayrıca, bir grup olan Azure AD Server sorumlusu 'ndan (oturum açma) bir Azure AD kullanıcısı oluşturmak için de desteklenir.

    Aşağıdaki örnek, Azure AD 'de mevcut olan _mygroup_ grubu için bir oturum açma oluşturur.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    **Mygroup** 'a ait olan tüm kullanıcılar **Mymitestdb** veritabanına erişebilir.

    > [!IMPORTANT]
    > Bir Azure AD sunucu sorumlusu 'ndan (oturum açma) bir **Kullanıcı** oluştururken, user_name **oturum açma**işleminden aynı login_name olarak belirtin.

    Daha fazla bilgi için bkz. [Kullanıcı oluşturma](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. Yeni bir sorgu penceresinde, aşağıdaki T-SQL komutunu kullanarak bir test tablosu oluşturun:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. SSMS 'de oluşturulan kullanıcıyla bir bağlantı oluşturun. Daha önce `sysadmin` tarafından oluşturulan **TestTable** tablosunu görmediğine dikkat edin. Kullanıcıya veritabanından veri okuma izinleri sağlanması gerekiyor.

1. Şu komutu yürüterek, kullanıcının sahip olduğu geçerli izni kontrol edebilirsiniz:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Veritabanı düzeyindeki rollere kullanıcı ekleme

Kullanıcının veritabanındaki verileri görmesini sağlamak için kullanıcıya [veritabanı düzeyinde roller](/sql/relational-databases/security/authentication-access/database-level-roles) sağlayabiliriz.

1. SQL Server Management Studio kullanarak yönetilen Örneğinizde `sysadmin` hesabı kullanarak oturum açın.

1. **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.

1. Aşağıdaki T-SQL söz dizimini kullanarak Azure AD kullanıcısına `db_datareader` veritabanı rolü verin:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    Aşağıdaki örnek, **Mymitestdb** veritabanı üzerinde Kullanıcı bob@aadsqlmi.net ve _mygroup_ grubunu `db_datareader` izinleriyle sağlar:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Aşağıdaki komutu yürüterek veritabanında oluşturulan Azure AD kullanıcısının mevcut olup olmadığını denetleyin:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. `db_datareader` rolüne eklenmiş kullanıcıyla yönetilen örneğe yeni bir bağlantı oluşturun.
1. Tabloyu görmek için **Nesne Gezgini** veritabanını genişletin.

    ![SSMS-test-Table. png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Yeni bir sorgu penceresi açın ve aşağıdaki SELECT ifadesini yürütün:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Tablodaki verileri görebiliyor musunuz? Döndürülen sütunları görmeniz gerekir.

    ![SSMS-test-Table-Query. png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Azure AD sunucu düzeyi sorumlularını (oturum açma) taklit etme

Yönetilen örnek, Azure AD sunucu düzeyi sorumlularının (oturum açma) kişiliğe bürünmeyi destekler.

### <a name="test-impersonation"></a>Kimliğe bürünme testi

1. SQL Server Management Studio kullanarak yönetilen Örneğinizde `sysadmin` hesabı kullanarak oturum açın.

1. **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.

1. Sorgu penceresinde, yeni bir saklı yordam oluşturmak için aşağıdaki komutu kullanın:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Saklı yordamı yürütürken taklit ettiğiniz kullanıcının **bob\@aadsqlmi.net**olduğunu görmek için aşağıdaki komutu kullanın.

    ```sql
    Exec dbo.usp_Demo
    ```

1. EXECUTE AS LOGIN ifadesini kullanarak kimliğe bürünme testi:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Yalnızca `sysadmin` rolünün parçası olan SQL Server düzeyindeki sorumlular (oturum açmalar), Azure AD sorumlularını hedefleyen aşağıdaki işlemleri yürütebilirler: 
> - KULLANıCı OLARAK YÜRÜT
> - OTURUM AÇMA OLARAK YÜRÜT

## <a name="using-cross-database-queries-in-managed-instances"></a>Yönetilen örneklerde çapraz veritabanı sorguları kullanma

Azure AD Server sorumlularını (oturum açmalar) içeren Azure AD hesapları için çapraz veritabanı sorguları desteklenir. Bir Azure AD grubuyla veritabanları arası sorguyu test etmek için başka bir veritabanı ve tablo oluşturmanız gerekir. Zaten varsa başka bir veritabanı ve tablo oluşturmayı atlayabilirsiniz.

1. SQL Server Management Studio kullanarak yönetilen Örneğinizde `sysadmin` hesabı kullanarak oturum açın.
1. **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.
1. Sorgu penceresinde, **MyMITestDB2** adlı bir veritabanı ve **TestTable2**adlı tablo oluşturmak için aşağıdaki komutu kullanın:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. Yeni bir sorgu penceresinde, yeni veritabanı **MyMITestDB2**Kullanıcı _grubunu_ oluşturmak için aşağıdaki komutu yürütün ve bu VERITABANıNDA _mygroup_'a SELECT izinleri verin:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Azure AD grubu _mygroup_'un bir üyesi olarak SQL Server Management Studio kullanarak yönetilen örnekte oturum açın. Yeni bir sorgu penceresi açın ve veritabanları arası SELECT ifadesini yürütün:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    **TestTable2**'deki tablo sonuçlarını görmeniz gerekir.

## <a name="additional-scenarios-supported-for-azure-ad-server-principals-logins"></a>Azure AD Server sorumluları (oturum açmalar) için desteklenen ek senaryolar

- SQL Aracısı yönetimi ve iş yürütmeleri, Azure AD Server sorumluları (oturumlar) için desteklenir.
- Veritabanı yedekleme ve geri yükleme işlemleri, Azure AD Server sorumluları (oturumlar) tarafından yürütülebilir.
- Azure AD Server sorumluları (oturum açmalar) ve kimlik doğrulama olayları ile ilgili tüm deyimlerin [denetlenmesi](sql-database-managed-instance-auditing.md) .
- `sysadmin` Server-role üyesi olan Azure AD Server sorumluları (oturumlar) için adanmış yönetici bağlantısı.
- Azure AD Server sorumluları (oturumlar), [sqlcmd yardımcı programını](/sql/tools/sqlcmd-utility) ve [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) aracını kullanarak desteklenir.
- Oturum açma Tetikleyicileri, Azure AD Server sorumlularına (oturum açmalar) gelen oturum açma olayları için desteklenir.
- Hizmet Aracısı ve DB Mail, Azure AD Server sorumlularını (oturum açma) kullanarak oluşturulabilir.


## <a name="next-steps"></a>Sonraki adımlar

### <a name="enable-security-features"></a>Güvenlik özelliklerini etkinleştir

Veritabanınızı güvenli hale getirme yöntemlerinin kapsamlı bir listesi için aşağıdaki [yönetilen örnek özellikleri güvenlik özellikleri](sql-database-managed-instance.md#azure-sql-database-security-features) makalesine bakın. Aşağıdaki güvenlik özellikleri ele alınmıştır:

- [Yönetilen örnek denetimi](sql-database-managed-instance-auditing.md) 
- [Her zaman şifreli](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Tehdit algılama](sql-database-managed-instance-threat-detection.md) 
- [Dinamik veri maskeleme](/sql/relational-databases/security/dynamic-data-masking)
- [Satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security) 
- [Saydam veri şifrelemesi (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Yönetilen örnek özellikleri

Yönetilen örnek özelliklerine yönelik kapsamlı bir genel bakış için bkz.:

> [!div class="nextstepaction"]
> [Yönetilen örnek özellikleri](sql-database-managed-instance.md)
