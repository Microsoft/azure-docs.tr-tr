---
title: Azure Active Directory kimlik doğrulaması
description: Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics 'te SYNAPSE SQL ile kimlik doğrulaması için Azure Active Directory kullanma hakkında bilgi edinin
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 04/23/2020
ms.openlocfilehash: a636c0e2a41b636f30ada14d4f16a022f2890b71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454303"
---
# <a name="use-azure-active-directory-authentication"></a>Azure Active Directory kimlik doğrulaması kullan

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Active Directory (Azure AD) kimlik doğrulaması, Azure AD 'deki kimlikleri kullanarak Azure [SQL veritabanı](sql-database-paas-overview.md), [Azure SQL yönetilen ÖRNEĞI](../managed-instance/sql-managed-instance-paas-overview.md)ve [SYNAPSE SQL](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 'e bağlanmak için kullanılan bir mekanizmadır.

> [!NOTE]
> Bu makale Azure SQL veritabanı, SQL yönetilen örneği ve Azure SYNAPSE Analytics için geçerlidir.

Azure AD kimlik doğrulaması ile veritabanı kullanıcılarının ve diğer Microsoft hizmetlerinin kimliklerini tek merkezden yönetebilirsiniz. Merkezi kimlik yönetimi, tüm veritabanı kullanıcılarını aynı yerden yönetmenizi sağlar ve izin yönetimini kolaylaştırır. Avantajları şunlardır:

- SQL Server kimlik doğrulaması için bir alternatif sağlar.
- Sunucu genelindeki Kullanıcı kimliklerinin uzamasını durdurmaya yardımcı olur.
- Tek bir yerde parola döndürmeye izin verir.
- Müşteriler, dış (Azure AD) gruplarını kullanarak veritabanı izinlerini yönetebilir.
- Tümleşik Windows kimlik doğrulamasını ve Azure Active Directory tarafından desteklenen diğer kimlik doğrulama biçimlerini etkinleştirerek parolaların depolanmasını ortadan kaldırabilir.
- Azure AD kimlik doğrulaması, veritabanı düzeyinde kimliklerin kimliğini doğrulamak için kapsanan veritabanı kullanıcılarını kullanır.
- Azure AD, SQL veritabanı ve SQL yönetilen örneği 'ne bağlanan uygulamalar için belirteç tabanlı kimlik doğrulamasını destekler.
- Azure AD kimlik doğrulaması şunları destekler:
  - Yalnızca Azure AD bulutu kimlikleri.
  - Azure AD karma kimlikleri şunları destekler:
    - Sorunsuz çoklu oturum açma (SSO) **geçişli** kimlik doğrulama ve **Parola karması** kimlik doğrulamasıyla iki seçenekten oluşan, bulut kimlik doğrulaması.
    - Federal kimlik doğrulaması.
  - Azure AD kimlik doğrulama yöntemleri ve aralarından seçim yapabileceğiniz daha fazla bilgi için aşağıdaki makaleye bakın:
    - [Azure Active Directory karma kimlik çözümünüz için doğru kimlik doğrulama yöntemini seçin](../../active-directory/hybrid/choose-ad-authn.md)

- Azure AD, Multi-Factor Authentication içeren Active Directory evrensel kimlik doğrulaması kullanan SQL Server Management Studio bağlantıları destekler. Multi-Factor Authentication, kolay bir doğrulama seçenekleriyle güçlü kimlik doğrulaması içerir: telefon araması, SMS mesajı, PIN ile akıllı kartlar veya mobil uygulama bildirimi. Daha fazla bilgi için bkz. Azure [SQL veritabanı, SQL yönetilen örneği ve Azure SYNAPSE Azure AD Multi-Factor Authentication Için SSMS desteği](authentication-mfa-ssms-overview.md)

