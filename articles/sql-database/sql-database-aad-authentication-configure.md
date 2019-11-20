---
title: Azure Active Directory kimlik doğrulamasını yapılandırma
description: Azure AD 'yi yapılandırdıktan sonra Azure Active Directory kimlik doğrulaması kullanarak SQL veritabanı, yönetilen örnek ve SQL veri ambarı 'na nasıl bağlanacağınızı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 11/06/2019
ms.openlocfilehash: 48334d8ce266ddcc92e4d2b27634db3d8c9f1bc9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816790"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>SQL ile Azure Active Directory kimlik doğrulamasını yapılandırma ve yönetme

Bu makalede Azure AD 'nin nasıl oluşturulacağı ve doldurulması ve Azure [SQL veritabanı](sql-database-technical-overview.md), [yönetilen örnek](sql-database-managed-instance.md)ve [SQL veri ambarı](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)ile Azure AD 'nin nasıl kullanılacağı gösterilir. Genel bakış için [Azure Active Directory kimlik doğrulaması](sql-database-aad-authentication.md)' na bakın.

> [!NOTE]
> Bu makale Azure SQL Server ve Azure SQL Server 'da oluşturulan SQL veritabanı ve SQL veri ambarı veritabanları için geçerlidir. Kolaylık açısından, hem SQL Veritabanı hem de SQL Veri Ambarı için SQL Veritabanı terimi kullanılmaktadır.
> [!IMPORTANT]  
> Bir Azure VM üzerinde çalışan SQL Server bağlanmak Azure Active Directory hesabı kullanılarak desteklenmez. Bunun yerine bir etki alanı Active Directory hesabını kullanın.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

## <a name="create-and-populate-an-azure-ad"></a>Azure AD oluşturma ve doldurma

Bir Azure AD oluşturun ve Kullanıcı ve gruplarla doldurun. Azure AD, ilk Azure AD tarafından yönetilen etki alanı olabilir. Azure AD, Azure AD ile federe olan bir şirket içi Active Directory Domain Services de olabilir.

Daha fazla bilgi edinmek için bkz. [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](../active-directory/hybrid/whatis-hybrid-identity.md), [Kendi etki alanı adınızı Azure AD'ye ekleme](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure artık Windows Server Active Directory ile federasyonu destekliyor](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Azure AD dizininizi yönetme](../active-directory/fundamentals/active-directory-administer.md), [Azure AD'yi Windows PowerShell kullanarak yönetme](/powershell/azure/overview) ve [Karma Kimlik için gerekli bağlantı noktaları ve protokoller](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure Active Directory bir Azure aboneliği ilişkilendirin veya ekleyin

1. Dizine, veritabanını barındıran Azure aboneliği için güvenilen bir dizin oluşturarak Azure aboneliğinizi Azure Active Directory ilişkilendirin. Ayrıntılar için bkz. [Azure aboneliklerinin Azure AD ile ilişkilendirilmesi](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
2. Etki alanıyla ilişkili aboneliğe geçmek için Azure portal Dizin değiştiricisini kullanın.

   **Ek bilgiler:** Her Azure aboneliğinin bir Azure AD örneğiyle bir güven ilişkisi vardır. Bu; Azure aboneliğinin kullanıcılar, hizmetler ve cihazlar için kimlik doğrulaması yapmak üzere bu dizine güvendiği anlamına gelir. Birden çok abonelik aynı dizine güvenebilir ancak bir abonelik yalnızca bir dizine güvenir. Aboneliğin bir dizinle arasındaki bu güven ilişkisi, bir aboneliğin daha çok abonelik alt kaynakları gibi olan, Azure'daki tüm diğer kaynaklarla (web siteleri, veritabanları ve benzeri) sahip olduğu ilişkiye benzer nitelikte değildir. Bir aboneliğin süresi dolarsa abonelikle ilişkili bu diğer kaynaklara erişim de durdurulur. Ancak dizin Azure içinde kalır, siz de başka bir aboneliği bu dizinle ilişkilendirebilir, dizin kullanıcılarını yönetmeye devam edebilirsiniz. Kaynaklar hakkında daha fazla bilgi için bkz. [Azure 'da kaynak erişimini anlama](../active-directory/active-directory-b2b-admin-add-users.md). Bu güvenilen ilişki hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory Azure aboneliğini ilişkilendirme veya ekleme](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Azure SQL Server için Azure AD Yöneticisi oluşturma

Her Azure SQL Server (bir SQL veritabanı veya SQL veri ambarı barındırır), tüm Azure SQL Server Yöneticisi olan tek bir sunucu yöneticisi hesabıyla başlar. İkinci bir SQL Server yöneticisinin oluşturulması gerekir. Bu bir Azure AD hesabıdır. Bu asıl, ana veritabanında kapsanan bir veritabanı kullanıcısı olarak oluşturulur. Yöneticiler olarak, Sunucu Yöneticisi hesapları her kullanıcı veritabanında **db_owner** rolün üyeleridir ve her bir kullanıcı veritabanını **dbo** kullanıcısı olarak girer. Sunucu Yöneticisi hesapları hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda veritabanlarını ve oturum açma Işlemlerini yönetme](sql-database-manage-logins.md).

Coğrafi çoğaltma ile Azure Active Directory kullanırken, hem birincil hem de ikincil sunucular için Azure Active Directory yöneticisinin yapılandırılması gerekir. Sunucuda bir Azure Active Directory yöneticisi yoksa, Azure Active Directory oturumlar ve kullanıcılar sunucu hatasına bir "bağlanamaz".

