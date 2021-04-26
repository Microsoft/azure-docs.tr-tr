---
title: Tüm Azure aboneliklerini ve yönetim gruplarını yönetmek için erişimi yükseltme
description: Azure portal veya REST API kullanarak Azure Active Directory tüm abonelikleri ve Yönetim gruplarını yönetmek üzere genel yönetici için erişimin nasıl yükseltilceğini açıklar.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/09/2020
ms.author: rolyon
ms.openlocfilehash: 37d50c030a2b426cb3e9af57afb899b7fab68388
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778483"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Tüm Azure aboneliklerini ve yönetim gruplarını yönetmek için erişimi yükseltme

Azure Active Directory (Azure AD) içinde genel yönetici olarak, dizininizdeki tüm aboneliklere ve yönetim gruplarına erişiminiz olmayabilir. Bu makalede, erişiminizi tüm aboneliklere ve yönetim gruplarına yükseltebilmeniz gereken yollar açıklanmaktadır.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Erişiminizi neden yükseltmek gerekir?

Genel yöneticiyseniz, aşağıdaki işlemleri yapmak istediğiniz zaman olabilir:

- Bir kullanıcı erişimi kaybettiği zaman bir Azure aboneliğine veya yönetim grubuna yeniden erişim elde edin
- Başka bir kullanıcıya veya kendisine Azure aboneliği veya yönetim grubu üzerinde erişim verme
- Bir kuruluştaki tüm Azure aboneliklerine veya yönetim gruplarına bakın
- Otomasyon uygulamasının (faturalama veya denetim uygulaması gibi) tüm Azure aboneliklerine veya yönetim gruplarına erişmesine izin ver

## <a name="how-does-elevated-access-work"></a>Yükseltilmiş erişim nasıl çalışır?

