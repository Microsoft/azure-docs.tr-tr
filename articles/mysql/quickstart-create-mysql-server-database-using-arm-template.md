---
title: 'Hızlı başlangıç: MySQL için Azure DB-ARM şablonu oluşturma'
description: Bu hızlı başlangıçta, bir Azure Resource Manager şablonu kullanarak, sanal ağ tümleştirmesiyle MySQL için Azure veritabanı sunucusu oluşturmayı öğrenin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/19/2020
ms.openlocfilehash: 3da3b1694a16507203d7f1f1f6cb5df58dd54423
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366186"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql-server"></a>Hızlı başlangıç: MySQL için Azure veritabanı sunucusu oluşturmak için ARM şablonu kullanma

MySQL için Azure veritabanı, bulutta yüksek oranda kullanılabilir MySQL veritabanlarını çalıştırmak, yönetmek ve ölçeklendirmek için kullandığınız yönetilen bir hizmettir. Bu hızlı başlangıçta, sanal ağ tümleştirmesiyle MySQL için Azure veritabanı sunucusu oluşturmak üzere bir Azure Resource Manager şablonu (ARM şablonu) kullanırsınız. Sunucuyu Azure portal, Azure CLı veya Azure PowerShell oluşturabilirsiniz.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure 'a dağıtma":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Önkoşullar

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

MySQL için Azure Veritabanı sunucusu, tanımlı bir dizi işlem ve depolama kaynağı ile oluşturulur. Daha fazla bilgi için bkz. [MySQL Için Azure veritabanı fiyatlandırma katmanları](concepts-pricing-tiers.md). Sunucu, [Azure kaynak grubu](../azure-resource-manager/management/overview.md) içinde oluşturulur.

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-managed-mysql-with-vnet/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-managed-mysql-with-vnet/azuredeploy.json":::

Şablon, beş Azure kaynağı tanımlar:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/virtualNetworks/alt ağları**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft. Dbformyısql/sunucuları**](/azure/templates/microsoft.dbformysql/servers)
* [**Microsoft. Dbformyısql/Servers/virtualNetworkRules**](/azure/templates/microsoft.dbformysql/servers/virtualnetworkrules)
* [**Microsoft. Dbformyısql/Servers/firewallRules**](/azure/templates/microsoft.dbformysql/servers/firewallrules)

MySQL için Azure veritabanı şablon örnekleri, [hızlı başlangıç şablonu galerisinde](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformysql&pageNumber=1&sort=Popular)bulunabilir.

## <a name="deploy-the-template"></a>Şablonu dağıtma

# <a name="portal"></a>[Portal](#tab/azure-portal)

MySQL için Azure veritabanı sunucu şablonunu Azure portal dağıtmak için aşağıdaki bağlantıyı seçin:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure’a dağıtın":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

**VNET Ile MySQL Için Azure veritabanı 'Nı dağıtma** sayfasında:

1. **Kaynak grubu** Için **Yeni oluştur**' u seçin, yeni kaynak grubu için bir ad girin ve **Tamam**' ı seçin.

2. Yeni bir kaynak grubu oluşturduysanız, kaynak grubu ve yeni sunucu için bir **konum** seçin.

3. **Sunucu adı**, **yönetici oturumu açma** ve **yönetici oturum açma parolası** girin.

    :::image type="content" source="./media/quickstart-create-mysql-server-database-using-arm-template/deploy-azure-database-for-mysql-with-vnet.png" alt-text="Sanal ağ ile MySQL için Azure veritabanı 'nı dağıtma, Azure hızlı başlangıç şablonu, Azure portal":::

4. İsterseniz diğer varsayılan ayarları değiştirin:

    * **Abonelik**: sunucu için kullanmak istediğiniz Azure aboneliği.
    * **SKU kapasitesi**: *2* (varsayılan), *4*, *8*, *16*, *32* veya *64* olabilen Vcore kapasitesi.
    * **SKU adı**: *B_Gen5_1*, *GP_Gen5_2* (varsayılan) veya *MO_Gen5_32* gıbı alt çizgilerden BIRLEŞTIRILMIŞ SKU katmanı öneki, SKU ailesi ve SKU kapasitesi.
    * **SKU boyutu MB**: MySQL Için Azure veritabanı sunucusu 'nun megabayt cinsinden depolama boyutu (varsayılan *5120*).
    * **SKU katmanı**: *temel*, *generalamacını* (varsayılan) veya *memoryoptıılanmış* gibi dağıtım katmanı.
    * **SKU ailesi**: sunucu dağıtımı için donanım oluşturmayı gösteren *4. nesil* veya *5. nesil* (varsayılan).
    * **MySQL sürümü**: dağıtılacak MySQL sunucusunun sürümü (örneğin, *5,6* veya *5,7* ) (varsayılan).
    * **Yedekleme bekletme günleri**: coğrafi olarak yedekli yedekleme saklama için gün cinsinden istenen süre (varsayılan *7*).
    * **Coğrafi olarak yedekli yedekleme**: coğrafi olağanüstü durum kurtarma (COĞRAFI-Dr) gereksinimlerine bağlı olarak *etkin* veya *devre dışı* (varsayılan).
    * **Sanal ağ adı**: sanal ağın adı (varsayılan *azure_mysql_vnet*).
    * **Alt ağ adı**: alt ağın adı (varsayılan *azure_mysql_subnet*).
    * **Sanal ağ kuralı adı**: alt ağa izin veren sanal ağ kuralının adı (varsayılan *allowsubnet*).
    * **VNET adresi ön eki**: sanal ağın adres ön eki (varsayılan *10.0.0.0/16*).
    * **Alt ağ ön eki**: alt ağın adres ön eki (varsayılan *10.0.0.0/16*).

