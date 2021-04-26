---
title: Azure Active Directory
description: SQL veritabanı, yönetilen örnek ve SYNAPSE SQL ile kimlik doğrulaması için Azure Active Directory kullanma hakkında bilgi edinin
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 92d06a95dcd32501a05dfd50e81f768f59742bd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674343"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>SYNAPSE SQL ile kimlik doğrulaması için Azure Active Directory kimlik doğrulaması kullanma

Azure Active Directory kimlik doğrulaması, Azure Active Directory kimlik (Azure AD) kullanarak [Azure SYNAPSE Analytics](../overview-faq.md) 'e bağlanan bir mekanizmadır.

Azure AD kimlik doğrulamasıyla, izin yönetimini basitleştirmek için Azure SYNAPSE erişimi olan kullanıcı kimliklerini merkezi olarak yönetebilirsiniz. Avantajları şunlardır:

- Normal Kullanıcı adı ve parola kimlik doğrulamasına bir alternatif sağlar.
- Sunucu genelindeki Kullanıcı kimliklerinin uzamasını durdurmaya yardımcı olur.
- Tek bir yerde parola döndürmeye izin verir.
- Müşteriler, dış (Azure AD) gruplarını kullanarak izinleri yönetebilir.
- Tümleşik Windows kimlik doğrulamasını ve Azure Active Directory tarafından desteklenen diğer kimlik doğrulama biçimlerini etkinleştirerek parolaların depolanmasını ortadan kaldırabilir.
- Azure AD, Azure SYNAPSE 'e bağlanan uygulamalar için belirteç tabanlı kimlik doğrulamasını destekler.
- Azure AD kimlik doğrulaması, etki alanı eşitlemesi olmadan yerel bir Azure Active Directory için ADFS (etki alanı Federasyonu) veya yerel kullanıcı/parola kimlik doğrulamasını destekler.
- Azure AD, Multi-Factor Authentication (MFA) içeren Active Directory Evrensel Kimlik Doğrulaması'nın kullanıldığı SQL Server Management Studio'dan gelen bağlantıları destekler.  MFA, telefon araması, SMS mesajı, PIN ile akıllı kartlar ya da mobil uygulama bildirimi gibi kolay bir doğrulama seçenekleri aralığıyla güçlü kimlik doğrulaması içerir. Daha fazla bilgi için bkz. [SYNAPSE SQL Ile Azure AD MFA Için SSMS desteği](mfa-authentication.md).
- Azure AD, Active Directory Etkileşimli Kimlik Doğrulaması'nın kullanıldığı SQL Server Veri Araçları'ndan (SSDT) gelen benzer bağlantıları destekler. Daha fazla bilgi için bkz. [SQL Server Veri Araçları'na (SSDT) Azure Active Directory desteği](/sql/ssdt/azure-active-directory?view=azure-sqldw-latest&preserve-view=true).

Yapılandırma adımları Azure Active Directory kimlik doğrulaması yapılandırmak ve kullanmak için aşağıdaki yordamları içerir.

1. Azure AD 'yi oluşturun ve doldurun.
2. Azure Active Directory kimliği oluşturma
3. SYNAPSE çalışma alanında oluşturulan Azure Active Directory kimliğine rol atama
4. Azure AD kimliklerini kullanarak SYNAPSE Studio 'ya bağlanın.

## <a name="azure-ad-pass-through-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te Azure AD geçişi

Azure SYNAPSE Analytics, Azure Active Directory kimliğinizi kullanarak Data Lake 'taki verilere erişmenizi sağlar.

Farklı veri altyapılarında yer alan dosyalarda ve verilerde erişim haklarının tanımlanması, izinlerin birden çok yerde tanımlanması yerine tanımlandıkları tek bir yere sahip olmak için Data Lake çözümlerinizi basitleştirmenizi sağlar.

## <a name="trust-architecture"></a>Güven mimarisi

Aşağıdaki üst düzey diyagram, Azure AD kimlik doğrulamasını SYNAPSE SQL ile kullanmayla ilgili çözüm mimarisini özetler. Azure AD yerel kullanıcı parolasını desteklemek için yalnızca bulut bölümü ve Azure AD/SYNAPSE SYNAPSE SQL kabul edilir. Federal kimlik doğrulamasını (veya Windows kimlik bilgileri için Kullanıcı/parola) desteklemek için, ADFS bloğundan iletişim gerekir. Oklar, iletişim yollarını gösterir.

![Azure AD kimlik doğrulama diyagramı](./media/aad-authentication/1-active-directory-authentication-diagram.png)

Aşağıdaki diyagramda, bir istemcinin bir belirteci göndererek bir veritabanına bağlanmasına izin veren Federasyon, güven ve barındırma ilişkileri belirtilmektedir. Belirtecin kimliği bir Azure AD tarafından doğrulanır ve veritabanı tarafından güvenilir. 

Müşteri 1, yerel kullanıcılar veya bir Azure AD ile federe kullanıcılarla bir Azure Active Directory temsil edebilir. Müşteri 2, içeri aktarılan kullanıcılar dahil olası bir çözümü temsil eder; Bu örnekte, ADFS ile Azure Active Directory eşitlenmiş bir federal Azure Active Directory geliyor. 

Azure AD kimlik doğrulaması kullanarak bir veritabanına erişimin, barındırma aboneliğinin Azure AD ile ilişkilendirilmesini gerektirdiğini anlamak önemlidir. Azure SQL veritabanını veya adanmış SQL havuzunu barındıran SQL Server oluşturmak için aynı aboneliğin kullanılması gerekir.

![Abonelik ilişkisi](./media/aad-authentication/2-subscription-relationship.png)

## <a name="administrator-structure"></a>Yönetici yapısı

Azure AD kimlik doğrulaması kullanılırken, SYNAPSE SQL için iki yönetici hesabı bulunur; özgün SQL Server Yöneticisi ve Azure AD yöneticisi. Yalnızca bir Azure AD hesabını temel alan yönetici, bir kullanıcı veritabanında ilk Azure AD kapsanan veritabanı kullanıcısını oluşturabilir. 

Azure AD yönetici oturumu, bir Azure AD kullanıcısı veya bir Azure AD grubu olabilir. Yönetici bir grup hesabı olduğunda, SYNAPSE SQL örneği için birden çok Azure AD yöneticisini etkinleştirerek herhangi bir grup üyesi tarafından kullanılabilir. 

Grup hesabını yönetici olarak kullanmak, Azure SYNAPSE Analytics çalışma alanındaki kullanıcıları veya izinleri değiştirmeden Azure AD 'de grup üyelerini merkezi olarak eklemenize ve kaldırmanıza olanak tanıyarak yönetilebilirlik geliştirir. Herhangi bir anda yalnızca bir Azure AD Yöneticisi (bir kullanıcı veya grup) yapılandırılabilir.

![Yönetici yapısı](./media/aad-authentication/3-admin-structure.png)

## <a name="permissions"></a>İzinler

Yeni kullanıcılar oluşturmak için, veritabanında izninizin olması gerekir `ALTER ANY USER` . `ALTER ANY USER`Herhangi bir veritabanı kullanıcısına izin verilebilir. `ALTER ANY USER`Ayrıca, bu izin Sunucu Yöneticisi hesapları tarafından ve `CONTROL ON DATABASE` Bu veritabanı için veya iznine sahip veritabanı kullanıcıları `ALTER ON DATABASE` ve veritabanı rolünün üyeleri tarafından tutulur `db_owner` .

SYNAPSE SQL 'de kapsanan bir veritabanı kullanıcısı oluşturmak için, bir Azure AD kimliği kullanarak veritabanına veya örneğe bağlanmanız gerekir. İlk kapsanan veritabanı kullanıcısını oluşturmak için, bir Azure AD Yöneticisi (veritabanının sahibi olan) kullanarak veritabanına bağlanmanız gerekir. 

Tüm Azure AD kimlik doğrulaması yalnızca Azure AD yöneticisi SYNAPSE SQL için oluşturulduysa mümkündür. Azure Active Directory Yöneticisi sunucudan kaldırılmışsa, daha önce SYNAPSE SQL içinde oluşturulan mevcut Azure Active Directory Kullanıcılar artık Azure Active Directory kimlik bilgilerini kullanarak veritabanına bağlanamaz.
 
## <a name="azure-ad-features-and-limitations"></a>Azure AD özellikleri ve sınırlamaları

- Azure AD 'nin aşağıdaki üyeleri SYNAPSE SQL 'de sağlanabilir:

  - Yerel Üyeler: yönetilen etki alanında veya bir müşteri etki alanında Azure AD 'de oluşturulan bir üye. Daha fazla bilgi için bkz. [Azure AD 'ye kendi etki alanı adınızı ekleme](../../active-directory/fundamentals/add-custom-domain.md).
  - Federasyon etki alanı üyeleri: Azure AD 'de bir Federasyon etki alanı ile oluşturulan bir üye. Daha fazla bilgi için bkz. [Microsoft Azure artık Windows Server Active Directory ile Federasyonu destekliyor](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Yerel veya Federasyon etki alanı üyesi olan diğer Azure reklamlarından alınan Üyeler.
  - Güvenlik grupları olarak oluşturulan grupları Active Directory.

