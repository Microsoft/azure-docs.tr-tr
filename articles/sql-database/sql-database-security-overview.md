---
title: Güvenlik Genel Bilgileri
description: Bulut ve şirket içi SQL Server arasındaki farklılıklar dahil olmak üzere Azure SQL veritabanı ve SQL Server güvenliği hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: d952229ab327440771db6cc5ac64db2256491179
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823247"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Azure SQL veritabanı güvenlik özelliklerine genel bakış

Bu makalede, Azure SQL veritabanı 'nı kullanarak bir uygulamanın veri katmanını güvenli hale getirmenin temelleri özetlenmektedir. Açıklanan güvenlik stratejisi aşağıdaki resimde gösterildiği gibi katmanlı derinlemesine savunma yaklaşımını takip eder ve dışarıdan şu şekilde gider:

![SQL-Security-Layer. png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Ağ güvenliği

Microsoft Azure SQL Veritabanı, bulut ve kurumsal uygulamalar için bir ilişkisel veritabanı hizmeti sağlar. Güvenlik duvarları, müşteri verilerini korumaya yardımcı olmak için, IP adresine veya Azure sanal ağ trafiği kaynağına dayalı olarak erişim izni verilene kadar veritabanı sunucusuna ağ erişimini engeller.

### <a name="ip-firewall-rules"></a>IP güvenlik duvarı kuralları

IP güvenlik duvarı kuralları, her isteğin kaynak IP adresine göre veritabanlarına erişim izni verir. Daha fazla bilgi için bkz. [Azure SQL veritabanı ve SQL veri ambarı güvenlik duvarı kurallarına genel bakış](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Sanal ağ güvenlik duvarı kuralları

[Sanal ağ hizmeti uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md) , sanal ağ bağlantınızı Azure omurgası üzerinden genişlettirecektir ve trafiğin kaynaklandığı sanal ağ alt ağını belirlemek IÇIN Azure SQL veritabanı 'nı etkinleştirin. Trafiğin Azure SQL veritabanına ulaşmasını sağlamak için, ağ güvenlik grupları aracılığıyla giden trafiğe izin vermek üzere SQL [hizmeti etiketlerini](../virtual-network/security-overview.md) kullanın.

[Sanal ağ kuralları](sql-database-vnet-service-endpoint-rule-overview.md) , Azure SQL veritabanı 'nın yalnızca bir sanal ağ içindeki seçili alt ağlardan gönderilen iletişimleri kabul etmesine olanak tanır.

> [!NOTE]
> Güvenlik Duvarı kurallarıyla erişimi denetlemek **yönetilen bir örnek** *için uygulanmaz.* Gereken ağ yapılandırması hakkında daha fazla bilgi için bkz. [yönetilen örneğe bağlanma](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Erişim yönetimi

> [!IMPORTANT]
> Azure 'daki veritabanlarını ve veritabanı sunucularını yönetmek, Portal Kullanıcı hesabınızın rol atamaları tarafından denetlenir. Bu makale hakkında daha fazla bilgi için, bkz. [Azure Portal rol tabanlı erişim denetimi](../role-based-access-control/overview.md).

### <a name="authentication"></a>Kimlik Doğrulaması

Kimlik doğrulama, kullanıcının talep ettikleri kim olduğunu kanıtlama işlemidir. Azure SQL veritabanı iki tür kimlik doğrulamasını destekler:

- **SQL kimlik doğrulaması**:

    SQL veritabanı kimlik doğrulaması, [Azure SQL veritabanı](sql-database-technical-overview.md) 'na bağlanırken Kullanıcı adı ve parola kullanarak bir kullanıcının kimlik doğrulamasını ifade eder. Veritabanı için veritabanı sunucu oluşturma sırasında, bir Kullanıcı adı ve parolayla "Sunucu Yöneticisi" oturum açması belirtilmelidir. Bu kimlik bilgilerini kullanarak bir "Sunucu Yöneticisi" veritabanı sahibi olarak veritabanı sunucusundaki herhangi bir veritabanında kimlik doğrulaması yapabilir. Bundan sonra, ek SQL oturum açmaları ve kullanıcılar, kullanıcıların Kullanıcı adı ve parola kullanarak bağlanmasına olanak tanıyan Sunucu Yöneticisi tarafından oluşturulabilir.

- **Azure Active Directory kimlik doğrulaması**:

    Azure Active Directory kimlik doğrulaması, [Azure SQL veritabanı](sql-database-technical-overview.md) ve [SQL veri ambarı](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 'Na Azure ACTIVE DIRECTORY kimlik (Azure AD) kullanarak bağlanma mekanizmasıdır. Azure AD kimlik doğrulaması, yöneticilerin, diğer Microsoft hizmetleriyle birlikte veritabanı kullanıcılarının kimliklerini ve izinlerini tek bir merkezi konumda merkezi olarak yönetmesine olanak tanır. Bu, parola depolamanın en az düzeyde bir kısmını içerir ve merkezi parola döndürme ilkelerini sunar.

     SQL veritabanı ile Azure AD kimlik doğrulamasını kullanmak için **Active Directory Yöneticisi** olarak adlandırılan bir sunucu yöneticisi oluşturulmalıdır. Daha fazla bilgi için bkz. [Azure Active Directory kimlik doğrulaması kullanarak SQL veritabanı 'Na bağlanma](sql-database-aad-authentication.md). Azure AD kimlik doğrulaması hem yönetilen hem de Federasyon hesaplarını destekler. Federasyon hesapları, Azure AD ile federe bir müşteri etki alanı için Windows kullanıcılarını ve gruplarını destekler.

    Kullanılabilir ek Azure AD kimlik doğrulama seçenekleri [Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) ve [koşullu erişim](sql-database-conditional-access.md)dahil [SQL Server Management Studio bağlantılar için evrensel kimlik doğrulaması Active Directory](sql-database-ssms-mfa-authentication.md) .

> [!IMPORTANT]
> Azure 'daki veritabanlarını ve sunucuları yönetmek, Portal Kullanıcı hesabınızın rol atamaları tarafından denetlenir. Bu makale hakkında daha fazla bilgi için, bkz. [Azure Portal rol tabanlı erişim denetimi](../role-based-access-control/overview.md). Güvenlik Duvarı kurallarıyla erişimi denetlemek **yönetilen bir örnek** *için uygulanmaz.* Gereken ağ yapılandırması hakkında daha fazla bilgi için lütfen [yönetilen bir örneğe bağlanma](sql-database-managed-instance-connect-app.md) hakkında aşağıdaki makaleye bakın.

## <a name="authorization"></a>Yetkilendirme

Yetkilendirme, bir Azure SQL veritabanı içindeki bir kullanıcıya atanan izinleri ifade eder ve kullanıcının ne yapmasına izin verileceğini belirler. İzinler, [veritabanı rollerine](/sql/relational-databases/security/authentication-access/database-level-roles) Kullanıcı hesapları eklenerek ve bu rollere veritabanı düzeyi izinleri atanarak veya kullanıcıya belirli [nesne düzeyi izinleri](/sql/relational-databases/security/permissions-database-engine)verilerek denetlenir. Daha fazla bilgi için bkz. [oturum açma ve kullanıcılar](sql-database-manage-logins.md)

En iyi uygulama olarak, gerektiğinde özel roller oluşturun. Kullanıcıları, iş işlevlerini yapmak için gereken en düşük ayrıcalıklara sahip olan role ekleyin. İzinleri doğrudan kullanıcılara atamayın. Sunucu Yöneticisi hesabı, kapsamlı izinlere sahip ve yalnızca yönetim görevleri olan birkaç kullanıcıya verilmesi gereken yerleşik db_owner rolünün bir üyesidir. Azure SQL veritabanı uygulamaları için, çağrılan modülün Yürütme bağlamını belirtmek için [execute as](/sql/t-sql/statements/execute-as-clause-transact-sql) kullanın veya sınırlı Izinlerle [uygulama rollerini](/sql/relational-databases/security/authentication-access/application-roles) kullanın. Bu uygulama, veritabanına bağlanan uygulamanın uygulama için gereken en düşük ayrıcalıklara sahip olmasını sağlar. Bu en iyi uygulamaları takip etmek, görevlerin ayrılmasını de çok daha da fazla.

### <a name="row-level-security"></a>Satır düzeyi güvenlik

Satır düzeyi güvenlik, müşterilerin bir veritabanı tablosundaki satırlara erişimi, sorguyu yürüten kullanıcının özelliklerine göre denetlemesini sağlar (örneğin, Grup üyeliği veya yürütme bağlamı). Satır düzeyi güvenlik, özel etiket tabanlı güvenlik kavramlarını uygulamak için de kullanılabilir. Daha fazla bilgi için bkz. [Satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security).

![Azure-Database-RLS. png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Tehdit koruması

SQL veritabanı, denetim ve tehdit algılama özellikleri sağlayarak müşteri verilerinin güvenliğini sağlar.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Azure Izleyici günlüklerinde ve Event Hubs SQL denetimi

SQL veritabanı denetimi, veritabanı etkinliklerini izler ve müşterilerin sahip olduğu bir Azure depolama hesabındaki bir denetim günlüğüne veritabanı olaylarını kaydederek güvenlik standartlarıyla uyumluluğu sürdürmenize yardımcı olur. Denetim, kullanıcıların devam eden veritabanı etkinliklerini izlemelerine ve olası tehditleri ve şüpheli kötüye kullanımı ve güvenlik ihlallerini belirlemek üzere geçmiş etkinliğini çözümleyip araştırmalarını sağlar. Daha fazla bilgi için bkz. [SQL veritabanı denetimini](sql-database-auditing.md)kullanmaya başlama.  

### <a name="advanced-threat-protection"></a>Gelişmiş Tehdit Koruması

Gelişmiş tehdit koruması SQL Server günlüklerinizi çözümleyerek, olağan dışı davranışları ve veritabanlarına erişme veya açıktan yararlanmaya yönelik olabilecek zararlı girişimleri tespit etmek SQL ekleme, olası veri girişi ve deneme yanılma saldırıları gibi şüpheli etkinlikler için uyarılar oluşturulur ve erişim desenlerine yönelik olarak ayrıcalık yürüyen istekleri ve ihlal edilen kimlik bilgileri kullanımını yakalayın. Uyarılar, şüpheli etkinliklerin ayrıntılarının sağlandığı ve tehdidi hafifletmek için eylemlerle birlikte daha fazla araştırma önerileri sunan [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)' nden görüntülenir. Gelişmiş tehdit koruması, sunucu başına ek bir ücret karşılığında etkinleştirilebilir. Daha fazla bilgi için bkz. [SQL veritabanı Gelişmiş tehdit koruması ile çalışmaya başlama](sql-database-threat-detection.md).

![Azure-Database-TD. jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Bilgi koruması ve şifreleme

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Aktarım Katmanı Güvenliği TLS (iletim içi şifreleme)

SQL veritabanı, [Aktarım Katmanı Güvenliği](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)ile hareket halindeki verileri şifreleyerek müşteri verilerinin güvenliğini sağlar.

SQL Server tüm bağlantılar için her zaman şifreleme (SSL/TLS) uygular. Bu, bağlantı dizesinde **şifreleme** veya **TrustServerCertificate** ayarından bağımsız olarak, tüm verilerin istemci ve sunucu arasında "geçişte" şifrelendiğinden emin olmanızı sağlar.

En iyi uygulama olarak, uygulamanızın bağlantı dizesinde şifreli bir bağlantı _**belirtmeniz ve sunucu**_ sertifikasına güvenmesini öneririz. Bu, uygulamanızı sunucu sertifikasını doğrulamaya zorlar ve böylece uygulamanızın ortadaki tür saldırılarına karşı savunmasız kalmasına engel olur.

Örneğin, ADO.NET sürücüsünü kullanırken bu, **encrypt = true** ve **TrustServerCertificate = false**aracılığıyla gerçekleştirilir. Bağlantı dizenizi Azure portal elde ediyorsanız, doğru ayarlara sahip olur.

> [!IMPORTANT]
> Bazı Microsoft dışı sürücülerin, çalışması için varsayılan olarak TLS veya daha eski bir TLS (< 1.2) sürümünü kullanabileceğini unutmayın. Bu durumda SQL Server veritabanınıza bağlanmanızı sağlar. Ancak, özellikle hassas verileri depoluiyorsanız, bu tür sürücülere ve uygulamanın SQL veritabanına bağlanmasına izin vermenin güvenlik risklerini değerlendirmenizi öneririz. 
>
> TLS ve bağlantı hakkında daha fazla bilgi için bkz. [TLS konuları](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Saydam Veri Şifrelemesi (bekleyen şifreleme)

[Azure SQL veritabanı için saydam veri şifrelemesi (TDE)](transparent-data-encryption-azure-sql.md) , bekleyen verilerin ham dosyalara veya yedeklemelere izinsiz veya çevrimdışı erişimden korunmasına yardımcı olmak için bir güvenlik katmanı ekler. Yaygın senaryolar, veri merkezi hırsızlık veya disk sürücüleri ve yedekleme bantları gibi donanım veya ortamların güvenli bir şekilde çıkarılması içerir. TDE, uygulama geliştiricilerinin mevcut uygulamalarda herhangi bir değişiklik yapmasını gerektirmeyen bir AES şifreleme algoritması kullanarak tüm veritabanını şifreler.

Azure 'da, yeni oluşturulan tüm SQL veritabanları varsayılan olarak şifrelenir ve veritabanı şifreleme anahtarı yerleşik bir sunucu sertifikası tarafından korunur.  Sertifika bakımı ve döndürme, hizmet tarafından yönetilir ve kullanıcıdan giriş gerektirmez. Şifreleme anahtarlarının denetimini almayı tercih eden müşteriler [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md)anahtarlarını yönetebilir.

### <a name="key-management-with-azure-key-vault"></a>Azure Key Vault ile anahtar yönetimi

[Kendi anahtarını getir](transparent-data-encryption-byok-azure-sql.md) (byok) [Saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption) (tde) desteği, müşterilerin Azure 'un bulut tabanlı dış anahtar yönetim sistemini [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md)kullanarak anahtar yönetiminin ve döndürmenin sahipliğini almasına olanak tanır. Veritabanının anahtar kasasına erişimi iptal edildiğinde, bir veritabanının şifresi çözülemez ve belleğe okunamaz. Azure Key Vault, merkezi bir temel yönetim platformu sağlar, sıkı izlenen donanım güvenlik modüllerini (HSM 'ler) kullanır ve güvenlik uyumluluk gereksinimlerini karşılamaya yardımcı olmak için anahtar ve veri yönetimi arasında görev ayrımı sağlar.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (kullanımda olan şifreleme)

![Azure-Database-AE. png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) , belirli veritabanı sütunlarında depolanan hassas verileri erişimden korumak için tasarlanmış bir özelliktir (örneğin, kredi kartı numaraları, ulusal kimlik numaraları veya tek _yapmanız gereken_ veriler). Bu, veritabanı yöneticilerini veya yönetim görevlerini gerçekleştirmek üzere veritabanına erişim yetkisi olan diğer ayrıcalıklı kullanıcıları içerir, ancak şifrelenmiş sütunlardaki belirli verilere erişmesi gereken iş gerektirmez. Veriler her zaman şifrelenir. Bu, şifrelenmiş verilerin yalnızca şifreleme anahtarına erişimi olan istemci uygulamaları tarafından işlenmek üzere şifresinin çözülmesi anlamına gelir.  Şifreleme anahtarı hiçbir şekilde SQL 'e gösterilmez ve [Windows sertifika deposunda](sql-database-always-encrypted.md) veya [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)depolanabilir.

### <a name="dynamic-data-masking"></a>Dinamik veri maskeleme

![Azure-Database-DDM. png](media/sql-database-security-overview/azure-database-ddm.png)

SQL veritabanı dinamik veri maskeleme, hassas veri pozlamasını ayrıcalıklı olmayan kullanıcılarla maskeleyerek kısıtlar. Dinamik veri maskeleme, Azure SQL veritabanı 'nda potansiyel olarak hassas verileri otomatik olarak bulur ve uygulama katmanında en az etkiyle bu alanları maskelemek için eylem yapılabilir öneriler sağlar. Bu özellik, hassas verileri belirlenen veritabanı alanlarına yapılan sorgunun sonuç kümesinde karartır ancak veritabanındaki veriler değişmez. Daha fazla bilgi için bkz. [SQL veritabanı dinamik veri maskeleme 'yi kullanmaya başlama](sql-database-dynamic-data-masking-get-started.md).

## <a name="security-management"></a>Güvenlik yönetimi

### <a name="vulnerability-assessment"></a>Güvenlik açığı değerlendirmesi

[Güvenlik açığı değerlendirmesi](sql-vulnerability-assessment.md) , genel veritabanı güvenliğini önceden iyileştirmek amacıyla hedefe yönelik olası veritabanı güvenlik açıklarını keşfettirecek, izleyebilen ve düzeltmeye yardımcı olabilecek bir hizmeti kolayca yapılandırabilir. Güvenlik açığı değerlendirmesi (VA), gelişmiş SQL güvenlik özelliklerine yönelik Birleşik bir paket olan gelişmiş veri güvenliği (ADS) sunumunun bir parçasıdır. Güvenlik açığı değerlendirmesi, merkezi SQL ADS portalı aracılığıyla erişilebilir ve yönetilebilir.

### <a name="data-discovery--classification"></a>Veri bulma ve sınıflandırma

Veri bulma & sınıflandırması (Şu anda önizlemede), veritabanınızdaki hassas verileri bulmak, sınıflandırmak, etiketlemek ve korumak için Azure SQL veritabanı 'nda yerleşik olarak bulunan gelişmiş özellikleri sağlar. En önemli verilerinizi bulma ve sınıflandırma (iş/finans, Sağlık Hizmetleri, kişisel veriler vb.), kurumsal bilgi koruma ortamınızda bir özetleme rolü oynayabilir. Bu, için altyapı işlevi görebilir:

- Hassas verilere yönelik anormal erişimlerde izleme (denetim) ve uyarı verme gibi çeşitli güvenlik senaryoları.
- Son derece hassas veriler içeren veritabanlarının güvenliğine erişimi ve güvenliğini sağlamlaştırma.
- Veri gizliliği standartları ve mevzuat uyumluluk gereksinimlerini karşılamanıza yardımcı olma.

Daha fazla bilgi için bkz. [veri bulma ile çalışmaya başlama & sınıflandırma](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Uyumluluk

Uygulamanızın çeşitli güvenlik gereksinimlerini karşılamasına yardımcı olabilecek yukarıdaki özelliklere ve işlevlere ek olarak, Azure SQL veritabanı normal denetim özelliklerine de katılır ve bir dizi uyumluluk standartlarına karşı sertifikalandırilmiştir. Daha fazla bilgi için SQL veritabanı uyumluluk sertifikalarının en güncel listesini bulabileceğiniz [Microsoft Azure Güven Merkezi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) ' ne bakın.

### <a name="feature-restrictions"></a>Özellik kısıtlamaları

Özellik kısıtlamaları, SQL ekleme işleminin başarılı olduğu durumlarda bile, bazı SQL ekleme biçimlerinin veritabanı hakkında bilgi sızdırmasını önlemeye yardımcı olur. Daha fazla bilgi için bkz. [Azure SQL veritabanı özellik kısıtlamaları](sql-database-feature-restrictions.md).

## <a name="next-steps"></a>Sonraki adımlar

- SQL Veritabanındaki erişim denetimi özelliklerinin kullanımı hakkında ayrıntılı bilgi için bkz. [Erişim denetimi](sql-database-control-access.md).
- Veritabanı denetimi ile ilgili bir tartışma için bkz. [SQL veritabanı denetimi](sql-database-auditing.md).
- Tehdit algılama hakkında bir tartışma için bkz. [SQL veritabanı tehdit algılama](sql-database-threat-detection.md).
