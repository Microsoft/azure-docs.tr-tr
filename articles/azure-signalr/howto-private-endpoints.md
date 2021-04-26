---
title: Özel uç noktaları kullanma
titleSuffix: Azure SignalR Service
description: Sanal ağlardan Azure SignalR hizmetine güvenli erişim için özel uç noktalara genel bakış.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 92e93c3746308d2d6c1a489efc6b5c866b0ad2d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682639"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>Azure SignalR hizmeti için özel uç noktaları kullanma

Bir sanal ağdaki (VNet) istemcilerin [özel bir bağlantı](../private-link/private-link-overview.md)üzerinden güvenli bir şekilde verilere erişmesine izin vermek Için, Azure SignalR hizmetiniz için [Özel uç noktaları](../private-link/private-endpoint-overview.md) kullanabilirsiniz. Özel uç nokta, Azure SignalR hizmetiniz için VNet adres alanından bir IP adresi kullanır. VNet ve Azure SignalR hizmetindeki istemciler arasındaki ağ trafiği, Microsoft omurga ağındaki özel bir bağlantı üzerinden geçiş yaparken genel İnternet 'ten etkilenme olasılığını ortadan kaldırır.

Azure SignalR hizmetiniz için özel uç noktalar kullanmak şunları yapmanızı sağlar:

- Azure SignalR hizmeti için genel uç noktasındaki tüm bağlantıları engellemek üzere ağ erişim denetimi 'ni kullanarak Azure SignalR hizmetinizi güvenli hale getirin.
- Sanal ağ (VNet) için güvenliği artırarak VNet 'ten veri alımını engelleyebilirsiniz.
- [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute](../expressroute/expressroute-locations.md) Ile özel eşleme ile sanal ağa bağlanan şirket Içi ağlardan Azure SignalR hizmetlerine güvenli bir şekilde bağlanın.

## <a name="conceptual-overview"></a>Kavramsal genel bakış

![Azure SignalR hizmeti için özel uç noktalara genel bakış](media/howto-private-endpoints/private-endpoint-overview.png)

Özel uç nokta, [sanal ağınızdaki](../virtual-network/virtual-networks-overview.md) (VNet) bir Azure hizmeti için özel bir ağ arabirimidir. Azure SignalR hizmetiniz için özel bir uç nokta oluşturduğunuzda, VNet 'iniz ve hizmetinizdeki istemciler arasında güvenli bağlantı sağlar. Özel uç noktaya sanal Ağınızın IP adresi aralığından bir IP adresi atanır. Özel uç nokta ve Azure SignalR hizmeti arasındaki bağlantı güvenli bir özel bağlantı kullanır.

VNet 'teki uygulamalar, **diğer durumlarda kullandıkları aynı bağlantı dizelerini ve yetkilendirme mekanizmalarını kullanarak**, Özel uç nokta üzerinden Azure SignalR hizmetine sorunsuz bir şekilde bağlanabilir. Özel uç noktalar, REST API dahil olmak üzere Azure SignalR hizmeti tarafından desteklenen tüm protokollerle kullanılabilir.

VNet 'iniz için bir Azure SignalR hizmeti için özel bir uç nokta oluşturduğunuzda, Azure SignalR hizmeti sahibine onay için bir izin isteği gönderilir. Özel uç noktanın oluşturulmasını isteyen kullanıcı aynı zamanda Azure SignalR hizmeti 'nin sahibiyseniz, bu onay isteği otomatik olarak onaylanır.

