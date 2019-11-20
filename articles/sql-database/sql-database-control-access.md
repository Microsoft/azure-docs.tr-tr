---
title: Erişim verme
description: Microsoft Azure SQL Veritabanı ve SQL veri ambarı 'na erişim verme hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: sql-data-warehouse, seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 05/08/2019
ms.openlocfilehash: 05a949bbd99a36c41143190d216116f78c433951
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826604"
---
# <a name="azure-sql-database-and-sql-data-warehouse-access-control"></a>Azure SQL veritabanı ve SQL veri ambarı erişim denetimi

Güvenlik, Azure [SQL veritabanı](sql-database-technical-overview.md) ve [SQL veri ambarı](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) denetim erişimi, IP adresine göre bağlantıyı sınırlayan güvenlik duvarı kurallarıyla, kullanıcıların kimliklerini kanıtlamasını gerektiren kimlik doğrulama mekanizmalarının ve yetkilendirme mekanizmalarının güvenliğini sağlamak için kullanıcıları belirli eylemlerle ve verilerle sınırlama. 

> [!IMPORTANT]
> SQL Veritabanı güvenlik özelliklerine genel bakış için bkz. [SQL güvenliğine genel bakış](sql-database-security-overview.md). Öğretici için bkz. [Azure SQL veritabanınızı güvenli hale getirme](sql-database-security-tutorial.md). SQL veri ambarı güvenlik özelliklerine genel bakış için bkz. [SQL veri ambarı güvenliğine genel bakış](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)

## <a name="firewall-and-firewall-rules"></a>Güvenlik duvarı ve güvenlik duvarı kuralları

Microsoft Azure SQL Veritabanı, Azure ile diğer İnternet tabanlı uygulamalar için ilişkisel veritabanı hizmeti sunar. Güvenlik duvarları, verilerinizin korunmasına yardımcı olmak üzere, hangi bilgisayarların izinli olduğunu belirtmenize kadar veritabanı sunucunuza tüm erişimi engeller. Güvenlik duvarı, her bir isteğin kaynak IP adresine göre veritabanlarına erişim verir. Daha fazla bilgi için bkz. [Azure SQL Veritabanı güvenlik duvarı kurallarına genel bakış](sql-database-firewall-configure.md)

Azure SQL Veritabanı hizmeti yalnızca 1433 numaralı TCP bağlantı noktasından kullanılabilir. Bilgisayarınızdan bir SQL Veritabanına erişmek için istemci bilgisayarınızdaki güvenlik duvarının 1433 numaralı TCP bağlantı noktasından giden TCP iletişimine izin verdiğinden emin olun. Başka uygulamalar için gerekli değilse 1433 numaralı bağlantı noktasından gelen TCP bağlantılarını engelleyin. 