Azure AD ve Azure kaynakları birbirinden bağımsız olarak güvenli hale getirilir. Diğer bir deyişle, Azure AD rol atamaları Azure kaynaklarına erişim vermez ve Azure rol atamaları Azure AD 'ye erişim vermez. Ancak, Azure AD 'de [genel yöneticiyseniz](../active-directory/roles/permissions-reference.md#global-administrator) , kendi dizininizdeki tüm Azure aboneliklerine ve yönetim gruplarına erişim izni atayabilirsiniz. Sanal makineler veya depolama hesapları gibi Azure abonelik kaynaklarına erişiminiz yoksa ve bu kaynaklara erişim kazanmak için genel yönetici ayrıcalıklarınızı kullanmak istiyorsanız bu özelliği kullanın.

Erişiminizi yükseltirse, Azure 'da kök kapsamında () [Kullanıcı erişimi yönetici](built-in-roles.md#user-access-administrator) rolü atamış olursunuz `/` .Bu, tüm kaynakları görüntülemenize ve dizindeki herhangi bir aboneliğe veya yönetim grubuna erişim atamanıza olanak tanır. Kullanıcı erişimi yönetici rolü atamaları Azure PowerShell, Azure CLı veya REST API kullanılarak kaldırılabilir.

Kök kapsamda yapmanız gereken değişiklikleri yaptıktan sonra bu yükseltilmiş erişimi kaldırmalısınız.

![Erişimi yükseltme](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure portalı

### <a name="elevate-access-for-a-global-administrator"></a>Genel yönetici için erişimi yükseltme

Azure portal kullanarak genel yöneticiye erişimi yükseltmek için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com) veya [Azure Active Directory Yönetim merkezinde](https://aad.portal.azure.com) genel yönetici olarak oturum açın.

    Azure AD Privileged Identity Management kullanıyorsanız, [genel yönetici rolü atamasını etkinleştirin](../active-directory/privileged-identity-management/pim-how-to-activate-role.md).

1. **Azure Active Directory** açın.

1. **Yönet**'in altında **Özellikler**'i seçin.

   ![Azure Active Directory özellikleri için özellikleri seçin-ekran görüntüsü](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. **Azure kaynakları Için erişim yönetimi** altında, geçiş seçeneğini **Evet** olarak ayarlayın.

   ![Azure kaynakları için erişim yönetimi-ekran görüntüsü](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Geçiş seçeneğini **Evet** olarak belirlediğinizde, Azure RBAC 'de kök kapsamda (/) Kullanıcı erişimi Yöneticisi rolü atanır. Bu, tüm Azure aboneliklerine ve bu Azure AD diziniyle ilişkili yönetim gruplarına rol atama izni verir. Bu geçiş yalnızca Azure AD 'de Genel yönetici rolü atanmış olan kullanıcılar tarafından kullanılabilir.

   **Hayır** olarak ayarlandığında, Azure RBAC 'Deki Kullanıcı erişimi yönetici rolü kullanıcı hesabınızdan kaldırılır. Artık bu Azure AD diziniyle ilişkili tüm Azure aboneliklerinde ve yönetim gruplarında rol atayamazsınız. Yalnızca erişim izni verilen Azure aboneliklerini ve Yönetim gruplarını görüntüleyebilir ve yönetebilirsiniz.

    > [!NOTE]
    > [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)kullanıyorsanız, rol atamalarınızın devre dışı bırakılması **Azure kaynakları Için erişim yönetiminin** **Hayır** olarak değişmez. En az ayrıcalıklı erişimi sürdürmek için, rol atamalarınızı devre dışı bırakmadan önce bu geçişi **Hayır** olarak ayarlamanızı öneririz.
    
1. Ayarınızı kaydetmek için **Kaydet** ' e tıklayın.

   Bu ayar genel bir özellik değildir ve yalnızca şu anda oturum açmış olan kullanıcı için geçerlidir. Genel yönetici rolünün tüm üyeleri için erişimi yükselmez.

1. Oturumunuzu yenilemek için oturumunuzu kapatın ve yeniden oturum açın.

    Artık dizininizdeki tüm aboneliklere ve yönetim gruplarına erişiminizin olması gerekir. Erişim denetimi (ıAM) bölmesini görüntülediğinizde, kök kapsamda Kullanıcı erişimi yönetici rolü atandığını fark edersiniz.

   ![Kök kapsamına sahip abonelik rolü atamaları-ekran görüntüsü](./media/elevate-access-global-admin/iam-root.png)

1. Yükseltilmiş erişim sırasında yapmanız gereken değişiklikleri yapın.

    Rol atama hakkında daha fazla bilgi için bkz. [Azure Portal kullanarak Azure rolleri atama](role-assignments-portal.md). Privileged Identity Management kullanıyorsanız bkz. [Azure Kaynak rollerini](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)yönetmek veya atamak [Için Azure kaynaklarını bulma](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) .

1. Yükseltilmiş erişiminizi kaldırmak için aşağıdaki bölümdeki adımları uygulayın.

### <a name="remove-elevated-access"></a>Yükseltilmiş erişimi kaldır

Kök kapsamda () Kullanıcı erişimi yönetici rolü atamasını kaldırmak için `/` aşağıdaki adımları izleyin.

1. Erişimi yükseltmek için kullanılan Kullanıcı olarak oturum açın.

1. Gezinti listesinde **Azure Active Directory** ' a ve ardından **Özellikler**' e tıklayın.

1. **Azure kaynakları Için erişim yönetimini** yeniden **Hayır** olarak ayarlayın. Bu, Kullanıcı başına bir ayar olduğundan, erişimi yükseltmek için aynı kullanıcı olarak oturum açmış olmanız gerekir.

    Erişim denetimi (ıAM) bölmesinde kullanıcı erişimi yönetici rolü atamasını kaldırmaya çalışırsanız aşağıdaki iletiyi görürsünüz. Rol atamasını kaldırmak için, geri tuşu **Hayır** olarak ayarlamanız veya Azure PowerShell, Azure clı veya REST API kullanmanız gerekir.

    ![Kök kapsamıyla rol atamalarını kaldırma](./media/elevate-access-global-admin/iam-root-remove.png)

1. Genel yönetici olarak oturum açın.

    Privileged Identity Management kullanıyorsanız, genel yönetici rolü atamalarınızı devre dışı bırakın.

    > [!NOTE]
    > [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)kullanıyorsanız, rol atamalarınızın devre dışı bırakılması **Azure kaynakları Için erişim yönetiminin** **Hayır** olarak değişmez. En az ayrıcalıklı erişimi sürdürmek için, rol atamalarınızı devre dışı bırakmadan önce bu geçişi **Hayır** olarak ayarlamanızı öneririz.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Kök kapsamda rol atamasını listeleyin (/)

Kök kapsamdaki () bir kullanıcıya yönelik kullanıcı erişimi yönetici rolü atamasını listelemek için `/` [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment) komutunu kullanın.

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>Yükseltilmiş erişimi kaldır

Kullanıcı erişimi yönetici rolü atamasını kendinize veya kök kapsamdaki başka bir kullanıcıya () kaldırmak için `/` aşağıdaki adımları izleyin.

1. Yükseltilmiş erişimi kaldırabilirler bir kullanıcı olarak oturum açın. Bu, erişimi veya başka bir genel yöneticiyi kök kapsamda yükseltilmiş erişimle yükseltmek için kullanılan Kullanıcı ile aynı olabilir.

1. Kullanıcı erişimi yönetici rolü atamasını kaldırmak için [Remove-Azroleatama](/powershell/module/az.resources/remove-azroleassignment) komutunu kullanın.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="elevate-access-for-a-global-administrator"></a>Genel yönetici için erişimi yükseltme

Azure CLı kullanarak genel yöneticiye erişimi yükseltmek için aşağıdaki temel adımları kullanın.

1. Bu uç noktayı çağırmak için [az Rest](/cli/azure/reference-index#az_rest) komutunu kullanın `elevateAccess` . Bu, size kök kapsamda () Kullanıcı erişimi yönetici rolü verir `/` .

    ```azurecli
    az rest --method post --url "/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01"
    ```

1. Yükseltilmiş erişim sırasında yapmanız gereken değişiklikleri yapın.

    Rol atama hakkında daha fazla bilgi için bkz. Azure [CLI kullanarak Azure rolleri atama](role-assignments-cli.md).

1. Yükseltilmiş erişiminizi kaldırmak için adımları sonraki bir bölümde gerçekleştirin.

### <a name="list-role-assignment-at-root-scope-"></a>Kök kapsamda rol atamasını listeleyin (/)

Kök kapsamdaki () bir kullanıcıya yönelik kullanıcı erişimi yönetici rolü atamasını listelemek için `/` [az role atama listesi](/cli/azure/role/assignment#az_role_assignment_list) komutunu kullanın.

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>Yükseltilmiş erişimi kaldır

Kullanıcı erişimi yönetici rolü atamasını kendinize veya kök kapsamdaki başka bir kullanıcıya () kaldırmak için `/` aşağıdaki adımları izleyin.

1. Yükseltilmiş erişimi kaldırabilirler bir kullanıcı olarak oturum açın. Bu, erişimi veya başka bir genel yöneticiyi kök kapsamda yükseltilmiş erişimle yükseltmek için kullanılan Kullanıcı ile aynı olabilir.

1. Kullanıcı erişimi yönetici rolü atamasını kaldırmak için [az role atama Delete](/cli/azure/role/assignment#az_role_assignment_delete) komutunu kullanın.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>Genel yönetici için erişimi yükseltme

REST API kullanarak genel yöneticiye erişimi yükseltmek için aşağıdaki temel adımları kullanın.

1. , `elevateAccess` Size kök kapsamda () Kullanıcı erişimi Yöneticisi rolü sağlayan Rest, çağrısını kullanarak `/` .

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Yükseltilmiş erişim sırasında yapmanız gereken değişiklikleri yapın.

    Rol atama hakkında daha fazla bilgi için bkz. [REST API kullanarak Azure rolleri atama](role-assignments-rest.md).

1. Yükseltilmiş erişiminizi kaldırmak için adımları sonraki bir bölümde gerçekleştirin.

### <a name="list-role-assignments-at-root-scope-"></a>Kök kapsamındaki rol atamalarını listeleyin (/)

Bir kullanıcı için tüm rol atamalarını kök kapsamda ( `/` ) listeleyebilirsiniz.

- Rol atamalarını almak istediğiniz kullanıcının nesne KIMLIĞI olan [Get Roleatamaları](/rest/api/authorization/roleassignments/listforscope) çağırın `{objectIdOfUser}` .

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Kök kapsamdaki atamaları reddetme listesi (/)

Bir kullanıcı için tüm reddetme atamalarını kök kapsamda ( `/` ) listeleyebilirsiniz.

- `{objectIdOfUser}`Reddetme atamalarını almak istediğiniz kullanıcının nesne kimliği olan get Denyaslarından çağırın.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Yükseltilmiş erişimi kaldır

`elevateAccess`' İ çağırdığınızda, kendiniz için bir rol ataması oluşturursunuz, bu ayrıcalıkları iptal etmek Için Kullanıcı erişimi yönetici rolü atamasını kök kapsamında () kaldırmanız gerekir `/` .

1. Kullanıcı [](/rest/api/authorization/roledefinitions/get) erişimi Yöneticisi `roleName` rolünün ad kimliğini belirleyebilmek Için Kullanıcı erişimi Yöneticisi 'Ne eşit olan roledefinitions ' i çağırın.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    `name`Bu durumda, parametreyi parametresinden kaydedin `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` .

1. Dizin kapsamındaki Dizin yöneticisinin rol atamasını de listeetmeniz gerekir. Yükseltme erişimi çağrısını yapan Dizin yöneticisinin Dizin kapsamındaki tüm atamaları listeleyin `principalId` . Bu işlem, ObjectID için dizindeki tüm atamaları listeler.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >Bir dizin yöneticisinin birçok ataması olmaması gerekir. önceki sorgu çok fazla atama döndürürse, yalnızca dizin kapsamı düzeyindeki tüm atamaları sorgulayabilir ve sonra sonuçları filtreleyebilirsiniz: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. Önceki çağrılar, rol atamalarının bir listesini döndürür. Kapsamın bulunduğu rol atamasını `"/"` ve `roleDefinitionId` 1. adımda bulduğunuz rol adı kimliği ile biter ve `principalId` Dizin yöneticisinin ObjectID ile eşleşen rolü bulun. 
    
    Örnek rol ataması:
    
    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    Yine, `name` parametreyi bu durumda 11111111-1111-1111-1111-111111111111 olarak kaydedin.

1. Son olarak, tarafından eklenen atamayı kaldırmak için rol atama KIMLIĞINI kullanın `elevateAccess` :

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Farklı rolleri anlama](rbac-and-directory-admin-roles.md)
- [REST API kullanarak Azure rolleri atama](role-assignments-rest.md)
