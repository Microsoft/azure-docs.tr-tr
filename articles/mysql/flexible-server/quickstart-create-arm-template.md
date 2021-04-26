---
title: 'Hızlı başlangıç: MySQL için Azure DB oluşturma-esnek sunucu-ARM şablonu'
description: Bu hızlı başlangıçta, ARM şablonunu kullanarak MySQL için Azure veritabanı-esnek sunucu oluşturmayı öğrenin.
author: mksuni
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 10/23/2020
ms.openlocfilehash: def9e4f1b3f1c4e8f88f77dfe6906a8c96a94744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389476"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql---flexible-server-preview"></a>Hızlı başlangıç: MySQL için Azure veritabanı oluşturmak için bir ARM şablonu kullanma-esnek sunucu (Önizleme)

> [!IMPORTANT]
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

MySQL için Azure veritabanı-esnek sunucu (Önizleme), bulutta yüksek oranda kullanılabilir MySQL veritabanlarını çalıştırmak, yönetmek ve ölçeklendirmek için kullandığınız yönetilen bir hizmettir. Bir sunucuda birden çok sunucuyu veya birden çok veritabanını dağıtmak üzere esnek bir sunucu sağlamak için bir Azure Resource Manager şablonu (ARM şablonu) kullanabilirsiniz.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Önkoşullar

Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Şablonu gözden geçirme

MySQL için Azure veritabanı esnek sunucu, bir bölgedeki bir veya daha fazla veritabanı için üst kaynaktır. Veritabanlarına uygulanan yönetim ilkeleri için kapsam sağlar: oturum açma, güvenlik duvarı, kullanıcılar, roller, konfigürasyonlar.

Dosyasında bir _mysql-flexible-server-template.js_ oluşturun ve bu JSON betiğini buna kopyalayın.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "administratorLogin": {
      "type": "String"
    },
    "administratorLoginPassword": {
      "type": "SecureString"
    },
    "location": {
      "type": "String"
    },
    "serverName": {
      "type": "String"
    },
    "serverEdition": {
      "type": "String"
    },
    "storageSizeMB": {
      "type": "Int"
    },
    "haEnabled": {
      "type": "string",
      "defaultValue": "Disabled"
    },
    "availabilityZone": {
      "type": "String"
    },
    "version": {
      "type": "String"
    },
    "tags": {
      "defaultValue": {},
      "type": "Object"
    },
    "firewallRules": {
      "defaultValue": {},
      "type": "Object"
    },
    "vnetData": {
      "defaultValue": {},
      "type": "Object"
    },
    "backupRetentionDays": {
      "type": "Int"
    }
  },
  "variables": {
    "api": "2020-07-01-preview",
    "firewallRules": "[parameters('firewallRules').rules]",
    "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"subnetArmResourceId\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"subnetArmResourceId\": \"', variables('vnetDataSet').subnetArmResourceId, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/flexibleServers",
      "apiVersion": "[variables('api')]",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_D4ds_v4",
        "tier": "[parameters('serverEdition')]"
      },
      "tags": "[parameters('tags')]",
      "properties": {
        "version": "[parameters('version')]",
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "publicNetworkAccess": "[variables('publicNetworkAccess')]",
        "DelegatedSubnetArguments": "[if(empty(parameters('vnetData')), json('null'), variables('finalVnetData'))]",
        "haEnabled": "[parameters('haEnabled')]",
        "storageProfile": {
          "storageMB": "[parameters('storageSizeMB')]",
          "backupRetentionDays": "[parameters('backupRetentionDays')]"
        },
        "availabilityZone": "[parameters('availabilityZone')]"
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-08-01",
      "name": "[concat('firewallRules-', copyIndex())]",
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforMySQL/flexibleServers/firewallRules",
              "name": "[concat(parameters('serverName'),'/',variables('firewallRules')[copyIndex()].name)]",
              "apiVersion": "[variables('api')]",
              "properties": {
                "StartIpAddress": "[variables('firewallRules')[copyIndex()].startIPAddress]",
                "EndIpAddress": "[variables('firewallRules')[copyIndex()].endIPAddress]"
              }
            }
          ]
        }
      },
      "copy": {
        "name": "firewallRulesIterator",
        "count": "[if(greater(length(variables('firewallRules')), 0), length(variables('firewallRules')), 1)]",
        "mode": "Serial"
      },
      "condition": "[greater(length(variables('firewallRules')), 0)]"
    }
  ]
}
```

Bu kaynaklar şablonda tanımlanmıştır:

- Microsoft. Dbformyısql/Flexibelservers

## <a name="deploy-the-template"></a>Şablonu dağıtma

[Azure Cloud Shell](../../cloud-shell/overview.md)açmak Için aşağıdaki PowerShell kod bloğundan **deneyin** öğesini seçin.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Sunucunuzun Azure 'da oluşturulup oluşturuçalışmadığını doğrulamak için aşağıdaki adımları izleyin.

### <a name="azure-portal"></a>Azure portalı

1. [Azure Portal](https://portal.azure.com), **MySQL için Azure veritabanı sunucuları**' nı arayıp seçin.
1. Veritabanı listesinde yeni sunucunuzu seçin. Yeni MySQL için Azure veritabanı sunucunuzun **genel bakış** sayfası görüntülenir.

### <a name="powershell"></a>PowerShell

MySQL için Azure veritabanı esnek sunucusu ayrıntılarını görüntülemek için yeni sunucunun adını girmeniz gerekir.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>CLI

MySQL için Azure veritabanı esnek sunucusu hakkındaki ayrıntıları görüntülemek için yeni sunucunun adını ve kaynak grubunu girmeniz gerekir.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/flexibleServers"
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

[Sonraki adımlara](#next-steps)gitmek istiyorsanız bu kaynak grubunu, sunucuyu ve tek veritabanını saklayın. Sonraki adımlarda, farklı yöntemler kullanarak veritabanınızı nasıl bağlayabilmeniz ve Sorgulayabileceğiniz gösterilmektedir.

Kaynak grubunu silmek için:

### <a name="azure-portal"></a>Azure portalı

1. [Azure Portal](https://portal.azure.com), **kaynak gruplarını** arayıp seçin.
1. Kaynak grubu listesinde, kaynak grubunuzun adını seçin.
1. Kaynak grubunuzun **genel bakış** sayfasında **kaynak grubunu sil**' i seçin.
1. Onay iletişim kutusunda, kaynak grubunuzun adını yazın ve ardından **Sil**' i seçin.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>CLI

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```
---

## <a name="next-steps"></a>Sonraki adımlar

ARM şablonu oluşturma sürecinde size kılavuzluk eden adım adım bir öğretici için, bkz.:

> [!div class="nextstepaction"]
> [Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

MySQL kullanarak App Service bir uygulama oluşturmaya yönelik adım adım bir öğretici için, bkz.:

> [!div class="nextstepaction"]
>[MySQL ile PHP (Laralevel) Web uygulaması oluşturma](tutorial-php-database-app.md)
