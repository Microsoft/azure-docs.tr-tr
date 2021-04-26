---
title: Multi-Factor Authentication’ı yapılandırma
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics için SSMS ile çok yönlü bir kimlik doğrulamasını nasıl kullanacağınızı öğrenin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 4f90299daed46d06dad9ab37103e3b8f53763ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454379"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>SQL Server Management Studio ve Azure AD için Multi-Factor Authentication 'ı yapılandırma
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Bu makalede, Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasının (MFA) SQL Server Management Studio (SSMS) ile nasıl kullanılacağı gösterilmektedir. Azure AD MFA, SSMS veya SqlPackage.exe [Azure SQL veritabanı](sql-database-paas-overview.md), [Azure SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md) ve [Azure SYNAPSE Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)'e bağlanırken kullanılabilir. Multi-Factor Authentication 'a genel bakış için bkz. [SQL veritabanı Ile evrensel kimlik doğrulaması, SQL yönetilen örneği ve Azure Synapse (MFA IÇIN SSMS desteği)](../database/authentication-mfa-ssms-overview.md).

> [!IMPORTANT]
> Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE ' deki veritabanları, bu makalenin geri kalanında veritabanı olarak ve sunucu, Azure SQL veritabanı ve Azure SYNAPSE için veritabanlarını barındıran [sunucuya](logical-servers.md) başvurmaktadır.

## <a name="configuration-steps"></a>Yapılandırma adımları

