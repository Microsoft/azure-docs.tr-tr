---
title: Azure Active Directory kimlik doğrulamasını yapılandırma
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure AD 'yi yapılandırdıktan sonra Azure Active Directory kimlik doğrulaması kullanarak SQL veritabanı, SQL yönetilen örneği ve Azure SYNAPSE Analytics 'e bağlanmayı öğrenin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: c75364f2565611b6738996c082610229db0cb2a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762236"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Azure SQL ile Azure AD kimlik doğrulamasını yapılandırma ve yönetme

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Bu makalede bir Azure Active Directory (Azure AD) örneği oluşturma ve doldurma ve Azure [SQL veritabanı](sql-database-paas-overview.md), [Azure SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md)ve [Azure SYNAPSE Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)ile Azure ad kullanma hakkında yönergeler verilmektedir. Genel bakış için [Azure Active Directory kimlik doğrulaması](authentication-aad-overview.md)' na bakın.

## <a name="azure-ad-authentication-methods"></a>Azure AD kimlik doğrulama yöntemleri

Azure AD kimlik doğrulaması aşağıdaki kimlik doğrulama yöntemlerini destekler:

- Yalnızca Azure AD bulutu kimlikleri
- Azure AD karma kimlikleri şunları destekler:
  - Sorunsuz çoklu oturum açma (SSO) ile birlikte iki seçenekten oluşan bulut kimlik doğrulaması
    - Azure AD Parola karması kimlik doğrulaması
    - Azure AD geçişli kimlik doğrulaması
  - Federasyon kimlik doğrulaması

Azure AD kimlik doğrulama yöntemleri hakkında daha fazla bilgi edinmek ve aralarından seçim yapmak için, bkz. [Azure Active Directory karma kimlik çözümünüz için doğru kimlik doğrulama yöntemini seçme](../../active-directory/hybrid/choose-ad-authn.md).

Azure AD karma kimlikleri, kurulumu ve eşitlemesi hakkında daha fazla bilgi için bkz.:

