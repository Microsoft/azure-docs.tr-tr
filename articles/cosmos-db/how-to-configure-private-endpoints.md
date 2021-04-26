---
title: Azure Cosmos hesabı için Azure özel bağlantısını yapılandırma
description: Bir sanal ağda özel bir IP adresi kullanarak bir Azure Cosmos hesabına erişmek için Azure özel bağlantısı ayarlamayı öğrenin.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/26/2021
ms.author: thweiss
ms.custom: devx-track-azurecli
ms.openlocfilehash: 034eb35eeef975be23cc318aa797282008d71728
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936912"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Azure Cosmos hesabı için Azure özel bağlantısını yapılandırma
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure özel bağlantısı ' nı kullanarak bir Azure Cosmos hesabına özel bir uç nokta aracılığıyla bağlanabilirsiniz. Özel uç nokta, sanal ağınız içindeki bir alt ağda bulunan özel IP adresleri kümesidir. Böylece, erişimi özel IP adresleri üzerinden bir Azure Cosmos hesabına sınırlayabilirsiniz. Özel bağlantı kısıtlanmış NSG ilkeleriyle birleştirildiğinde, veri sızdırma riskini azaltmaya yardımcı olur. Özel uç noktalar hakkında daha fazla bilgi için bkz. [Azure özel bağlantı](../private-link/private-link-overview.md) makalesi.

> [!NOTE]
> Özel bağlantı, Azure Cosmos uç noktalarınızın genel DNS tarafından çözülmesini engellemez. Gelen isteklerin filtrelenmesi, aktarım veya ağ düzeyinde değil, uygulama düzeyinde gerçekleşir.

Özel bağlantı, kullanıcıların bir Azure Cosmos hesabına sanal ağ içinden veya eşlenen herhangi bir sanal ağdan erişmesine olanak sağlar. Özel bağlantıyla eşlenen kaynaklara, VPN veya Azure ExpressRoute aracılığıyla özel eşleme üzerinden şirket içinde de erişilebilir.

Otomatik veya el ile onay yöntemini kullanarak, özel bağlantıyla yapılandırılmış bir Azure Cosmos hesabına bağlanabilirsiniz. Daha fazla bilgi edinmek için özel bağlantı belgelerinin [onay iş akışı](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) bölümüne bakın.

Bu makalede, Azure Cosmos DB işlemsel depolama için özel uç noktaların nasıl ayarlanacağı açıklanır. Otomatik onay yöntemini kullandığınızı varsayar. Analitik depoyu kullanıyorsanız, bkz. [analitik mağaza Için özel uç noktalar](analytical-store-private-endpoints.md) .

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Azure portal kullanarak özel uç nokta oluşturma

Azure portal kullanarak mevcut bir Azure Cosmos hesabı için özel bir uç nokta oluşturmak için aşağıdaki adımları kullanın:

1. **Tüm kaynaklar** bölmesinden bir Azure Cosmos hesabı seçin.

1. Ayarlar listesinden **Özel uç nokta bağlantıları** ' nı seçin ve ardından **Özel uç nokta**' ı seçin:

   :::image type="content" source="./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png" alt-text="Azure portal özel uç nokta oluşturma seçimleri":::

1. **Özel uç nokta oluşturma-temel bilgiler** bölmesinde, aşağıdaki ayrıntıları girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | Kaynak grubunu seçin.|
    | **Örnek ayrıntıları** |  |
    | Name | Özel uç noktanız için herhangi bir ad girin. Bu ad alındıysanız, benzersiz bir tane oluşturun. |
    |Region| Özel bağlantı dağıtmak istediğiniz bölgeyi seçin. Özel uç noktayı, sanal ağınızın bulunduğu aynı konumda oluşturun.|
    |||
1. **Sonraki: kaynak**' ı seçin.
1. **Özel uç nokta oluştur-kaynak** bölümünde bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |Bağlantı yöntemi  | **Dizinimde bir Azure kaynağına bağlan '** ı seçin. <br/><br/> Daha sonra, özel bağlantı kurmak için kaynaklarınızdan birini seçebilirsiniz. Veya sizinle paylaştığınız bir kaynak KIMLIĞI veya diğer ad kullanarak başka birisinin kaynağına bağlanabilirsiniz.|
    | Abonelik| Aboneliğinizi seçin. |
    | Kaynak türü | **Microsoft. Azumisinizsmosdb/databaseAccounts**' ı seçin. |
    | Kaynak |Azure Cosmos hesabınızı seçin. |
    |Hedef alt kaynak |Eşlemek istediğiniz Azure Cosmos DB API türünü seçin. Bu, varsayılan olarak SQL, MongoDB ve Cassandra API 'Leri için yalnızca bir seçeneğe sahiptir. Gremlin ve tablo API 'Leri için bu API 'Ler SQL API 'SI ile birlikte çalışabilen **SQL** ' i de seçebilirsiniz. |
    |||

