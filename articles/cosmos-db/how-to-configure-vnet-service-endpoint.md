---
title: Azure Cosmos DB hesabınız için sanal ağ ve alt ağ tabanlı erişimi yapılandırın
description: Bu belgede Azure Cosmos DB için bir sanal ağ hizmeti uç noktası ayarlamak için gereken adımlar açıklanmaktadır.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: c399bed803145659bae1863e9e0b919f33254627
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820209"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Sanal ağlardan (VNet) erişimi yapılandırma

Bir Azure sanal ağının yalnızca belirli bir alt ağından erişime izin vermek için Azure Cosmos DB hesaplarını yapılandırabilirsiniz. Bir Azure Cosmos DB hesabına erişimi, bir sanal ağdaki bir alt ağdan bağlantılarla sınırlamak için:

1. Alt ağ ve sanal ağ kimliğini Azure Cosmos DB göndermek için alt ağı etkinleştirin. Bu, belirli bir alt ağdaki Azure Cosmos DB için bir hizmet uç noktası etkinleştirerek elde edebilirsiniz.

1. Alt ağı hesabın erişebileceği bir kaynak olarak belirtmek için Azure Cosmos DB hesaba bir kural ekleyin.

> [!NOTE]
> Bir alt ağda Azure Cosmos DB hesabınız için bir hizmet uç noktası etkinleştirildiğinde, bir genel IP 'den bir sanal ağa ve alt ağa Azure Cosmos DB anahtarlara ulaşan trafik kaynağıdır. Trafik geçişi, bu alt ağdan erişilen tüm Azure Cosmos DB hesapları için geçerlidir. Azure Cosmos DB hesaplarınızın bu alt ağa izin veren IP tabanlı bir güvenlik duvarı varsa, hizmet etkin alt ağdan gelen istekler artık IP güvenlik duvarı kurallarıyla eşleşmez ve reddedilir.
>
> Daha fazla bilgi edinmek için, bu makalenin [IP güvenlik duvarı kuralından bir sanal ağ erişim denetim listesine geçme](#migrate-from-firewall-to-vnet) bölümünde özetlenen adımlara bakın.

Aşağıdaki bölümlerde, bir Azure Cosmos DB hesabı için sanal ağ hizmeti uç noktasının nasıl yapılandırılacağı açıklanır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a id="configure-using-portal"></a>Azure portal kullanarak bir hizmet uç noktası yapılandırma

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Mevcut bir Azure sanal ağı ve alt ağı için bir hizmet uç noktası yapılandırın

1. **Tüm kaynaklar** dikey penceresinde, güvenli hale getirmek istediğiniz Azure Cosmos DB hesabını bulun.

1. Ayarlar menüsünden **güvenlik duvarları ve sanal ağlar** ' ı seçin ve **Seçilen ağlardan**erişime izin ver ' i seçin.

1. Var olan bir sanal ağın alt ağına erişim vermek için, **sanal ağlar**altında **var olan Azure sanal ağını Ekle**' yi seçin.

1. Azure sanal ağını eklemek istediğiniz **aboneliği** seçin. Azure Cosmos DB hesabınıza erişim sağlamak istediğiniz Azure **sanal ağlarını** ve **alt ağlarını** seçin. Sonra, "Microsoft. Azu, Smosdb" için hizmet uç noktalarına sahip seçili ağları etkinleştirmek üzere **Etkinleştir** ' i seçin. Tamamlandıktan sonra **Ekle**' yi seçin.

   ![Sanal ağ ve alt ağ seçin](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Azure Cosmos DB hesabı bir sanal ağdan erişim için etkinleştirildikten sonra, yalnızca bu seçili alt ağdan gelen trafiğe izin verir. Eklediğiniz sanal ağ ve alt ağ aşağıdaki ekran görüntüsünde gösterildiği gibi görünmelidir:

   ![Sanal ağ ve alt ağ başarıyla yapılandırıldı](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Sanal ağ hizmet uç noktalarını etkinleştirmek için aşağıdaki Abonelik izinlerine sahip olmanız gerekir:
>   * Sanal ağı olan abonelik: ağ katılımcısı
>   * Azure Cosmos DB hesabıyla abonelik: DocumentDB hesabı Katılımcısı
>   * Sanal ağınız ve Azure Cosmos DB hesabınız farklı aboneliklerdeyse, sanal ağa sahip aboneliğin da `Microsoft.DocumentDB` kaynak sağlayıcısı kayıtlı olduğundan emin olun. Bir kaynak sağlayıcısını kaydetmek için bkz. [Azure kaynak sağlayıcıları ve türleri](../azure-resource-manager/resource-manager-supported-services.md) makalesi.

Aboneliği kaynak sağlayıcısına kaydetme yönergeleri aşağıda verilmiştir.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Yeni bir Azure sanal ağı ve alt ağı için hizmet uç noktası yapılandırma

1. **Tüm kaynaklar** dikey penceresinde, güvenli hale getirmek istediğiniz Azure Cosmos DB hesabını bulun.  

1. Ayarlar menüsünden **güvenlik duvarları ve Azure sanal ağları** ' nı seçin ve **Seçilen ağlardan**erişime izin ver ' i seçin.  

1. Yeni bir Azure sanal ağına erişim vermek için, **sanal ağlar**altında **Yeni sanal ağ ekle**' yi seçin.  

1. Yeni bir sanal ağ oluşturmak için gereken ayrıntıları girip **Oluştur**' u seçin. Alt ağ "Microsoft. Azu, Smosdb" etkin bir hizmet uç noktası ile oluşturulur.

   ![Yeni bir sanal ağ için bir sanal ağ ve alt ağ seçin](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Azure Cosmos DB hesabınız Azure Bilişsel Arama gibi diğer Azure hizmetleri tarafından kullanılıyorsa veya Stream Analytics veya Power BI 'e erişildiğinde, **Genel Azure veri merkezlerinden gelen bağlantıları kabul et**' i seçerek erişime izin verebilirsiniz.

Portaldan Azure Cosmos DB ölçümlerine erişiminizin olduğundan emin olmak için **Azure Portal seçeneklerinden erişime Izin ver** ' i etkinleştirmeniz gerekir. Bu seçenekler hakkında daha fazla bilgi edinmek için bkz. [IP güvenlik duvarı yapılandırma](how-to-configure-firewall.md) makalesi. Erişimi etkinleştirdikten sonra ayarları kaydetmek için **Kaydet** ' i seçin.

## <a id="remove-vnet-or-subnet"></a>Sanal ağ veya alt ağ kaldırma

1. **Tüm kaynaklar** dikey penceresinde, hizmet uç noktalarını atadığınız Azure Cosmos DB hesabını bulun.  

2. Ayarlar menüsünden **güvenlik duvarları ve sanal ağlar ' ı** seçin.  

3. Bir sanal ağ veya alt ağ kuralını kaldırmak için, sanal ağ veya alt ağın yanındaki **...** öğesini seçin ve **Kaldır**' ı seçin.

   ![Sanal ağı kaldır](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4. Değişikliklerinizi uygulamak için **Kaydet** ' i seçin.

## <a id="configure-using-powershell"></a>Azure PowerShell kullanarak bir hizmet uç noktası yapılandırma

> [!NOTE]
> PowerShell veya Azure CLı kullanırken, yalnızca eklenmesi gereken tek değil, parametrelerde IP filtrelerinin ve sanal ağ ACL 'lerinin tam listesini belirttiğinizden emin olun.

Azure PowerShell kullanarak bir hizmet uç noktasını bir Azure Cosmos DB hesabına yapılandırmak için aşağıdaki adımları kullanın:  

1. [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) yükleyip [oturum açın](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Bir sanal ağın mevcut alt ağı için hizmet uç noktasını etkinleştirin.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
   ```

1. Sanal ağ bilgilerini al.

   ```powershell
   $vnProp = Get-AzVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Aşağıdaki cmdlet 'i çalıştırarak Azure Cosmos DB hesabının özelliklerini alın:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Daha sonra kullanmak üzere değişkenleri başlatın. Varolan hesap tanımındaki tüm değişkenleri ayarlayın.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Aşağıdaki cmdlet 'leri çalıştırarak Azure Cosmos DB hesabı özelliklerini yeni yapılandırmayla güncelleştirin: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Azure Cosmos DB hesabınızın, önceki adımda yapılandırdığınız sanal ağ hizmeti uç noktasıyla güncelleştirildiğinden emin olmak için aşağıdaki komutu çalıştırın:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Azure CLı kullanarak hizmet uç noktası yapılandırma

Azure Cosmos hesapları, alt ağ zaten yapılandırılmışsa, daha sonra oluşturulduklarında veya güncellendiklerinde hizmet uç noktaları için yapılandırılabilir. Hizmet uç noktaları, alt ağın henüz kendileri için yapılandırılmadığı ve sonra alt ağ daha sonra yapılandırıldığında çalışmaya başlayacağı Cosmos hesabında da etkinleştirilebilir. Bu esneklik, yapılandırmalarının birbirini birbirinden bağımsız hale getirmek için hem Cosmos hesabına hem de sanal ağ kaynaklarına erişimi olmayan yöneticilere izin verir.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Yeni bir Cosmos hesabı oluşturun ve yeni bir sanal ağ için bir arka uç alt ağına bağlayın

Bu örnekte, sanal ağ ve alt ağ, her ikisi de oluşturulduğunda etkinleştirilmiş hizmet uç noktaları ile oluşturulur.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Cosmos hesabını bağımsız olarak arka uç alt ağına bağlama ve yapılandırma

Bu örnek, bir Azure Cosmos hesabının alt ağın henüz hizmet uç noktaları için yapılandırılmadığı mevcut yeni bir sanal ağa nasıl bağlanacağını göstermek için tasarlanmıştır. Bu, `--ignore-missing-vnet-service-endpoint` parametresi kullanılarak yapılır. Bu, sanal ağın alt ağının yapılandırması tamamlanmadan önce Cosmos hesabının yapılandırmasının hatasız tamamlanmasını sağlar. Alt ağ yapılandırması tamamlandıktan sonra, Cosmos hesabına, yapılandırılmış alt ağ üzerinden erişilebilecektir.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a id="migrate-from-firewall-to-vnet"></a>IP güvenlik duvarı kuralından bir sanal ağ ACL 'sine geçiş

Aşağıdaki adımları yalnızca bir alt ağa izin veren mevcut IP güvenlik duvarı kurallarına sahip Azure Cosmos DB hesapları için, bir IP güvenlik duvarı kuralı yerine sanal ağ ve alt ağ tabanlı ACL 'Ler kullanmak istediğinizde kullanın.

Bir alt ağ için Azure Cosmos DB hesabı için bir hizmet uç noktası açıldıktan sonra, istekler, bir genel IP yerine sanal ağ ve alt ağ bilgilerini içeren bir kaynakla gönderilir. Bu istekler bir IP filtresiyle eşleşmez. Bu kaynak anahtarı, bir hizmet uç noktası etkin olan alt ağdan erişilen tüm Azure Cosmos DB hesaplara yapılır. Kapalı kalma süresini engellemek için aşağıdaki adımları kullanın:

1. Aşağıdaki cmdlet 'i çalıştırarak Azure Cosmos DB hesabının özelliklerini alın:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Daha sonra kullanmak için değişkenleri başlatın. Varolan hesap tanımındaki tüm değişkenleri ayarlayın. Sanal ağ ACL 'sini alt ağdan erişilen tüm Azure Cosmos DB hesaplara `ignoreMissingVNetServiceEndpoint` bayrağıyla ekleyin.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Aşağıdaki cmdlet 'leri çalıştırarak Azure Cosmos DB hesabı özelliklerini yeni yapılandırmayla güncelleştirin:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Alt ağdan erişebileceğiniz tüm Azure Cosmos DB hesapları için 1-3 arasındaki adımları yineleyin.

1.  15 dakika bekleyin ve ardından hizmet uç noktasını etkinleştirmek için alt ağı güncelleştirin.

1.  Bir sanal ağın mevcut alt ağı için hizmet uç noktasını etkinleştirin.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
    ```

1. Alt ağ için IP güvenlik duvarı kuralını kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Cosmos DB için bir güvenlik duvarı yapılandırmak için, [güvenlik duvarı destek](firewall-support.md) makalesine bakın.
