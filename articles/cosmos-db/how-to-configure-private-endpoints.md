---
title: Azure Cosmos hesabı için Azure özel bağlantısını yapılandırma
description: Bir sanal ağda özel bir IP adresi kullanarak bir Azure Cosmos hesabına erişmek için Azure özel bağlantısı ayarlamayı öğrenin.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: fde8829da3e523ced44143db0dee6b93cf9152bd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147775"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Azure Cosmos hesabı için Azure özel bağlantısını yapılandırma (Önizleme)

Azure özel bağlantısı ' nı kullanarak bir Azure Cosmos hesabına özel bir uç nokta aracılığıyla bağlanabilirsiniz. Özel uç nokta, sanal ağınız içindeki bir alt ağda bulunan özel IP adresleri kümesidir. Böylece, erişimi özel IP adresleri üzerinden bir Azure Cosmos hesabına sınırlayabilirsiniz. Özel bağlantı kısıtlanmış NSG ilkeleriyle birleştirildiğinde, veri sızdırma riskini azaltmaya yardımcı olur. Özel uç noktalar hakkında daha fazla bilgi için bkz. [Azure özel bağlantı](../private-link/private-link-overview.md) makalesi.

Özel bağlantı, kullanıcıların bir Azure Cosmos hesabına sanal ağ içinden veya eşlenen herhangi bir sanal ağdan erişmesine olanak sağlar. Özel bağlantıyla eşlenen kaynaklara, VPN veya Azure ExpressRoute aracılığıyla özel eşleme üzerinden şirket içinde de erişilebilir. 

Otomatik veya el ile onay yöntemini kullanarak, özel bağlantıyla yapılandırılmış bir Azure Cosmos hesabına bağlanabilirsiniz. Daha fazla bilgi edinmek için özel bağlantı belgelerinin [onay iş akışı](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) bölümüne bakın. 

Bu makalede özel bir uç nokta oluşturma adımları açıklanır. Otomatik onay yöntemini kullandığınızı varsayar.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Azure portal kullanarak özel uç nokta oluşturma

Azure portal kullanarak mevcut bir Azure Cosmos hesabı için özel bir uç nokta oluşturmak için aşağıdaki adımları kullanın:

1. **Tüm kaynaklar** bölmesinden bir Azure Cosmos hesabı seçin.

