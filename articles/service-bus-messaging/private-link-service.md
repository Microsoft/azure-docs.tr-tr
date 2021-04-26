---
title: Azure Service Bus Azure özel bağlantı hizmeti ile tümleştirme
description: Azure Service Bus Azure özel bağlantı hizmeti ile tümleştirme hakkında bilgi edinin
author: spelluru
ms.author: spelluru
ms.date: 03/29/2021
ms.topic: article
ms.openlocfilehash: 833d7e9fb4d517b71aab5039ae9081407eed84cd
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960546"
---
# <a name="allow-access-to-azure-service-bus-namespaces-via-private-endpoints"></a>Özel uç noktalar aracılığıyla Azure Service Bus ad alanlarına erişime izin ver
Azure özel bağlantı hizmeti, Azure hizmetlerine (örneğin, Azure Service Bus, Azure depolama ve Azure Cosmos DB) ve Azure 'da barındırılan müşteri/iş ortağı hizmetlerine sanal ağınızdaki **özel bir uç nokta** üzerinden erişmenizi sağlar.

Özel uç nokta, Azure özel bağlantısı tarafından desteklenen bir hizmete özel ve güvenli bir şekilde bağlanan bir ağ arabirimidir. Özel uç nokta, sanal ağınızdan bir özel IP adresi kullanarak hizmeti sanal ağınıza etkin bir şekilde getiriyor. Hizmete giden tüm trafik özel uç nokta aracılığıyla yönlendirilebilir, bu nedenle ağ geçitleri, NAT cihazları, ExpressRoute veya VPN bağlantıları ya da genel IP adresleri gerekmez. Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağı üzerinden geçer ve genel İnternet’ten etkilenme olasılığı ortadan kaldırılır. Bir Azure kaynağı örneğine bağlanarak, erişim denetimi için en yüksek düzeyde ayrıntı düzeyi sağlayabilirsiniz.

Daha fazla bilgi için bkz. [Azure özel bağlantısı nedir?](../private-link/private-link-overview.md)

