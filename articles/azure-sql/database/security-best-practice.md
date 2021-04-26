---
title: Ortak güvenlik gereksinimlerini ele almak için PlayBook
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Bu makalede, Azure SQL veritabanı ve Azure SQL yönetilen örneği için ortak güvenlik gereksinimleri ve en iyi uygulamalar sağlanmaktadır.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 09/21/2020
ms.reviewer: ''
ms.openlocfilehash: c16764d1ce985755b6a3042873cc18b09b697bcf
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551620"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL veritabanı ve Azure SQL yönetilen örneği ile ortak güvenlik gereksinimlerini ele almak için PlayBook
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Bu makalede, ortak güvenlik gereksinimlerinin nasıl çözübir konusunda en iyi yöntemler sunulmaktadır. Tüm gereksinimler tüm ortamlar için geçerli değildir ve uygulanacak özellikler hakkında veritabanınıza ve güvenlik ekibinize başvurmanız gerekir.

## <a name="solving-common-security-requirements"></a>Ortak güvenlik gereksinimlerini çözme

Bu belge, Azure SQL veritabanı ve Azure SQL yönetilen örneği kullanılarak yeni veya mevcut uygulamalar için ortak güvenlik gereksinimlerinin nasıl çözülebileceğine ilişkin yönergeler sağlar. Üst düzey güvenlik alanlarıyla düzenlenmiştir. Belirli tehditleri adreslemek için [ortak güvenlik tehditleri ve olası azaltmaları](#common-security-threats-and-potential-mitigations) bölümüne bakın. Şirket içinden Azure 'a uygulama geçirirken sunulan önerilerin bazıları uygulanabilir olsa da, geçiş senaryoları bu belgenin odağı değildir.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Bu kılavuzda ele alınan Azure SQL veritabanı dağıtımı teklifleri

- [Azure SQL veritabanı](./index.yml): [sunuculardaki](logical-servers.md) [tek veritabanları](single-database-overview.md) ve [elastik havuzlar](elastic-pool-overview.md)
- [Azure SQL Yönetilen Örnek](../managed-instance/sql-managed-instance-paas-overview.md)

### <a name="deployment-offers-not-covered-in-this-guide"></a>Bu kılavuzda kapsanmayan dağıtım teklifleri

- Azure Synapse Analytics
- Azure SQL VM 'Leri (IaaS)
- SQL Server

### <a name="audience"></a>Hedef kitle

Bu kılavuza yönelik hedef kitleler, müşterilerin Azure SQL veritabanı 'nı güvenli hale getirmeye yönelik sorulardır. Bu en iyi uygulama makalesinde ilgilendiğiniz roller şunları içerir, ancak bunlarla sınırlı değildir:

- Güvenlik mimarları
- Güvenlik yöneticileri
- Uyumluluk ofisleri
- Gizlilik ofisleri
- Güvenlik mühendisleri

### <a name="using-this-guide"></a><a id="using"></a> Bu kılavuzu kullanma

Bu belge, mevcut [Azure SQL veritabanı güvenlik](security-overview.md) belgeleriyle bir yardımcı olarak hazırlanmıştır.

Aksi belirtilmedikçe, ilgili hedefi veya gereksinimi elde etmek için her bölümde listelenen en iyi uygulamaları izlemeniz önerilir. Belirli güvenlik uyumluluk standartlarını veya en iyi uygulamaları karşılamak için, önemli mevzuata uyumluluk denetimleri, uygun yerlerde gereksinimler veya hedefler bölümünde listelenir. Bunlar, bu kağıda başvuruda bulunulan güvenlik standartları ve düzenlemelerdir:

- [Fedrampa](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): cm-3, SDL-3
- [ISO/ıec 27001](https://www.iso27001security.com/html/27001.html): Access Control, şifreleme
- [Microsoft operasyonel güvenlik güvencesi (OSA) uygulamaları](https://www.microsoft.com/securityengineering/osa/practices): uygulama #1-6 ve #9
- [NIST özel yayını 800-53 güvenlik denetimleri](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

Burada listelenen öneriler ve en iyi uygulamalar güncelleştirilmeye devam ediyor. Bu makalenin altındaki **geri bildirim** bağlantısını kullanarak bu belge için giriş veya herhangi bir düzeltme sağlayın.

## <a name="authentication"></a>Kimlik Doğrulaması

Kimlik doğrulama, kullanıcının talep ettikleri kim olduğunu kanıtlama işlemidir. Azure SQL veritabanı ve SQL yönetilen örneği iki tür kimlik doğrulamasını destekler:

- SQL kimlik doğrulaması
- Azure Active Directory kimlik doğrulaması

> [!NOTE]
> Azure Active Directory kimlik doğrulaması, tüm araçlar ve üçüncü taraf uygulamalar için desteklenmez.

### <a name="central-management-for-identities"></a>Kimlikler için merkezi yönetim

Merkezi kimlik yönetimi aşağıdaki avantajları sunar:

- Sunucular, veritabanları ve yönetilen örnekler arasında oturum açmaları olmadan grup hesaplarını yönetin ve Kullanıcı izinlerini denetleyin.
- Basitleştirilmiş ve esnek izin yönetimi.
- Ölçekteki uygulamaların yönetimi.

**Nasıl uygulanır**:

- Merkezi kimlik yönetimi için Azure Active Directory (Azure AD) kimlik doğrulaması kullanın.

**En iyi uygulamalar**:

- Azure AD kiracısı oluşturun ve insan kullanıcılarını temsil eden [kullanıcılar oluşturun](../../active-directory/fundamentals/add-users-azure-active-directory.md) ve uygulamaları, hizmetleri ve otomasyon araçlarını temsil etmek için [hizmet sorumluları](../../active-directory/develop/app-objects-and-service-principals.md) oluşturun. Hizmet sorumluları, Windows ve Linux 'ta hizmet hesaplarıyla eşdeğerdir.

- Kaynaklara erişim haklarını Grup ataması aracılığıyla Azure AD sorumlularına atama: Azure AD grupları oluşturun, gruplara erişim izni verin ve gruplara ayrı ayrı Üyeler ekleyin. Veritabanınızda, Azure AD gruplarınızı eşleyen, kapsanan veritabanı kullanıcıları oluşturun. Veritabanına izin atamak için, Azure AD gruplarınızla ilişkilendirilen kullanıcıları, uygun izinlerle birlikte veritabanı rollerine koyun.
  - SQL ile [Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](authentication-aad-configure.md) ve [SQL ile kimlik doğrulaması Için Azure ad kullanma](authentication-aad-overview.md)makalelerini inceleyin.
  > [!NOTE]
  > SQL yönetilen örneği 'nde, ana veritabanında Azure AD sorumluları ile eşlenen oturum açma işlemleri de oluşturabilirsiniz. Bkz. [oturum oluşturma (Transact-SQL)](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

- Azure AD gruplarının kullanılması, izin yönetimini ve her iki grup sahibini de basitleştirir ve kaynak sahibi gruba/gruptan üye ekleyip kaldırabilir.

- Her sunucu veya yönetilen örnek için Azure AD yöneticileri için ayrı bir grup oluşturun.

  - [Sunucunuz için Azure Active Directory Yöneticisi sağlama](authentication-aad-configure.md#provision-azure-ad-admin-sql-database)makalesine bakın.

- Azure AD denetim etkinlik raporlarını kullanarak Azure AD grup üyeliği değişikliklerini izleyin.

- Yönetilen bir örnek için, bir Azure AD Yöneticisi oluşturmak için ayrı bir adım gerekir.
  - [Yönetilen örneğiniz için Azure Active Directory Yöneticisi sağlama](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)makalesine bakın.

> [!NOTE]
>
> - Azure AD kimlik doğrulaması Azure SQL denetim günlüklerine kaydedilir, ancak Azure AD oturum açma günlüklerinde değildir.
> - Azure 'da verilen Azure RBAC izinleri, Azure SQL veritabanı veya SQL yönetilen örnek izinleri için geçerlidir. Bu izinlerin mevcut SQL izinleri kullanılarak el ile oluşturulması/eşlenmesi gerekir.
> - İstemci tarafında, Azure AD kimlik doğrulamasının internet 'e erişmesi veya bir sanal ağa Kullanıcı tanımlı yol (UDR) aracılığıyla ihtiyacı vardır.
> - Azure AD erişim belirteci, istemci tarafında önbelleğe alınır ve süresi, belirteç yapılandırmasına bağlıdır. [Azure Active Directory yapılandırılabilir belirteç yaşam süreleri](../../active-directory/develop/active-directory-configurable-token-lifetimes.md) makalesine bakın
> - Azure AD kimlik doğrulaması sorunlarını giderme konusunda yönergeler için şu bloga bakın: [Azure AD sorunlarını giderme](https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991).

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication

> Bahsedilen: OSA uygulaması #2, ISO Access Control (AC)

Azure AD Multi-Factor Authentication, birden fazla kimlik doğrulama biçimi gerektirerek ek güvenlik sağlanmasına yardımcı olur.

**Nasıl uygulanır**:

- Koşullu erişimi kullanarak Azure AD 'de [Multi-Factor Authentication etkinleştirin](../../active-directory/authentication/concept-mfa-howitworks.md) ve etkileşimli kimlik doğrulaması kullanın.

- Alternatif olarak, tüm Azure AD veya AD etki alanı için Multi-Factor Authentication etkinleştirilir.

**En iyi uygulamalar**:

- Azure AD 'de koşullu erişimi etkinleştirin (Premium aboneliği gerektirir).
  - [Azure AD 'de koşullu erişim](../../active-directory/conditional-access/overview.md)makalesine bakın.  

- Azure AD grupları oluşturun ve Azure AD koşullu erişim kullanarak seçili gruplar için Multi-Factor Authentication İlkesi etkinleştirin.
  - [Koşullu erişim dağıtımını planlayın](../../active-directory/conditional-access/plan-conditional-access.md)başlıklı makaleye bakın.

- Multi-Factor Authentication tüm Azure AD veya Azure AD ile Federal tüm Active Directory için etkinleştirilebilir.

- Azure SQL veritabanı için Azure AD etkileşimli kimlik doğrulaması modunu ve bir parolanın etkileşimli olarak istendiği Azure SQL yönetilen örneğini, ardından Multi-Factor Authentication şunu kullanın:
  - SSMS 'de evrensel kimlik doğrulaması kullanın. [Azure SQL veritabanı, SQL yönetilen örneği, Azure Synapse (Multi-Factor Authentication IÇIN SSMS desteği) Ile Multi-Factor Azure AD kimlik doğrulamasını kullanma](authentication-mfa-ssms-overview.md)makalesine bakın.
  - SQL Server Veri Araçları (SSDT) içinde desteklenen etkileşimli kimlik doğrulaması kullanın. [SQL Server veri araçları (SSDT) Azure Active Directory destek](/sql/ssdt/azure-active-directory?view=azuresqldb-current&preserve-view=true)makalesine bakın.
  - Multi-Factor Authentication destekleyen diğer SQL araçlarını kullanın.
    - Dışarı aktarma/ayıklama/dağıtım veritabanı için SSMS Sihirbazı desteği  
    - [sqlpackage.exe](/sql/tools/sqlpackage): '/uA ' seçeneği
    - [sqlcmd yardımcı programı](/sql/tools/sqlcmd-utility): Option-G (etkileşimli)
    - [bcp yardımcı programı](/sql/tools/bcp-utility): Option-G (etkileşimli)

- Multi-Factor Authentication desteğiyle etkileşimli kimlik doğrulaması kullanarak Azure SQL veritabanı 'na veya Azure SQL yönetilen örneğine bağlanmak için uygulamalarınızı uygulayın.
  - Azure [AD Multi-Factor Authentication Ile Azure SQL veritabanı 'Na bağlanma](active-directory-interactive-connect-azure-sql-db.md)makalesine bakın.
  > [!NOTE]
  > Bu kimlik doğrulama modu, Kullanıcı tabanlı kimlikler gerektirir. Tek tek Azure AD Kullanıcı kimlik doğrulamasını atlayarak güvenilir bir kimlik modelinin kullanıldığı durumlarda (ör. Azure kaynakları için yönetilen kimliği kullanarak) Multi-Factor Authentication uygulanmaz.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Kullanıcılar için parola tabanlı kimlik doğrulaması kullanımını en aza indirme

> Bahsedilen: OSA uygulaması #4, ISO Access Control (AC)

Parola tabanlı kimlik doğrulama yöntemleri, daha zayıf bir kimlik doğrulama biçimidir. Kimlik bilgileri tehlikeye girebilir veya yanlışlıkla verilebilir.

**Nasıl uygulanır**:

- Parolaların kullanımını ortadan kaldıran bir Azure AD Tümleşik kimlik doğrulaması kullanın.

**En iyi uygulamalar**:

- Windows kimlik bilgilerini kullanarak çoklu oturum açma kimlik doğrulaması kullanın. Şirket içi AD etki alanını Azure AD ile Federasyonun ve tümleşik Windows kimlik doğrulaması kullanın (Azure AD ile etki alanına katılmış makineler için).
  - [Azure AD Tümleşik kimlik doğrulaması Için SSMS desteği](authentication-aad-configure.md#active-directory-integrated-authentication)makalesine bakın.

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Uygulamalar için parola tabanlı kimlik doğrulama kullanımını en aza indirme

> Bahsedilen: OSA uygulaması #4, ISO Access Control (AC)

**Nasıl uygulanır**:

- Azure yönetilen kimliğini etkinleştirin. Tümleşik veya sertifika tabanlı kimlik doğrulaması da kullanabilirsiniz.

**En iyi uygulamalar**:

- [Azure kaynakları için Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md)kullanın.
  - [Sistem tarafından atanan yönetilen kimlik](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)
  - [Kullanıcı tarafından atanan yönetilen kimlik](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Yönetilen kimliğe sahip Azure App Service Azure SQL veritabanı 'nı kullanın (kod değişikliği olmadan)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Bir uygulama için sertifika tabanlı kimlik doğrulaması kullanın.
  - Bu [kod örneğine](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token)bakın.

- Tümleşik Federasyon etki alanı ve etki alanına katılmış makine için Azure AD kimlik doğrulamasını kullanın (yukarıdaki bölüme bakın).
  - [Tümleşik kimlik doğrulaması için örnek uygulamaya](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated)bakın.

### <a name="protect-passwords-and-secrets"></a>Parolaları ve gizli dizileri koruma

Parolaların kaçınılmaz olduğu durumlarda, bunların güvenli olduklarından emin olun.

**Nasıl uygulanır**:

- Parolaları ve gizli dizileri depolamak için Azure Key Vault kullanın. Uygun olduğunda Azure AD kullanıcılarıyla Azure SQL veritabanı için Multi-Factor Authentication kullanın.

**En iyi uygulamalar**:

- Parola veya gizli dizileri önleme mümkün değilse, Kullanıcı parolalarını ve uygulama gizli dizilerini Azure Key Vault depolayın ve Key Vault erişim ilkeleri aracılığıyla erişimi yönetin.

- Çeşitli uygulama geliştirme çerçeveleri, uygulamadaki gizli dizileri korumak için çerçeveye özgü mekanizmalar de sunabilir. Örneğin: [ASP.NET Core uygulaması](/aspnet/core/security/app-secrets?tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>Eski uygulamalar için SQL kimlik doğrulaması kullan

SQL kimlik doğrulaması, Kullanıcı adı ve parola kullanarak Azure SQL veritabanı veya SQL yönetilen örneği ile bağlantı kurulurken bir kullanıcının kimlik doğrulamasını ifade eder. Her bir sunucuda veya yönetilen örnekte ve her bir veritabanında oluşturulan bir kullanıcı için bir oturum açma gerekir.

**Nasıl uygulanır**:

- SQL kimlik doğrulaması kullanın.

**En iyi uygulamalar**:

- Sunucu veya örnek Yöneticisi olarak, oturum açma bilgileri ve kullanıcılar oluşturun. İçerilen veritabanı kullanıcıları parolalarla birlikte kullanmadıkça, tüm parolalar ana veritabanında depolanır.
  - [SQL veritabanı, SQL yönetilen örneği ve Azure SYNAPSE Analytics 'e veritabanı erişimini denetleme ve verme](logins-create-manage.md)makalesine bakın.

## <a name="access-management"></a>Erişim yönetimi

Erişim yönetimi (yetkilendirme olarak da bilinir), yetkili kullanıcıların Azure SQL veritabanı veya SQL yönetilen örneği için erişim ve ayrıcalıkların denetlenmesine ve yönetilmesine yönelik bir işlemdir.

### <a name="implement-principle-of-least-privilege"></a>En az ayrıcalık ilkesini uygula

> Bahsedilen: Fedrampa denetimleri AC-06, NıST: AC-6, OSA uygulama #3

En az ayrıcalık ilkesi, kullanıcıların görevlerini tamamlaması için gerekenden daha fazla ayrıcalığa sahip olmaması gerektiğini belirtir. Daha fazla bilgi için, [yalnızca yeterli yönetim](/powershell/scripting/learn/remoting/jea/overview)makalesine bakın.

**Nasıl uygulanır**:

Gerekli görevleri gerçekleştirmek için yalnızca gerekli [izinleri](/sql/relational-databases/security/permissions-database-engine) atayın:

- SQL veritabanlarında:
  - Ayrıntılı izinleri ve Kullanıcı tanımlı veritabanı rollerini (veya yönetilen örnekteki sunucu rollerini) kullanın:
    1. Gerekli rolleri oluşturma
       - [ROL OLUŞTUR](/sql/t-sql/statements/create-role-transact-sql)
       - [SUNUCU ROLÜ OLUŞTUR](/sql/t-sql/statements/create-server-role-transact-sql)
    1. Gerekli kullanıcıları oluşturun
       - [KULLANıCı OLUŞTUR](/sql/t-sql/statements/create-user-transact-sql)
    1. Rollere üye olarak Kullanıcı ekleme
       - [ROLÜ DEĞIŞTIR](/sql/t-sql/statements/alter-role-transact-sql)
       - [SUNUCU ROLÜNÜ DEĞIŞTIR](/sql/t-sql/statements/alter-server-role-transact-sql)
    1. Ardından rollere izinler atayın.
       - [GRANT](/sql/t-sql/statements/grant-transact-sql)
  - Kullanıcıları gereksiz rollere atamadığınızdan emin olun.

- Azure Resource Manager:
  - Kullanılabilir veya Azure özel rolleri yerleşik roller kullanın ve gerekli izinleri atayın.
    - [Yerleşik Azure rolleri](../../role-based-access-control/built-in-roles.md)
    - [Özel Azure rolleri](../../role-based-access-control/custom-roles.md)

**En iyi uygulamalar**:

Aşağıdaki en iyi yöntemler isteğe bağlıdır, ancak güvenlik stratejinizin daha iyi yönetilebilirlik ve desteklenebilirliği ile sonuçlanır:

- Mümkünse, mümkün olan en az sayıda izin ile başlayın ve gerçek bir zorunludur (ve gerekçe) varsa izinleri bir tane ekleyerek bir tane, zıt yaklaşımdan farklı olarak bir adım adım.

- Bireysel kullanıcılara izin atamayı engellemez. Bunun yerine rolleri (veritabanı veya sunucu rolleri) sürekli olarak kullanın. Roller, raporlama ve sorun giderme izinlerinin büyük ölçüde sağlanmasına yardımcı olur. (Azure RBAC yalnızca roller aracılığıyla izin atamasını destekler.)

- Gerekli izinlere sahip özel roller oluşturup kullanın. Uygulamada kullanılan tipik roller:
  - Güvenlik dağıtımı
  - Yönetici
  - Geliştirici
  - Destek personeli
  - Denetleyici
  - Otomatikleştirilmiş süreçler
  - Son kullanıcı
  
- Yalnızca rollerin izinleri Kullanıcı için gerekli izinlere tam olarak eşleşiyorsa yerleşik rolleri kullanın. Kullanıcıları birden çok role atayabilirsiniz.

- Veritabanı Altyapısındaki izinlerin aşağıdaki kapsamlar içinde uygulanabileceğini unutmayın (kapsam daha küçük olduğunda, verilen izinlerin ne kadar küçük olduğunu):
  - Azure 'da sunucu (ana veritabanındaki özel roller)
  - Veritabanı
  - Şema
    - Bir veritabanı içinde izinler vermek için şemaları kullanmak en iyi uygulamadır. (Ayrıca bkz. [şema-tasarım: güvenlikle birlikte şema tasarımı Için öneriler](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - Nesne (tablo, görünüm, yordam, vb.)

  > [!NOTE]
  > Bu düzey, genel uygulamaya gereksiz karmaşıklık eklediğinden nesne düzeyinde izinlerin uygulanması önerilmez. Nesne düzeyi izinleri kullanmaya karar verirseniz, bunlar açıkça açıklanmalıdır. Aynı nedenlerden dolayı daha az recommendable olan sütun düzeyi izinler için de geçerlidir. Ayrıca, varsayılan olarak bir tablo düzeyinde [reddetme](/sql/t-sql/statements/deny-object-permissions-transact-sql) , sütun DÜZEYINDE bir izni geçersiz kılmaz. Bu, [ortak ölçütler uyumluluk sunucusu yapılandırmasının](/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option) etkinleştirilmesini gerektirir.

- Çok fazla izin için test etmek üzere [güvenlik açığı değerlendirmesi (VA)](/sql/relational-databases/security/sql-vulnerability-assessment) kullanarak düzenli denetimler gerçekleştirin.

### <a name="implement-separation-of-duties"></a>Görevlerin ayrılmasını uygulama

> Bahsedilen: Fedrampa: AC-04, NıST: AC-5, ISO: A. 6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

Görev ayrımı olarak da adlandırılan görevlerin ayrılması, hassas görevleri farklı kullanıcılara atanmış birden çok göreve bölme gereksinimini açıklar. Görevlerin ayrımı, veri ihlallerinin engellenmesine yardımcı olur.

**Nasıl uygulanır**:

- Gerekli görevlerin ayrılma düzeyini belirler. Örnekler:
  - Geliştirme/test ve üretim ortamları arasında
  - Güvenlik temelinde hassas görevler vs veritabanı Yöneticisi (DBA) Yönetim düzeyi görevler ve geliştirici görevleri.
    - Örnekler: Denetçi, rol düzeyi güvenlik (RLS) için güvenlik ilkesi oluşturma, DDL izinleri ile SQL veritabanı nesneleri uygulama.

- Sisteme erişen kullanıcıların (ve otomatikleştirilmiş işlemlerin) kapsamlı bir hiyerarşisini belirler.

- Gerekli Kullanıcı gruplarına göre roller oluşturun ve rollere izinler atayın.
  - Azure portal veya PowerShell aracılığıyla yönetim düzeyindeki görevler için Azure rollerini kullanın. Gereksinimle eşleşen bir yerleşik rol bulun ya da kullanılabilir izinleri kullanarak bir Azure özel rolü oluşturun
  - Yönetilen bir örnekte sunucu genelindeki görevler için sunucu rolleri oluşturun (yeni oturumlar, veritabanları oluşturma).
  - Veritabanı düzeyindeki görevler için veritabanı rolleri oluşturun.

- Belirli hassas görevler için, görevleri kullanıcılar adına yürütmek üzere bir sertifika tarafından imzalanan özel saklı yordamlar oluşturmayı düşünün. Dijital olarak imzalanan saklı yordamların önemli avantajlarından biri, yordam değiştirilirse yordamın önceki sürümüne verilen izinler hemen kaldırılır.
  - Örnek: [öğretici: saklı yordamları bir sertifikayla imzalama](/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate)

- Veri sahibi ve güvenlik sahibi arasında görevlerin ayrılmasını sağlamak için Azure Key Vault müşteri tarafından yönetilen anahtarlarla Saydam Veri Şifrelemesi (TDE) uygulayın.
  - [Azure Portal Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırma](../../storage/common/customer-managed-keys-configure-key-vault.md)makalesine bakın.

- Bir DBA 'nın yüksek oranda duyarlı kabul edilen verileri görememesini sağlamak ve yine de DBA görevlerini yapabilmeniz için rol ayrımı ile Always Encrypted kullanabilirsiniz.
  - Bkz. [Always Encrypted Için temel yönetime genel bakış](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), [rol ayrımı ile anahtar hazırlama](/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)ve [rol ayrımı ile sütun ana anahtar döndürme](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation).

- Always Encrypted kullanımı uygun değil veya en az önemli maliyetler ve ücretsiz olarak sistem tarafından kullanılamaz duruma gelmemiş olan çalışmalarda, comdavalar şu gibi telafi denetimleri kullanılarak yapılabilir ve azaltılabilir:
  - İşlemlerde insan katılımı.
  - Denetim izleri – denetim hakkında daha fazla bilgi için bkz. [kritik güvenlik olaylarını denetleme](#audit-critical-security-events).

**En iyi uygulamalar**:

- Geliştirme/test ve üretim ortamları için farklı hesapların kullanıldığından emin olun. Farklı hesaplar, test ve üretim sistemlerinin ayrılmalarına uyum sağlamaya yardımcı olur.

- Bireysel kullanıcılara izin atamayı engellemez. Bunun yerine rolleri (veritabanı veya sunucu rolleri) sürekli olarak kullanın. Rollerin olması, raporlama ve sorun giderme izinlerinin büyük ölçüde sağlanmasına yardımcı olur.

- İzinler tam olarak gereken izinlerle eşleşiyorsa yerleşik roller kullanın – birden çok yerleşik rolün tüm izinlerinin birleşimi %100 eşleştirmeye neden olursa, aynı anda birden fazla rol de atayabilirsiniz.

- Yerleşik roller çok fazla izin veya yetersiz izin vermekle, Kullanıcı tanımlı roller oluşturup kullanın.

- Rol atamaları Ayrıca, T-SQL ' d a SQL Agent Iş adımlarında veya Azure rolleri için Azure PıM ' yi kullanarak, Ayrıca, örneğin dinamik olarak (DSD), aynı zamanda bir veya daha fazla performans ayrımı olarak da

- DBAs 'nin şifreleme anahtarlarına veya anahtar depolarına erişimi olmadığından ve anahtarlara erişimi olan güvenlik yöneticilerinin sırasıyla veritabanına erişimi olmadığından emin olun. [Genişletilebilir anahtar yönetimi (ekm)](/sql/relational-databases/security/encryption/extensible-key-management-ekm) kullanımı, bu ayrımı daha kolay hale getirir. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , EKM 'yi uygulamak için kullanılabilir.

- Güvenlikle ilgili eylemler için her zaman bir denetim izlemesine sahip olduğunuzdan emin olun.

- Kullanılan izinleri görmek için Azure yerleşik rollerinin tanımını alabilir ve bunları PowerShell aracılığıyla alıntıları ve birikmelerini temel alan özel bir rol oluşturabilirsiniz.

- Db_owner veritabanı rolünün herhangi bir üyesi Saydam Veri Şifrelemesi (TDE) gibi güvenlik ayarlarını değiştirebildiğinden veya SLO 'yu değiştirebileceğinden, bu üyelikle ilgili Üyelik verilmelidir. Ancak db_owner ayrıcalıkları gerektiren çok sayıda görev vardır. DB seçeneklerini değiştirme gibi herhangi bir veritabanı ayarını değiştirme gibi görev. Denetim, herhangi bir çözümde önemli bir rol oynar.

- Db_owner izinlerini kısıtlamak mümkün değildir ve bu nedenle bir yönetim hesabının kullanıcı verilerini görüntülemesini engelleyin. Veritabanında son derece hassas veriler varsa, db_owners veya herhangi bir DBA 'nın bunu görüntülemesini güvenli bir şekilde engellemek için Always Encrypted kullanılabilir.

> [!NOTE]
> Güvenlik ile ilgili veya sorun giderme görevleri için, görevlerin (SoD) ayrımı elde etmek zor olabilir. Geliştirme ve son kullanıcı rolleri gibi diğer alanların ayırt etmek daha kolay olur. Uyumlulukla ilgili çoğu denetim, diğer çözümlerin pratik olmadığı durumlarda denetim gibi alternatif denetim işlevlerinin kullanılmasına izin verir.

SoD 'ye daha ayrıntılı bir şekilde geçmek isteyen okuyucular için aşağıdaki kaynakları öneririz:

- Azure SQL veritabanı ve SQL yönetilen örneği için:  
  - [Denetleme ve veritabanına erişim izni verme](logins-create-manage.md)
  - [Uygulama geliştiricisi için görevlerin altyapı ayrımı](/previous-versions/sql/sql-server-2008/cc974525(v=sql.100))
  - [Görevlerin ayrımı](https://www.microsoft.com/download/details.aspx?id=39269)
  - [İmza saklı yordamları](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Azure Kaynak yönetimi için:
  - [Yerleşik Azure rolleri](../../role-based-access-control/built-in-roles.md)
  - [Özel Azure rolleri](../../role-based-access-control/custom-roles.md)
  - [Yükseltilmiş erişim için Azure AD Privileged Identity Management kullanma](https://www.microsoft.com/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Düzenli kod İncelemeleri gerçekleştirin

> Bahsedilen: PCI: 6.3.2, SOC: SDL-3

Görev ayrımı bir veritabanındaki verilerle sınırlı değildir, ancak uygulama kodunu içerir. Kötü amaçlı kod güvenlik denetimlerini değiştirebilir. Üretime özel kod dağıtılmadan önce, nelerin dağıtıldığını gözden geçirmeniz önemlidir.

**Nasıl uygulanır**:

- Kaynak denetimini destekleyen Azure Data Studio gibi bir veritabanı aracı kullanın.

- Ayrılmış bir kod dağıtım işlemi uygulayın.

- Ana dala geçmeden önce, bir kişinin (kodun kendi yazarından başka biri) olası Ayrıcalıkların yükseltilmesi için kodu incelemektir ve sahte ve standart dışı erişim için kötü amaçlı veri değişiklikleri yapmanız gerekir. Bu, kaynak denetimi mekanizmaları kullanılarak yapılabilir.

**En iyi uygulamalar**:

- Standartlaştırma: herhangi bir kod güncelleştirmesi için izlenen standart bir yordam uygulamaya yardımcı olur.

- Güvenlik açığı değerlendirmesi, aşırı izin denetleyen kuralları, eski şifreleme algoritmalarının kullanımını ve bir veritabanı şeması içindeki diğer güvenlik sorunlarını kontrol eden kuralları içerir.

- Daha fazla denetim, SQL yerleştirmeye açık olan kodu tarayan Gelişmiş tehdit koruması kullanılarak QA veya test ortamında yapılabilir.

- Neye baktığın örnekleri:
  - Otomatik SQL kodu-güncelleştirme dağıtımı içinden bir Kullanıcı oluşturma veya güvenlik ayarlarını değiştirme.
  - Belirtilen parametrelere bağlı olarak, bir hücredeki parasal bir değeri, uygun olmayan bir şekilde güncelleştiren bir saklı yordam.

- Gözden geçirmeyi çalıştıran kişinin, kaynak kod yazarı dışında bir bireyin ve kod incelemelerinde ve güvenli kodlarda bilgili olduğundan emin olun.

- Tüm kod değişiklikleri kaynaklarını bilindiğinizden emin olun. Kod T-SQL betiklerine ait olabilir. Bu, çalıştırılacak veya görünümler, Işlevler, Tetikleyiciler ve saklı yordamların formlarında dağıtılması için geçici komutlar olabilir. SQL Aracısı Iş tanımlarının (adımlar) bir parçası olabilir. Ayrıca, SSIS paketleri, Azure Data Factory ve diğer hizmetler içinden de yürütülebilir.

## <a name="data-protection"></a>Veri koruma

Veri koruma, şifreleme veya gizleme ile önemli bilgilerin tehlikeye düşmesi için bir dizi özellik kümesidir.

> [!NOTE]
> Microsoft, Azure SQL veritabanı ve SQL yönetilen örneği için FIPS 140-2 düzey 1 uyumlu olarak test atmaktadır. Bu işlem, FIPS 140-2 düzey 1 kabul edilebilir algoritmaların katı kullanımını doğruladıktan sonra ve gerekli anahtar uzunlukları, anahtar yönetimi, anahtar oluşturma ve anahtar depolama ile tutarlılık dahil olmak üzere bu algoritmaların FIPS 140-2 düzey 1 tarafından onaylanmış örneklerinin doğrulanmasından sonra yapılır. Bu kanıtlama, müşterilerimizin veri işlemesinde veya sistem veya uygulama tesliminde FIPS 140-2 düzey 1 doğrulanan örneklerin kullanımı gereksinimini veya gereksinimini yanıtlamasını sağlamak için tasarlanmıştır. Yukarıdaki bildirimde "FIPS 140-2 düzey 1 uyumlu" ve "FIPS 140-2 düzey 1 uyumluluk" terimlerini, ABD ve Kanada kamu, farklı "FIPS 140-2 düzey 1" teriminin kullanımı için tasarlanan koşulları gösterecek şekilde tanımladık.

### <a name="encrypt-data-in-transit"></a>Yoldaki verileri şifreleme

> Bahsedilen: OSA uygulaması #6, ISO denetim ailesi: şifreleme

Verileriniz, istemciniz ve sunucunuz arasında taşınırken verilerinizi korur. [Ağ güvenliği](#network-security)' ne bakın.

### <a name="encrypt-data-at-rest"></a>Bekleyen verileri şifreleme

> Bahsedilen: OSA uygulaması #6, ISO denetim ailesi: şifreleme

Bekleyen şifreleme, veritabanı, günlük ve yedekleme dosyalarında kalıcı hale geldiğinde verilerin şifreleme korumasından oluşur.

**Nasıl uygulanır**:

- Azure SQL veritabanı ve SQL yönetilen örneği 2017 sonrasında oluşturulan tüm veritabanları için hizmet tarafından yönetilen anahtarlarla [Saydam veritabanı şifrelemesi (TDE)](transparent-data-encryption-tde-overview.md) varsayılan olarak etkindir.
- Yönetilen bir örnekte, veritabanı şirket içi sunucu kullanan bir geri yükleme işleminden oluşturulduysa, özgün veritabanının TDE ayarı uygulanır. Özgün veritabanında TDE etkin değilse, yönetilen örnek için TDE 'nın el ile açık olmasını öneririz.

**En iyi uygulamalar**:

- Ana veritabanında bekleyen şifreleme gerektiren verileri depolamayın. Ana veritabanı TDE ile şifrelenemez.

- TDE koruması üzerinde daha fazla saydamlığa ve ayrıntılı denetime ihtiyacınız varsa Azure Key Vault müşteri tarafından yönetilen anahtarları kullanın. Azure Key Vault, veritabanını erişilemez hale getirecek herhangi bir zamanda izinleri iptal etme yeteneğinin yapılmasına izin verir. Diğer anahtarlarla birlikte TDE koruyucuları merkezi olarak yönetebilir veya Azure Key Vault kullanarak TDE koruyucuyu kendi zamanlamanızda döndürebilirsiniz.

- Azure Key Vault ' de müşteri tarafından yönetilen anahtarlar kullanıyorsanız, makaleyi, [TDE ile Azure Key Vault yapılandırma yönergelerini](transparent-data-encryption-byok-overview.md#recommendations-when-configuring-akv) ve [Azure Key Vault Ile coğrafi-Dr](transparent-data-encryption-byok-overview.md#geo-dr-and-customer-managed-tde)'yi yapılandırmayı izleyin.

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Yüksek ayrıcalıklı, yetkisiz kullanıcılardan kullanımda olan hassas verileri koruyun

Kullanılan veriler, SQL sorgularının yürütülmesi sırasında veritabanı sisteminde bellekte depolanan veriler. Veritabanınız hassas verileri depoluyorsa, kuruluşunuz, yüksek ayrıcalıklı kullanıcıların veritabanınızdaki hassas verileri görüntülemesi önlendiğinden emin olmak için gerekli olabilir. Kuruluşunuzdaki Microsoft işleçleri veya DBAs gibi yüksek ayrıcalıklı kullanıcıların veritabanını yönetebilmesi, ancak büyük olasılıkla SQL sürecinin belleğinden veya veritabanını sorgulayarak, önemli verileri görüntülemesini engellemiş olması gerekir.

Hangi verilerin gizli olduğunu ve gizli verilerin bellekte şifrelenmesi gerekip gerekmediğini ve düz metin olarak yöneticilerin erişimine açık olup olmadığını belirleyen ilkeler, kuruluşunuza ve uyumlu olması için gereken uyumluluk düzenlemelerine özgüdür. Lütfen ilgili gereksinime bakın: [hassas verileri tanımlamak ve etiketleyerek](#identify-and-tag-sensitive-data).

**Nasıl uygulanır**:

- Hassas verilerin, bellekte/kullanımda olsa bile Azure SQL veritabanı veya SQL yönetilen örneği 'nde düz metin olarak gösterilmemesini sağlamak için [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) kullanın. Always Encrypted, veritabanı yöneticilerinin (DBAs) ve bulut yöneticilerinin (ya da yüksek ayrıcalıklı ancak yetkisiz kullanıcıların kimliğine bürünebilen kötü aktörlerin) verilerini korur ve verilerinize kimlerin erişebileceği hakkında daha fazla denetim sağlar.

**En iyi uygulamalar**:

- Always Encrypted, verileri bekleyen (TDE) veya aktarım (SSL/TLS) ile şifrelemek için bir alternatif değildir. Always Encrypted, performans ve işlevsellik etkilerini en aza indirmek için hassas olmayan veriler için kullanılmamalıdır. Verilerin Rest, aktarım sırasında ve kullanımda olan kapsamlı korunması için TDE ve Aktarım Katmanı Güvenliği (TLS) ile birlikte Always Encrypted kullanılması önerilir.

- Bir üretim veritabanında Always Encrypted dağıtmadan önce, tanımlanan hassas veri sütunlarını şifreleme etkisini değerlendirin. Genel olarak, Always Encrypted şifreli sütunlardaki sorguların işlevlerini azaltır ve [Always Encrypted Özellik ayrıntılarında](/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details)listelenen başka sınırlamalar vardır. Bu nedenle, işlevselliği yeniden uygulamak için uygulamanızı yeniden tetikleyebilmeniz gerekebilir, bir sorgu, istemci tarafında veya//ve saklı yordamların, işlevlerin, görünümlerin ve tetikleyicilerin tanımları dahil olmak üzere veritabanı şemanızı yeniden düzenleyebilir. Mevcut uygulamalar, Always Encrypted kısıtlamalarına ve kısıtlamalarına bağlı olmadıkları takdirde şifrelenmiş sütunlarla çalışmayabilir. Microsoft araçları, ürün ve Hizmetleri Always Encrypted destekleyen ekosistemi, şifrelenmiş sütunlarla birlikte çalışmaz. Bir sütunu şifrelemek, iş yükünüzün özelliklerine bağlı olarak sorgu performansını da etkileyebilir.

- Kötü amaçlı DBAs verilerini korumak için Always Encrypted kullanıyorsanız rol ayrımı ile Always Encrypted anahtarlarını yönetin. Rol ayrımı ile bir güvenlik yöneticisi fiziksel anahtarları oluşturur. DBA, veritabanında fiziksel anahtarları açıklayan sütun ana anahtarını ve sütun şifreleme anahtarı meta veri nesnelerini oluşturur. Bu işlem sırasında güvenlik yöneticisinin veritabanına erişmesi gerekmez ve DBA 'nın fiziksel anahtarlara düz metin olarak erişmesi gerekmez.
  - Ayrıntılar için, [rol ayrımı Ile anahtarları yönetme](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) makalesine bakın.

- Sütun ana anahtarlarınızı yönetim kolaylığı için Azure Key Vault depolayın. Anahtar yönetimini sabit hale getirmek için Windows sertifika deposu 'nı (ve genel, dağıtılmış anahtar deposu çözümlerini, diğer merkezi anahtar yönetim çözümleri olarak) kullanmaktan kaçının.

- Birden çok anahtar (sütun ana anahtar veya sütun şifreleme anahtarı) kullanmanın avantajları hakkında dikkatle düşünün. Anahtar yönetim maliyetini azaltmak için anahtar sayısını küçük tutun. Tek sütunlu ana anahtar ve veritabanı başına bir sütun şifreleme anahtarı genellikle sabit durum ortamlarında (anahtar dönüşünün ortasında değil) yeterlidir. Farklı anahtarlar kullanarak ve farklı verilere erişirken, farklı Kullanıcı gruplarınız varsa ek anahtarlar gerekebilir.  

- Uyumluluk gereksinimlerinize göre sütun ana anahtarlarını döndürün. Ayrıca, sütun şifreleme anahtarlarını döndürmenize gerek duyuyorsanız, uygulama kapalı kalma süresini en aza indirmek için çevrimiçi şifrelemeyi kullanmayı düşünün.
  - Bkz. [performans ve kullanılabilirlik konuları](/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations).

- Veriler üzerinde hesaplamaların (eşitlik) desteklenme ihtiyacı varsa belirleyici şifrelemeyi kullanın. Aksi takdirde, rastgele şifreleme kullanın. Düşük entropi veri kümeleri veya genel olarak bilinen dağıtıma sahip veri kümeleri için belirleyici şifreleme kullanmaktan kaçının.

- İzniniz olmadan verilerinize uygun olmayan üçüncü taraflardan endişe ediyorsanız, düz metin içindeki anahtarlara ve verilere erişimi olan tüm uygulama ve araçların Microsoft Azure bulut dışında çalıştırıldığına emin olun. Anahtarlara erişim olmadan, üçüncü tarafın şifrelemeyi atlayamadığı müddetçe verilerin şifresini çözme yöntemi olmayacaktır.

- Always Encrypted, anahtarlara (ve korunan verilere) geçici erişim vermeyi kolay bir şekilde desteklemez. Örneğin, DBA 'nın hassas ve şifrelenmiş veriler üzerinde bazı temizleme işlemleri yapmasına izin vermek için anahtarları bir DBA ile paylaşmanız gerekiyorsa. Güvenilirlikten gelen verilere erişimi iptal etmenin tek yolu, hem sütun şifreleme anahtarlarını hem de verileri koruyan, pahalı bir işlem olan sütun ana anahtarlarını döndürmek olacaktır.

- Şifrelenmiş sütunlardaki düz metin değerlerine erişmek için, kullanıcının CMK 'yı tutan anahtar deposunda yapılandırılan sütunları koruyan sütun ana anahtarına (CMK) erişimi olması gerekir. Kullanıcının aynı zamanda **herhangi BIR sütun ana anahtar tanımını görüntülemesini** ve **HERHANGI BIR sütun şifreleme anahtarı tanımı** veritabanı izinlerini görüntülemesini de gerekir.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Uygulama kullanıcılarına şifreleme aracılığıyla hassas verilere erişimi denetleme

Şifreleme, yalnızca şifreleme anahtarlarına erişimi olan belirli uygulama kullanıcılarının verileri görüntüleyebilen veya güncelleştirebilen bir yöntem olarak kullanılabilir.

**Nasıl uygulanır**:

- Hücre düzeyinde şifrelemeyi (CLE) kullanın. Ayrıntılar için [bir veri sütununu şifreleyin](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) başlıklı makaleye bakın.
- Always Encrypted kullanın, ancak kısıtlamasından haberdar olun. Sınırlamalar aşağıda listelenmiştir.

**En iyi uygulamalar**

CLE kullanırken:

- SQL izinleri ve rolleri aracılığıyla anahtarlara erişimi denetleyin.

- Veri şifreleme için AES (AES 256 önerilir) kullanın. Bu tür RC4, DES ve TripleDES algoritmaları kullanım dışıdır ve bilinen güvenlik açıkları nedeniyle kullanılmamalıdır.

- 3DES kullanmaktan kaçınmak için simetrik anahtarları asimetrik anahtarlar/sertifikalarla koruyun (parolalar).

- Dışarı aktarma/içeri aktarma (bacpac dosyaları) aracılığıyla Cell-Level şifrelemeyi kullanarak bir veritabanını geçirirken dikkatli olun.
  - Verileri geçirirken anahtar kaybını önleme ve diğer en iyi Yöntem Kılavuzu için [Azure SQL veritabanı 'Nda hücre düzeyinde şifrelemeyi kullanma önerileri](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database) başlıklı makaleye bakın.

Always Encrypted birincil olarak Azure SQL veritabanı 'nın (bulut işleçleri, DBAs) yüksek ayrıcalıklı kullanıcılarından kullanılan hassas verileri korumak için tasarlandığını unutmayın; [yüksek ayrıcalıklı, yetkisiz kullanıcılardan kullanılan hassas verileri koruyun](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Uygulama kullanıcılarından verileri korumak için Always Encrypted kullanırken aşağıdaki güçlükleri göz önünde bulundurun:

- Varsayılan olarak, Always Encrypted destekleyen tüm Microsoft istemci sürücüleri, sütun şifreleme anahtarlarının genel (uygulama başına bir) önbelleğini korur. Bir istemci sürücüsü bir sütun ana anahtarı tutan anahtar deposuyla iletişim kurarak bir düz metin sütun şifreleme anahtarı aldıktan sonra, düz metin sütunu şifreleme anahtarı önbelleğe alınır. Bu, çok kullanıcılı bir uygulamanın kullanıcılarından veri yalıtma yapmayı zorlaştırır. Uygulamanız, bir anahtar deposuyla (örneğin, Azure Key Vault) etkileşim kurarken son kullanıcıları taklit ettiğinde, bir kullanıcının sorgusu önbelleği bir sütun şifreleme anahtarı ile doldurduktan sonra, aynı anahtarı gerektiren, ancak başka bir kullanıcı tarafından tetiklenen bir sonraki sorgu önbelleğe alınmış anahtarı kullanır. Sürücü, anahtar deposunu çağırmayacaktır ve ikinci kullanıcının sütun şifreleme anahtarına erişim izni olup olmadığını denetlemez. Sonuç olarak, Kullanıcı anahtarlara erişimi olmasa bile şifrelenmiş verileri görebilir. Çok kullanıcılı bir uygulamadaki kullanıcıların yalıtımına ulaşmak için, sütun şifreleme anahtarı önbelleğe almayı devre dışı bırakabilirsiniz. Sürücünün her bir veri şifreleme veya şifre çözme işlemi için anahtar deposuyla iletişim kurabilmesi gerekeceğinden, önbelleğe almanın devre dışı bırakılması ek performans sorunlarına neden olur.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Veri biçimini korurken, uygulama kullanıcılarına yönelik verileri yetkisiz görüntülemeye karşı koruma

Yetkisiz kullanıcıların verileri görüntülemesini engellemeye yönelik başka bir teknik ise, kullanıcı uygulamalarının işleme devam edip verileri görüntülemesini sağlamak için veri türlerini ve biçimlerini korurken verileri belirsizleyebilir veya maskeleyebilir.

**Nasıl uygulanır**:

- Tablo sütunlarını belirsizetmek için [dinamik veri maskeleme](/sql/relational-databases/security/dynamic-data-masking) kullanın.

> [!NOTE]
> Always Encrypted, dinamik veri maskeleme ile çalışmaz. Aynı sütunu şifrelemek ve maskelemek mümkün değildir; bu, kullanımdaki verilerin korunmasını, dinamik veri maskeleme aracılığıyla uygulama kullanıcılarınız için verileri maskeleme açısından önceliklendirmenize gerek kalmaz.

**En iyi uygulamalar**:

> [!NOTE]
> Dinamik veri maskeleme, yüksek ayrıcalıklı kullanıcılardan verileri korumak için kullanılamaz. Maskeleme ilkeleri, db_owner gibi yönetici erişimi olan kullanıcılara uygulanmaz.

- Uygulama kullanıcılarının geçici sorgular çalıştırmasına izin verme (dinamik veri maskeleme 'nin çevresinde çalışabilen şekilde).  
  - Ayrıntılar için [çıkarım veya deneme yanılma tekniklerini kullanarak maskelemeyi atlayarak](/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) makaleye bakın.  

- Maskelenmiş sütunlarda güncelleştirme yapmak için Kullanıcı izinlerini sınırlamak üzere uygun bir erişim denetim ilkesi (SQL izinleri, roller, RLS aracılığıyla) kullanın. Bir sütunda maske oluşturma, bu sütun için güncelleştirmeleri engellemez. Maskelenmiş sütunu sorgularken maskelenmiş verileri alan kullanıcılar, yazma izinlerine sahip olmaları durumunda verileri güncelleştirebilir.

- Dinamik veri maskeleme, maskelenmiş değerlerin istatistiksel özelliklerini korumaz. Bu sorgu sonuçlarını etkileyebilir (örneğin, filtreleme koşullarını içeren sorgular veya maskelenmiş verilerdeki birleşimler).

## <a name="network-security"></a>Ağ güvenliği

Ağ güvenliği, Azure SQL veritabanı 'na aktarma sırasında verilerinizi güvenli hale getirmek için erişim denetimlerine ve en iyi yöntemlere başvurur.

### <a name="configure-my-client-to-connect-securely-to-sql-databasesql-managed-instance"></a>İstemcimi SQL veritabanı/SQL yönetilen örneği 'ne güvenli bir şekilde bağlanacak şekilde yapılandırma

Azure SQL veritabanı ve SQL yönetilen örneği ile bağlantı kurarak, iyi bilinen güvenlik açıklarına sahip istemci makinelerin ve uygulamaların (örneğin, eski TLS protokollerini ve şifre paketlerini kullanma) önlenmesi için en iyi yöntemler.

**Nasıl uygulanır**:

- Azure SQL veritabanı ve SQL yönetilen örneği 'ne bağlanan istemci makinelerin  [Aktarım Katmanı Güvenliği (TLS)](security-overview.md#transport-layer-security-encryption-in-transit)kullandığından emin olun.

**En iyi uygulamalar**:

- Şifreleme etkinken SQL veritabanına bağlanmak için tüm uygulamalarınızı ve araçlarınızı yapılandırın
  - Encrypt = on, TrustServerCertificate = off (veya Microsoft olmayan sürücülerle eşdeğerdir).

- Uygulamanız TLS desteklemeyen bir sürücü kullanıyorsa veya daha eski bir TLS sürümünü destekliyorsa, mümkünse sürücüyü değiştirin. Mümkün değilse, güvenlik risklerini dikkatle değerlendirin.

- SSL 2,0, SSL 3,0, TLS 1,0 ve TLS 1,1 güvenlik açıkları aracılığıyla saldırı vektörlerini, [Aktarım Katmanı Güvenliği (TLS) kayıt defteri ayarları](/windows-server/security/tls/tls-registry-settings#tls-10)BAŞıNA Azure SQL veritabanı 'na bağlanan istemci makinelerde devre dışı bırakarak azaltabilirsiniz.

- İstemci üzerindeki şifreleme paketlerini denetleyin: [TLS/SSL (Schannel SSP) Içindeki şifre paketleri](/windows/desktop/SecAuthN/cipher-suites-in-schannel). Özellikle, [TLS şifre paketi sırasını yapılandırma](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)başına 3DES 'i devre dışı bırakın.

- Azure SQL veritabanı ve SQL yönetilen örneği için şifreleme hem proxy hem de yeniden yönlendirme bağlantı türleri için zorlanır. Azure SQL yönetilen örneği için, sunucu tarafında şifrelemeyi zorladığı için **proxy** bağlantı türünü (varsayılan) kullanın. **Yeniden yönlendirme** bağlantı türü şu anda şifreleme Zorlamayı desteklemiyor ve yalnızca özel IP bağlantılarında kullanılabilir.

- Daha fazla bilgi için bkz. [Azure SQL veritabanı bağlantı mimarisi-bağlantı ilkesi](connectivity-architecture.md#connection-policy).

### <a name="minimize-attack-surface"></a>Saldırı yüzeyini en aza indirme

Kötü amaçlı bir kullanıcının saldırıya uğramakta olabilecek Özellik sayısını en aza indirin. Azure SQL veritabanı için ağ erişim denetimleri uygulayın.

> Bahsedilen: OSA uygulaması #5

**Nasıl uygulanır**:

SQL veritabanında:

- Sunucu düzeyinde Azure hizmetlerine erişime Izin ver ' i ayarla
- VNet hizmet uç noktaları ve VNet güvenlik duvarı kurallarını kullanın.
- Özel bağlantı (Önizleme) kullanın.

SQL yönetilen örneği 'nde:

- [Ağ gereksinimleri](../managed-instance/connectivity-architecture-overview.md#network-requirements)bölümündeki yönergeleri izleyin.

**En iyi uygulamalar**:

- Özel bir uç noktaya bağlanarak (örneğin, özel bir veri yolu kullanarak) Azure SQL veritabanı ve SQL yönetilen örneği erişimini kısıtlama:
  - Yönetilen bir örnek, dış erişimi engellemek için bir sanal ağ içinde yalıtılabilir. Aynı bölgedeki aynı veya eşlenmiş sanal ağdaki uygulamalar ve araçlar doğrudan erişebilir. Farklı bölgedeki uygulamalar ve araçlar, bağlantı kurmak için sanal-ağdan sanal ağ bağlantısı veya ExpressRoute devre eşlemesini kullanabilir. Müşteri, 1433 numaralı bağlantı noktası üzerinden erişimi yalnızca yönetilen bir örneğe erişim gerektiren kaynaklarla kısıtlamak için ağ güvenlik grupları (NSG) kullanmalıdır.
  - Bir SQL veritabanı için, sanal ağınız içindeki sunucu için adanmış bir özel IP sağlayan [özel bağlantı](../../private-link/private-endpoint-overview.md) özelliğini kullanın. Sunucularınızla erişimi kısıtlamak için sanal ağ [güvenlik duvarı kurallarıyla sanal ağ hizmet uç noktalarını](vnet-service-endpoint-rule-overview.md) da kullanabilirsiniz.
  - Mobil kullanıcıların, veri yolu üzerinden bağlanmak için Noktadan siteye VPN bağlantıları kullanması gerekir.
  - Şirket içi ağına bağlanan kullanıcıların, veri yolu üzerinden bağlanmak için siteden siteye VPN bağlantısı veya ExpressRoute kullanması gerekir.

- Azure SQL veritabanı ve SQL yönetilen örneğine, genel bir uç noktaya bağlanarak erişebilirsiniz (örneğin, ortak bir veri yolu kullanarak). Aşağıdaki en iyi yöntemler göz önünde bulundurulmalıdır:
  - SQL veritabanındaki bir sunucu için, erişimi yalnızca yetkili IP adresleriyle kısıtlamak üzere [IP güvenlik duvarı kurallarını](firewall-configure.md) kullanın.
  - SQL yönetilen örneği için ağ güvenlik grupları (NSG) kullanarak 3342 numaralı bağlantı noktasına yalnızca gerekli kaynaklarla erişimi kısıtlayın. Daha fazla bilgi için bkz. [genel uç noktalarla güvenli bir şekilde yönetilen örnek kullanma](../managed-instance/public-endpoint-overview.md).

> [!NOTE]
> SQL yönetilen örnek genel uç noktası varsayılan olarak etkinleştirilmemiştir ve açıkça etkinleştirilmelidir. Şirket ilkesi genel uç noktaların kullanılmasına izin vermez, ilk yerde genel bitiş noktalarının etkinleştirilmesini engellemek için [Azure ilkesi](../../governance/policy/overview.md) 'ni kullanın.

- Azure ağ bileşenlerini ayarlama:
  - [Ağ güvenliği için En Iyi Azure uygulamalarını](../../security/fundamentals/network-best-practices.md)izleyin.
  - [Azure sanal ağ hakkında sık sorulan sorular (SSS)](../../virtual-network/virtual-networks-faq.md) ve plan 'da özetlenen en iyi uygulamalar başına sanal ağ yapılandırması planlayın.
  - Bir sanal ağı birden çok alt ağa bölüm ve benzer rolün kaynaklarını aynı alt ağa (örneğin, ön uç ile arka uç kaynakları) atayın.
  - Azure sanal ağ sınırının içindeki alt ağlar arasındaki trafiği denetlemek için [ağ güvenlik grupları (NSG 'ler)](../../virtual-network/network-security-groups-overview.md) kullanın.
  - Gelen ve giden ağ trafiğini izlemek üzere aboneliğiniz için [Azure Ağ İzleyicisi](../../network-watcher/network-watcher-monitoring-overview.md) 'ni etkinleştirin.

### <a name="configure-power-bi-for-secure-connections-to-sql-databasesql-managed-instance"></a>SQL veritabanı/SQL yönetilen örneği için güvenli bağlantılar için Power BI yapılandırma

**En iyi uygulamalar**:

- Power BI Desktop için, mümkün olan her durumda özel veri yolunu kullanın.

- İstemci makinesindeki kayıt defteri anahtarını [Aktarım Katmanı Güvenliği (TLS)](/windows-server/security/tls/tls-registry-settings) kayıt defteri ayarları başına AYARLAYARAK Power BI Desktop TLS 1.2 kullanarak bağlantı aldığından emin olun.

- [Power BI Ile satır düzeyi güvenlik (RLS)](/power-bi/service-admin-rls)aracılığıyla belirli kullanıcılar için veri erişimini kısıtlayın.

- Power BI hizmeti için şirket [içi veri ağ geçidini](/power-bi/service-gateway-onprem)kullanarak göz önünde bulundurun [ve önemli noktalara dikkat](/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway)edin.

### <a name="configure-app-service-for-secure-connections-to-sql-databasesql-managed-instance"></a>SQL veritabanı/SQL yönetilen örneği için güvenli bağlantılar için App Service yapılandırma

**En iyi uygulamalar**:

- Basit bir Web uygulaması için genel uç nokta üzerinden bağlantı kurmak için **Azure HIZMETLERININ** açık olması gerekir.

- Yönetilen bir örneğe özel veri yolu bağlantısı için [uygulamanızı bir Azure sanal ağıyla tümleştirin](../../app-service/web-sites-integrate-with-vnet.md) . İsteğe bağlı olarak, [App Service ortamları (Ao)](../../app-service/environment/intro.md)Ile bir Web uygulaması da dağıtabilirsiniz.

- Aya veya sanal ağ ile tümleşik Web uygulamasına sahip Web uygulaması için SQL veritabanı 'ndaki bir veritabanına bağlanırken, [sanal ağ hizmet uç noktalarını ve sanal ağ güvenlik duvarı kurallarını](vnet-service-endpoint-rule-overview.md) kullanarak belirli bir sanal ağ ve alt ağdan erişimi sınırlayabilirsiniz. Daha sonra **Azure HIZMETLERININ kapalı çalışmasına Izin ver** seçeneğini belirleyin. Ayrıca, ASE 'yi bir özel veri yolu üzerinden SQL yönetilen örneğindeki yönetilen bir örneğe de bağlayabilirsiniz.  

- Web uygulamanızın, [bir hizmet olarak platform (PaaS) Web ve mobil uygulamaları Azure App Service kullanarak güvenli hale getirmek Için en iyi yöntemler](../../security/fundamentals/paas-applications-using-app-services.md)makalesine göre yapılandırıldığından emin olun.

- Web uygulamanızı ortak güvenlik açıklarından ve güvenlik açıklarından korumak için [Web uygulaması güvenlik duvarını (WAF)](../../web-application-firewall/ag/ag-overview.md) yükler.

### <a name="configure-azure-virtual-machine-hosting-for-secure-connections-to-sql-databasesql-managed-instance"></a>SQL veritabanı/SQL yönetilen örneği için güvenli bağlantılar için Azure sanal makine barındırma yapılandırma

**En iyi uygulamalar**:

- VM 'den hangi bölgelere erişilebileceğini denetlemek için Azure sanal makinelerinde NSG 'ler üzerinde Izin verme ve reddetme kurallarının bir birleşimini kullanın.

- [Azure 'Da IaaS iş yükleri için En Iyi güvenlik uygulamaları](../../security/fundamentals/iaas.md)olan her makale için sanal makinenizin yapılandırıldığından emin olun.

- Tüm VM 'Lerin belirli bir sanal ağ ve alt ağ ile ilişkilendirildiğinden emin olun.

- [Zorlamalı tünel oluşturma hakkında bilgi](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)için 0.0.0.0/Internet varsayılan yoluna ihtiyacınız olup olmadığını değerlendirin.
  - Evet ise (örneğin, ön uç alt ağı) varsayılan yolu koruyun.
  - Hayır – Örneğin, orta katman veya arka uç alt ağı – sonra, şirket içi (a. k. bir şirket içi) ulaşmak için Internet üzerinden hiçbir trafik kalmayacak şekilde Zorlamalı tünel özelliğini etkinleştirin.

- Eşleme kullanıyorsanız veya şirket içi sunucuya bağlanıyorsanız [isteğe bağlı varsayılan yollar](../../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) uygulayın.

- Paket incelemesi için sanal ağdaki tüm trafiği bir ağ sanal gerecine göndermeniz gerekiyorsa [Kullanıcı tanımlı yollar](../../virtual-network/virtual-networks-udr-overview.md#user-defined) uygulayın.

- Azure omurga ağı aracılığıyla Azure depolama gibi PaaS hizmetlerine güvenli erişim için [sanal ağ hizmet uç noktalarını](vnet-service-endpoint-rule-overview.md) kullanın.

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Dağıtılmış hizmet reddi (DDoS) saldırılarına karşı koruma

Dağıtılmış hizmet reddi (DDoS) saldırıları, kötü niyetli bir kullanıcının Azure SQL veritabanı 'na, Azure altyapısını yoğun hale getirmeye ve geçerli oturum açmaları ve iş yükünü reddetmesine neden olan bir ağ trafiği yükünü bir şekilde göndermesinin denemeleridir.

> Bahsedilen: OSA uygulaması #9

**Nasıl uygulanır**:

DDoS koruması, Azure platformunun bir parçası olarak otomatik olarak etkinleştirilir. Ortak uç noktalarda her zaman açık trafik izleme ve ağ düzeyinde saldırıları gerçek zamanlı olarak hafifletme bilgilerini içerir.

- Sanal ağlarda dağıtılan kaynaklarla ilişkili genel IP adreslerini izlemek için [Azure DDoS koruması](../../ddos-protection/ddos-protection-overview.md) 'nı kullanın.

- Veritabanlarına karşı hizmet reddi (DoS) saldırılarını algılamak için [Azure SQL veritabanı Için Gelişmiş tehdit koruması](threat-detection-overview.md) 'nı kullanın.

**En iyi uygulamalar**:

- [Saldırı yüzeyini en aza indirmek](#minimize-attack-surface) , DDoS saldırı tehditlerini en aza indirmenize yardımcı olur.

- Gelişmiş tehdit koruması **deneme YANıLMA SQL kimlik bilgilerini zorla** uyarısı, deneme yanılma saldırılarını algılamaya yardımcı olur. Bazı durumlarda, uyarı, sızma testi iş yüklerini de ayırt edebilir.

- SQL veritabanı 'na bağlanan Azure VM barındırma uygulamaları için:
  - Azure Güvenlik Merkezi 'nde Internet 'e yönelik uç noktalar aracılığıyla erişimi kısıtlamak için öneriyi izleyin.
  - Azure VM 'lerinde uygulamanızın birden çok örneğini çalıştırmak için sanal makine ölçek kümelerini kullanın.
  - Deneme yanılma saldırısı oluşmasını engellemek için Internet 'ten RDP ve SSH 'yi devre dışı bırakın.

## <a name="monitoring-logging-and-auditing"></a>İzleme, günlüğe kaydetme ve denetim

Bu bölüm, veritabanları için olağan dışı ve zararlı olabilecek girişimleri belirten anormal etkinlikleri algılamanıza yardımcı olan özellikleri ifade eder. Ayrıca veritabanı olaylarını izlemek ve yakalamak üzere veritabanı denetimini yapılandırmak için en iyi yöntemleri açıklar.

### <a name="protect-databases-against-attacks"></a>Veritabanlarını saldırılara karşı koruma

Gelişmiş tehdit koruması, anormal etkinliklerde güvenlik uyarıları sunarak meydana gelebilecek olası tehditleri algılamanıza ve bunlara yanıt vermenize olanak tanır.

**Nasıl uygulanır**:

- [SQL Için Gelişmiş tehdit koruması](threat-detection-overview.md#alerts) 'nı kullanarak veritabanlarına erişme veya bu veritabanlarına yararlanmaya yönelik olağan dışı ve olası zararlı girişimleri tespit etme:
  - SQL ekleme saldırısı.
  - Kimlik bilgileri hırsızlığı/sızıntısı.
  - Ayrıcalık kötüye kullanımı.
  - Veri kaybı.

**En iyi uygulamalar**:

- Belirli bir sunucu veya yönetilen bir örnek için [SQL Için Azure Defender 'ı](azure-defender-for-sql.md)yapılandırın   . Ayrıca, [Azure Güvenlik Merkezi Standart katmanına](../../security-center/security-center-pricing.md)geçerek bir abonelikteki tüm sunucular ve yönetilen örnekler Için Azure Defender 'ı yapılandırabilirsiniz.

- Tam araştırma deneyimi için, [SQL veritabanı denetimini](../../azure-sql/database/auditing-overview.md)etkinleştirmeniz önerilir. Denetimle, veritabanı olaylarını izleyebilir ve bunları bir Azure depolama hesabındaki veya Azure Log Analytics çalışma alanındaki bir denetim günlüğüne yazabilirsiniz.

### <a name="audit-critical-security-events"></a>Kritik güvenlik olaylarını denetleme

Veritabanı olaylarının izlenmesi, veritabanı etkinliğini anlamanıza yardımcı olur. İş kaygılarını veya şüpheli güvenlik ihlallerini gösterebilen tutarsızlıklar ve bozukluklar hakkında öngörüler elde edebilirsiniz. Ayrıca uyumluluk standartlarını sağlar ve kolaylaştırır.

**Nasıl uygulanır**:

- Veritabanı olaylarını izlemek ve bunları Azure Depolama hesabınızdaki bir denetim günlüğüne yazmak, Log Analytics çalışma alanı (Önizleme) veya Event Hubs (Önizleme) için [SQL veritabanı denetimi](../../azure-sql/database/auditing-overview.md) 'Ni veya [yönetilen örnek denetimini](../managed-instance/auditing-configure.md) etkinleştirin.

- Denetim günlükleri bir Azure depolama hesabına, Azure Izleyici günlükleri tarafından tüketim için bir Log Analytics çalışma alanına veya Olay Hub 'ı kullanılarak tüketim için Olay Hub 'ına yazılabilir. Bu seçeneklerin herhangi bir birleşimini yapılandırabilirsiniz ve denetim günlükleri her birine yazılır.

**En iyi uygulamalar**:

- Sunucunuz üzerinde [SQL veritabanı denetimi](../../azure-sql/database/auditing-overview.md) veya olayları denetlemek Için [yönetilen örnek denetimi](../managed-instance/auditing-configure.md) yapılandırarak, bu sunucudaki tüm mevcut ve yeni oluşturulan veritabanları denetlenir.
- Varsayılan olarak denetim ilkesi, veritabanlarına karşı tüm eylemleri (sorgular, saklı yordamlar ve başarılı ve başarısız oturumlar) içerir ve bu da yüksek düzeyde denetim günlüklerine neden olabilir. Müşterilerin [PowerShell kullanarak farklı türlerde eylemler ve eylem grupları için denetimi yapılandırması](./auditing-overview.md#manage-auditing)önerilir. Bunu yapılandırmak, denetlenen eylemlerin sayısını denetlemeye yardımcı olur ve olay kaybı riskini en aza indirir. Özel denetim yapılandırması, müşterilerin yalnızca gerekli denetim verilerini yakalamasına olanak tanır.
- Denetim günlükleri doğrudan [Azure Portal](https://portal.azure.com/)veya yapılandırılan depolama konumundan tüketilebilir.

> [!NOTE]
> Log Analytics için denetimin etkinleştirilmesi, alma ücretlerine göre maliyet doğurur. Lütfen bu [seçeneği](https://azure.microsoft.com/pricing/details/monitor/)kullanarak ilişkili maliyetten haberdar olun veya denetim günlüklerini bir Azure depolama hesabında depolamayı düşünün.

**Daha fazla kaynak**:

- [SQL veritabanı denetimi](../../azure-sql/database/auditing-overview.md)
- [SQL Server denetimi](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="secure-audit-logs"></a>Güvenli denetim günlükleri

Depolama hesabına erişimi, görevlerin ayrılmasını ve DBA 'nın denetçilerin ayrı ayrı sayısını destekleyecek şekilde kısıtlayın.

**Nasıl uygulanır**:

- Denetim günlüklerini Azure depolama 'ya kaydederken, depolama hesabına erişimin en düşük güvenlik ilkelerine sınırlı olduğundan emin olun. Depolama hesabına kimlerin erişimi olduğunu denetleyin.
- Daha fazla bilgi için bkz. [Azure depolama 'ya erişimi yetkilendirme](../../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**En iyi uygulamalar**:

- Denetim hedefine erişimi denetlemek, DBA 'daki denetçilerin ayrıldıkları bir temel kavramdır.

- Gizli verilere erişimi denetlerken, denetçi ile ilgili bilgi sızıntısını önlemek için veri şifreleme ile verilerin güvenliğini sağlamayı göz önünde bulundurun. Daha fazla bilgi için, [yüksek ayrıcalıklı, yetkisiz kullanıcılardan kullanılan hassas verileri koruma](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)bölümüne bakın.

## <a name="security-management"></a>Güvenlik yönetimi

Bu bölümde, veritabanlarının güvenlik duruşunuzu yönetmek için farklı yönleri ve en iyi uygulamalar açıklanmaktadır. Veritabanlarınızı, veritabanlarınızdaki potansiyel olarak duyarlı verilere yönelik verileri bulmak ve sınıflandırmak için güvenlik standartlarını karşılayacak şekilde yapılandırmak için en iyi yöntemleri içerir.

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Veritabanlarının en iyi güvenlik uygulamalarını karşılayacak şekilde yapılandırıldığından emin olun

Olası veritabanı güvenlik açıklarını bulup çözerek veritabanı güvenlerinizi önceden geliştirebilirsiniz.

**Nasıl uygulanır**:

- [SQL güvenlik açığı değerlendirmesini](/sql/relational-databases/security/sql-vulnerability-assessment) (VA), veritabanınızı güvenlik sorunları açısından taramak ve veritabanlarınızdaki düzenli aralıklarla otomatik olarak çalıştırmak için etkinleştirin.

**En iyi uygulamalar**:

- Başlangıçta, veritabanlarınızda VA çalıştırıp, en iyi güvenlik uygulamalarını opkaldıran başarısız denetimleri düzeltme yoluyla yineleyebilirsiniz. Tarama _temiz_ olana veya tüm denetimler geçtiğinden, kabul edilebilir yapılandırmalara yönelik taban çizgileri ayarlayın.  

- Düzenli aralıklarla yinelenen taramaların haftada bir kez çalışacak şekilde yapılandırın ve ilgili kişiyi Özet e-postaları alacak şekilde yapılandırın.

- Her haftalık taramanın ardından VA özetini gözden geçirin. Bulunan herhangi bir güvenlik açığı için, önceki tarama sonucundan DRFT 'yi değerlendirin ve denetim 'in çözülmesi gerekip gerekmediğini saptayın. Yapılandırmada değişiklik için meşru bir neden olup olmadığını gözden geçirin.

- İlgili yerlerde denetimleri ve güncelleştirme temellerini çözümleyin. Eylemleri çözümlemek için bilet öğeleri oluşturun ve bunları çözümlenene kadar izleyin.

**Daha fazla kaynak**:

- [SQL Güvenlik Açığı Değerlendirmesi](/sql/relational-databases/security/sql-vulnerability-assessment)
- [SQL güvenlik açığı değerlendirme hizmeti, veritabanı güvenlik açıklarını belirlemenize yardımcı olur](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Hassas verileri tanımla ve etiketleme

Büyük olasılıkla hassas veriler içeren sütunları bulur. Hassas veriler büyük ölçüde müşteriye, uyumluluk yönetmeine ve bu verilerin ücretlendirilmesi gereken kullanıcılar tarafından değerlendirilmelidir. Sütunları, gelişmiş duyarlılık tabanlı denetim ve koruma senaryolarını kullanacak şekilde sınıflandırın.

**Nasıl uygulanır**:

- Veritabanlarınızdaki hassas verileri bulmak, sınıflandırmak, etiketlemek ve korumak için [SQL veri bulma ve sınıflandırmasını](data-discovery-and-classification-overview.md) kullanın.
  - SQL veri bulma ve sınıflandırma panosundaki otomatik bulma tarafından oluşturulan sınıflandırma önerilerini görüntüleyin. Hassas verilerinizin kalıcı olarak sınıflandırma etiketleriyle etiketlenmesi gibi ilgili sınıflandırmaları kabul edin.
  - Otomatik mekanizmaya bulunmayan ek gizli veri alanları için sınıflandırmaları el ile ekleyin.
- Daha fazla bilgi için bkz. [SQL veri bulma ve sınıflandırma](/sql/relational-databases/security/sql-data-discovery-and-classification).

**En iyi uygulamalar**:

- Veritabanının sınıflandırma durumunun doğru değerlendirmesi için sınıflandırma panosunu düzenli olarak izleyin. Veritabanı sınıflandırması durumundaki bir rapor, uyumluluk ve denetim amaçlarıyla paylaşmak üzere aktarılabilir veya yazdırılabilir.

- SQL güvenlik açığı değerlendirmesinde önerilen hassas verilerin durumunu sürekli olarak izleyin. Hassas veri bulma kuralını izleyin ve sınıflandırma için önerilen sütunlarda tüm DRFT 'yi belirler.  

- Sınıflandırmayı, kuruluşunuzun belirli ihtiyaçlarına göre uyarlanmış bir şekilde kullanın. Azure Güvenlik Merkezi 'nde [SQL Information Protection](../../security-center/security-center-info-protection-policy.md) ilkesinde Information Protection ilkenizi (duyarlılık etiketleri, bilgi türleri, bulma mantığı) özelleştirin.

### <a name="track-access-to-sensitive-data"></a>Gizli verilere erişimi izleme

Gizli verilere erişen ve denetim günlüklerindeki gizli verilerdeki sorguları yakalayan izleyici.

**Nasıl uygulanır**:

- SQL Denetimi ile Veri Sınırlandırması'nı birlikte kullanın.
  - [SQL veritabanı denetim](../../azure-sql/database/auditing-overview.md) günlüğlemenizde, erişimi gizli verilere özel olarak izleyebilirsiniz. Ayrıca, erişilen veriler ve duyarlık etiketi gibi bilgileri de görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [veri bulma ve sınıflandırma](data-discovery-and-classification-overview.md) ve [hassas verilere erişimi denetleme](data-discovery-and-classification-overview.md#audit-sensitive-data).

**En iyi uygulamalar**:

- Bkz. denetim ve veri sınıflandırması bölümleri için en iyi uygulamalar:
  - [Kritik güvenlik olaylarını denetleme](#audit-critical-security-events)
  - [Hassas verileri tanımla ve etiketleme](#identify-and-tag-sensitive-data)

### <a name="visualize-security-and-compliance-status"></a>Güvenlik ve uyumluluk durumunu görselleştirin

Veri merkezlerinizin güvenlik duruşunu güçlendirir (SQL veritabanı 'ndaki veritabanları dahil) Birleşik bir altyapı güvenliği yönetim sistemi kullanın. Veritabanlarının güvenliğine ve uyumluluk durumuna ilişkin önerilerin bir listesini görüntüleyin.

**Nasıl uygulanır**:

- [Azure Güvenlik Merkezi](https://azure.microsoft.com/documentation/services/security-center/)'nde SQL ile ilgili güvenlik önerilerini ve etkin tehditleri izleyin.

## <a name="common-security-threats-and-potential-mitigations"></a>Yaygın güvenlik tehditleri ve olası azaltmaları

Bu bölüm bazı saldırı vektörlerine karşı korumaya yönelik güvenlik önlemleri bulmanıza yardımcı olur. Yukarıdaki güvenlik yönergelerinden biri veya daha fazlası izlenerek en çok azaltmaları elde edilebilir.

### <a name="security-threat-data-exfiltration"></a>Güvenlik tehdidi: veri taşalımı

Veri tanımı, bir bilgisayardan veya sunucudan veri kopyalama, aktarma veya alma yetkisi yok. Vivon 'da [veri](https://en.wikipedia.org/wiki/Data_exfiltration) akışı için bir tanım görüntüleyin.

Genel bir uç nokta üzerinden sunucuya bağlanmak, müşterilerin güvenlik duvarlarını ortak IP 'lere açmasını gerektirdiğinden veri sızdırma riskini gösterir.  

**Senaryo 1**: Azure VM 'deki bir uygulama, Azure SQL veritabanı 'ndaki bir veritabanına bağlanır. Standart dışı bir aktör VM 'ye erişim sağlar ve uygulamayı bu şekilde alır. Bu senaryoda, veri sızdırma işlemi, standart dışı VM kullanan bir dış varlığın veritabanına bağlanmasını, kişisel verileri kopyaladığını ve bir BLOB depolama alanında ya da farklı bir abonelikte farklı bir SQL veritabanında depoladığını gösterir.

**Senaryo 2**: bir kaba DBA. Bu senaryo, genellikle güvenliği önemli müşteriler tarafından düzenlenen sektörlerden oluşur. Bu senaryoda, yüksek ayrıcalıklı bir kullanıcı verileri Azure SQL veritabanından veri sahibi tarafından denetlenen başka bir aboneliğe kopyalayabilir.

**Olası azaltmaları**:

Bugün, Azure SQL veritabanı ve SQL yönetilen örneği, veri çıkarma tehditleri için aşağıdaki teknikleri sunmaktadır:

- VM 'den hangi bölgelere erişilebileceğini denetlemek için Azure VM 'lerinin NSG 'lerinde Izin verme ve reddetme kurallarının bir birleşimini kullanın.
- SQL veritabanında bir sunucu kullanıyorsanız, aşağıdaki seçenekleri ayarlayın:
  - Azure hizmetlerinin kapalı çalışmasına izin verin.
  - Yalnızca VNet güvenlik duvarı kuralı ayarlayarak, Azure VM 'nizi içeren alt ağdan gelen trafiğe izin verin.
  - [Özel bağlantı](../../private-link/private-endpoint-overview.md) kullan
- SQL yönetilen örneği için, özel IP erişiminin kullanılması standart dışı bir VM 'nin ilk veri savunma sorunu ele alınmıştır. Bir SQL yönetilen örnek alt ağında en kısıtlayıcı ilkeyi otomatik olarak ayarlamak için bir alt ağ üzerinde alt ağ temsili özelliğini açın.
- Daha büyük bir yüzey alanı olduğu ve ağ gereksinimlerinin müşteriler tarafından görülebilmesi için, sahte bir DBA, SQL yönetilen örneği ile daha fazla açığa çıkarılır. Bunun için en iyi şekilde risk azaltma, bu güvenlik kılavuzundaki tüm uygulamaların ilk yerinde (yalnızca veri ayıklanma değil) standart dışı DBA senaryosunu önlemesini sağlar. Always Encrypted hassas verileri şifreleyerek korumak ve anahtarı DBA için erişilemez durumda tutmak için bir yöntemdir.

## <a name="security-aspects-of-business-continuity-and-availability"></a>İş sürekliliği ve kullanılabilirliğinin güvenlik yönleri

Çoğu güvenlik standardı, tek hata noktalarından kaçınmak için artıklık ve yük devretme özellikleri uygulayarak gerçekleştirilen işlemsel sürekliliği açısından veri kullanılabilirliğini ele geçirir. Olağanüstü durum senaryolarında verilerin ve günlük dosyalarının yedeklerini tutan yaygın bir uygulamadır.Aşağıdaki bölümde, Azure 'da yerleşik olan yetenekler hakkında üst düzey bir genel bakış sağlanmaktadır. Ayrıca, belirli ihtiyaçları karşılamak üzere yapılandırılabilecek ek seçenekler de sağlar:

- Azure, yerleşik yüksek kullanılabilirlik sunar: [SQL veritabanı ve SQL yönetilen örneği Ile yüksek kullanılabilirlik](high-availability-sla.md)

- İş Açısından Kritik katmanı, varsayılan olarak etkinleştirilen yük devretme gruplarını, tam ve fark günlüğü yedeklemelerini ve zaman içindeki geri yükleme yedeklemelerini içerir:  
  - [Otomatik yedeklemeler](automated-backups-overview.md)
  - [Otomatik veritabanı yedeklemeleri kullanarak bir veritabanını kurtarma-zaman içinde geri yükleme](recovery-using-backups.md#point-in-time-restore)

- Farklı Azure coğrafyalar 'larda bölge yedekli yapılandırma ve otomatik yük devretme grupları gibi ek iş sürekliliği özellikleri yapılandırılabilir: 
    - [Premium & İş Açısından Kritik hizmet katmanları için yüksek kullanılabilirlik-bölge yedekli yapılandırma](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)
    - [Genel Amaçlı hizmet katmanı için yüksek kullanılabilirlik-bölge yedekli yapılandırma](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
    - [İş sürekliliği 'ne genel bakış](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure SQL veritabanı güvenlik özelliklerine genel bakış](security-overview.md)