Azure SignalR hizmet sahipleri, [Azure Portal](https://portal.azure.com)Azure SignalR hizmeti Için '*Özel uç noktalar*' sekmesi aracılığıyla izin isteklerini ve özel uç noktaları yönetebilir.

> [!TIP]
> Yalnızca özel uç nokta aracılığıyla Azure SignalR hizmetinize erişimi kısıtlamak istiyorsanız, [ağ Access Control](howto-network-access-control.md#managing-network-access-control) genel uç nokta üzerinden erişimi reddetmek veya denetlemek üzere yapılandırın.

### <a name="connecting-to-private-endpoints"></a>Özel uç noktalara bağlanma

Özel uç nokta kullanan bir sanal ağdaki istemciler, genel uç noktaya bağlanan istemciler için Azure SignalR hizmeti için aynı bağlantı dizesini kullanmalıdır. DNS çözümünden, bağlantıları VNet 'ten Azure SignalR hizmetine özel bir bağlantı üzerinden otomatik olarak yönlendirmek için kullanırız.

> [!IMPORTANT]
> Diğer durumlarda kullandığınız gibi özel uç noktaları kullanarak Azure SignalR hizmetine bağlanmak için aynı bağlantı dizesini kullanın. Lütfen bu alt `privatelink` etki alanı URL 'sini kullanarak Azure SignalR hizmetine bağlanmayın.

Varsayılan olarak, Özel uç noktalara yönelik gerekli güncelleştirmelerle VNet 'e bağlı [Özel BIR DNS bölgesi](../dns/private-dns-overview.md) oluşturacağız. Ancak, kendi DNS sunucunuzu kullanıyorsanız, DNS yapılandırmanızda ek değişiklikler yapmanız gerekebilir. Aşağıdaki [DNS değişikliklerinin](#dns-changes-for-private-endpoints) bölümünde, Özel uç noktalar için gereken güncelleştirmeler açıklanmaktadır.

## <a name="dns-changes-for-private-endpoints"></a>Özel uç noktalar için DNS değişiklikleri

Özel bir uç nokta oluşturduğunuzda, Azure SignalR hizmetiniz için DNS CNAME kaynak kaydı, ön ek içeren bir alt etki alanındaki diğer ada güncelleştirilir `privatelink` . Varsayılan olarak, [](../dns/private-dns-overview.md) `privatelink` Özel uç noktalar için DNS a kaynak kayıtlarıyla birlikte alt etki alanına karşılık gelen özel bir DNS bölgesi de oluşturacağız.

Azure SignalR hizmeti etki alanı adınızı VNet dışından özel uç noktayla çözdüğünde, Azure SignalR hizmetinin genel uç noktasına dönüşür. Özel uç noktasını barındıran VNet 'ten çözümlendiğinde, etki alanı adı özel bitiş noktasının IP adresine çözümlenir.

Yukarıdaki gösterilen örnek için, Özel uç noktayı barındıran VNet dışından çözümlendiğinde ' foobar ' Azure SignalR hizmeti için DNS kaynak kayıtları şu şekilde olur:

| Ad                                                  | Tür  | Değer                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | \<Azure SignalR Service public IP address\>           |

Daha önce belirtildiği gibi, ağ erişim denetimi kullanarak VNet dışındaki istemciler için genel uç nokta aracılığıyla erişimi reddedebilir veya kontrol edebilirsiniz.

Özel uç noktasını barındıran VNet 'teki bir istemci tarafından çözümlendiğinde ' foobar ' için DNS kaynak kayıtları şu şekilde olur:

| Ad                                                  | Tür  | Değer                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | 10.1.1.5                                              |

Bu yaklaşım, Özel uç noktaları barındıran VNet 'teki istemciler ve VNet dışındaki istemciler için **aynı bağlantı dizesini kullanarak** Azure SignalR hizmetine erişim sağlar.

Ağınızda özel bir DNS sunucusu kullanıyorsanız, istemciler Azure SignalR hizmeti uç noktası için FQDN 'yi özel uç nokta IP adresine çözümleyebilmelidir. DNS sunucunuzu özel bağlantı alt etki alanınızı, sanal ağın özel DNS bölgesine devretmek veya `foobar.privatelink.service.signalr.net` Özel uç nokta IP adresi ile Için bir kayıt yapılandırmak üzere yapılandırmanız gerekir.

> [!TIP]
> Özel veya şirket içi bir DNS sunucusu kullanırken, DNS sunucunuzu, alt etki alanındaki Azure SignalR hizmet adını `privatelink` Özel uç nokta IP adresine çözümlemek üzere yapılandırmalısınız. Bunu, alt `privatelink` etki alanını VNET 'in özel DNS bölgesine devredererek veya DNS SUNUCUNUZDA DNS bölgesini yapılandırarak ve DNS A kayıtlarını ekleyerek yapabilirsiniz.

Azure SignalR hizmeti için özel uç noktalar için önerilen DNS bölgesi adı: `privatelink.service.signalr.net` .

Kendi DNS sunucunuzu özel uç noktaları destekleyecek şekilde yapılandırma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure sanal ağlarındaki kaynaklar için ad çözümlemesi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Özel uç noktalar için DNS yapılandırması](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>Özel uç nokta oluşturma

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>Azure portal yeni bir Azure SignalR hizmeti ile birlikte özel bir uç nokta oluşturun

1. Yeni bir Azure SignalR hizmeti oluştururken **ağ** sekmesini seçin. **Özel uç noktayı** bağlantı yöntemi olarak seçin.

    ![Azure SignalR hizmeti oluşturma-Ağ sekmesi](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. **Ekle**'ye tıklayın. Yeni özel uç nokta için abonelik, kaynak grubu, konum, ad ' u girin. Bir sanal ağ ve alt ağ seçin.

    ![Azure SignalR hizmeti oluşturma-özel uç nokta ekleme](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. **Gözden geçir ve oluştur**’a tıklayın.

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>Azure portal mevcut bir Azure SignalR hizmeti için özel bir uç nokta oluşturun

1. Azure SignalR hizmetine gidin.

1. **Özel uç nokta bağlantıları** adlı ayarlar menüsüne tıklayın.

1. Üstteki düğme **+ Özel uç noktaya** tıklayın.

    ![Özel uç nokta bağlantıları dikey penceresi](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. Yeni özel uç nokta için abonelik, kaynak grubu, kaynak adı ve bölge ' yi girin.
    
    ![Özel uç nokta oluşturma-temel bilgiler](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. Hedef Azure SignalR hizmeti kaynağını seçin.

    ![Özel uç nokta oluşturma-kaynak](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. Hedef sanal ağı seçin

    ![Özel uç nokta oluşturma-yapılandırma](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. **Gözden geçir ve oluştur**’a tıklayın.

### <a name="create-a-private-endpoint-using-azure-cli"></a>Azure CLı kullanarak özel uç nokta oluşturma

1. Azure CLı 'da oturum açma
    ```azurecli
    az login
    ```
1. Azure Aboneliğinizi seçin
    ```azurecli
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. Yeni Kaynak Grubu oluşturma
    ```azurecli
    az group create -n {RG} -l {AZURE REGION}
    ```
1. Microsoft. SignalRService 'i sağlayıcı olarak kaydet
    ```azurecli
    az provider register -n Microsoft.SignalRService
    ```
1. Yeni bir Azure SignalR hizmeti oluşturun
    ```azurecli
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. Sanal Ağ Oluşturma
    ```azurecli
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. Alt ağ ekleme
    ```azurecli
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. Sanal ağ Ilkelerini devre dışı bırak
    ```azurecli
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. Özel DNS bölgesi ekleme
    ```azurecli
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. Özel DNS bölgesini sanal ağa bağlama
    ```azurecli
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. Özel uç nokta oluştur (otomatik olarak Onayla)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. Özel uç nokta oluşturma (El Ile onay ISTEME)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. Bağlantı durumunu göster
    ```azurecli
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>Fiyatlandırma

Fiyatlandırma ayrıntıları için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Bilinen Sorunlar

Azure SignalR hizmeti için özel uç noktalar hakkında aşağıdaki bilinen sorunları göz önünde bulundurun

### <a name="free-tier"></a>Ücretsiz katmanı

Ücretsiz Azure SignalR hizmeti için özel bir uç nokta oluşturamazsınız.

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Özel uç noktalarla VNET 'lerdeki istemciler için erişim kısıtlamaları

Özel uç noktalara sahip diğer Azure SignalR hizmeti örneklerine erişirken, mevcut özel uç noktalar ile VNET 'lerdeki istemciler. Örneğin, VNet N1 'nin bir Azure SignalR hizmet örneği S1 için özel bir uç noktası olduğunu varsayalım. Azure SignalR hizmeti S2, VNet 'de özel bir uç nokta içeriyorsa, VNet N1 içindeki istemciler ayrıca özel bir uç nokta kullanarak Azure SignalR hizmeti S2 ' na de erişmelidir. Azure SignalR hizmeti S2 'nin özel uç noktaları yoksa, VNet 'teki istemciler bu hesapta yer alan Azure SignalR hizmetine özel bir uç nokta olmadan erişebilir.

Bu kısıtlama, Azure SignalR hizmeti S2 özel bir uç nokta oluşturduğunda yapılan DNS değişikliklerinin bir sonucudur.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Özel uç noktaları olan alt ağlar için Ağ Güvenlik Grubu kuralları

Şu anda, [ağ güvenlik grubu](../virtual-network/network-security-groups-overview.md) (NSG) kurallarını ve özel uç noktalar için Kullanıcı tanımlı yolları yapılandıramazsınız. Özel uç noktasını barındıran alt ağa uygulanan NSG kuralları özel uç noktaya uygulanır. Bu sorun için sınırlı bir geçici çözüm, kaynak alt ağlardaki özel uç noktalar için erişim kurallarınızı uygulamaktır, ancak bu yaklaşım daha yüksek bir yönetim yükü gerektirebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Ağ Access Control yapılandırma](howto-network-access-control.md)