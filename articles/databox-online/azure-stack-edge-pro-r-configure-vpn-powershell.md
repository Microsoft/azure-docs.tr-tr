---
title: Azure Stack Edge Pro R cihazınızda VPN 'yi Azure PowerShell kullanarak yapılandırma
description: Azure kaynakları oluşturmak için bir Azure PowerShell betiği kullanarak Azure Stack Edge Pro R cihazınızda VPN 'nin nasıl yapılandırılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 66edd4cad5b2f38696ef1df2030687bf4c7d9956
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102634184"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>Azure Stack Edge Pro R cihazınızda VPN 'yi Azure PowerShell aracılığıyla yapılandırma

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

VPN seçeneği, Azure Stack Edge Pro R cihazınızdan Azure 'a kadar olan *TLS* üzerinden veri hareket etmek için ikinci bir şifreleme katmanı sağlar. Azure Stack Edge Pro R cihazınızda VPN 'yi Azure portal veya Azure PowerShell aracılığıyla yapılandırabilirsiniz.

Bu makalede, bulutta yapılandırmayı oluşturmak için Azure PowerShell kullanarak Azure Stack Edge Pro R cihazınızda VPN yapılandırmak için gereken adımlar açıklanmaktadır.

## <a name="about-vpn-setup"></a>VPN Kurulumu hakkında

Şirketler arası VPN bağlantısı, bir Azure VPN ağ geçidinden, şirket içi VPN cihazından ve iki bağlantı kurarak bir IPSec S2S VPN tünelinden oluşur. Tipik iş akışı aşağıdaki adımları içerir:

- Önkoşulları yapılandırma.
- Azure 'da gerekli kaynakları ayarlayın.
    - Bir Azure VPN ağ geçidi (sanal ağ geçidi) oluşturun ve yapılandırın.
    - Şirket içi ağınızı ve VPN cihazınızı temsil eden bir Azure yerel ağ geçidi oluşturun ve yapılandırın.
    - Azure VPN ağ geçidi ve yerel ağ geçidi arasında bir Azure VPN bağlantısı oluşturun ve yapılandırın.
    - Azure Güvenlik duvarını ayarlama ve ağ ve uygulama kuralları ekleme.
    - Azure yönlendirme tablosu oluşturun ve rotalar ekleyin.
- Cihazın yerel Web Kullanıcı arabiriminde VPN 'yi ayarlayın. Azure VPN ağ geçidi ile gerçek S2S VPN tüneli oluşturmak için yerel ağ geçidi tarafından temsil edilen şirket içi VPN cihazını yapılandırırsınız.

Ayrıntılı adımlar aşağıdaki bölümlerde verilmiştir.

## <a name="configure-prerequisites"></a>Önkoşulları yapılandırma

1. [Azure Stack Edge Pro r cihazınızı yükleme](azure-stack-edge-pro-r-deploy-install.md)yönergelerindeki yönergelere göre yüklenen bir Azure Stack Edge Pro r cihazına erişiminizin olması gerekir. Bu cihaz, Azure ile VPN bağlantısını oluşturmak için şirket içi VPN cihazı olarak görev yapar. 

2. VPN cihazınızın statik bir genel IP adresi (dış) olmalıdır. Bu adres NAT olmamalıdır.

3. Azure 'da Azure Stack Edge hizmeti için etkinleştirilen geçerli bir Azure aboneliğine erişiminizin olması gerekir. Azure Stack Edge Pro R cihazınızı yönetmek üzere Azure 'da karşılık gelen bir kaynak oluşturmak için bu aboneliği kullanın.  

4. Azure Stack Edge Pro R cihazınıza erişmek için kullanacağınız bir Windows istemcisine erişiminiz var. Bu istemciyi, yapılandırmayı bulutta programlı bir şekilde oluşturmak için kullanacaksınız.

    1. Windows istemcinizdeki gerekli PowerShell sürümünü yüklemek için aşağıdaki komutları çalıştırın:

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Azure hesabınıza ve aboneliğine bağlanmak için aşağıdaki komutları çalıştırın:

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        VPN 'yi yapılandırmak için Azure Stack Edge Pro R cihazınkiyle kullandığınız Azure abonelik adını sağlayın.

    3. Bulutta yapılandırma oluşturmak için gereken [betiği indirin](https://aka.ms/ase-vpn-deployment) . Betik şunları yapacak:
        
        - Bir Azure sanal ağı ve aşağıdaki alt ağları oluşturun: *Gatewaysubnet* ve *AzureFirewallSubnet*.
        - Bir Azure VPN Ağ Geçidi oluşturun ve yapılandırın.
        - Azure yerel ağ geçidi oluşturun ve yapılandırın.
        - Azure VPN ağ geçidi ve yerel ağ geçidi arasında bir Azure VPN bağlantısı oluşturun ve yapılandırın.
        - Bir Azure Güvenlik duvarı oluşturun ve ağ kuralları, uygulama kuralları ekleyin.
        - Bir Azure yönlendirme tablosu oluşturun ve buna yollar ekleyin.


## <a name="use-the-script"></a>Betiği kullan

İlk `parameters.json` olarak, parametrelerinizi girmek için dosyayı değiştirirsiniz. Ardından, değiştirilmiş json dosyasını kullanarak betiği çalıştırırsınız.

Bu adımların her biri aşağıdaki bölümlerde ele alınmıştır.

### <a name="download-service-tags-file"></a>Hizmet etiketleri dosyasını indir

`ServiceTags.json`Betiği indirdiğiniz klasörde zaten bir dosyanız olabilir. Aksi takdirde, hizmet etiketleri dosyasını indirebilirsiniz.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Parametre dosyasını Değiştir

İlk adım, `parameters.json` dosyayı değiştirmek ve değişiklikleri kaydetmek olacaktır. 


Oluşturduğunuz Azure kaynakları için aşağıdaki adları sağlarsınız:

|Parametre adı  |Açıklama  |
|---------|---------|
|virtualNetworks_vnet_name    | Azure sanal ağ adı        |
|azureFirewalls_firewall_name     | Azure Güvenlik duvarı adı        |
|routeTables_routetable_name     | Azure yol tablosu adı        |
|publicIPAddresses_VNGW_public_ip_name     | Sanal ağ geçidinizin genel IP adresi adı       |
|virtualNetworkGateways_VNGW_name    | Azure VPN Gateway (sanal ağ geçidi) adı        |
|publicIPAddresses_firewall_public_ip_name     | Azure Güvenlik duvarınız için genel IP adresi adı         |
|localNetworkGateways_LNGW_name    |Azure yerel ağ geçidi adı          |
|connections_vngw_lngw_name    | Azure VPN bağlantı adı. Bu, sanal ağ geçidiniz ile yerel ağ geçidi arasındaki bağlantıdır.       |
|location     |Bu, Sanal ağınızı oluşturmak istediğiniz bölgedir. Aygıtınızla ilişkili bir bölgeyi seçin.         |

Aşağıdaki IP adresleri ve adres alanları, sanal ağ ve ilişkili alt ağlar (varsayılan, güvenlik duvarı, GatewaySubnet) dahil olmak üzere oluşturulan Azure kaynakları ile ilgilidir.

|Parametre adı  |Açıklama  |
|---------|---------|
|VnetIPv4AddressSpace    | Bu, sanal ağınızla ilişkili adres alanıdır.       |
|DefaultSubnetIPv4AddressSpace    |Bu, `Default` sanal ağınızın alt ağıyla ilişkili adres alanıdır.         |
|FirewallSubnetIPv4AddressSpace    |Bu, `Firewall` sanal ağınızın alt ağıyla ilişkili adres alanıdır.          |
|GatewaySubnetIPv4AddressSpace    |Bu, sanal ağınız için ile ilişkili adres alanıdır `GatewaySubnet` .          |
|GatewaySubnetIPv4bgpPeeringAddress    | Bu, BGP iletişimi için ayrılan ve sanal ağınız için ile ilişkili adres alanını temel alan IP adresidir `GatewaySubnet` .          |

Aşağıdaki IP adresleri ve adres alanları, şirket içi ağ (Azure Stack Edge Pro R cihazınızın dağıtıldığı) ile ilgilidir.

|Parametre adı  |Açıklama  |
|---------|---------|
|CustomerNetworkAddressSpace    |  Bu, özel IP adresiniz için adres alanıdır.       |
|CustomerPublicNetworkAddressSpace    |  Bu, genel IP adresiniz için adres alanıdır.       |
|DbeIOTNetworkAddressSpace    |Bu IP adresi IoT hizmeti tarafından ayrılmıştır. Bu parametreyi değiştirmeyin.        |
|AzureVPNsharedKey    |Bu paylaşılan anahtar, Azure VPN bağlantı kaynağı oluşturma işlemi sırasında kullanılır. Bu anahtar, yerel Web ARABIRIMI VPN yapılandırması sırasında VPN paylaşılan gizliliği olarak da sağlanır.         |
|DIN-Gateway-IPAddress   | Azure Stack Edge Pro R cihazınız için genel IP adresi. Bu bilinen bir durum olabilir ve betiği bir yer tutucu IP adresiyle çalıştırabilirsiniz. Yerel ağ geçidini daha sonra gerçek IP adresiyle düzenleyin.     |

#### <a name="caveats-to-keep-in-mind"></a>Aklınızda tutulacak uyarılar:

- Azure Stack Edge Pro R cihazının IP adresine sahip olacaksınız. Kaynak oluşturmak için bir yer tutucu IP adresi kullanabilir ve daha sonra, gerçek cihaz IP adresini ve cihaz için yerel ağın adres alanını atamak üzere Azure yerel ağ geçidini değiştirebilirsiniz.
- Internet atanmış numaralar yetkilisi (ıANA) üzerindeki IETF 'nin yönüne bağlı olarak, 172.16. x. y ile 172.24. z.a. arasında herhangi bir alt ağ kullanın Azure ağı için 172,24 IPv4 adres aralıklarını ayırdık.

### <a name="run-the-script"></a>Betiği çalıştırın

Değiştirme 'yi kullanmak `parameters.json` ve betiği çalıştırmak Için Azure kaynakları oluşturmak için aşağıdaki adımları izleyin.

1. PowerShell'i yönetici olarak çalıştırın.

2. Betiğin bulunduğu dizine geçin.

3. Betiği çalıştırın.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    Örnek çıktı aşağıda gösterilmiştir.

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    Betiğin çalıştırılması yaklaşık 90 dakika sürer. Betik tamamlandıktan sonra, bir dağıtım günlüğü, betiğin bulunduğu klasörde oluşturulur.


## <a name="verify-the-azure-resources"></a>Azure kaynaklarını doğrulama

Betiği başarıyla çalıştırdıktan sonra, tüm kaynakların Azure 'da oluşturulduğunu doğrulayın.

Daha sonra, cihazınızın yerel Web Kullanıcı arabiriminde VPN 'yi yapılandıracaksınız.


## <a name="vpn-configuration-on-the-device"></a>Cihazdaki VPN yapılandırması

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>VPN 'yi doğrula

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>VPN üzerinden veri aktarımını doğrulama

VPN 'nin çalıştığını doğrulamak için, verileri bir SMB paylaşımında kopyalayın. Azure Stack Edge Pro R cihazınızda [paylaşma ekleme](azure-stack-edge-gpu-manage-shares.md#add-a-share) bölümündeki adımları izleyin. 

1. \data\pictures\waterfall.jpg, örneğin, istemci sisteminize bağladığınız SMB paylaşımında bir dosya kopyalayın. 
2. Bu dosyanın buluttaki depolama hesabınızda göründüğünü doğrulayın.

Verilerin VPN üzerinden gittiğini doğrulamak için:

1. Kaynak grubunda bulunan bağlantı kaynağını açın. 

2. İçindeki verileri ve veri çıkış değerini denetleyin.
 
3. Kaynak grubunuzda sanal ağ geçidini açın. **Toplam Tünel** girişi ve **Toplam Tünel** çıkışı için grafikleri görüntüleyin.
 
## <a name="debug-issues"></a>Hata ayıklama sorunları

Herhangi bir sorunu ayıklamak için aşağıdaki komutları kullanın:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

Örnek çıktı aşağıda gösterilmektedir:


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure Stack Edge Pro R cihazınızda yerel kullanıcı arabirimi aracılığıyla VPN 'yi yapılandırma](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)