- Azure AD, Active Directory Etkileşimli Kimlik Doğrulaması'nın kullanıldığı SQL Server Veri Araçları'ndan (SSDT) gelen benzer bağlantıları destekler. Daha fazla bilgi için bkz. [SQL Server Veri Araçları Azure Active Directory desteği (SSDT)](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> Azure sanal makinesi (VM) üzerinde çalıştırılan bir SQL Server örneğine bağlanma işlemi, Azure Active Directory hesabı kullanıldığında desteklenmez. Bunun yerine etki alanı Active Directory hesabı kullanın.  

Yapılandırma adımları Azure Active Directory kimlik doğrulaması yapılandırmak ve kullanmak için aşağıdaki yordamları içerir.

1. Azure AD 'yi oluşturun ve doldurun.
2. İsteğe bağlı: Azure aboneliğinizle ilişkili olan Active Directory 'yi Ilişkilendirin veya değiştirin.
3. Azure Active Directory Yöneticisi oluşturun.
4. İstemci bilgisayarlarınızı yapılandırın.
5. Veritabanınızda Azure AD kimlikleriyle eşlenmiş kapsanan veritabanı kullanıcıları oluşturun.
6. Azure AD kimliklerini kullanarak veritabanınıza bağlanın.

> [!NOTE]
> Azure AD 'yi oluşturma ve doldurma hakkında bilgi edinmek ve Azure SYNAPSE Analytics 'te Azure SQL veritabanı, SQL yönetilen örneği ve SYNAPSE SQL ile Azure AD 'yi yapılandırmak için bkz. Azure [ad 'Yi Azure SQL veritabanı Ile yapılandırma](authentication-aad-configure.md).

## <a name="trust-architecture"></a>Güven mimarisi

- Azure AD yerel kullanıcı parolalarını desteklemek için yalnızca Azure AD, SQL veritabanı, SQL yönetilen örneği ve Azure SYNAPSE bulut bölümü kabul edilir.
- Windows çoklu oturum açma kimlik bilgilerini (veya Windows kimlik bilgileri için Kullanıcı/parola) desteklemek için, geçişli ve parola karması kimlik doğrulaması için sorunsuz çoklu oturum açma için yapılandırılmış bir Federasyon veya yönetilen etki alanından Azure Active Directory kimlik bilgilerini kullanın. Daha fazla bilgi için bkz. [kesintisiz çoklu oturum açma Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md).
- Federal kimlik doğrulamasını (veya Windows kimlik bilgileri için Kullanıcı/parola) desteklemek için, ADFS bloğundan iletişim gerekir.