> [!NOTE]
> Azure AD hesabı (Azure SQL Server yönetici hesabı dahil) tabanlı olmayan kullanıcılar, Azure AD tabanlı kullanıcılar oluşturamaz, çünkü bu kullanıcıların Azure AD ile önerilen veritabanı kullanıcılarını doğrulama izni yoktur.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Yönetilen örneğiniz için Azure Active Directory Yöneticisi sağlama

> [!IMPORTANT]
> Yalnızca yönetilen bir örnek sağlıyorsanız bu adımları izleyin. Bu işlem, yalnızca genel/şirket yöneticisi veya Azure AD 'de ayrıcalıklı rol yöneticisi tarafından yürütülebilir. Aşağıdaki adımlarda, dizinde farklı ayrıcalıklara sahip kullanıcılar için izin verme işlemi açıklanır.

> [!NOTE]
> GA öncesinde oluşturulan mı için Azure AD yöneticileri için, ancak devam eden GA 'ye devam ederseniz, mevcut davranışta işlevsel bir değişiklik yoktur. Daha fazla bilgi için, daha fazla ayrıntı için [yeni Azure AD yönetici IŞLEVSELLIĞINE mı](#new-azure-ad-admin-functionality-for-mi) bölümüne bakın.

Yönetilen Örneğinizde, güvenlik grubu üyeliği veya Yeni Kullanıcı oluşturma aracılığıyla kullanıcıların kimlik doğrulaması gibi görevleri başarılı bir şekilde gerçekleştirmek için Azure AD 'yi okumak için izinler gerekir. Bunun çalışması için Azure AD 'yi okumak üzere yönetilen örneğe izinler vermeniz gerekir. Bunu iki şekilde yapabilirsiniz: Portal ve PowerShell 'den. Aşağıdaki adımlarda her iki yöntem de vardır.

1. Azure portal, sağ üst köşede, olası etkin dizinlerin listesini aşağı eklemek için bağlantınızı seçin.
2. Varsayılan Azure AD olarak doğru Active Directory seçin.

   Bu adım, Azure AD ve yönetilen örnek için aynı aboneliğin kullanıldığından emin olmak üzere Active Directory ile ilişkili aboneliği yönetilen örnekle bağlar.
