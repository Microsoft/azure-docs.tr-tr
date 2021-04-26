---
title: Azure Event Grid konuları veya etki alanları için özel uç noktaları yapılandırma
description: Bu makalede, Azure Event Grid konular veya etki alanı için özel uç noktaların nasıl yapılandırılacağı açıklanır.
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 85546e99a8c431dc75b1af3d5044e06a18cf226d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770516"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Azure Event Grid konuları veya etki alanları için özel uç noktaları yapılandırma
[Özel uç noktaları](../private-link/private-endpoint-overview.md) kullanarak doğrudan sanal ağınızdan, genel İnternet üzerinden geçmeden [özel bir bağlantı](../private-link/private-link-overview.md) üzerinden konulara ve etki alanlarına olay girişi sağlayabilirsiniz. Özel uç nokta, konu veya etki alanınız için VNet adres alanından bir IP adresi kullanır. Daha fazla kavramsal bilgi için bkz. [ağ güvenliği](network-security.md).

Bu makalede, konular veya etki alanları için özel uç noktaların nasıl yapılandırılacağı açıklanır.

## <a name="use-azure-portal"></a>Azure portalı kullanma 
Bu bölümde, bir konu veya etki alanı için özel bir uç nokta oluşturmak üzere Azure portal nasıl kullanılacağı gösterilmektedir.

> [!NOTE]
> Bu bölümde gösterilen adımlar genellikle konular içindir. **Etki alanları** için özel uç noktalar oluşturmak üzere benzer adımları kullanabilirsiniz. 

