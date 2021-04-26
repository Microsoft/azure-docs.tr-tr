---
title: Oturum açma bilgilerini ve Kullanıcı hesaplarını kullanarak sunucu ve veritabanı erişimini yetkilendir
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure SQL veritabanı, SQL yönetilen örneği ve Azure SYNAPSE 'in, oturum açma bilgileri ve Kullanıcı hesapları kullanarak erişim için kullanıcıların kimliğini nasıl doğrulayacağını öğrenin. Ayrıca, Kullanıcı Yetkilendirme ve kullanıcılara işlem ve sorgu verileri sağlamak için veritabanı rolleri ve açık izinler verme hakkında bilgi edinin.
keywords: sql veritabanı güvenliği,veritabanı güvenliği yönetimi,oturum açma güvenliği,veritabanı güvenliği,veritabanı erişimi
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 03/23/2020
ms.openlocfilehash: a0e9cc9967509eba05bd26f3bad86eefeeb6ca3e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639481"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>SQL Veritabanı’na, SQL Yönetilen Örneği’ne ve Azure Synapse Analytics’e veritabanı erişimini yetkilendirme
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Bu makalede şunları öğreneceksiniz:

- Kullanıcıların yönetim görevleri gerçekleştirmesini ve bu veritabanlarında depolanan verilere erişmesini sağlamak için Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics 'i yapılandırma seçenekleri.
- İlk olarak yeni bir sunucu oluşturulduktan sonra erişim ve yetkilendirme yapılandırması.
- Ana veritabanına ve Kullanıcı hesaplarına oturum açma bilgileri ve Kullanıcı hesapları ekleme ve ardından bu hesaplara yönetici izinleri verme.
- Kullanıcı veritabanlarına, oturum açmalar veya dahil edilen kullanıcı hesaplarıyla ilişkili kullanıcı hesapları ekleme.
- Veritabanı rollerini ve açık izinleri kullanarak Kullanıcı veritabanlarında izinleri olan kullanıcı hesaplarını yapılandırın.

> [!IMPORTANT]
> Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE ' deki veritabanları, bu makalenin geri kalanında veritabanı olarak ve sunucu, Azure SQL veritabanı ve Azure SYNAPSE için veritabanlarını yöneten [sunucuya](logical-servers.md) başvurmaktadır.

## <a name="authentication-and-authorization"></a>Kimlik doğrulaması ve yetkilendirme