3. Yönetilen örneğe gidin ve Azure AD tümleştirmesi için kullanmak istediğiniz birini seçin.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Active Directory yönetici sayfasının üst başlığını seçin ve geçerli kullanıcıya izin verin. Azure AD 'de küresel/şirket yöneticisi olarak oturum açtıysanız, Azure portal veya PowerShell 'i aşağıdaki komut dosyasıyla yapabilirsiniz.

    ![izin verme-Portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

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
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null)
    {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1))
    {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null)
    {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else
    {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. İşlem başarıyla tamamlandıktan sonra, sağ üst köşede aşağıdaki bildirim görünür:

    ![başarılı](./media/sql-database-aad-authentication/success.png)

6. Artık yönetilen örneğiniz için Azure AD yöneticinizle seçim yapabilirsiniz. Bunun için, Active Directory yönetici sayfasında, **yönetici komutunu ayarla** ' yı seçin.

    ![Set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. AAD Yönetim sayfasında, bir Kullanıcı arayın, yönetici olacak kullanıcıyı veya grubu seçin ve ardından **Seç**' i seçin.

   Active Directory yönetici sayfası, Active Directory tüm üyelerini ve gruplarını gösterir. Gri olan kullanıcılar veya gruplar Azure AD yöneticileri olarak desteklenmediği için seçilemez. [Azure AD özellikleri ve kısıtlamalarından](sql-database-aad-authentication.md#azure-ad-features-and-limitations)desteklenen Yöneticiler listesine bakın. Rol tabanlı erişim denetimi (RBAC) yalnızca Azure portal için geçerlidir ve SQL Server yayılmaz.

    ![Yönetici Ekle](./media/sql-database-aad-authentication/add-admin.png)

8. Active Directory Yöneticisi sayfasının en üstünde **Kaydet**' i seçin.

    ![Kaydet](./media/sql-database-aad-authentication/save.png)

    Yöneticiyi değiştirme işlemi birkaç dakika sürebilir. Ardından yeni yönetici Active Directory yönetici kutusunda görünür.

Yönetilen örneğiniz için bir Azure AD yöneticisi sağlamaktan sonra, <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">create LOGIN</a> sözdizimi Ile Azure AD Server sorumlularını (oturum açma) oluşturmaya başlayabilirsiniz. Daha fazla bilgi için bkz. [yönetilen örneğe genel bakış](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Bir yöneticiyi daha sonra kaldırmak için, Active Directory yönetici sayfasının üst kısmında, **Yöneticiyi Kaldır**' ı seçin ve ardından **Kaydet**' i seçin.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Mı için yeni Azure AD yönetici işlevselliği

Aşağıdaki tabloda, MI için Azure AD oturum açma Yöneticisi 'nin, Azure AD oturum açmaları için GA ile sunulan yeni bir işlevselliğe karşı genel önizleme işlevselliği özetlenmektedir.

| Genel Önizleme sırasında mı için Azure AD oturum açma Yöneticisi | Mı için Azure AD yöneticisi için GA işlevselliği |
| --- | ---|
| Azure AD kimlik doğrulamasını sağlayan SQL veritabanı için Azure AD yöneticisi gibi benzer bir şekilde davranır, ancak Azure AD yöneticisi MI? için ana veritabanında Azure AD ya da SQL oturum açmaları oluşturamaz. | Azure AD yöneticisi sysadmin iznine sahiptir ve mı için Master DB 'de AAD ve SQL oturum açmaları oluşturabilir. |
| Sys. server_principals görünümünde yok | Sys. server_principals görünümünde mevcuttur |
| Tek tek Azure AD Konuk kullanıcılarının mı için Azure AD yöneticisi olarak ayarlanalmasına izin verir. Daha fazla bilgi için [Azure portal Azure ACTIVE DIRECTORY B2B işbirliği kullanıcıları ekleme](../active-directory/b2b/add-users-administrator.md)bölümüne bakın. | Bu grubu mı için bir Azure AD yöneticisi olarak ayarlamak için konuk kullanıcıları olan bir Azure AD grubu oluşturulmasını gerektirir. Daha fazla bilgi için bkz. [Azure AD Business to Business Support](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

GA öncesinde oluşturulan mı için mevcut Azure AD yöneticileri için en iyi yöntem olarak, aynı Azure AD kullanıcısı veya grubu için "Yöneticiyi Kaldır" Azure portal ve "Yönetici ayarla" seçeneğini kullanarak Azure AD yöneticisi 'ni sıfırlayın.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Mı için Azure AD oturum açma GA ile ilgili bilinen sorunlar

- T-SQL komutu `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`kullanılarak oluşturulan mı için ana veritabanında bir Azure AD oturum açma varsa, MI? için bir Azure AD yöneticisi olarak ayarlanamaz. Azure AD oturum açma bilgilerini oluşturmak için Azure portal, PowerShell veya CLı komutlarını kullanarak Azure AD yöneticisi olarak oturum açma ayarını yaparken bir hata yaşarsınız. 
  - Hesabın bir Azure AD yöneticisi olarak oluşturulabilmesi için, komut `DROP LOGIN [myaadaccount]`kullanılarak oturum açma ana veritabanına bırakılmalıdır.
  - `DROP LOGIN` başarılı olduktan sonra Azure portal Azure AD yönetici hesabını ayarlayın. 
  - Azure AD yönetici hesabı 'nı ayarlayamıyorum, oturum açma için yönetilen Örneğin asıl veritabanını kontrol edin. Şu komutu kullanın: `SELECT * FROM sys.server_principals`
  - Mı için bir Azure AD yöneticisi ayarlamak, bu hesabın ana veritabanında otomatik olarak bir oturum açma oluşturur. Azure AD yöneticisi 'nin kaldırılması, oturum açma bilgilerini ana veritabanından otomatik olarak kaldırır.
   
- Tek tek Azure AD Konuk kullanıcıları mı için Azure AD yöneticileri olarak desteklenmez. Konuk kullanıcıların Azure AD yöneticisi olarak ayarlanması için bir Azure AD grubunun parçası olması gerekir. Şu anda Azure portal dikey penceresi, kullanıcıların yönetici kurulumuna devam etmesine izin veren başka bir Azure AD için konuk kullanıcılar tarafından gri kullanıma sunulmamaktadır. Konuk kullanıcılarını Azure AD yöneticisi olarak kaydetmek, Kurulumun başarısız olmasına neden olur. 
  - Konuk kullanıcıyı mı için bir Azure AD yöneticisi yapmak istiyorsanız, Konuk kullanıcıyı bir Azure AD grubuna ekleyin ve bu grubu Azure AD yöneticisi olarak ayarlayın.


### <a name="powershell-for-sql-managed-instance"></a>SQL yönetilen örneği için PowerShell

PowerShell cmdlet 'lerini çalıştırmak için Azure PowerShell yüklü ve çalışıyor olması gerekir. Ayrıntılı bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/overview). Bir Azure AD yöneticisi sağlamak için aşağıdaki Azure PowerShell komutları yürütün:

- Connect-AzAccount
- Select-AzSubscription

SQL yönetilen örneği için Azure AD yöneticisi 'ni sağlamak ve yönetmek için kullanılan cmdlet 'ler:

| Cmdlet adı | Açıklama |
| --- | --- |
| [Set-Azsqlınstanceactivedirectoryadministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Geçerli abonelikte SQL yönetilen örneği için bir Azure AD yöneticisi sağlar. (Geçerli abonelikte olmalıdır)|
| [Remove-Azsqlınstanceactivedirectoryadministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Geçerli abonelikte SQL yönetilen örneği için bir Azure AD yöneticisini kaldırır. |
| [Get-Azsqlınstanceactivedirectoryadministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Geçerli abonelikte SQL yönetilen örneği için bir Azure AD yöneticisiyle ilgili bilgileri döndürür.|

### <a name="powershell-examples-for-managed-instance"></a>Yönetilen örnek için PowerShell örnekleri

Aşağıdaki komut, ResourceGroup01 adlı bir kaynak grubuyla ilişkili ManagedInstance01 adlı yönetilen bir örnek için bir Azure AD yöneticisiyle ilgili bilgileri alır.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Aşağıdaki komut, ManagedInstance01 adlı yönetilen örnek için DBAs adlı bir Azure AD yönetici grubu sağlar. Bu sunucu, kaynak grubu ResourceGroup01 ile ilişkilendirilmiştir.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Aşağıdaki komut, ResourceGroup01 kaynak grubu ile ilişkili ManagedInstanceName01 adlı yönetilen örnek için Azure AD yöneticisini kaldırır.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

### <a name="cli-for-sql-managed-instance"></a>SQL yönetilen örneği için CLı

Ayrıca, aşağıdaki CLı komutlarını çağırarak SQL yönetilen örneği için bir Azure AD yöneticisi sağlayabilirsiniz:

| Komut | Açıklama |
| --- | --- |
|[az SQL mı ad-admin Create](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) |SQL yönetilen örneği için Azure Active Directory Yöneticisi sağlar. (Geçerli abonelikte olmalıdır) |
|[az SQL mı ad-admin Delete](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) |SQL yönetilen örneği için Azure Active Directory yöneticisini kaldırır. |
|[az SQL mı ad-yönetici listesi](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) |SQL yönetilen örneği için şu anda yapılandırılmış bir Azure Active Directory Yöneticisi hakkındaki bilgileri döndürür. |
|[az SQL mi ad-Admin Update](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) |Active Directory yöneticisini bir SQL yönetilen örneği için güncelleştirir. |

CLı komutları hakkında daha fazla bilgi için bkz. [az SQL mi](https://docs.microsoft.com/cli/azure/sql/mi). 

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Azure SQL veritabanı sunucunuz için Azure Active Directory Yöneticisi sağlama

> [!IMPORTANT]
> Yalnızca bir Azure SQL veritabanı sunucusu veya veri ambarı sağlıyorsanız bu adımları izleyin.

Aşağıdaki iki yordamda, Azure portal Azure SQL Server için ve PowerShell kullanarak Azure Active Directory Yöneticisi sağlama işlemleri gösterilmektedir.

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com/), sağ üst köşede, olası etkin dizinlerin listesini aşağı eklemek için bağlantınızı seçin. Varsayılan Azure AD olarak doğru Active Directory seçin. Bu adım, Azure AD ve SQL Server aynı aboneliğin kullanıldığından emin olmak için abonelikle ilişkili Active Directory Azure SQL Server ile bağlantılandırır. (Azure SQL Server, Azure SQL veritabanı veya Azure SQL veri ambarı 'nı barındırıyor olabilir.) ![seçin-ad][8]

2. Sol başlıkta **tüm hizmetler**' i ve **SQL Server**'daki filtre türü ' nü seçin. **SQL sunucuları**' nı seçin.

    ![SQLServers. png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > Bu sayfada, **SQL Server**'ı seçmeden önce, kategorinin en *sevdiğiniz* adı yanındaki **yıldızı** seçip sol gezinti çubuğuna **SQL sunucuları** ekleyebilirsiniz.

3. **SQL Server** sayfasında, **Active Directory yönetici**' yi seçin.
4. **Active Directory Yöneticisi** sayfasında, **yönetici ayarla**' yı seçin.  Active Directory](./media/sql-database-aad-authentication/select-active-directory.png) ![seçin  

5. **Yönetici Ekle** sayfasında, bir Kullanıcı arayın, yönetici olacak kullanıcıyı veya grubu seçin ve ardından **Seç**' i seçin. (Active Directory yönetici sayfası, Active Directory tüm üyelerini ve gruplarını gösterir. Gri olan kullanıcılar veya gruplar Azure AD yöneticileri olarak desteklenmediğinden seçilemiyor. ( [SQL veritabanı veya SQL veri ambarı ile kimlik doğrulaması için Azure Active Directory kimlik doğrulaması kullan](sql-database-aad-authentication.md)' ın **Azure AD özellikleri ve sınırlamaları** bölümünde desteklenen Yöneticiler listesine bakın.) Rol tabanlı erişim denetimi (RBAC) yalnızca Portal için geçerlidir ve SQL Server yayılmaz.
    Yönetici](./media/sql-database-aad-authentication/select-admin.png) ![seçin  

6. **Active Directory Yöneticisi** sayfasının en üstünde **Kaydet**' i seçin.
    Yönetici](./media/sql-database-aad-authentication/save-admin.png) ![Kaydet

Yöneticiyi değiştirme işlemi birkaç dakika sürebilir. Ardından yeni yönetici **Active Directory yönetici** kutusunda görünür.

   > [!NOTE]
   > Azure AD yöneticisi ayarlanırken yeni yönetici adı (Kullanıcı veya grup), sanal ana veritabanında SQL Server kimlik doğrulaması kullanıcısı olarak zaten mevcut olamaz. Varsa, Azure AD yönetici kurulumu başarısız olur; oluşturma geri alınıyor ve bu tür bir yöneticinin (ad) zaten var olduğunu gösterir. SQL Server bir kimlik doğrulama kullanıcısı Azure AD 'nin bir parçası olmadığından, Azure AD kimlik doğrulaması kullanarak sunucuya bağlanma çabaları başarısız olur.

Bir yöneticiyi daha sonra kaldırmak için, **Active Directory yönetici** sayfasının üst kısmında, **Yöneticiyi Kaldır**' ı seçin ve ardından **Kaydet**' i seçin.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Azure SQL veritabanı ve Azure SQL veri ambarı için PowerShell

PowerShell cmdlet 'lerini çalıştırmak için Azure PowerShell yüklü ve çalışıyor olması gerekir. Ayrıntılı bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/overview). Bir Azure AD yöneticisi sağlamak için aşağıdaki Azure PowerShell komutları yürütün:

- Connect-AzAccount
- Select-AzSubscription

Azure SQL veritabanı ve Azure SQL veri ambarı için Azure AD yöneticisi 'ni sağlamak ve yönetmek için kullanılan cmdlet 'ler:

| Cmdlet adı | Açıklama |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Azure SQL Server veya Azure SQL veri ambarı için Azure Active Directory Yöneticisi sağlar. (Geçerli abonelikte olmalıdır) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Azure SQL Server veya Azure SQL veri ambarı için Azure Active Directory yöneticisini kaldırır. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Azure SQL Server veya Azure SQL veri ambarı için şu anda yapılandırılmış bir Azure Active Directory Yöneticisi hakkında bilgi döndürür. |

Bu komutların her biri hakkında daha fazla bilgi için Get-Help PowerShell komutunu kullanın. Örneğin, ``get-help Set-AzSqlServerActiveDirectoryAdministrator``.

### <a name="powershell-examples-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Azure SQL veritabanı ve Azure SQL veri ambarı için PowerShell örnekleri

Aşağıdaki betik, **Grup-23**adlı bir kaynak grubundaki **demo_server** sunucusu IÇIN **DBA_Group** (nesne KIMLIĞI `40b79501-b343-44ed-9ce7-da4c8cc7353f`) adlı bir Azure AD yönetici grubu sağlar:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** giriş parametresi, Azure AD görünen adını veya Kullanıcı asıl adını kabul eder. Örneğin, ``DisplayName="John Smith"`` ve ``DisplayName="johns@contoso.com"``. Azure AD grupları için yalnızca Azure AD görünen adı desteklenir.

> [!NOTE]
> Azure PowerShell komutu ```Set-AzSqlServerActiveDirectoryAdministrator```, desteklenmeyen kullanıcılar için Azure AD yöneticileri 'ni sağlamanıza engel olmaz. Desteklenmeyen bir Kullanıcı sağlanabilir, ancak bir veritabanına bağlanamaz.

Aşağıdaki örnek, isteğe bağlı **ObjectID**'yi kullanır:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> **DisplayName** benzersiz OLMADıĞıNDA Azure AD **ObjectID** gereklidir. **ObjectID** ve **DisplayName** değerlerini almak için, klasik Azure portalı Active Directory bölümünü kullanın ve bir kullanıcının veya grubun özelliklerini görüntüleyin.

Aşağıdaki örnek, Azure SQL Server için geçerli Azure AD Yöneticisi hakkındaki bilgileri döndürür:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Aşağıdaki örnek bir Azure AD yöneticisini kaldırır:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

> [!NOTE]
> REST API 'Lerini kullanarak da Azure Active Directory Yöneticisi sağlayabilirsiniz. Daha fazla bilgi için bkz. [Azure SQL veritabanı Için Azure SQL veritabanı işlemlerine yönelik hizmet yönetimi REST API başvurusu ve işlemleri](https://docs.microsoft.com/rest/api/sql/)

### <a name="cli-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Azure SQL veritabanı ve Azure SQL veri ambarı için CLı

Ayrıca, aşağıdaki CLı komutlarını çağırarak bir Azure AD yöneticisi sağlayabilirsiniz:

| Komut | Açıklama |
| --- | --- |
|[az SQL Server ad-admin Create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) |Azure SQL Server veya Azure SQL veri ambarı için Azure Active Directory Yöneticisi sağlar. (Geçerli abonelikte olmalıdır) |
|[az SQL Server ad-admin Delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) |Azure SQL Server veya Azure SQL veri ambarı için Azure Active Directory yöneticisini kaldırır. |
|[az SQL Server ad-yönetici listesi](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) |Azure SQL Server veya Azure SQL veri ambarı için şu anda yapılandırılmış bir Azure Active Directory Yöneticisi hakkında bilgi döndürür. |
|[az SQL Server ad-Admin Update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) |Azure SQL Server veya Azure SQL veri ambarı için Active Directory yöneticisini güncelleştirir. |

CLı komutları hakkında daha fazla bilgi için bkz. [az SQL Server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="configure-your-client-computers"></a>İstemci bilgisayarlarınızı yapılandırma

Uygulamalarınızın veya kullanıcılarınızın Azure AD kimliklerini kullanarak Azure SQL veritabanı veya Azure SQL veri ambarı 'na bağlandığı tüm istemci makinelerde aşağıdaki yazılımları yüklemelisiniz:

- [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).NET Framework 4,6 veya üzeri.
- SQL Server için Azure Active Directory kimlik doğrulama kitaplığı (**Adalsql. DLL**), [Microsoft SQL Server için Microsoft Active Directory Authentication Library](https://www.microsoft.com/download/details.aspx?id=48742)'de bulunan İndirme Merkezi 'nden birden çok dilde (x86 ve AMD64) kullanılabilir.

Bu gereksinimleri şu şekilde karşılayabilirsiniz:

- [Visual Studio 2015 için](https://msdn.microsoft.com/library/mt204009.aspx) [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) veya SQL Server veri araçları yüklemek .NET Framework 4,6 gereksinimini karşılar.
- SSMS, Adalsql 'in x86 sürümünü yüklüyor **. DLL**.
- SSDT, Adalsql 'in AMD64 sürümünü yüklüyor **. DLL**.
- [Visual Studio Indirmelerinin](https://www.visualstudio.com/downloads/download-visual-studio-vs) en son Visual studio indirmeleri .NET Framework 4,6 gereksinimini karşılar, ancak Adalsql 'nin gerekli AMD64 sürümünü yüklemez **. DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Veritabanınızda Azure AD kimlikleriyle eşlenen kapsanan veritabanı kullanıcıları oluşturun

> [!IMPORTANT]
> Yönetilen örnek artık Azure AD kullanıcıları, grupları veya uygulamalarından oturum açma işlemleri oluşturmanızı sağlayan Azure AD Server sorumlularını (oturum açma) desteklemektedir. Azure AD Server sorumluları (oturumlar), yönetilen örneğiniz için, veritabanı kullanıcılarının kapsanan veritabanı kullanıcısı olarak oluşturulmasını gerektirmeden kimlik doğrulaması yapma olanağı sağlar. Daha fazla bilgi için bkz. [yönetilen örneğe genel bakış](sql-database-managed-instance.md#azure-active-directory-integration). Azure AD Server sorumlularını (oturum açma) oluşturma hakkında sözdizimi için bkz. <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">oturum oluşturma</a>.

Azure Active Directory kimlik doğrulaması, veritabanı kullanıcılarının kapsanan veritabanı kullanıcıları olarak oluşturulmasını gerektirir. Bir Azure AD kimliğine dayanan bir kapsanan veritabanı kullanıcısı, ana veritabanında oturum açma işlemi olmayan ve veritabanı ile ilişkili Azure AD dizinindeki bir kimlikle eşlenen bir veritabanı kullanıcısı. Azure AD kimliği, tek bir kullanıcı hesabı ya da bir grup olabilir. Kapsanan veritabanı kullanıcıları hakkında daha fazla bilgi için bkz. [Kapsanan Veritabanı kullanıcıları-veritabanınızı taşınabilir hale getirme](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Veritabanı kullanıcıları (Yöneticiler hariç) Azure portal kullanılarak oluşturulamaz. RBAC rolleri SQL Server, SQL veritabanı veya SQL veri ambarı 'na yayılmaz. Azure RBAC rolleri, Azure kaynaklarını yönetmek için kullanılır ve veritabanı izinlerine uygulanmaz. Örneğin, **SQL Server katkıda** bulunan rolü, SQL VERITABANı veya SQL veri ambarına bağlanmak için erişim vermez. Erişim izni doğrudan veritabanında Transact-SQL deyimleri kullanılarak verilmelidir.
> [!WARNING]
> T-SQL CREATE LOGıN ve CREATE USER deyimlerine Kullanıcı adları olarak dahil edildiğinde, iki nokta üst üste `:` veya ve işareti `&` gibi özel karakterler desteklenmez.

Azure AD tabanlı bir kapsanan veritabanı kullanıcısı (veritabanının sahibi olan Sunucu Yöneticisi dışında) oluşturmak için, en az **Kullanıcı Değiştirme** iznine sahip bir kullanıcı olarak BIR Azure AD kimliğiyle veritabanına bağlanın. Ardından aşağıdaki Transact-SQL sözdizimini kullanın:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* , bir Azure AD kullanıcısının Kullanıcı asıl adı veya BIR Azure AD grubu için görünen ad olabilir.

**Örnekler:** Bir Azure AD Federasyon veya yönetilen etki alanı kullanıcısını temsil eden bir kapsanan veritabanı kullanıcısı oluşturmak için:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Bir Azure AD veya Federasyon etki alanı grubunu temsil eden bir kapsanan veritabanı kullanıcısı oluşturmak için, bir güvenlik grubunun görünen adını belirtin:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Bir Azure AD belirteci kullanarak bağlanan bir uygulamayı temsil eden bir kapsanan veritabanı kullanıcısı oluşturmak için:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Bu komut, oturum açmış kullanıcı adına SQL 'e Azure AD 'nin ("dış sağlayıcı") erişmesini gerektirir. Bazen Azure AD 'nin SQL 'e bir özel durum döndürmesine neden olacak durumlar ortaya çıkar. Bu durumlarda, Kullanıcı AAD 'ye özgü hata iletisini içermesi gereken SQL hatası 33134 ' i görür. Çoğu zaman, hata erişimin reddedildiğini veya kullanıcının kaynağa erişmek için MFA 'ya kaydolması gerekir ya da birinci taraf uygulamalar arasındaki erişimin ön kimlik doğrulama aracılığıyla işlenmesi gerekir. İlk iki durumda, sorun genellikle kullanıcının AAD kiracısında ayarlanan koşullu erişim ilkelerinin oluşmasına neden olur: kullanıcının dış sağlayıcıya erişmesini önler. CA ilkelerinin ' 00000002-0000-0000-C000-000000000000 ' uygulamasına erişime izin verecek şekilde güncelleştirilmesi (AAD Graph API uygulama KIMLIĞI) sorunu çözmelidir. Hatanın ilk taraf uygulamalar arasındaki erişimi, ön kimlik doğrulama aracılığıyla işlenmesi gerektiğinden sorun, kullanıcının hizmet sorumlusu olarak oturum açmış olmasından kaynaklanır. Bunun yerine bir kullanıcı tarafından yürütüldüğünde komutun başarılı olması gerekir.

> [!TIP]
> Azure aboneliğinizle ilişkili Azure Active Directory dışında bir Azure Active Directory doğrudan Kullanıcı oluşturamazsınız. Ancak, ilişkili Active Directory (dış kullanıcılar olarak bilinir) içeri aktarılan diğer etkin dizinlerin üyeleri, kiracı Active Directory bir Active Directory grubuna eklenebilir. Bu AD grubu için kapsanan bir veritabanı kullanıcısı oluşturarak, dış Active Directory kullanıcılar SQL veritabanına erişim sağlayabilir.

Azure Active Directory kimliklerine göre kapsanan veritabanı kullanıcıları oluşturma hakkında daha fazla bilgi için bkz. [Create User (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Azure SQL Server Azure Active Directory yöneticisini kaldırmak, herhangi bir Azure AD kimlik doğrulama kullanıcısının sunucuya bağlanmasını engeller. Gerekirse, kullanılamayan Azure AD kullanıcıları bir SQL veritabanı yöneticisi tarafından el ile bırakılabilir.
> [!NOTE]
> Bir **bağlantı zaman aşımı süresi dolmuşsa**, bağlantı dizesinin `TransparentNetworkIPResolution` parametresini false olarak ayarlamanız gerekebilir. Daha fazla bilgi için bkz. [.NET Framework 4.6.1-TransparentNetworkIPResolution Ile bağlantı zaman aşımı sorunu](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Bir veritabanı kullanıcısı oluşturduğunuzda, bu kullanıcı **bağlantı** iznini alır ve bu veritabanına **genel** rolün bir üyesi olarak bağlanabilir. Başlangıçta yalnızca Kullanıcı için kullanılabilen izinler, **genel** role veya üyesi oldukları herhangi BIR Azure AD grubuna verilen izinlere sahiptir. Azure AD tabanlı bir kapsanan veritabanı kullanıcısı sağladığınızda, diğer Kullanıcı türlerine izin verdiğiniz şekilde kullanıcıya ek izinler verebilirsiniz. Genellikle veritabanı rollerine izinler verin ve rollere kullanıcı ekleyin. Daha fazla bilgi için bkz. [veritabanı altyapısı Izin temelleri](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Özel SQL veritabanı rolleri hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda veritabanlarını ve oturum açma Işlemlerini yönetme](sql-database-manage-logins.md).
Yönetilen bir etki alanına dış Kullanıcı olarak içeri aktarılan bir Federasyon etki alanı kullanıcı hesabı, yönetilen etki alanı kimliğini kullanmalıdır.

> [!NOTE]
> Azure AD kullanıcıları, E (EXTERNAL_USER) türü ve X (EXTERNAL_GROUPS) türündeki gruplar için veritabanı meta verilerinde işaretlenir. Daha fazla bilgi için bkz. [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>SSMS veya SSDT kullanarak Kullanıcı veritabanına veya veri ambarına bağlanma  

Azure AD yöneticisi 'nin düzgün şekilde ayarlandığını onaylamak için, Azure AD yönetici hesabını kullanarak **ana** veritabanına bağlanın.
Azure AD tabanlı bir kapsanan veritabanı kullanıcısını (veritabanının sahibi olan Sunucu Yöneticisi dışında) sağlamak için veritabanına erişimi olan bir Azure AD kimliğiyle veritabanına bağlanın.

> [!IMPORTANT]
> Azure Active Directory kimlik doğrulaması desteği [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ve Visual Studio 2015 ' de [SQL Server veri araçları](https://msdn.microsoft.com/library/mt204009.aspx) ile kullanılabilir. SSMS 'nin 2016 Ağustos sürümü, yöneticilerin telefon araması, SMS mesajı, PIN ile akıllı kartlar veya mobil uygulama bildirimi kullanarak Multi-Factor Authentication gerektirmesini sağlayan Active Directory evrensel kimlik doğrulaması desteğini de içerir.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>SSMS veya SSDT kullanarak bağlanmak için bir Azure AD kimliği kullanma

Aşağıdaki yordamlarda, SQL Server Management Studio veya SQL Server veritabanı araçlarını kullanarak bir Azure AD kimliğiyle bir SQL veritabanına nasıl bağlanabilmeniz gösterilmektedir.

### <a name="active-directory-integrated-authentication"></a>Active Directory tümleşik kimlik doğrulaması

Windows 'da, Federasyon etki alanından Azure Active Directory kimlik bilgilerinizi kullanarak oturum açtıysanız bu yöntemi kullanın.

1. Management Studio veya veri araçlarını başlatın ve **sunucuya Bağlan** (veya **veritabanı altyapısına Bağlan**) Iletişim kutusunda, **kimlik doğrulama** kutusunda **Active Directory tümleştirilmiş**' ı seçin. Mevcut kimlik bilgileriniz bağlantı için sunulacağından parola gerekmez veya girilemez.

    ![AD Tümleşik kimlik doğrulaması seçin][11]
2. **Seçenekler** düğmesini seçin ve **bağlantı özellikleri** sayfasında, **veritabanına Bağlan** kutusunda, bağlanmak istediğiniz kullanıcı veritabanının adını yazın. ( **Ad etki alanı adı veya KIRACı kimliği**"SEÇENEĞI yalnızca **MFA bağlantı seçenekleriyle Universal** için desteklenir, aksi halde gri olur.)  

    ![Veritabanı adını seçin][13]

## <a name="active-directory-password-authentication"></a>Active Directory parola kimlik doğrulaması

Azure AD tarafından yönetilen etki alanını kullanarak bir Azure AD asıl adı ile bağlanırken bu yöntemi kullanın. Bunu, etki alanına erişimi olmayan federe hesaplar için de kullanabilirsiniz. Örneğin, uzaktan çalışırken.

Yerel veya federal Azure AD kullanıcıları için Azure AD ile SQL DB/DW kimlik doğrulaması yapmak için bu yöntemi kullanın. Yerel Kullanıcı Azure AD 'de açık bir şekilde oluşturulur ve Kullanıcı adı ve parola kullanılarak kimlik doğrulaması yapılır, ancak Federasyon kullanıcısı etki alanı Azure AD ile federe olan bir Windows kullanıcısı olduğunda. İkinci Yöntem (Kullanıcı & parolasını kullanarak), bir Kullanıcı Windows kimlik bilgilerini kullanmak istediğinde, ancak yerel makineleri etki alanına katılmadığında (örneğin, uzaktan erişim kullanarak) kullanılabilir. Bu durumda, bir Windows kullanıcısı etki alanı hesabını ve parolasını belirtebilir ve Federasyon kimlik bilgilerini kullanarak SQL DB/DW kimlik doğrulamasını yapabilir.

1. Management Studio veya veri araçlarını başlatın ve **sunucuya Bağlan** (veya **veritabanı altyapısına Bağlan**) Iletişim kutusunda, **kimlik doğrulama** kutusunda **Active Directory-Password**' ı seçin.
2. **Kullanıcı adı** kutusuna Azure Active Directory Kullanıcı adınızı kullanıcı adı **\@Domain.com**biçiminde yazın. Kullanıcı adları, Azure Active Directory bir hesap veya Azure Active Directory bir etki alanı Federasyondan bir hesap olmalıdır.
3. **Parola** kutusuna Azure Active Directory hesabı veya Federasyon etki alanı hesabı için Kullanıcı parolanızı yazın.

    ![AD parola kimlik doğrulamasını seçin][12]
4. **Seçenekler** düğmesini seçin ve **bağlantı özellikleri** sayfasında, **veritabanına Bağlan** kutusunda, bağlanmak istediğiniz kullanıcı veritabanının adını yazın. (Önceki seçenekte bulunan grafiğe bakın.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Bir istemci uygulamasından bağlanmak için bir Azure AD kimliği kullanma

Aşağıdaki yordamlarda, bir SQL veritabanına bir Azure AD kimliğiyle bir istemci uygulamasından nasıl bağlanacağı gösterilmektedir.

### <a name="active-directory-integrated-authentication"></a>Active Directory tümleşik kimlik doğrulaması

Tümleşik Windows kimlik doğrulamasını kullanmak için, etki alanının Active Directory Azure Active Directory ile federe olması gerekir. Veritabanına bağlanan istemci uygulamanızın (veya bir hizmetin), bir kullanıcının etki alanı kimlik bilgileri altında etki alanına katılmış bir makinede çalışıyor olması gerekir.

Tümleşik kimlik doğrulaması ve bir Azure AD kimliği kullanarak bir veritabanına bağlanmak için veritabanı bağlantı dizesindeki authentication anahtar sözcüğünün tümleşik Active Directory olarak ayarlanması gerekir. Aşağıdaki C# kod örneği ADO .net kullanır.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Bağlantı dizesi anahtar sözcüğü ``Integrated Security=True`` Azure SQL veritabanı 'na bağlanmak için desteklenmiyor. ODBC bağlantısı yaparken, boşlukları kaldırmanız ve kimlik doğrulamasını ' Activedirectoryıntegrated ' olarak ayarlamanız gerekir.

### <a name="active-directory-password-authentication"></a>Active Directory parola kimlik doğrulaması

Tümleşik kimlik doğrulaması ve bir Azure AD kimliği kullanarak bir veritabanına bağlanmak için, kimlik doğrulama anahtar sözcüğünün Active Directory parola olarak ayarlanması gerekir. Bağlantı dizesinin Kullanıcı KIMLIĞI/UID ve Password/PWD anahtar sözcükleri ve değerleri içermesi gerekir. Aşağıdaki C# kod örneği ADO .net kullanır.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Azure AD [kimlik doğrulaması GitHub tanıtımı](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)'nda bulunan tanıtım kodu örneklerini kullanarak Azure AD kimlik doğrulama yöntemleri hakkında daha fazla bilgi edinin.

## <a name="azure-ad-token"></a>Azure AD belirteci

Bu kimlik doğrulama yöntemi, Azure Active Directory (AAD) ' den bir belirteç edinerek, orta katman hizmetlerin Azure SQL veritabanı 'na veya Azure SQL veri ambarı 'na bağlanmasına olanak sağlar. Sertifika tabanlı kimlik doğrulaması dahil olmak üzere gelişmiş senaryolar sunar. Azure AD belirteç kimlik doğrulamasını kullanmak için dört temel adımı tamamlamalısınız:

1. Uygulamanızı Azure Active Directory kaydedin ve kodunuzun istemci KIMLIĞINI alın.
2. Uygulamayı temsil eden bir veritabanı kullanıcısı oluşturun. (Adım 6 ' da daha önce tamamlandı.)
3. İstemci bilgisayarda bir sertifika oluşturma uygulamayı çalıştırır.
4. Sertifikayı uygulamanız için bir anahtar olarak ekleyin.

Örnek bağlantı dizesi:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Daha fazla bilgi için bkz. [güvenlik blogu SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Sertifika ekleme hakkında daha fazla bilgi için, bkz. [Azure Active Directory Sertifika tabanlı kimlik doğrulaması ile çalışmaya başlama](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Aşağıdaki deyimler, [Indirme merkezinden](https://www.microsoft.com/download/details.aspx?id=53591)erişilebilen sqlcmd sürüm 13,1 ' i kullanarak bağlanır.

> [!NOTE]
> `-G` komutuyla `sqlcmd` sistem kimlikleriyle çalışmaz ve Kullanıcı sorumlusu oturumu gerektirir.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Sonraki adımlar

- SQL Veritabanında erişim ve denetime genel bakış için bkz. [SQL Veritabanında erişim ve denetim](sql-database-control-access.md).
- SQL Veritabanındaki oturum açma bilgileri, kullanıcılar ve veritabanı rollerine genel bakış için bkz. [Oturum açma bilgileri, kullanıcılar ve veritabanı rolleri](sql-database-manage-logins.md).
- Veritabanı sorumluları hakkında daha fazla bilgi için bkz. [Sorumlular](https://msdn.microsoft.com/library/ms181127.aspx).
- Veritabanı rolleri hakkında daha fazla bilgi için bkz. [Veritabanı rolleri](https://msdn.microsoft.com/library/ms189121.aspx).
- SQL Veritabanındaki güvenlik duvarı kuralları hakkında daha fazla bilgi için bkz. [SQL Veritabanı güvenlik duvarı kuralları](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
