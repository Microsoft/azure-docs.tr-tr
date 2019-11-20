---
title: Veritabanını güvenli hale getirme
description: Azure SQL veri ambarı 'nda çözüm geliştirmeye yönelik bir veritabanını güvenli hale getirme ipuçları.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c51a945bae7cc0b03c219bc041d64f4703baef19
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692566"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>SQL veri ambarı 'nda bir veritabanının güvenliğini sağlama
> [!div class="op_single_selector"]
> * [Güvenliğe genel bakış](sql-data-warehouse-overview-manage-security.md)
> * [Kimlik doğrulaması](sql-data-warehouse-authentication.md)
> * [Şifreleme (portal)](sql-data-warehouse-encryption-tde.md)
> * [Şifreleme (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Bu makalede, Azure SQL veri ambarı veritabanınızı güvenli hale getirmenin temelleri anlatılmaktadır. Özellikle bu makale, bir veritabanında erişimi sınırlandırma, verileri koruma ve etkinlikleri izlemeye yönelik kaynakları kullanmaya başlamanızı gösterir.

## <a name="connection-security"></a>Bağlantı güvenliği
Bağlantı Güvenliği, veritabanı bağlantılarını güvenlik duvarı kuralları ve bağlantı şifrelemesi kullanarak kısıtlamayı ve bu bağlantıların güvenliğini sağlamayı kapsar.

Güvenlik duvarı kuralları hem sunucu hem de veritabanı tarafından açık olarak beyaz listeye eklenmeyen IP adreslerinden gelen bağlantıları reddetmek için kullanılır. Uygulamanızın veya istemci makinenizin ortak IP adresinden gelen bağlantılara izin vermek için, önce Azure portal, REST API veya PowerShell 'i kullanarak sunucu düzeyinde bir güvenlik duvarı kuralı oluşturmanız gerekir. En iyi uygulama olarak sunucu güvenlik duvarınızdan geçmesine izin verilen IP adresi aralıklarını mümkün olduğunca sınırlı tutmanız gerekir.  Yerel bilgisayarınızdan Azure SQL veri ambarı 'na erişmek için, ağınızdaki ve yerel bilgisayarınızdaki güvenlik duvarının TCP bağlantı noktası 1433 ' de giden iletişime izin verdiğinden emin olun.  

SQL veri ambarı, sunucu düzeyinde güvenlik duvarı kuralları kullanır. Veritabanı düzeyinde güvenlik duvarı kurallarını desteklemez. Daha fazla bilgi için bkz. [Azure SQL veritabanı güvenlik duvarı][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

SQL veri Ambarınızla kurulan bağlantılar varsayılan olarak şifrelenir.  Şifrelemeyi devre dışı bırakmak için bağlantı ayarlarını değiştirme yok sayılır.

## <a name="authentication"></a>Kimlik Doğrulaması
Kimlik doğrulaması, veritabanına bağlanırken kimliğinizi nasıl kanıtlayacağınızı belirtir. SQL veri ambarı Şu anda Kullanıcı adı ve parolayla SQL Server kimlik doğrulamasını destekler ve Azure Active Directory. 

Veritabanınıza ait mantıksal sunucuyu oluşturduktan sonra kullanıcı adı ve parola belirleyerek "sunucu yöneticisi" oturum açma bilgisi oluşturdunuz. Bu kimlik bilgilerini kullanarak, bu sunucuda veritabanı sahibi olarak veya SQL Server kimlik doğrulaması aracılığıyla "dbo" olarak herhangi bir veritabanında kimlik doğrulaması yapabilirsiniz.

Ancak, en iyi uygulama olarak, kuruluşunuzun kullanıcıları kimlik doğrulaması için farklı bir hesap kullanmalıdır. Bu şekilde, uygulamaya verilen izinleri sınırlayabilir ve uygulama kodunuzun bir SQL ekleme saldırısından etkilenmesi durumunda kötü amaçlı etkinliğin riskleri azaltabilirsiniz. 

SQL Server kimliği doğrulanmış bir kullanıcı oluşturmak için Sunucu Yöneticisi oturum açma bilgileriyle sunucunuzdaki **ana** veritabanına bağlanın ve yeni bir sunucu oturumu oluşturun.  Ayrıca, Azure SQL veri ambarı kullanıcıları için ana veritabanında bir kullanıcı oluşturmak iyi bir fikirdir. Ana öğe içinde Kullanıcı oluşturmak, bir kullanıcının bir veritabanı adı belirtmeden SSMS gibi araçları kullanarak oturum açmasına olanak tanır.  Ayrıca, bir SQL Server üzerindeki tüm veritabanlarını görüntülemek için Nesne Gezgini 'ni kullanmalarına izin verir.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ardından, Sunucu Yöneticisi oturum açma bilgilerinizi kullanarak **SQL veri ambarı veritabanınıza** bağlanın ve oluşturduğunuz sunucu oturumuna göre bir veritabanı kullanıcısı oluşturun.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Bir kullanıcıya oturum açma veya yeni veritabanları oluşturma gibi ek işlemler gerçekleştirme izni vermek için, kullanıcıyı ana veritabanındaki `Loginmanager` ve `dbmanager` rollere atayın. Bu ek roller ve bir SQL veritabanında kimlik doğrulama hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda veritabanlarını ve oturum açma Işlemlerini yönetme][Managing databases and logins in Azure SQL Database].  Daha fazla bilgi için bkz. [Azure Active Directory kimlik doğrulaması kullanarak SQL Data Warehouse 'A bağlanma][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Yetkilendirme
Yetkilendirme, bir Azure SQL veri ambarı veritabanında yapabileceklerinizi ifade eder. Yetkilendirme ayrıcalıkları, rol üyelikleri ve izinleri tarafından belirlenir. En iyi uygulama olarak, kullanıcılarınıza gerekli olan en düşük ayrıcalıkları tanımanız gerekir. Rolleri yönetmek için aşağıdaki saklı yordamları kullanabilirsiniz:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Bağlantı kurmak için kullandığınız sunucu yöneticisi hesabı, veritabanında tüm işlemleri gerçekleştirme yetkisi olan db_owner rolünün üyesidir. Bu hesabı şema yükseltmeleri ve diğer yönetimsel işlemlerde kullanmak üzere saklayın. Uygulamanızdan veritabanına, uygulamanızın ihtiyaç duyduğu en düşük ayrıcalıklarla bağlanmak için daha sınırlı izinlere sahip olan "ApplicationUser" hesabını kullanın.

Bir kullanıcının Azure SQL veri ambarı ile neler yapabileceğini daha da kısıtlama yolları vardır:

* Ayrıntılı [izinler][Permissions] , veritabanındaki tek tek sütunlarda, tablolarda, görünümlerde, şemalarda, yordamlarda ve diğer nesnelerde hangi işlemleri yapabilmeniz gerektiğini denetlemenize olanak tanır. En fazla denetime sahip olmak ve gereken en düşük izinleri vermek için ayrıntılı izinler kullanın. 
* Db_datareader ve db_datawriter dışındaki [veritabanı rolleri][Database roles] , daha güçlü uygulama kullanıcı hesapları veya daha az güçlü yönetim hesapları oluşturmak için kullanılabilir. Yerleşik sabit veritabanı rolleri, izin vermek için kolay bir yol sağlar, ancak gerekenden daha fazla izin verilmesine neden olabilir.
* [Saklı yordamlar][Stored procedures] ile veritabanında gerçekleştirilebilecek eylemler sınırlandırılabilir.

Aşağıdaki örnek, Kullanıcı tanımlı bir şemaya okuma erişimi verir.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Veritabanlarını ve mantıksal sunucuları Azure portal yönetme veya Azure Resource Manager API 'sini kullanma, Portal Kullanıcı hesabınızın rol atamalarınız tarafından denetlenir. Daha fazla bilgi için, bkz. [Azure Portal rol tabanlı erişim denetimi][Role-based access control in Azure portal].

## <a name="encryption"></a>Şifreleme
Azure SQL veri ambarı Saydam Veri Şifrelemesi (TDE), bekleyen verilerinizi şifreleyerek ve şifresini çözerek kötü amaçlı etkinlik tehditlerine karşı korunmaya yardımcı olur.  Veritabanınızı şifrelerken, ilişkili yedeklemeler ve işlem günlük dosyaları, uygulamalarınızda herhangi bir değişiklik yapılmadan şifrelenir. TDE, veritabanı şifreleme anahtarı adlı bir simetrik anahtar kullanarak veritabanının tamamının depolanmasını şifreler. 

SQL veritabanında, veritabanı şifreleme anahtarı yerleşik bir sunucu sertifikası tarafından korunur. Yerleşik sunucu sertifikası her SQL veritabanı sunucusu için benzersizdir. Microsoft bu sertifikaları en az 90 günde bir otomatik olarak döndürür. SQL veri ambarı tarafından kullanılan şifreleme algoritması AES-256 ' dir. TDE genel bir açıklaması için bkz. [Saydam veri şifrelemesi][Transparent Data Encryption].

[Azure Portal][Encryption with Portal] veya [T-SQL][Encryption with TSQL]' i kullanarak veritabanınızı şifreleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
SQL veri ambarınıza farklı protokollerle bağlanma hakkındaki ayrıntılar ve örnekler için bkz. [SQL Data Warehouse 'A bağlanma][Connect to SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Connect to SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
