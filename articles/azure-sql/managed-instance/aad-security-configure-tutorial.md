---
title: Azure AD Server sorumluları (oturum açmalar) ile SQL yönetilen örnek güvenliği
description: Azure SQL yönetilen örneğini güvenli hale getirmeye yönelik teknikler ve özellikler hakkında bilgi edinin ve Azure AD Server sorumlularını (oturum açma) kullanın
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 50544507f9d83c216bb6c18e004c5ce7ad1ca346
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639843"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>Öğretici: Azure AD sunucu sorumlularını (oturum açmalar) kullanarak Azure SQL Yönetilen Örneği'nde güvenlik
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL yönetilen örneği, en son SQL Server (Enterprise Edition) veritabanı altyapısının sahip olduğu neredeyse tüm güvenlik özelliklerini sağlar:

- Yalıtılmış bir ortamda erişimi sınırlama
- Kimlik gerektiren kimlik doğrulama mekanizmalarını kullanın: Azure Active Directory (Azure AD) ve SQL kimlik doğrulaması
- Rol tabanlı Üyelikler ve izinlerle yetkilendirmeyi kullanma
- Güvenlik özelliklerini etkinleştir

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> - Yönetilen örnek için bir Azure AD sunucu sorumlusu (oturum açma) oluşturma
> - Yönetilen bir örnekteki Azure AD Server sorumlularına (oturum açma) izin verme
> - Azure AD Server sorumlularını (oturum açma) Azure AD kullanıcıları oluşturma
> - Azure AD kullanıcılarına izin atama ve veritabanı güvenliğini yönetme
> - Azure AD kullanıcıları ile kimliğe bürünme kullanma
> - Azure AD kullanıcılarıyla veritabanları arası sorgular kullanma
> - Tehdit koruması, denetim, veri maskeleme ve şifreleme gibi güvenlik özellikleri hakkında bilgi edinin

Daha fazla bilgi edinmek için bkz. [Azure SQL yönetilen örneğine genel bakış](sql-managed-instance-paas-overview.md). 

## <a name="prerequisites"></a>Önkoşullar

Öğreticiyi tamamlayabilmeniz için aşağıdaki önkoşullara sahip olduğunuzdan emin olun:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Yönetilen bir örnek
  - Şu makaleyi izleyin: [hızlı başlangıç: yönetilen örnek oluşturma](instance-create-quickstart.md)