1. Ileri 'yi seçin **: yapılandırma**.
1. **Özel uç nokta oluşturma-yapılandırma**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |**Ağ**| |
    | Sanal ağ| Sanal ağınızı seçin. |
    | Alt ağ | Alt ağlarınızı seçin. |
    |**Özel DNS tümleştirme**||
    |Özel DNS bölgesi ile tümleştirme |**Evet**’i seçin. <br><br/> Özel uç noktanıza özel olarak bağlanmak için bir DNS kaydına ihtiyacınız vardır. Özel uç noktanızı özel bir DNS bölgesiyle tümleştirmenizi öneririz. Ayrıca, kendi DNS sunucularınızı kullanabilir veya sanal makinelerinizdeki konak dosyalarını kullanarak DNS kayıtları oluşturabilirsiniz. <br><br/> Bu seçenek için Evet ' i seçtiğinizde, özel bir DNS bölge grubu da oluşturulur. DNS bölgesi grubu, özel DNS bölgesi ve özel uç nokta arasındaki bağlantıdır. Bu bağlantı, Özel uç noktada bir güncelleştirme olduğunda özel DNS bölgesini otomatik olarak güncelleştirmenize yardımcı olur. Örneğin, bölge eklediğinizde veya kaldırdığınızda, özel DNS bölgesi otomatik olarak güncelleştirilir. |
    |Özel DNS Bölgesi |**privatelink.documents.Azure.com** öğesini seçin. <br><br/> Özel DNS bölgesi otomatik olarak belirlenir. Azure portal kullanarak değiştiremezsiniz.|
    |||

1. **Gözden geçir ve oluştur**’u seçin. **Gözden geçir + oluştur** sayfasında, Azure yapılandırmanızı doğrular.
1. **Doğrulama başarılı** iletisini gördüğünüzde **Oluştur**’u seçin.

Bir Azure Cosmos hesabı için özel bağlantıyı onayladıysanız, Azure portal, **güvenlik duvarı ve sanal ağlar** bölmesindeki **tüm ağlar** seçeneği kullanılamaz.

## <a name="api-types-and-private-zone-names"></a><a id="private-zone-name-mapping"></a>API türleri ve özel bölge adları

Aşağıdaki tabloda, farklı Azure Cosmos hesabı API türleri, desteklenen alt kaynaklar ve karşılık gelen özel bölge adları arasındaki eşleme gösterilmektedir. Ayrıca, Gremlin ve Tablo API'si hesaplarına SQL API aracılığıyla erişebilirsiniz, bu nedenle bu API 'Ler için iki giriş vardır.

|Azure Cosmos hesabı API türü  |Desteklenen alt kaynaklar (veya grup kimlikleri) |Özel bölge adı  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Tablo    |    Tablo     |   privatelink.table.cosmos.azure.com    |
|Tablo     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Özel IP adreslerini getir

Özel uç nokta sağlandıktan sonra IP adreslerini sorgulayabilirsiniz. Azure portal IP adreslerini görüntülemek için:

1. **Tüm kaynaklar**’ı seçin.
1. Daha önce oluşturduğunuz özel uç nokta için arama yapın. Bu durumda, **cdbPrivateEndpoint3**.
1. DNS ayarlarını ve IP adreslerini görmek için **genel bakış** sekmesini seçin.

:::image type="content" source="./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png" alt-text="Azure portal özel IP adresleri":::

Özel uç nokta başına birden çok IP adresi oluşturuldu:

* Azure Cosmos hesabının genel (bölge belirsiz) uç noktası için bir tane
* Azure Cosmos hesabının dağıtıldığı her bölge için bir tane

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Azure PowerShell kullanarak özel uç nokta oluşturma

Mevcut bir Azure Cosmos hesabı için "MyPrivateEndpoint" adlı özel bir uç nokta oluşturmak için aşağıdaki PowerShell betiğini çalıştırın. Değişken değerlerini ortamınızın ayrıntıları ile değiştirin.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Özel uç noktasını özel bir DNS bölgesi ile tümleştirme

