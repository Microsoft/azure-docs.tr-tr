---
title: 'Azure Resource Manager: tek bir veritabanı oluşturma'
description: Azure Resource Manager şablonunu kullanarak Azure SQL veritabanı 'nda tek bir veritabanı oluşturun.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: b7888215a2d463c8007168e215179ace898ca1cc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821020"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Hızlı başlangıç: Azure Resource Manager şablonunu kullanarak Azure SQL veritabanı 'nda tek bir veritabanı oluşturma

Tek bir [veritabanı](sql-database-single-database.md) oluşturmak, Azure SQL veritabanı 'nda veritabanı oluşturmak için en hızlı ve en basit dağıtım seçeneğidir. Bu hızlı başlangıçta, Azure Resource Manager şablonunu kullanarak tek bir veritabanının nasıl oluşturulacağı gösterilmektedir. Daha fazla bilgi için bkz. [Azure Resource Manager belgeleri](/azure/azure-resource-manager/).

Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="create-a-single-database"></a>Tek bir veritabanı oluşturma

Tek bir veritabanı, iki [satın alma modelinden](sql-database-purchase-models.md)birini kullanarak tanımlı bir işlem, bellek, GÇ ve depolama kaynakları kümesine sahiptir. Tek bir veritabanı oluşturduğunuzda, bunu yönetmek için bir [SQL veritabanı sunucusu](sql-database-servers.md) tanımlar ve belirli bir bölgedeki [Azure Kaynak grubuna](../azure-resource-manager/resource-group-overview.md) yerleştirebilirsiniz.

Aşağıdaki JSON dosyası, bu makalede kullanılan şablondur. Şablon [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/SQLServerAndDatabase/azuredeploy.json)'da depolanır. [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular), daha fazla Azure SQL veritabanı şablonu örneği bulunabilir.

[!code-json[create-azure-sql-database-server-and-database](~/resourcemanager-templates/SQLServerAndDatabase/azuredeploy.json)]

1. Azure Cloud Shell açmak için aşağıdaki PowerShell kod bloğundan **deneyin** öğesini seçin.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
    $adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
    $adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

    $resourceGroupName = "${projectName}rg"


    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" -projectName $projectName -adminUser $adminUser -adminPassword $adminPassword

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

1. PowerShell betiğini Pano 'ya kopyalamak için **Kopyala** ' yı seçin.
1. Kabuk bölmesine sağ tıklayın ve ardından **Yapıştır**' ı seçin.

    Veritabanı sunucusunun ve veritabanının oluşturulması birkaç dakika sürer.

## <a name="query-the-database"></a>Veritabanını sorgulama

Veritabanını sorgulamak için bkz. [Veritabanını sorgulama](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Kaynakları temizleme

[Sonraki adımlara](#next-steps)gitmek istiyorsanız bu kaynak grubunu, veritabanı sunucusunu ve tek veritabanını saklayın. Sonraki adımlarda, farklı yöntemler kullanarak veritabanınızı nasıl bağlayabilmeniz ve Sorgulayabileceğiniz gösterilmektedir.

Kaynak grubunu Azure PowerShell 'e göre silmek için:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Sonraki adımlar

- Şirket içi veya uzak araçlardan tek veritabanına bağlanmak için sunucu düzeyinde bir güvenlik duvarı kuralı oluşturun. Daha fazla bilgi için bkz. [sunucu düzeyinde güvenlik duvarı kuralı oluşturma](sql-database-server-level-firewall-rule.md).
- Sunucu düzeyinde bir güvenlik duvarı kuralı oluşturduktan sonra, birkaç farklı araç ve dil kullanarak veritabanınızı [bağlayın ve sorgulayın](sql-database-connect-query.md) .
  - [SQL Server Management Studio kullanarak bağlanma ve sorgulama](sql-database-connect-query-ssms.md)
  - [Azure Data Studio kullanarak bağlanma ve sorgulama](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Azure CLı kullanarak tek bir veritabanı oluşturmak için bkz. [Azure CLI örnekleri](sql-database-cli-samples.md).
- Azure PowerShell kullanarak tek bir veritabanı oluşturmak için bkz. [Azure PowerShell örnekleri](sql-database-powershell-samples.md).