1. Ayarlar listesinden **Özel uç nokta bağlantıları** ' nı seçin ve ardından **Özel uç nokta**' ı seçin:

   ![Azure portal özel uç nokta oluşturma seçimleri](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. **Özel uç nokta (Önizleme) oluştur-temel bilgiler** bölmesinde, aşağıdaki ayrıntıları girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | Kaynak grubunu seçin.|
    | **Örnek ayrıntıları** |  |
    | Ad | Özel uç noktanız için herhangi bir ad girin. Bu ad alındıysanız, benzersiz bir tane oluşturun. |
    |Bölge| Özel bağlantı dağıtmak istediğiniz bölgeyi seçin. Özel uç noktayı, sanal ağınızın bulunduğu aynı konumda oluşturun.|
    |||
1. **Sonraki: kaynak**' ı seçin.
1. **Özel uç nokta oluştur-kaynak**bölümünde bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |Bağlantı yöntemi  | **Dizinimde bir Azure kaynağına bağlan '** ı seçin. <br/><br/> Daha sonra, özel bağlantı kurmak için kaynaklarınızdan birini seçebilirsiniz. Veya sizinle paylaştığınız bir kaynak KIMLIĞI veya diğer ad kullanarak başka birisinin kaynağına bağlanabilirsiniz.|
    | Abonelik| Aboneliğinizi seçin. |
    | Kaynak türü | **Microsoft. Azumisinizsmosdb/databaseAccounts**' ı seçin. |
    | Kaynak |Azure Cosmos hesabınızı seçin. |
    |Hedef alt kaynak |Eşlemek istediğiniz Azure Cosmos DB API türünü seçin. Bu, varsayılan olarak SQL, MongoDB ve Cassandra API 'Leri için yalnızca bir seçeneğe sahiptir. Gremlin ve tablo API 'Leri için bu API 'Ler SQL API 'SI ile birlikte çalışabilen **SQL** ' i de seçebilirsiniz. |
    |||

1. Ileri 'yi seçin **: yapılandırma**.
1. **Özel uç nokta (Önizleme) oluştur-yapılandırma**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |**Ağ**| |
    | Sanal ağ| Sanal ağınızı seçin. |
    | Alt ağ | Alt ağlarınızı seçin. |
    |**Özel DNS tümleştirme**||
    |Özel DNS bölgesiyle tümleştirin |**Evet**’i seçin. <br><br/> Özel uç noktanıza özel olarak bağlanmak için bir DNS kaydına ihtiyacınız vardır. Özel uç noktanızı özel bir DNS bölgesiyle tümleştirmenizi öneririz. Ayrıca, kendi DNS sunucularınızı kullanabilir veya sanal makinelerinizdeki konak dosyalarını kullanarak DNS kayıtları oluşturabilirsiniz. |
    |Özel DNS bölgesi |**Privatelink.Documents.Azure.com**öğesini seçin. <br><br/> Özel DNS bölgesi otomatik olarak belirlenir. Azure portal kullanarak değiştiremezsiniz.|
    |||

1. **İncele ve oluştur**’u seçin. **Gözden geçir + oluştur** sayfasında, Azure yapılandırmanızı doğrular.
1. **Doğrulama başarılı** Iletisini gördüğünüzde **Oluştur**' u seçin.

Bir Azure Cosmos hesabı için özel bağlantıyı onayladıysanız, Azure portal, **güvenlik duvarı ve sanal ağlar** bölmesindeki **tüm ağlar** seçeneği kullanılamaz.

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

![Azure portal özel IP adresleri](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

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
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
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

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak özel uç nokta oluşturma

Bir sanal ağ alt ağında özel bir uç nokta oluşturarak özel bir bağlantı kurabilirsiniz. Bunu bir Azure Resource Manager şablonu kullanarak elde edersiniz.

"PrivateEndpoint_template. JSON" adlı bir Kaynak Yöneticisi şablonu oluşturmak için aşağıdaki kodu kullanın. Bu şablon, mevcut bir sanal ağdaki mevcut bir Azure Cosmos SQL API hesabı için özel bir uç nokta oluşturur.

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

Şablon için bir parametre dosyası oluşturun ve "PrivateEndpoint_parameters. JSON" olarak adlandırın. Aşağıdaki kodu parametreler dosyasına ekleyin:

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

PowerShell betiğinde `GroupId` değişkeni yalnızca bir değer içerebilir. Bu değer, hesabın API türüdür. İzin verilen değerler: `Sql`, `MongoDB`, `Cassandra`, `Gremlin`ve `Table`. Bazı Azure Cosmos hesap türlerine birden çok API aracılığıyla erişilebilir. Örneğin:

* Gremlin API hesabına hem Gremlin hem de SQL API hesaplarından erişilebilir.
* Tablo API'si hesaba hem tablo hem de SQL API hesaplarından erişilebilir.

Bu hesaplar için, her API türü için bir özel uç nokta oluşturmanız gerekir. Karşılık gelen API türü `GroupId` dizisinde belirtilir.

Şablon başarıyla dağıtıldıktan sonra, aşağıdaki görüntüde gösterilene benzer bir çıktı görebilirsiniz. Özel uç noktalar doğru ayarlandıysa `provisioningState` değeri `Succeeded`.

![Kaynak Yöneticisi şablonu için dağıtım çıkışı](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Şablon dağıtıldıktan sonra, özel IP adresleri alt ağ içinde ayrılır. Azure Cosmos hesabının güvenlik duvarı kuralı yalnızca özel uç noktadan gelen bağlantıları kabul edecek şekilde yapılandırılmıştır.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Özel uç noktayı bir Özel DNS bölgesiyle tümleştirme

"PrivateZone_template. JSON" adlı bir Kaynak Yöneticisi şablonu oluşturmak için aşağıdaki kodu kullanın. Bu şablon, mevcut bir sanal ağdaki mevcut bir Azure Cosmos SQL API hesabı için özel bir DNS bölgesi oluşturur.

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

"PrivateZoneRecords_template. JSON" adlı bir Kaynak Yöneticisi şablonu oluşturmak için aşağıdaki kodu kullanın.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }       
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }   
    ]
}
```

**Şablon için parametreler dosyasını tanımlayın**

Şablon için aşağıdaki iki parametre dosyasını oluşturun. "PrivateZone_parameters. JSON" oluşturun. aşağıdaki kodla:

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

"PrivateZoneRecords_parameters. JSON" oluşturun. aşağıdaki kodla:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
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

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

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

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Özel API'yi yapılandırma

Özel uç noktasını oluşturduğunuz alt ağ içinde özel bir DNS bölgesi kullanmanız gerekir. Uç noktaları, her özel IP adresinin bir DNS girdisiyle eşlenecek şekilde yapılandırın. (Daha önce gösterilen yanıtta `fqdns` özelliğine bakın.)

Özel uç noktayı oluştururken, Azure 'da özel bir DNS bölgesi ile tümleştirilebilir. Bunun yerine özel bir DNS bölgesi kullanmayı tercih ederseniz, Özel uç nokta için ayrılan tüm özel IP adresleri için DNS kayıtları eklemek üzere yapılandırmanız gerekir.

## <a name="private-link-combined-with-firewall-rules"></a>Özel bağlantı, güvenlik duvarı kurallarıyla birleştirildi

Özel bağlantıyı güvenlik duvarı kurallarıyla birlikte kullandığınızda aşağıdaki durumlar ve sonuçlar mümkündür:

* Herhangi bir güvenlik duvarı kuralı yapılandırmazsanız, varsayılan olarak tüm trafik bir Azure Cosmos hesabına erişebilir.

* Ortak trafiği veya bir hizmet uç noktasını yapılandırırsanız ve özel uç noktalar oluşturursanız, ilgili güvenlik duvarı kuralı tarafından farklı gelen trafik türlerine izin verilir.

* Herhangi bir ortak trafik veya hizmet uç noktası yapılandırmazsanız ve özel uç noktalar oluşturursanız, Azure Cosmos hesabına yalnızca özel uç noktalar aracılığıyla erişilebilir. Ortak trafiği veya bir hizmet uç noktasını yapılandırmazsanız, tüm onaylanan özel uç noktalar reddedildikten veya silindikten sonra hesap tüm ağa açıktır.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Bölge eklediğinizde veya kaldırdığınızda özel bir uç noktayı güncelleştirme

Azure Cosmos hesabına bölge ekleme veya kaldırma, bu hesaba ait DNS girdilerini eklemenizi veya kaldırmanızı gerektirir. Aşağıdaki adımları kullanarak bu değişiklikleri özel uç noktada bunlara göre güncelleştirin:

1. Azure Cosmos DB Yöneticisi bölge ekler veya kaldırdığında, ağ yöneticisi bekleyen değişiklikler hakkında bir bildirim alır. Azure Cosmos hesabıyla eşlenen özel uç nokta için `ActionsRequired` özelliğinin değeri `None` `Recreate`olarak değişir. Daha sonra, ağ yöneticisi özel uç noktayı, onu oluşturmak için kullanılan aynı Kaynak Yöneticisi yüküne sahip bir PUT isteği vererek günceller.

1. Özel uç nokta güncelleştirildikten sonra, alt ağın özel DNS bölgesini, eklenen veya kaldırılan DNS girişlerini ve bunlara karşılık gelen özel IP adreslerini yansıtacak şekilde güncelleştirebilirsiniz.

Örneğin, bir Azure Cosmos hesabını üç bölgede dağıtdığınızı düşünün: "Batı ABD," "Orta ABD," ve "Batı Avrupa." Hesabınız için özel bir uç nokta oluşturduğunuzda, alt ağda dört özel IP ayrılır. Üç bölgenin her biri için bir IP vardır ve küresel/bölge agtik uç noktası için bir IP vardır.

Daha sonra, Azure Cosmos hesabına yeni bir bölge (örneğin, "Doğu ABD") ekleyebilirsiniz. Varsayılan olarak, yeni bölgeye mevcut özel uç noktadan erişilemez. Azure Cosmos Hesap Yöneticisi, yeni bölgeden erişmeden önce özel uç nokta bağlantısını yenilemelidir. 

` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` komutunu çalıştırdığınızda komutun çıktısı `actionsRequired` parametresini içerir. Bu parametre `Recreate`olarak ayarlanır. Bu değer, Özel uç noktanın yenilenmesi gerektiğini gösterir. Daha sonra, Azure Cosmos Hesap Yöneticisi özel uç nokta yenilemeyi tetiklemek için `Set-AzPrivateEndpoint` komutunu çalıştırır.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Bu özel uç nokta altındaki alt ağda yeni bir özel IP otomatik olarak ayrılır. `actionsRequired` değeri `None`olur. Herhangi bir özel DNZ bölge tümleştirmesi yoksa (başka bir deyişle, özel bir özel DNS bölgesi kullanıyorsanız), özel DNS bölgenizi yeni bölgeye karşılık gelen özel IP için yeni bir DNS kaydı ekleyecek şekilde yapılandırmanız gerekir.

Bir bölgeyi kaldırırken de aynı adımları kullanabilirsiniz. Kaldırılan bölgenin özel IP 'si otomatik olarak geri kazanılır ve `actionsRequired` bayrağı `None`olur. Herhangi bir özel DNZ bölge tümleştirmesi yoksa, özel DNS bölgenizi, kaldırılan bölgenin DNS kaydını kaldıracak şekilde yapılandırmanız gerekir.

Özel bir uç nokta silindiğinde veya Azure Cosmos hesabından bir bölge kaldırıldığında özel DNS bölgesindeki DNS kayıtları otomatik olarak kaldırılmaz. DNS kayıtlarını el ile kaldırmanız gerekir.

## <a name="current-limitations"></a>Geçerli sınırlamalar

Bir Azure Cosmos hesabıyla özel bağlantı kullandığınızda aşağıdaki sınırlamalar geçerlidir:

* Azure Cosmos hesapları ve sanal ağlar için özel bağlantı desteği yalnızca belirli bölgelerde kullanılabilir. Desteklenen bölgelerin listesi için özel bağlantı makalesinin [kullanılabilir bölgeler](../private-link/private-link-overview.md#availability) bölümüne bakın. 

  > [!NOTE]
  > Özel bir uç nokta oluşturmak için hem sanal ağın hem de Azure Cosmos hesabının desteklenen bölgelerde olduğundan emin olun.

* Doğrudan mod bağlantısı kullanarak bir Azure Cosmos hesabıyla özel bağlantı kullanırken, yalnızca TCP protokolünü kullanabilirsiniz. HTTP protokolü henüz desteklenmiyor.

* MongoDB hesapları için Azure Cosmos DB API 'sini kullanırken, yalnızca sunucu sürümü 3,6 (yani, uç `*.mongo.cosmos.azure.com`noktasını kullanan hesaplar) hesapları için özel bir uç nokta desteklenir. Özel bağlantı, sunucu sürümü 3,2 (yani `*.documents.azure.com`biçimde uç noktasını kullanan hesaplar) hesapları için desteklenmez. Özel bağlantıyı kullanmak için eski hesapları yeni sürüme geçirmeniz gerekir.

* Özel bağlantısı olan MongoDB hesapları için Azure Cosmos DB API 'sini kullanırken Robo 3T, Studio 3T ve Mongoose gibi araçları kullanamazsınız. Uç noktanın yalnızca `appName=<account name>` parametresi belirtilmişse özel bağlantı desteği olabilir. `replicaSet=globaldb&appName=mydbaccountname` bunun bir örneğidir. Bu araçlar, uygulama adını hizmete bağlantı dizesinde geçirmediği için özel bağlantıyı kullanamazsınız. Ancak, 3,6 sürümüyle SDK sürücülerini kullanarak bu hesaplara erişmeye devam edebilirsiniz.

* Özel bağlantı içeriyorsa sanal ağı taşıyamaz veya silemezsiniz.

* Bir Azure Cosmos hesabını özel bir uç noktaya eklenmişse silemezsiniz.

* Bir Azure Cosmos hesabını, hesaba bağlı olan tüm özel uç noktalara eşlenmemiş bir bölgeye devretmek.

* Otomatik olarak onaylanan özel uç noktalar oluşturmak için Azure Cosmos hesap kapsamında en az "*/PrivateEndpointConnectionsApproval" iznine sahip bir ağ yöneticisine verilmelidir.

### <a name="limitations-to-private-dns-zone-integration"></a>Özel DNS bölge tümleştirmesi sınırlamaları

Özel bir uç noktayı sildiğinizde veya Azure Cosmos hesabından bir bölge kaldırdığınızda, özel DNS bölgesindeki DNS kayıtları otomatik olarak kaldırılmaz. Önce DNS kayıtlarını el ile kaldırmanız gerekir:

* Bu özel DNS bölgesine bağlı yeni bir özel uç nokta ekleniyor.
* Özel uç noktaları bu özel DNS bölgesine bağlı olan herhangi bir veritabanı hesabına yeni bir bölge ekleme.

DNS kayıtlarını temizleyemezseniz, beklenmeyen veri düzlemi sorunları ortaya çıkabilir. Bu sorunlar, Özel uç nokta kaldırma veya bölge kaldırma işleminden sonra eklenen bölgelere yönelik veri kesintilerine dahildir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB güvenlik özellikleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* Azure Cosmos DB için bir güvenlik duvarı yapılandırmak için bkz. [güvenlik duvarı desteği](firewall-support.md).

* Azure Cosmos hesabınız için bir sanal ağ hizmeti uç noktası yapılandırma hakkında bilgi edinmek için bkz. [sanal ağlardan erişimi yapılandırma](how-to-configure-vnet-service-endpoint.md).

* Özel bağlantı hakkında daha fazla bilgi edinmek için bkz. [Azure özel bağlantı](../private-link/private-link-overview.md) belgeleri.
