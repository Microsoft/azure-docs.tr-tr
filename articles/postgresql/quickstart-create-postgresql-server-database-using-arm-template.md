---
title: 'Hızlı başlangıç: PostgreSQL için Azure DB-ARM şablonu oluşturma'
description: Bu hızlı başlangıçta, bir Azure Resource Manager şablonu kullanarak PostgreSQL için Azure veritabanı oluşturma hakkında bilgi edinin.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/11/2021
ms.openlocfilehash: db65a4bb5137da23f125d67920a2ff9a1a801141
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606732"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---single-server"></a>Hızlı başlangıç: bir ARM şablonu kullanarak PostgreSQL için Azure veritabanı oluşturma-tek sunucu

PostgreSQL için Azure Veritabanı, bulutta son derece kullanılabilir olan PostgreSQL veritabanlarını çalıştırmak, yönetmek ve ölçeklendirmek için kullandığınız, yönetilen bir hizmettir. Bu hızlı başlangıçta, Azure portal, PowerShell veya Azure CLı 'de PostgreSQL için Azure veritabanı oluşturmak üzere bir Azure Resource Manager şablonu (ARM şablonu) kullanırsınız.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure 'a dağıtma":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

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

Yapılandırılmış bir işlem ve depolama kaynakları kümesi ile PostgreSQL için Azure veritabanı sunucusu oluşturun. Daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı 'Nda fiyatlandırma katmanları-tek sunucu](concepts-pricing-tiers.md). Sunucu, [Azure kaynak grubu](../azure-resource-manager/management/overview.md) içinde oluşturulur.

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-managed-postgresql-with-vnet/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-managed-postgresql-with-vnet/azuredeploy.json":::

Şablon, beş Azure kaynağı tanımlar:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/virtualNetworks/alt ağları**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft. DBforPostgreSQL/sunucuları**](/azure/templates/microsoft.dbforpostgresql/servers)
* [**Microsoft. DBforPostgreSQL/Servers/virtualNetworkRules**](/azure/templates/microsoft.dbforpostgresql/servers/virtualnetworkrules)
* [**Microsoft. DBforPostgreSQL/Servers/firewallRules**](/azure/templates/microsoft.dbforpostgresql/servers/firewallrules)

PostgreSQL için Azure veritabanı şablon örnekleri, [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbforpostgresql&pageNumber=1&sort=Popular)bulunabilir.

## <a name="deploy-the-template"></a>Şablonu dağıtma

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal PostgreSQL için Azure veritabanı sunucu şablonunu dağıtmak için aşağıdaki bağlantıyı seçin:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure’a dağıtın":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

**VNET Ile PostgreSQL Için Azure veritabanı 'Nı dağıtma** sayfası:

1. **Kaynak grubu** Için **Yeni oluştur**' u seçin, yeni kaynak grubu için bir ad girin ve **Tamam**' ı seçin.

2. Yeni bir kaynak grubu oluşturduysanız, kaynak grubu ve yeni sunucu için bir **konum** seçin.

3. **Sunucu adı**, **yönetici oturumu açma** ve **yönetici oturum açma parolası** girin.

    :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-arm-template/deploy-azure-database-for-postgresql-with-vnet.png" alt-text="VNet ile PostgreSQL için Azure veritabanı 'nı dağıtma, Azure hızlı başlangıç şablonu, Azure portal":::

4. İsterseniz diğer varsayılan ayarları değiştirin:

    * **Abonelik**: sunucu için kullanmak istediğiniz Azure aboneliği.
    * **SKU kapasitesi**: *2* (varsayılan), *4*, *8*, *16*, *32* veya *64* olabilen Vcore kapasitesi.
    * **SKU adı**: *B_Gen5_1*, *GP_Gen5_2* (varsayılan) veya *MO_Gen5_32* gıbı alt çizgilerden BIRLEŞTIRILMIŞ SKU katmanı öneki, SKU ailesi ve SKU kapasitesi.
    * **SKU boyutu MB**: PostgreSQL Için Azure veritabanı sunucusu 'nun megabayt cinsinden depolama boyutu (varsayılan *51200*).
    * **SKU katmanı**: *temel*, *generalamacını* (varsayılan) veya *memoryoptıılanmış* gibi dağıtım katmanı.
    * **SKU ailesi**: sunucu dağıtımı için donanım oluşturmayı gösteren *4. nesil* veya *5. nesil* (varsayılan).
    * **PostgreSQL sürümü**: dağıtılacak PostgreSQL sunucusunun sürümü, örneğin *9,5*, *9,6*, *10* veya *11* (varsayılan).
    * **Yedekleme bekletme günleri**: coğrafi olarak yedekli yedekleme saklama için gün cinsinden istenen süre (varsayılan *7*).
    * **Coğrafi olarak yedekli yedekleme**: coğrafi olağanüstü durum kurtarma (COĞRAFI-Dr) gereksinimlerine bağlı olarak *etkin* veya *devre dışı* (varsayılan).
    * **Sanal ağ adı**: sanal ağın adı (varsayılan *azure_postgresql_vnet*).
    * **Alt ağ adı**: alt ağın adı (varsayılan *azure_postgresql_subnet*).
    * **Sanal ağ kuralı adı**: alt ağa izin veren sanal ağ kuralının adı (varsayılan *allowsubnet*).
    * **VNET adresi ön eki**: sanal ağın adres ön eki (varsayılan *10.0.0.0/16*).
    * **Alt ağ ön eki**: alt ağın adres ön eki (varsayılan *10.0.0.0/16*).