1. [Azure Portal](https://portal.azure.com) oturum açın ve konu veya etki alanına gidin.
2. Konu sayfanızın **ağ** sekmesine geçin. Araç çubuğunda **+ Özel uç nokta** ' ı seçin.

    ![Özel uç nokta Ekle](./media/configure-private-endpoints/add-button.png)
2. **Temel bilgiler** sayfasında, aşağıdaki adımları izleyin: 
    1. Özel uç noktayı oluşturmak istediğiniz **Azure aboneliğini** seçin. 
    2. Özel uç nokta için bir **Azure Kaynak grubu** seçin. 
    3. Uç nokta için bir **ad** girin. 
    4. Uç nokta için **bölgeyi** seçin. Özel uç noktanız, sanal ağınızla aynı bölgede olmalıdır, ancak özel bağlantı kaynağından farklı bir bölgede (Bu örnekte bir olay Kılavuzu konusu) olabilir. 
    5. Ardından sayfanın alt kısmındaki **İleri: kaynak >** düğmesini seçin. 

      ![Özel uç nokta-temel bilgiler sayfası](./media/configure-private-endpoints/basics-page.png)
3. **Kaynak** sayfasında, aşağıdaki adımları izleyin: 
    1. Bağlantı yöntemi için **dizinimde bir Azure kaynağına bağlan**' ı seçerseniz, aşağıdaki adımları izleyin. Bu örnek, dizininizde bir Azure kaynağına nasıl bağlanılacağını gösterir. 
        1. **Konunun/etki alanının** bulunduğu **Azure aboneliğini** seçin. 
        1. **Kaynak türü** Için, **kaynak türü** Için **Microsoft. Eventgrid/konular** veya **Microsoft. eventgrid/Domains** ' i seçin.
        2. **Kaynak** için, açılan listeden bir konu/etki alanı seçin. 
        3. **Hedef alt kaynağın** **Konu** veya **etki alanı** olarak ayarlandığını onaylayın (seçtiğiniz kaynak türüne göre).    
        4. Sayfanın alt kısmındaki **İleri: yapılandırma >** düğmesini seçin. 

            !["Özel uç nokta kaynağı oluşturma" sayfasını gösteren ekran görüntüsü.](./media/configure-private-endpoints/resource-page.png)
    2. Kaynak **kimliği veya diğer ad kullanarak bir kaynağa Bağlan**' ı seçerseniz, aşağıdaki adımları izleyin:
        1. Kaynağın KIMLIĞINI girin. Örneğin: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. **Kaynak** için **konu başlığını** veya **etki alanını** girin. 
        3. seçim İstek iletisi ekleyin. 
        4. Sayfanın alt kısmındaki **İleri: yapılandırma >** düğmesini seçin. 

            ![Özel uç nokta-kaynak sayfası](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. **Yapılandırma** sayfasında, bir sanal ağda özel uç noktayı dağıtmak istediğiniz alt ağı seçersiniz. 
    1. Bir **sanal ağ** seçin. Yalnızca şu anda seçili olan abonelikte ve konumda bulunan sanal ağlar açılan listede listelenir. 
    2. Seçtiğiniz sanal ağda bir **alt ağ** seçin. 
    3. Sayfanın alt kısmındaki **İleri: etiketler >** düğmesini seçin. 

    ![Özel uç nokta-yapılandırma sayfası](./media/configure-private-endpoints/configuration-page.png)
5. **Etiketler** sayfasında, Özel uç nokta kaynağıyla ilişkilendirmek istediğiniz etiketleri (adlar ve değerler) oluşturun. Ardından sayfanın alt kısmındaki **gözden geçir + oluştur** düğmesini seçin. 
6. **Gözden geçir + oluştur**' da, tüm ayarları gözden geçirin ve **Oluştur** ' u seçerek özel uç noktasını oluşturun. 

    ![Özel uç nokta-& sayfa oluşturmayı İncele](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Özel bağlantı bağlantısını Yönet

Özel bir uç nokta oluşturduğunuzda bağlantının onaylanması gerekir. Özel bir uç noktası oluşturmakta olduğunuz kaynak dizininizdeki ise, yeterli izinlere sahip olduğunuz belirtilen bağlantı isteğini onaylayabilirsiniz. Başka bir dizindeki bir Azure kaynağına bağlanıyorsanız, bu kaynağın sahibinin bağlantı isteğinizi onaylamasını beklemeniz gerekir.

Dört sağlama durumu vardır:

| Hizmet eylemi | Hizmet tüketicisi özel uç nokta durumu | Açıklama |
|--|--|--|
| Hiçbiri | Beklemede | Bağlantı el ile oluşturulur ve özel bağlantı kaynağı sahibinden onay bekliyor. |
| Onaylama | Onaylandı | Bağlantı otomatik olarak veya el ile onaylandı ve kullanılabilir hale gelmiştir. |
| Reddet | Reddedildi | Bağlantı, özel bağlantı kaynağı sahibi tarafından reddedildi. |
| Kaldır | Bağlantı kesildi | Bağlantı, özel bağlantı kaynağı sahibi tarafından kaldırıldı, Özel uç nokta bilgilendirici hale gelir ve temizlik için silinmelidir. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Özel bir uç nokta bağlantısını yönetme
Aşağıdaki bölümlerde, özel bir uç nokta bağlantısını onaylama veya reddetme işlemlerinin nasıl yapılacağı gösterilmektedir. 

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Arama çubuğuna **Event Grid konuları** ' nı veya **Event Grid etki alanlarını** yazın.
1. Yönetmek istediğiniz **konuyu** veya **etki alanını** seçin.
1. **Ağ** sekmesini seçin.
1. Bekleyen herhangi bir bağlantı varsa, sağlama durumunda **bekliyor** ile listelenmiş bir bağlantı görürsünüz. 

### <a name="to-approve-a-private-endpoint"></a>Özel bir uç noktayı onaylamak için
Bekleyen durumunda olan özel bir uç noktayı onaylayabilirsiniz. Onaylamak için aşağıdaki adımları izleyin: 

> [!NOTE]
> Bu bölümde gösterilen adımlar genellikle konular içindir. **Etki alanlarının** özel uç noktalarını onaylamak için de benzer adımları kullanabilirsiniz. 

1. Onaylamak istediğiniz **Özel uç noktayı** seçin ve araç çubuğunda **Onayla** ' yı seçin.

    ![Özel uç nokta-bekleme durumu](./media/configure-private-endpoints/pending.png)
1. **Bağlantıyı Onayla** iletişim kutusunda bir açıklama girin (isteğe bağlı) ve **Evet**' i seçin. 

    ![Özel uç nokta-Onayla](./media/configure-private-endpoints/approve.png)
1. Uç noktanın durumunu **Onaylandı** olarak görtığınızdan emin olun. 

    ![Özel uç nokta-onaylanan durum](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Özel bir uç noktayı reddetmek için
Bekleyen durumunda veya onaylanan durumunda olan özel bir uç noktayı reddedebilirsiniz. Reddetmek için şu adımları izleyin: 

> [!NOTE]
> Bu bölümde gösterilen adımlar konular içindir. **Etki alanları** için özel uç noktaları reddetmek üzere benzer adımları kullanabilirsiniz. 

1. Reddetmek istediğiniz **Özel uç noktayı** seçin ve araç çubuğunda **Reddet** ' i seçin.

    !["Ağ-özel uç nokta bağlantıları" nı "Reddet" seçiliyken gösteren ekran görüntüsü.](./media/configure-private-endpoints/reject-button.png)
1. **Bağlantıyı Reddet** iletişim kutusunda bir açıklama girin (isteğe bağlı) ve **Evet**' i seçin. 

    ![Özel uç nokta-Reddet](./media/configure-private-endpoints/reject.png)
1. Uç noktanın durumunu **reddedildi** olarak görtiğinizi onaylayın. 

    ![Özel uç nokta-reddedildi durumu](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Bir özel uç noktayı, Azure portal reddedildiğinde onaylayamazsınız. 


## <a name="use-azure-cli"></a>Azure CLI kullanma
Özel bir uç nokta oluşturmak için, aşağıdaki örnekte gösterildiği gibi [az Network Private-ENDPOINT Create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) metodunu kullanın:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Örnekte kullanılan parametrelerin açıklamaları için, [az Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create)için belgeler bölümüne bakın. Bu örnekte birkaç noktaya göz önünde bulunmaktadır: 

- İçin `private-connection-resource-id` , **konunun** veya **etki alanının** kaynak kimliğini belirtin. Yukarıdaki örnek, türü: konusunu kullanır.
- için `group-ids` , `topic` veya belirtin `domain` . Yukarıdaki örnekte `topic` kullanılır. 

Özel bir uç noktayı silmek için, aşağıdaki örnekte gösterildiği gibi [az Network Private-Endpoint Delete](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) metodunu kullanın:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Bu bölümde gösterilen adımlar konular içindir. **Etki alanları** için özel uç noktalar oluşturmak üzere benzer adımları kullanabilirsiniz. 



### <a name="prerequisites"></a>Önkoşullar
Aşağıdaki komutu çalıştırarak CLı için Azure Event Grid uzantısını güncelleştirin: 

```azurecli-interactive
az extension update -n eventgrid
```

Uzantı yüklü değilse, yüklemek için aşağıdaki komutu çalıştırın: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Özel uç nokta oluşturma
Özel bir uç nokta oluşturmak için, aşağıdaki örnekte gösterildiği gibi [az Network Private-ENDPOINT Create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) metodunu kullanın:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Örnekte kullanılan parametrelerin açıklamaları için, [az Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create)için belgeler bölümüne bakın. Bu örnekte birkaç noktaya göz önünde bulunmaktadır: 

- İçin `private-connection-resource-id` , **konunun** veya **etki alanının** kaynak kimliğini belirtin. Yukarıdaki örnek, türü: konusunu kullanır.
- için `group-ids` , `topic` veya belirtin `domain` . Yukarıdaki örnekte `topic` kullanılır. 

Özel bir uç noktayı silmek için, aşağıdaki örnekte gösterildiği gibi [az Network Private-Endpoint Delete](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) metodunu kullanın:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Bu bölümde gösterilen adımlar konular içindir. **Etki alanları** için özel uç noktalar oluşturmak üzere benzer adımları kullanabilirsiniz. 

#### <a name="sample-script"></a>Örnek betik
Aşağıdaki Azure kaynaklarını oluşturan örnek bir betik aşağıda verilmiştir:

- Kaynak grubu
- Sanal ağ
- Sanal ağdaki alt ağ
- Azure Event Grid konusu
- Konu için özel uç nokta

> [!NOTE]
> Bu bölümde gösterilen adımlar konular içindir. Etki alanları için özel uç noktalar oluşturmak üzere benzer adımları kullanabilirsiniz.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>Özel uç noktayı onaylama
Aşağıdaki örnek CLı kod parçacığı, özel bir uç nokta bağlantısının nasıl onaylanacağını göstermektedir. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Özel uç noktayı reddetme
Aşağıdaki örnek CLı kod parçacığı, özel bir uç nokta bağlantısının nasıl reddedileceği gösterilmektedir. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Ortak ağ erişimini devre dışı bırak
Varsayılan olarak, genel ağ erişimi bir Event Grid konusu veya etki alanı için etkinleştirilmiştir. Yalnızca özel uç noktalar aracılığıyla erişime izin vermek için aşağıdaki komutu çalıştırarak ortak ağ erişimini devre dışı bırakın:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>PowerShell kullanma
Bu bölüm, PowerShell kullanarak bir konu veya etki alanı için özel bir uç nokta oluşturmayı gösterir. 

### <a name="prerequisite"></a>Önkoşul
[Nasıl yapılır: Azure AD uygulaması ve hizmet sorumlusu oluşturmak için,](../active-directory/develop/howto-create-service-principal-portal.md) bir Azure Active Directory uygulaması oluşturmak ve **Dizin (kiracı) kimliği**, **uygulama (istemci) kimliği** ve **uygulama (istemci) gizliliğine** ilişkin değerleri aramak için, nasıl yapılır: portalı kullanma konusundaki yönergeleri izleyin. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>REST API çağrıları için belirteci ve üstbilgileri hazırlama 
REST API çağrılar ve yetkilendirme ve diğer üst bilgi bilgileriyle kullanılacak bir kimlik doğrulama belirteci almak için aşağıdaki önkoşul komutlarını çalıştırın. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Uç nokta ağ ilkeleri devre dışı olan bir alt ağ oluşturma

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Özel bir uç nokta ile bir olay Kılavuzu konusu oluşturma

> [!NOTE]
> Bu bölümde gösterilen adımlar konular içindir. **Etki alanları** için özel uç noktalar oluşturmak üzere benzer adımları kullanabilirsiniz. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Uç noktanın oluşturulduğunu doğruladıktan sonra, aşağıdakine benzer bir sonuç görmeniz gerekir:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Özel bir uç nokta bağlantısını onaylama
Aşağıdaki örnek PowerShell kod parçacığında özel bir uç noktanın nasıl onaylanacağı gösterilmektedir. 

> [!NOTE]
> Bu bölümde gösterilen adımlar konular içindir. **Etki alanlarının** özel uç noktalarını onaylamak için de benzer adımları kullanabilirsiniz. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Özel bir uç nokta bağlantısını reddetme
Aşağıdaki örnek, PowerShell kullanarak özel bir uç noktanın nasıl reddedileceği gösterilmektedir. Önceki GET komutunun sonucundan özel uç nokta için GUID 'ı alabilirsiniz. 

> [!NOTE]
> Bu bölümde gösterilen adımlar konular içindir. **Etki alanları** için özel uç noktaları reddetmek üzere benzer adımları kullanabilirsiniz. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

Bağlantıyı, API aracılığıyla reddedildikten sonra da onaylayabilirsiniz. Azure portal kullanıyorsanız, reddedilen bir uç noktayı onaylayamazsınız. 

## <a name="next-steps"></a>Sonraki adımlar
* IP Güvenlik Duvarı ayarlarının nasıl yapılandırılacağı hakkında bilgi edinmek için bkz. [Azure Event Grid konular veya etki alanları IÇIN IP güvenlik duvarını yapılandırma](configure-firewall.md).
* Ağ bağlantısı sorunlarını gidermek için bkz. [ağ bağlantısı sorunlarını giderme](troubleshoot-network-connectivity.md)