5. Hüküm ve koşulları okuyun ve ardından **yukarıda belirtilen hüküm ve koşulları kabul ediyorum**' u seçin.

6. **Satın al**'ı seçin.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Şablonu kullanarak MySQL için yeni bir Azure veritabanı sunucusu oluşturmak üzere aşağıdaki etkileşimli kodu kullanın. Kod sizden yeni sunucu adı, yeni bir kaynak grubunun adını ve konumunu ve bir yönetici hesabı adı ile parolasını ister.

Kodu Azure Cloud Shell çalıştırmak için herhangi bir kod bloğunun üst köşesinde **deneyin** ' i seçin.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Şablonu kullanarak MySQL için yeni bir Azure veritabanı sunucusu oluşturmak üzere aşağıdaki etkileşimli kodu kullanın. Kod sizden yeni sunucu adı, yeni bir kaynak grubunun adını ve konumunu ve bir yönetici hesabı adı ile parolasını ister.

Kodu Azure Cloud Shell çalıştırmak için herhangi bir kod bloğunun üst köşesinde **deneyin** ' i seçin.

```azurecli-interactive
echo "Enter a name for the new Azure Database for MySQL server:" &&
read serverName &&
echo "Enter a name for the new resource group where the server will exist:" &&
read resourceGroupName &&
echo "Enter an Azure region (for example, centralus) for the resource group:" &&
read location &&
echo "Enter the Azure Database for MySQL server's administrator account name:" &&
read adminUser &&
echo "Enter the administrator password:" &&
read adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

# <a name="portal"></a>[Portal](#tab/azure-portal)

Yeni MySQL sunucusu için Azure veritabanı 'na genel bir bakış görmek için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com), **MySQL için Azure veritabanı sunucuları**' nı arayıp seçin.

2. Veritabanı listesinde yeni sunucunuzu seçin. Yeni MySQL için Azure veritabanı sunucunuzun **genel bakış** sayfası görüntülenir.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

MySQL sunucusu için Azure veritabanı ile ilgili ayrıntıları görüntülemek için aşağıdaki etkileşimli kodu çalıştırın. Yeni sunucunun adını girmeniz gerekir.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/servers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

MySQL sunucusu için Azure veritabanı ile ilgili ayrıntıları görüntülemek için aşağıdaki etkileşimli kodu çalıştırın. Yeni sunucunun adını ve kaynak grubunu girmeniz gerekir.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/servers"
```

---

## <a name="exporting-arm-template-from-the-portal"></a>, Portaldan ARM şablonu dışarı aktarılıyor
[BIR ARM şablonunu Azure Portal dışa aktarabilirsiniz](../azure-resource-manager/templates/export-template-portal.md) . Şablonu dışarı aktarmanın iki yolu vardır:

- [Kaynak grubundan veya kaynaktan dışarı aktarın](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource). Bu seçenek, mevcut kaynaklardan yeni bir şablon oluşturur. Bu şablon, kaynak grubunun geçerli durumunun "Snapshot" ' dır. Kaynak grubunun tamamını veya kaynak grubu içindeki belirli kaynakları dışarı aktarabilirsiniz.
- [Dağıtımdan veya geçmişten önce dışarı aktarın](../azure-resource-manager/templates/export-template-portal.md#export-template-before-deployment). Bu seçenek, dağıtım için kullanılan bir şablonun tam bir kopyasını alır.

Şablonu dışarı aktarırken, ```"properties":{ }``` MySQL Server kaynağının bölümünde, güvenlik nedenleriyle bu olduğunu fark edersiniz ```administratorLogin``` ```administratorLoginPassword``` . Şablonu dağıtılmadan önce bu parametreleri şablonunuza eklemeniz **gerekir** , aksi olarak şablon başarısız olur.

```
"resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "name": "[parameters('servers_name')]",
      "location": "southcentralus",
      "sku": {
                "name": "B_Gen5_1",
                "tier": "Basic",
                "family": "Gen5",
                "capacity": 1
            },
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
```

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
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

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
> [ Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