Özel uç noktasını oluşturduktan sonra, aşağıdaki PowerShell betiğini kullanarak özel bir DNS bölgesi ile tümleştirilebilir:

```azurepowershell-interactive
Import-Module Az.PrivateDns

# Zone name differs based on the API type and group ID you are using. 
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"

# Create DNS configuration

$PrivateDnsZoneId = $zone.ResourceId

$config = New-AzPrivateDnsZoneConfig -Name $zoneName`
 -PrivateDnsZoneId $PrivateDnsZoneId

## Create a DNS zone group
New-AzPrivateDnsZoneGroup -ResourceGroupName $ResourceGroupName`
 -PrivateEndpointName $PrivateEndpointName`
 -Name "MyPrivateZoneGroup"`
 -PrivateDnsZoneConfig $config
```

### <a name="fetch-the-private-ip-addresses"></a>Özel IP adreslerini getir

Özel uç nokta sağlandıktan sonra, aşağıdaki PowerShell betiğini kullanarak IP adreslerini ve FQDN eşlemesini sorgulayabilirsiniz:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Azure CLı kullanarak özel uç nokta oluşturma

Mevcut bir Azure Cosmos hesabı için "myPrivateEndpoint" adlı özel bir uç nokta oluşturmak için aşağıdaki Azure CLı betiğini çalıştırın. Değişken değerlerini ortamınızın ayrıntıları ile değiştirin.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Özel uç noktasını özel bir DNS bölgesi ile tümleştirme

Özel uç noktasını oluşturduktan sonra, aşağıdaki Azure CLı betiğini kullanarak özel bir DNS bölgesi ile tümleştirebilirsiniz:

```azurecli-interactive
#Zone name differs based on the API type and group ID you are using. 
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Create a DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group $ResourceGroupName \
   --endpoint-name $PrivateEndpointName \
   --name "MyPrivateZoneGroup" \
   --private-dns-zone $zoneName \
   --zone-name "myzone"
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak özel uç nokta oluşturma

Bir sanal ağ alt ağında özel bir uç nokta oluşturarak özel bir bağlantı kurabilirsiniz. Bunu bir Azure Resource Manager şablonu kullanarak elde edersiniz.

"PrivateEndpoint_template.json" adlı bir Kaynak Yöneticisi şablonu oluşturmak için aşağıdaki kodu kullanın. Bu şablon, mevcut bir sanal ağdaki mevcut bir Azure Cosmos SQL API hesabı için özel bir uç nokta oluşturur.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Şablon için parametreler dosyasını tanımlayın**

Şablon için bir parametre dosyası oluşturun ve bunu "PrivateEndpoint_parameters.jsüzerinde" olarak adlandırın. Aşağıdaki kodu parametreler dosyasına ekleyin:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**Bir PowerShell betiği kullanarak şablonu dağıtma**

Aşağıdaki kodu kullanarak bir PowerShell betiği oluşturun. Betiği çalıştırmadan önce, abonelik KIMLIĞINI, kaynak grubu adını ve diğer değişken değerlerini ortamınızın ayrıntıları ile değiştirin.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

PowerShell betikte, `GroupId` değişken yalnızca bir değer içerebilir. Bu değer, hesabın API türüdür. İzin verilen değerler: `Sql` , `MongoDB` , `Cassandra` , `Gremlin` , ve `Table` . Bazı Azure Cosmos hesap türlerine birden çok API aracılığıyla erişilebilir. Örnek:

* Gremlin API hesabına hem Gremlin hem de SQL API hesaplarından erişilebilir.
* Tablo API'si hesaba hem tablo hem de SQL API hesaplarından erişilebilir.

Bu hesaplar için, her API türü için bir özel uç nokta oluşturmanız gerekir. Karşılık gelen API türü `GroupId` dizide belirtilir.

Şablon başarıyla dağıtıldıktan sonra, aşağıdaki görüntüde gösterilene benzer bir çıktı görebilirsiniz. Bu `provisioningState` değer, `Succeeded` Özel uç noktaların doğru şekilde ayarlandığının değeridir.

:::image type="content" source="./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png" alt-text="Kaynak Yöneticisi şablonu için dağıtım çıkışı":::

Şablon dağıtıldıktan sonra, özel IP adresleri alt ağ içinde ayrılır. Azure Cosmos hesabının güvenlik duvarı kuralı yalnızca özel uç noktadan gelen bağlantıları kabul edecek şekilde yapılandırılmıştır.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Özel uç noktayı bir Özel DNS bölgesiyle tümleştirme

"PrivateZone_template.json" adlı bir Kaynak Yöneticisi şablonu oluşturmak için aşağıdaki kodu kullanın. Bu şablon, mevcut bir sanal ağdaki mevcut bir Azure Cosmos SQL API hesabı için özel bir DNS bölgesi oluşturur.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

**Şablon için parametreler dosyasını tanımlayın**

Şablon için aşağıdaki iki parametre dosyasını oluşturun. "PrivateZone_parameters.jsüzerinde" oluşturun. aşağıdaki kodla:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

"PrivateZoneGroup_template.json" adlı bir Kaynak Yöneticisi şablonu oluşturmak için aşağıdaki kodu kullanın. Bu şablon, mevcut bir sanal ağdaki mevcut bir Azure Cosmos SQL API hesabı için özel bir DNS bölge grubu oluşturur.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "PrivateEndpointDnsGroupName": {
            "value": "string"
        },
        "privateEndpointName":{
            "value": "string"
        }        
    },
    "resources": [
        {
            "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
            "apiVersion": "2020-06-01",
            "name": "[parameters('PrivateEndpointDnsGroupName')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]",
                "[variables('privateEndpointName')]"
            ],
          "properties": {
            "privateDnsZoneConfigs": [
              {
                "name": "config1",
                "properties": {
                  "privateDnsZoneId": "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
                }
              }
            ]
          }
        }
    ]
}
```