Azure AD karma kimlikleri, kurulum ve eşitleme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- Parola karması kimlik doğrulaması- [Azure AD Connect eşitleme ile parola karması eşitlemesi uygulama](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Geçişli kimlik doğrulaması- [Azure Active Directory geçişli kimlik doğrulaması](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federal kimlik doğrulaması- [Azure 'da Active Directory Federasyon Hizmetleri (AD FS) dağıtma](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) ve [Azure AD Connect ve Federasyon](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

ADFS altyapısıyla (veya Windows kimlik bilgileri için Kullanıcı/parola) örnek bir Federasyon kimlik doğrulaması için aşağıdaki diyagrama bakın. Oklar, iletişim yollarını gösterir.

![aad kimlik doğrulama diyagramı][1]

Aşağıdaki diyagramda, bir istemcinin bir belirteci göndererek bir veritabanına bağlanmasına izin veren Federasyon, güven ve barındırma ilişkileri belirtilmektedir. Belirtecin kimliği bir Azure AD tarafından doğrulanır ve veritabanı tarafından güvenilir. Müşteri 1, yerel kullanıcılar veya bir Azure AD ile federe kullanıcılarla bir Azure Active Directory temsil edebilir. Müşteri 2, içeri aktarılan kullanıcılar dahil olası bir çözümü temsil eder. Bu örnekte, ADFS ile Azure Active Directory eşitlenen bir federal Azure Active Directory geliyor. Azure AD kimlik doğrulaması kullanarak bir veritabanına erişimin, barındırma aboneliğinin Azure AD ile ilişkilendirilmesini gerektirdiğini anlamak önemlidir. Azure SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE kaynaklarını oluşturmak için aynı aboneliğin kullanılması gerekir.

![Abonelik ilişkisi][2]

## <a name="administrator-structure"></a>Yönetici yapısı

Azure AD kimlik doğrulaması kullanılırken, iki yönetici hesabı vardır: özgün Azure SQL veritabanı yöneticisi ve Azure AD yöneticisi. Aynı kavramlar Azure SYNAPSE için de geçerlidir. Yalnızca bir Azure AD hesabını temel alan yönetici, bir kullanıcı veritabanında ilk Azure AD kapsanan veritabanı kullanıcısını oluşturabilir. Azure AD yönetici oturumu, bir Azure AD kullanıcısı veya bir Azure AD grubu olabilir. Yönetici bir grup hesabı olduğunda, sunucu için birden çok Azure AD yöneticisini etkinleştirerek herhangi bir grup üyesi tarafından kullanılabilir. Grup hesabını yönetici olarak kullanmak, SQL veritabanı veya Azure SYNAPSE 'de kullanıcıları veya izinleri değiştirmeden Azure AD 'de grup üyelerini merkezi olarak eklemenize ve kaldırmanıza olanak tanıyarak yönetilebilirlik geliştirir. Herhangi bir anda yalnızca bir Azure AD Yöneticisi (bir kullanıcı veya grup) yapılandırılabilir.

![Yönetici yapısı][3]

## <a name="permissions"></a>İzinler

Yeni kullanıcılar oluşturmak için, veritabanında izninizin olması gerekir `ALTER ANY USER` . `ALTER ANY USER`Herhangi bir veritabanı kullanıcısına izin verilebilir. `ALTER ANY USER`Ayrıca, bu izin Sunucu Yöneticisi hesapları tarafından ve `CONTROL ON DATABASE` Bu veritabanı için veya iznine sahip veritabanı kullanıcıları `ALTER ON DATABASE` ve veritabanı rolünün üyeleri tarafından tutulur `db_owner` .

Azure SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE içinde kapsanan bir veritabanı kullanıcısı oluşturmak için, bir Azure AD kimliği kullanarak veritabanına veya örneğe bağlanmanız gerekir. İlk kapsanan veritabanı kullanıcısını oluşturmak için, bir Azure AD Yöneticisi (veritabanının sahibi olan) kullanarak veritabanına bağlanmanız gerekir. Bu, [Azure Active Directory kimlik DOĞRULAMASıNı SQL veritabanı veya Azure SYNAPSE Ile yapılandırma ve yönetme](authentication-aad-configure.md)bölümünde gösterilmiştir. Azure AD kimlik doğrulaması yalnızca Azure SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE için Azure AD yöneticisi oluşturulduysa mümkündür. Azure Active Directory Yöneticisi sunucudan kaldırılmışsa, daha önce SQL Server içinde oluşturulan mevcut Azure Active Directory Kullanıcılar artık Azure Active Directory kimlik bilgilerini kullanarak veritabanına bağlanamaz.

## <a name="azure-ad-features-and-limitations"></a>Azure AD özellikleri ve sınırlamaları

- Azure AD 'nin aşağıdaki üyeleri Azure SQL veritabanı için sağlanabilir:

  - Yerel Üyeler: yönetilen etki alanında veya bir müşteri etki alanında Azure AD 'de oluşturulan bir üye. Daha fazla bilgi için bkz. [Azure AD 'ye kendi etki alanı adınızı ekleme](../../active-directory/fundamentals/add-custom-domain.md).
  - Yönetilen bir etki alanında, geçişli veya parola karması kimlik doğrulamasıyla sorunsuz çoklu oturum açma için yapılandırılmış Azure Active Directory ile federe Active Directory etki alanı üyeleri. Daha fazla bilgi için bkz. [Microsoft Azure artık Windows Server Active Directory ile Federasyonu destekliyor](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) ve [sorunsuz çoklu oturum açma Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md).
  - Diğer Azure AD 'den, yerel veya Federasyon etki alanı üyesi olan Üyeler içeri aktarıldı.
  - Güvenlik grupları olarak oluşturulan grupları Active Directory.

- Sunucu rolüne sahip bir grubun parçası olan Azure AD kullanıcıları `db_owner` , Azure SQL veritabanı ve Azure SYNAPSE 'de **[VERITABANı KAPSAMLı kimlik bilgisi oluştur](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** sözdizimini kullanamaz. Aşağıdaki hatayı görürsünüz:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    `db_owner` **VERITABANı KAPSAMLı kimlik bilgileri oluşturma** sorununu hafifletmek için role doğrudan tek bir Azure AD kullanıcısına izin verin.

- Bu sistem işlevleri, Azure AD sorumluları altında yürütüldüğünde NULL değerler döndürüyor:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>SQL Yönetilen Örnek

- Azure AD Server sorumluları (oturumlar) ve kullanıcıları [SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md)için desteklenir.
- Bir Azure AD grubuna eşlenen Azure AD Server sorumlularını (oturum açma) [SQL yönetilen örneğinde](../managed-instance/sql-managed-instance-paas-overview.md)ayarlama desteklenmez.
  - Bunun bir uzantısı, bir grup sunucu rolünün bir parçası olarak eklendiğinde `dbcreator` , bu gruptaki KULLANıCıLAR SQL yönetilen örneğine bağlanabilir ve yeni veritabanları oluşturabilir, ancak veritabanına erişemeyecektir. Bunun nedeni, yeni veritabanı sahibinin Azure AD kullanıcısı değil, SA. Bu sorun, bireysel kullanıcı sunucu rolüne eklenirse bildirim yapmaz `dbcreator` .
- SQL Aracısı yönetimi ve işlerin yürütülmesi, Azure AD Server sorumluları (oturumlar) için desteklenir.
- Veritabanı yedekleme ve geri yükleme işlemleri, Azure AD Server sorumluları (oturumlar) tarafından yürütülebilir.
- Azure AD Server sorumluları (oturum açmalar) ve kimlik doğrulama olayları ile ilgili tüm deyimlerin denetlenmesi desteklenir.
- Sysadmin sunucu rolünün üyeleri olan Azure AD Server sorumluları (oturumlar) için adanmış yönetici bağlantısı desteklenir.
  - SQLCMD yardımcı programı ve SQL Server Management Studio aracılığıyla desteklenir.
- Oturum açma Tetikleyicileri, Azure AD Server sorumlularına (oturum açmalar) gelen oturum açma olayları için desteklenir.
- Hizmet Aracısı ve DB Mail, bir Azure AD sunucusu sorumlusu (oturum açma) kullanılarak ayarlanabilir.

## <a name="connect-by-using-azure-ad-identities"></a>Azure AD kimliklerini kullanarak bağlanma

Azure Active Directory kimlik doğrulaması, Azure AD kimliklerini kullanarak bir veritabanına bağlanmak için aşağıdaki yöntemleri destekler:

- Azure Active Directory parolası
- Azure Active Directory tümleşik
- Multi-Factor Authentication ile evrensel Azure Active Directory
- Uygulama belirteci kimlik doğrulamasını kullanma

Aşağıdaki kimlik doğrulama yöntemleri Azure AD Server sorumluları (oturum açmalar) için desteklenir:

- Azure Active Directory parolası
- Azure Active Directory tümleşik
- Multi-Factor Authentication ile evrensel Azure Active Directory

### <a name="additional-considerations"></a>Diğer konular

- Yönetilebilirlik geliştirmek için, bir yönetici olarak adanmış bir Azure AD grubu sağlamanızı öneririz.
- Yalnızca bir Azure AD Yöneticisi (bir kullanıcı veya grup), SQL veritabanı veya Azure SYNAPSE 'de dilediğiniz zaman bir sunucu için yapılandırılabilir.
  - SQL yönetilen örneği için Azure AD Server sorumlularını (oturum açma) ekleme, role eklenebilen birden çok Azure AD Server sorumlusu (oturum açma) oluşturma olasılığa izin verir `sysadmin` .
- Yalnızca sunucu için bir Azure AD yöneticisi, Azure Active Directory bir hesabı kullanarak sunucuya veya yönetilen örneğe ilk olarak bağlanabilir. Active Directory Yöneticisi, sonraki Azure AD veritabanı kullanıcılarını yapılandırabilir.
- Bağlantı zaman aşımını 30 saniyeye ayarlamayı öneririz.
- SQL Server 2016 Management Studio ve SQL Server Veri Araçları Visual Studio 2015 için (sürüm 14.0.60311.1 Nisan 2016 veya üzeri) Azure Active Directory kimlik doğrulamasını destekler. (Azure AD kimlik doğrulaması, **SqlServer için .NET Framework veri sağlayıcısı** tarafından desteklenir; en az sürüm .NET Framework 4,6). Bu nedenle, bu araçların ve veri katmanı uygulamalarının (DAC ve BACPAC) en yeni sürümleri Azure AD kimlik doğrulaması kullanabilir.
- Sürüm 15.0.1 ile başlayarak, [sqlcmd yardımcı](/sql/tools/sqlcmd-utility) programı ve [bcp yardımcı programı](/sql/tools/bcp-utility) , Multi-Factor Authentication ile etkileşimli kimlik doğrulama desteği Active Directory.
- Visual Studio 2015 için SQL Server Veri Araçları, en az veri araçları 'nın 2016 Nisan sürümü (sürüm 14.0.60311.1) gerektirir. Şu anda, Azure AD kullanıcıları SSDT Nesne Gezgini gösterilmez. Geçici bir çözüm olarak, [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql)kullanıcıları görüntüleyin.
- [SQL Server Için MICROSOFT JDBC sürücüsü 6,0](https://www.microsoft.com/download/details.aspx?id=11774) , Azure AD kimlik doğrulamasını destekler. Ayrıca bkz. [bağlantı özelliklerini ayarlama](/sql/connect/jdbc/setting-the-connection-properties).
- PolyBase, Azure AD kimlik doğrulamasını kullanarak kimlik doğrulaması yapamaz.
- Azure AD kimlik doğrulaması, Azure SQL veritabanı ve Azure SYNAPSE için Azure portal **Içeri aktarma veritabanı** ve **dışarı aktarma veritabanı** dikey pencereleri kullanılarak desteklenir. Azure AD kimlik doğrulaması kullanarak içeri ve dışarı aktarma, bir PowerShell komutundan de desteklenir.
- CLı kullanılarak SQL veritabanı, SQL yönetilen örneği ve Azure SYNAPSE için Azure AD kimlik doğrulaması desteklenir. Daha fazla bilgi için bkz. [Azure AD kimlik DOĞRULAMASıNı SQL veritabanı veya Azure SYNAPSE Ile yapılandırma ve yönetme](authentication-aad-configure.md) [SQL Server-az SQL Server](/cli/azure/sql/server).

## <a name="next-steps"></a>Sonraki adımlar

- Bir Azure AD örneği oluşturup doldurmayı ve sonra Azure SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE ile yapılandırmayı öğrenmek için bkz. [SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](authentication-aad-configure.md).
- SQL yönetilen örneği ile Azure AD Server sorumlularını (oturum açma) kullanmanın bir öğreticisi için bkz. [SQL yönetilen örneği Ile Azure AD Server sorumluları (oturumlar)](../managed-instance/aad-security-configure-tutorial.md)
- SQL veritabanında oturum açma bilgileri, kullanıcılar, veritabanı rolleri ve izinlere genel bakış için bkz. [oturum açma bilgileri, kullanıcılar, veritabanı rolleri ve izinler](logins-create-manage.md).
- Veritabanı sorumluları hakkında daha fazla bilgi için bkz. [Sorumlular](/sql/relational-databases/security/authentication-access/principals-database-engine).
- Veritabanı rolleri hakkında daha fazla bilgi için bkz. [Veritabanı rolleri](/sql/relational-databases/security/authentication-access/database-level-roles).
- SQL yönetilen örneği için Azure AD Server sorumlularını (oturum açma) oluşturma hakkında sözdizimi için bkz.  [oturum oluşturma](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).
- SQL Veritabanındaki güvenlik duvarı kuralları hakkında daha fazla bilgi için bkz. [SQL Veritabanı güvenlik duvarı kuralları](firewall-configure.md).

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
