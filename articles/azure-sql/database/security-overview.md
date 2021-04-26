---
title: Güvenlik Genel Bakış
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL veritabanı ve Azure SQL yönetilen örneği ' nde, SQL Server nasıl farklılık gösteren güvenlik hakkında bilgi edinin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 10/26/2020
ms.openlocfilehash: 084f9aae16cfbf495f05c90c8244b2b9b71cf624
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812993"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Azure SQL veritabanı ve SQL yönetilen örnek güvenlik özelliklerine genel bakış
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Bu makalede, [Azure SQL veritabanı](sql-database-paas-overview.md), [Azure SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md)ve [Azure SYNAPSE Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)kullanarak bir uygulamanın veri katmanını güvenli hale getirmenin temelleri özetlenmektedir. Açıklanan güvenlik stratejisi aşağıdaki resimde gösterildiği gibi katmanlı derinlemesine savunma yaklaşımını takip eder ve dışarıdan şu şekilde gider:

![Katmanlı derinlemesine savunma diyagramı. Müşteri verileri, ağ güvenliği, erişim yönetimi ve tehdit ve bilgi korumalarının katmanlarında yapılır.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>Ağ güvenliği

Microsoft Azure SQL Veritabanı, SQL yönetilen örneği ve Azure SYNAPSE Analytics, bulut ve kurumsal uygulamalar için bir ilişkisel veritabanı hizmeti sağlar. Güvenlik duvarları, müşteri verilerini korumaya yardımcı olmak için, IP adresine veya Azure sanal ağ trafiği kaynağına dayalı olarak erişim izni verilene kadar sunucuya ağ erişimini engeller.

### <a name="ip-firewall-rules"></a>IP güvenlik duvarı kuralları

IP güvenlik duvarı kuralları, her isteğin kaynak IP adresine göre veritabanlarına erişim izni verir. Daha fazla bilgi için bkz. [Azure SQL veritabanı ve Azure SYNAPSE Analytics güvenlik duvarı kurallarına genel bakış](firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Sanal ağ güvenlik duvarı kuralları

[Sanal ağ hizmeti uç noktaları](../../virtual-network/virtual-network-service-endpoints-overview.md) , sanal ağ bağlantınızı Azure omurgası üzerinden genişlettirecektir ve trafiğin kaynaklandığı sanal ağ alt ağını belirlemek IÇIN Azure SQL veritabanı 'nı etkinleştirin. Trafiğin Azure SQL veritabanına ulaşmasını sağlamak için, ağ güvenlik grupları aracılığıyla giden trafiğe izin vermek üzere SQL [hizmeti etiketlerini](../../virtual-network/network-security-groups-overview.md) kullanın.

[Sanal ağ kuralları](vnet-service-endpoint-rule-overview.md) , Azure SQL veritabanı 'nın yalnızca bir sanal ağ içindeki seçili alt ağlardan gönderilen iletişimleri kabul etmesine olanak tanır.

> [!NOTE]
> Güvenlik Duvarı kurallarıyla erişimi denetlemek **SQL yönetilen örneği** *için geçerlidir.* Gereken ağ yapılandırması hakkında daha fazla bilgi için bkz. [yönetilen örneğe bağlanma](../managed-instance/connect-application-instance.md)

## <a name="access-management"></a>Erişim yönetimi

> [!IMPORTANT]
> Azure 'daki veritabanlarını ve sunucuları yönetmek, Portal Kullanıcı hesabınızın rol atamaları tarafından denetlenir. Bu makale hakkında daha fazla bilgi için [Azure Portal Azure rol tabanlı erişim denetimi](../../role-based-access-control/overview.md)bölümüne bakın.

### <a name="authentication"></a>Kimlik Doğrulaması

Kimlik doğrulama, kullanıcının talep ettikleri kim olduğunu kanıtlama işlemidir. Azure SQL veritabanı ve SQL yönetilen örneği iki tür kimlik doğrulamasını destekler:

- **SQL kimlik doğrulaması**:

    SQL kimlik doğrulaması, Kullanıcı adı ve parola kullanarak Azure SQL veritabanı veya Azure SQL yönetilen örneği ile bağlantı kurulurken bir kullanıcının kimlik doğrulamasını ifade eder. Sunucu oluşturulurken Kullanıcı adı ve parola ile **Sunucu Yöneticisi** oturum açma belirtilmelidir. Bu kimlik bilgilerini kullanarak **Sunucu Yöneticisi** , veritabanı sahibi olarak bu sunucu veya örnekteki herhangi bir veritabanında kimlik doğrulaması yapabilir. Bundan sonra, ek SQL oturum açmaları ve kullanıcılar, kullanıcıların Kullanıcı adı ve parola kullanarak bağlanmasına olanak tanıyan Sunucu Yöneticisi tarafından oluşturulabilir.

- **Azure Active Directory kimlik doğrulaması**:

    Azure Active Directory kimlik doğrulaması, Azure [SQL veritabanı](sql-database-paas-overview.md), [Azure SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md) ve [Azure SYNAPSE ANALYTICS](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 'e Azure Active Directory (Azure AD) kimlikleri kullanılarak bağlanma mekanizmasıdır. Azure AD kimlik doğrulaması, yöneticilerin diğer Azure hizmetleriyle birlikte veritabanı kullanıcılarının kimliklerini ve izinlerini tek bir merkezi konumda merkezi olarak yönetmesine olanak tanır. Bu, parola depolamanın en az düzeyde bir kısmını içerir ve merkezi parola döndürme ilkelerini sunar.

     SQL veritabanı ile Azure AD kimlik doğrulamasını kullanmak için **Active Directory Yöneticisi** olarak adlandırılan bir sunucu yöneticisi oluşturulmalıdır. Daha fazla bilgi için bkz. [Azure Active Directory kimlik doğrulaması kullanarak SQL veritabanı 'Na bağlanma](authentication-aad-overview.md). Azure AD kimlik doğrulaması hem yönetilen hem de Federasyon hesaplarını destekler. Federasyon hesapları, Azure AD ile federe bir müşteri etki alanı için Windows kullanıcılarını ve gruplarını destekler.

    Kullanılabilir ek Azure AD kimlik doğrulama seçenekleri [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) ve [koşullu erişim](conditional-access-configure.md)dahil [SQL Server Management Studio bağlantılar için evrensel kimlik doğrulaması Active Directory](authentication-mfa-ssms-overview.md) .

> [!IMPORTANT]
> Azure 'daki veritabanlarını ve sunucuları yönetmek, Portal Kullanıcı hesabınızın rol atamaları tarafından denetlenir. Bu makale hakkında daha fazla bilgi için, bkz. [Azure Portal Azure rol tabanlı erişim denetimi](../../role-based-access-control/overview.md). Güvenlik Duvarı kurallarıyla erişimi denetlemek **SQL yönetilen örneği** *için geçerlidir.* Gereken ağ yapılandırması hakkında daha fazla bilgi için lütfen [yönetilen bir örneğe bağlanma](../managed-instance/connect-application-instance.md) hakkında aşağıdaki makaleye bakın.

## <a name="authorization"></a>Yetkilendirme

Yetkilendirme, Azure SQL veritabanı veya Azure SQL yönetilen örneği içindeki bir veritabanı içindeki bir kullanıcıya atanan izinlere başvurur ve kullanıcının ne yapmasına izin verileceğini belirler. İzinler, [veritabanı rollerine](/sql/relational-databases/security/authentication-access/database-level-roles) Kullanıcı hesapları eklenerek ve bu rollere veritabanı düzeyi izinleri atanarak veya kullanıcıya belirli [nesne düzeyi izinleri](/sql/relational-databases/security/permissions-database-engine)verilerek denetlenir. Daha fazla bilgi için bkz. [oturum açma ve kullanıcılar](logins-create-manage.md)

En iyi uygulama olarak, gerektiğinde özel roller oluşturun. Kullanıcıları, iş işlevlerini yapmak için gereken en düşük ayrıcalıklara sahip olan role ekleyin. İzinleri doğrudan kullanıcılara atamayın. Sunucu Yöneticisi hesabı, kapsamlı izinlere sahip ve yalnızca yönetim görevleri olan birkaç kullanıcıya verilmesi gereken yerleşik db_owner rolünün bir üyesidir. Uygulamalar için, çağrılan modülün Yürütme bağlamını belirtmek için [execute as](/sql/t-sql/statements/execute-as-clause-transact-sql) kullanın veya sınırlı Izinlerle [uygulama rollerini](/sql/relational-databases/security/authentication-access/application-roles) kullanın. Bu uygulama, veritabanına bağlanan uygulamanın uygulama için gereken en düşük ayrıcalıklara sahip olmasını sağlar. Bu en iyi uygulamaları takip etmek, görevlerin ayrılmasını de çok daha da fazla.

### <a name="row-level-security"></a>Satır düzeyinde güvenlik

Row-Level güvenliği, müşterilerin bir veritabanı tablosundaki satırlara erişimi, sorguyu yürüten kullanıcının özelliklerine göre denetlemesini sağlar (örneğin, Grup üyeliği veya yürütme bağlamı). Row-Level güvenliği, özel etiket tabanlı güvenlik kavramlarını uygulamak için de kullanılabilir. Daha fazla bilgi için bkz. [satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security).

![Bir SQL veritabanının bir istemci uygulaması aracılığıyla Row-Level Security kalkan bireysel satırlarının kullanıcılara erişmesini gösteren diyagram.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Tehdit koruması

Denetim ve tehdit algılama özellikleri sağlayarak SQL veritabanı ve SQL yönetilen örnek güvenli müşteri verileri.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Azure Izleyici günlüklerinde ve Event Hubs SQL denetimi

SQL veritabanı ve SQL yönetilen örnek denetimi, veritabanı etkinliklerini müşterinin sahip olduğu bir Azure depolama hesabındaki bir denetim günlüğüne kaydederek güvenlik standartlarıyla uyumluluğu sürdürmenize yardımcı olur. Denetim, kullanıcıların devam eden veritabanı etkinliklerini izlemelerine ve olası tehditleri ve şüpheli kötüye kullanımı ve güvenlik ihlallerini belirlemek üzere geçmiş etkinliğini çözümleyip araştırmalarını sağlar. Daha fazla bilgi için bkz. [SQL veritabanı denetimini](../../azure-sql/database/auditing-overview.md)kullanmaya başlama.  

### <a name="advanced-threat-protection"></a>Gelişmiş Tehdit Koruması

Gelişmiş tehdit koruması, olağan dışı davranışları ve veritabanlarına erişme ya da yararlanmaya yönelik olabilecek olası girişimleri algılamak için günlüklerinizi analiz etmeyi sağlar. SQL ekleme, olası veri girişi ve deneme yanılma saldırıları gibi şüpheli etkinlikler için uyarılar oluşturulur ve erişim desenlerine yönelik olarak ayrıcalık yürüyen istekleri ve ihlal edilen kimlik bilgileri kullanımını yakalayın. Uyarılar, şüpheli etkinliklerin ayrıntılarının sağlandığı ve tehdidi hafifletmek için eylemlerle birlikte daha fazla araştırma önerileri sunan  [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)' nden görüntülenir. Gelişmiş tehdit koruması, sunucu başına ek bir ücret karşılığında etkinleştirilebilir. Daha fazla bilgi için bkz. [SQL veritabanı Gelişmiş tehdit koruması ile çalışmaya başlama](threat-detection-configure.md).

![Dış saldırgandan veya kötü amaçlı bir Insider 'dan bir Web uygulaması için SQL veritabanına SQL veritabanı izleme erişimini gösteren diyagram.](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Bilgi koruması ve şifreleme

### <a name="transport-layer-security-encryption-in-transit"></a>Aktarım Katmanı Güvenliği (iletim içi şifreleme)

[Aktarım Katmanı Güvenliği (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)ile hareket halindeki VERILERI şifreleyerek SQL VERITABANı, SQL yönetilen örneği ve Azure SYNAPSE Analytics güvenli müşteri verileri.

SQL veritabanı, SQL yönetilen örneği ve Azure SYNAPSE Analytics, tüm bağlantılar için her zaman şifreleme (SSL/TLS) uygular. Bu, bağlantı dizesinde **şifreleme** veya **TrustServerCertificate** ayarından bağımsız olarak, tüm verilerin istemci ve sunucu arasında "geçişte" şifrelendiğinden emin olmanızı sağlar.

En iyi yöntem olarak, uygulama tarafından kullanılan bağlantı dizesinde, şifreli bir bağlantı belirtmeniz ve sunucu sertifikasına _**güvenmemeniz**_ önerilir. Bu, uygulamanızı sunucu sertifikasını doğrulamaya zorlar ve böylece uygulamanızın ortadaki tür saldırılarına karşı savunmasız kalmasına engel olur.

Örneğin, ADO.NET sürücüsünü kullanırken bu,  **encrypt = true** ve **TrustServerCertificate = false** aracılığıyla gerçekleştirilir. Bağlantı dizenizi Azure portal elde ediyorsanız, doğru ayarlara sahip olur.

> [!IMPORTANT]
> Bazı Microsoft dışı sürücülerin, çalışması için varsayılan olarak TLS veya daha eski bir TLS sürümünü (<1,2) kullanabileceğini unutmayın. Bu durumda sunucu yine de veritabanınıza bağlanmanızı sağlar. Ancak, özellikle hassas verileri depoluiyorsanız, bu tür sürücülere ve uygulamanın SQL veritabanına bağlanmasına izin vermenin güvenlik risklerini değerlendirmenizi öneririz.
>
> TLS ve bağlantı hakkında daha fazla bilgi için bkz. [TLS konuları](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Saydam Veri Şifrelemesi (bekleyen şifreleme)

[SQL veritabanı, SQL yönetilen örneği ve Azure SYNAPSE Analytics Için saydam veri şifrelemesi (TDE),](transparent-data-encryption-tde-overview.md) bekleyen verilerin ham dosyalara veya yedeklemelere izinsiz veya çevrimdışı erişimden korunmasına yardımcı olmak için bir güvenlik katmanı ekler. Yaygın senaryolar, veri merkezi hırsızlığı veya disk sürücüleri ve yedekleme bantları gibi donanım veya ortamların güvenli bir şekilde çıkarılması içerir.TDE, uygulama geliştiricilerinin mevcut uygulamalarda herhangi bir değişiklik yapmasını gerektirmeyen bir AES şifreleme algoritması kullanarak tüm veritabanını şifreler.

Azure 'da, yeni oluşturulan tüm veritabanları varsayılan olarak şifrelenir ve veritabanı şifreleme anahtarı yerleşik bir sunucu sertifikası tarafından korunur.  Sertifika bakımı ve döndürme, hizmet tarafından yönetilir ve kullanıcıdan giriş gerektirmez. Şifreleme anahtarlarının denetimini almayı tercih eden müşteriler [Azure Key Vault](../../key-vault/general/security-features.md)anahtarlarını yönetebilir.

### <a name="key-management-with-azure-key-vault"></a>Azure Key Vault ile anahtar yönetimi

[Kendi anahtarını getir](transparent-data-encryption-byok-overview.md) (byok) [Saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption) (tde) desteği, müşterilerin Azure 'un bulut tabanlı dış anahtar yönetim sistemini [Azure Key Vault](../../key-vault/general/security-features.md)kullanarak anahtar yönetiminin ve döndürmenin sahipliğini almasına olanak tanır. Veritabanının anahtar kasasına erişimi iptal edildiğinde, bir veritabanının şifresi çözülemez ve belleğe okunamaz. Azure Key Vault, merkezi bir temel yönetim platformu sağlar, sıkı izlenen donanım güvenlik modüllerini (HSM 'ler) kullanır ve güvenlik uyumluluk gereksinimlerini karşılamaya yardımcı olmak için anahtar ve veri yönetimi arasında görev ayrımı sağlar.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (kullanımda olan şifreleme)

![Always Encrypted özelliğinin temellerini gösteren diyagram. Kilidi olan bir SQL veritabanı yalnızca anahtar içeren bir uygulama tarafından erişilir.](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) , belirli veritabanı sütunlarında depolanan hassas verileri erişimden korumak için tasarlanmış bir özelliktir (örneğin, kredi kartı numaraları, ulusal kimlik numaraları veya tek _yapmanız gereken_ veriler). Bu, veritabanı yöneticilerini veya yönetim görevlerini gerçekleştirmek üzere veritabanına erişim yetkisi olan diğer ayrıcalıklı kullanıcıları içerir, ancak şifrelenmiş sütunlardaki belirli verilere erişmesi gereken iş gerektirmez. Veriler her zaman şifrelenir. Bu, şifrelenmiş verilerin yalnızca şifreleme anahtarına erişimi olan istemci uygulamaları tarafından işlenmek üzere şifresinin çözülmesi anlamına gelir. Şifreleme anahtarı hiçbir şekilde SQL veritabanı veya SQL yönetilen örneği için gösterilmez ve [Windows sertifika deposunda](always-encrypted-certificate-store-configure.md) veya [Azure Key Vault](always-encrypted-azure-key-vault-configure.md)depolanabilir.

### <a name="dynamic-data-masking"></a>Dinamik veri maskeleme

![Dinamik veri maskeleme gösteren diyagram. Bir iş uygulaması, verileri iş uygulamasına geri göndermeden önce maskelerden oluşan bir SQL veritabanına gönderir.](./media/security-overview/azure-database-ddm.png)

Dinamik veri maskeleme, hassas verilerin görünürlüğünü ayrıcalık sahibi olmayan kullanıcılardan gizler. Dinamik veri maskeleme, Azure SQL veritabanı ve SQL yönetilen örneği 'nde potansiyel olarak hassas verileri otomatik olarak bulur ve uygulama katmanında en az etkiyle bu alanları maskelemek için eylem yapılabilir öneriler sağlar. Bu özellik, hassas verileri belirlenen veritabanı alanlarına yapılan sorgunun sonuç kümesinde karartır ancak veritabanındaki veriler değişmez. Daha fazla bilgi için bkz. [SQL veritabanı ve SQL yönetilen örnek dinamik veri maskeleme 'yi kullanmaya başlama](dynamic-data-masking-overview.md).

## <a name="security-management"></a>Güvenlik yönetimi

### <a name="vulnerability-assessment"></a>Güvenlik açığı değerlendirmesi

[Güvenlik açığı değerlendirmesi](sql-vulnerability-assessment.md) , genel veritabanı güvenliğini önceden iyileştirmek amacıyla hedefe yönelik olası veritabanı güvenlik açıklarını keşfettirecek, izleyebilen ve düzeltmeye yardımcı olabilecek bir hizmeti kolayca yapılandırabilir. Güvenlik açığı değerlendirmesi (VA), gelişmiş SQL güvenlik özelliklerine yönelik Birleşik bir paket olan SQL teklifi için Azure Defender 'ın bir parçasıdır. Güvenlik açığı değerlendirmesi, SQL Portal için merkezi Azure Defender aracılığıyla erişilebilir ve yönetilebilir.

### <a name="data-discovery-and-classification"></a>Veri bulma ve sınıflandırma

Veri bulma ve sınıflandırma (Şu anda önizleme aşamasında), veritabanlarınızdaki hassas verileri bulmak, sınıflandırmak, etiketlemek ve korumak için Azure SQL veritabanı ve SQL yönetilen örneği yerleşik olarak bulunan gelişmiş özellikleri sağlar. En önemli verilerinizi bulma ve sınıflandırma (iş/finans, Sağlık Hizmetleri, kişisel veriler vb.), kurumsal bilgi koruma ortamınızda bir özetleme rolü oynayabilir. Şunlara altyapı sağlayabilir:

- Hassas verilere yönelik anormal erişimlerde izleme (denetim) ve uyarı verme gibi çeşitli güvenlik senaryoları.
- Son derece hassas veriler içeren veritabanlarının güvenliğine erişimi ve güvenliğini sağlamlaştırma.
- Veri gizliliği standartlarına uymaya ve mevzuat uyumluluğu gereksinimlerini karşılamaya yardımcı olma.

Daha fazla bilgi için, bkz. [veri bulma ve sınıflandırmayla çalışmaya başlama](data-discovery-and-classification-overview.md).

### <a name="compliance"></a>Uyumluluk

Uygulamanızın çeşitli güvenlik gereksinimlerini karşılamasına yardımcı olabilecek yukarıdaki özelliklere ve işlevlere ek olarak, Azure SQL veritabanı normal denetim özelliklerine de katılır ve bir dizi uyumluluk standartlarına karşı sertifikalandırilmiştir. Daha fazla bilgi için SQL veritabanı uyumluluk sertifikalarının en güncel listesini bulabileceğiniz [Microsoft Azure Güven Merkezi](https://www.microsoft.com/trust-center/compliance/compliance-overview) ' ne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- SQL veritabanı ve SQL yönetilen örneği için oturum açma bilgileri, Kullanıcı hesapları, veritabanı rolleri ve izinlerin kullanımı ile ilgili bir tartışma için bkz. [oturum açma bilgilerini ve Kullanıcı hesaplarını yönetme](logins-create-manage.md).
- Veritabanı denetimi ile ilgili bir tartışma için bkz. [Denetim](../../azure-sql/database/auditing-overview.md).
- Tehdit algılama hakkında bir tartışma için bkz. [tehdit algılama](threat-detection-configure.md).