**Şablon için parametreler dosyasını tanımlayın**

Şablon için aşağıdaki iki parametre dosyasını oluşturun. "PrivateZoneGroup_parameters.jsüzerinde" oluşturun. aşağıdaki kodla:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "PrivateEndpointDnsGroupName": {
            "value": ""
        },
        "privateEndpointName":{
            "value": ""
        }
    }
}
```

**Bir PowerShell betiği kullanarak şablonu dağıtma**

Aşağıdaki kodu kullanarak bir PowerShell betiği oluşturun. Betiği çalıştırmadan önce, abonelik KIMLIĞINI, kaynak grubu adını ve diğer değişken değerlerini ortamınızın ayrıntıları ile değiştirin.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

# Name of the DNS zone group to create
$PrivateEndpointDnsGroupName = "myPrivateDNSZoneGroup"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"
$PrivateZoneGroupTemplateFilePath = "PrivateZoneGroup_template.json"
$PrivateZoneGroupParametersFilePath = "PrivateZoneGroup_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneGroupDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneGroupTemplateFilePath `
    -TemplateParameterFile $PrivateZoneGroupParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -PrivateEndpointName $PrivateEndpointName`
    -PrivateEndpointDnsGroupName $PrivateEndpointDnsGroupName

```

## <a name="configure-custom-dns"></a>Özel API'yi yapılandırma

Özel uç noktasını oluşturduğunuz alt ağ içinde özel bir DNS bölgesi kullanmanız gerekir. Uç noktaları, her özel IP adresinin bir DNS girdisiyle eşlenecek şekilde yapılandırın. ( `fqdns` Daha önce gösterilen yanıtta özelliğine bakın.)

Özel uç noktayı oluştururken, Azure 'da özel bir DNS bölgesi ile tümleştirilebilir. Bunun yerine özel bir DNS bölgesi kullanmayı tercih ederseniz, Özel uç nokta için ayrılan tüm özel IP adresleri için DNS kayıtları eklemek üzere yapılandırmanız gerekir.

> [!IMPORTANT]
> Bu isteklerin özel uç noktalarınız üzerinde gidip gitmediğini belirleyen isteklerinizin DNS çözümünden veya standart genel rotayı alıp almayacağını belirler. Yerel DNS 'nizin özel uç noktanız tarafından eşlenen özel IP 'ye doğru şekilde başvurduğundan emin olun.

## <a name="private-link-combined-with-firewall-rules"></a>Özel bağlantı, güvenlik duvarı kurallarıyla birleştirildi

Özel bağlantıyı güvenlik duvarı kurallarıyla birlikte kullandığınızda aşağıdaki durumlar ve sonuçlar mümkündür:

* Herhangi bir güvenlik duvarı kuralı yapılandırmazsanız, varsayılan olarak tüm trafik bir Azure Cosmos hesabına erişebilir.

