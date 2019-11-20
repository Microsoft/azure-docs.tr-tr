---
title: Kimlik Doğrulaması
description: Azure Active Directory (AAD) veya SQL Server kimlik doğrulaması kullanarak Azure SQL veri ambarı 'nda kimlik doğrulaması yapmayı öğrenin.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fda29e432fbd952261893f3c32a4df7b9990ae66
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692926"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda kimlik doğrulama
Azure Active Directory (AAD) veya SQL Server kimlik doğrulaması kullanarak Azure SQL veri ambarı 'nda kimlik doğrulaması yapmayı öğrenin.

SQL veri ambarı 'na bağlanmak için kimlik doğrulama amacıyla güvenlik kimlik bilgilerini geçirmeniz gerekir. Bir bağlantı kurulduktan sonra, sorgu oturumunuzu oluşturma işleminin parçası olarak belirli bağlantı ayarları yapılandırılır.  

Güvenlik ve veri Ambarınızla bağlantıları etkinleştirme hakkında daha fazla bilgi için bkz. [SQL veri ambarı 'nda veritabanını güvenli hale getirme][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>SQL kimlik doğrulaması
SQL veri ambarı 'na bağlanmak için aşağıdaki bilgileri sağlamanız gerekir:

* Tam nitelenmiş ServerName
* SQL kimlik doğrulaması belirtin
* Kullanıcı adı
* Parola
* Varsayılan veritabanı (isteğe bağlı)

Varsayılan olarak bağlantınız, Kullanıcı veritabanınıza değil *ana* veritabanına bağlanır. Kullanıcı veritabanınıza bağlanmak için iki işlemlerden birini seçebilirsiniz:

* Sunucunuzu SSDT, SSMS veya uygulama bağlantı dizeniz içindeki SQL Server Nesne Gezgini kaydederken varsayılan veritabanını belirtin. Örneğin, bir ODBC bağlantısı için InitialCatalog parametresini ekleyin.
* SSDT 'de oturum oluşturmadan önce kullanıcı veritabanını vurgulayın.

> [!NOTE]
> Transact-SQL ifadesinin **MyDatabase; kullanımı** , bir bağlantının veritabanını değiştirmek için desteklenmez. SSDT ile SQL veri ambarı 'na bağlanma Kılavuzu için, [Visual Studio Ile sorgu][Query with Visual Studio] makalesini inceleyin.
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD) kimlik doğrulaması
[Azure Active Directory][What is Azure Active Directory] kimlik doğrulaması, Azure Active Directory (Azure AD) kimlikleri kullanılarak Microsoft Azure SQL veri ambarı bağlanma mekanizmasıdır. Azure Active Directory kimlik doğrulaması ile, veritabanı kullanıcılarının ve diğer Microsoft hizmetlerinin kimliklerini tek bir merkezi konumda merkezi olarak yönetebilirsiniz. Merkezi KIMLIK yönetimi, SQL veri ambarı kullanıcılarını yönetmek ve izin yönetimini basitleştireceğinizi sağlayan tek bir yer sağlar. 

### <a name="benefits"></a>Avantajlar
Azure Active Directory avantajları şunlardır:

* SQL Server kimlik doğrulaması için bir alternatif sağlar.
* Veritabanı sunucuları arasında kullanıcı kimliklerinin uzamasını durdurmaya yardımcı olur.
* Tek konumda parola dönüşüne olanak sağlar
* Dış (AAD) gruplarını kullanarak veritabanı izinlerini yönetin.
* Tümleşik Windows kimlik doğrulamasını ve Azure Active Directory tarafından desteklenen diğer kimlik doğrulama biçimlerini etkinleştirerek parolaların depolanmasını ortadan kaldırır.
* , Veritabanı düzeyinde kimliklerin kimliğini doğrulamak için kapsanan veritabanı kullanıcılarını kullanır.
* SQL veri ambarı 'na bağlanan uygulamalar için belirteç tabanlı kimlik doğrulamasını destekler.
* [SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md) ve [SQL Server veri araçları](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json)gibi çeşitli araçlar Için Active Directory evrensel kimlik doğrulaması aracılığıyla çok faktörlü kimlik doğrulamasını destekler.

> [!NOTE]
> Azure Active Directory nispeten yenidir ve bazı sınırlamalar vardır. Azure Active Directory ortamınıza uygun olduğundan emin olmak için bkz. [Azure AD özellikleri ve sınırlamaları][Azure AD features and limitations], özellikle de ek konular.
> 
> 

### <a name="configuration-steps"></a>Yapılandırma adımları
Azure Active Directory kimlik doğrulamasını yapılandırmak için aşağıdaki adımları izleyin.

1. Azure Active Directory oluşturma ve doldurma
2. İsteğe bağlı: Şu anda Azure aboneliğinizle ilişkili olan Active Directory 'yi Ilişkilendir veya Değiştir
3. Azure SQL veri ambarı için Azure Active Directory Yöneticisi oluşturun.
4. İstemci bilgisayarlarınızı yapılandırma
5. Veritabanınızda Azure AD kimlikleriyle eşlenen kapsanan veritabanı kullanıcıları oluşturun
6. Azure AD kimliklerini kullanarak veri ambarınıza bağlanma

Şu anda Azure Active Directory kullanıcılar SSDT Nesne Gezgini içinde gösterilmez. Geçici bir çözüm olarak, [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx)içindeki kullanıcıları görüntüleyin.

### <a name="find-the-details"></a>Ayrıntıları bul
* Azure Active Directory kimlik doğrulamasını yapılandırma ve kullanma adımları Azure SQL veritabanı ve Azure SQL veri ambarı için neredeyse aynıdır. [Azure Active Directory kimlik doğrulaması kullanarak SQL veritabanı 'na veya SQL Data Warehouse 'A bağlanma](../sql-database/sql-database-aad-authentication.md)konusundaki ayrıntılı adımları izleyin.
* Özel veritabanı rolleri oluşturun ve rollere kullanıcı ekleyin. Ardından rollere ayrıntılı izinler verin. Daha fazla bilgi için bkz. [veritabanı altyapısı izinleri Ile çalışmaya](https://msdn.microsoft.com/library/mt667986.aspx)başlama.

## <a name="next-steps"></a>Sonraki adımlar
Visual Studio ve diğer uygulamalarla veri ambarınızı sorgulamaya başlamak için bkz. [Visual Studio ile sorgulama][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
