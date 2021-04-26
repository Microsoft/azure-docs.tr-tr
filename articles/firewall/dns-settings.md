---
title: Azure Güvenlik duvarı DNS ayarları
description: Azure Güvenlik Duvarı 'Nı DNS sunucusu ve DNS proxy ayarları ile yapılandırabilirsiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: d6a79e87e9999dd520358e0722011cf4e54d8c63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546253"
---
# <a name="azure-firewall-dns-settings"></a>Azure Güvenlik duvarı DNS ayarları

Özel bir DNS sunucusu yapılandırabilir ve Azure Güvenlik Duvarı için DNS proxy 'yi etkinleştirebilirsiniz. Bu ayarları, güvenlik duvarını dağıtırken yapılandırın veya daha sonra **DNS ayarları** sayfasından yapılandırın.

## <a name="dns-servers"></a>DNS sunucuları

DNS sunucusu, etki alanı adlarını IP adreslerine tutar ve çözümler. Azure Güvenlik Duvarı, varsayılan olarak ad çözümlemesi için Azure DNS kullanır. **DNS sunucusu** ayarı, kendi DNS sunucularınızı Azure Güvenlik Duvarı ad çözümlemesi için yapılandırmanızı sağlar. Tek bir sunucu veya birden çok sunucu yapılandırabilirsiniz.

> [!NOTE]
> Azure Güvenlik Duvarı Yöneticisi kullanılarak yönetilen Azure Güvenlik Duvarı örnekleri için DNS ayarları, ilişkili Azure Güvenlik duvarı ilkesinde yapılandırılır.

### <a name="configure-custom-dns-servers---azure-portal"></a>Özel DNS sunucularını Yapılandırma-Azure portal

1. Azure Güvenlik Duvarı **ayarları** altında **DNS ayarları**' nı seçin.
2. **DNS sunucuları** altında, sanal ağınızda daha önce BELIRTILEN mevcut DNS sunucularını yazabilir veya ekleyebilirsiniz.
3. **Kaydet**’i seçin.

Güvenlik duvarı artık DNS trafiğini ad çözümlemesi için belirtilen DNS sunucularına yönlendirir.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="D N S sunucularının ayarlarını gösteren ekran görüntüsü.":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Özel DNS sunucularını Yapılandırma-Azure CLı

