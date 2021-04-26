---
title: Azure SQL ile hizmet sorumlusu Azure Active Directory
description: Azure AD uygulamaları (hizmet sorumluları) Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics 'te Azure AD Kullanıcı oluşturmayı destekler
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 17e846c7435e2f1cc77c5915f7e0b308c3706f96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775430"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure SQL ile hizmet sorumlusu Azure Active Directory

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure AD uygulamaları (hizmet sorumluları) adına Azure SQL veritabanı 'nda (SQL DB) ve [Azure SYNAPSE Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 'te Azure Active Directory (Azure AD) Kullanıcı oluşturma desteği şu anda **genel önizlemededir**.

> [!NOTE]
> Bu işlev SQL yönetilen örneği için zaten desteklenmektedir.

## <a name="service-principal-azure-ad-applications-support"></a>Hizmet sorumlusu (Azure AD uygulamaları) desteği

Bu makale, Azure AD ile tümleştirilmiş ve Azure AD kaydı 'nın bir parçası olan uygulamalar için geçerlidir. Bu uygulamalar genellikle çeşitli görevleri gerçekleştirmek için Azure SQL 'e kimlik doğrulama ve yetkilendirme erişimine ihtiyaç duyar. **Genel önizlemede** bulunan bu özellik artık hizmet SORUMLULARıN SQL veritabanı ve Azure SYNAPSE 'de Azure AD kullanıcıları oluşturmalarına olanak tanır. Kaldırılmış olan Azure AD uygulamaları adına Azure AD nesne oluşturulmasını engelleyen bir sınırlama vardı.

Bir Azure AD uygulaması Azure portal veya bir PowerShell komutu kullanılarak kaydettirilirse, Azure AD kiracısında iki nesne oluşturulur:

- Uygulama nesnesi
- Hizmet sorumlusu nesnesi

Azure AD uygulamaları hakkında daha fazla bilgi için bkz. [Azure Active Directory Içindeki uygulama ve hizmet sorumlusu nesneleri](../../active-directory/develop/app-objects-and-service-principals.md) ve [Azure PowerShell bir Azure hizmet sorumlusu oluşturma](/powershell/azure/create-azure-service-principal-azureps).

SQL veritabanı, Azure SYNAPSE ve SQL yönetilen örneği aşağıdaki Azure AD nesnelerini destekler:

- Azure AD kullanıcıları (yönetilen, Federasyon ve konuk)
- Azure AD grupları (yönetilen ve Federasyon)
- Azure AD uygulamaları 

`CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER`SQL veritabanı ve Azure SYNAPSE için bir Azure AD uygulaması adına T-SQL komutu artık desteklenmektedir.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Hizmet sorumlularını kullanarak Azure AD Kullanıcı oluşturma işlevselliği

Bu işlevi desteklemek, Azure ad nesnelerinin SQL veritabanı 'nda oluşturulup korunacağı ve Azure SYNAPSE insan etkileşimi olmadan Azure AD uygulama Otomasyonu işlemlerinde yararlı olur. Hizmet sorumluları, bir grubun veya tek bir kullanıcının parçası olarak SQL mantıksal sunucusu için bir Azure AD yöneticisi olabilir. Uygulama, bir sistem yöneticisi olarak çalıştırıldığında SQL veritabanı ve Azure SYNAPSE Azure AD nesne oluşturma işlemini otomatikleştirebilir ve ek SQL ayrıcalıklarına gerek yoktur. Bu, veritabanı kullanıcı oluşturma 'nın tam otomasyonu için izin verir. Bu özellik, sistem tarafından atanan yönetilen kimlik ve Kullanıcı tarafından atanan yönetilen kimlik için de desteklenir. Daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Hizmet sorumlularını Azure AD kullanıcıları oluşturmak için etkinleştirme

Bir Azure AD uygulaması adına SQL veritabanı ve Azure SYNAPSE 'de Azure AD nesne oluşturmayı etkinleştirmek için aşağıdaki ayarlar gereklidir:

1. Sunucu kimliğini atayın. Atanan sunucu kimliği Yönetilen Hizmet Kimliği (MSI) temsil eder. Şu anda Azure SQL için sunucu kimliği kullanıcı tarafından yönetilen kimliği (UMI) desteklemiyor.
    - Yeni bir Azure SQL mantıksal sunucusu için aşağıdaki PowerShell komutunu yürütün:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Daha fazla bilgi için bkz. [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) komutu.

    - Mevcut Azure SQL mantıksal sunucuları için aşağıdaki komutu yürütün:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Daha fazla bilgi için bkz. [set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) komutu.

    - Sunucu kimliğinin sunucuya atanıp atanmadığını denetlemek için Get-AzSqlServer komutunu yürütün.

    > [!NOTE]
    > Sunucu kimliği, CLı komutları kullanılarak da atanabilir. Daha fazla bilgi için bkz. [az SQL Server Create](/cli/azure/sql/server#az_sql_server_create) ve [az SQL Server Update](/cli/azure/sql/server#az_sql_server_update).

2. Oluşturulan veya sunucuya atanan sunucu kimliği için Azure AD [**Dizin okuyucularına**](../../active-directory/roles/permissions-reference.md#directory-readers) izin verin.
    - Bu izni vermek için, aşağıdaki makalede kullanılabilen SQL yönetilen örneği için kullanılan açıklamayı izleyin: [Azure AD yöneticisi sağlama (SQL yönetilen örneği)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - Bu izni veren Azure AD kullanıcısının Azure AD **genel Yöneticisi** veya **ayrıcalıklı roller Yöneticisi** rolünün bir parçası olması gerekir.

> [!IMPORTANT]
> 1 ve 2. adımlar yukarıdaki sırada yürütülmelidir. İlk olarak, sunucu kimliğini oluşturun veya atayın ve ardından [**Dizin okuyucuları**](../../active-directory/roles/permissions-reference.md#directory-readers) iznini vererek. Bu adımlardan birini atlayarak veya her ikisi de Azure AD uygulaması adına Azure SQL 'de Azure AD nesne oluşturma sırasında bir yürütme hatasına neden olur.
>
> Azure AD yöneticisi 'ni ayarlamak veya kaldırmak için hizmet sorumlusu kullanıyorsanız, uygulamanın Ayrıca Azure AD 'de [Directory. Read. All](/graph/permissions-reference#application-permissions-18) Application API iznine sahip olması gerekir. Bir Azure AD [Yöneticisi ayarlamak için gereken izinler](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin)ve Azure AD uygulaması adına BIR Azure AD kullanıcısı oluşturmak için adım adım yönergeler hakkında daha fazla bilgi için bkz. [öğretici: Azure AD UYGULAMALARı kullanarak Azure AD kullanıcıları oluşturma](authentication-aad-service-principal-tutorial.md).
>
> **Genel önizlemede**, **Dizin OKUYUCULARı** rolünü Azure AD 'deki bir gruba atayabilirsiniz. Grup sahipleri daha sonra yönetilen kimliği bu grubun bir üyesi olarak ekleyebilir, bu da bir **genel yönetici** veya **ayrıcalıklı roller yöneticisinin** **Dizin okuyucuları** rolünü vermesi gereksinimini atlar. Bu özellik hakkında daha fazla bilgi için bkz. [Azure SQL için Azure Active Directory Directory okuyucuları rolü](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Sorun giderme ve genel önizlemeye yönelik sınırlamalar

- Azure AD nesnelerini bir Azure AD uygulaması adına sunucu kimliğini etkinleştirmeden ve **Dizin okuyucuları** izni vermeden oluştururken, işlem aşağıdaki olası hatalarla başarısız olur. Aşağıdaki örnek hata, bir SQL veritabanı kullanıcısı oluşturmaya yönelik bir PowerShell komutu yürütmesi için, `myapp` [Azure AD uygulamaları aracılığıyla Azure AD kullanıcıları](authentication-aad-service-principal-tutorial.md)oluşturma için verilmiştir.
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - Yukarıdaki hata için, [Azure SQL mantıksal sunucusuna bir kimlik atamak](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) ve [SQL mantıksal sunucu kimliğine Dizin okuyucuları atamak](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity)için adımları izleyin.
    > [!NOTE]
    > Yukarıda belirtilen hata iletileri, Azure AD uygulama desteği için eksik kurulum gereksinimini açıkça tanımlamak için, özellik GA 'den önce değiştirilir.
- Azure AD uygulamasının SQL yönetilen örneği için Azure AD yöneticisi olarak ayarlanması yalnızca CLı komutu ve PowerShell komutu kullanılarak desteklenir; Örneğin, [az. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) veya üzeri. Daha fazla bilgi için, bkz. [az SQL mı ad-admin Create](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_create) and [set-Azsqlınstanceactivedirectoryadministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) komutları. 
    - Azure AD yöneticisini ayarlamak için SQL yönetilen örneği için Azure portal kullanmak istiyorsanız, olası bir geçici çözüm bir Azure AD grubu oluşturmaktır. Sonra bu gruba hizmet sorumlusu 'nı (Azure AD uygulaması) ekleyin ve bu grubu SQL yönetilen örneği için bir Azure AD yöneticisi olarak ayarlayın.
    - Hizmet sorumlusunu (Azure AD uygulaması) SQL veritabanı için Azure AD yöneticisi olarak ayarlama ve Azure SYNAPSE Azure portal, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)ve [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) komutları kullanılarak desteklenir.
- Farklı bir kiracıda oluşturulan SQL veritabanı veya SQL yönetilen örneği 'ne erişirken, başka bir Azure AD kiracısından hizmet sorumlusu olan bir Azure AD uygulamasının kullanılması başarısız olur. Bu uygulamaya atanan bir hizmet sorumlusu, SQL mantıksal sunucusu veya yönetilen örnekle aynı kiracıdan olmalıdır.
- Azure SQL için Azure AD yöneticisi olarak tek bir Azure AD uygulaması kurmak üzere PowerShell kullanılırken, daha [az. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) modülü veya üzeri gereklidir. En son modüle yükseltildiğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: Azure AD uygulamalarını kullanarak Azure AD kullanıcıları oluşturma](authentication-aad-service-principal-tutorial.md)
