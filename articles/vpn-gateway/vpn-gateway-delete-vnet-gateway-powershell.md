---
title: 'Azure VPN Gateway: ağ geçidini silme: PowerShell'
description: Kaynak Yöneticisi dağıtım modelinde PowerShell kullanarak bir sanal ağ geçidini silin.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: 2c02b656f8d7879115d25516bf49f49d9921a290
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146319"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>PowerShell kullanarak bir sanal ağ geçidini silme
> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasik)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

VPN ağ geçidi yapılandırması için bir sanal ağ geçidini silmek istediğinizde uygulayabileceğiniz birkaç farklı yaklaşım vardır.

- Her şeyi silmek ve baştan başlamak istiyorsanız, bir test ortamı durumunda olduğu gibi, kaynak grubunu silebilirsiniz. Bir kaynak grubunu sildiğinizde, Grup içindeki tüm kaynakları siler. Bu yöntem yalnızca kaynak grubundaki kaynakları tutmak istemiyorsanız önerilir. Bu yaklaşımı kullanarak yalnızca birkaç kaynağı seçmeli olarak silemezsiniz.

- Kaynak grubunuzdaki bazı kaynakları korumak istiyorsanız, bir sanal ağ geçidini silmek biraz daha karmaşık hale gelir. Sanal ağ geçidini silebilmeniz için önce ağ geçidine bağlı olan tüm kaynakları silmeniz gerekir. İzleyeceğiniz adımlar, oluşturduğunuz bağlantı türüne ve her bağlantı için bağımlı kaynaklara bağlıdır.

## <a name="before-beginning"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. en son Azure Resource Manager PowerShell cmdlet 'lerini indirin.