- Yönetilen örneğe erişebilir ve [yönetilen örnek için bir Azure AD yöneticisi temin](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)edebilir. Daha fazla bilgi edinmek için şu makalelere bakın:
  - [Uygulamanızı yönetilen bir örneğe bağlama](connect-application-instance.md)
  - [SQL yönetilen örnek bağlantısı mimarisi](connectivity-architecture-overview.md)
  - [SQL ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>Erişimi sınırla 

Yönetilen örneklere, özel bir IP adresi üzerinden erişilebilir. Yalıtılmış bir SQL Server ortamı gibi, bir bağlantı oluşturulmadan önce uygulamaların veya kullanıcıların SQL yönetilen örnek ağı 'na (VNet) erişmesi gerekir. Daha fazla bilgi için bkz. [UYGULAMANıZı SQL yönetilen örneğine bağlama](connect-application-instance.md).

Ayrıca, Azure SQL veritabanı ile aynı şekilde genel bağlantılara izin veren yönetilen bir örnekte hizmet uç noktası yapılandırmak da mümkündür.
Daha fazla bilgi için bkz. [Azure SQL yönetilen örneği 'nde ortak uç noktayı yapılandırma](public-endpoint-configure.md).

> [!NOTE]
> Hizmet uç noktaları etkin olsa bile, [Azure SQL veritabanı güvenlik duvarı kuralları](../database/firewall-configure.md) uygulanmaz. Azure SQL yönetilen örneğinin, bağlantıyı yönetmek için kendi [yerleşik güvenlik duvarı](management-endpoint-verify-built-in-firewall.md) vardır.

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>SSMS kullanarak bir Azure AD sunucu sorumlusu (oturum açma) oluşturma

İlk Azure AD sunucu sorumlusu (oturum açma) `sysadmin` , bir veya sağlama işlemi sırasında oluşturulan yönetilen örnek Için Azure AD yöneticisi olan standart SQL yönetici hesabı (Azure AD) tarafından oluşturulabilir. Daha fazla bilgi için bkz. [SQL yönetilen örneği için Azure Active Directory Yöneticisi sağlama](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

SQL yönetilen örneğine bağlanma örnekleri için aşağıdaki makalelere bakın:

- [Hızlı başlangıç: SQL yönetilen örneğine bağlanmak için Azure VM 'yi yapılandırma](connect-vm-instance-configure.md)
- [Hızlı başlangıç: Şirket içinden SQL yönetilen örneği için Noktadan siteye bağlantı yapılandırma](point-to-site-p2s-configure.md)

1. `sysadmin` [SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms)kullanarak, SQL yönetilen örneği Için BIR veya Azure AD yöneticisi olan standart bir SQL oturum açma HESABı (Azure AD) kullanarak yönetilen örneğiniz üzerinde oturum açın.

2. **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.

3. Sorgu penceresinde, yerel bir Azure AD hesabı için oturum açma oluşturmak üzere aşağıdaki sözdizimini kullanın:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Bu örnek, hesap için bir oturum açma oluşturur nativeuser@aadsqlmi.onmicrosoft.com .

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

    ![Yeni eklenen oturum açmanın adını, principal_id, SID 'sini, türünü ve type_desc gösteren S S s Nesne Gezgini sonuçları sekmesinin ekran görüntüsü.](./media/aad-security-configure-tutorial/native-login.png)

Daha fazla bilgi için bkz. [oturum oluşturma](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

## <a name="grant-permissions-to-create-logins"></a>Oturum açma işlemleri oluşturmak için izin verme

Diğer Azure AD sunucu sorumlularını (oturum açma) oluşturmak için, SQL Server rolün veya izinlerin sorumluya verilmesi gerekir (SQL veya Azure AD).

### <a name="sql-authentication"></a>SQL kimlik doğrulaması

- Oturum açma bir SQL sorumlusu ise, yalnızca rolün parçası olan oturum açma işlemleri `sysadmin` bir Azure AD hesabı için oturum açma bilgileri oluşturmak üzere Oluştur komutunu kullanabilir.

### <a name="azure-ad-authentication"></a>Azure AD kimlik doğrulaması

- Yeni oluşturulan Azure AD sunucu sorumlusuna (oturum açma) diğer Azure AD kullanıcıları, grupları veya uygulamaları için başka oturumlar oluşturma olanağı sağlamak için, oturum açma `sysadmin` veya sunucu rolüne izin verin `securityadmin` .
- Diğer Azure AD Server sorumlularını (oturum açma) oluşturmak için, en azından Azure AD sunucu sorumlusuna (oturum açma) **herhangi BIR oturum açma** izninin verilmesi gerekir.
- Varsayılan olarak, ana sunucuda yeni oluşturulan Azure AD Server sorumlularına (oturum açma) verilen standart izin: **SQL 'e bağlanın** ve **herhangi bir veritabanını görüntüleyin**.
- `sysadmin`Sunucu rolü, yönetilen bir örnek içinde çok sayıda Azure AD Server sorumlularına (oturum açma) verilebilir.

Oturumu `sysadmin` sunucu rolüne eklemek için:

1. Yönetilen örnek içinde yeniden oturum açın veya mevcut bağlantıyı Azure AD yöneticisi veya olan SQL sorumlusu ile kullanın `sysadmin` .

1. **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.

1. `sysadmin`Aşağıdaki T-SQL söz dizimini kullanarak sunucu rolüne Azure ad sunucusu sorumlusu (oturum açma) verin:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    Aşağıdaki örnek, `sysadmin` oturum açma için sunucu rolünü verir nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>SSMS kullanarak ek Azure AD sunucu sorumluları (oturum açma) oluşturma

Azure AD Server sorumlusu (oturum açma) oluşturulduktan ve ayrıcalıklarla sağlandıktan sonra `sysadmin` , bu oturum açma, **oturum oluştur** Ile **From dış sağlayıcı** yan tümcesini kullanarak ek oturum açma işlemleri oluşturabilir.

1. SQL Server Management Studio kullanarak, Azure AD sunucusu sorumlusu (oturum açma) ile yönetilen örneğe bağlanın. SQL yönetilen örnek ana bilgisayar adınızı girin. SSMS 'de kimlik doğrulaması için bir Azure AD hesabıyla oturum açarken seçebileceğiniz üç seçenek vardır:

   - Active Directory-Universal with MFA desteği
   - Active Directory-parola
   - Active Directory ile tümleşik </br>

     ![Kimlik doğrulaması açılan menüsünde MFA desteğiyle birlikte Active Directory-Universal ile sunucuya Bağlan iletişim kutusunun ekran görüntüsü.](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     Daha fazla bilgi için bkz. [evrensel kimlik doğrulaması (Multi-Factor Authentication IÇIN SSMS desteği)](../database/authentication-mfa-ssms-overview.md).

1. **MFA desteğiyle Active Directory-Universal**' i seçin. Bu, Multi-Factor Authentication bir oturum açma penceresi getirir. Azure AD parolanızla oturum açın.

    ![Parola gir alanında imleç ile Multi-Factor Authentication oturum açma penceresinin ekran görüntüsü.](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. SSMS **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.
1. Sorgu penceresinde, başka bir Azure AD hesabı için oturum açma oluşturmak üzere aşağıdaki sözdizimini kullanın:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Bu örnekte bob@aadsqlmi.net , etki alanı Aadsqlmi.net Azure ad aadsqlmi.onmicrosoft.com etki alanı ile federe olan Azure AD kullanıcısı için bir oturum açma işlemi oluşturulur.

    Aşağıdaki T-SQL komutunu yürütün. Federasyon Azure AD hesapları, şirket içi Windows oturum açmaları ve kullanıcıları için SQL yönetilen örnek değiştirlarıdır.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. [Veritabanı oluştur](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current&preserve-view=true) sözdizimini kullanarak yönetilen örnekte bir veritabanı oluşturun. Bu veritabanı, sonraki bölümde kullanıcı oturumlarını test etmek için kullanılacaktır.
    1. **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.
    1. Sorgu penceresinde, **Mymitestdb** adlı bir veritabanı oluşturmak için aşağıdaki sözdizimini kullanın.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Azure AD 'de bir grup için SQL yönetilen örnek oturum açma oluşturun. Oturum açmayı SQL yönetilen örneğine ekleyebilmeniz için önce grubun Azure AD 'de mevcut olması gerekir. Bkz. [temel Grup oluşturma ve Azure Active Directory kullanarak üye ekleme](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). _Mygroup_ grubunu oluşturun ve bu gruba üye ekleyin.

1. SQL Server Management Studio yeni bir sorgu penceresi açın.

    Bu örnek, Azure AD 'de _mygroup_ adlı bir grup olduğunu varsayar. Aşağıdaki komutu yürütün:

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
> Azure AD Konuk kullanıcıları SQL yönetilen örnek oturum açmaları için, yalnızca bir Azure AD grubunun parçası olarak eklendiğinde desteklenir. Azure AD Konuk kullanıcısı, başka bir Azure AD örneğinden, yönetilen Örneğin ait olduğu Azure AD örneğine davet edilen bir hesaptır. Örneğin, joe@contoso.com (Azure AD hesabı) veya steve@outlook.com (Microsoft hesabı), Azure AD aadsqlmı örneğindeki bir gruba eklenebilir. Kullanıcılar bir gruba eklendikten sonra, **create LOGIN** sözdizimi kullanılarak grup Için SQL yönetilen örnek **ana** veritabanında bir oturum açma oluşturulabilir. Bu grubun üyesi olan Konuk kullanıcılar, geçerli oturumlarını (örneğin, veya) kullanarak yönetilen örneğe bağlanabilir joe@contoso.com steve@outlook.com .

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>Azure AD Server sorumlusu 'ndan bir Azure AD kullanıcısı oluşturma (oturum açma)

Tek tek veritabanlarına yönelik yetkilendirmeler, SQL yönetilen örneğinde SQL Server veritabanlarında olduğu gibi aynı şekilde çalışmaktadır. Bir Kullanıcı, veritabanında var olan bir oturum açma işleminden oluşturulabilir ve bu veritabanı üzerindeki izinlerle veya bir veritabanı rolüne eklenebilir.

Artık **Mymitestdb** adlı bir veritabanı oluşturduğumuz ve yalnızca varsayılan izinlere sahip olan bir oturum açma işlemi, sonraki adım bu oturum açma işleminden bir Kullanıcı oluşturmaktır. Şu anda, oturum açma yönetilen örneğe bağlanabilir ve tüm veritabanlarını görebilir, ancak veritabanlarıyla etkileşim kurabilir. Varsayılan izinleri olan Azure AD hesabıyla oturum açarsanız ve yeni oluşturulan veritabanını genişletmeyi denerseniz, aşağıdaki hatayı görürsünüz:

![S S M Nesne Gezgini, "MyMITestDB veritabanı erişilebilir değil ' i okuyan bir hata iletisinin ekran görüntüsü. (Objecbir) ".](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

Veritabanı izinleri verme hakkında daha fazla bilgi için bkz. [veritabanı altyapısı Ile çalışmaya başlama izinleri](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Azure AD kullanıcısı oluşturma ve örnek tablo oluşturma

1. SQL Server Management Studio kullanarak bir hesabı kullanarak yönetilen örnekte oturum açın `sysadmin` .
1. **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.
1. Sorgu penceresinde, bir Azure AD sunucusu sorumlusundan (oturum açma) bir Azure AD kullanıcısı oluşturmak için aşağıdaki sözdizimini kullanın:

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    Aşağıdaki örnek, bob@aadsqlmi.net oturum açma işleminden bir kullanıcı oluşturur bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Ayrıca, bir grup olan Azure AD Server sorumlusu 'ndan (oturum açma) bir Azure AD kullanıcısı oluşturmak için de desteklenir.

    Aşağıdaki örnek, Azure AD Örneğinizde bulunan _mygroup_ grubu için bir oturum açma oluşturur.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    *Mygroup* 'a ait olan tüm kullanıcılar **Mymitestdb** veritabanına erişebilir.

    > [!IMPORTANT]
    > Bir Azure AD sunucu sorumlusu 'ndan (oturum açma) bir **Kullanıcı** oluştururken, user_name **oturum açma** işleminden aynı login_name olarak belirtin.

    Daha fazla bilgi için bkz. [Kullanıcı oluşturma](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

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

1. SSMS 'de oluşturulan kullanıcıyla bir bağlantı oluşturun. Daha önce oluşturulan **TestTable** tablosunu görmediğine dikkat edin `sysadmin` . Kullanıcıya veritabanından veri okuma izinleri sağlanması gerekiyor.

1. Şu komutu yürüterek, kullanıcının sahip olduğu geçerli izni kontrol edebilirsiniz:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Veritabanı düzeyindeki rollere kullanıcı ekleme

Kullanıcının veritabanındaki verileri görmesini sağlamak için kullanıcıya [veritabanı düzeyinde roller](/sql/relational-databases/security/authentication-access/database-level-roles) sağlayabiliriz.

1. SQL Server Management Studio kullanarak bir hesabı kullanarak yönetilen örnekte oturum açın `sysadmin` .

1. **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.

1. `db_datareader`Aşağıdaki T-SQL söz dizimini kullanarak Azure AD kullanıcısına veritabanı rolü verin:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    Aşağıdaki örnek, bob@aadsqlmi.net  `db_datareader` **mymitestdb** veritabanı üzerinde Kullanıcı ve grup mygroup izinleri sağlar:

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

1. Role eklenmiş olan kullanıcıyla yönetilen örneğe yeni bir bağlantı oluşturun `db_datareader` .
1. Tabloyu görmek için **Nesne Gezgini** veritabanını genişletin.

    ![MyMITestDB içindeki tabloların klasör yapısını gösteren, S s M içindeki Nesne Gezgini ekran görüntüsü. Dbo. TestTable klasörü vurgulanır.](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. Yeni bir sorgu penceresi açın ve aşağıdaki SELECT ifadesini yürütün:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Tablodaki verileri görebiliyor musunuz? Döndürülen sütunları görmeniz gerekir.

    ![S S s içindeki sonuçlar sekmesinin ekran görüntüsü, tablo sütunu başlıkları AccountNum, City, Name ve State ' i gösterir Nesne Gezgini.](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>Azure AD sunucu düzeyi sorumlularına (oturum açma) bürün

SQL yönetilen örneği, Azure AD sunucu düzeyi sorumlularının (oturum açma) kişiliğe bürünmeyi destekler.

### <a name="test-impersonation"></a>Kimliğe bürünme testi

1. SQL Server Management Studio kullanarak bir hesabı kullanarak yönetilen örnekte oturum açın `sysadmin` .

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

1. Saklı yordamı yürütürken taklit ettiğiniz kullanıcının **bob \@ aadsqlmi.net** olduğunu görmek için aşağıdaki komutu kullanın.

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
> Yalnızca rolün parçası olan SQL Server düzeyindeki sorumlular (oturum açmalar), `sysadmin` Azure AD sorumlularını hedefleyen aşağıdaki işlemleri yürütebilirler:
>
> - KULLANıCı OLARAK YÜRÜT
> - OTURUM AÇMA OLARAK YÜRÜT

## <a name="use-cross-database-queries"></a>Veritabanları arası sorguları kullanma

Azure AD Server sorumlularını (oturum açmalar) içeren Azure AD hesapları için çapraz veritabanı sorguları desteklenir. Bir Azure AD grubuyla veritabanları arası sorguyu test etmek için başka bir veritabanı ve tablo oluşturmanız gerekir. Zaten varsa başka bir veritabanı ve tablo oluşturmayı atlayabilirsiniz.

1. SQL Server Management Studio kullanarak bir hesabı kullanarak yönetilen örnekte oturum açın `sysadmin` .
1. **Nesne Gezgini**, sunucuya sağ tıklayın ve **Yeni sorgu**' yı seçin.
1. Sorgu penceresinde, **MyMITestDB2** adlı bir veritabanı ve **TestTable2** adlı tablo oluşturmak için aşağıdaki komutu kullanın:

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

1. Yeni bir sorgu penceresinde, yeni veritabanı **MyMITestDB2** Kullanıcı _grubunu_ oluşturmak için aşağıdaki komutu yürütün ve bu VERITABANıNDA _mygroup_'a SELECT izinleri verin:

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

## <a name="additional-supported-scenarios"></a>Desteklenen ek senaryolar

- SQL Aracısı yönetimi ve iş yürütmeleri, Azure AD Server sorumluları (oturumlar) için desteklenir.
- Veritabanı yedekleme ve geri yükleme işlemleri, Azure AD Server sorumluları (oturumlar) tarafından yürütülebilir.
- Azure AD Server sorumluları (oturum açmalar) ve kimlik doğrulama olayları ile ilgili tüm deyimlerin [denetlenmesi](auditing-configure.md) .
- Sunucu rolünün üyesi olan Azure AD Server sorumluları (oturumlar) için adanmış yönetici bağlantısı `sysadmin` .
- Azure AD Server sorumluları (oturumlar), [sqlcmd yardımcı programını](/sql/tools/sqlcmd-utility) ve [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) aracını kullanarak desteklenir.
- Oturum açma Tetikleyicileri, Azure AD Server sorumlularına (oturum açmalar) gelen oturum açma olayları için desteklenir.
- Hizmet Aracısı ve DB Mail, Azure AD Server sorumlularını (oturum açma) kullanarak oluşturulabilir.

## <a name="next-steps"></a>Sonraki adımlar

### <a name="enable-security-features"></a>Güvenlik özelliklerini etkinleştir

Veritabanınızı güvenli hale getirmeye yönelik yolların kapsamlı bir listesi için [SQL yönetilen örnek güvenlik özellikleri](sql-managed-instance-paas-overview.md#security-features) makalesine bakın. Aşağıdaki güvenlik özellikleri ele alınmıştır:

- [SQL yönetilen örnek denetimi](auditing-configure.md)
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Tehdit algılama](threat-detection-configure.md)
- [Dinamik veri maskeleme](/sql/relational-databases/security/dynamic-data-masking)
- [Satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security)
- [Saydam veri şifrelemesi (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>SQL yönetilen örnek özellikleri

SQL yönetilen örnek özelliklerine yönelik kapsamlı bir genel bakış için bkz.:

> [!div class="nextstepaction"]
> [SQL yönetilen örnek özellikleri](sql-managed-instance-paas-overview.md)