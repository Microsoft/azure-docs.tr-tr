---
title: Azure Resource Manager şablonu kullanarak Azure özel rolleri oluşturma veya güncelleştirme-Azure RBAC
description: Azure Resource Manager şablonu (ARM şablonu) ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak Azure özel rolleri oluşturma veya güncelleştirme hakkında bilgi edinin.
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: 0626a9e36d05ac9cb51f62652dbe6f3133bbc6d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095904"
---
# <a name="create-or-update-azure-custom-roles-using-an-arm-template"></a>ARM şablonu kullanarak Azure özel rolleri oluşturma veya güncelleştirme

[Azure yerleşik rolleri](built-in-roles.md) , kuruluşunuzun belirli ihtiyaçlarını karşılamıyorsa, kendi [özel rollerinizi](custom-roles.md)de oluşturabilirsiniz. Bu makalede, bir Azure Resource Manager şablonu (ARM şablonu) kullanarak özel bir rol oluşturma veya güncelleştirme açıklanmaktadır.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Özel bir rol oluşturmak için, rol adı, izinler ve rolün kullanılabileceği yerleri belirtirsiniz. Bu makalede, bir abonelik kapsamında veya daha düşük bir abonelik kapsamında atanabilen, kaynak izinleri olan _özel rol-RG Reader_ adlı bir rol oluşturacaksınız.

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsubscription-deployments%2Fcreate-role-def%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Özel bir rol oluşturmak için şunları yapmanız gerekir:

- [Sahip](built-in-roles.md#owner) veya [Kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator)gibi özel roller oluşturma izinleri.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu makalede kullanılan şablon, [Azure hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/create-role-def). Şablonda dört parametre ve bir kaynak bölümü vardır. Dört parametre şunlardır:

- Varsayılan değeri olan eylem dizisi `["Microsoft.Resources/subscriptions/resourceGroups/read"]` .
- `notActions`Boş bir varsayılan değere sahip dizi.
- Varsayılan değeri olan rol adı `Custom Role - RG Reader` .
- Varsayılan değeri olan rol açıklaması `Subscription Level Deployment of a Role Definition` .

Bu özel rolün atanabileceği kapsam geçerli aboneliğe ayarlanır.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

Şablonda tanımlanan kaynak:

- [Microsoft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

## <a name="deploy-the-template"></a>Şablonu dağıtma

Önceki şablonu dağıtmak için aşağıdaki adımları izleyin.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. PowerShell için Azure Cloud Shell açın.

1. Aşağıdaki betiği kopyalayıp Cloud Shell yapıştırın.

    ```azurepowershell-interactive
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"
    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Dağıtım için gibi bir konum girin `centralus` .

1. Özel rol için, gibi bir virgülle ayrılmış liste olarak eylemlerin bir listesini girin `Microsoft.Resources/resources/read,Microsoft.Resources/subscriptions/resourceGroups/read` .

1. Gerekirse, komutu çalıştırmak için ENTER tuşuna basın `New-AzDeployment` .

    [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) komutu şablonu özel rol oluşturmak için dağıtır.

    Aşağıdakine benzer bir çıktı görmeniz gerekir:

    ```azurepowershell-interactive
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions

    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0

    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition

    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Özel rolün oluşturulduğunu doğrulamak için aşağıdaki adımları izleyin.

1. Özel rolü listelemek için [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) komutunu çalıştırın.

    ```azurepowershell-interactive
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    Aşağıdakine benzer bir çıktı görmeniz gerekir:

    ```azurepowershell-interactive
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. Azure portal aboneliğinizi açın.

1. Sol menüde **erişim denetimi (IAM)** seçeneğini belirleyin.

1. **Roller** sekmesini seçin.

1. **Tür** listesini **customrole** olarak ayarlayın.

1. **Özel rol-RG okuyucu** rolünün listelendiğini doğrulayın.

   ![Azure portal yeni özel rol](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="update-a-custom-role"></a>Özel rolü güncelleştirme

Özel bir rol oluşturmaya benzer şekilde, bir şablon kullanarak var olan bir özel rolü güncelleştirebilirsiniz. Özel bir rolü güncelleştirmek için, güncelleştirmek istediğiniz rolü belirtmeniz gerekir.

Özel rolü güncelleştirmek için, önceki hızlı başlangıç şablonunda yapmanız gereken değişiklikler aşağıda verilmiştir.

- Rol KIMLIĞINI parametre olarak ekleyin.
    ```json
        ...
        "roleDefName": {
          "type": "string",
          "metadata": {
            "description": "ID of the role definition"
          }
        ...
    ```

- Rol tanımına rol KIMLIĞI parametresini ekleyin.

    ```json
      ...
      "resources": [
        {
          "type": "Microsoft.Authorization/roleDefinitions",
          "apiVersion": "2018-07-01",
          "name": "[parameters('roleDefName')]",
          "properties": {
            ...
    ```

Şablonun nasıl dağıtılacağı hakkında bir örnek aşağıda verilmiştir.

```azurepowershell
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
[string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
$actions = $actions.Split(',')
$roleDefName = Read-Host -Prompt "Enter the role ID to update"
$templateFile = "rg-reader-update.json"
New-AzDeployment -Location $location -TemplateFile $templateFile -actions $actions -roleDefName $roleDefName
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Özel rolü kaldırmak için aşağıdaki adımları izleyin.

1. Özel rolü kaldırmak için aşağıdaki komutu çalıştırın.

    ```azurepowershell-interactive
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Özel rolü kaldırmak istediğinizi onaylamak için **Y** yazın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure rol tanımlarını anlama](role-definitions.md)
- [Hızlı başlangıç: Azure Resource Manager şablonu kullanarak Azure rolü atama](quickstart-role-assignments-template.md)
- [ARM şablonu belgeleri](../azure-resource-manager/templates/index.yml)
