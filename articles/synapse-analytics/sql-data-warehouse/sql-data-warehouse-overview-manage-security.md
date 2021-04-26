---
title: Adanmış bir SQL havuzunun güvenliğini sağlama (eski adıyla SQL DW)
description: Adanmış bir SQL havuzunun güvenliğini sağlamaya yönelik ipuçları (eski adıyla SQL DW) ve Azure SYNAPSE Analytics 'te çözüm geliştirme.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: ad19c976cceab76d6eb0dbfbea5840d9764bffec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092944"
---
# <a name="secure-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış bir SQL havuzunun (eski adıyla SQL DW) güvenliğini sağlama

> [!div class="op_single_selector"]
>
> * [Güvenliğe genel bakış](sql-data-warehouse-overview-manage-security.md)
> * [Kimlik Doğrulaması](sql-data-warehouse-authentication.md)
> * [Şifreleme (portal)](sql-data-warehouse-encryption-tde.md)
> * [Şifreleme (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Bu makalede, adanmış SQL havuzunuzu (eski adıyla SQL DW) güvenli hale getirmenin temelleri adım adım açıklanmaktadır. Özellikle, bu makale, Özel SQL Havuzu (eski adıyla SQL DW) kullanarak erişimi sınırlandırma, verileri koruma ve etkinlikleri izleme kaynakları ile çalışmaya başlamanızı gösterir.

## <a name="connection-security"></a>Bağlantı güvenliği

Bağlantı Güvenliği, veritabanı bağlantılarını güvenlik duvarı kuralları ve bağlantı şifrelemesi kullanarak kısıtlamayı ve bu bağlantıların güvenliğini sağlamayı kapsar.

Güvenlik duvarı kuralları, açıkça onaylanmamış IP adreslerinden gelen bağlantı girişimlerini reddetmek için hem [MANTıKSAL SQL Server](../../azure-sql/database/logical-servers.md) hem de veritabanları tarafından kullanılır. Uygulamanızın veya istemci makinenizin ortak IP adresinden gelen bağlantılara izin vermek için, önce Azure portal, REST API veya PowerShell 'i kullanarak sunucu düzeyinde bir güvenlik duvarı kuralı oluşturmanız gerekir.

En iyi uygulama olarak, sunucu düzeyi güvenlik duvarınız aracılığıyla izin verilen IP adresi aralıklarını mümkün olduğunca kısıtlamalısınız.  Yerel bilgisayarınızdan adanmış SQL havuzunuza (eski adıyla SQL DW) erişmek için, ağınızdaki ve yerel bilgisayarınızdaki güvenlik duvarının TCP bağlantı noktası 1433 ' de giden iletişime izin verdiğinden emin olun.  

Adanmış SQL Havuzu (eski adıyla SQL DW) sunucu düzeyinde IP güvenlik duvarı kuralları kullanır. Veritabanı düzeyinde IP güvenlik duvarı kurallarını desteklemez. Daha fazla bilgi için bkz. [Azure SQL veritabanı güvenlik duvarı kuralları](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

Adanmış SQL havuzunuza (eski adıyla SQL DW) bağlantılar varsayılan olarak şifrelenir.  Şifrelemeyi devre dışı bırakmak için bağlantı ayarlarını değiştirme yok sayılır.

## <a name="authentication"></a>Kimlik Doğrulaması

Kimlik doğrulaması, veritabanına bağlanırken kimliğinizi nasıl kanıtlayacağınızı belirtir. Adanmış SQL Havuzu (eski adıyla SQL DW) Şu anda Kullanıcı adı ve parolayla SQL Server kimlik doğrulamasını destekler ve Azure Active Directory.

Veritabanınız için sunucuyu oluşturduğunuzda, bir Kullanıcı adı ve parolayla "Sunucu Yöneticisi" oturum açma adı belirttiniz. Bu kimlik bilgilerini kullanarak, bu sunucuda veritabanı sahibi olarak veya SQL Server kimlik doğrulaması aracılığıyla "dbo" olarak herhangi bir veritabanında kimlik doğrulaması yapabilirsiniz.

Ancak, en iyi uygulama olarak, kuruluşunuzun kullanıcıları kimlik doğrulaması için farklı bir hesap kullanmalıdır. Bu şekilde, uygulamaya verilen izinleri sınırlayabilir ve uygulama kodunuzun bir SQL ekleme saldırısından etkilenmesi durumunda kötü amaçlı etkinliğin riskleri azaltabilirsiniz.

SQL Server kimliği doğrulanmış bir kullanıcı oluşturmak için Sunucu Yöneticisi oturum açma bilgileriyle sunucunuzdaki **ana** veritabanına bağlanın ve yeni bir sunucu oturumu oluşturun.  Ana veritabanında da bir kullanıcı oluşturmak iyi bir fikirdir. Ana öğe içinde Kullanıcı oluşturmak, bir kullanıcının bir veritabanı adı belirtmeden SSMS gibi araçları kullanarak oturum açmasına olanak tanır.  Ayrıca, bir sunucudaki tüm veritabanlarını görüntülemek için Nesne Gezgini 'ni kullanmalarına izin verir.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Daha sonra, Sunucu Yöneticisi oturum açma bilgileriyle **ADANMıŞ SQL havuzunuza (eski ADıYLA SQL DW)** bağlanın ve oluşturduğunuz sunucu oturumuna göre bir veritabanı kullanıcısı oluşturun.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Bir kullanıcıya oturum açma veya yeni veritabanları oluşturma gibi ek işlemler gerçekleştirme izni vermek için, kullanıcıyı `Loginmanager` `dbmanager` ana veritabanındaki ve rollerine atayın.

Bu ek roller ve bir SQL veritabanında kimlik doğrulama hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda veritabanlarını ve oturum açma Işlemlerini yönetme](../../azure-sql/database/logins-create-manage.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Azure Active Directory kullanarak bağlanma hakkında daha fazla bilgi için bkz. [Azure Active Directory kimlik doğrulaması kullanarak bağlanma](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Yetkilendirme

Yetkilendirme, kimlik doğrulamasından ve bağlandıktan sonra bir veritabanı içinde yapabileceklerinizi ifade eder. Yetkilendirme ayrıcalıkları, rol üyelikleri ve izinleri tarafından belirlenir. En iyi uygulama olarak, kullanıcılarınıza gerekli olan en düşük ayrıcalıkları tanımanız gerekir. Rolleri yönetmek için aşağıdaki saklı yordamları kullanabilirsiniz:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Bağlantı kurmak için kullandığınız sunucu yöneticisi hesabı, veritabanında tüm işlemleri gerçekleştirme yetkisi olan db_owner rolünün üyesidir. Bu hesabı şema yükseltmeleri ve diğer yönetimsel işlemlerde kullanmak üzere saklayın. Uygulamanızdan veritabanına, uygulamanızın ihtiyaç duyduğu en düşük ayrıcalıklarla bağlanmak için daha sınırlı izinlere sahip olan "ApplicationUser" hesabını kullanın.

Kullanıcının veritabanında neler yapabileceğini daha fazla sınırlandırmak için yollar vardır:

* Ayrıntılı [izinler](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , veritabanındaki tek tek sütunlarda, tablolarda, görünümlerde, şemalarda, yordamlarda ve diğer nesnelerde hangi işlemleri yapabilmeniz gerektiğini denetlemenize olanak tanır. En fazla denetime sahip olmak ve gereken en düşük izinleri vermek için ayrıntılı izinler kullanın.
* Db_datareader ve db_datawriter dışındaki [veritabanı rolleri](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , daha güçlü uygulama kullanıcı hesapları veya daha az güçlü yönetim hesapları oluşturmak için kullanılabilir. Yerleşik sabit veritabanı rolleri, izin vermek için kolay bir yol sağlar, ancak gerekenden daha fazla izin verilmesine neden olabilir.
* [Saklı yordamlar](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ile veritabanında gerçekleştirilebilecek eylemler sınırlandırılabilir.

Aşağıdaki örnek, Kullanıcı tanımlı bir şemaya okuma erişimi verir.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Veritabanlarını ve sunucuları Azure portal veya Azure Resource Manager API 'sini kullanarak yönetmek, Portal Kullanıcı hesabınızın rol atamaları tarafından denetlenir. Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Şifreleme

Saydam Veri Şifrelemesi (TDE), bekleyen verilerinizi şifreleyerek ve şifresini çözerek kötü amaçlı etkinlik tehditlerine karşı korunmaya yardımcı olur. Veritabanınızı şifrelerken, ilişkili yedeklemeler ve işlem günlük dosyaları, uygulamalarınızda herhangi bir değişiklik yapılmadan şifrelenir. Saydam Veri Şifrelemesi, veritabanı şifreleme anahtarı olarak adlandırılan bir simetrik anahtarı kullanarak veritabanı depolama alanının tamamını şifreler.

SQL veritabanında, veritabanı şifreleme anahtarı yerleşik bir sunucu sertifikası tarafından korunur. Yerleşik sunucu sertifikası her bir sunucu için benzersizdir. Microsoft bu sertifikaları en az 90 günde bir otomatik olarak döndürür. Kullanılan şifreleme algoritması AES-256 ' dir. TDE genel bir açıklaması için bkz. [Saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

[Azure Portal](sql-data-warehouse-encryption-tde.md) veya [T-SQL](sql-data-warehouse-encryption-tde-tsql.md)' i kullanarak veritabanınızı şifreleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Farklı protokollerle ambarınıza bağlanma hakkındaki ayrıntılar ve örnekler için bkz. [ADANMıŞ SQL havuzuna bağlanma (eski ADıYLA SQL DW)](sql-data-warehouse-connect-overview.md).
