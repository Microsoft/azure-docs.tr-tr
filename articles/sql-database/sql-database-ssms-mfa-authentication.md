---
title: Multi-Factor AAD kimlik doğrulamasını kullanma
description: Azure SQL veritabanı ve Azure SQL veri ambarı, Active Directory evrensel kimlik doğrulaması kullanarak SQL Server Management Studio (SSMS) bağlantılarını destekler.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/08/2018
ms.openlocfilehash: 7183193f3639ea809c6e7aa19af7844bd134111e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820913"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-sql-data-warehouse-ssms-support-for-mfa"></a>Azure SQL veritabanı ve Azure SQL veri ambarı ile Multi-Factor AAD kimlik doğrulamasını kullanma (MFA için SSMS desteği)
Azure SQL veritabanı ve Azure SQL veri ambarı, *Active Directory evrensel kimlik doğrulaması*kullanarak SQL Server Management Studio (SSMS) bağlantılarını destekler. Bu makalede çeşitli kimlik doğrulama seçenekleri arasındaki farklar ve ayrıca evrensel kimlik doğrulaması kullanımıyla ilişkili sınırlamalar ele alınmaktadır. 

**En son SSMS 'Yi indirin** -istemci bilgisayarda, [Download SQL Server Management Studio 'ten (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)en son SSMS sürümünü indirin. 