- Sunucu rolüne sahip bir grubun parçası olan Azure AD kullanıcıları `db_owner` , SYNAPSE SQL 'e KARŞı **[VERITABANı KAPSAMLı kimlik bilgisi oluştur](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true)** söz dizimini kullanamaz. Aşağıdaki hatayı görürsünüz:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    `db_owner` **VERITABANı KAPSAMLı kimlik bilgileri oluşturma** sorununu hafifletmek için role doğrudan tek bir Azure AD kullanıcısına izin verin.

- Bu sistem işlevleri, Azure AD sorumluları altında yürütüldüğünde NULL değerler döndürüyor:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

## <a name="connect-using-azure-ad-identities"></a>Azure AD kimliklerini kullanarak bağlanma

Azure Active Directory kimlik doğrulaması, Azure AD kimliklerini kullanarak bir veritabanına bağlanmak için aşağıdaki yöntemleri destekler:

- Azure Active Directory parolası
- Azure Active Directory tümleşik
- MFA ile evrensel Azure Active Directory
- Uygulama belirteci kimlik doğrulamasını kullanma

Aşağıdaki kimlik doğrulama yöntemleri Azure AD Server sorumluları (oturum açmalar) için desteklenir:

- Azure Active Directory parolası
- Azure Active Directory tümleşik
- MFA ile evrensel Azure Active Directory

