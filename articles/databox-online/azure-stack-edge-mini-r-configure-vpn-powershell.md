---
title: Azure Stack Edge Mini R cihazınızda VPN 'yi Azure PowerShell kullanarak yapılandırma
description: Azure kaynakları oluşturmak için Azure PowerShell betiği kullanarak Azure Stack Edge Mini R cihazınızda VPN 'nin nasıl yapılandırılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 9fa4c678a04342b47601f81ede7c49ab841f42ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630971"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>Azure Stack Edge Mini R cihazınızda VPN 'yi Azure PowerShell aracılığıyla yapılandırma

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

VPN seçeneği, Azure Stack Edge Mini R veya Azure Stack Edge Pro R cihazınızdan Azure 'a kadar olan *TLS* üzerinden yapılan veriler için ikinci bir şifreleme katmanı sağlar. Azure Stack Edge Mini R cihazınızda VPN 'yi, Azure portal veya Azure PowerShell aracılığıyla yapılandırabilirsiniz. 

Bu makalede, bulutta yapılandırmayı oluşturmak için Azure PowerShell betiği kullanarak Azure Stack Edge Mini R cihazınızda bir noktadan siteye (P2S) VPN yapılandırmak için gereken adımlar açıklanmaktadır. Azure Stack Edge cihazında yapılandırma, yerel kullanıcı arabirimi aracılığıyla yapılır.

## <a name="about-vpn-setup"></a>VPN Kurulumu hakkında

Bir P2S VPN Gateway bağlantısı, tek bir istemci bilgisayarından veya Azure Stack Edge Mini cihazınızdan sanal ağınıza güvenli bir bağlantı oluşturmanıza olanak sağlar. P2S bağlantısını istemci bilgisayarından veya cihazdan başlatabilirsiniz. Bu örnekte P2S bağlantısı, standart tabanlı bir IPSec VPN çözümü olan IKEv2 VPN kullanır.

Tipik iş akışı aşağıdaki adımları içerir:

1. Önkoşulları yapılandırma.
2. Azure 'da gerekli kaynakları ayarlayın.
    1. Bir sanal ağ ve gerekli alt ağları oluşturun ve yapılandırın. 
    2. Bir Azure VPN ağ geçidi (sanal ağ geçidi) oluşturun ve yapılandırın.
    3. Azure Güvenlik duvarını ayarlama ve ağ ve uygulama kuralları ekleme.
    4. Azure yönlendirme tabloları oluşturun ve rotalar ekleyin.
    5. VPN Gateway 'de Noktadan siteye 'yi etkinleştirin.
        1. İstemci adres havuzunu ekleyin.
        2. Tünel türünü yapılandırın.
        3. Kimlik doğrulama türünü yapılandırın.
        4. Sertifika oluşturun.
        5. Sertifikayı karşıya yükleyin.
    6. Telefon rehberini indirin.
3. Cihazın yerel Web Kullanıcı arabiriminde VPN 'yi ayarlayın. 
    1. Telefon rehberi belirtin.
    2. Hizmet etiketleri (JSON) dosyası sağlayın.


Ayrıntılı adımlar aşağıdaki bölümlerde verilmiştir.

## <a name="configure-prerequisites"></a>Önkoşulları yapılandırma

- [Azure Stack Edge Mini r cihazınızı yükleme](azure-stack-edge-mini-r-deploy-install.md)konusundaki yönergelere göre yüklenen bir Azure Stack Edge Mini r cihazına erişiminizin olması gerekir. Bu cihaz Azure ile P2S bağlantısı kurmaya sunulacaktır. 

- Azure 'da Azure Stack Edge hizmeti için etkinleştirilen geçerli bir Azure aboneliğine erişiminizin olması gerekir. Azure Stack Edge Mini R cihazınızı yönetmek için Azure 'da karşılık gelen bir kaynak oluşturmak için bu aboneliği kullanın.  

