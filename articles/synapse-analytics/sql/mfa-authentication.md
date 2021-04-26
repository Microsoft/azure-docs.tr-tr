---
title: Multi-Factor AAD kimlik doğrulamasını kullanma
description: Active Directory evrensel kimlik doğrulaması kullanarak SQL Server Management Studio (SSMS) üzerinden SQL destek bağlantılarını SYNAPSE.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.custom: has-adal-ref
ms.openlocfilehash: 7b3b8aae1345339dc34137550f3fe4c5be915ae5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672721"
---
# <a name="use-multi-factor-aad-authentication-with-synapse-sql-ssms-support-for-mfa"></a>Multi-Factor AAD kimlik doğrulamasını SYNAPSE SQL ile kullanma (MFA için SSMS desteği)

*Active Directory evrensel kimlik doğrulaması* kullanarak SQL Server Management Studio (SSMS) üzerinden SQL destek bağlantılarını SYNAPSE. 

Bu makalede çeşitli kimlik doğrulama seçenekleri arasındaki farklar ve ayrıca evrensel kimlik doğrulaması kullanımıyla ilişkili sınırlamalar ele alınmaktadır. 

**En son SSMS 'Yi indirin** -istemci bilgisayarda, [Download SQL Server Management Studio 'ten (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?view=azure-sqldw-latest&preserve-view=true)en son SSMS sürümünü indirin.

Bu makalede ele alınan tüm özellikler için en az 2017 Temmuz sürüm 17,2 kullanın.  En son bağlantı iletişim kutusu aşağıdaki görüntüye benzer görünmelidir:

![Ekran görüntüsü, sunucu adı ve kimlik doğrulama seçeneğini seçebileceğiniz sunucuya Bağlan iletişim kutusunu gösterir.](./media/mfa-authentication/1mfa-universal-connect.png "Kullanıcı adı kutusunu tamamlar.")  

## <a name="the-five-authentication-options"></a>Beş kimlik doğrulama seçeneği  

Active Directory evrensel kimlik doğrulaması, etkileşimli olmayan iki kimlik doğrulama yöntemini destekler:
    - `Active Directory - Password` yetkilendirmesi
    - `Active Directory - Integrated` yetkilendirmesi

Birçok farklı uygulamada (ADO.NET, JDCB, ODC vb.) kullanılabilecek iki etkileşimli olmayan kimlik doğrulama modeli vardır. Bu iki yöntem hiçbir koşulda açılan iletişim kutularına neden olmaz:

- `Active Directory - Password`
- `Active Directory - Integrated`

Etkileşimli Yöntem ayrıca Azure AD Multi-Factor Authentication (MFA) de desteklenir:

- `Active Directory - Universal with MFA`

Azure AD MFA, kullanıcıların oturum açmaya yönelik basit işlem taleplerini karşılarken, verilere ve uygulamalara erişimi korumaya da yardımcı olur. Kolay doğrulama seçenekleriyle (telefon araması, SMS mesajı, PIN ile akıllı kartlar veya mobil uygulama bildirimi), kullanıcıların tercih ettikleri yöntemi seçebilmesine olanak tanıyan güçlü kimlik doğrulaması sağlar. Azure AD ile etkileşimli MFA, doğrulama için bir açılır iletişim kutusu ile sonuçlanabilir.

