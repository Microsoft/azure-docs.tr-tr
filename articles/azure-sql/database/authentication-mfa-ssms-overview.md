---
title: Multi-Factor Azure Active Directory kimlik doğrulamasını kullanma
description: Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics, Active Directory evrensel kimlik doğrulaması kullanarak SQL Server Management Studio (SSMS) bağlantılarını destekler.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/28/2020
tags: azure-synapse
ms.openlocfilehash: 9afad44bcf67478a81e75c17d0ff8ffc6d8c65aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94841142"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Multi-Factor Azure Active Directory kimlik doğrulamasını kullanma
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics, MFA kimlik doğrulamasıyla *Azure Active Directory-Universal* kullanarak [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) bağlantılarını destekler. Bu makalede çeşitli kimlik doğrulama seçenekleri arasındaki farklar ve ayrıca Azure SQL için Azure Active Directory (Azure AD) içinde evrensel kimlik doğrulaması kullanmayla ilişkili sınırlamalar ele alınmaktadır.

**En son SSMS 'Yi indirin** -istemci bilgisayarda, [Download SQL Server Management Studio 'ten (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)en son SSMS sürümünü indirin. 

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]


Bu makalede ele alınan tüm özellikler için en az 2017 Temmuz sürüm 17,2 kullanın. En son bağlantı iletişim kutusu aşağıdaki görüntüye benzer görünmelidir:

  ![Sunucu türü, sunucu adı ve kimlik doğrulaması ayarlarını gösteren SQL Server Management Studio sunucuya Bağlan iletişim kutusunun ekran görüntüsü.](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png)

## <a name="authentication-options"></a>Kimlik doğrulaması seçenekleri

Azure AD için etkileşimli olmayan iki kimlik doğrulama modeli vardır. Bu, birçok farklı uygulamada (ADO.NET, JDCB, ODC vb.) kullanılabilir. Bu iki yöntem hiçbir koşulda açılan iletişim kutularına neden olmaz:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

Azure AD Multi-Factor Authentication (MFA) de destekleyen etkileşimli yöntem şunlardır: 

- `Azure Active Directory - Universal with MFA`

Azure AD MFA, kullanıcıların oturum açmaya yönelik basit işlem taleplerini karşılarken, verilere ve uygulamalara erişimi korumaya da yardımcı olur. Kolay doğrulama seçenekleriyle (telefon araması, SMS mesajı, PIN ile akıllı kartlar veya mobil uygulama bildirimi), kullanıcıların tercih ettikleri yöntemi seçebilmesine olanak tanıyan güçlü kimlik doğrulaması sağlar. Azure AD ile etkileşimli MFA, doğrulama için bir açılır iletişim kutusu ile sonuçlanabilir.

Azure AD Multi-Factor Authentication 'nin bir açıklaması için bkz. [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).
Yapılandırma adımları için bkz. [SQL Server Management Studio için Azure SQL Veritabanı çok faktörlü kimlik doğrulamasını yapılandırma](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD etki alanı adı veya kiracı KIMLIĞI parametresi

[SSMS sürüm 17](/sql/ssms/download-sql-server-management-studio-ssms)' den başlayarak, mevcut Azure AD 'ye, diğer Azure Active Directory 'lerden Konuk Kullanıcı olarak içeri aktarılan kullanıcılar, BAĞLANDıKLARıNDA Azure AD etki alanı adını veya kiracı kimliğini sağlayabilir. Konuk kullanıcılar, diğer Azure reklamları, outlook.com, hotmail.com, live.com gibi Microsoft hesaplarından veya gmail.com gibi diğer hesaplarla davet edilen kullanıcıları içerir. Bu bilgiler `Azure Active Directory - Universal with MFA` kimlik doğrulamanın doğru kimlik doğrulama yetkilisini belirlemesine izin verir. Bu seçenek ayrıca, outlook.com, hotmail.com, live.com veya MSA olmayan hesaplar gibi Microsoft hesaplarını (MSA) desteklemek için de gereklidir. 

Evrensel kimlik doğrulaması kullanarak kimlik doğrulaması yapmak isteyen tüm konuk kullanıcıların Azure AD etki alanı adını veya kiracı KIMLIĞINI girmesi gerekir. Bu parametre, Azure SQL mantıksal sunucusunun ilişkilendirildiği geçerli Azure AD etki alanı adını veya kiracı KIMLIĞINI temsil eder. Örneğin, SQL mantıksal sunucusu Azure AD etki alanı ile ilişkiliyse `contosotest.onmicrosoft.com` , Kullanıcı `joe@contosodev.onmicrosoft.com` Azure AD etki alanından içeri aktarılmış bir kullanıcı olarak barındırılır, `contosodev.onmicrosoft.com` Bu kullanıcının kimliğini doğrulamak için gereken etki alanı adı `contosotest.onmicrosoft.com` . Kullanıcı, Azure AD 'nin SQL mantıksal sunucusuyla ilişkili yerel bir kullanıcısı olduğunda ve bir MSA hesabı olmadığında, etki alanı adı veya kiracı KIMLIĞI gerekmez. Parametreyi (SSMS sürüm 17,2 ' den başlayarak) girmek için:


1. SSMS 'de bir bağlantı açın. Sunucu adınızı girin ve MFA kimlik doğrulamasıyla **Azure Active Directory-Universal** ' ı seçin. Oturum açmak istediğiniz **Kullanıcı adını** ekleyin.
1. **Seçenekler** kutusunu seçin ve **bağlantı özellikleri** sekmesine gidin. **Veritabanına Bağlan** iletişim kutusunda veritabanınızın iletişim kutusunu doldurun. **Ad etki alanı adı veya KIRACı kimliği** kutusunu işaretleyin ve kimlik doğrulaması yetkilisini (etki alanı adı (**contosotest.onmicrosoft.com**) veya kiracı kimliğinin GUID 'si gibi) belirtin. 

   ![Veritabanına bağlanma ve AD etki alanı adına veya kiracı KIMLIĞINE yönelik ayarları vurgulayan bağlantı özellikleri sekmesinin ekran görüntüsü.](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

SSMS 18. x veya üzeri bir sürümü çalıştırıyorsanız, bu AD etki alanı adı veya kiracı KIMLIĞI artık Konuk kullanıcılar için gerekli değildir çünkü 18. x veya daha sonraki bir sürümü otomatik olarak tanır.

   ![Veritabanına Bağlan iletişim kutusundaki bağlantı özellikleri sekmesinin ekran görüntüsü. veritabanı bağlantısı alanında "MyDatabase" seçilidir.](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD iş 'e iş desteği

> [!IMPORTANT]
> Konuk kullanıcıların Azure SQL veritabanı 'na bağlanmasına yönelik destek, SQL yönetilen örneği ve bir grubun parçası olmadan Azure SYNAPSE Şu anda **genel önizlemededir**. Daha fazla bilgi için bkz. [Azure AD Konuk kullanıcıları oluşturma ve Azure AD yöneticisi olarak ayarlama](authentication-aad-guest-users.md).

Azure AD B2B senaryolarında Konuk Kullanıcı olarak desteklenen Azure AD kullanıcıları (bkz. [Azure B2B Işbirliği nedir](../../active-directory/external-identities/what-is-b2b.md)), yalnızca ILIŞKILI Azure AD 'de oluşturulan bir grubun üyelerinin PARÇASı olarak SQL Database ve Azure SYNAPSE 'a bağlanabilir ve belirli BIR veritabanındaki [Kullanıcı oluşturma (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) ifadesini kullanarak el ile eşleştirilir. Örneğin, Azure AD `steve@gmail.com` 'ye `contosotest` (Azure AD etki alanı ile) davet edildiyseniz, `contosotest.onmicrosoft.com` `usergroup` üyeyi içeren Azure AD 'de, gibi bir Azure AD grubunun oluşturulması gerekir `steve@gmail.com` . Daha sonra, `MyDatabase` Transact-SQL ifadesini yürüterek, bu grup bir Azure ad SQL Yöneticisi veya Azure AD dbo tarafından belirli bir veritabanı için oluşturulmalıdır (örneğin,) `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . 

Veritabanı kullanıcısı oluşturulduktan sonra, Kullanıcı `steve@gmail.com` `MyDatabase` SSMS kimlik doğrulama seçeneğini kullanarak oturum açabilir `Azure Active Directory – Universal with MFA` . Varsayılan olarak, `usergroup` yalnızca Connect iznine sahiptir. Daha fazla veri erişiminin, yeterli ayrıcalığa sahip bir kullanıcı tarafından veritabanında [verilmesi](/sql/t-sql/statements/grant-transact-sql) gerekir. 

> [!NOTE]
> SSMS 17. x için, `steve@gmail.com` Konuk Kullanıcı olarak kullanarak, **ad etki alanı adını veya Kiracı kimliği** kutusunu denetlemeniz ve `contosotest.onmicrosoft.com` **bağlantı özelliği** iletişim kutusunda ad etki alanı adını eklemeniz gerekir. **Ad etki alanı adı veya KIRACı kimliği** SEÇENEĞI yalnızca MFA kimlik doğrulamasıyla **Azure Active Directory Universal** için desteklenir. Aksi halde, onay kutusu gri renkte olur.

## <a name="universal-authentication-limitations"></a>Evrensel kimlik doğrulaması sınırlamaları

- SSMS ve SqlPackage.exe, şu anda Active Directory evrensel kimlik doğrulaması aracılığıyla MFA için etkinleştirilen tek araçlardır.
- SSMS sürüm 17,2, MFA ile evrensel kimlik doğrulaması kullanarak çok kullanıcılı eşzamanlı erişimi destekler. SSMS sürüm 17,0 ve 17,1 için, araç, evrensel kimlik doğrulaması kullanan SSMS örnekleri için tek bir Azure Active Directory hesabıyla bir oturum açmayı kısıtlar. Başka bir Azure AD hesabı olarak oturum açmak için SSMS 'nin başka bir örneğini kullanmanız gerekir. Bu kısıtlama Active Directory evrensel kimlik doğrulaması ile sınırlıdır; `Azure Active Directory - Password` kimlik doğrulaması, kimlik doğrulama veya kimlik doğrulama kullanarak farklı bir sunucuda oturum açabilirsiniz `Azure Active Directory - Integrated` `SQL Server Authentication` .
- SSMS, Nesne Gezgini, sorgu Düzenleyicisi ve sorgu deposu görselleştirmesi için Active Directory evrensel kimlik doğrulamasını destekler.
- SSMS sürüm 17,2, veri veritabanını dışa/ayıklamayı/dağıtmayı sağlayan DacFx Sihirbazı desteği sağlar. Belirli bir kullanıcının kimliği, evrensel kimlik doğrulaması kullanılarak ilk kimlik doğrulama iletişim kutusu aracılığıyla doğrulandıktan sonra, DacFx Sihirbazı diğer tüm kimlik doğrulama yöntemleri için aynı şekilde çalışır.
- SSMS Tablo Tasarımcısı evrensel kimlik doğrulamasını desteklemez.
- Active Directory evrensel kimlik doğrulaması için ek yazılım gereksinimi yoktur, ancak desteklenen bir SSMS sürümü kullanmanız gerekir.  
- Evrensel kimlik doğrulaması için en son Active Directory Authentication Library (ADAL) sürümü için şu bağlantıya bakın: [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Sonraki adımlar

- Yapılandırma adımları için bkz. [SQL Server Management Studio için Azure SQL Veritabanı çok faktörlü kimlik doğrulamasını yapılandırma](authentication-mfa-ssms-configure.md).
- Veritabanınıza başkalarının erişmesine izin verme: [SQL veritabanı kimlik doğrulaması ve yetkilendirme: erişim verme](logins-create-manage.md)  
- Başkalarının güvenlik duvarından bağlanmasına emin olun: [Azure Portal kullanarak sunucu düzeyinde güvenlik duvarı kuralı yapılandırma](firewall-configure.md)  
- [Azure Active Directory kimlik doğrulamasını SQL veritabanı veya Azure SYNAPSE ile yapılandırma ve yönetme](authentication-aad-configure.md)
- [Azure AD konuk kullanıcıları oluşturma ve bir Azure AD yöneticisi olarak ayarlama](authentication-aad-guest-users.md) 
- [Microsoft SQL Server Data-Tier uygulama çerçevesi (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](/sql/tools/sqlpackage)  
- [BACPAC dosyasını yeni bir veritabanına aktarma](database-import.md)  
- [BACPAC dosyasından veritabanını dışarı aktarma](database-export.md)  
- C# arabirimi [ıüniversalauthprovider arabirimi](/dotnet/api/microsoft.sqlserver.dac.iuniversalauthprovider)  
- MFA kimlik doğrulamasıyla **Azure Active Directory Universal** KULLANıRKEN, adal Izleme [SSMS 17,3](/sql/ssms/download-sql-server-management-studio-ssms)ile başlayarak kullanılabilir. Varsayılan olarak, **Azure hizmetleri**, **Azure Cloud**, **adal çıkış penceresi izleme düzeyi** altındaki **Araçlar**, **Seçenekler** menüsünü ve ardından **Görünüm** menüsünde **çıktıyı** etkinleştirerek, adal izlemeyi açabilirsiniz. İzlemeler **Azure Active Directory seçenek** belirlendiğinde çıkış penceresinde kullanılabilir.