## <a name="important-points"></a>Önemli nokta
- Bu özellik, Azure Service Bus **Premium** katmanıyla desteklenir. Premium katmanı hakkında daha fazla bilgi için, [Service Bus Premium ve standart mesajlaşma katmanları](service-bus-premium-messaging.md) makalesine bakın.
- Özel uç noktaları uygulamak, diğer Azure hizmetlerinin Service Bus etkileşimde olmasını engelleyebilir. Özel uç noktalar etkinken bile belirli **Güvenilen hizmetlerden** Service Bus kaynaklara erişime izin verebilirsiniz. Güvenilen hizmetler listesi için bkz. [Güvenilen hizmetler](#trusted-microsoft-services).

    Aşağıdaki Microsoft hizmetlerinin bir sanal ağda olması gerekir
    - Azure App Service
    - Azure İşlevleri
- Yalnızca belirtilen IP adreslerinden veya bir sanal ağın alt ağından gelen trafiğe izin vermek için ad alanı için **en az BIR IP kuralı veya sanal ağ kuralı** belirtin. IP ve sanal ağ kuralları yoksa, ad alanına genel İnternet üzerinden erişilebilir (erişim anahtarı kullanılarak). 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Azure portal kullanarak özel uç nokta ekleme

### <a name="prerequisites"></a>Önkoşullar

Bir Service Bus ad alanını Azure özel bağlantısıyla bütünleştirmek için aşağıdaki varlıklara veya izinlere ihtiyacınız olacaktır:

- Bir Service Bus ad alanı.
- Bir Azure sanal ağı.
- Sanal ağdaki bir alt ağ. **Varsayılan** alt ağı kullanabilirsiniz. 
- Hem Service Bus ad alanı hem de sanal ağ için sahip veya katkıda bulunan izinleri.

Özel uç noktanız ve sanal ağınız aynı bölgede olmalıdır. Portalı kullanarak özel uç nokta için bir bölge seçtiğinizde bu, yalnızca o bölgedeki sanal ağları otomatik olarak filtreleyecek. Service Bus ad alanınız farklı bir bölgede olabilir. Ve özel uç noktanız sanal ağınızda özel bir IP adresi kullanıyor.

### <a name="steps"></a>adımlar

Zaten mevcut bir ad alanınız varsa, aşağıdaki adımları izleyerek özel bir uç nokta oluşturabilirsiniz:

1. [Azure portalında](https://portal.azure.com) oturum açın. 
2. Arama çubuğuna **Service Bus** yazın.
3. Listeden özel uç nokta eklemek istediğiniz **ad alanını** seçin.
2. Sol taraftaki menüde, **Ayarlar** altında **ağ** seçeneği ' ni seçin.     Varsayılan olarak, **Seçili ağlar** seçeneği seçilidir.
 
    > [!NOTE]
    > **Ağ** sekmesini yalnızca **Premium** ad alanları için görürsünüz.  
   
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Ağ sayfası-varsayılan" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::

    > [!WARNING]
    > Bu sayfada en az bir IP güvenlik duvarı kuralı veya bir sanal ağ eklememeniz durumunda, ad alanına genel İnternet üzerinden erişilebilir (erişim anahtarı kullanılarak).
   
    **Tüm ağlar** seçeneğini belirlerseniz, Service Bus ad alanınız HERHANGI bir IP adresinden gelen bağlantıları kabul eder (erişim anahtarı kullanılarak). Bu varsayılan ayar 0.0.0.0/0 IP adres aralığını kabul eden kuralla eşdeğerdir. 

    ![Güvenlik Duvarı-tüm ağlar seçeneği seçildi](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
5. Özel uç noktalar aracılığıyla ad alanına erişime izin vermek için sayfanın üst kısmındaki **Özel uç nokta bağlantıları** sekmesini seçin
6. Sayfanın üst kısmındaki **+ Özel uç nokta** düğmesini seçin.

    ![Özel uç nokta Ekle düğmesi](./media/private-link-service/private-link-service-3.png)
7. **Temel bilgiler** sayfasında, aşağıdaki adımları izleyin: 
    1. Özel uç noktasını oluşturmak istediğiniz **Azure aboneliğini** seçin. 
    2. Özel uç nokta kaynağı için **kaynak grubunu** seçin.
    3. Özel uç nokta için bir **ad** girin. 
    5. Özel uç nokta için bir **bölge** seçin. Özel uç noktanız, sanal ağınızla aynı bölgede olmalıdır, ancak bağlanmakta olduğunuz özel bağlantı kaynağından farklı bir bölgede olabilir. 
    6. Sayfanın alt kısmındaki **İleri: kaynak >** düğmesini seçin.

        ![Özel uç nokta oluşturma-temel bilgiler sayfası](./media/private-link-service/create-private-endpoint-basics-page.png)
8. **Kaynak** sayfasında, aşağıdaki adımları izleyin:
    1. Bağlantı yöntemi için **dizinimde bir Azure kaynağına bağlan**' ı seçerseniz şu adımları izleyin:   
        1. **Service Bus ad alanının** bulunduğu **Azure aboneliğini** seçin. 
        2. **Kaynak türü** Için, **kaynak türü** Için **Microsoft. ServiceBus/namespaces** ' i seçin.
        3. **Kaynak** için, açılan listeden bir Service Bus ad alanı seçin. 
        4. **Hedef alt kaynağın** **ad alanı** olarak ayarlandığını onaylayın.
        5. Sayfanın alt kısmındaki **İleri: yapılandırma >** düğmesini seçin. 
        
            ![Özel uç nokta oluşturma-kaynak sayfası](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. **Kaynak kimliği veya diğer ad ile bir Azure kaynağına bağlan**' ı seçerseniz, aşağıdaki adımları izleyin:
        1. **Kaynak kimliğini** veya **diğer adı** girin. Bu, birinin sizinle paylaştığı kaynak KIMLIĞI veya diğer ad olabilir. Kaynak KIMLIĞINI almanın en kolay yolu, Azure portal Service Bus ad alanına gitmeniz ve URI 'nin ' den başlayarak olan kısmını kopyalamadır `/subscriptions/` . Örnek için aşağıdaki resme bakın. 
        2. **Hedef alt kaynak** için **ad alanı** girin. Bu, Özel uç noktanızın erişebileceği alt kaynağın türüdür. 
        3. seçim **İstek iletisi** girin. Kaynak sahibi, Özel uç nokta bağlantısını yönetirken bu iletiyi görür. 
        4. Ardından sayfanın alt kısmındaki **İleri: yapılandırma >** düğmesini seçin. 

            ![Özel uç nokta oluşturma-kaynak KIMLIĞI kullanarak bağlanma](./media/private-link-service/connect-resource-id.png)
9. **Yapılandırma** sayfasında, bir sanal ağda özel uç noktayı dağıtmak istediğiniz alt ağı seçersiniz. 
    1. Bir **sanal ağ** seçin. Yalnızca şu anda seçili olan abonelikte ve konumda bulunan sanal ağlar açılan listede listelenir. 
    2. Seçtiğiniz sanal ağda bir **alt ağ** seçin. 
    3. Sayfanın alt kısmındaki **İleri: etiketler >** düğmesini seçin. 

        ![Özel uç nokta oluşturma-yapılandırma sayfası](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. **Etiketler** sayfasında, Özel uç nokta kaynağıyla ilişkilendirmek istediğiniz etiketleri (adlar ve değerler) oluşturun. Ardından sayfanın alt kısmındaki **gözden geçir + oluştur** düğmesini seçin. 
11. **Gözden geçir + oluştur**' da, tüm ayarları gözden geçirin ve **Oluştur** ' u seçerek özel uç noktasını oluşturun.
    
    ![Özel uç nokta oluşturma-sayfa Inceleme ve oluşturma](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Özel uç noktanın oluşturulduğunu doğrulayın. Kaynak sahibiyseniz ve **bağlantı yöntemi** için **Dizinimde bir Azure kaynağına bağlan** seçeneğini belirlediyseniz, uç nokta bağlantısı **otomatik olarak onaylanmalıdır**. **Bekleme** durumundaysa, [Azure Portal kullanarak özel uç noktaları yönetme](#manage-private-endpoints-using-azure-portal) bölümüne bakın.

    ![Özel uç nokta oluşturuldu](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="add-a-private-endpoint-using-powershell"></a>PowerShell kullanarak özel uç nokta ekleme
Aşağıdaki örnek, bir Service Bus ad alanına özel bir uç nokta bağlantısı oluşturmak için Azure PowerShell nasıl kullanacağınızı gösterir.

Özel uç noktanız ve sanal ağınız aynı bölgede olmalıdır. Service Bus ad alanınız farklı bir bölgede olabilir. Ve özel uç noktanız sanal ağınızda özel bir IP adresi kullanıyor.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```


## <a name="manage-private-endpoints-using-azure-portal"></a>Azure portal kullanarak özel uç noktaları yönetme

Özel bir uç nokta oluşturduğunuzda bağlantının onaylanması gerekir. Özel bir uç noktası oluşturmakta olduğunuz kaynak dizininizdeki ise, yeterli izinlere sahip olduğunuz belirtilen bağlantı isteğini onaylayabilirsiniz. Başka bir dizindeki bir Azure kaynağına bağlanıyorsanız, bu kaynağın sahibinin bağlantı isteğinizi onaylamasını beklemeniz gerekir.

Dört sağlama durumu vardır:

| Hizmet eylemi | Hizmet tüketicisi özel uç nokta durumu | Açıklama |
|--|--|--|
| Hiçbiri | Beklemede | Bağlantı el ile oluşturulur ve özel bağlantı kaynağı sahibinden onay bekliyor. |
| Onaylama | Onaylandı | Bağlantı otomatik olarak veya el ile onaylandı ve kullanılabilir hale gelmiştir. |
| Reddet | Reddedildi | Bağlantı, özel bağlantı kaynağı sahibi tarafından reddedildi. |
| Kaldır | Bağlantı kesildi | Bağlantı, özel bağlantı kaynağı sahibi tarafından kaldırıldı, Özel uç nokta bilgilendirici hale gelir ve temizlik için silinmelidir. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Özel bir uç nokta bağlantısını onaylama, reddetme veya kaldırma

1. Azure portalında oturum açın.
1. Arama çubuğuna **Service Bus** yazın.
1. Yönetmek istediğiniz **ad alanını** seçin.
1. **Ağ** sekmesini seçin.
5. Şunları yapmak istediğiniz işleme göre aşağıdaki ilgili bölüme gidin: onaylama, reddetme veya kaldırma. 

### <a name="approve-a-private-endpoint-connection"></a>Özel bir uç nokta bağlantısını onaylama

1. Bekleyen herhangi bir bağlantı varsa, sağlama durumunda **bekliyor** ile listelenmiş bir bağlantı görürsünüz. 
2. Onaylamak istediğiniz **Özel uç noktayı** seçin
3. **Onayla** düğmesini seçin.

    ![Özel uç noktayı Onayla](./media/private-link-service/private-endpoint-approve.png)
4. **Bağlantıyı Onayla** sayfasında, isteğe bağlı bir **Açıklama** girin ve **Evet**' i seçin. **Hayır**' ı seçerseniz, hiçbir şey olmaz. 

    ![Bağlantı sayfasını Onayla](./media/private-link-service/approve-connection-page.png)
5. Listede, **Onaylandı** olarak değiştirilen bağlantının durumunu görmeniz gerekir. 

    ![Bağlantı durumu-onaylandı](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Özel bir uç nokta bağlantısını reddetme

1. Reddetmek istediğiniz özel uç nokta bağlantıları varsa, bekleyen bir istek ya da daha önce onaylanmış mevcut bir bağlantı olup olmadığı takdirde, uç nokta bağlantısını seçin ve **Reddet** düğmesine tıklayın.

    ![Reddetme düğmesi](./media/private-link-service/private-endpoint-reject.png)
2. **Bağlantıyı Reddet** sayfasında, isteğe bağlı bir açıklama girin ve **Evet**' i seçin. **Hayır**' ı seçerseniz, hiçbir şey olmaz. 

    ![Bağlantıyı Reddet sayfası](./media/private-link-service/reject-connection-page.png)
3. **Reddedilen** değiştirilen listede bağlantının durumunu görmeniz gerekir. 

    ![Uç nokta reddedildi](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Özel uç nokta bağlantısını kaldırma

1. Özel bir uç nokta bağlantısını kaldırmak için listeden seçin ve araç çubuğundan **Kaldır** ' ı seçin. 

    ![Kaldır düğmesi](./media/private-link-service/remove-endpoint.png)
2. **Bağlantıyı Sil** sayfasında, Özel uç noktasının silinmesini onaylamak için **Evet** ' i seçin. **Hayır**' ı seçerseniz, hiçbir şey olmaz. 

    ![Bağlantı sayfasını Sil](./media/private-link-service/delete-connection-page.png)
3. Durumu **bağlı değil** olarak değiştirildiğini görmeniz gerekir. Ardından, uç noktanın listeden kaybolduğunu görürsünüz. 

## <a name="validate-that-the-private-link-connection-works"></a>Özel bağlantı bağlantısının çalışıp çalışmadığını doğrulama

Özel uç noktanın sanal ağı içindeki kaynakların, özel bir IP adresi üzerinden Service Bus ad alanına bağlandığını ve doğru özel DNS bölge tümleştirmesine sahip olduğunu doğrulamanız gerekir.

İlk olarak, [Azure Portal Windows sanal makinesi oluşturma](../virtual-machines/windows/quick-create-portal.md) bölümündeki adımları izleyerek bir sanal makine oluşturun

**Ağ** sekmesinde: 

1. **Sanal ağ** ve **alt ağ** belirtin. Özel uç noktasını dağıttığınız sanal ağı seçmeniz gerekir.
2. Genel bir **IP** kaynağı belirtin.
3. **NIC ağ güvenlik grubu** için **hiçbiri**' ni seçin.
4. **Yük Dengeleme** için **Hayır**' ı seçin.

VM 'ye bağlanın, komut satırını açın ve şu komutu çalıştırın:

```console
nslookup <service-bus-namespace-name>.servicebus.windows.net
```

Aşağıdakine benzer bir sonuç görmeniz gerekir. 

```console
Non-authoritative answer:
Name:    <service-bus-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Sınırlamalar ve tasarım konuları

**Fiyatlandırma**: fiyatlandırma bilgileri için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link/).

**Sınırlamalar**: Bu özellik tüm Azure genel bölgelerinde kullanılabilir.

**Service Bus ad alanı başına en fazla özel uç nokta sayısı**: 120.

Daha fazla bilgi için bkz [. Azure özel bağlantı hizmeti: sınırlamalar](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Sonraki Adımlar

- [Azure özel bağlantısı](../private-link/private-link-service-overview.md) hakkında daha fazla bilgi edinin
- [Azure Service Bus](service-bus-messaging-overview.md) hakkında daha fazla bilgi edinin
