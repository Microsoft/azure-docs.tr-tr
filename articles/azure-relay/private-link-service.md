---
title: Azure Relay Azure özel bağlantı hizmeti ile tümleştirme
description: Azure Relay Azure özel bağlantı hizmeti ile tümleştirme hakkında bilgi edinin
ms.date: 09/24/2020
ms.topic: article
ms.openlocfilehash: 13644082160704ba9918e6bd6257fa314bb463a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98134390"
---
# <a name="integrate-azure-relay-with-azure-private-link"></a>Azure Relay Azure özel bağlantısıyla tümleştirin 
Azure **özel bağlantı hizmeti** , Azure hizmetlerine (örneğin, Azure Relay, Azure Service Bus, Azure Event Hubs, Azure depolama ve Azure Cosmos DB) ve Azure 'da barındırılan müşteri/iş ortağı hizmetlerine sanal ağınızdaki özel bir uç nokta üzerinden erişmenizi sağlar. Daha fazla bilgi için bkz. [Azure özel bağlantısı nedir?](../private-link/private-link-overview.md)

**Özel uç nokta** , bir sanal ağda çalışan iş yüklerinizin **özel bir bağlantı kaynağına** (örneğin, bir geçiş ad alanı) sahip olan bir hizmete özel ve güvenli bir şekilde bağlanmasına olanak tanıyan bir ağ arabirimidir. Özel uç nokta, sanal ağınızdan bir özel IP adresi kullanarak hizmeti sanal ağınıza etkin bir şekilde getiriyor. Hizmete giden tüm trafik özel uç nokta aracılığıyla yönlendirilebilir, bu nedenle ağ geçitleri, NAT cihazları, ExpressRoute, VPN bağlantıları veya genel IP adresleri gerekmez. Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağı üzerinden, genel Internet 'ten etkilenme olasılığını ortadan kaldırır. Belirli Azure Relay ad alanlarına bağlantılara izin vererek, erişim denetiminde ayrıntı düzeyi düzeyi sağlayabilirsiniz. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Azure portal kullanarak özel uç nokta ekleme

### <a name="prerequisites"></a>Önkoşullar
Bir Azure Relay ad alanını Azure özel bağlantısıyla bütünleştirmek için aşağıdaki varlıklara veya izinlere ihtiyacınız olacaktır:

- Bir Azure Relay ad alanı.
- Bir Azure sanal ağı.
- Sanal ağdaki bir alt ağ.
- Sanal ağ üzerinde sahip veya katkıda bulunan izinleri.

Özel uç noktanız ve sanal ağınız aynı bölgede olmalıdır. Portalı kullanarak özel uç nokta için bir bölge seçtiğinizde bu, yalnızca o bölgedeki sanal ağları otomatik olarak filtreleyecek. Ad alanınız farklı bir bölgede olabilir.

Özel uç noktanız sanal ağınızda özel bir IP adresi kullanıyor.

### <a name="steps"></a>Adımlar
İçinde yeni bir Azure Relay ad alanı ve varlık oluşturmaya yönelik adım adım yönergeler için, bkz. [Azure Portal kullanarak Azure Relay ad alanı oluşturma](relay-create-namespace-portal.md).

