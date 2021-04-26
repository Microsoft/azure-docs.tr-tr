---
title: SQL Server & Azure SQL yönetilen örneği arasındaki T-SQL farklılıkları
description: Bu makalede, Azure SQL yönetilen örneği ve SQL Server arasındaki Transact-SQL (T-SQL) farklılıkları ele alınmaktadır.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 3/16/2021
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: f744b718919a6da75b2064efdc163ef4618b5a7c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815909"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>SQL Server & Azure SQL yönetilen örneği arasındaki T-SQL farklılıkları
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Bu makalede, Azure SQL yönetilen örneği ve SQL Server arasındaki sözdizimi ve davranış farklılıkları özetlenmektedir ve açıklanmaktadır. 


SQL yönetilen örneği SQL Server veritabanı altyapısı ile yüksek uyumluluk sağlar ve birçok özellik SQL yönetilen örneğinde desteklenir.

![SQL Server 'den kolay geçiş](./media/transact-sql-tsql-differences-sql-server/migration.png)

SQL yönetilen örneği 'nde tanıtılan bazı PaaS sınırlamaları vardır ve bazı davranış değişiklikleri SQL Server ile karşılaştırılır. Farklar aşağıdaki kategorilere ayrılmıştır: <a name="Differences"></a>

- [Kullanılabilirlik](#availability) , [her zaman açık kullanılabilirlik grupları](#always-on-availability-groups) ve [yedeklemelerdeki](#backup)farkları içerir.
- [Güvenlik](#security) , [Denetim](#auditing), [sertifika](#certificates), [kimlik bilgileri](#credential), [şifreleme sağlayıcıları](#cryptographic-providers), [oturum açmalar ve kullanıcılar](#logins-and-users)ve [hizmet anahtarı ile hizmet ana anahtarı](#service-key-and-service-master-key)arasındaki farkları içerir.
- [Yapılandırma](#configuration) , [arabellek havuzu genişletme](#buffer-pool-extension), [harmanlama](#collation), [Uyumluluk düzeyleri](#compatibility-levels), [veritabanı yansıtma](#database-mirroring), [veritabanı seçenekleri](#database-options), [SQL Server Agent](#sql-server-agent)ve [tablo seçeneklerindeki](#tables)farklılıkları içerir.
- [İşlevler](#functionalities) şunlardır [bulk INSERT/OPENROWSET](#bulk-insert--openrowset), [clr](#clr), [DBCC](#dbcc), [Dağıtılmış işlemler](#distributed-transactions), [genişletilmiş olaylar](#extended-events), [dış kitaplıklar](#external-libraries), [FILESTREAM ve FileTable](#filestream-and-filetable), [tam metin anlam arama](#full-text-semantic-search), [bağlı sunucular](#linked-servers), [PolyBase](#polybase), [çoğaltma](#replication), [geri yükleme](#restore-statement), [Hizmet Aracısı](#service-broker), [saklı yordamlar, işlevler ve Tetikleyiciler](#stored-procedures-functions-and-triggers).
- Sanal ağlar ve alt ağ yapılandırması gibi [ortam ayarları](#Environment) .

Bu özelliklerin çoğu mimari kısıtlamalardır ve hizmet özelliklerini temsil eder.

SQL yönetilen örneğinde bulunan ve gelecekte çözümlenecek olan geçici bilinen sorunlar, [sürüm notları sayfasında](../database/doc-changes-updates-release-notes.md)açıklanmaktadır.

## <a name="availability"></a>Kullanılabilirlik

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>AlwaysOn Kullanılabilirlik Grupları

[Yüksek KULLANıLABILIRLIK](../database/high-availability-sla.md) SQL yönetilen örneği ile yerleşiktir ve kullanıcılar tarafından denetlenemez. Aşağıdaki deyimler desteklenmez:

- [UÇ NOKTA OLUŞTUR... DATABASE_MIRRORING IÇIN](/sql/t-sql/statements/create-endpoint-transact-sql)
- [KULLANıLABILIRLIK GRUBU OLUŞTUR](/sql/t-sql/statements/create-availability-group-transact-sql)
- [KULLANıLABILIRLIK GRUBUNU DEĞIŞTIR](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [KULLANıLABILIRLIK GRUBU BıRAKMA](/sql/t-sql/statements/drop-availability-group-transact-sql)
- [Alter database](/sql/t-sql/statements/alter-database-transact-sql) ifadesinin [set HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) yan tümcesi

### <a name="backup"></a>Backup

SQL yönetilen örneğinin otomatik yedeklemeleri olduğundan, kullanıcılar tam veritabanı `COPY_ONLY` yedeklemeleri oluşturabilir. Fark, günlük ve dosya anlık görüntüsü yedeklemeleri desteklenmez.

- SQL yönetilen örneği ile bir örnek veritabanını yalnızca bir Azure Blob depolama hesabına yedekleyebilirsiniz:
  - Yalnızca `BACKUP TO URL` desteklenir.
  - `FILE`, `TAPE` , ve yedekleme cihazları desteklenmez.
- Genel `WITH` seçeneklerin çoğu desteklenir.
  - `COPY_ONLY` zorunludur.
  - `FILE_SNAPSHOT` desteklenmez.
  - Bant seçenekleri: `REWIND` , `NOREWIND` , `UNLOAD` , ve `NOUNLOAD` desteklenmez.
  - Günlüğe özel seçenekler: `NORECOVERY` , `STANDBY` ve `NO_TRUNCATE` desteklenmez.

Sınırlamalar: 

- SQL yönetilen örneği sayesinde, yedekleme sıkıştırması kullanılıyorsa 4 TB 'a kadar olan veritabanları için yeterli olan bir örnek veritabanını en fazla 32 şeritli bir yedeklemeye yedekleyebilirsiniz.
- `BACKUP DATABASE ... WITH COPY_ONLY`Hizmet tarafından yönetilen saydam veri şifrelemesi (TDE) ile şifrelenmiş bir veritabanında yürütemezsiniz. Hizmet tarafından yönetilen TDE, yedeklemelerin dahili bir TDE anahtarla şifrelenmesini zorlar. Anahtar verilemiyor, bu nedenle yedeklemeyi geri alamazsınız. Otomatik yedeklemeler ve zaman içinde geri yükleme kullanın veya bunun yerine [müşteri tarafından yönetilen (BYOK) TDE](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) kullanın. Ayrıca, veritabanında şifrelemeyi devre dışı bırakabilirsiniz.
- Yönetilen bir örnek üzerinde gerçekleştirilen yerel yedeklemeler SQL Server geri yüklenemez. Bunun nedeni, yönetilen Örneğin SQL Server herhangi bir sürümüne kıyasla daha yüksek iç veritabanı sürümüne sahip olmasından kaynaklanır.
- SQL yönetilen örneği 'nde komutu kullanılarak en fazla yedek şerit boyutu `BACKUP` 195 GB 'tır ve bu en büyük blob boyutudur. Tek tek Stripe boyutunu azaltmak ve bu sınırın içinde kalmak için yedekleme komutundaki şeritler sayısını artırın.

    > [!TIP]
    > Bu kısıtlamayı geçici olarak çözmek için, bir veritabanını şirket içi bir ortamda veya bir sanal makinede SQL Server yedekleyerek şunları yapabilirsiniz:
    >
    > - ' A yedeklemek `DISK` yerine uygulamasına yedekleyin `URL` .
    > - Yedekleme dosyalarını blob depolamaya yükleyin.
    > - SQL yönetilen örneğine geri yükleyin.
    >
    > `Restore`Karşıya yüklenen yedekleme dosyalarının depolanması için farklı bir blob türü kullanıldığından, SQL yönetilen örneğindeki komut yedekleme dosyalarındaki daha büyük blob boyutlarını destekler.

T-SQL kullanarak yedeklemeler hakkında daha fazla bilgi için bkz. [yedekleme](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Güvenlik

### <a name="auditing"></a>Denetim

Microsoft Azure SQL ve SQL Server içindeki denetim arasındaki temel farklılıklar şunlardır:

- SQL yönetilen örneği ile denetim, sunucu düzeyinde çalışmaktadır. `.xel`Günlük dosyaları Azure Blob depolama alanında depolanır.
- Azure SQL veritabanı ile denetim veritabanı düzeyinde çalışmaktadır. `.xel`Günlük dosyaları Azure Blob depolama alanında depolanır.
- SQL Server, şirket içi veya sanal makinelerde, denetim sunucu düzeyinde çalışmaktadır. Olaylar dosya sistemi veya Windows olay günlüklerine depolanır.
 
SQL yönetilen örneği 'nde XEvent denetimi Azure Blob depolama hedeflerini destekler. Dosya ve Windows günlükleri desteklenmez.

`CREATE AUDIT`Azure Blob depolamaya denetim için söz diziminde önemli farklılıklar şunlardır:

- `TO URL`Dosyaların yerleştirildiği Azure Blob depolama KAPSAYıCıSıNıN URL 'sini belirtmek için kullanabileceğiniz yeni bir sözdizimi sağlanır `.xel` .
- `TO FILE`SQL yönetilen örnek Windows dosya paylaşımlarına erişemediği için sözdizimi desteklenmiyor.

Daha fazla bilgi için bkz. 

- [SUNUCU DENETIMI OLUŞTUR](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [SUNUCU DENETIMINI DEĞIŞTIR](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Denetim](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Sertifikalar

SQL yönetilen örneği dosya paylaşımlarına ve Windows klasörlerine erişemez, bu nedenle aşağıdaki kısıtlamalar geçerlidir:

- `CREATE FROM` / `BACKUP TO` Dosya sertifikalar için desteklenmiyor.
- `CREATE` / `BACKUP` Sertifika `FILE` / `ASSEMBLY` desteklenmiyor. Özel anahtar dosyaları kullanılamıyor. 

Bkz. SERTIFIKA ve [yedekleme sertifikası](/sql/t-sql/statements/backup-certificate-transact-sql) [oluşturma](/sql/t-sql/statements/create-certificate-transact-sql) . 
 
**Geçici çözüm**: sertifika yedeklemesi oluşturmak ve yedeklemeyi geri yüklemek yerine [sertifika ikili içeriğini ve özel anahtarı alın, bunu. SQL dosyası olarak depolayın ve ikiliden oluşturun](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Kimlik Bilgisi

Yalnızca Azure Key Vault ve `SHARED ACCESS SIGNATURE` kimlikleri desteklenir. Windows kullanıcıları desteklenmez.

Bkz. [KIMLIK bilgisi oluşturma](/sql/t-sql/statements/create-credential-transact-sql) ve [kimlik bilgisi değiştirme](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Şifreleme sağlayıcıları

SQL yönetilen örneği dosyalara erişemez, bu nedenle şifreleme sağlayıcıları oluşturulamıyor:

- `CREATE CRYPTOGRAPHIC PROVIDER` desteklenmez. Bkz. [ŞIFRELEME sağlayıcısı oluşturma](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` desteklenmez. Bkz. [alter CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Oturum açma bilgileri ve kullanıcılar

- , Ve kullanılarak oluşturulan SQL oturum açmaları `FROM CERTIFICATE` `FROM ASYMMETRIC KEY` `FROM SID` desteklenir. Bkz. [oturum oluşturma](/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD), [oturum açma sözdizimi oluştur](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) veya [oturum açma işleminden Kullanıcı oluşturma [Azure AD oturum açma]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true) sözdizimi kullanılarak oluşturulan sunucu sorumluları (oturumlar) desteklenir. Bu oturumlar sunucu düzeyinde oluşturulur.

    SQL yönetilen örneği, söz dizimi ile Azure AD veritabanı sorumlularını destekler `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` . Bu özellik, Azure AD içeren veritabanı kullanıcıları olarak da bilinir.

- Sözdizimi ile oluşturulan Windows oturum açmaları `CREATE LOGIN ... FROM WINDOWS` desteklenmez. Azure Active Directory oturumlarını ve kullanıcıları kullanın.
- Örneği oluşturan Azure AD kullanıcısının [sınırsız yönetici ayrıcalıkları](../database/logins-create-manage.md)vardır.
- Yönetici olmayan Azure AD Veritabanı düzeyi kullanıcılar `CREATE USER ... FROM EXTERNAL PROVIDER` söz dizimi kullanılarak oluşturulabilir. Bkz [. Kullanıcı oluşturma... Dış SAĞLAYıCıDAN](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).
- Azure AD Server sorumluları (oturum açmalar) yalnızca bir SQL yönetilen örneği içindeki SQL özelliklerini destekler. Aynı Azure AD kiracısı veya farklı kiracılar dahilinde olup olmadıkları fark etmeksizin, Azure AD kullanıcıları için desteklenmez. Bu özelliklere örnek olarak şunlar verilebilir:

  - SQL işlem çoğaltması.
  - Sunucu bağlayın.

- Veritabanı sahibi olarak Azure AD grubuna eşlenmiş bir Azure AD oturum açmanın ayarlanması desteklenmez.
- Diğer Azure AD sorumlularını kullanarak Azure AD sunucu düzeyi sorumluları 'nın kimliğe bürünme işlemi, [execute as](/sql/t-sql/statements/execute-as-transact-sql) yan tümcesi gibi desteklenir. FARKLı ÇALıŞTıR sınırlamaları şunlardır:

  - Ad, oturum açma adından farklı olduğunda Azure AD kullanıcıları için Kullanıcı tarafından desteklenmez. Örneğin, kullanıcının LOGIN [] adlı Kullanıcı oluşturma [myAadUser] sözdizimi aracılığıyla oluşturulduğu john@contoso.com ve kimliğe bürünme özelliği, exec as user = _myaaduser_ aracılığıyla denendiğinde bir örnektir. Bir Azure AD sunucu sorumlusu 'ndan (oturum açma) bir **Kullanıcı** oluşturduğunuzda, user_name **oturum açma** işleminden aynı login_name olarak belirtin.
  - Yalnızca rolün parçası olan SQL Server düzeyi sorumlular (oturumlar), `sysadmin` Azure AD sorumlularını hedefleyen aşağıdaki işlemleri yürütebilir:

    - KULLANıCı OLARAK YÜRÜT
    - OTURUM AÇMA OLARAK YÜRÜT

  - FARKLı ÇALıŞTıR ifadesiyle bir kullanıcının kimliğine bürünmek için kullanıcının doğrudan Azure AD sunucu sorumlusu (oturum açma) ile eşlenmesi gerekir. Azure AD Server sorumlularına eşlenen Azure AD gruplarının üyesi olan kullanıcılar, çağıran, belirtilen kullanıcı adında kimliğe bürünme izinlerine sahip olsa bile, EXECUTE AS ifadesiyle birlikte kullanılamaz.

- Bacpac dosyalarını kullanarak veritabanı dışarı aktarma/içeri aktarma işlemi, SQL yönetilen örneğindeki [SSMS v 18.4 veya üzeri](/sql/ssms/download-sql-server-management-studio-ssms)ya da [SQLPackage.exe](/sql/tools/sqlpackage-download)kullanılarak Azure AD kullanıcıları için desteklenir.
  - Aşağıdaki konfigürasyonlar veritabanı bacpac dosyası kullanılarak desteklenir: 
    - Aynı Azure AD etki alanı içindeki farklı yönetme örnekleri arasında bir veritabanını içeri/dışarı aktarın.
    - SQL yönetilen örneğinden bir veritabanını dışarı aktarın ve aynı Azure AD etki alanı içindeki SQL veritabanı 'na içeri aktarın. 
    - SQL veritabanından bir veritabanını dışarı aktarın ve aynı Azure AD etki alanı içinde SQL yönetilen örneği 'ne aktarın.
    - SQL yönetilen örneğinden bir veritabanını dışarı aktarın ve SQL Server (sürüm 2012 veya üzeri) içeri aktarın.
      - Bu yapılandırmada tüm Azure AD kullanıcıları, oturum açma bilgileri olmayan SQL Server veritabanı sorumluları (kullanıcılar) olarak oluşturulur. Kullanıcı türü olarak listelenir `SQL` ve `SQL_USER` sys.database_principals) olarak görünür. İzinleri ve rolleri SQL Server veritabanı meta verilerinde kalır ve kimliğe bürünme için kullanılabilir. Ancak, kimlik bilgilerini kullanarak SQL Server erişmek ve oturum açmak için kullanılamaz.

- Yalnızca SQL yönetilen örnek sağlama işlemi tarafından oluşturulan sunucu düzeyi asıl oturum açma, veya gibi sunucu rollerinin üyeleri veya `securityadmin` `sysadmin` sunucu düzeyinde herhangi bir oturum açma iznini Değiştir ile diğer oturumlar, SQL yönetilen örneği için ana VERITABANıNDA Azure ad sunucu sorumlularını (oturum açma) oluşturabilir.
- Oturum açma bir SQL sorumlusu ise, yalnızca rolün parçası olan oturum açma işlemleri `sysadmin` bir Azure AD hesabı için oturum açma bilgileri oluşturmak üzere Oluştur komutunu kullanabilir.
- Azure AD oturum açma, Azure SQL yönetilen örneği için kullanılan aynı dizin içinde bir Azure AD 'nin üyesi olmalıdır.
- Azure AD Server sorumluları (oturum açmalar), SQL Server Management Studio 18,0 Preview 5 ' te başlayan Nesne Gezgini görünür.
- Azure AD yönetici hesabıyla çakışan Azure AD Server sorumlularına (oturum açmalar) izin verilir. Sorumluyu çözümlediğinizde ve SQL yönetilen örneği için izinleri uyguladığınızda Azure AD Server sorumluları (oturum açmalar) Azure AD yöneticisi 'nden önceliklidir.
- Kimlik doğrulama sırasında, kimliği doğrulanan sorumluyu çözümlemek için aşağıdaki sıra uygulanır:

    1. Azure AD hesabı, Azure AD Server sorumlusu (oturum açma) ile doğrudan eşlenmiş olarak mevcutsa sys.server_principals, Azure AD sunucu sorumlusu (oturum açma) için erişim izni verin ve izinleri uygulayın.
    2. Azure AD hesabı, "X" türü olarak sys.server_principals mevcut olan Azure ad sunucusu sorumlusu (oturum açma) ile eşlenmiş bir Azure AD grubunun üyesiyse, erişim izni verin ve Azure AD grubu oturum açma izinlerini uygulayın.
    3. Azure AD hesabı, SQL yönetilen örnek sistemi görünümlerinde mevcut olmayan SQL yönetilen örneği için özel bir portal tarafından yapılandırılmış Azure AD yöneticisi ise, SQL yönetilen örneği (eski mod) için Azure AD yöneticisi 'nin özel sabit izinlerini uygulayın.
    4. Azure AD hesabı, bir veritabanında bulunan bir Azure AD kullanıcısına doğrudan eşlenmişse, "E" türü olarak sys.database_principals, Azure AD veritabanı kullanıcısına erişim izni verin ve izinleri uygulayın.
    5. Azure AD hesabı, bir veritabanında bulunan bir Azure AD kullanıcısına eşlenmiş bir Azure AD grubunun üyesiyse, "X" türü olarak sys.database_principals, Azure AD grubu oturum açma izinlerine erişim ve uygulama izinleri verin.
    6. Kimlik doğrulaması yapan kullanıcıya çözümlenen bir Azure AD kullanıcı hesabıyla veya bir Azure AD grup hesabıyla eşlenmiş bir Azure AD oturum açma bilgileri varsa, bu Azure AD oturum açma bilgilerini tüm izinler uygulanır.

### <a name="service-key-and-service-master-key"></a>Hizmet anahtarı ve hizmet ana anahtarı

- [Ana anahtar yedeklemesi](/sql/t-sql/statements/backup-master-key-transact-sql) DESTEKLENMIYOR (SQL veritabanı hizmeti tarafından yönetiliyor).
- [Ana anahtar geri yükleme](/sql/t-sql/statements/restore-master-key-transact-sql) desteklenmez (SQL veritabanı hizmeti tarafından yönetiliyor).
- [Hizmet ana anahtarı yedeklemesi](/sql/t-sql/statements/backup-service-master-key-transact-sql) DESTEKLENMIYOR (SQL veritabanı hizmeti tarafından yönetiliyor).
- [Hizmet ana anahtarı geri yükleme](/sql/t-sql/statements/restore-service-master-key-transact-sql) desteklenmez (SQL veritabanı hizmeti tarafından yönetiliyor).

## <a name="configuration"></a>Yapılandırma

### <a name="buffer-pool-extension"></a>Arabellek havuzu uzantısı

- [Arabellek havuzu uzantısı](/sql/database-engine/configure-windows/buffer-pool-extension) desteklenmiyor.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` desteklenmez. Bkz. [Alter Server CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Harmanlama

Varsayılan örnek harmanlaması, `SQL_Latin1_General_CP1_CI_AS` ve oluşturma parametresi olarak belirtilebilir. Bkz. [harmanlamalar](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Uyumluluk düzeyleri

- Desteklenen uyumluluk düzeyleri 100, 110, 120, 130, 140 ve 150.
- 100 altındaki uyumluluk düzeyleri desteklenmez.
- Yeni veritabanları için varsayılan uyumluluk düzeyi 140 ' dir. Geri yüklenen veritabanları için, uyumluluk düzeyi 100 ve üzeri olursa değişmeden kalır.

Bkz. [ALTER DATABASE Compatibility Level](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Veritabanı yansıtma

Veritabanı yansıtma desteklenmez.

- `ALTER DATABASE SET PARTNER` ve `SET WITNESS` seçenekleri desteklenmez.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` desteklenmez.

Daha fazla bilgi için bkz. [alter database set Partner, TANıK ayarla](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) ve [uç nokta oluştur... DATABASE_MIRRORING IÇIN](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Veritabanı seçenekleri

- Birden çok günlük dosyası desteklenmez.
- Bellek içi nesneler Genel Amaçlı hizmeti katmanında desteklenmez. 
- Her veritabanı için en fazla 280 dosya Genel Amaçlı örnek başına 280 dosya sınırı vardır. Genel Amaçlı katmanındaki veriler ve günlük dosyaları bu sınıra doğru sayılır. [İş açısından kritik katmanı veritabanı başına 32.767 dosyasını destekler](./resource-limits.md#service-tier-characteristics).
- Veritabanı FILESTREAM verileri içeren dosya gruplarını içeremez. . Bak veri içeriyorsa geri yükleme başarısız olur `FILESTREAM` . 
- Her dosya Azure Blob depolama alanına yerleştirilir. Dosya başına GÇ ve aktarım hızı her bir dosyanın boyutuna bağlıdır.

#### <a name="create-database-statement"></a>CREATE DATABASE ekstresi

Aşağıdaki sınırlamalar şunlar için geçerlidir `CREATE DATABASE` :

- Dosyalar ve dosya grupları tanımlanamıyor. 
- `CONTAINMENT`Seçenek desteklenmiyor. 
- `WITH` Seçenekler desteklenmez. 
   > [!TIP]
   > Geçici bir çözüm olarak, `ALTER DATABASE` `CREATE DATABASE` dosya eklemek veya kapsama ayarlamak için veritabanı seçeneklerini ayarlamak üzere sonra ' yi kullanın. 

- `FOR ATTACH`Seçenek desteklenmiyor.
- `AS SNAPSHOT OF`Seçenek desteklenmiyor.

Daha fazla bilgi için bkz. [veritabanı oluşturma](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>ALTER DATABASE ifadesi

Bazı dosya özellikleri ayarlanamaz veya değiştirilemez:

- T-SQL ifadesinde bir dosya yolu belirtilemez `ALTER DATABASE ADD FILE (FILENAME='path')` . `FILENAME`SQL yönetilen örneği dosyaları otomatik olarak yerleştirtiğinden, betikten kaldır. 
- Bir dosya adı, ifadesini kullanarak değiştirilemez `ALTER DATABASE` .

Aşağıdaki seçenekler varsayılan olarak ayarlanır ve değiştirilemez:

- `MULTI_USER`
- `ENABLE_BROKER`
- `AUTO_CLOSE OFF`

Aşağıdaki seçenekler değiştirilemez:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Bazı `ALTER DATABASE` deyimler (örneğin, [kapsama ayarla](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#converting-a-database-to-partially-contained-using-transact-sql)), örneğin otomatik veritabanı yedeklemesi sırasında veya bir veritabanı oluşturulduktan sonra doğru bir şekilde geçiş yapabilir. Bu durumda `ALTER DATABASE` deyimde yeniden denenmelidir. İlgili hata iletileri hakkında daha fazla bilgi için, [açıklamalar bölümüne](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&tabs=sqlpool&view=azuresqldb-mi-current#remarks-2)bakın.

Daha fazla bilgi için bkz. [alter database](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- SQL Server Agent etkinleştirme ve devre dışı bırakma Şu anda SQL yönetilen örneği 'nde desteklenmiyor. SQL Server Agent sürekli çalışır.
- Boş bir CPU 'ya dayalı iş zamanlaması tetiklemesi desteklenmiyor.
- SQL Server Agent ayarları salt okunurdur. Yordam, `sp_set_agent_properties` SQL yönetilen örneği 'nde desteklenmiyor. 
- İşler
  - T-SQL iş adımları desteklenir.
  - Aşağıdaki çoğaltma işleri desteklenir:
    - İşlem-günlük okuyucu
    - Anlık Görüntü
    - Dağıtım
  - SSIS iş adımları desteklenir.
  - Diğer iş adımı türleri şu anda desteklenmemektedir:
    - Birleştirme çoğaltması işi adımı desteklenmez. 
    - Sıra okuyucusu desteklenmez. 
    - Komut kabuğu henüz desteklenmiyor.
  - SQL yönetilen örneği, örneğin, Robocopy aracılığıyla ağ paylaşımları gibi dış kaynaklara erişemez. 
  - SQL Server Analysis Services desteklenmez.
- Bildirimler kısmen desteklenir.
- E-posta bildirimi desteklenir, ancak bir Veritabanı Postası profili yapılandırmanızı gerektirir. SQL Server Agent yalnızca bir Veritabanı Postası profili kullanabilir ve çağrılması gerekir `AzureManagedInstance_dbmail_profile` . 
  - Sayfalayıcı desteklenmiyor.
  - NetSend desteklenmez.
  - Uyarılar henüz desteklenmiyor.
  - Proxy 'ler desteklenmez.
- EventLog desteklenmiyor.
- SQL Aracısı işleri oluşturmak, değiştirmek veya yürütmek için kullanıcının doğrudan Azure AD sunucu sorumlusuna (oturum açma) eşlenmesi gerekir. Doğrudan eşlenmeyen kullanıcılar, örneğin, SQL Aracısı işleri oluşturma, değiştirme veya yürütme haklarına sahip olan bir Azure AD grubuna ait olan kullanıcılar bu işlemleri etkili bir şekilde gerçekleştiremez. Bunun nedeni yönetilen örnek kimliğe bürünme ve [yürütme olarak yürütme kısıtlamalarıdır](#logins-and-users).
- Ana/hedef (MSX/TSX) işleri için çoklu sunucu yönetim özelliği desteklenmez.

SQL Server Agent hakkında bilgi için bkz. [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tables

Aşağıdaki tablo türleri desteklenmez:

- [AKıŞı](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [Dış tablo](/sql/t-sql/statements/create-external-table-transact-sql) (PolyBase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (yalnızca genel amaçlı katmanında desteklenmez)

Tablo oluşturma ve değiştirme hakkında daha fazla bilgi için bkz. [Create Table](/sql/t-sql/statements/create-table-transact-sql) ve [alter table](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>İşlevler

### <a name="bulk-insert--openrowset"></a>Toplu ekleme/OPENROWSET

SQL yönetilen örneği dosya paylaşımlarına ve Windows klasörlerine erişemez, bu nedenle dosyalar Azure Blob depolamadan içeri aktarılmalıdır:

- `DATASOURCE` , `BULK INSERT` Azure Blob depolamadan dosyaları içeri aktarırken komutunda gereklidir. Bkz. [bulk INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` , `OPENROWSET` Azure Blob depolama alanındaki bir dosyanın içeriğini okurken işlevinde gereklidir. Bkz. [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` Azure SQL veritabanı, Azure SQL yönetilen örneği veya SQL Server örneklerinden veri okumak için kullanılabilir. Oracle veritabanları veya Excel dosyaları gibi diğer kaynaklar desteklenmez.

### <a name="clr"></a>CLR

SQL yönetilen örneği dosya paylaşımlarına ve Windows klasörlerine erişemez, bu nedenle aşağıdaki kısıtlamalar geçerlidir:

- Yalnızca `CREATE ASSEMBLY FROM BINARY` desteklenir. Bkz. [IKILIDEN derleme oluşturma](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` desteklenmez. Bkz. [dosyadan derleme oluşturma](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` dosyalara başvurulamıyor. Bkz. [alter assembly](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Veritabanı Postası (db_mail)
 - `sp_send_dbmail` parametre kullanılarak ekler gönderilemez @file_attachments . Yerel dosya sistemi ve dış paylaşımlar veya Azure Blob depolamaya bu yordamdan erişilemez.
 - `@query`Parametreli ve kimlik doğrulamasıyla ilgili bilinen sorunlara bakın.
 
### <a name="dbcc"></a>DBCC

SQL Server ' de etkinleştirilen DBCC deyimleri SQL yönetilen örneği 'nde desteklenmez.

- Yalnızca sınırlı sayıda genel Izleme bayrağı desteklenir. Oturum düzeyi `Trace flags` desteklenmiyor. Bkz. [izleme bayrakları](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) ve [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) , sınırlı sayıda Global Trace-Flags ile çalışır.
- Veritabanı modda ayarlanamadığı için REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST ve REPAIR_REBUILD seçenekleriyle [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) kullanılamıyor `SINGLE_USER` -bkz. [alter database farkları](#alter-database-statement). Olası veritabanı bozulması, Azure destek ekibi tarafından işlenir. Herhangi bir veritabanı bozulması göstergesi varsa Azure desteği 'ne başvurun.

### <a name="distributed-transactions"></a>Dağıtılmış işlemler

[Dağıtılmış işlemler](../database/elastic-transactions-overview.md) için kısmi destek şu anda genel önizlemededir. Dağıtılmış işlemler aşağıdaki koşullarda desteklenir (tümünün karşılanması gerekir):
* tüm işlem katılımcıları, [sunucu güven grubunun](./server-trust-group-overview.md)parçası olan Azure SQL yönetilen örnekleridir.
* işlemler .NET (TransactionScope sınıfı) veya Transact-SQL ' den başlatılır.

Azure SQL yönetilen örneği şu anda MSDTC şirket içi veya Azure sanal makinelerinde düzenli olarak desteklenen diğer senaryoları desteklemez.

### <a name="extended-events"></a>Genişletilmiş Olaylar

Genişletilmiş Olaylar (XEvents) için Windows'a özgü bazı hedefler desteklenmez:

- `etw_classic_sync`Hedef desteklenmiyor. `.xel`Azure Blob depolamada dosyaları depolayın. Bkz. [etw_classic_sync hedefi](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file`Hedef desteklenmiyor. `.xel`Azure Blob depolamada dosyaları depolayın. Bkz. [event_file hedefi](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Dış kitaplıklar

Veritabanı içi R ve Python dış kitaplıkları sınırlı genel önizlemede desteklenir. Bkz. [Azure SQL yönetilen örneği (Önizleme) Machine Learning Services](machine-learning-services-overview.md).

### <a name="filestream-and-filetable"></a>FILESTREAM ve FileTable

- FILESTREAM verileri desteklenmez.
- Veritabanı, verileri içeren dosya grupları içeremez `FILESTREAM` .
- `FILETABLE` desteklenmez.
- Tablolarda tür olamaz `FILESTREAM` .
- Aşağıdaki işlevler desteklenmez:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Daha fazla bilgi için bkz. [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) ve [FileTables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Tam metin anlam arama

[Anlamsal arama](/sql/relational-databases/search/semantic-search-sql-server) desteklenmiyor.

### <a name="linked-servers"></a>Bağlı sunucular

SQL yönetilen örneğindeki bağlantılı sunucular, sınırlı sayıda hedefi destekler:

- Desteklenen hedefler SQL yönetilen örneği, SQL veritabanı, Azure SYNAPSE SQL [sunucusuz](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) ve adanmış havuzlardır ve SQL Server örnekleridir. 
- Dağıtılmış yazılabilir işlemler yalnızca yönetilen örnekler arasında mümkündür. Daha fazla bilgi için bkz. [Dağıtılmış işlemler](../database/elastic-transactions-overview.md). Ancak MS DTC desteklenmez.
- Desteklenmeyen hedefler dosya, Analysis Services ve diğer RDBMS ' dir. Azure Blob Storage 'dan `BULK INSERT` veya `OPENROWSET` Dosya içeri aktarma için alternatif olarak veya [Azure SYNAPSE Analytics 'te SUNUCUSUZ bir SQL havuzu](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)kullanarak dosyaları yüklemeden yerel CSV içeri aktarmayı kullanmayı deneyin.

İşlemler: 

- [Çapraz örnek](../database/elastic-transactions-overview.md) yazma Işlemleri yalnızca yönetilen örnekler için desteklenir.
- `sp_dropserver` , bağlı bir sunucunun atılması için desteklenir. Bkz. [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET`İşlevi yalnızca SQL Server örneklerinde sorgu yürütmek için kullanılabilir. Bunlar yönetilen, şirket içi veya sanal makinelerde olabilir. Bkz. [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE`İşlevi yalnızca SQL Server örneklerinde sorgu yürütmek için kullanılabilir. Bunlar yönetilen, şirket içi veya sanal makinelerde olabilir. Yalnızca `SQLNCLI` , `SQLNCLI11` ve `SQLOLEDB` değerleri sağlayıcı olarak desteklenir. `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee` bunun bir örneğidir. Bkz. [opendatasource](/sql/t-sql/functions/opendatasource-transact-sql).
- Bağlı sunucular ağ paylaşımlarından dosyaları (Excel, CSV) okumak için kullanılamaz. Azure Blob depolama alanından CSV dosyalarını okuyan [bulk INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file), [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) veya [SYNAPSE ANALYTICS 'te sunucusuz bir SQL havuzuna başvuran bağlı bir sunucu](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)kullanmayı deneyin. [SQL yönetilen örnek geri bildirim öğesinde](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources) bu istekleri izle|

### <a name="polybase"></a>PolyBase

Mevcut dış kaynak türü, Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE havuzu için RDBMS 'dir (genel önizlemede). [SYNAPSE Analytics 'te sunucusuz SQL havuzuna başvuran bir dış tablo](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/) kullanarak doğrudan Azure Storage 'Dan okuyan PolyBase dış tabloları için geçici bir çözüm oluşturabilirsiniz. Azure SQL yönetilen örneği 'nde, bağlı sunucuları [SYNAPSE Analytics 'te sunucusuz BIR SQL havuzunda](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) veya Azure depolama verilerini okumak için SQL Server kullanabilirsiniz.
PolyBase hakkında daha fazla bilgi için bkz. [PolyBase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Çoğaltma

- Anlık görüntü ve Iki yönlü çoğaltma türleri desteklenir. Birleştirme çoğaltması, eşler arası çoğaltma ve güncelleştirilebilir abonelikler desteklenmez.
- [Işlemsel çoğaltma](replication-transactional-overview.md) , SQL yönetilen örneği üzerinde bazı kısıtlamalarla genel önizleme için kullanılabilir:
    - Tüm çoğaltma katılımcıları türleri (yayımcı, dağıtıcı, çekme abonesi ve anında Iletme abonesi) SQL yönetilen örneği üzerine yerleştirilebilir, ancak yayımcı ve dağıtıcı hem bulutta hem de şirket içinde olmalıdır.
    - SQL yönetilen örneği SQL Server son sürümleriyle iletişim kurabilir. Daha fazla bilgi için [Desteklenen sürümler matrisine](replication-transactional-overview.md#supportability-matrix) bakın.
    - İşlemsel çoğaltma bazı [ek ağ gereksinimlerine](replication-transactional-overview.md#requirements)sahiptir.

İşlem çoğaltmasını yapılandırma hakkında daha fazla bilgi için aşağıdaki öğreticilere bakın:
- [Bir SQL MI yayımcısı ile SQL MI abonesi arasında çoğaltma](replication-between-two-instances-configure-tutorial.md)
- [Bir SQL MI yayımcısı, SQL mı dağıtıcısı ve SQL Server abonesi arasında çoğaltma](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>RESTORE ekstresi 

- Desteklenen sözdizimi:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Desteklenmeyen sözdizimi:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Kaynak: 
  - `FROM URL` (Azure Blob depolama) desteklenen tek seçenektir.
  - `FROM DISK`/`TAPE`/Backup cihazı desteklenmiyor.
  - Yedekleme kümeleri desteklenmez.
- `WITH` Seçenekler desteklenmez. ,, `WITH` Vb. gibi geri yükleme girişimleri `DIFFERENTIAL` `STATS` `REPLACE` başarısız olur.
- `ASYNC RESTORE`: Geri yükleme, istemci bağlantısı kesilse bile devam eder. Bağlantınız atıldıysanız, `sys.dm_operation_status` geri yükleme işleminin durumunun görünümünü ve bır oluşturma ve bırakma veritabanı için görünümü kontrol edebilirsiniz. Bkz. [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Aşağıdaki veritabanı seçenekleri ayarlanır veya geçersiz kılınır ve daha sonra değiştirilemez: 

- `NEW_BROKER` Aracı. bak dosyasında etkinleştirilmemişse. 
- `ENABLE_BROKER` Aracı. bak dosyasında etkinleştirilmemişse. 
- `AUTO_CLOSE=OFF` . bak dosyasındaki bir veritabanı varsa `AUTO_CLOSE=ON` . 
- `RECOVERY FULL` . bak dosyasındaki bir veritabanının `SIMPLE` veya `BULK_LOGGED` Kurtarma modu varsa.
- Bellek için iyileştirilmiş bir dosya grubu eklenir ve kaynak. bak dosyasında yoksa XTP çağırılır. 
- Bellek için iyileştirilmiş mevcut dosya grubu XTP olarak yeniden adlandırılır. 
- `SINGLE_USER` ve `RESTRICTED_USER` seçenekleri öğesine dönüştürülür `MULTI_USER` .

Sınırlamalar: 

- Bozulan veritabanlarının yedeklemeleri, bozulmanın türüne bağlı olarak geri yüklenebilir, ancak bozulma düzeltilinceye kadar otomatik yedeklemeler alınmaz. `DBCC CHECKDB`Kaynak SQL yönetilen örneği üzerinde çalıştırdığınızdan ve `WITH CHECKSUM` Bu sorunu engellemek için yedeklemeyi kullandığınızdan emin olun.
- `.BAK`Bu belgede (örneğin, veya nesneler) açıklanan herhangi bir kısıtlamayı içeren bir veritabanının dosyasını geri yükleme `FILESTREAM` `FILETABLE` SQL yönetilen örneği üzerinde geri yüklenemez.
- `.BAK` birden çok yedekleme kümesi içeren dosyalar geri yüklenemez. 
- `.BAK` birden çok günlük dosyası içeren dosyalar geri yüklenemez.
- 8 TB 'den büyük veritabanları, etkin bellek içi OLTP nesneleri veya örnek başına 280 dosya aşılacak dosya sayısı bir Genel Amaçlı örneğine geri yüklenemez. 
- 4 TB 'den büyük veya bellek içi OLTP nesnelerinden daha büyük olan veritabanları içeren yedeklemeler, İş Açısından Kritik örneğine [geri yüklenemez.](resource-limits.md)
Restore deyimleri hakkında daha fazla bilgi için bkz. [restore deyimleri](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Aynı sınırlamalar, yerleşik bir noktadan sonra geri yükleme işlemi için geçerlidir. Örnek olarak, 4 TB 'den büyük Genel Amaçlı veritabanı İş Açısından Kritik örneğine geri yüklenemez. Bellek içi OLTP dosyaları veya 280 'den fazla dosya içeren İş Açısından Kritik veritabanı Genel Amaçlı örneğine geri yüklenemez.

### <a name="service-broker"></a>Hizmet Aracısı

Çapraz örnek hizmet Aracısı ileti değişimi yalnızca Azure SQL yönetilen örnekleri arasında desteklenir:

- `CREATE ROUTE`: `CREATE ROUTE` `ADDRESS` `LOCAL` Başka bir SQL YÖNETILEN örneğinin veya DNS adı ile kullanamazsınız.
- `ALTER ROUTE`: `ALTER ROUTE` `ADDRESS` `LOCAL` Başka bir SQL YÖNETILEN örneğinin veya DNS adı ile kullanamazsınız.

Aktarım güvenliği desteklenir, iletişim kutusu güvenliği şu değildir:
- `CREATE REMOTE SERVICE BINDING`desteklenmez.

Hizmet Aracısı varsayılan olarak etkindir ve devre dışı bırakılamaz. Aşağıdaki ALTER DATABI seçenekleri desteklenmez:
- `ENABLE_BROKER`
- `DISABLE_BROKER`

### <a name="stored-procedures-functions-and-triggers"></a>Saklı yordamlar, işlevler ve Tetikleyiciler

- `NATIVE_COMPILATION` Genel Amaçlı katmanında desteklenmez.
- Aşağıdaki [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) seçenekleri desteklenmez: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
  - `scan for startup procs`
- `sp_execute_external_scripts` desteklenmez. Bkz. [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` desteklenmez. Bkz. [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` desteklenmez ve bu `sp_addextendedproc` ve içerir `sp_dropextendedproc` . Bu işlev, SQL Server için kullanımdan kaldırma yolunda olduğundan desteklenmez. Daha fazla ayrıntı için bkz. [genişletilmiş saklı yordamlar](/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming).
- `sp_attach_db`, `sp_attach_single_file_db` ve `sp_detach_db` desteklenmez. Bkz. [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)ve [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Sistem işlevleri ve değişkenler

Aşağıdaki değişkenler, işlevler ve görünümler farklı sonuçlar döndürüyor:

- `SERVERPROPERTY('EngineEdition')` 8 değerini döndürür. Bu özellik SQL yönetilen örneğini benzersiz bir şekilde tanımlar. Bkz. [ServerProperty](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` SQL yönetilen örneği için SQL Server olduğu gibi örnek kavramı, NULL değerini döndürür. Bkz. [ServerProperty (' ÖrnekAdı ')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` tam DNS "bağlanılabilir" adı döndürür, örneğin, my-managed-instance.wcus17662feb9ce98.database.windows.net. Bkz [. @SERVERNAME @](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS``myinstance.domain.database.windows.net`"ad" ve "data_source" özellikleri gibi bir tam DNS "bağlanılabilir" adı döndürür. Bkz [. sys. Sunucu](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` SQL Server için mevcut hizmet kavramı SQL yönetilen örneği için uygulanamadığından NULL değerini döndürür. Bkz [. @SERVICENAME @](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` desteklenir. Azure AD oturum açma sys.sysoturum açmalarında NULL değeri döndürür. Bkz. [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` desteklenmez. Yanlış veriler döndürülür, bu geçici olarak bilinen bir sorundur. Bkz. [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Ortam kısıtlamaları

### <a name="subnet"></a>Alt ağ
-  SQL yönetilen örneğinizi dağıttığınız alt ağa başka herhangi bir kaynak (örneğin, sanal makineler) yerleştirebilirsiniz. Bu kaynakları farklı bir alt ağ kullanarak dağıtın.
- Alt ağda yeterli sayıda kullanılabilir [IP adresi](connectivity-architecture-overview.md#network-requirements)olmalıdır. Alt ağda en az 32 IP adresi olmalıdır.
- Bir bölgede dağıtabileceğiniz sanal çekirdek sayısı ve örnek türleri bazı [kısıtlamalar ve sınırlara](resource-limits.md#regional-resource-limitations)sahiptir.
- Alt ağa uygulanması gereken bir [ağ yapılandırması](connectivity-architecture-overview.md#network-requirements) vardır.

### <a name="vnet"></a>Sanal ağ
- VNet, kaynak modeli kullanılarak dağıtılabilir-sanal ağ için klasik model desteklenmez.
- SQL yönetilen örneği oluşturulduktan sonra, SQL yönetilen örneği veya VNet 'in başka bir kaynak grubuna veya aboneliğe taşınması desteklenmez.
- 9/22/2020 [genel](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) eşlemesinin önüne göre oluşturulan sanal kümeler ÜZERINDE barındırılan SQL yönetilen örnekleri için desteklenmez. Sanal ağ geçitleri aracılığıyla ExpressRoute veya VNet-VNet aracılığıyla bu kaynaklara bağlanabilirsiniz.

### <a name="failover-groups"></a>Yük devretme grupları
Sistem veritabanları, bir yük devretme grubundaki ikincil örneğe çoğaltılmaz. Bu nedenle, nesneler ikincil üzerinde el ile oluşturulmadığı takdirde, sistem veritabanlarından nesnelere bağlı senaryolar ikincil örnekte imkansız olur.

### <a name="tempdb"></a>'Nın
- Genel Amaçlı katmanındaki en büyük dosya boyutu, `tempdb` çekirdek başına 24 GB 'den büyük olamaz. `tempdb`İş açısından kritik katmanındaki en büyük boyut, SQL yönetilen örnek depolama boyutuyla sınırlıdır. `Tempdb` günlük dosyası boyutu Genel Amaçlı katmanında 120 GB ile sınırlıdır. Bazı sorgular, üzerinde çekirdek başına 24 GB 'den fazla gereksinim duyduklarında `tempdb` veya 120 GB 'den fazla günlük verisi ürettiklerinde bir hata döndürebilir.
- `Tempdb` her zaman 12 veri dosyasına bölünür: Ana, veri dosyası ve 11 birincil olmayan veri dosyası olarak da bilinen 1 birincil dosya. Dosya yapısı değiştirilemez ve yeni dosyalar içine eklenemez `tempdb` . 
- Yeni bir SQL Server 2019 bellek veritabanı özelliği olan [bellek için iyileştirilmiş `tempdb` meta veriler](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15&preserve-view=true#memory-optimized-tempdb-metadata)desteklenmez.
- Model veritabanında oluşturulan nesneler, `tempdb` bir yeniden başlatma işleminden sonra veya bir yük devretmeden sonra otomatik olarak oluşturulamaz çünkü bu `tempdb` , model veritabanından ilk nesne listesini almaz. `tempdb`Her yeniden başlatma veya yük devretme sonrasında nesneleri el ile oluşturmanız gerekir.

### <a name="msdb"></a>MSDB

SQL yönetilen örneğindeki aşağıdaki MSDB şemaları, kendi önceden tanımlanmış rollerine ait olmalıdır:

- Genel roller
  - TargetServersRole
- [Düzeltilen veritabanı rolleri](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15&preserve-view=true)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail rolleri](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15&preserve-view=true#DBProfile):
  - DatabaseMailUserRole
- [Tümleştirme Hizmetleri rolleri](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15&preserve-view=true):
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Önceden tanımlanmış rol adlarını, şema adlarını ve şema sahiplerini müşterilere göre değiştirmek, hizmetin normal işlemlerini etkiler. Bunlar üzerinde yapılan tüm değişiklikler, algılanan en kısa sürede önceden tanımlanmış değerlere geri döndürülecek veya normal hizmet işlemini sağlamak için en son bir sonraki hizmet güncelleştirmesinde olacaktır.

### <a name="error-logs"></a>Hata günlükleri

SQL yönetilen örneği, ayrıntılı bilgileri hata günlüklerine koyar. Hata günlüğünde günlüğe kaydedilen çok sayıda iç sistem olayı vardır. İlgisiz bazı girdilerin filtrelediğini belirten hata günlüklerini okumak için özel bir yordam kullanın. Daha fazla bilgi için, Azure Data Studio için [SQL yönetilen örneği – sp_readmierrorlog](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) veya [SQL yönetilen örnek uzantısı (Önizleme)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) konusuna bakın.

## <a name="next-steps"></a>Sonraki adımlar

- SQL yönetilen örneği hakkında daha fazla bilgi için bkz. [SQL yönetilen örneği nedir?](sql-managed-instance-paas-overview.md)
- Özellikler ve karşılaştırma listesi için bkz. [Azure SQL yönetilen örnek özelliği karşılaştırması](../database/features-comparison.md).
- Yayın güncelleştirmeleri ve bilinen sorunlar durumu için bkz. [SQL yönetilen örnek sürüm notları](../database/doc-changes-updates-release-notes.md)
- Yeni bir SQL yönetilen örneği oluşturmayı gösteren bir hızlı başlangıç için bkz. [SQL yönetilen örneği oluşturma](instance-create-quickstart.md).