- Parola karması kimlik doğrulaması- [Azure AD Connect eşitleme ile parola karması eşitlemesi uygulama](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Geçişli kimlik doğrulaması- [Azure Active Directory geçişli kimlik doğrulaması](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federal kimlik doğrulaması- [Azure 'da Active Directory Federasyon Hizmetleri (AD FS) dağıtma](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) ve [Azure AD Connect ve Federasyon](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Azure AD örneği oluşturma ve doldurma

Bir Azure AD örneği oluşturun ve Kullanıcı ve gruplarla doldurun. Azure AD, ilk Azure AD tarafından yönetilen etki alanı olabilir. Azure AD, Azure AD ile federe olan bir şirket içi Active Directory Domain Services de olabilir.

Daha fazla bilgi edinmek için bkz. [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](../../active-directory/hybrid/whatis-hybrid-identity.md), [Kendi etki alanı adınızı Azure AD'ye ekleme](../../active-directory/fundamentals/add-custom-domain.md), [Microsoft Azure artık Windows Server Active Directory ile federasyonu destekliyor](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Azure AD dizininizi yönetme](../../active-directory/fundamentals/active-directory-whatis.md), [Azure AD'yi Windows PowerShell kullanarak yönetme](/powershell/azure/) ve [Karma Kimlik için gerekli bağlantı noktaları ve protokoller](../../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure Active Directory’ye bir Azure aboneliğini ekleme veya ilişkilendirme

1. Dizine, veritabanını barındıran Azure aboneliği için güvenilen bir dizin oluşturarak Azure aboneliğinizi Azure Active Directory ilişkilendirin. Ayrıntılar için bkz. [Azure Active Directory Kiracınıza Azure aboneliği ilişkilendirme veya ekleme](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Etki alanıyla ilişkili aboneliğe geçmek için Azure portal Dizin değiştiricisini kullanın.

   > [!IMPORTANT]
   > Her Azure aboneliği bir Azure AD örneğiyle güven ilişkisine sahiptir. Bu; Azure aboneliğinin kullanıcılar, hizmetler ve cihazlar için kimlik doğrulaması yapmak üzere bu dizine güvendiği anlamına gelir. Birden çok abonelik aynı dizine güvenebilir ancak bir abonelik yalnızca bir dizine güvenir. Aboneliğin bir dizinle arasındaki bu güven ilişkisi, bir aboneliğin daha çok abonelik alt kaynakları gibi olan, Azure'daki tüm diğer kaynaklarla (web siteleri, veritabanları ve benzeri) sahip olduğu ilişkiye benzer nitelikte değildir. Bir aboneliğin süresi dolarsa abonelikle ilişkili bu diğer kaynaklara erişim de durdurulur. Ancak dizin Azure içinde kalır, siz de başka bir aboneliği bu dizinle ilişkilendirebilir, dizin kullanıcılarını yönetmeye devam edebilirsiniz. Kaynaklar hakkında daha fazla bilgi için bkz. [Azure 'da kaynak erişimini anlama](../../active-directory/external-identities/add-users-administrator.md). Bu güvenilen ilişki hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory Azure aboneliğini ilişkilendirme veya ekleme](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>SQL veritabanında bir sunucu ile Azure AD Yöneticisi

Azure 'daki her [sunucu](logical-servers.md) (SQL veritabanı veya Azure SYNAPSE ' ni barındırır), sunucunun tamamının Yöneticisi olan tek bir sunucu yöneticisi hesabıyla başlar. Azure AD hesabı olarak ikinci bir yönetici hesabı oluşturun. Bu asıl sunucunun ana veritabanında kapsanan bir veritabanı kullanıcısı olarak oluşturulur. Yönetici hesapları her kullanıcı veritabanında **db_owner** rolün üyeleridir ve her bir kullanıcı veritabanını **dbo** kullanıcısı olarak girer. Yönetici hesapları hakkında daha fazla bilgi için bkz. [veritabanlarını ve oturum açma Işlemlerini yönetme](logins-create-manage.md).

Coğrafi çoğaltma ile Azure Active Directory kullanırken, hem birincil hem de ikincil sunucular için Azure Active Directory yöneticisinin yapılandırılması gerekir. Sunucuda Azure Active Directory yöneticisi yoksa, Azure Active Directory oturumlar ve kullanıcılar bir `Cannot connect` sunucu hatası alır.

> [!NOTE]
> Bir Azure AD hesabına (Sunucu Yöneticisi hesabı dahil) dayalı olmayan kullanıcılar, Azure AD tabanlı kullanıcılar oluşturamaz, çünkü bu kişiler önerilen veritabanı kullanıcılarını Azure AD ile doğrulama iznine sahip değildir.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Azure AD yöneticisi sağlama (SQL yönetilen örneği)

> [!IMPORTANT]
> Yalnızca bir Azure SQL yönetilen örneği sağlıyorsanız bu adımları izleyin. Bu işlem, Azure AD 'de yalnızca genel yönetici veya ayrıcalıklı rol yöneticisi tarafından yürütülebilir.
>
> **Genel önizlemede**, **Dizin OKUYUCULARı** rolünü Azure AD 'deki bir gruba atayabilirsiniz. Grup sahipleri daha sonra yönetilen örnek kimliğini bu grubun bir üyesi olarak ekleyebilir, bu da SQL yönetilen örneği için bir Azure AD yöneticisi sağlamanıza olanak tanır. Bu özellik hakkında daha fazla bilgi için bkz. [Azure SQL için Azure Active Directory Directory okuyucuları rolü](authentication-aad-directory-readers-role.md).

SQL yönetilen örneğinizin, güvenlik grubu üyeliği veya Yeni Kullanıcı oluşturma aracılığıyla kullanıcıların kimlik doğrulaması gibi görevleri başarılı bir şekilde gerçekleştirmek için Azure AD 'yi okuma izinlerine ihtiyacı vardır. Bunun çalışması için, Azure AD 'yi okumak üzere SQL yönetilen örneği iznini vermeniz gerekir. Azure portal veya PowerShell 'i kullanarak bunu yapabilirsiniz.

### <a name="azure-portal"></a>Azure portalı

SQL yönetilen örneğinizi Azure portal kullanarak Azure AD Okuma iznini vermek için Azure AD 'de Genel yönetici olarak oturum açın ve şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com), sağ üst köşede, olası etkin dizinlerin açılan listesinden bağlantınızı seçin.

2. Varsayılan Azure AD olarak doğru Active Directory seçin.

   Bu adım, Azure AD örneği ve SQL yönetilen örneği için aynı aboneliğin kullanıldığından emin olmak üzere Active Directory ile ilişkili aboneliği SQL yönetilen örneğine bağlar.

3. Azure AD tümleştirmesi için kullanmak istediğiniz SQL yönetilen örneğine gidin.

   ![Seçilen SQL yönetilen örneği için açık Active Directory yönetici sayfasını gösteren Azure portal ekran görüntüsü.](./media/authentication-aad-configure/aad.png)

4. Active Directory yönetici sayfasının üst başlığını seçin ve geçerli kullanıcıya izin verin.

    ![Active Directory erişmek için bir SQL yönetilen örneğine izin vermeye yönelik iletişim kutusunun ekran görüntüsü. İzin verme düğmesi seçilidir.](./media/authentication-aad-configure/grant-permissions.png)

5. İşlem başarılı olduktan sonra, sağ üst köşede aşağıdaki bildirim görünür:

    ![Yönetilen örnek için Active Directory okuma izinlerinin başarıyla güncelleştirildiğini onaylayan bir bildirimin ekran görüntüsü.](./media/authentication-aad-configure/success.png)

6. Artık SQL yönetilen örneğiniz için Azure AD yöneticinizle seçim yapabilirsiniz. Bunun için, Active Directory yönetici sayfasında, **yönetici komutunu ayarla** ' yı seçin.

    ![Seçili SQL yönetilen örneği için Active Directory yönetici sayfasında vurgulanan yönetici ayarla komutunun gösterildiği ekran görüntüsü.](./media/authentication-aad-configure/set-admin.png)

7. Azure AD Yöneticisi sayfasında, bir Kullanıcı arayın, yönetici olacak kullanıcıyı veya grubu seçin ve ardından **Seç**' i seçin.

   Active Directory yönetici sayfası, Active Directory tüm üyelerini ve gruplarını gösterir. Gri olan kullanıcılar veya gruplar Azure AD yöneticileri olarak desteklenmediği için seçilemez. [Azure AD özellikleri ve kısıtlamalarından](authentication-aad-overview.md#azure-ad-features-and-limitations)desteklenen Yöneticiler listesine bakın. Azure rol tabanlı erişim denetimi (Azure RBAC) yalnızca Azure portal geçerlidir ve SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE 'e yayılmaz.

    ![Azure Active Directory Yöneticisi ekleme](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. Active Directory Yöneticisi sayfasının en üstünde **Kaydet**' i seçin.

    ![Yönetici ayarla ve Yöneticiyi Kaldır düğmelerinin yanındaki üst satırdaki Kaydet düğmesine sahip Active Directory yönetici sayfasının ekran görüntüsü.](./media/authentication-aad-configure/save.png)

    Yöneticiyi değiştirme işlemi birkaç dakika sürebilir. Ardından yeni yönetici Active Directory yönetici kutusunda görünür.

SQL yönetilen örneğiniz için bir Azure AD yöneticisi sağlamaktan sonra, [create LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) sözdizimi Ile Azure AD Server sorumlularını (oturum açma) oluşturmaya başlayabilirsiniz. Daha fazla bilgi için bkz. [SQL yönetilen örneğe genel bakış](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration).

> [!TIP]
> Bir yöneticiyi daha sonra kaldırmak için, Active Directory yönetici sayfasının üst kısmında, **Yöneticiyi Kaldır**' ı seçin ve ardından **Kaydet**' i seçin.

### <a name="powershell"></a>PowerShell

PowerShell kullanarak SQL yönetilen örneği Azure AD Okuma iznini vermek için şu betiği çalıştırın:

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
# Can be executed only by a "Global Administrator" or "Privileged Role Administrator" type of user.

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

# Get service principal for your SQL Managed Instance
$roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}

# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

    #Write-Output "Dumping service principal '$($managedInstanceName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else {
    Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
}
```

### <a name="powershell-for-sql-managed-instance"></a>SQL yönetilen örneği için PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell cmdlet 'lerini çalıştırmak için Azure PowerShell yüklü ve çalışıyor olması gerekir. Ayrıntılı bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/).

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) modülü Azure SQL yönetilen örneği tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. AzureRM modülü, en az Aralık 2020 ' e kadar hata düzeltmeleri almaya devam edecektir.  Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır. Uyumluluklarını hakkında daha fazla bilgi için bkz. [new Azure PowerShell konusuna giriş az Module](/powershell/azure/new-azureps-module-az).

Bir Azure AD yöneticisi sağlamak için aşağıdaki Azure PowerShell komutları yürütün:

- Connect-AzAccount
- Select-AzSubscription

SQL yönetilen örneğiniz için Azure AD yöneticisi 'ni sağlamak ve yönetmek için kullanılan cmdlet 'ler aşağıdaki tabloda listelenmiştir:

| Cmdlet adı | Description |
| --- | --- |
| [Set-Azsqlınstanceactivedirectoryadministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Geçerli abonelikte SQL yönetilen örneği için bir Azure AD yöneticisi sağlar. (Geçerli abonelikte olmalıdır)|
| [Remove-Azsqlınstanceactivedirectoryadministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Geçerli abonelikteki SQL yönetilen örneği için bir Azure AD yöneticisini kaldırır. |
| [Get-Azsqlınstanceactivedirectoryadministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Geçerli abonelikteki SQL yönetilen örneği için bir Azure AD yöneticisiyle ilgili bilgileri döndürür.|

Aşağıdaki komut, ResourceGroup01 adlı bir kaynak grubuyla ilişkili ManagedInstance01 adlı SQL yönetilen örneği için bir Azure AD yöneticisiyle ilgili bilgileri alır.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Aşağıdaki komut, ManagedInstance01 adlı SQL yönetilen örneği için DBAs adlı bir Azure AD yönetici grubu sağlar. Bu sunucu, kaynak grubu ResourceGroup01 ile ilişkilendirilmiştir.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Aşağıdaki komut, ResourceGroup01 kaynak grubu ile ilişkili ManagedInstanceName01 adlı SQL yönetilen örneği için Azure AD yöneticisi 'ni kaldırır.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ayrıca, aşağıdaki CLı komutlarını çağırarak SQL yönetilen örneği için bir Azure AD yöneticisi sağlayabilirsiniz:

| Komut | Açıklama |
| --- | --- |
|[az SQL mı ad-admin Create](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_create) | SQL yönetilen örneği için bir Azure Active Directory Yöneticisi sağlar (geçerli abonelikte olmalıdır). |
|[az SQL mı ad-admin Delete](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_delete) | SQL yönetilen örneği için Azure Active Directory yöneticisini kaldırır. |
|[az SQL mı ad-yönetici listesi](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_list) | SQL yönetilen örneği için şu anda yapılandırılmış bir Azure Active Directory Yöneticisi hakkında bilgiler döndürür. |
|[az SQL mi ad-Admin Update](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_update) | SQL yönetilen örneği için Active Directory yöneticisini güncelleştirir. |

CLı komutları hakkında daha fazla bilgi için bkz. [az SQL mi](/cli/azure/sql/mi).

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Azure AD yöneticisi sağlama (SQL veritabanı)

> [!IMPORTANT]
> Yalnızca SQL veritabanı veya Azure SYNAPSE için bir [sunucu](logical-servers.md) sağlıyorsanız bu adımları izleyin.

Aşağıdaki iki yordam, Azure portal sunucusu için ve PowerShell kullanarak sunucunuzun Azure Active Directory yöneticisini nasıl sağlayacağınızı gösterir.

### <a name="azure-portal"></a>Azure portalı

1. [Azure Portal](https://portal.azure.com/), sağ üst köşede, olası etkin dizinlerin listesini aşağı eklemek için bağlantınızı seçin. Varsayılan Azure AD olarak doğru Active Directory seçin. Bu adım, Azure AD ve sunucu için aynı aboneliğin kullanıldığından emin olmak üzere abonelikle ilişkili Active Directory sunucuyla bağlantılandırır.

2. **SQL Server** araması yapın ve seçin.

    ![SQL Server arama ve seçme](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Bu sayfada, **SQL Server**'ı seçmeden önce, kategorinin en *sevdiğiniz* adı yanındaki **yıldızı** seçip sol gezinti çubuğuna **SQL sunucuları** ekleyebilirsiniz.

3. **SQL Server** sayfasında, **Active Directory yönetici**' yi seçin.

4. **Active Directory Yöneticisi** sayfasında, **yönetici ayarla**' yı seçin.

    ![Active Directory yönetici olarak ayarlanan SQL Server 'lar](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. **Yönetici Ekle** sayfasında, bir Kullanıcı arayın, yönetici olacak kullanıcıyı veya grubu seçin ve ardından **Seç**' i seçin. (Active Directory yönetici sayfası, Active Directory tüm üyelerini ve gruplarını gösterir. Gri olan kullanıcılar veya gruplar Azure AD yöneticileri olarak desteklenmediğinden seçilemiyor. ( [SQL veritabanı veya Azure SYNAPSE kimlik doğrulaması için Azure Active Directory kimlik doğrulaması kullan](authentication-aad-overview.md)' ın **Azure AD özellikleri ve sınırlamaları** bölümünde desteklenen Yöneticiler listesine bakın.) Azure rol tabanlı erişim denetimi (Azure RBAC) yalnızca portala uygulanır ve SQL Server yayılmaz.

    ![Azure Active Directory yönetici seçin](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. **Active Directory Yöneticisi** sayfasının en üstünde **Kaydet**' i seçin.

    ![Yöneticiyi Kaydet](./media/authentication-aad-configure/save-admin.png)

Yöneticiyi değiştirme işlemi birkaç dakika sürebilir. Ardından yeni yönetici **Active Directory yönetici** kutusunda görünür.

   > [!NOTE]
   > Azure AD yöneticisi ayarlanırken yeni yönetici adı (Kullanıcı veya grup), sanal ana veritabanında sunucu kimlik doğrulaması kullanıcısı olarak zaten mevcut olamaz. Varsa, Azure AD yönetici kurulumu başarısız olur; oluşturma geri alınıyor ve bu tür bir yöneticinin (ad) zaten var olduğunu gösterir. Bu tür bir sunucu kimlik doğrulama kullanıcısı Azure AD 'nin bir parçası olmadığından, Azure AD kimlik doğrulaması kullanarak sunucuya bağlanma çabaları başarısız olur.

Bir yöneticiyi daha sonra kaldırmak için, **Active Directory yönetici** sayfasının üst kısmında, **Yöneticiyi Kaldır**' ı seçin ve ardından **Kaydet**' i seçin.

### <a name="powershell-for-sql-database-and-azure-synapse"></a>SQL veritabanı ve Azure SYNAPSE için PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell cmdlet 'lerini çalıştırmak için Azure PowerShell yüklü ve çalışıyor olması gerekir. Ayrıntılı bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/). Bir Azure AD yöneticisi sağlamak için aşağıdaki Azure PowerShell komutları yürütün:

- Connect-AzAccount
- Select-AzSubscription

SQL veritabanı ve Azure SYNAPSE için Azure AD yöneticisi 'ni sağlamak ve yönetmek için kullanılan cmdlet 'ler:

| Cmdlet adı | Description |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |SQL veritabanı veya Azure SYNAPSE barındıran sunucu için Azure Active Directory Yöneticisi sağlar. (Geçerli abonelikte olmalıdır) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |SQL veritabanı veya Azure SYNAPSE barındıran sunucu için Azure Active Directory yöneticisini kaldırır.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |SQL veritabanı veya Azure SYNAPSE barındıran sunucu için şu anda yapılandırılmış bir Azure Active Directory Yöneticisi hakkındaki bilgileri döndürür. |

Bu komutların her biri hakkında daha fazla bilgi için Get-Help PowerShell komutunu kullanın. Örneğin, `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Aşağıdaki betik,  `40b79501-b343-44ed-9ce7-da4c8cc7353f` **Grup-23** adlı bir kaynak grubunda **DEMO_SERVER** sunucusu için DBA_Group (nesne kimliği) adlı bir Azure AD yönetici grubu sağlar:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** giriş parametresi, Azure AD görünen adını veya Kullanıcı asıl adını kabul eder. Örneğin ``DisplayName="John Smith"`` ve ``DisplayName="johns@contoso.com"``. Azure AD grupları için yalnızca Azure AD görünen adı desteklenir.

> [!NOTE]
> Azure PowerShell komutu, ```Set-AzSqlServerActiveDirectoryAdministrator``` Desteklenmeyen kullanıcılar Için Azure AD yöneticileri 'ni sağlamanıza engel olmaz. Desteklenmeyen bir Kullanıcı sağlanabilir, ancak bir veritabanına bağlanamaz.

Aşağıdaki örnek, isteğe bağlı **ObjectID**'yi kullanır:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> **DisplayName** benzersiz OLMADıĞıNDA Azure AD **ObjectID** gereklidir. **ObjectID** ve **DisplayName** değerlerini almak için, klasik Azure portalı Active Directory bölümünü kullanın ve bir kullanıcının veya grubun özelliklerini görüntüleyin.

Aşağıdaki örnek, sunucusu için geçerli Azure AD Yöneticisi hakkındaki bilgileri döndürür:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Aşağıdaki örnek bir Azure AD yöneticisini kaldırır:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Aşağıdaki CLı komutlarını çağırarak bir Azure AD yöneticisi sağlayabilirsiniz:

| Komut | Açıklama |
| --- | --- |
|[az SQL Server ad-admin Create](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) | SQL veritabanı veya Azure SYNAPSE barındıran sunucu için Azure Active Directory Yöneticisi sağlar. (Geçerli abonelikte olmalıdır) |
|[az SQL Server ad-admin Delete](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) | SQL veritabanı veya Azure SYNAPSE barındıran sunucu için Azure Active Directory yöneticisini kaldırır. |
|[az SQL Server ad-yönetici listesi](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) | SQL veritabanı veya Azure SYNAPSE barındıran sunucu için şu anda yapılandırılmış bir Azure Active Directory Yöneticisi hakkındaki bilgileri döndürür. |
|[az SQL Server ad-Admin Update](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) | SQL veritabanı veya Azure SYNAPSE barındıran sunucu için Active Directory yöneticisini güncelleştirir. |

CLı komutları hakkında daha fazla bilgi için bkz. [az SQL Server](/cli/azure/sql/server).

* * *

> [!NOTE]
> REST API 'Lerini kullanarak da Azure Active Directory Yöneticisi sağlayabilirsiniz. Daha fazla bilgi için bkz. [Azure SQL veritabanı Için Azure SQL veritabanı işlemlerine yönelik hizmet yönetimi REST API başvurusu ve işlemleri](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>İstemci bilgisayarlarınızı yapılandırma

Uygulamalarınızın veya kullanıcılarınızın Azure AD kimliklerini kullanarak SQL veritabanı 'na veya Azure SYNAPSE 'e bağlandığı tüm istemci makinelerde aşağıdaki yazılımları yüklemelisiniz:

- .NET Framework 4,6 veya sonraki bir sürümü [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) .
- SQL Server için Azure Active Directory kimlik doğrulama kitaplığı (*ADAL.DLL*). Aşağıda, *ADAL.DLL* kitaplığını içeren en son SSMS, ODBC ve OLE DB sürücüsünü yüklemek için karşıdan yükleme bağlantıları verilmiştir.
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [SQL Server için ODBC sürücüsü 17](/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)
  - [SQL Server için 18 OLE DB sürücüsü](/sql/connect/oledb/download-oledb-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)

Bu gereksinimleri şu şekilde karşılayabilirsiniz:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) veya [SQL Server veri araçları](/sql/ssdt/download-sql-server-data-tools-ssdt) en son sürümünü yüklemek .NET Framework 4,6 gereksinimini karşılar.
  - SSMS *ADAL.DLL* x86 sürümünü yüklüyor.
  - SSDT, *ADAL.DLL* AMD64 sürümünü yüklüyor.
  - [Visual Studio Indirmelerinin](https://www.visualstudio.com/downloads/download-visual-studio-vs) en son Visual studio indirmeleri .NET Framework 4,6 gereksinimini karşılar, ancak gerekli amd64 sürümünü *ADAL.DLL* yüklemez.

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Azure AD kimliklerine eşlenen bağımsız kullanıcılar oluşturma

SQL yönetilen örneği Azure AD sunucu sorumlularını (oturumlar) desteklediğinden, kapsanan veritabanı kullanıcıları kullanılması gerekli değildir. Azure AD sunucu sorumluları (oturum açma), Azure AD kullanıcılarından, gruplarından veya uygulamalarından oturum açma bilgileri oluşturmanıza olanak tanır. Bu, kapsanan bir veritabanı kullanıcısı yerine Azure AD sunucusu oturum açma bilgilerini kullanarak SQL yönetilen örneğinizle kimlik doğrulayabilmeniz anlamına gelir. Daha fazla bilgi için bkz. [SQL yönetilen örneğe genel bakış](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration). Azure AD Server sorumlularını (oturum açma) oluşturma hakkında sözdizimi için bkz. [oturum oluşturma](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

Ancak, SQL veritabanı ve Azure SYNAPSE ile Azure Active Directory kimlik doğrulaması kullanmak için Azure AD kimliğine göre kapsanan veritabanı kullanıcılarının kullanılması gerekir. Kapsanan bir veritabanı kullanıcısının ana veritabanında oturum açma bilgileri yoktur ve Azure AD 'de veritabanıyla ilişkili bir kimlikle eşlenir. Azure AD kimliği, tek bir kullanıcı hesabı ya da bir grup olabilir. Kapsanan veritabanı kullanıcıları hakkında daha fazla bilgi için bkz. [Kapsanan Veritabanı kullanıcıları-veritabanınızı taşınabilir hale getirme](/sql/relational-databases/security/contained-database-users-making-your-database-portable).

> [!NOTE]
> Veritabanı kullanıcıları (Yöneticiler hariç) Azure portal kullanılarak oluşturulamaz. Azure rolleri SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE veritabanına yayılmaz. Azure rolleri, Azure kaynaklarını yönetmek için kullanılır ve veritabanı izinlerine uygulanmaz. Örneğin, **SQL Server katılımcısı** rolü SQL VERITABANı, SQL yönetilen örneği veya Azure SYNAPSE veritabanına bağlanmak için erişim vermez. Erişim izni doğrudan veritabanında Transact-SQL deyimleri kullanılarak verilmelidir.

> [!WARNING]
> `:` `&` T-SQL ve deyimlerdeki Kullanıcı adları olarak dahil edildiğinde iki nokta veya ve işareti gibi özel karakterler `CREATE LOGIN` `CREATE USER` desteklenmez.

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
> Bu komut, oturum açmış kullanıcı adına SQL 'e Azure AD 'nin ("dış sağlayıcı") erişmesini gerektirir. Bazen Azure AD 'nin SQL 'e bir özel durum döndürmesine neden olacak durumlar ortaya çıkar. Bu durumlarda, Kullanıcı Azure AD 'ye özgü hata iletisini içermesi gereken SQL hatası 33134 ' i görür. Çoğu zaman, hata erişimin reddedildiğini veya kullanıcının kaynağa erişmek için MFA 'ya kaydolması gerekir ya da birinci taraf uygulamalar arasındaki erişimin ön kimlik doğrulama aracılığıyla işlenmesi gerekir. İlk iki durumda, sorun genellikle kullanıcının Azure AD kiracısında ayarlanan koşullu erişim ilkelerinin nedeni: kullanıcının dış sağlayıcıya erişmesini önler. Koşullu erişim ilkelerinin ' 00000002-0000-0000-C000-000000000000 ' uygulamasına erişime izin verecek şekilde güncelleştirilmesi (Azure AD Graph API uygulama KIMLIĞI) sorunu çözmelidir. Hatanın ilk taraf uygulamalar arasındaki erişimi, ön kimlik doğrulama aracılığıyla işlenmesi gerektiğinden sorun, kullanıcının hizmet sorumlusu olarak oturum açmış olmasından kaynaklanır. Bunun yerine bir kullanıcı tarafından yürütüldüğünde komutun başarılı olması gerekir.

> [!TIP]
> Azure aboneliğinizle ilişkili Azure Active Directory dışında bir Azure Active Directory doğrudan Kullanıcı oluşturamazsınız. Ancak, ilişkili Active Directory (dış kullanıcılar olarak bilinir) içeri aktarılan diğer etkin dizinlerin üyeleri, kiracı Active Directory bir Active Directory grubuna eklenebilir. Bu AD grubu için kapsanan bir veritabanı kullanıcısı oluşturarak, dış Active Directory kullanıcılar SQL veritabanına erişim sağlayabilir.

Azure Active Directory kimliklerine göre kapsanan veritabanı kullanıcıları oluşturma hakkında daha fazla bilgi için bkz. [Create User (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql).

> [!NOTE]
> Sunucu için Azure Active Directory yöneticisini kaldırmak, herhangi bir Azure AD kimlik doğrulama kullanıcısının sunucuya bağlanmasını engeller. Gerekirse, kullanılamayan Azure AD kullanıcıları bir SQL veritabanı yöneticisi tarafından el ile bırakılabilir.

> [!NOTE]
> Bir **bağlantı zaman aşımı süresi dolmuşsa**, `TransparentNetworkIPResolution` bağlantı dizesinin parametresini false olarak ayarlamanız gerekebilir. Daha fazla bilgi için bkz. [.NET Framework 4.6.1-TransparentNetworkIPResolution Ile bağlantı zaman aşımı sorunu](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution).

Bir veritabanı kullanıcısı oluşturduğunuzda, bu kullanıcı **bağlantı** iznini alır ve bu veritabanına **genel** rolün bir üyesi olarak bağlanabilir. Başlangıçta yalnızca Kullanıcı için kullanılabilen izinler, **genel** role veya üyesi oldukları herhangi BIR Azure AD grubuna verilen izinlere sahiptir. Azure AD tabanlı bir kapsanan veritabanı kullanıcısı sağladığınızda, diğer Kullanıcı türlerine izin verdiğiniz şekilde kullanıcıya ek izinler verebilirsiniz. Genellikle veritabanı rollerine izinler verin ve rollere kullanıcı ekleyin. Daha fazla bilgi için bkz. [veritabanı altyapısı Izin temelleri](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Özel SQL veritabanı rolleri hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda veritabanlarını ve oturum açma Işlemlerini yönetme](logins-create-manage.md).
Yönetilen bir etki alanına dış Kullanıcı olarak içeri aktarılan bir Federasyon etki alanı kullanıcı hesabı, yönetilen etki alanı kimliğini kullanmalıdır.

> [!NOTE]
> Azure AD kullanıcıları, E (EXTERNAL_USER) türü ve X (EXTERNAL_GROUPS) türündeki gruplar için veritabanı meta verilerinde işaretlenir. Daha fazla bilgi için bkz. [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>SSMS veya SSDT kullanarak veritabanına bağlanma  

Azure AD yöneticisi 'nin düzgün şekilde ayarlandığını onaylamak için, Azure AD yönetici hesabını kullanarak **ana** veritabanına bağlanın.
Azure AD tabanlı bir kapsanan veritabanı kullanıcısını (veritabanının sahibi olan Sunucu Yöneticisi dışında) sağlamak için veritabanına erişimi olan bir Azure AD kimliğiyle veritabanına bağlanın.

> [!IMPORTANT]
> Azure Active Directory kimlik doğrulaması desteği [SQL Server 2016 Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ve Visual Studio 2015 ' de [SQL Server veri araçları](/sql/ssdt/download-sql-server-data-tools-ssdt) ile kullanılabilir. SSMS 'nin 2016 Ağustos sürümü, yöneticilerin telefon araması, SMS mesajı, PIN ile akıllı kartlar veya mobil uygulama bildirimi kullanarak Multi-Factor Authentication gerektirmesini sağlayan Active Directory evrensel kimlik doğrulaması desteğini de içerir.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>SSMS veya SSDT kullanarak bağlanmak için bir Azure AD kimliği kullanma

Aşağıdaki yordamlarda, SQL Server Management Studio veya SQL Server veritabanı araçlarını kullanarak SQL veritabanı 'na bir Azure AD kimliğiyle nasıl bağlanabilmeniz gösterilmektedir.

### <a name="active-directory-integrated-authentication"></a>Active Directory tümleşik kimlik doğrulaması

Bir Federasyon etki alanındaki Azure Active Directory kimlik bilgilerinizi veya doğrudan geçiş ve parola karması kimlik doğrulaması için sorunsuz çoklu oturum açma için yapılandırılmış bir yönetilen etki alanını kullanarak Windows 'da oturum açtıysanız bu yöntemi kullanın. Daha fazla bilgi için bkz. [kesintisiz çoklu oturum açma Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md).

1. Management Studio veya veri araçlarını başlatın ve **sunucuya Bağlan** (veya **veritabanı altyapısına Bağlan**) Iletişim kutusunda, **kimlik doğrulama** kutusunda **Azure Active Directory tümleştirilmiş**' ı seçin. Mevcut kimlik bilgileriniz bağlantı için sunulacağından parola gerekmez veya girilemez.

   ![AD Tümleşik kimlik doğrulaması seçin][11]

2. **Seçenekler** düğmesini seçin ve **bağlantı özellikleri** sayfasında, **veritabanına Bağlan** kutusunda, bağlanmak istediğiniz kullanıcı veritabanının adını yazın. Daha fazla bilgi için, SSMS 17. x ve 18. x bağlantı özellikleri arasındaki farklılıklar üzerinde [Multi-Factor Azure AD auth](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter) makalesine bakın.

   ![Veritabanı adını seçin][13]

### <a name="active-directory-password-authentication"></a>Active Directory parola kimlik doğrulaması

Azure AD tarafından yönetilen etki alanını kullanarak bir Azure AD asıl adı ile bağlanırken bu yöntemi kullanın. Bunu, etki alanına erişimi olmayan federe hesaplar için de kullanabilirsiniz. Örneğin, uzaktan çalışırken.

SQL veritabanı ya da SQL yönetilen örneği ile SQL veritabanı kimlik doğrulaması yapmak için bu yöntemi kullanın. yalnızca Azure AD bulutu, kimlik kullanıcıları ya da Azure AD karma kimliklerini kullananlar. Bu yöntem, Windows kimlik bilgilerini kullanmak isteyen kullanıcıları destekler, ancak yerel makineleri etki alanına (örneğin, uzaktan erişim kullanılarak) katılmadı. Bu durumda, bir Windows kullanıcısı etki alanı hesabını ve parolasını belirtebilir ve SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE 'de veritabanında kimlik doğrulaması yapabilir.

1. Management Studio veya veri araçlarını başlatın ve **sunucuya Bağlan** (veya **veritabanı altyapısına Bağlan**) Iletişim kutusunda, **kimlik doğrulama** kutusunda **Azure Active Directory-Password**' ı seçin.

2. **Kullanıcı adı** kutusuna Azure Active Directory Kullanıcı adınızı **UserName \@ Domain.com** biçiminde yazın. Kullanıcı adları, Azure Active Directory sahip yönetilen veya Federasyon etki alanındaki bir Azure Active Directory veya bir hesaptan hesap olmalıdır.

3. **Parola** kutusuna Azure Active Directory hesabı veya yönetilen/Federasyon etki alanı hesabı için Kullanıcı parolanızı yazın.

    ![AD parola kimlik doğrulamasını seçin][12]

4. **Seçenekler** düğmesini seçin ve **bağlantı özellikleri** sayfasında, **veritabanına Bağlan** kutusunda, bağlanmak istediğiniz kullanıcı veritabanının adını yazın. (Önceki seçenekte bulunan grafiğe bakın.)

### <a name="active-directory-interactive-authentication"></a>Etkileşimli kimlik doğrulaması Active Directory

Etkileşimli kimlik doğrulaması için Multi-Factor Authentication (MFA) ile veya olmadan etkileşimli kimlik doğrulaması için bu yöntemi kullanın, parola etkileşimli olarak istenir. Bu yöntem, SQL veritabanı, SQL yönetilen örneği ve Azure AD karma kimlik kullanıcıları için Azure SYNAPSE 'da veritabanında kimlik doğrulaması yapmak için kullanılabilir.

Daha fazla bilgi için bkz. [SQL veritabanı ile Multi-Factor Azure AD kimlik doğrulamasını kullanma ve Azure Synapse (MFA IÇIN SSMS desteği)](authentication-mfa-ssms-overview.md).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Bir istemci uygulamasından bağlanmak için bir Azure AD kimliği kullanma

Aşağıdaki yordamlarda, bir SQL veritabanına bir Azure AD kimliğiyle bir istemci uygulamasından nasıl bağlanacağı gösterilmektedir.

### <a name="active-directory-integrated-authentication"></a>Active Directory tümleşik kimlik doğrulaması

Tümleşik Windows kimlik doğrulamasını kullanmak için, etki alanının Active Directory Azure Active Directory ile federe olması veya doğrudan geçiş ya da parola karması kimlik doğrulaması için sorunsuz çoklu oturum açma için yapılandırılmış bir yönetilen etki alanı olması gerekir. Daha fazla bilgi için bkz. [kesintisiz çoklu oturum açma Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md).

> [!NOTE]
> Tümleşik Windows kimlik doğrulaması için [msal.net (Microsoft. Identity. Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) , geçişli ve parola karması kimlik doğrulaması için sorunsuz çoklu oturum açma için desteklenmez.

Veritabanına bağlanan istemci uygulamanızın (veya bir hizmetin), bir kullanıcının etki alanı kimlik bilgileri altında etki alanına katılmış bir makinede çalışıyor olması gerekir.

Tümleşik kimlik doğrulaması ve bir Azure AD kimliği kullanarak bir veritabanına bağlanmak için, veritabanı bağlantı dizesindeki authentication anahtar sözcüğünün olarak ayarlanması gerekir `Active Directory Integrated` . Aşağıdaki C# kod örneği ADO .NET kullanır.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

`Integrated Security=True`Azure SQL veritabanı 'na bağlanmak için bağlantı dizesi anahtar sözcüğü desteklenmiyor. ODBC bağlantısı yaparken, boşlukları kaldırmanız ve kimlik doğrulamasını ' Activedirectoryıntegrated ' olarak ayarlamanız gerekir.

### <a name="active-directory-password-authentication"></a>Active Directory parola kimlik doğrulaması

Yalnızca Azure AD bulutu kimlik Kullanıcı hesapları veya Azure AD karma kimlikleri kullanan bir veritabanına bağlanmak için, kimlik doğrulaması anahtar sözcüğü olarak ayarlanmalıdır `Active Directory Password` . Bağlantı dizesinin Kullanıcı KIMLIĞI/UID ve Password/PWD anahtar sözcükleri ve değerleri içermesi gerekir. Aşağıdaki C# kod örneği ADO .NET kullanır.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Azure AD [kimlik doğrulaması GitHub tanıtımı](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)'nda bulunan tanıtım kodu örneklerini kullanarak Azure AD kimlik doğrulama yöntemleri hakkında daha fazla bilgi edinin.

## <a name="azure-ad-token"></a>Azure AD belirteci

Bu kimlik doğrulama yöntemi, Azure AD 'den bir belirteç alarak SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE ' deki veritabanına bağlanmak için, orta katmanlı hizmetlerin [JSON Web belirteçleri (JWT)](../../active-directory/develop/id-tokens.md) almasına olanak tanır. Bu yöntem, sertifika tabanlı kimlik doğrulaması kullanan hizmet kimlikleri, hizmet sorumluları ve uygulamalar dahil çeşitli uygulama senaryolarına izin vermez. Azure AD belirteç kimlik doğrulamasını kullanmak için dört temel adımı tamamlamalısınız:

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

Daha fazla bilgi için bkz. [güvenlik blogu SQL Server](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth). Sertifika ekleme hakkında daha fazla bilgi için, bkz. [Azure Active Directory Sertifika tabanlı kimlik doğrulaması ile çalışmaya başlama](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Aşağıdaki deyimler, [Indirme merkezinden](https://www.microsoft.com/download/details.aspx?id=53591)erişilebilen sqlcmd sürüm 13,1 ' i kullanarak bağlanır.

> [!NOTE]
> `sqlcmd``-G`komutuyla sistem kimlikleriyle birlikte çalışmaz ve Kullanıcı sorumlusu oturum açması gerekir.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Azure AD kimlik doğrulaması sorunlarını giderme

Azure AD kimlik doğrulamasıyla ilgili sorunları gidermeye yönelik yönergeler aşağıdaki blogda bulunabilir: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Sonraki adımlar

- SQL veritabanı 'nda oturum açma bilgileri, kullanıcılar, veritabanı rolleri ve izinlere genel bakış için bkz. [oturum açma bilgileri, kullanıcılar, veritabanı rolleri ve Kullanıcı hesapları](logins-create-manage.md).
- Veritabanı sorumluları hakkında daha fazla bilgi için bkz. [Sorumlular](/sql/relational-databases/security/authentication-access/principals-database-engine).
- Veritabanı rolleri hakkında daha fazla bilgi için bkz. [Veritabanı rolleri](/sql/relational-databases/security/authentication-access/database-level-roles).
- SQL Veritabanındaki güvenlik duvarı kuralları hakkında daha fazla bilgi için bkz. [SQL Veritabanı güvenlik duvarı kuralları](firewall-configure.md).
- Azure AD Konuk kullanıcısını Azure AD yöneticisi olarak ayarlama hakkında daha fazla bilgi için bkz. [Azure AD Konuk kullanıcıları oluşturma ve Azure AD yöneticisi olarak ayarlama](authentication-aad-guest-users.md).
- Azure SQL ile sorumlular hakkında daha fazla bilgi için bkz. Azure AD [uygulamaları kullanarak Azure AD kullanıcıları oluşturma](authentication-aad-service-principal-tutorial.md)

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
