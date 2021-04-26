---
title: 'Hızlı başlangıç: PostgresSQL için Azure DB esnek sunucu-ARM şablonu oluşturma'
description: Bu hızlı başlangıçta ARM şablonunu kullanarak PostgresSQL için Azure veritabanı esnek sunucu oluşturmayı öğrenin.
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 2/11/2021
ms.openlocfilehash: 1f6abb62086bf92be8ae2fe50abbfa5300185fd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384274"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---flexible-server"></a>Hızlı başlangıç: bir ARM şablonu kullanarak PostgreSQL için Azure veritabanı-esnek sunucu oluşturma

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

Esnek sunucu, bulutta yüksek oranda kullanılabilir PostgreSQL veritabanlarını çalıştırmak, yönetmek ve ölçeklendirmek için kullandığınız yönetilen bir hizmettir. Bir sunucuya birden çok sunucu veya birden çok veritabanı dağıtmak üzere bir PostgreSQL esnek sunucusu sağlamak için bir Azure Resource Manager şablonu (ARM şablonu) kullanabilirsiniz.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Azure Resource Manager, Azure için dağıtım ve yönetim hizmetidir. Azure hesabınızda kaynak oluşturma, güncelleştirme ve silme işlemlerini gerçekleştirmenizi sağlayan bir yönetim katmanı sunar. Dağıtım sonrasında kaynaklarınızın güvenliğini sağlamak ve onları düzenlemek için erişim denetimleri, kilitler ve etiketler gibi yönetim özelliklerini kullanabilirsiniz. Azure Resource Manager şablonları hakkında bilgi edinmek için bkz. [şablon dağıtımı genel bakış](../../azure-resource-manager/templates/overview.md).

## <a name="prerequisites"></a>Önkoşullar

Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Şablonu gözden geçirme

PostgresSQL Server için Azure veritabanı, bir bölgedeki bir veya daha fazla veritabanı için üst kaynaktır. Bu, veritabanlarına uygulanan yönetim ilkeleri için kapsam sağlar: oturum açma, güvenlik duvarı, kullanıcılar, roller ve Konfigürasyonlar.

Dosyasında bir _postgres-flexible-server-template.js_ oluşturun ve aşağıdaki JSON betiğini buna kopyalayın.

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
      "type": "string"
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
    "api": "2020-02-14-privatepreview",
    "firewallRules": "[parameters('firewallRules').rules]",
    "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"subnetArmResourceId\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"subnetArmResourceId\": \"', variables('vnetDataSet').subnetArmResourceId, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/flexibleServers",
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
        "[concat('Microsoft.DBforPostgreSQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforPostgreSQL/flexibleServers/firewallRules",
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

- Microsoft. DBforPostgreSQL/Flexibtaservers

## <a name="deploy-the-template"></a>Şablonu dağıtma

Azure Cloud Shell açmak için aşağıdaki PowerShell kod bloğundan **deneyin** öğesini seçin.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Sunucunuzun Azure 'da oluşturulup oluşturuçalışmadığını doğrulamak için aşağıdaki adımları izleyin.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

1. [Azure Portal](https://portal.azure.com), **PostgreSQL Için Azure veritabanı esnek sunucular (Önizleme)** için arama yapın ve seçin.
1. Veritabanı listesinde, sunucuyu yönetmek üzere **genel bakış** sayfasını görüntülemek için yeni sunucunuzu seçin.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

PostgreSQL için Azure veritabanı esnek sunucusu ayrıntılarını görüntülemek için yeni sunucunun adını girmeniz gerekir.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DBforPostgreSQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

PostgreSQL için Azure veritabanı esnek sunucusu hakkındaki ayrıntıları görüntülemek için yeni sunucunun adını ve kaynak grubunu girmeniz gerekir.

```azurecli-interactive
echo "Enter your Azure Database for PostgreSQL Flexible Server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for PostgreSQL Flexible Server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DBforPostgreSQL/flexibleServers"
```

---


## <a name="clean-up-resources"></a>Kaynakları temizleme

[Sonraki adımlara](#next-steps)gitmek istiyorsanız bu kaynak grubunu, sunucuyu ve tek veritabanını saklayın. Sonraki adımlarda, farklı yöntemler kullanarak veritabanınızı nasıl bağlayabilmeniz ve Sorgulayabileceğiniz gösterilmektedir.

Kaynak grubunu silmek için:

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Portalda](https://portal.azure.com), silmek istediğiniz kaynak grubunu seçin.

1. **Kaynak grubunu sil**'i seçin.
1. Silmeyi onaylamak için kaynak grubunun adını yazın

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```
----

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Döküm ve geri yükleme kullanarak veritabanınızı geçirme](../howto-migrate-using-dump-and-restore.md)
