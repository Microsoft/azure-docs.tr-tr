---
title: Multi-Factor Authentication 'ı yapılandırma
description: SQL veritabanı ve SQL veri ambarı için SSMS ile çok yönlü bir kimlik doğrulamasını nasıl kullanacağınızı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 7849f6d391cad7e973babfa97f2859b9df40f23e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820888"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>SQL Server Management Studio ve Azure AD için Multi-Factor Authentication 'ı yapılandırma

Bu konu, Azure Active Directory Multi-Factor Authentication (MFA) SQL Server Management Studio ile nasıl kullanacağınızı gösterir. SSMS veya SqlPackage. exe, Azure [SQL veritabanı](sql-database-technical-overview.md) ve [SQL veri ambarı](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)'NA bağlanırken Azure AD MFA kullanılabilir. Azure SQL veritabanı Multi-Factor Authentication 'a genel bakış için bkz. [SQL veritabanı ve SQL veri ambarı Ile evrensel kimlik doğrulaması (MFA IÇIN SSMS desteği)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Bu konu başlığı, Azure SQL sunucusunun yanı sıra Azure SQL sunucusu üzerinde oluşturulmuş olan SQL Veritabanı ve SQL Veri Ambarı veritabanları için de geçerlidir. Kolaylık açısından, hem SQL Veritabanı hem de SQL Veri Ambarı için SQL Veritabanı terimi kullanılmaktadır.

## <a name="configuration-steps"></a>Yapılandırma adımları

1. **Azure Active Directory yapılandırma** -daha fazla bilgi için bkz. [Azure AD dizininizi yönetme](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Şirket içi kimliklerinizi Azure ACTIVE DIRECTORY ile tümleştirme](../active-directory/hybrid/whatis-hybrid-identity.md), [Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) ['ye kendi etki alanı adınızı ekleme, Microsoft Azure artık Windows Server Active Directory ile Federasyonu destekler](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)ve [Windows PowerShell kullanarak Azure AD 'yi yönetebilir](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **MFA 'Yı yapılandırma** -adım adım yönergeler için bkz. Azure [SQL veritabanı ve veri ambarı ile](sql-database-conditional-access.md) [Azure Multi-Factor Authentication nedir?](../active-directory/authentication/multi-factor-authentication.md), koşullu erişim (MFA). (Tam koşullu erişim bir Premium Azure Active Directory (Azure AD) gerektirir. Standart bir Azure AD ile sınırlı MFA kullanılabilir.)
3. **SQL veritabanı veya SQL veri ambarı 'Nı Azure AD kimlik doğrulaması Için yapılandırma** -adım adım yönergeler için, bkz. [Azure Active Directory KIMLIK doğrulaması kullanarak SQL VERITABANı 'Na veya SQL Data Warehouse 'a bağlanma](sql-database-aad-authentication.md).
4. **SSMS 'Yi indirme** -istemci bilgisayarda, [indirme SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)adresinden en son SSMS 'yi indirin. Bu konudaki tüm özellikler için en az 2017 Temmuz sürüm 17,2 kullanın.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>SSMS ile evrensel kimlik doğrulaması kullanarak bağlanma

Aşağıdaki adımlarda, en son SSMS kullanılarak SQL veritabanı veya SQL veri ambarı 'na nasıl bağlanayapılacağı gösterilmektedir.

1. Evrensel kimlik doğrulaması kullanarak bağlanmak için, **sunucuya Bağlan** ILETIŞIM kutusunda **MFA desteğiyle Active Directory-Universal**' ı seçin. ( **Active Directory evrensel kimlik doğrulaması** ' nı görürseniz, en son SSMS sürümünde değilsiniz.)  
   ![1mfa-Universal-Connect][1]  
2. Azure Active Directory kimlik bilgileriyle **Kullanıcı adı** kutusunu `user_name@domain.com`biçiminde doldurun.  
   ![1mfa-Universal-Connect-User](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Konuk Kullanıcı olarak bağlanıyorsanız, SMS 18. x veya daha sonraki bir sürümü tarafından otomatik olarak tanıyacağından, Konuk kullanıcılar için AD etki alanı adını veya kiracı KIMLIĞI alanını doldurmanız artık gerekmez. Daha fazla bilgi için bkz. [SQL veritabanı ve SQL veri ambarı Ile evrensel kimlik doğrulaması (MFA IÇIN SSMS desteği)](sql-database-ssms-mfa-authentication.md).
   ![MFA-kiracı-SSMS](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   Ancak, SSMS 17. x veya daha eski bir sürümle Konuk Kullanıcı olarak bağlanıyorsanız, **Seçenekler**' e tıklamanız ve **bağlantı özelliği** iletişim kutusunda, **ad etkı alanı adını veya Kiracı kimliği** kutusunu doldurmanız gerekir.
   ![MFA-kiracı-SSMS](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. SQL veritabanı ve SQL veri ambarı için her zamanki gibi **Seçenekler** ' e tıklamanız ve **Seçenekler** iletişim kutusunda veritabanını belirtmeniz gerekir. (Bağlı Kullanıcı bir Konuk Kullanıcı (yani joe@outlook.com), kutuyu denetlemeniz ve geçerli AD etki alanı adını ya da kiracı KIMLIĞINI seçeneklerin bir parçası olarak eklemeniz gerekir. Bkz. [SQL veritabanı ve SQL veri ambarı Ile evrensel kimlik doğrulaması (MFA IÇIN SSMS desteği)](sql-database-ssms-mfa-authentication.md). Ardından **Bağlan**’a tıklayın.  
5. **Hesapta oturum açma** iletişim kutusu göründüğünde, Azure Active Directory kimliğinizin hesabını ve parolasını girin. Bir Kullanıcı Azure AD ile federe bir etki alanının parçasıysa parola gerekli değildir.  
   ![2mfa-oturum açma][2]  

   > [!NOTE]
   > MFA gerektirmeyen bir hesapla evrensel kimlik doğrulaması için bu noktaya bağlanırsınız. MFA gerektiren kullanıcılar için aşağıdaki adımlarla devam edin:
   >  
   
6. İki MFA kurulum iletişim kutusu görünebilir. Bu bir kerelik işlem MFA yönetici ayarına bağlıdır ve bu nedenle isteğe bağlı olabilir. MFA etkin bir etki alanı için bu adım bazen önceden tanımlanmıştır (örneğin, etki alanı kullanıcıların bir akıllı kart ve PIN kullanmasını gerektirir).  
   ![3mfa-kurulum][3]  
7. İkinci olası bir zaman iletişim kutusu, kimlik doğrulama yönteminizin ayrıntılarını seçmenize olanak sağlar. Olası seçenekler yöneticiniz tarafından yapılandırılır.  
   ![4mfa-doğrula-1][4]  
8. Azure Active Directory onaylama bilgilerini size gönderir. Doğrulama kodunu aldığınızda, **doğrulama kodunu girin** kutusuna girin ve **oturum aç**' a tıklayın.  
   ![5mfa-Verify-2][5]  

Doğrulama tamamlandığında SSMS, genellikle geçerli kimlik bilgilerini ve güvenlik duvarı erişimini önceden izleyerek bağlar.

## <a name="next-steps"></a>Sonraki adımlar

- Azure SQL veritabanı Multi-Factor Authentication 'a genel bakış için bkz. [SQL veritabanı ve SQL veri ambarı Ile evrensel kimlik doğrulaması (MFA IÇIN SSMS desteği)](sql-database-ssms-mfa-authentication.md).  
- Veritabanınıza başkalarının erişmesine izin verme: [SQL veritabanı kimlik doğrulaması ve yetkilendirme: erişim verme](sql-database-manage-logins.md)  
- Başkalarının güvenlik duvarından bağlanmasına emin olun: [Azure Portal kullanarak Azure SQL veritabanı sunucu düzeyi güvenlik duvarı kuralı yapılandırma](sql-database-configure-firewall-settings.md)  
- MFA kimlik doğrulamasıyla **Active Directory Universal** KULLANıRKEN, adal Izleme [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)ile başlayarak kullanılabilir. Varsayılan olarak, **Azure hizmetleri**, **Azure Cloud**, **adal çıkış penceresi izleme düzeyi**altındaki **Araçlar**, **Seçenekler** menüsünü ve ardından **Görünüm** menüsünde **çıktıyı** etkinleştirerek, adal izlemeyi açabilirsiniz. İzlemeler **Azure Active Directory seçenek**belirlendiğinde çıkış penceresinde kullanılabilir.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

