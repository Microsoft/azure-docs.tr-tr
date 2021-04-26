---
title: Adanmış SQL havuzu için kimlik doğrulaması (eski adıyla SQL DW)
description: Azure Active Directory (Azure AD) veya SQL Server kimlik doğrulaması kullanarak Azure SYNAPSE Analytics 'te adanmış SQL havuzunda (eski adıyla SQL DW) kimlik doğrulaması yapmayı öğrenin.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 80bc9f6fc6af94ba2a5ade77cc1d53b3fc29f1ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685353"
---
# <a name="authenticate-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzunda (eski adıyla SQL DW) kimlik doğrulaması

Azure Active Directory (Azure AD) veya SQL Server kimlik doğrulaması kullanarak Azure SYNAPSE 'de adanmış SQL Havuzu (eski adıyla SQL DW) için kimlik doğrulaması yapmayı öğrenin.

Adanmış bir SQL havuzuna (eski adıyla SQL DW) bağlanmak için kimlik doğrulama amacıyla güvenlik kimlik bilgilerini geçirmeniz gerekir. Bir bağlantı kurulduktan sonra, sorgu oturumunuzu oluşturma işleminin parçası olarak belirli bağlantı ayarları yapılandırılır.  

Güvenlik hakkında daha fazla bilgi ve adanmış SQL havuzunuza (eski adıyla SQL DW) bağlantıları etkinleştirme hakkında daha fazla bilgi için bkz. [veritabanı belgelerinin güvenliğini sağlama](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>SQL kimlik doğrulaması

Adanmış SQL havuzuna (eski adıyla SQL DW) bağlanmak için aşağıdaki bilgileri sağlamanız gerekir:

* Tam nitelenmiş ServerName
* SQL kimlik doğrulaması belirtin
* Kullanıcı adı
* Parola
* Varsayılan veritabanı (isteğe bağlı)

Varsayılan olarak, bağlantınız Kullanıcı veritabanınıza değil *ana* veritabanına bağlanır. Kullanıcı veritabanınıza bağlanmak için iki işlemlerden birini seçebilirsiniz:

* Sunucunuzu SSDT, SSMS veya uygulama bağlantı dizeniz içindeki SQL Server Nesne Gezgini kaydederken varsayılan veritabanını belirtin. Örneğin, bir ODBC bağlantısı için InitialCatalog parametresini ekleyin.
* SSDT 'de oturum oluşturmadan önce kullanıcı veritabanını vurgulayın.

> [!NOTE]
> Transact-SQL ifadesinin **MyDatabase; kullanımı** , bir bağlantının veritabanını değiştirmek için desteklenmez. SSDT ile bir SQL havuzuna bağlanma Kılavuzu için, [Visual Studio Ile sorgu](sql-data-warehouse-query-visual-studio.md) makalesini inceleyin.

## <a name="azure-active-directory-authentication"></a>Azure Active Directory kimlik doğrulaması

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) kimlik doğrulaması, Azure Active Directory (Azure AD) KIMLIKLERI kullanılarak SQL havuzuna bağlanma mekanizmasıdır. Azure Active Directory kimlik doğrulaması ile, veritabanı kullanıcılarının ve diğer Microsoft hizmetlerinin kimliklerini tek bir merkezi konumda merkezi olarak yönetebilirsiniz. Merkezi KIMLIK yönetimi, adanmış SQL Havuzu (eski adıyla SQL DW) kullanıcılarını yönetmek ve izin yönetimini basitleştireceğinizi sağlayan tek bir yer sağlar.

### <a name="benefits"></a>Avantajlar

Azure Active Directory avantajları şunlardır:

* SQL Server kimlik doğrulaması için bir alternatif sağlar.
* Sunucu genelindeki Kullanıcı kimliklerinin uzamasını durdurmaya yardımcı olur.
* Tek konumda parola dönüşüne olanak sağlar
* Dış (Azure AD) gruplarını kullanarak veritabanı izinlerini yönetin.
* Tümleşik Windows kimlik doğrulamasını ve Azure Active Directory tarafından desteklenen diğer kimlik doğrulama biçimlerini etkinleştirerek parolaların depolanmasını ortadan kaldırır.
* , Veritabanı düzeyinde kimliklerin kimliğini doğrulamak için kapsanan veritabanı kullanıcılarını kullanır.
* SQL havuzuna bağlanan uygulamalar için belirteç tabanlı kimlik doğrulamasını destekler.
* [SQL Server Management Studio](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ve [SQL Server veri araçları](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)gibi çeşitli araçlar Için Active Directory evrensel kimlik doğrulaması aracılığıyla çok faktörlü kimlik doğrulamasını destekler.

> [!NOTE]
> Azure Active Directory nispeten yenidir ve bazı sınırlamalar vardır. Azure Active Directory ortamınıza uygun olduğundan emin olmak için bkz. [Azure AD özellikleri ve sınırlamaları](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), özellikle de ek konular.

### <a name="configuration-steps"></a>Yapılandırma adımları

Azure Active Directory kimlik doğrulamasını yapılandırmak için aşağıdaki adımları izleyin.

1. Azure Active Directory oluşturma ve doldurma
2. İsteğe bağlı: Şu anda Azure aboneliğinizle ilişkili olan Active Directory 'yi Ilişkilendir veya Değiştir
3. Azure SYNAPSE için Azure Active Directory Yöneticisi oluşturma
4. İstemci bilgisayarlarınızı yapılandırma
5. Veritabanınızda Azure AD kimlikleriyle eşlenen kapsanan veritabanı kullanıcıları oluşturun
6. Azure AD kimliklerini kullanarak SQL havuzunuza bağlanma

Şu anda Azure Active Directory kullanıcılar SSDT Nesne Gezgini içinde gösterilmez. Geçici bir çözüm olarak, [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)kullanıcıları görüntüleyin.

### <a name="find-the-details"></a>Ayrıntıları bul

* Azure Active Directory kimlik doğrulaması yapılandırma ve kullanma adımları, Azure SQL veritabanı ve SYNAPSE SQL için Azure SYNAPSE ile neredeyse aynıdır. [Azure Active Directory kimlik doğrulaması kullanarak SQL veritabanı 'na veya SQL Pool 'A bağlanma](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)konusundaki ayrıntılı adımları izleyin.
* Özel veritabanı rolleri oluşturun ve rollere kullanıcı ekleyin. Ardından rollere ayrıntılı izinler verin. Daha fazla bilgi için bkz. [veritabanı altyapısı izinleri Ile çalışmaya](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)başlama.

## <a name="next-steps"></a>Sonraki adımlar

Visual Studio ve diğer uygulamalarla sorgulamaya başlamak için bkz. [Visual Studio Ile sorgulama](sql-data-warehouse-query-visual-studio.md).