[**Kimlik doğrulama**](security-overview.md#authentication) , kullanıcının talep ettikleri kim olduğunu kanıtlama işlemidir. Kullanıcı bir kullanıcı hesabı kullanarak bir veritabanına bağlanır.
Bir Kullanıcı bir veritabanına bağlanmaya çalıştığında, bir kullanıcı hesabı ve kimlik doğrulama bilgileri sağlarlar. Kullanıcının kimliği, aşağıdaki iki kimlik doğrulama yönteminden birini kullanarak doğrulanır:

- [SQL kimlik doğrulaması](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Bu kimlik doğrulama yöntemiyle kullanıcı bir bağlantı kurmak için bir kullanıcı hesabı adı ve ilişkili parola gönderir. Bu parola, bir oturum açma ile bağlantılı veya bir oturum açma ile bağlantılı *olmayan* Kullanıcı hesaplarını içeren veritabanında depolanan kullanıcı hesapları için ana veritabanında depolanır.
- [Azure Active Directory Kimlik Doğrulaması](authentication-aad-overview.md)

  Bu kimlik doğrulama yöntemiyle kullanıcı, bir kullanıcı hesabı adı ve hizmetin Azure Active Directory (Azure AD) ' de depolanan kimlik bilgisi bilgilerini kullandığı istekleri gönderir.

**Oturum açmalar ve kullanıcılar**: bir veritabanındaki bir kullanıcı hesabı, ana veritabanında depolanan bir oturum ile ilişkilendirilebilir veya ayrı bir veritabanında depolanan bir Kullanıcı adı olabilir.

- **Oturum açma** , ana veritabanında bir veya daha fazla veritabanında bulunan bir kullanıcı hesabının bağlanacağı bireysel bir hesaptır. Oturum açma hesabında, kullanıcı hesabının kimlik bilgileri oturum açma hesabıyla depolanır.
- **Kullanıcı hesabı** , herhangi bir veritabanında bir oturum açma ile bağlantılı olan, ancak olması gereken tek bir hesaptır. Oturum açmaya bağlantılı olmayan bir kullanıcı hesabında kimlik bilgileri kullanıcı hesabında depolanır.

Veri erişimi ve çeşitli eylemleri gerçekleştirme [**yetkilendirmesi**](security-overview.md#authorization) , veritabanı rolleri ve açık izinler kullanılarak yönetilir. Yetkilendirme, bir kullanıcıya atanan izinleri ifade eder ve kullanıcının ne yapmasına izin verileceğini belirler. Yetkilendirme, Kullanıcı hesabınızın veritabanı [rolü üyelikleri](/sql/relational-databases/security/authentication-access/database-level-roles) ve [nesne düzeyi izinleri](/sql/relational-databases/security/permissions-database-engine)tarafından denetlenir. En iyi uygulama olarak, kullanıcılarınıza gerekli olan en düşük ayrıcalıkları tanımanız gerekir.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Yeni bir veritabanı oluşturduktan sonra mevcut oturumlar ve Kullanıcı hesapları

Azure SQL 'i ilk kez dağıttığınızda, bu oturum açma için yönetici oturumu ve ilişkili bir parola belirtirsiniz. Bu yönetim hesabına **Sunucu Yöneticisi** adı verilir. Dağıtım sırasında, ana ve Kullanıcı veritabanlarındaki oturum açma ve kullanıcıların aşağıdaki yapılandırması oluşur:

- Yönetim ayrıcalıklarına sahip bir SQL oturumu açma, belirttiğiniz oturum açma adı kullanılarak oluşturulur. [Oturum açma](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) , SQL VERITABANı, SQL yönetilen örneği ve Azure SYNAPSE 'da oturum açmak için bireysel bir kullanıcı hesabıdır.
- Bu oturum açma, tüm veritabanlarında [sunucu düzeyi sorumlusu](/sql/relational-databases/security/authentication-access/principals-database-engine)olarak tam yönetim izinleri verilmesini ister. Oturum açma tüm kullanılabilir izinlere sahiptir ve bunlarla sınırlı olamaz. SQL yönetilen örneğinde, bu oturum açma [sysadmin sabit sunucu rolüne](/sql/relational-databases/security/authentication-access/server-level-roles) eklenir (Bu rol Azure SQL veritabanında bulunmaz).
- [](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) `dbo` Bu oturum için her kullanıcı veritabanında çağrılan bir kullanıcı hesabı oluşturulur. [Dbo](/sql/relational-databases/security/authentication-access/principals-database-engine) kullanıcısı veritabanında tüm veritabanı izinlerine sahiptir ve `db_owner` sabit veritabanı rolüne eşlenir. Ek sabit veritabanı rolleri bu makalenin ilerleyen kısımlarında ele alınmıştır.

Bir veritabanının yönetici hesaplarını belirlemek için, Azure portal açın ve sunucunuzun veya yönetilen örneğinizin **Özellikler** sekmesine gidin.

![SQL Server Yöneticileri](./media/logins-create-manage/sql-admins.png)

![Özellikler menü seçeneğini vurgulayan ekran görüntüsü.](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> Yönetici oturum açma adı oluşturulduktan sonra değiştirilemez. Sunucu yöneticisinin parolasını sıfırlamak için [Azure Portal](https://portal.azure.com)gidin, **SQL sunucuları**' na tıklayın, listeden sunucuyu seçin ve ardından **Parolayı Sıfırla**' ya tıklayın. SQL yönetilen örneği parolasını sıfırlamak için Azure portal gidin, örneğe tıklayın ve **Parolayı Sıfırla**' ya tıklayın. PowerShell veya Azure CLı 'yi de kullanabilirsiniz.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Yönetici izinlerine sahip ek oturumlar ve kullanıcılar oluşturun

Bu noktada, sunucunuz veya yönetilen örneğiniz yalnızca tek bir SQL oturum açma ve Kullanıcı hesabı kullanılarak erişim için yapılandırılmıştır. Tam veya kısmi yönetim izinleriyle ek oturumlar oluşturmak için aşağıdaki seçeneklere (dağıtım modunuza bağlı olarak) sahipsiniz:

- **Tam yönetici izinleriyle bir Azure Active Directory yönetici hesabı oluşturun**

  Azure Active Directory kimlik doğrulamasını etkinleştirin ve bir Azure AD yönetici oturumu oluşturun. Bir Azure Active Directory hesap, Azure SQL dağıtımının Yöneticisi olarak tam yönetici izinleriyle yapılandırılabilir. Bu hesap, bir bireysel veya güvenlik grubu hesabı olabilir. SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE 'e bağlanmak için Azure AD hesapları kullanmak istiyorsanız bir Azure **ad Yöneticisi yapılandırılmalıdır** . Tüm Azure SQL dağıtım türleri için Azure AD kimlik doğrulamasını etkinleştirme hakkında ayrıntılı bilgi için aşağıdaki makalelere bakın:

  - [SQL ile kimlik doğrulaması için Azure Active Directory kimlik doğrulaması kullanma](authentication-aad-overview.md)
  - [SQL ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](authentication-aad-configure.md)

- **SQL yönetilen örneği 'nde, tam yönetici izinleriyle SQL oturumları oluşturun**

  - Ana veritabanında ek bir SQL oturum açma oluşturun.
  - [Alter Server role](/sql/t-sql/statements/alter-server-role-transact-sql) deyimini kullanarak [sysadmin sabit sunucu rolüne](/sql/relational-databases/security/authentication-access/server-level-roles) oturum açma ekleyin. Bu oturum açma tam yönetim izinlerine sahip olacaktır.
  - Alternatif olarak, [create LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) sözdizimini kullanarak BIR [Azure AD oturum açma bilgisi](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) oluşturun.

- **SQL veritabanı 'nda, sınırlı yönetim izinleriyle SQL oturumları oluşturun**

  - Ana veritabanında ek bir SQL oturum açma oluşturun.
  - Ana veritabanında bu yeni oturum açmayla ilişkili bir kullanıcı hesabı oluşturun.
  - `dbmanager` `loginmanager` Alter role deyimini kullanarak veritabanında, role veya her ikisine de Kullanıcı hesabını ekleyin `master` (Azure SYNAPSE [](/sql/t-sql/statements/alter-role-transact-sql) için [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) deyimini kullanın).

  > [!NOTE]
  > `dbmanager` ve `loginmanager` rolleri, SQL yönetilen örnek dağıtımlarıyla ilgili **değildir** .

  Azure SQL veritabanı için bu [özel ana veritabanı rollerinin](/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) üyeleri veritabanları oluşturma ve yönetme veya oturum açma işlemleri oluşturma ve Yönetme yetkisine sahiptir. Rolün üyesi olan bir kullanıcı tarafından oluşturulan veritabanlarında `dbmanager` , üye `db_owner` sabit veritabanı rolüne eşlenir ve bu veritabanını oturum açıp `dbo` Kullanıcı hesabını kullanarak yönetebilir. Bu rollerin, ana veritabanı dışında açık izinleri yoktur.

  > [!IMPORTANT]
  > SQL veritabanında tam yönetici izinleriyle ek bir SQL oturumu oluşturamazsınız.

## <a name="create-accounts-for-non-administrator-users"></a>Yönetici olmayan kullanıcılar için hesap oluşturma

Yönetici olmayan kullanıcılar için, iki yöntemden birini kullanarak hesap oluşturabilirsiniz:

- **Oturum oluşturur**

  Ana veritabanında bir SQL oturum açma oluşturun. Ardından, kullanıcının erişmesi gereken her veritabanında bir kullanıcı hesabı oluşturun ve Kullanıcı hesabını bu oturum açmayla ilişkilendirin. Bu yaklaşım, kullanıcının birden çok veritabanına erişmesi gerektiğinde ve parolaları eşitlenmiş halde tutmak istediğinizde tercih edilir. Ancak, bu yaklaşım, hem birincil sunucuda hem de ikincil sunucularda oturum açmanın oluşturulması için coğrafi çoğaltma ile birlikte kullanıldığında karmaşıklıkları vardır. Daha fazla bilgi için bkz. [coğrafi geri yükleme veya yük devretme Için Azure SQL veritabanı güvenliğini yapılandırma ve yönetme](active-geo-replication-security-configure.md).
- **Kullanıcı hesabı oluşturma**

  Bir kullanıcının erişim ihtiyacı olan veritabanında ( [Kapsanan kullanıcı](/sql/relational-databases/security/contained-database-users-making-your-database-portable)olarak da adlandırılır) bir kullanıcı hesabı oluşturun.

  - SQL veritabanı ile, her zaman bu tür bir kullanıcı hesabı oluşturabilirsiniz.
  - [Azure AD Server sorumlularını](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)destekleyen SQL yönetilen örneği sayesinde, veritabanı kullanıcılarının kapsanan veritabanı kullanıcısı olarak OLUŞTURULMASıNı gerektirmeden SQL yönetilen örneği için kimlik doğrulaması yapmak üzere Kullanıcı hesapları oluşturabilirsiniz.

  Bu yaklaşımda, Kullanıcı kimlik doğrulama bilgileri her bir veritabanında depolanır ve coğrafi olarak çoğaltılan veritabanlarına otomatik olarak çoğaltılır. Ancak, birden çok veritabanında aynı hesap varsa ve Azure SQL kimlik doğrulaması kullanıyorsanız, parolaları el ile eşitlenmiş tutmanız gerekir. Ayrıca, bir kullanıcının farklı parolalara sahip farklı veritabanlarında bir hesabı varsa, bu Parolaları hatırlama bir sorun haline gelebilir.

> [!IMPORTANT]
> Azure AD kimlikleriyle eşleştirilmiş kapsanan kullanıcıları oluşturmak için Azure SQL veritabanı 'nda veritabanında yönetici olan bir Azure AD hesabı kullanarak oturum açmış olmanız gerekir. SQL yönetilen örneğinde, izinlerle bir SQL oturumu açma `sysadmin` da Azure AD oturum açma veya Kullanıcı oluşturabilir.

Oturum açma ve Kullanıcı oluşturma işlemlerini gösteren örnekler için bkz.:

- [Azure SQL veritabanı için oturum açma oluştur](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)
- [Azure SQL yönetilen örneği için oturum açma oluştur](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true#examples-2)
- [Azure SYNAPSE için oturum açma oluşturma](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-3)
- [Kullanıcı Oluştur](/sql/t-sql/statements/create-user-transact-sql#examples)
- [Azure AD kapsanan kullanıcılar oluşturuluyor](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> Azure SQL veritabanı 'nda Kullanıcı oluşturmayı içeren bir güvenlik öğreticisi için bkz. [öğretici: güvenli Azure SQL veritabanı](secure-database-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Sabit ve özel veritabanı rollerini kullanma

Bir veritabanında bir oturum açma ya da kapsanan Kullanıcı olarak bir kullanıcı hesabı oluşturduktan sonra, söz konusu kullanıcıya çeşitli eylemler gerçekleştirme ve belirli bir veritabanındaki verilere erişme yetkisi verebilirsiniz. Erişimi yetkilendirmek için aşağıdaki yöntemleri kullanabilirsiniz:

- **Düzeltilen veritabanı rolleri**

  Kullanıcı hesabını bir [sabit veritabanı rolüne](/sql/relational-databases/security/authentication-access/database-level-roles)ekleyin. Her biri tanımlanmış bir izin kümesine sahip 9 sabit veritabanı rolü vardır. En yaygın sabit veritabanı rolleri şunlardır: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** ve **db_denydatareader**. Birkaç kullanıcıya tam izin vermek için genelde **db_owner** kullanılır. Diğer sabit veritabanı rolleri, geliştirme aşamasında basit bir veritabanını hızlı bir şekilde kullanıma almak için kullanışlıdır ancak çoğu üretim veritabanı için önerilmez. Örneğin, **db_datareader** sabit veritabanı rolü, veritabanı içindeki her tabloya okuma erişimi verir ve bu, kesinlikle gereklidir.

  - Sabit bir veritabanı rolüne kullanıcı eklemek için:

    - Azure SQL veritabanı 'nda [alter role](/sql/t-sql/statements/alter-role-transact-sql) deyimini kullanın. Örnekler için bkz. [alter role örnekleri](/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure SYNAPSE, [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ifadesini kullanın. Örnekler için bkz. [sp_addrolemember örnekleri](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql#examples).

- **Özel veritabanı rolü**

  [Create role](/sql/t-sql/statements/create-role-transact-sql) ifadesini kullanarak özel bir veritabanı rolü oluşturun. Özel bir rol, kendi Kullanıcı tanımlı veritabanı rollerinizi oluşturmanıza ve her bir role iş ihtiyacı için gereken en az izni sağlamanıza olanak sağlar. Ardından, özel Role Kullanıcı ekleyebilirsiniz. Birden fazla rolün üyesi olan kullanıcılar, tüm rollerin izinlerine sahip olur.
- **İzinleri doğrudan verme**

  Kullanıcı hesabı [izinlerini](/sql/relational-databases/security/permissions-database-engine) doğrudan verin. SQL Veritabanında ayrı ayrı verilebilen veya reddedilebilen 100'den fazla izin vardır. Bu izinlerin çoğu iç içe geçmiş haldedir. Örneğin, bir şemada için verilen `UPDATE` izni, o şema içindeki tüm tablolar için `UPDATE` iznini de içerir. Çoğu izin sisteminde olduğu gibi bir iznin reddedilmesi, aynı iznin verilme durumunu geçersiz kılar. İç içe geçmiş yapısı ve izin sayısı nedeniyle, veritabanınızı doğru şekilde korumak için uygun bir izin sistemi tasarlamak uzun ve dikkatli bir çalışma gerektirebilir. [İzinler (Veritabanı Altyapısı)](/sql/relational-databases/security/permissions-database-engine) ile başlayın ve izinlerin [poster boyutundaki tablosunu](/sql/relational-databases/security/media/database-engine-permissions.png) inceleyin.

## <a name="using-groups"></a>Grupları kullanma

Verimli erişim yönetimi, bireysel kullanıcılar yerine Active Directory güvenlik gruplarına ve sabit veya özel rollere atanan izinleri kullanır.

- Azure Active Directory kimlik doğrulaması kullanırken, Azure Active Directory Kullanıcıları Azure Active Directory güvenlik grubuna yerleştirin. Grup için bir bağımsız veritabanı kullanıcısı oluşturun. Özel veya yerleşik veritabanı rollerine üye olarak bir veya daha fazla veritabanı kullanıcısı ekleyin bu kullanıcı grubuna uygun olan belirli izinlerle.

- SQL kimlik doğrulaması kullanırken, veritabanında kapsanan veritabanı kullanıcıları oluşturun. Bir veya daha fazla veritabanı kullanıcıyı, bu kullanıcı grubuna uygun belirli izinlerle özel bir veritabanı rolüne yerleştirin.

  > [!NOTE]
  > Ayrıca, içerilmeyen veritabanı kullanıcıları için grupları kullanabilirsiniz.

İzinleri sınırlamak veya yükseltmek için kullanılabilen aşağıdaki özellikleri tanımanız gerekir:

- [Kimliğe Bürünme](/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) ve [modül imzalama](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) ile izinler geçici olarak ve güvenli bir şekilde artırılabilir.
- [Satır Düzeyinde Güvenlik](/sql/relational-databases/security/row-level-security) ile bir kullanıcının erişebileceği satırlar sınırlandırılabilir.
- [Veri Maskeleme](dynamic-data-masking-overview.md) ile hassas verilerin kapsamı sınırlandırılabilir.
- [Saklı yordamlar](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) ile veritabanında gerçekleştirilebilecek eylemler sınırlandırılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Tüm Azure SQL veritabanı ve SQL yönetilen örnek güvenlik özelliklerine genel bakış için bkz. [Güvenliğe genel bakış](security-overview.md).