1. [Azure portalında](https://portal.azure.com) oturum açın. 
2. Arama çubuğuna **geçişler**' i yazın.
3. Listeden özel uç nokta eklemek istediğiniz **ad alanını** seçin.
4. **Ayarlar** altında **ağ** sekmesini seçin.
5. Sayfanın üst kısmındaki **Özel uç nokta bağlantıları** sekmesini seçin
6. Sayfanın üst kısmındaki **+ Özel uç nokta** düğmesini seçin.

    ![Özel uç nokta Ekle düğmesi](./media/private-link-service/add-private-endpoint-button.png)
7. **Temel bilgiler** sayfasında, aşağıdaki adımları izleyin: 
    1. Özel uç noktasını oluşturmak istediğiniz **Azure aboneliğini** seçin. 
    2. Özel uç nokta kaynağı için **kaynak grubunu** seçin.
    3. Özel uç nokta için bir **ad** girin. 
    5. Özel uç nokta için bir **bölge** seçin. Özel uç noktanız, sanal ağınızla aynı bölgede olmalıdır, ancak bağlandığınız Azure Relay ad alanından farklı bir bölgede olabilir. 
    6. Sayfanın alt kısmındaki **İleri: kaynak >** düğmesini seçin.

        ![Özel uç nokta oluşturma-temel bilgiler sayfası](./media/private-link-service/create-private-endpoint-basics-page.png)
8. **Kaynak** sayfasında, aşağıdaki adımları izleyin:
    1. Bağlantı yöntemi için **dizinimde bir Azure kaynağına bağlan**' ı seçerseniz, ad alanına sahip veya katkıda bulunan erişimi vardır ve bu ad alanı özel uç noktayla aynı dizinde yer alıyorsa, şu adımları izleyin: 
        1. **Azure Relay ad alanının** bulunduğu **Azure aboneliğini** seçin. 
        2. **Kaynak türü** Için, **kaynak türü** için **Microsoft. Relay/namespaces** ' i seçin.
        3. **Kaynak** için, açılan listeden bir geçiş ad alanı seçin. 
        4. **Hedef alt kaynağın** **ad alanı** olarak ayarlandığını onaylayın.
        5. Sayfanın alt kısmındaki **İleri: yapılandırma >** düğmesini seçin. 
        
            ![Özel uç nokta oluşturma-kaynak sayfası](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Ad alanı özel uç noktayla aynı dizinde olmadığından, **kaynak kimliğiyle veya diğer adla Azure kaynağına bağlan** ' ı seçerseniz, aşağıdaki adımları izleyin:
        1. **Kaynak kimliğini** veya **diğer adı** girin. Bu, birinin sizinle paylaştığı kaynak KIMLIĞI veya diğer ad olabilir. Kaynak KIMLIĞINI almanın en kolay yolu, Azure portal Azure Relay ad alanına gitmeniz ve URI 'nin ' den başlayarak olan kısmını kopyalamadır `/subscriptions/` . Örnek aşağıda verilmiştir: `/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. **Hedef alt kaynak** için **ad alanı** girin. Bu, Özel uç noktanızın erişebileceği alt kaynağın türüdür.
        3. seçim **İstek iletisi** girin. Kaynak sahibi, Özel uç nokta bağlantısını yönetirken bu iletiyi görür.
        4. Ardından sayfanın alt kısmındaki **İleri: yapılandırma >** düğmesini seçin.

            ![Özel uç nokta oluşturma-kaynak KIMLIĞI kullanarak bağlanma](./media/private-link-service/connect-resource-id.png)
9. **Yapılandırma** sayfasında, bir sanal ağda özel uç noktayı dağıtmak istediğiniz alt ağı seçersiniz. 
    1. Bir **sanal ağ** seçin. Yalnızca şu anda seçili olan abonelikte ve konumda bulunan sanal ağlar açılan listede listelenir. 
    2. Seçtiğiniz sanal ağda bir **alt ağ** seçin. 
    3. Özel uç noktanızı özel bir DNS bölgesiyle bütünleştirmek istiyorsanız **özel DNS bölgesi Ile tümleştirmeyi** etkinleştirin. 
    
        Özel uç noktanıza özel olarak bağlanmak için bir DNS kaydına ihtiyacınız vardır. Özel uç noktanızı **Özel BIR DNS bölgesiyle** tümleştirmenizi öneririz. Ayrıca, kendi DNS sunucularınızı kullanabilir veya sanal makinelerinizdeki konak dosyalarını kullanarak DNS kayıtları oluşturabilirsiniz. Daha fazla bilgi için bkz. [Azure özel uç nokta DNS yapılandırması](../private-link/private-endpoint-dns.md). Bu örnekte, **özel DNS bölgesiyle tümleştirin** seçeneği seçilidir ve sizin için özel bir DNS bölgesi oluşturulur. 
    3. Sayfanın alt kısmındaki **İleri: etiketler >** düğmesini seçin. 

        ![Özel uç nokta oluşturma-yapılandırma sayfası](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. **Etiketler** sayfasında, Özel uç nokta ve özel DNS bölgesi ile ilişkilendirmek istediğiniz etiketleri (adlar ve değerler) oluşturun (seçeneğini etkinleştirdiyseniz). Ardından sayfanın alt kısmındaki **gözden geçir + oluştur** düğmesini seçin. 
11. **Gözden geçir + oluştur**' da, tüm ayarları gözden geçirin ve **Oluştur** ' u seçerek özel uç noktasını oluşturun.
    
    ![Özel uç nokta oluşturma-sayfa Inceleme ve oluşturma](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. **Özel uç nokta** sayfasında, Özel uç nokta bağlantısının durumunu görebilirsiniz. Geçiş ad alanının sahibiyseniz veya bağlantıyı Yönet ' e sahipseniz ve **bağlantı yöntemi** için **Dizinimde bir Azure kaynağına bağlan** seçeneğini belirlediyseniz, uç nokta bağlantısının **otomatik olarak onaylanması** gerekir. **Bekleme** durumundaysa, [Azure Portal kullanarak özel uç noktaları yönetme](#manage-private-endpoints-using-azure-portal) bölümüne bakın.

    ![Özel uç nokta sayfası](./media/private-link-service/private-endpoint-page.png)
13. **Ad alanının** **ağ** sayfasına geri gidin ve **Özel uç nokta bağlantıları** sekmesine geçin. Oluşturduğunuz özel uç noktayı görmeniz gerekir. 

    ![Özel uç nokta oluşturuldu](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>PowerShell kullanarak özel uç nokta ekleme
Aşağıdaki örnek, bir Azure Relay ad alanına özel bir uç nokta bağlantısı oluşturmak için Azure PowerShell nasıl kullanacağınızı gösterir.

Özel uç noktanız ve sanal ağınız aynı bölgede olmalıdır. Azure Relay ad alanınız farklı bir bölgede olabilir. Ve özel uç noktanız sanal ağınızda özel bir IP adresi kullanıyor.

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

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

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

Özel bir uç nokta oluşturduğunuzda bağlantının onaylanması gerekir. Özel bir uç noktası oluşturmakta olduğunuz kaynak (geçiş ad alanı) dizininizdeki ise, geçiş ad alanı üzerinde ayrıcalıklarınızı yönettiğiniz belirtilen bağlantı isteğini onaylayabilirsiniz. Yönetme erişimi olmayan bir geçiş ad alanına bağlanıyorsanız, bu kaynağın sahibinin bağlantı isteğinizi onaylamasını beklemeniz gerekir.

Dört sağlama durumu vardır:

| Hizmet eylemi | Hizmet tüketicisi özel uç nokta durumu | Açıklama |
|--|--|--|
| Hiçbiri | Beklemede | Bağlantı el ile oluşturulur ve Azure Relay ad alanı sahibinden onay bekliyor. |
| Onaylama | Onaylandı | Bağlantı otomatik olarak veya el ile onaylandı ve kullanılabilir hale gelmiştir. |
| Reddet | Reddedildi | Bağlantı, Azure Relay ad alanı sahibi tarafından reddedildi. |
| Kaldır | Bağlantı kesildi | Bağlantı, Azure Relay ad alanı sahibi tarafından kaldırıldı, Özel uç nokta bilgilendirici hale gelir ve temizlik için silinmelidir. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Özel bir uç nokta bağlantısını onaylama, reddetme veya kaldırma

1. Azure portalında oturum açın.
1. Arama çubuğuna **geçiş** yazın.
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

### <a name="reject-a-private-endpoint-connection"></a>Özel bir uç nokta bağlantısını reddetme

1. Reddetmek istediğiniz özel uç nokta bağlantıları varsa, bekleyen bir istek ya da daha önce onaylanmış mevcut bir bağlantı olup olmadığı takdirde, uç nokta bağlantısını seçin ve **Reddet** düğmesine tıklayın.

    ![Reddetme düğmesi](./media/private-link-service/private-endpoint-reject.png)
2. **Bağlantıyı Reddet** sayfasında, isteğe bağlı bir açıklama girin ve **Evet**' i seçin. **Hayır**' ı seçerseniz, hiçbir şey olmaz. 

    ![Bağlantıyı Reddet sayfası](./media/private-link-service/reject-connection-page.png)
3. **Reddedilen** değiştirilen listede bağlantının durumunu görmeniz gerekir.


### <a name="remove-a-private-endpoint-connection"></a>Özel uç nokta bağlantısını kaldırma

1. Özel bir uç nokta bağlantısını kaldırmak için listeden seçin ve araç çubuğundan **Kaldır** ' ı seçin. 

    ![Kaldır düğmesi](./media/private-link-service/remove-endpoint.png)
2. **Bağlantıyı Sil** sayfasında, Özel uç noktasının silinmesini onaylamak için **Evet** ' i seçin. **Hayır**' ı seçerseniz, hiçbir şey olmaz. 

    ![Bağlantı sayfasını Sil](./media/private-link-service/delete-connection-page.png)
3. Durumu **bağlı değil** olarak değiştirildiğini görmeniz gerekir. Ardından, uç noktanın listeden kaybolduğunu görürsünüz. 

## <a name="validate-that-the-private-link-connection-works"></a>Özel bağlantı bağlantısının çalışıp çalışmadığını doğrulama
Özel uç noktanın sanal ağı içindeki kaynakların, kendi özel IP adresi üzerinden Azure Relay ad alanına bağlandığını doğrulamanız gerekir.

Bu test için [Azure Portal Windows sanal makinesi oluşturma](../virtual-machines/windows/quick-create-portal.md) içindeki adımları izleyerek bir sanal makine oluşturun

**Ağ** sekmesinde: 

1. **Sanal ağ** ve **alt ağ** belirtin. Özel uç noktasını dağıttığınız sanal ağı seçmeniz gerekir.
2. Genel bir **IP** kaynağı belirtin.
3. **NIC ağ güvenlik grubu** için **hiçbiri**' ni seçin.
4. **Yük Dengeleme** için **Hayır**' ı seçin.

VM 'ye bağlanın ve komut satırını açın ve şu komutu çalıştırın:

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

Aşağıdakine benzer bir sonuç görmeniz gerekir. 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Sınırlamalar ve tasarım konuları

### <a name="design-considerations"></a>Tasarım konusunda dikkat edilmesi gerekenler
- Fiyatlandırma bilgileri için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link/).

### <a name="limitations"></a>Sınırlamalar 
- Azure Relay ad alanı başına en fazla özel uç nokta sayısı: 64.
- Abonelik başına özel uç noktalara sahip Azure Relay ad alanı sayısı üst sınırı: 64.
- Ağ güvenlik grubu (NSG) kuralları ve User-Defined yolları özel uç nokta için uygulanmaz. Daha fazla bilgi için bkz [. Azure özel bağlantı hizmeti: sınırlamalar](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Sonraki Adımlar

- [Azure özel bağlantısı](../private-link/private-link-service-overview.md) hakkında daha fazla bilgi edinin
- [Azure Relay](relay-what-is-it.md) hakkında daha fazla bilgi edinin