Aşağıdaki örnek Azure CLı 'yı kullanarak Azure Güvenlik Duvarı 'nı özel DNS sunucularıyla güncelleştirir.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Komut `az network firewall` Için Azure CLI uzantısının `azure-firewall` yüklü olması gerekir. Komutunu kullanarak yükleyebilirsiniz `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Özel DNS sunucularını Yapılandırma-Azure PowerShell

Aşağıdaki örnek, Azure PowerShell kullanarak özel DNS sunucularıyla Azure Güvenlik Duvarı 'Nı güncelleştirir.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>DNS proxy 'si

Azure Güvenlik duvarını, DNS proxy 'si olarak davranacak şekilde yapılandırabilirsiniz. DNS proxy, istemci sanal makinelerinden bir DNS sunucusuna olan DNS isteklerine yönelik bir ara sunucu olur. Özel bir DNS sunucusu yapılandırırsanız DNS çözümleme uyuşmazlığını önlemek için DNS proxy 'yi etkinleştirin ve ağ kurallarında FQDN (tam etki alanı adı) filtrelemesini etkinleştirin.

:::image type="content" source="media/dns-settings/dns-proxy-2.png" alt-text="D N S proxy yapılandırması özel D N S sunucusunu kullanarak.":::


DNS proxy 'yi etkinleştirmezseniz, istemciden gelen DNS istekleri farklı bir zamanda bir DNS sunucusuna seyahat edebilir veya güvenlik duvarından karşılaştırıldığında farklı bir yanıt döndürebilir. DNS proxy, tutarsızlığın önüne geçmek için Azure Güvenlik duvarını istemci isteklerinin yoluna koyar.

Azure Güvenlik Duvarı bir DNS proxy 'si olduğunda, iki önbelleğe alma işlevi türü mümkündür:

- **Olumlu önbellek**: DNS çözümlemesi başarılı. Güvenlik Duvarı, paketin veya nesnenin TTL 'sini (yaşam süresi) kullanır. 

- **Negatif önbellek**: DNS çözümlemesi yanıt vermez veya hiçbir çözüm vermez. Güvenlik Duvarı bu bilgileri bir saat boyunca önbelleğe alır.

DNS proxy, tüm çözümlenen IP adreslerini ağ kurallarında FQDN 'lerden depolar. En iyi uygulama olarak, bir IP adresine çözümleyecek FQDN 'leri kullanın.  

### <a name="dns-proxy-configuration"></a>DNS proxy yapılandırması

DNS proxy yapılandırması üç adım gerektirir:
1. Azure Güvenlik duvarı DNS ayarları 'nda DNS proxy 'yi etkinleştirin.
2. İsteğe bağlı olarak, özel DNS sunucunuzu yapılandırın veya belirtilen varsayılanı kullanın.
3. Azure Güvenlik Duvarı özel IP adresini, sanal ağ DNS sunucusu ayarlarınızda özel bir DNS adresi olarak yapılandırın. Bu ayar, DNS trafiğinin Azure Güvenlik Duvarı 'na yönlendirilmesini sağlar.

#### <a name="configure-dns-proxy---azure-portal"></a>DNS proxy 'yi Yapılandırma-Azure portal

DNS proxy 'yi yapılandırmak için, sanal ağ DNS sunucularınız ayarınızı güvenlik duvarı özel IP adresini kullanacak şekilde yapılandırmanız gerekir. Ardından, Azure Güvenlik Duvarı **DNS ayarları**' nda DNS proxy 'yi etkinleştirin.

##### <a name="configure-virtual-network-dns-servers"></a>Sanal ağ DNS sunucularını yapılandırma 

1. DNS trafiğinin Azure Güvenlik Duvarı örneği aracılığıyla yönlendirileceği sanal ağı seçin.
2. **Ayarlar** altında, **DNS sunucuları**' nı seçin.
3. **DNS sunucuları** altında **özel**' i seçin.
4. Güvenlik duvarının özel IP adresini girin.
5. **Kaydet**’i seçin.
6. Sanal ağa bağlı VM 'Leri yeniden başlatarak yeni DNS sunucusu ayarları atanır. VM 'Ler yeniden başlatılana kadar geçerli DNS ayarlarını kullanmaya devam eder.

##### <a name="enable-dns-proxy"></a>DNS proxy 'yi etkinleştir

1. Azure Güvenlik Duvarı örneğinizi seçin.
2. **Ayarlar** altında **DNS ayarları**' nı seçin.
3. **DNS proxy** varsayılan olarak devre dışıdır. Bu ayar etkinleştirildiğinde, güvenlik duvarı bağlantı noktası 53 ' i dinler ve DNS isteklerini yapılandırılmış DNS sunucularına iletir.
4. Ayarların ortamınıza uygun olduğundan emin olmak için **DNS sunucularının** yapılandırmasını gözden geçirin.
5. **Kaydet**’i seçin.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="D N S proxy 'sinin ayarlarını gösteren ekran görüntüsü.":::

#### <a name="configure-dns-proxy---azure-cli"></a>DNS proxy 'yi Yapılandırma-Azure CLı

Azure Güvenlik duvarında DNS proxy ayarlarını yapılandırmak için Azure CLı 'yı kullanabilirsiniz. Ayrıca, Azure Güvenlik Duvarı 'nı DNS sunucusu olarak kullanmak üzere sanal ağları güncelleştirmek için de kullanabilirsiniz.

##### <a name="configure-virtual-network-dns-servers"></a>Sanal ağ DNS sunucularını yapılandırma

Aşağıdaki örnek, sanal ağı DNS sunucusu olarak Azure Güvenlik Duvarı 'nı kullanacak şekilde yapılandırır.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>DNS proxy 'yi etkinleştir

Aşağıdaki örnek, Azure Güvenlik duvarında DNS proxy özelliğini sunar.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>DNS proxy 'yi Yapılandırma-Azure PowerShell

Azure Güvenlik duvarında DNS proxy ayarlarını yapılandırmak için Azure PowerShell kullanabilirsiniz. Ayrıca, Azure Güvenlik Duvarı 'nı DNS sunucusu olarak kullanmak üzere sanal ağları güncelleştirmek için de kullanabilirsiniz.

##### <a name="configure-virtual-network-dns-servers"></a>Sanal ağ DNS sunucularını yapılandırma

Aşağıdaki örnek, sanal ağı Azure Güvenlik duvarını bir DNS sunucusu olarak kullanacak şekilde yapılandırır.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>DNS proxy 'yi etkinleştir

Aşağıdaki örnek, Azure Güvenlik duvarında DNS proxy özelliğini sunar.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Sonraki adımlar

[Ağ kurallarında FQDN filtrelemesi](fqdn-filtering-network-rules.md)
