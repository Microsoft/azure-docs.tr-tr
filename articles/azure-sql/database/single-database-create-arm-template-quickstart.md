---
title: 'Azure Resource Manager: tek bir veritabanı oluşturma'
description: Azure Resource Manager şablonu kullanarak Azure SQL veritabanı 'nda tek bir veritabanı oluşturun.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: sstein
ms.date: 06/24/2020
ms.openlocfilehash: b7883144509760c9670decd50062d2595b8dc495
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96187094"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak Azure SQL veritabanı 'nda tek bir veritabanı oluşturma

Tek bir [veritabanı](single-database-overview.md) oluşturmak, Azure SQL veritabanı 'nda veritabanı oluşturmak için en hızlı ve en basit seçenektir. Bu hızlı başlangıçta, Azure Resource Manager şablonu kullanarak tek bir veritabanının nasıl oluşturulacağı gösterilmektedir (ARM şablonu).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-database%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Şablonu gözden geçirme

Tek bir veritabanı, iki [satın alma modelinden](purchasing-models.md)birini kullanarak tanımlı bir işlem, bellek, GÇ ve depolama kaynakları kümesine sahiptir. Tek bir veritabanı oluşturduğunuzda, bunu yönetmek için bir [sunucu](logical-servers.md) tanımlar ve belirli bir bölgedeki [Azure Kaynak grubu](../../active-directory-b2c/overview.md) içine yerleştirebilirsiniz.

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-sql-database/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-sql-database/azuredeploy.json":::

Bu kaynaklar şablonda tanımlanmıştır:

- [**Microsoft. SQL/sunucuları**](/azure/templates/microsoft.sql/servers)
- [**Microsoft. SQL/Servers/veritabanları**](/azure/templates/microsoft.sql/servers/databases)

[Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular), daha fazla Azure SQL veritabanı şablonu örneği bulunabilir.

## <a name="deploy-the-template"></a>Şablonu dağıtma

Azure Cloud Shell açmak için aşağıdaki PowerShell kod bloğundan **deneyin** öğesini seçin.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-database/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Veritabanını sorgulamak için bkz. [Veritabanını sorgulama](single-database-create-quickstart.md#query-the-database).

## <a name="clean-up-resources"></a>Kaynakları temizleme

[Sonraki adımlara](#next-steps)gitmek istiyorsanız bu kaynak grubunu, sunucuyu ve tek veritabanını saklayın. Sonraki adımlarda, farklı yöntemler kullanarak veritabanınızı nasıl bağlayabilmeniz ve Sorgulayabileceğiniz gösterilmektedir.

Kaynak grubunu silmek için:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

- Şirket içi veya uzak araçlardan tek veritabanına bağlanmak için sunucu düzeyinde bir güvenlik duvarı kuralı oluşturun. Daha fazla bilgi için bkz. [sunucu düzeyinde güvenlik duvarı kuralı oluşturma](firewall-create-server-level-portal-quickstart.md).
- Sunucu düzeyinde bir güvenlik duvarı kuralı oluşturduktan sonra, birkaç farklı araç ve dil kullanarak veritabanınızı [bağlayın ve sorgulayın](connect-query-content-reference-guide.md) .
  - [SQL Server Management Studio kullanarak bağlanma ve sorgulama](connect-query-ssms.md)
  - [Azure Data Studio kullanarak bağlanma ve sorgulama](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)
- Azure CLı kullanarak tek bir veritabanı oluşturmak için bkz. [Azure CLI örnekleri](az-cli-script-samples-content-guide.md).
- Azure PowerShell kullanarak tek bir veritabanı oluşturmak için bkz. [Azure PowerShell örnekleri](powershell-script-content-guide.md).
- ARM şablonları oluşturmayı öğrenmek için bkz. [ilk şablonunuzu oluşturma](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).