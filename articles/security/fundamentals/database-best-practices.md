---
title: Veritabanı güvenliği en iyi yöntemler-Microsoft Azure
description: Bu makalede, Azure veritabanı güvenliği için bir dizi en iyi uygulama sunulmaktadır.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 72b15d77baedae318d4503f2d481b08202730459
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927999"
---
# <a name="azure-database-security-best-practices"></a>Azure güvenliği için en iyi yöntemler
Bu makalede veritabanı güvenliği için en iyi yöntemler açıklanmaktadır.

En iyi uygulamalar, görüşlerden oluşan yarışmaları temel alır ve geçerli Azure platformu özellikleri ve özellik kümeleriyle çalışır. Zamanla ve teknolojiler zaman içinde değişir ve bu makale, bu değişiklikleri yansıtacak şekilde düzenli olarak güncelleştirilir.

## <a name="secure-databases"></a>Güvenli veritabanları
Güvenlik, veritabanlarının yönetiminde önemli bir konudur ve [Azure SQL veritabanı](../../sql-database/index.yml)için her zaman bir önceliktir. Veritabanları, HIPAA, ISO 27001/27002 ve PCI DSS düzey 1 dahil olmak üzere çok sayıda yasal veya güvenlik gereksinimini karşılamanıza yardımcı olmak için sıkı bir şekilde güvenli hale getirilir. Güvenlik uyumluluğu sertifikalarına yönelik geçerli bir liste, [Microsoft Trust Center sitesinde](https://azure.microsoft.com/support/trust-center/services/)bulunabilir. Ayrıca, veritabanlarını yasal gereksinimlere göre belirli Azure veri merkezlerine yerleştirmeyi seçebilirsiniz.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Veritabanı erişimini kısıtlamak için güvenlik duvarı kuralları kullanma
Microsoft Azure SQL Veritabanı, Azure ve diğer internet tabanlı uygulamalar için bir ilişkisel veritabanı hizmeti sağlar. Erişim güvenliği sağlamak için SQL veritabanı, ile erişimi denetler:

- Bağlantıyı IP adresine göre sınırlayan güvenlik duvarı kuralları.
- Kullanıcıların kimliklerini kanıtlamasını gerektiren kimlik doğrulama mekanizmaları.
- Kullanıcıları belirli eylemlerle ve verilerle sınırlayan Yetkilendirme mekanizmaları.

Güvenlik duvarları, hangi bilgisayarların izne sahip olduğunu belirtene kadar veritabanı sunucunuza tüm erişimi engeller. Güvenlik duvarı, her bir isteğin kaynak IP adresine göre veritabanlarına erişim verir.

Aşağıdaki şekilde, SQL veritabanında bir sunucu güvenlik duvarı ayarladığınız yer gösterilmektedir:

![Güvenlik duvarı kuralları](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

Azure SQL veritabanı hizmeti yalnızca TCP bağlantı noktası 1433 üzerinden kullanılabilir. Bilgisayarınızdan bir SQL veritabanına erişmek için, istemci bilgisayar güvenlik duvarınızın 1433 numaralı TCP bağlantı noktasında giden TCP iletişimine izin verdiğinden emin olun. Diğer uygulamalar için bu bağlantılara ihtiyacınız yoksa güvenlik duvarı kurallarını kullanarak TCP bağlantı noktası 1433 ' deki gelen bağlantıları engelleyin.

Bağlantı sürecinin bir parçası olarak, Azure sanal makinelerinin bağlantıları her çalışan rolü için benzersiz olan bir IP adresine ve bağlantı noktasına yönlendirilir. Bağlantı noktası numarası 11000 ile 11999 arasındadır. TCP bağlantı noktaları hakkında daha fazla bilgi için bkz. [ADO.NET 4,5 için 1433](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

SQL Veritabanındaki güvenlik duvarı kuralları hakkında daha fazla bilgi için bkz. [SQL Veritabanı güvenlik duvarı kuralları](../../sql-database/sql-database-firewall-configure.md).

> [!Note]
> IP kurallarına ek olarak, güvenlik duvarı sanal ağ kurallarını yönetir. Sanal ağ kuralları, sanal ağ hizmeti uç noktalarına dayalıdır. Sanal ağ kuralları, bazı durumlarda IP kuralları için tercih edilebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı Için sanal ağ hizmet uç noktaları ve kuralları](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Veritabanı kimlik doğrulamasını etkinleştir
SQL veritabanı iki tür kimlik doğrulaması, SQL Server kimlik doğrulaması ve Azure AD kimlik doğrulaması destekler.

### <a name="sql-server-authentication"></a>*SQL Server kimlik doğrulaması*

Avantajları şunlardır:

- SQL veritabanı 'nın, tüm kullanıcıların kimlik doğrulaması bir Windows etki alanı tarafından doğrulanmadığı karma işletim sistemlerine sahip ortamları desteklemesini sağlar.
- SQL veritabanı 'nın, SQL Server kimlik doğrulaması gerektiren eski uygulamaları ve iş ortakları tarafından sağlanan uygulamaları desteklemesini sağlar.
- Kullanıcıların bilinmeyen veya güvenilmeyen etki alanlarından bağlanmasına izin verir. Örnek olarak, belirlenen müşterilerin siparişlerinin durumunu almak için atanan SQL Server oturum açma bilgileriyle bağlanacağı bir uygulamadır.
- SQL Database 'in, kullanıcıların kendi kimliklerini oluşturdıkları Web tabanlı uygulamaları desteklemesini sağlar.
- Yazılım geliştiricilerinin, bilinen, önceden ayarlanmış SQL Server oturumlarını temel alan karmaşık bir izin hiyerarşisini kullanarak uygulamalarını dağıtmasını sağlar.

> [!NOTE]
> SQL Server kimlik doğrulaması, Kerberos güvenlik protokolünü kullanamıyor.

SQL Server kimlik doğrulaması kullanıyorsanız şunları yapmanız gerekir:

- Güçlü kimlik bilgilerini kendiniz yönetin.
- Bağlantı dizesindeki kimlik bilgilerini koruyun.
- (Potansiyel) ağ üzerinden Web sunucusundan geçirilen kimlik bilgilerini veritabanına koruyun. Daha fazla bilgi için [nasıl yapılır: ASP.NET 2,0](/previous-versions/msp-n-p/ff648340(v=pandp.10))' de SQL kimlik doğrulaması kullanarak SQL Server bağlanın.

### <a name="azure-active-directory-ad-authentication"></a>*Azure Active Directory (AD) kimlik doğrulaması*
Azure AD kimlik doğrulaması, Azure AD 'de kimlikleri kullanarak Azure SQL veritabanı ve [SQL veri ambarı](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 'na bağlanma mekanizmasıdır. Azure AD kimlik doğrulamasıyla, veritabanı kullanıcılarının ve diğer Microsoft hizmetlerinin kimliklerini tek bir merkezi konumda yönetebilirsiniz. Merkezi KIMLIK yönetimi, veritabanı kullanıcılarını yönetmek ve izin yönetimini basitleştireceğinizi sağlayan tek bir yer sağlar.

> [!NOTE]
> SQL Server kimlik doğrulaması kullanımı üzerinden Azure AD kimlik doğrulamasının kullanılmasını öneririz.

Avantajları şunlardır:

- SQL Server kimlik doğrulaması için bir alternatif sağlar.
- Veritabanı sunucuları arasında kullanıcı kimliklerinin uzamasını durdurmaya yardımcı olur.
- Tek bir yerde parola döndürmeye izin verir.
- Müşteriler, dış (Azure AD) gruplarını kullanarak veritabanı izinlerini yönetebilir.
- Tümleşik Windows kimlik doğrulamasını ve Azure Active Directory tarafından desteklenen diğer kimlik doğrulama biçimlerini etkinleştirerek parolaların depolanmasını ortadan kaldırabilir.
- Veritabanı düzeyinde kimliklerin kimliğini doğrulamak için kapsanan veritabanı kullanıcılarını kullanır.
- SQL veritabanı 'na bağlanan uygulamalar için belirteç tabanlı kimlik doğrulamasını destekler.
- Etki alanı eşitlemesi olmadan yerel bir Azure Active Directory örneği için AD FS (etki alanı Federasyonu) veya yerel kullanıcı/parola kimlik doğrulamasını destekler.
- Azure AD, çok faktörlü kimlik doğrulaması içeren Active Directory evrensel kimlik doğrulaması kullanan SQL Server Management Studio bağlantıları destekler. Multi-Factor Authentication; telefon araması, SMS mesajı, PIN ile akıllı kartlar veya mobil uygulama bildirimi gibi bir dizi doğrulama seçeneği ile güçlü kimlik doğrulaması sağlar. Daha fazla bilgi için bkz. [SQL veritabanı ve SQL veri ambarı Ile Azure AD Multi-Factor Authentication Için SSMS desteği](../../sql-database/sql-database-ssms-mfa-authentication.md).

Yapılandırma adımları, Azure AD kimlik doğrulamasını yapılandırmak ve kullanmak için aşağıdaki yordamları içerir:

- Azure AD 'yi oluşturun ve doldurun.
- İsteğe bağlı: Azure aboneliğinizle ilişkili olan Active Directory örneğini ilişkilendirin veya değiştirin.
- Azure SQL veritabanı veya [Azure SQL veri ambarı](https://azure.microsoft.com/services/sql-data-warehouse/)için Azure Active Directory Yöneticisi oluşturun.
- İstemci bilgisayarlarınızı yapılandırın.
- Veritabanınızda Azure AD kimlikleriyle eşlenmiş kapsanan veritabanı kullanıcıları oluşturun.
- Azure AD kimliklerini kullanarak veritabanınıza bağlanın.

[SQL veritabanı, yönetilen örnek veya SQL veri ambarı ile kimlik doğrulaması için Azure Active Directory kimlik doğrulaması kullanma](../../sql-database/sql-database-aad-authentication.md)hakkında ayrıntılı bilgileri bulabilirsiniz.

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Şifreleme ve satır düzeyinde güvenlik kullanarak verilerinizi koruma
[Azure SQL veritabanı saydam veri şifrelemesi](../../sql-database/transparent-data-encryption-azure-sql.md) , diskteki verilerin korunmasına yardımcı olur ve donanıma yetkisiz erişime karşı koruma sağlar. Uygulamada değişiklik gerektirmeden, bekleyen veritabanı, ilişkili yedeklemeler ve işlem günlüğü dosyaları için gerçek zamanlı şifreleme ve şifre çözme işlemleri gerçekleştirir. Saydam veri şifrelemesi, veritabanı şifreleme anahtarı adlı bir simetrik anahtar kullanarak veritabanının tamamının depolanmasını şifreler.

Tüm depolama alanı şifrelendiğinde bile veritabanının kendisini şifrelemek da önemlidir. Bu, veri koruma için derinlemesine savunma yaklaşımını bir uygulamasıdır. Azure SQL veritabanı kullanıyorsanız ve hassas verileri (kredi kartı veya sosyal güvenlik numaraları gibi) korumak istiyorsanız, FIPS 140-2 tarafından doğrulanan 256 bit AES şifrelemesiyle veritabanlarını şifreleyebilirsiniz. Bu şifreleme birçok sektör standardı gereksinimini karşılar (örneğin, HIPAA ve PCI).

Bir veritabanını saydam veri şifrelemesi kullanarak şifrelerken, [arabellek havuzu uzantısı (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) ile ilgili dosyalar şifrelenmez. BPE ile ilgili dosyalar için [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) veya [şifreleme dosya sistemi (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) gibi dosya sistemi düzeyinde şifreleme araçlarını kullanmanız gerekir.

Bir güvenlik yöneticisi veya bir veritabanı yöneticisi gibi yetkili bir Kullanıcı, veritabanı saydam veri şifrelemesiyle şifrelense bile verilere erişebildiğinden, bu önerileri de izlemeniz gerekir:

- Veritabanı düzeyinde SQL Server kimlik doğrulamasını etkinleştirin.
- [RBAC rollerini](/azure/role-based-access-control/overview)kullanarak Azure AD kimlik doğrulamasını kullanın.
- Kullanıcıların ve uygulamaların kimlik doğrulaması için ayrı hesaplar kullanmasını sağlayın. Bu şekilde, kullanıcılara ve uygulamalara verilen izinleri sınırlayabilir ve kötü amaçlı etkinlik riskini azaltabilirsiniz.
- Veritabanı düzeyindeki güvenliği, sabit veritabanı rollerini (örneğin db_datareader veya db_datawriter) kullanarak uygulayın. İsterseniz, seçili veritabanı nesnelerine açık izinler vermek için uygulamanız için özel roller de oluşturabilirsiniz.

Verilerinizi güvenli hale getirmeye yönelik diğer yollar için şunları göz önünde bulundurun:

- [Hücre düzeyinde şifreleme](/sql/relational-databases/security/encryption/encrypt-a-column-of-data): Belirli sütunları hatta veri hücrelerini farklı şifreleme anahtarlarıyla şifrelemenizi sağlar.
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), istemcilerin istemci uygulamalarında hassas verileri şifrelemesine olanak tanır ve şifreleme anahtarlarını veritabanı ALTYAPıSıNA (SQL veritabanı veya SQL Server) hiçbir şekilde açığa çıkarmayın. Sonuç olarak Always Encrypted, verilerin sahibi olan (ve görüntüleyebilen) ve verileri yöneten (ancak erişimi olmayan) aralarında bir ayrım sağlar.
- Müşterilerin bir veritabanı tablosundaki satırlara erişimi, sorguyu yürüten kullanıcının özelliklerine göre denetlemesini sağlayan [satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security). (Örnek özellikler grup üyedir ve yürütme bağlamıdır.)

Veritabanı düzeyinde şifrelemeyi kullanmayan kuruluşlar, SQL veritabanlarında bulunan verilerin tehlikeye düşmesi saldırılarına karşı daha savunmasız olabilir.

[Azure SQL veritabanı ile saydam veri şifrelemesi](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx)MAKALESINI okuyarak SQL veritabanı saydam veri şifrelemesi hakkında daha fazla bilgi edinebilirsiniz.

## <a name="enable-database-auditing"></a>Veritabanı denetimini etkinleştir
SQL Server veritabanı altyapısının veya tek bir veritabanının bir örneğini denetlemek izleme ve günlüğe kaydetme olaylarını içerir. SQL Server için, sunucu düzeyi olaylar ve veritabanı düzeyindeki olaylara yönelik belirtimlerle ilgili belirtimleri içeren denetimler oluşturabilirsiniz. Denetlenen olaylar olay günlüklerine yazılabilir veya dosyaları denetlemek için kullanılabilir.

Yüklemenizin kamu veya standartlar gereksinimlerine bağlı olarak SQL Server için birkaç denetim düzeyi vardır. SQL Server denetim, çeşitli sunucu ve veritabanı nesnelerinde denetimleri etkinleştirmek, depolamak ve görüntülemek için araçlar ve süreçler sağlar.

[Azure SQL veritabanı denetimi](/azure/sql-database/sql-database-auditing) , veritabanı olaylarını izler ve bunları Azure Depolama hesabınızdaki bir denetim günlüğüne yazar.

Denetim, yasal uyumluluğu korumanıza, veritabanı etkinliklerini anlamanıza ve iş endişeleri veya güvenlik ihlallerine işaret edebilecek tutarsızlıklar ve bozukluklar bulmanıza yardımcı olabilir. Denetim, uyumluluk standartlarını kolaylaştırır ancak uyumluluk garantisi vermez.

Veritabanı denetimi ve nasıl etkinleştirileceği hakkında daha fazla bilgi edinmek için bkz. [SQL veritabanı denetimini kullanmaya başlama](/azure/sql-database/sql-database-auditing).

## <a name="enable-database-threat-detection"></a>Veritabanı tehdit algılamayı etkinleştir
Tehdit koruması algılamayı aşacak. Veritabanı tehdit koruması şunları içerir:

- Verilerinizi koruyabilmeniz için en hassas verilerinizi bulup sınıflandırın.
- Veritabanınızı koruyabilmeniz için veritabanınıza güvenli yapılandırma uygulama.
- Hızlı yanıt verebilir ve düzeltebilmeniz için olası tehditleri tespit etme ve bunlara yanıt verme.

**En iyi uygulama**: Veritabanlarınızdaki hassas verileri bulun, sınıflandırın ve etiketleyin.   
**Ayrıntı**: Azure SQL veritabanı 'nda [veri bulmayı ve sınıflandırmayı](/azure/sql-database/sql-database-data-discovery-and-classification) etkinleştirerek SQL veritabanınızdaki verileri sınıflandırın. Azure panosu 'nda hassas verilerinize erişimi izleyebilir veya raporları indirebilirsiniz.

**En iyi uygulama**: Veritabanı güvenlerinizi öngörüden iyileştirebilmeniz için veritabanı güvenlik açıklarını izleyin.   
**Ayrıntı**: Olası veritabanı güvenlik açıklarını tarayan Azure SQL veritabanı [güvenlik açığı değerlendirme](/azure/sql-database/sql-vulnerability-assessment) hizmetini kullanın. Hizmet, güvenlik açıklarına işaret eden ve yanlış yapılandırma, aşırı izin ve korunmayan gizli veriler gibi en iyi uygulamalardan sapmaları gösteren bir kural temel bilgilerini kullanır.

Kurallar, Microsoft 'un en iyi uygulamalarını temel alır ve veritabanınıza ve değerli verilere en büyük riskleri sunan güvenlik sorunlarına odaklanmaktadır. Sunucu güvenlik duvarı ayarları ve sunucu düzeyi izinler gibi veritabanı düzeyinde sorunları ve sunucu düzeyi güvenlik sorunlarını kapsar. Bu kurallar aynı zamanda, uyumluluk standartlarını karşılamak için mevzuat gövdelerinden birçok gereksinimi temsil eder.

**En iyi uygulama**: Tehdit algılamayı etkinleştirin.  
**Ayrıntı**:  Güvenlik uyarıları ve tehditleri araştırıp azaltma hakkında öneriler almak için Azure SQL veritabanı [tehdit algılamasını](/azure/sql-database/sql-database-threat-detection) etkinleştirin. Şüpheli veritabanı etkinlikleri, olası güvenlik açıkları ve SQL ekleme saldırıları ve anormal veritabanı erişimi ve sorgu desenleri hakkında uyarılar alırsınız.

[Gelişmiş tehdit koruması](/azure/sql-database/sql-advanced-threat-protection) , gelişmiş SQL güvenlik özelliklerine yönelik Birleşik bir pakettir. Daha önce bahsedilen hizmetleri içerir: Veri bulma ve sınıflandırma, güvenlik açığı değerlendirmesi ve tehdit algılama. Bu özellikleri etkinleştirmek ve yönetmek için tek bir konum sağlar.

Bu özellikleri etkinleştirmek şunları yapmanıza yardımcı olur:

- Veri gizliliği standartları ve mevzuat uyumluluk gereksinimlerini karşılayın.
- Veritabanlarınıza erişimi denetleyin ve bunların güvenliğini sağlamlaştırın.
- Değişikliklerin izlenmesi zor olan dinamik bir veritabanı ortamını izleyin.
- Olası tehditleri tespit edin ve bunlara yanıt verin.

Ayrıca, tehdit algılama, tüm Azure kaynaklarınızın güvenlik durumuna ilişkin merkezi bir görünüm için uyarıları Azure Güvenlik Merkezi ile tümleştirir.

## <a name="enable-feature-restrictions"></a>Özellik Kısıtlamalarını Etkinleştir

Veritabanlarınızda bulunan veriler, veritabanı hatalarından ve sorgu yürütme sürelerinden yararlanan saldırı vektörlerini kullanan saldırganlar tarafından sunulabilir. Azure SQL veritabanı, veritabanınızı korumak için bir dizi özellik kısıtlama mekanizması sağlar. Daha fazla bilgi için bkz. [SQL veritabanı özellik kısıtlamaları](/azure/sql-database/sql-database-feature-restrictions).

## <a name="next-steps"></a>Sonraki adımlar
Azure 'u kullanarak bulut çözümlerinizi tasarlarken, dağıttığınızda ve yönetirken en iyi güvenlik uygulamaları için bkz. [Azure Güvenlik en iyi uygulamaları ve desenleri](best-practices-and-patterns.md) .

Aşağıdaki kaynaklar, Azure güvenliği ve ilgili Microsoft hizmetleri hakkında daha genel bilgiler sağlamak için kullanılabilir:
* Azure [güvenlik ekibi blogu](https://blogs.msdn.microsoft.com/azuresecurity/) -Azure güvenliği ile ilgili en son bilgiler için
* [Microsoft Güvenlik](https://technet.microsoft.com/library/dn440717.aspx) açıkları, Azure ile ilgili sorunlar da dahil olmak üzere Microsoft güvenlik açıklarına göre bildirilebilir veya e-posta ilesecure@microsoft.com