- Azure Stack Edge Mini R cihazınıza erişmek için kullanacağınız bir Windows istemcisine erişiminiz var. Bu istemciyi, yapılandırmayı bulutta programlı bir şekilde oluşturmak için kullanacaksınız.

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
        VPN 'yi yapılandırmak için Azure Stack Edge Mini R cihazınkiyle kullandığınız Azure abonelik adını sağlayın.

    3. Bulutta yapılandırma oluşturmak için gereken [betiği indirin](https://aka.ms/ase-vpn-deployment) . Betik şunları yapacak:
        
        - Bir Azure sanal ağı ve aşağıdaki alt ağları oluşturun: *Gatewaysubnet* ve *AzureFirewallSubnet*.
        - Bir Azure VPN Ağ Geçidi oluşturun ve yapılandırın.
        - Azure yerel ağ geçidi oluşturun ve yapılandırın.
        - Azure VPN ağ geçidi ve yerel ağ geçidi arasında bir Azure VPN bağlantısı oluşturun ve yapılandırın.
        - Bir Azure Güvenlik duvarı oluşturun ve ağ kuralları, uygulama kuralları ekleyin.
        - Bir Azure yönlendirme tablosu oluşturun ve buna yollar ekleyin.

    4. Kaynak grubunu, Azure kaynaklarının oluşturulmasını istediğiniz Azure portal oluşturun. Azure portal içindeki Hizmetler listesine gidin, **kaynak grubu** ' nu seçin ve **+ Ekle**' yi seçin. Kaynak grubunuz için abonelik bilgilerini ve adı girip **Oluştur**' u seçin. Bu kaynak grubuna giderseniz, şu anda kapsamında herhangi bir kaynak olmamalıdır.

        ![Azure kaynak grubu](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. `.cer`Azure Stack Edge Mini R cihazınız için bir temel 64 kodlu sertifikaya sahip olmanız gerekir. Bu sertifika `pfx` , özel bir anahtarla birlikte Azure Stack Edge cihazınıza yüklenmelidir. Bu sertifika, P2S bağlantısını kurmaya çalışan istemcideki deponun güvenilir köküne da yüklenmelidir.

## <a name="use-the-script"></a>Betiği kullan

İlk `parameters-p2s.json` olarak, parametrelerinizi girmek için dosyayı değiştirirsiniz. Ardından, değiştirilmiş json dosyasını kullanarak betiği çalıştırırsınız.

Bu adımların her biri aşağıdaki bölümlerde ele alınmıştır.

### <a name="download-service-tags-file"></a>Hizmet etiketleri dosyasını indir

`ServiceTags.json`Betiği indirdiğiniz klasörde zaten bir dosyanız olabilir. Aksi takdirde, hizmet etiketleri dosyasını indirebilirsiniz.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Parametre dosyasını Değiştir

İlk adım, `parameters-p2s.json` dosyayı değiştirmek ve değişiklikleri kaydetmek olacaktır. 

Oluşturduğunuz Azure kaynakları için aşağıdaki adları sağlarsınız:

|Parametre adı  |Açıklama  |
|---------|---------|
|virtualNetworks_vnet_name    | Azure sanal ağ adı        |
|azureFirewalls_firewall_name     | Azure Güvenlik duvarı adı        |
|routeTables_routetable_name     | Azure yol tablosu adı        |
|publicIPAddresses_VNGW_public_ip_name     | Sanal ağ geçidinizin genel IP adresi adı       |
|virtualNetworkGateways_VNGW_name    | Azure VPN Gateway (sanal ağ geçidi) adı        |
|publicIPAddresses_firewall_public_ip_name     | Azure Güvenlik duvarınız için genel IP adresi adı         |
|location     |Bu, Sanal ağınızı oluşturmak istediğiniz bölgedir. Aygıtınızla ilişkili bir bölgeyi seçin.         |
|RouteTables_routetable_onprem_name| Bu, güvenlik duvarının paketlerin Azure Stack Edge cihazına geri yönlendirilmesini sağlamak için ek yol tablosunun adıdır. Betik iki ek yol oluşturur ve bu yol tablosuyla *varsayılan* ve *firewallsubnet* 'i ilişkilendirir.|

Sanal ağ ve ilişkili alt ağlar (*varsayılan*, *güvenlik duvarı*, *gatewaysubnet*) dahil olmak üzere oluşturulan Azure kaynakları için aşağıdaki IP adreslerini ve adres alanlarını sağlayın.

|Parametre adı  |Açıklama  |
|---------|---------|
|VnetIPv4AddressSpace    | Bu, sanal ağınızla ilişkili adres alanıdır. VNET IP aralığını özel IP aralığı olarak belirtin ( https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) .     |
|DefaultSubnetIPv4AddressSpace    |Bu, `Default` sanal ağınızın alt ağıyla ilişkili adres alanıdır.         |
|FirewallSubnetIPv4AddressSpace    |Bu, `Firewall` sanal ağınızın alt ağıyla ilişkili adres alanıdır.          |
|GatewaySubnetIPv4AddressSpace    |Bu, sanal ağınız için ile ilişkili adres alanıdır `GatewaySubnet` .          |
|GatewaySubnetIPv4bgpPeeringAddress    | Bu, BGP iletişimi için ayrılan ve sanal ağınız için ile ilişkili adres alanını temel alan IP adresidir `GatewaySubnet` .          |
|ClientAddressPool    | Bu IP adresi, Azure portal P2S yapılandırmasındaki adres havuzu için kullanılır.         |
|PublicCertData     | Ortak sertifika verileri, kendisine bağlanan P2S istemcilerinin kimliğini doğrulamak için VPN Gateway tarafından kullanılır. Sertifika verilerini almak için kök sertifikayı yükler. Sertifikanın bir. cer uzantısıyla kodlanmış Base-64 olduğundan emin olun. Bu sertifikayı açın ve sertifikayı bir sürekli satırda = = BEGIN SERTIFIKASı = = ve = = son SERTIFIKA = = arasında kopyalayın.     |



### <a name="run-the-script"></a>Betiği çalıştırın

Değiştirme 'yi kullanmak `parameters-p2s.json` ve betiği çalıştırmak Için Azure kaynakları oluşturmak için aşağıdaki adımları izleyin.

1. PowerShell 'i çalıştırın. Betiğin bulunduğu dizine geçin.

3. Betiği çalıştırın.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > Bu sürümde, betik yalnızca Doğu ABD konumunda çalışmaktadır.

    Betiği çalıştırdığınızda aşağıdaki bilgileri de yazmanız gerekir:

    
    |Parametre  |Açıklama  |
    |---------|---------|
    |Konum     |Bu, Azure kaynaklarının oluşturulması gereken bölgedir.         |
    |AzureAppRuleFilePath     | Bu, için dosya yoludur `appRule.json` .       |
    |AzureIPRangesFilePath     |Bu, önceki adımda indirdiğiniz hizmet etiketi JSON dosyasıdır.         |
    |ResourceGroupName     | Bu, tüm Azure kaynaklarının oluşturulduğu kaynak grubunun adıdır.        |
    |AzureDeploymentName    |Bu, Azure dağıtımınızın adıdır.         |
    |NetworkRuleCollectionName            | Bu, oluşturulan ve Azure güvenlik duvarınızdan eklenen tüm ağ kurallarının toplanması için olan addır.             |
    |Öncelik            | Bu, oluşturulan tüm ağ ve uygulama kurallarına atanan önceliktir.              |
    |AppRuleCollectionName            |Bu, oluşturulan ve Azure güvenlik duvarınızdan eklenen tüm uygulama kurallarının toplanması için olan addır.                |


    Örnek çıktı aşağıda gösterilmiştir.
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - Betiğin çalıştırılması yaklaşık 90 dakika sürer. Betik başlamadan önce ağınızda oturum açmanız emin olun.
    > - Komut dosyası ile başarısız bir oturum varsa, altında oluşturulan tüm kaynakları silmek için kaynak grubunu da seçtiğinizden emin olun.
  
    
    Betik tamamlandıktan sonra, bir dağıtım günlüğü, betiğin bulunduğu klasörde oluşturulur.


## <a name="verify-the-azure-resources"></a>Azure kaynaklarını doğrulama

Betiği başarıyla çalıştırdıktan sonra, tüm kaynakların Azure 'da oluşturulduğunu doğrulayın. Oluşturduğunuz kaynak grubuna gidin. Aşağıdaki kaynakları görmeniz gerekir:

![Azure kaynakları](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>VPN profili için telefon rehberini indirin

Bu adımda, cihazınızın VPN profilini indirirsiniz.

1. Azure portal, kaynak grubuna gidin ve ardından önceki adımda oluşturduğunuz sanal ağ geçidini seçin.

    ![Azure sanal ağ geçidi](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. **Ayarlar > Noktadan siteye yapılandırma**' ya gidin. **VPN Istemcisini indir**' i seçin.

    ![P2S yapılandırma 1 ' i etkinleştir](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. Zip profilini kaydedin ve Windows istemcinizden ayıklayın.

    ![P2S yapılandırmasını etkinleştir 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. *WindowsAmd64* klasörüne gidin ve `.exe` : *VpnClientSetupAmd64.exe* ayıklayın.

    ![P2S yapılandırma 3 ' ü etkinleştir](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. Geçici bir yol oluşturun. Örnek:

    `C:\NewTemp\vnet\tmp`

4. PowerShell 'i çalıştırın ve bulunduğu dizine gidin `.exe` . Yürütmek için `.exe` şunu yazın:

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. Geçici yolun yeni dosyaları olacaktır. Örnek çıktı aşağıdaki gibidir:

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. *. Pbk* dosyası, VPN profili için telefon defteridir. Bunu, yerel kullanıcı arabiriminde kullanacaksınız.


## <a name="vpn-configuration-on-the-device"></a>Cihazdaki VPN yapılandırması

Azure Stack Edge cihazınızın yerel kullanıcı arabirimindeki adımları izleyin.

1. Yerel Kullanıcı arabiriminde, **VPN** sayfasına gidin. VPN durumu altında **Yapılandır**' ı seçin.

    ![VPN 1 yapılandırma](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. **VPN Yapılandır** dikey penceresinde:
    
    1. Telefon defteri dosyasını karşıya yükle bölümünde, önceki adımda oluşturduğunuz. pbk dosyasını işaret edin.
    2. Ortak IP listesi yapılandırma dosyasını karşıya yükle ' de, Azure veri merkezi IP aralığı JSON dosyasını girdi olarak sağlayın. Bu dosyayı önceki bir adımda şuradan indirdiniz: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
    3. Bölge olarak **eastus** ' u seçin ve **Uygula**' yı seçin.

    ![VPN 2 yapılandırma](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. **IP adresi aralıklarına yalnızca VPN kullanılarak erişilmek üzere** , Azure sanal ağınız Için seçtiğiniz VNET IPv4 aralığını girin.

    ![VPN 3 yapılandırma](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>İstemci bağlantısını doğrula

1. Azure portal, VPN ağ geçidine gidin.
2. **Ayarlar > Noktadan siteye yapılandırma**' ya gidin. **AYRıLAN IP adresleri** altında, Azure Stack Edge cihazınızın IP adresi görünür olmalıdır.

## <a name="validate-data-transfer-through-vpn"></a>VPN üzerinden veri aktarımını doğrulama

VPN 'nin çalıştığını doğrulamak için, verileri bir SMB paylaşımında kopyalayın. Azure Stack Edge cihazınızda [paylaşma ekleme](azure-stack-edge-gpu-manage-shares.md#add-a-share) bölümündeki adımları izleyin. 

1. \data\pictures\waterfall.jpg, örneğin, istemci sisteminize bağladığınız SMB paylaşımında bir dosya kopyalayın. 
2. Veriler kopyalanırken verilerin VPN üzerinden gittiğini doğrulamak için:

    1. Azure portal VPN Gateway 'e gidin. 

    2. **İzleme > ölçümleri**' ne gidin.

    3. Sağ bölmede, VPN ağ geçidiniz olarak **kapsam** , ağ geçidi P2S bant genişliği olarak **ölçüm** ve ortalama olarak **toplama** seçeneğini belirleyin.

    4. Veriler kopyalanırken, bant genişliği kullanımında bir artış görürsünüz ve veri kopyalama tamamlandığında bant genişliği kullanımı başlatılır.

        ![Azure VPN ölçümleri](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. Bu dosyanın buluttaki depolama hesabınızda göründüğünü doğrulayın.
 
## <a name="debug-issues"></a>Hata ayıklama sorunları

Herhangi bir sorunu ayıklamak için aşağıdaki komutları kullanın:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

Örnek çıktı aşağıda gösterilmektedir:


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
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

[Azure Stack Edge cihazınızda yerel kullanıcı arabirimi aracılığıyla VPN 'Yi yapılandırın](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn).