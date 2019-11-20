---
title: Azure Active Directory
description: SQL veritabanı, yönetilen örnek ve SQL veri ambarı ile kimlik doğrulaması için Azure Active Directory kullanma hakkında bilgi edinin
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 02/20/2019
ms.openlocfilehash: e0eeb48490c869c4a3b46bfd71fca72e0ab1c2ff
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816529"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>SQL ile kimlik doğrulaması için Azure Active Directory kimlik doğrulaması kullanma

Azure Active Directory kimlik doğrulaması, Azure [SQL veritabanı](sql-database-technical-overview.md), [yönetilen örnek](sql-database-managed-instance.md)ve [SQL veri ambarı](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 'na Azure Active Directory kimlik (Azure AD) kullanarak bağlanma mekanizmasıdır. 

> [!NOTE]
> Bu konu başlığı, Azure SQL sunucusunun yanı sıra Azure SQL sunucusu üzerinde oluşturulmuş olan SQL Veritabanı ve SQL Veri Ambarı veritabanları için de geçerlidir. Kolaylık açısından, hem SQL Veritabanı hem de SQL Veri Ambarı için SQL Veritabanı terimi kullanılmaktadır.

Azure AD kimlik doğrulamasıyla, veritabanı kullanıcılarının ve diğer Microsoft hizmetlerinin kimliklerini tek bir konumda merkezi olarak yönetebilirsiniz. Merkezi KIMLIK yönetimi, veritabanı kullanıcılarını yönetmek ve izin yönetimini basitleştireceğinizi sağlayan tek bir yer sağlar. Avantajları şunlardır:

- SQL Server kimlik doğrulaması için bir alternatif sağlar.
- Veritabanı sunucuları arasında kullanıcı kimliklerinin uzamasını durdurmaya yardımcı olur.
- Tek bir yerde parola döndürmeye izin verir.
- Müşteriler, dış (Azure AD) gruplarını kullanarak veritabanı izinlerini yönetebilir.
- Tümleşik Windows kimlik doğrulamasını ve Azure Active Directory tarafından desteklenen diğer kimlik doğrulama biçimlerini etkinleştirerek parolaların depolanmasını ortadan kaldırabilir.
- Azure AD kimlik doğrulaması, veritabanı düzeyinde kimliklerin kimliğini doğrulamak için kapsanan veritabanı kullanıcılarını kullanır.
- Azure AD, SQL veritabanı 'na bağlanan uygulamalar için belirteç tabanlı kimlik doğrulamasını destekler.
- Azure AD kimlik doğrulaması, etki alanı eşitlemesi olmadan yerel bir Azure Active Directory için ADFS (etki alanı Federasyonu) veya yerel kullanıcı/parola kimlik doğrulamasını destekler.
- Azure AD, Multi-Factor Authentication (MFA) içeren Active Directory Evrensel Kimlik Doğrulaması'nın kullanıldığı SQL Server Management Studio'dan gelen bağlantıları destekler.  MFA telefon görüşmesi, SMS mesajı, pin ile akıllı kart veya mobil uygulama bildirimi gibi çok sayıda kolay doğrulama seçeneğine sahiptir. Daha fazla bilgi için bkz. [SQL Veritabanı ve SQL Veri Ambarı ile Azure AD MFA için SSMS desteği](sql-database-ssms-mfa-authentication.md).
- Azure AD, Active Directory Etkileşimli Kimlik Doğrulaması'nın kullanıldığı SQL Server Veri Araçları'ndan (SSDT) gelen benzer bağlantıları destekler. Daha fazla bilgi için bkz. [SQL Server Veri Araçları'na (SSDT) Azure Active Directory desteği](/sql/ssdt/azure-active-directory).

> [!NOTE]  
> Bir Azure VM üzerinde çalışan SQL Server bağlanmak Azure Active Directory hesabı kullanılarak desteklenmez. Bunun yerine bir etki alanı Active Directory hesabını kullanın.  

Yapılandırma adımları Azure Active Directory kimlik doğrulaması yapılandırmak ve kullanmak için aşağıdaki yordamları içerir.

1. Azure AD 'yi oluşturun ve doldurun.
2. İsteğe bağlı: Azure aboneliğinizle ilişkili olan Active Directory 'yi Ilişkilendirin veya değiştirin.
3. Azure SQL veritabanı sunucusu, yönetilen örnek veya [Azure SQL veri ambarı](https://azure.microsoft.com/services/sql-data-warehouse/)için Azure Active Directory Yöneticisi oluşturun.
4. İstemci bilgisayarlarınızı yapılandırın.
5. Veritabanınızda Azure AD kimlikleriyle eşlenmiş kapsanan veritabanı kullanıcıları oluşturun.
6. Azure AD kimliklerini kullanarak veritabanınıza bağlanın.

> [!NOTE]
> Azure AD oluşturma ve doldurma hakkında bilgi edinmek ve Azure SQL veritabanı, yönetilen örnek ve SQL veri ambarı ile Azure AD 'yi yapılandırmak için bkz. Azure [ad 'Yi Azure SQL veritabanı Ile yapılandırma](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Güven mimarisi

Aşağıdaki üst düzey diyagram Azure SQL veritabanı ile Azure AD kimlik doğrulaması kullanmanın çözüm mimarisini özetler. Aynı kavramlar SQL veri ambarı için de geçerlidir. Azure AD yerel kullanıcı parolasını desteklemek için yalnızca bulut bölümü ve Azure AD/Azure SQL veritabanı kabul edilir. Federal kimlik doğrulamasını (veya Windows kimlik bilgileri için Kullanıcı/parola) desteklemek için, ADFS bloğundan iletişim gerekir. Oklar, iletişim yollarını gösterir.

![aad kimlik doğrulama diyagramı][1]

Aşağıdaki diyagramda, bir istemcinin bir belirteci göndererek bir veritabanına bağlanmasına izin veren Federasyon, güven ve barındırma ilişkileri belirtilmektedir. Belirtecin kimliği bir Azure AD tarafından doğrulanır ve veritabanı tarafından güvenilir. Müşteri 1, yerel kullanıcılar veya bir Azure AD ile federe kullanıcılarla bir Azure Active Directory temsil edebilir. Müşteri 2, içeri aktarılan kullanıcılar dahil olası bir çözümü temsil eder; Bu örnekte, ADFS ile Azure Active Directory eşitlenmiş bir federal Azure Active Directory geliyor. Azure AD kimlik doğrulaması kullanarak bir veritabanına erişimin, barındırma aboneliğinin Azure AD ile ilişkilendirilmesini gerektirdiğini anlamak önemlidir. Azure SQL veritabanı veya SQL veri ambarı 'nı barındıran SQL Server oluşturmak için aynı aboneliğin kullanılması gerekir.

![Abonelik ilişkisi][2]

## <a name="administrator-structure"></a>Yönetici yapısı

Azure AD kimlik doğrulaması kullanırken, SQL veritabanı sunucusu ve yönetilen örnek için iki yönetici hesabı bulunur; özgün SQL Server Yöneticisi ve Azure AD yöneticisi. Aynı kavramlar SQL veri ambarı için de geçerlidir. Yalnızca bir Azure AD hesabını temel alan yönetici, bir kullanıcı veritabanında ilk Azure AD kapsanan veritabanı kullanıcısını oluşturabilir. Azure AD yönetici oturumu, bir Azure AD kullanıcısı veya bir Azure AD grubu olabilir. Yönetici bir grup hesabı olduğunda, SQL Server örneği için birden çok Azure AD yöneticisini etkinleştirerek herhangi bir grup üyesi tarafından kullanılabilir. Grup hesabını yönetici olarak kullanmak, SQL veritabanında kullanıcıları veya izinleri değiştirmeden Azure AD 'de grup üyelerini merkezi olarak eklemenize ve kaldırmanıza olanak tanıyarak yönetilebilirlik geliştirir. Herhangi bir anda yalnızca bir Azure AD Yöneticisi (bir kullanıcı veya grup) yapılandırılabilir.

![Yönetici yapısı][3]

## <a name="permissions"></a>İzinler

Yeni kullanıcılar oluşturmak için, veritabanında `ALTER ANY USER` izninizin olması gerekir. `ALTER ANY USER` izni herhangi bir veritabanı kullanıcısına verilebilir. `ALTER ANY USER` izin, Sunucu Yöneticisi hesapları tarafından da tutulur ve bu veritabanı için `CONTROL ON DATABASE` veya `ALTER ON DATABASE` iznine sahip veritabanı kullanıcıları ve `db_owner` veritabanı rolü üyeleri tarafından kullanılır.

Azure SQL veritabanı, yönetilen örnek veya SQL veri ambarı 'nda kapsanan bir veritabanı kullanıcısı oluşturmak için, bir Azure AD kimliği kullanarak veritabanına veya örneğe bağlanmanız gerekir. İlk kapsanan veritabanı kullanıcısını oluşturmak için, bir Azure AD Yöneticisi (veritabanının sahibi olan) kullanarak veritabanına bağlanmanız gerekir. Bu, [SQL veritabanı veya SQL veri ambarı ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](sql-database-aad-authentication-configure.md)bölümünde gösterilmiştir. Herhangi bir Azure AD kimlik doğrulaması, Azure SQL veritabanı veya SQL veri ambarı sunucusu için Azure AD yöneticisi oluşturulmuşsa mümkündür. Azure Active Directory Yöneticisi sunucudan kaldırılmışsa, daha önce SQL Server içinde oluşturulan mevcut Azure Active Directory Kullanıcılar artık Azure Active Directory kimlik bilgilerini kullanarak veritabanına bağlanamaz.

## <a name="azure-ad-features-and-limitations"></a>Azure AD özellikleri ve sınırlamaları

- Azure AD 'nin aşağıdaki üyeleri Azure SQL Server veya SQL veri ambarı 'nda sağlanabilir:

  - Yerel Üyeler: yönetilen etki alanında veya bir müşteri etki alanında Azure AD 'de oluşturulan bir üye. Daha fazla bilgi için bkz. [Azure AD 'ye kendi etki alanı adınızı ekleme](../active-directory/active-directory-domains-add-azure-portal.md).
  - Federasyon etki alanı üyeleri: Azure AD 'de bir Federasyon etki alanı ile oluşturulan bir üye. Daha fazla bilgi için bkz. [Microsoft Azure artık Windows Server Active Directory ile Federasyonu destekliyor](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Diğer Azure AD 'den, yerel veya Federasyon etki alanı üyesi olan Üyeler içeri aktarıldı.
  - Güvenlik grupları olarak oluşturulan grupları Active Directory.

- `db_owner` Server rolüne sahip bir grubun parçası olan Azure AD kullanıcıları, Azure SQL veritabanı ve Azure SQL veri ambarı 'nda **[VERITABANı KAPSAMLı KIMLIK bilgisi oluştur](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** sözdizimini kullanamaz. Aşağıdaki hatayı görürsünüz:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    **VERITABANı KAPSAMLı KIMLIK bilgileri oluşturma** sorununu hafifletmek için, `db_owner` rolünü tek BIR Azure AD kullanıcısına doğrudan verin.

- Bu sistem işlevleri, Azure AD sorumluları altında yürütüldüğünde NULL değerler döndürüyor:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Yönetilen Örnekler

- Azure AD Server sorumluları (oturumlar) ve kullanıcılar, [yönetilen örnekler](sql-database-managed-instance.md)için Önizleme özelliği olarak desteklenir.
- Bir Azure AD grubuna eşlenen Azure AD Server sorumlularını (oturum açma) veritabanı sahibi olarak ayarlama, [yönetilen örneklerde](sql-database-managed-instance.md)desteklenmez.
    - Bunun bir uzantısı, bir grup `dbcreator` sunucusu rolünün bir parçası olarak eklendiğinde, bu gruptaki kullanıcılar yönetilen örneğe bağlanabilir ve yeni veritabanları oluşturabilir, ancak veritabanına erişemeyecektir. Bunun nedeni, yeni veritabanı sahibinin Azure AD kullanıcısı değil, SA. Bu sorun, bireysel kullanıcı `dbcreator` sunucu rolüne eklenirse bildirim almaz.
- SQL Aracısı yönetimi ve işlerin yürütülmesi, Azure AD Server sorumluları (oturumlar) için desteklenir.
- Veritabanı yedekleme ve geri yükleme işlemleri, Azure AD Server sorumluları (oturumlar) tarafından yürütülebilir.
- Azure AD Server sorumluları (oturum açmalar) ve kimlik doğrulama olayları ile ilgili tüm deyimlerin denetlenmesi desteklenir.
- Sysadmin sunucu rolünün üyeleri olan Azure AD Server sorumluları (oturumlar) için adanmış yönetici bağlantısı desteklenir.
    - SQLCMD yardımcı programı ve SQL Server Management Studio aracılığıyla desteklenir.
- Oturum açma Tetikleyicileri, Azure AD Server sorumlularına (oturum açmalar) gelen oturum açma olayları için desteklenir.
- Hizmet Aracısı ve DB Mail, bir Azure AD sunucusu sorumlusu (oturum açma) kullanılarak ayarlanabilir.


## <a name="connecting-using-azure-ad-identities"></a>Azure AD kimliklerini kullanarak bağlanma

Azure Active Directory kimlik doğrulaması, Azure AD kimliklerini kullanarak bir veritabanına bağlanmak için aşağıdaki yöntemleri destekler:

- Azure Active Directory parolası
- Azure Active Directory tümleşik
- MFA ile evrensel Azure Active Directory
- Uygulama belirteci kimlik doğrulamasını kullanma

Aşağıdaki kimlik doğrulama yöntemleri Azure AD Server sorumluları (oturum açmalar) için desteklenir (**genel önizleme**):

- Azure Active Directory parolası
- Azure Active Directory tümleşik
- MFA ile evrensel Azure Active Directory


### <a name="additional-considerations"></a>Diğer konular

- Yönetilebilirlik geliştirmek için, bir yönetici olarak adanmış bir Azure AD grubu sağlamanızı öneririz.   
- Azure SQL veritabanı sunucusu veya Azure SQL veri ambarı için herhangi bir zamanda yalnızca bir Azure AD Yöneticisi (Kullanıcı veya grup) yapılandırılabilir.
  - Yönetilen örnekler için Azure AD Server sorumlularını (oturum açma) ekleme (**genel önizleme**), `sysadmin` rolüne eklenebilen birden çok Azure AD Server sorumlusu (oturum açma) oluşturma olasılığa izin verir.
- SQL Server için yalnızca bir Azure AD yöneticisi, bir Azure Active Directory hesabı kullanarak başlangıçta Azure SQL veritabanı sunucusuna, yönetilen örneğe veya Azure SQL veri ambarına bağlanabilir. Active Directory Yöneticisi, sonraki Azure AD veritabanı kullanıcılarını yapılandırabilir.   
- Bağlantı zaman aşımını 30 saniyeye ayarlamayı öneririz.   
- SQL Server 2016 Management Studio ve SQL Server Veri Araçları Visual Studio 2015 için (sürüm 14.0.60311.1 Nisan 2016 veya üzeri) Azure Active Directory kimlik doğrulamasını destekler. (Azure AD kimlik doğrulaması, **SqlServer için .NET Framework veri sağlayıcısı**tarafından desteklenir; en az sürüm .NET Framework 4,6). Bu nedenle, bu araçların ve veri katmanı uygulamalarının en yeni sürümleri (DAC ve. BACPAC), Azure AD kimlik doğrulaması kullanabilir.   
- Version 15.0.1 sürümünden itibaren [sqlcmd yardımcı](/sql/tools/sqlcmd-utility) programı ve [bcp](/sql/tools/bcp-utility) ile etkileşimli kimlik doğrulama desteği Active Directory.
- Visual Studio 2015 için SQL Server Veri Araçları, en az veri araçları 'nın 2016 Nisan sürümü (sürüm 14.0.60311.1) gerektirir. Şu anda Azure AD kullanıcıları SSDT Nesne Gezgini gösterilmez. Geçici bir çözüm olarak, [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx)içindeki kullanıcıları görüntüleyin.   
- [SQL Server Için MICROSOFT JDBC sürücüsü 6,0](https://www.microsoft.com/download/details.aspx?id=11774) , Azure AD kimlik doğrulamasını destekler. Ayrıca bkz. [bağlantı özelliklerini ayarlama](https://msdn.microsoft.com/library/ms378988.aspx).   
- PolyBase, Azure AD kimlik doğrulamasını kullanarak kimlik doğrulaması yapamaz.   
- Azure AD kimlik doğrulaması, SQL veritabanı için Azure portal **Içeri aktarma veritabanı** ve **dışarı aktarma veritabanı** dikey pencereleri tarafından desteklenir. Azure AD kimlik doğrulaması kullanılarak içeri ve dışarı aktarma, PowerShell komutundan de desteklenir.   
- Azure AD kimlik doğrulaması, CLı kullanarak SQL veritabanı, yönetilen örnek ve SQL veri ambarı için desteklenir. Daha fazla bilgi için bkz. [SQL veritabanı veya SQL veri ambarı ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](sql-database-aad-authentication-configure.md) ve [az SQL Server SQL Server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Sonraki adımlar

- Azure AD oluşturma ve doldurma hakkında bilgi edinmek ve Azure SQL veritabanı veya Azure SQL veri ambarı ile Azure AD 'yi yapılandırmak için bkz. [SQL veritabanı, yönetilen örnek veya SQL veri ambarı ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](sql-database-aad-authentication-configure.md).
- Yönetilen örneklerle Azure AD Server sorumlularını (oturum açma) kullanmanın bir öğreticisi için bkz. [yönetilen örneklerle Azure ad sunucu sorumluları (oturumlar)](sql-database-managed-instance-aad-security-tutorial.md)
- SQL Veritabanında erişim ve denetime genel bakış için bkz. [SQL Veritabanında erişim ve denetim](sql-database-control-access.md).
- SQL Veritabanındaki oturum açma bilgileri, kullanıcılar ve veritabanı rollerine genel bakış için bkz. [Oturum açma bilgileri, kullanıcılar ve veritabanı rolleri](sql-database-manage-logins.md).
- Veritabanı sorumluları hakkında daha fazla bilgi için bkz. [Sorumlular](https://msdn.microsoft.com/library/ms181127.aspx).
- Veritabanı rolleri hakkında daha fazla bilgi için bkz. [Veritabanı rolleri](https://msdn.microsoft.com/library/ms189121.aspx).
- Yönetilen örnekler için Azure AD Server sorumlularını (oturum açma) oluşturma hakkında sözdizimi için bkz. [oturum oluşturma](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
- SQL Veritabanındaki güvenlik duvarı kuralları hakkında daha fazla bilgi için bkz. [SQL Veritabanı güvenlik duvarı kuralları](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png
