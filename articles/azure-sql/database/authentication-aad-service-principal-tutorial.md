---
title: Hizmet sorumlularını kullanarak Azure AD kullanıcıları oluşturma
description: Bu öğreticide Azure SQL veritabanı ve Azure SYNAPSE Analytics 'te Azure AD uygulamaları (hizmet sorumluları) ile bir Azure AD kullanıcısı oluşturma işlemi adım adım açıklanmaktadır
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 13e049d3e7e0c87bd0a214a92491e10d652a3619
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380619"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>Öğretici: Azure AD uygulamalarını kullanarak Azure AD kullanıcıları oluşturma

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> Bu makale **genel önizlemede**. Daha fazla bilgi için bkz. [Azure SQL ile Azure Active Directory hizmet sorumlusu](authentication-aad-service-principal.md). Bu makale, gerekli öğretici adımlarını göstermek için Azure SQL veritabanı 'nı kullanır, ancak aynı şekilde [Azure SYNAPSE Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)'e uygulanabilir.

Bu makale, Azure hizmet sorumlularını (Azure AD uygulamaları) kullanarak Azure SQL veritabanı 'nda Azure AD kullanıcıları oluşturma sürecinde size kılavuzluk ediyor. Bu işlevsellik Azure SQL yönetilen örneği 'nde zaten var, ancak artık Azure SQL veritabanı ve Azure SYNAPSE Analytics 'te kullanıma sunulmuştur. Bu senaryoyu desteklemek için bir Azure AD kimliğinin oluşturulup Azure SQL mantıksal sunucusuna atanması gerekir.

Azure SQL için Azure AD kimlik doğrulaması hakkında daha fazla bilgi için [Azure Active Directory kimlik doğrulaması kullanma](authentication-aad-overview.md)makalesine bakın.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> - Azure SQL mantıksal sunucusuna bir kimlik atama
> - SQL mantıksal sunucusu kimliğine Dizin okuyucuları atama
> - Azure AD 'de hizmet sorumlusu (bir Azure AD uygulaması) oluşturma
> - Azure SQL veritabanı 'nda hizmet sorumlusu kullanıcısı oluşturma
> - Azure AD hizmet sorumlusu kullanıcısını kullanarak SQL veritabanı 'nda farklı bir Azure AD kullanıcısı oluşturma

## <a name="prerequisites"></a>Önkoşullar

- Mevcut bir [Azure SQL veritabanı](single-database-create-quickstart.md) veya [Azure SYNAPSE Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) dağıtımı. Bu öğretici için çalışan bir SQL veritabanınız olduğunu varsayıyoruz.
- Zaten var olan bir Azure Active Directory erişim.
- Azure SQL için Azure AD yöneticisi olarak tek bir Azure AD uygulaması kurmak üzere PowerShell kullanılırken, daha [az. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) modülü veya üzeri gereklidir. En son modüle yükseltildiğinizden emin olun.

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>Azure SQL mantıksal sunucusuna bir kimlik atama

