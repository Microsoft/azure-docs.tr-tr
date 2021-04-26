---
title: Azure AD grubuna Dizin okuyucuları rolü atama ve rol atamalarını yönetme
description: Bu makalede Azure AD gruplarını kullanarak Azure AD rol atamalarını Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics ile yönetmek için Dizin okuyucuları rolünü etkinleştirme konusunda size kılavuzluk eder
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: bc809cf02b827b7498890cb7d929c44bd360ab53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99094750"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>Öğretici: Azure AD grubuna Dizin okuyucuları rolü atama ve rol atamalarını yönetme

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Bu makaledeki bir gruba **Dizin okuyucuları** rolü ataması **genel önizlemede**. 

Bu makale, Azure Active Directory (Azure AD) içinde bir grup oluşturma ve bu gruba [**Dizin okuyucuları**](../../active-directory/roles/permissions-reference.md#directory-readers) rolü atama konusunda size rehberlik eder. Dizin okuyucuları izinleri grup sahiplerinin, gruba, [Azure SQL veritabanı](sql-database-paas-overview.md), [Azure SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md)ve [Azure SYNAPSE Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)'in [yönetilen kimliği](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) gibi ek Üyeler eklemesine izin verir. Bu, Kiracıdaki her bir Azure SQL mantıksal sunucu kimliği için Dizin okuyucuları rolünü doğrudan atamak üzere [genel yönetici](../../active-directory/roles/permissions-reference.md#global-administrator) veya [ayrıcalıklı rol yöneticisinin](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) gereksinimini atlar.

Bu öğretici, [Azure Active Directory (Önizleme) içinde rol atamalarını yönetmek için bulut gruplarını kullanma](../../active-directory/roles/groups-concept.md)bölümünde sunulan özelliği kullanır. 

Azure SQL için bir Azure AD grubuna Dizin okuyucuları rolünü atamanın avantajları hakkında daha fazla bilgi için bkz. [Azure SQL için Azure Active Directory Directory okuyucuları rolü](authentication-aad-directory-readers-role.md).

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure AD örneği. Daha fazla bilgi için bkz. Azure [SQL Ile Azure AD kimlik doğrulamasını yapılandırma ve yönetme](authentication-aad-configure.md).
- Bir SQL veritabanı, SQL yönetilen örneği veya Azure SYNAPSE.

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>Azure portal kullanarak Dizin okuyucuları rol ataması

### <a name="create-a-new-group-and-assign-owners-and-role"></a>Yeni bir grup oluştur ve sahipleri ve rolü ata

1. Bu ilk kurulum için [genel yönetici](../../active-directory/roles/permissions-reference.md#global-administrator) veya [ayrıcalıklı rol yöneticisi](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) izinlerine sahip bir kullanıcı gereklidir.
1. Ayrıcalıklı kullanıcının [Azure Portal](https://portal.azure.com)oturum açmasını sağlayabilirsiniz.
1. **Azure Active Directory** kaynağına gidin. **Yönetilen** altında **gruplar**' a gidin. Yeni grup oluşturmak için yeni **Grup** ' u seçin.
1. Grup türü olarak **güvenlik** ' i seçin ve alanları geri kalanını girin. **Azure AD rollerinin, gruba atanabileceği (Önizleme)** ayarının **Evet**'e geçdiğinizden emin olun. Ardından Azure AD **Dizin okuyucuları** rolünü gruba atayın.
1. Azure AD kullanıcılarını oluşturulan gruba sahip olarak atayın. Bir grup sahibi, herhangi bir Azure AD Yönetim rolü atanmadan normal bir AD kullanıcısı olabilir. Sahip, SQL veritabanınızı, SQL yönetilen örneğinizi veya Azure SYNAPSE 'yi yöneten bir kullanıcı olmalıdır.

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="aad-yeni-Grup":::

1. **Oluştur**’u seçin

### <a name="checking-the-group-that-was-created"></a>Oluşturulan grup denetleniyor

> [!NOTE]
> **Grup türünün** **güvenlik** olduğundan emin olun. *Microsoft 365* gruplar Azure SQL için desteklenmez.

Oluşturulan grubu denetlemek ve yönetmek için, Azure portal **gruplar** bölmesine dönün ve grup adınızı arayın. Grubu seçtikten sonra, ayarları **Yönet** ' in **sahipler** ve **Üyeler** menüsünün altına ek sahipler ve Üyeler eklenebilir. Ayrıca grup için **atanan rolleri** gözden geçirebilirsiniz.

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="Üyeler, sahipler ve atanan roller (Önizleme) için ayarlar menülerini açan bağlantıları içeren bir grup bölmesinin ekran görüntüsü.":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>Gruba Azure SQL yönetilen kimliği ekleme

> [!NOTE]
> Bu örnek için SQL yönetilen örneği kullanıyoruz, ancak aynı sonuçlara ulaşmak için SQL veritabanı veya Azure SYNAPSE için benzer adımlar uygulanabilir.

Sonraki adımlar için, genel yönetici veya ayrıcalıklı rol yöneticisi kullanıcısına artık gerek yoktur.

1. SQL yönetilen örneğini yöneten kullanıcı olarak Azure portal oturum açın ve daha önce oluşturulan grubun sahibidir.

1. Azure portal **SQL yönetilen örnek** kaynağınızın adını bulun.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="SQL yönetilen örnekler ekranının SQL örnek adı ssomtest ve subnet Name ManagedInstance vurgulanmış ekran görüntüsü.":::

   SQL yönetilen örneğinizin oluşturulması sırasında örneğiniz için bir Azure kimliği oluşturulmuştur. Oluşturulan kimlik, SQL yönetilen örnek adınızın ön ekiyle aynı ada sahip. Aşağıdaki adımları izleyerek, bir Azure AD uygulaması olarak oluşturulan SQL yönetilen örnek Kimliğiniz için hizmet sorumlusu ' nı bulabilirsiniz:

    - **Azure Active Directory** kaynağına gidin. **Yönet** ayarı altında **Kurumsal uygulamalar**' ı seçin. **Nesne kimliği** , örneğin kimliğidir.
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="SQL yönetilen örneğinin nesne KIMLIĞINE sahip bir Azure Active Directory kaynağa yönelik kurumsal uygulamalar sayfasının ekran görüntüsü.":::

1. **Azure Active Directory** kaynağına gidin. **Yönetilen** altında **gruplar**' a gidin. Oluşturduğunuz grubu seçin. Grubunuzun **yönetilen** ayarı altında **Üyeler**' i seçin. Yukarıda bulunan adı arayarak **üye Ekle** ' yi SEÇIN ve SQL yönetilen örnek hizmeti sorumlunuzu grubun bir üyesi olarak ekleyin.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="Yeni üye olarak bir SQL yönetilen örneği eklemek için vurgulanmış seçeneklere sahip bir Azure Active Directory kaynağı için Üyeler sayfasının ekran görüntüsü.":::

> [!NOTE]
> Hizmet sorumlusu izinlerinin Azure sistemi aracılığıyla yayılması ve Azure AD Graph API erişimine izin vermek birkaç dakika sürebilir. SQL yönetilen örneği için bir Azure AD yöneticisi sağlamadan önce birkaç dakika beklemeniz gerekebilir.

### <a name="remarks"></a>Açıklamalar

SQL veritabanı ve Azure SYNAPSE için sunucu kimliği, Azure SQL mantıksal sunucusu oluşturma sırasında veya sunucu oluşturulduktan sonra oluşturulabilir. SQL veritabanı veya Azure SYNAPSE 'da sunucu kimliği oluşturma veya ayarlama hakkında daha fazla bilgi için bkz. [Azure AD kullanıcıları oluşturmak için hizmet sorumlularını etkinleştirme](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

[Yönetilen örnek için bir Azure AD yöneticisi](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)ayarlamadan önce, SQL yönetilen örneği Için, **Dizin okuyucular** rolü yönetilen örnek kimliğine atanmalıdır.

Mantıksal sunucu için bir Azure AD yöneticisi ayarlanırken SQL veritabanı veya Azure SYNAPSE için **Dizin okuyucuları** rolünü sunucu kimliğine atama gerekli değildir. Ancak, bir Azure AD uygulaması adına SQL veritabanı veya Azure SYNAPSE 'de Azure AD nesne oluşturmayı etkinleştirmek için **Dizin okuyucuları** rolü gereklidir. Rol SQL mantıksal sunucu kimliğine atanmamışsa Azure SQL 'de Azure AD kullanıcıları oluşturmak başarısız olur. Daha fazla bilgi için bkz. [Azure SQL ile Azure Active Directory hizmet sorumlusu](authentication-aad-service-principal.md).

## <a name="directory-readers-role-assignment-using-powershell"></a>PowerShell kullanarak Dizin okuyucuları rol ataması

> [!IMPORTANT]
> [Genel yönetici](../../active-directory/roles/permissions-reference.md#global-administrator) veya [ayrıcalıklı rol yöneticisinin](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) bu ilk adımları çalıştırması gerekir. PowerShell 'e ek olarak Azure AD, [Azure AD 'de rol atanabilir bir grup oluşturmak](../../active-directory/roles/groups-create-eligible.md#using-microsoft-graph-api)IÇIN Microsoft Graph API sunar.

1. Aşağıdaki komutları kullanarak Azure AD Preview PowerShell modülünü indirin. PowerShell 'i yönetici olarak çalıştırmanız gerekebilir.

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. Azure AD kiracınıza bağlanın.

    ```powershell
    Connect-AzureAD
    ```

1. **Dizin okuyucuları** rolünü atamak için bir güvenlik grubu oluşturun.

    - `DirectoryReaderGroup`, `Directory Reader Group` ve `DirRead` tercihinize göre değiştirilebilir.

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. Gruba **Dizin okuyucuları** rolü atayın.

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. Gruba sahip atayın.

    - `<username>`Bu gruba sahip olmak istediğiniz kullanıcıyla değiştirin. Bu adımlar tekrarlanarak çeşitli sahipler eklenebilir.

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    Aşağıdaki komutu kullanarak grubun sahiplerini denetleyin:

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    Ayrıca [Azure Portal](https://portal.azure.com)grubun sahiplerini doğrulayabilirsiniz. [Oluşturulan grubu denetlemek](#checking-the-group-that-was-created)için adımları izleyin.

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>Hizmet sorumlusu grubun üyesi olarak atanıyor

Sonraki adımlar için, genel yönetici veya ayrıcalıklı rol yöneticisi kullanıcısına artık gerek yoktur.

1. Azure SQL kaynağını da yöneten grubun sahibini kullanarak Azure AD 'nize bağlanmak için aşağıdaki komutu çalıştırın.

    ```powershell
    Connect-AzureAD
    ```

1. Hizmet sorumlusunu oluşturulan grubun bir üyesi olarak atayın.

    - `<ServerName>`Azure SQL mantıksal sunucu adınızla veya yönetilen örnek adınızla değiştirin. Daha fazla bilgi için, [gruba Azure SQL Service Identity ekleme](#add-azure-sql-managed-identity-to-the-group) bölümüne bakın

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    Aşağıdaki komut, gruba eklendiğini belirten hizmet sorumlusu nesne KIMLIĞINI döndürür:

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL için Azure Active Directory Directory okuyucuları rolü](authentication-aad-directory-readers-role.md)
- [Öğretici: Azure AD uygulamalarını kullanarak Azure AD kullanıcıları oluşturma](authentication-aad-service-principal-tutorial.md)
- [Azure SQL ile Azure AD kimlik doğrulamasını yapılandırma ve yönetme](authentication-aad-configure.md)