* Ortak trafiği veya bir hizmet uç noktasını yapılandırırsanız ve özel uç noktalar oluşturursanız, ilgili güvenlik duvarı kuralı tarafından farklı gelen trafik türlerine izin verilir. Hizmet uç noktasının de yapılandırıldığı bir alt ağda özel bir uç nokta yapılandırılmışsa:
  * Özel uç noktayla eşlenen veritabanı hesabına giden trafik, Özel uç nokta aracılığıyla yönlendirilir,
  * alt ağdan diğer veritabanı hesaplarına giden trafik, hizmet uç noktası aracılığıyla yönlendirilir.

* Herhangi bir ortak trafik veya hizmet uç noktası yapılandırmazsanız ve özel uç noktalar oluşturursanız, Azure Cosmos hesabına yalnızca özel uç noktalar aracılığıyla erişilebilir. Genel trafiği veya bir hizmet uç noktası yapılandırmazsanız, tüm onaylanan özel uç noktalar reddedildikten veya silindikten sonra, PublicNetworkAccess devre dışı olarak ayarlanmadığı takdirde hesap tüm ağa açıktır (aşağıdaki bölüme bakın).

## <a name="blocking-public-network-access-during-account-creation"></a>Hesap oluşturma sırasında ortak ağ erişimini engelleme

Önceki bölümde açıklandığı gibi, belirli güvenlik duvarı kuralları ayarlanmamışsa, özel bir uç nokta eklemek Azure Cosmos hesabınızı yalnızca özel uç noktalar aracılığıyla erişilebilir hale getirir. Bu, Azure Cosmos hesabına oluşturulduktan sonra ve özel bir uç nokta eklendikten sonra ortak trafiğinden erişilebileceği anlamına gelir. Genel ağ erişiminin özel uç noktaları oluşturmadan önce bile devre dışı bırakıldığından emin olmak için, `publicNetworkAccess` bayrağı `Disabled` Hesap oluşturma sırasında ayarlayabilirsiniz. Bu bayrağın herhangi bir IP veya sanal ağ kuralıyla öncelikli olduğunu unutmayın; `Disabled`güvenlik duvarı yapılandırmasında kaynak IP veya sanal ağa izin verilse bile, bayrak olarak ayarlandığında tüm ortak ve sanal ağ trafiği engellenir.

Bu bayrağın nasıl kullanılacağını gösteren bir örnek için [bu Azure Resource Manager şablonuna](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/) bakın.

## <a name="adding-private-endpoints-to-an-existing-cosmos-account-with-no-downtime"></a>Kapalı kalma süresi olmadan mevcut bir Cosmos hesabına özel uç noktalar ekleme

Varsayılan olarak, var olan bir hesaba özel bir uç nokta eklemek yaklaşık 5 dakikalık kısa bir kapalı kalma süresine neden olur. Bu kapalı kalma süresini önlemek için aşağıdaki yönergeleri izleyin:

1. İstemci bağlantılarınızı açıkça izin vermek için Güvenlik Duvarı yapılandırmanıza IP veya sanal ağ kuralları ekleyin.
1. Yapılandırma güncelleştirmesinin uygulandığından emin olmak için 10 dakika bekleyin.
1. Yeni özel uç noktanızı yapılandırın.
1. Adım 1 ' de ayarlanan güvenlik duvarı kurallarını kaldırın.

## <a name="port-range-when-using-direct-mode"></a>Doğrudan mod kullanılırken bağlantı noktası aralığı

Bir Azure Cosmos hesabıyla doğrudan mod bağlantısıyla özel bağlantı kullanırken, TCP bağlantı noktalarının (0-65535) tam aralığının açık olduğundan emin olmanız gerekir.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Bölge eklediğinizde veya kaldırdığınızda özel bir uç noktayı güncelleştirme

Örneğin, üç bölgede bir Azure Cosmos hesabı dağıtırsanız: "Batı ABD," "Orta ABD" ve "Batı Avrupa." Hesabınız için özel bir uç nokta oluşturduğunuzda, alt ağda dört özel IP ayrılır. Üç bölgenin her biri için bir IP vardır ve küresel/bölge agtik uç noktası için bir IP vardır. Daha sonra, Azure Cosmos hesabına yeni bir bölge (örneğin, "Doğu ABD") ekleyebilirsiniz. Özel DNS bölgesi aşağıdaki şekilde güncelleştirilir:

* **Özel DNS bölge grubu kullanılıyorsa:**

  Özel bir DNS bölge grubu kullanıyorsanız, Özel uç nokta güncelleştirildiği zaman özel DNS bölgesi otomatik olarak güncelleştirilir. Önceki örnekte, yeni bir bölge eklendikten sonra, özel DNS bölgesi otomatik olarak güncelleştirilir.