Multi-Factor Authentication açıklaması için bkz. [Multi-Factor Authentication](../../active-directory/authentication//concept-mfa-howitworks.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD etki alanı adı veya kiracı KIMLIĞI parametresi

[SSMS sürümü 17](/sql/ssms/download-sql-server-management-studio-ssms?view=azure-sqldw-latest&preserve-view=true)' den başlayarak, Konuk Kullanıcı olarak diğer Azure Active Directory 'lerden Geçerli Active Directory içeri aktarılan kullanıcılar, BAĞLANDıKLARıNDA Azure AD etki alanı adını veya kiracı kimliğini sağlayabilir. 

Konuk kullanıcılar, diğer Azure reklamları, outlook.com, hotmail.com, live.com gibi Microsoft hesaplarından veya gmail.com gibi diğer hesaplarla davet edilen kullanıcıları içerir. Bu bilgiler, **MFA kimlik doğrulamasıyla evrensel Active Directory** , doğru kimlik doğrulama yetkilisini belirlemesine izin verir. Bu seçenek ayrıca, outlook.com, hotmail.com, live.com veya MSA olmayan hesaplar gibi Microsoft hesaplarını (MSA) desteklemek için de gereklidir. 

Evrensel kimlik doğrulaması kullanarak kimlik doğrulaması yapmak isteyen tüm bu kullanıcıların Azure AD etki alanı adını veya kiracı KIMLIĞINI girmesi gerekir. Bu parametre, Azure Server ile bağlantılı olan geçerli Azure AD etki alanı adını/kiracı KIMLIĞINI temsil eder. 

Örneğin, Azure Server, `contosotest.onmicrosoft.com` kullanıcının `joe@contosodev.onmicrosoft.com` Azure AD etki alanından içeri aktarılmış bir kullanıcı olarak BARıNDıRıLDıĞı Azure AD etki alanı ile ilişkiliyse `contosodev.onmicrosoft.com` , bu kullanıcının kimliğini doğrulamak için gereken etki alanı adı `contosotest.onmicrosoft.com` . 

Kullanıcı Azure AD 'nin Azure Server 'a bağlı olduğu yerel bir kullanıcı olduğunda ve bir MSA hesabı olmadığında, etki alanı adı veya kiracı KIMLIĞI gerekmez. 

Parametreyi (SSMS sürüm 17,2 ' den başlayarak) girmek için, **veritabanına Bağlan** iletişim kutusunda, iletişim kutusunu doldurun, MFA kimlik doğrulamasıyla **Active Directory-Universal** seçeneğini belirleyin, **Seçenekler**' i seçin, **Kullanıcı adı** kutusunu doldurun ve ardından **bağlantı özellikleri** sekmesini seçin. 

**Ad etki alanı adı veya KIRACı kimliği** kutusunu işaretleyin ve kimlik doğrulaması yetkilisini (etki alanı adı (**contosotest.onmicrosoft.com**) veya kiracı kimliğinin GUID 'si gibi) belirtin.  

   ![Ekran görüntüsü bağlantı özellikleri sekmesinde, girilen değerlerle sunucuya Bağlan ' a gösterilir.](./media/mfa-authentication/mfa-tenant-ssms.png)

SSMS 18. x veya üzeri bir sürümü çalıştırıyorsanız, bu durumda 18. x veya daha sonraki bir sürümü otomatik olarak tanıyacağından, AD etki alanı adı veya kiracı KIMLIĞI artık Konuk kullanıcılar için gerekli değildir.

   ![MFA-kiracı-SSMS](./media/mfa-authentication/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD iş 'e iş desteği   
Azure AD B2B senaryolarında Konuk Kullanıcı olarak desteklenen Azure AD kullanıcıları (bkz. [Azure B2B Işbirliği nedir,](../../active-directory/external-identities/what-is-b2b.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) GEÇERLI Azure AD 'de oluşturulan bir grubun üyelerinin parçası olarak yalnızca SYNAPSE SQL 'e bağlanabilir ve `CREATE USER` belirli bir VERITABANıNDAKI Transact-SQL ifadesiyle el ile eşleştirilir. 

Örneğin, Azure AD `steve@gmail.com` 'ye `contosotest` (Azure AD etki alanı ile) davet edildiyseniz, `contosotest.onmicrosoft.com` `usergroup` üyeyi içeren Azure AD 'de, gibi bir Azure AD grubunun oluşturulması gerekir `steve@gmail.com` . Ardından, bir Transact-SQL ekstresi yürüterek, bu grup Azure AD SQL Yöneticisi veya Azure AD DBO tarafından belirli bir veritabanı (yani, MyDatabase) için oluşturulmalıdır `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . 

Veritabanı kullanıcısı oluşturulduktan sonra, Kullanıcı `steve@gmail.com` `MyDatabase` SSMS kimlik doğrulama seçeneğini kullanarak oturum açabilir `Active Directory – Universal with MFA support` . 

UserGroup, varsayılan olarak yalnızca Connect iznine ve normal şekilde verilmesi gereken daha fazla veri erişimine sahiptir. 

Konuk Kullanıcı olarak, `steve@gmail.com` kutuyu işaretleyip `contosotest.onmicrosoft.com` SSMS **bağlantı ÖZELLIĞI** iletişim kutusunda ad etki alanı adını eklemeniz gerekir. **Ad etki alanı adı veya KIRACı kimliği** SEÇENEĞI yalnızca MFA bağlantı seçenekleriyle Universal için desteklenir, aksi halde gri olur.

## <a name="universal-authentication-limitations-for-synapse-sql"></a>SYNAPSE SQL için evrensel kimlik doğrulama sınırlamaları

- SSMS ve SqlPackage.exe, şu anda Active Directory evrensel kimlik doğrulaması aracılığıyla MFA için etkinleştirilen tek araçlardır.
- SSMS sürüm 17,2, MFA ile evrensel kimlik doğrulaması kullanarak çok kullanıcılı eşzamanlı erişimi destekler. Sürüm 17,0 ve 17,1, evrensel kimlik doğrulaması kullanan SSMS örnekleri için tek bir Azure Active Directory hesabıyla bir oturum açmayı kısıtlamalıdır. Başka bir Azure AD hesabı olarak oturum açmak için SSMS 'nin başka bir örneğini kullanmanız gerekir. (Bu kısıtlama Active Directory evrensel kimlik doğrulaması ile sınırlıdır; Active Directory parola kimlik doğrulaması, Active Directory tümleşik kimlik doğrulaması veya SQL Server kimlik doğrulaması kullanarak farklı sunucularda oturum açabilirsiniz).
- SSMS, Nesne Gezgini, sorgu Düzenleyicisi ve sorgu deposu görselleştirmesi için Active Directory evrensel kimlik doğrulamasını destekler.
- SSMS sürüm 17,2, veri veritabanını dışa/ayıklamayı/dağıtmayı sağlayan DacFx Sihirbazı desteği sağlar. Belirli bir kullanıcının kimliği, evrensel kimlik doğrulaması kullanılarak ilk kimlik doğrulama iletişim kutusu aracılığıyla doğrulandıktan sonra, DacFx Sihirbazı diğer tüm kimlik doğrulama yöntemleri için aynı şekilde çalışır.
- SSMS Tablo Tasarımcısı evrensel kimlik doğrulamasını desteklemez.
- Active Directory evrensel kimlik doğrulaması için ek yazılım gereksinimi yoktur, ancak desteklenen bir SSMS sürümü kullanmanız gerekir.  
- Evrensel kimlik doğrulaması için Active Directory Authentication Library (ADAL) sürümü en son ADAL.dll kullanılabilir yayımlanmış sürüme güncelleştirildi. Bkz. [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için bkz. [SYNAPSE SQL Ile bağlanma SQL Server Management Studio](get-started-ssms.md) makalesi.