---
title: Azure 'da PaaS veritabanlarının güvenliğini sağlama | Microsoft Docs
description: 'PaaS Web ve mobil uygulamalarınızın güvenliğini sağlamak için Azure SQL veritabanı ve SQL veri ambarı güvenlik en iyi uygulamaları hakkında bilgi edinin. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: f7d993799fed637fbec55afc8f06d90c8fc6910f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726776"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Azure 'da PaaS veritabanlarını güvenli hale getirmek için en iyi yöntemler

Bu makalede, hizmet olarak platform (PaaS) Web ve mobil uygulamalarınızın güvenliğini sağlamak için [Azure SQL veritabanı](../../sql-database/sql-database-technical-overview.md) ve [SQL veri ambarı](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 'nın en iyi güvenlik uygulamalarının bir koleksiyonunu tartıştık. Bu en iyi uygulamalar, Azure deneyimimizden ve sizin gibi müşterilerin deneyimlerini elde edilmiştir.

Azure SQL veritabanı ve SQL veri ambarı, internet tabanlı uygulamalarınız için bir ilişkisel veritabanı hizmeti sağlar. PaaS dağıtımında Azure SQL veritabanı ve SQL veri ambarı 'nı kullanırken uygulamalarınızın ve verilerinizin korunmasına yardımcı olan hizmetlere göz atalım:

- Azure Active Directory kimlik doğrulaması (SQL Server kimlik doğrulaması yerine)
- Azure SQL güvenlik duvarı
- Saydam Veri Şifrelemesi (TDE)

## <a name="use-a-centralized-identity-repository"></a>Merkezi kimlik deposu kullanma
Azure SQL veritabanları, iki kimlik doğrulama türünden birini kullanacak şekilde yapılandırılabilir:

- **SQL kimlik doğrulaması** bir Kullanıcı adı ve parola kullanır. Veritabanınıza ait mantıksal sunucuyu oluşturduktan sonra kullanıcı adı ve parola belirleyerek "sunucu yöneticisi" oturum açma bilgisi oluşturdunuz. Bu kimlik bilgilerini kullanarak, veritabanı sahibi olarak söz konusu sunucudaki herhangi bir veritabanında kimlik doğrulaması yapabilirsiniz.

- **Azure Active Directory kimlik doğrulaması** , Azure Active Directory tarafından yönetilen kimlikleri kullanır ve yönetilen ve tümleşik etki alanları için desteklenir. Azure Active Directory kimlik doğrulaması kullanmak için, Azure AD kullanıcıları ve grupları 'nı yönetmeye izin verilen "Azure AD yöneticisi" adlı başka bir sunucu yöneticisi oluşturmanız gerekir. Bu yönetici normal bir sunucu yöneticisinin gerçekleştirebileceği tüm işlemleri yapabilir.

[Azure Active Directory kimlik doğrulaması](../../active-directory/develop/authentication-scenarios.md) , Azure SQL VERITABANı ve SQL veri ambarı 'na Azure ACTIVE DIRECTORY (ad) kimlikleri kullanarak bağlanma mekanizmasıdır. Azure AD, veritabanı sunucularında Kullanıcı kimliklerinin kullanımını durdurabilmeniz için SQL Server kimlik doğrulaması için bir alternatif sağlar. Azure AD kimlik doğrulaması, veritabanı kullanıcılarının ve diğer Microsoft hizmetlerinin kimliklerini tek bir merkezi konumda merkezi olarak yönetmenize olanak sağlar. Merkezi KIMLIK yönetimi, veritabanı kullanıcılarını yönetmek ve izin yönetimini basitleştireceğinizi sağlayan tek bir yer sağlar.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>SQL kimlik doğrulaması yerine Azure AD kullanmanın avantajları
- Tek bir yerde parola döndürmeye izin verir.
- Dış Azure AD gruplarını kullanarak veritabanı izinlerini yönetir.
- Tümleşik Windows kimlik doğrulamasını ve Azure AD tarafından desteklenen diğer kimlik doğrulama biçimlerini etkinleştirerek parolaların depolanmasını ortadan kaldırır.
- , Veritabanı düzeyinde kimliklerin kimliğini doğrulamak için kapsanan veritabanı kullanıcılarını kullanır.
- SQL veritabanı 'na bağlanan uygulamalar için belirteç tabanlı kimlik doğrulamasını destekler.
- Etki alanı eşitlemesi olmadan yerel bir Azure AD için Active Directory Federasyon Hizmetleri (AD FS) (ADFS) veya yerel kullanıcı/parola kimlik doğrulaması ile etki alanı Federasyonu destekler.
- , [Çok faktörlü kimlik doğrulaması (MFA)](/azure/active-directory/authentication/multi-factor-authentication)Içeren Active Directory evrensel kimlik doğrulaması kullanan SQL Server Management Studio bağlantıları destekler. MFA telefon görüşmesi, SMS mesajı, pin ile akıllı kart veya mobil uygulama bildirimi gibi çok sayıda kolay doğrulama seçeneğine sahiptir. Daha fazla bilgi için bkz. [SQL veritabanı ve SQL veri ambarı Ile evrensel kimlik doğrulaması](../../sql-database/sql-database-ssms-mfa-authentication.md).

Azure AD kimlik doğrulaması hakkında daha fazla bilgi için bkz.

- [SQL veritabanı, yönetilen örnek veya SQL veri ambarı ile kimlik doğrulaması için Azure Active Directory kimlik doğrulaması kullanma](../../sql-database/sql-database-aad-authentication.md)
- [Azure SQL Veri Ambarı’nda kimlik doğrulama](../../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Azure AD kimlik doğrulaması kullanarak Azure SQL DB için belirteç tabanlı kimlik doğrulama desteği](../../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Azure Active Directory ortamınıza uygun olduğundan emin olmak için bkz. [Azure AD özellikleri ve sınırlamaları](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>IP adresine göre erişimi kısıtla
Kabul edilebilir IP adresleri aralıklarını belirten güvenlik duvarı kuralları oluşturabilirsiniz. Bu kurallar hem sunucu hem de veritabanı düzeylerine hedeflenebilir. Güvenliği geliştirmek ve veritabanınızı daha taşınabilir hale getirmek için mümkün olduğunda veritabanı düzeyinde güvenlik duvarı kuralları kullanmanızı öneririz. Sunucu düzeyinde güvenlik duvarı kuralları, Yöneticiler için en iyi seçenektir ve aynı erişim gereksinimlerine sahip birçok veritabanınız olduğunda, ancak her veritabanını ayrı ayrı yapılandırırken zaman harcamayı istemezsiniz.

SQL veritabanı varsayılan kaynak IP adresi kısıtlamaları, diğer abonelikler ve kiracılar dahil olmak üzere herhangi bir Azure adresinden erişime izin verir. Bunu, yalnızca IP adreslerinizin örneğe erişmesine izin verecek şekilde kısıtlayabilirsiniz. SQL güvenlik duvarınız ve IP adresi kısıtlamalarınız da dahil olmak üzere güçlü kimlik doğrulaması gerekir. Bu makalede daha önce yapılan önerilere bakın.

Azure SQL güvenlik duvarı ve IP kısıtlamaları hakkında daha fazla bilgi edinmek için bkz.:

- [Azure SQL veritabanı ve SQL veri ambarı erişim denetimi](../../sql-database/sql-database-control-access.md)
- [Azure SQL veritabanı ve SQL veri ambarı güvenlik duvarı kuralları](../../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Bekleyen verileri şifreleme
[Saydam veri şifrelemesi (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) varsayılan olarak etkindir. TDE SQL Server, Azure SQL veritabanı ve Azure SQL veri ambarı verilerini ve günlük dosyalarını saydam bir şekilde şifreler. TDE, dosyalara veya yedeklemesine doğrudan erişim güvenliğinin aşılmasına karşı koruma sağlar. Bu, mevcut uygulamaları değiştirmeden bekleyen verileri şifrelemenizi sağlar. TDE her zaman etkin kalır; Ancak, bu, normal erişim yolunu kullanan bir saldırgan tarafından durdurulmaz. TDE, çeşitli sektörlerde sağlanan birçok yasalar, düzenleme ve kılavuzla uyum sağlar.

Azure SQL, TDE ile ilgili önemli sorunları yönetir. TDE ile birlikte, kurtarılabilirlik sağlamak ve veritabanlarını taşırken, şirket içi özel dikkatli olunmalıdır. Daha karmaşık senaryolarda anahtarlar, Genişletilebilir anahtar yönetimi aracılığıyla Azure Key Vault içinde açıkça yönetilebilir. Bkz. [EKM kullanarak TDE SQL Server etkinleştirme](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Bu ayrıca Azure Anahtar Kasası BYOK yeteneği aracılığıyla Kendi Anahtarını Getir (BYOK) sağlar.

Azure SQL, [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)aracılığıyla sütunlar için şifreleme sağlar. Bu, yalnızca yetkili uygulamaların hassas sütunlara erişmesine izin verir. Bu tür bir şifreleme kullanıldığında, şifrelenmiş sütunlar için SQL sorguları eşitlik tabanlı değerlere göre sınırlandırılır.

Uygulama düzeyi şifreleme, seçmeli veriler için de kullanılmalıdır. Veri egemenliği sorunları bazen doğru ülkede/bölgede tutulan bir anahtarla veri şifreleyerek azaltılabilir. Bu, bir güçlü algoritmanın kullanıldığı varsayılarak (AES 256 gibi), anahtar olmadan verilerin şifresinin çözülmesi olanaksız olduğundan, yanlışlıkla veri aktarımının soruna neden olmasına engel olur.

Güvenli bir sistem tasarlama, gizli varlıkları şifreleme ve veritabanı sunucuları etrafında bir güvenlik duvarı oluşturma gibi ek önlemleri kullanarak veritabanının güvenliğini sağlamaya yardımcı olabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Bu makale, PaaS Web ve mobil uygulamalarınızın güvenliğini sağlamaya yönelik bir SQL veritabanı ve SQL veri ambarı güvenlik en iyi uygulamaları koleksiyonuna tanıtılmıştır. PaaS dağıtımlarınızın güvenliğini sağlama hakkında daha fazla bilgi edinmek için bkz.:

- [PaaS dağıtımlarının güvenliğini sağlama](paas-deployments.md)
- [Azure Uygulama Hizmetleri 'ni kullanarak PaaS Web ve mobil uygulamalarının güvenliğini sağlama](paas-applications-using-app-services.md)