Azure Resource Manager PowerShell cmdlet 'lerinin en son sürümünü indirip yükleyin. PowerShell cmdlet 'lerini indirme ve yükleme hakkında daha fazla bilgi için bkz. [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Azure hesabınıza bağlanın.

PowerShell konsolunuzu açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:

```powershell
Connect-AzAccount
```

Hesapla ilişkili abonelikleri kontrol edin.

```powershell
Get-AzSubscription
```

Birden fazla aboneliğiniz varsa, kullanmak istediğiniz aboneliği belirtin.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>Siteden siteye VPN ağ geçidini silme

Bir S2S yapılandırmasına yönelik bir sanal ağ geçidini silmek için, önce sanal ağ geçidi ile ilgili olan her kaynağı silmeniz gerekir. Bağımlılıklar nedeniyle kaynakların belirli bir sırada silinmesi gerekir. Aşağıdaki örneklerle çalışırken bazı değerler belirtilmelidir, diğer değerler de çıkış sonucudur. Örnek gösterim amacıyla örneklerde aşağıdaki belirli değerleri kullanıyoruz:

VNet adı: VNet1<br>
Kaynak grubu adı: RG1<br>
Sanal ağ geçidi adı: GW1<br>

Aşağıdaki adımlar Kaynak Yöneticisi dağıtım modeli için geçerlidir.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. silmek istediğiniz sanal ağ geçidini alın.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. sanal ağ geçidinin herhangi bir bağlantıya sahip olup olmadığını denetleyin.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. tüm bağlantıları silin.

Bağlantıların her birini silme işlemini onaylamanız istenebilir.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. sanal ağ geçidini silin.

Ağ geçidini silme işlemini onaylamanız istenebilir. S2S yapılandırmanıza ek olarak bu VNet 'e yönelik bir P2S yapılandırmasına sahipseniz, sanal ağ geçidi silindiğinde tüm P2S istemcilerinin uyarı vermeden otomatik olarak bağlantısı kesilir.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Bu noktada, sanal ağ geçidiniz silindi. Artık kullanılmayan kaynakları silmek için sonraki adımları kullanabilirsiniz.

### <a name="5-delete-the-local-network-gateways"></a>5 yerel ağ geçitlerini silin.

Karşılık gelen yerel ağ geçitlerinin listesini alın.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Yerel ağ geçitlerini silin. Yerel ağ geçidinin her birini silme işlemini onaylamanız istenebilir.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. genel IP adresi kaynaklarını silin.

Sanal ağ geçidinin IP yapılandırmasını alın.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Bu sanal ağ geçidi için kullanılan genel IP adresi kaynaklarının listesini alın. Sanal ağ geçidi etkin-etkin ise, iki genel IP adresi görürsünüz.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Genel IP kaynaklarını silin.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. ağ geçidi alt ağını silin ve yapılandırmayı ayarlayın.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>VNet 'ten VNet 'e VPN ağ geçidini silme

Bir V2V yapılandırmasına yönelik bir sanal ağ geçidini silmek için, önce sanal ağ geçidi ile ilgili olan her kaynağı silmelisiniz. Bağımlılıklar nedeniyle kaynakların belirli bir sırada silinmesi gerekir. Aşağıdaki örneklerle çalışırken bazı değerler belirtilmelidir, diğer değerler de çıkış sonucudur. Örnek gösterim amacıyla örneklerde aşağıdaki belirli değerleri kullanıyoruz:

VNet adı: VNet1<br>
Kaynak grubu adı: RG1<br>
Sanal ağ geçidi adı: GW1<br>

Aşağıdaki adımlar Kaynak Yöneticisi dağıtım modeli için geçerlidir.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. silmek istediğiniz sanal ağ geçidini alın.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. sanal ağ geçidinin herhangi bir bağlantıya sahip olup olmadığını denetleyin.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Farklı bir kaynak grubunun parçası olan sanal ağ geçidine yönelik başka bağlantılar olabilir. Her ek kaynak grubundaki diğer bağlantıları denetleyin. Bu örnekte, RG2 adresinden bağlantıları denetliyoruz. Bunu, sanal ağ geçidine bağlantısı olabilecek her kaynak grubu için çalıştırın.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. bağlantı listesini her iki yönde de alın.

Bu, VNet 'ten VNet 'e yönelik bir yapılandırma olduğundan her iki yönde de bağlantı listesine ihtiyacınız vardır.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Bu örnekte, RG2 adresinden bağlantıları denetliyoruz. Bunu, sanal ağ geçidine bağlantısı olabilecek her kaynak grubu için çalıştırın.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. tüm bağlantıları silin.

Bağlantıların her birini silme işlemini onaylamanız istenebilir.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. sanal ağ geçidini silin.

Sanal ağ geçidini silme işlemini onaylamanız istenebilir. V2V yapılandırmanıza ek olarak sanal ağlarınız için P2S yapılandırmaları varsa, sanal ağ geçitlerinin silinmesi uyarı vermeden tüm P2S istemcilerinin bağlantısını otomatik olarak keser.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Bu noktada, sanal ağ geçidiniz silindi. Artık kullanılmayan kaynakları silmek için sonraki adımları kullanabilirsiniz.

### <a name="6-delete-the-public-ip-address-resources"></a>6. genel IP adresi kaynaklarını silme

Sanal ağ geçidinin IP yapılandırmasını alın.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Bu sanal ağ geçidi için kullanılan genel IP adresi kaynaklarının listesini alın. Sanal ağ geçidi etkin-etkin ise, iki genel IP adresi görürsünüz.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Genel IP kaynaklarını silin. Genel IP 'nin silinmesini onaylamanız istenebilir.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. ağ geçidi alt ağını silin ve yapılandırmayı ayarlayın.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>Noktadan siteye VPN ağ geçidini silme

Bir P2S yapılandırmasına yönelik bir sanal ağ geçidini silmek için, önce sanal ağ geçidi ile ilgili olan her kaynağı silmelisiniz. Bağımlılıklar nedeniyle kaynakların belirli bir sırada silinmesi gerekir. Aşağıdaki örneklerle çalışırken bazı değerler belirtilmelidir, diğer değerler de çıkış sonucudur. Örnek gösterim amacıyla örneklerde aşağıdaki belirli değerleri kullanıyoruz:

VNet adı: VNet1<br>
Kaynak grubu adı: RG1<br>
Sanal ağ geçidi adı: GW1<br>

Aşağıdaki adımlar Kaynak Yöneticisi dağıtım modeli için geçerlidir.


>[!NOTE]
> VPN ağ geçidini sildiğinizde, tüm bağlı istemcilerin hiçbir uyarı vermeden VNet bağlantısı kesilir.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. silmek istediğiniz sanal ağ geçidini alın.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. sanal ağ geçidini silin.

Sanal ağ geçidini silme işlemini onaylamanız istenebilir.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Bu noktada, sanal ağ geçidiniz silindi. Artık kullanılmayan kaynakları silmek için sonraki adımları kullanabilirsiniz.

### <a name="3-delete-the-public-ip-address-resources"></a>3. genel IP adresi kaynaklarını silme

Sanal ağ geçidinin IP yapılandırmasını alın.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Bu sanal ağ geçidi için kullanılan genel IP adreslerinin listesini alın. Sanal ağ geçidi etkin-etkin ise, iki genel IP adresi görürsünüz.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Genel IP 'Leri silin. Genel IP 'nin silinmesini onaylamanız istenebilir.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. ağ geçidi alt ağını silin ve yapılandırmayı ayarlayın.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>Kaynak grubunu silerek VPN ağ geçidini silme

Kaynak grubunda kaynaklarınızdan herhangi birini tutmanın endişeniz yoksa ve yalnızca baştan başlamak istiyorsanız, bir kaynak grubunun tamamını silebilirsiniz. Bu, her şeyi kaldırmanın hızlı bir yoludur. Aşağıdaki adımlar yalnızca Kaynak Yöneticisi dağıtım modeli için geçerlidir.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. aboneliğinizdeki tüm kaynak gruplarının bir listesini alın.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. silmek istediğiniz kaynak grubunu bulun.

Silmek istediğiniz kaynak grubunu bulun ve bu kaynak grubundaki kaynakların listesini görüntüleyin. Örnekte, kaynak grubunun adı RG1 ' dir. Tüm kaynakların bir listesini almak için örneği değiştirin.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. listedeki kaynakları doğrulayın.

Liste döndürüldüğünde, kaynak grubundaki tüm kaynakları ve kaynak grubunun kendisini silmek istediğinizi doğrulamak için gözden geçirin. Kaynak grubundaki bazı kaynakları tutmak istiyorsanız, bu makalenin önceki bölümlerindeki adımları kullanarak ağ geçidinizi silin.

### <a name="4-delete-the-resource-group-and-resources"></a>4. kaynak grubunu ve kaynakları silin.

Kaynak grubunu ve kaynak grubunda yer alan tüm kaynakları silmek için, örneği değiştirin ve çalıştırın.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. durumu denetleyin.

Azure 'un tüm kaynakları silmesi biraz zaman alır. Bu cmdlet 'i kullanarak kaynak grubunuzun durumunu kontrol edebilirsiniz.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

Döndürülen sonuç ' başarılı ' olarak gösterilir.

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