5. Hüküm ve koşulları okuyun ve ardından **yukarıda belirtilen hüküm ve koşulları kabul ediyorum**' u seçin.

6. **Satın al**'ı seçin.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Şablonu kullanarak yeni bir PostgreSQL için Azure veritabanı sunucusu oluşturmak üzere aşağıdaki etkileşimli kodu kullanın. Kod sizden yeni sunucu adı, yeni bir kaynak grubunun adını ve konumunu ve bir yönetici hesabı adı ile parolasını ister.

Kodu Azure Cloud Shell çalıştırmak için herhangi bir kod bloğunun üst köşesinde **deneyin** ' i seçin.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Şablonu kullanarak yeni bir PostgreSQL için Azure veritabanı sunucusu oluşturmak üzere aşağıdaki etkileşimli kodu kullanın. Kod sizden yeni sunucu adı, yeni bir kaynak grubunun adını ve konumunu ve bir yönetici hesabı adı ile parolasını ister.

Kodu Azure Cloud Shell çalıştırmak için herhangi bir kod bloğunun üst köşesinde **deneyin** ' i seçin.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for PostgreSQL server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for PostgreSQL server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

# <a name="portal"></a>[Portal](#tab/azure-portal)

Yeni PostgreSQL için Azure veritabanı sunucusuna genel bir bakış görmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com), **PostgreSQL için Azure veritabanı sunucuları**' nı arayıp seçin.

2. Veritabanı listesinde yeni sunucunuzu seçin. Yeni PostgreSQL için Azure veritabanı sunucunuzun **genel bakış** sayfası görüntülenir.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

PostgreSQL için Azure veritabanı sunucusu ile ilgili ayrıntıları görüntülemek için aşağıdaki etkileşimli kodu çalıştırın. Yeni sunucunun adını girmeniz gerekir.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DbForPostgreSQL/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

PostgreSQL için Azure veritabanı sunucusu ile ilgili ayrıntıları görüntülemek için aşağıdaki etkileşimli kodu çalıştırın. Yeni sunucunun adını ve kaynak grubunu girmeniz gerekir.

```azurecli-interactive
read -p "Enter your Azure Database for PostgreSQL server name: " serverName &&
read -p "Enter the resource group where the Azure Database for PostgreSQL server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForPostgreSQL/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="exporting-arm-template-from-the-portal"></a>, Portaldan ARM şablonu dışarı aktarılıyor
[BIR ARM şablonunu Azure Portal dışa aktarabilirsiniz](../azure-resource-manager/templates/export-template-portal.md) . Şablonu dışarı aktarmanın iki yolu vardır:

- [Kaynak grubundan veya kaynaktan dışarı aktarın](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource). Bu seçenek, mevcut kaynaklardan yeni bir şablon oluşturur. Bu şablon, kaynak grubunun geçerli durumunun "Snapshot" ' dır. Kaynak grubunun tamamını veya kaynak grubu içindeki belirli kaynakları dışarı aktarabilirsiniz.
- [Dağıtımdan veya geçmişten önce dışarı aktarın](../azure-resource-manager/templates/export-template-portal.md#export-template-before-deployment). Bu seçenek, dağıtım için kullanılan bir şablonun tam bir kopyasını alır.

Şablonu dışarı aktarırken, ```"properties":{ }```  PostgreSQL sunucu kaynağının bölümünde, güvenlik nedenleriyle bu olduğunu fark edersiniz ```administratorLogin``` ```administratorLoginPassword``` . Şablonu dağıtılmadan önce bu parametreleri şablonunuza eklemeniz **gerekir** , aksi olarak şablon başarısız olur.

```
"resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
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

Şablon oluşturma sürecinde size kılavuzluk eden adım adım bir öğretici için, bkz.:

> [!div class="nextstepaction"]
> [ Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