1. Azure Active Directory bağlanın. Kiracı KIMLIĞINIZI bulmanız gerekecektir. Bu, [Azure Portal](https://portal.azure.com)giderek ve **Azure Active Directory** kaynağına giderek bulunabilir. **Genel bakış** BÖLMESINDE **kiracı kimliğinizi** görmeniz gerekir. Aşağıdaki PowerShell komutunu çalıştırın:

    - `<TenantId>` **Kiracı Kimliğinizle** değiştirin.

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    `TenantId`Daha sonra bu öğreticide, daha sonra kullanmak üzere kaydedin.

1. Azure SQL mantıksal sunucusu 'na bir Azure AD kimliği oluşturun ve atayın. Aşağıdaki PowerShell komutunu yürütün:

    - `<resource group>`Ve `<server name>` kaynaklarınızı ile değiştirin. Sunucunuzun adı ise `myserver.database.windows.net` `<server name>` ile değiştirin `myserver` .

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    Daha fazla bilgi için bkz. [set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) komutu.

    > [!IMPORTANT]
    > Azure SQL mantıksal sunucusu için bir Azure AD kimliği ayarlandıysa, kimliğe [**Dizin okuyucular**](../../active-directory/roles/permissions-reference.md#directory-readers) izni verilmesi gerekir. Aşağıdaki bölümde bu adımla gezineceğiz. Azure AD kimlik doğrulaması çalışmayı durduracaktır, bu **adımı atlayın.**

    - Geçmişte yeni bir SQL Server oluşturma parametresi ile [New-azsqlserver](/powershell/module/az.sql/new-azsqlserver) komutunu kullandıysanız `AssignIdentity` , bu özelliği Azure dokusunda etkinleştirmek Için daha sonra [set-azsqlserver](/powershell/module/az.sql/set-azsqlserver) komutunu ayrı bir komut olarak yürütmeniz gerekir.

1. Sunucu kimliğinin başarıyla atandığını denetleyin. Aşağıdaki PowerShell komutunu yürütün:

    - `<resource group>`Ve `<server name>` kaynaklarınızı ile değiştirin. Sunucunuzun adı ise `myserver.database.windows.net` `<server name>` ile değiştirin `myserver` .
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    Çıktın size, ve, gösterilmesi gerekir `PrincipalId` `Type` `TenantId` . Atanan kimlik `PrincipalId` .

1. [Azure Portal](https://portal.azure.com)giderek kimliği de denetleyebilirsiniz.

    - **Azure Active Directory** kaynağı altında **Kurumsal uygulamalar**' a gidin. SQL mantıksal sunucunuzun adını yazın. Kaynağa bağlı bir **nesne kimliği** olduğunu görürsünüz.
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="nesne kimliği":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>SQL mantıksal sunucusu kimliğine Dizin okuyucuları atama

Azure AD atanan kimliğin Azure SQL 'de düzgün çalışmasına izin vermek için, sunucu kimliğine Azure AD `Directory Readers` izni verilmelidir.

Gerekli izni vermek için aşağıdaki betiği çalıştırın.

> [!NOTE] 
> Bu betiğin bir Azure AD veya bir tarafından yürütülmesi gerekir `Global Administrator` `Privileged Roles Administrator` .
>
> **Genel önizlemede**, `Directory Readers` rolü Azure AD 'deki bir gruba atayabilirsiniz. Grup sahipleri daha sonra yönetilen kimliği bu grubun bir üyesi olarak ekleyebilir ve bu da `Global Administrator` `Privileged Roles Administrator` rolü verme gereksinimini atlar `Directory Readers` . Bu özellik hakkında daha fazla bilgi için bkz. [Azure SQL için Azure Active Directory Directory okuyucuları rolü](authentication-aad-directory-readers-role.md).

- `<TenantId>` `TenantId` Daha önce topladığınız ile değiştirin.
- `<server name>`SQL mantıksal sunucu adınızla değiştirin. Sunucunuzun adı ise `myserver.database.windows.net` `<server name>` ile değiştirin `myserver` .

```powershell
# This script grants Azure "Directory Readers" permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or "Privileged Roles Administrator" type of user.
# To check if the "Directory Readers" permission was granted, execute this script again

Import-Module AzureAD
Connect-AzureAD -TenantId "<TenantId>"    #Enter your actual TenantId
$AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}
 
# Get service principal for managed instance
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$($AssignIdentityName)', make sure that AssignIdentityName parameter was entered correctly."
    exit
}

if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
 
if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($AssignIdentityName)' to 'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to 'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
} else {
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> Yukarıdaki bu Betiğin çıktısı, Dizin okuyucuları izninin kimliğe verildiğini gösterir. İznin verildiğinden emin değilseniz betiği yeniden çalıştırabilirsiniz.

SQL yönetilen örneği için **Dizin okuyucuları** izninin nasıl ayarlanacağı hakkında benzer bir yaklaşım için bkz. [Azure AD YÖNETICISI sağlama (SQL yönetilen örneği)](authentication-aad-configure.md#powershell).

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>Azure AD 'de hizmet sorumlusu (bir Azure AD uygulaması) oluşturma

1. [Uygulamanızı kaydetmek ve izinleri ayarlamak](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions)için buradaki kılavuzu izleyin.

    **Uygulama izinlerinin** yanı sıra **temsilci izinleri** de eklediğinizden emin olun.

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="Azure Active Directory için Uygulama kayıtları sayfasını gösteren ekran görüntüsü. AppSP görünen adına sahip bir uygulama vurgulanır.":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="api izinleri":::

2. Oturum açmak için bir istemci parolası da oluşturmanız gerekir. [Oturum açmak için buradaki kılavuzu izleyerek bir sertifikayı karşıya yükleyin veya bir parola oluşturun](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options).

3. Uygulama kaydınızdan aşağıdakileri kaydedin. **Genel bakış** bölmesinizden erişilebilir olması gerekir:
    - **Uygulama Kimliği**
    - **KIRACı kimliği** -bu, önceki ile aynı olmalıdır

Bu öğreticide ana hizmet sorumlusu olarak *Appsp* 'Yi ve *Appsp* tarafından Azure SQL 'de oluşturulacak ikinci hizmet sorumlusu kullanıcısı olarak *MyApp* 'i kullanacağız. *Appsp* ve *MyApp* olmak üzere iki uygulama oluşturmanız gerekir.

Azure AD uygulaması oluşturma hakkında daha fazla bilgi için bkz. [nasıl yapılır: Azure AD uygulaması ve kaynaklara erişebilen hizmet sorumlusu oluşturmak için portalı kullanma](../../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="permissions-required-to-set-or-unset-the-azure-ad-admin"></a>Azure AD yöneticisini ayarlamak veya ayarını kaldırmak için gereken izinler

Hizmet sorumlusu 'nın Azure SQL için bir Azure AD yöneticisi ayarlaması veya ayarı ayarlaması için ek bir API Izni gereklidir. [Directory. Read. tüm](/graph/permissions-reference#application-permissions-18) Application API IZINLERININ Azure AD 'de uygulamanıza eklenmesi gerekir.

:::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-directory-reader-all-permissions.png" alt-text="Directory. Reader. Azure AD 'deki tüm izinler":::

Ayrıca hizmet sorumlusu SQL veritabanı için [**SQL Server katkıda**](../../role-based-access-control/built-in-roles.md#sql-server-contributor) bulunan rolüne veya SQL yönetilen örneği Için [**SQL yönetilen örnek katılımcısı**](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) rolüne gerekecektir.

> [!NOTE]
> Azure AD Graph API kullanım dışı olsa da, bu öğretici için **Directory. Reader. All** izni hala geçerlidir. Microsoft Graph API bu öğretici için geçerlidir.

## <a name="create-the-service-principal-user-in-azure-sql-database"></a>Azure SQL veritabanı 'nda hizmet sorumlusu kullanıcısı oluşturma

Azure AD 'de bir hizmet sorumlusu oluşturulduktan sonra, kullanıcıyı SQL veritabanı 'nda oluşturun. Veritabanında Kullanıcı oluşturma izinleriyle geçerli bir oturum açma ile SQL veritabanınıza bağlanmanız gerekir.

1. Aşağıdaki T-SQL komutunu kullanarak SQL veritabanında *Appsp* 'yi oluşturun:

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. `db_owner`Kullanıcının veritabanında diğer Azure AD kullanıcılarını oluşturmalarına izin veren *appsp*'ye izin verin.

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    Daha fazla bilgi için bkz. [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    Alternatif olarak, `ALTER ANY USER` rol vermek yerine izin verilebilir `db_owner` . Bu, hizmet sorumlusunun diğer Azure AD kullanıcılarını eklemesine izin verir.

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > *Appsp* , sunucu Için BIR Azure AD yöneticisi olarak ayarlandığında yukarıdaki ayar gerekli değildir. Hizmet sorumlusunu SQL mantıksal sunucusu için bir AD yöneticisi olarak ayarlamak için Azure portal, PowerShell veya Azure CLı komutlarını kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure AD yöneticisi sağlama (SQL veritabanı)](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse).

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>Azure AD hizmet sorumlusu kullanarak SQL veritabanı 'nda bir Azure AD kullanıcısı oluşturma

> [!IMPORTANT]
> SQL veritabanı 'nda oturum açmak için kullanılan hizmet sorumlusu bir istemci gizli anahtarı içermelidir. Yoksa, [Azure AD 'de hizmet sorumlusu oluşturma (bir Azure AD uygulaması)](#create-a-service-principal-an-azure-ad-application-in-azure-ad)adım 2 ' yi izleyin. Bu istemci parolasının aşağıdaki betiğe bir giriş parametresi olarak eklenmesi gerekir.

1. Hizmet sorumlusu *Appsp* kullanarak *MyApp* adlı bir Azure AD hizmet sorumlusu kullanıcısı oluşturmak için aşağıdaki betiği kullanın.

    - `<TenantId>` `TenantId` Daha önce topladığınız ile değiştirin.
    - `<ClientId>` `ClientId` Daha önce topladığınız ile değiştirin.
    - `<ClientSecret>`Daha önce oluşturulan istemci gizli anahtarı ile değiştirin.
    - `<server name>`SQL mantıksal sunucu adınızla değiştirin. Sunucunuzun adı ise `myserver.database.windows.net` `<server name>` ile değiştirin `myserver` .
    - `<database name>`SQL veritabanınızın adıyla değiştirin.

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret
    # AppSP is part of an Azure AD admin for the Azure SQL server below
    
    # Download latest  MSAL  - https://www.powershellgallery.com/packages/MSAL.PS
    Import-Module MSAL.PS
    
    $tenantId = "<TenantId>"   # tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   # AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   # Client secret for AppSP 
    $scopes = "https://database.windows.net/.default" # The end-point
    
    $result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -ClientSecret (ConvertTo-SecureString $clientSecret -AsPlainText -Force) -TenantId $tenantId -Scopes $scopes
    
    $Tok = $result.AccessToken
    #Write-host "token"
    $Tok
      
    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    $DatabaseName = "<database name>"     # Azure SQL database name
    
    Write-Host "Create SQL connection string"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()
    ``` 

    Alternatif olarak, [Azure AD hizmet sorumlusu kimlik DOĞRULAMASıNı SQL DB-Code örneğine](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)blogda kullanarak kod örneğini kullanabilirsiniz. Betiği bir DDL ifadesini yürütecek şekilde değiştirin `CREATE USER [myapp] FROM EXTERNAL PROVIDER` . Aynı komut dosyası, SQL veritabanında normal bir Azure AD kullanıcısı grubu oluşturmak için kullanılabilir.

    
2. Aşağıdaki komutu yürüterek kullanıcı *MyApp* 'in veritabanında mevcut olup olmadığını denetleyin:

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    Şuna benzer bir çıktı görmeniz gerekir:

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL ile hizmet sorumlusu Azure Active Directory](authentication-aad-service-principal.md)
- [Azure kaynakları için Yönetilen kimlikler nelerdir?](../../active-directory/managed-identities-azure-resources/overview.md)
- [App Service ve Azure Işlevleri için Yönetilen kimlikler kullanma](../../app-service/overview-managed-identity.md)
- [SQL DB 'de Azure AD hizmet sorumlusu kimlik doğrulaması-kod örneği](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)
- [Azure Active Directory'deki uygulama ve hizmet sorumlusu nesneleri](../../active-directory/develop/app-objects-and-service-principals.md)
- [Azure PowerShell ile bir Azure hizmet sorumlusu oluşturma](/powershell/azure/create-azure-service-principal-azureps)
- [Azure SQL için Azure Active Directory Directory okuyucuları rolü](authentication-aad-directory-readers-role.md)