### <a name="additional-considerations"></a>Diğer konular

- Yönetilebilirlik geliştirmek için, bir yönetici olarak adanmış bir Azure AD grubu sağlamanızı öneririz.
- Yalnızca bir Azure AD Yöneticisi (bir kullanıcı veya grup), SYNAPSE SQL havuzu için dilediğiniz zaman yapılandırılabilir.
  - SYNAPSE SQL için Azure AD Server sorumlularını (oturum açma) ekleme, role eklenebilen birden çok Azure AD Server sorumlusu (oturum açma) oluşturma olasılığa izin verir `sysadmin` .
- Yalnızca SYNAPSE SQL için bir Azure AD yöneticisi, Azure Active Directory bir hesabı kullanarak başlangıçta SYNAPSE SQL 'e bağlanabilir. Active Directory Yöneticisi, sonraki Azure AD veritabanı kullanıcılarını yapılandırabilir.
- Bağlantı zaman aşımını 30 saniyeye ayarlamayı öneririz.
- SQL Server 2016 Management Studio ve SQL Server Veri Araçları Visual Studio 2015 için (sürüm 14.0.60311.1 Nisan 2016 veya üzeri) Azure Active Directory kimlik doğrulamasını destekler. (Azure AD kimlik doğrulaması, **SqlServer için .NET Framework veri sağlayıcısı** tarafından desteklenir; en az sürüm .NET Framework 4,6). Bu nedenle, bu araçların ve veri katmanı uygulamalarının en yeni sürümleri (DAC ve. BACPAC), Azure AD kimlik doğrulaması kullanabilir.
- Version 15.0.1 sürümünden itibaren [sqlcmd yardımcı](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true) programı ve [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) ile etkileşimli kimlik doğrulama desteği Active Directory.
- Visual Studio 2015 için SQL Server Veri Araçları, en az veri araçları 'nın 2016 Nisan sürümü (sürüm 14.0.60311.1) gerektirir. Şu anda, Azure AD kullanıcıları SSDT Nesne Gezgini gösterilmez. Geçici bir çözüm olarak, [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=azure-sqldw-latest&preserve-view=true)kullanıcıları görüntüleyin.
- [SQL Server Için MICROSOFT JDBC sürücüsü 6,0](https://www.microsoft.com/download/details.aspx?id=11774) , Azure AD kimlik doğrulamasını destekler. Ayrıca bkz. [bağlantı özelliklerini ayarlama](/sql/connect/jdbc/setting-the-connection-properties?view=azure-sqldw-latest&preserve-view=true).

## <a name="next-steps"></a>Sonraki adımlar

- SYNAPSE SQL 'deki erişime ve denetime genel bakış için bkz. [SYNAPSE SQL Access Control](../security/synapse-workspace-access-control-overview.md).
- Veritabanı sorumluları hakkında daha fazla bilgi için bkz. [Sorumlular](/sql/relational-databases/security/authentication-access/principals-database-engine?view=azure-sqldw-latest&preserve-view=true).
- Veritabanı rolleri hakkında daha fazla bilgi için bkz. [Veritabanı rolleri](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true).