Bu makalede ele alınan tüm özellikler için en az 2017 Temmuz sürüm 17,2 kullanın.  En son bağlantı iletişim kutusu aşağıdaki görüntüye benzer görünmelidir:
 
  ![1mfa-Universal-Connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Kullanıcı adı kutusunu tamamlar.")  

## <a name="the-five-authentication-options"></a>Beş kimlik doğrulama seçeneği  

Active Directory evrensel kimlik doğrulaması, etkileşimli olmayan iki kimlik doğrulama yöntemini destekler:
    - `Active Directory - Password` kimlik doğrulaması
    - `Active Directory - Integrated` kimlik doğrulaması

Birçok farklı uygulamada (ADO.NET, JDCB, ODC vb.) kullanılabilecek iki etkileşimli olmayan kimlik doğrulama modeli vardır. Bu iki yöntem hiçbir koşulda açılan iletişim kutularına neden olmaz: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

Etkileşimli Yöntem de Azure Multi-Factor Authentication (MFA) hizmetini de destekler: 
- `Active Directory - Universal with MFA` 


Azure MFA, kullanıcıların oturum açmaya yönelik basit işlem taleplerini karşılarken, verilere ve uygulamalara erişimi korumaya da yardımcı olur. Kolay doğrulama seçenekleriyle (telefon araması, SMS mesajı, PIN ile akıllı kartlar veya mobil uygulama bildirimi), kullanıcıların tercih ettikleri yöntemi seçebilmesine olanak tanıyan güçlü kimlik doğrulaması sağlar. Azure AD ile etkileşimli MFA, doğrulama için bir açılır iletişim kutusu ile sonuçlanabilir.

Multi-Factor Authentication açıklaması için bkz. [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md).
Yapılandırma adımları için bkz. [SQL Server Management Studio Için Azure SQL veritabanı Multi-Factor Authentication 'ı yapılandırma](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD etki alanı adı veya kiracı KIMLIĞI parametresi   

[SSMS sürümü 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)' den başlayarak, Konuk Kullanıcı olarak diğer Azure Active Directory 'lerden Geçerli Active Directory içeri aktarılan kullanıcılar, BAĞLANDıKLARıNDA Azure AD etki alanı adını veya kiracı kimliğini sağlayabilir. Konuk kullanıcılar, diğer Azure reklamları, outlook.com, hotmail.com, live.com gibi Microsoft hesaplarından veya gmail.com gibi diğer hesaplarla davet edilen kullanıcıları içerir. Bu bilgiler, **MFA kimlik doğrulamasıyla evrensel Active Directory** , doğru kimlik doğrulama yetkilisini belirlemesine izin verir. Bu seçenek ayrıca, outlook.com, hotmail.com, live.com veya MSA olmayan hesaplar gibi Microsoft hesaplarını (MSA) desteklemek için de gereklidir. Evrensel kimlik doğrulaması kullanarak kimlik doğrulaması yapmak isteyen tüm bu kullanıcıların Azure AD etki alanı adını veya kiracı KIMLIĞINI girmesi gerekir. Bu parametre, Azure Server ile bağlantılı olan geçerli Azure AD etki alanı adını/kiracı KIMLIĞINI temsil eder. Örneğin, Azure Server, Kullanıcı `joe@contosodev.onmicrosoft.com` Azure AD etki alanı `contosodev.onmicrosoft.com`tarafından içeri aktarılan bir kullanıcı olarak barındırıldığı `contosotest.onmicrosoft.com` Azure AD etki alanı ile ilişkiliyse, bu kullanıcının kimliğini doğrulamak için gereken etki alanı adı `contosotest.onmicrosoft.com`. Kullanıcı Azure AD 'nin Azure Server 'a bağlı olduğu yerel bir kullanıcı olduğunda ve bir MSA hesabı olmadığında, etki alanı adı veya kiracı KIMLIĞI gerekmez. Parametreyi (SSMS sürüm 17,2 ' den başlayarak) girmek için, **veritabanına Bağlan** iletişim kutusunda, iletişim kutusunu doldurun, MFA kimlik doğrulamasıyla **Active Directory-Universal** ' i seçin, **Seçenekler**' e tıklayın, **Kullanıcı adını doldurun** kutusunu ve ardından **bağlantı özellikleri** sekmesine tıklayın. **ad etki alanı adını veya Kiracı kimliği** kutusunu işaretleyin ve etki alanı adı (**contosotest.onmicrosoft.com**) ya da kiracı kimliğinin GUID 'si gibi kimlik doğrulama yetkilisi sağlayın.  
   ![MFA-kiracı-SSMS](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

SSMS 18. x veya sonraki bir sürümünü çalıştırıyorsanız, bu AD etki alanı adı veya kiracı KIMLIĞI artık Konuk kullanıcılar için gerekli değildir çünkü 18. x veya daha sonraki bir sürümü otomatik olarak tanır.

   ![MFA-kiracı-SSMS](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD iş 'e iş desteği   
Azure AD B2B senaryolarında Konuk Kullanıcı olarak desteklenen Azure AD kullanıcıları (bkz. [Azure B2B Işbirliği nedir),](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)SQL VERITABANı ve SQL veri ambarı 'na yalnızca GEÇERLI Azure AD 'de oluşturulan ve Transact-SQL kullanılarak el ile eşlenmiş bir grup üyesinin parçası olarak bağlanabilir belirli bir veritabanında `CREATE USER` deyimleri. Örneğin, `steve@gmail.com` Azure AD `contosotest` (Azure AD etki alanı `contosotest.onmicrosoft.com`) olarak davet edildiyseniz, `steve@gmail.com` üyesini içeren Azure AD 'de `usergroup` gibi bir Azure AD grubunun oluşturulması gerekir. Bu durumda, bir Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` ekstresi yürüterek, bu grup Azure AD SQL Yöneticisi veya Azure AD DBO tarafından belirli bir veritabanı (yani, MyDatabase) için oluşturulmalıdır. Veritabanı kullanıcısı oluşturulduktan sonra, Kullanıcı `steve@gmail.com` SSMS kimlik doğrulama seçeneği `Active Directory – Universal with MFA support`kullanarak `MyDatabase` oturum açabilir. UserGroup, varsayılan olarak yalnızca Connect iznine ve normal şekilde verilmesi gereken daha fazla veri erişimine sahiptir. Konuk Kullanıcı olarak `steve@gmail.com` kullanıcının kutuyu denetlemesi ve SSMS **bağlantı özelliği** iletişim kutusuna `contosotest.onmicrosoft.com` ad etki alanı adını eklemesi gerektiğini unutmayın. **Ad etki alanı adı veya KIRACı kimliği** SEÇENEĞI yalnızca MFA bağlantı seçenekleriyle Universal için desteklenir, aksi halde gri olur.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>SQL veritabanı ve SQL veri ambarı için evrensel kimlik doğrulama sınırlamaları
- SSMS ve SqlPackage. exe, şu anda Active Directory evrensel kimlik doğrulaması aracılığıyla MFA için etkinleştirilen tek araçlardır.
- SSMS sürüm 17,2, MFA ile evrensel kimlik doğrulaması kullanarak çok kullanıcılı eşzamanlı erişimi destekler. Sürüm 17,0 ve 17,1, evrensel kimlik doğrulaması kullanan SSMS örnekleri için tek bir Azure Active Directory hesabıyla bir oturum açmayı kısıtlamalıdır. Başka bir Azure AD hesabı olarak oturum açmak için SSMS 'nin başka bir örneğini kullanmanız gerekir. (Bu kısıtlama Active Directory evrensel kimlik doğrulaması ile sınırlıdır; Active Directory parola kimlik doğrulaması, Active Directory tümleşik kimlik doğrulaması veya SQL Server kimlik doğrulaması kullanarak farklı sunucularda oturum açabilirsiniz).
- SSMS, Nesne Gezgini, sorgu Düzenleyicisi ve sorgu deposu görselleştirmesi için Active Directory evrensel kimlik doğrulamasını destekler.
- SSMS sürüm 17,2, veri veritabanını dışa/ayıklamayı/dağıtmayı sağlayan DacFx Sihirbazı desteği sağlar. Belirli bir kullanıcının kimliği, evrensel kimlik doğrulaması kullanılarak ilk kimlik doğrulama iletişim kutusu aracılığıyla doğrulandıktan sonra, DacFx Sihirbazı diğer tüm kimlik doğrulama yöntemleri için aynı şekilde çalışır.
- SSMS Tablo Tasarımcısı evrensel kimlik doğrulamasını desteklemez.
- Active Directory evrensel kimlik doğrulaması için ek yazılım gereksinimi yoktur, ancak desteklenen bir SSMS sürümü kullanmanız gerekir.  
- Evrensel kimlik doğrulaması için Active Directory Authentication Library (ADAL) sürümü en son ADAL. dll 3.13.9 kullanılabilir yayımlanmış sürümü olarak güncelleştirildi. Bkz. [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Sonraki adımlar

- Yapılandırma adımları için bkz. [SQL Server Management Studio Için Azure SQL veritabanı Multi-Factor Authentication 'ı yapılandırma](sql-database-ssms-mfa-authentication-configure.md).
- Veritabanınıza başkalarının erişmesine izin verme: [SQL veritabanı kimlik doğrulaması ve yetkilendirme: erişim verme](sql-database-manage-logins.md)  
- Başkalarının güvenlik duvarından bağlanmasına emin olun: [Azure Portal kullanarak Azure SQL veritabanı sunucu düzeyi güvenlik duvarı kuralı yapılandırma](sql-database-configure-firewall-settings.md)  
- [SQL Veritabanı veya SQL Veri Ambarı ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [BACPAC dosyasını yeni bir Azure SQL Veritabanı’na içeri aktarma](../sql-database/sql-database-import.md)  
- [Azure SQL Veritabanı’nı bir BACPAC dosyasına dışarı aktarma](../sql-database/sql-database-export.md)  
- C#arabirim [ıüniversalauthprovider arabirimi](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- MFA kimlik doğrulamasıyla **Active Directory Universal** KULLANıRKEN, adal Izleme [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)ile başlayarak kullanılabilir. Varsayılan olarak, **Azure hizmetleri**, **Azure Cloud**, **adal çıkış penceresi izleme düzeyi**altındaki **Araçlar**, **Seçenekler** menüsünü ve ardından **Görünüm** menüsünde **çıktıyı** etkinleştirerek, adal izlemeyi açabilirsiniz. İzlemeler **Azure Active Directory seçenek**belirlendiğinde çıkış penceresinde kullanılabilir.  