Bağlantı işleminin bir parçası olarak, Azure sanal makinelerinden gelen bağlantılar her bir çalışan rolü için farklı olan bir IP adresine ve bağlantı noktasına yönlendirilir. Bağlantı noktası numarası 11000 ile 11999 arasındadır. TCP bağlantı noktaları hakkında daha fazla bilgi için bkz. [ADO.NET 4,5 ve SQL Veritabanı2 için 1433 dışındaki bağlantı noktaları](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Kimlik Doğrulaması

SQL Veritabanı iki kimlik doğrulaması türünü destekler:

- **SQL kimlik doğrulaması**:

  Bu kimlik doğrulama yöntemi bir Kullanıcı adı ve parola kullanır. Veritabanınız için SQL veritabanı sunucusunu oluşturduğunuzda, bir Kullanıcı adı ve parolayla "Sunucu Yöneticisi" oturum açma adı belirttiniz. Bu kimlik bilgilerini kullanarak veritabanı sahibi veya "dbo" olarak sunucudaki tüm veritabanları için kimlik doğrulamasından geçebilirsiniz. 
- **Azure Active Directory kimlik doğrulaması**:

  Bu kimlik doğrulama yöntemi, Azure Active Directory tarafından yönetilen kimlikleri kullanır ve yönetilen ve tümleşik etki alanları için desteklenir. Azure Active Directory Kimlik Doğrulamasını kullanmak istiyorsanız, Azure AD kullanıcılarını ve gruplarını yönetme izni olan "Azure AD yöneticisi" adlı başka bir sunucu yöneticisi daha oluşturmanız gerekir. Bu yönetici normal bir sunucu yöneticisinin gerçekleştirebileceği tüm işlemleri yapabilir. Azure Active Directory kimlik doğrulamasını etkinleştirme amacıyla Azure AD yöneticisi oluşturma adımları için bkz. [Azure Active Directory Kimlik Doğrulaması kullanarak SQL Veritabanına Bağlanma](sql-database-aad-authentication.md).

Veritabanı Altyapısı, 30 dakikadan fazla boşta kalan bağlantıları kapatır. Bağlantının kullanılabilmesi için yeniden oturum açılması gerekir. SQL Veritabanına yapılan sürekli etkin bağlantılar için en az 10 saatte bir yeniden yetkilendirme (veritabanı altyapısı tarafından gerçekleştirilir) gerekir. Veritabanı altyapısı, kullanılan özgün parolayı kullanarak yeniden yetkilendirme gerçekleştirmeyi dener ve kullanıcı müdahalesi gerekli değildir. Performans nedenleriyle, SQL veritabanında bir parola sıfırlandığında bağlantı, bağlantı havuzu nedeniyle sıfırlansa bile yeniden kimlik doğrulaması yapılmaz. Bu, şirket içi SQL Server'ın davranışından farklıdır. Bağlantının ilk yetkilendirme adımından sonra parolanın değiştirilmesi halinde bağlantının sonlandırılması ve yeni parolayla yeni bir bağlantı kurulması gerekir. `KILL DATABASE CONNECTION` iznine sahip bir kullanıcı SQL Veritabanı bağlantılarını [KILL](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql) komutunu kullanarak sonlandırabilir.

Kullanıcı hesapları ana veritabanında oluşturulabilir ve sunucudaki tüm veritabanlarında izin verilebilir veya bunlar veritabanı içinde oluşturulabilir (bağımsız kullanıcılar olarak adlandırılır). Oturum açma bilgisi oluşturma ve yönetme hakkında bilgi için bkz. [Oturum açma bilgilerini yönetme](sql-database-manage-logins.md). Taşınabilirlik ve ölçeklenebilirliği geliştirmek için kapsanan veritabanlarını kullanın. Bağımsız kullanıcılar hakkında daha fazla bilgi için bkz. [Bağımsız Veritabanı Kullanıcıları - Veritabanınızı Taşınabilir Hale Getirme](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), [CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) ve [Bağımsız Veritabanları](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases).

En iyi uygulama olarak, uygulamanızın kimlik doğrulaması için özel bir hesap kullanması gerekir. Bu şekilde uygulamaya verilen izinleri sınırlayabilir ve uygulama kodunuzun SQL ekleme saldırısına açık olması durumunda kötü niyetli etkinlik risklerini azaltabilirsiniz. Önerilen yaklaşım, uygulamanızın doğrudan veritabanında kimlik doğrulamasından geçmesi için [bağımsız veritabanı kullanıcısı](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) oluşturmaktır. 

## <a name="authorization"></a>Yetkilendirme

Yetkilendirme, bir kullanıcının bir Azure SQL Veritabanında gerçekleştirebileceklerini tanımlar ve bu durum, kullanıcı hesabınızın veritabanı [rolü üyelikleri](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) ve [nesne düzeyi izinleri](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) ile denetlenir. En iyi uygulama olarak, kullanıcılarınıza gerekli olan en düşük ayrıcalıkları tanımanız gerekir. Bağlantı kurmak için kullandığınız sunucu yöneticisi hesabı, veritabanında tüm işlemleri gerçekleştirme yetkisi olan db_owner rolünün üyesidir. Bu hesabı şema yükseltmeleri ve diğer yönetimsel işlemlerde kullanmak üzere saklayın. Uygulamanızdan veritabanına, uygulamanızın ihtiyaç duyduğu en düşük ayrıcalıklarla bağlanmak için daha sınırlı izinlere sahip olan "ApplicationUser" hesabını kullanın. Daha fazla bilgi için bkz. [Oturum açma bilgilerini yönetme](sql-database-manage-logins.md).

Genelde `master` veritabanına yalnızca yöneticilerin erişmesi gerekir. Tüm kullanıcı veritabanlarına rutin erişim, her bir veritabanında oluşturulan bağımsız yönetici olmayan veritabanı kullanıcılarıyla sağlanmalıdır. Bağımsız veritabanı kullanıcılarını tercih ettiğinizde `master` veritabanı için oturum açma bilgisi oluşturmanız gerekmez. Daha fazla bilgi için bkz. [Bağımsız Veritabanı Kullanıcıları - Veritabanınızı Taşınabilir Hale Getirme](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable).

İzinleri sınırlamak veya yükseltmek için kullanılabilen aşağıdaki özellikleri tanımanız gerekir:

- [Kimliğe Bürünme](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) ve [modül imzalama](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) ile izinler geçici olarak ve güvenli bir şekilde artırılabilir.
- [Satır Düzeyinde Güvenlik](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) ile bir kullanıcının erişebileceği satırlar sınırlandırılabilir.
- [Veri Maskeleme](sql-database-dynamic-data-masking-get-started.md) ile hassas verilerin kapsamı sınırlandırılabilir.
- [Saklı yordamlar](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) ile veritabanında gerçekleştirilebilecek eylemler sınırlandırılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- SQL Veritabanı güvenlik özelliklerine genel bakış için bkz. [SQL güvenliğine genel bakış](sql-database-security-overview.md).
- Güvenlik duvarı kuralları hakkında daha fazla bilgi edinmek için bkz. [güvenlik duvarı kuralları](sql-database-firewall-configure.md).
- Kullanıcılar ve oturum açma bilgileri hakkında bilgi almak için bkz. [Oturum açma bilgilerini yönetme](sql-database-manage-logins.md). 
- Proaktif izleme hakkında bir tartışma için bkz. [veritabanı denetimi](sql-database-auditing.md) ve [SQL veritabanı tehdit algılama](sql-database-threat-detection.md).
- Öğretici için bkz. [Azure SQL veritabanınızı güvenli hale getirme](sql-database-security-tutorial.md).
