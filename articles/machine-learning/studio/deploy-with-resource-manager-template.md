---
title: Azure Resource Manager ile Studio (klasik) çalışma alanını dağıtma
titleSuffix: ML Studio (classic) - Azure
description: Azure Resource Manager şablonu kullanarak Azure Machine Learning Studio (klasik) için çalışma alanı dağıtma
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: 5ca45d772e3804dce4e27e5a6d4388acca4d1486
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837686"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Azure Resource Manager kullanarak Azure Machine Learning Studio (klasik) çalışma alanını dağıtma

Bir Azure Resource Manager dağıtım şablonu kullanmak, bir doğrulama ve yeniden deneme mekanizmasıyla bağlantılı bileşenleri dağıtmak için ölçeklenebilir bir yol sunarak size zaman kazandırır. Örneğin Azure Machine Learning Studio (klasik) çalışma alanlarını ayarlamak için önce bir Azure depolama hesabı yapılandırmanız ve ardından çalışma alanınızı dağıtmanız gerekir. Yüzlerce çalışma alanı için el ile bunu düşünün. Daha kolay bir alternatif, bir Studio (klasik) çalışma alanını ve tüm bağımlılıklarını dağıtmak için Azure Resource Manager şablonu kullanmaktır. Bu makale, bu işlem adım adım boyunca size kılavuzluk eden bir işlemdir. Azure Resource Manager harika bir genel bakış için bkz. [Azure Resource Manager genel bakış](../../azure-resource-manager/resource-group-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Adım adım: Machine Learning Çalışma Alanı oluşturma
Bir Azure Kaynak grubu oluşturacak, sonra yeni bir Azure depolama hesabı ve yeni bir Azure Machine Learning Studio (klasik) çalışma alanını Kaynak Yöneticisi şablonu kullanarak dağıtacağız. Dağıtım tamamlandıktan sonra, oluşturulan çalışma alanları (birincil anahtar, çalışma alanı kimliği ve çalışma alanının URL 'SI) hakkındaki önemli bilgileri yazdıracağız.

### <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu oluşturma

Machine Learning Çalışma Alanı, kendisiyle bağlantılı veri kümesini depolamak için bir Azure depolama hesabı gerektirir.
Aşağıdaki şablon, depolama hesabı adını ve çalışma alanı adını oluşturmak için kaynak grubunun adını kullanır.  Ayrıca, çalışma alanını oluştururken bir özellik olarak depolama hesabı adını kullanır.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Bu şablonu c:\temp\ın altında mlworkspace. JSON dosyası olarak kaydedin.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Şablona göre kaynak grubunu dağıtma

* PowerShell’i açın
* Azure Resource Manager ve Azure hizmet yönetimi için modülleri yükler

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Bu adımlar, kalan adımları tamamlayabilmeniz için gereken modülleri indirir ve yükler. Bu, PowerShell komutlarını yürüttüğünüz ortamda yalnızca bir kez yapılmalıdır.

* Azure 'da kimlik doğrulama

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Bu adımın her oturum için yinelenmesi gerekir. Kimliği doğrulandıktan sonra, abonelik bilgileriniz görüntülenmelidir.

![Azure hesabı](./media/deploy-with-resource-manager-template/azuresubscription.png)

Artık Azure 'a erişimimiz olduğuna göre, kaynak grubunu oluşturuyoruz.

* Kaynak grubu oluşturma

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Kaynak grubunun doğru şekilde sağlandığını doğrulayın. **Provisioningstate** "başarılı" olmalıdır.
Kaynak grubu adı, şablon tarafından depolama hesabı adını oluşturmak için kullanılır. Depolama hesabı adı 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakam ve küçük harf kullanılmalıdır.

![Kaynak Grubu](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Kaynak grubu dağıtımını kullanarak yeni bir Machine Learning Çalışma Alanı dağıtın.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Dağıtım tamamlandıktan sonra, dağıttığınız çalışma alanının özelliklerine erişim basittir. Örneğin, birincil anahtar belirtecine erişebilirsiniz.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Mevcut çalışma alanının belirteçlerini almanın başka bir yolu da Invoke-AzResourceAction komutunu kullanmaktır. Örneğin, tüm çalışma alanlarının birincil ve ikincil belirteçlerini listeleyebilirsiniz.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Çalışma alanı sağlandıktan sonra, [Azure Machine Learning Studio (klasik) Için PowerShell modülünü](https://aka.ms/amlps)kullanarak pek çok Azure Machine Learning Studio (klasik) görevi otomatikleştirebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Resource Manager şablonları yazma](../../azure-resource-manager/resource-group-authoring-templates.md)hakkında daha fazla bilgi edinin.
* [Azure hızlı başlangıç şablonları deposuna](https://github.com/Azure/azure-quickstart-templates)göz atın.
* [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39)hakkındaki bu videoyu izleyin.
* [Kaynak Yöneticisi şablonu başvurusu yardımına](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions) bakın

<!--Link references-->