* **Özel DNS bölge grubu kullanılmazsa:**

  Özel bir DNS bölge grubu kullanmıyorsanız, bir Azure Cosmos hesabına bölge ekleme veya kaldırma, bu hesaba ait DNS girdilerini eklemenizi veya kaldırmanızı gerektirir. Bölgeler eklendikten veya kaldırıldıktan sonra alt ağın özel DNS bölgesini, eklenen veya kaldırılan DNS girişlerini ve bunlara karşılık gelen özel IP adreslerini yansıtacak şekilde güncelleştirebilirsiniz.

  Önceki örnekte, yeni bölge eklendikten sonra, özel DNS bölgenize veya özel DNS 'ye karşılık gelen bir DNS kaydı eklemeniz gerekir. Bir bölgeyi kaldırırken de aynı adımları kullanabilirsiniz. Bölge kaldırıldıktan sonra, ilgili DNS kaydını özel DNS bölgeinizden veya özel DNS 'nizden kaldırmanız gerekir.

## <a name="current-limitations"></a>Geçerli sınırlamalar

Bir Azure Cosmos hesabıyla özel bağlantı kullandığınızda aşağıdaki sınırlamalar geçerlidir:

* Tek bir Azure Cosmos hesabında 200 ' den fazla özel uç noktası olamaz.

* Bir Azure Cosmos hesabıyla doğrudan mod bağlantısı aracılığıyla özel bağlantı kullanırken, yalnızca TCP protokolünü kullanabilirsiniz. HTTP protokolü Şu anda desteklenmiyor.

* MongoDB hesapları için Azure Cosmos DB API 'sini kullanırken, yalnızca sunucu sürümü 3,6 (yani, o şekilde, o) hesapları için özel bir uç nokta desteklenir `*.mongo.cosmos.azure.com` . Özel bağlantı, sunucu sürümü 3,2 (yani, bu uç noktayı kullanan hesaplar) hesapları için desteklenmez `*.documents.azure.com` . Özel bağlantıyı kullanmak için eski hesapları yeni sürüme geçirmeniz gerekir.

* Özel bağlantısı olan MongoDB hesabı için bir Azure Cosmos DB API 'SI kullanırken, Araçlar/kitaplıklar hizmet adı tanımlamasını (SNı) desteklemelidir veya `appName` bağlantı dizesinden parametreyi doğru bir şekilde bağlanacak şekilde geçirmeye melidir. Bazı eski araçlar/kitaplıklar özel bağlantı özelliğini kullanmak için uyumlu olmayabilir.

* `Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action`Otomatik olarak onaylanan özel uç noktalar oluşturmak için, bir ağ yöneticisine en azından Azure Cosmos hesap kapsamında izin verilmelidir.

### <a name="limitations-to-private-dns-zone-integration"></a>Özel DNS bölge tümleştirmesi sınırlamaları

Özel bir DNS bölge grubu kullanmıyorsanız, özel bir uç noktayı sildiğinizde veya Azure Cosmos hesabından bir bölge kaldırdığınızda özel DNS bölgesindeki DNS kayıtları otomatik olarak kaldırılmaz. Önce DNS kayıtlarını el ile kaldırmanız gerekir:

* Bu özel DNS bölgesine bağlı yeni bir özel uç nokta ekleniyor.
* Özel uç noktaları bu özel DNS bölgesine bağlı olan herhangi bir veritabanı hesabına yeni bir bölge ekleme.

DNS kayıtlarını temizleyemezseniz, beklenmeyen veri düzlemi sorunları ortaya çıkabilir. Bu sorunlar, Özel uç nokta kaldırma veya bölge kaldırma işleminden sonra eklenen bölgelere yönelik veri kesintilerine dahildir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB güvenlik özellikleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* Azure Cosmos DB için bir güvenlik duvarı yapılandırmak için bkz. [güvenlik duvarı desteği](how-to-configure-firewall.md).

* Azure Cosmos hesabınız için bir sanal ağ hizmeti uç noktası yapılandırma hakkında bilgi edinmek için bkz. [sanal ağlardan erişimi yapılandırma](how-to-configure-vnet-service-endpoint.md).

* Özel bağlantı hakkında daha fazla bilgi edinmek için bkz. [Azure özel bağlantı](../private-link/private-link-overview.md) belgeleri.
