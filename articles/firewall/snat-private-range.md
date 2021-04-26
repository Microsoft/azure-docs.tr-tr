---
title: Azure Güvenlik Duvarı SNAT özel IP adresi aralıkları
description: SNAT için IP adresi aralıklarını yapılandırabilirsiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: 91d4d631376c03b668128936f3840ce1119f9b6f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482760"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Güvenlik Duvarı SNAT özel IP adresi aralıkları

Azure Güvenlik Duvarı, genel IP adreslerine giden tüm trafik için otomatik SNAT sağlar. Varsayılan olarak, hedef IP adresi [ıANA RFC 1918](https://tools.ietf.org/html/rfc1918)başına özel bir IP adresi aralığında olduğunda Azure Güvenlik Duvarı ağ kurallarıyla SNAT yapmaz. Uygulama kuralları, her zaman hedef IP adresinin her ne kadar [saydam bir ara sunucu](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) kullanılarak uygulanır.

Bu mantık, trafiği doğrudan Internet 'e yönlendirdiğinizde iyi sonuç verir. Bununla birlikte, [Zorlamalı tünel](forced-tunneling.md)seçeneğini etkinleştirdiyseniz, Internet 'e bağlı trafik AzureFirewallSubnet içindeki güvenlik DUVARı özel IP adreslerinden birine karşı, kaynağı şirket içi güvenlik duvarından gizleyerek gizler.

Kuruluşunuz özel ağlar için genel bir IP adresi aralığı kullanıyorsa, Azure Güvenlik Duvarı AzureFirewallSubnet 'deki güvenlik duvarı özel IP adreslerinden birine giden trafiği yeniden çıkarır. Ancak, Azure Güvenlik duvarını genel IP adresi **aralığınızı SNAT olarak** yapılandırmak için yapılandırabilirsiniz. Örneğin, tek bir IP adresi belirtmek için bunu şöyle belirtebilirsiniz: `192.168.1.10` . IP adresi aralığını belirtmek için bunu şöyle belirtebilirsiniz: `192.168.1.0/24` .

- Azure Güvenlik duvarını hedef IP adresinden bağımsız olarak **hiçbir şekilde hiçbir şekilde hiçbir şekilde hiçbir** şekilde SNAT olarak yapılandırmak için, özel IP adresi aralığınız olarak **0.0.0.0/0** kullanın Bu yapılandırmayla, Azure Güvenlik Duvarı trafiği hiçbir şekilde doğrudan Internet 'e yönlendirmez. 

- Güvenlik duvarını, hedef adresten bağımsız olarak **her zaman** SNAT olarak yapılandırmak için, özel IP adresi aralığınızdan **255.255.255.255/32** kullanın.

> [!IMPORTANT]
> Belirttiğiniz özel adres aralığı yalnızca ağ kuralları için geçerlidir. Şu anda uygulama kuralları her zaman SNAT.

> [!IMPORTANT]
> Kendi özel IP adresi aralıklarını belirtmek ve varsayılan ıANA RFC 1918 adres aralıklarını korumak istiyorsanız, özel listenizin ıANA RFC 1918 aralığını hala içerdiğinden emin olun. 

Aşağıdaki yöntemleri kullanarak SNAT özel IP adreslerini yapılandırabilirsiniz. Yapılandırmanızda uygun yöntemi kullanarak SNAT özel adreslerini yapılandırmanız gerekir. Bir güvenlik duvarı ilkesiyle ilişkili güvenlik duvarları, ilkede Aralık belirtmeli ve kullanımda olmamalıdır `AdditionalProperties` .


|Yöntem            |Klasik kuralları kullanma  |Güvenlik Duvarı İlkesi kullanma  |
|---------|---------|---------|
|Azure portalı     | [Destek](#classic-rules-3)| [Destek](#firewall-policy-1)|
|Azure PowerShell     |[yapılandırma `PrivateRange`](#classic-rules)|Şu anda desteklenmiyor|
|Azure CLI|[yapılandırma `--private-ranges`](#classic-rules-1)|Şu anda desteklenmiyor|
|ARM şablonu     |[`AdditionalProperties`güvenlik duvarı özelliğinde yapılandırma](#classic-rules-2)|[`snat/privateRanges`güvenlik duvarı ilkesinde yapılandırma](#firewall-policy)|


## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>SNAT özel IP adresi aralıklarını Yapılandırma-Azure PowerShell
### <a name="classic-rules"></a>Klasik kurallar

Güvenlik Duvarı için özel IP adresi aralıklarını belirtmek üzere Azure PowerShell kullanabilirsiniz.

> [!NOTE]
> Güvenlik Duvarı `PrivateRange` özelliği bir güvenlik duvarı ilkesiyle ilişkili güvenlik duvarları için yok sayılır. `SNAT`Özelliğini, `firewallPolicies` [SNAT özel IP adresı aralıkları-ARM şablonunu yapılandırma](#firewall-policy)bölümünde açıklandığı gibi kullanmanız gerekir.

#### <a name="new-firewall"></a>Yeni güvenlik duvarı

Klasik kuralları kullanan yeni bir güvenlik duvarı için Azure PowerShell cmdlet 'i şunlardır:

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> Kullanarak Azure Güvenlik Duvarı dağıtmak `New-AzFirewall` , mevcut bir VNET ve genel IP adresi gerektirir. Tam dağıtım kılavuzu için [Azure PowerShell kullanarak Azure Güvenlik Duvarı dağıtma ve yapılandırma](deploy-ps.md) konusuna bakın.

> [!NOTE]
> IANAPrivateRanges, diğer aralıklar buna eklenirken Azure Güvenlik duvarında geçerli varsayılan değerlere genişletilir. Özel Aralık belirtimde IANAPrivateRanges varsayılan kalmasını sağlamak için, `PrivateRange` Aşağıdaki örneklerde gösterildiği gibi belirtimde kalması gerekir.

Daha fazla bilgi için bkz. [New-AzFirewall](/powershell/module/az.network/new-azfirewall).

#### <a name="existing-firewall"></a>Mevcut güvenlik duvarı

Klasik kuralları kullanarak var olan bir güvenlik duvarını yapılandırmak için aşağıdaki Azure PowerShell cmdlet 'lerini kullanın:

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>SNAT özel IP adresi aralıklarını Yapılandırma-Azure CLı
### <a name="classic-rules"></a>Klasik kurallar

Klasik kuralları kullanarak güvenlik duvarı için özel IP adresi aralıklarını belirtmek üzere Azure CLı kullanabilirsiniz. 

#### <a name="new-firewall"></a>Yeni güvenlik duvarı

Klasik kuralları kullanan yeni bir güvenlik duvarı için Azure CLı komutu şu şekilde olur:

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Azure CLı komutunu kullanarak Azure Güvenlik Duvarı 'Nı dağıtmak `az network firewall create` , genel IP adresleri ve IP yapılandırması oluşturmak için ek yapılandırma adımları gerektirir. Tam dağıtım kılavuzu için bkz. [Azure CLI kullanarak Azure Güvenlik duvarını dağıtma ve yapılandırma](deploy-cli.md) .

> [!NOTE]
> IANAPrivateRanges, diğer aralıklar buna eklenirken Azure Güvenlik duvarında geçerli varsayılan değerlere genişletilir. Özel Aralık belirtimde IANAPrivateRanges varsayılan kalmasını sağlamak için, `private-ranges` Aşağıdaki örneklerde gösterildiği gibi belirtimde kalması gerekir.

#### <a name="existing-firewall"></a>Mevcut güvenlik duvarı

Klasik kuralları kullanarak var olan bir güvenlik duvarını yapılandırmak için, Azure CLı komutu şu şekilde olur:

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>SNAT özel IP adresi aralıklarını yapılandırma-ARM şablonu
### <a name="classic-rules"></a>Klasik kurallar

ARM Şablon dağıtımı sırasında SNAT 'yi yapılandırmak için, özelliğine aşağıdakileri ekleyebilirsiniz `additionalProperties` :

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```
### <a name="firewall-policy"></a>Güvenlik duvarı ilkesi

Bir güvenlik duvarı ilkesiyle ilişkili Azure Güvenlik duvarları, 2020-11-01 API sürümünden bu yana SNAT özel aralıklarını destekliyordu. Şu anda, güvenlik duvarı Ilkesinde SNAT özel aralığını güncelleştirmek için bir şablon kullanabilirsiniz. Aşağıdaki örnek güvenlik duvarını **her zaman** SNAT ağ trafiğine yapılandırır:

```json
{ 

            "type": "Microsoft.Network/firewallPolicies", 
            "apiVersion": "2020-11-01", 
            "name": "[parameters('firewallPolicies_DatabasePolicy_name')]", 
            "location": "eastus", 
            "properties": { 
                "sku": { 
                    "tier": "Standard" 
                }, 
                "snat": { 
                    "privateRanges": [255.255.255.255/32] 
                } 
            } 
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>SNAT özel IP adresi aralıklarını Yapılandırma-Azure portal
### <a name="classic-rules"></a>Klasik kurallar

Güvenlik Duvarı için özel IP adresi aralıklarını belirtmek üzere Azure portal kullanabilirsiniz.

1. Kaynak grubunuzu seçin ve ardından güvenlik duvarınızı seçin.
2. **Genel bakış** SAYFASıNDA **özel IP aralıkları**' nı seçerek **IANA RFC 1918** varsayılan değerini seçin.

   **Özel IP öneklerini Düzenle** sayfası açılır:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Özel IP öneklerini Düzenle":::

1. Varsayılan olarak, **IANAPrivateRanges** yapılandırılır.
2. Ortamınız için özel IP adresi aralıklarını düzenleyin ve ardından **Kaydet**' i seçin.

### <a name="firewall-policy"></a>Güvenlik duvarı ilkesi

1.  Kaynak grubunuzu seçin ve ardından güvenlik duvarı ilkenizi seçin.
2.  **Ayarlar** sütununda **özel IP aralıkları (SNAT)** seçeneğini belirleyin.

    Varsayılan olarak, **varsayılan Azure Güvenlik Duvarı ILKESI SNAT davranışını kullan** seçilidir. 
3. SNAT yapılandırmasını özelleştirmek için onay kutusunun işaretini kaldırın ve **SNAT gerçekleştir** altında ORTAMıNıZ için SNAT gerçekleştirme koşullarını seçin.
      :::image type="content" source="media/snat-private-range/private-ip-ranges-snat.png" alt-text="Özel IP aralıkları (SNAT)":::


4.   **Uygula**’yı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Güvenlik Duvarı Zorlamalı tünel oluşturma](forced-tunneling.md)hakkında bilgi edinin.