1. **Azure Active Directory yapılandırma** -daha fazla bilgi için bkz. [Azure AD dizininizi yönetme](/previous-versions/azure/azure-services/hh967611(v=azure.100)), [Şirket içi kimliklerinizi Azure ACTIVE DIRECTORY ile tümleştirme](../../active-directory/hybrid/whatis-hybrid-identity.md), [Azure AD 'ye kendi etki alanı adınızı ekleme](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure artık Windows Server ACTIVE DIRECTORY ile Federasyonu destekliyor](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)ve [Windows PowerShell kullanarak Azure AD 'yi yönetme](/previous-versions/azure/jj151815(v=azure.100)).
2. **MFA 'Yı yapılandırma** -adım adım yönergeler için bkz. Azure [SQL veritabanı ve veri AMBARı ile](conditional-access-configure.md) [Azure AD Multi-Factor Authentication nedir?](../../active-directory/authentication/concept-mfa-howitworks.md), koşullu erişim (MFA). (Tam koşullu erişim bir Premium Azure Active Directory gerektirir. Standart bir Azure AD ile sınırlı MFA kullanılabilir.)
3. **Azure AD kimlik doğrulamasını yapılandırma** -adım adım yönergeler Için [Azure Active Directory KIMLIK doğrulaması kullanarak SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE 'a bağlanma](authentication-aad-overview.md)konusuna bakın.
4. **SSMS 'Yi indirme** -istemci bilgisayarda, [indirme SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)adresinden en son SSMS 'yi indirin.

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>SSMS ile evrensel kimlik doğrulaması kullanarak bağlanma

Aşağıdaki adımlarda, en son SSMS kullanılarak nasıl bağlanacağı gösterilmektedir.

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

1. Evrensel kimlik doğrulaması kullanarak bağlanmak için, SQL Server Management Studio (SSMS) içindeki **sunucuya Bağlan** iletişim kutusunda, **MFA desteği olan Active Directory-Universal**' i seçin. ( **Active Directory evrensel kimlik doğrulaması** ' nı görürseniz, en son SSMS sürümünde değilsiniz.)

   ![Sunucuya Bağlan iletişim kutusundaki bağlantı özellikleri sekmesinin ekran görüntüsü. "MyDatabase", veritabanına Bağlan açılır listesinde seçilidir.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. Azure Active Directory kimlik bilgileriyle **Kullanıcı adı** kutusunu, biçimde doldurun `user_name@domain.com` .

   ![Sunucu türü, sunucu adı, kimlik doğrulaması ve Kullanıcı adı için sunucuya Bağlan iletişim kutusunun ekran görüntüsü.](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. Konuk Kullanıcı olarak bağlanıyorsanız, SMS 18. x veya daha sonraki bir sürümü tarafından otomatik olarak tanıyacağından, Konuk kullanıcılar için AD etki alanı adını veya kiracı KIMLIĞI alanını doldurmanız artık gerekmez. Daha fazla bilgi için bkz. [SQL veritabanı, SQL yönetilen örneği ve Azure Synapse (MFA IÇIN SSMS desteği) Ile evrensel kimlik doğrulaması](../database/authentication-mfa-ssms-overview.md).

   ![Sunucuya Bağlan iletişim kutusundaki bağlantı özellikleri sekmesinin ekran görüntüsü. "MyDatabase", veritabanına Bağlan açılır listesinde seçilidir.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   Ancak, SSMS 17. x veya daha eski bir sürümle Konuk Kullanıcı olarak bağlanıyorsanız, **Seçenekler**' e tıklamanız ve **bağlantı özelliği** iletişim kutusunda, **ad etkı alanı adını veya Kiracı kimliği** kutusunu doldurmanız gerekir.

   ![Sunucu s 'de sunucuya Bağlan iletişim kutusundaki bağlantı özellikleri sekmesinin ekran görüntüsü. AD etki alanı adı veya kiracı KIMLIĞI özelliği seçeneği doldurulur.](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. **Seçenekler** ' i seçin ve **Seçenekler** iletişim kutusunda veritabanını belirtin. (Bağlı Kullanıcı bir Konuk Kullanıcı (yani joe@outlook.com ), kutuyu denetlemeniz ve geçerlI ad etki alanı adını ya da KIRACı kimliğini seçeneklerin bir parçası olarak eklemeniz gerekir. Bkz. [SQL veritabanı ve Azure SYNAPSE Analytics (MFA IÇIN SSMS desteği) Ile evrensel kimlik doğrulaması](../database/authentication-mfa-ssms-overview.md). Ardından **Bağlan**’a tıklayın.  
5. **Hesapta oturum açma** iletişim kutusu göründüğünde, Azure Active Directory kimliğinizin hesabını ve parolasını girin. Bir Kullanıcı Azure AD ile federe bir etki alanının parçasıysa parola gerekli değildir.

   ![Azure SQL veritabanı ve veri ambarı için hesap iletişim kutusunda oturum açma 'nın ekran görüntüsü. Hesap ve parola doldurulur.](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > MFA gerektirmeyen bir hesapla evrensel kimlik doğrulaması için bu noktaya bağlanırsınız. MFA gerektiren kullanıcılar için aşağıdaki adımlarla devam edin:
   >  

6. İki MFA kurulum iletişim kutusu görünebilir. Bu bir kerelik işlem MFA yönetici ayarına bağlıdır ve bu nedenle isteğe bağlı olabilir. MFA etkin bir etki alanı için bu adım bazen önceden tanımlanmıştır (örneğin, etki alanı kullanıcıların bir akıllı kart ve PIN kullanmasını gerektirir).

   ![Azure SQL veritabanı ve veri ambarı için hesap iletişim kutusunda ek güvenlik doğrulaması ayarlamak için bir istem ile oturum açma 'nın ekran görüntüsü.](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. İkinci olası bir zaman iletişim kutusu, kimlik doğrulama yönteminizin ayrıntılarını seçmenize olanak sağlar. Olası seçenekler yöneticiniz tarafından yapılandırılır.

   ![Kimlik doğrulama yöntemini seçme ve yapılandırma seçenekleriyle ek güvenlik doğrulama iletişim kutusunun ekran görüntüsü.](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. Azure Active Directory onaylama bilgilerini size gönderir. Doğrulama kodunu aldığınızda, **doğrulama kodunu girin** kutusuna girin ve **oturum aç**' a tıklayın.

   ![Azure SQL veritabanı ve veri ambarı için hesap iletişim kutusunda doğrulama kodu girmek için bir istem içeren ekran görüntüsü.](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

Doğrulama tamamlandığında SSMS, genellikle geçerli kimlik bilgilerini ve güvenlik duvarı erişimini önceden izleyerek bağlar.

## <a name="next-steps"></a>Sonraki adımlar

- Multi-Factor Authentication 'a genel bakış için bkz. [SQL veritabanı Ile evrensel kimlik doğrulaması, SQL yönetilen örneği ve Azure Synapse (MFA IÇIN SSMS desteği)](../database/authentication-mfa-ssms-overview.md).  
- Veritabanınıza başkalarının erişmesine izin verme: [SQL veritabanı kimlik doğrulaması ve yetkilendirme: erişim verme](logins-create-manage.md)  
- Başkalarının güvenlik duvarından bağlanmasına emin olun: [Azure Portal kullanarak sunucu düzeyinde güvenlik duvarı kuralı yapılandırma](./firewall-configure.md)  
- MFA kimlik doğrulamasıyla **Active Directory Universal** KULLANıRKEN, adal Izleme [SSMS 17,3](/sql/ssms/download-sql-server-management-studio-ssms)ile başlayarak kullanılabilir. Varsayılan olarak, **Azure hizmetleri**, **Azure Cloud**, **adal çıkış penceresi izleme düzeyi** altındaki **Araçlar**, **Seçenekler** menüsünü ve ardından **Görünüm** menüsünde **çıktıyı** etkinleştirerek, adal izlemeyi açabilirsiniz. İzlemeler **Azure Active Directory seçenek** belirlendiğinde çıkış penceresinde kullanılabilir.