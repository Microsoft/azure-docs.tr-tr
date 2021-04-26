---
title: 'Hızlı başlangıç: MariaDB için Azure DB-ARM şablonu oluşturma'
description: Bu hızlı başlangıç makalesinde Azure Resource Manager şablonu kullanarak MariaDB sunucusu için Azure veritabanı oluşturmayı öğrenin.
author: savjani
ms.author: pariks
ms.date: 05/14/2020
ms.topic: quickstart
ms.service: mariadb
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6bf7f4d30f2ad4f9e0181aed332e3f6cb9265ca0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531330"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mariadb-server"></a>Hızlı başlangıç: bir ARM şablonu kullanarak MariaDB sunucusu için Azure veritabanı oluşturma

MariaDB için Azure veritabanı, bulutta yüksek oranda kullanılabilir MariaDB veritabanlarını çalıştırmak, yönetmek ve ölçeklendirmek için kullandığınız bir yönetilen hizmettir. Bu hızlı başlangıçta, Azure portal, PowerShell veya Azure CLı 'da MariaDB sunucusu için Azure veritabanı oluşturmak üzere bir Azure Resource Manager şablonu (ARM şablonu) kullanırsınız.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

# <a name="portal"></a>[Portal](#tab/azure-portal)

Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/).
* Kodu yerel olarak çalıştırmak istiyorsanız, [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[CLI](#tab/CLI)

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/).
* Kodu yerel olarak çalıştırmak istiyorsanız, [Azure CLI](/cli/azure/).

---

## <a name="review-the-template"></a>Şablonu gözden geçirme

Tanımlı bir dizi işlem ve depolama kaynağı ile MariaDB için Azure Veritabanı sunucusu oluşturulur. Daha fazla bilgi edinmek için bkz. [MariaDB Için Azure veritabanı fiyatlandırma katmanları](concepts-pricing-tiers.md). Sunucu, [Azure kaynak grubu](../azure-resource-manager/management/overview.md) içinde oluşturulur.

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-managed-mariadb-with-vnet/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-managed-mariadb-with-vnet/azuredeploy.json":::

Şablon, beş Azure kaynağı tanımlar:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/virtualNetworks/alt ağları**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft. Dbformarıdb/sunucular**](/azure/templates/microsoft.dbformariadb/servers)
* [**Microsoft. Dbformarıdb/Servers/virtualNetworkRules**](/azure/templates/microsoft.dbformariadb/servers/virtualnetworkrules)
* [**Microsoft. Dbformarıdb/Servers/firewallRules**](/azure/templates/microsoft.dbformariadb/servers/firewallrules)

MariaDB şablon örnekleri için daha fazla Azure veritabanı, [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformariadb&pageNumber=1&sort=Popular)bulunabilir.

## <a name="deploy-the-template"></a>Şablonu dağıtma

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal için Azure veritabanı sunucu şablonunu dağıtmak için aşağıdaki bağlantıyı seçin:

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

**VNET Ile MariaDB Için Azure veritabanı 'Nı dağıtma** sayfasında:

1. **Kaynak grubu** Için **Yeni oluştur**' u seçin, yeni kaynak grubu için bir ad girin ve **Tamam**' ı seçin.

2. Yeni bir kaynak grubu oluşturduysanız, kaynak grubu ve yeni sunucu için bir **konum** seçin.

3. **Sunucu adı**, **yönetici oturumu açma** ve **yönetici oturum açma parolası** girin.

    ![VNet ile MariaDB için Azure veritabanı penceresi, Azure hızlı başlangıç şablonu, Azure portal](./media/quickstart-create-mariadb-server-database-arm-template/deploy-azure-database-mariadb-vnet.png)

4. İsterseniz diğer varsayılan ayarları değiştirin:

    * **Abonelik**: sunucu için kullanmak istediğiniz Azure aboneliği.
    * **SKU kapasitesi**: *2* (varsayılan), *4*, *8*, *16*, *32* veya *64* olabilen Vcore kapasitesi.
    * **SKU adı**: *B_Gen5_1*, *GP_Gen5_2* (varsayılan) veya *MO_Gen5_32* gıbı alt çizgilerden BIRLEŞTIRILMIŞ SKU katmanı öneki, SKU ailesi ve SKU kapasitesi.
    * **SKU boyutu MB**: MariaDB sunucusu Için Azure veritabanı 'nın megabayt cinsinden depolama boyutu (varsayılan *51200*).
    * **SKU katmanı**: *temel*, *generalamacını* (varsayılan) veya *memoryoptıılanmış* gibi dağıtım katmanı.
    * **SKU ailesi**: sunucu dağıtımı için donanım oluşturmayı gösteren *4. nesil* veya *5. nesil* (varsayılan).
    * **MariaDB sürümü**: dağıtılacak MariaDB sunucusunun sürümü (örneğin, *10,2* veya *10,3* ) (varsayılan).
    * **Yedekleme bekletme günleri**: coğrafi olarak yedekli yedekleme saklama için gün cinsinden istenen süre (varsayılan *7*).
    * **Coğrafi olarak yedekli yedekleme**: coğrafi olağanüstü durum kurtarma (COĞRAFI-Dr) gereksinimlerine bağlı olarak *etkin* veya *devre dışı* (varsayılan).
    * **Sanal ağ adı**: sanal ağın adı (varsayılan *azure_mariadb_vnet*).
    * **Alt ağ adı**: alt ağın adı (varsayılan *azure_mariadb_subnet*).
    * **Sanal ağ kuralı adı**: alt ağa izin veren sanal ağ kuralının adı (varsayılan *allowsubnet*).
    * **VNET adresi ön eki**: sanal ağın adres ön eki (varsayılan *10.0.0.0/16*).
    * **Alt ağ ön eki**: alt ağın adres ön eki (varsayılan *10.0.0.0/16*).

5. Hüküm ve koşulları okuyun ve ardından **yukarıda belirtilen hüküm ve koşulları kabul ediyorum**' u seçin.

6. **Satın al**'ı seçin.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Şablonu kullanarak MariaDB sunucusu için yeni bir Azure veritabanı oluşturmak üzere aşağıdaki etkileşimli kodu kullanın. Kod sizden yeni sunucu adı, yeni bir kaynak grubunun adını ve konumunu ve bir yönetici hesabı adı ile parolasını ister.

Kodu Azure Cloud Shell çalıştırmak için herhangi bir kod bloğunun üst köşesinde **deneyin** ' i seçin.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MariaDB server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MariaDB server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Şablonu kullanarak MariaDB sunucusu için yeni bir Azure veritabanı oluşturmak üzere aşağıdaki etkileşimli kodu kullanın. Kod sizden yeni sunucu adı, yeni bir kaynak grubunun adını ve konumunu ve bir yönetici hesabı adı ile parolasını ister.

Kodu Azure Cloud Shell çalıştırmak için herhangi bir kod bloğunun üst köşesinde **deneyin** ' i seçin.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for MariaDB server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for MariaDB server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

# <a name="portal"></a>[Portal](#tab/azure-portal)

MariaDB sunucusu için yeni Azure veritabanı 'na genel bir bakış görmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com), **MariaDB sunucuları için Azure veritabanı**' nı arayıp seçin.

2. Veritabanı listesinde yeni sunucunuzu seçin. MariaDB sunucusu için yeni Azure veritabanınızın **genel bakış** sayfası görüntülenir.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

MariaDB sunucusu için Azure veritabanı ile ilgili ayrıntıları görüntülemek için aşağıdaki etkileşimli kodu çalıştırın. Yeni sunucunun adını girmeniz gerekir.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MariaDB server"
Get-AzResource -ResourceType "Microsoft.DbForMariaDB/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

MariaDB sunucusu için Azure veritabanı ile ilgili ayrıntıları görüntülemek için aşağıdaki etkileşimli kodu çalıştırın. Yeni sunucunun adını ve kaynak grubunu girmeniz gerekir.

```azurecli-interactive
read -p "Enter your Azure Database for MariaDB server name: " serverName &&
read -p "Enter the resource group where the Azure Database for MariaDB server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMariaDB/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubundaki kaynakları silen kaynak grubunu silin.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com), **kaynak gruplarını** arayıp seçin.

2. Kaynak grubu listesinde, kaynak grubunuzun adını seçin.

3. Kaynak grubunuzun **genel bakış** sayfasında **kaynak grubunu sil**' i seçin.

4. Onay iletişim kutusunda, kaynak grubunuzun adını yazın ve ardından **Sil**' i seçin.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the Resource Group name: " resourceGroupName &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Sonraki adımlar

ARM şablonu oluşturma sürecinde size kılavuzluk eden adım adım bir öğretici için, bkz.:

> [!div class="nextstepaction"]
> [ Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
